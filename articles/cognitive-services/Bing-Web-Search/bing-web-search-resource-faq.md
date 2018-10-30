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
ms.openlocfilehash: 2d43a73d93b24599b28af849ee9d1532441ef6bc
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233563"
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

Aby uzyskać dostęp do nagłówków, może wykonać żądania interfejsu API wyszukiwania Bing w sieci Web za pośrednictwem serwera proxy mechanizmu CORS. Odpowiedź z tego serwera proxy zawiera nagłówek `Access-Control-Expose-Headers`, który zezwala na nagłówki odpowiedzi i udostępnia je dla języka JavaScript.

To proste zainstalować serwer proxy CORS, aby zezwolić na naszych [samouczek aplikacji](tutorial-bing-web-search-single-page-app.md) nagłówki opcjonalne klienta dostępu do. Najpierw [zainstaluj platformę Node.js](https://nodejs.org/en/download/), jeśli jeszcze jej nie masz. Następnie wprowadź następujące polecenie w wierszu polecenia.

    npm install -g cors-proxy-server

Następnie Zmień punkt końcowy interfejsu API wyszukiwania Bing w sieci Web w pliku HTML, aby:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Na koniec uruchom serwer proxy CORS za pomocą następującego polecenia:

    cors-proxy-server

Podczas korzystania z aplikacji samouczka pozostaw okno polecenia otwarte, ponieważ jego zamknięcie spowoduje zatrzymanie serwera proxy. W rozwijanej sekcji nagłówków HTML poniżej wyników wyszukiwania można teraz zobaczyć nagłówek `X-MSEdge-ClientID` (pomiędzy innymi) i sprawdzić, czy jest on taki sam dla każdego żądania.

## <a name="response-headers-in-production"></a>Nagłówki odpowiedzi w środowisku produkcyjnym

Podejście proxy CORS, które są opisane w poprzedniej odpowiedzi jest odpowiednia dla rozwoju, testowania i nauki.

Jednak w środowisku produkcyjnym należy obsługiwać skryptu po stronie serwera w tej samej domenie co strony sieci Web, który używa API wyszukiwania w Internecie Bing. Ten skrypt powinien rzeczywiście nie wywołania interfejsu API na żądanie z JavaScript strony sieci Web i przekazać wszystkie wyniki, włącznie z nagłówkami, z powrotem do klienta. Ponieważ dwóch zasobów (strony i skryptów) udostępnić punkt początkowy, mechanizm CORS nie dochodzą do głosu i specjalnych nagłówków są dostępne dla JavaScript na stronie sieci Web.

Takie podejście chroni także klucz interfejsu API przed narażenia na wartość publiczne, ponieważ tylko skrypt po stronie serwera, należy go. Skrypt można użyć innej metody, aby upewnić się, że żądanie jest autoryzowane.

## <a name="next-steps"></a>Kolejne kroki

To pytanie dotyczące brakujących funkcji lub funkcje? Należy wziąć pod uwagę żądania lub głosowanie na jego w nasze [witrynę User Voice](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Zobacz także

 [Witrynie Stack Overflow: Usługi Cognitive Services](http://stackoverflow.com/questions/tagged/bing-api)
