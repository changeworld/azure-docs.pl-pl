---
title: Rozwiązywanie problemów z usługą Azure Data Box Disk | Microsoft Docs
description: W tym artykule opisano sposób rozwiązywania problemy z usługą Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f725f38a335972ae8e0a8b8402a99202caa54a70
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147081"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Użyj dzienników, aby rozwiązać problemy ze sprawdzaniem poprawności w dysku Azure Data Box

Ten artykuł dotyczy dysku systemu Microsoft Azure Data Box. W artykule opisano sposób użycia dzienniki, aby rozwiązać problemy ze sprawdzaniem poprawności, który można uzyskać w przypadku wdrażania tego rozwiązania.

## <a name="validation-tool-log-files"></a>Pliki dziennika narzędzia do sprawdzania poprawności

Podczas sprawdzania poprawności danych na dyskach, przy użyciu [narzędzia sprawdzającego poprawność](data-box-disk-deploy-copy-data.md#validate-data), *error.xml* jest generowany, aby rejestrować wszystkie błędy. Plik dziennika znajduje się w `Drive:\DataBoxDiskImport\logs` folder na dysku. Po uruchomieniu sprawdzania poprawności, zostanie podane łącze do dziennika błędów.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Jeśli uruchamiasz wiele sesji dla sprawdzania poprawności, jeden dziennik błędów jest generowany na sesję.

- Poniżej znajduje się przykładowy dziennika błędów, gdy dane załadowane do `PageBlob` folder nie jest wyrównany 512-bajtów. Wszelkie dane przekazywane do PageBlob musi być 512-bytes wyrównane, na przykład dysk VHD lub VHDX. Błędy w tym pliku znajdują się w `<Errors>` i ostrzeżenia w `<Warnings>`.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Poniżej przedstawiono przykładowy dziennik błędów, gdy nazwa kontenera jest nieprawidłowy. Folder, który tworzysz w obszarze `BlockBlob`, `PageBlob`, lub `AzureFile` foldery na dysku staje się kontener na koncie usługi Azure Storage. Nazwa kontenera musi być zgodna [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Błędy narzędzia do sprawdzania poprawności

Błędy zawarte w *error.xml* z odpowiednimi zalecane akcje są podsumowane w poniższej tabeli.

| Kod błędu| Opis                       | Zalecane akcje               |
|------------|--------------------------|-----------------------------------|
| `None` | Pomyślnie zweryfikowana danych. | Nie jest wymagana żadna akcja. |
| `InvalidXmlCharsInPath` |Nie można utworzyć plik manifestu, ponieważ ścieżka pliku zawiera znaki, które nie są prawidłowe. | Usuń te znaki, aby kontynuować.  |
| `OpenFileForReadFailed`| Nie można przetworzyć pliku. Może to być spowodowane uszkodzeniem systemu dostępu, jak problem lub pliku.|Nie można odczytać pliku z powodu błędu. Szczegóły błędu znajdują się w wyjątku. |
| `Not512Aligned` | Ten plik nie jest w prawidłowym formacie PageBlob folderu.| Tylko dane przekazywania, która jest 512 bajtów wyrównane do `PageBlob` folderu. Usuń plik z folderu PageBlob lub przenieś go do folderu BlockBlob. Ponawianie próby weryfikacji.|
| `InvalidBlobPath` | Ścieżka pliku nie jest mapowany na ścieżkę prawidłowy obiekt blob w chmurze zgodnie z obiektów Blob platformy Azure, konwencje nazewnictwa.|Postępuj zgodnie z wytycznymi nazewnictwa platformy Azure można zmienić nazwy ścieżki pliku. |
| `EnumerationError` | Nie można wyliczyć pliku do sprawdzania poprawności. |Może istnieć kilka przyczyn tego błędu. Najbardziej prawdopodobną przyczyną jest dostęp do pliku. |
| `ShareSizeExceeded` | Ten plik spowodowane rozmiar udziału plików platformy Azure, Azure przekroczyć 5 TB.|Zmniejsz rozmiar danych w udziale, dlatego, że spełnia on [limity rozmiaru obiektów platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Ponawianie próby weryfikacji. |
| `AzureFileSizeExceeded` | Rozmiar pliku przekracza ograniczenia rozmiaru plików platformy Azure.| Zmniejsz rozmiar pliku lub dane, więc, że spełnia on [limity rozmiaru obiektów platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Ponawianie próby weryfikacji.|
| `BlockBlobSizeExceeded` | Rozmiar pliku przekracza ograniczenia rozmiaru usługi Azure Block Blob. | Zmniejsz rozmiar pliku lub dane, więc, że spełnia on [limity rozmiaru obiektów platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Ponawianie próby weryfikacji. |
| `ManagedDiskSizeExceeded` | Rozmiar pliku przekracza ograniczenia rozmiaru dysk zarządzany platformy Azure. | Zmniejsz rozmiar pliku lub dane, więc, że spełnia on [limity rozmiaru obiektów platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Ponawianie próby weryfikacji. |
| `PageBlobSizeExceeded` | Rozmiar pliku przekracza ograniczenia rozmiaru dysk zarządzany platformy Azure. | Zmniejsz rozmiar pliku lub dane, więc, że spełnia on [limity rozmiaru obiektów platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Ponawianie próby weryfikacji. |
| `InvalidShareContainerFormat`  |Nazwy katalogów nie jest zgodna z konwencjami nazewnictwa platformy Azure for containers lub udziałów.         |Pierwszego folderu utworzonych w ramach istniejących folderów na dysku staje się kontener na koncie magazynu. Ta nazwa udziału lub kontenera nie jest zgodny z konwencjami nazewnictwa platformy Azure. Zmień nazwę pliku, tak że spełnia on [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ponawianie próby weryfikacji.   |
| `InvalidBlobNameFormat` | Ścieżka pliku nie jest mapowany na ścieżkę prawidłowy obiekt blob w chmurze zgodnie z obiektów Blob platformy Azure, konwencje nazewnictwa.|Zmień nazwę pliku, tak że spełnia on [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ponawianie próby weryfikacji. |
| `InvalidFileNameFormat` | Ścieżka pliku nie są mapowane na prawidłową ścieżkę do pliku w chmurze zgodnie z konwencji nazewnictwa plików platformy Azure. |Zmień nazwę pliku, tak że spełnia on [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ponawianie próby weryfikacji. |
| `InvalidDiskNameFormat` | Ścieżka pliku nie jest mapowany na nazwę prawidłowy dysk w chmurze zgodnie z konwencjami nazewnictwa dysk zarządzany platformy Azure. |Zmień nazwę pliku, tak że spełnia on [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ponawianie próby weryfikacji.       |
| `NotPartOfFileShare` | Nie można przekazać pliki, ponieważ ścieżka przekazywania jest nieprawidłowa. Przekaż pliki do folderu w usłudze Azure Files.   | Usuń pliki z błędami i przekazać te pliki do folderu precreated. Ponawianie próby weryfikacji. |
| `NonVhdFileNotSupportedForManagedDisk` | Nie można przekazać pliku-VHD jako dysk zarządzany. |Usuń pliki wirtualnego dysku twardego, ponieważ te nie są obsługiwane. Ponawianie próby weryfikacji. |


## <a name="next-steps"></a>Kolejne kroki

- Rozwiązywanie problemów z [błędy przekazywania danych](data-box-disk-troubleshoot-upload.md).
