---
title: Tworzenie zarządzanej maszyny Wirtualnej platformy Azure na podstawie wirtualnego dysku twardego uogólnionego lokalnych | Dokumentacja firmy Microsoft
description: Przekazywanie uogólnionego wirtualnego dysku twardego do systemu Azure i użyć go do utworzenia nowych maszyn wirtualnych, w modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: 9846bf7b28f1205f98eb59671553d309fe754d30
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707936"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Przekazywanie uogólnionego wirtualnego dysku twardego i użyć go do utworzenia nowych maszyn wirtualnych na platformie Azure

Ten artykuł przeprowadzi przy użyciu programu PowerShell do przekazania dysku VHD uogólnionej maszyny wirtualnej na platformie Azure, utworzyć obraz z wirtualnego dysku twardego i tworzenie nowej maszyny Wirtualnej na podstawie tego obrazu. Możesz przekazać wirtualny dysk twardy wyeksportowane z narzędzia wirtualizacji w środowisku lokalnym lub innej chmury. Za pomocą [Managed Disks](managed-disks-overview.md) dla nowej maszyny Wirtualnej upraszcza zarządzanie maszyny Wirtualnej i zapewnia lepszą dostępność, gdy maszyna wirtualna jest umieszczany w zestawie dostępności. 

Aby uzyskać przykładowy skrypt, zobacz [przykładowy skrypt w celu przekazania dysku VHD na platformie Azure i Utwórz nową maszynę Wirtualną](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Przed przekazaniem jakiegokolwiek dysku VHD na platformie Azure, należy przestrzegać [przygotowanie Windows dysku VHD lub VHDX można przekazać na platformę Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Przegląd [zaplanować migrację do usługi Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) przed rozpoczęciem migracji do [Managed Disks](managed-disks-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Uogólnianie źródłowej maszyny Wirtualnej przy użyciu narzędzia Sysprep

Narzędzie Sysprep między innymi usuwa wszystkie informacje osobiste związane z kontem i przygotowuje maszynę do używania jako obraz. Aby uzyskać szczegółowe informacje o narzędziu Sysprep, zobacz [Omówienie programu Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Upewnij się, że role serwera uruchomionego na maszynie są obsługiwane przez program Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa narzędzia Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Jeśli użytkownik chce uruchomić programu Sysprep przed przekazaniem wirtualnego dysku twardego na platformie Azure po raz pierwszy, upewnij się, masz [przygotować maszyny Wirtualnej](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Zaloguj się do maszyny wirtualnej Windows.
2. Otwórz okno wiersza polecenia jako administrator. Zmień katalog na % windir%\system32\sysprep, a następnie uruchom `sysprep.exe`.
3. W **narzędzie przygotowywania systemu** okno dialogowe, wybierz opcję **wprowadź System Out-of-Box środowiska (OOBE)** i upewnij się, że **Generalize** pole wyboru jest włączone.
4. Aby uzyskać **opcje zamykania**, wybierz opcję **zamknięcia**.
5. Kliknij przycisk **OK**.
   
    ![Uruchom program Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Po zakończeniu zamknięcie maszyny wirtualnej. Nie uruchamiaj ponownie maszyny Wirtualnej.


## <a name="get-a-storage-account"></a>Utwórz konto magazynu

Musisz mieć konto magazynu, na platformie Azure do przechowywania przekazanego obrazu maszyny Wirtualnej. Możesz użyć istniejącego konta magazynu lub Utwórz nową. 

Jeśli wirtualny dysk twardy będzie używany do utworzenia dysku zarządzanego dla maszyny Wirtualnej, Lokalizacja konta magazynu musi być w tej samej lokalizacji, w którym zostanie utworzona maszyna wirtualna.

Aby wyświetlić konta dostępnego magazynu, wpisz:

```azurepowershell
Get-AzStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Przekazanie dysku VHD do konta magazynu

Użyj [AzVhd Dodaj](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) polecenia cmdlet do przekazania dysku VHD do kontenera na koncie magazynu. Ten przykładowy przekazuje plik *myVHD.vhd* z *dyski twarde C:\Users\Public\Documents\Virtual\\*  na konto magazynu o nazwie *mystorageaccount* w *myResourceGroup* grupy zasobów. Plik zostanie umieszczony w kontenerze o nazwie *mycontainer* nową nazwę pliku. zostanie ona *myUploadedVHD.vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Jeśli to się powiedzie, otrzymasz odpowiedź, która wygląda podobnie do następującej:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

W zależności od połączenia sieciowego i rozmiar pliku wirtualnego dysku twardego tego polecenia może potrwać trochę czasu.

### <a name="other-options-for-uploading-a-vhd"></a>Inne opcje do przekazania dysku VHD
 
Możesz również przekazać dysku VHD do konta magazynu przy użyciu jednej z następujących czynności:

- [Narzędzie AzCopy](https://aka.ms/downloadazcopy)
- [Usługa Azure Storage obiektu Blob kopiowania interfejsu API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Przekazywanie obiektów blob Eksploratora usługi Azure Storage](https://azurestorageexplorer.codeplex.com/)
- [Dokumentacja interfejsu API REST usługi Import/Export magazynu](https://msdn.microsoft.com/library/dn529096.aspx)
-   Firma Microsoft zaleca, za pomocą usługi Import/Export, jeśli szacowany czas przekazywania jest dłuższy niż 7 dni. Możesz użyć [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) można oszacować, na godzinę z jednostek rozmiaru i transfer danych. 
    Import/Export może służyć do skopiowania do konta magazynu w warstwie standardowa. Należy skopiować z magazynu standard storage do konta usługi premium storage przy użyciu narzędzia, takiego jak narzędzie AzCopy.

> [!IMPORTANT]
> Jeśli przekazywanie wirtualnego dysku twardego do systemu Azure przy użyciu narzędzia AzCopy upewnij się, zostało ustawione [ **/BlobType:page** ](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs#upload-a-file) przed uruchomieniem skryptu przekazywania. Jeśli obiektem docelowym jest obiekt blob, a ta opcja nie jest określona, narzędzie AzCopy domyślnie tworzy blokowych obiektów blob.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Tworzenie obrazu zarządzanego na podstawie przekazanych wirtualnego dysku twardego 

Utworzenie obrazu zarządzanego z uogólnionego wirtualnego dysku twardego systemu operacyjnego. Zastąp następujące wartości, podając własne informacje.


Najpierw należy ustawić niektóre parametry:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Tworzenie obrazu przy użyciu uogólnionego wirtualnego dysku twardego systemu operacyjnego.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Teraz, gdy masz już obraz, możesz za jego pomocą utworzyć jedną lub więcej nowych maszyn wirtualnych. W tym przykładzie utworzono maszynę Wirtualną o nazwie *myVM* z *myImage*w *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Następne kroki

Zaloguj się do swojej nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [jak połączyć i zaloguj się na maszynie wirtualnej platformy Azure, systemem Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

