---
title: Artykuł personalizacji - kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Samouczek dotyczący personalizacji artykułu w usłudze Azure niestandardowe decyzji, oparte na chmurze interfejsu API kontekstowe podejmowania decyzji.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh;marossi
ms.openlocfilehash: 35d0567f81a23d4726461059eb6fd31e04228697
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349009"
---
# <a name="article-personalization"></a>Artykuł personalizacji

Ten samouczek koncentruje się na personalizowanie zaznaczenie artykułów na stronie frontonu witryny sieci Web. Wpływa na usługę decyzji niestandardowe *wiele* listę artykułów na pierwszej stronie dla wystąpienia. Być może strona jest wiadomości sieci Web, która obejmuje tylko polityka i sportowych. Czy Pokaż trzy listy uporządkowanej według rangi artykułów: polityka, oraz informacji sportowych, a ostatnie.

## <a name="applications-and-action-sets"></a>Aplikacje i zestawy akcji

Poniżej przedstawiono sposób mieści się w ramach danego scenariusza. Teraz załóżmy trzech aplikacji, po jednej dla każdej listy, który jest optymalizowana: polityka aplikacji, oraz informacji sportowych aplikacji, a ostatnie aplikacji. Aby określić artykuły candidate dla każdej aplikacji, dostępne są dwa zestawy akcji: jeden dla polityka i jeden dla aplikacji sportowych. Akcję ustawioną dla aplikacji ostatnie automatycznie jest dostarczany jako złożenie dwóch zestawów.

> [!TIP]
> Ustawia akcji mogą być współużytkowane przez aplikacje w usłudze decyzji niestandardowe.

## <a name="prepare-action-set-feeds"></a>Przygotowanie źródła zestaw akcji

Usługa niestandardowa decyzji zużywa zestawów akcji za pomocą takich źródeł dostarczone przez klienta. Musisz podać dwa źródła danych: jeden dla polityka i jeden dla aplikacji sportowych. Załóżmy, że są one udostępniane przez `http://www.domain.com/feeds/<feed-name>`.

Każdego źródła danych zawiera listę artykułów. RSS, każdy z nich jest określona przez `<item>` elementu w następujący sposób:

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

Kolejność artykułów jest ważne. Określa klasyfikację domyślny, który jest najbardziej prawdopodobną odpowiedź dla uporządkowanie artykułów. Domyślnie jest stosowana jest następnie używany do porównania wydajności na [pulpitu nawigacyjnego](#performance-dashboard).

Aby uzyskać więcej informacji na formatu źródła danych, zobacz [dokumentacja interfejsu API](custom-decision-service-api-reference.md#action-set-api-customer-provided).

## <a name="register-a-new-app"></a>Zarejestrować nową aplikację

1. Zaloguj się przy użyciu programu [konta Microsoft](https://account.microsoft.com/account). Na wstążce kliknij **portalu Moje**.

2. Aby zarejestrować nową aplikację, kliknij przycisk **nową aplikację** przycisku.

    ![Portal usługi Custom Decision Service](./media/custom-decision-service-tutorial/portal.png)

3. Wprowadź unikatową nazwę dla aplikacji w **identyfikator aplikacji** pola tekstowego. Jeśli ta nazwa jest już używany przez innego klienta, system wyświetla monit o pobranie identyfikatora dla różnych aplikacji. Wybierz **zaawansowane** pole wyboru, a następnie wprowadź [ciąg połączenia](../../storage/common/storage-configure-connection-string.md) dla konta magazynu Azure. Zwykle Użyj tego samego konta magazynu dla wszystkich aplikacji.

    ![Okno dialogowe Nowy aplikacji](./media/custom-decision-service-tutorial/new-app-dialog.png)

    Po zarejestrowaniu wszystkie trzy aplikacje w powyższym scenariuszu są wyświetlane:

    ![Lista aplikacji](./media/custom-decision-service-tutorial/apps.png)

    Możesz wrócić do tej listy, klikając **aplikacji** przycisku.

4. W **nową aplikację** oknie dialogowym Określ źródło akcji. Akcja źródeł danych można również określić, klikając **źródła** , a następnie przez **nowego źródła danych** przycisku. Wprowadź **nazwa** dla nowego źródła danych, wprowadź **adres URL** z którym jest obsługiwany, a następnie wprowadź **czasu odświeżania**. Czas odświeżania Określa, jak często niestandardowy decyzji usługi należy odświeżyć źródło.

    ![Nowe okno dialogowe źródła](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    Akcja źródła może służyć przez dowolną aplikację, niezależnie od tego, gdzie jest określona. Po określeniu obu źródeł akcji w scenariuszu, są one wyświetlane:

    ![Lista źródeł danych](./media/custom-decision-service-tutorial/feeds.png)

    Możesz wrócić do tej listy, klikając **źródła** przycisku.

## <a name="use-the-apis"></a>Korzystanie z interfejsów API

Usługa decyzji niestandardowe szereguje artykuły za pomocą interfejsu API klasyfikacji. Aby używać tego interfejsu API, Wstaw następujący kod do head HTML front strony:

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

Odpowiedź HTTP z interfejsu API Klasyfikacja to ciąg sformatowany JSONP. Dla polityka aplikacji na przykład ciąg wygląda następująco:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

Przeglądarka następnie wykonuje tego ciągu jako wywołanie `callback()` funkcji. `data` Argument `callback()` funkcja zawiera identyfikator aplikacji i klasyfikacji adresów URL do renderowania. W szczególności `callback()` należy używać `data.appId` odróżnienie trzech aplikacji. `eventId` jest używana wewnętrznie przez usługę decyzji niestandardowe do dopasowania podana Klasyfikacja kliknąć odpowiednie, jeśli istnieje.

> [!TIP]
> `callback()` Sprawdź każda akcja źródła danych dla świeżości przy użyciu `lastRefresh` pola. Jeśli danego źródła danych nie jest wystarczająco nowa `callback()` może ignorowanie podanych klasyfikację, bezpośrednio wywołać tego źródła danych i korzystać z klasyfikacji domyślne obsługiwane przez źródło.

Aby uzyskać więcej informacji o specyfikacjach i dodatkowe opcje udostępniony przez interfejs API klasyfikacji, zobacz [dokumentacja interfejsu API](custom-decision-service-api-reference.md).

Opcje istotny artykuł przez użytkownika są zwracane przez wywołanie interfejsu API osób trzecich. Po odebraniu wyboru Istotny artykuł następujący kod powinna być wywoływana na stronie witryny:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

Przy użyciu `appId` i `eventId` w kodzie obsługi kliknij wymaga niektórych szczególną uwagę. Na przykład można zaimplementować `callback()` działają w następujący sposób:

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

W tym przykładzie wdrożenia `render()` funkcji do renderowania danym artykule dla danej aplikacji. Ta funkcja danych wejściowych Identyfikatora aplikacji oraz artykuł (w formacie z klasyfikacji interfejsu API). `onClick` Parametr jest funkcją, która powinna być wywoływana z `render()` do obsługi kliknięcia. Sprawdza, czy kliknięcie jest na najwyższym miejsca. Następnie wywołuje API opłatą o identyfikatorze odpowiednich aplikacji i identyfikatora zdarzenia.

## <a name="next-steps"></a>Kolejne kroki

* Zapoznaj się [dokumentacja interfejsu API](custom-decision-service-api-reference.md) Aby dowiedzieć się więcej o funkcjach podana.