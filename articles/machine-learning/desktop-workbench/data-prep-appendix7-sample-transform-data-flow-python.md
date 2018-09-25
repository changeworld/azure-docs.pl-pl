---
title: Przykład przekształcania danych przepływ przekształcenia możliwości usługi Azure Machine Learning danych przygotowanie | Dokumentacja firmy Microsoft
description: Ten dokument zawiera zbiór przykłady przekształcenia przepływów danych przekształcenie możliwego do przygotowania danych usługi Azure Machine Learning
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
ms.openlocfilehash: 82295e412c70065ff8ce3a686aec790614f39f2e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947204"
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>Przykładowe niestandardowe przekształcenia przepływów danych (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Nazwa przekształcenia w menu jest **Przekształć przepływ danych (skrypt)**. Przed przeczytaniem tego dodatku, przeczytaj [Przegląd rozszerzalności Python](data-prep-python-extensibility-overview.md).

## <a name="transform-frame"></a>Przekształcanie ramki
### <a name="create-a-new-column-dynamically"></a>Utwórz nową kolumnę dynamicznie 
Dynamicznie tworzy kolumnę (**city2**) i uzgadnia wiele różnych wersji San Francisco do jednego z istniejących kolumny city.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Dodaj nowe agregacje
Tworzy nową ramkę z wartości zagregowanych pierwszy i ostatni obliczane kolumny wynik. Są one pogrupowane według **risk_category** kolumny.
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>Winsorize kolumny 
Reformulates dane zgodnie z wymaganiami formułę na zmniejszenie wartości odstających w kolumnie.
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>Przekształcanie przepływu danych
### <a name="fill-down"></a>Wypełnij w dół 

Wypełnienie w dół wymaga dwóch przekształcenia. Przyjęto założenie, danych, która wygląda podobnie do poniższej tabeli:

|Stan         |Miasto       |
|--------------|-----------|
|Waszyngton    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |Seattle    |
|Kalifornia    |Los Angeles|
|              |San Diego  |
|              |San Jose   |
|Teksas         |Dallas     |
|              |San Antonio|
|              |Houston    |

1. Utwórz przekształcenie "Dodaj kolumnę (skrypt)", używając następującego kodu:
```python
    row['State'] if len(row['State']) > 0 else None
```

2. Utwórz przekształcenie "Przekształć przepływ danych (skrypt)", zawierający następujący kod:
```python
    df = df.fillna( method='pad')
```

Dane wygląda teraz następująco poniższej tabeli:

|Stan         |newState         |Miasto       |
|--------------|--------------|-----------|
|Waszyngton    |Waszyngton    |Redmond    |
|              |Waszyngton    |Bellevue   |
|              |Waszyngton    |Issaquah   |
|              |Waszyngton    |Seattle    |
|Kalifornia    |Kalifornia    |Los Angeles|
|              |Kalifornia    |San Diego  |
|              |Kalifornia    |San Jose   |
|Teksas         |Teksas         |Dallas     |
|              |Teksas         |San Antonio|
|              |Teksas         |Houston    |


### <a name="min-max-normalization"></a>Normalizacji minimum maksimum
```python
    df["NewCol"] = (df["Col1"]-df["Col1"].mean())/df["Col1"].std()
```