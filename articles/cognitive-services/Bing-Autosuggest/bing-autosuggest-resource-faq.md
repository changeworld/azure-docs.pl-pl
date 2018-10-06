---
title: Często zadawane pytania (FAQ) - API automatycznego sugerowania Bing
titlesuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące interfejsu API automatycznego sugerowania Bing.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 84f1b0555922119e9de4addc3d51ac233e7bae65
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831368"
---
# <a name="frequently-asked-questions-faq-about-bing-autosuggest-api"></a>Często zadawane że API automatycznego sugerowania pytania (FAQ) dotyczących usługi Bing
 
 Znajdź odpowiedzi na często zadawane pytania na temat pojęć, kodu i scenariusze związane z interfejsu API automatycznego sugerowania dla usług Azure Cognitive Services.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Jak uzyskać nagłówki opcjonalne klienta, podczas wywoływania interfejsu API automatycznego sugerowania Bing z języka JavaScript?

Następujące nagłówki są opcjonalne, ale firma Microsoft zaleca, aby traktować je zgodnie z potrzebami. Nagłówki te pomagają API automatycznego sugerowania Bing zwraca dokładniejsze wyniki.

- Lokalizacja w przypadku wyszukiwania X
- ClientID-X-MSEdge
- X-MSEdge-ClientIP

Jednak po wywołaniu interfejsu API automatycznego sugerowania Bing poziomu języka JavaScript, funkcje zabezpieczeń wbudowane w przeglądarce może uniemożliwić dostęp do wartości tych nagłówków.

Aby rozwiązać ten problem, może wykonać żądania interfejsu API automatycznego sugerowania usługi Bing za pośrednictwem serwera proxy mechanizmu CORS. Odpowiedź z serwera proxy ma `Access-Control-Expose-Headers` Nagłówek tego dozwolonych nagłówków odpowiedzi i udostępnienie ich dla języka JavaScript.

To proste zainstalować serwer proxy CORS, aby zezwolić na naszych [samouczek aplikacji](tutorials/autosuggest.md) nagłówki opcjonalne klienta dostępu do. Pierwsze, jeśli nie masz jeszcze, [zainstalować środowisko Node.js](https://nodejs.org/en/download/). Następnie wprowadź następujące polecenie w wierszu polecenia.

    npm install -g cors-proxy-server

Następnie Zmień punkt końcowy interfejsu API automatycznego sugerowania Bing w pliku HTML, aby:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Na koniec uruchom serwer proxy mechanizmu CORS za pomocą następującego polecenia:

    cors-proxy-server

Pozostaw otwarte okno polecenia podczas korzystania z samouczka aplikacji; Zamyka okno zatrzymuje serwera proxy. W sekcji można rozwijać nagłówków HTTP poniżej wyniki wyszukiwania, będą teraz widoczne `X-MSEdge-ClientID` nagłówka (między innymi) i sprawdź, czy jest taka sama dla każdego żądania.

## <a name="next-steps"></a>Kolejne kroki

To pytanie dotyczące brakujących funkcji lub funkcje? Należy wziąć pod uwagę żądania lub głosowanie na jego w nasze [witrynę User Voice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Zobacz także

- [Witrynie Stack Overflow: Usługi Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
