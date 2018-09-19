---
title: Często zadawane pytania (FAQ) — interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Znajdź odpowiedzi na często zadawane pytania na temat pojęć, kodu i scenariusze związane z interfejsu API wyszukiwania obrazów Bing.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: troubleshooting
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: ea170f4751952288c7894cab9c5acda2bf443043
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295506"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Często zadawane pytania (FAQ) dotyczące interfejsu API wyszukiwania obrazów Bing

Znajdź odpowiedzi na często zadawane pytania na temat pojęć, kodu i scenariusze związane z interfejsu API wyszukiwania obrazów Bing dla usług Microsoft Cognitive Services na platformie Azure.

## <a name="response-headers-in-javascript"></a>Nagłówki odpowiedzi w języku JavaScript

Następujące nagłówki mogą występować w odpowiedzi z interfejsu API wyszukiwania obrazów Bing.

|||
|-|-|
|`X-MSEdge-ClientID`|Unikatowy identyfikator, który Bing został przypisany do użytkownika|
|`BingAPIs-Market`|Na rynku, który został użyty do spełnienia żądania|
|`BingAPIs-TraceId`|Wpis dziennika na serwerze interfejsu API Bing dla tego żądania (w przypadku pomocy technicznej)|

Jest to szczególnie ważne utrwalić identyfikator klienta i przywrócić go z kolejnymi żądaniami. Gdy to zrobisz, wyszukiwania korzystania z wcześniejszych kontekście Klasyfikacja wyników wyszukiwania, a także zapewnić spójne środowisko użytkownika.

Jednak po wywołaniu interfejsu API wyszukiwania obrazów Bing poziomu języka JavaScript, w przeglądarce wbudowane funkcje zabezpieczeń (między źródłami CORS) może uniemożliwić dostęp do wartości tych nagłówków.

Aby uzyskać dostęp do nagłówków, może wykonać żądania interfejsu API wyszukiwania obrazów Bing za pośrednictwem serwera proxy mechanizmu CORS. Odpowiedź z serwera proxy ma `Access-Control-Expose-Headers` Nagłówek tego dozwolonych nagłówków odpowiedzi i udostępnienie ich dla języka JavaScript.

To proste zainstalować serwer proxy CORS, aby zezwolić na naszych [samouczek aplikacji](tutorial-bing-image-search-single-page-app.md) nagłówki opcjonalne klienta dostępu do. Pierwsze, jeśli nie masz jeszcze, [zainstalować środowisko Node.js](https://nodejs.org/en/download/). Następnie wprowadź następujące polecenie w wierszu polecenia.

    npm install -g cors-proxy-server

Następnie Zmień punkt końcowy interfejsu API wyszukiwania obrazów Bing w pliku HTML, aby:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Na koniec uruchom serwer proxy mechanizmu CORS za pomocą następującego polecenia:

    cors-proxy-server

Pozostaw otwarte okno polecenia podczas korzystania z samouczka aplikacji; Zamyka okno zatrzymuje serwera proxy. W sekcji można rozwijać nagłówków HTTP poniżej wyniki wyszukiwania, będą teraz widoczne `X-MSEdge-ClientID` nagłówka (między innymi) i sprawdź, czy jest taka sama dla każdego żądania.

## <a name="response-headers-in-production"></a>Nagłówki odpowiedzi w środowisku produkcyjnym

Podejście proxy CORS, które są opisane w poprzedniej odpowiedzi jest odpowiednia dla rozwoju, testowania i nauki.

Jednak w środowisku produkcyjnym należy obsługiwać skryptu po stronie serwera w tej samej domenie co strony sieci Web, który używa API wyszukiwania w Internecie Bing. Ten skrypt powinien rzeczywiście nie wywołania interfejsu API na żądanie z JavaScript strony sieci Web i przekazać wszystkie wyniki, włącznie z nagłówkami, z powrotem do klienta. Od dwóch zasobów (strony i skryptów) udostępnić punkt początkowy, mechanizm CORS nie dochodzą do głosu i specjalnych nagłówków są acessible w kodzie JavaScript na stronie sieci Web.

Takie podejście chroni także klucz interfejsu API przed narażenia na wartość publiczne, ponieważ tylko skrypt po stronie serwera, należy go. Skrypt można użyć innej metody (na przykład odwołania HTTP) aby upewnić się, że żądanie jest autoryzowane.

## <a name="next-steps"></a>Kolejne kroki

To pytanie dotyczące brakujących funkcji lub funkcje? Należy wziąć pod uwagę żądania lub głosowanie na jego w nasze [witrynę User Voice](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Zobacz także

 [Witrynie Stack Overflow: Usługi Cognitive Services](http://stackoverflow.com/questions/tagged/bing-api)
