---
title: Wyszukiwanie w sieci Web Bing interfejsu API na platformie Azure — często zadawane pytania (FAQ) | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące programu Microsoft kognitywnych usług Bing wyszukiwania interfejsu API sieci Web na platformie Azure.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 321f571c48f2231d1ced43848cdefd17adaa1a08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347065"
---
# <a name="frequently-asked-questions-faq-about-bing-web-search-api-cognitive-services"></a>Często zadawane pytania (FAQ) dotyczące API wyszukiwania usługi Bing sieci Web (kognitywnych usługi)
 
 Odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariusze związane z interfejsu API wyszukiwania usługi Bing sieci Web dla kognitywnych usług Microsoft Azure.

## <a name="response-headers-in-javascript"></a>Nagłówki odpowiedzi w języku JavaScript

W odpowiedzi z interfejsu API wyszukiwania usługi Bing sieci Web mogą występować następujące nagłówki.

|||
|-|-|
|`X-MSEdge-ClientID`|Unikatowy identyfikator, który Bing przypisana do użytkownika|
|`BingAPIs-Market`|Rynku, które zostało użyte do wykonania żądania|
|`BingAPIs-TraceId`|Wpis dziennika na serwerze interfejsu API Bing dla tego żądania (dla pomocy technicznej)|

Jest to szczególnie ważne utrwalić identyfikator klienta i przywrócić go z kolejnych żądań. Po wykonaniu tej czynności wyszukiwania korzystania z wcześniejszych kontekstu w klasyfikacji wyniki wyszukiwania, a także zapewnić spójne środowisko.

Jednak po wywołaniu interfejsu API wyszukiwania usługi Bing sieci Web z poziomu języka JavaScript, funkcje zabezpieczeń w przeglądarce (CORS) może uniemożliwić dostęp do wartości tych nagłówków.

Aby uzyskać dostęp do nagłówków, można wykonać żądania interfejsu API Bing sieci Web wyszukiwania przez serwer proxy CORS. Odpowiedź z serwera proxy ma `Access-Control-Expose-Headers` Nagłówek tego whitelists nagłówki odpowiedzi i udostępnia je JavaScript.

Łatwo zainstalować serwer proxy CORS, aby umożliwić naszych [samouczek aplikacji](tutorial-bing-web-search-single-page-app.md) dostępu nagłówki opcjonalne klienta do. Pierwsza strona, jeśli nie masz jeszcze, [instalowania programu Node.js](https://nodejs.org/en/download/). Następnie wprowadź następujące polecenie w wierszu polecenia.

    npm install -g cors-proxy-server

Następnie można zmienić punktu końcowego interfejsu API Bing sieci Web wyszukiwania w pliku w formacie HTML do:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Na koniec uruchom CORS proxy za pomocą następującego polecenia:

    cors-proxy-server

Pozostaw otwarte okno wiersza poleceń podczas korzystania z samouczka aplikacji; Zamyka okno zatrzymuje serwer proxy. W sekcji nagłówków HTTP rozwijanych poniżej wyniki wyszukiwania, możesz teraz przeglądać `X-MSEdge-ClientID` nagłówka (między innymi) i sprawdź, czy jest taka sama dla każdego żądania.

## <a name="response-headers-in-production"></a>Nagłówki odpowiedzi w środowisku produkcyjnym

Podejście proxy CORS, które są opisane w poprzedniej odpowiedzi jest odpowiednie do tworzenia, testowania i uczenia. 

Jednak w środowisku produkcyjnym należy udostępnić skryptu po stronie serwera w tej samej domenie co stronę sieci Web, która używa interfejsu API wyszukiwania usługi Bing sieci Web. Ten skrypt powinien rzeczywiście nie wywołań interfejsu API na żądanie z poziomu języka JavaScript strony sieci Web i Przekaż wszystkie wyniki, włącznie z nagłówkami do klienta. Ponieważ dwa zasoby (strony i skrypt) udziału pochodzenia, CORS nie przejdzie do gry i specjalnych nagłówków są acessible do JavaScript na stronie sieci Web. 

Takie podejście chroni także klucza interfejsu API z narażenia na publiczne, ponieważ tylko skryptu po stronie serwera, należy go. Skrypt można użyć innej metody do upewnij się, że żądanie jest autoryzowane.

## <a name="next-steps"></a>Kolejne kroki

To pytanie dotyczące funkcji lub funkcjonalności? Należy wziąć pod uwagę żądania lub głosu dla niego naszych [witryny User Voice](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Zobacz także

 [Przepełnienie stosu: Usługi kognitywnych](http://stackoverflow.com/questions/tagged/bing-api)