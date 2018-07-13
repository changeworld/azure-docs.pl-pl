---
title: Czym jest wyszukiwanie jednostek Bing? | Microsoft Docs
description: Dowiedz się, jak używać interfejsu API wyszukiwania jednostek Bing do wyszukiwania w sieci web jednostek i miejsca.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 0B54E747-61BF-42AA-8788-E25D63F625FC
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2016
ms.author: scottwhi
ms.openlocfilehash: 275430bc6ee8f935978243e61f68713974648189
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008114"
---
# <a name="what-is-bing-entity-search"></a>Czym jest wyszukiwanie jednostek Bing?

Interfejs API wyszukiwania jednostek Bing wysyła zapytanie do usługi Bing i pobiera wyniki, które zawierają jednostki i miejsca. Umieść wyniki obejmują restauracji, hotelu lub innych lokalnych firm. Wyszukiwarka Bing zwróci miejsc, czy zapytanie określa nazwę lokalnych firmach prosi o rodzaju firm (na przykład, restauracje w pobliżu). Wyszukiwarka Bing zwraca jednostki, jeśli zapytanie określa dobrze znanych osób, miejsc (atrakcji, Stany, kraje itp.) lub rzeczy.

## <a name="suggesting--using-search-terms"></a>Sugerowanie i używanie wyszukiwanych terminów

Jeśli udostępniasz pole wyszukiwania, w którym użytkownik wprowadza wyszukiwany termin, użyj [interfejsu API automatycznego sugerowania Bing](../bing-autosuggest/get-suggested-search-terms.md) w celu ulepszenia jego działania. Interfejs API zwraca sugerowane ciągi zapytań na podstawie częściowych wyszukiwanych terminów w miarę wpisywania ich przez użytkownika.

Gdy użytkownik wprowadzi wyszukiwany termin, zakoduj go w formacie URL przed ustawieniem parametru zapytania [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query). Na przykład, jeśli użytkownik wprowadzi *Marcus Appel*ustaw `q` do *Marcus + Appel* lub *Marcus % 20Appel*.

Jeśli termin wyszukiwania zawiera błąd pisowni, odpowiedź wyszukiwania zawiera [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) obiektu. Obiekt zawiera oryginalną pisownię i poprawiony pisowni, który Bing używany do wyszukiwania.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="requesting-entities"></a>Jednostki żądania

Aby uzyskać przykładowe żądanie, zobacz [dokonywania pierwszego żądania](./quick-start.md).

## <a name="the-response"></a>Odpowiedź

Odpowiedź zawiera [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse) obiektu. Jeśli Bing znajdzie jednostki lub miejsca, która jest odpowiednia, zawiera obiekt `entities` pola `places` pole lub oba. W przeciwnym razie obiekt odpowiedzi nie obejmuje obu pól.
> [!NOTE]
> Jednostki odpowiedzi obsługuje różne rynki, ale odpowiedź umieszcza obsługuje tylko lokalizacje firmowe USA. 

`entities` Pole jest [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entityanswer) obiekt, który zawiera listę [jednostki](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity) obiektów (zobacz `value` pola). Lista może zawierać pojedynczy element dominujący i/lub wielu jednostek uściślania. 

Dominujący jednostki jest jednostką, którego Bing jest tylko jednostki, która spełnia żądanie (nie ma żadnych niejednoznaczności, określić jednostki spełnia żądanie). Jeśli wiele jednostek może spełnić żądania, lista zawiera więcej niż jednej jednostki uściślania. Na przykład jeśli żądanie używa ogólny tytuł udzielają film, prawdopodobnie listy zawiera rozróżniania podmiotów. Ale jeśli żądanie określa określonego tytułu z udzielają, lista prawdopodobnie zawiera pojedynczy element dominującego.

