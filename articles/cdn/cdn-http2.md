---
title: Obsługa protokołu HTTP/2 w usłudze Azure CDN | Dokumentacja firmy Microsoft
description: Więcej informacji na temat obsługi protokołu HTTP/2 i CDN.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: rli
ms.openlocfilehash: b4751320af82a29fb13dc6012c1b197ebc2b1f9b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="http2-support-in-azure-cdn"></a>Obsługa protokołu HTTP/2 w usługi Azure CDN

HTTP/2 jest znaczne zmiany do HTTP/1.1\. Zapewnia szybszy wydajności sieci web, czas odpowiedzi obniżona i lepsze środowisko, przy zachowaniu znanych metod HTTP, kodów stanu i semantyki. Chociaż HTTP/2 jest przeznaczony do pracy z protokołów HTTP i HTTPS, wiele przeglądarek sieci web klienta obsługują tylko HTTP/2 za pośrednictwem protokołu TLS.

### <a name="http2-benefits"></a>Korzyści HTTP/2

Zalety HTTP/2:

*   **Multipleksowanie i współbieżność**

    Przy użyciu protokołu HTTP 1.1, wprowadzania wielu żądań zasobów wymaga wielu połączeń TCP, a każde połączenie ma zmniejszenie wydajności związane z nią. HTTP/2 umożliwia wielu zasobów wymagane dla pojedynczego połączenia TCP.

*   **Kompresja nagłówka**

    Przez kompresowanie nagłówków HTTP obsługiwane zasobów, czas przesyłania jest znacznie ograniczony.

*   **Zależności strumienia**

    Zależności strumienia pozwalają klientowi wskazują na serwer zasoby, które mają priorytet.


## <a name="http2-browser-support"></a>Obsługa przeglądarek HTTP/2

Wszystkie główne przeglądarki wdrożono Obsługa HTTP/2 w ich bieżącej wersji. Nieobsługiwany przeglądarek automatycznie powrotu do HTTP/1.1.

|Przeglądarka|Minimalna wersja|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Włączanie obsługi protokołu HTTP/2 w Azure CDN

Obecnie aktywne dla wszystkich profilów usługi Azure CDN jest obsługa protokołu HTTP/2. Żadne dalsze akcje nie jest wymagana od klientów.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z zalet HTTP/2 w akcji, zobacz [ten pokaz from Akamai](https://http2.akamai.com/demo).

Aby dowiedzieć się więcej na temat protokołu HTTP/2, można znaleźć w następujących zasobach:

*   [Strona główna specyfikacji HTTP/2](https://http2.github.io/)
*   [Oficjalna HTTP/2 — często zadawane pytania](https://http2.github.io/faq/)
*   [Informacje o Akamai HTTP/2](https://http2.akamai.com/)

Aby dowiedzieć się więcej na temat dostępnych funkcji usługi Azure CDN, zobacz [Omówienie usługi Azure CDN](https://azure.microsoft.com/documentation/articles/cdn-overview/).