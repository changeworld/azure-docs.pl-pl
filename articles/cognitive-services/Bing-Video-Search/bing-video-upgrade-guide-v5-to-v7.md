---
title: Uaktualnij Bing w wersji 5 interfejsu API wyszukiwania wideo do wersji 7
titlesuffix: Azure Cognitive Services
description: Identyfikuje części Twojej aplikacji, należy zaktualizować do używania w wersji 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 633981682bd8820d72a98b3fc6fbd802e0cd2afb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759898"
---
# <a name="video-search-api-upgrade-guide"></a>Przewodnik uaktualnienia z interfejsu API wyszukiwania wideo

Ten przewodnik uaktualniania identyfikuje zmiany między w wersji 5 i interfejs API wyszukiwania wideo Bing w wersji 7. Użyj tego przewodnika, aby pomóc w zidentyfikowaniu części Twojej aplikacji, należy zaktualizować do używania w wersji 7.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego zmieniła się z 5 w wersji 7. Na przykład `https://api.cognitive.microsoft.com/bing/v7.0/videos/search`.

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

- Zmieniono nazwę `modulesRequested` parametr do zapytania [modułów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Zmiany obiektu

- Zmieniono nazwę `nextOffsetAddCount` pole [wideo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) do `nextOffset`. Sposób użycia przesunięcie został zmieniony. Wcześniej, należy ustawić [przesunięcie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) parametr do zapytania `nextOffset` wartości oraz poprzedniej wartości przesunięcia plus liczba filmów wideo w wyniku. Teraz, po prostu ustaw `offset` parametr do zapytania `nextOffset` wartość.  
  
- Zmieniony typ danych `relatedVideos` pola z `Video[]` do [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videosmodule) (zobacz [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videodetails)).

