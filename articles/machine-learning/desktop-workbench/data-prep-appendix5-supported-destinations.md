---
title: "Obsługiwane miejsca docelowe danych i dane wyjściowe dostępna w programie Przygotowanie danych usługi Azure Machine Learning | Dokumentacja firmy Microsoft"
description: "Ten dokument zawiera pełną listę obsługiwanych lokalizacji docelowych i wyświetla dostępne w celu przygotowania danych usługi Azure Machine Learning"
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
ms.openlocfilehash: 5451a5f525da845f97141a97241efb53ec42ff58
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="supported-data-exports-for-this-preview"></a>Obsługiwane eksportuje dane dla tej wersji zapoznawczej 
Istnieje możliwość eksportowania do kilku różnych formatach. Te formaty służy do przechowania wyników pośrednich przygotowywania danych przed integracji wyniki w pozostałej części przepływu pracy Machine Learning.

## <a name="types"></a>Typy 
### <a name="csv-file"></a>Plik CSV 
Zapis pliku — wartości rozdzielanych do magazynu.

#### <a name="options"></a>Opcje
- Zakończenia wierszy
- Zastąp wartości null z
- Zastąp błędy 
- Separator


### <a name="parquet"></a>Parquet 
Zestaw danych do magazynu zapisu jako Parquet.

Parquet zgodnie z formatem mogą mieć różne formy w magazynie. W przypadku mniejszych zestawów danych jest czasami używana .parquet pojedynczego pliku. Różne biblioteki Python obsługują odczytywanie i zapisywanie w plikach .parquet pojedynczego. 

Obecnie Azure Machine Learning Workbench korzysta z biblioteki PyArrow Python wypisywanie Parquet podczas lokalnego interakcyjnej. Oznacza to, że pojedynczy plik Parquet ma obecnie tylko format danych wyjściowych Parquet, który jest obsługiwany podczas lokalnego interakcyjnej.

Azure Machine Learning Workbench podczas skalowania w poziomie działa (na Spark), opiera się na Parquet platforma Spark odczytywanie i zapisywanie możliwości. Platforma Spark domyślny format danych wyjściowych Parquet (obecnie jedyną obsługiwane) ma podobną strukturę DataSet Hive. Oznacza to, że folder zawiera wiele plików .parquet, z których każda mniejszych partycji większy zestaw danych. 

#### <a name="caveats"></a>Ostrzeżenia 
Parquet format jest stosunkowo małych i ma pewne niespójności implementacji w różne biblioteki. Na przykład Spark umieszcza ograniczenia, na których znaków są dozwolone w nazwach kolumny podczas zapisywania Parquet. PyArrow nie robi to. Nazwa kolumny nie może mieć następujące znaki: 
- ,
- ;
- {}
- ()
- \\n
- \\t
- =

>[!NOTE]
>- Aby zapewnić zgodność z Spark, zapisać danych do Parquet, za każdym razem wystąpień tych znaków nazwy kolumn są zamieniane na i znaku podkreślenia (_).
>- Zapewnienie spójności przebiegów lokalnych i skalowalnego w poziomie, wszystkie dane zapisane za pomocą aplikacji, Python lub Spark, Parquet, nazwy kolumn został oczyszczony zgodności Spark. W celu zapewnienia oczekiwanej kolumny nazw podczas zapisywania Parquet znaków Spark, Usuń Nieprawidłowa wartość z kolumny przed ich zapisaniem.



## <a name="locations"></a>Lokalizacje 
### <a name="local"></a>Lokalna 
Lokalny dysk twardy lub lokalizacji magazynu mapowanej sieci.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Magazyn obiektów Blob Azure wymaga subskrypcji platformy Azure.

