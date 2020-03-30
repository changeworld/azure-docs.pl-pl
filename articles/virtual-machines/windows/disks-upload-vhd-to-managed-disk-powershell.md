---
title: Przekazywanie wersji vhd na platformę Azure przy użyciu programu Azure PowerShell
description: Dowiedz się, jak przekazać vhd na dysk zarządzany platformy Azure i skopiować dysk zarządzany w różnych regionach za pomocą programu Azure PowerShell za pośrednictwem bezpośredniego przekazywania.
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 883fea1e25ded26c35e96d11edd8f417e96db30e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369560"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Przekazywanie wersji vhd na platformę Azure przy użyciu programu Azure PowerShell

W tym artykule wyjaśniono, jak przekazać vhd z komputera lokalnego na dysk zarządzany platformy Azure. Wcześniej trzeba było wykonać bardziej zaangażowany proces, który obejmował przemieszczanie danych na koncie magazynu i zarządzanie tym kontem magazynu. Teraz nie trzeba już zarządzać kontem magazynu ani przesyłać w nim danych do etapu, aby przesłać vhd. Zamiast tego należy utworzyć pusty dysk zarządzany i przekazać vhd bezpośrednio do niego. Upraszcza to przekazywanie lokalnych maszyn wirtualnych na platformę Azure i umożliwia przekazywanie vhd do 32 TiB bezpośrednio na dużym dysku zarządzanym.

Jeśli udostępniasz rozwiązanie do tworzenia kopii zapasowych dla maszyn wirtualnych IaaS na platformie Azure, zalecamy użycie bezpośredniego przekazywania w celu przywrócenia kopii zapasowych klientów na dyskach zarządzanych. Jeśli przekazujesz dysk VHD z komputera zewnętrznego na platformę Azure, szybkość zależy od przepustowości lokalnej. Jeśli używasz maszyny Wirtualnej platformy Azure, przepustowość będzie taka sama jak standardowe dyski twarde.

Obecnie przesyłanie bezpośrednie jest obsługiwane dla standardowych dysków twardych, standardowych dysków SSD i dysków zarządzanych w wersji premium. Nie jest jeszcze obsługiwany dla ultra SSD.

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz najnowszą [wersję AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Zainstaluj moduł programu Azure PowerShell](/powershell/azure/install-Az-ps).
- Jeśli zamierzasz przekazać dysk VHD z lokalnego: dysk VHD o stałym rozmiarze, który [został przygotowany dla platformy Azure,](prepare-for-upload-vhd-image.md)przechowywany lokalnie.
- Lub dysku zarządzanego na platformie Azure, jeśli zamierzasz wykonać akcję kopiowania.

## <a name="create-an-empty-managed-disk"></a>Tworzenie pustego dysku zarządzanego

Aby przekazać vhd na platformę Azure, musisz utworzyć pusty dysk zarządzany, który jest skonfigurowany dla tego procesu przekazywania. Przed utworzeniem jednego z nich, istnieje kilka dodatkowych informacji, które należy wiedzieć o tych dyskach.

Ten rodzaj dysku zarządzanego ma dwa unikatowe stany:

- ReadToUpload, co oznacza, że dysk jest gotowy do odbierania przekazywania, ale nie [zabezpieczono podpis dostępu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) został wygenerowany.
- ActiveUpload, co oznacza, że dysk jest gotowy do odbierania przekazywania i sygnatury dostępu Współdzielonego został wygenerowany.

