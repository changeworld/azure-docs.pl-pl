---
title: Obsługiwane miejsca docelowe danych i danych wyjściowych udostępniono przygotowywania danych usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Ten dokument zawiera pełną listę obsługiwanych lokalizacji docelowych, a także generuje dostępne podczas przygotowywania danych usługi Azure Machine Learning
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
ms.openlocfilehash: 123328010758eea6e7eadce29440e204f91dcef6
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644934"
---
# <a name="supported-data-exports-for-this-preview"></a>Obsługiwane eksportu danych dla tej wersji zapoznawczej 
Istnieje możliwość wyeksportować do kilku różnych formatach. Te formaty służy do przechowywania wyników pośrednich przygotowywania danych, zanim włączysz wyniki w pozostałej części przepływu pracy usługi Machine Learning.

## <a name="types"></a>Typy 
### <a name="csv-file"></a>Plik CSV 
Wpisywanie do pliku tekstowego przecinkami wartości rozdzielanych przecinkami w magazynie.

#### <a name="options"></a>Opcje
- Końce wierszy
- Zastąp wartości null za pomocą
- Zamień błędy 
- Separator


### <a name="parquet"></a>Parquet 
Pisanie zestawu danych do magazynu jako Parquet.

Parquet, ponieważ format może mieć różną postać w magazynie. Dla mniejszych zestawów danych jest czasami używane .parquet pojedynczego pliku. Różnych bibliotek języka Python obsługuje Odczyt i zapis do pojedynczego .parquet plików. 

Obecnie usługa Azure Machine Learning Workbench opiera się na biblioteki PyArrow Python wypisywanie Parquet podczas użycia lokalnego interaktywne. Oznacza to, że pojedynczy plik Parquet jest obecnie tylko format danych wyjściowych Parquet, który jest obsługiwany podczas użycia lokalnego interaktywne.

Podczas skalowania w poziomie działa (na platformie Spark) Azure Machine Learning Workbench opiera się na Parquet platforma Spark odczytywanie i zapisywanie możliwości. Platforma Spark domyślny format danych wyjściowych Parquet (obecnie jedyną obsługiwane) jest strukturę podobną do zestawu danych programu Hive. Oznacza to, że folder zawiera wiele plików .parquet, z których każda mniejszych partycji większy zestaw danych. 

#### <a name="caveats"></a>Ostrzeżenia 
Parquet formatu jest stosunkowo młode i ma pewne niespójności wdrożenia w różnych bibliotek. Na przykład Spark umieszcza ograniczenia, na których znaki są prawidłowe w nazwach kolumn podczas zapisywania Parquet. PyArrow nie tego robić. Następujące znaki nie mogą być w nazwie kolumny: 
- , 
- ;
- {}
- ()
- \\n
- \\t
- =

>[!NOTE]
>- Aby zapewnić zgodność z platformą Spark, każdym razem, gdy można zapisać danych do Parquet, wystąpienia tych znaków w nazwach kolumn są zastępowane i znaku podkreślenia (_).
>- W celu zapewnienia spójności we wszystkich przebiegach lokalnych i w poziomie, wszystkie dane zapisane Parquet, za pośrednictwem aplikacji języka Python i platformy Spark, nazwy kolumn został oczyszczony do zapewnienia zgodności platformy Spark. W celu zapewnienia oczekiwanej kolumny nazwy, podczas zapisywania znaków Parquet platformy Spark, Usuń nieprawidłowe ustawienie z kolumn przed ich wypisywanie.



## <a name="locations"></a>Lokalizacje 
### <a name="local"></a>Lokalna 
Lokalny dysk twardy lub lokalizacji magazynu mapowanej sieci.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Usługa Azure Blob storage wymaga subskrypcji platformy Azure.

