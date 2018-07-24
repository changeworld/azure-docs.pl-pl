---
title: Przykładowe wyrażenia filtru możliwego do przygotowania danych usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Ten dokument zawiera zestaw przykładów wyrażeń filtru możliwego do przygotowania danych usługi Azure Machine Learning
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
ms.openlocfilehash: a960197c89e1d051de0e9b8b73cbab231982cf52
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216525"
---
# <a name="sample-of-filter-expressions-python"></a>Przykładowe wyrażenia filtru (Python) 
Przed przeczytaniem tego dodatku, przeczytaj [Przegląd rozszerzalności Python](data-prep-python-extensibility-overview.md).

## <a name="filter-with-equivalence-test"></a>Filtrowanie za pomocą testu równoważności
Filtr tylko te wiersze, w których wartość Col2 (numeryczne) jest większa niż 4. 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Filtrowanie za pomocą wielu kolumn 
Filtr tylko te wiersze, w którym Col1 zawiera wartość **dobre** i Col2 zawiera (liczbowe) wartość 1. 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Filtr testu mają wartości null
Filtr tylko te wiersze, w którym Col1 ma wartość null. 
```python
    pd.isnull(row["Col1"])
```
