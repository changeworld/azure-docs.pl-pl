---
title: Przekazywanie dysku VHD na platformę Azure lub kopiowanie dysku w różnych regionach — narzędzie interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak przekazać dysk VHD na zarządzany dysk platformy Azure i skopiować dysk zarządzany w różnych regionach za pomocą interfejsu wiersza polecenia platformy Azure za pośrednictwem bezpośredniego przekazywania.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6813206aebe67e4e6d2afd0d9c78d03f0c20c952
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420976"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Przekazywanie dysku VHD na platformę Azure lub kopiowanie dysku zarządzanego do innego regionu — interfejsu wiersza polecenia platformy Azure

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz najnowszą [wersję AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Zainstaluj narzędzie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
- Jeśli zamierzasz przekazać dysk VHD z lokalnego: dysk VHD o stałym rozmiarze, który [został przygotowany dla platformy Azure,](../windows/prepare-for-upload-vhd-image.md)przechowywany lokalnie.
- Lub dysku zarządzanego na platformie Azure, jeśli zamierzasz wykonać akcję kopiowania.

## <a name="getting-started"></a>Wprowadzenie

Jeśli wolisz przekazać dyski za pośrednictwem interfejsu użytkownika, możesz to zrobić za pomocą Eksploratora usługi Azure Storage. Aby uzyskać szczegółowe informacje, zobacz: [Zarządzanie dyskami zarządzanymi platformy Azure za pomocą Eksploratora usługi Azure](disks-use-storage-explorer-managed-disks.md)

Aby przekazać dysk VHD na platformę Azure, musisz utworzyć pusty dysk zarządzany, który jest skonfigurowany dla tego procesu przekazywania. Przed utworzeniem jednego z nich, istnieje kilka dodatkowych informacji, które należy wiedzieć o tych dyskach.

Ten rodzaj dysku zarządzanego ma dwa unikatowe stany:

- ReadToUpload, co oznacza, że dysk jest gotowy do odbierania przekazywania, ale nie [zabezpieczono podpis dostępu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) został wygenerowany.
- ActiveUpload, co oznacza, że dysk jest gotowy do odbierania przekazywania i sygnatury dostępu Współdzielonego został wygenerowany.

> [!NOTE]
> W każdym z tych stanów dysk zarządzany będzie rozliczany według [standardowych cen dysku twardego](https://azure.microsoft.com/pricing/details/managed-disks/), niezależnie od rzeczywistego typu dysku. Na przykład P10 będą rozliczane jako S10. Będzie to prawdą, dopóki nie `revoke-access` zostanie wywołana na dysku zarządzanym, który jest wymagany do podłączenia dysku do maszyny Wirtualnej.

## <a name="create-an-empty-managed-disk"></a>Tworzenie pustego dysku zarządzanego

Zanim będzie można utworzyć pusty standardowy dysk twardy do przesyłania, będziesz potrzebować rozmiaru pliku VHD, który chcesz przekazać, w bajtach. Aby to uzyskać, można `wc -c <yourFileName>.vhd` użyć `ls -al <yourFileName>.vhd`albo lub . Ta wartość jest używana podczas określania parametru **--upload-size-bytes.**

Utwórz pusty standardowy dysk twardy do przekazywania, określając zarówno parametr **--for-upload,** jak i parametr **--upload-size-bytes** w [połączeniu](/cli/azure/disk#az-disk-create) cmdlet na dysku:

Zastąp `<yourdiskname>`, `<yourresourcegroupname>` `<yourregion>` wybranymi wartościami. Parametr `--upload-size-bytes` zawiera przykładową `34359738880`wartość , zastąpić go wartością odpowiednią dla Ciebie.

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Jeśli chcesz przesłać ssd premium lub standardowy SSD, zastąp **standard_lrs** **premium_LRS** lub **standardssd_lrs**. Dyski ultra nie są na razie obsługiwane.

Po utworzeniu pustego dysku zarządzanego skonfigurowanego dla procesu przekazywania można przekazać do niego dysk wirtualny. Aby przekazać dysk VHD na dysk, musisz zapisywać sas, dzięki czemu można odwoływać się do niego jako miejsce docelowe przekazywania.

Aby wygenerować zapisywalny sygnatura dostępu Współdzielonego pustego dysku zarządzanego, zastąp `<yourdiskname>`i `<yourresourcegroupname>`użyj następującego polecenia:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Próbka zwrócona wartość:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Przekazywanie wirtualnego dysku twardego

Teraz, gdy masz sygnatury dostępu Współdzielonego dla pustego dysku zarządzanego, można go użyć do skonfigurowania dysku zarządzanego jako miejsca docelowego polecenia przekazywania.

Użyj AzCopy v10, aby przekazać lokalny plik VHD na dysk zarządzany, określając wygenerowany identyfikator URI sygnatury dostępu Współdzielonego.

To przesyłanie ma taką samą przepustowość jak równoważny [standardowy dysk twardy](disks-types.md#standard-hdd). Na przykład jeśli masz rozmiar, który odpowiada S4, będziesz miał przepływność do 60 MiB/s. Ale jeśli masz rozmiar, który odpowiada S70, będziesz miał przepustowość do 500 MiB / s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Po zakończeniu przekazywania i nie trzeba już zapisywać więcej danych na dysku, odwołać sygnaturę dostępu Współdzielonego. Odwołanie sygnatury dostępu Współdzielonego spowoduje zmianę stanu dysku zarządzanego i umożliwi dołączenie dysku do maszyny Wirtualnej.

Zamień `<yourdiskname>`i `<yourresourcegroupname>`, a następnie użyj następującego polecenia, aby dysk był użyteczny:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
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

Po pomyślnym przekazaniu dysku wirtualnego na dysk zarządzany dysk można dołączyć go jako [dysk danych do istniejącej maszyny Wirtualnej](add-disk.md) lub [dołączyć dysk do maszyny Wirtualnej jako dysk systemu operacyjnego,](upload-vhd.md#create-the-vm)aby utworzyć nową maszynę wirtualną. 

