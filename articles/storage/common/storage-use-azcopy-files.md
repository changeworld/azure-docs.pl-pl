---
title: Przesyłanie danych do lub z Azure Files przy użyciu AzCopy v10 | Microsoft Docs
description: Transferowanie danych za pomocą AzCopy i magazynu plików.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 225fcd475d488cedb8bd210fe2fa9371849314ac
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615520"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transferowanie danych za pomocą AzCopy i magazynu plików 

AzCopy to narzędzie wiersza polecenia, które służy do kopiowania obiektów blob lub plików do lub z konta magazynu. Ten artykuł zawiera przykładowe polecenia, które współpracują z Azure Files.

Przed rozpoczęciem Zobacz artykuł [wprowadzenie do AzCopy](storage-use-azcopy-v10.md) w celu pobrania AzCopy i zapoznaj się z narzędziem.

## <a name="create-file-shares"></a>Utwórz udziały plików

Możesz użyć [AzCopy](storage-ref-azcopy-make.md) Utwórz polecenie, aby utworzyć udział plików. W przykładzie w tej sekcji jest tworzony udział plików o nazwie `myfileshare`.

> [!TIP]
> W przykładach w tej sekcji zamieszczono argumenty Path z pojedynczymi cudzysłowami (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd. exe). Jeśli używasz powłoki poleceń systemu Windows (cmd. exe), ujmij argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczego cudzysłowu ("").

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Przykład** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy Make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Przekazywanie plików

Aby przekazać pliki i katalogi z komputera lokalnego, można użyć polecenia [copy AzCopy](storage-ref-azcopy-copy.md) .

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Przekazywanie pliku
> * Przekaż katalog
> * Przekaż zawartość katalogu
> * Przekaż określony plik

> [!NOTE]
> AzCopy nie oblicza automatycznie ani nie zapisuje kodu skrótu MD5 pliku. Jeśli chcesz, aby AzCopy to zrobić, Dodaj flagę `--put-md5` do każdego polecenia copy. Dzięki temu, gdy plik zostanie pobrany, AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywany we właściwości `Content-md5` pliku jest zgodny z obliczonym skrótem.

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> W przykładach w tej sekcji zamieszczono argumenty Path z pojedynczymi cudzysłowami (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd. exe). Jeśli używasz powłoki poleceń systemu Windows (cmd. exe), ujmij argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczego cudzysłowu ("").

