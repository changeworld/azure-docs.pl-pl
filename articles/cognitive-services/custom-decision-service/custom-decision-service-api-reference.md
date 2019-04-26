---
title: Dokumentacja interfejsu API — Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Kompletny przewodnik interfejsu API usługi Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ms.openlocfilehash: be9966f5d8e8d94aa3f49aac91b35b105195b108
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60510957"
---
# <a name="api"></a>Interfejs API

Azure Custom Decision Service udostępnia dwa interfejsy API, które są wywoływane dla każdej decyzji: [Ranking interfejsu API](#ranking-api) wprowadzanie klasyfikacji akcje i [interfejsu API za wynagrodzeniem](#reward-api) służący do wypełniania wyjściowego nagrody. Ponadto należy podać [API zestawu akcji](#action-set-api-customer-provided) umożliwia określenie akcji do usługi Azure Custom Decision Service. W tym artykule opisano te trzy interfejsy API. Typowym scenariuszem jest używana poniżej do wyświetlenia, gdy usługi Custom Decision Service optymalizuje klasyfikacji artykułów.

## <a name="ranking-api"></a>Klasyfikacja interfejsu API

Interfejs API klasyfikacji używa standardowego [JSONP](https://en.wikipedia.org/wiki/JSONP)— wzorzec komunikacji stylu, aby zoptymalizować czas oczekiwania i pominąć [zasadami tego samego źródła](https://en.wikipedia.org/wiki/Same-origin_policy). Te ostatnie zabrania JavaScript pobieranie dane pochodzące spoza źródła strony.

Wstaw ten fragment kodu do głowy HTML pierwszej strony (w którym są wyświetlane spersonalizowane listę artykułów):

```html
// define the "callback function" to render UI
<script> function callback(data) { … } </script>
// "data" provides the ranking of URLs, see example below.

// call to Ranking API
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>?<parameters>" async></script>
// action set id is the name of the corresponding RSS/Atom feed.

// same call with multiple action sets:
// <script src="https://ds.microsoft.com/api/v2/<appId>/rank/<A1>/<A2>/.../<An>?<parameters>" async></script>
```

> [!IMPORTANT]
> Funkcja wywołania zwrotnego muszą być zdefiniowane przed wywołaniem interfejsu API klasyfikacji.

> [!TIP]
> Aby poprawić czas oczekiwania, interfejs API Klasyfikacja jest uwidaczniany za pomocą protokołu HTTP zamiast HTTPS, podobnie jak w `https://ds.microsoft.com/api/v2/<appId>/rank/*`.
> Jednak punkt końcowy HTTPS należy użyć, jeśli pierwsza strona jest obsługiwany za pośrednictwem protokołu HTTPS.

Gdy nie są używane parametry, odpowiedź HTTP z interfejsu API Klasyfikacja jest ciąg w formacie JSONP:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Następnie przeglądarka wykonuje ten ciąg jako wywołanie funkcji `callback()`.

Parametr do funkcji wywołania zwrotnego w powyższym przykładzie jest zgodny z następującym schematem:

- `ranking` udostępnia klasyfikacji adresów URL, który będzie wyświetlany.
- `eventId` jest używana wewnętrznie przez usługi Custom Decision Service odpowiadający tej klasyfikacji za pomocą odpowiedniego kliknięć.
- `appId` Umożliwia korzystanie z funkcji wywołania zwrotnego do rozróżnienia wielu aplikacji uruchomionych na tej samej strony sieci Web usługi Custom Decision Service.
- `actionSets` Wyświetla listę każdej akcji, używany w klasyfikacji wywołania interfejsu API, oraz sygnatura czasowa UTC ostatnie pomyślne odświeżenie. Usługa Custom Decision Service okresowo odświeża źródła danych z zestawu działań. Na przykład jeśli niektóre z zestawów działań nie są aktualne, funkcja wywołania zwrotnego może być konieczne wracać do ich klasyfikację domyślną.

> [!IMPORTANT]
> Ustawia określoną akcję są przetwarzane i prawdopodobnie przeczyścić w celu utworzenia klasyfikacji domyślnej artykułów. Domyślnie jest stosowana pobiera następnie zmieniana i zwracany w odpowiedzi HTTP. Domyślnie jest stosowana jest zdefiniowane w tym miejscu:
>
> - W ramach każdego zestawu akcji artykuły są oczyszczane 15 najnowsze artykuły (Jeśli nie zostały zwrócone więcej niż 15).
> - Jeśli określono wiele zestawów działań, są one scalane w takiej samej kolejności jak wywołania interfejsu API. Oryginalna kolejność artykuły są zachowywane w obrębie każdego zestawu akcji. Duplikaty są usuwane na rzecz wcześniejszych wersji.
> - Pierwszy `n` artykuły są przechowywane z scalonych listę artykułów, których `n=20` domyślnie.

### <a name="ranking-api-with-parameters"></a>Klasyfikacja interfejsu API z parametrami

Klasyfikacja API umożliwia następujące parametry:

- `details=1` i `details=2` Wstawia dodatkowe szczegóły dotyczące każdego artykułu na liście `ranking`.
- `limit=<n>` Określa maksymalny numer artykułów w klasyfikacji domyślnej. `n` musi mieć długość od `2` i `30` (lub inne jest obcinana do `2` lub `30`odpowiednio).
- `dnt=1` Wyłącza plików cookie użytkownika.

Parametry można łączyć w warstwie standardowa składni ciągów zapytania, na przykład `details=2&dnt=1`.

> [!IMPORTANT]
> Domyślne ustawienie w Europie powinien być `dnt=1` do momentu zaakceptowania transparent pliku cookie klienta. Należy również domyślne ustawienie dla witryn sieci Web przez osoby niepełnoletnie docelowych. Aby uzyskać więcej informacji, zobacz [warunki użytkowania](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

`details=1` Wstawia element każdego artykułu `guid`, jeśli jest obsługiwany przez interfejs API zestawu akcji. Odpowiedź HTTP:

```json
callback({
   "ranking":[{"id":"url1","details":[{"guid":"123"}]},
              {"id":"url2","details":[{"guid":"456"}]},
              {"id":"url3","details":[{"guid":"789"}]}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

`details=2` Elementu dodaje więcej szczegółów, które usługi Custom Decision Service może wyodrębnić z metatagi optymalizacji dla aparatów wyszukiwania artykułach [kodu cechowania](https://github.com/Microsoft/mwt-ds/tree/master/Crawl):

- `title` z `<meta property="og:title" content="..." />` lub `<meta property="twitter:title" content="..." />` lub `<title>...</title>`
- `description` z `<meta property="og:description" ... />` lub `<meta property="twitter:description" content="..." />` lub `<meta property="description" content="..." />`
- `image` Z `<meta property="og:image" content="..." />`
- `ds_id` Z `<meta name=”microsoft:ds_id” content="..." />`

Odpowiedź HTTP:

```json
callback({
   "ranking":[{"id":"url1","details":<details>},
              {"id":"url2","details":<details>},
              {"id":"url3","details":<details>}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

`<details>` Elementu:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>Nagrody interfejsu API

Niestandardowe kliknięć Decision Service używa tylko na najważniejszych miejsca. Każde kliknięcie jest interpretowany jako wynagrodzenie 1. Brak kliknięcie jest interpretowany jako wynagrodzenie 0. Kliknięcia są dopasowywane o odpowiedniej klasyfikacji przy użyciu identyfikatorów zdarzeń, które są generowane przez [Ranking API](#ranking-api) wywołania. Jeśli to konieczne, zdarzenia identyfikatory mogą być przekazywane za pomocą plików cookie sesji.

Aby obsłużyć kliknięć w górnym gniazda, umieść ten kod na pierwszej stronie:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

W tym miejscu `data` jest argumentem `callback()` działać zgodnie z wcześniejszym opisem. Za pomocą `data` kliknięcie kodu obsługującego wymaga niektórych opiekę. Przykład przedstawiono w tym [samouczek](custom-decision-service-tutorial-news.md#use-the-apis).

Tylko do celów testowych, można wywołać interfejsu API programu nagradzania za pośrednictwem [cURL](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

Oczekiwany efekt jest odpowiedź HTTP 200 (OK). Możesz zobaczyć nagradzania 1 dla tego zdarzenia w dzienniku, (Jeśli klucz konta magazynu platformy Azure została dostarczona w portalu).

## <a name="action-set-api-customer-provided"></a>Akcja Ustaw API (klienta)

Na wysokim poziomie interfejsu API zestawu akcji zwraca listę artykułów (Akcje). Każdego artykułu jest określony przez jego adres URL i (opcjonalnie) tytuł artykułu i Data publikacji. Można określić kilka akcji zestawy w portalu. Różne API zestawu akcji powinna służyć dla każdego zestawu działań jako odrębne adresu URL.

Interfejs API każdej akcji Ustaw można zaimplementować na dwa sposoby: jako źródło danych RSS lub źródła danych Atom. Albo jeden powinna być zgodna ze standardem i zwróć poprawny kod XML. Aby uzyskać RSS Oto przykład:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <guid>guid (could be a your internal database id)</guid>
      <pubDate>date</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Każdy najwyższego poziomu `<item>` element zawiera opis akcji:

- `<link>` jest wymagane i jest używany jako identyfikator działania.
- `<date>` jest ignorowana, jeśli jest on mniejszy niż lub równa 15 elementów. w przeciwnym razie jest obowiązkowe.
  - W przypadku więcej niż 15 elementów 15 ostatnich te są używane.
  - Muszą być odpowiednio w standardowym formacie dla źródła danych RSS lub Atom:
    - [RFC 822](https://tools.ietf.org/html/rfc822) dla RSS: na przykład `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) dla Atom: na przykład `"2016-12-19T16:39:57-08:00"`
- `<title>` jest opcjonalna i jest używany do generowania funkcji, które opisują tego artykułu.
- `<guid>` jest opcjonalny i przekazanych przez system do funkcji wywołania zwrotnego (Jeśli `?details` określono parametr w wywołaniu Ranking interfejsu API).

Inne elementy wewnątrz `<item>` są ignorowane.

Źródło danych używa wersji Atom, konwencje i tej samej składni XML.

> [!TIP]
> Jeśli system używa swój własny artykuł identyfikatorów, one mogą być przekazywane do funkcji wywołania zwrotnego, za pomocą `<guid>`.
