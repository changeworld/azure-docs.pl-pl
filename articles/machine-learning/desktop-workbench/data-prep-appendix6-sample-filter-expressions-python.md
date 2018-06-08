---
title: Przykładowe wyrażenia filtru jest możliwe za pomocą usługi Azure Machine Learning Przygotowanie danych | Dokumentacja firmy Microsoft
description: Ten dokument zawiera zestaw przykładów wyrażeń filtru przy użyciu usługi Azure Machine Learning Przygotowanie danych
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
ms.openlocfilehash: 434da595762a077b94ff034325d5bdb156585884
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830364"
---
# <a name="sample-of-filter-expressions-python"></a>Przykładowe wyrażenia filtru (Python) 
Przed przeczytaniem tego dodatku odczytu [Przegląd rozszerzalności języka Python](data-prep-python-extensibility-overview.md).

## <a name="filter-with-equivalence-test"></a>Filtrowanie za pomocą równoważność testu
Filtruj tylko w wierszach których wartość Col2 (numeryczną) jest większa niż 4. 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Filtruj z wieloma kolumnami 
Filtruj tylko w wierszach gdzie Col1 zawiera wartość **dobrej** i Col2 zawiera wartość 1 (numeryczną). 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Filtr testu względem wartości null
Filtruj tylko w wierszach gdzie Col1 ma wartość null. 
```python
    pd.isnull(row["Col1"])
```
