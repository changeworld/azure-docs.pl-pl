---
title: Często zadawane pytania dotyczące Bing pisowni API wyboru - kognitywnych usług platformy Azure | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące API sprawdzania pisowni usługi Bing na platformie Azure.
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 87b1f3ed3e0aaa9f3c3c804dc9eac3ee60b4a565
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349249"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Często zadawane pytania dotyczące pisowni usługi Bing Sprawdź interfejsu API

 Odpowiedzi na często zadawane pytania dotyczące pojęć, kodu i scenariusze związane z API sprawdzania pisowni usługi Bing kognitywnych usług Microsoft Azure.

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>Jak uzyskać nagłówki opcjonalne klienta podczas wywoływania funkcji API sprawdzania pisowni usługi Bing z poziomu języka JavaScript?

Następujące nagłówki są opcjonalne, ale zaleca się potraktować je zgodnie z potrzebami. Nagłówki te pomagają dokładniejsze wyniki zwracane API sprawdzania pisowni usługi Bing.

- X wyszukiwania lokalizacji
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Jednak gdy API sprawdzania pisowni usługi Bing jest wywoływana z kodu JavaScript, funkcje zabezpieczeń w przeglądarce może uniemożliwić dostęp do wartości tych nagłówków.

Aby rozwiązać ten problem, może wykonać żądania API sprawdzania pisowni usługi Bing za pośrednictwem serwera proxy CORS. Odpowiedź z serwera proxy ma `Access-Control-Expose-Headers` Nagłówek tego whitelists nagłówki odpowiedzi i udostępnia je JavaScript.

Łatwo zainstalować serwer proxy CORS, aby umożliwić [samouczek aplikacji](tutorials/spellcheck.md) dostępu nagłówki opcjonalne klienta do. Pierwsza strona, jeśli nie masz jeszcze, [instalowania programu Node.js](https://nodejs.org/en/download/). Następnie wprowadź następujące polecenie w wierszu polecenia.

    npm install -g cors-proxy-server

Następnie można zmienić punktu końcowego API sprawdzania pisowni usługi Bing w pliku w formacie HTML do:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Na koniec uruchom CORS proxy za pomocą następującego polecenia:

    cors-proxy-server

Pozostaw otwarte okno wiersza poleceń podczas korzystania z samouczka aplikacji; Zamyka okno zatrzymuje serwer proxy. W sekcji nagłówków HTTP rozwijanych poniżej wyniki wyszukiwania, możesz teraz przeglądać `X-MSEdge-ClientID` nagłówka (między innymi) i sprawdź, czy jest taka sama dla każdego żądania.

## <a name="next-steps"></a>Kolejne kroki

To pytanie dotyczące funkcji lub funkcjonalności? Należy wziąć pod uwagę żądania lub głosu dla niego [witryny sieci web UserVoice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Zobacz także

 [StackOverflow: Usługi kognitywnych](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
