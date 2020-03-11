---
title: Przekazywanie wirtualnego dysku twardego przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak przekazać dysk VHD do dysku zarządzanego platformy Azure i skopiować dysk zarządzany między regionami przy użyciu interfejsu wiersza polecenia platformy Azure za pośrednictwem bezpośredniego przekazywania.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2a5bfec08546d6cf00b1e04017b3879db8f016ee
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970342"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Przekazywanie wirtualnego dysku twardego do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób przekazywania dysku VHD z komputera lokalnego do dysku zarządzanego platformy Azure. Wcześniej musiałeś postępować zgodnie z większym procesem, który obejmuje przemieszczenie danych na konto magazynu, i zarządzanie tym kontem magazynu. Teraz nie trzeba już zarządzać kontem magazynu ani przemieszczać danych w celu przekazania dysku VHD. Zamiast tego należy utworzyć pusty dysk zarządzany i bezpośrednio przekazać do niego dysk VHD. Upraszcza to przekazywanie lokalnych maszyn wirtualnych na platformę Azure i umożliwia przekazywanie wirtualnego dysku twardego do 32 TiB bezpośrednio do dysku zarządzanego.

Jeśli udostępniasz rozwiązanie do tworzenia kopii zapasowych dla maszyn wirtualnych IaaS na platformie Azure, zalecamy użycie funkcji bezpośredniego przekazywania w celu przywrócenia kopii zapasowych klientów na dyskach zarządzanych. Jeśli przekazujesz wirtualny dysk twardy z maszyny zewnętrznej do platformy Azure, szybkość będzie zależeć od lokalnej przepustowości. W przypadku korzystania z maszyny wirtualnej platformy Azure przepustowość będzie taka sama jak standardowa HDD.

