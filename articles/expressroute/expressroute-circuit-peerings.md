---
title: Usługa Azure ExpressRoute obwody i domeny routingu | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie obwody usługi ExpressRoute i domeny routingu.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: 6f0c5d8e-cc60-4a04-8641-2c211bda93d9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: ganesr,cherylmc
ms.openlocfilehash: be04a1cd723cf27e764daa468607d6495baf0291
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849934"
---
# <a name="expressroute-circuits-and-routing-domains"></a>Obwody usługi ExpressRoute i domeny routingu
 Należy zamówić *obwodu ExpressRoute* do podłączenia infrastruktury lokalnej do firmy Microsoft za pośrednictwem dostawcy łączności. Na poniższej ilustracji przedstawiono logiczną reprezentację połączenia między sieci WAN i firmy Microsoft.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

## <a name="expressroute-circuits"></a>Obwody usługi ExpressRoute
*Obwodu ExpressRoute* reprezentuje połączenie logiczne między lokalną infrastrukturą i usługi w chmurze firmy Microsoft za pośrednictwem dostawcy łączności. Można zamówić wielu obwody usługi ExpressRoute. Każdy obwód mogą znajdować się w tej samej lub różnych regionów, a można podłączyć do lokalnej za pośrednictwem połączenia różnych dostawców. 

Obwody usługi ExpressRoute nie są mapowane na żadnych jednostek fizycznych. Obwód jest unikatowo identyfikowana przez standardowej GUID o nazwie jako klucz usługi (s-key). Klucz usługi to jedyna wymieniane między firmą Microsoft, dostawca połączenia, a informacje. Klucz s nie jest klucz tajny ze względów bezpieczeństwa. Brak mapowania 1:1 między obwodu usługi ExpressRoute i klucz s.

Obwodu usługi ExpressRoute może mieć maksymalnie trzech niezależnych komunikacji równorzędnych: Azure prywatnego publiczne, Azure i firmy Microsoft. Każdy równorzędna to dwa niezależne BGP sesji ich nadmiarowo skonfigurowany pod kątem wysokiej dostępności. Brak 1: n (1 < = N < = 3) mapowanie między obwodu usługi ExpressRoute i domeny routingu. Obwodu usługi ExpressRoute może mieć jeden, dwa lub wszystkich trzech komunikacji równorzędnych włączone dla obwodu usługi expressroute.

Każdy obwód ma stały przepustowości (50 MB/s, 100 MB/s, 200 MB/s, 500 MB/s, 1 GB/s, 10 GB/s) i jest mapowany na dostawca połączenia i lokalizacja komunikacji równorzędnej. Przepustowości, którą wybierzesz jest współużytkowana przez wszystkie komunikacji równorzędnych dla obwodu. 

### <a name="quotas-limits-and-limitations"></a>Przydziały, ograniczenia i ograniczenia
Domyślne przydziały i limity mają zastosowanie dla każdego obwodu usługi expressroute. Zapoznaj się [subskrypcji platformy Azure i ograniczenia usługi, przydziały i ograniczenia](../azure-subscription-service-limits.md) stronę aktualne informacje dotyczące przydziałów.

## <a name="expressroute-routing-domains"></a>Domeny routingu usługi ExpressRoute
Obwodu usługi ExpressRoute ma wiele domen routingu skojarzonych z nim: Azure prywatnego publiczne, Azure i firmy Microsoft. Wszystkich domen routingu jest skonfigurowane tak samo w parze składającej się z routerów (aktywny aktywny lub udostępniania obciążenia konfiguracji) wysokiej dostępności. Usług platformy Azure są sklasyfikowane jako *Azure publicznego* i *Azure prywatnego* do reprezentowania schematów adresowania IP.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a>Azure prywatnej komunikacji równorzędnej
Usługi, a mianowicie maszyn wirtualnych (IaaS) rozwiązań usługi obliczenia Azure i usługi w chmurze (PaaS), które są wdrażane w ramach sieci wirtualnej mogą zostać połączone za pośrednictwem prywatnej komunikacji równorzędnej domeny. Prywatnej komunikacji równorzędnej domeny jest uważany za zaufany rozszerzenia sieci podstawowej w Microsoft Azure. Można skonfigurować dwukierunkowej łączności między sieci podstawowej i sieci wirtualnych platformy Azure (sieci wirtualne). Komunikacji równorzędnej umożliwia nawiązać połączenia z maszynami wirtualnymi i usługami bezpośrednio na swoich prywatnych adresów IP w chmurze.  

