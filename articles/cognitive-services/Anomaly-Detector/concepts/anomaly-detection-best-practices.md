---
title: Najlepsze rozwiązania dotyczące interfejsu API narzędzia do wykrywania anomalii
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o najlepszych rozwiązaniach podczas wykrywania anomalii za pomocą interfejsu API detektora anomalii.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67721625"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Najważniejsze wskazówki dotyczące korzystania z interfejsu API detektora anomalii

Interfejs API detektora anomalii jest usługą wykrywania anomalii bezstanowych. Na dokładność i wydajność jego wyników mogą mieć wpływ:

* Sposób przygotowania danych szeregów czasowych.
* Parametry interfejsu API detektora anomalii, które zostały użyte.
* Liczba punktów danych w żądaniu interfejsu API. 

Użyj tego artykułu, aby dowiedzieć się o najlepszych praktykach dotyczących korzystania z interfejsu API, uzyskując najlepsze wyniki dla danych. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Kiedy używać wykrywania anomalii punktu partii (w całości) lub najnowszej (ostatniej)

Punkt końcowy wykrywania wsadowego detektora anomalii umożliwia wykrywanie anomalii za pośrednictwem danych serii cały czas. W tym trybie wykrywania tworzony jest pojedynczy model statystyczny i stosowany do każdego punktu w zestawie danych. Jeśli szeregi czasowe mają następujące cechy, zaleca się użycie wykrywania partii do podglądu danych w jednym wywołaniu interfejsu API.

* Sezonowe szeregi czasowe z sporadycznymi anomaliami.
* Płaskie serie czasowe trendu, z okazjonalnymi skokami/spadkami. 

Nie zaleca się używania wykrywania anomalii partii do monitorowania danych w czasie rzeczywistym lub używania go w danych szeregów czasowych, które nie mają powyższych cech. 

* Wykrywanie partii tworzy i stosuje tylko jeden model, wykrywanie dla każdego punktu odbywa się w kontekście całej serii. Jeśli trendy danych szeregów czasowych w górę i w dół bez sezonowości, niektóre punkty zmian (spadki i skoki w danych) mogą zostać pominięte przez model. Podobnie niektóre punkty zmian, które są mniej istotne niż te w późniejszym zestawie danych, mogą nie być liczone jako wystarczająco istotne, aby można je było włączyć do modelu.

* Wykrywanie partii jest wolniejsze niż wykrywanie stanu anomalii najnowszego punktu podczas monitorowania danych w czasie rzeczywistym, ze względu na liczbę analizowanych punktów.

W przypadku monitorowania danych w czasie rzeczywistym zaleca się wykrywanie stanu anomalii tylko najnowszego punktu danych. Dzięki ciągłego stosowania najnowszego wykrywania punktów, monitorowanie danych strumieniowych można wykonać wydajniej i dokładniej.

W poniższym przykładzie opisano wpływ tych trybów wykrywania na wydajność. Pierwsze zdjęcie przedstawia wynik ciągłego wykrywania stanu anomalii ostatniego punktu wzdłuż 28 wcześniej widzianych punktów danych. Czerwone punkty są anomalie.

![Obraz przedstawiający wykrywanie anomalii przy użyciu najnowszego punktu](../media/last.png)

Poniżej znajduje się ten sam zestaw danych przy użyciu wykrywania anomalii partii. Model zbudowany dla operacji zignorował kilka anomalii, oznaczonych prostokątami.

![Obraz przedstawiający wykrywanie anomalii przy użyciu metody wsadowej](../media/entire.png)

## <a name="data-preparation"></a>Przygotowywanie danych

