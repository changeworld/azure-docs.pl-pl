---
title: Rozwiązywanie problemów z przekazywaniem danych przy użyciu dzienników
titleSuffix: Azure Data Box Disk
description: W tym artykule opisano, jak używać dzienników i rozwiązywać problemy widoczne podczas przekazywania danych na dysk azure data box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260139"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Opis dzienników w celu rozwiązywania problemów z przekazywaniem danych na dysku usługi Azure Data Box

Ten artykuł dotyczy dysku microsoft azure data box i opisuje problemy widoczne podczas przekazywania danych na platformę Azure.

## <a name="about-upload-logs"></a>Informacje o dziennikach przekazywania

Gdy dane są przekazywane na platformę Azure `_error.xml` `_verbose.xml` w centrum danych, a pliki są generowane dla każdego konta magazynu. Te dzienniki są przekazywane na to samo konto magazynu, które zostało użyte do przekazywania danych. 

Oba dzienniki są w tym samym formacie i zawierają opisy XML zdarzeń, które wystąpiły podczas kopiowania danych z dysku na konto usługi Azure Storage.

Pełny dziennik zawiera pełne informacje o stanie operacji kopiowania dla każdego obiektu blob lub pliku, podczas gdy dziennik błędów zawiera tylko informacje dotyczące obiektów blob lub plików, które napotkały błędy podczas przekazywania.

Dziennik błędów ma taką samą strukturę jak pełny dziennik, ale odfiltrowuje pomyślne operacje.

## <a name="download-logs"></a>Pobieranie dzienników

Aby zlokalizować dzienniki przekazywania, należy wykonać następujące czynności.

