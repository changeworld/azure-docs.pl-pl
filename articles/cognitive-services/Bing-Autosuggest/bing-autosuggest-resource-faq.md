---
title: Interfejs API automatycznego sugerowania pytania dotyczące usługi Azure — często zadawane | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure kognitywnych automatycznego sugerowania interfejsu API usług Azure.
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 00b91728bcfec52ff30697f080d5c2619bab79a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348933"
---
# <a name="frequently-asked-questions-faq-about-autosuggest-api-cognitive-services"></a>Często zadawane się, że (usługi kognitywnych) interfejsu API automatycznego sugerowania pytania (FAQ)
 
 Odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariusze związane z interfejsu API automatycznego sugerowania kognitywnych usług Azure.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Jak uzyskać nagłówki opcjonalne klienta podczas wywoływania metody interfejsu API automatycznego sugerowania usługi Bing z poziomu języka JavaScript?

Następujące nagłówki są opcjonalne, ale zaleca się potraktować je zgodnie z potrzebami. Nagłówki te pomagają API automatycznego sugerowania usługi Bing zwracają dokładniejsze wyniki.

- X wyszukiwania lokalizacji
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Jednak po wywołaniu interfejsu API automatycznego sugerowania usługi Bing z poziomu języka JavaScript, funkcje zabezpieczeń w przeglądarce może uniemożliwić dostęp do wartości tych nagłówków.

Aby rozwiązać ten problem, może wykonać żądania API automatycznego sugerowania usługi Bing za pośrednictwem serwera proxy CORS. Odpowiedź z serwera proxy ma `Access-Control-Expose-Headers` Nagłówek tego whitelists nagłówki odpowiedzi i udostępnia je JavaScript.

Łatwo zainstalować serwer proxy CORS, aby umożliwić naszych [samouczek aplikacji](tutorials/autosuggest.md) dostępu nagłówki opcjonalne klienta do. Pierwsza strona, jeśli nie masz jeszcze, [instalowania programu Node.js](https://nodejs.org/en/download/). Następnie wprowadź następujące polecenie w wierszu polecenia.

    npm install -g cors-proxy-server

Następnie można zmienić punktu końcowego API automatycznego sugerowania usługi Bing w pliku w formacie HTML do:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Na koniec uruchom CORS proxy za pomocą następującego polecenia:

    cors-proxy-server

Pozostaw otwarte okno wiersza poleceń podczas korzystania z samouczka aplikacji; Zamyka okno zatrzymuje serwer proxy. W sekcji nagłówków HTTP rozwijanych poniżej wyniki wyszukiwania, możesz teraz przeglądać `X-MSEdge-ClientID` nagłówka (między innymi) i sprawdź, czy jest taka sama dla każdego żądania.

## <a name="next-steps"></a>Kolejne kroki

To pytanie dotyczące funkcji lub funkcjonalności? Należy wziąć pod uwagę żądania lub głosu dla niego naszych [witryny User Voice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Zobacz także

- [Przepełnienie stosu: Usługi kognitywnych](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