Jednostki zawierają dobrze znanych osobowości piosenkarze aktorów, sportowców, modele, np.; miejsca i charakterystycznych elementów krajobrazu, takich jak Roman instalacji lub Lincoln pamięci; i elementy, takie jak tytuł banany, goldendoodle, książki lub filmu. [EntityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) pole zawiera wskazówki, które identyfikują typ jednostki. Na przykład, jeśli osoba, filmów, zwierzę lub przyciągania. Aby uzyskać listę możliwych typów, zobacz [typów jednostek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Poniżej przedstawiono odpowiedź, która zawiera jednostkę dominujący i uściślania.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "http://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

Zawiera jednostki `name`, `description`, i `image` pola. Gdy tych pól jest wyświetlana w środowiska użytkownika, należy je atrybutu. `contractualRules` Pole zawiera listę przypisań, które należy zastosować. Zobowiązania umowne reguła identyfikuje pola, które dotyczy wpisu. Aby uzyskać informacji dotyczących stosowania autorstwa, zobacz [autorstwa](#data-attribution).

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "http://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

Podczas wyświetlania informacji dotyczących jednostki (nazwa, opis i obraz), należy również użyć adresu URL w `webSearchUrl` pola, aby utworzyć łącze do wyszukiwania Bing wyniki strony, która zawiera jednostki.


`places` Pole jest [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#localentityanswer) obiekt, który zawiera listę [miejscu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#place) obiektów (zobacz `value` pola). Lista zawiera co najmniej jeden lokalny jednostek spełniających kryteria żądania.

Umieszcza obejmują restauracji, hotele lub lokalnych firm. [EntityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) pole zawiera wskazówki, które identyfikują typ jednostki lokalnego. Lista zawiera listę wskazówek dotyczących serwerów, takich jak miejsce, LocalBusiness restauracji. Każda kolejne wskazówka w tablicy powoduje zawężenie typu jednostki. Aby uzyskać listę możliwych typów, zobacz [typów jednostek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> Jednostki odpowiedzi obsługuje różne rynki, ale odpowiedź umieszcza obsługuje tylko lokalizacje firmowe USA. 

Lokalne pamiętać jednostki takich zapytań jak *restauracji w pobliżu* wymagają lokalizacji użytkownika, aby zapewnić dokładne wyniki. Żądań zawsze należy używać X wyszukiwania lokalizacji i nagłówków X-MSEdge-ClientIP do określenia lokalizacji użytkownika. Jeśli ta opcja uzna Bing za zapytania będą korzystać z lokalizacji użytkownika, ustawia `askUserForLocation` pole [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) do **true**. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

Wynik miejscu zawiera nazwę, adres, numer telefonu i adres URL to miejsce do jednostki witryny sieci Web. Podczas wyświetlania informacji dotyczących jednostki, musisz również użyć adresu URL w `webSearchUrl` pola, aby utworzyć łącze do wyszukiwania Bing wyniki strony, która zawiera jednostki.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> Możesz lub innych firm w Twoim imieniu może nie używać, zachować, przechowywania, pamięci podręcznej, udostępnić, Dystrybuuj wszystkie dane z jednostki interfejsu API na potrzeby testowania, tworzenia, szkolenia, rozpowszechniania lub udostępniania dowolnej usługi firmy Microsoft lub funkcji.  

## <a name="data-attribution"></a>Autorstwa danych

Interfejs API jednostek Bing odpowiedzi zawiera informacje należące do innych firm. Ponosisz odpowiedzialność, aby upewnić się, że użytkowanie jest odpowiednie, na przykład zgodnych z licencji creative commons środowiska użytkownika może polegać na.

Jeśli wynik lub odpowiedzi zawiera `contractualRules`, `attributions`, lub `provider` pól, należy atrybutów danych. Jeśli odpowiedź nie zawiera żadnego z tych pól, autorstwa nie jest wymagana. Jeśli odpowiedź obejmuje `contractualRules` pola i `attributions` i/lub `provider` pola, musisz podać umowne stosowane w UE zasady do atrybutu danych.

W poniższym przykładzie przedstawiono jednostki, która zawiera regułę umownych MediaAttribution i obraz, który zawiera `provider` pola. Reguła MediaAttribution identyfikuje obraz jako obiekt docelowy reguły, dzięki czemu będzie ignorować obrazu `provider` pola, a następnie używają reguł MediaAttribution zapewnienie autorstwa.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Jeśli zawiera regułę umownych `targetPropertyName` pole, reguła dotyczy tylko pola docelowego. W przeciwnym razie ta reguła ma zastosowanie do obiektu nadrzędnego, który zawiera `contractualRules` pola.

W poniższym przykładzie `LinkAttribution` reguła zawiera `targetPropertyName` pola, więc ta reguła ma zastosowanie do `description` pola. Dla reguł, które dotyczą określonych pól musi zawierać wiersz bezpośrednio po docelowych dane, które zawierają hiperłącza do witryny sieci Web dostawcy. Na przykład aby atrybut Opis, dołączyć linię natychmiast po tekst opisu zawierający hiperłącze do danych w witrynie sieci Web dostawcy, w tym przypadku utworzenie łącza do domeny contoso.com.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Autorstwa licencji

Jeśli zawiera listę reguł umownych [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) regułę, należy wyświetlić powiadomienia w wierszu bezpośrednio po zawartości, która dotyczy licencji. `LicenseAttribution` Reguła używa `targetPropertyName` pola, aby zidentyfikować właściwość, która dotyczy licencji.

Poniżej pokazano przykład, który zawiera `LicenseAttribution` reguły.

![Autorstwa licencji](./media/cognitive-services-bing-entities-api/licenseattribution.png)

Ogłoszenie licencji, którą można wyświetlać musi zawierać hiperlink do witryny sieci Web, który zawiera informacje o licencji. Zazwyczaj należy nazwa licencji hiperłącze. Na przykład, jeśli jest Akceptuję **tekst w polu DW przez SA licencji** i DW przez SA nazwa licencji, czyniłyby DW przez SA hiperłącze.

### <a name="link-and-text-attribution"></a>Łącza i tekst: uznanie autorstwa

[LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#linkattribution) i [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#textattribution) zasady zwykle są używane do identyfikowania dostawcy danych. `targetPropertyName` Pole określa pola, które dotyczy reguła.

Aby atrybutu dostawców, Dołącz wiersz bezpośrednio po zawartości, pierwsza stosowane do (na przykład pola docelowego). Wiersz należy wyraźnie oznaczone do wskazania, że dostawcy są źródła danych. Na przykład "dane z: contoso.com". Aby uzyskać `LinkAttribution` zasady, należy utworzyć hiperłącze do witryny sieci Web dostawcy.

Poniżej pokazano przykład, który zawiera `LinkAttribution` i `TextAttribution` reguły.

![Tekst łącza: uznanie autorstwa](./media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Nośnik: uznanie autorstwa

Jeśli jednostka zawiera obraz, możesz wyświetlić, musisz podać klikalny link do witryny sieci Web dostawcy. Jeśli jednostka zawiera [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mediaattribution) regułę, należy użyć adresu URL reguły do utworzenia łącza za pomocą kliknięć. W przeciwnym razie użyj adresu URL uwzględnione w obrazie `provider` pola, aby utworzyć link za pomocą kliknięć.

Poniżej pokazano przykład, który zawiera obraz `provider` pola i reguły umownych. Przykład zawiera reguły umowne stosowane w UE, możesz zignorować obrazu `provider` pola, a następnie zastosować `MediaAttribution` reguły.

![Nośnik: uznanie autorstwa](./media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Wyszukaj lub jak wyszukiwania

Tak samo, jak za pomocą interfejsu API wyszukiwania w sieci Web Bing, interfejsu API wyszukiwania jednostek Bing może można używać tylko w wyniku zapytania bezpośredniego użytkownika lub wyszukiwania lub wyniku akcji w obrębie aplikacji lub środowisko, które logicznie może być interpretowana jako żądania wyszukiwania przez użytkownika. W celach ilustracyjnych poniżej przedstawiono kilka przykładów dopuszczalne wyszukiwania lub doświadczeń podobne do wyszukiwania.

- Użytkownik wprowadza zapytania bezpośrednio w polu wyszukiwania w aplikacji
- Użytkownik wybiera określony tekst lub obraz i żądań "informacje dodatkowe" lub "informacje dodatkowe"
- Użytkownik poleca bot wyszukiwania o określonym temacie
- Użytkownik dwells na konkretny obiekt lub jednostki w scenariuszu typu wyszukiwania wizualnego

Jeśli nie masz pewności, jeśli środowisko jest uznawana za środowisko podobne do wyszukiwania, zaleca się sprawdzenie, czy z firmą Microsoft.

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Kolejne kroki

Aby szybko rozpocząć pracę z pierwszego żądania, zobacz [wprowadzania Your pierwsze żądanie](./quick-start.md).

Zapoznaj się z [interfejs API wyszukiwania jednostek Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) odwołania. Odwołania zawiera nagłówki i parametry zapytania, które umożliwia żądanie wyników wyszukiwania. Zawiera także definicje obiektów odpowiedzi. 

Aby zwiększyć wygodę korzystania z pola wyszukiwania, zobacz [interfejs API automatycznego sugerowania Bing](../bing-autosuggest/get-suggested-search-terms.md). Gdy użytkownik wprowadza swój termin zapytania, możesz wywołać ten interfejs API w celu uzyskania powiązanych terminów zapytania, które były używane przez inne osoby.

Nie zapomnij przeczytać [wymagań w zakresie korzystania z usługi Bing i wyświetlania danych z niej](./use-display-requirements.md), aby nie złamać żadnych reguł dotyczących korzystania z wyników wyszukiwania.