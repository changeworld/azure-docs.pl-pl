---
title: Uaktualnianie interfejsu API wyszukiwania wiadomości Bing w wersji 5 do wersji 7
titleSuffix: Azure Cognitive Services
description: Identyfikuje części aplikacji, które należy zaktualizować, aby użyć wersji 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: bad0ef849af7c94e63f1dfbebda7f47caef9947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294365"
---
# <a name="news-search-api-upgrade-guide"></a>Przewodnik po uaktualnieniu interfejsu API wyszukiwania wiadomości

Ten przewodnik uaktualnienia identyfikuje zmiany między wersją 5 i wersją 7 interfejsu API wyszukiwania wiadomości Bing. Ten przewodnik ułatwia identyfikowanie części aplikacji, które należy zaktualizować, aby korzystać z wersji 7.

## <a name="breaking-changes"></a>Fundamentalne zmiany

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego zmieniono z v5 na v7. Na przykład `https://api.cognitive.microsoft.com/bing/v7.0/news/search`.

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

### <a name="object-changes"></a>Zmiany obiektu

- Dodano `contractualRules` to pole do obiektu [NewsArticle.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) To `contractualRules` pole zawiera listę reguł, które należy przestrzegać (na przykład atrybucja artykułu). Należy zastosować atrybucję `contractualRules` podana `provider`zamiast używać . Artykuł zawiera `contractualRules` tylko wtedy, gdy odpowiedź [interfejsu API wyszukiwania w sieci Web](../bing-web-search/search-the-web.md) zawiera odpowiedź wiadomości.

## <a name="non-breaking-changes"></a>Nieprzełamane zmiany

### <a name="query-parameters"></a>Parametry zapytania

- Dodano produkty jako możliwą wartość, na którą można ustawić parametr zapytania [kategorii.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) Zobacz [Kategorie według rynków](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

- Dodano parametr [SortBy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) query, który zwraca popularne tematy posortowane według daty z najnowszymi pierwszymi.

- Dodano [parametr Od](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) kwerendy, który zwraca popularne tematy, które zostały wykryte przez Bing na lub po określonej epoce Unix sygnatury czasowej.

### <a name="object-changes"></a>Zmiany obiektu

- Dodano `mentions` to pole do obiektu [NewsArticle.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) To `mentions` pole zawiera listę encji (osób lub miejsc), które zostały znalezione w artykule.

- Dodano `video` to pole do obiektu [NewsArticle.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) To `video` pole zawiera film powiązany z artykułem z wiadomościami. Film jest elementem \<iframe,\> który można osadzić, lub miniaturą ruchu.

- Dodano `sort` to pole do obiektu [Wiadomości.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) To `sort` pole zawiera kolejność sortowania artykułów. Na przykład artykuły są sortowane według istotności (domyślnie) lub daty.

- Dodano obiekt [SortValue,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) który definiuje kolejność sortowania. To `isSelected` pole wskazuje, czy w odpowiedzi użyto kolejności sortowania. Jeśli **true**, odpowiedź używane kolejność sortowania. Jeśli `isSelected` jest **false,** można użyć `url` adresu URL w polu, aby zażądać innej kolejności sortowania.