1. Jeśli występują błędy podczas przekazywania danych do platformy Azure, portal wyświetla ścieżkę do folderu, w którym znajdują się dzienniki diagnostyczne.

    ![Łącze do dzienników w portalu](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Przejdź do **waies**.

    ![błędy i pełne dzienniki](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

W każdym przypadku są widoczne dzienniki błędów i pełne dzienniki. Wybierz każdy dziennik i pobierz kopię lokalną.

## <a name="sample-upload-logs"></a>Przykładowe dzienniki przekazywania

Próbka znajduje `_verbose.xml` się poniżej. W takim przypadku zamówienie zostało pomyślnie zakończone bez błędów.

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

W przypadku tej samej `_error.xml` kolejności poniżej przedstawiono próbkę.

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

Próbka znajduje `_error.xml` się poniżej, gdzie zamówienie zakończone błędami. 

Plik błędu w tym `Summary` przypadku ma sekcję i inną sekcję, która zawiera wszystkie błędy poziomu pliku. 

Zawiera `Summary` `ValidationErrors` i `CopyErrors`. W takim przypadku 8 plików lub folderów zostały przekazane na platformę Azure i nie było żadnych błędów sprawdzania poprawności. Po skopiowaniu danych na konto usługi Azure Storage pomyślnie przekazano 5 plików lub folderów. Pozostałe 3 pliki lub foldery zostały zmienione zgodnie z konwencjami nazewnictwa kontenerów platformy Azure, a następnie pomyślnie przekazane na platformę Azure.

Stan poziomu pliku `BlobStatus` są w tym opisuje wszelkie akcje podjęte w celu przekazania obiektów blob. W takim przypadku zmienia się nazwę trzech kontenerów, ponieważ foldery, do których dane zostały skopiowane, nie były zgodne z konwencjami nazewnictwa platformy Azure dla kontenerów. W przypadku obiektów blob przekazanych w tych kontenerach uwzględniono nową nazwę kontenera, ścieżkę obiektu blob na platformie Azure, oryginalną nieprawidłową ścieżkę pliku i rozmiar obiektu blob.
    
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

Błędy generowane podczas przekazywania danych na platformę Azure są podsumowane w poniższej tabeli.

| Kod błędu | Opis                   |
|-------------|------------------------------|
|`None` |  Ukończono pomyślnie.           |
|`Renamed` | Zmieniono nazwę obiektu blob.   |
|`CompletedWithErrors` | Prześlij zakończone z błędami. Szczegóły plików w błąd są zawarte w pliku dziennika.  |
|`Corrupted`|CRC obliczone podczas pozyskiwania danych nie pasuje do CRC obliczonego podczas przekazywania.  |  
|`StorageRequestFailed` | Żądanie usługi Azure storage nie powiodło się.   |     
|`LeasePresent` | Ten element jest dzierżawiony i jest używany przez innego użytkownika. |
|`StorageRequestForbidden` |Nie można przekazać z powodu problemów z uwierzytelnianiem. |
|`ManagedDiskCreationTerminalFailure` | Nie można przekazać jako dysków zarządzanych. Pliki są dostępne na koncie magazynu przemieszczania jako obiekty blob strony. Obiekty blob stron można ręcznie konwertować na dyski zarządzane.  |
|`DiskConversionNotStartedTierInfoMissing` | Ponieważ plik VHD został skopiowany poza wstępnie utworzone foldery warstwy, dysk zarządzany nie został utworzony. Plik jest przekazytyny jako obiekt blob strony do przemieszczania konta magazynu, jak określono podczas tworzenia zamówienia. Można go przekonwertować ręcznie na dysk zarządzany.|
|`InvalidWorkitem` | Nie można przekazać danych, ponieważ nie jest zgodna z konwencjami nazewnictwa i limitów platformy Azure.|
|`InvalidPageBlobUploadAsBlockBlob` | Przekazywane jako blokowe obiekty blob `databoxdisk-invalid-pb-`w kontenerze z prefiksem .|
|`InvalidAzureFileUploadAsBlockBlob` | Przekazane jako blokowe obiekty blob `databoxdisk-invalid-af`w kontenerze z prefiksem -.|
|`InvalidManagedDiskUploadAsBlockBlob` | Przekazane jako blokowe obiekty blob `databoxdisk-invalid-md`w kontenerze z prefiksem -.|
|`InvalidManagedDiskUploadAsPageBlob` |Przekazane jako obiekty blob strony w `databoxdisk-invalid-md-`kontenerze z prefiksem . |
|`MovedToOverflowShare` |Przekazane pliki do nowego udziału, ponieważ oryginalny rozmiar udziału przekroczył maksymalny limit rozmiaru platformy Azure. Nowa nazwa udziału plików ma oryginalną nazwę `-2`sufiks z .   |
|`MovedToDefaultAzureShare` |Przekazane pliki, które nie były częścią żadnego folderu do udziału domyślnego. Nazwa udziału zaczyna `databox-`się od . |
|`ContainerRenamed` |Kontener dla tych plików nie jest zgodny z konwencjami nazewnictwa platformy Azure i został zmieniony. Nowa nazwa zaczyna `databox-` się od i jest sufiksem z skrótem SHA1 oryginalnej nazwy |
|`ShareRenamed` |Udział dla tych plików nie jest zgodny z konwencjami nazewnictwa platformy Azure i został zmieniony. Nowa nazwa zaczyna `databox-` się od i jest sufiksem z skrótem SHA1 oryginalnej nazwy. |
|`BlobRenamed` |Te pliki nie są zgodne z konwencjami nazewnictwa platformy Azure i zostały zmienione. Sprawdź `BlobPath` pole pod kątem nowej nazwy. |
|`FileRenamed` |Te pliki nie są zgodne z konwencjami nazewnictwa platformy Azure i zostały zmienione. Sprawdź `FileStoragePath` pole pod kątem nowej nazwy. |
|`DiskRenamed` |Te pliki nie są zgodne z konwencjami nazewnictwa platformy Azure i zostały zmienione. Sprawdź `BlobPath` pole pod kątem nowej nazwy. |


## <a name="next-steps"></a>Następne kroki

- [Otwórz bilet pomocy technicznej dla problemów z dyskiem pola danych](data-box-disk-contact-microsoft-support.md).
