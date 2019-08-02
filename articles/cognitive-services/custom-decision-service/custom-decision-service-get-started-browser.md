---
title: Wywoływanie interfejsu API z przeglądarki — Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Jak zoptymalizować stronę sieci Web przez wykonywanie wywołań interfejsu API bezpośrednio z przeglądarki do Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 28ad4681242765bf2da9b1f13dc828e23cce1794
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707268"
---
# <a name="call-api-from-a-browser"></a>Wywoływanie interfejsu API z przeglądarki

Ten artykuł pomaga w podejmowaniu połączeń do interfejsów API Custom Decision Service platformy Azure bezpośrednio z przeglądarki.

Pamiętaj, aby najpierw [zarejestrować aplikację](custom-decision-service-get-started-register.md).

Zacznijmy. Aplikacja jest modelowana jako pierwsza strona, która zawiera linki do kilku stron artykułów. Strona frontonu używa Custom Decision Service, aby określić kolejność stron artykułu. Wstaw następujący kod do nagłówka HTML strony frontonu:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

`data` Argument zawiera klasyfikację adresów URL, które mają być renderowane. Aby uzyskać więcej informacji, zobacz [interfejs API](custom-decision-service-api-reference.md)Reference.

Aby obsłużyć użytkownikowi, kliknij w górnym artykule, wywołaj następujący kod na stronie frontonu:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

W tym miejscu `callback()` jest argumentem funkcji. `data` Przykład implementacji można znaleźć w tym samouczku [](custom-decision-service-tutorial-news.md#use-the-apis).

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
