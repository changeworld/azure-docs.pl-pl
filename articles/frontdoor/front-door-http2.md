---
title: Drzwi wejściowe platformy Azure — obsługa protokołu HTTP2 | Dokumenty firmy Microsoft
description: Ten artykuł ułatwia zapoznanie się z obsługą protokołu HTTP/2 w drzwiach frontowych platformy Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 415a89cbccd6b43d64ba1a553d44ac003df5806a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471629"
---
# <a name="http2-support-in-azure-front-door"></a>Obsługa protokołu HTTP/2 w drzwiach frontowych platformy Azure

Obecnie obsługa protokołu HTTP/2 jest aktywna dla wszystkich konfiguracji drzwi ekwiomowych. Nie są wymagane żadne dalsze działania ze strony klientów.

HTTP/2 jest główną wersją http/1.1. Zapewnia szybszą wydajność sieci Web, krótszy czas reakcji i lepsze środowisko użytkownika, przy jednoczesnym zachowaniu znanych metod HTTP, kodów stanu i semantyki. Chociaż protokół HTTP/2 jest przeznaczony do pracy z protokołem HTTP i HTTPS, wiele klienckich przeglądarek internetowych obsługuje tylko protokół HTTP/2 za pośrednictwem zabezpieczeń warstwy transportu (TLS).

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

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o http/2, odwiedź następujące zasoby:

- [Strona główna specyfikacji HTTP/2](https://http2.github.io/)
- [Oficjalne często zadawane pytania dotyczące protokołu HTTP/2](https://http2.github.io/faq/)
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
