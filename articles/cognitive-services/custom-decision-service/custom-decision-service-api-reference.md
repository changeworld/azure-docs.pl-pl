---
title: API — kognitywnych usług platformy Azure | Dokumentacja firmy Microsoft
description: Pełne i przyjazny dla użytkownika interfejsu API przewodnik Azure niestandardowe decyzji, oparte na chmurze interfejsu API usługi kontekstowe podejmowania decyzji, które wyostrza z doświadczeniem.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/11/2018
ms.author: slivkins
ms.reviewer: marcozo, alekh
ms.openlocfilehash: 403b17e33394016a07a7b33ba1bcbfe6afdcc05b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349252"
---
# <a name="api"></a>Interfejs API

Azure usługa decyzji niestandardowe udostępnia dwa interfejsy API, które są wywoływane dla każdej decyzji: [Klasyfikacja interfejsu API](#ranking-api) jako danych wejściowych klasyfikację akcje i [API opłatą](#reward-api) do wyjściowego nagrody. Ponadto podaj [akcji Ustaw API](#action-set-api-customer-provided) umożliwia określenie akcji w usłudze Azure niestandardowe decyzji. W tym artykule omówiono te trzy interfejsów API. Typowy scenariusz jest używana poniżej do wyświetlenia, gdy usługa decyzji niestandardowe optymalizuje klasyfikacji artykułów.

## <a name="ranking-api"></a>Klasyfikacja interfejsu API

Interfejs API klasyfikacji wykorzystuje standardowe [JSONP](https://en.wikipedia.org/wiki/JSONP)— styl komunikacji wzorzec do optymalizacji opóźnień i obejście [zasad samego pochodzenia](https://en.wikipedia.org/wiki/Same-origin_policy). Drugie zabrania JavaScript dane pochodzące spoza pochodzenia strony pobierania.

Wstaw następujący fragment kodu do head HTML strony front (w którym są wyświetlane spersonalizowane listę artykułów):

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
> Aby zwiększyć czas oczekiwania, Klasyfikacja interfejsu API jest narażony za pośrednictwem protokołu HTTP zamiast HTTPS, podobnie jak w `http://ds.microsoft.com/api/v2/<appId>/rank/*`.
> Jednak punkt końcowy HTTPS należy użyć, jeśli pierwsza strona jest obsługiwany za pośrednictwem protokołu HTTPS.

Gdy nie są używane parametry, odpowiedź HTTP z interfejsu API Klasyfikacja jest ciąg sformatowany JSONP:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Przeglądarka następnie wykonuje tego ciągu jako wywołanie `callback()` funkcji.

Parametr funkcji wywołania zwrotnego w poprzednim przykładzie ma następującego schematu:

- `ranking` udostępnia klasyfikacji adresów URL, który będzie wyświetlany.
- `eventId` jest używana wewnętrznie przez usługę decyzji niestandardowe do dopasowania tej klasyfikacji odpowiedniego kliknięć.
- `appId` zezwala na działanie funkcji wywołania zwrotnego w celu rozróżnienia wielu aplikacji niestandardowych decyzji usługa jest uruchomiona w tej samej strony sieci Web.
- `actionSets` Wyświetla listę każda akcja używany w wywołaniu interfejsu API z klasyfikacji, wraz z sygnatury czasowej UTC ostatnie udane odświeżanie. Usługa niestandardowa decyzji okresowo odświeża źródła zestaw akcji. Na przykład jeśli niektóre zestawy akcji nie są aktualne, funkcja wywołania zwrotnego może być konieczne może wrócić do ich klasyfikację domyślną.

> [!IMPORTANT]
> Ustawia określoną akcję są przetwarzane i prawdopodobnie przeczyścić tworzą domyślnie jest stosowana artykułów. Domyślnie jest stosowana następnie pobiera kolejności i zwracany w odpowiedzi HTTP. Domyślnie jest stosowana jest zdefiniowany w tym miejscu:
>
> - W każdym z nich akcji artykułów są usuwane w artykułach najnowszych 15 (Jeśli nie zostały zwrócone więcej niż 15).
> - Jeśli określono wiele zestawów akcji, są one scalane w takiej samej kolejności jak wywołanie interfejsu API. Oryginalny porządkowania artykułów są zachowywane w obrębie każdej akcji. Duplikaty są usuwane na rzecz wcześniejszych wersji.
> - Pierwszy `n` przechowywania artykułów z scalone listy artykułów, gdzie `n=20` domyślnie.

### <a name="ranking-api-with-parameters"></a>Klasyfikacja interfejsu API z parametrami

Klasyfikacja API umożliwia następujące parametry:

- `details=1` i `details=2` Wstawia dodatkowe szczegóły dotyczące każdego artykułu na liście `ranking`.
- `limit=<n>` Określa maksymalny numer artykułów w klasyfikację domyślną. `n` musi należeć do zakresu od `2` i `30` (lub w przeciwnym razie jest obcinana do `2` lub `30`odpowiednio).
- `dnt=1` Wyłącza plików cookie użytkownika.

Parametry można łączyć w standardowe, składnia ciągu kwerendy, na przykład `details=2&dnt=1`.

> [!IMPORTANT]
> Domyślne ustawienie w Europie powinien być `dnt=1` do momentu klient zgadza się na transparencie pliku cookie. Należy również ustawieniem domyślnym dla witryn sieci Web, które osoby nieletnie docelowych. Aby uzyskać więcej informacji, zobacz [warunki użytkowania](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

`details=1` Każdego artykułu wstawia element `guid`, jeśli jest obsługiwany przez interfejs API ustawić akcji. Odpowiedź HTTP:

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

`details=2` Element dodaje więcej szczegółów, które usługi decyzji niestandardowy może wyodrębnić z metatagi optymalizacji dla aparatów wyszukiwania artykułów [kod featurization](https://github.com/Microsoft/mwt-ds/tree/master/Crawl):

- `title` z `<meta property="og:title" content="..." />` lub `<meta property="twitter:title" content="..." />` lub `<title>...</title>`
- `description` z `<meta property="og:description" ... />` lub `<meta property="twitter:description" content="..." />` lub `<meta property="description" content="..." />`
- `image` z `<meta property="og:image" content="..." />`
- `ds_id` z `<meta name=”microsoft:ds_id” content="..." />`

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

## <a name="reward-api"></a>Wynagrodzenie interfejsu API

Niestandardowe kliknięć używa usługi decyzji tylko na najwyższym miejsca. Każde kliknięcie jest interpretowany jako wynagrodzenia 1. Brak kliknięcie jest interpretowany jako wynagrodzenia 0. Kliknięć są dopasowywane o odpowiedniej klasyfikacji przy użyciu identyfikatorów zdarzeń, które są generowane przez [Klasyfikacja interfejsu API](#ranking-api) wywołania. W razie potrzeby zdarzeń identyfikatory mogą być przekazywane za pośrednictwem plików cookie sesji.

Do obsługi kliknij na najwyższym miejsca, umieść ten kod na stronie front:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

W tym miejscu `data` jest argumentem `callback()` funkcji, jak opisano wcześniej. Przy użyciu `data` w kliknij kod obsługi wymaga niektórych szczególną uwagę. Przykład znajduje się w tym [samouczek](custom-decision-service-tutorial-news.md#use-the-apis).

Tylko do celów testowych, można wywołać interfejsu API opłatą za pośrednictwem [cURL](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

Oczekiwano efekt jest odpowiedź HTTP 200 (OK). Można wyświetlić nagrody 1 dla tego zdarzenia w dzienniku (klucz konta magazynu platformy Azure została dostarczona w portalu).

## <a name="action-set-api-customer-provided"></a>Akcja Ustaw API (dostarczanych przez klienta)

Na wysokim poziomie API ustawić akcji zwraca listę artykułów (Akcje). Każdego artykułu jest określony przez adres URL (opcjonalnie) tytuł artykułu i Data publikacji. Można określić kilka akcji ustawia w portalu. Dla każdego zestawu działań jako odrębne adres URL stosuje się różne API ustawić akcji.

Można zaimplementować interfejsu API każdej akcji Ustaw na dwa sposoby: jako źródło danych RSS lub źródła danych Atom. Albo jeden powinny być zgodne ze standardem i zwracać poprawny XML. Dla danych RSS Oto przykład:

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

- `<link>` jest wymagana i jest używany jako identyfikator akcji.
- `<date>` jest ignorowana, jeśli jest mniejsza niż lub równa 15 elementów. w przeciwnym razie jest to konieczne.
  - Jeśli istnieje więcej niż 15 elementów, 15 najnowszych są używane.
  - Musi być odpowiednio w standardowym formacie dla źródła danych RSS lub Atom:
    - [RFC 822](https://tools.ietf.org/html/rfc822) dla RSS: na przykład `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) dla Atom: na przykład `"2016-12-19T16:39:57-08:00"`
- `<title>` jest opcjonalna i służy do generowania funkcje, które opisują tego artykułu.
- `<guid>` jest opcjonalny i przekazany za pośrednictwem funkcji wywołania zwrotnego systemu (Jeśli `?details` określony parametr w wywołaniu Klasyfikacja interfejsu API).

Inne elementy wewnątrz `<item>` są ignorowane.

Źródło danych używa wersji Atom, sama składnia XML i Konwencji.

> [!TIP]
> Jeśli system używa własnego artykułu identyfikatorów, ich można przekazać do funkcji wywołania zwrotnego przez przy użyciu `<guid>`.