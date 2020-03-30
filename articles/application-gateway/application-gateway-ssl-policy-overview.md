---
title: Omówienie zasad protokołu TLS dla bramy aplikacji platformy Azure
description: Dowiedz się, jak skonfigurować zasady TLS dla usługi Azure Application Gateway i zmniejszyć obciążenie związane z szyfrowaniem i odszyfrowywaniem z farmy serwerów zaplecza.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 871cb930e867002d8af1e7755de27d4873327543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257380"
---
# <a name="application-gateway-tls-policy-overview"></a>Omówienie zasad TLS bramy aplikacji

Za pomocą usługi Azure Application Gateway można scentralizować zarządzanie certyfikatami TLS/SSL oraz zmniejszyć obciążenie związane z szyfrowaniem i odszyfrowywaniem z farmy serwerów zaplecza. Ta scentralizowana obsługa protokołu TLS umożliwia również określenie centralnej zasady TLS dostosowanej do wymagań zabezpieczeń organizacji. Pomaga to spełnić wymagania dotyczące zgodności, a także wskazówki dotyczące zabezpieczeń i zalecane praktyki.

Zasady TLS obejmują kontrolę nad wersją protokołu TLS, a także mechanizmami szyfrowania i kolejnością, w jakiej szyfry są używane podczas uzgadniania TLS. Brama aplikacji oferuje dwa mechanizmy kontrolowania zasad TLS. Można użyć wstępnie zdefiniowanych zasad lub zasad niestandardowych.

## <a name="predefined-tls-policy"></a>Wstępnie zdefiniowane zasady TLS

Brama aplikacji ma trzy wstępnie zdefiniowane zasady zabezpieczeń. Bramę można skonfigurować przy każdej z tych zasad, aby uzyskać odpowiedni poziom zabezpieczeń. Nazwy zasad są opisywane przez rok i miesiąc, w którym zostały skonfigurowane. Każda zasada oferuje różne wersje protokołów TLS i mechanizmy szyfrowania. Zaleca się korzystanie z najnowszych zasad TLS, aby zapewnić najlepsze zabezpieczenia TLS.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Właściwość  |Wartość  |
|---|---|
|Nazwa     | AppGwSslPolicy20150501        |
|Wersja MinProtocolVersion     | TLSv1_0        |
|Domyślne| Wartość true (jeśli nie określono wstępnie zdefiniowanych zasad) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Właściwość  |Wartość  |
|   ---      |  ---       |
|Nazwa     | AppGwSslPolicy20170401        |
|Wersja MinProtocolVersion     | TLSv1_1        |
|Domyślne| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Właściwość  |Wartość  |
|---|---|
|Nazwa     | AppGwSslPolicy20170401S        |
|Wersja MinProtocolVersion     | TLSv1_2        |
|Domyślne| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Niestandardowe zasady TLS

Jeśli wstępnie zdefiniowane zasady TLS muszą być skonfigurowane dla twoich wymagań, należy zdefiniować własne niestandardowe zasady TLS. Dzięki niestandardowej polityce TLS masz pełną kontrolę nad minimalną wersją protokołu TLS do obsługi, a także obsługiwanymi mechanizmami szyfrowania i ich kolejnością priorytetów.
 
### <a name="tlsssl-protocol-versions"></a>Wersje protokołów TLS/SSL

* SSL 2.0 i 3.0 są domyślnie wyłączone dla wszystkich bram aplikacji. Te wersje protokołu nie są konfigurowalne.
* Niestandardowe zasady TLS umożliwiają wybranie jednego z następujących trzech protokołów jako minimalnej wersji protokołu TLS dla bramy: TLSv1_0, TLSv1_1 i TLSv1_2.
* Jeśli nie zdefiniowano żadnych zasad TLS, wszystkie trzy protokoły (TLSv1_0, TLSv1_1 i TLSv1_2) są włączone.

### <a name="cipher-suites"></a>Zestawy szyfrowania

Brama aplikacji obsługuje następujące mechanizmy szyfrowania, z których można wybrać zasady niestandardowe. Kolejność mechanizmów szyfrowania określa kolejność priorytetów podczas negocjacji TLS.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> Mechanizmy szyfrowania TLS używane dla połączenia są również oparte na typie używanego certyfikatu. W przypadku połączeń bramy klient-aplikacja używane mechanizmy szyfrowania są oparte na typie certyfikatów serwera w detektorze bramy aplikacji. W bramie aplikacji do połączeń puli wewnętrznej bazy danych używane mechanizmy szyfrowania są oparte na typie certyfikatów serwera na serwerach puli wewnętrznej bazy danych.

## <a name="known-issue"></a>Znany problem
Brama aplikacji w wersji 2 nie obsługuje obecnie następujących szyfrów:
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>Następne kroki

Aby nauczyć się konfigurować zasady TLS, zobacz [Konfigurowanie wersji zasad TLS i pakietów szyfrowania w bramie aplikacji](application-gateway-configure-ssl-policy-powershell.md).
