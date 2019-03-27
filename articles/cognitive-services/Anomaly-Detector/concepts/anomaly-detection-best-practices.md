---
title: Najlepsze rozwiązania w przypadku korzystania z interfejsu API wykrywanie anomalii
description: Dowiedz się więcej o najlepszych rozwiązaniach podczas wykrywania anomalii przy użyciu interfejsu API wykrywanie anomalii.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 467ac4e475a1c23e25b62c76cfbc959e7ed49465
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484045"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Najlepsze rozwiązania dotyczące korzystania z interfejsu API wykrywanie anomalii

Interfejs API usługi Wykrywanie anomalii jest usługa wykrywania anomalii bezstanowe. Niezawodność i wydajność jej wynik może mieć wpływ na:

* Jak przygotować danych szeregów czasowych.
* Parametry interfejsu API wykrywanie anomalii, które były używane.
* Liczba punktów danych w Twoim żądaniu interfejsu API. 

Aby poznać najlepsze rozwiązania dotyczące korzystania z interfejsu API, uzyskania najlepszych rezultatów dla swoich danych, należy użyć w tym artykule. 

## <a name="data-preparation"></a>Przygotowywanie danych

Interfejs API usługi Wykrywanie anomalii akceptuje szeregów czasowych dane sformatowane do obiektu żądania JSON. Szeregi czasowe może być dowolnym dane liczbowe, zapisywane wraz z upływem czasu, w kolejności sekwencyjnej. Windows danych szeregów czasowych może wysyłać do punktu końcowego interfejsu API wykrywanie anomalii, aby zwiększyć wydajność interfejsu API. Minimalna liczba punktów danych, który można wysłać to 12, a wartość maksymalna to 8640 punktów. 

Punktów danych wysłanych do interfejsu API wykrywanie anomalii musi mieć prawidłową sygnaturę czasową uniwersalnego czasu koordynowanego (UTC), a wartość liczbową. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

### <a name="missing-data-points"></a>Brak punktów danych

Brak punktów danych są wspólne w zestawach danych serii czasu równomierną dystrybucję, szczególnie te, które z zapewniającym dużą szczegółowość (interwał próbkowania małe. Adapterem, dane próbkowane co kilka minut). Brak mniej niż 10% oczekiwanej liczby punktów danych, nie powinny mieć negatywny wpływ na wyniki wykrywania. Należy wziąć pod uwagę wypełnianie luki w oparciu o jego cechy, takie jak podstawianie punktów danych w poprzednim okresie, interpolacji liniowej lub średniej ruchomej danych.

### <a name="aggregate-distributed-data"></a>Agregowanie danych rozproszonych

Interfejs API usługi Wykrywanie anomalii sprawdza się najlepiej w szeregu czasowego równomierną dystrybucję. Jeśli Twoje dane są losowo rozproszone, możesz powinien agregować je przez jednostkę czasu, takich jak na minutę, godzinową lub dzienną na przykład.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Wykrywanie anomalii w danych ze wzorcami sezonowych

Jeśli wiesz, że szeregami czasowymi ma wzorcu sezonowym, (jeden, który występuje w regularnych odstępach czasu), można zwiększyć dokładność i czasu odpowiedzi interfejsu API. 

Określanie `period` podczas konstruowania żądania JSON może zmniejszyć opóźnienie wykrywania anomalii nawet o 50%. `period` Jest liczba całkowita określająca punkty około ile danych szeregów czasowych zajmuje się do powtarzania wzorca. Na przykład miałby szeregów czasowych z jeden punkt danych na dzień `period` jako `7`, muszą szeregów czasowych z jednym punktem na godzinę (przy użyciu tego samego wzorca co tydzień) `period` z `7*24`. Jeśli masz pewności co do wzorców swoje dane, nie trzeba określenia tego parametru.

Aby uzyskać najlepsze wyniki, należy podać 4 `period`użytkownika, przez które punktu danych, a także drugą. Na przykład co godzinę danych za pomocą wzorca co tydzień zgodnie z powyższym opisem powinny dostarczyć 673 punktów danych w treści żądania (`7 * 24 * 4 + 1`).

### <a name="sampling-data-for-real-time-monitoring"></a>Dane z próbkowania dla monitorowania w czasie rzeczywistym

Jeśli dane przesyłania strumieniowego są próbkowane tak, w krótkich odstępach czasu (w sekundach lub minutach), wysyłając zalecana liczba punktów danych może przekroczyć API wykrywanie anomalii maksymalna liczba dozwolonych (8640 punkty danych). Jeśli dane pokazuje stabilny wzorcu sezonowym, należy wziąć pod uwagę wysyłania próbkę danych szeregów czasowych w większych odstępach czasu, takich jak godziny. Próbkowanie danych w ten sposób może również znacznie poprawić czas odpowiedzi interfejsu API. 

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API usługi Wykrywanie anomalii?](../overview.md)
* [Szybki start: Wykrywanie anomalii w danych szeregów czasowych za pomocą interfejsu API REST wykrywanie anomalii](../quickstarts/detect-data-anomalies-csharp.md)
