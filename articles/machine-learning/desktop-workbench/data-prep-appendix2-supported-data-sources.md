---
title: Obsługiwane źródła danych dostępne za pomocą przygotowywania danych usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Ten dokument zawiera pełną listę obsługiwanych źródeł danych dotyczących przygotowywania danych usługi Azure Machine Learning.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: c4686bf01031e30bd9dc94f42d80da524db82931
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969261"
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Obsługiwane źródła danych w celu przygotowania danych usługi Azure Machine Learning 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

W tym artykule przedstawiono aktualnie obsługiwanych źródeł danych w celu przygotowania danych usługi Azure Machine Learning.

Dostępne są następujące źródła danych obsługiwane w tej wersji.

## <a name="types"></a>Typy 

### <a name="sql-server"></a>Oprogramowanie SQL Server
Odczyt z lokalnego programu SQL server lub bazie danych Azure SQL.

#### <a name="options"></a>Opcje
- Adres serwera
- Serwer (nawet jeśli certyfikatu na serwerze jest nieprawidłowy. Należy używać ostrożnie)
- Typ uwierzytelniania (Windows, serwer)
- Nazwa użytkownika
- Hasło
- Bazy danych, aby nawiązać połączenie
- Zapytanie SQL

#### <a name="notes"></a>Uwagi
- Kolumny z wariantu języka SQL nie są obsługiwane.
- Kolumna czasu jest konwertowany na daty i godziny, dodając czasu z bazy danych Data 1970/1/1
- Gdy wykonywane w klastrze Spark, wszystkie dane związane z kolumn (date, datetime, datetime2, datetimeoffset) będą oceniać niepoprawne wartości dat przed 1583
- Wartości w kolumnach dziesiętnych mogą tracić dokładność z powodu konwersji do dziesiętnego

### <a name="directory-vs-file"></a>Katalog, a plik
Wybierz pojedynczy plik, a wczytywany przygotowywania danych. Typ pliku jest analizowana, aby określić domyślne parametry połączenia pliku, pokazane na następnym ekranie.

Wybierz katalog lub zestawu plików w katalogu (selektor plików został wyboru wielokrotnego). Za pomocą każda z tych metod pliki są wczytywane jako przepływ danych jednego i są dołączane do siebie nawzajem z nagłówkami, wycięte, jeśli to konieczne.

Obsługiwane typy plików są:
- Przecinkami (CSV, tsv, txt, itp.)
- Stała szerokość
- Zwykły tekst
- Plik JSON

### <a name="csv-file"></a>Plik CSV
Odczytaj plik przecinkami wartości rozdzielanych przecinkami z magazynu.

#### <a name="options"></a>Opcje
- Separator
- Komentarz
- Nagłówki
- Symbol dziesiętny
- Kodowanie pliku
- Wierszy do pominięcia

### <a name="tsv-file"></a>Plik TSV
Odczytaj plik kartę z wartościami oddzielonymi z magazynu.

#### <a name="options"></a>Opcje
- Komentarz
- Nagłówki
- Kodowanie pliku
- Wierszy do pominięcia

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Przeczytaj arkusz jeden plik programu Excel w czasie, określając nazwę arkusza lub liczba.

#### <a name="options"></a>Opcje
- Nazwa arkusza lub numer
- Nagłówki
- Wierszy do pominięcia

### <a name="json-file"></a>Plik JSON
Odczytaj plik JSON z magazynu. Plik jest "spłaszczany" przy odczycie.

#### <a name="options"></a>Opcje
- Brak

### <a name="parquet"></a>Parquet
Zestaw danych Parquet, do odczytu albo pojedynczy plik lub folder.

Parquet, ponieważ format może mieć różną postać w magazynie. Dla mniejszych zestawów danych jest czasami używane .parquet pojedynczego pliku. Różnych bibliotek języka Python obsługuje Odczyt lub zapis pojedynczej .parquet plików. Na tę chwilę przygotowywania danych usługi Azure Machine Learning polega na biblioteki PyArrow Python do odczytywania Parquet podczas użycia lokalnego interaktywne. Obsługuje ona pojedynczy .parquet plików (tak długo, jak zostały napisane działa w taki sposób, a nie jako część większego zbioru danych), a także Parquet zestawów danych.

Zestaw danych Parquet to zbiór więcej niż jeden plik .parquet, z których każdy reprezentuje mniejszy partycji większy zestaw danych. Zestawy danych zwykle są zawarte w folderze i są domyślnym formatem danych wyjściowych parquet platform, takich jak Spark i Hive.

>[!NOTE]
>Podczas czytania danych Parquet, który znajduje się w folderze z wieloma plikami .parquet, najbezpieczniej wybierz katalog do odczytu, jest i **zestawu danych Parquet** opcji. Dzięki temu PyArrow odczytu całego folderu zamiast pojedynczych plików. Dzięki temu odczytywanie bardziej złożone sposoby przechowywania Parquet na dysku, takie jak folder partycjonowania.

Wykonanie skalowalnego w poziomie opiera się na Parquet platforma Spark odczytywanie możliwości i obsługuje pojedynczych plików oraz folderów, podobne do użytku lokalnego interaktywne.

#### <a name="options"></a>Opcje
- Parquet zestawu danych. Ta opcja określa, czy przygotowywania danych usługi Azure Machine Learning rozwija danego katalogu, a następnie próbuje odczytać każdego pliku osobno (tryb niezaznaczone), lub czy traktuje katalogu jako cały zestaw danych (tryb wybrany). Przy użyciu wybranego trybu PyArrow wybiera najlepszy sposób interpretowania plików.


## <a name="locations"></a>Lokalizacje
### <a name="local"></a>Lokalna
Lokalny dysk twardy lub lokalizacji magazynu mapowanej sieci.

### <a name="sql-server"></a>Oprogramowanie SQL Server
Lokalne programu SQL Server, lub bazy danych Azure SQL.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Usługa Azure Blob storage, co wymaga subskrypcji platformy Azure.

