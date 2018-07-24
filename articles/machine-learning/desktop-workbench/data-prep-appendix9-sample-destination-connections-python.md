---
title: Przykład miejsc docelowych lub wyjściowych możliwego do przygotowania danych usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Ten dokument zawiera zestaw przykładowych danych niestandardowych miejsc docelowych lub wyjściowych przy użyciu usługi Azure Machine Learning, przygotowywanie danych
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
ms.openlocfilehash: 2173ff15906940b8628aba3615f31e3f7137e3e2
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216537"
---
# <a name="sample-of-destination-connections-python"></a>Przykładowe połączenia miejsc docelowych (Python) 
Przed przeczytaniem tego dodatku, przeczytaj [Przegląd rozszerzalności Python](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Zapis do programu Excel 


Zapisywanie do programu Excel wymaga dodatkowe biblioteki. Dodawanie nowej biblioteki jest udokumentowany w omówieniu rozszerzalności. `openpyxl` to biblioteka, który chcesz dodać.

Przed przystąpieniem do napisania do programu Excel, niektóre inne zmiany mogą być wymagane. Niektóre typy danych, które są używane w przygotowaniu danych nie są obsługiwane w niektórych formatach docelowego. Na przykład jeśli istnieją obiekty "Error", mogą nie serializować poprawnie do programu Excel. Dlatego przed podjęciem próby zapisu do programu Excel, należy przekształcenie "Zastąp wartości błędów", która usuwa błędy z żadnych kolumn.

Jeśli wszystkie poprzednie pracy zostało zakończone, następujący wiersz zapisuje tabeli danych do pojedynczej w dokumencie programu Excel. Dodaj przekształcenie Przekształć przepływ danych (skrypt). Następnie wprowadź poniższy kod w sekcji wyrażenia.


### <a name="on-windows"></a>W systemie Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>W systemie macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
