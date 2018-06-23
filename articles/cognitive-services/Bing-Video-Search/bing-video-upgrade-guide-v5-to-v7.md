---
title: Uaktualnij usługi Bing v5 wideo interfejsu API, aby w wersji 7 | Dokumentacja firmy Microsoft
description: Identyfikuje części aplikacji, który należy zaktualizować w wersji 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: FA7DDF07-97AC-4EBE-8C50-A9737D43B38E
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 62646d026e141d0549c68e18f9318fa32d3e00df
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347044"
---
# <a name="video-search-api-upgrade-guide"></a>Wideo przewodnik uaktualniania wyszukiwania interfejsu API

Ten przewodnik uaktualniania identyfikuje zmiany między w wersji 5 i w wersji 7 interfejsu API wyszukiwania usługi Bing wideo. Użyj tego przewodnika, aby zidentyfikować części aplikacji, który należy zaktualizować w wersji 7.

## <a name="breaking-changes"></a>Zmiany powodujące niezgodność

### <a name="endpoints"></a>Punkty końcowe

- Numer wersji punktu końcowego zmieniła się z v5 w wersji 7. Na przykład https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/videos/search.

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

- Zmieniona `modulesRequested` parametr do zapytania [modułów](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Zmiany obiektu

- Zmieniona `nextOffsetAddCount` pole [wideo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) do `nextOffset`. Sposób użycia przesunięcie został zmieniony. Wcześniej, należy ustawić [przesunięcie](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) parametr do zapytania `nextOffset` wartość plus poprzedniej wartości przesunięcia oraz liczbę plików wideo, w wyniku. Teraz, po prostu ustawić `offset` parametr do zapytania `nextOffset` wartość.  
  
- Zmienić typ danych `relatedVideos` pola `Video[]` do [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videosmodule) (zobacz [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videodetails)).

