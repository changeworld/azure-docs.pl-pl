---
title: Uaktualnij interfejs API wyszukiwania wiadomości Bing w wersji 5 do wersji 7
titlesuffix: Azure Cognitive Services
description: Identyfikuje części Twojej aplikacji, należy zaktualizować do używania w wersji 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 04c457fba5cb32cc1312ffac2c2f7c1470b5a46b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519014"
---
# <a name="news-search-api-upgrade-guide"></a>Podręcznik uaktualnienia interfejsu API wyszukiwania wiadomości

Ten przewodnik uaktualniania identyfikuje zmiany między w wersji 5 i interfejsu API wyszukiwania wiadomości Bing w wersji 7. Użyj tego przewodnika, aby pomóc w zidentyfikowaniu części Twojej aplikacji, należy zaktualizować do używania w wersji 7.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego zmieniła się z 5 w wersji 7. Na przykład https://api.cognitive.microsoft.com/bing/ **v7.0**  /wiadomości/wyszukiwania.

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

### <a name="object-changes"></a>Zmiany obiektu

- Dodano `contractualRules` pole [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) obiektu. `contractualRules` Pole zawiera listę reguł, które należy wykonać, (na przykład: uznanie autorstwa artykułu). Należy najpierw zastosować autorstwa podawany `contractualRules` zamiast `provider`. Artykuł zawiera `contractualRules` tylko wtedy, gdy [API wyszukiwania w Internecie](../bing-web-search/search-the-web.md) odpowiedź zawiera odpowiedź wiadomości.

## <a name="non-breaking-changes"></a>Zmian niepowodujących niezgodności

### <a name="query-parameters"></a>Parametry zapytania

- Dodaje produktów jako możliwej wartości, której możesz ustawić [kategorii](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) parametr do zapytania. Zobacz [kategorii według rynków](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).

- Dodano [sortby —](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortby) parametr zapytania, który zwraca popularne tematy, posortowane według daty przy użyciu najnowsze na początku.

- Dodano [ponieważ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#since) parametr zapytania, które zwraca popularne tematy, które zostały odnalezione przez usługę Bing na lub po określonej sygnatura czasowa epoki systemu Unix.

### <a name="object-changes"></a>Zmiany obiektu

- Dodano `mentions` pole [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) obiektu. `mentions` Pole zawiera listę jednostek (osoby lub miejsca), które zostały znalezione w artykule.

- Dodano `video` pole [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) obiektu. `video` Pole zawiera film wideo, który jest powiązany z artykułu z wiadomościami. To wideo \<iframe\> , możesz osadzić lub miniaturę ruchu.

- Dodano `sort` pole [wiadomości](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news) obiektu. `sort` Pole zawiera kolejność sortowania artykułów. Na przykład artykuły są sortowane według istotności (ustawienie domyślne) lub daty.

- Dodano [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortvalue) obiektu, który definiuje kolejność sortowania. `isSelected` Pole wskazuje, czy odpowiedź używany porządek sortowania. Jeśli **true**, odpowiedź używane porządek sortowania. Jeśli `isSelected` jest **false**, można użyć adresu URL w `url` pola, aby zażądać porządek sortowania.
