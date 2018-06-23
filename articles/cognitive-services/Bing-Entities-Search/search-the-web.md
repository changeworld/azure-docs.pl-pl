---
title: Co to jest wyszukiwania usługi Bing jednostki? | Microsoft Docs
description: Dowiedz się, jak wyszukać w sieci web jednostki i miejsca przy użyciu interfejsu API wyszukiwania usługi Bing jednostki.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 0B54E747-61BF-42AA-8788-E25D63F625FC
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2016
ms.author: scottwhi
ms.openlocfilehash: f1b87c07d5b56307fd6b3fc68999598aeab6eb82
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349216"
---
# <a name="what-is-bing-entity-search"></a>Co to jest wyszukiwania usługi Bing jednostki?

Interfejs API wyszukiwania usługi Bing jednostki wysyła kwerendę wyszukiwania usługi Bing i pobiera wyniki obejmujące jednostki i miejsca. Miejsce wyniki obejmują restauracji, hoteli lub innych firm lokalnego. Bing zwraca miejsca, jeśli zapytanie określa nazwę lokalnego biznesowych lub zapyta, czy typ firm (na przykład restauracji pobliżu). Bing zwraca jednostki, jeśli zapytanie określa dobrze znanych osób, miejsc (atrakcji, stanów, krajach itp.) lub rzeczy.

## <a name="suggesting--using-search-terms"></a>Sugerowanie & przy użyciu terminy wyszukiwania

Jeśli podasz pole wyszukiwania, w którym użytkownik wprowadza swoje wyszukiwany termin, użyj [API automatycznego sugerowania usługi Bing](../bing-autosuggest/get-suggested-search-terms.md) Aby ulepszyć środowisko użytkownika. Interfejs API zwraca sugerowane zapytanie ciągów na podstawie wyszukiwania z częściowa warunków jako typy użytkownika.

Gdy użytkownik wprowadzi ich wyszukiwany termin, adres URL kodowania termin przed ustawieniem [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) parametr zapytania. Na przykład, jeśli użytkownik wprowadzi *Marcus Appel*ustaw `q` do *Marcus + Appel* lub *Marcus % 20Appel*.

Jeśli wyszukiwany termin zawiera błąd pisowni, odpowiedź wyszukiwania zawiera [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) obiektu. Obiekt pokazuje oryginalny pisownię i poprawiony pisowni, używany do wyszukiwania usługi Bing.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="requesting-entities"></a>Jednostki żądania

Aby uzyskać przykładowe żądanie, zobacz [wnioskiem pierwszy](./quick-start.md).

## <a name="the-response"></a>Odpowiedź

Odpowiedź zawiera [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse) obiektu. Jeśli Bing znajdzie jednostkę lub miejsca, która jest odpowiednia, zawiera obiekt `entities` pola `places` pola lub oba. W przeciwnym wypadku obiekt odpowiedzi nie zawiera albo pola.

`entities` Pole jest [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entityanswer) obiekt, który zawiera listę [jednostki](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity) obiektów (zobacz `value` pól). Lista może zawierać pojedynczy element dominującej i/lub wiele jednostek Uściślanie. 

Jednostki dominującej jest jednostką, którego usługi Bing jest tylko jednostki, która obsługuje żądania (Brak nie niejednoznaczności, które jednostki spełnia żądania). Jeśli wiele jednostek można spełnić żądania, lista zawiera więcej niż jednej jednostki Uściślanie. Na przykład jeśli żądanie używa ogólnego tytuł franchisingowe film, prawdopodobnie listy zawiera Uściślanie jednostki. Ale jeśli żądanie określa określonego tytułu z franchising, prawdopodobnie lista zawiera pojedynczą jednostką dominującą.

