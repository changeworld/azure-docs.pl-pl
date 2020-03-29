---
title: Jak korzystać z interfejsu API detektora anomalii na danych szeregów czasowych
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wykrywać anomalie w danych jako partia lub na danych przesyłania strumieniowego.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71840224"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Jak: Korzystanie z interfejsu API detektora anomalii w danych szeregów czasowych  

[Interfejs API detektora anomalii](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) udostępnia dwie metody wykrywania anomalii. Anomalie można wykryć jako partię w całej serii czasów lub w miarę generowania danych przez wykrycie stanu anomalii najnowszego punktu danych. Model wykrywania zwraca wyniki anomalii wraz z oczekiwaną wartością każdego punktu danych oraz górne i dolne granice wykrywania anomalii. można użyć tych wartości do wizualizacji zakresu wartości normalnych i anomalii w danych.

## <a name="anomaly-detection-modes"></a>Tryby wykrywania anomalii 

Interfejs API detektora anomalii udostępnia tryby wykrywania: wsadowe i przesyłane strumieniowo.

> [!NOTE]
> Następujące adresy URL żądań muszą być połączone z odpowiednim punktem końcowym dla subskrypcji. Na przykład: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Wykrywanie partii

Aby wykryć anomalie w całej partii punktów danych w danym zakresie czasu, należy użyć następującego identyfikatora URI żądania z danymi szeregów czasowych: 

`/timeseries/entire/detect`. 

Wysyłając dane szeregów czasowych jednocześnie, interfejs API wygeneruje model przy użyciu całej serii i analizować każdy punkt danych z nim.  

### <a name="streaming-detection"></a>Wykrywanie przesyłania strumieniowego

Aby stale wykrywać anomalie danych strumieniowych, użyj następującego identyfikatora URI żądania z najnowszym punktem danych: 

`/timeseries/last/detect'`. 

Wysyłając nowe punkty danych podczas ich generowania, możesz monitorować swoje dane w czasie rzeczywistym. Model zostanie wygenerowany z wysyłanych punktów danych, a interfejs API określi, czy najnowszy punkt w szeregach czasowych jest anomalią.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Dostosowywanie dolnych i górnych granic wykrywania anomalii

Domyślnie górne i dolne granice wykrywania `expectedValue`anomalii są obliczane za pomocą , `upperMargin`i `lowerMargin`. Jeśli potrzebujesz różnych granic, zalecamy `marginScale` zastosowanie `upperMargin` `lowerMargin`do lub . Granice będą obliczane w następujący sposób:

|Granicy  |Obliczenia  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Poniższe przykłady pokazują wynik interfejsu API detektora anomalii przy różnych czułościach.

### <a name="example-with-sensitivity-at-99"></a>Przykład z czułością przy 99

![Domyślna czułość](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Przykład z czułością przy 95

![99 Czułość](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Przykład z czułością przy 85

![85 Czułość](../media/sensitivity_85.png)

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API narzędzia do wykrywania anomalii?](../overview.md)
* [Szybki start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST detektora anomalii](../quickstarts/detect-data-anomalies-csharp.md)
