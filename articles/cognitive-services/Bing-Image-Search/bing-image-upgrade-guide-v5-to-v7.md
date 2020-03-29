---
title: Uaktualnienie z interfejsu API wyszukiwania obrazów Bing w wersji 5 do wersji 7
titleSuffix: Azure Cognitive Services
description: W tym przewodniku po uaktualnieniu opisano zmiany między wersją 5 a wersją 7 interfejsu API wyszukiwania obrazów Bing. Ten przewodnik ułatwia identyfikowanie części aplikacji, które należy zaktualizować, aby korzystać z wersji 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: c4c6b95996206cfb38ea3f77b89c3ebe3c2c0026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68883502"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Przewodnik po uaktualnieniu interfejsu API wyszukiwania obrazów Bing w wersji 7

Ten przewodnik uaktualnienia identyfikuje zmiany między wersją 5 i wersją 7 interfejsu API wyszukiwania obrazów Bing. Ten przewodnik ułatwia identyfikowanie części aplikacji, które należy zaktualizować, aby korzystać z wersji 7.

## <a name="breaking-changes"></a>Fundamentalne zmiany

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego zmieniono z v5 na v7. Na przykład https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search.

### <a name="error-response-objects-and-error-codes"></a>Obiekty odpowiedzi na błędy i kody błędów

- Wszystkie żądania nie powiodło `ErrorResponse` się powinny teraz zawierać obiekt w treści odpowiedzi.

- Dodano następujące pola `Error` do obiektu.  
  - `subCode`&mdash;Dzieli kod błędu na dyskretne zasobniki, jeśli to możliwe
  - `moreDetails`&mdash;Dodatkowe informacje o błędzie opisanym w `message` polu


- Zastąpiono kody błędów v5 następującymi możliwymi `code` i `subCode` wartościami.

|Code|Subcode|Opis
|-|-|-
|Serwer Serwera|UnexpectedError (Nieoczekiwanyeror)<br/>Źródło zasobów<br/>Nienałożony|Bing zwraca ServerError zawsze, gdy wystąpi którykolwiek z warunków podkodu. Odpowiedź zawiera te błędy, jeśli kod stanu HTTP wynosi 500.
|Prośba o unieważnienie|Odsuwanie parametrów<br/>Wartość parametruInvalidValue<br/>HttpNotallowed (Nieuwolna)<br/>Zablokowane|Bing zwraca InvalidRequest, gdy jakakolwiek część żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>Jeśli błąd to ParameterMissing lub ParameterInvalidValue, kod stanu HTTP wynosi 400.<br/><br/>Jeśli błąd jest HttpNotAllowed, kod stanu HTTP 410.
|RateLimitExceeded (RateLimitExceeded)||Bing zwraca RateLimitExceeded zawsze, gdy przekraczasz zapytania na sekundę (QPS) lub zapytania miesięcznie (QPM) przydział.<br/><br/>Bing zwraca kod stanu HTTP 429, jeśli przekroczono QPS i 403, jeśli przekroczono QPM.
|InvalidAuthorization (In invalidAuthorization)|AuthorizationMissing<br/>AutoryzacjaRedundancy|Bing zwraca InvalidAuthorization, gdy bing nie może uwierzytelnić wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje, jeśli określisz więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli błąd to InvalidAuthorization, kod stanu HTTP to 401.
|Niewystarczająca autoryzacja|AuthorizationDisabled (Nie można wyjawić)<br/>AuthorizationExpired (Brak uprawnień)|Bing zwraca InsufficientAuthorization, gdy wywołujący nie ma uprawnień dostępu do zasobu. Taka możliwość może wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli błąd jest InsufficientAuthorization, kod stanu HTTP jest 403.

- Poniżej przedstawiono mapy poprzednich kodów błędów do nowych kodów. Jeśli została podjęta zależność od kodów błędów v5, należy odpowiednio zaktualizować kod.

