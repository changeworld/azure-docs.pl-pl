---
title: Uaktualnianie z usługi Bing sieci Web v5 wyszukiwania interfejsu API, aby w wersji 7 | Dokumentacja firmy Microsoft
description: Identyfikuje części aplikacji, który należy zaktualizować w wersji 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 01/15/2017
ms.author: scottwhi
ms.openlocfilehash: 155297f230c0ee02d6fa49d6d35eb24d9941f29b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348972"
---
# <a name="web-search-api-upgrade-guide"></a>Przewodnik dotyczący uaktualniania wyszukiwania interfejsu API sieci Web

Ten przewodnik uaktualniania identyfikuje zmiany między w wersji 5 i w wersji 7 interfejsu API wyszukiwania usługi Bing sieci Web. Użyj tego przewodnika, aby zidentyfikować części aplikacji, który należy zaktualizować w wersji 7.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego zmieniła się z v5 w wersji 7. Na przykład https:\/\/api.cognitive.microsoft.com/bing/**v7.0**  /wyszukiwania.

### <a name="error-response-objects-and-error-codes"></a>Błąd odpowiedzi obiektów i kody błędów

- Teraz obejmuje wszystkich żądań zakończonych niepowodzeniem `ErrorResponse` obiektu w treści odpowiedzi.

- Dodano następujące pola do `Error` obiektu.  
  - `subCode`&mdash;Partycje kod błędu w odrębny zasobników, jeśli to możliwe
  - `moreDetails`&mdash;Dodatkowe informacje o błędzie opisany w `message` pola
   

- Zastąpione kody błędów v5 następujące możliwości `code` i `subCode` wartości.

|Kod|Kod podrzędny|Opis
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Nie zaimplementowano|Bing zwraca ServerError zawsze, gdy występują warunki kod podrzędny. Odpowiedź będzie zawierać te błędy, jeśli kod stanu HTTP 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowany|Bing zwraca InvalidRequest zawsze, gdy częścią żądania jest nieprawidłowy. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>Jeśli błąd jest ParameterMissing lub ParameterInvalidValue, kod stanu HTTP jest 400.<br/><br/>Jeśli błąd jest HttpNotAllowed, kod stanu HTTP 410.
|RateLimitExceeded||Bing zwraca RateLimitExceeded zawsze, gdy przekracza z zapytania na sekundę (QPS) lub zapytania na przydziału miesięcznego (QPM).<br/><br/>Bing zwraca kod stanu HTTP 429 po przekroczeniu QPM przekroczeniu QPS i 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing zwraca InvalidAuthorization, gdy obiekt wywołujący nie mogą uwierzytelnić się Bing. Na przykład `Ocp-Apim-Subscription-Key` Brak nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje, jeśli określono więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli błąd jest InvalidAuthorization, kod stanu HTTP jest 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing zwraca InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Ten błąd może wystąpić, jeśli klucz Subskrypcja została wyłączona lub jego ważność wygasła. <br/><br/>Jeśli błąd jest InsufficientAuthorization, kod stanu HTTP jest 403.

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


## <a name="non-breaking-changes"></a>Zmiany nie podziału  

### <a name="headers"></a>Nagłówki

- Dodaje opcjonalne [Pragma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#pragma) nagłówek żądania. Domyślnie Bing zwraca zawartości w pamięci podręcznej, jeśli jest dostępna. Aby zapobiec Bing zwracanie zawartości w pamięci podręcznej, ustaw nagłówek Pragma no-cache (na przykład Pragma: nie-cache).

### <a name="query-parameters"></a>Parametry zapytania

- Dodaje [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) parametr zapytania. Ten parametr umożliwia określenie liczby odpowiedź ma uwzględnić odpowiedzi. Odpowiedzi są wybierane w zależności od klasyfikacji. Na przykład, jeśli ten parametr jest ustawiony na trzech (3), odpowiedź zawiera trzy top odpowiedzi uporządkowanej według rangi.  
  
- Dodaje [wspierania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) parametr zapytania. Użyj tego parametru razem z `answerCount` jawnie uwzględnienie jeden lub więcej typów odpowiedzi, niezależnie od ich klasyfikacji. Na przykład, aby podwyższyć poziom klipów wideo i obrazów do odpowiedzi, należy ustawić podwyższanie poziomu do *wideo, obrazy*. Lista odpowiedzi, które chcesz podwyższyć nie odliczona `answerCount` limit. Na przykład jeśli `answerCount` 2 i `promote` ustawiono *wideo, obrazy*, odpowiedź może zawierać stron sieci Web, grup dyskusyjnych, wideo i obrazów.

### <a name="object-changes"></a>Zmiany obiektu

- Dodaje `someResultsRemoved` do [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) obiektu. Pole zawiera wartość logiczną wskazującą, czy odpowiedź wyłączone niektóre wyniki z odpowiedzi sieci web.  

