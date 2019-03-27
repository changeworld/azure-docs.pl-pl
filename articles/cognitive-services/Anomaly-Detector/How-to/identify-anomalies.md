---
title: Jak używać interfejsu API wykrywanie anomalii w danych szeregów czasowych
description: Dowiedz się, jak wykrywać anomalie w danych jako zadania wsadowego lub na danych przesyłanych strumieniowo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 63ede8fe90d5c19c2473ffb315bf6096599ffb9c
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473331"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>Instrukcje: Za pomocą interfejsu API wykrywanie anomalii w danych szeregów czasowych  

[Interfejsu API wykrywanie anomalii](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) udostępnia dwie metody wykrywania anomalii. Możesz albo wykrywać anomalie jako zadania wsadowego, przez cały Twoje czasy serii lub wygenerowane dane oparte na wykrywaniu anomalii stan najnowszego punktu danych. Model wykrywania zwraca wyniki anomalii wraz z każdego punktu danych oczekiwanej wartości i anomalii górne i dolne granice wykrywania. te wartości można użyć do wizualizacji zakres wartości normalnych i anomalii w danych.

## <a name="anomaly-detection-modes"></a>Tryby wykrywania anomalii 

Interfejs API usługi Wykrywanie anomalii zapewnia tryby wykrywania: usługi batch i przesyłania strumieniowego.

> [!NOTE]
> Następujące żądanie, które adresy URL muszą być połączone za pomocą odpowiednich punktów końcowych dla Twojej subskrypcji. Na przykład: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>Wykrywanie usługi Batch

Do wykrywania anomalii w zadaniu wsadowym punktów danych w danym okresie, należy użyć następującego identyfikatora URI żądania z danych szeregów czasowych: 

`/timeseries/entire/detect`. 

Jednocześnie wysyłając szeregami czasowymi, interfejs API będzie wygenerować model przy użyciu całej serii i analizowanie każdego punktu danych z nim.  

### <a name="streaming-detection"></a>Wykrywanie przesyłania strumieniowego

Stale wykrywania anomalii w danych przesyłanych strumieniowo, należy użyć następującego identyfikatora URI żądania z Twojego najnowszego punktu danych: 

`/timeseries/last/detect'`. 

Wysyłając nowe punkty danych, zgodnie z ich generowania, możesz monitorować dane w czasie rzeczywistym. Model zostanie wygenerowany za pomocą punktów danych, które wysyłasz i interfejs API Określa, czy ostatniego punktu w serii czasu jest anomalii.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>Dostosowywanie granice wykrywania anomalii w dolnym i górnym numerem

Domyślnie górne i dolne granice wykrywania anomalii są obliczane przy użyciu `expectedValue`, `upperMargin`, i `lowerMargin`. Jeśli potrzebujesz różnych granice, firma Microsoft zaleca stosowanie `marginScale` do `upperMargin` lub `lowerMargin`. Granice byłoby obliczone w następujący sposób:

|Granic  |Obliczenia  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

Poniższe przykłady pokazują wynik interfejsu API wykrywanie anomalii w różnej wagi.

### <a name="example-with-sensitivity-at-99"></a>Przykład: czułości w 99

![Domyślna ważność](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>Przykład: wrażliwości na 95

![Czułość 99](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>Przykład: wrażliwości na 85

![Czułość 85](../media/sensitivity_85.png)

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API usługi Wykrywanie anomalii?](../overview.md)
* [Szybki start: Wykrywanie anomalii w danych szeregów czasowych za pomocą interfejsu API REST wykrywanie anomalii](../quickstarts/detect-data-anomalies-csharp.md)