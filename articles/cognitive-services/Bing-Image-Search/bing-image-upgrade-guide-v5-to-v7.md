---
title: Uaktualnienia z usługi Bing do wersji 7 w wersji 5 interfejsu API wyszukiwania obrazów
titleSuffix: Azure Cognitive Services
description: Ten przewodnik uaktualniania zawiera opis zmian między wersjami w wersji 5 i interfejsu API wyszukiwania obrazów Bing w wersji 7. Użyj tego przewodnika, aby pomóc w zidentyfikowaniu części Twojej aplikacji, należy zaktualizować do używania w wersji 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 123c5556dc76b35cf4a6b4b34e0c3e2fe437cebe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635571"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Podręcznik uaktualnienia interfejsu API wyszukiwania obrazów Bing w wersji 7

Ten przewodnik uaktualniania identyfikuje zmiany między w wersji 5 i interfejsu API wyszukiwania obrazów Bing w wersji 7. Użyj tego przewodnika, aby pomóc w zidentyfikowaniu części Twojej aplikacji, należy zaktualizować do używania w wersji 7.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego zmieniła się z 5 w wersji 7. Na przykład https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search.

### <a name="error-response-objects-and-error-codes"></a>Błąd odpowiedzi obiektów i kody błędów

- Wszystkie żądania zakończone niepowodzeniem powinny znajdować się teraz `ErrorResponse` obiektu w treści odpowiedzi.

- Dodano następujące pola do `Error` obiektu.  
  - `subCode`&mdash;Dzieli na partycje kod błędu w przedziały dyskretnych, jeśli jest to możliwe
  - `moreDetails`&mdash;Dodatkowe informacje o błędzie opisany w `message` pola


- Zastąpione następujące możliwe kody błędów w wersji 5 `code` i `subCode` wartości.

|Kod|Podrzędnego|Opis
|-|-|-
|Błąd ServerError|UnexpectedError<br/>ResourceError<br/>Nie zaimplementowano|Wyszukiwarka Bing zwróci błąd ServerError w każdym przypadku, gdy wystąpi którykolwiek z warunków podkodem. Odpowiedź zawiera te błędy, jeśli kod stanu HTTP 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowany|Wyszukiwarka Bing zwróci InvalidRequest zawsze wtedy, gdy dowolnej części żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>W przypadku ParameterMissing lub ParameterInvalidValue błędu 400 jest kod stanu HTTP.<br/><br/>Jeśli ten błąd jest HttpNotAllowed, kod stanu HTTP 410.
|RateLimitExceeded||Wyszukiwarka Bing zwróci RateLimitExceeded zawsze wtedy, gdy przekracza z zapytań na sekundę (QPS) lub zapytania na miesiąc (QPM) limitu przydziału.<br/><br/>Wyszukiwarka Bing zwróci kod stanu HTTP 429 w przypadku przekroczenia liczby zapytań na Sekundę i 403 przekroczeniu QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Wyszukiwarka Bing zwróci InvalidAuthorization, kiedy Bing nie może uwierzytelnić obiektu wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje w przypadku określenia więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli ten błąd jest InvalidAuthorization, kod stanu HTTP jest 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Wyszukiwarka Bing zwróci InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Może to wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli ten błąd jest InsufficientAuthorization, kod stanu HTTP jest 403.

- Następujące mapuje poprzednie kody błędów nowe kody. Jeśli zależność wykonanych o kodach błędów w wersji 5, należy odpowiednio zaktualizować swój kod.

|Kod wersji 5|Code.subCode w wersji 7
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Disabled (Wyłączony)|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Nie zaimplementowano|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Zablokowany|InvalidRequest.Blocked



### <a name="query-parameters"></a>Parametry zapytania

- Zmieniono nazwę `modulesRequested` parametr do zapytania [modułów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).  

- Zmieniono nazwę adnotacje do znaczników. Zobacz [modułów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) parametr do tagów zapytania.  

