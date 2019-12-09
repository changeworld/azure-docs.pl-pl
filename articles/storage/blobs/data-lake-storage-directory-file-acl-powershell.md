---
title: Korzystanie z programu PowerShell dla plików & list ACL w Azure Data Lake Storage Gen2 (wersja zapoznawcza)
description: Polecenia cmdlet programu PowerShell umożliwiają zarządzanie katalogami oraz listami kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 360845d75c27fab0151c3a64846ff788f9f56b95
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931241"
---
# <a name="use-powershell-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Korzystanie z programu PowerShell dla plików & list ACL w Azure Data Lake Storage Gen2 (wersja zapoznawcza)

W tym artykule przedstawiono sposób użycia programu PowerShell do tworzenia katalogów, plików i uprawnień w ramach kont magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS), oraz do zarządzania nimi. 

> [!IMPORTANT]
> Moduł programu PowerShell, który jest proponowany w tym artykule, jest obecnie w publicznej wersji zapoznawczej.

[Mapowanie Gen1 do Gen2](#gen1-gen2-map) | [Przekaż opinię](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz artykuł [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.
> * .NET Framework jest 4.7.2 lub większa. Zobacz [pobieranie .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Program PowerShell w wersji `5.1` lub nowszej.

## <a name="install-powershell-modules"></a>Zainstaluj moduły programu PowerShell

1. Sprawdź, czy zainstalowana wersja programu PowerShell to `5.1` lub nowsza przy użyciu następującego polecenia. 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    Aby uaktualnić wersję programu PowerShell, zobacz [uaktualnianie istniejącego środowiska Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Zainstaluj najnowszy moduł **PowershellGet** . Następnie zamknij i ponownie otwórz konsolę programu PowerShell.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  Zainstaluj **AZ. Storage** Preview module.

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    Aby uzyskać więcej informacji na temat sposobu instalowania modułów programu PowerShell, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Połącz z kontem

1. Otwórz okno poleceń programu Windows PowerShell.

2. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

3. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, w którym chcesz utworzyć katalogi i zarządzać nimi.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```

   Zastąp wartość symbolu zastępczego `<subscription-id>` IDENTYFIKATORem subskrypcji.

4. Pobierz konto magazynu.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   ```

   * Zastąp wartość symbolu zastępczego `<resource-group-name>` nazwą grupy zasobów.

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

5. Pobierz kontekst konta magazynu.

   ```powershell
   $ctx = $storageAccount.Context
   ```

## <a name="create-a-file-system"></a>Tworzenie systemu plików

System plików działa jako kontener dla plików. Można go utworzyć za pomocą polecenia cmdlet `New-AzDatalakeGen2FileSystem`. 

Ten przykład tworzy system plików o nazwie `my-file-system`.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu za pomocą polecenia cmdlet `New-AzDataLakeGen2Item`. 

Ten przykład dodaje katalog o nazwie `my-directory` do systemu plików.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Ten przykład dodaje ten sam katalog, ale również ustawia uprawnienia, maska umask, wartości właściwości i wartości metadanych. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Pokaż właściwości katalogu

Ten przykład pobiera katalog przy użyciu polecenia cmdlet `Get-AzDataLakeGen2Item`, a następnie drukuje wartości właściwości w konsoli programu.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Directory.PathProperties.Group
$dir.Directory.PathProperties.Owner
$dir.Directory.Metadata
$dir.Directory.Properties
```

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub Przenieś katalog przy użyciu polecenia cmdlet `Move-AzDataLakeGen2Item`.

Ten przykład zmienia nazwę katalogu na podstawie nazwy `my-directory` na `my-new-directory`nazwy.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

Ten przykład przenosi katalog o nazwie `my-directory` do podkatalogu `my-directory-2` o nazwie `my-subdirectory`. W tym przykładzie stosuje się również maska umask do podkatalogu.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog przy użyciu polecenia cmdlet `Remove-AzDataLakeGen2Item`.

Ten przykład usuwa katalog o nazwie `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Możesz użyć parametru `-Force`, aby usunąć plik bez monitu.

## <a name="download-from-a-directory"></a>Pobierz z katalogu

Pobierz plik z katalogu za pomocą polecenia cmdlet `Get-AzDataLakeGen2ItemContent`.

Ten przykład pobiera plik o nazwie `upload.txt` z katalogu o nazwie `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Wyświetl zawartość katalogu za pomocą polecenia cmdlet `Get-AzDataLakeGen2ChildItem`.

W tym przykładzie wymieniono zawartość katalogu o nazwie `my-directory`. 

Aby wyświetlić listę zawartości systemu plików, Pomiń parametr `-Path` w poleceniu.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

Ten przykład zawiera listę zawartości katalogu o nazwie `my-directory` i zawiera listy ACL na liście. Używa także parametru `-Recurse`, aby wyświetlić listę zawartości wszystkich podkatalogów.

Aby wyświetlić listę zawartości systemu plików, Pomiń parametr `-Path` w poleceniu.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
```

## <a name="upload-a-file-to-a-directory"></a>Przekaż plik do katalogu

Przekaż plik do katalogu za pomocą polecenia cmdlet `New-AzDataLakeGen2Item`.

Ten przykład przekazuje plik o nazwie `upload.txt` do katalogu o nazwie `my-directory`. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Ten przykład przekazuje ten sam plik, ale następnie ustawia uprawnienia, maska umask, wartości właściwości i wartości metadanych pliku docelowego. Ten przykład również drukuje te wartości w konsoli programu.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>Pokaż właściwości pliku

Ten przykład pobiera plik za pomocą polecenia cmdlet `Get-AzDataLakeGen2Item`, a następnie drukuje wartości właściwości w konsoli programu.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.File.PathProperties.Group
$file.File.PathProperties.Owner
$file.File.Metadata
$file.File.Properties
```

## <a name="delete-a-file"></a>Usuwanie pliku

Usuń plik za pomocą polecenia cmdlet `Remove-AzDataLakeGen2Item`.

Ten przykład usuwa plik o nazwie `upload.txt`. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Możesz użyć parametru `-Force`, aby usunąć plik bez monitu.

## <a name="manage-access-permissions"></a>Zarządzanie uprawnieniami dostępu

Uprawnienia dostępu do katalogów i plików można uzyskiwać, ustawiać i aktualizować.

### <a name="get-directory-and-file-permissions"></a>Uzyskiwanie uprawnień do katalogów i plików

Pobierz listę kontroli dostępu do katalogu lub pliku za pomocą polecenia cmdlet `Get-AzDataLakeGen2Item`.

Ten przykład pobiera listę ACL **katalogu**, a następnie drukuje listę kontroli dostępu do konsoli programu.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Ten przykład pobiera listę ACL **pliku** , a następnie drukuje listę kontroli dostępu do konsoli programu.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Na poniższej ilustracji przedstawiono dane wyjściowe po uzyskaniu listy ACL katalogu.

![Pobieranie danych wyjściowych listy ACL](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

W tym przykładzie użytkownik będący właścicielem ma uprawnienia do odczytu, zapisu i wykonania. Grupa będąca właścicielem ma tylko uprawnienia do odczytu i wykonywania. Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Ustawianie uprawnień do katalogów i plików

Użyj polecenia cmdlet `New-AzDataLakeGen2ItemAclObject`, aby utworzyć listę ACL dla użytkownika będącego właścicielem, grupy będącej właścicielem lub innych użytkowników. Następnie należy użyć polecenia cmdlet `Update-AzDataLakeGen2Item`, aby zatwierdzić listę kontroli dostępu.

Ten przykład ustawia listę ACL dla **katalogu** dla użytkownika będącego właścicielem, grupy będącej właścicielem lub innych użytkowników, a następnie drukuje listę kontroli dostępu do konsoli programu.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
Ten przykład ustawia listę ACL dla **pliku** dla użytkownika będącego właścicielem, grupy będącej właścicielem lub innych użytkowników, a następnie drukuje listę kontroli dostępu do konsoli programu.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Na poniższej ilustracji przedstawiono dane wyjściowe po ustawieniu listy ACL pliku.

![Pobieranie danych wyjściowych listy ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

W tym przykładzie użytkownik będący właścicielem i grupa będąca właścicielem mają tylko uprawnienia do odczytu i zapisu. Wszyscy inni użytkownicy mają uprawnienia do zapisu i wykonywania. Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Aktualizowanie uprawnień do katalogów i plików

Użyj polecenia cmdlet `Get-AzDataLakeGen2Item`, aby pobrać listę kontroli dostępu do katalogu lub pliku. Następnie użyj polecenia cmdlet `New-AzDataLakeGen2ItemAclObject`, aby utworzyć nowy wpis listy ACL. Użyj polecenia cmdlet `Update-AzDataLakeGen2Item`, aby zastosować nową listę ACL.

Ten przykład daje użytkownikowi uprawnienia do zapisu i wykonywania w katalogu.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```
Ten przykład daje użytkownikowi uprawnienia do zapisu i wykonania na pliku.

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $acl
```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>Ustawianie uprawnień dla wszystkich elementów w systemie plików

Można użyć `Get-AzDataLakeGen2Item` i parametru `-Recurse` razem z poleceniem cmdlet `Update-AzDataLakeGen2Item`, aby cyklicznie ustawić listę kontroli dostępu dla wszystkich katalogów i plików w systemie plików. 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Mapowanie Gen1 do Gen2

W poniższej tabeli przedstawiono sposób używania poleceń cmdlet do Data Lake Storage Gen1 mapowania poleceń cmdlet dla Data Lake Storage Gen2.

|Data Lake Storage Gen1 polecenie cmdlet| Data Lake Storage Gen2 polecenie cmdlet| Uwagi |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Domyślnie polecenie cmdlet Get-AzDataLakeGen2ChildItem wyświetla tylko elementy podrzędne pierwszego poziomu. Parametr-rekursywnie wyświetla listę elementów podrzędnych rekursywnie. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Elementy wyjściowe polecenia cmdlet Get-AzDataLakeGen2Item mają następujące właściwości: list ACL, właściciel, Grupa, uprawnienie.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Polecenie cmdlet Get-AzDataLakeGen2FileContent pobiera zawartość pliku do pliku lokalnego.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|To polecenie cmdlet przekazuje nową zawartość pliku z pliku lokalnego.|
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Polecenie cmdlet Update-AzDataLakeGen2Item aktualizuje tylko jeden element i nie rekursywnie. Jeśli chcesz zaktualizować cyklicznie, Wyświetl listę elementów za pomocą polecenia cmdlet Get-AzDataLakeStoreChildItem, a następnie potoku do polecenia cmdlet Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Polecenie cmdlet Get-AzDataLakeGen2Item zgłosi błąd, jeśli element nie istnieje.|



## <a name="see-also"></a>Zobacz także

* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Używanie Azure PowerShell z usługą Azure Storage](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
* [Polecenia cmdlet programu PowerShell magazynu](/powershell/module/az.storage).