Jednostek obejmują dobrze znanego osobowości piosenkarze uczestników, athletes, modeli, np.; miejsca i punkty orientacyjne, takie jak instalacji Roman lub pamięci Lincoln; i działania, takie jak tytuł bananów, goldendoodle, książki lub filmu. [EntityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) pole zawiera wskazówki, które identyfikują do typu jednostki. Na przykład, jeśli jest osoba, filmów, zwierzę lub przyciągania. Aby uzyskać listę możliwych typów, zobacz [typów jednostek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Poniżej przedstawiono odpowiedzi, która zawiera jednostki dominujące i Uściślanie.

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

Zawiera jednostki `name`, `description`, i `image` pola. Po wyświetleniu tych pól w użytkowników, należy ich atrybutów. `contractualRules` Pole zawiera listę przypisań, które należy zastosować. Reguła umownymi identyfikuje pola, które dotyczą wpisu. Informacje o zastosowaniu autorstwa, zobacz [autorstwa](#data-attribution).

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

Podczas wyświetlania informacji podmiotu (nazwa, opis i obraz), należy również użyć adresu URL na liście `webSearchUrl` strony, która zawiera obiekt wyniki pól do odesłania do wyszukiwania usługi Bing.


`places` Pole jest [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#localentityanswer) obiekt, który zawiera listę [miejsce](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#place) obiektów (zobacz `value` pól). Lista zawiera co najmniej jeden lokalny jednostek spełniających kryteria żądania.

Miejsca to restauracji, hotele lub lokalnych firm. [EntityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) pole zawiera wskazówki określającymi typ jednostki lokalnej. Lista zawiera listę wskazówek, takie jak miejsce, LocalBusiness restauracji. Każda kolejne wskazówkę w tablicy zawęża element do typu jednostki. Aby uzyskać listę możliwych typów, zobacz [typów jednostek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```

Lokalne jednostki pamiętać, takie jak zapytania *restauracji pobliżu* wymagają lokalizacji użytkownika, aby zapewnić dokładne wyniki. Żądań zawsze należy używać X wyszukiwania lokalizacji i nagłówków ClientIP-X-MSEdge Aby określić lokalizację użytkownika. Jeśli Bing sądzi, zapytanie będzie korzystać z lokalizacji użytkownika, ustawia `askUserForLocation` pole [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) do **true**. 

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

Wynik miejscu obejmuje miejsce nazwa, adres, numer telefonu i adres URL do witryny sieci Web jednostki. Podczas wyświetlania informacji jednostki, należy również użyć adresu URL na liście `webSearchUrl` strony, która zawiera obiekt wyniki pól do odesłania do wyszukiwania usługi Bing.

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
> Możesz lub innej firmy w Twoim imieniu może nie, Zachowaj, przechowywania, pamięci podręcznej, udział, albo dystrybucji żadnych danych z jednostki interfejsu API na potrzeby testowania, tworzenie, uczenie, dystrybucja lub udostępnia usługi firmy Microsoft lub funkcji.  

## <a name="data-attribution"></a>Autorstwa danych

Odpowiedzi interfejsu API Bing jednostki zawierają informacje posiadane przez osoby trzecie. Ponosisz odpowiedzialność za zapewnienie, że używanie jest odpowiednia, na przykład przez zgodne z licencji creative commons przez użytkowników mogą polegać na.

Jeśli odpowiedzi lub wynik zawiera `contractualRules`, `attributions`, lub `provider` pól, musi atrybutu danych. Jeśli odpowiedź nie zawiera żadnego z tych pól, przypisanie nie jest wymagana. Jeśli odpowiedź zawiera `contractualRules` pola i `attributions` i/lub `provider` pola, muszą używać zasad umownych atrybutu danych.

W poniższym przykładzie przedstawiono jednostki, która zawiera regułę umownymi MediaAttribution i obraz, który zawiera `provider` pola. Reguła MediaAttribution identyfikuje obraz jako element docelowy reguły, więc będzie ignorować obrazu `provider` pola i zamiast tego użyj reguł MediaAttribution, aby zapewnić autorstwa.  

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

Jeśli reguła umownymi zawiera `targetPropertyName` pola, ta reguła ma zastosowanie tylko do pola docelowego. W przeciwnym razie ta reguła ma zastosowanie do obiektu nadrzędnego, który zawiera `contractualRules` pola.

W poniższym przykładzie `LinkAttribution` reguła zawiera `targetPropertyName` pola, więc ta reguła ma zastosowanie do `description` pola. Dla reguł, które dotyczą określonych pól należy wprowadzić wiersza od razu po wybranych danych, który zawiera hiperłącza do witryny sieci Web dostawcy. Na przykład atrybutu opis, obejmują linii natychmiast po tekst opisu, który zawiera hiperłącza do danych w witrynie sieci Web dostawcy, w tym przypadku utworzyć łącze do domeny contoso.com.

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

### <a name="license-attribution"></a>Przypisanie licencji

Jeśli na liście reguł umownymi zawiera [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) regułę, należy wyświetlić ogłoszenia w wierszu bezpośrednio po zawartości dotyczącej licencji. `LicenseAttribution` Zasada używa `targetPropertyName` pola, aby zidentyfikować właściwość, która dotyczy licencji.

Poniżej przedstawiono przykład zawierający `LicenseAttribution` reguły.

![Przypisanie licencji](./media/cognitive-services-bing-entities-api/licenseattribution.png)

Zawiadomienia licencji, które można wyświetlić musi zawierać hiperłącza do witryny sieci Web, który zawiera informacje o licencji. Zazwyczaj wprowadzeniu nazwy licencji hiperłącze. Na przykład, jeśli jest postanowień **tekstu w ramach licencji DW przez SA** i DW przez administratora systemu jest nazwą licencji, spowodowałoby DW przez administratora systemu hiperłącze.

### <a name="link-and-text-attribution"></a>Autorstwa łącza i tekst

[LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#linkattribution) i [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#textattribution) zasady zwykle są używane do identyfikowania dostawcy danych. `targetPropertyName` Pole określa pole, które będzie stosowana ta reguła.

Atrybutu dostawców, obejmują linii bezpośrednio po zawartości, które mają zastosowanie pierwsza na (na przykład pole docelowe). Wiersz należy wyraźnie etykietami do wskazania, że źródło danych dostawców. Na przykład "dane z: contoso.com". Aby uzyskać `LinkAttribution` zasady, należy utworzyć hiperłącza do witryny sieci Web dostawcy.

Poniżej przedstawiono przykład zawierający `LinkAttribution` i `TextAttribution` reguły.

![Autorstwa tekst łącza](./media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Nośnik autorstwa

Jeśli obiekt zawiera obraz i wyświetl ją, musisz podać kliknięć łącze do witryny sieci Web dostawcy. Jeśli obiekt zawiera [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mediaattribution) reguły, użyj jej adres URL, aby utworzyć łącze kliknięć. W przeciwnym razie użyj adresu URL uwzględniony w obrazie `provider` pola, aby utworzyć łącze kliknięć.

Poniżej przedstawiono przykład zawierający obraz `provider` pola i umownymi reguły. Przykład zawiera umownymi reguły, możesz zignorować obrazu `provider` pola i Zastosuj `MediaAttribution` reguły.

![Nośnik autorstwa](./media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Wyszukiwania lub jak wyszukiwania

Podobnie jak z API wyszukiwania usługi Bing sieci Web interfejsu API wyszukiwania usługi Bing jednostki mogą być używane tylko w wyniku zapytania bezpośredniego użytkownika lub wyszukiwania lub wyniku akcji w obrębie aplikacji lub doświadczenia, które logicznie mogą być interpretowane jako żądania wyszukiwania użytkownika. W celach ilustracyjnych poniżej przedstawiono kilka przykładów dopuszczalne wyszukiwania lub środowiska przypominającej wyszukiwania.

- Użytkownik wprowadza zapytanie bezpośrednio w polu wyszukiwania w aplikacji
- Użytkownik wybiera określony tekst lub obraz i żądania "więcej informacji" lub "informacje dodatkowe"
- Użytkownik żąda bot wyszukiwania o konkretnym temacie
- Użytkownik dwells określonego obiektu lub jednostki w scenariuszu typu visual wyszukiwania

Jeśli nie masz pewności, czy środowiska jest uznawana za środowiska przypominającej wyszukiwania, zalecane jest sprawdzenie z firmą Microsoft.

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Kolejne kroki

Aby szybko rozpocząć pracę z pierwszego żądania, zobacz [co Twoje pierwsze żądanie](./quick-start.md).

Zapoznaj się z [w wersji 7 interfejsu API Bing encji wyszukiwania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) odwołania. Odwołanie zawiera nagłówki i parametry zapytania, które umożliwia żądanie wyników wyszukiwania. Zawiera także definicje obiektów odpowiedzi. 

Aby zwiększyć pole wyszukiwania użytkowników, zobacz [API automatycznego sugerowania usługi Bing](../bing-autosuggest/get-suggested-search-terms.md). Jako użytkownik wprowadza swoje wyszukiwanego terminu, można wywołać tego interfejsu API można pobrać terminów odpowiednich zapytania, które były używane przez innych użytkowników.

Należy przeczytać [Bing oraz wymagania dotyczące wyświetlania](./use-display-requirements.md) , nie zostanie przerwane dowolne zasady dotyczące korzystania z wyników wyszukiwania.