### <a name="upload-a-file"></a>Przekazywanie pliku

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>'` |
| **Przykład** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Możesz również przekazać plik za pomocą symbolu wieloznacznego (*) w dowolnym miejscu w ścieżce lub nazwie pliku. Na przykład: `'C:\myDirectory\*.txt'`lub `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Przekaż katalog

Ten przykład kopiuje katalog (i wszystkie pliki w tym katalogu) do udziału plików. Wynik jest katalogiem w udziale plików o tej samej nazwie.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Aby skopiować do katalogu w udziale plików, po prostu podaj nazwę tego katalogu w ciągu polecenia.

|    |     |
|--------|-----------|
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

W przypadku określenia nazwy katalogu, który nie istnieje w udziale plików, AzCopy tworzy nowy katalog o tej nazwie.

### <a name="upload-the-contents-of-a-directory"></a>Przekaż zawartość katalogu

Zawartość katalogu można przekazać bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Przykład** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Dołącz flagę `--recursive` do przekazywania plików we wszystkich podkatalogach.

### <a name="upload-specific-files"></a>Przekazywanie określonych plików

Możesz określić pełne nazwy plików lub użyć częściowych nazw z symbolami wieloznacznymi (*).

#### <a name="specify-multiple-complete-file-names"></a>Określ wiele pełnych nazw plików

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z opcją `--include-path`. Oddziel poszczególne nazwy plików przy użyciu średnika (`;`).

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

W tym przykładzie AzCopy transferuje katalog `C:\myDirectory\photos` i plik `C:\myDirectory\documents\myFile.txt`. Należy uwzględnić opcję `--recursive`, aby przenieść wszystkie pliki w katalogu `C:\myDirectory\photos`.

Można również wykluczać pliki przy użyciu opcji `--exclude-path`. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Użyj symboli wieloznacznych

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z opcją `--include-pattern`. Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy przy użyciu semicolin (`;`).

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczać pliki przy użyciu opcji `--exclude-pattern`. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

Opcje `--include-pattern` i `--exclude-pattern` mają zastosowanie tylko do nazw plików, a nie do ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj opcji `–recursive`, aby uzyskać całe drzewo katalogów, a następnie użyj `–include-pattern` i określ `*.txt`, aby pobrać wszystkie pliki tekstowe.

## <a name="download-files"></a>Pobieranie plików

Można użyć polecenia [copy AzCopy](storage-ref-azcopy-copy.md) do pobrania plików, katalogów i udziałów plików na komputer lokalny.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Pobieranie pliku
> * Pobierz katalog
> * Pobierz zawartość katalogu
> * Pobieranie określonych plików

> [!NOTE]
> Jeśli wartość właściwości `Content-md5` pliku zawiera skrót, AzCopy oblicza skrót MD5 dla pobranych danych i weryfikuje, czy skrót MD5 przechowywany we właściwości `Content-md5` pliku jest zgodny z obliczonym skrótem. Jeśli te wartości nie są zgodne, pobieranie nie powiedzie się, o ile nie zastąpisz tego zachowania, dołączając `--check-md5=NoCheck` lub `--check-md5=LogOnly` do polecenia copy.

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> W przykładach w tej sekcji zamieszczono argumenty Path z pojedynczymi cudzysłowami (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd. exe). Jeśli używasz powłoki poleceń systemu Windows (cmd. exe), ujmij argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczego cudzysłowu ("").

### <a name="download-a-file"></a>Pobieranie pliku

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>?<SAS-token>' '<local-file-path>'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Pobierz katalog

|    |     |
|--------|-----------|
| **Obowiązuje** | "AzCopy Copy" https://< Storage-account-name >. File. Core. Windows. NET/< plik-Share-Name >/< ścieżka katalogu >? < sygnatura dostępu współdzielonego > " 
"< ścieżka katalogu lokalnego >"--rekursywnie " |
| **Przykład** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

Ten przykład powoduje, że katalog o nazwie `C:\myDirectory\myFileShareDirectory` zawiera wszystkie pobrane pliki.

### <a name="download-the-contents-of-a-directory"></a>Pobierz zawartość katalogu

Zawartość katalogu można pobrać bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>' '<local-directory-path>/'` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Dołącz flagę `--recursive`, aby pobrać pliki we wszystkich podkatalogach.

### <a name="download-specific-files"></a>Pobieranie określonych plików

Możesz określić pełne nazwy plików lub użyć częściowych nazw z symbolami wieloznacznymi (*).

#### <a name="specify-multiple-complete-file-names"></a>Określ wiele pełnych nazw plików

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z opcją `--include-path`. Oddziel poszczególne nazwy plików przy użyciu semicolin (`;`).

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

W tym przykładzie AzCopy transferuje katalog `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` i plik `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt`. Należy uwzględnić opcję `--recursive`, aby przenieść wszystkie pliki w katalogu `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos`.

Można również wykluczać pliki przy użyciu opcji `--exclude-path`. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Użyj symboli wieloznacznych

Użyj polecenia [copy AzCopy](storage-ref-azcopy-copy.md) z opcją `--include-pattern`. Określ częściowe nazwy, które zawierają symbole wieloznaczne. Oddziel nazwy przy użyciu semicolin (`;`).

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>?<SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Przykład** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Można również wykluczać pliki przy użyciu opcji `--exclude-pattern`. Aby dowiedzieć się więcej, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md) Reference docs.

Opcje `--include-pattern` i `--exclude-pattern` mają zastosowanie tylko do nazw plików, a nie do ścieżki.  Jeśli chcesz skopiować wszystkie pliki tekstowe, które istnieją w drzewie katalogów, użyj opcji `–recursive`, aby uzyskać całe drzewo katalogów, a następnie użyj `–include-pattern` i określ `*.txt`, aby pobrać wszystkie pliki tekstowe.

## <a name="copy-files-between-storage-accounts"></a>Kopiuj pliki między kontami magazynu

Można użyć AzCopy do kopiowania plików na inne konta magazynu. Operacja kopiowania jest synchroniczna, dlatego gdy polecenie zwróci wartość, która wskazuje, że wszystkie pliki zostały skopiowane.

