---
title: Obsługa protokołu HTTP/2 w usłudze Azure CDN | Dokumentacja firmy Microsoft
description: Więcej informacji na temat obsługi protokołu HTTP/2 i usługi CDN.
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
ms.author: rli
ms.openlocfilehash: c59ecceee35587f1b7d844f160ac0f61c5288e0e
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593746"
---
# <a name="http2-support-in-azure-cdn"></a>Obsługa protokołu HTTP/2 w usłudze Azure CDN

Protokołu HTTP/2 jest znaczne zmiany do HTTP/1.1\. Zapewnia ona szybsze wydajności sieci web, czas reakcji mniejsze i ulepszone środowisko pracy, przy zachowaniu znanych metod HTTP, kodów stanu i semantyki. Chociaż protokołu HTTP/2 jest przeznaczona do pracy za pomocą protokołu HTTP i HTTPS, wiele przeglądarek sieci web klienta obsługują tylko protokołu HTTP/2 za pośrednictwem protokołu TLS.

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

|Browser|Minimalna wersja|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Włączanie obsługi protokołu HTTP/2 w usłudze Azure CDN

Obecnie obsługa protokołu HTTP/2 jest aktywny dla wszystkich profilów usługi Azure CDN. Nie są wymagane dalsze działania od klientów.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z zalet protokołu HTTP/2 w akcji, zobacz [tej wersji demonstracyjnej Akamai](https://http2.akamai.com/demo).

Aby dowiedzieć się więcej na temat protokołu HTTP/2, odwiedź następujące zasoby:

*   [Strona główna specyfikacji protokołu HTTP/2](https://http2.github.io/)
*   [Oficjalna protokołu HTTP/2 — często zadawane pytania](https://http2.github.io/faq/)
*   [Informacje o Akamai protokołu HTTP/2](https://http2.akamai.com/)

Aby dowiedzieć się więcej na temat dostępności funkcji usługi Azure CDN, zobacz [Omówienie usługi CDN Azure](https://azure.microsoft.com/documentation/articles/cdn-overview/).