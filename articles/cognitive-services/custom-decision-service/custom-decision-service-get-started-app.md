---
title: Wywoływanie interfejsu API z aplikacji - kognitywnych usług platformy Azure | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z usługą decyzji niestandardowe Azure, interfejsów API połączeń z aplikacja na smartfona.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: slivkins
ms.reviewer: marcozo, alekh
ms.openlocfilehash: 2d02b0deaaa701dd0b4818638827c04e2c946558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349745"
---
# <a name="call-api-from-an-app"></a>Wywoływanie interfejsu API z aplikacji

Wykonywanie wywołań do interfejsów API usługi Azure niestandardowe decyzji Service z aplikacja na smartfona. W tym artykule wyjaśniono, jak rozpocząć pracę z niektóre podstawowe opcje.

Upewnij się, [zarejestrować aplikację](custom-decision-service-get-started-register.md), pierwszy.

Istnieją dwa interfejsu API wywołania z aplikacji smartphone usługi decyzji niestandardowe: wywołanie interfejsu API klasyfikacji do uzyskiwania listy uporządkowanej według rangi zawartości i wywołanie interfejsu API opłatą zgłoszenia wynagrodzenia. Poniżej przedstawiono przykładowe wywołań w [cURL](https://en.wikipedia.org/wiki/CURL).

Aby uzyskać więcej informacji, zobacz dokumentację [interfejsu API](custom-decision-service-api-reference.md).

Rozpocznij od wywołania interfejsu API klasyfikacji. Utwórz plik `<request.json>`, która ma akcji wartość identyfikatora. Ten identyfikator jest nazwą odpowiedniej RSS lub źródło danych Atom wprowadzony w portalu:

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

Ten plik JSON jest następnie wysyłana jako część żądania klasyfikacji:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

W tym miejscu `<appId>` jest zarejestrowana nazwa aplikacji w portalu. Powinien zostać wyświetlony uporządkowany zestaw elementów zawartości, które mogą być renderowane w aplikacji. Przykładowe zwracany wygląda następująco:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

Pierwsza część zwracany ma listy uporządkowanej akcje, określone przez ich identyfikatorów akcję. Dla artykułu identyfikator akcji jest adresem URL. Ogólne żądanie również ma unikatową `<eventId>`, utworzony przez system.

Później, możesz określić, jeśli przestrzegane kliknięcie pierwszego elementu zawartości, to zdarzenie, które jest `<actionId3>`. Raportować wynagrodzenia na tym `<eventId>` niestandardowe decyzji usługi za pomocą interfejsu API wynagrodzenie, z innego żądania takich jak:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Na koniec należy podać akcji Ustaw interfejsu API, który zwraca listę artykułów (Akcje) uznany przez usługę decyzji niestandardowe. Implementuje ten interfejs API jako źródła danych RSS, jak pokazano poniżej:

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

Tutaj każdy najwyższego poziomu `<item>` artykuł opisuje element. `<link>` Jest wymagana i jest używany jako identyfikator akcji przez usługę decyzji niestandardowe. Określ `<date>` (w formacie RSS) Jeśli znasz artykuły więcej niż 15. 15 najnowsze artykuły są używane. `<title>` Jest opcjonalna i służy do tworzenia funkcjach tekstu artykułu.

## <a name="next-steps"></a>Kolejne kroki

* Pracy za pośrednictwem [samouczek](custom-decision-service-tutorial-news.md) na pełniejsze przykład.
* Zapoznaj się odwołanie [interfejsu API](custom-decision-service-api-reference.md) Aby dowiedzieć się więcej o funkcjach podana.