- Zmienić listę obsługiwane rynki wartość filtru ShoppingSources en-us. Zobacz [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Obraz insights zmiany

- Zmieniono nazwę `annotations` pole [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) do `imageTags`.  

- Zmieniono nazwę `AnnotationModule` obiekt [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule).  

- Zmieniono nazwę `Annotation` obiekt [Tag](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag)i usuwane `confidence` pola.  

- Zmieniono nazwę `insightsSourcesSummary` pole [obraz](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) obiekt `insightsMetadata`.  

- Zmieniono nazwę `InsightsSourcesSummary` obiekt [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata).  

- Dodano `https://api.cognitive.microsoft.com/bing/v7.0/images/details` punktu końcowego. Użyj tego punktu końcowego do żądania szczegółowe informacje o obrazach zamiast punkt końcowy wyszukiwania/obrazy /. Zobacz [Insights obrazu](./image-insights.md).

- Następujące parametry zapytania są teraz prawidłowe tylko w przypadku `/images/details` punktu końcowego.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [Moduły](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [Ct](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  

- Zmieniono nazwę `ImageInsightsResponse` obiekt [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  

- Zmieniła typy danych pola w [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights) obiektu.  

    -   Zmieniono typu `relatedCollections` pola z `ImageGallery[]` do [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Zmieniono typu `pagesIncluding` pola z `Image[]` do [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Zmieniono typu `relatedSearches` pola z `Query[]` do [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Zmieniono typu `recipes` pola z `Recipe[]` do [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule).  

    -   Zmieniono typu `visuallySimilarImages` pola z `Image[]` do [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Zmieniono typu `visuallySimilarProducts` pola z `ProductSummaryImage[]` do [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Usunięte `ProductSummaryImage` obiektu i przenieść pola związane z produktem do [obraz](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) obiektu. `Image` Obiekt zawiera pola związane z produktem, tylko wtedy, gdy obraz, który wchodzi w skład wizualnie podobnych produktów w odpowiedzi szczegółowe informacje o obrazie.  

    -   Zmieniono typu `recognizedEntityGroups` pola z `RecognizedEntityGroup[]` do [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Zmieniono nazwę `categoryClassification` pole [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) do `annotations`i zmienić jej typ `AnnotationsModule`.  

### <a name="images-answer"></a>Obrazy odpowiedzi

-   Usunięte pola displayShoppingSourcesBadges i displayRecipeSourcesBadges z [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images).  

-   Zmieniono nazwę `nextOffsetAddCount` pole [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) do `nextOffset`. Sposób użycia przesunięcie został zmieniony. Wcześniej ustaw [przesunięcie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) parametr do zapytania `nextOffsetAddCount` wartości oraz poprzedniej wartości przesunięcia oraz liczbę obrazów w wyniku. Teraz ustaw `offset` do `nextOffset` wartości.  


## <a name="non-breaking-changes"></a>Inne niż fundamentalne zmiany

### <a name="query-parameters"></a>Parametry zapytania

- Dodano przezroczysty możliwie [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) wartość filtru. Przezroczysty filtr zwraca tylko obrazy z przezroczystym tłem.

- Dodaje wszystkie możliwie [licencji](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) wartość filtru. Dowolny filtr zwraca tylko obrazy, które są objęte licencji.

- Dodano [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) i [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) parametry zapytania. Użyj tych filtrów, aby zwrócić obrazów w zakresie rozmiarów plików.  

- Dodano [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth), [wartości elementu minWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth) parametry zapytania. Użyj tych filtrów, aby zwrócić obrazów w zakresie wysokości i szerokości.  

### <a name="object-changes"></a>Zmiany obiektu

- Dodano `description` i `lastUpdated` polom [oferują](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer) obiektu.  

- Dodano `name` pole [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery) obiektu.  

- Dodano `similarTerms` do [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) obiektu. To pole zawiera listę warunków, które są podobne znaczenie dla użytkownika ciąg zapytania.  
