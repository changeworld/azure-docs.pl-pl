---
title: Tworzenie zarządzanego maszyny Wirtualnej platformy Azure z wirtualnego dysku twardego uogólnionego lokalnymi | Dokumentacja firmy Microsoft
description: Przekazać uogólniony wirtualny dysk twardy na platformie Azure, aby go użyć do utworzenia nowych maszyn wirtualnych, w modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: cynthn
ms.openlocfilehash: 9ebe1f67c7c662af6d9e1888580149834a007200
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657475"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Przekazywanie uogólniony wirtualny dysk twardy i umożliwia tworzenie nowych maszyn wirtualnych na platformie Azure

Ten temat przeprowadzi Cię przez przekazywanie wirtualnego dysku twardego uogólnionego maszyny wirtualnej na platformie Azure, tworzenie obrazu na podstawie wirtualnego dysku twardego i utworzyć nową maszynę Wirtualną z tego obrazu przy użyciu programu PowerShell. Możesz przekazać dysku VHD wyeksportowane z narzędzia wirtualizacji lokalnej lub innej chmury. Przy użyciu [dysków zarządzanych](managed-disks-overview.md) dla nowej maszyny Wirtualnej upraszcza zarządzanie maszyny Wirtualnej i zapewnia większą dostępność, gdy maszyna wirtualna jest umieszczona w zestawie dostępności. 

Jeśli chcesz użyć przykładowego skryptu, zobacz [przykładowy skrypt do przekazania dysku VHD na platformie Azure i utworzyć nową maszynę Wirtualną](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Przed przekazaniem jakiegokolwiek dysku VHD na platformę Azure, należy wykonać [Przygotowywanie wirtualnego dysku twardego Windows lub VHDX do przekazania do platformy Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Przegląd [planowanie migracji do zarządzanych dysków](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) przed rozpoczęciem migracji do [dysków zarządzanych](managed-disks-overview.md).
- W tym artykule wymaga AzureRM wersji modułu 5.6 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM.Compute`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="generalize-the-source-vm-using-sysprep"></a>Generalize źródłowej maszyny Wirtualnej za pomocą programu Sysprep

Narzędzie Sysprep między innymi usuwa wszystkie informacje osobiste związane z kontem i przygotowuje maszynę do używania jako obraz. Aby uzyskać więcej informacji o narzędziu Sysprep, zobacz [Omówienie narzędzia Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Upewnij się, że ról serwera uruchomionych na komputerze są obsługiwane przez program Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa programu Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Jeśli korzystasz z programu Sysprep przed przekazaniem dysk VHD do platformy Azure po raz pierwszy, upewnij się, masz [przygotować maszyny Wirtualnej](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przed uruchomieniem programu Sysprep. 
> 
> 

1. Zaloguj się do maszyny wirtualnej systemu Windows.
2. Otwórz okno wiersza polecenia jako administrator. Zmień katalog na **%windir%\system32\sysprep**, a następnie uruchom `sysprep.exe`.
3. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz pozycję **Włącz systemowy tryb OOBE** i upewnij się, że pole wyboru **Uogólnij** jest zaznaczone.
4. W **opcje zamykania**, wybierz pozycję **zamknięcia**.
5. Kliknij przycisk **OK**.
   
    ![Uruchom program Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Gdy narzędzie Sysprep zakończy działanie, maszyna wirtualna zostanie wyłączona. Nie uruchamiaj ponownie maszyny Wirtualnej.


## <a name="get-the-storage-account"></a>Uzyskaj konto magazynu

Potrzebujesz konta magazynu na platformie Azure do przechowywania załadowanego obrazu maszyny Wirtualnej. Możesz użyć istniejącego konta magazynu lub Utwórz nową. 

Jeśli wirtualny dysk twardy będzie używany do tworzenia zarządzanego dysku dla maszyny Wirtualnej, Lokalizacja konta magazynu musi być w tej samej lokalizacji, w którym zostanie utworzenie maszyny Wirtualnej.

Aby wyświetlić konta dostępny magazyn, wpisz:

```azurepowershell
Get-AzureRmStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Przekazanie dysku VHD do konta magazynu

Użyj [AzureRmVhd Dodaj](https://msdn.microsoft.com/library/mt603554.aspx) polecenia cmdlet w celu przekazania dysku VHD do kontenera na koncie magazynu. W tym przykładzie powoduje przekazanie pliku *myVHD.vhd* z *"dyski twarde C:\Users\Public\Documents\Virtual\"*  na konto magazynu o nazwie *mojekontomagazynu* w *myResourceGroup* grupy zasobów. Plik zostaną umieszczone w kontenerze o nazwie *mojkontener* i Nowa nazwa pliku będzie *myUploadedVHD.vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


W przypadku powodzenia można uzyskać odpowiedzi, która wygląda podobnie do poniższego:

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

W zależności od połączenia sieciowego i rozmiar pliku VHD to polecenie może zająć trochę czasu, aby zakończyć

### <a name="other-options-for-uploading-a-vhd"></a>Inne opcje przekazywanie wirtualnego dysku twardego
 
Możesz również przekazywać dysku VHD do konta magazynu przy użyciu jednej z następujących czynności:

- [Narzędzie AzCopy](http://aka.ms/downloadazcopy)
- [Obiektu Blob magazynu Azure kopiowania interfejsu API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Obiekty BLOB magazynu Azure Explorer przekazywania](https://azurestorageexplorer.codeplex.com/)
- [Dokumentacja interfejsu API REST usługi Import/Eksport magazynu](https://msdn.microsoft.com/library/dn529096.aspx)
-   Zaleca się za pomocą usługi Import/eksport, jeśli szacowany czas przekazywania jest dłuższa niż 7 dni. Można użyć [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) oszacowanie godzinę z jednostki rozmiaru i transferu danych. 
    Narzędzie importu/eksportu może służyć do skopiowania do konta magazynu w warstwie standardowa. Należy skopiować z magazynu w warstwie standardowa do konta magazynu premium za pomocą narzędzia, takiego jak narzędzie AzCopy.

> [!IMPORTANT]
> Jeśli używasz narzędzia AzCopy przekazywania dysk VHD na platformę Azure, upewnij się, ustawiono [/BlobType:page](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) przed uruchomieniem Przekaż skryptu. Jeśli obiektem docelowym jest obiektu blob, a ta opcja nie jest określona, domyślnie AzCopy tworzy blokowych obiektów blob.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Tworzenie zarządzanego obrazu z przekazanego wirtualnego dysku twardego 

Tworzenie obrazu zarządzanych za pomocą programu uogólniony wirtualny dysk twardy systemu operacyjnego. Zastąp wartości odpowiednimi informacjami.


Najpierw należy ustawić niektóre parametry:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Utworzyć obraz przy użyciu programu uogólniony wirtualny dysk twardy systemu operacyjnego.

```powershell
$imageConfig = New-AzureRmImageConfig `
   -Location $location
$imageConfig = Set-AzureRmImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzureRmImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Teraz, gdy masz już obraz, możesz za jego pomocą utworzyć jedną lub więcej nowych maszyn wirtualnych. To przykładowe polecenie tworzy Maszynę wirtualną o nazwie *myVM* z *myImage*w *myResourceGroup*.


```powershell
New-AzureRmVm `
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


## <a name="next-steps"></a>Kolejne kroki

Zaloguj się do nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [jak połączenia i zaloguj się do maszyny wirtualnej platformy Azure systemem Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