Interfejs API detektora anomalii akceptuje dane szeregów czasowych sformatowane w obiekcie żądania JSON. Szeregi czasowe mogą być dowolnymi danymi liczbowymi zarejestrowanymi w czasie w kolejności sekwencyjnej. Można wysłać okna danych szeregów czasowych do punktu końcowego interfejsu API detektora anomalii, aby poprawić wydajność interfejsu API. Minimalna liczba punktów danych, które można wysłać, wynosi 12, a maksymalna to 8640 punktów. [Ziarnistość](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) jest definiowana jako szybkość, w jakim dane są próbkowane. 

Punkty danych wysyłane do interfejsu API detektora anomalii muszą mieć prawidłowy sygnatura czasowa skoordynowanego czasu uniwersalnego (UTC) i wartość liczbową. 

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

Jeśli dane są próbkowanie w niestandardowym przedziale czasu, `customInterval` można określić go przez dodanie atrybutu w żądaniu. Na przykład jeśli seria jest próbkowana co 5 minut, można dodać następujące elementy do żądania JSON:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Brakujące punkty danych

Brakujące punkty danych są powszechne w równomiernie rozproszonych zestawach danych szeregów czasowych, zwłaszcza tych o drobnym szczegółowości (mały interwał próbkowania. Na przykład dane próbkowania co kilka minut). Brak mniej niż 10% oczekiwanej liczby punktów w danych nie powinien mieć negatywnego wpływu na wyniki wykrywania. Rozważ wypełnienie luk w danych na podstawie ich cech, takich jak zastąpienie punktów danych z wcześniejszego okresu, interpolacja liniowa lub średnia ruchoma.

### <a name="aggregate-distributed-data"></a>Agregowanie danych rozproszonych

Interfejs API detektora anomalii działa najlepiej w równomiernie rozproszonych szeregach czasowych. Jeśli dane są dystrybuowane losowo, należy zagregować je według jednostki czasu, na przykład na minutę, co godzinę lub codziennie.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Wykrywanie anomalii na danych z sezonowymi wzorcami

Jeśli wiesz, że dane szeregów czasowych ma wzorzec sezonowy (taki, który występuje w regularnych odstępach czasu), można poprawić dokładność i czas odpowiedzi interfejsu API. 

Określenie `period` podczas konstruowania żądania JSON może zmniejszyć opóźnienie wykrywania anomalii nawet o 50%. Jest `period` całkowitej liczby, która określa mniej więcej, ile punktów danych szeregów czasowych trwa do powtórzenia wzorca. Na przykład szeregi czasowe z jednym `period` punktem `7`danych dziennie będą miały as , a szeregi czasowe `period` `7*24`z jednym punktem na godzinę (z tym samym wzorcem tygodniowym) miałyby jeden schemat . Jeśli nie masz pewności co do wzorców danych, nie musisz określać tego parametru.

Aby uzyskać najlepsze wyniki, podaj punkt danych o wartości 4 `period`000 000 000 000 000 000 000 000 000 000 000 Na przykład dane godzinowe z tygodniowym wzorcem, jak opisano powyżej, powinny`7 * 24 * 4 + 1`dostarczyć 673 punktów danych w treści żądania ( ).

### <a name="sampling-data-for-real-time-monitoring"></a>Dane próbkowania do monitorowania w czasie rzeczywistym

Jeśli dane przesyłania strumieniowego są próbkowane w krótkim odstępie czasu (na przykład sekund lub minut), wysłanie zalecanej liczby punktów danych może przekroczyć maksymalną dozwoloną liczbę interfejsu API detektora anomalii (8640 punktów danych). Jeśli dane pokazują stabilny wzorzec sezonowy, należy rozważyć wysłanie próbki danych szeregów czasowych w większych odstępach czasu, takich jak godziny. Próbkowanie danych w ten sposób może również znacznie poprawić czas odpowiedzi interfejsu API. 

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API narzędzia do wykrywania anomalii?](../overview.md)
* [Szybki start: wykrywanie anomalii w danych szeregów czasowych przy użyciu interfejsu API REST detektora anomalii](../quickstarts/detect-data-anomalies-csharp.md)
