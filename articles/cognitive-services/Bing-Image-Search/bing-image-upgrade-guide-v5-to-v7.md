---
title: Uaktualnij usługi Bing obrazu v5 wyszukiwania interfejsu API, aby w wersji 7 | Dokumentacja firmy Microsoft
description: Identyfikuje części aplikacji, który należy zaktualizować w wersji 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b4d785eafe9ced6fb12e2dac3282dfd286849eb6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347169"
---
# <a name="image-search-api-upgrade-guide"></a>Przewodnik uaktualnienia obrazu wyszukiwania interfejsu API

Ten przewodnik uaktualniania identyfikuje zmiany między w wersji 5 i w wersji 7 interfejsu API wyszukiwania usługi Bing obrazu. Użyj tego przewodnika, aby zidentyfikować części aplikacji, który należy zaktualizować w wersji 7.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego zmieniła się z v5 w wersji 7. Na przykład https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/images/search.

### <a name="error-response-objects-and-error-codes"></a>Błąd odpowiedzi obiektów i kody błędów

- Teraz obejmuje wszystkich żądań zakończonych niepowodzeniem `ErrorResponse` obiektu w treści odpowiedzi.

- Dodano następujące pola do `Error` obiektu.  
  - `subCode`&mdash;Partycje kod błędu w odrębny zasobników, jeśli to możliwe
  - `moreDetails`&mdash;Dodatkowe informacje o błędzie opisany w `message` pola
   

- Zastąpione kody błędów v5 następujące możliwości `code` i `subCode` wartości.

|Kod|Kod podrzędny|Opis
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Nie zaimplementowano|Bing zwraca ServerError zawsze, gdy występują warunki kod podrzędny. Odpowiedź zawiera te błędy, jeśli kod stanu HTTP 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowany|Bing zwraca InvalidRequest zawsze, gdy częścią żądania jest nieprawidłowy. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>Jeśli błąd jest ParameterMissing lub ParameterInvalidValue, kod stanu HTTP jest 400.<br/><br/>Jeśli błąd jest HttpNotAllowed, kod stanu HTTP 410.
|RateLimitExceeded||Bing zwraca RateLimitExceeded zawsze, gdy przekracza z zapytania na sekundę (QPS) lub zapytania na przydziału miesięcznego (QPM).<br/><br/>Bing zwraca kod stanu HTTP 429 po przekroczeniu QPM przekroczeniu QPS i 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing zwraca InvalidAuthorization, gdy obiekt wywołujący nie mogą uwierzytelnić się Bing. Na przykład `Ocp-Apim-Subscription-Key` Brak nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje, jeśli określono więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli błąd jest InvalidAuthorization, kod stanu HTTP jest 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing zwraca InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Może to wystąpić, jeśli klucz Subskrypcja została wyłączona lub jego ważność wygasła. <br/><br/>Jeśli błąd jest InsufficientAuthorization, kod stanu HTTP jest 403.

- Następujące mapuje poprzednie kody błędów nowe kody. Jeśli zależność wykonałeś kodów błędów v5, zaktualizuj odpowiednio kod.

|Kod w wersji 5|Code.subCode w wersji 7
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

- Zmieniona `modulesRequested` parametr do zapytania [modułów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules).  
  
- Zmieniona adnotacje do tagów. Zobacz [modułów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules) parametr do tagów zapytania.  

- Zmienić listę obsługiwanych rynkach wartość filtru ShoppingSources en-us. Zobacz [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype).  
 

### <a name="image-insights-changes"></a>Zmiany insights obrazu
  
- Zmieniona `annotations` pole [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) do `imageTags`.  
  
- Zmieniona `AnnotationModule` do obiektu [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule).  
  
- Zmieniona `Annotation` do obiektu [Tag](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag)i usuwane `confidence` pola.  
  
- Zmieniona `insightsSourcesSummary` pole [obrazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) do obiektu `insightsMetadata`.  
  
- Zmieniona `InsightsSourcesSummary` do obiektu [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata).  
  
- Dodaje `https://api.cognitive.microsoft.com/bing/v7.0/images/details` punktu końcowego. Używać tego punktu końcowego do szczegółowych danych obrazu żądania zamiast / obrazów/wyszukiwania punktu końcowego. Zobacz [obrazu Insights](./image-insights.md). 
  
- Następujące parametry zapytania są teraz prawidłowe tylko w przypadku `/images/details` punktu końcowego.  
  
    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [Moduły](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [plik cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [CAL](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [samochodu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [CAT](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [ierz](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  
  
- Zmieniona `ImageInsightsResponse` do obiektu [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  
  
- Zmienić typ danych pola w [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights) obiektu.  
  
    -   Zmieniono typu `relatedCollections` pola `ImageGallery[]` do [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule).  
  
    -   Zmieniono typu `pagesIncluding` pola `Image[]` do [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Zmieniono typu `relatedSearches` pola `Query[]` do [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule).  
  
    -   Zmieniono typu `recipes` pola `Recipe[]` do [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule).  
  
    -   Zmieniono typu `visuallySimilarImages` pola `Image[]` do [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Zmieniono typu `visuallySimilarProducts` pola `ProductSummaryImage[]` do [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Usunięte `ProductSummaryImage` obiektu i przenieść pól dotyczących produktów do [obrazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) obiektu. `Image` Obiektu zawiera pola dotyczących produktów, tylko wtedy, gdy obraz wchodzi w skład wizualnie podobnych produktów w odpowiedzi szczegółowe informacje o obrazie.  
  
    -   Zmieniono typu `recognizedEntityGroups` pola `RecognizedEntityGroup[]` do [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule).  
  
-   Zmieniona `categoryClassification` pole [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) do `annotations`i zmienić jej typu na `AnnotationsModule`.  

### <a name="images-answer"></a>Obrazy odpowiedzi

-   Usunąć pola displayShoppingSourcesBadges i displayRecipeSourcesBadges [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images).  
  
-   Zmieniona `nextOffsetAddCount` pole [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) do `nextOffset`. Sposób użycia przesunięcie został zmieniony. Wcześniej, ustaw [przesunięcie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) parametr do zapytania `nextOffsetAddCount` wartość plus poprzedniej wartości przesunięcia oraz liczbę obrazów w wyniku. Teraz, możesz ustawić `offset` do `nextOffset` wartości.  
    
  
## <a name="non-breaking-changes"></a>Zmiany nie podziału

### <a name="query-parameters"></a>Parametry zapytania
  
- Dodaje przezroczysty jako potencjalnie [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) wartość filtru. Przezroczysty filtr zwraca tylko obrazy z przeźroczystym tłem.

- Dodano żadnych serwerów, jak to możliwe [licencji](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) wartość filtru. Dowolny filtr zwraca tylko te obrazy, które są w ramach licencji.
  
- Dodaje [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) i [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) parametry zapytania. Użyj tych filtrów, aby zwrócić obrazów w zakresie rozmiary plików.  
  
- Dodaje [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth), [wartości elementu minWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth) parametry zapytania. Użyj tych filtrów, aby zwrócić obrazów w zakresie wysokości i szerokości.  

### <a name="object-changes"></a>Zmiany obiektu
  
- Dodaje `description` i `lastUpdated` pól do [oferują](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer) obiektu.  
  
- Dodaje `name` do [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery) obiektu.  
  
- Dodaje `similarTerms` do [obrazów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) obiektu. To pole zawiera listę warunków, które są podobne znaczenie na ciąg zapytania użytkownika.  
