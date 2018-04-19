---
title: "Przykładowe Python do wyprowadzanie nowe kolumny w przygotowaniu danych usługi Azure Machine Learning | Dokumentacja firmy Microsoft"
description: "Ten dokument zawiera przykłady kodu dla języka Python do tworzenia nowych kolumn w przygotowaniu danych usługi Azure Machine Learning."
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
ms.openlocfilehash: 02670fa4506237ac391e467e5c7a1187b1131ece
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
---
# <a name="sample-of-custom-column-transforms-python"></a>Przykładowe kolumny niestandardowych przekształceń (Python) 
Nazwa tej transformacji w menu jest **Dodawanie kolumny (skrypt)**.

Przed przeczytaniem tego dodatku odczytu [Przegląd rozszerzalności języka Python](data-prep-python-extensibility-overview.md).

## <a name="test-equivalence-and-replace-values"></a>Testowanie równoważność i Zastąp wartości 
Jeśli wartość Col1 jest mniejszy niż 4, nowej kolumny, która powinna mieć wartość 1. Jeśli wartość Col1 jest więcej niż 4, nowej kolumny, która ma wartość 2. 

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
## <a name="evaluate-for-nullness"></a>Ocena programu nullness 
Jeśli Col1 zawiera wartość null, oznacz nową kolumnę jako **zły**. Jeśli nie, oznacz go jako **dobra.** 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Nowe kolumny obliczanej 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Obliczenia epoka. 
Liczba sekund od epoki systemu Unix (przyjmuje Col1 jest już datę): 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```

## <a name="hash-a-column-value-into-a-new-column"></a>Hash — wartość kolumny w nową kolumnę
```python
    import hashlib
    hash(row["MyColumnToHashCol1"])

```




