---
title: Transferowanie danych do lub z usługi Azure Blob Storage za pomocą AzCopy v10 | Microsoft Docs
description: Ten artykuł zawiera kolekcję przykładowych poleceń AzCopy, które pomagają w tworzeniu kontenerów, kopiowaniu plików i synchronizowaniu katalogów między lokalnymi systemami plików i kontenerami.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 736957ec68f592da74fc6903acade1150d253467
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72527034"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB

AzCopy to narzędzie wiersza polecenia, za pomocą którego można kopiować dane do, z lub między kontami magazynu. Ten artykuł zawiera przykładowe polecenia, które działają z usługą BLOB Storage.

## <a name="get-started"></a>Rozpocznij

Zapoznaj się z artykułem [wprowadzenie do AzCopy](storage-use-azcopy-v10.md) w celu pobrania AzCopy i Dowiedz się więcej na temat sposobów dostarczania poświadczeń autoryzacji do usługi magazynu.

> [!NOTE]
> W przykładach w tym artykule przyjęto założenie, że tożsamość została uwierzytelniona przy użyciu `AzCopy login` polecenia. AzCopy następnie używa konta usługi Azure AD do autoryzacji dostępu do danych w usłudze BLOB Storage.
>
> Jeśli wolisz używać tokenu SAS do autoryzacji dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu AzCopy.
>
> Na przykład: `'https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>'`.

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener, można użyć polecenia [AzCopy](storage-ref-azcopy-make.md) . Przykłady w tej sekcji tworzą kontener o nazwie `mycontainer`.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Przykład** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy Make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Przekazywanie plików

Aby przekazać pliki i katalogi z komputera lokalnego, można użyć polecenia [copy AzCopy](storage-ref-azcopy-copy.md) .

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Przekazywanie pliku
> * Przekaż katalog
> * Przekaż zawartość katalogu 
> * Przekazywanie określonych plików

> [!NOTE]
> AzCopy nie oblicza automatycznie ani nie zapisuje kodu skrótu MD5 pliku. Jeśli chcesz, aby AzCopy to zrobić, Dodaj flagę `--put-md5` do poszczególnych poleceń kopiowania. Dzięki temu po pobraniu obiektu BLOB AzCopy oblicza skrót MD5 dla pobranych danych i weryfikuje, czy skrót MD5 przechowywany we właściwości `Content-md5` obiektu BLOB jest zgodny z obliczonym skrótem.

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md).

### <a name="upload-a-file"></a>Przekazywanie pliku

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Przykład** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Możesz również przekazać plik za pomocą symbolu wieloznacznego (*) w dowolnym miejscu w ścieżce lub nazwie pliku. Na przykład: `'C:\myDirectory\*.txt'` lub `C:\my*\*.txt`.

