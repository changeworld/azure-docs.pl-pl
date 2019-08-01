---
title: 'Samouczek: Personalizacja artykułów — Custom Decision Service'
titlesuffix: Azure Cognitive Services
description: Samouczek dotyczący personalizacji artykułów na potrzeby kontekstowego podejmowania decyzji.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: f7eafed9db25fba904d98ddea652671dc45aa01d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707220"
---
# <a name="tutorial-article-personalization-for-contextual-decision-making"></a>Samouczek: Personalizacja artykułów na potrzeby kontekstowego podejmowania decyzji

Ten samouczek dotyczy personalizacji artykułów wyświetlanych na stronie głównej witryny internetowej. Usługa Custom Decision Service może mieć na przykład wpływ na *wiele* list artykułów na stronie głównej. Strona może na przykład znajdować się w witrynie wiadomości dotyczących jedynie polityki i sportu. Pokazuje ona trzy uporządkowane listy artykułów: polityka, sport i najnowsze.

## <a name="applications-and-action-sets"></a>Aplikacje i zestawy akcji

Poniżej opisano sposób dopasowania scenariusza do tej struktury. Wyobraźmy sobie trzy aplikacje, po jednej dla każdej optymalizowanej listy: app-politics, app-sports i app-recent. Aby określić kandydujące artykuły dla każdej z tych aplikacji, mamy dwa zestawy akcji: jeden dla polityki i jeden dla sportu. Zestaw akcji dla aplikacji app-recent jest tworzony automatycznie jako połączenie dwóch pozostałych zestawów.

> [!TIP]
> Zestawy akcji można udostępniać między aplikacjami w usłudze Custom Decision Service.

## <a name="prepare-action-set-feeds"></a>Przygotowywanie źródeł danych zestawu akcji

Usługa Custom Decision Service używa zestawów akcji za pośrednictwem źródeł danych RSS lub Atom udostępnianych przez klienta. Należy podać dwa źródła danych: jedno dla polityki i jedno dla sportu. Załóżmy, że są one udostępniane z adresu `http://www.domain.com/feeds/<feed-name>`.

Każde źródło danych zawiera listę artykułów. W źródle danych RSS każdy z nich jest określany przez element `<item>`, jak pokazano poniżej:

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

Kolejność artykułów jest ważna. Określa ona klasyfikację domyślną, która jest najlepszą podpowiedzią dotyczącą kolejności artykułów na liście. Klasyfikacja domyślna jest następnie stosowana na potrzeby porównania wydajności w pulpicie nawigacyjnym.

