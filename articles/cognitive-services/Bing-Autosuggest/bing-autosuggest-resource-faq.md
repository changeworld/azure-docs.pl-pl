---
title: Często zadawane pytania (FAQ) - API automatycznego sugerowania Bing
titlesuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejsu API automatycznego sugerowania Bing.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: b0ec10bbf03e8a8d005eece4b6496b74b2943233
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173262"
---
# <a name="frequently-asked-questions-faq-about-bing-autosuggest-api"></a>Często zadawane że API automatycznego sugerowania pytania (FAQ) dotyczących usługi Bing
 
 Znajdź odpowiedzi na często zadawane pytania na temat pojęć, kodu i scenariusze związane z interfejsu API automatycznego sugerowania dla usług Azure Cognitive Services.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Jak uzyskać nagłówki opcjonalne klienta, podczas wywoływania interfejsu API automatycznego sugerowania Bing z języka JavaScript?

Następujące nagłówki są opcjonalne, ale firma Microsoft zaleca, aby traktować je zgodnie z potrzebami. Nagłówki te pomagają API automatycznego sugerowania Bing zwraca dokładniejsze wyniki.

- Lokalizacja w przypadku wyszukiwania X
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Jednak po wywołaniu interfejsu API automatycznego sugerowania Bing poziomu języka JavaScript, funkcje zabezpieczeń wbudowane w przeglądarce może uniemożliwić dostęp do wartości tych nagłówków.

Aby rozwiązać ten problem, może wykonać żądania interfejsu API automatycznego sugerowania usługi Bing za pośrednictwem serwera proxy mechanizmu CORS. Odpowiedź z serwera proxy ma `Access-Control-Expose-Headers` Nagłówek tego dozwolonych nagłówków odpowiedzi i udostępnienie ich dla języka JavaScript.

To proste zainstalować serwer proxy CORS, aby zezwolić na naszych [samouczek aplikacji](tutorials/autosuggest.md) nagłówki opcjonalne klienta dostępu do. Najpierw [zainstaluj platformę Node.js](https://nodejs.org/en/download/), jeśli jeszcze jej nie masz. Następnie wprowadź następujące polecenie w wierszu polecenia.

    npm install -g cors-proxy-server

Następnie Zmień punkt końcowy interfejsu API automatycznego sugerowania Bing w pliku HTML, aby:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Na koniec uruchom serwer proxy CORS za pomocą następującego polecenia:

    cors-proxy-server

Podczas korzystania z aplikacji samouczka pozostaw okno polecenia otwarte, ponieważ jego zamknięcie spowoduje zatrzymanie serwera proxy. W rozwijanej sekcji nagłówków HTML poniżej wyników wyszukiwania można teraz zobaczyć nagłówek `X-MSEdge-ClientID` (pomiędzy innymi) i sprawdzić, czy jest on taki sam dla każdego żądania.

## <a name="next-steps"></a>Kolejne kroki

To pytanie dotyczące brakujących funkcji lub funkcje? Należy wziąć pod uwagę żądania lub głosowanie na jego w nasze [witrynę User Voice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Zobacz także

- [Stack Overflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
