---
title: Rozwiązywanie problemów na urządzenie Azure Data Box | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób rozwiązywania problemów występujących w usłudze Azure Data Box przy przekazywaniu danych do platformy Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/13/2019
ms.author: alkohli
ms.openlocfilehash: 1126002a93419371be3216c55114385c9c600419
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65593998"
---
# <a name="troubleshoot-issues-related-to-azure-data-box"></a>Rozwiązywanie problemów związanych z usługi Azure Data Box

W tym artykule zawiera szczegółowe informacje na temat rozwiązywania problemów, że może zostać wyświetlony w przypadku korzystania z usługi Azure Data Box.

## <a name="errors-during-data-copy"></a>Błędy podczas kopiowania danych

Wszystkie błędy, które nie są widoczne podczas kopiowania danych podsumowano w poniższych sekcjach.

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH 

**Opis błędu:** Nazwa kontenera lub udziału musi mieć długość od 3 do 63 znaków.

**Sugerowane rozwiązanie:** Folderu w udziale urządzenia Data Box (SMB/NFS), do którego skopiowano danych staje się kontenerów platformy Azure, w ramach konta magazynu. 

- Na **Połącz i skopiuj** strony lokalnego internetowego interfejsu użytkownika urządzenia Data Box, pobierania i przejrzyj pliki błędów, aby zidentyfikować folder nazwy z problemami.
- Zmień nazwę folderu w udziale urządzenia Data Box, aby upewnić się, że:

    - Nazwa ma od 3 do 63 znaków.
    - Nazwy mogą mieć tylko litery, cyfry i łączniki.
    - Nazwy nie może zaczynać się ani kończyć łączników.
    - Nazwy nie może zawierać następujących po sobie łączników.
    - Przykłady prawidłowych nazw: `my-folder-1`, `my-really-extra-long-folder-111`.
    - Przykłady nazw, które nie są prawidłowe: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Aby uzyskać więcej informacji, zobacz Azure konwencje nazewnictwa dla [nazwy kontenera](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) i [nazwy udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Opis błędu:** Nazwa kontenera lub udziału musi mieć długość od 3 do 63 znaków. 

**Sugerowane rozwiązanie:** Folderu w udziale urządzenia Data Box (SMB/NFS), do którego skopiowano danych staje się kontenerów platformy Azure, w ramach konta magazynu. 

- Na **Połącz i skopiuj** strony lokalnego internetowego interfejsu użytkownika urządzenia Data Box, pobierania i przejrzyj pliki błędów, aby zidentyfikować folder nazwy z problemami.
- Zmień nazwę folderu w udziale urządzenia Data Box, aby upewnić się, że:

    - Nazwa ma od 3 do 63 znaków.
    - Nazwy mogą mieć tylko litery, cyfry i łączniki.
    - Nazwy nie może zaczynać się ani kończyć łączników.
    - Nazwy nie może zawierać następujących po sobie łączników.
    - Przykłady prawidłowych nazw: `my-folder-1`, `my-really-extra-long-folder-111`
    - Przykłady nazw, które nie są prawidłowe: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Aby uzyskać więcej informacji, zobacz Azure konwencje nazewnictwa dla [nazwy kontenera](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) i [nazwy udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Opis błędu:** Nazwa kontenera lub udziału musi zawierać wyłącznie litery, cyfry lub łączniki.

**Sugerowane rozwiązanie:** Folderu w udziale urządzenia Data Box (SMB/NFS), do którego skopiowano danych staje się kontenerów platformy Azure, w ramach konta magazynu. 

- Na **Połącz i skopiuj** strony lokalnego internetowego interfejsu użytkownika urządzenia Data Box, pobierania i przejrzyj pliki błędów, aby zidentyfikować folder nazwy z problemami.
- Zmień nazwę folderu w udziale urządzenia Data Box, aby upewnić się, że:

    - Nazwa ma od 3 do 63 znaków.
    - Nazwy mogą mieć tylko litery, cyfry i łączniki.
    - Nazwy nie może zaczynać się ani kończyć łączników.
    - Nazwy nie może zawierać następujących po sobie łączników.
    - Przykłady prawidłowych nazw: `my-folder-1`, `my-really-extra-long-folder-111`
    - Przykłady nazw, które nie są prawidłowe: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Aby uzyskać więcej informacji, zobacz Azure konwencje nazewnictwa dla [nazwy kontenera](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) i [nazwy udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Opis błędu:** Nie można uruchomić nazwy kontenera i nazwy udziału lub kończyć się znakiem łączniki oraz nie może zawierać następujących po sobie łączników.

**Sugerowane rozwiązanie:** Folderu w udziale urządzenia Data Box (SMB/NFS), do którego skopiowano danych staje się kontenerów platformy Azure, w ramach konta magazynu. 

- Na **Połącz i skopiuj** strony lokalnego internetowego interfejsu użytkownika urządzenia Data Box, pobierania i przejrzyj pliki błędów, aby zidentyfikować folder nazwy z problemami.
- Zmień nazwę folderu w udziale urządzenia Data Box, aby upewnić się, że:

    - Nazwa ma od 3 do 63 znaków.
    - Nazwy mogą mieć tylko litery, cyfry i łączniki.
    - Nazwy nie może zaczynać się ani kończyć łączników.
    - Nazwy nie może zawierać następujących po sobie łączników.
    - Przykłady prawidłowych nazw: `my-folder-1`, `my-really-extra-long-folder-111`
    - Przykłady nazw, które nie są prawidłowe: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Aby uzyskać więcej informacji, zobacz Azure konwencje nazewnictwa dla [nazwy kontenera](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) i [nazwy udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Opis błędu:** Kontenera nieprawidłowej nazwy zostały określone dla dysków zarządzanych udziałów.

**Sugerowane rozwiązanie:** W przypadku dysków zarządzanych w ramach każdego udziału tworzone są następujące foldery, które odnoszą się do kontenerów w ramach konta magazynu: Premium SSD, standardowych dysków Twardych i SSD w warstwie standardowa. Te foldery odpowiadać warstwa wydajności dysku zarządzanego.

- Upewnij się, skopiuj dane obiektów blob strony (VHD) do jednego z tych istniejących folderów. Tylko dane z tych istniejących kontenerów zostanie przekazany na platformę Azure.
- Inny folder, który jest tworzony na tym samym poziomie jak dysku Premium SSD, standardowych dysków Twardych i SSD w warstwie standardowa nie jest zgodny z warstwy wydajności prawidłowe i nie można używać.
- Usuń pliki i foldery utworzone poza warstwy wydajności.

Aby uzyskać więcej informacji, zobacz [kopiowania do usługi managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Opis błędu:** Udział plików platformy Azure ogranicza udział 5 TB danych. Ten limit został przekroczony dla niektórych udziałów.

**Sugerowane rozwiązanie:** Na **Połącz i skopiuj** strony urządzenia Data Box lokalnego Interfejsu w przeglądarce, pobrać i przejrzeć plików błędów.

Określ foldery, które mają ten problem z dzienników błędów i upewnij się, że pliki w tym folderze znajdują się w sekcji 5 TB.

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Opis błędu:** Nazwy obiektów blob lub plik zawierać znaków kontrolnych nieobsługiwany.

**Sugerowane rozwiązanie:** Obiekty BLOB lub pliki, które zostały skopiowane zawierają dzięki nieobsługiwane znaki.

Na **Połącz i skopiuj** strony z lokalnego Interfejsu w przeglądarce, pobrać i przejrzeć plików błędów.
Usuń lub zmień nazwy plików, aby usunąć nieobsługiwane znaki.

Aby uzyskać więcej informacji, zobacz Azure konwencje nazewnictwa dla [blob nazwy](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazwy plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Opis błędu:** Nazwy obiektów blob lub pliku zawiera niedozwolone znaki.

**Sugerowane rozwiązanie:** Obiekty BLOB lub pliki, które zostały skopiowane zawierają dzięki nieobsługiwane znaki.

Na **Połącz i skopiuj** strony z lokalnego Interfejsu w przeglądarce, pobrać i przejrzeć plików błędów.
Usuń lub zmień nazwy plików, aby usunąć nieobsługiwane znaki.

Aby uzyskać więcej informacji, zobacz Azure konwencje nazewnictwa dla [blob nazwy](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazwy plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Opis błędu:** Nazwy obiektów blob lub plików kończą się przy użyciu nieprawidłowych znaków.

**Sugerowane rozwiązanie:** Obiekty BLOB lub pliki, które zostały skopiowane zawierają dzięki nieobsługiwane znaki.

Na **Połącz i skopiuj** strony z lokalnego Interfejsu w przeglądarce, pobrać i przejrzeć plików błędów.
Usuń lub zmień nazwy plików, aby usunąć nieobsługiwane znaki.

Aby uzyskać więcej informacji, zobacz Azure konwencje nazewnictwa dla [blob nazwy](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazwy plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Opis błędu:** Nazwa obiektu blob lub plik zawiera zbyt wiele segmentów ścieżki.

**Sugerowane rozwiązanie:** Obiekty BLOB lub pliki, które zostały skopiowane przekroczyły maksymalną liczbę segmentów ścieżki. Segment ścieżki jest ciągiem od ogranicznika następujących po sobie znaków, na przykład ukośnik /.

- Na **Połącz i skopiuj** strony z lokalnego Interfejsu w przeglądarce, pobrać i przejrzeć plików błędów.
- Upewnij się, że [blob nazwy](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazwy plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) są zgodne z konwencjami nazewnictwa platformy Azure.

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Opis błędu:** Nazwa obiektu blob lub pliku jest za długa.

**Sugerowane rozwiązanie:** Obiekt blob lub nazwy pliku przekracza maksymalną długość.

- Na **Połącz i skopiuj** strony z lokalnego Interfejsu w przeglądarce, pobrać i przejrzeć plików błędów.
- Nazwa obiektu blob nie może przekraczać 1024 znaków.
- Usuń lub zmień nazwę obiektu blob lub pliki, aby nazwy nie może przekraczać 1024 znaków.

Aby uzyskać więcej informacji zobacz Azure konwencje nazewnictwa dla nazwy obiektu blob i plików.

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Opis błędu:** Jeden z segmentów w nazwie obiektu blob lub pliku jest zbyt długi.

**Sugerowane rozwiązanie:** Jeden z segmentów ścieżki w nazwie pliku lub obiektu blob przekracza maksymalną dopuszczalną liczbę znaków. Segment ścieżki jest ciągiem od ogranicznika następujących po sobie znaków, na przykład ukośnik /.

- Na **Połącz i skopiuj** strony z lokalnego Interfejsu w przeglądarce, pobrać i przejrzeć plików błędów.
- Upewnij się, że [blob nazwy](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazwy plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) są zgodne z konwencjami nazewnictwa platformy Azure.

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Opis błędu:** Rozmiar pliku przekracza maksymalny rozmiar pliku do przekazania.

**Sugerowane rozwiązanie:** Obiekt blob lub rozmiary plików przekraczają maksymalny limit dozwolony dla przekazywania.

- Na **Połącz i skopiuj** strony z lokalnego Interfejsu w przeglądarce, pobrać i przejrzeć plików błędów.
- Upewnij się, że rozmiarów obiektów blob i plików nie przekraczają limity rozmiaru obiektów platformy Azure.

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Opis błędu:** Obiekt blob lub plik jest nieprawidłowo wyrównany.

**Sugerowane rozwiązanie:** Powiązana udziału blob strony na urządzenie Data Box tylko obsługuje pliki, które są 512 bajtów (np. VHD/VHDX). Wszelkie dane skopiowane do udziału blob strony zostanie przekazany na platformę Azure jako stronicowe obiekty BLOB.

Usuń wszystkie dane bez do dysku VHD/VHDX z udziału blob strony. Można używać udziałów dla blokowych obiektów blob i plików platformy Azure dla danych typu ogólnego.

Aby uzyskać więcej informacji, zobacz [Przegląd stronicowe obiekty BLOB](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Opis błędu:** Nieobsługiwany typ pliku jest obecny w udziale dysku zarządzanego. Dozwolone są tylko stałych dysków VHD.

**Sugerowane rozwiązanie:**

- Upewnij się, przekazać tylko stałych dysków VHD w celu utworzenia dysków zarządzanych.
- Pliki VHDX lub **dynamiczne** i **różnicowych** wirtualne dyski twarde nie są obsługiwane.

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Opis błędu:** Katalog nie jest dozwolona w jakiekolwiek istniejące foldery za dyski zarządzane. W tych folderów, dozwolone są tylko stałych dysków VHD.

**Sugerowane rozwiązanie:** W przypadku dysków zarządzanych w ramach każdego udziału następujące trzy foldery są tworzone, które odnoszą się do kontenerów w ramach konta magazynu: Premium SSD, standardowych dysków Twardych i SSD w warstwie standardowa. Te foldery odpowiadać warstwa wydajności dysku zarządzanego.

- Upewnij się, skopiuj dane obiektów blob strony (VHD) do jednego z tych istniejących folderów.
- Folder lub katalog nie jest dozwolona w tych istniejących folderów. Usuń wszystkie foldery, które zostały utworzone wewnątrz istniejących folderów.

Aby uzyskać więcej informacji, zobacz [kopiowania do usługi managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [wymagania systemowe magazynu obiektów Blob pole danych](data-box-system-requirements-rest.md).
