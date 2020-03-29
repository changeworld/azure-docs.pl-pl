---
title: Uaktualnienie z interfejsu API w wersji 5 do wersji 7 — interfejs API wyszukiwania w sieci Web usługi Bing
titleSuffix: Azure Cognitive Services
description: Określ, które części aplikacji wymagają aktualizacji, aby używać interfejsów API usługi Bing Web Search w wersji 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 2133cd59c524112ae8a77c0a20cbce1d1336a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881303"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Uaktualnienie z interfejsu API wyszukiwania w sieci Web usługi Bing w wersji 5 do wersji 7

Ten przewodnik uaktualnienia identyfikuje zmiany między wersją 5 i wersją 7 interfejsu API wyszukiwania w sieci Web Bing. Ten przewodnik ułatwia identyfikowanie części aplikacji, które należy zaktualizować, aby korzystać z wersji 7.

## <a name="breaking-changes"></a>Fundamentalne zmiany

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego został zmieniony z v5 na v7. Na przykład https:\/\/api.cognitive.microsoft.com/bing/**v7.0**/search.

### <a name="error-response-objects-and-error-codes"></a>Obiekty odpowiedzi na błędy i kody błędów

- Wszystkie żądania nie powiodło `ErrorResponse` się powinny teraz zawierać obiekt w treści odpowiedzi.

- Dodano następujące pola `Error` do obiektu.  
  - `subCode`&mdash;Dzieli kod błędu na dyskretne zasobniki, jeśli to możliwe
  - `moreDetails`&mdash;Dodatkowe informacje o błędzie opisanym w `message` polu


- Zastąpiono kody błędów v5 następującymi możliwymi `code` i `subCode` wartościami.

|Code|Subcode|Opis
|-|-|-
|Serwer Serwera|UnexpectedError (Nieoczekiwanyeror)<br/>Źródło zasobów<br/>Nienałożony|Bing zwraca ServerError zawsze, gdy wystąpi którykolwiek z warunków podkodu. Odpowiedź będzie zawierać te błędy, jeśli kod stanu HTTP jest 500.
|Prośba o unieważnienie|Odsuwanie parametrów<br/>Wartość parametruInvalidValue<br/>HttpNotallowed (Nieuwolna)<br/>Zablokowane|Bing zwraca InvalidRequest, gdy jakakolwiek część żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>Jeśli błąd to ParameterMissing lub ParameterInvalidValue, kod stanu HTTP wynosi 400.<br/><br/>Jeśli błąd jest HttpNotAllowed, kod stanu HTTP 410.
|RateLimitExceeded (RateLimitExceeded)||Bing zwraca RateLimitExceeded zawsze, gdy przekraczasz zapytania na sekundę (QPS) lub zapytania miesięcznie (QPM) przydział.<br/><br/>Bing zwraca kod stanu HTTP 429, jeśli przekroczono QPS i 403, jeśli przekroczono QPM.
|InvalidAuthorization (In invalidAuthorization)|AuthorizationMissing<br/>AutoryzacjaRedundancy|Bing zwraca InvalidAuthorization, gdy bing nie może uwierzytelnić wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje, jeśli określisz więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli błąd to InvalidAuthorization, kod stanu HTTP to 401.
|Niewystarczająca autoryzacja|AuthorizationDisabled (Nie można wyjawić)<br/>AuthorizationExpired (Brak uprawnień)|Bing zwraca InsufficientAuthorization, gdy wywołujący nie ma uprawnień dostępu do zasobu. Ten błąd może wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli błąd jest InsufficientAuthorization, kod stanu HTTP jest 403.

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


## <a name="non-breaking-changes"></a>Zmiany nierozłamane  

### <a name="headers"></a>Nagłówki

- Dodano opcjonalny nagłówek żądania [Pragma.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) Usługa Bing domyślnie zwraca zawartość buforowaną, jeśli jest dostępna. Aby zapobiec zwracaniu zawartości buforowanej przez usługę Bing, dla nagłówka Pragma ustaw wartość no-cache (na przykład Pragma: no-cache).

### <a name="query-parameters"></a>Parametry zapytania

- Dodano parametr zapytania [answerCount.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) Ten parametr służy do określania liczby odpowiedzi, które mają zostać uwzględnione w odpowiedzi. Odpowiedzi są wybierane na podstawie rankingu. Na przykład jeśli ustawisz ten parametr na trzy (3), odpowiedź zawiera trzy najlepsze odpowiedzi rankingowe.  

- Dodano parametr [kwerendy podwyższającej.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) Użyj tego parametru wraz z `answerCount` jawnie dołączyć jeden lub więcej typów odpowiedzi, niezależnie od ich rankingu. Na przykład, aby promować filmy i obrazy w odpowiedzi, należy ustawić promowanie *filmów, obrazów.* Lista odpowiedzi, które chcesz promować, nie jest `answerCount` wliczona do limitu. Na przykład, `answerCount` jeśli jest `promote` 2 i jest ustawiona na *filmy, obrazy*, odpowiedź może zawierać strony internetowe, wiadomości, filmy i obrazy.

### <a name="object-changes"></a>Zmiany obiektu

- Dodano `someResultsRemoved` to pole do obiektu [WebAnswer.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) To pole zawiera wartość logiczną, która wskazuje, czy odpowiedź wykluczyła niektóre wyniki z odpowiedzi sieci web.  
