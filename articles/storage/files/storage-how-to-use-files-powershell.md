---
title: Zarządzanie udziałami plików platformy Azure przy użyciu programu Azure PowerShell
description: Dowiedz się, jak zarządzać udziałami plików platformy Azure przy użyciu programu Azure PowerShell.
services: storage
author: wmgries
ms.service: storage
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: a69e8946c050cc69411f843b07e907a119c6edea
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522933"
---
# <a name="managing-azure-file-shares-with-azure-powershell"></a>Zarządzanie udziałami plików platformy Azure przy użyciu programu Azure PowerShell 
[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure można instalować w systemach Windows, Linux i macOS. W tym przewodniku przedstawiono podstawowe informacje dotyczące pracy z udziałami plików platformy Azure przy użyciu programu PowerShell. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie grupy zasobów i konta magazynu
> * Tworzenie udziału plików platformy Azure 
> * Tworzenie katalogu
> * Przekazywanie pliku 
> * Pobieranie pliku
> * Tworzenie i używanie migawki udziału

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli chcesz zainstalować program PowerShell i używać go lokalnie, ten przewodnik wymaga modułu Azure PowerShell w wersji 5.1.1 lub nowszej. Aby dowiedzieć się, jaka wersja modułu Azure PowerShell jest uruchomiona, wykonaj polecenie `Get-Module -ListAvailable AzureRM`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Jeśli nie masz jeszcze grupy zasobów platformy Azure, możesz utworzyć nową grupę za pomocą polecenia cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w regionie East US (Wschodnie stany USA):

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Konto magazynu to udostępniona pula magazynu, której można używać do wdrażania udziałów plików platformy Azure lub innych zasobów magazynu, takich jak obiekty Blob i kolejki. Konto magazynu może zawierać nieograniczoną liczbę udziałów, a udział może obejmować nieograniczoną liczbę plików, nieprzekraczającą limitów pojemności konta magazynu.

Ten przykład obejmuje tworzenie konta magazynu o nazwie `mystorageacct<random number>` i umieszczenie odwołania do tego konta magazynu w zmiennej **$storageAcct**. Nazwy kont magazynów muszą być unikatowe, użyj zatem polecenia `Get-Random` w celu dołączenia liczby do nazwy, aby była unikatowa. 

```azurepowershell-interactive 
$storageAcct = New-AzureRmStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Teraz możesz utworzyć swój pierwszy udział plików platformy Azure. Udział plików możesz utworzyć za pomocą polecenia cmdlet [New-AzureStorageShare](/powershell/module/azurerm.storage/new-azurestorageshare). W tym przykładzie tworzony jest udział o nazwie `myshare`.

```azurepowershell-interactive
New-AzureStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

> [!Important]  
> Nazwy udziałów muszą składać się z małych liter, cyfr i pojedynczych łączników, ale nie mogą zaczynać się od łącznika. Szczegółowe informacje o nazwach plików i udziałów plików można znaleźć w temacie [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Nazywanie i odwoływanie się do udziałów, katalogów, plików i metadanych).

## <a name="work-with-the-contents-of-the-azure-file-share"></a>Praca z zawartością udziału plików platformy Azure
Po utworzeniu udziału plików platformy Azure możesz zainstalować ten udział plików w systemie [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) lub [macOS](storage-how-to-use-files-mac.md) za pomocą protokołu SMB. Możesz też pracować z udziałem plików platformy Azure przy użyciu modułu Azure PowerShell. Jest to korzystniejsze od instalowania udziału plików za pomocą protokołu SMB, ponieważ wszystkie żądania przekazywane za pośrednictwem programu PowerShell są przekazywane przy użyciu interfejsu API REST usługi Pliki, co umożliwia tworzenie, modyfikowanie oraz usuwanie plików i katalogów w udziale plików z następujących źródeł:

- Program PowerShell Cloud Shell (który nie może instalować udziałów plików za pośrednictwem protokołu SMB).
- Klienci, którzy nie mogą instalować udziałów SMB, tacy jak klienci lokalni bez odblokowanego portu 445.
- Scenariusze bez serwerów, tak jak w przypadku rozwiązania [Azure Functions](../../azure-functions/functions-overview.md). 


### <a name="create-directory"></a>Tworzenie katalogu
Aby utworzyć nowy katalog o nazwie *myDirectory* w katalogu głównym udziału plików platformy Azure, użyj polecenia cmdlet [New-AzureStorageDirectory](/powershell/module/azurerm.storage/new-azurestoragedirectory).


```azurepowershell-interactive
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

### <a name="upload-a-file"></a>Przekazywanie pliku
Aby zademonstrować przekazywanie pliku przy użyciu polecenia cmdlet [Set-AzureStorageFileContent](/powershell/module/azure.storage/set-azurestoragefilecontent), najpierw należy utworzyć plik do przekazania w obrębie dysku tymczasowego usługi Cloud Shell dla programu PowerShell. 

Ten przykład obejmuje umieszczenie bieżącej daty i godziny w nowym pliku na dysku tymczasowym, a następnie przekazanie tego pliku do udziału plików.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzureStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Jeśli korzystasz z programu PowerShell lokalnie, zastąp ciąg `C:\Users\ContainerAdministrator\CloudDrive\` ścieżką, która istnieje na Twoim komputerze.

Po przekazaniu pliku możesz upewnić się, że plik został przekazany do udziału plików platformy Azure, korzystając z polecenia cmdlet [Get-AzureStorageFile](/powershell/module/Azure.Storage/Get-AzureStorageFile). 

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

### <a name="download-a-file"></a>Pobieranie pliku
Za pomocą polecenia cmdlet [Get-AzureStorageFileContent](/powershell/module/azure.storage/get-azurestoragefilecontent) możesz pobrać kopię pliku, który właśnie został przekazany na dysk tymczasowy usługi Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item 
    `-Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzureStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

Po pobraniu pliku możesz sprawdzić, czy plik został pobrany na dysk tymczasowy programu PowerShell Cloud Shell, za pomocą polecenia `Get-ChildItem`.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

### <a name="copy-files"></a>Kopiowanie plików
Jednym z często wykonywanych zadań jest kopiowanie plików z jednego udziału plików do innego lub do/z kontenera usługi Azure Blob Storage. Aby zademonstrować tę funkcję, możesz utworzyć nowy udział i skopiować właśnie przekazany plik do tego nowego udziału przy użyciu polecenia cmdlet [Start-AzureStorageFileCopy](/powershell/module/azure.storage/start-azurestoragefilecopy). 

```azurepowershell-interactive
New-AzureStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzureStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Teraz, po wyświetleniu listy plików w nowym udziale, skopiowany plik powinien być widoczny.

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Polecenie cmdlet `Start-AzureStorageFileCopy` jest wygodne w przypadku przenoszenia plików ad-hoc między udziałami plików platformy Azure i kontenerami usługi Azure Blob Storage, jednak do przenoszenia większej liczby plików lub plików o większych rozmiarach zalecamy używanie narzędzia AzCopy. Dowiedz się więcej o [narzędziu AzCopy dla systemu Windows](../common/storage-use-azcopy.md) i [narzędziu AzCopy dla systemu Linux](../common/storage-use-azcopy-linux.md). Narzędzie AzCopy musi być zainstalowane lokalnie — nie jest dostępne w usłudze Cloud Shell. 

## <a name="create-and-modify-share-snapshots"></a>Tworzenie i modyfikowanie migawek udziałów
Jedną z dodatkowych przydatnych czynności, które można wykonywać na udziałach plików platformy Azure, jest tworzenie migawek udziałów. Migawka zachowuje określony moment w czasie dla udziału plików platformy Azure. Migawki udziałów są podobne do technologii systemów operacyjnych, które być może już znasz, takich jak:
- [Usługa kopiowania woluminów w tle (VSS)](https://docs.microsoft.com/en-us/windows/desktop/VSS/volume-shadow-copy-service-portal) dla systemów plików systemu Windows, takich jak NTFS i ReFS
- Migawki [Menedżera woluminów logicznych (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) dla systemu Linux
- Migawki [Systemu plików firmy Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) dla systemu macOS 

Migawkę dla udziału można utworzyć, używając metody `Snapshot` na obiekcie programu PowerShell dla udziału plików, który jest pobierany za pomocą polecenia cmdlet [Get-AzureStorageShare](/powershell/module/azure.storage/get-azurestorageshare). 

```azurepowershell-interactive
$share = Get-AzureStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Przeglądanie migawek udziałów
Aby przeglądać zawartość migawki udziału, należy przekazać odwołanie do migawki (`$snapshot`) do parametru `-Share` polecenia cmdlet `Get-AzureStorageFile`.

```azurepowershell-interactive
Get-AzureStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Wyświetlanie listy migawek udziałów
Za pomocą następującego polecenia można wyświetlić listę migawek wykonanych dla udziału.

```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Przywracanie na podstawie migawki udziału
Aby przywrócić plik, użyj polecenia `Start-AzureStorageFileCopy`, którego używaliśmy wcześniej. Na potrzeby tego przewodnika Szybki start najpierw usuniemy nasz plik `SampleUpload.txt`, który wcześniej przekazaliśmy, aby można go było przywrócić na podstawie migawki.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzureStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzureStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Usuwanie migawki udziału
Aby usunąć migawkę udziału, użyj polecenia cmdlet [Remove-AzureStorageShare](/powershell/module/azure.storage/remove-azurestorageshare) ze zmienną zawierającą odwołanie `$snapshot` do parametru `-Share`.

```azurepowershell-interactive
Remove-AzureStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy skończysz, możesz usunąć grupę zasobów i wszystkie pokrewne zasoby za pomocą polecenia cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Zasoby możesz również usuwać pojedynczo:

- Aby usunąć udziały plików platformy Azure utworzone na potrzeby tego przewodnika Szybki start.
```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
    Remove-AzureStorageShare -Context $storageAcct.Context -Name $_.Name
}
```

- Aby usunąć konto magazynu (spowoduje to niejawne usunięcie utworzonych udziałów plików platformy Azure oraz wszelkich innych zasobów magazynu, które mogły zostać utworzone, takich jak kontener usługi Azure Blob Storage).
```azurepowershell-interactive
Remove-AzureRmStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
```

## <a name="next-steps"></a>Następne kroki
- [Zarządzanie udziałami plików za pomocą witryny Azure Portal](storage-how-to-use-files-portal.md)
- [Zarządzanie udziałami plików za pomocą interfejsu wiersza polecenia platformy Azure](storage-how-to-use-files-cli.md)
- [Zarządzanie udziałami plików za pomocą Eksploratora usługi Storage](storage-how-to-use-files-storage-explorer.md)
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
