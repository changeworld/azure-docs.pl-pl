---
title: Dane dotyczące rozwiązywania problemów z dysku Data Box Azure Przekaż problemów przy użyciu dzienników | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak używać dzienników i rozwiązywanie problemów występujących podczas przekazywania danych do dysku Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 6f3ac38c3eac968bd2f7ec2aada435466d3ff279
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148311"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Zrozumienie dzienniki rozwiązywania problemów przekazywania danych z dysku Azure Data Box

Ten artykuł ma zastosowanie do dysku systemu Microsoft Azure Data Box i opisano problemy, które zobaczysz podczas przekazywania danych na platformie Azure.

## <a name="data-upload-logs"></a>Przekazywanie danych do dzienników

Gdy dane są przekazywane do platformy Azure w centrum danych, `_error.xml` i `_verbose.xml` pliki są generowane. Te dzienniki są przekazywane do tego samego konta magazynu, który został użyty do przekazania danych. Próbka `_error.xml` znajdują się poniżej.
    
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

## <a name="download-logs"></a>Pobieranie dzienników

Istnieją dwa sposoby, aby znaleźć i pobrać dzienniki diagnostyczne.

- Jeśli występują błędy podczas przekazywania danych na platformie Azure, portal zawiera ścieżkę do folderu, w którym znajdują się dzienniki diagnostyczne.

    ![Połącz z dzienników w portalu](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

- Przejdź do konta magazynu skojarzone z Twoje zamówienie urządzenia Data Box. Przejdź do pozycji **Blob Service > Przeglądaj obiekty blob** i wyszukaj obiekt blob odpowiadający kontu magazynu. Przejdź do **waies**.

    ![Dzienniki kopiowania 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

W każdym przypadku Zobacz dzienniki błędów i pełne dzienniki. Wybierz każdego dziennika i Pobierz kopię lokalną.


## <a name="data-upload-errors"></a>Błędy przekazywania danych

Błędy generowane podczas przekazywania danych na platformie Azure są podsumowane w poniższej tabeli.

| Kod błędu | Opis                        |
|-------------|------------------------------|
|`None` |  Zakończone pomyślnie.           |
|`Renamed` | Pomyślnie zmieniono nazwę obiektu blob.  |                                                            |
|`CompletedWithErrors` | Przekazywanie zostało zakończone z błędami. Szczegóły dotyczące plików w wyniku błędu znajdują się w pliku dziennika.  |
|`Corrupted`|CRC obliczane podczas pozyskiwania danych nie jest zgodna CRC obliczane podczas przekazywania.  |  
|`StorageRequestFailed` | Usługa Azure storage, żądanie nie powiodło się.   |     |
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


## <a name="next-steps"></a>Kolejne kroki

- [Otwórz bilet pomocy technicznej dla dysku Data Box wystawia](data-box-disk-contact-microsoft-support.md).
