---
title: Uaktualnienie wersji interfejsu API w wersji 5 do 7 — interfejs API wyszukiwania w sieci Web Bing
titleSuffix: Azure Cognitive Services
description: Określ aktualizacje przy użyciu interfejsów API wyszukiwania w Internecie Bing w wersji 7 części, które wymagają Twojej aplikacji.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: reference
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: e3d78a1b7488e7489b02e34e9733a5d741213855
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66384890"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Uaktualnienia z usługi Bing sieci Web interfejsu API wyszukiwania w wersji 5 do wersji 7

Ten przewodnik uaktualniania identyfikuje zmiany między w wersji 5 i API wyszukiwania w Internecie Bing w wersji 7. Użyj tego przewodnika, aby pomóc w zidentyfikowaniu części Twojej aplikacji, należy zaktualizować do używania w wersji 7.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego zmienił się z 5 w wersji 7. Na przykład https:\/\/api.cognitive.microsoft.com/bing/**v7.0**  /wyszukiwania.

### <a name="error-response-objects-and-error-codes"></a>Błąd odpowiedzi obiektów i kody błędów

- Wszystkie żądania zakończone niepowodzeniem powinny znajdować się teraz `ErrorResponse` obiektu w treści odpowiedzi.

- Dodano następujące pola do `Error` obiektu.  
  - `subCode`&mdash;Dzieli na partycje kod błędu w przedziały dyskretnych, jeśli jest to możliwe
  - `moreDetails`&mdash;Dodatkowe informacje o błędzie opisany w `message` pola


- Zastąpione następujące możliwe kody błędów w wersji 5 `code` i `subCode` wartości.

|Kod|Podrzędnego|Opis
|-|-|-
|Błąd ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Wyszukiwarka Bing zwróci błąd ServerError w każdym przypadku, gdy wystąpi którykolwiek z warunków podkodem. Odpowiedź będzie zawierać te błędy, jeśli ma wartość Kod stanu HTTP 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowane|Wyszukiwarka Bing zwróci InvalidRequest zawsze wtedy, gdy dowolnej części żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>W przypadku ParameterMissing lub ParameterInvalidValue błędu 400 jest kod stanu HTTP.<br/><br/>Jeśli ten błąd jest HttpNotAllowed, kod stanu HTTP 410.
|RateLimitExceeded||Wyszukiwarka Bing zwróci RateLimitExceeded zawsze wtedy, gdy przekracza z zapytań na sekundę (QPS) lub zapytania na miesiąc (QPM) limitu przydziału.<br/><br/>Wyszukiwarka Bing zwróci kod stanu HTTP 429 w przypadku przekroczenia liczby zapytań na Sekundę i 403 przekroczeniu QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Wyszukiwarka Bing zwróci InvalidAuthorization, kiedy Bing nie może uwierzytelnić obiektu wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje w przypadku określenia więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli ten błąd jest InvalidAuthorization, kod stanu HTTP jest 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Wyszukiwarka Bing zwróci InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Ten błąd może wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli ten błąd jest InsufficientAuthorization, kod stanu HTTP jest 403.

- Następujące mapuje poprzednie kody błędów nowe kody. Jeśli zależność wykonanych o kodach błędów w wersji 5, należy odpowiednio zaktualizować swój kod.

|Kod wersji 5|Code.subCode w wersji 7
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Wyłączone|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Zablokowane|InvalidRequest.Blocked


## <a name="non-breaking-changes"></a>Inne niż fundamentalne zmiany  

### <a name="headers"></a>Nagłówki

- Dodano opcjonalny [Pragma](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) nagłówek żądania. Usługa Bing domyślnie zwraca zawartość buforowaną, jeśli jest dostępna. Aby zapobiec zwracaniu zawartości buforowanej przez usługę Bing, dla nagłówka Pragma ustaw wartość no-cache (na przykład Pragma: no-cache).

### <a name="query-parameters"></a>Parametry zapytania

- Dodano [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) parametr zapytania. Użyj tego parametru, aby określić liczbę odpowiedzi, które mają odpowiedzi do uwzględnienia. Odpowiedzi są wybierane w oparciu o klasyfikacji. Na przykład, jeśli ten parametr jest ustawiony na trzech (3), odpowiedź zawiera trzy najważniejsze odpowiedzi rangi.  

- Dodano [podwyższanie poziomu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) parametr zapytania. Użyj tego parametru, wraz z `answerCount` do jawnie dołączyć znak jeden lub więcej typów odpowiedzi, niezależnie od ich klasyfikacji. Na przykład, aby wspierać filmów wideo i obrazy do odpowiedzi, należy ustawić Podwyższ do poziomu *filmów wideo, obrazów*. Lista odpowiedzi, które chcesz podwyższyć nie wliczają `answerCount` limit. Na przykład jeśli `answerCount` 2 i `promote` ustawiono *filmów wideo, obrazów*, odpowiedź może zawierać stron sieci Web, wiadomości, wideo i obrazy.

### <a name="object-changes"></a>Zmiany obiektu

- Dodano `someResultsRemoved` pole [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) obiektu. Pole zawiera wartość logiczną, wskazującą, czy odpowiedź wyłączone niektóre wyniki z odpowiedzi sieci web.  
