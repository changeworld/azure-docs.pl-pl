---
title: Przesyłanie danych do lub z Azure Files przy użyciu AzCopy v10 | Microsoft Docs
description: Transferowanie danych za pomocą AzCopy i magazynu plików.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 0c350776323c1b2949285a7ebe6a7c2778ae4dc4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648756"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transferowanie danych za pomocą AzCopy i magazynu plików 

AzCopy to narzędzie wiersza polecenia, które służy do kopiowania obiektów blob lub plików do lub z konta magazynu. Ten artykuł zawiera przykładowe polecenia, które współpracują z Azure Files.

Przed rozpoczęciem Zobacz artykuł [wprowadzenie do AzCopy](storage-use-azcopy-v10.md) w celu pobrania AzCopy i zapoznaj się z narzędziem.

## <a name="create-file-shares"></a>Utwórz udziały plików

Można użyć polecenia AzCopy `make` , aby utworzyć udział plików. W przykładzie w tej sekcji jest tworzony udział plików o `myfileshare`nazwie.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Przykład** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Przekazywanie plików

Aby przekazać pliki i katalogi `copy` z komputera lokalnego, można użyć polecenia AzCopy.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Przekaż plik
> * Przekaż katalog
> * Przekazywanie plików przy użyciu symboli wieloznacznych

> [!NOTE]
> AzCopy nie oblicza automatycznie ani nie zapisuje kodu skrótu MD5 pliku. Jeśli chcesz, aby AzCopy to zrobić, Dodaj `--put-md5` flagę do poszczególnych poleceń kopiowania. Dzięki temu, gdy plik zostanie pobrany, AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywany we `Content-md5` właściwości pliku jest zgodny z obliczonym skrótem.

### <a name="upload-a-file"></a>Przekaż plik

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Przykład** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Przekaż katalog

Ten przykład kopiuje katalog (i wszystkie pliki w tym katalogu) do udziału plików. Wynik jest katalogiem w udziale plików o tej samej nazwie.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Przykład** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Aby skopiować do katalogu w udziale plików, po prostu podaj nazwę tego katalogu w ciągu polecenia.

|    |     |
|--------|-----------|
| **Przykład** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

W przypadku określenia nazwy katalogu, który nie istnieje w udziale plików, AzCopy tworzy nowy katalog o tej nazwie.

### <a name="upload-the-contents-of-a-directory"></a>Przekaż zawartość katalogu

Zawartość katalogu można przekazać bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Przykład** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> `--recursive` Dołącz flagę do przekazywania plików we wszystkich podkatalogach.

## <a name="download-files"></a>Pobieranie plików

Można użyć polecenia AzCopy `copy` , aby pobrać pliki, katalogi i udziały plików na komputer lokalny.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Pobierz plik
> * Pobierz katalog
> * Pobieranie plików przy użyciu symboli wieloznacznych

> [!NOTE]
> Jeśli wartość `Content-md5` właściwości pliku zawiera skrót, AzCopy oblicza skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywany we właściwości pliku jest zgodny z obliczonym skrótem. `Content-md5` Jeśli te wartości nie są zgodne, pobieranie nie powiedzie się, o ile nie zastąpisz tego zachowania przez dołączenie `--check-md5=NoCheck` lub `--check-md5=LogOnly` do polecenia copy.

### <a name="download-a-file"></a>Pobierz plik

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Przykład** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Pobierz katalog

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Przykład** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

Ten przykład powoduje, że katalog o `C:\myDirectory\myFileShareDirectory` nazwie zawiera wszystkie pobrane pliki.

### <a name="download-the-contents-of-a-directory"></a>Pobierz zawartość katalogu

Zawartość katalogu można pobrać bez kopiowania samego katalogu zawierającego symbol wieloznaczny (*).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Przykład** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> `--recursive` Dołącz flagę do pobierania plików we wszystkich podkatalogach.

## <a name="next-steps"></a>Następne kroki

Więcej przykładów znajdziesz w jednym z następujących artykułów:

- [Wprowadzenie do AzCopy](storage-use-azcopy-v10.md)

- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)

- [Transferowanie danych za pomocą zasobników AzCopy i Amazon S3](storage-use-azcopy-s3.md)

- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)