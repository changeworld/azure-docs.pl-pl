---
title: Uaktualnienie automatyczne sugerowanie interfejsu API w wersji 5 do wersji 7
titlesuffix: Azure Cognitive Services
description: Identyfikuje części Twojej aplikacji, należy zaktualizować do używania w wersji 7.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: scottwhi
ms.openlocfilehash: 13e1a2d574abd080b1e947896ed5c2de568fc853
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178481"
---
# <a name="autosuggest-api-upgrade-guide"></a>Automatyczne sugerowanie uaktualnienia Podręcznik interfejsu API

Ten przewodnik uaktualniania identyfikuje zmiany między w wersji 5 i interfejs API automatycznego sugerowania Bing w wersji 7. Użyj tego przewodnika, aby pomóc w zidentyfikowaniu części Twojej aplikacji, należy zaktualizować do używania w wersji 7.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego zmieniła się z 5 w wersji 7. Na przykład https://api.cognitive.microsoft.com/bing/\ * \*v7.0 ** / sugestie.

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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Use and display requirements (Wymagania dotyczące użycia i wyświetlania)](./UseAndDisplayRequirements.md)
