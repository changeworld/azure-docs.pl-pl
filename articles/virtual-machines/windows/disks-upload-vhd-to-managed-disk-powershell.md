---
title: Przekazywanie wirtualnego dysku twardego do platformy Azure przy użyciu Azure PowerShell
description: Dowiedz się, jak przekazać dysk VHD do dysku zarządzanego platformy Azure i skopiować dysk zarządzany między regionami przy użyciu Azure PowerShell.
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 88b5cacf432e467c893dac6fc5839c468b2eafbd
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828659"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Przekazywanie wirtualnego dysku twardego do platformy Azure przy użyciu Azure PowerShell

W tym artykule opisano sposób przekazywania dysku VHD z komputera lokalnego do dysku zarządzanego platformy Azure. Wcześniej musiałeś postępować zgodnie z większym procesem, który obejmuje przemieszczenie danych na konto magazynu, i zarządzanie tym kontem magazynu. Teraz nie trzeba już zarządzać kontem magazynu ani przemieszczać danych w celu przekazania dysku VHD. Zamiast tego należy utworzyć pusty dysk zarządzany i bezpośrednio przekazać do niego dysk VHD. Upraszcza to przekazywanie lokalnych maszyn wirtualnych na platformę Azure i umożliwia przekazywanie wirtualnego dysku twardego do 32 TiB bezpośrednio do dysku zarządzanego.

Jeśli udostępniasz rozwiązanie do tworzenia kopii zapasowych dla maszyn wirtualnych IaaS na platformie Azure, zalecamy użycie funkcji bezpośredniego przekazywania w celu przywrócenia kopii zapasowych klientów na dyskach zarządzanych. W przypadku przekazywania wirtualnego dysku twardego z maszyny zewnętrznej na platformę Azure szybkości z zależą od przepustowości lokalnej. W przypadku korzystania z maszyny wirtualnej platformy Azure przepustowość będzie taka sama jak standardowa HDD.

Obecnie bezpośrednie przekazywanie jest obsługiwane dla standardowego dysku twardego, standardowego SSD i dysków zarządzanych w warstwie Premium SSD. Nie jest jeszcze obsługiwane dla Ultra dysków SSD.

## <a name="prerequisites"></a>Wymagania wstępne

- Pobierz najnowszą [wersję programu AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Zainstaluj moduł Azure PowerShell](/powershell/azure/install-Az-ps).
- Jeśli zamierzasz przekazać dysk VHD z dysku na PEM: dysk VHD, który został [przygotowany na platformie Azure, jest](prepare-for-upload-vhd-image.md)przechowywany lokalnie.
- Lub dysk zarządzany na platformie Azure, jeśli zamierzasz wykonać akcję kopiowania.

## <a name="create-an-empty-managed-disk"></a>Utwórz pusty dysk zarządzany

Aby przekazać dysk VHD na platformę Azure, musisz utworzyć pusty dysk zarządzany skonfigurowany dla tego procesu przekazywania. Przed utworzeniem jednego z tych dysków należy zapoznać się z dodatkowymi informacjami.

Ten rodzaj dysku zarządzanego ma dwa unikatowe Stany:

