---
title: Najważniejsze wskazówki dotyczące wybierania identyfikatora szeregów czasowych — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się więcej o najlepszych rozwiązaniach podczas wybierania identyfikatora szeregów czasowych w usłudze Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083531"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Najważniejsze wskazówki dotyczące wybierania identyfikatora szeregów czasowych

W tym artykule podsumowano znaczenie identyfikatora szeregów czasowych dla środowiska usługi Azure Time Series Insights Preview oraz najważniejsze wskazówki dotyczące wybierania jednego z nich.

## <a name="choose-a-time-series-id"></a>Wybieranie identyfikatora szeregów czasowych

Wybór odpowiedniego identyfikatora szeregów czasowych ma kluczowe znaczenie. Wybieranie identyfikatora szeregów czasowych jest jak wybranie klucza partycji dla bazy danych. Jest to wymagane podczas tworzenia środowiska usługi Time Series Insights Preview. 

> [!IMPORTANT]
> Identyfikatory szeregów czasowych to:
> * Właściwość *z uwzględnieniem wielkości liter:* wielkość liter i znaków są używane w wyszukiwaniach, porównaniach, aktualizacjach i podczas partycjonowania.
> * Właściwość *niezmienna:* po utworzeniu nie można jej zmienić.

> [!TIP]
> Jeśli źródłem zdarzeń jest centrum IoT hub, identyfikator szeregów czasowych będzie prawdopodobnie ***iothub-connection-device-id***.

Najważniejsze najważniejsze wskazówki do naśladowania obejmują:

* Wybierz klucz partycji z wieloma różnymi wartościami (na przykład setkami lub tysiącami). W wielu przypadkach może to być identyfikator urządzenia, identyfikator czujnika lub identyfikator tagu w JSON.
* Identyfikator szeregów czasowych powinien być unikatowy na poziomie węzła [liścia modelu szeregów czasowych.](./time-series-insights-update-tsm.md)
* Limit znaków dla ciągu nazwy właściwości identyfikatora szeregów czasowych wynosi 128. Dla wartości właściwości identyfikatora szeregów czasowych limit znaków wynosi 1024.
* Jeśli brakuje unikatowej wartości właściwości dla identyfikatora szeregów czasowych, jest traktowana jako wartość null i jest zgodna z tą samą regułą ograniczenia unikatowości.
* Można również wybrać maksymalnie *trzy* kluczowe właściwości jako identyfikator szeregów czasowych. Ich kombinacja będzie klucz złożony, który reprezentuje identyfikator szeregów czasowych.  
  > [!NOTE]
  > Trzy właściwości klucza muszą być ciągami.
  > Należy zbadać względem tego klucza złożonego zamiast jednej właściwości naraz.

## <a name="select-more-than-one-key-property"></a>Wybierz więcej niż jedną właściwość klucza

W poniższych scenariuszach opisano wybranie więcej niż jednej właściwości klucza jako identyfikatora szeregów czasowych.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Przykład 1: Identyfikator szeregów czasowych z unikatowym kluczem

* Masz starsze floty aktywów. Każdy z nich ma unikatowy klucz.
* Jedna flota jest jednoznacznie identyfikowana przez **właściwość deviceId**. Dla innej floty unikatową właściwością jest **objectId**. Żadna z flot nie zawiera wyjątkowej własności drugiej floty. W tym przykładzie należy wybrać dwa klucze, **deviceId** i **objectId**, jako unikatowe klucze.
* Akceptujemy wartości null, a brak obecności właściwości w ładunku zdarzenia liczy się jako wartość null. Jest to również odpowiedni sposób obsługi wysyłania danych do dwóch źródeł zdarzeń, gdzie dane w każdym źródle zdarzeń ma unikatowy identyfikator szeregów czasowych.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Przykład 2: Identyfikator szeregów czasowych z kluczem złożonym

* Wymaga to, aby wiele właściwości było unikatowych w ramach tej samej floty zasobów. 
* Jesteś producentem inteligentnych budynków i wdrażasz czujniki w każdym pomieszczeniu. W każdym pomieszczeniu zazwyczaj masz te same wartości dla **sensorId**. Przykładami są **sensor1**, **sensor2**i **sensor3**.
* Twój budynek ma nakładające się numery podłogi i pomieszczeń w różnych miejscach w **flrRm**nieruchomości. Te liczby mają takie wartości jak **1a**, **2b**i **3a**.
* Masz właściwość, **lokalizację**, która zawiera wartości, takie jak **Redmond**, **Barcelona**i **Tokio**. Aby utworzyć unikatowość, należy wyznaczyć następujące trzy właściwości jako klucze identyfikatora szeregów czasowych: **sensorId**, **flrRm**i **location**.

Przykładowe zdarzenie pierwotne:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

W witrynie Azure portal można następnie wprowadzić klucz złożony w następujący sposób: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [modelowaniu danych](./time-series-insights-update-tsm.md).

* Zaplanuj [środowisko usługi Azure Time Series Insights Preview](./time-series-insights-update-plan.md).