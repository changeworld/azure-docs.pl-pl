---
title: Najlepsze rozwiązania dotyczące wybierania identyfikatora szeregów czasowych w wersji zapoznawczej Azure Time Series Insights | Microsoft Docs
description: Zrozumienie najlepszych rozwiązań w przypadku wybrania identyfikatora szeregów czasowych w wersji zapoznawczej Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: 48f1fb542f5e28c7b8130d03cd86442390a8ad56
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989939"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Najlepsze rozwiązania dotyczące wybierania identyfikatora szeregów czasowych

Ten artykuł podsumowuje ważność identyfikatora szeregów czasowych dla środowiska Azure Time Series Insights w wersji zapoznawczej oraz najlepsze rozwiązania dotyczące ich wyboru.

## <a name="choose-a-time-series-id"></a>Wybieranie identyfikatora szeregów czasowych

Wybór identyfikatora szeregów czasowych jest taki sam jak wybór klucza partycji dla bazy danych. Należy ją wybrać podczas tworzenia środowiska Time Series Insights w wersji zapoznawczej. Jest to *niezmienna* właściwość. Oznacza to, że po utworzeniu środowiska Time Series Insights w wersji zapoznawczej przy użyciu identyfikatora szeregów czasowych nie można zmienić go dla tego środowiska. 

> [!IMPORTANT]
> W IDENTYFIKATORze szeregów czasowych jest rozróżniana wielkość liter.

Wybór odpowiedniego identyfikatora szeregów czasowych ma krytyczne znaczenie. Poniżej przedstawiono niektóre najlepsze rozwiązania, które można wykonać:

* Wybierz klucz partycji z wieloma unikatowymi wartościami (na przykład setki lub tysiące). W wielu przypadkach może to być identyfikator urządzenia, identyfikator czujnika lub identyfikator tagu w formacie JSON.
* Identyfikator szeregów czasowych musi być unikatowy na poziomie węzła liścia [modelu szeregów czasowych](./time-series-insights-update-tsm.md).
* Jeśli źródłem zdarzenia jest centrum IoT, identyfikator szeregów czasowych najprawdopodobniej będzie *iothub-Connection-ID*.
* Limit znaków dla ciągu nazwy właściwości identyfikatora szeregów czasowych to 128. W przypadku wartości właściwości identyfikator szeregów czasowych limit znaków to 1 024.
* Jeśli brakuje unikatowej wartości właściwości identyfikatora szeregów czasowych, jest ona traktowana jako wartość null i zgodna z tą samą regułą ograniczenia unikatowości.
* Możesz również wybrać do *trzech* właściwości klucza jako identyfikator szeregów czasowych. Ich kombinacje będą kluczem złożonym, który reprezentuje identyfikator szeregów czasowych.  

  > [!NOTE]
  > Twoje trzy właściwości klucza muszą być ciągami.
  > Należy wykonać zapytanie względem tego klucza złożonego zamiast jednej właściwości naraz.

W poniższych scenariuszach opisano wybieranie więcej niż jednej właściwości klucza jako identyfikatora szeregów czasowych.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Przykład 1: Identyfikator szeregów czasowych z unikatowym kluczem

* Masz starsze floty zasobów. Każdy z nich ma unikatowy klucz.
* Jedna flota jest unikatowo identyfikowana przez identyfikator **deviceId**właściwości. Dla innej floty właściwość UNIQUE ma wartość **objectid**. Żadna flota nie zawiera unikatowej właściwości innej floty. W tym przykładzie należy wybrać dwa klucze, **deviceId** i **objectid**, jako unikatowe klucze.
* Akceptujemy wartości null, a brak obecności właściwości w ładunku zdarzenia jest traktowany jako wartość null. Jest to również odpowiedni sposób obsługi wysyłania danych do dwóch źródeł zdarzeń, gdzie dane w każdym źródle zdarzeń mają unikatowy identyfikator szeregów czasowych.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Przykład 2: Identyfikator szeregów czasowych z kluczem złożonym

* Wymagane jest, aby wiele właściwości była unikatowa w ramach tej samej floty zasobów. 
* Jesteś producentem inteligentnych budynków i wdrażaj czujniki w każdym pokoju. W każdym pokoju zwykle są te same wartości dla **sensorId**. Przykłady to **sensor1**, **Sensor2**i **sensor3**.
* Budynek zawiera nakładające się numery podłóg i pomieszczeń między lokacjami we właściwości **flrRm**. Te liczby zawierają wartości, takie jak **1a**, **2b**i **3a**.
* Istnieje właściwość, **Lokalizacja**, która zawiera wartości, takie jak **Redmond**, **Barcelona**i **Tokio**. Aby utworzyć unikatowość, należy wyznaczyć następujące trzy właściwości jako klucze identyfikatorów szeregów czasowych: **sensorId**, **flrRm**i **Location**.

Przykładowe zdarzenie pierwotne:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

W Azure Portal można wprowadzić ten klucz złożony jako: 

`[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]`

## <a name="next-steps"></a>Następne kroki

* Przeczytaj więcej na temat [modelowania danych](./time-series-insights-update-tsm.md).

* Zaplanuj [środowisko Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-plan.md).