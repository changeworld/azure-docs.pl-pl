---
title: Wywoływanie interfejsu API z aplikacji Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Jak wywoływać Custom Decision Service interfejsów API z poziomu aplikacji telefonu smartphone.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 08fbc1716d402c83bc2c33be82cba143c1737a55
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707252"
---
# <a name="call-api-from-an-app"></a>Wywoływanie interfejsu API z aplikacji

Wykonaj wywołania interfejsów API usługi Azure Custom Decision Service z poziomu aplikacji Smartphone. W tym artykule wyjaśniono, jak rozpocząć pracę z niektórymi podstawowymi opcjami.

Pamiętaj, aby najpierw [zarejestrować aplikację](custom-decision-service-get-started-register.md).

Istnieją dwa wywołania interfejsu API wprowadzone z aplikacji telefonu smartphone do Custom Decision Service: wywołanie interfejsu API klasyfikacji w celu uzyskania uporządkowanej listy zawartości i wywołania interfejsu API nagradzania w celu zgłoszenia nagrody. Oto przykładowe [wywołania w](https://en.wikipedia.org/wiki/CURL)zawieszeniu.

Aby uzyskać więcej informacji, zobacz [interfejs API](custom-decision-service-api-reference.md)Reference.

Zacznij od wywołania interfejsu API klasyfikacji. Utwórz plik `<request.json>`, który ma identyfikator zestawu akcji. Ten identyfikator jest nazwą odpowiedniego kanału informacyjnego RSS lub Atom wprowadzonego w portalu:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Wiele zestawów akcji można określić w następujący sposób:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Ten plik JSON jest następnie wysyłany jako część żądania rankingu:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

W tym miejscu jest nazwą aplikacji zarejestrowanej w portalu. `<appId>` Należy otrzymać uporządkowany zestaw elementów zawartości, które można renderować w aplikacji. Przykładowa próba jest następująca:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

Pierwsza część powrotu ma listę uporządkowanych akcji określonych przez ich identyfikatory akcji. W przypadku artykułu Identyfikator akcji jest adresem URL. Ogólne żądanie ma również unikatowy `<eventId>`, utworzony przez system.

Później możesz określić, czy zauważono kliknięcie pierwszego elementu zawartości z tego zdarzenia `<actionId3>`. Następnie można zgłosić na ten `<eventId>` temat wynagrodzenie, aby Custom Decision Service za pośrednictwem interfejsu API nagradzania, z innymi żądaniami, takimi jak:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Na koniec należy podać interfejs API zestawu akcji, który zwraca listę artykułów (akcji), które mają być uwzględniane przez Custom Decision Service. Zaimplementuj ten interfejs API jako źródło danych RSS, jak pokazano poniżej:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

W tym miejscu każdy element najwyższego poziomu `<item>` opisuje artykuł. Parametr `<link>` jest obowiązkowy i służy jako identyfikator akcji przez Custom Decision Service. Określ `<date>` (w standardowym formacie RSS), jeśli masz więcej niż 15 artykułów. Używane są 15 najnowszych artykułów. `<title>` Jest to opcjonalne i służy do tworzenia funkcji związanych z tekstem dla artykułu.

## <a name="next-steps"></a>Następne kroki

* Przechodzenie przez [samouczek](custom-decision-service-tutorial-news.md) , aby zapoznać się z bardziej szczegółowym przykładem.
* Zapoznaj się z [interfejsem API](custom-decision-service-api-reference.md) referencyjnym, aby dowiedzieć się więcej o podanej funkcji.