Możesz połączyć więcej niż jedną sieć wirtualną do domeny prywatnej komunikacji równorzędnej. Przegląd [z często Zadawanymi pytaniami](expressroute-faqs.md) informacji na temat limity i ograniczenia. Użytkownik może odwiedzić [subskrypcji platformy Azure i ograniczenia usługi, przydziały i ograniczenia](../azure-subscription-service-limits.md) stronę aktualnych informacji o granicach.  Zapoznaj się [Routing](expressroute-routing.md) strony, aby uzyskać szczegółowe informacje o konfiguracji routingu.

### <a name="azure-public-peering"></a>Azure publicznej komunikacji równorzędnej

> [!IMPORTANT]
> Wszystkie usługi PaaS platformy Azure są również dostępne za pośrednictwem komunikacji równorzędnej firmy Microsoft. Zalecamy utworzenie komunikacji równorzędnej firmy Microsoft i nawiązanie połączenia z usługami PaaS platformy Azure za pośrednictwem komunikacji równorzędnej firmy Microsoft.  
>   


Usługi, takie jak usługi Azure Storage, baz danych i witryn sieci Web są oferowane na publiczne adresy IP. Umożliwia nawiązywanie prywatnie usług hostowanych na publiczne adresy IP, w tym adresy VIP usług chmury, za pośrednictwem publicznej komunikacji równorzędnej domeny routingu. Możesz łączyć publicznej komunikacji równorzędnej domeny do sieci Obwodowej i połączyć do wszystkich usług platformy Azure na publiczne adresy IP z sieci WAN, bez konieczności używania połączenia internetowego. 

Połączenie jest zawsze inicjowane z sieci WAN do usług Microsoft Azure. Usługi Microsoft Azure nie będą mogły inicjować połączeń do sieci za pomocą tej domeny routingu. Po włączeniu publicznej komunikacji równorzędnej, można połączyć się do wszystkich usług platformy Azure. Firma Microsoft nie umożliwiają selektywne wybierz usługi, dla których firma Microsoft anonsować tras.

W sieci, użycie trasy, które są potrzebne, można zdefiniować filtry tras niestandardowych. Zapoznaj się [Routing](expressroute-routing.md) strony, aby uzyskać szczegółowe informacje o konfiguracji routingu. 

Aby uzyskać więcej informacji o usługach obsługiwane za pośrednictwem publicznej komunikacji równorzędnej domeny routingu, zobacz [— często zadawane pytania](expressroute-faqs.md).

### <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Łączności z usługami online firmy Microsoft (usługi Office 365, Dynamics 365 i Azure PaaS) jest za pomocą komunikacji równorzędnej firmy Microsoft. Firma Microsoft Włącz dwukierunkowej łączności między usług w chmurze firmy Microsoft i WAN za pośrednictwem domeny routingu komunikacji równorzędnej firmy Microsoft. Wymagane jest połączenie z usługi w chmurze firmy Microsoft tylko w przypadku publicznych adresów IP, które należą do firmy przez użytkownika lub dostawcą połączenia i musi być zgodne zdefiniowanych reguł. Aby uzyskać więcej informacji, zobacz [wymagania wstępne usługi ExpressRoute](expressroute-prerequisites.md) strony.

