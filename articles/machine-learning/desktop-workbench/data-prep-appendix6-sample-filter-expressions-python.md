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
ROBOTS: NOINDEX
ms.openlocfilehash: a389007dea344de461b23b96f2942686005aa119
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982396"
---
# <a name="sample-of-filter-expressions-python"></a>Przykładowe wyrażenia filtru (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


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
