---
title: Usługa Azure drzwiami frontowymi — obsługę protokołu HTTP2 | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci więcej informacji na temat obsługi protokołu HTTP/2 w usłudze Azure Service drzwi
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
ms.openlocfilehash: 4282c9e9b660476992ba6f948bc5e408e9b064a5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968615"
---
# <a name="http2-support-in-azure-front-door-service"></a>Obsługa protokołu HTTP/2 w usłudze Azure Service drzwi
Protokołu HTTP/2 jest główną wersję protokołu HTTP/1.1. Zapewnia ona szybsze wydajności sieci web, czas reakcji mniejsze i ulepszone środowisko pracy, przy zachowaniu znanych metod HTTP, kodów stanu i semantyki. Chociaż protokołu HTTP/2 jest przeznaczona do pracy za pomocą protokołu HTTP i HTTPS, wiele przeglądarek sieci web klienta obsługują tylko protokołu HTTP/2 za pośrednictwem zabezpieczeń TLS (Transport Layer).

### <a name="http2-benefits"></a>Korzyści z protokołu HTTP/2

Zalety protokołu HTTP/2:

*   **Funkcje multipleksowania i współbieżność**

    Przy użyciu protokołu HTTP 1.1, wysyłania wielu żądań zasobów wymaga wielu połączeń TCP, a każde połączenie jest zmniejszenie wydajności skojarzonych z nim. Protokołu HTTP/2 umożliwia wielu zasoby wymagane dla pojedynczego połączenia TCP.

*   **Kompresja nagłówków**

    Poprzez kompresowanie nagłówków HTTP obsługiwane zasobów, czasu przesyłania jest znacznie ograniczony.

*   **Stream zależności**

    Zależności Stream umożliwiają klientowi wskazanie serwerowi, zasoby, które mają priorytet.


## <a name="http2-browser-support"></a>Obsługa przeglądarek protokołu HTTP/2

Wszystkie popularne przeglądarki wdrożono Obsługa protokołu HTTP/2 w ich bieżącej wersji. Nieobsługiwany przeglądarek automatycznie rezerwowego protokołu HTTP/1.1.

|Przeglądarka|Minimalna wersja|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-front-door-service"></a>Włączanie obsługi protokołu HTTP/2 w usłudze Azure Service drzwi

Obecnie obsługa protokołu HTTP/2 jest aktywny w przypadku wszystkich konfiguracji wejściu. Nie są wymagane dalsze działania od klientów.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat protokołu HTTP/2, odwiedź następujące zasoby:

- [Strona główna specyfikacji protokołu HTTP/2](https://http2.github.io/)
- [Oficjalna protokołu HTTP/2 — często zadawane pytania](https://http2.github.io/faq/)
- Dowiedz się, jak [tworzenie drzwiami frontowymi](quickstart-create-front-door.md).
- Dowiedz się, [działania drzwiami frontowymi](front-door-routing-architecture.md).
