---
title: Przykład przekształcania danych przepływ przekształcenia przy użyciu usługi Azure Machine Learning danych przygotowanie | Dokumentacja firmy Microsoft
description: Ten dokument zawiera zbiór przykłady przekształcania danych przepływ przekształcenia przy użyciu usługi Azure Machine Learning Przygotowanie danych
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 655e9d41911fbb008470cf58b2538407933787bd
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832078"
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>Przykładowe dane niestandardowe przepływ przekształcenia (Python) 
Nazwa przekształcenie w menu jest **przekształcenie przepływu danych (skrypt)**. Przed przeczytaniem tego dodatku odczytu [Przegląd rozszerzalności języka Python](data-prep-python-extensibility-overview.md).

## <a name="transform-frame"></a>Przekształć ramki
### <a name="create-a-new-column-dynamically"></a>Utwórz nową kolumnę dynamicznie 
Dynamicznie tworzy kolumny (**city2**) i uzgadnia wielu różnych wersji Poznań do jednego z istniejących kolumny miasta.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Dodawanie nowego zagregowanych danych
Tworzy nową ramkę z wartości zagregowanych pierwszy i ostatni obliczanej kolumny wynik. Są one pogrupowane według **risk_category** kolumny.
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>Winsorize kolumny 
Reformulates dane spełniają formułę zmniejszenie wartości oddalone w kolumnie.
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>Przekształć przepływu danych
### <a name="fill-down"></a>Wypełnij w dół 

Wypełnienie w dół wymaga dwóch transformacji. Przyjęto założenie, danych, która wygląda podobnie do poniższej tabeli:

|Stan         |Miasto       |
|--------------|-----------|
|Waszyngton    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |Seattle    |
|Kalifornia    |Los Angeles|
|              |Diego sieci SAN  |
|              |San Jose   |
|Teksas         |Dallas     |
|              |San Antonio|
|              |Houston    |

1. Utwórz transformacji "Dodaj kolumny (skrypt)", używając następującego kodu:
```python
    row['State'] if len(row['State']) > 0 else None
```

2. Utwórz transformacji "Przekształcenia przepływ danych (skrypt)", zawierający następujący kod:
```python
    df = df.fillna( method='pad')
```

Dane teraz wygląda w poniższej tabeli:

|Stan         |newState         |Miasto       |
|--------------|--------------|-----------|
|Waszyngton    |Waszyngton    |Redmond    |
|              |Waszyngton    |Bellevue   |
|              |Waszyngton    |Issaquah   |
|              |Waszyngton    |Seattle    |
|Kalifornia    |Kalifornia    |Los Angeles|
|              |Kalifornia    |Diego sieci SAN  |
|              |Kalifornia    |San Jose   |
|Teksas         |Teksas         |Dallas     |
|              |Teksas         |San Antonio|
|              |Teksas         |Houston    |


### <a name="min-max-normalization"></a>Maksymalna liczba min normalizacji
```python
    df["NewCol"] = (df["Col1"]-df["Col1"].mean())/df["Col1"].std()
```