Zobacz [z często Zadawanymi pytaniami](expressroute-faqs.md) Aby uzyskać więcej informacji na temat usługi obsługiwane, kosztów i szczegóły konfiguracji. Zobacz [lokalizacje ExpressRoute](expressroute-locations.md) strony, aby uzyskać informacje na liście dostawców łączności oferty komunikacji równorzędnej pomocy technicznej firmy Microsoft.

## <a name="routing-domain-comparison"></a>Porównanie domeny routingu
W poniższej tabeli porównano trzy domeny routingu:

|  | **Prywatnej komunikacji równorzędnej** | **Publicznej komunikacji równorzędnej** (uznane za przestarzałe w tworzenie nowych) | **Komunikacji równorzędnej firmy Microsoft** |
| --- | --- | --- | --- |
| **Maks. prefiksy # obsługiwane na komunikacji równorzędnej** |4000 domyślnie 10 000 z ExpressRoute — wersja Premium |200 |200 |
| **Obsługiwane zakresów adresów IP** |Dowolny prawidłowy adres IP w sieci WAN. |Publiczne adresy IP należące do przez użytkownika lub dostawcą połączenia. |Publiczne adresy IP należące do przez użytkownika lub dostawcą połączenia. |
| **JAKO liczba wymagań** |Prywatne i publiczne jako liczby. Musi być właścicielem publicznego jako numer, jeśli chcesz używać jednego. |Prywatne i publiczne jako liczby. Jednak musisz udowodnić własność publicznych adresów IP. |Prywatne i publiczne jako liczby. Jednak musisz udowodnić własność publicznych adresów IP. |
| **Obsługiwane protokoły IP**| Protokół IPv4 | Protokół IPv4 | IPv4, IPv6 |
| **Routingu adresów IP interfejsu** |RFC1918 i publiczne adresy IP |Publiczne adresy IP, w zarejestrowany w rejestrach routingu. |Publiczne adresy IP, w zarejestrowany w rejestrach routingu. |
| **Obsługa wyznaczania wartości skrótu MD5** |Yes |Yes |Yes |



Można włączyć jedną lub więcej domen routingu w ramach obwodu usługi ExpressRoute. Można mieć wszystkich domen routingu umieszczanie na tej samej sieci VPN, jeśli chcesz połączyć je w jednej domeny routingu. Można również wprowadzić je w różnych domenach routingu, podobne do diagramu. Zalecana konfiguracja jest prywatnej komunikacji równorzędnej jest podłączony bezpośrednio do sieci podstawowej, czy publicznego i łączy komunikacji równorzędnej firmy Microsoft są podłączone do sieci Obwodowej.

Jeśli wybierzesz opcję wszystkie trzy sesje komunikacji równorzędnej, muszą mieć trzy pary sesje BGP (jedna para dla każdego typu komunikacji równorzędnej). Pary sesji BGP podaj łącze wysokiej dostępności. Jeśli łączysz się za pośrednictwem warstwy 2 łączności dostawców, jest odpowiedzialny za konfigurowanie i zarządzanie nimi routingu. Dowiedz się więcej, przeglądając [przepływy pracy](expressroute-workflows.md) dotyczące konfigurowania usługi ExpressRoute.

## <a name="expressroute-health"></a>Kondycja usługi ExpressRoute
Obwody usługi ExpressRoute może być monitorowany, dostępność, łączność sieci wirtualnych i użyciu wykorzystania przepustowości [monitora wydajności sieci](https://docs.microsoft.com/en-us/azure/networking/network-monitoring-overview) (NPM).

NPM monitoruje kondycję Azure prywatnej komunikacji równorzędnej i komunikacji równorzędnej firmy Microsoft.  Zapoznaj się z naszym [post](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki
* Znajdź dostawcę usługi. Zobacz [usługi ExpressRoute dostawców i lokalizacje](expressroute-locations.md).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).
* Skonfiguruj połączenie usługi ExpressRoute.
  * [Tworzenie obwodów usługi ExpressRoute i zarządzanie nimi](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurowanie routingu (komunikacji równorzędnej) dla obwodów usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)

