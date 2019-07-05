---
title: Transfer danych do lub z usługi Azure Blob storage za pomocą narzędzia AzCopy v10 | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zbiór AzCopy przykład poleceń, które ułatwiają tworzenie kontenerów, skopiuj pliki i synchronizację katalogów między systemami plików lokalnych i kontenerów.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: f95af348eb11abee5a46a89e08da5bf4eb873c42
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566143"
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

Możesz użyć narzędzia AzCopy `copy` polecenie, aby przekazać pliki i katalogi z komputera lokalnego.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Przekazywanie pliku
> * Przekaż katalogu
> * Przekazywanie plików przy użyciu symboli wieloznacznych

> [!NOTE]
> Narzędzie AzCopy nie automatycznie obliczyć i Zapisz wartość skrótu md5 pliku. Jeśli narzędzie AzCopy, aby to zrobić, należy następnie dołącz `--put-md5` Flaga każdego polecenia Kopiuj. W ten sposób podczas pobierania obiektu blob AzCopy oblicza Skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywanych w obiekcie blob `Content-md5` właściwość odpowiada skrót obliczony.

### <a name="upload-a-file"></a>Przekazywanie pliku

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Przykład** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> Narzędzie AzCopy domyślnie przekazuje dane do blokowych obiektów blob. Aby przekazać pliki jako obiekty BLOB dołączania lub stronicowe obiekty BLOB, Użyj flagi `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-directory"></a>Przekaż katalogu

W tym przykładzie kopiuje katalog (i wszystkich plików w tym katalogu) do kontenera obiektów blob. Wynik jest katalogiem w kontenerze o tej samej nazwie.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Przykład** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Aby skopiować do katalogu, w tym kontenerze, określ nazwę tego katalogu w ciągu polecenia.

|    |     |
|--------|-----------|
| **Przykład** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Jeśli określisz nazwę katalogu, który nie istnieje w kontenerze, narzędzia AzCopy tworzy nowy katalog o tej nazwie.

### <a name="upload-the-contents-of-a-directory"></a>Przekazywanie zawartości katalogu

Możesz przekazać zawartość katalogu bez kopiowania sam katalog zawierający za pomocą symbolu wieloznacznego (*).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Przykład** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> Dołącz `--recursive` flagi, aby przekazać pliki we wszystkich katalogach podrzędnych.

## <a name="download-files"></a>Pobieranie plików

Możesz użyć narzędzia AzCopy `copy` polecenie, aby pobrać obiekty BLOB, katalogów i kontenery na komputerze lokalnym.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Pobieranie pliku
> * Pobierz katalog
> * Pobieranie plików przy użyciu symboli wieloznacznych

> [!NOTE]
> Jeśli `Content-md5` wartości właściwości obiektu blob zawiera skrót, narzędzia AzCopy oblicza Skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywanych w obiekcie blob `Content-md5` właściwość odpowiada skrót obliczony. Jeśli te wartości nie są zgodne, pliki do pobrania zakończy się niepowodzeniem, chyba że zastąpienia tego zachowania, dodając `--check-md5=NoCheck` lub `--check-md5=LogOnly` do polecenia Kopiuj.

### <a name="download-a-file"></a>Pobieranie pliku

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Przykład** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Pobierz katalog

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Przykład** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Przykład** (hierarchicznej przestrzeni nazw) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

Ten przykład powoduje zwrócenie katalog o nazwie `C:\myDirectory\myBlobDirectory` zawierający wszystkie pobrane pliki.

### <a name="download-the-contents-of-a-directory"></a>Pobieranie zawartości katalogu

Bez kopiowania sam katalog zawierający za pomocą symbolu wieloznacznego (*), można pobrać zawartości katalogu.

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko dla kont, które nie mają hierarchicznej przestrzeni nazw.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Przykład** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> Dołącz `--recursive` flagę, aby pobrać pliki we wszystkich katalogach podrzędnych.

## <a name="copy-blobs-between-storage-accounts"></a>Kopiowanie obiektów blob między kontami magazynu

Za pomocą narzędzia AzCopy do kopiowania obiektów blob na inne konta magazynu. Operacja kopiowania jest synchroniczna, dlatego gdy polecenie jest zwracane, oznacza to, że wszystkie pliki zostały skopiowane.

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko dla kont, które nie mają hierarchicznej przestrzeni nazw. 

Korzysta z narzędzia AzCopy [umieścić blok z adresu URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) interfejsu API, dzięki czemu dane są kopiowane bezpośrednio między serwerami magazynu. Przepustowość sieci na komputerze nie należy używać tych operacji kopiowania.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Skopiuj obiekt blob do innego konta magazynu
> * Kopiowanie katalogu do innego konta magazynu
> * Kopiowanie kontenerów do innego konta magazynu
> * Skopiuj wszystkie kontenery, katalogi i pliki do innego konta magazynu

> [!NOTE]
> W bieżącej wersji, musisz dołączyć tokenu sygnatury dostępu Współdzielonego do poszczególnych źródłowy adres URL. Jeśli zostaną podane poświadczenia autoryzacji za pomocą usługi Azure Active Directory (AD), możesz pominąć tokenu sygnatury dostępu Współdzielonego tylko z docelowego adresu URL. 

### <a name="copy-a-blob-to-another-storage-account"></a>Skopiuj obiekt blob do innego konta magazynu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Przykład** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiowanie katalogu do innego konta magazynu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" --recursive` |
| **Przykład** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Kopiowanie kontenerów do innego konta magazynu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Przykład** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>Skopiuj wszystkie kontenery, katalogi i pliki do innego konta magazynu

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Przykład** | `azcopy cp "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Synchronizuj pliki

Zawartość lokalny system plików można synchronizować z kontenera obiektów blob. Synchronizacja jest jednokierunkowa. Innymi słowy możesz wybrać który te dwa punkty końcowe jest źródłem, a który z nich jest miejscem docelowym.

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko dla kont, które nie mają hierarchicznej przestrzeni nazw. Bieżąca wersja programu AzCopy nie Synchronizacja pomiędzy innych źródeł i miejsc docelowych (na przykład: Magazyn plików lub pakiety Amazon Web Services (AWS) S3).

`sync` Polecenia porównuje nazwy plików i Data ostatniej modyfikacji sygnatur czasowych. Ustaw `--delete-destination` opcjonalna Flaga wartości `true` lub `prompt` można usunąć pliki w katalogu docelowym, jeśli te pliki nie istnieją już w katalogu źródłowym.

Jeśli ustawisz `--delete-destination` flaga `true` AzCopy usuwa pliki bez podawania w wierszu. Jeśli wiersz znajdować się przed AzCopy usuwa plik, należy ustawić `--delete-destination` flaga `prompt`.

> [!NOTE]
> Aby zapobiec przypadkowym, upewnij się umożliwić [usuwania nietrwałego](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkcji przed użyciem `--delete-destination=prompt|true` flagi.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Zaktualizuj kontenera, wprowadzając zmiany w lokalnym systemie plików

W tym przypadku kontener jest miejscem docelowym, a w lokalnym systemie plików jest źródłem.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Przykład** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Zaktualizuj lokalny system plików, wprowadzając zmiany w kontenerze

W takim przypadku w lokalnym systemie plików jest miejscem docelowym, a kontener jest źródłem.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Przykład** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>Kolejne kroki

Znajdź więcej przykładów w dowolnym z następujących artykułów:

- [Rozpoczynanie pracy za pomocą narzędzia AzCopy](storage-use-azcopy-v10.md)

- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transferowanie danych za pomocą narzędzia AzCopy oraz plików magazynu](storage-use-azcopy-files.md)

- [Transferowanie danych za pomocą narzędzia AzCopy i Amazon S3 przedziałów](storage-use-azcopy-s3.md)

- [Konfigurowanie optymalizacji i rozwiązywanie problemów z narzędzia AzCopy](storage-use-azcopy-configure.md)