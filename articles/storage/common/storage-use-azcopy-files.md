---
title: Przesyłanie danych do lub z usługi Azure Files przy użyciu programu AzCopy w wersji 10 | Dokumenty firmy Microsoft
description: Przesyłanie danych za pomocą AzCopy i przechowywania plików.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8aa0e5304825b3f016694a40b3fc1e176518237a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526692"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Przesyłanie danych za pomocą AzCopy i przechowywania plików 

AzCopy to narzędzie wiersza polecenia, za pomocą którego można kopiować obiekty BLOB lub pliki do lub z konta magazynu. Ten artykuł zawiera przykładowe polecenia, które współpracują z usługą Azure Files.

Zanim zaczniesz, zobacz artykuł [Wprowadzenie do AzCopy,](storage-use-azcopy-v10.md) aby pobrać aplikację AzCopy i zapoznać się z narzędziem.

## <a name="create-file-shares"></a>Tworzenie udziałów plików

Za pomocą polecenia [azcopy make](storage-ref-azcopy-make.md) można utworzyć udział plików. W tym przykładzie w tej `myfileshare`sekcji utworzy się udział plików o nazwie .

> [!TIP]
> Przykłady w tej sekcji ujęć argumenty ścieżki z pojedynczymi cudzysłowami (''). Użyj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), należy ująć argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczych cudzysłowów (').

|    |     |
|--------|-----------|
| **Składni** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Przykład** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Szczegółowe dokumenty referencyjne można znaleźć [w pliku azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Przekazywanie plików

Za pomocą polecenia [kopiowania azcopy](storage-ref-azcopy-copy.md) można przesyłać pliki i katalogi z komputera lokalnego.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Przekazywanie pliku
> * Przekazywanie katalogu
> * Przekazywanie zawartości katalogu
> * Przekazywanie określonego pliku

> [!NOTE]
> AzCopy nie automatycznie oblicza i nie przechowuje kodu skrótu md5 pliku. Jeśli chcesz, aby AzCopy to zrobić, a następnie dołączyć flagę `--put-md5` do każdego polecenia copy. W ten sposób po pobraniu pliku AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy `Content-md5` skrót MD5 przechowywany we właściwości pliku jest zgodny z obliczonym skrótem.

Szczegółowe dokumenty referencyjne można znaleźć [w kopii azcopy](storage-ref-azcopy-copy.md).

> [!TIP]
> Przykłady w tej sekcji ujęć argumenty ścieżki z pojedynczymi cudzysłowami (''). Użyj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), należy ująć argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczych cudzysłowów (').

### <a name="upload-a-file"></a>Przekazywanie pliku

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Przykład** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Plik można również przekazać za pomocą symbolu wieloznacznego (*) w dowolnym miejscu ścieżki pliku lub nazwy pliku. Na przykład: `'C:\myDirectory\*.txt'` `C:\my*\*.txt`, lub .

### <a name="upload-a-directory"></a>Przekazywanie katalogu

W tym przykładzie kopiuje katalog (i wszystkie pliki w tym katalogu) do udziału plików. Wynikiem jest katalog w udziale plików o tej samej nazwie.

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Aby skopiować do katalogu w ramach udziału plików, wystarczy określić nazwę tego katalogu w ciągu polecenia.

|    |     |
|--------|-----------|
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Jeśli określisz nazwę katalogu, który nie istnieje w udziale plików, AzCopy utworzy nowy katalog o tej nazwie.

### <a name="upload-the-contents-of-a-directory"></a>Przekazywanie zawartości katalogu

Zawartość katalogu można przekazać bez kopiowania samego katalogu zawierającego za pomocą symbolu wieloznacznego (*).

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Przykład** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Dołącz flagę, `--recursive` aby przekazać pliki we wszystkich podkatarzy.

### <a name="upload-specific-files"></a>Przekazywanie określonych plików

Można określić pełne nazwy plików lub użyć nazw częściowych ze znakami wieloznaczowymi (*).

#### <a name="specify-multiple-complete-file-names"></a>Określanie wielu kompletnych nazw plików

Użyj polecenia [kopiowania azcopy](storage-ref-azcopy-copy.md) z `--include-path` opcją. Oddziel poszczególne nazwy plików za`;`pomocą średnika ( ).

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

W tym przykładzie AzCopy przenosi `C:\myDirectory\photos` `C:\myDirectory\documents\myFile.txt` katalog i plik. Należy dołączyć `--recursive` opcję, aby przenieść `C:\myDirectory\photos` wszystkie pliki w katalogu.

Można również wykluczyć pliki `--exclude-path` za pomocą tej opcji. Aby dowiedzieć się więcej, zobacz dokumenty odwołania do [kopii azcopy.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Używanie symboli wieloznacznych

Użyj polecenia [kopiowania azcopy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją. Określ nazwy częściowe, które zawierają symbole wieloznaczne. Oddziel nazwy za pomocą`;`półkoliny ( ).

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczyć pliki `--exclude-pattern` za pomocą tej opcji. Aby dowiedzieć się więcej, zobacz dokumenty odwołania do [kopii azcopy.](storage-ref-azcopy-copy.md)

Opcje `--include-pattern` `--exclude-pattern` i dotyczą tylko nazwy plików, a nie ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w `–recursive` drzewie katalogów, użyj opcji, `–include-pattern` aby `*.txt` uzyskać całe drzewo katalogów, a następnie użyj i określ, aby uzyskać wszystkie pliki tekstowe.

## <a name="download-files"></a>Pobieranie plików

Za pomocą polecenia [kopiowania azcopy](storage-ref-azcopy-copy.md) można pobierać pliki, katalogi i udziały plików na komputer lokalny.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Pobieranie pliku
> * Pobieranie katalogu
> * Pobieranie zawartości katalogu
> * Pobieranie określonych plików

> [!NOTE]
> Jeśli `Content-md5` wartość właściwości pliku zawiera skrót, AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót `Content-md5` MD5 przechowywany we właściwości pliku jest zgodny z obliczonym skrótem. Jeśli te wartości nie są zgodne, pobieranie nie powiedzie się, `--check-md5=NoCheck` `--check-md5=LogOnly` chyba że zostanie zastąpione to zachowanie przez dołączenie lub polecenie copy.

Szczegółowe dokumenty referencyjne można znaleźć [w kopii azcopy](storage-ref-azcopy-copy.md).

> [!TIP]
> Przykłady w tej sekcji ujęć argumenty ścieżki z pojedynczymi cudzysłowami (''). Użyj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), należy ująć argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczych cudzysłowów (').

### <a name="download-a-file"></a>Pobieranie pliku

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Pobieranie katalogu

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

W tym przykładzie jest `C:\myDirectory\myFileShareDirectory` to katalog o nazwie, który zawiera wszystkie pobrane pliki.

### <a name="download-the-contents-of-a-directory"></a>Pobieranie zawartości katalogu

Zawartość katalogu można pobrać bez kopiowania samego katalogu zawierającego za pomocą symbolu wieloznacznego (*).

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Dołącz flagę, `--recursive` aby pobrać pliki we wszystkich podkatarzy.

### <a name="download-specific-files"></a>Pobieranie określonych plików

Można określić pełne nazwy plików lub użyć nazw częściowych ze znakami wieloznaczowymi (*).

#### <a name="specify-multiple-complete-file-names"></a>Określanie wielu kompletnych nazw plików

Użyj polecenia [kopiowania azcopy](storage-ref-azcopy-copy.md) z `--include-path` opcją. Oddziel poszczególne nazwy plików za`;`pomocą średnika ( ).

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

W tym przykładzie AzCopy przenosi `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` katalog i plik. Należy dołączyć `--recursive` opcję, aby przenieść `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` wszystkie pliki w katalogu.

Można również wykluczyć pliki `--exclude-path` za pomocą tej opcji. Aby dowiedzieć się więcej, zobacz dokumenty odwołania do [kopii azcopy.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Używanie symboli wieloznacznych

Użyj polecenia [kopiowania azcopy](storage-ref-azcopy-copy.md) z `--include-pattern` opcją. Określ nazwy częściowe, które zawierają symbole wieloznaczne. Oddziel nazwy za pomocą`;`półkoliny ( ).

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczyć pliki `--exclude-pattern` za pomocą tej opcji. Aby dowiedzieć się więcej, zobacz dokumenty odwołania do [kopii azcopy.](storage-ref-azcopy-copy.md)

Opcje `--include-pattern` `--exclude-pattern` i dotyczą tylko nazwy plików, a nie ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w `–recursive` drzewie katalogów, użyj opcji, `–include-pattern` aby `*.txt` uzyskać całe drzewo katalogów, a następnie użyj i określ, aby uzyskać wszystkie pliki tekstowe.

## <a name="copy-files-between-storage-accounts"></a>kopiować pliki między kontami magazynu;

Za pomocą programu AzCopy można kopiować pliki na inne konta magazynu. Operacja kopiowania jest synchroniczowa, więc po powrocie polecenia oznacza to, że wszystkie pliki zostały skopiowane.

AzCopy używa [interfejsów API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [serwera do serwera,](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) dzięki czemu dane są kopiowane bezpośrednio między serwerami magazynu. Te operacje kopiowania nie używają przepustowości sieci komputera. Można zwiększyć przepływność tych operacji, ustawiając `AZCOPY_CONCURRENCY_VALUE` wartość zmiennej środowiskowej. Aby dowiedzieć się więcej, zobacz [Optymalizowanie przepływności](storage-use-azcopy-configure.md#optimize-throughput).

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Kopiowanie pliku na inne konto magazynu
> * Kopiowanie katalogu na inne konto magazynu
> * Kopiowanie udziału plików na inne konto magazynu
> * Kopiowanie wszystkich udziałów plików, katalogów i plików na inne konto magazynu

Szczegółowe dokumenty referencyjne można znaleźć [w kopii azcopy](storage-ref-azcopy-copy.md).

> [!TIP]
> Przykłady w tej sekcji ujęć argumenty ścieżki z pojedynczymi cudzysłowami (''). Użyj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), należy ująć argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczych cudzysłowów (').

### <a name="copy-a-file-to-another-storage-account"></a>Kopiowanie pliku na inne konto magazynu

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiowanie katalogu na inne konto magazynu

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Kopiowanie udziału plików na inne konto magazynu

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Kopiowanie wszystkich udziałów plików, katalogów i plików na inne konto magazynu

|    |     |
|--------|-----------|
| **Składni** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Synchronizowanie plików

Zawartość udziału plików można zsynchronizować z innym udziałem plików. Można również zsynchronizować zawartość katalogu w udziale plików z zawartością katalogu znajdującego się w innym udziale plików. Synchronizacja jest jednokierunkowa. Innymi słowy można wybrać, który z tych dwóch punktów końcowych jest źródłem, a który jest miejscem docelowym. Synchronizacja używa również interfejsów API serwera do serwera.

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko dla kont, które nie mają hierarchicznego obszaru nazw. Bieżąca wersja programu AzCopy nie synchronizuje się między usługami Azure Files i Blob Storage.

Polecenie [synchronizacji](storage-ref-azcopy-sync.md) porównuje nazwy plików i ostatnio zmodyfikowane znaczniki czasu. Ustaw `--delete-destination` opcjonalną flagę `true` na `prompt` wartość lub usunięcie plików w katalogu docelowym, jeśli te pliki nie istnieją już w katalogu źródłowym.

Jeśli flaga `--delete-destination` zostanie `true` ustawiona na AzCopy, spowoduje usunięcie plików bezdawania monitu. Jeśli chcesz, aby monit był wyświetlany, zanim AzCopy `prompt`usunie plik, ustaw flagę na `--delete-destination` .

Szczegółowe dokumenty referencyjne można znaleźć [w części Azcopy Sync](storage-ref-azcopy-sync.md).

> [!TIP]
> Przykłady w tej sekcji ujęć argumenty ścieżki z pojedynczymi cudzysłowami (''). Użyj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd.exe). Jeśli używasz powłoki poleceń systemu Windows (cmd.exe), należy ująć argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczych cudzysłowów (').

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Aktualizowanie udziału plików o zmiany w innym udziale plików

Pierwszym udziałem plików, który pojawia się w tym poleceniu, jest źródło. Drugi to cel podróży.

|    |     |
|--------|-----------|
| **Składni** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualizowanie katalogu ze zmianami w katalogu w innym udziale plików

Pierwszym katalogiem, który pojawia się w tym poleceniu, jest źródło. Drugi to cel podróży.

|    |     |
|--------|-----------|
| **Składni** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>Następne kroki

Znajdź więcej przykładów w każdym z tych artykułów:

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)

- [Przesyłanie danych za pomocą pamięci masowej AzCopy i obiektów blob](storage-use-azcopy-blobs.md)

- [Przesyłanie danych za pomocą wiader AzCopy i Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z programem AzCopy](storage-use-azcopy-configure.md)