AzCopy używa [interfejsów API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [serwer-serwer](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , aby dane były kopiowane bezpośrednio między serwerami magazynu. Te operacje kopiowania nie korzystają z przepustowości sieci komputera. Przepływność tych operacji można zwiększyć, ustawiając wartość zmiennej środowiskowej `AZCOPY_CONCURRENCY_VALUE`. Aby dowiedzieć się więcej, zobacz [Optymalizowanie przepływności](storage-use-azcopy-configure.md#optimize-throughput).

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Kopiowanie pliku na inne konto magazynu
> * Kopiowanie katalogu do innego konta magazynu
> * Kopiuj udział plików na inne konto magazynu
> * Kopiowanie wszystkich udziałów plików, katalogów i plików na inne konto magazynu

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy Copy](storage-ref-azcopy-copy.md).

> [!TIP]
> W przykładach w tej sekcji zamieszczono argumenty Path z pojedynczymi cudzysłowami (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd. exe). Jeśli używasz powłoki poleceń systemu Windows (cmd. exe), ujmij argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczego cudzysłowu ("").

### <a name="copy-a-file-to-another-storage-account"></a>Kopiowanie pliku na inne konto magazynu

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>'` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Kopiowanie katalogu do innego konta magazynu

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Kopiuj udział plików na inne konto magazynu

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Kopiowanie wszystkich udziałów plików, katalogów i plików na inne konto magazynu

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/?<SAS-token>' --recursive'` |
| **Przykład** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Synchronizuj pliki

Zawartość udziału plików można zsynchronizować z innym udziałem plików. Możesz również zsynchronizować zawartość katalogu w udziale plików z zawartością katalogu znajdującego się w innym udziale plików. Synchronizacja jest jednokierunkowa. Innymi słowy, możesz wybrać, które z tych dwóch punktów końcowych są źródłem, a które są lokalizacją docelową. Synchronizacja używa także serwera do interfejsów API serwera.

> [!NOTE]
> Obecnie ten scenariusz jest obsługiwany tylko w przypadku kont, które nie mają hierarchicznej przestrzeni nazw. Bieżąca wersja programu AzCopy nie jest synchronizowana między Azure Files i Blob Storage.

Polecenie [Sync](storage-ref-azcopy-sync.md) porównuje nazwy plików i ostatnio modyfikowane sygnatury czasowe. Ustaw opcjonalną flagę `--delete-destination` na wartość `true` lub `prompt`, aby usunąć pliki w katalogu docelowym, jeśli te pliki nie istnieją już w katalogu źródłowym.

Jeśli ustawisz flagę `--delete-destination` na `true` AzCopy usuwa pliki bez podawania monitu. Jeśli chcesz, aby monit pojawił się przed AzCopy usunięciem pliku, Ustaw flagę `--delete-destination` na `prompt`.

Aby uzyskać szczegółowe dokumenty referencyjne, zobacz [AzCopy Sync](storage-ref-azcopy-sync.md).

> [!TIP]
> W przykładach w tej sekcji zamieszczono argumenty Path z pojedynczymi cudzysłowami (' '). Używaj pojedynczych cudzysłowów we wszystkich powłokach poleceń z wyjątkiem powłoki poleceń systemu Windows (cmd. exe). Jeśli używasz powłoki poleceń systemu Windows (cmd. exe), ujmij argumenty ścieżki z podwójnymi cudzysłowami ("") zamiast pojedynczego cudzysłowu ("").

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Aktualizowanie udziału plików ze zmianami w innym udziale plików

Pierwszym udziałem plików, który pojawia się w tym poleceniu jest źródło. Drugim z nich jest miejsce docelowe.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aktualizowanie katalogu ze zmianami w katalogu w innym udziale plików

Pierwszym katalogiem, który pojawia się w tym poleceniu jest źródło. Drugim z nich jest miejsce docelowe.

|    |     |
|--------|-----------|
| **Obowiązuje** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' --recursive` |
| **Przykład** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>Następne kroki

Więcej przykładów znajdziesz w jednym z następujących artykułów:

- [Wprowadzenie do AzCopy](storage-use-azcopy-v10.md)

- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)

- [Transferowanie danych za pomocą zasobników AzCopy i Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)
