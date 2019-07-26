---
title: Omówienie zasad SSL dla usługi Azure Application Gateway | Microsoft Docs
description: Dowiedz się, jak usługa Azure Application Gateway umożliwia konfigurowanie zasad SSL
services: application gateway
documentationcenter: na
author: amsriva
manager: ''
editor: ''
tags: azure resource manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
ms.date: 08/03/2017
ms.author: amsriva
ms.openlocfilehash: 1710635f145136e564a2bad48d539f242c9bc228
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359913"
---
# <a name="application-gateway-ssl-policy-overview"></a>Omówienie zasad protokołu SSL Application Gateway

Usługi Azure Application Gateway można używać do scentralizowanego zarządzania certyfikatami SSL i zmniejszania obciążenia szyfrowania i odszyfrowywania z farmy serwerów zaplecza. Ta Scentralizowana obsługa protokołu SSL umożliwia również określenie centralnych zasad protokołu SSL, które są odpowiednie dla wymagań bezpieczeństwa organizacji. Pomaga to spełnić wymagania dotyczące zgodności, a także wskazówki dotyczące zabezpieczeń i zalecane rozwiązania.

Zasady protokołu SSL obejmują kontrolę wersji protokołu SSL, a także mechanizmy szyfrowania i kolejność, w jakiej szyfry są używane podczas uzgadniania protokołu SSL. Application Gateway oferuje dwa mechanizmy kontrolowania zasad protokołu SSL. Można użyć wstępnie zdefiniowanych zasad lub zasad niestandardowych.

## <a name="predefined-ssl-policy"></a>Wstępnie zdefiniowane zasady SSL

Application Gateway ma trzy wstępnie zdefiniowane zasady zabezpieczeń. Bramę można skonfigurować przy użyciu dowolnej z tych zasad, aby uzyskać odpowiedni poziom zabezpieczeń. Nazwy zasad są oznaczane przez rok i miesiąc, w których zostały skonfigurowane. Każda zasada oferuje różne wersje protokołów SSL i mechanizmy szyfrowania. Zalecamy używanie najnowszych zasad protokołu SSL w celu zapewnienia najlepszego zabezpieczenia protokołu SSL.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Właściwość  |Wartość  |
|---|---|
|Name (Nazwa)     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Domyślny| True (jeśli nie określono żadnych wstępnie zdefiniowanych zasad) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Właściwość  |Value  |
|   ---      |  ---       |
|Name (Nazwa)     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Domyślny| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Właściwość  |Value  |
|---|---|
|Name (Nazwa)     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Domyślny| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Niestandardowe zasady protokołu SSL

Jeśli konieczne jest skonfigurowanie wstępnie zdefiniowanych zasad SSL dla wymagań, należy zdefiniować własne niestandardowe zasady protokołu SSL. Przy użyciu niestandardowych zasad protokołu SSL masz pełną kontrolę nad minimalną wersją protokołu SSL do obsługi, a także obsługiwanymi mechanizmami szyfrowania i ich kolejności priorytetów.
 
### <a name="ssl-protocol-versions"></a>Wersje protokołu SSL

* Protokoły SSL 2,0 i 3,0 są domyślnie wyłączone dla wszystkich bram aplikacji. Te wersje protokołu nie są konfigurowalne.
* Niestandardowe zasady protokołu SSL umożliwiają wybranie jednego z trzech następujących protokołów jako minimalnej wersji protokołu SSL dla bramy: TLSv1_0, TLSv1_1 i TLSv1_2.
* Jeśli żadna zasada SSL nie zostanie zdefiniowana, wszystkie trzy protokoły (TLSv1_0, TLSv1_1 i TLSv1_2) są włączone.

### <a name="cipher-suites"></a>Mechanizmy szyfrowania

Application Gateway obsługuje następujące mechanizmy szyfrowania, z których można wybrać zasady niestandardowe. Porządkowanie mechanizmów szyfrowania określa kolejność priorytetów podczas negocjowania protokołu SSL.


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
> Mechanizmy szyfrowania SSL używane na potrzeby połączenia są również oparte na typie używanego certyfikatu. W przypadku połączeń między klientem a bramą aplikacji używane mechanizmy szyfrowania są oparte na typie certyfikatów serwera w odbiorniku bramy aplikacji. W usłudze Application Gateway do połączeń puli zaplecza używane mechanizmy szyfrowania są oparte na typie certyfikatów serwera na serwerach puli zaplecza.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak skonfigurować zasady protokołu SSL, zobacz [Konfigurowanie zasad SSL w bramie aplikacji](application-gateway-configure-ssl-policy-powershell.md).