W każdym z tych stanów dysk zarządzany będzie rozliczany według [standardowych cen dysku twardego](https://azure.microsoft.com/pricing/details/managed-disks/), niezależnie od rzeczywistego typu dysku. Na przykład P10 będą rozliczane jako S10. Będzie to prawdą, dopóki nie `revoke-access` zostanie wywołana na dysku zarządzanym, który jest wymagany do podłączenia dysku do maszyny Wirtualnej.

Przed utworzeniem pustego standardowego dysku twardego do przesyłania, będziesz potrzebować rozmiaru pliku w bajtach vhd, który chcesz przekazać. Przykładowy kod dostanie to za Ciebie, ale aby `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`zrobić to sam, możesz użyć: . Ta wartość jest używana podczas określania **parametru -UploadSizeInBytes.**

Teraz na lokalnej powłoce utwórz pusty standardowy dysk twardy do przekazywania, określając ustawienie **Przekazywanie** w parametrze **-CreateOption,** a także parametr **-UploadSizeInBytes** w poleceniu [New-AzDiskConfig.](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Następnie zadzwoń do [New-AzDisk,](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) aby utworzyć dysk:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Jeśli chcesz przesłać ssd premium lub standardowy SSD, zastąp **Standard_LRS** **Premium_LRS** lub **StandardSSD_LRS**. Ultra SSD nie jest jeszcze obsługiwany.

Utworzono teraz pusty dysk zarządzany, który jest skonfigurowany dla procesu przekazywania. Aby przekazać vhd na dysk, musisz zapisywalny sygnatura dostępu Współdzielonego, dzięki czemu można odwoływać się do niego jako miejsca docelowego przekazywania.

Aby wygenerować zapisywalny sygnatura dostępu Współdzielonego pustego dysku zarządzanego, użyj następującego polecenia:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Prześlij vhd

Teraz, gdy masz sygnatury dostępu Współdzielonego dla pustego dysku zarządzanego, można go użyć do skonfigurowania dysku zarządzanego jako miejsca docelowego polecenia przekazywania.

Użyj AzCopy v10, aby przekazać lokalny plik VHD na dysk zarządzany, określając wygenerowany identyfikator URI sygnatury dostępu Współdzielonego.

To przesyłanie ma taką samą przepustowość jak równoważny [standardowy dysk twardy](disks-types.md#standard-hdd). Na przykład jeśli masz rozmiar, który odpowiada S4, będziesz miał przepływność do 60 MiB/s. Ale jeśli masz rozmiar, który odpowiada S70, będziesz miał przepustowość do 500 MiB / s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Po zakończeniu przekazywania i nie trzeba już zapisywać więcej danych na dysku, odwołać sygnaturę dostępu Współdzielonego. Odwołanie sygnatury dostępu Współdzielonego spowoduje zmianę stanu dysku zarządzanego i umożliwi dołączenie dysku do maszyny Wirtualnej.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Kopiowanie dysku zarządzanego

Bezpośrednie przekazywanie upraszcza również proces kopiowania dysku zarządzanego. Można skopiować w tym samym regionie lub międzyregionami (do innego regionu).

Skrypt obserwowany zrobi to za Ciebie, proces jest podobny do kroków opisanych wcześniej, z pewnymi różnicami, ponieważ pracujesz z istniejącym dyskiem.

> [!IMPORTANT]
> Należy dodać przesunięcie 512, gdy udostępniasz rozmiar dysku w bajtach dysku zarządzanego z platformy Azure. Dzieje się tak, ponieważ platforma Azure pomija stopki podczas zwracania rozmiaru dysku. Kopia zakończy się niepowodzeniem, jeśli tego nie zrobisz. Poniższy skrypt już to robi za Ciebie.

`<sourceResourceGroupHere>`Zastąp `<targetDiskNameHere>` `<targetResourceGroupHere>`, `<yourOSTypeHere>` `<sourceDiskNameHere>` `<yourTargetLocationHere>` , , i (przykładem wartości lokalizacji będzie uswest2) wartościami, a następnie uruchom następujący skrypt w celu skopiowania dysku zarządzanego.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy pomyślnie przeminięto na dysk zarządzany dysk można dołączyć do maszyny Wirtualnej i rozpocząć jej używanie.

Aby dowiedzieć się, jak dołączyć dysk danych do maszyny Wirtualnej, zobacz nasz artykuł na ten temat: [Dołączanie dysku z danymi do maszyny Wirtualnej systemu Windows za pomocą programu PowerShell](attach-disk-ps.md). Aby użyć dysku jako dysku systemu operacyjnego, zobacz [Tworzenie maszyny Wirtualnej systemu Windows z dysku specjalistycznego](create-vm-specialized.md#create-the-new-vm).
