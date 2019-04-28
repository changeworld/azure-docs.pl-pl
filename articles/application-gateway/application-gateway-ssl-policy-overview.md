---
title: Omówienie zasad protokołu SSL dla usługi Azure Application Gateway | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o jak usługi Azure Application Gateway umożliwia konfigurowanie zasad protokołu SSL
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
origin.date: 08/03/2017
ms.date: 02/26/2019
ms.author: v-junlch
ms.openlocfilehash: 46a823e4e230656b53a93a97f195d0879fd08bf2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122238"
---
# <a name="application-gateway-ssl-policy-overview"></a>Omówienie zasad SSL bramy aplikacji

Usługi Azure Application Gateway umożliwia scentralizowane zarządzanie certyfikatami SSL i zmniejszyć koszty szyfrowania i odszyfrowywania z farmy serwerów zaplecza. Scentralizowane Obsługa również SSL pozwala określić centralne zasady protokołu SSL, który jest odpowiedni do określonych wymagań dotyczących zabezpieczeń w organizacji. To ułatwia spełnianie wymagań dotyczących zgodności, a także wskazówki dotyczące zabezpieczeń i zalecane rozwiązania.

Zasady protokołu SSL zawierają kontroli wersji protokołu SSL, a także mechanizmów szyfrowania i kolejność, w którym mechanizmów szyfrowania są używane podczas uzgadniania protokołu SSL. Usługa Application Gateway oferuje dwa mechanizmy kontroli zasad protokołu SSL. Można użyć wstępnie zdefiniowanych zasad lub niestandardowych zasad.

## <a name="predefined-ssl-policy"></a>Wstępnie zdefiniowane zasady SSL

Usługa Application Gateway zawiera trzy wstępnie zdefiniowanych zasad. Bramy można skonfigurować za pomocą dowolnego z tych zasad, aby uzyskać odpowiedni poziom zabezpieczeń. Nazwy zasad posiadają adnotację, za rok i miesiąc, w którym zostały skonfigurowane. Każdej wersji protokołu SSL różnych ofert zasad i mechanizmów szyfrowania. Zalecamy użycie najnowsze zasady SSL aby zapewnić najlepsze zabezpieczenia SSL.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Właściwość  |Wartość  |
|---|---|
|Name (Nazwa)     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Domyślne| Wartość true, (Jeśli określono parametr ma wstępnie zdefiniowanych zasad) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Właściwość  |Wartość  |
|   ---      |  ---       |
|Name (Nazwa)     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Domyślne| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Właściwość  |Wartość  |
|---|---|
|Name (Nazwa)     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Domyślne| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Niestandardowe zasady protokołu SSL

Jeśli wstępnie zdefiniowanych zasad protokołu SSL musi być skonfigurowany zgodnie z wymaganiami dotyczącymi, należy zdefiniować własne niestandardowe zasady protokołu SSL. Niestandardowe zasady protokołu SSL masz pełną kontrolę nad minimalna wersja protokołu SSL do obsługi, a także obsługiwanych mechanizmów szyfrowania i ich kolejność według priorytetu.
 
### <a name="ssl-protocol-versions"></a>Wersji protokołu SSL

- Protokół SSL 2.0 i 3.0 są domyślnie wyłączone dla wszystkich bram aplikacji. Te wersje protokołu nie są konfigurowalne.
- Niestandardowe zasady protokołu SSL daje możliwość wybrania jednej z następujących trzech protokołów jako minimalnej wersji protokołu SSL dla bramy: TLS 1_0, TLS 1_1 i TLS 1_2.
- Jeśli żadna zasada SSL nie jest zdefiniowana, wszystkie trzy protokoły (TLS 1_0, TLS 1_1 i TLS 1_2) są włączone.

### <a name="cipher-suites"></a>Mechanizmy szyfrowania

Usługa Application Gateway obsługuje następujące mechanizmów szyfrowania, z których można wybrać zasad niestandardowych. Kolejność mechanizmów szyfrowania określa kolejność priorytetów podczas negocjacji w protokole SSL.


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

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz dowiedzieć się, jak skonfigurować zasady protokołu SSL, zobacz [Konfigurowanie zasad protokołu SSL w bramie aplikacji](application-gateway-configure-ssl-policy-powershell.md).

<!-- Update_Description: wording update -->