> [!NOTE]
> AzCopy domyślnie przekazuje dane do blokowych obiektów BLOB. Aby przekazać pliki jako Dołącz obiekty blob lub stronicowe obiekty blob, Użyj flagi `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-directory"></a>Przekaż katalog

Ten przykład kopiuje katalog (i wszystkie pliki w tym katalogu) do kontenera obiektów BLOB. Wynik jest katalogiem w kontenerze o tej samej nazwie.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Aby skopiować do katalogu w kontenerze, należy po prostu określić nazwę tego katalogu w ciągu polecenia.

|    |     |
|--------|-----------|
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

W przypadku określenia nazwy katalogu, który nie istnieje w kontenerze, AzCopy tworzy nowy katalog o tej nazwie.

### <a name="upload-the-contents-of-a-directory"></a>Przekaż zawartość katalogu

Zawartość katalogu można przekazać bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Przykład** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Dołącz flagę `--recursive` w celu przekazywania plików we wszystkich podkatalogach.

### <a name="upload-specific-files"></a>Przekazywanie określonych plików

Możesz określić pełne nazwy plików lub użyć częściowych nazw z symbolami wieloznacznymi (*).

#### <a name="specify-multiple-complete-file-names"></a>Określ wiele pełnych nazw plików

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z opcją `--include-path`. Oddziel poszczególne nazwy plików przy użyciu średnika (`;`).

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

W tym przykładzie AzCopy transferuje katalog `C:\myDirectory\photos` i plik `C:\myDirectory\documents\myFile.txt`. Należy uwzględnić opcję `--recursive`, aby przenieść wszystkie pliki w katalogu `C:\myDirectory\photos`.

Można również wykluczać pliki przy użyciu opcji `--exclude-path`. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.


#### <a name="use-wildcard-characters"></a>Użyj symboli wieloznacznych

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z opcją `--include-pattern`. Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy przy użyciu semicolin (`;`). 

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczać pliki przy użyciu opcji `--exclude-pattern`. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

Opcje `--include-pattern` i `--exclude-pattern` mają zastosowanie tylko do nazw plików, a nie do ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj opcji `–recursive`, aby uzyskać całe drzewo katalogów, a następnie użyj `–include-pattern` i określ `*.txt`, aby pobrać wszystkie pliki tekstowe.

## <a name="download-files"></a>Pobieranie plików

Aby pobrać obiekty blob, katalogi i kontenery na komputer lokalny, można użyć polecenia [copy AzCopy](storage-ref-azcopy-copy.md) .

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Pobieranie pliku
> * Pobierz katalog
> * Pobierz zawartość katalogu
> * Pobieranie określonych plików

> [!NOTE]
> Jeśli wartość właściwości `Content-md5` obiektu BLOB zawiera skrót, AzCopy oblicza skrót MD5 dla pobranych danych i weryfikuje, czy skrót MD5 przechowywany we właściwości `Content-md5` obiektu BLOB jest zgodny z obliczonym skrótem. Jeśli te wartości nie są zgodne, pobieranie nie powiedzie się, o ile nie zastąpisz tego zachowania, dołączając `--check-md5=NoCheck` lub `--check-md5=LogOnly` do polecenia copy.

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md).

### <a name="download-a-file"></a>Pobieranie pliku

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Pobierz katalog

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory 'C:\myDirectory'  --recursive` |

Ten przykład powoduje, że katalog o nazwie `C:\myDirectory\myBlobDirectory` zawiera wszystkie pobrane pliki.

### <a name="download-the-contents-of-a-directory"></a>Pobierz zawartość katalogu

Zawartość katalogu można pobrać bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko w przypadku kont, które nie mają hierarchicznej przestrzeni nazw.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Dołącz flagę `--recursive`, aby pobrać pliki we wszystkich podkatalogach.

### <a name="download-specific-files"></a>Pobieranie określonych plików

Możesz określić pełne nazwy plików lub użyć częściowych nazw z symbolami wieloznacznymi (*).

#### <a name="specify-multiple-complete-file-names"></a>Określ wiele pełnych nazw plików

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z opcją `--include-path`. Oddziel poszczególne nazwy plików przy użyciu semicolin (`;`).

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

W tym przykładzie AzCopy transferuje katalog `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` i plik `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt`. Należy uwzględnić opcję `--recursive`, aby przenieść wszystkie pliki w katalogu `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos`.

Można również wykluczać pliki przy użyciu opcji `--exclude-path`. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Użyj symboli wieloznacznych

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z opcją `--include-pattern`. Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy przy użyciu semicolin (`;`).

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczać pliki przy użyciu opcji `--exclude-pattern`. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

Opcje `--include-pattern` i `--exclude-pattern` mają zastosowanie tylko do nazw plików, a nie do ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj opcji `–recursive`, aby uzyskać całe drzewo katalogów, a następnie użyj `–include-pattern` i określ `*.txt`, aby pobrać wszystkie pliki tekstowe.

## <a name="copy-blobs-between-storage-accounts"></a>Kopiowanie obiektów BLOB między kontami magazynu

Możesz użyć AzCopy, aby skopiować obiekty blob do innych kont magazynu. Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zwróci wartość, która wskazuje, że wszystkie pliki zostały skopiowane. 