Obecnie bezpośrednie przekazywanie jest obsługiwane dla standardowego dysku twardego, standardowego SSD i dysków zarządzanych w warstwie Premium SSD. Nie jest jeszcze obsługiwane dla Ultra dysków SSD.

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz najnowszą [wersję programu AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
- Plik VHD zapisany lokalnie
- Jeśli zamierzasz przekazać dysk VHD ze swojego miejsca lokalnego: dysku VHD [przygotowanego dla platformy Azure](../windows/prepare-for-upload-vhd-image.md), który jest przechowywany lokalnie.
- Lub dysk zarządzany na platformie Azure, jeśli zamierzasz wykonać akcję kopiowania.

## <a name="create-an-empty-managed-disk"></a>Utwórz pusty dysk zarządzany

Aby przekazać dysk VHD na platformę Azure, musisz utworzyć pusty dysk zarządzany skonfigurowany dla tego procesu przekazywania. Przed utworzeniem jednego z tych dysków należy zapoznać się z dodatkowymi informacjami.

Ten rodzaj dysku zarządzanego ma dwa unikatowe Stany:

- ReadToUpload, co oznacza, że dysk jest gotowy do odebrania przekazania, ale nie Wygenerowano [sygnatury bezpiecznego dostępu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, co oznacza, że dysk jest gotowy do odebrania przekazywania i Wygenerowano sygnaturę dostępu współdzielonego.

W każdym z tych stanów dysk zarządzany jest rozliczany zgodnie ze [standardowymi cenami](https://azure.microsoft.com/pricing/details/managed-disks/)dysków twardych, niezależnie od rzeczywistego typu dysku. Na przykład P10 będzie rozliczany jako S10. Ta wartość będzie prawdziwa do momentu wywołania `revoke-access` na dysku zarządzanym, co jest wymagane w celu dołączenia dysku do maszyny wirtualnej.

Przed utworzeniem pustego standardowego dysku twardego do przekazania należy mieć rozmiar pliku wirtualnego dysku twardego, który ma zostać przekazany, w bajtach. Aby to zrobić, możesz użyć obu `wc -c <yourFileName>.vhd` lub `ls -al <yourFileName>.vhd`. Ta wartość jest używana podczas określania parametru **--upload-size-Bytes** .

Tworzenie pustego standardowego dysku twardego do przekazywania przez określenie zarówno parametru **--for-upload** , jak i parametru **--upload-size-Bytes** w poleceniu cmdlet [Create Disk](/cli/azure/disk#az-disk-create) :

```bash
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Jeśli chcesz przekazać dysk SSD w warstwie Premium lub dysk SSD w warstwie Standardowa, Zastąp **standard_lrs** z **premium_LRS** lub **standardssd_lrs**. SSD w warstwie Ultra nie jest jeszcze obsługiwana.

Utworzono pusty dysk zarządzany skonfigurowany dla procesu przekazywania. Aby przekazać dysk VHD do dysku, musisz mieć zapisywalne sygnatury dostępu współdzielonego, aby można było odwołać się do niego jako miejsce docelowe przekazywania.

Aby wygenerować zapisywalne SAS pustego dysku zarządzanego, użyj następującego polecenia:

```bash
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Przykładowa zwracana wartość:

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Przekazywanie wirtualnego dysku twardego

Teraz, gdy masz sygnaturę dostępu współdzielonego dla pustego dysku zarządzanego, możesz użyć jej do ustawienia dysku zarządzanego jako miejsca docelowego dla polecenia przekazywania.

Za pomocą AzCopy v10 Przekaż swój lokalny plik VHD na dysk zarządzany, określając wygenerowany identyfikator URI sygnatury dostępu współdzielonego.

To przekazywanie ma taką samą przepływność jak odpowiednik [standardowego dysku twardego](disks-types.md#standard-hdd). Na przykład jeśli masz rozmiar, który jest równa S4, będziesz mieć przepływność do 60 MiB/s. Ale jeśli masz rozmiar, który jest równa S70, będziesz mieć przepływność do 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Jeśli Twoje sygnatury dostępu współdzielonego wygasną podczas przekazywania i nie wywołałeś jeszcze `revoke-access`, możesz uzyskać nowe sygnatury dostępu współdzielonego, aby kontynuować przekazywanie za pomocą `grant-access`ponownie.

Po zakończeniu przekazywania i nie musisz już pisać więcej danych na dysku, odwołaj sygnaturę dostępu współdzielonego. Odwoływanie sygnatury dostępu współdzielonego spowoduje zmianę stanu dysku zarządzanego i umożliwi dołączenie dysku do maszyny wirtualnej.

```bash
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Kopiowanie dysku zarządzanego

Bezpośrednie przekazywanie upraszcza również proces kopiowania dysku zarządzanego. Można wykonać kopię w tym samym regionie lub w różnych regionach (do innego regionu).

Wykonanie poniższej skryptu spowoduje to, że proces jest podobny do opisanego wcześniej procedury, z pewnymi różnicami, ponieważ pracujesz z istniejącym dyskiem.

> [!IMPORTANT]
> Należy dodać przesunięcie 512, gdy podajesz rozmiar dysku w bajtach dysku zarządzanego na platformie Azure. Wynika to z faktu, że platforma Azure pomija stopkę podczas zwracania rozmiaru dysku. Kopia nie powiedzie się, jeśli to zrobisz. Poniższy skrypt już robi to za Ciebie.

Zastąp `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`i `<yourTargetLocationHere>` (przykładem wartości lokalizacji byłaby uswest2) wartościami, a następnie uruchom poniższy skrypt w celu skopiowania dysku zarządzanego.

```bash
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wirtualny dysk twardy został pomyślnie przekazany do dysku zarządzanego, możesz dołączyć dysk jako [dysk danych do istniejącej maszyny wirtualnej](add-disk.md) lub [dołączyć dysk do maszyny wirtualnej jako dysk systemu operacyjnego](upload-vhd.md#create-the-vm), aby utworzyć nową maszynę wirtualną. 

