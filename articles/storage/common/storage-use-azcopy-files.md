---
title: Transfer danych do lub z usługi Azure Files za pomocą narzędzia AzCopy v10 | Dokumentacja firmy Microsoft
description: Transferowanie danych za pomocą narzędzia AzCopy i pliku magazynu.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 5d11d5c0da350b9abf2e2b06a11a1690cf3f6922
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247112"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transferowanie danych za pomocą narzędzia AzCopy oraz plików magazynu 

AzCopy to narzędzie wiersza polecenia używanej do kopiowania obiektów blob lub plików, do lub z konta magazynu. Ten artykuł zawiera przykładowe polecenia, które działają z usługą Azure Files.

Przed rozpoczęciem wykonywania tej procedury, zobacz [Rozpoczynanie pracy za pomocą narzędzia AzCopy](storage-use-azcopy-v10.md) artykuł, aby pobrać narzędzia AzCopy i zapoznać się z narzędziem.

## <a name="create-file-shares"></a>Tworzenie udziałów plików

Możesz użyć narzędzia AzCopy `make` polecenie, aby utworzyć udział plików. W przykładzie w tej sekcji tworzy udział plików o nazwie `myfileshare`.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Przykład** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Przekazywanie plików

Możesz użyć narzędzia AzCopy `copy` polecenie, aby przekazać pliki i foldery z komputera lokalnego.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Przekazywanie pliku
> * Przekazywanie folderu
> * Przekazywanie plików przy użyciu symboli wieloznacznych

> [!NOTE]
> Narzędzie AzCopy nie automatycznie obliczyć i Zapisz wartość skrótu md5 pliku. Jeśli narzędzie AzCopy, aby to zrobić, należy następnie dołącz `--put-md5` Flaga każdego polecenia Kopiuj. W ten sposób podczas pobierania pliku narzędzia AzCopy oblicza Skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywane w pliku `Content-md5` właściwość odpowiada skrót obliczony.

### <a name="upload-a-file"></a>Przekazywanie pliku

|    |     |
|--------|-----------|
| **Składnia** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Przykład** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-folder"></a>Przekazywanie folderu

W tym przykładzie kopiuje folder (i wszystkich plików w tym folderze) do udziału plików. Wynik jest folderu w udziale plików o tej samej nazwie.

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Przykład** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Aby skopiować do folderu w udziale plików, określ nazwę tego folderu, w ciągu polecenia.

|    |     |
|--------|-----------|
| **Przykład** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Jeśli określisz nazwę folderu, który nie istnieje w udziale plików, narzędzia AzCopy tworzy nowy folder o takiej nazwie.

### <a name="upload-the-contents-of-a-folder"></a>Załaduj zawartość folderu

Możesz przekazać zawartość folderu bez kopiowania folderu zawierającego za pomocą symbolu wieloznacznego (*).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "<local-folder-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>` |
| **Przykład** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Dołącz `--recursive` flagi, aby przekazać pliki w podfolderach, wszystkie.

## <a name="download-files"></a>Pobieranie plików

Możesz użyć narzędzia AzCopy `copy` polecenie, aby pobrać plików, folderów i plików udziałów na komputerze lokalnym.

Ta sekcja zawiera następujące przykłady:

> [!div class="checklist"]
> * Pobieranie pliku
> * Folder pobierania
> * Pobieranie plików przy użyciu symboli wieloznacznych

> [!NOTE]
> Jeśli `Content-md5` wartość właściwości pliku zawiera skrót, narzędzia AzCopy oblicza Skrót MD5 dla pobranych danych i sprawdza, czy skrót MD5 przechowywane w pliku `Content-md5` właściwość odpowiada skrót obliczony. Jeśli te wartości nie są zgodne, pliki do pobrania zakończy się niepowodzeniem, chyba że zastąpienia tego zachowania, dodając `--check-md5=NoCheck` lub `--check-md5=LogOnly` do polecenia Kopiuj.

### <a name="download-a-file"></a>Pobieranie pliku

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Przykład** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Folder pobierania

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>" "<local-folder-path>" --recursive` |
| **Przykład** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"  --recursive` |

Ten przykład powoduje zwrócenie folder o nazwie `C:\myFolder\myFileShareFolder` zawierający wszystkie pobrane pliki.

### <a name="download-the-contents-of-a-folder"></a>Pobierz zawartość folderu

Możesz pobrać zawartość folderu bez kopiowania folderu zawierającego za pomocą symbolu wieloznacznego (*).

|    |     |
|--------|-----------|
| **Składnia** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-folder-path>/"` |
| **Przykład** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"` |

> [!NOTE]
> Dołącz `--recursive` flagi do pobierania plików we wszystkich podrzędnych folderów.

## <a name="next-steps"></a>Kolejne kroki

Znajdź więcej przykładów w dowolnym z następujących artykułów:

- [Rozpoczynanie pracy za pomocą narzędzia AzCopy](storage-use-azcopy-v10.md)

- [Transferowanie danych za pomocą narzędzia AzCopy i blob storage](storage-use-azcopy-blobs.md)

- [Transferowanie danych za pomocą narzędzia AzCopy i Amazon S3 przedziałów](storage-use-azcopy-s3.md)

- [Konfigurowanie optymalizacji i rozwiązywanie problemów z narzędzia AzCopy](storage-use-azcopy-configure.md)