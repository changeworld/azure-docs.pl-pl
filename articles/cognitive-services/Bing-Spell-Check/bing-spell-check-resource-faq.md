---
title: Często zadawane pytania dotyczące pisowni Bing Sprawdź interfejsu API
titlesuffix: Azure Cognitive Services
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące API sprawdzania pisowni Bing na platformie Azure.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 90acc80b19058c2a7eb963f9e423883846519b2c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164932"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Często zadawane pytania dotyczące pisowni Bing Sprawdź interfejsu API

 Znajdź odpowiedzi na często zadawane pytania na temat pojęć, kodu i scenariusze związane z API sprawdzania pisowni Bing dla usług Microsoft Cognitive Services na platformie Azure.

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>Jak uzyskać nagłówki opcjonalne klienta, podczas wywołania API sprawdzania pisowni Bing poziomu języka JavaScript?

Następujące nagłówki są opcjonalne, ale firma Microsoft zaleca, aby traktować je zgodnie z potrzebami. Nagłówki te pomagają dokładniejsze wyniki zwracane interfejsu API sprawdzania pisowni Bing.

- Lokalizacja w przypadku wyszukiwania X
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Jednak gdy wywołujesz API sprawdzania pisowni Bing poziomu języka JavaScript w przeglądarce wbudowane funkcje zabezpieczeń może uniemożliwić dostęp do wartości tych nagłówków.

Aby rozwiązać ten problem, może wykonać żądania interfejsu API sprawdzania pisowni Bing za pośrednictwem serwera proxy mechanizmu CORS. Odpowiedź z serwera proxy ma `Access-Control-Expose-Headers` Nagłówek tego dozwolonych nagłówków odpowiedzi i udostępnienie ich dla języka JavaScript.

To proste zainstalować serwer proxy CORS, aby umożliwić [samouczek aplikacji](tutorials/spellcheck.md) nagłówki opcjonalne klienta dostępu do. Najpierw [zainstaluj platformę Node.js](https://nodejs.org/en/download/), jeśli jeszcze jej nie masz. Następnie wprowadź następujące polecenie w wierszu polecenia.

    npm install -g cors-proxy-server

Następnie Zmień punkt końcowy interfejsu API sprawdzania pisowni Bing w pliku HTML, aby:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Na koniec uruchom serwer proxy CORS za pomocą następującego polecenia:

    cors-proxy-server

Podczas korzystania z aplikacji samouczka pozostaw okno polecenia otwarte, ponieważ jego zamknięcie spowoduje zatrzymanie serwera proxy. W sekcji można rozwijać nagłówków HTTP poniżej wyniki wyszukiwania, będą teraz widoczne `X-MSEdge-ClientID` nagłówka (między innymi) i sprawdź, czy jest taka sama dla każdego żądania.

## <a name="next-steps"></a>Kolejne kroki

To pytanie dotyczące brakujących funkcji lub funkcje? Należy wziąć pod uwagę żądania lub głosowanie na jej w [witryny sieci web usługi UserVoice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Zobacz także

 [StackOverflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
