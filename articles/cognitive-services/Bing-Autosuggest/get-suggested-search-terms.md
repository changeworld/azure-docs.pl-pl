---
title: Czym jest funkcja automatycznego sugerowania Bing? | Microsoft Docs
description: Dowiedz się, jak używać interfejsu API automatycznego sugerowania Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 6F4AFEDA-71A7-48C1-B3E2-D0D430428CDC
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: overview
ms.date: 09/12/2017
ms.author: scottwhi
ms.openlocfilehash: 76e509289f495e09c367af926fd26e0581b6e7c6
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091110"
---
# <a name="what-is-bing-autosuggest"></a>Czym jest funkcja automatycznego sugerowania Bing?

W przypadku wysyłania zapytań do dowolnego z interfejsów API wyszukiwania Bing można użyć interfejsu API automatycznego sugerowania Bing w celu ulepszenia sposobu korzystania z pola wyszukiwania. Interfejs API automatycznego sugerowania Bing zwraca listę proponowanych zapytań na podstawie częściowego ciągu zapytania wprowadzanego przez użytkownika w polu wyszukiwania. Sugestie są wyświetlane na liście rozwijanej pola wyszukiwania. Sugerowane terminy bazują na sugerowanych zapytaniach, które wprowadzili do wyszukiwania inni użytkownicy, oraz na intencji użytkownika.

Ten interfejs API jest zwykle wywoływany za każdym razem, kiedy użytkownik wpisuje nowy znak w polu wyszukiwania. Kompletność ciągu zapytania ma wpływ na istotność sugerowanych terminów zapytania zwracanych przez interfejs API. Im bardziej kompletny jest ciąg zapytania, tym bardziej istotne są sugerowane terminy zapytania na wyświetlanej liście. Na przykład sugestie, które może zwrócić interfejs API po wpisaniu litery *p*, mogą być mniej istotne niż zapytania zwrócone dla terminu *pływanie łódką*.

## <a name="getting-suggested-search-terms"></a>Uzyskiwanie sugerowanych terminów wyszukiwania

W poniższym przykładzie przedstawiono żądanie, które zwraca sugerowane ciągi zapytania dla terminu *sail*. Jeśli ustawiono parametr zapytania [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query), należy pamiętać o zakodowaniu w adresie URL częściowego terminu zapytania użytkownika. Na przykład, jeśli użytkownik wprowadził termin *sailing les*, ustaw parametr `q` na wartość `sailing+les` lub `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

W następującej odpowiedzi widać listę obiektów [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction), które zawierają sugerowane terminy zapytania.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

Każda sugestia obejmuje pola `displayText`, `query` oraz `url`. Pole `displayText` zawiera sugerowane zapytanie, używane do zapełnienia listy rozwijanej w polu wyszukiwania. Należy wyświetlić wszystkie sugestie, które zawiera odpowiedź, w podanej kolejności.

Poniżej przedstawiono przykładowe pole wyszukiwania z listą rozwijaną sugerowanych terminów zapytania.

![Pole wyszukiwania z listą rozwijaną automatycznie sugerowanych terminów](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Jeśli użytkownik wybierze sugerowane zapytanie z listy rozwijanej, termin zapytania z pola `query` zostanie użyty w celu wywołania [interfejsu API wyszukiwania w sieci Web Bing](../bing-web-search/search-the-web.md) i wyświetlenia wyników. Alternatywnie można też użyć adresu URL z pola `url` w celu przeniesienia użytkownika na stronę wyników wyszukiwania usługi Bing.

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Następne kroki

Aby szybko rozpocząć pracę z pierwszym żądaniem, zobacz [Making Your First Query](quickstarts/csharp.md) (Tworzenie pierwszego zapytania).

Zapoznaj się z dokumentacją [interfejsu API automatycznego sugerowania Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference). Dokumentacja zawiera listę punktów końcowych, nagłówków i parametrów zapytań, które są stosowane w żądaniach sugerowanych wyszukiwanych terminów, oraz definicje obiektów odpowiedzi.

Dowiedz się, jak wyszukiwać w Internecie za pomocą [interfejsu API wyszukiwania w sieci Web Bing](../bing-web-search/search-the-web.md).

Nie zapomnij przeczytać [wymagań w zakresie korzystania z usługi Bing i wyświetlania danych z niej](./useanddisplayrequirements.md), aby nie złamać żadnych reguł dotyczących korzystania z wyników wyszukiwania.