AzCopy używa [interfejsów API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [serwer-serwer](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , aby dane były kopiowane bezpośrednio między serwerami magazynu. Te operacje kopiowania nie korzystają z przepustowości sieci komputera. Przepływność tych operacji można zwiększyć, ustawiając wartość zmiennej środowiskowej `AZCOPY_CONCURRENCY_VALUE`. Aby dowiedzieć się więcej, zobacz [Optymalizowanie przepływności](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Ten scenariusz ma następujące ograniczenia w bieżącej wersji.
>
> - Obsługiwane są tylko konta, które nie mają hierarchicznej przestrzeni nazw.
> - Musisz dołączyć token sygnatury dostępu współdzielonego do każdego źródłowego adresu URL. W przypadku podania poświadczeń autoryzacji za pomocą Azure Active Directory (AD) można pominąć token sygnatury dostępu współdzielonego tylko z docelowego adresu URL.
>-  Konta magazynu blokowych obiektów BLOB w warstwie Premium nie obsługują warstw dostępu. Pomiń warstwę dostępu obiektu BLOB z operacji kopiowania, ustawiając wartość `s2s-preserve-access-tier` na `false` (na przykład: `--s2s-preserve-access-tier=false`).

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Kopiowanie obiektu BLOB do innego konta magazynu
> * Kopiowanie katalogu do innego konta magazynu
> * Kopiowanie kontenera na inne konto magazynu
> * Kopiuj wszystkie kontenery, katalogi i pliki na inne konto magazynu

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md).

### <a name="copy-a-blob-to-another-storage-account"></a>Kopiowanie obiektu BLOB do innego konta magazynu

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiowanie katalogu do innego konta magazynu

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Kopiowanie kontenera na inne konto magazynu

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Kopiuj wszystkie kontenery, katalogi i obiekty blob na inne konto magazynu

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive'` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Synchronizuj pliki

Zawartość lokalnego systemu plików można zsynchronizować z kontenerem obiektów BLOB. Możesz również synchronizować kontenery i katalogi wirtualne ze sobą. Synchronizacja jest jednokierunkowa. Innymi słowy, możesz wybrać, które z tych dwóch punktów końcowych są źródłem, a które są lokalizacją docelową. Synchronizacja używa także serwera do interfejsów API serwera.

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko w przypadku kont, które nie mają hierarchicznej przestrzeni nazw. Bieżąca wersja AzCopy nie jest synchronizowana między innymi źródłami i miejscami docelowymi (na przykład: Magazyn plików lub Amazon Web Services (AWS).

Polecenie [Sync](storage-ref-azcopy-sync.md) porównuje nazwy plików i ostatnio modyfikowane sygnatury czasowe. Ustaw opcjonalną flagę `--delete-destination` na wartość `true` lub `prompt`, aby usunąć pliki w katalogu docelowym, jeśli te pliki nie znajdują się już w katalogu źródłowym.

Jeśli ustawisz flagę `--delete-destination` do `true` AzCopy usuwa pliki bez wyświetlania monitu. Jeśli chcesz, aby monit pojawił się zanim AzCopy usunie plik, Ustaw flagę `--delete-destination` na `prompt`.

> [!NOTE]
> Aby zapobiec przypadkowemu usunięciu, upewnij się, że funkcja [usuwania nietrwałego](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) została włączona przed użyciem flagi `--delete-destination=prompt|true`.

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy Sync](storage-ref-azcopy-sync.md).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualizowanie kontenera ze zmianami w lokalnym systemie plików

W takim przypadku kontener jest miejscem docelowym, a lokalny system plików jest źródłem.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualizowanie lokalnego systemu plików przy użyciu zmian w kontenerze

W takim przypadku lokalny system plików jest miejscem docelowym, a kontener jest źródłem.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Przykład** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |
|

### <a name="update-a-container-with-changes-in-another-container"></a>Aktualizowanie kontenera ze zmianami w innym kontenerze

Pierwszym kontenerem, który pojawia się w tym poleceniu jest źródło. Drugim z nich jest miejsce docelowe.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualizowanie katalogu ze zmianami w katalogu w innym udziale plików

Pierwszym katalogiem, który pojawia się w tym poleceniu jest źródło. Drugim z nich jest miejsce docelowe.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Następne kroki

Więcej przykładów znajdziesz w jednym z następujących artykułów:

- [Wprowadzenie do AzCopy](storage-use-azcopy-v10.md)

- [Samouczek: Migrowanie danych lokalnych do magazynu w chmurze za pomocą AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transferowanie danych za pomocą AzCopy i magazynu plików](storage-use-azcopy-files.md)

- [Transferowanie danych za pomocą zasobników AzCopy i Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)
