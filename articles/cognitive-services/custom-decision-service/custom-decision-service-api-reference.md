---
title: Dokumentacja interfejsu API — Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Kompletny przewodnik po interfejsie API dla Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 4f263e3b57103174f0084ab3d25430d8c47359fd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707308"
---
# <a name="api"></a>interfejs API

Usługa Azure Custom Decision Service udostępnia dwa interfejsy API, które są wywoływane dla każdej decyzji: [interfejs API klasyfikacji](#ranking-api) do wprowadzania rankingu akcji i [interfejsu API nagradzania](#reward-api) w celu wygenerowania nagrody. Ponadto należy podać [interfejs API zestawu akcji](#action-set-api-customer-provided) , aby określić akcje na platformie Azure Custom Decision Service. W tym artykule opisano te trzy interfejsy API. Typowy scenariusz jest używany poniżej, aby pokazać, kiedy Custom Decision Service optymalizuje klasyfikację artykułów.

## <a name="ranking-api"></a>Interfejs API klasyfikowania

Interfejs API klasyfikacji używa standardowego wzorca komunikacji [JSONP](https://en.wikipedia.org/wiki/JSONP)do optymalizowania opóźnień i pomijania [zasad tego samego źródła](https://en.wikipedia.org/wiki/Same-origin_policy). To drugie zabrania skryptom JavaScript pobieranie danych spoza źródła strony.

Wstaw ten fragment kodu do nagłówka HTML strony frontonu (gdzie jest wyświetlana spersonalizowana lista artykułów):

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
> Funkcja wywołania zwrotnego musi być zdefiniowana przed wywołaniem interfejsu API klasyfikacji.

> [!TIP]
> Aby zwiększyć opóźnienie, interfejs API klasyfikowania jest udostępniany za pośrednictwem protokołu HTTP, `https://ds.microsoft.com/api/v2/<appId>/rank/*`a nie https, jak w przypadku programu.
> Jednak punkt końcowy HTTPS musi być używany, jeśli strona frontonu jest obsługiwana za pośrednictwem protokołu HTTPS.

Gdy parametry nie są używane, odpowiedź HTTP z interfejsu API klasyfikowania jest ciągiem sformatowanym przez JSONP:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Następnie przeglądarka wykonuje ten ciąg jako wywołanie funkcji `callback()`.

Parametr funkcji wywołania zwrotnego w poprzednim przykładzie ma następujący schemat:

- `ranking`zawiera klasyfikację adresów URL, które mają być wyświetlane.
- `eventId`jest używany wewnętrznie przez Custom Decision Service w celu dopasowania do tej klasyfikacji przy użyciu odpowiednich kliknięć.
- `appId`zezwala funkcji wywołania zwrotnego na rozróżnienie między wieloma aplikacjami Custom Decision Service uruchomionymi na tej samej stronie sieci Web.
- `actionSets`Wyświetla listę wszystkich zestawów akcji używanych w wywołaniu interfejsu API klasyfikacji wraz z sygnaturą czasową UTC ostatniego pomyślnego odświeżenia. Custom Decision Service okresowo odświeża źródła zestawu akcji. Na przykład jeśli niektóre z zestawów akcji nie są aktualne, funkcja wywołania zwrotnego może wymagać powrotu do ich domyślnej klasyfikacji.

> [!IMPORTANT]
> Określone zestawy akcji są przetwarzane i prawdopodobnie oczyszczone, aby można było utworzyć domyślną klasyfikację artykułów. Klasyfikacja domyślna jest następnie zmieniana i zwracana w odpowiedzi HTTP. Klasyfikacja domyślna jest definiowana tutaj:
>
> - W ramach każdego zestawu akcji artykuły są oczyszczane do 15 najnowszych artykułów (jeśli zwracane są więcej niż 15).
> - Jeśli określono wiele zestawów akcji, są one scalane w takiej samej kolejności jak w wywołaniu interfejsu API. Oryginalna kolejność artykułów jest zachowywana w ramach każdego zestawu akcji. Duplikaty są usuwane na korzyść wcześniejszych kopii.
> - Pierwsze `n` artykuły są przechowywane na podstawie Scalonej listy artykułów, gdzie `n=20` domyślnie.

### <a name="ranking-api-with-parameters"></a>Interfejs API klasyfikacji z parametrami

Interfejs API klasyfikowania umożliwia następujące parametry:

- `details=1`i `details=2` Wstawia dodatkowe szczegółowe informacje o każdym artykule wymienionym w `ranking`temacie.
- `limit=<n>`Określa maksymalną liczbę artykułów w klasyfikacji domyślnej. `n`musi należeć `2` do `30` zakresu od do (lub w przeciwnym `2` razie `30`jest obcinany do lub, odpowiednio).
- `dnt=1`wyłącza pliki cookie użytkownika.

Parametry można łączyć w standardowym, składni ciągu zapytania, na przykład `details=2&dnt=1`.

> [!IMPORTANT]
> Ustawieniem domyślnym w Europie powinna być `dnt=1` do momentu, gdy klient wyrazi zgodę na baner plików cookie. Powinno to być również ustawienie domyślne dla witryn sieci Web, które są pomocnicze. Aby uzyskać więcej informacji, zobacz [warunki użytkowania](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

Element wstawia każdy `guid`artykuł, jeśli jest obsługiwany przez interfejs API zestawu akcji. `details=1` Odpowiedź HTTP:

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

Element dodaje więcej szczegółów, które Custom Decision Service mogą wyodrębnić z artykułów "cechowania" w kodzie Meta tagów: [](https://github.com/Microsoft/mwt-ds/tree/master/Crawl) `details=2`

- `title`z `<meta property="og:title" content="..." />` lub `<meta property="twitter:title" content="..." />` lub`<title>...</title>`
- `description`z `<meta property="og:description" ... />` lub `<meta property="twitter:description" content="..." />` lub`<meta property="description" content="..." />`
- `image`wniosek`<meta property="og:image" content="..." />`
- `ds_id`wniosek`<meta name=”microsoft:ds_id” content="..." />`

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

`<details>` Element:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>Interfejs API nagradzania

Custom Decision Service używa kliknięć tylko w górnym gnieździe. Każde kliknięcie jest interpretowane jako wynagrodzenie 1. Brak kliknięcia jest interpretowany jako nagrody o wartości 0. Kliknięcia są zgodne z odpowiednimi klasyfikacjami przy użyciu identyfikatorów zdarzeń, które są generowane przez wywołanie [interfejsu API klasyfikacji](#ranking-api) . W razie konieczności identyfikatory zdarzeń mogą być przesyłane za pośrednictwem plików cookie sesji.

Aby obsłużyć kliknij górne miejsce, Umieść ten kod na stronie frontonu:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Oto `data` argument`callback()` funkcji, jak opisano wcześniej. Użycie `data` w kodzie obsługi kliknij wymaganie. W tym samouczku przedstawiono przykład [](custom-decision-service-tutorial-news.md#use-the-apis).

Do celów testowych można wywołać interfejs API nagradzania [za pomocą zwinięcia](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

Oczekiwany efekt to odpowiedź HTTP 200 (OK). W dzienniku można zobaczyć wynagrodzenie wartości 1 (Jeśli klucz konta usługi Azure Storage został dostarczony w portalu).

## <a name="action-set-api-customer-provided"></a>Interfejs API zestawu akcji (dostarczony klient)

Na wysokim poziomie interfejs API zestawu akcji zwraca listę artykułów (akcji). Każdy artykuł jest określany za pomocą adresu URL i (opcjonalnie) tytułu artykułu oraz daty publikacji. Możesz określić kilka zestawów akcji w portalu. Dla każdego zestawu akcji należy użyć innego interfejsu API zestawu akcji, jako odrębnego adresu URL.

Każdy interfejs API zestawu akcji można zaimplementować na dwa sposoby: jako źródło danych RSS lub źródło danych Atom. Jeden z nich powinien być zgodny ze standardem i zwracać prawidłowy kod XML. Oto przykład dla funkcji RSS:

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

Każdy element najwyższego poziomu `<item>` opisuje akcję:

- `<link>`jest obowiązkowy i jest używany jako identyfikator akcji.
- `<date>`jest ignorowany, jeśli jest mniejsze niż lub równe 15 elementów; w przeciwnym razie jest to wymagane.
  - Jeśli jest więcej niż 15 elementów, używane są 15 najnowszych z nich.
  - Musi być odpowiednio w standardowym formacie RSS lub Atom:
    - [RFC 822](https://tools.ietf.org/html/rfc822) dla RSS: na przykład`"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) for Atom: na przykład`"2016-12-19T16:39:57-08:00"`
- `<title>`jest opcjonalne i służy do generowania funkcji opisujących artykuł.
- `<guid>`jest opcjonalne i przesyłane przez system do funkcji wywołania zwrotnego (Jeśli `?details` parametr jest określony w wywołaniu interfejsu API klasyfikacji).

Inne elementy wewnątrz elementu `<item>` są ignorowane.

Wersja kanału informacyjnego Atom używa tych samych składni i Konwencji XML.

> [!TIP]
> Jeśli system używa własnych identyfikatorów artykułów, mogą one być przesyłane do funkcji wywołania zwrotnego przy użyciu `<guid>`.
