---
title: Przykład miejsc docelowych/dane wyjściowe przy użyciu usługi Azure Machine Learning Przygotowanie danych | Dokumentacja firmy Microsoft
description: Ten dokument zawiera zestaw przykładowych danych niestandardowych miejsc docelowych/wyjść z Przygotowanie danych usługi Azure Machine Learning
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
ms.openlocfilehash: 93da07006280b38c9e6539773e6ac22e50e48b57
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831816"
---
# <a name="sample-of-destination-connections-python"></a>Przykładowe połączeń docelowego (Python) 
Przed przeczytaniem tego dodatku odczytu [Przegląd rozszerzalności języka Python](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Zapis do programu Excel 


Zapisywanie do programu Excel wymaga dodatkowych biblioteki. Dodawanie nowej biblioteki jest udokumentowany w przeglądzie rozszerzalności. `openpyxl` jest biblioteki, który należy dodać.

Przed przystąpieniem do napisania do programu Excel, niektóre inne zmiany mogą być wymagane. Niektóre typy danych, które są używane w przygotowaniu danych nie są obsługiwane w niektórych formatów docelowego. Na przykład jeśli istnieją obiekty "Błąd", ich nie będzie serializować poprawnie do programu Excel. Przed podjęciem próby zapisu do programu Excel, w związku z tym należy transformacji "Zastąp wartości błędów", która usuwa błędy z żadnych kolumn.

Jeśli wszystkie poprzednie pracy zostało zakończone, następujący wiersz zapisuje tabeli danych do jednego arkusza w dokumencie programu Excel. Dodaj transformacji przekształcenie przepływu danych (skrypt). Następnie wprowadź poniższy kod w sekcji wyrażenia.


### <a name="on-windows"></a>W systemie Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>Na macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
