---
title: Najlepsze rozwiązania dotyczące wybierania identyfikatora szeregów czasowych — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się więcej o najlepszych rozwiązaniach dotyczących wybierania identyfikatora szeregów czasowych w wersji zapoznawczej Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/19/2019
ms.custom: seodec18
ms.openlocfilehash: 6fd8d6187c86306840c33b3aaf334e71086b20a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452742"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Najlepsze rozwiązania dotyczące wybierania Identyfikatora serii czasu

Ten artykuł podsumowuje ważność identyfikatora szeregów czasowych dla środowiska Azure Time Series Insights w wersji zapoznawczej oraz najlepsze rozwiązania dotyczące ich wyboru.

## <a name="choose-a-time-series-id"></a>Wybieranie identyfikatora szeregów czasowych

Wybór odpowiedniego identyfikatora szeregów czasowych ma krytyczne znaczenie. Wybieranie Identyfikatora serii czasu przypomina wybór klucza partycji dla bazy danych. Jest to wymagane w przypadku tworzenia środowiska Time Series Insights w wersji zapoznawczej. 

> [!IMPORTANT]
> Identyfikatory szeregów czasowych:
> * Właściwość uwzględniająca *wielkość* liter: litery i wielkości liter znaków są używane w wyszukiwaniach, porównaniach, aktualizacjach i podczas partycjonowania.
> * *Niezmienna* Właściwość: po utworzeniu nie można jej zmienić.

> [!TIP]
> Jeśli źródłem zdarzenia jest centrum IoT, identyfikator szeregów czasowych będzie prawdopodobnie ***iothub-Connection-ID***.

Najważniejsze wskazówki dotyczące najważniejszych rozwiązań:

* Wybierz klucz partycji z wieloma unikatowymi wartościami (na przykład setki lub tysiące). W wielu przypadkach może to być identyfikator urządzenia, identyfikator czujnika lub identyfikator tagu w formacie JSON.
* Identyfikator serii czasu powinna być unikatowa na poziomie liścia węzła usługi [modelu szeregów czasowych](./time-series-insights-update-tsm.md).
* Limit znaków dla ciągu nazwy właściwości identyfikatora szeregów czasowych to 128. W przypadku wartości właściwości identyfikator szeregów czasowych limit znaków to 1 024.
* Jeśli brakuje unikatowej wartości właściwości identyfikatora szeregów czasowych, jest ona traktowana jako wartość null i zgodna z tą samą regułą ograniczenia unikatowości.
* Możesz również wybrać do *trzech* właściwości klucza jako identyfikator szeregów czasowych. Ich kombinacje będą kluczem złożonym, który reprezentuje identyfikator szeregów czasowych.  
  > [!NOTE]
  > Twoje trzy właściwości klucza muszą być ciągami.
  > Należy wykonać zapytanie względem tego klucza złożonego zamiast jednej właściwości naraz.

## <a name="select-more-than-one-key-property"></a>Wybierz więcej niż jedną właściwość klucza

W poniższych scenariuszach opisano wybieranie więcej niż jednej właściwości klucza jako identyfikatora szeregów czasowych.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>Przykład 1: Identyfikator szeregów czasowych z unikatowym kluczem

* Masz starsze floty zasobów. Każdy z nich ma unikatowy klucz.
* Jedna flota jest unikatowo identyfikowana przez identyfikator **deviceId**właściwości. Dla innej floty właściwość UNIQUE ma wartość **objectid**. Żadna flota nie zawiera unikatowej właściwości innej floty. W tym przykładzie należy wybrać dwa klucze, **deviceId** i **objectid**, jako unikatowe klucze.
* Akceptujemy wartości null, a brak obecności właściwości w ładunku zdarzenia jest traktowany jako wartość null. Jest to również odpowiedni sposób obsługi wysyłania danych do dwóch źródeł zdarzeń, gdzie dane w każdym źródle zdarzeń mają unikatowy identyfikator szeregów czasowych.

### <a name="example-2-time-series-id-with-a-composite-key"></a>Przykład 2: Identyfikator szeregów czasowych z kluczem złożonym

* Wymagane jest wiele właściwości, aby być unikatowe w obrębie tego samego jej zasobów. 
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

W Azure Portal można następnie wprowadzić klucz złożony w następujący sposób: 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>Następne kroki

* Przeczytaj więcej na temat [modelowania danych](./time-series-insights-update-tsm.md).

* Zaplanuj [środowisko Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-plan.md).