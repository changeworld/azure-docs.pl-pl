---
title: "Obsługiwane źródła danych dostępne z Przygotowanie danych usługi Azure Machine Learning | Dokumentacja firmy Microsoft"
description: "Ten dokument zawiera pełną listę obsługiwanych źródeł danych dostępnych w celu przygotowania danych usługi Azure Machine Learning."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 7b42080ea4bf9a9e49f2695ab8746d9ead7348bd
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Obsługiwanych źródeł danych w celu przygotowania danych usługi Azure Machine Learning 
W tym artykule opisano aktualnie obsługiwanych źródeł danych w celu przygotowania danych usługi Azure Machine Learning.

Dostępne są następujące obsługiwanych źródeł danych w tej wersji.

## <a name="types"></a>Typy 

### <a name="sql-server"></a>Oprogramowanie SQL Server
Odczytany z lokalnego serwera SQL lub bazy danych Azure SQL.

#### <a name="options"></a>Opcje
- Adres serwera
- Serwer (nawet jeśli certyfikatu na serwerze jest nieprawidłowy. Należy używać ostrożnie)
- Typ uwierzytelniania (z systemem Windows, serwer)
- Nazwa użytkownika
- Hasło
- Bazy danych, aby nawiązać połączenie
- Zapytanie SQL

#### <a name="notes"></a>Uwagi
- Kolumny wariantu języka SQL nie są obsługiwane.
- Kolumna czasu jest konwertowana na typ datetime przez dodanie czasu z bazy danych do daty 1970/1/1
- Podczas wykonywania w klastrze Spark, wszystkie dane związane z kolumn (date, datetime, datetime2, datetimeoffset) będą oceniać niepoprawne wartości dat przed 1583
- Wartości w kolumnach dziesiętnych mogą tracić dokładność z powodu konwersji na dziesiętną

### <a name="directory-vs-file"></a>Katalog lub plik
Wybierz pojedynczy plik, a następnie go odczytać w przygotowaniu danych. Typ pliku jest analizowany w celu określenia domyślnych parametrów połączenia pliku pokazano na następnym ekranie.

Wybierz katalog lub zestawu plików w katalogu (selektora plików jest multiselect). Albo podejścia przy rozwiązywaniu pliki są odczytywane w jako przepływ danych jednego i dołączony do siebie, nagłówki wycięte w razie potrzeby.

Są obsługiwane typy plików:
- Delimited (.csv, .tsv, .txt, etc.)
- O stałej szerokości
- Zwykły tekst
- Plik JSON

### <a name="csv-file"></a>Plik CSV
Odczytaj plik przecinkami wartości rozdzielanych przecinkami magazynu.

#### <a name="options"></a>Opcje
- Separator
- Komentarz
- Nagłówki
- Decimal — symbol
- Kodowanie pliku
- Wierszy do pominięcia

### <a name="tsv-file"></a>Plik TSV
Odczytaj plik kartę wartości rozdzielanych przecinkami magazynu.

#### <a name="options"></a>Opcje
- Komentarz
- Nagłówki
- Kodowanie pliku
- Wierszy do pominięcia

### <a name="excel-xlsxlsx"></a>Program Excel (.xls/.xlsx)
Odczytać Excel pliku jeden arkusz naraz, określając nazwę arkusza lub numer.

#### <a name="options"></a>Opcje
- Nazwa arkusza lub numer
- Nagłówki
- Wierszy do pominięcia

### <a name="json-file"></a>Plik JSON
Plik JSON do odczytu z magazynu. Plik jest "spłaszczane" na odczyt.

#### <a name="options"></a>Opcje
- None

### <a name="parquet"></a>Parquet
Przeczytaj Parquet zestawu danych, albo jednym pliku lub folderu.

Parquet zgodnie z formatem mogą mieć różne formy w magazynie. Dla mniejszych zestawów danych jest czasami używana .parquet pojedynczego pliku. Różne biblioteki Python obsługują odczytywanie lub zapisywanie w plikach .parquet pojedynczego. W tej chwili Przygotowanie danych usługi Azure Machine Learning zależy od biblioteki języka PyArrow Python do odczytywania Parquet podczas lokalnego interakcyjnej. Obsługuje pliki pojedynczego .parquet (tak długo, jak zostały napisane działa w taki sposób, a nie jako część większy zestaw danych), a także Parquet zestawów danych.

Zestaw danych Parquet jest kolekcją więcej niż jeden plik .parquet, z których każdy reprezentuje mniejszy partycji większy zestaw danych. Zestawy danych zwykle są zawarte w folderze i są domyślnym formatem danych wyjściowych parquet platformach, na przykład Spark i Hive.

>[!NOTE]
>Podczas czytania Parquet danych, który znajduje się w folderze z wieloma plikami .parquet, jest możliwie najbezpieczniejszy wybierz katalog do odczytu i **zestawu danych Parquet** opcji. Dzięki temu PyArrow odczytu całego folderu zamiast poszczególnych plików. Dzięki temu odczytywanie bardziej skomplikowane sposobów Parquet są przechowywane na dysku, takie jak folder partycjonowania.

Wykonanie skalowania w poziomie zależy od możliwości odczytu Parquet platforma Spark i obsługuje pojedynczych plików oraz folderów, podobny do użytku lokalnego interaktywnego.

#### <a name="options"></a>Opcje
- Parquet zestawu danych. Ta opcja określa, czy Przygotowanie danych usługi Azure Machine Learning rozwijane w podanym katalogu i podejmuje próbę odczytania każdego pliku osobno (tryb niezaznaczone), lub czy traktuje katalogu jako cały zestaw danych (tryb wybrany). Wybrany tryb PyArrow wybiera najlepszy sposób, aby zinterpretować plików.


## <a name="locations"></a>Lokalizacje
### <a name="local"></a>Lokalna
Lokalny dysk twardy lub lokalizacji magazynu mapowanej sieci.

### <a name="sql-server"></a>Oprogramowanie SQL Server
Lokalnego programu SQL Server, lub bazy danych Azure SQL.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Magazyn obiektów Blob Azure, która wymaga subskrypcji platformy Azure.

