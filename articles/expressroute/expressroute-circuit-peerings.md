---
title: 'Azure ExpressRoute: obwody i komunikacja równorzędna'
description: Ta strona zawiera omówienie obwodów usługi ExpressRoute i domen routingu/komunikacji równorzędnej.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281355"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute circuits and peering (Obwody usługi ExpressRoute i komunikacja równorzędna)

Obwody usługi ExpressRoute łączą infrastrukturę lokalną z firmą Microsoft za pośrednictwem dostawcy łączności. Ten artykuł ułatwia zrozumienie obwodów usługi ExpressRoute i domen routingu/komunikacji równorzędnej. Na poniższej ilustracji przedstawiono logiczną reprezentację łączności między siecią WAN a firmą Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Publiczne udostępnianie komunikacji równorzędnej platformy Azure zostało przestarzałe i nie jest dostępne dla nowych obwodów usługi ExpressRoute. Nowe obwody obsługują komunikację równorzędnie firmy Microsoft i prywatną komunikację równorzędnych.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>Obwody usługi ExpressRoute

Obwód usługi ExpressRoute reprezentuje logiczne połączenie między infrastrukturą lokalną a usługami w chmurze firmy Microsoft za pośrednictwem dostawcy łączności. Można zamówić wiele obwodów usługi ExpressRoute. Każdy obwód może znajdować się w tym samym lub różnych regionach i może być połączony z lokalem za pośrednictwem różnych dostawców łączności.

Obwody usługi ExpressRoute nie są mapowane na żadne elementy fizyczne. Obwód jest jednoznacznie identyfikowany przez standardowy identyfikator GUID nazywany jako klucz usługi (klucz s). Klucz usługi jest jedyną informacją wymienianą między firmą Microsoft, dostawcą łączności i użytkownikiem. Klucz s nie jest tajemnicą ze względów bezpieczeństwa. Między obwodem usługi ExpressRoute a klawiszem s jest dostępne mapowanie 1:1.

Nowe obwody usługi ExpressRoute mogą obejmować dwie niezależne komunikacji równorzędnej: prywatną komunikację równorzędności i komunikację równorzędności firmy Microsoft. Istniejące obwody usługi ExpressRoute mogą zawierać trzy komunikacji równorzędnej: Azure Public, Azure Private i Microsoft. Każda komunikacja równorzędna jest parą niezależnych sesji BGP, z których każda jest konfigurowana nadmiarowo w celu zapewnienia wysokiej dostępności. Istnieje mapowanie 1:N (1 <= N <= 3) między obwodem usługi ExpressRoute a domenami routingu. Obwód usługi ExpressRoute może mieć jeden, dwa lub wszystkie trzy komunikacji równorzędnej włączone na obwód usługi ExpressRoute.

Każdy obwód ma stałą przepustowość (50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 10 Gb/s) i jest mapowany na dostawcę łączności i lokalizację komunikacji równorzędnej. Wybrana przepustowość jest współużytkowana przez wszystkie komunikacji równorzędnej obwodu

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Przydziały, limity i ograniczenia

