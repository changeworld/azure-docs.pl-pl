---
title: Przesyłanie danych do lub z magazynu obiektów Blob platformy Azure przy użyciu programu AzCopy w wersji 10 | Dokumenty firmy Microsoft
description: Ten artykuł zawiera kolekcję przykładowych poleceń AzCopy, które ułatwiają tworzenie kontenerów, kopiowanie plików i synchronizowanie katalogów między lokalnymi systemami plików i kontenerami.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 73685f124f93bb541f33b3b70727d90ce22b3cdd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263441"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Przesyłanie danych za pomocą pamięci masowej AzCopy i Blob

AzCopy to narzędzie wiersza polecenia, za pomocą którego można kopiować dane do, z lub między kontami magazynu. Ten artykuł zawiera przykładowe polecenia, które współpracują z magazynem obiektów Blob.

> [!TIP]
> Przykłady w tym artykule ująć argumenty ścieżki z pojedynczymi cudzysłowami (''). Użyj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), należy ująć argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczych cudzysłowów (').

## <a name="get-started"></a>Rozpoczęcie pracy

Zobacz artykuł [Wprowadzenie do azcopy,](storage-use-azcopy-v10.md) aby pobrać azcopy i dowiedzieć się, w jaki sposób można podać poświadczenia autoryzacji do usługi magazynu.

> [!NOTE]
> Przykłady w tym artykule zakładają, że uwierzytelniłeś `AzCopy login` swoją tożsamość za pomocą polecenia. AzCopy następnie używa konta usługi Azure AD do autoryzowania dostępu do danych w magazynie obiektów Blob.
>
> Jeśli wolisz używać tokenu Sygnatury dostępu Współdzielonego do autoryzowania dostępu do danych obiektów blob, możesz dołączyć ten token do adresu URL zasobu w każdym poleceniu AzCopy.
>
> Na przykład: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Tworzenie kontenera

Aby utworzyć kontener, można użyć polecenia [azcopy make.](storage-ref-azcopy-make.md) Przykłady w tej sekcji utworzyć `mycontainer`kontener o nazwie .

|    |     |
|--------|-----------|
| **Składni** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Przykład** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Szczegółowe dokumenty referencyjne można znaleźć [w pliku azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Przekazywanie plików

Za pomocą polecenia [kopiowania azcopy](storage-ref-azcopy-copy.md) można przesyłać pliki i katalogi z komputera lokalnego.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Przekazywanie pliku
> * Przekazywanie katalogu
> * Przekazywanie zawartości katalogu 
> * Przekazywanie określonych plików

> [!TIP]
> Operację przesyłania można dostosować, korzystając z opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Przekaż pliki jako dołączanie obiektów blob lub stronicowych obiektów blob.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Przekaż do określonej warstwy dostępu (takiej jak warstwa archiwum).|**--block-blob-tier**=\[\|Brak\|\|Hot Cool Archiwum\]|
> |Automatyczna dekompresja plików.|**--dekompresja**=\[\|gzip deflate\]|
> 
> Aby uzyskać pełną listę, zobacz [opcje](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Przekazywanie pliku

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Przykład** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Plik można również przekazać za pomocą symbolu wieloznacznego (*) w dowolnym miejscu ścieżki pliku lub nazwy pliku. Na przykład: `'C:\myDirectory\*.txt'` `C:\my*\*.txt`, lub .

### <a name="upload-a-directory"></a>Przekazywanie katalogu

W tym przykładzie kopiuje katalog (i wszystkie pliki w tym katalogu) do kontenera obiektów blob. Wynikiem jest katalog w kontenerze o tej samej nazwie.

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Aby skopiować do katalogu w kontenerze, wystarczy określić nazwę tego katalogu w ciągu polecenia.

|    |     |
|--------|-----------|
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Jeśli określisz nazwę katalogu, który nie istnieje w kontenerze, AzCopy utworzy nowy katalog o tej nazwie.

### <a name="upload-the-contents-of-a-directory"></a>Przekazywanie zawartości katalogu

Zawartość katalogu można przekazać bez kopiowania samego katalogu zawierającego za pomocą symbolu wieloznacznego (*).

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Przykład** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Dołącz flagę, `--recursive` aby przekazać pliki we wszystkich podkatarzy.

### <a name="upload-specific-files"></a>Przekazywanie określonych plików

Można określić pełne nazwy plików lub użyć nazw częściowych ze znakami wieloznaczowymi (*).

#### <a name="specify-multiple-complete-file-names"></a>Określanie wielu kompletnych nazw plików

Użyj polecenia [kopiowania azcopy](storage-ref-azcopy-copy.md) z `--include-path` opcją. Oddziel poszczególne nazwy plików za`;`pomocą średnika ( ).

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

W tym przykładzie AzCopy przenosi `C:\myDirectory\photos` `C:\myDirectory\documents\myFile.txt` katalog i plik. Należy dołączyć `--recursive` opcję, aby przenieść `C:\myDirectory\photos` wszystkie pliki w katalogu.

Można również wykluczyć pliki `--exclude-path` za pomocą tej opcji. Aby dowiedzieć się więcej, zobacz dokumenty odwołania do [kopii azcopy.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Używanie symboli wieloznacznych

Użyj polecenia [kopiowania azcopy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją. Określ nazwy częściowe, które zawierają symbole wieloznaczne. Oddziel nazwy za pomocą`;`półkoliny ( ). 

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczyć pliki `--exclude-pattern` za pomocą tej opcji. Aby dowiedzieć się więcej, zobacz dokumenty odwołania do [kopii azcopy.](storage-ref-azcopy-copy.md)

Opcje `--include-pattern` `--exclude-pattern` i dotyczą tylko nazwy plików, a nie ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w `–recursive` drzewie katalogów, użyj opcji, `–include-pattern` aby `*.txt` uzyskać całe drzewo katalogów, a następnie użyj i określ, aby uzyskać wszystkie pliki tekstowe.

## <a name="download-files"></a>Pobieranie plików

Za pomocą polecenia [kopiowania azcopy](storage-ref-azcopy-copy.md) można pobrać obiekty BLOB, katalogi i kontenery na komputer lokalny.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Pobieranie pliku
> * Pobieranie katalogu
> * Pobieranie zawartości katalogu
> * Pobieranie określonych plików

> [!TIP]
> Możesz dostosować operację pobierania, używając opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Automatyczna dekompresja plików.|**--dekompresja**=\[\|gzip deflate\]|
> |Określ, jak szczegółowe mają być wpisy dziennika związane z kopiowaniem.|**--log-level**=\[\|OSTRZEŻENIE\|\|BŁĄD INFO BRAK\]|
> |Określ, czy i jak zastąpić pliki i obiekty blob powodujące konflikt w miejscu docelowym.|**--zastąpić**=\[true\|false\|ifSourceNewer\|monit\]|
> 
> Aby uzyskać pełną listę, zobacz [opcje](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Jeśli `Content-md5` wartość właściwości obiektu blob zawiera skrót, AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót `Content-md5` MD5 przechowywany we właściwości obiektu blob jest zgodny z obliczonym skrótem. Jeśli te wartości nie są zgodne, pobieranie nie powiedzie się, `--check-md5=NoCheck` `--check-md5=LogOnly` chyba że zostanie zastąpione to zachowanie przez dołączenie lub polecenie copy.

### <a name="download-a-file"></a>Pobieranie pliku

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Pobieranie katalogu

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

W tym przykładzie jest `C:\myDirectory\myBlobDirectory` to katalog o nazwie, który zawiera wszystkie pobrane pliki.

### <a name="download-the-contents-of-a-directory"></a>Pobieranie zawartości katalogu

Zawartość katalogu można pobrać bez kopiowania samego katalogu zawierającego za pomocą symbolu wieloznacznego (*).

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko dla kont, które nie mają hierarchicznego obszaru nazw.

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Dołącz flagę, `--recursive` aby pobrać pliki we wszystkich podkatarzy.

### <a name="download-specific-files"></a>Pobieranie określonych plików

Można określić pełne nazwy plików lub użyć nazw częściowych ze znakami wieloznaczowymi (*).

#### <a name="specify-multiple-complete-file-names"></a>Określanie wielu kompletnych nazw plików

Użyj polecenia [kopiowania azcopy](storage-ref-azcopy-copy.md) z `--include-path` opcją. Oddziel poszczególne nazwy plików za`;`pomocą średnika ( ).

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

W tym przykładzie AzCopy przenosi `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` katalog i plik. Należy dołączyć `--recursive` opcję, aby przenieść `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` wszystkie pliki w katalogu.

Można również wykluczyć pliki `--exclude-path` za pomocą tej opcji. Aby dowiedzieć się więcej, zobacz dokumenty odwołania do [kopii azcopy.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Używanie symboli wieloznacznych

Użyj polecenia [kopiowania azcopy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją. Określ nazwy częściowe, które zawierają symbole wieloznaczne. Oddziel nazwy za pomocą`;`półkoliny ( ).

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczyć pliki `--exclude-pattern` za pomocą tej opcji. Aby dowiedzieć się więcej, zobacz dokumenty odwołania do [kopii azcopy.](storage-ref-azcopy-copy.md)

Opcje `--include-pattern` `--exclude-pattern` i dotyczą tylko nazwy plików, a nie ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w `–recursive` drzewie katalogów, użyj opcji, `–include-pattern` aby `*.txt` uzyskać całe drzewo katalogów, a następnie użyj i określ, aby uzyskać wszystkie pliki tekstowe.

## <a name="copy-blobs-between-storage-accounts"></a>Kopiowanie obiektów blob między kontami magazynu

Za pomocą programu AzCopy można kopiować obiekty blob na inne konta magazynu. Operacja kopiowania jest synchroniczowa, więc po powrocie polecenia oznacza to, że wszystkie pliki zostały skopiowane. 

AzCopy używa [interfejsów API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [serwera do serwera,](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) dzięki czemu dane są kopiowane bezpośrednio między serwerami magazynu. Te operacje kopiowania nie używają przepustowości sieci komputera. Można zwiększyć przepływność tych operacji, ustawiając `AZCOPY_CONCURRENCY_VALUE` wartość zmiennej środowiskowej. Aby dowiedzieć się więcej, zobacz [Optymalizowanie przepływności](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Ten scenariusz ma następujące ograniczenia w bieżącej wersji.
>
> - Musisz dołączyć token sygnatury dostępu Współdzielonego do każdego źródłowego adresu URL. Jeśli podasz poświadczenia autoryzacji przy użyciu usługi Azure Active Directory (AD), można pominąć token sygnatury dostępu Współdzielonego tylko z docelowego adresu URL.
>-  Konta magazynu obiektów blob programu Premium nie obsługują warstw dostępu. Pomiń warstwę dostępu obiektu blob z `s2s-preserve-access-tier` operacji `false` kopiowania, `--s2s-preserve-access-tier=false`ustawiając na (Na przykład: ).

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Kopiowanie obiektu blob na inne konto magazynu
> * Kopiowanie katalogu na inne konto magazynu
> * Kopiowanie kontenera na inne konto magazynu
> * Kopiowanie wszystkich kontenerów, katalogów i plików na inne konto magazynu

Te przykłady działają również z kontami, które mają hierarchiczną przestrzeń nazw. [Dostęp do wielu protokołów w magazynie usługi Data Lake](../blobs/data-lake-storage-multi-protocol-access.md) `blob.core.windows.net`umożliwia użycie tej samej składni adresu URL ( ) na tych kontach.

> [!TIP]
> Operację kopiowania można dostosować za pomocą opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Kopiowanie plików jako dołączanie obiektów blob lub stronicowych obiektów blob.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Kopiowanie do określonej warstwy dostępu (takiej jak warstwa archiwum).|**--block-blob-tier**=\[\|Brak\|\|Hot Cool Archiwum\]|
> |Automatyczna dekompresja plików.|**--dekompresja**=\[\|gzip deflate\]|
> 
> Aby uzyskać pełną listę, zobacz [opcje](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>Kopiowanie obiektu blob na inne konto magazynu

Użyj tej samej składni adresu URL (`blob.core.windows.net`) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Przykład** (hierarchiczna przestrzeń nazw) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiowanie katalogu na inne konto magazynu

Użyj tej samej składni adresu URL (`blob.core.windows.net`) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Kopiowanie kontenera na inne konto magazynu

Użyj tej samej składni adresu URL (`blob.core.windows.net`) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Kopiowanie wszystkich kontenerów, katalogów i obiektów blob na inne konto magazynu

Użyj tej samej składni adresu URL (`blob.core.windows.net`) dla kont, które mają hierarchiczną przestrzeń nazw.

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Przykład** (hierarchiczna przestrzeń nazw)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Synchronizowanie plików

Zawartość lokalnego systemu plików można zsynchronizować z kontenerem obiektów blob. Można również synchronizować kontenery i katalogi wirtualne ze sobą. Synchronizacja jest jednokierunkowa. Innymi słowy można wybrać, który z tych dwóch punktów końcowych jest źródłem, a który jest miejscem docelowym. Synchronizacja używa również interfejsów API serwera do serwera. Przykłady przedstawione w tej sekcji również pracy z kontami, które mają hierarchiczną przestrzeń nazw. 

> [!NOTE]
> Bieżąca wersja programu AzCopy nie synchronizuje się między innymi źródłami i miejscami docelowymi (na przykład: magazyn plików lub zasobniki S3 usług Amazon Web Services (AWS).

Polecenie [synchronizacji](storage-ref-azcopy-sync.md) porównuje nazwy plików i ostatnio zmodyfikowane znaczniki czasu. Ustaw `--delete-destination` opcjonalną flagę `true` na `prompt` wartość lub usunięcie plików w katalogu docelowym, jeśli te pliki nie istnieją już w katalogu źródłowym.

Jeśli flaga `--delete-destination` zostanie `true` ustawiona na AzCopy, spowoduje usunięcie plików bezdawania monitu. Jeśli chcesz, aby monit był wyświetlany, zanim AzCopy `prompt`usunie plik, ustaw flagę na `--delete-destination` .

> [!NOTE]
> Aby zapobiec przypadkowym usunięciu, przed użyciem flagi należy `--delete-destination=prompt|true` włączyć operację [usuwania nietrwałego.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)

> [!TIP]
> Operację synchronizacji można dostosować, korzystając z opcjonalnych flag. Oto kilka przykładów.
>
> |Scenariusz|Flaga|
> |---|---|
> |Określ, jak ściśle skróty MD5 powinny być sprawdzane podczas pobierania.|**--check-md5**=\[NoCheck\|LogOnly\|FailIfPodróżent\|FailIfDifferentOrMissing\]|
> |Wykluczanie plików na podstawie wzorca.|**--exclude-path**|
> |Określ, jak szczegółowe mają być wpisy dziennika związane z synchronizacją.|**--log-level**=\[\|OSTRZEŻENIE\|\|BŁĄD INFO BRAK\]|
> 
> Aby uzyskać pełną listę, zobacz [opcje](storage-ref-azcopy-sync.md#options).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aktualizowanie kontenera ze zmianami w lokalnym systemie plików

W takim przypadku kontener jest miejscem docelowym, a źródłem jest lokalny system plików. 

|    |     |
|--------|-----------|
| **Składni** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aktualizowanie lokalnego systemu plików ze zmianami w kontenerze

W takim przypadku lokalny system plików jest miejscem docelowym, a kontener jest źródłem.

|    |     |
|--------|-----------|
| **Składni** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Przykład** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Aktualizowanie kontenera ze zmianami w innym kontenerze

Pierwszy kontener, który pojawia się w tym poleceniu jest źródłem. Drugi to cel podróży.

|    |     |
|--------|-----------|
| **Składni** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualizowanie katalogu ze zmianami w katalogu w innym udziale plików

Pierwszym katalogiem, który pojawia się w tym poleceniu, jest źródło. Drugi to cel podróży.

|    |     |
|--------|-----------|
| **Składni** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Następne kroki

Znajdź więcej przykładów w każdym z tych artykułów:

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)

- [Samouczek: Migracja danych lokalnych do magazynu w chmurze za pomocą narzędzia AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Przesyłanie danych za pomocą AzCopy i przechowywania plików](storage-use-azcopy-files.md)

- [Przesyłanie danych za pomocą wiader AzCopy i Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)
