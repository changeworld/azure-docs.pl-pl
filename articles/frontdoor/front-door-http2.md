---
title: Usługa Azure Front-The-HTTP2 support | Microsoft Docs
description: Ten artykuł ułatwia zapoznanie się z obsługą protokołu HTTP/2 w usłudze Azure front-drzwi
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
ms.openlocfilehash: c3c1721454c0b3c96071c685a764f34d4fa540b9
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775270"
---
# <a name="http2-support-in-azure-front-door-service"></a>Obsługa protokołu HTTP/2 w usłudze Azure front-drzwi

Obecnie obsługa protokołu HTTP/2 jest aktywna dla wszystkich konfiguracji czołowych drzwi. Od klientów nie są wymagane żadne dalsze działania.

HTTP/2 to główna wersja protokołu HTTP/1.1. Zapewnia to szybszą wydajność sieci Web, krótszy czas odpowiedzi i ulepszone środowisko użytkownika przy zachowaniu znanych metod HTTP, kodów stanu i semantyki. Mimo że protokół HTTP/2 jest przeznaczony do pracy z protokołami HTTP i HTTPS, wiele przeglądarek sieci Web klienta obsługuje tylko protokół HTTP/2 dla Transport Layer Security (TLS).

### <a name="http2-benefits"></a>Korzyści z protokołu HTTP/2

Zalety protokołu HTTP/2 obejmują:

*   **Multipleksowanie i współbieżność**

    Przy użyciu protokołu HTTP 1,1 wykonanie wielu żądań zasobów wymaga wielu połączeń TCP, a każde połączenie ma powiązane z nim obciążenie. Protokół HTTP/2 umożliwia żądanie wielu zasobów na jednym połączeniu TCP.

*   **Kompresowanie nagłówka**

    Dzięki kompresji nagłówków HTTP dla obsługiwanych zasobów czas w sieci jest zmniejszany znacznie.

*   **Zależności strumienia**

    Zależności strumienia umożliwiają klientowi wskazanie serwera, którego zasoby mają priorytet.


## <a name="http2-browser-support"></a>Obsługa przeglądarki HTTP/2

Wszystkie główne przeglądarki wdrożyły obsługę protokołu HTTP/2 w ich bieżących wersjach. Nieobsługiwane przeglądarki automatycznie powracają do protokołu HTTP/1.1.

|Browser|Wersja minimalna|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat protokołu HTTP/2 można znaleźć w następujących zasobach:

- [Strona główna specyfikacji protokołu HTTP/2](https://http2.github.io/)
- [Oficjalne pytania dotyczące protokołu HTTP/2](https://http2.github.io/faq/)
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
