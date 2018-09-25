---
title: Przykładowe języka Python do pochodząca z nowymi kolumnami podczas przygotowywania danych usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Ten dokument zawiera przykłady kodu Python do tworzenia nowych kolumn podczas przygotowywania danych usługi Azure Machine Learning.
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
ms.openlocfilehash: 43a66efde2623a848065bbde2be4b56fd6c7fc8b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969312"
---
# <a name="sample-of-custom-column-transforms-python"></a>Przykładowe transformacje kolumn niestandardowych (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Nazwa tej transformacji, w menu jest **Dodaj kolumnę (skrypt)**.

Przed przeczytaniem tego dodatku, przeczytaj [Przegląd rozszerzalności Python](data-prep-python-extensibility-overview.md).

## <a name="test-equivalence-and-replace-values"></a>Testowanie równoważności i Zastąp wartości 
Jeśli wartość w Col1 jest mniejszy niż 4, nowej kolumny, która powinna mieć wartość 1. Jeśli wartość w Col1 jest więcej niż 4, nowej kolumny, która ma wartość 2. 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>Bieżąca data i godzina 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>Rzutowanie typów 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>Oceny pod kątem 
Jeśli Col1 zawiera wartość null, a następnie Oznacz jako nowe kolumny **zły**. Jeśli nie, oznacz ją jako **dobre.** 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Nowe kolumny obliczanej 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Obliczenie epoka. 
Liczba sekund od początku epoki systemu Unix (Col1 przyjmuje już jest wartość typu date): 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```

## <a name="hash-a-column-value-into-a-new-column"></a>Hash — wartość kolumny w nową kolumnę
```python
    import hashlib
    hash(row["MyColumnToHashCol1"])

```




