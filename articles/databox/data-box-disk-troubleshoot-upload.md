---
title: Dane dotyczące rozwiązywania problemów z dysku Data Box Azure Przekaż problemów przy użyciu dzienników | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak używać dzienników i rozwiązywanie problemów występujących podczas przekazywania danych do dysku Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: deaa9a220ee4d765650779b40742225e300ffdb7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807493"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Zrozumienie dzienniki rozwiązywania problemów przekazywania danych z dysku Azure Data Box

Ten artykuł ma zastosowanie do dysku systemu Microsoft Azure Data Box i opisano problemy, które zobaczysz podczas przekazywania danych na platformie Azure.

## <a name="about-upload-logs"></a>Przekaż dzienniki — informacje

Gdy dane są przekazywane do platformy Azure w centrum danych, `_error.xml` i `_verbose.xml` pliki są generowane dla każdego konta magazynu. Te dzienniki są przekazywane do tego samego konta magazynu, który został użyty do przekazania danych. 

Zarówno Dzienniki znajdują się w tym samym formacie i zawierają opisy XML zdarzenia, które wystąpiły podczas kopiowania danych z dysku do konta usługi Azure Storage.

Pełny dziennik zawiera pełne informacje na temat stanu operacji kopiowania dla każdego obiektu blob lub pliku dziennika błędów zawiera tylko informacje dotyczące obiektów blob lub pliki, które napotkały błędy podczas przekazywania.

Dziennik błędów ma tę samą strukturę co pełny dziennik, ale odfiltrowuje operacje zakończone powodzeniem.

## <a name="download-logs"></a>Pobieranie dzienników

Wykonaj następujące kroki, aby zlokalizować Przekaż dzienniki.

