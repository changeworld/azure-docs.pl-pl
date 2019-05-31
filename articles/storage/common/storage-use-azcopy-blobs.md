---
title: Transfer danych do lub z usługi Azure Blob storage za pomocą narzędzia AzCopy v10 | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zbiór AzCopy przykład poleceń, które ułatwiają tworzenie kontenerów, skopiuj pliki i zsynchronizować folderów między systemami plików lokalnych i kontenerów.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 98e33f838ee9b6f506bf1dc01e1dd61ad587aa05
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299406"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Transferowanie danych za pomocą narzędzia AzCopy i Blob storage

AzCopy to narzędzie wiersza polecenia, który służy do kopiowania danych do z lub między kontami magazynu. Ten artykuł zawiera przykładowe polecenia, które działają z usługą Blob storage.

## <a name="get-started"></a>Rozpoczęcie pracy

Zobacz [Rozpoczynanie pracy za pomocą narzędzia AzCopy](storage-use-azcopy-v10.md) artykuł, aby pobrać narzędzia AzCopy i informacje o sposobach zapewniają poświadczenia autoryzacji do usługi storage.

> [!NOTE]
> Przykłady w niniejszym artykule przyjęto założenie, że uwierzytelniono swoją tożsamość za pomocą `AzCopy login` polecenia. Narzędzie AzCopy następnie używa konta usługi Azure AD do autoryzowania dostępu do danych w magazynie obiektów Blob.
>
> Jeśli zostanie wykorzystany raczej tokenu sygnatury dostępu Współdzielonego do autoryzowania dostępu do danych obiektów blob, można dołączyć ten token do adresu URL zasobu w każdym poleceniu narzędzia AzCopy.
>
> Na przykład: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Tworzenie kontenera

Możesz użyć narzędzia AzCopy `make` polecenie, aby utworzyć kontener. Przykłady w tej sekcji utworzysz kontener o nazwie `mycontainer`.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Przykład** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Przekazywanie plików

Możesz użyć narzędzia AzCopy `copy` polecenie, aby przekazać pliki i foldery z komputera lokalnego.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Przekazywanie pliku
> * Przekazywanie folderu
> * Przekazywanie plików przy użyciu symboli wieloznacznych

> [!NOTE]
> Narzędzie AzCopy nie automatycznie obliczyć i Zapisz wartość skrótu md5 pliku. Jeśli narzędzie AzCopy, aby to zrobić, należy następnie dołącz `--put-md5` Flaga każdego polecenia Kopiuj. W ten sposób podczas pobierania obiektu blob AzCopy oblicza Skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywanych w obiekcie blob `Content-md5` właściwość odpowiada skrót obliczony.

### <a name="upload-a-file"></a>Przekazywanie pliku

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Przykład** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> Narzędzie AzCopy domyślnie przekazuje dane do blokowych obiektów blob. Aby przekazać pliki jako obiekty BLOB dołączania lub stronicowe obiekty BLOB, Użyj flagi `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-folder"></a>Przekazywanie folderu

W tym przykładzie kopiuje folder (i wszystkich plików w tym folderze) do kontenera obiektów blob. Wynik jest folderem w kontenerze o tej samej nazwie.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Przykład** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Aby skopiować do folderu, w tym kontenerze, określ nazwę tego folderu, w ciągu polecenia.

|    |     |
|--------|-----------|
| **Przykład** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder" --recursive` |

Jeśli określisz nazwę folderu, który nie istnieje w kontenerze, narzędzia AzCopy tworzy nowy folder o takiej nazwie.

### <a name="upload-the-contents-of-a-folder"></a>Załaduj zawartość folderu

Możesz przekazać zawartość folderu bez kopiowania folderu zawierającego za pomocą symbolu wieloznacznego (*).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "<local-folder-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>` |
| **Przykład** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder"` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder"` |

> [!NOTE]
> Dołącz `--recursive` flagi, aby przekazać pliki w podfolderach, wszystkie.

## <a name="download-files"></a>Pobieranie plików

Możesz użyć narzędzia AzCopy `copy` polecenie, aby pobrać obiekty BLOB, folderów i kontenery na komputerze lokalnym.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Pobieranie pliku
> * Folder pobierania
> * Pobieranie plików przy użyciu symboli wieloznacznych

> [!NOTE]
> Jeśli `Content-md5` wartości właściwości obiektu blob zawiera skrót, narzędzia AzCopy oblicza Skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywanych w obiekcie blob `Content-md5` właściwość odpowiada skrót obliczony. Jeśli te wartości nie są zgodne, pliki do pobrania zakończy się niepowodzeniem, chyba że zastąpienia tego zachowania, dodając `--check-md5=NoCheck` lub `--check-md5=LogOnly` do polecenia Kopiuj.

### <a name="download-a-file"></a>Pobieranie pliku

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Przykład** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Folder pobierania

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>" "<local-folder-path>" --recursive` |
| **Przykład** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |

Ten przykład powoduje zwrócenie folder o nazwie `C:\myFolder\myBlobFolder` zawierający wszystkie pobrane pliki.

### <a name="download-the-contents-of-a-folder"></a>Pobierz zawartość folderu

Możesz pobrać zawartość folderu bez kopiowania folderu zawierającego za pomocą symbolu wieloznacznego (*).

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko dla kont, które nie mają hierarchicznej przestrzeni nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-folder-path>/"` |
| **Przykład** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder/*" "C:\myFolder"` |

> [!NOTE]
> Dołącz `--recursive` flagi do pobierania plików we wszystkich podrzędnych folderów.

## <a name="copy-blobs-between-storage-accounts"></a>Kopiowanie obiektów blob między kontami magazynu

Za pomocą narzędzia AzCopy do kopiowania obiektów blob na inne konta magazynu. Operacja kopiowania jest synchroniczna, dlatego gdy polecenie jest zwracane, oznacza to, że wszystkie pliki zostały skopiowane.

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko dla kont, które nie mają hierarchicznej przestrzeni nazw.

Korzysta z narzędzia AzCopy [umieścić blok z adresu URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) interfejsu API, dzięki czemu dane są kopiowane bezpośrednio między serwerami magazynu. Przepustowość sieci na komputerze nie należy używać tych operacji kopiowania.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Skopiuj obiekt blob do innego konta magazynu
> * Skopiuj folder do innego konta magazynu
> * Kopiowanie kontenerów do innego konta magazynu
> * Skopiuj wszystkie kontenery, foldery i pliki do innego konta magazynu

### <a name="copy-a-blob-to-another-storage-account"></a>Skopiuj obiekt blob do innego konta magazynu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Przykład** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-folder-to-another-storage-account"></a>Skopiuj folder do innego konta magazynu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" --recursive` |
| **Przykład** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobFolder" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Kopiowanie kontenerów do innego konta magazynu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Przykład** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-folders-and-files-to-another-storage-account"></a>Skopiuj wszystkie kontenery, foldery i pliki do innego konta magazynu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Przykład** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Synchronizuj pliki

Możesz zsynchronizować zawartość z lokalnego systemu plików do kontenera obiektów blob. Możesz także zsynchronizować kontenerem obiektów blob do lokalnego systemu plików na komputerze. Synchronizacja jest jednokierunkowa. Innymi słowy możesz wybrać który te dwa punkty końcowe jest źródłem, a który z nich jest miejscem docelowym.

> [!NOTE]
> Bieżąca wersja programu AzCopy nie Synchronizacja pomiędzy innych źródeł i miejsc docelowych (na przykład: Magazyn plików lub pakiety Amazon Web Services (AWS) S3).

`sync` Polecenia porównuje nazwy plików i Data ostatniej modyfikacji sygnatur czasowych. Ustaw `--delete-destination` opcjonalna Flaga wartości `true` lub `prompt` można usunąć pliki w folderze docelowym, jeśli te pliki nie są już istnieje w folderze źródłowym.

Jeśli ustawisz `--delete-destination` flaga `true` AzCopy usuwa pliki bez podawania w wierszu. Jeśli wiersz znajdować się przed AzCopy usuwa plik, należy ustawić `--delete-destination` flaga `prompt`.

> [!NOTE]
> Aby zapobiec przypadkowym, upewnij się umożliwić [usuwania nietrwałego](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkcji przed użyciem `--delete-destination=prompt|true` flagi.

### <a name="synchronize-a-container-to-a-local-file-system"></a>Synchronizuj kontener służący do lokalnego systemu plików

W takim przypadku w lokalnym systemie plików źródłem i kontenera jest miejscem docelowym.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Przykład** | `azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy sync "C:\myFolder" "https://<storage-account-name>.dfs.core.windows.net/mycontainer" --recursive` |


### <a name="synchronize-a-local-file-system-to-a-container"></a>Zsynchronizować lokalny system plików do kontenera

W tym przypadku kontener, staje się źródła i lokalnego systemu plików jest miejscem docelowym.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myFolder" --recursive` |
| **Przykład** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myFolder" --recursive` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy sync "https://mystorageaccount.dfs.core.windows.net/mycontainer" "C:\myFolder" --recursive` |

## <a name="next-steps"></a>Kolejne kroki

Znajdź więcej przykładów w dowolnym z następujących artykułów:

- [Rozpoczynanie pracy za pomocą narzędzia AzCopy](storage-use-azcopy-v10.md)

- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transferowanie danych za pomocą narzędzia AzCopy oraz plików magazynu](storage-use-azcopy-files.md)

- [Transferowanie danych za pomocą narzędzia AzCopy i Amazon S3 przedziałów](storage-use-azcopy-s3.md)

- [Konfigurowanie optymalizacji i rozwiązywanie problemów z narzędzia AzCopy](storage-use-azcopy-configure.md)