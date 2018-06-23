---
title: Wywołanie interfejsu API z poziomu przeglądarki - kognitywnych usług Azure | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z usługą decyzji niestandardowe Azure tak, aby zoptymalizować wywołań interfejsu API bezpośrednio w przeglądarce strony sieci Web.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: slivkins,marcozo,alekh
ms.openlocfilehash: 10236c9d8f70d9b90a896464b4f86a847ee904c2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349116"
---
# <a name="call-api-from-a-browser"></a>Wywoływanie interfejsu API z przeglądarki

Ten artykuł ułatwia wykonywanie wywołań do interfejsów API usługi Azure niestandardowe decyzji Service bezpośrednio z przeglądarki.

Upewnij się, [zarejestrować aplikację](custom-decision-service-get-started-register.md), pierwszy.

Zacznijmy od początku. Aplikacja ma formę o stronę początkową, która łączy się kilka stron artykułów. Pierwsza strona używa usługi decyzji niestandardowe do określania kolejności stron artykułu. Wstaw następujący kod do head HTML front strony:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

`data` Argument zawiera klasyfikację adresy URL do renderowania. Aby uzyskać więcej informacji, zobacz dokumentację [interfejsu API](custom-decision-service-api-reference.md).

Aby obsłużyć użytkownika kliknięcie istotny artykuł, należy wywołać następujący kod na stronie witryny:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

W tym miejscu `data` jest argumentem `callback()` funkcji. Przykład wdrożenia znajduje się w tym [samouczek](custom-decision-service-tutorial-news.md#use-the-apis).

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

Tutaj każdy najwyższego poziomu `<item>` artykuł opisuje element. `<link>` Jest wymagana i jest używany jako identyfikator akcji przez usługę decyzji niestandardowe. Określ `<date>` (w formacie RSS), jeśli masz więcej niż 15 artykułów. 15 najnowsze artykuły są używane. `<title>` Jest opcjonalna i służy do tworzenia funkcjach tekstu artykułu.

## <a name="next-steps"></a>Kolejne kroki

* Pracy za pośrednictwem [samouczek](custom-decision-service-tutorial-news.md) na pełniejsze przykład.
* Zapoznaj się odwołanie [interfejsu API](custom-decision-service-api-reference.md) Aby dowiedzieć się więcej o funkcjach podana.