Aby uzyskać więcej informacji o formacie źródła danych, zobacz [dokumentację interfejsu API](custom-decision-service-api-reference.md#action-set-api-customer-provided).

## <a name="register-a-new-app"></a>Rejestrowanie nowej aplikacji

1. Zaloguj się za pomocą [konta Microsoft](https://portal.ds.microsoft.com/). Na wstążce kliknij pozycję **My Portal** (Mój portal).

2. Aby zarejestrować nową aplikację, kliknij przycisk **New App** (Nowa aplikacja).

    ![Portal usługi Custom Decision Service](./media/custom-decision-service-tutorial/portal.png)

3. Wprowadź unikatową nazwę aplikacji w polu tekstowym **App ID** (Identyfikator aplikacji). Jeśli nazwa jest już używana przez innego klienta, system wyświetli monit o wybranie innego identyfikatora aplikacji. Zaznacz pole wyboru **Advanced** (Zaawansowane), a następnie wprowadź [parametry połączenia](../../storage/common/storage-configure-connection-string.md) dla konta magazynu platformy Azure. Zwykle używa się tego samego konta magazynu dla wszystkich aplikacji.

    ![Okno dialogowe nowej aplikacji](./media/custom-decision-service-tutorial/new-app-dialog.png)

    Po zarejestrowaniu wszystkich trzech aplikacji w powyższym scenariuszu zostaną one wyświetlone:

    ![Lista aplikacji](./media/custom-decision-service-tutorial/apps.png)

    Możesz wrócić do tej listy, klikając przycisk **Apps** (Aplikacje).

4. W oknie dialogowym **New App** (Nowa aplikacja) określ źródło danych akcji. Źródła danych akcji można również określić, klikając przycisk **Feeds** (Źródła danych), a następnie klikając przycisk **New Feed** (Nowe źródło danych). Wprowadź **nazwę** dla nowego źródła danych, **adres URL**, z którego jest udostępniane, a następnie wprowadź wartość w polu **Refresh Time** (Czas odświeżania). Czas odświeżania określa, jak często usługa Custom Decision Service ma odświeżać to źródło danych.

    ![Okno dialogowe nowego źródła danych](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    Źródła danych akcji mogą być używane przez dowolną aplikację, niezależnie od tego, gdzie są określone. Po określeniu obu źródeł danych akcji dla naszego scenariusza zostaną one wyświetlone:

    ![Lista źródeł danych](./media/custom-decision-service-tutorial/feeds.png)

    Możesz wrócić do tej listy, klikając przycisk **Feeds** (Źródła danych).

## <a name="use-the-apis"></a>Używanie interfejsów API

Usługa Custom Decision Service klasyfikuje artykuły za pośrednictwem interfejsu API klasyfikowania (Ranking API). Aby użyć tego interfejsu API, wstaw następujący kod w nagłówku HTML strony głównej:

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

Odpowiedź HTTP z interfejsu API klasyfikowania jest ciągiem w formacie JSONP. Na przykład dla aplikacji app-politics ciąg ten wygląda następująco:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

Następnie przeglądarka wykonuje ten ciąg jako wywołanie funkcji `callback()`. Argument `data` funkcji `callback()` zawiera identyfikator aplikacji i klasyfikację adresów URL, które mają zostać zrenderowane. W szczególności funkcja `callback()` powinna używać argumentu `data.appId`, aby rozróżnić nasze trzy aplikacje. Element `eventId` jest używany wewnętrznie przez usługę Custom Decision Service w celu dopasowania podanej klasyfikacji do odpowiedniego kliknięcia, jeśli takie istnieje.

> [!TIP]
> Funkcja `callback()` może sprawdzać świeżość każdego źródła danych akcji, używając pola `lastRefresh`. Jeśli określone źródło danych nie jest wystarczająco świeże, funkcja `callback()` może zignorować podaną klasyfikację, wywołać to źródło danych bezpośrednio i zastosować domyślna klasyfikację udostępnianą przez to źródło.

Aby uzyskać więcej informacji na temat specyfikacji i dodatkowych opcji udostępnianych przez interfejs API klasyfikowania, zobacz [dokumentację interfejsu API](custom-decision-service-api-reference.md).

Artykuły wybierane najczęściej przez użytkownika są zwracane przez wywołanie interfejsu API nagradzania (Reward API). Po otrzymaniu informacji o najczęściej wybieranym artykule na stronie głównej powinien zostać wyzwolony następujący kod:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

Użycie argumentów `appId` i `eventId` w kodzie obsługującym kliknięcia wymaga nieco uwagi. Na przykład funkcję `callback()` można zaimplementować w następujący sposób:

```javascript
function callback(data) {
    $.map(data.ranking, function (element) {
        //custom rendering function given content info
        render({appId:data.appId,
                article:element,
                onClick: element.id != data.rewardAction ? null :
                   function() {
                       $.ajax({
                       type: "POST",
                       url: '//ds.microsoft.com/api/v2/' + data.appId + '/reward/' + data.eventId,
                       contentType: "application/json" })}
                });
}}
```

W tym przykładzie funkcję `render()` zaimplementowano w celu renderowania określonego artykułu dla określonej aplikacji. Ta funkcja jako danych wejściowych używa identyfikatora aplikacji i artykułu (w formacie z interfejsu API klasyfikowania). Parametr `onClick` jest funkcją, która powinna zostać wywołana z funkcji `render()`, aby obsłużyć kliknięcie. Sprawdza ona, czy kliknięcie dotyczy pierwszego miejsca. Następnie wywołuje interfejs API nagradzania z odpowiednim identyfikatorem aplikacji i identyfikatorem zdarzenia.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [dokumentacją interfejsu API](custom-decision-service-api-reference.md), aby dowiedzieć się więcej o funkcjach usługi.
