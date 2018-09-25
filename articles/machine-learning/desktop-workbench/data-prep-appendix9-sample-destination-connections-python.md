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
ROBOTS: NOINDEX
ms.openlocfilehash: 3e827c56caa787ae9fc33119810eea0f268f0120
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958425"
---
# <a name="sample-of-destination-connections-python"></a>Przykładowe połączenia miejsc docelowych (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


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
