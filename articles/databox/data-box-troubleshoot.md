---
title: Rozwiązywanie problemów z usługą Azure Data Box, Azure Data Box Heavy
description: W tym artykule opisano sposób rozwiązywania problemów widocznych w usłudze Azure Data Box i Azure Data Box Heavy podczas kopiowania danych na te urządzenia.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 51935516e26f263e44a926bf9b7d7ec24a5eeb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560069"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Rozwiązywanie problemów związanych z usługą Azure Data Box i azure data box heavy

W tym artykule opisano informacje dotyczące rozwiązywania problemów, które mogą być widoczne podczas korzystania z usługi Azure Data Box lub Azure Data Box Heavy. Artykuł zawiera listę możliwych błędów widocznych podczas kopiowania danych do pola danych lub po przekazaniu danych z pola danych.

## <a name="error-classes"></a>Klasy błędów

Błędy w polu danych i danych Box Grube są podsumowane w następujący sposób:

| Kategoria błędu*        | Opis        | Zalecana akcja    |
|----------------------------------------------|---------|--------------------------------------|
| Nazwy kontenerów lub udziałów | Nazwy kontenerów lub udziałów nie są zgodne z regułami nazewnictwa platformy Azure.  |Pobierz listy błędów. <br> Zmień nazwę kontenerów lub udziałów. [Dowiedz się więcej](#container-or-share-name-errors).  |
| Limit rozmiaru kontenera lub udziału | Łączna liczba danych w kontenerach lub udziałach przekracza limit platformy Azure.   |Pobierz listy błędów. <br> Zmniejsz ogólne dane w kontenerze lub udziale. [Dowiedz się więcej](#container-or-share-size-limit-errors).|
| Limit rozmiaru obiektu lub pliku | Obiekt lub pliki w kontenerach lub udziałach przekracza limit platformy Azure.|Pobierz listy błędów. <br> Zmniejsz rozmiar pliku w kontenerze lub udziale. [Dowiedz się więcej](#object-or-file-size-limit-errors). |    
| Dane lub typ pliku | Format danych lub typ pliku nie jest obsługiwany. |Pobierz listy błędów. <br> W przypadku obiektów blob stron lub dysków zarządzanych upewnij się, że dane są wyrównane do 512 bajtów i skopiowane do wstępnie utworzonych folderów. [Dowiedz się więcej](#data-or-file-type-errors). |
| Niekrytyczne błędy obiektów blob lub plików  | Nazwy obiektów blob lub plików nie są zgodne z regułami nazewnictwa platformy Azure lub typ pliku nie jest obsługiwany. | Te obiekty blob lub pliki nie mogą być kopiowane lub nazwy mogą zostać zmienione. [Dowiedz się, jak naprawić te błędy](#non-critical-blob-or-file-errors). |

\*Pierwsze cztery kategorie błędów są błędami krytycznymi i muszą zostać naprawione, zanim będzie można przystąpić do przygotowania do wysyłki.


## <a name="container-or-share-name-errors"></a>Błędy nazw kontenerów lub udziałów

Są to błędy związane z nazwami kontenerów i udziałów.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Opis błędu:** Nazwa kontenera lub udziału musi zawierać od 3 do 63 znaków. 

**Sugerowana rozdzielczość:** Folder w obszarze udziału Data Box lub Data Box Heavy(SMB/NFS), do którego skopiowano dane, staje się kontenerem platformy Azure na koncie magazynu. 

- Na stronie **Połącz i skopuj** lokalnego interfejsu użytkownika sieci Web urządzenia pobierz i przejrzyj pliki błędów, aby zidentyfikować nazwy folderów z problemami.
- Zmień nazwę folderu w obszarze udziału Data Box lub Data Box Heavy, aby upewnić się, że:

    - Nazwa ma od 3 do 63 znaków.
    - Nazwy mogą mieć tylko litery, cyfry i łączniki.
    - Nazwy nie mogą rozpoczynać się ani kończyć łącznikami.
    - Nazwy nie mogą mieć kolejnych łączników.
    - Przykłady prawidłowych `my-folder-1`nazw: ,`my-really-extra-long-folder-111`
    - Przykłady nazw, które nie `my-folder_1`są `my` `--myfolder`prawidłowe: , , , `myfolder--``myfolder!`

    Aby uzyskać więcej informacji, zobacz konwencje nazewnictwa platformy Azure dla [nazw kontenerów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) i [nazw udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Opis błędu:** Nazwa kontenera lub udziału musi składać się tylko z liter, cyfr lub łączników.

**Sugerowana rozdzielczość:** Folder w obszarze udziału Data Box lub Data Box Heavy(SMB/NFS), do którego skopiowano dane, staje się kontenerem platformy Azure na koncie magazynu. 

- Na stronie **Połącz i skopuj** lokalnego interfejsu użytkownika sieci Web urządzenia pobierz i przejrzyj pliki błędów, aby zidentyfikować nazwy folderów z problemami.
- Zmień nazwę folderu w obszarze udziału Data Box lub Data Box Heavy, aby upewnić się, że:

    - Nazwa ma od 3 do 63 znaków.
    - Nazwy mogą mieć tylko litery, cyfry i łączniki.
    - Nazwy nie mogą rozpoczynać się ani kończyć łącznikami.
    - Nazwy nie mogą mieć kolejnych łączników.
    - Przykłady prawidłowych `my-folder-1`nazw: ,`my-really-extra-long-folder-111`
    - Przykłady nazw, które nie `my-folder_1`są `my` `--myfolder`prawidłowe: , , , `myfolder--``myfolder!`

    Aby uzyskać więcej informacji, zobacz konwencje nazewnictwa platformy Azure dla [nazw kontenerów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) i [nazw udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Opis błędu:** Nazwy kontenerów i nazwy udziałów nie mogą rozpoczynać się ani kończyć łącznikami i nie mogą mieć kolejnych łączników.

**Sugerowana rozdzielczość:** Folder w obszarze udziału Data Box lub Data Box Heavy(SMB/NFS), do którego skopiowano dane, staje się kontenerem platformy Azure na koncie magazynu. 

- Na stronie **Połącz i skopuj** lokalnego interfejsu użytkownika sieci Web urządzenia pobierz i przejrzyj pliki błędów, aby zidentyfikować nazwy folderów z problemami.
- Zmień nazwę folderu w obszarze udziału Data Box lub Data Box Heavy, aby upewnić się, że:

    - Nazwa ma od 3 do 63 znaków.
    - Nazwy mogą mieć tylko litery, cyfry i łączniki.
    - Nazwy nie mogą rozpoczynać się ani kończyć łącznikami.
    - Nazwy nie mogą mieć kolejnych łączników.
    - Przykłady prawidłowych `my-folder-1`nazw: ,`my-really-extra-long-folder-111`
    - Przykłady nazw, które nie `my-folder_1`są `my` `--myfolder`prawidłowe: , , , `myfolder--``myfolder!`

    Aby uzyskać więcej informacji, zobacz konwencje nazewnictwa platformy Azure dla [nazw kontenerów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) i [nazw udziałów](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

## <a name="container-or-share-size-limit-errors"></a>Błędy limitu rozmiaru kontenera lub udziału

Są to błędy związane z danymi przekraczającymi rozmiar danych dozwolonych w kontenerze lub udziale.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Opis błędu:** Udział plików platformy Azure ogranicza udział do 5 TB danych. Limit ten został przekroczony dla niektórych akcji.

**Sugerowana rozdzielczość:** Na stronie **Połącz i skopuj** lokalnego interfejsu użytkownika sieci Web pobierz i przejrzyj pliki błędów.

Zidentyfikuj foldery, które mają ten problem z dzienników błędów i upewnij się, że pliki w tym folderze są poniżej 5 TB.


## <a name="object-or-file-size-limit-errors"></a>Błędy limitu rozmiaru obiektu lub pliku

Są to błędy związane z danymi przekraczającymi maksymalny rozmiar obiektu lub pliku, który jest dozwolony na platformie Azure. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Opis błędu:** Rozmiar pliku przekracza maksymalny rozmiar pliku do przekazania.

**Sugerowana rozdzielczość:** Rozmiary obiektów blob lub plików przekraczają maksymalny limit dozwolony dla przekazywania.

- Na stronie **Połącz i skopuj** lokalnego interfejsu użytkownika sieci Web pobierz i przejrzyj pliki błędów.
- Upewnij się, że rozmiary obiektów blob i plików nie przekraczają limitów rozmiaru obiektu platformy Azure.

## <a name="data-or-file-type-errors"></a>Błędy danych lub typu pliku

Są to błędy związane z nieobsługiconym typem pliku lub typem danych znalezionym w kontenerze lub udziale. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Opis błędu:** Obiekt blob lub plik jest niepoprawnie wyrównany.

**Sugerowana rozdzielczość:** Udział obiektu blob strony w polu danych lub data box grube obsługuje tylko pliki, które są wyrównane 512 bajtów (na przykład VHD / VHDX). Wszystkie dane skopiowane do udziału obiektu blob strony są przekazywane na platformę Azure jako obiekty blob strony.

Usuń wszystkie dane inne niż VHD/VHDX z udziału obiektu blob strony. Można użyć udziałów dla bloku obiektu blob lub plików platformy Azure dla danych ogólnych.

Aby uzyskać więcej informacji, zobacz [Omówienie obiektów blob strony](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Opis błędu:** Nieobsługiwał typ pliku w zarządzanym udziale dysku. Dozwolone są tylko stałe VHD.

**Sugerowana rozdzielczość:**

- Upewnij się, że tylko stałe dyski VHD do tworzenia dysków zarządzanych.
- Pliki VHDX lub **dynamiczne** i **różnicujące** dyski VHD nie są obsługiwane.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Opis błędu:** Katalog nie jest dozwolony w żadnym z istniejących folderów dysków zarządzanych. Tylko stałe VHD są dozwolone w tych folderach.

**Sugerowana rozdzielczość:** W przypadku dysków zarządzanych w ramach każdego udziału tworzone są następujące trzy foldery, które odpowiadają kontenerom na koncie magazynu: Dysk SSD premium, Standardowy dysk twardy i standardowy dysk SSD. Te foldery odpowiadają warstwie wydajności dysku zarządzanego.

- Upewnij się, że dane obiektu blob strony (VHD) do jednego z tych istniejących folderów.
- Folder lub katalog nie jest dozwolony w tych istniejących folderach. Usuń wszystkie foldery utworzone wewnątrz wcześniej istniejących folderów.

Aby uzyskać więcej informacji, zobacz [Kopiowanie do dysków zarządzanych](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Opis błędu:** Dowiązania symboliczne nie są dozwolone w systemie Linux. 

**Sugerowana rozdzielczość:** Dowiązania symboliczne są zwykle łącza, potoki i inne takie pliki. Usuń łącza lub rozwiąż łącza i skopiuj dane.


## <a name="non-critical-blob-or-file-errors"></a>Niekrytyczne błędy obiektów blob lub plików

Wszystkie błędy niekrytyczne związane z nazwami obiektów blob, plików lub kontenerów, które są widoczne podczas kopiowania danych, są podsumowane w poniższej sekcji. Jeśli te błędy są obecne, nazwy zostaną zmodyfikowane w celu dostosowania się do konwencji nazewnictwa platformy Azure. Odpowiedni stan zamówienia dla przekazywania danych zostanie **uzupełniony ostrzeżeniami**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Opis błędu:** Nazwy obiektów blob lub plików zawierają nieobsługiwały znaki kontrolne.

**Sugerowana rozdzielczość:** Skopiowane obiekty BLOB lub pliki zawierają nazwy z nieobsługiwzonymi znakami.

Na stronie **Połącz i skopuj** lokalnego interfejsu użytkownika sieci Web pobierz i przejrzyj pliki błędów.
Usuń pliki lub zmień ich nazwę, aby usunąć nieobsługiwały się znaki.

Aby uzyskać więcej informacji, zobacz konwencje nazewnictwa platformy Azure dla [nazw obiektów blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazw plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Opis błędu:** Nazwy obiektów blob lub plików zawierają niedozwolona liczba znaków.

**Sugerowana rozdzielczość:** Skopiowane obiekty BLOB lub pliki zawierają nazwy z nieobsługiwzonymi znakami.

Na stronie **Połącz i skopuj** lokalnego interfejsu użytkownika sieci Web pobierz i przejrzyj pliki błędów.
Usuń pliki lub zmień ich nazwę, aby usunąć nieobsługiwały się znaki.

Aby uzyskać więcej informacji, zobacz konwencje nazewnictwa platformy Azure dla [nazw obiektów blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazw plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Opis błędu:** Nazwy obiektów blob lub plików kończą się złymi znakami.

**Sugerowana rozdzielczość:** Skopiowane obiekty BLOB lub pliki zawierają nazwy z nieobsługiwzonymi znakami.

Na stronie **Połącz i skopuj** lokalnego interfejsu użytkownika sieci Web pobierz i przejrzyj pliki błędów.
Usuń pliki lub zmień ich nazwę, aby usunąć nieobsługiwały się znaki.

Aby uzyskać więcej informacji, zobacz konwencje nazewnictwa platformy Azure dla [nazw obiektów blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazw plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Opis błędu:** Nazwa obiektu blob lub pliku zawiera zbyt wiele segmentów ścieżki.

**Sugerowana rozdzielczość:** Skopiowane obiekty BLOB lub pliki przekraczają maksymalną liczbę segmentów ścieżki. Segment ścieżki jest ciągiem między kolejnymi znakami ogranicznika, na przykład ukośnikiem /.

- Na stronie **Połącz i skopuj** lokalnego interfejsu użytkownika sieci Web pobierz i przejrzyj pliki błędów.
- Upewnij się, że [nazwy obiektów blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazwy plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) są zgodne z konwencjami nazewnictwa platformy Azure.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Opis błędu:** Nazwa obiektu blob lub pliku jest za długa.

**Sugerowana rozdzielczość:** Nazwy obiektów blob lub plików przekraczają maksymalną długość.

- Na stronie **Połącz i skopuj** lokalnego interfejsu użytkownika sieci Web pobierz i przejrzyj pliki błędów.
- Nazwa obiektu blob nie może przekraczać 1024 znaków.
- Usuń lub zmień nazwę obiektu blob lub plików, tak aby nazwy nie przekraczać 1024 znaków.

Aby uzyskać więcej informacji, zobacz konwencje nazewnictwa platformy Azure dla nazw obiektów blob i nazw plików.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Opis błędu:** Jeden z segmentów w obiekcie blob lub nazwa pliku jest zbyt długi.

**Sugerowana rozdzielczość:** Jeden z segmentów ścieżki w nazwie obiektu blob lub pliku przekracza maksymalną liczbę znaków. Segment ścieżki jest ciągiem między kolejnymi znakami ogranicznika, na przykład ukośnikiem /.

- Na stronie **Połącz i skopuj** lokalnego interfejsu użytkownika sieci Web pobierz i przejrzyj pliki błędów.
- Upewnij się, że [nazwy obiektów blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) i [nazwy plików](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) są zgodne z konwencjami nazewnictwa platformy Azure.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Opis błędu:** Nieprawidłowe nazwy kontenerów są określone dla udziałów dysków zarządzanych.

**Sugerowana rozdzielczość:** W przypadku dysków zarządzanych w ramach każdego udziału tworzone są następujące foldery, które odpowiadają kontenerom na koncie magazynu: Dysk SSD premium, standardowy dysk twardy i standardowy dysk SSD. Te foldery odpowiadają warstwie wydajności dysku zarządzanego.

- Upewnij się, że dane obiektu blob strony (VHD) do jednego z tych istniejących folderów. Tylko dane z tych istniejących kontenerów są przekazywane na platformę Azure.
- Każdy inny folder utworzony na tym samym poziomie co dysk SSD premium, standardowy dysk twardy i standardowy dysk SSD nie odpowiada prawidłowej warstwie wydajności i nie może być używany.
- Usuń pliki lub foldery utworzone poza warstwami wydajności.

Aby uzyskać więcej informacji, zobacz [Kopiowanie do dysków zarządzanych](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wymaganiach systemowych przechowywania obiektów Blob.](data-box-system-requirements-rest.md)