- ReadToUpload, co oznacza, że dysk jest gotowy do odebrania przekazania, ale nie Wygenerowano [sygnatury bezpiecznego dostępu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, co oznacza, że dysk jest gotowy do odebrania przekazywania i Wygenerowano sygnaturę dostępu współdzielonego.

W każdym z tych stanów dysk zarządzany jest rozliczany zgodnie ze [standardowymi cenami](https://azure.microsoft.com/pricing/details/managed-disks/)dysków twardych, niezależnie od rzeczywistego typu dysku. Na przykład P10 będzie rozliczany jako S10. Ta wartość będzie prawdziwa do momentu wywołania `revoke-access` na dysku zarządzanym, co jest wymagane w celu dołączenia dysku do maszyny wirtualnej.

Przed utworzeniem pustego standardowego dysku twardego do przekazania należy zmienić rozmiar pliku w bajtach wirtualnego dysku twardego, który ma zostać przekazany. Przykładowy kod uzyskasz dla Ciebie, ale aby zrobić to samodzielnie, możesz użyć: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`. Ta wartość jest używana podczas określania parametru **-UploadSizeInBytes** .

Teraz w lokalnej powłoce Utwórz pusty standardowy dysk twardy do przekazania przez określenie ustawienia **przekazywania** w parametrze **-i** parametru, a także parametru **-UploadSizeInBytes** w poleceniu cmdlet [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) . Następnie Wywołaj polecenie [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) , aby utworzyć dysk:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Jeśli chcesz przekazać dysk SSD Premium lub standardowy dysk SSD, Zastąp **Standard_LRS** wartością **Premium_LRS** lub **StandardSSD_LRS**. SSD w warstwie Ultra nie jest jeszcze obsługiwana.

Utworzono pusty dysk zarządzany skonfigurowany dla procesu przekazywania. Aby przekazać dysk VHD do dysku, musisz mieć zapisywalne sygnatury dostępu współdzielonego, aby można było odwołać się do niego jako miejsce docelowe przekazywania.

Aby wygenerować zapisywalne SAS pustego dysku zarządzanego, użyj następującego polecenia:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Przekazywanie wirtualnego dysku twardego

Teraz, gdy masz sygnaturę dostępu współdzielonego dla pustego dysku zarządzanego, możesz użyć jej do ustawienia dysku zarządzanego jako miejsca docelowego dla polecenia przekazywania.

Za pomocą AzCopy v10 Przekaż swój lokalny plik VHD na dysk zarządzany, określając wygenerowany identyfikator URI sygnatury dostępu współdzielonego.

To przekazywanie ma taką samą przepływność jak odpowiednik [standardowego dysku twardego](disks-types.md#standard-hdd). Na przykład jeśli masz rozmiar, który jest równa S4, będziesz mieć przepływność do 60 MiB/s. Ale jeśli masz rozmiar, który jest równa S70, będziesz mieć przepływność do 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Jeśli sygnatura dostępu współdzielonego wygaśnie podczas przekazywania i nie wywołano jeszcze `revoke-access`, można uzyskać nowe sygnatury dostępu współdzielonego, aby kontynuować przekazywanie przy użyciu `grant-access`.

Po zakończeniu przekazywania i nie musisz już pisać więcej danych na dysku, odwołaj sygnaturę dostępu współdzielonego. Odwoływanie sygnatury dostępu współdzielonego spowoduje zmianę stanu dysku zarządzanego i umożliwi dołączenie dysku do maszyny wirtualnej.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Kopiowanie dysku zarządzanego

Bezpośrednie przekazywanie upraszcza również proces kopiowania dysku zarządzanego. Można wykonać kopię w tym samym regionie lub w różnych regionach (do innego regionu).

Wykonanie poniższej skryptu spowoduje to, że proces jest podobny do opisanego wcześniej procedury, z pewnymi różnicami, ponieważ pracujesz z istniejącym dyskiem.

> [!IMPORTANT]
> Należy dodać przesunięcie 512, gdy podajesz rozmiar dysku w bajtach dysku zarządzanego na platformie Azure. Wynika to z faktu, że platforma Azure pomija stopkę podczas zwracania rozmiaru dysku. Kopia nie powiedzie się, jeśli to zrobisz. Poniższy skrypt już robi to za Ciebie.

Zastąp `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourOSTypeHere>` i `<yourTargetLocationHere>` (przykładem wartości lokalizacji byłaby uswest2) wartościami, a następnie uruchom poniższy skrypt w celu skopiowania dysku zarządzanego.

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

Teraz, gdy wirtualny dysk twardy został pomyślnie przekazany do dysku zarządzanego, możesz dołączyć dysk do maszyny wirtualnej i zacząć z niego korzystać.

Aby dowiedzieć się, jak dołączyć dysk do maszyny wirtualnej, zapoznaj się z artykułem na temat: [dołączanie dysku danych do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell](attach-disk-ps.md).
