---
title: LogDownloader — Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Pobierz pliki dziennika, które są tworzone przez usługę Azure Custom Decision Service.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ROBOTS: NOINDEX
ms.openlocfilehash: 33cc0d0dcf16ff82ac128507566427e123020236
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707241"
---
# <a name="logdownloader"></a>LogDownloader

Pobierz pliki dziennika, które są tworzone przez usługę Azure Custom Decision Service i Wygeneruj pliki *. gz* , które są używane przez eksperymentowanie.

## <a name="prerequisites"></a>Wymagania wstępne

- Python 3: Zainstalowane i na ścieżce. Zalecamy używanie wersji 64-bitowej do obsługi dużych plików.
- Repozytorium *Microsoft/MWT-ds* : [Klonowanie repozytorium](https://github.com/Microsoft/mwt-ds).
- Pakiet *Azure-Storage-BLOB* : Aby uzyskać szczegółowe informacje dotyczące instalacji, przejdź do [biblioteki Microsoft Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Wprowadź parametry połączenia usługi Azure Storage w *MWT-ds/datascience/ds. config*: Postępuj zgodnie z szablonem *my_app_id: my_connectionString* . Można określić wiele `app_id`. `app_id` `ds.config` `LogDownloader.py` Jeśli dane wejściowe nie zostaną znalezione w programie, program korzysta z `$Default` parametrów połączenia. `LogDownloader.py`

## <a name="usage"></a>Użycie

Przejdź do `mwt-ds/DataScience` i uruchom `LogDownloader.py` z odpowiednimi argumentami, zgodnie z opisem w poniższym kodzie:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Parametry

| Dane wejściowe | Opis | Domyślny |
| --- | --- | --- |
| `-h`, `--help` | Pokaż komunikat pomocy i Zakończ. | |
| `-a APP_ID`, `--app_id APP_ID` | Identyfikator aplikacji (czyli nazwa kontenera obiektów BLOB usługi Azure Storage). | Wymagane |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Katalog podstawowy do pobierania danych (zostanie utworzony podfolder).  | Wymagane |
| `-s START_DATE`, `--start_date START_DATE` | Data rozpoczęcia pobierania (uwzględniona) w formacie *rrrr-mm-dd* . | `None` |
| `-e END_DATE`, `--end_date END_DATE` | Data zakończenia pobierania (uwzględniona) w formacie *rrrr-mm-dd* . | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | Tryb zastępowania, który ma być używany. | |
| | `0`: Nigdy nie zastępuj; poproszenie użytkownika o to, czy obiekty blob są obecnie używane. | Domyślny |
| | `1`: Poproszenie użytkownika o to, jak można wykonać, gdy pliki mają różne rozmiary lub kiedy obiekty blob są obecnie używane. | |
| | `2`: Zawsze Zastępuj; Pobierz obecnie używane obiekty blob. | |
| | `3`: Nigdy nie zastępuj i Dołącz, jeśli rozmiar jest większy, bez pytania; Pobierz obecnie używane obiekty blob. | |
| | `4`: Nigdy nie zastępuj i Dołącz, jeśli rozmiar jest większy, bez pytania; Pomiń aktualnie używane obiekty blob. | |
| `--dry_run` | Drukowanie, które obiekty blob zostałyby pobrane bez pobierania. | `False` |
| `--create_gzip` | Utwórz plik *gzip* dla Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Przedział czasu (w sekundach) służący do wykrywania, czy plik jest aktualnie w użyciu. | `3600`s (`1` godzina) |
| `--verbose` | Wydrukuj więcej szczegółów. | `False` |
| `-v VERSION`, `--version VERSION` | Wersja narzędzia do pobierania dzienników. | |
| | `1`: W przypadku dzienników niegotowanych (tylko w celu zapewnienia zgodności z poprzednimi wersjami). | Przestarzałe |
| | `2`: Dla dzienników gotowane. | Domyślny |

### <a name="examples"></a>Przykłady

Aby przeprowadzić pobieranie wszystkich danych z kontenera obiektów BLOB usługi Azure Storage, należy użyć następującego kodu:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Aby pobrać tylko dzienniki utworzone od 1 stycznia 2018 z `overwrite_mode=4`, użyj następującego kodu:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Aby utworzyć plik *gzip* łączący wszystkie pobrane pliki, użyj następującego kodu:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
