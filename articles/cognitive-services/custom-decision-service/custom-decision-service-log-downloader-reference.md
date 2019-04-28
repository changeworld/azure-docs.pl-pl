---
title: LogDownloader — Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Pobierz pliki dziennika, które są produkowane przez Azure Custom Decision Service.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 8a8f669c33f40fb80dc826ec04203880dee74d82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829994"
---
# <a name="logdownloader"></a>LogDownloader

Pobierz pliki dziennika, które są produkowane przez Azure Custom Decision Service oraz generować *.gz* pliki, które są używane przez eksperymentowanie w usłudze.

## <a name="prerequisites"></a>Wymagania wstępne

- Python 3: Zainstalowana i w zmiennej path. Firma Microsoft zaleca 64-bitowej wersji do obsługi dużych plików.
- *O Microsoft-ds* repozytorium: [Sklonuj repozytorium](https://github.com/Microsoft/mwt-ds).
- *Obiektu blob magazynu azure* pakietu: Aby uzyskać szczegółowe informacje dotyczące instalacji, przejdź do [biblioteki usługi Microsoft Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Wprowadź parametry połączenia usługi Azure storage w *mwt-ds/DataScience/ds.config*: Postępuj zgodnie z *my_app_id: my_connectionString* szablonu. Można określić wiele `app_id`. Po uruchomieniu `LogDownloader.py`, jeśli dane wejściowe `app_id` nie zostanie znaleziony w `ds.config`, `LogDownloader.py` używa `$Default` parametry połączenia.

## <a name="usage"></a>Sposób użycia

Przejdź do `mwt-ds/DataScience` i uruchom `LogDownloader.py` z odpowiednimi argumentami, zgodnie z opisem w poniższym kodzie:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Parametry

| Dane wejściowe | Opis | Domyślne |
| --- | --- | --- |
| `-h`, `--help` | Pokaż komunikat pomocy i wyjścia. | |
| `-a APP_ID`, `--app_id APP_ID` | Identyfikator aplikacji (czyli usługi Azure Storage blob nazwę kontenera). | Wymagane |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Katalog podstawowy dla pobieranie danych (są tworzone).  | Wymagane |
| `-s START_DATE`, `--start_date START_DATE` | Pobieranie rozpoczęcia (włączone), w *RRRR-MM-DD* formatu. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | Pobieranie Data zakończenia (włączone), w *RRRR-MM-DD* formatu. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | Tryb zastępowania do użycia. | |
| | `0`: Nigdy nie zastąpi; Poproś użytkownika, czy obiekty BLOB są obecnie używane. | Domyślne |
| | `1`: Poproś użytkownika w sposób kontynuować, jeśli pliki mają różne rozmiary, lub gdy obiekty BLOB są obecnie używane. | |
| | `2`: Zawsze mają pierwszeństwo przed; Pobierz obiekty BLOB obecnie używane. | |
| | `3`: Nigdy nie należy zastępować i dołączenia, jeśli rozmiar jest większy, bez monitowania; Pobierz obiekty BLOB obecnie używane. | |
| | `4`: Nigdy nie należy zastępować i dołączenia, jeśli rozmiar jest większy, bez monitowania; Pomiń aktualnie używanego obiektów blob. | |
| `--dry_run` | Drukowanie, które obiekty BLOB czy zostały pobrane, bez pobierania. | `False` |
| `--create_gzip` | Tworzenie *gzip* pliku Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Przedział czasu w ciągu kilku sekund do wykrywania, czy plik jest obecnie w użyciu. | `3600` s (`1` godziny) |
| `--verbose` | Drukowanie więcej szczegółów. | `False` |
| `-v VERSION`, `--version VERSION` | Wersja narzędzia do pobierania dzienników do użycia. | |
| | `1`: W przypadku niegotowane dzienników (tylko dla zgodności z poprzednimi wersjami). | Przestarzałe |
| | `2`: W przypadku dzienników gotowe. | Domyślne |

### <a name="examples"></a>Przykłady

Do uruchomienia próbnego pobierania wszystkie dane w kontenerze obiektów blob usługi Azure Storage Użyj następującego kodu:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Aby pobrać tylko dzienniki utworzone od 1 stycznia 2018 r. za pomocą `overwrite_mode=4`, użyj następującego kodu:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Aby utworzyć *gzip* pliku scalanie pobranych plików, użyj następującego kodu:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