Domyślne przydziały i limity dotyczą każdego obwodu usługi ExpressRoute. Aby uzyskać aktualne informacje na temat przydziałów, zapoznaj się ze stroną [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia.](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="expressroute-peering"></a><a name="routingdomains"></a>Komunikacja równorzędna usługi ExpressRoute

Obwód usługi ExpressRoute ma wiele domen routingu/komunikacji równorzędnej skojarzonych z nim: publiczna platforma Azure, platforma azure prywatna i firma Microsoft. Każda komunikacja równorzędna jest skonfigurowana identycznie na parze routerów (w konfiguracji active-active lub load sharing) w celu uzyskania wysokiej dostępności. Usługi platformy Azure są klasyfikowane jako publiczne platformy *Azure* i *platformy Azure prywatne* do reprezentowania schematów adresowania IP.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Prywatna komunikacja równorzędna Azure

Usługi obliczeniowe platformy Azure, a mianowicie maszyny wirtualne (IaaS) i usługi w chmurze (PaaS), które są wdrażane w sieci wirtualnej, mogą być połączone za pośrednictwem prywatnej domeny komunikacji równorzędnej. Prywatna domena komunikacji równorzędnej jest uważana za zaufane rozszerzenie sieci podstawowej na platformę Microsoft Azure. Można skonfigurować dwukierunkową łączność między siecią bazową a sieciami wirtualnymi platformy Azure(VNets). Ta komunikacja równorzędna umożliwia łączenie się z maszynami wirtualnymi i usługami w chmurze bezpośrednio na ich prywatnych adresach IP.  

Do prywatnej domeny komunikacji równorzędnej można połączyć więcej niż jedną sieć wirtualną. Przejrzyj [stronę często zadawanych pytań,](expressroute-faqs.md) aby uzyskać informacje na temat ograniczeń i ograniczeń. Możesz odwiedzić [stronę Subskrypcja i limity usług platformy Azure, przydziały i ograniczenia,](../azure-resource-manager/management/azure-subscription-service-limits.md) aby uzyskać aktualne informacje na temat limitów.  Szczegółowe informacje na temat konfiguracji routingu można znaleźć na stronie [Routing.](expressroute-routing.md)

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Komunikacja równorzędna firmy Microsoft

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Łączność z usługami online firmy Microsoft (usługi Office 365 i usługi Azure PaaS) odbywa się za pośrednictwem komunikacji równorzędnej firmy Microsoft. Umożliwiamy dwukierunkową łączność między usługami w chmurze sieci WAN i Microsoft za pośrednictwem domeny routingu komunikacji równorzędnej firmy Microsoft. Należy połączyć się z usługami w chmurze firmy Microsoft tylko za pomocą publicznych adresów IP, które są własnością użytkownika lub dostawcy łączności i należy przestrzegać wszystkich zdefiniowanych reguł. Aby uzyskać więcej informacji, zobacz stronę [wymagań wstępnych usługi ExpressRoute.](expressroute-prerequisites.md)

Więcej informacji na temat obsługiwanych usług, kosztów i szczegółów konfiguracji można znaleźć na [stronie często zadawanych](expressroute-faqs.md) pytań. Informacje na temat listy dostawców łączności oferujących pomoc techniczną firmy Microsoft można znaleźć na stronie [Lokalizacje usługi ExpressRoute.](expressroute-locations.md)

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Porównanie komunikacji równorzędnej

W poniższej tabeli porównano trzy komunikacji równorzędnej:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

W ramach obwodu usługi ExpressRoute można włączyć co najmniej jedną domenę routingu. Jeśli chcesz połączyć je w jedną domenę routingu, możesz wybrać, że wszystkie domeny routingu są umieszczane w tej samej sieci VPN. Można również umieścić je w różnych domenach routingu, podobnie jak diagram. Zalecana konfiguracja jest taka, że prywatna komunikacja równorzędna jest połączona bezpośrednio z siecią bazową, a łącza komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft są połączone z witryną DMZ.

Każda komunikacja równorzędna wymaga oddzielnych sesji BGP (jedna para dla każdego typu komunikacji równorzędnej). Pary sesji BGP zapewniają łącze o wysokiej dostępności. Jeśli łączysz się za pośrednictwem dostawców łączności warstwy 2, użytkownik jest odpowiedzialny za konfigurowanie routingu i zarządzanie nim. Więcej informacji można dowiedzieć się więcej, przeglądając [przepływy pracy](expressroute-workflows.md) dotyczące konfigurowania usługi ExpressRoute.

## <a name="expressroute-health"></a><a name="health"></a>Kondycja usługi ExpressRoute

Obwody usługi ExpressRoute mogą być monitorowane pod kątem dostępności, łączności z sieciami wirtualnymi i wykorzystania przepustowości za pomocą [Monitora wydajności sieci](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM).

Serwer NPM monitoruje kondycję prywatnej komunikacji równorzędnej platformy Azure i komunikacji równorzędnej firmy Microsoft. Sprawdź nasz [post,](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) aby uzyskać więcej informacji.

## <a name="next-steps"></a>Następne kroki

* Znajdź dostawcę usługi. Zobacz [dostawców usług i lokalizacje usługi Usługi Usługi ExpressRoute](expressroute-locations.md).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).
* Skonfiguruj połączenie usługi ExpressRoute.
  * [Tworzenie obwodów usługi ExpressRoute i zarządzanie nimi](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurowanie routingu (komunikacji równorzędnej) dla obwodów usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