|Kod w wersji 5|Wersja 7 code.subCode
|-|-
|RequestParameterMissing|Nieprawidłowyrequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|Niewystarczająca autoryzacja
Przekroczonawolienie|RateLimitExceeded (RateLimitExceeded)
ExceededQpsLimit|RateLimitExceeded (RateLimitExceeded)
Disabled (Wyłączony)|InsufficientAuthorization.AuthorizationWydanie
UnexpectedError (Nieoczekiwanyeror)|SerwerError.UnexpectedError
Źródła danychErrory|SerwerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotallowed (Nieuwolna)|InvalidRequest.httpNie wolno
UserAgentMissing|Nieprawidłowyrequest.ParameterMissing
Nienałożony|ServerError.NotImplemented (Niewlegiętym)
InvalidAuthorization (In invalidAuthorization)|InvalidAuthorization (In invalidAuthorization)
Nieprawidłowametoda autoryzacji|InvalidAuthorization (In invalidAuthorization)
WieleautoryzacjiMetoda|InvalidAuthorization.AuthorizationRedundancy InvalidAuthorization.AuthorizationRedundancy InvalidAuthorization.AuthorizationRedundancy Invalid
Wygasła autoryzacjaPokajła|InsufficientAuthorization.AuthorizationExpired
InsufficientScope (niewystarczające skos|Niewystarczająca autoryzacja
Zablokowane|InvalidRequest.Blocked (InvalidRequest.Blocked)



### <a name="query-parameters"></a>Parametry zapytania

- Zmieniono `modulesRequested` nazwę parametru kwerendy na [moduły](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Zmieniono nazwę na Adnotacje na Znaczniki. Zobacz [moduły kwerendy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) parametr tagów.  

- Zmieniono listę obsługiwanych rynków wartości filtru ShoppingSources tylko na pl-US. Zobacz [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Zmiany w szczegółowych statystykach obrazu

- Zmieniono `annotations` nazwę pola [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) na `imageTags`.  

- Zmieniono `AnnotationModule` nazwę obiektu na [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- Zmieniono `Annotation` nazwę obiektu na [Znacznik](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag)i usunięto `confidence` pole.  

- Zmieniono `insightsSourcesSummary` nazwę pola obiektu `insightsMetadata` [Obraz](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) na .  

- Zmieniono `InsightsSourcesSummary` nazwę obiektu na [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- Dodano `https://api.cognitive.microsoft.com/bing/v7.0/images/details` punkt końcowy. Ten punkt końcowy służy do żądania szczegółowych informacji o obrazie zamiast punktu końcowego /images/search. Zobacz [Szczegółowe informacje o obrazie](./image-insights.md).

- Następujące parametry kwerendy są teraz `/images/details` prawidłowe tylko z punktem końcowym.  

    -   [insightsToken (właso)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [Moduły](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl ( imgUrl )](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [Kabiny](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [cal (cal)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [Cat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [Ct](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Zmieniono `ImageInsightsResponse` nazwę obiektu na [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- Zmieniono typy danych następujących pól w obiekcie [ImageInsights.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights)  

    -   Zmieniono typ `relatedCollections` pola `ImageGallery[]` z [na RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Zmieniono typ `pagesIncluding` pola `Image[]` z [na ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Zmieniono typ `relatedSearches` pola `Query[]` z [na RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Zmieniono typ `recipes` pola `Recipe[]` z [na RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Zmieniono typ `visuallySimilarImages` pola `Image[]` z [na ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Zmieniono typ `visuallySimilarProducts` pola `ProductSummaryImage[]` z [na ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Usunięto `ProductSummaryImage` obiekt i przeniesiono pola związane z produktem do obiektu [Obraz.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) Obiekt `Image` zawiera pola związane z produktem tylko wtedy, gdy obraz jest uwzględniony jako część wizualnie podobnych produktów w odpowiedzi wglądu obrazu.  

    -   Zmieniono typ `recognizedEntityGroups` pola `RecognizedEntityGroup[]` z [rozpoznanego](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule)trybu .  

-   Zmieniono `categoryClassification` nazwę pola [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) obrazów `annotations`na , i `AnnotationsModule`zmieniono jego typ na .  

### <a name="images-answer"></a>Odpowiedź na obrazy

-   Usunięto displayShoppingSourcesBadges i displayRecipeSourcesBadges pola z [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   Zmieniono `nextOffsetAddCount` nazwę pola `nextOffset`Obrazy [na](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) . Zmieniono również sposób użycia odsunięcia. Wcześniej parametr kwerendy [odsunięcia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) został ustawiony na `nextOffsetAddCount` wartość powiększoną o poprzednią wartość przesunięcia powiększoną o liczbę obrazów w wyniku. Teraz ustaw `offset` wartość. `nextOffset`  


## <a name="non-breaking-changes"></a>Zmiany nierozłamane

### <a name="query-parameters"></a>Parametry zapytania

- Dodano przezroczystą jako możliwą wartość filtru [obrazuTyp.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) Filtr Przezroczysty zwraca tylko obrazy z przezroczystym tłem.

- Dodano dowolną jako możliwą wartość [filtru licencji.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) Filtr Dowolna zwraca tylko obrazy, które są objęte licencją.

- Dodano parametry zapytania [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) i [minFileSize.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) Użyj tych filtrów, aby zwrócić obrazy w zakresie rozmiarów plików.  

- Dodano parametry zapytania [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth), [minWidth.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) Te filtry umożliwia zwracanie obrazów w zakresie wysokości i szerokości.  

### <a name="object-changes"></a>Zmiany obiektu

- Dodano `description` pola `lastUpdated` i pola do obiektu [Oferta.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer)  

- Dodano `name` to pole do obiektu [ImageGallery.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery)  

- Dodano `similarTerms` do obiektu [Obrazy.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) To pole zawiera listę terminów, które są podobne pod względem znaczenia do ciągu zapytania użytkownika.  
