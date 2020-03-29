---
title: Przekazywanie dysku twardego z użyciem interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak przekazać vhd na dysk zarządzany platformy Azure i skopiować dysk zarządzany w różnych regionach, przy użyciu interfejsu wiersza polecenia platformy Azure, za pośrednictwem bezpośredniego przekazywania.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: d89a4279d425e4b12e92aae81edfd6c1514c3eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062665"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Przekazywanie vhd na platformę Azure przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule wyjaśniono, jak przekazać vhd z komputera lokalnego na dysk zarządzany platformy Azure. Wcześniej trzeba było wykonać bardziej zaangażowany proces, który obejmował przemieszczanie danych na koncie magazynu i zarządzanie tym kontem magazynu. Teraz nie trzeba już zarządzać kontem magazynu ani przesyłać w nim danych do etapu, aby przesłać vhd. Zamiast tego należy utworzyć pusty dysk zarządzany i przekazać vhd bezpośrednio do niego. Upraszcza to przekazywanie lokalnych maszyn wirtualnych na platformę Azure i umożliwia przekazywanie vhd do 32 TiB bezpośrednio na dużym dysku zarządzanym.

Jeśli udostępniasz rozwiązanie do tworzenia kopii zapasowych dla maszyn wirtualnych IaaS na platformie Azure, zalecamy użycie bezpośredniego przekazywania w celu przywrócenia kopii zapasowych klientów na dyskach zarządzanych. Jeśli przekazujesz dysk VHD z komputera zewnętrznego na platformę Azure, szybkość będzie zależeć od przepustowości lokalnej. Jeśli używasz maszyny Wirtualnej platformy Azure, przepustowość będzie taka sama jak standardowe dyski twarde.

Obecnie przesyłanie bezpośrednie jest obsługiwane dla standardowych dysków twardych, standardowych dysków SSD i dysków zarządzanych w wersji premium. Nie jest jeszcze obsługiwany dla ultra SSD.

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz najnowszą [wersję AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Zainstaluj narzędzie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
- Plik vhd przechowywany lokalnie
- Jeśli zamierzasz przekazać vhd z lokalnego: O stałym rozmiarze vhd, który [został przygotowany dla platformy Azure,](../windows/prepare-for-upload-vhd-image.md)przechowywane lokalnie.
- Lub dysku zarządzanego na platformie Azure, jeśli zamierzasz wykonać akcję kopiowania.

## <a name="create-an-empty-managed-disk"></a>Tworzenie pustego dysku zarządzanego

Aby przekazać vhd na platformę Azure, musisz utworzyć pusty dysk zarządzany, który jest skonfigurowany dla tego procesu przekazywania. Przed utworzeniem jednego z nich, istnieje kilka dodatkowych informacji, które należy wiedzieć o tych dyskach.

Ten rodzaj dysku zarządzanego ma dwa unikatowe stany:

- ReadToUpload, co oznacza, że dysk jest gotowy do odbierania przekazywania, ale nie [zabezpieczono podpis dostępu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) został wygenerowany.
- ActiveUpload, co oznacza, że dysk jest gotowy do odbierania przekazywania i sygnatury dostępu Współdzielonego został wygenerowany.

W każdym z tych stanów dysk zarządzany będzie rozliczany według [standardowych cen dysku twardego](https://azure.microsoft.com/pricing/details/managed-disks/), niezależnie od rzeczywistego typu dysku. Na przykład P10 będą rozliczane jako S10. Będzie to prawdą, dopóki nie `revoke-access` zostanie wywołana na dysku zarządzanym, który jest wymagany do podłączenia dysku do maszyny Wirtualnej.

Zanim będzie można utworzyć pusty standardowy dysk twardy do przesyłania, musisz mieć rozmiar pliku vhd, który chcesz przekazać, w bajtach. Aby to uzyskać, można `wc -c <yourFileName>.vhd` użyć `ls -al <yourFileName>.vhd`albo lub . Ta wartość jest używana podczas określania parametru **--upload-size-bytes.**

Utwórz pusty standardowy dysk twardy do przekazywania, określając zarówno parametr **--for-upload,** jak i parametr **--upload-size-bytes** w [połączeniu](/cli/azure/disk#az-disk-create) cmdlet na dysku:

```azurecli
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Jeśli chcesz przesłać ssd premium lub standardowy SSD, zastąp **standard_lrs** **premium_LRS** lub **standardssd_lrs**. Ultra SSD nie jest jeszcze obsługiwany.

Utworzono teraz pusty dysk zarządzany, który jest skonfigurowany dla procesu przekazywania. Aby przekazać vhd na dysk, musisz zapisywalny sygnatura dostępu Współdzielonego, dzięki czemu można odwoływać się do niego jako miejsca docelowego przekazywania.

Aby wygenerować zapisywalny sygnatura dostępu Współdzielonego pustego dysku zarządzanego, użyj następującego polecenia:

```azurecli
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Próbka zwrócona wartość:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Prześlij vhd

Teraz, gdy masz sygnatury dostępu Współdzielonego dla pustego dysku zarządzanego, można go użyć do skonfigurowania dysku zarządzanego jako miejsca docelowego polecenia przekazywania.

Użyj AzCopy v10, aby przekazać lokalny plik VHD na dysk zarządzany, określając wygenerowany identyfikator URI sygnatury dostępu Współdzielonego.

To przesyłanie ma taką samą przepustowość jak równoważny [standardowy dysk twardy](disks-types.md#standard-hdd). Na przykład jeśli masz rozmiar, który odpowiada S4, będziesz miał przepływność do 60 MiB/s. Ale jeśli masz rozmiar, który odpowiada S70, będziesz miał przepustowość do 500 MiB / s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Po zakończeniu przekazywania i nie trzeba już zapisywać więcej danych na dysku, odwołać sygnaturę dostępu Współdzielonego. Odwołanie sygnatury dostępu Współdzielonego spowoduje zmianę stanu dysku zarządzanego i umożliwi dołączenie dysku do maszyny Wirtualnej.

```azurecli
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Kopiowanie dysku zarządzanego

Bezpośrednie przekazywanie upraszcza również proces kopiowania dysku zarządzanego. Można skopiować w tym samym regionie lub międzyregionami (do innego regionu).

Skrypt obserwowany zrobi to za Ciebie, proces jest podobny do kroków opisanych wcześniej, z pewnymi różnicami, ponieważ pracujesz z istniejącym dyskiem.

> [!IMPORTANT]
> Należy dodać przesunięcie 512, gdy udostępniasz rozmiar dysku w bajtach dysku zarządzanego z platformy Azure. Dzieje się tak, ponieważ platforma Azure pomija stopki podczas zwracania rozmiaru dysku. Kopia zakończy się niepowodzeniem, jeśli tego nie zrobisz. Poniższy skrypt już to robi za Ciebie.

`<sourceResourceGroupHere>`Zastąp `<targetDiskNameHere>` `<targetResourceGroupHere>`, `<sourceDiskNameHere>` `<yourTargetLocationHere>` , , i (przykładem wartości lokalizacji będzie uswest2) wartościami, a następnie uruchom następujący skrypt w celu skopiowania dysku zarządzanego.

```azurecli
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

Teraz, gdy pomyślnie przeminięno vhd na dysk zarządzany, można dołączyć dysk jako [dysk danych do istniejącej maszyny Wirtualnej](add-disk.md) lub [dołączyć dysk do maszyny Wirtualnej jako dysk systemu operacyjnego,](upload-vhd.md#create-the-vm)aby utworzyć nową maszynę wirtualną. 

