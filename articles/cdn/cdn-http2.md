---
title: Obsługa protokołu HTTP/2 w usłudze Azure CDN | Dokumenty firmy Microsoft
description: Dowiedz się więcej o obsłudze protokołu HTTP/2 i sieci CDN.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: 703623e3f7f314d87417458f3f9a218dfdf45427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67849970"
---
# <a name="http2-support-in-azure-cdn"></a>Obsługa protokołu HTTP/2 w usłudze Azure CDN

HTTP/2 jest główną wersją http/1.1\. Zapewnia szybszą wydajność sieci Web, krótszy czas reakcji i lepsze środowisko użytkownika, przy jednoczesnym zachowaniu znanych metod HTTP, kodów stanu i semantyki. Chociaż protokół HTTP/2 jest przeznaczony do pracy z protokołem HTTP i HTTPS, wiele klienckich przeglądarek internetowych obsługuje tylko protokół HTTP/2 za pośrednictwem protokołu TLS.

### <a name="http2-benefits"></a>Korzyści http/2

Korzyści z HTTP/2 obejmują:

*   **Multipleksowanie i współbieżność**

    Przy użyciu protokołu HTTP 1.1, wykonywanie wielu żądań zasobów wymaga wielu połączeń TCP, a każde połączenie ma obciążenie wydajności związane z nim. Protokół HTTP/2 umożliwia żądanie wielu zasobów w przypadku jednego połączenia TCP.

*   **Kompresja nagłówka**

    Przez kompresję nagłówków HTTP dla obsługiwanych zasobów, czas na przewodowym jest znacznie zmniejszona.

*   **Zależności strumienia**

    Zależności strumienia pozwalają klientowi wskazać serwerowi, które zasoby mają priorytet.


## <a name="http2-browser-support"></a>Obsługa przeglądarki HTTP/2

Wszystkie główne przeglądarki zaimplementowały obsługę HTTP/2 w swoich aktualnych wersjach. Nieobjęte przeglądarkami automatycznie powrócą do protokołu HTTP/1.1.

|Przeglądarka|Minimalna wersja|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Włączanie obsługi protokołu HTTP/2 w usłudze Azure CDN

Obecnie obsługa protokołu HTTP/2 jest aktywna dla wszystkich profilów usługi Azure CDN. Nie są wymagane żadne dalsze działania ze strony klientów.

## <a name="next-steps"></a>Następne kroki

Aby zobaczyć korzyści z HTTP/2 w akcji, zobacz [to demo z Akamai](https://http2.akamai.com/demo).

Aby dowiedzieć się więcej o http/2, odwiedź następujące zasoby:

*   [Strona główna specyfikacji HTTP/2](https://http2.github.io/)
*   [Oficjalne często zadawane pytania dotyczące protokołu HTTP/2](https://http2.github.io/faq/)
*   [Akamai HTTP/2 informacje](https://http2.akamai.com/)

Aby dowiedzieć się więcej o dostępnych funkcjach usługi Azure CDN, zobacz [Omówienie usługi Azure CDN](https://azure.microsoft.com/documentation/articles/cdn-overview/).