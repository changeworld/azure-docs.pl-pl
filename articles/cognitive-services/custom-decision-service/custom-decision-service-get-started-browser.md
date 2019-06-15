---
title: Wywoływanie interfejsu API z poziomu przeglądarki — Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Jak zoptymalizować strony sieci Web, wywołań interfejsu API z przeglądarki do usługi Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.openlocfilehash: 2b356e2f0fe9235d49dffa7417cd3894059f9caf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60829161"
---
# <a name="call-api-from-a-browser"></a>Wywoływanie interfejsu API z przeglądarki

Ten artykuł pomaga wykonywać wywołania interfejsów API usługi Azure Custom Decision usługi bezpośrednio w przeglądarce.

Pamiętaj, aby [Zarejestruj swoją aplikację](custom-decision-service-get-started-register.md)najpierw.

Zacznijmy. Aplikacja jest modelowane jako mające stronę początkową, który stanowi łącze do strony kilka artykułów. Pierwsza strona używa usługi Custom Decision Service do określania kolejności jej strony artykułu. Wstaw następujący kod do głowy HTML strony frontonu:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

`data` Argument zawiera klasyfikacji adresy URL służące do renderowania. Aby uzyskać więcej informacji, zobacz odwołania [API](custom-decision-service-api-reference.md).

Aby obsłużyć użytkownika kliknięcie istotny artykuł, należy wywołać następujący kod na pierwszej stronie:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

W tym miejscu `data` jest argumentem `callback()` funkcji. Przykład wdrożenia można znaleźć w tym [samouczek](custom-decision-service-tutorial-news.md#use-the-apis).

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
