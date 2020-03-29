---
title: Często zadawane pytania (FAQ) — interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Znajdź odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariuszy związanych z interfejsem API wyszukiwania obrazów Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6841e573446103466e2719797da9e4161b70b5a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881698"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Często zadawane pytania dotyczące interfejsu API wyszukiwania obrazów Bing

Znajdź odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariuszy związanych z interfejsem API wyszukiwania obrazów Bing dla usług Microsoft Cognitive Services na platformie Azure.

## <a name="response-headers-in-javascript"></a>Nagłówki odpowiedzi w języku JavaScript

Następujące nagłówki mogą wystąpić w odpowiedziach z interfejsu API wyszukiwania obrazów Bing.

| `Attribute`         | `Description` |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |Unikatowy identyfikator przypisany przez bing do użytkownika |
| `BingAPIs-Market`   |Rynek, który został użyty do spełnienia wniosku |
| `BingAPIs-TraceId`  |Wpis dziennika na serwerze interfejsu API usługi Bing dla tego żądania (dla pomocy technicznej) |

Jest szczególnie ważne, aby utrwalić identyfikator klienta i zwrócić go z kolejnymi żądaniami. Po wykonaniu tej tej funkcji wyszukiwanie użyje kontekstu z przeszłości w wynikach wyszukiwania rankingowego, a także zapewni spójne środowisko użytkownika.

Jednak po wywołaniu interfejsu API wyszukiwania obrazów Bing z języka JavaScript wbudowane funkcje zabezpieczeń przeglądarki (CORS) mogą uniemożliwić dostęp do wartości tych nagłówków.

Aby uzyskać dostęp do nagłówków, można wykonać żądanie interfejsu API wyszukiwania obrazów Bing za pośrednictwem serwera proxy CORS. Odpowiedź z tego serwera proxy zawiera nagłówek `Access-Control-Expose-Headers`, który zezwala na nagłówki odpowiedzi i udostępnia je dla języka JavaScript.

Łatwo jest zainstalować serwer proxy CORS, aby umożliwić naszej [aplikacji samouczka](tutorial-bing-image-search-single-page-app.md) dostęp do opcjonalnych nagłówków klienta. Najpierw [zainstaluj platformę Node.js](https://nodejs.org/en/download/), jeśli jeszcze jej nie masz. Potem wprowadź poniższe polecenie w wierszu polecenia.

    npm install -g cors-proxy-server

Następnie zmień punkt końcowy interfejsu API wyszukiwania obrazów Bing w pliku HTML na:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Na koniec uruchom serwer proxy CORS za pomocą następującego polecenia:

    cors-proxy-server

Podczas korzystania z aplikacji samouczka pozostaw okno polecenia otwarte, ponieważ jego zamknięcie spowoduje zatrzymanie serwera proxy. W rozwijanej sekcji nagłówków HTML poniżej wyników wyszukiwania można teraz zobaczyć nagłówek `X-MSEdge-ClientID` (pomiędzy innymi) i sprawdzić, czy jest on taki sam dla każdego żądania.

## <a name="response-headers-in-production"></a>Nagłówki odpowiedzi w produkcji

Podejście serwera proxy CORS opisane w poprzedniej odpowiedzi jest odpowiednie do rozwoju, testowania i uczenia się.

W środowisku produkcyjnym należy jednak hostować skrypt po stronie serwera w tej samej domenie co strona sieci Web, która używa interfejsu API wyszukiwania w sieci Web Bing. Ten skrypt powinien faktycznie wykonać wywołania interfejsu API na żądanie ze strony sieci Web JavaScript i przekazać wszystkie wyniki, w tym nagłówki, z powrotem do klienta. Ponieważ dwa zasoby (strona i skrypt) współużytkują pochodzenie, cors nie wchodzi w grę, a specjalne nagłówki są dostępne dla javascript na stronie sieci Web.

Takie podejście chroni również klucz interfejsu API przed narażeniem na działanie opinii publicznej, ponieważ potrzebuje go tylko skrypt po stronie serwera. Skrypt może użyć innej metody (takiej jak osoba odsyłacza HTTP), aby upewnić się, że żądanie jest autoryzowane.

## <a name="next-steps"></a>Następne kroki

Czy masz pytanie dotyczące brakującej funkcji lub funkcji? Rozważ zgłoszenie lub oddanie na nie głosu na naszej [stronie internetowej User Voice.](https://cognitive.uservoice.com/forums/555907-bing-search)

## <a name="see-also"></a>Zobacz też

 [Przepełnienie stosu: usługi kognitywne](https://stackoverflow.com/questions/tagged/bing-api)
