---
title: Często zadawane pytania (FAQ) - API wyszukiwania w Internecie Bing
titleSuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące API wyszukiwania w Internecie Bing.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: troubleshooting
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: b0b8fd3eb80d7418546788565402042de20ab3e7
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129322"
---
# <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

 Znajdź odpowiedzi na często zadawane pytania na temat pojęć, kodu i scenariusze związane z interfejsu API wyszukiwania Bing w sieci Web dla usług Microsoft Cognitive Services na platformie Azure.

## <a name="response-headers-in-javascript"></a>Nagłówki odpowiedzi w języku JavaScript

Następujące nagłówki mogą występować w odpowiedzi z API wyszukiwania w Internecie Bing.

|||
|-|-|
|`X-MSEdge-ClientID`|Unikatowy identyfikator, który Bing został przypisany do użytkownika|
|`BingAPIs-Market`|Na rynku, który został użyty do spełnienia żądania|
|`BingAPIs-TraceId`|Wpis dziennika na serwerze interfejsu API Bing dla tego żądania (w przypadku pomocy technicznej)|

Jest to szczególnie ważne utrwalić identyfikator klienta i przywrócić go z kolejnymi żądaniami. Gdy to zrobisz, wyszukiwania korzystania z wcześniejszych kontekście Klasyfikacja wyników wyszukiwania, a także zapewnić spójne środowisko użytkownika.

Jednak gdy wywołujesz API wyszukiwania w Internecie Bing poziomu języka JavaScript w przeglądarce wbudowane funkcje zabezpieczeń (między źródłami CORS) może uniemożliwić dostęp do wartości tych nagłówków.

Aby uzyskać dostęp do nagłówków, może wykonać żądania interfejsu API wyszukiwania Bing w sieci Web za pośrednictwem serwera proxy mechanizmu CORS. Odpowiedź z serwera proxy ma `Access-Control-Expose-Headers` Nagłówek tego dozwolonych nagłówków odpowiedzi i udostępnienie ich dla języka JavaScript.

To proste zainstalować serwer proxy CORS, aby zezwolić na naszych [samouczek aplikacji](tutorial-bing-web-search-single-page-app.md) nagłówki opcjonalne klienta dostępu do. Pierwsze, jeśli nie masz jeszcze, [zainstalować środowisko Node.js](https://nodejs.org/en/download/). Następnie wprowadź następujące polecenie w wierszu polecenia.

    npm install -g cors-proxy-server

Następnie Zmień punkt końcowy interfejsu API wyszukiwania Bing w sieci Web w pliku HTML, aby:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Na koniec uruchom serwer proxy mechanizmu CORS za pomocą następującego polecenia:

    cors-proxy-server

Pozostaw otwarte okno polecenia podczas korzystania z samouczka aplikacji; Zamyka okno zatrzymuje serwera proxy. W sekcji można rozwijać nagłówków HTTP poniżej wyniki wyszukiwania, będą teraz widoczne `X-MSEdge-ClientID` nagłówka (między innymi) i sprawdź, czy jest taka sama dla każdego żądania.

## <a name="response-headers-in-production"></a>Nagłówki odpowiedzi w środowisku produkcyjnym

Podejście proxy CORS, które są opisane w poprzedniej odpowiedzi jest odpowiednia dla rozwoju, testowania i nauki.

Jednak w środowisku produkcyjnym należy obsługiwać skryptu po stronie serwera w tej samej domenie co strony sieci Web, który używa API wyszukiwania w Internecie Bing. Ten skrypt powinien rzeczywiście nie wywołania interfejsu API na żądanie z JavaScript strony sieci Web i przekazać wszystkie wyniki, włącznie z nagłówkami, z powrotem do klienta. Od dwóch zasobów (strony i skryptów) udostępnić punkt początkowy, mechanizm CORS nie dochodzą do głosu i specjalnych nagłówków są acessible w kodzie JavaScript na stronie sieci Web.

Takie podejście chroni także klucz interfejsu API przed narażenia na wartość publiczne, ponieważ tylko skrypt po stronie serwera, należy go. Skrypt można użyć innej metody, aby upewnić się, że żądanie jest autoryzowane.

## <a name="next-steps"></a>Kolejne kroki

To pytanie dotyczące brakujących funkcji lub funkcje? Należy wziąć pod uwagę żądania lub głosowanie na jego w nasze [witrynę User Voice](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Zobacz także

 [Witrynie Stack Overflow: Usługi Cognitive Services](http://stackoverflow.com/questions/tagged/bing-api)
