---
title: Często zadawane pytania dotyczące usługi Azure Private Link (często zadawane pytania)
description: Dowiedz się więcej o łączu prywatnym platformy Azure.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7870b68ca931123d50e88e846aa066ce53972dbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349937"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Często zadawane pytania dotyczące usługi Azure Private Link (często zadawane pytania)

## <a name="private-link"></a>Link prywatny

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Co to jest prywatny punkt końcowy platformy Azure i usługa azure private link?

- **[Prywatny punkt końcowy platformy Azure:](private-endpoint-overview.md)** Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Prywatne punkty końcowe umożliwiają łączenie się z usługą Azure PaaS obsługującą łącze prywatne lub z własną usługą łącza prywatnego.
- **[Usługa Azure Private Link Service:](private-link-service-overview.md)** Usługa Azure Private Link to usługa utworzona przez dostawcę usług. Obecnie usługę Private Link można dołączyć do konfiguracji IP frontonu standardowego modułu równoważenia obciążenia. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>W jaki sposób ruch jest wysyłany podczas korzystania z łącza prywatnego?
Ruch jest wysyłany prywatnie przy użyciu szkieletu firmy Microsoft. Nie przechodzi przez Internet.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Jaka jest różnica między punktami końcowymi usługi a prywatnymi punktami końcowymi?
- Korzystając z prywatnych punktów końcowych, dostęp do sieci jest przyznawany określonym zasobom za daną usługą zapewniającą szczegółową segmentację, a także ruch może dotrzeć do zasobu usługi lokalnie bez korzystania z publicznych punktów końcowych.
- Punkt końcowy usługi pozostaje publicznie rutowalnym adresem IP.  Prywatny punkt końcowy to prywatny adres IP w przestrzeni adresowej sieci wirtualnej, w której skonfigurowany jest prywatny punkt końcowy.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Jaka jest relacja między usługą Private Link a prywatnym punktem końcowym?
Prywatny punkt końcowy zapewnia dostęp do wielu typów zasobów łączy prywatnych, w tym usług PaaS platformy Azure i własnej usługi linków prywatnych. Jest to relacja jeden do wielu. Jedna usługa Private Link może odbierać połączenia z wielu prywatnych punktów końcowych. Z drugiej strony jeden prywatny punkt końcowy może łączyć się tylko z jedną usługą Private Link.    

## <a name="private-endpoint"></a>Prywatny punkt końcowy 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Czy można utworzyć wiele prywatnych punktów końcowych w tej samej sieci wirtualnej? Czy mogą łączyć się z różnymi usługami? 
Tak. Może mieć wiele prywatnych punktów końcowych w tej samej sieci wirtualnej lub podsieci. Mogą łączyć się z różnymi usługami.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Czy potrzebuję dedykowanej podsieci dla prywatnych punktów końcowych? 
Nie. Nie potrzebujesz dedykowanej podsieci dla prywatnych punktów końcowych. Można wybrać adres IP prywatnego punktu końcowego z dowolnej podsieci z sieci wirtualnej, w której usługa jest wdrażana.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Czy prywatny punkt końcowy może łączyć się z usługą łącze prywatne w dzierżawach usługi Azure Active Directory? 
Tak. Prywatne punkty końcowe mogą łączyć się z usługami private link lub usługą Azure PaaS w dzierżawach usługi AD.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Czy prywatny punkt końcowy może łączyć się z zasobami usługi Azure PaaS w regionach platformy Azure?
Tak. Prywatne punkty końcowe mogą łączyć się z zasobami usługi Azure PaaS w regionach platformy Azure.

## <a name="private-link-service"></a>Usługa łącza prywatnego
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Jakie są wymagania wstępne dotyczące tworzenia usługi Private Link? 
Zaplecze usługi powinny znajdować się w sieci wirtualnej i za modułem równoważenia obciążenia standardowego.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Jak skalować usługę Private Link? 
Usługę Private Link można skalować na kilka różnych sposobów: 
- Dodawanie maszyn wirtualnych zaplecza do puli za modułem równoważenia obciążenia standardowego 
- Dodaj adres IP do usługi Łącze prywatne. Zezwalamy na maksymalnie 8 adresów IP na usługę Private Link.  
- Dodaj nową usługę Private Link do standardowego modułu równoważenia obciążenia. Zezwalamy na maksymalnie osiem usług Private Link na moduł równoważenia obciążenia.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Czy mogę połączyć moją usługę z wieloma prywatnymi punktami końcowymi?
Tak. Jedna usługa Private Link może odbierać połączenia z wielu prywatnych punktów końcowych. Jednak jeden prywatny punkt końcowy może łączyć się tylko z jedną usługą Private Link.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Jak należy kontrolować ekspozycję usługi Private Link?
Ekspozycję można kontrolować za pomocą konfiguracji widoczności w usłudze Private Link. Widoczność obsługuje trzy ustawienia:

- **Brak** — tylko subskrypcje z dostępem RBAC mogą zlokalizować usługę. 
- **Restrykcyjne** — tylko subskrypcje, które są na białej liście i z dostępem RBAC można zlokalizować usługi. 
- **Wszystko** — każdy może zlokalizować usługę. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Czy można utworzyć usługę Private Link z podstawowym modułem równoważenia obciążenia? 
Nie. Usługa Private Link za pomocą podstawowego modułu równoważenia obciążenia nie jest obsługiwana.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Czy dedykowana podsieć jest wymagana dla usługi Private Link? 
Nie. Usługa Private Link nie wymaga dedykowanej podsieci. Można wybrać dowolną podsieć w sieci wirtualnej, w której usługa jest wdrażana.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Jestem usługodawcą korzystającym z usługi Azure Private Link. Czy muszę upewnić się, że wszyscy moi klienci mają unikatową przestrzeń IP i nie pokrywają się z moją przestrzenią IP? 
Nie. Usługa Azure Private Link udostępnia tę funkcję. W związku z tym nie są wymagane, aby mieć nienakładającą się przestrzeń adresową z przestrzenią adresową klienta. 

##  <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [łączu prywatnym platformy Azure](private-link-overview.md)
