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
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67805705"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Rozwiązywanie problemów z sprawdzaniem poprawności na dysku usługi Azure Data Box Disk za pomocą dzienników

Ten artykuł dotyczy dysku pola danych platformy Microsoft Azure. W tym artykule opisano, jak używać dzienników do rozwiązywania problemów z sprawdzaniem poprawności, które można zobaczyć podczas wdrażania tego rozwiązania.

## <a name="validation-tool-log-files"></a>Pliki dziennika narzędzia sprawdzania poprawności

Podczas sprawdzania poprawności danych na dyskach za pomocą [narzędzia sprawdzania poprawności](data-box-disk-deploy-copy-data.md#validate-data)generowany jest plik *error.xml* w celu rejestrowania błędów. Plik dziennika znajduje się `Drive:\DataBoxDiskImport\logs` w folderze dysku. Łącze do dziennika błędów jest podana po uruchomieniu sprawdzania poprawności.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Jeśli uruchomisz wiele sesji do sprawdzania poprawności, jeden dziennik błędów jest generowany na sesję.

- Oto przykład dziennika błędów, gdy dane `PageBlob` załadowane do folderu nie są wyrównane do 512 bajtów. Wszelkie dane przesłane do PageBlob muszą być wyrównane do 512 bajtów, na przykład VHD lub VHDX. Błędy w tym pliku są `<Errors>` w `<Warnings>`i ostrzeżenia w .

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

- Oto przykład dziennika błędów, gdy nazwa kontenera jest nieprawidłowa. Folder utworzony w `BlockBlob`obszarze `PageBlob`programu `AzureFile` , lub foldery na dysku staje się kontenerem na koncie usługi Azure Storage. Nazwa kontenera musi być zgodna z [konwencjami nazewnictwa platformy Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)

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

## <a name="validation-tool-errors"></a>Błędy narzędzia sprawdzania poprawności

Błędy zawarte w *pliku error.xml* z odpowiednimi zalecanymi akcjami są podsumowane w poniższej tabeli.

| Kod błędu| Opis                       | Zalecane działania               |
|------------|--------------------------|-----------------------------------|
| `None` | Pomyślnie zweryfikowano dane. | Nie jest wymagana żadna akcja. |
| `InvalidXmlCharsInPath` |Nie można utworzyć pliku manifestu, ponieważ ścieżka pliku zawiera znaki, które są nieprawidłowe. | Usuń te znaki, aby kontynuować.  |
| `OpenFileForReadFailed`| Nie można przetworzyć pliku. Może to być spowodowane problemem z dostępem lub uszkodzeniem systemu plików.|Nie można odczytać pliku z powodu błędu. Szczegóły błędu są w wyjątku. |
| `Not512Aligned` | Ten plik nie jest w prawidłowym formacie dla folderu PageBlob.| Przesyłaj tylko dane o masie 512 bajtów wyrównanych do `PageBlob` folderu. Usuń plik z folderu PageBlob lub przenieś go do folderu BlockBlob. Ponów próbę weryfikacji.|
| `InvalidBlobPath` | Ścieżka pliku nie mapuje do prawidłowej ścieżki obiektu blob w chmurze, zgodnie z konwencjami nazewnictwa obiektów Blob platformy Azure.|Postępuj zgodnie z wytycznymi dotyczącymi nazewnictwa platformy Azure, aby zmienić nazwę ścieżki pliku. |
| `EnumerationError` | Nie można wyliczyć pliku do weryfikacji. |Może istnieć wiele przyczyn tego błędu. Najbardziej prawdopodobną przyczyną jest dostęp do pliku. |
| `ShareSizeExceeded` | Ten plik spowodował, że rozmiar udziału plików platformy Azure przekroczył limit platformy Azure wynoszący 5 TB.|Zmniejsz rozmiar danych w udziale, tak aby były zgodne z [limitami rozmiaru obiektu platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Ponów próbę weryfikacji. |
| `AzureFileSizeExceeded` | Rozmiar pliku przekracza limity rozmiaru pliku platformy Azure.| Zmniejsz rozmiar pliku lub danych, tak aby był zgodny z [limitami rozmiaru obiektu platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Ponów próbę weryfikacji.|
| `BlockBlobSizeExceeded` | Rozmiar pliku przekracza limity rozmiaru obiektów blob bloku platformy Azure. | Zmniejsz rozmiar pliku lub danych, tak aby był zgodny z [limitami rozmiaru obiektu platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Ponów próbę weryfikacji. |
| `ManagedDiskSizeExceeded` | Rozmiar pliku przekracza limity rozmiaru dysku zarządzanego platformy Azure. | Zmniejsz rozmiar pliku lub danych, tak aby był zgodny z [limitami rozmiaru obiektu platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Ponów próbę weryfikacji. |
| `PageBlobSizeExceeded` | Rozmiar pliku przekracza limity rozmiaru dysku zarządzanego platformy Azure. | Zmniejsz rozmiar pliku lub danych, tak aby był zgodny z [limitami rozmiaru obiektu platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Ponów próbę weryfikacji. |
| `InvalidShareContainerFormat`  |Nazwy katalogów nie są zgodne z konwencjami nazewnictwa platformy Azure dla kontenerów lub udziałów.         |Pierwszy folder utworzony w ramach istniejących folderów na dysku staje się kontenerem na koncie magazynu. Ta nazwa udziału lub kontenera nie jest zgodna z konwencjami nazewnictwa platformy Azure. Zmień nazwę pliku tak, aby był zgodny z [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ponów próbę weryfikacji.   |
| `InvalidBlobNameFormat` | Ścieżka pliku nie mapuje do prawidłowej ścieżki obiektu blob w chmurze, zgodnie z konwencjami nazewnictwa obiektów Blob platformy Azure.|Zmień nazwę pliku tak, aby był zgodny z [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ponów próbę weryfikacji. |
| `InvalidFileNameFormat` | Ścieżka pliku nie jest mapowana na prawidłową ścieżkę plików w chmurze zgodnie z konwencjami nazewnictwa plików platformy Azure. |Zmień nazwę pliku tak, aby był zgodny z [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ponów próbę weryfikacji. |
| `InvalidDiskNameFormat` | Ścieżka pliku nie mapuje prawidłowej nazwy dysku w chmurze zgodnie z konwencjami nazewnictwa dysku zarządzanego platformy Azure. |Zmień nazwę pliku tak, aby był zgodny z [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ponów próbę weryfikacji.       |
| `NotPartOfFileShare` | Ścieżka przekazywania plików jest nieprawidłowa. Przekaż pliki do folderu w usłudze Azure Files.   | Usuń pliki omyłkowo i prześlij je do wstępnie skrealizowanego folderu. Ponów próbę weryfikacji. |
| `NonVhdFileNotSupportedForManagedDisk` | Nie można przekazać pliku o wiele, który nie jest wirtualny. |Usuń pliki inne niż `ManagedDisk` VHD z folderu, ponieważ nie `PageBlob` są one obsługiwane, lub przenieś te pliki do folderu. Ponów próbę weryfikacji. |


## <a name="next-steps"></a>Następne kroki

- Rozwiązywanie problemów z [błędami przekazywania danych](data-box-disk-troubleshoot-upload.md).
