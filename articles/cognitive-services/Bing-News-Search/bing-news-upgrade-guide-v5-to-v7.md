---
title: Uaktualnij wyszukiwania wiadomości Bing interfejsu API do 7 w wersji 5 | Dokumentacja firmy Microsoft
description: Identyfikuje części aplikacji, który należy zaktualizować w wersji 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 5334C475-4841-4736-A66E-DC1E07CBCEC9
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: baed6f0091ddad40b4802c0fb52dc2ca1818cd03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347125"
---
# <a name="news-search-api-upgrade-guide"></a>Przewodnik uaktualnienia API wyszukiwania wiadomości

Ten przewodnik uaktualniania identyfikuje zmiany między w wersji 5 i w wersji 7 interfejsu API wyszukiwania usługi Bing wiadomości. Użyj tego przewodnika, aby zidentyfikować części aplikacji, który należy zaktualizować w wersji 7.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego zmieniła się z v5 w wersji 7. Na przykład https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/news/search.

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

### <a name="object-changes"></a>Zmiany obiektu

- Dodaje `contractualRules` do [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) obiektu. `contractualRules` Pole zawiera listę reguł, które należy wykonać, (na przykład autorstwa artykułu). Należy najpierw zastosować autorstwa w `contractualRules` zamiast `provider`. Artykuł zawiera `contractualRules` tylko wtedy, gdy [interfejsu API sieci Web wyszukiwania](../bing-web-search/search-the-web.md) odpowiedzi zawiera wiadomości odpowiedzi.

## <a name="non-breaking-changes"></a>Zmiany nierozdzielające

### <a name="query-parameters"></a>Parametry zapytania

- Dodaje produktów jako możliwą wartość, które można ustawić [kategorii](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) parametr do zapytania. Zobacz [kategoriami rynkach](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#categories-by-market).  
    
- Dodaje [SortBy](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortby) parametru zapytania, która zwraca trendów tematy sortowane według daty z najnowszych pierwszy.  
  
- Dodaje [ponieważ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#since) parametru zapytania, która zwraca trendów tematów, które zostały wykryte przez Bing na lub po określonej sygnatury czasowej epoki systemu Unix.

### <a name="object-changes"></a>Zmiany obiektu

- Dodaje `mentions` do [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) obiektu. `mentions` Pole zawiera listę jednostek (osób lub miejsca), które zostały odnalezione w artykule.  
  
- Dodaje `video` do [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) obiektu. `video` Pole zawiera wideo dotyczące nowości. Wideo jest \<iframe\> który można osadzić lub miniaturę ruchu.   
  
- Dodaje `sort` do [wiadomości](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news) obiektu. `sort` Pole zawiera kolejność sortowania artykułów. Na przykład artykułów są sortowane według istotność (ustawienie domyślne) lub daty.  
  
- Dodaje [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortvalue) obiektu, który definiuje kolejność sortowania. `isSelected` Pola wskazuje, czy odpowiedź używane kolejności sortowania. Jeśli **true**, kolejność sortowania użyć odpowiedzi. Jeśli `isSelected` jest **false**, można użyć adresu URL na liście `url` pola, aby zażądać porządek sortowania.
