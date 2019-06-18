---
title: Wywoływanie interfejsu API z aplikacji — Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Jak wywołać niestandardowe interfejsy API usługi decyzji z aplikacja na smartfona.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ms.openlocfilehash: 0e5c99aae61fb927ea7f101bab74d661a747f88b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60511549"
---
# <a name="call-api-from-an-app"></a>Wywoływanie interfejsu API z aplikacji

Bezpieczne wywołania interfejsów API usługi Azure Custom Decision usługi z aplikacja na smartfona. W tym artykule wyjaśniono, jak rozpocząć pracę z niektórych podstawowych opcji.

Pamiętaj, aby [Zarejestruj swoją aplikację](custom-decision-service-get-started-register.md)najpierw.

Istnieją dwa wywołania interfejsu API, można dokonać z aplikacja na smartfona Custom Decision Service: wywołanie interfejsu API klasyfikacji można uzyskać uporządkowaną listę zawartości i wywołanie interfejsu API za wynagrodzeniem zgłosić wynagrodzenia. Poniżej przedstawiono przykładowe wywołania w [cURL](https://en.wikipedia.org/wiki/CURL).

Aby uzyskać więcej informacji, zobacz odwołania [API](custom-decision-service-api-reference.md).

Uruchom przy użyciu wywołania interfejsu API klasyfikacji. Utwórz plik `<request.json>`, która Akcja ustawił identyfikatora. Ten identyfikator jest nazwą odpowiedniej RSS lub źródło danych Atom wprowadzony w portalu:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Można określić wiele zestawów działań w następujący sposób:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Ten plik JSON jest następnie wysyłana jako część żądania klasyfikacji:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

W tym miejscu `<appId>` jest nazwą aplikacji jest zarejestrowany w portalu. Powinien zostać wyświetlony uporządkowany zestaw elementów zawartości, które można narysować w aplikacji. Przykład zwracany wygląda następująco:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

Pierwsza część zwracana ma listę uporządkowaną akcji, określonego przez ich identyfikatorów akcji. Artykułu identyfikator akcji jest adres URL. Ogólne żądanie również ma unikatową `<eventId>`utworzony przez system.

Później można określić, jeśli są przestrzegane kliknięcie pierwszego elementu zawartości, to zdarzenie, które jest `<actionId3>`. Raportować wynagrodzenia w tym `<eventId>` do usługi Custom Decision Service za pośrednictwem interfejsu API osób trzecich, z innego żądania takich jak:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Na koniec należy podać akcji Ustaw interfejsu API, które zwraca listę artykułów (Akcje), aby zostały uznane za przez usługi Custom Decision Service. Implementuje ten interfejs API jako źródło danych RSS, jak pokazano poniżej:

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

Tutaj każdy najwyższego poziomu `<item>` element zawiera opis artykułu. `<link>` Jest obowiązkowy i jest używany jako identyfikator akcji przez usługi Custom Decision Service. Określ `<date>` (w standardowym formacie RSS) Jeśli masz więcej niż 15 artykułów. 15 najnowsze artykuły są używane. `<title>` Jest opcjonalna i jest używany do tworzenia powiązanych z tekstem funkcji dla tego artykułu.

## <a name="next-steps"></a>Kolejne kroki

* Trzymać się kolejności [samouczek](custom-decision-service-tutorial-news.md) bardziej szczegółowy przykład.
* Zapoznaj się z odwołania [API](custom-decision-service-api-reference.md) Aby dowiedzieć się więcej o podanej funkcji.
