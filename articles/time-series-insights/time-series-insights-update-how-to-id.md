---
title: Najlepsze rozwiązania dotyczące wybierania Identyfikatora serii czasu w wersji zapoznawczej Azure czas serii szczegółowych informacji | Dokumentacja firmy Microsoft
description: Zrozumienie najlepszych rozwiązań w przypadku wybrania identyfikatora szeregów czasowych w wersji zapoznawczej Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 7057ce27cbbba8d70835493fc91a88ad823369bb
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947196"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>Najlepsze rozwiązania dotyczące wybierania Identyfikatora serii czasu

W tym artykule opisano klucza partycji Azure czas Series Insights w wersji zapoznawczej, identyfikator serii czasu i najlepszych rozwiązań dotyczących wybierania jednej.

## <a name="choose-a-time-series-id"></a>Wybieranie identyfikatora szeregów czasowych

Wybieranie Identyfikatora serii czasu przypomina wybór klucza partycji dla bazy danych. Jest to decyzja ważna, które powinny być podawane w czasie projektowania. Nie można zaktualizować istniejącego środowiska czasu Series Insights w wersji zapoznawczej można użyć różnych serii czasu, identyfikator. Oznacza to gdy środowisko zostało utworzone przy użyciu Identyfikatora serii czasu, zasady są niezmienne właściwość, której nie można zmienić.

> [!IMPORTANT]
> Identyfikator serii czasu jest rozróżniana wielkość liter i niezmienne (nie można zmienić po jego ustawieniu).

Mając to na uwadze wybierając z odpowiednim Identyfikatorem serii czasu ma krytyczne znaczenie. Po wybraniu Identyfikatora serii czasu, należy wziąć pod uwagę następujące te najlepsze rozwiązania:

* Wybierz nazwę właściwości, która ma szeroki zakres wartości, a nawet wzorców dostępu. Jest najlepszym rozwiązaniem jest zapewnienie klucza partycji przy użyciu wielu różnych wartości (na przykład, setek lub tysięcy). Dla wielu klientów będzie to coś jak identyfikator urządzenia lub SensorID w kod JSON.
* Identyfikator serii czasu powinna być unikatowa na poziomie liścia węzła usługi [modelu szeregów czasowych](./time-series-insights-update-tsm.md).
* Ciąg znaków nazwy właściwości identyfikator serii czasu mogą mieć maksymalnie 128 znaków i wartości właściwości identyfikator serii czasu może mieć maksymalnie 1024 znaki.
* Jeśli brakuje niektórych unikatowych wartości właściwości identyfikator serii czasu, będą one traktowane jako wartości null, które biorą udział w ograniczenie unikatowości.

Ponadto możesz wybrać maksymalnie *trzy* (3) właściwości klucza jako identyfikatora serii czasu

  > [!NOTE]
  > Twoje *trzy* (3) właściwości klucza muszą być ciągami.

W poniższych scenariuszach opisano, wybierając opcję więcej niż jedną właściwość klucza jako swój identyfikator serii czasu:  

### <a name="scenario-one"></a>Scenariusz jeden

* Masz flot starszych zasobów, z których każdy unikatowy klucz.
* Na przykład jeden floty jest unikatowo identyfikowana przez właściwość *deviceId* i inny unikatowy właściwości *objectId*. Żadna floty zawiera inne floty unikatowa właściwość. W tym przykładzie będzie wybierz dwa klucze, identyfikator urządzenia i identyfikator obiektu jako klucze unikatowe.
* Możemy zaakceptować wartości null i braku obecność właściwości w ładunku zdarzenia jest liczona jako `null` wartość. Jest to również odpowiedni sposób obsługi wysyłania danych do dwóch źródeł zdarzeń, gdzie dane w każdym źródło zdarzenia ma unikatowy identyfikator czas serii

### <a name="scenario-two"></a>Scenariusz — dwa

* Wymagane jest wiele właściwości, aby być unikatowe w obrębie tego samego jej zasobów. 
* Na przykład załóżmy, że jesteś producenta inteligentne tworzenie i wdrażanie czujników w każdym pokoju. W każdym pomieszczeniu, zazwyczaj mają takie same wartości *sensorId*, takich jak *sensor1*, *sensor2*, i *sensor3*.
* Ponadto budynku ma nakładające się produkcją i miejsca w witrynach we właściwości *flrRm*, które mają wartości takie jak *1a*, *2b*, *3a* i tak dalej.
* Na koniec ma właściwości, *lokalizacji*, który zawiera wartości takie jak *Redmond*, *Barcelona*, i *Tokio*. Aby utworzyć unikatowości, czy wyznaczyć następujących trzech właściwości jako klucze identyfikator serii czasu: *sensorId*, *flrRm*, i *lokalizacji*.

## <a name="next-steps"></a>Kolejne kroki

* Przeczytaj więcej na temat [modelowania danych](./time-series-insights-update-tsm.md).

* Plan usługi [środowisko usługi Azure Time Series Insights (wersja zapoznawcza)](./time-series-insights-update-plan.md).