---
title: Prywatne linki na platformie Azure — często zadawane pytania
description: Dowiedz się więcej o usłudze Azure Private link.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 9239917599b40ac159e1cdbd4d3db6f6e70e12d2
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019046"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Prywatne linki na platformie Azure — często zadawane pytania

## <a name="private-link"></a>Link prywatny

### <a name="what-is-azure-private-link-service-and-private-endpoint"></a>Co to jest usługa Azure Private Link Service i prywatny punkt końcowy?

- **Prywatny punkt końcowy platformy Azure**: Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Możesz użyć prywatnych punktów końcowych, aby nawiązać połączenie z usługą Azure PaaS, która obsługuje link prywatny lub do własnej usługi linku prywatnego.
- **Usługa prywatnego połączenia platformy Azure**: Usługa link prywatny platformy Azure to usługa utworzona przez dostawcę usług. Obecnie usługa linku prywatnego może być dołączona do konfiguracji adresu IP frontonu usługa Load Balancer w warstwie Standardowa. 

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Czy mogę połączyć usługę z wieloma prywatnymi punktami końcowymi?
Tak. Jedna usługa linku prywatnego może odbierać połączenia z wielu prywatnych punktów końcowych. Jednak jeden prywatny punkt końcowy może nawiązać połączenie tylko z jedną usługą linku prywatnego.  
 
### <a name="is-data-transferred-over-private-link-always-private"></a>Czy dane przesyłane za pośrednictwem prywatnego linku są zawsze prywatne?
Tak. Wszystkie dane za pośrednictwem prywatnego linku platformy Azure pozostają w sieci szkieletowej firmy Microsoft. Nie przechodzi ona przez Internet.  
 
### <a name="what-is-the-difference-between-a-vnet-service-endpoint-and-a-private-endpoint"></a>Jaka jest różnica między punktem końcowym usługi sieci wirtualnej a prywatnym punktem końcowym?
- Punkty końcowe usługi sieci wirtualnej umożliwiają rozbudowanie prywatnej przestrzeni adresowej i tożsamość usługi Azure Virtual Network w ramach połączenia bezpośredniego. Punkty końcowe usługi umożliwiają Zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych, podczas gdy ruch jest zoptymalizowany pod kątem trasy i pozostaje w sieci szkieletowej firmy Microsoft, sdestined na publiczny adres IP usługi.
- Prywatny punkt końcowy to zasób sieciowy, który działa jako punkt wejścia w sieci wirtualnej i korzysta z prywatnego adresu IP w celu uzyskania dostępu do usług obsługiwanych przez link prywatny. Ten ruch jest zoptymalizowany pod kątem trasy i pozostaje w sieci szkieletowej firmy Microsoft.   

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Jaka jest relacja między usługą link prywatny i prywatnym punktem końcowym?
Jest to relacja jeden do wielu. Jedna usługa linku prywatnego może odbierać połączenia z wielu prywatnych punktów końcowych. Z drugiej strony jeden prywatny punkt końcowy może łączyć się tylko z jedną usługą linku prywatnego.    
 
### <a name="will-vnet-service-endpoints-be-deprecated-once-private-endpoints-are-available"></a>Czy punkty końcowe usługi sieci wirtualnej będą przestarzałe po udostępnieniu prywatnych punktów końcowych? 
Nie. Punkty końcowe usługi sieci wirtualnej i prywatne punkty końcowe są niezależnymi technologiami/zasobami. Mogą one uzupełniać siebie nawzajem i oba będą współistnieć. Niektóre funkcje i przypadki użycia mogą nakładać się na siebie, ale można wybrać model, który odpowiada Twoim potrzebom.  
 
### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Jestem dostawcą usług korzystającym z prywatnego linku platformy Azure. Czy muszę mieć pewność, że wszyscy klienci mają unikatową przestrzeń adresów IP i nie nakładają się na przestrzeń adresową IP? 
Nie. Usługa Azure Private Link udostępnia te funkcje. W związku z tym nie trzeba mieć nienakładających się przestrzeni adresowych z przestrzenią adresową klienta. 
 
## <a name="private-link-service"></a>Usługa link prywatny
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Jakie są wymagania wstępne dotyczące tworzenia usługi linku prywatnego? 
Frontony usługi powinny znajdować się w sieci wirtualnej i za usługa Load Balancer w warstwie Standardowa.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Jak mogę skalować moją usługę linku prywatnego? 
Usługę linku prywatnego można skalować na kilka różnych sposobów: 
- Dodaj maszyny wirtualne zaplecza do puli za usługa Load Balancer w warstwie Standardowa 
- Dodaj adres IP do usługi łącza prywatnego. Zezwalamy na maksymalnie 8 adresów IP na usługę łącza prywatnego.  
- Dodaj nową usługę linku prywatnego do usługa Load Balancer w warstwie Standardowa. Zezwalamy na maksymalnie osiem usług linków prywatnych na moduł równoważenia obciążenia.   


### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Jak należy kontrolować ekspozycję mojej usługi linku prywatnego?
Można kontrolować narażenie przy użyciu konfiguracji widoczności w usłudze link prywatny. Widoczność obsługuje trzy ustawienia:

- **Brak** — tylko subskrypcje z dostępem RBAC mogą zlokalizować usługę. 
- Tylko **restrykcyjne** subskrypcje listy dozwolonych i z dostępem RBAC mogą zlokalizować usługę. 
- **Wszyscy — wszyscy** mogą zlokalizować usługę. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Czy można utworzyć usługę linku prywatnego z podstawową Load Balancer? 
Nie. Usługa linku prywatnego za pośrednictwem podstawowego Load Balancer nie jest obsługiwana.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Czy dedykowana podsieć jest wymagana dla usługi link prywatny? 
Nie. Usługa link prywatny nie wymaga dedykowanej podsieci. Możesz wybrać dowolną podsieć w sieci wirtualnej, w której została wdrożona usługa.   

## <a name="private-endpoint"></a>Prywatny punkt końcowy 
 
### <a name="cani-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>CanI utworzyć wiele prywatnych punktów końcowych w tej samej sieci wirtualnej? Czy mogą łączyć się z różnymi usługami? 
Tak. W tej samej sieci wirtualnej lub podsieci można mieć wiele prywatnych punktów końcowych. Mogą łączyć się z różnymi usługami.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Czy dla prywatnych punktów końcowych jest wymagana dedykowana podsieć? 
Nie. Nie jest wymagana dedykowana podsieć dla prywatnych punktów końcowych. Możesz wybrać prywatny adres IP punktu końcowego z dowolnej podsieci z sieci wirtualnej, w której została wdrożona usługa.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Czy prywatny punkt końcowy nawiązuje połączenie z usługą link prywatny w ramach dzierżaw Azure Active Directory? 
Tak. Prywatne punkty końcowe mogą łączyć się z usługami linków prywatnych lub Azure PaaS w ramach dzierżaw usługi AD.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Czy prywatny punkt końcowy nawiązuje połączenie z zasobami usługi Azure PaaS w różnych regionach platformy Azure?
Tak. Prywatne punkty końcowe mogą łączyć się z zasobami usługi Azure PaaS w różnych regionach platformy Azure.

##  <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Private link](private-link-overview.md)
