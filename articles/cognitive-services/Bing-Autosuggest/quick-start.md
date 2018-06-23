---
title: Automatycznego sugerowania szybkiego startu interfejsu API | Dokumentacja firmy Microsoft
description: Pokazuje, jak rozpocząć pracę przy użyciu interfejsu API automatycznego sugerowania usługi Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1482E781-7352-4A3F-B1D5-B896381348C4
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a7b54a1fb0b7c76eb72097357a6b51aa02e6e2fd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349004"
---
# <a name="making-your-first-autosuggest-query"></a>Tworzenie pierwszego zapytania Autosugerowanie

Przed skonfigurowaniem pierwszego połączenia, należy uzyskać klucz subskrypcji usługi kognitywnych. Aby uzyskać klucz, zobacz [spróbuj kognitywnych usług](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api).

Aby uzyskać wyniki wyszukiwania w sieci Web, czy wysłać żądania GET do następujący punkt końcowy:

```http
https://api.cognitive.microsoft.com/bing/v5.0/Suggestions
```

> [!NOTE]
> Punkt końcowy w wersji 7 w wersji zapoznawczej:
>
> ```http
> https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
> ```

Żądanie musi używać protokołu HTTPS.

Zaleca się, że wszystkie żądania pochodzi z serwera. Dystrybucja klucza w ramach aplikacji klienta zawiera więcej możliwości złośliwego innej do niego dostęp. Ponadto nawiązywanie połączeń z serwerem zapewnia jeden punkt uaktualniania wersji interfejsu API.

Żądanie musi określać [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) parametru zapytania, który zawiera użytkownika częściowe wyszukiwanego terminu. Chociaż jest to opcjonalne, powinny również określać żądanie [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) parametru zapytania, który identyfikuje rynku miejscu wyniki pochodzą z. Listę parametrów opcjonalnych zapytania, zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Wszystkie wartości parametrów zapytania musi być zakodowane w adresie URL.

Żądanie musi określać [Ocp-Apim-subskrypcji — klucz](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) nagłówka. Mimo że jest to opcjonalne, zachęca się także określić następujące nagłówki:

- [Agent użytkownika](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [ClientIP-X-wyszukiwania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X wyszukiwania lokalizacji](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Nagłówki adresów IP i lokalizacji klienta są ważne dla zwracania zawartości pamiętać lokalizacji.

Aby uzyskać listę wszystkich nagłówków żądań i odpowiedzi, zobacz [nagłówki](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

## <a name="the-request"></a>Żądanie

Żądanie powinna zawierać wszystkie parametry sugerowane zapytania i nagłówków. Ten interfejs API spowodowałoby wywołanie w każdym razem, użytkownik wpisze nowego znaku w polu wyszukiwania. Zwraca informacje były kompletne wpływów ciąg zapytania, że znaczenie zapytania sugerowane warunki interfejsu API. Im więcej ukończyć ciąg zapytania, tym większe znaczenie czy listę sugerowane są warunki zapytania. Na przykład sugestii, które interfejs API może zwrócić dla *s* mogą być istotne mniej niż zapytanie zwraca dla *dinghies prowadzenia*. 

W poniższym przykładzie przedstawiono żądanie, które zwraca ciągi zapytań sugerowane dla *kierowania*.

```http
GET https://api.cognitive.microsoft.com/bing/v5.0/suggestions?q=sail&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> Żądanie w wersji 7 w wersji zapoznawczej:

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Jeśli pierwsza wywoływania żadnego z interfejsów API Bing, nie dołączaj nagłówka Identyfikatora klienta. Tylko Dołącz nagłówka Identyfikatora klienta, jeżeli zostały wcześniej wywołuje interfejs API Bing i Bing zwrócony identyfikator klienta dla użytkownika lub kombinacji urządzeń.

Poniżej przedstawiono odpowiedzi na poprzedniego żądania. Odpowiedź zawiera grupy sugestię sieci web, która zawiera listę sugestie dotyczące zapytań wyszukiwania. Obejmuje każdego sugestię `displayText`, `query`, i `url` pola.

`displayText` Pole zawiera sugerowane zapytania, który ma zostać użyty do wypełnienia listy rozwijanej pole wyszukiwania. Konieczne jest wyświetlenie wszystkich sugestii, które zawiera odpowiedź, a w podanej kolejności.  

Jeśli użytkownik wybierze zapytania z listy rozwijanej, można skorzystać z ciągu zapytania w `query` pola do wywołania [API wyszukiwania usługi Bing](../bing-web-search/search-the-web.md) i wyświetlić wyniki samodzielnie. Można użyć adresu URL na liście `url` strona wyników pola, aby wysłać do użytkownika do wyszukiwania usługi Bing.

```  
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Kolejne kroki

Wypróbuj interfejsu API. Przejdź do [automatycznego sugerowania API testowania konsoli](https://dev.cognitive.microsoft.com/docs/services/56c7694ecf5ff801a090fbd1/operations/56c769a2cf5ff801a090fbd2).

Aby uzyskać więcej informacji o korzystanie z obiektów odpowiedzi, zobacz [pobierania sugerowane terminy wyszukiwania](./get-suggested-search-terms.md).