1. Jeśli występują błędy podczas przekazywania danych na platformie Azure, portal zawiera ścieżkę do folderu, w którym znajdują się dzienniki diagnostyczne.

    ![Połącz z dzienników w portalu](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Przejdź do **waies**.

    ![błąd i pełne dzienniki](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

W każdym przypadku Zobacz dzienniki błędów i pełne dzienniki. Wybierz każdego dziennika i Pobierz kopię lokalną.

## <a name="sample-upload-logs"></a>Przykład Przekaż dzienniki

Próbka `_verbose.xml` znajdują się poniżej. W takim przypadku zostanie zakończone pomyślnie bez błędów.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

Dla tej samej kolejności, przykładowe `_error.xml` znajdują się poniżej.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Próbka `_error.xml` znajdują się poniżej miejsca kolejność została ukończona z błędami. 

Plik błędu w tym przypadku ma `Summary` i innej sekcji, która zawiera wszystkich plików na poziomie błędy. 

`Summary` Zawiera `ValidationErrors` i `CopyErrors`. W tym przypadku 8 pliki lub foldery, które zostały przekazane do platformy Azure i nie było żadnych błędów sprawdzania poprawności. Jeśli dane zostały skopiowane do konta usługi Azure Storage, 5 plików lub folderów został pomyślnie przekazany. Pozostałe 3 pliki lub foldery zostały zmieniana zgodnie z konwencjami nazewnictwa usługi Azure container i pomyślnie przekazana, na platformie Azure.

Stan poziomu pliku znajdują się w `BlobStatus` opisujący wszelkie akcje podjęte w celu przekazywania obiektów blob. W tym przypadku trzy kontenery zostały zmienione, ponieważ foldery, których dane zostały skopiowane nie jest zgodny z konwencjami nazewnictwa platformy Azure dla kontenerów. Dla obiektów blob przekazanych w tych kontenerach nową nazwę kontenera, ścieżka obiektu blob na platformie Azure, oryginalnym Nieprawidłowa ścieżka pliku i rozmiar obiektu blob są dołączone.
    
```xml
 <?xml version="1.0" encoding="utf-8"?>
    <DriveLog Version="2018-10-01">
      <DriveId>18041C582D7E</DriveId>
      <Summary>
     <!--Summary for validation and data copy to Azure -->
        <ValidationErrors>
          <None Count="8" />
        </ValidationErrors>
        <CopyErrors>
          <Completed Count="5" Description="No errors encountered" />
          <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
        </CopyErrors>
      </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
        <SizeInBytes>71528448</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Status>CompletedWithErrors</Status>
    </DriveLog>
```

## <a name="data-upload-errors"></a>Błędy przekazywania danych

Błędy generowane podczas przekazywania danych na platformie Azure są podsumowane w poniższej tabeli.

| Kod błędu | Opis                   |
|-------------|------------------------------|
|`None` |  Zakończone pomyślnie.           |
|`Renamed` | Pomyślnie zmieniono nazwę obiektu blob.   |
|`CompletedWithErrors` | Przekazywanie zostało zakończone z błędami. Szczegóły dotyczące plików w wyniku błędu znajdują się w pliku dziennika.  |
|`Corrupted`|CRC obliczane podczas pozyskiwania danych nie jest zgodna CRC obliczane podczas przekazywania.  |  
|`StorageRequestFailed` | Usługa Azure storage, żądanie nie powiodło się.   |     
|`LeasePresent` | Ten element jest dzierżawiony i jest on używany przez innego użytkownika. |
|`StorageRequestForbidden` |Nie można przekazać ze względu na problemy z uwierzytelnianiem. |
|`ManagedDiskCreationTerminalFailure` | Nie można przekazać jako dysków zarządzanych. Pliki są dostępne na koncie magazynu przejściowego jako stronicowe obiekty BLOB. Można ręcznie przekonwertować stronicowe obiekty BLOB do usługi managed disks.  |
|`DiskConversionNotStartedTierInfoMissing` | Ponieważ poza folderów precreated warstwy został skopiowany plik wirtualnego dysku twardego, dysk zarządzany nie został utworzony. Plik jest przekazywany jako stronicowy obiekt blob do konta magazynu przejściowego podaną podczas tworzenia zamówienia. Możesz przekonwertować go ręcznie, na dysk zarządzany.|
|`InvalidWorkitem` | Nie można przekazać danych, ponieważ nie jest zgodny z nazewnictwa platformy Azure i ogranicza Konwencji.|
|`InvalidPageBlobUploadAsBlockBlob` | Przekazany jako blokowe obiekty BLOB w kontenerze z prefiksem `databoxdisk-invalid-pb-`.|
|`InvalidAzureFileUploadAsBlockBlob` | Przekazany jako blokowe obiekty BLOB w kontenerze z prefiksem `databoxdisk-invalid-af`—.|
|`InvalidManagedDiskUploadAsBlockBlob` | Przekazany jako blokowe obiekty BLOB w kontenerze z prefiksem `databoxdisk-invalid-md`—.|
|`InvalidManagedDiskUploadAsPageBlob` |Przekazany jako stronicowe obiekty BLOB w kontenerze z prefiksem `databoxdisk-invalid-md-`. |
|`MovedToOverflowShare` |Przekazane pliki do nowego udziału jako oryginalnego rozmiaru udziału przekracza limit maksymalnego rozmiaru platformy Azure. Nazwa nowego udziału plików ma oryginalną nazwę z sufiksem `-2`.   |
|`MovedToDefaultAzureShare` |Przekazane pliki, które nie zostały w ramach dowolnego folderu do udziału domyślne. Nazwa udziału rozpoczyna się od `databox-`. |
|`ContainerRenamed` |Kontener dla tych plików nie są zgodne z konwencjami nazewnictwa platformy Azure i została zmieniona. Nowa nazwa zaczyna się od `databox-` i sufiks Skrót SHA1 oryginalna nazwa |
|`ShareRenamed` |Udział w tych plików, nie są zgodne z konwencjami nazewnictwa platformy Azure i została zmieniona. Nowa nazwa zaczyna się od `databox-` i sufiks Skrót SHA1 oryginalną nazwę. |
|`BlobRenamed` |Pliki te nie są zgodne z konwencjami nazewnictwa platformy Azure i zostały zmienione. Sprawdź `BlobPath` pola dla nowej nazwy. |
|`FileRenamed` |Pliki te nie są zgodne z konwencjami nazewnictwa platformy Azure i zostały zmienione. Sprawdź `FileStoragePath` pola dla nowej nazwy. |
|`DiskRenamed` |Pliki te nie są zgodne z konwencjami nazewnictwa platformy Azure i zostały zmienione. Sprawdź `BlobPath` pola dla nowej nazwy. |


## <a name="next-steps"></a>Następne kroki

- [Otwórz bilet pomocy technicznej dla dysku Data Box wystawia](data-box-disk-contact-microsoft-support.md).
