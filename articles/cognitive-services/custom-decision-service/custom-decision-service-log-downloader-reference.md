---
title: LogDownloader - kognitywnych usług platformy Azure | Dokumentacja firmy Microsoft
description: Pobierz pliki dziennika, które są tworzone przez usługę decyzji niestandardowe Azure.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 783b534b3b3f4bb7f5d9f073f491690759edfea5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348985"
---
# <a name="logdownloader"></a>LogDownloader

Pobierz pliki dziennika, które są tworzone przez usługę decyzji niestandardowe Azure i generować *.gz* pliki, które są używane przez eksperymenty.

## <a name="prerequisites"></a>Wymagania wstępne

- Python 3: Zainstalowane i ścieżki. Firma Microsoft zaleca 64-bitowej wersji do obsługi dużych plików.
- *O Microsoft-ds* repozytorium: [sklonować repozytorium](https://github.com/Microsoft/mwt-ds).
- *Obiektu blob magazynu azure* pakietu: Aby uzyskać szczegółowe informacje dotyczące instalacji, przejdź do [biblioteki Microsoft usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Wprowadź parametry połączenia magazynu Azure w *mwt-ds/DataScience/ds.config*: wykonaj *my_app_id: my_connectionString* szablonu. Można określić wiele `app_id`. Po uruchomieniu `LogDownloader.py`, jeśli dane wejściowe `app_id` nie został znaleziony w `ds.config`, `LogDownloader.py` używa `$Default` parametry połączenia.

## <a name="usage"></a>Sposób użycia

Przejdź do `mwt-ds/DataScience` i uruchom `LogDownloader.py` z odpowiednich argumentów, zgodnie z opisem w poniższym kodzie:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Parametry

| Dane wejściowe | Opis | Domyślne |
| --- | --- | --- |
| `-h`, `--help` | Pokaż komunikat pomocy i Zakończ. | |
| `-a APP_ID`, `--app_id APP_ID` | Identyfikator aplikacji (oznacza to, że magazyn Azure blob nazwa kontenera). | Wymagane |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Katalog podstawowy pobierania danych (podfolder zostanie utworzony).  | Wymagane |
| `-s START_DATE`, `--start_date START_DATE` | Pobieranie Data (dołączony), w *RRRR-MM-DD* format. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | Pobieranie Data zakończenia (dołączony), w *RRRR-MM-DD* format. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | Tryb zastępowania do użycia. | |
| | `0`: Nigdy nie należy zastępować; Poproś użytkownika, czy obiekty BLOB są obecnie używane. | Domyślne | |
| | `1`: Monitowanie użytkownika o postępowania, gdy pliki mają różne rozmiary lub obiekty BLOB są obecnie używane. | |
| | `2`: Zastąp zawsze; obiekty BLOB pobierania obecnie używane. | |
| | `3`: Nigdy nie należy zastępować i dołączyć, jeśli rozmiar jest większy, bez pytania; obiekty BLOB pobierania obecnie używane. | |
| | `4`: Nigdy nie należy zastępować i dołączyć, jeśli rozmiar jest większy, bez pytania; obiekty BLOB Pomiń obecnie używane. | |
| `--dry_run` | Drukowanie, które obiekty BLOB może zostać pobrany, bez pobierania. | `False` |
| `--create_gzip` | Utwórz *gzip* pliku Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Przedział czasu w sekundach, wykrywania, czy plik jest aktualnie używany. | `3600` s (`1` godziny) |
| `--verbose` | Drukowanie więcej szczegółów. | `False` |
| `-v VERSION`, `--version VERSION` | Wersja narzędzia do pobierania dziennika do użycia. | |
| | `1`: W przypadku niegotowane dzienniki (tylko w przypadku zgodności z poprzednimi wersjami). | Przestarzałe |
| | `2`: W przypadku dzienników gotowe. | Domyślne |

### <a name="examples"></a>Przykłady

Dla uruchomienia suchej pobierania wszystkich danych w kontenerze obiektu blob magazynu Azure Użyj następującego kodu:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Aby pobrać tylko dzienniki utworzone po 1 stycznia 2018 z `overwrite_mode=4`, użyj następującego kodu:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Aby utworzyć *gzip* pliku scalanie pobranych plików, użyj następującego kodu:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```