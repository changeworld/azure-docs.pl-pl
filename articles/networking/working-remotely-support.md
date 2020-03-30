---
title: Praca zdalna przy użyciu usług sieciowych platformy Azure
description: Na tej stronie opisano, jak można korzystać z usług sieciowych platformy Azure, które są dostępne, aby umożliwić pracę zdalną i jak ograniczyć problemy z ruchem wynikające ze zwiększonej liczby osób pracujących zdalnie.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 78081329b70edff5ed2c728cb2f7c3e562a44e56
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346284"
---
# <a name="working-remotely-using-azure-networking-services"></a>Praca zdalna przy użyciu usług sieciowych platformy Azure

>[!NOTE]
> W tym artykule opisano, jak można korzystać z usług sieci platformy Azure, sieci firmy Microsoft i ekosystemu partnerów platformy Azure do pracy zdalnej i łagodzenia problemów sieciowych, które mogą być narażona z powodu kryzysu COVID-19.

W tym artykule opisano opcje, które są dostępne dla organizacji w celu skonfigurowania dostępu zdalnego dla swoich użytkowników lub uzupełnienia istniejących rozwiązań o dodatkową pojemność w okresach szczytowego wykorzystania. Architekci sieci stoją przed następującymi wyzwaniami:

- Zaajmy się zwiększeniem wykorzystania sieci.
- Zapewnij niezawodną bezpieczną łączność większej liczbie pracowników swojej firmy i klientów.
- Zapewnij łączność z odległymi lokalizacjami na całym świecie.

Nie wszystkie sieci (na przykład prywatna sieć WAN i firmowe sieci podstawowe) doświadczają przeciążenia z powodu szczytowego obciążenia zdalnego pracownika. Wąskie gardła są powszechnie zgłaszane tylko w domowych sieciach szerokopasmowych i bramkach sieci VPN lokalnych sieci korporacji.

Planiści sieci mogą pomóc złagodzić wąskie gardła i zmniejszyć przeciążenie sieci, pamiętając, że różne rodzaje ruchu ruchu wymagają różnych priorytetów leczenia sieci oraz przez inteligentne przekierowanie/dystrybucję obciążenia. Na przykład, w czasie rzeczywistym tele-medecine ruchu interakcji lekarz-pacjent ma duże znaczenie i opóźnienie / jitter wrażliwe. Podczas gdy replikacja tego samego ruchu między magazynami nie jest z opóźnieniem poufne. Poprzedni ruch musi być kierowany przez najbardziej optymalną ścieżkę sieciową o wyższej jakości usług; dopuszczalne jest kierowanie późniejszego ruchu drogą nieoptymalnej.

>[!NOTE] 
>Pod koniec tego artykułu znajdują się łącza do artykułów wykorzystujących różne funkcje sieci platformy Azure i ekosystemy do obsługi użytkowników pracujących zdalnie.


## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Udostępnianie naszych najlepszych praktyk — sieć platformy Azure została zaprojektowana z myślą o elastyczności i wysokiej dostępności

Platforma Azure została zaprojektowana tak, aby wytrzymać nagłe zmiany w wykorzystaniu zasobów i może znacznie pomóc w okresach szczytowego wykorzystania. Ponadto Microsoft utrzymuje i obsługuje jedną z największych sieci na świecie. Sieć firmy Microsoft została zaprojektowana z myślą o wysokiej dostępności, która może wytrzymać różne typy awarii: od awarii pojedynczego elementu sieciowego do awarii całego regionu.

Sieć firmy Microsoft została zaprojektowana tak, aby spełniać wymagania i zapewniać optymalną wydajność dla różnych typów ruchu sieciowego, w tym opóźniania ruchu multimedialnego wrażliwego na opóźnienia dla Skype i teams, usługi CDN, analizy dużych zbiorów danych w czasie rzeczywistym, magazynu platformy Azure, usługi Bing i konsoli Xbox. Aby zapewnić optymalną wydajność dla różnych typów ruchu, sieć Firmy Microsoft przyciąga cały ruch, który jest przeznaczony do- lub chcąc przejść przez swoje zasoby jak najbliżej pochodzenia ruchu.

>[!NOTE] 
>Korzystanie z funkcji sieci platformy Azure opisanych poniżej wykorzystuje zachowanie przyciągania ruchu w globalnej sieci firmy Microsoft, aby zapewnić lepsze środowisko sieci klientów. Zachowanie przyciągania ruchu w sieci Firmy Microsoft pomaga wyłączyć ładowanie ruchu tak szybko, jak to możliwe z sieci pierwszej/ostatniej mili, które mogą wystąpić przeciążenia w okresach szczytowego wykorzystania.
>

## <a name="enable-employees-to-work-remotely"></a>Umożliwianie pracownikom pracy zdalnej

Brama sieci VPN platformy Azure obsługuje zarówno połączenia sieci VPN typu "punkt-lokacja" i "Lokacja lokacja" (S2S). Za pomocą bramy sieci VPN platformy Azure można skalować połączenia pracownika, aby bezpiecznie uzyskiwać dostęp zarówno do zasobów wdrożonych na platformie Azure, jak i zasobów lokalnych. Aby uzyskać więcej informacji, zobacz [Jak umożliwić użytkownikom pracę zdalną](https://go.microsoft.com/fwlink/?linkid=2123770). 

Jeśli używasz protokołu SSTP (Secure Sockets Tunneling Protocol), liczba równoczesnych połączeń jest ograniczona do 128. Aby uzyskać większą liczbę połączeń, sugerujemy przejście do OpenVPN lub IKEv2. Aby uzyskać więcej informacji, zobacz [Przejście do protokołu OpenVPN lub IKEv2 z protokołu SSTP](https://go.microsoft.com/fwlink/?linkid=2124112).

Aby uzyskać dostęp do zasobów wdrożonych na platformie Azure, deweloperzy zdalni mogą używać rozwiązania Azure Bastion zamiast połączenia sieci VPN, aby uzyskać bezpieczny dostęp do powłoki (RDP lub SSH) bez konieczności uzyskiwania publicznych wiadomości IP na maszynach wirtualnych, do których są uzyskiwane dostęp. Aby uzyskać więcej informacji, zobacz [Praca zdalnie przy użyciu usługi Azure Bastion](https://go.microsoft.com/fwlink/?linkid=2123939).

Do agregowania połączeń vpn na dużą skalę, do obsługi dowolnych połączeń między zasobami w różnych lokalnych lokalizacjach globalnych, w różnych regionalnych sieciach centralnych i sieciach wirtualnych, oraz w celu optymalizacji wykorzystania wielu domowych sieci szerokopasmowych można użyć wirtualnej sieci WAN platformy Azure. Aby uzyskać więcej informacji, zobacz [Walka z zaspokajaniem potrzeb domowych? Oto, gdzie azure wirtualna sieć WAN może pomóc](https://go.microsoft.com/fwlink/?linkid=2123769).

Innym sposobem obsługi zdalnej siły roboczej jest wdrożenie infrastruktury pulpitu wirtualnego (VDI) hostowanego w sieci wirtualnej platformy Azure, zabezpieczonego za pomocą zapory platformy Azure. Na przykład pulpit wirtualny systemu Windows (WVD) to usługa wirtualizacji pulpitu i aplikacji, która działa na platformie Azure. Dzięki pulpitowi wirtualnemu systemu Windows można skonfigurować skalowalny i elastyczny środowisko w ramach subskrypcji platformy Azure bez konieczności uruchamiania dodatkowych serwerów bramy. Użytkownik jest odpowiedzialny tylko za maszyny wirtualne WVD w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Pomoc techniczna usługi azure firewall pracy zdalnej](../firewall/remote-work-support.md). 

Platforma Azure ma również bogaty zestaw partnerów systemu ekologicznego. Nasi partnerzy Network Virtual Appliances na platformie Azure mogą również pomóc w skalowaniu łączności sieci VPN. Aby uzyskać więcej informacji, zobacz [Zagadnienia dotyczące pracy zdalnej (Network Virtual Appliance, NVA).](https://go.microsoft.com/fwlink/?linkid=2123771)

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Rozszerzanie połączeń pracowników w celu uzyskania dostępu do globalnie rozproszonych zasobów

Następujące usługi platformy Azure mogą pomóc pracownikom w dostępie do zasobów rozproszonych globalnie. Zasoby mogą znajdować się w dowolnym regionie platformy Azure, w sieciach lokalnych, a nawet w innych chmurach publicznych lub prywatnych. 

- **Komunikacja równorzędna sieci wirtualnej platformy Azure:** Jeśli zasoby są wdrażane w więcej niż jednym regionie platformy Azure i/lub jeśli zagregujesz łączność zdalnie pracujących pracowników przy użyciu wielu sieci wirtualnych, można ustanowić łączność między wieloma sieciami wirtualnymi platformy Azure przy użyciu komunikacji równorzędnej w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Komunikacja równorzędna sieci wirtualnej][VNet-peer].

- **Rozwiązanie oparte na sieci VPN platformy Azure:** dla pracowników zdalnych połączonych z platformą Azure za pośrednictwem sieci VPN P2S lub S2S można włączyć dostęp do sieci lokalnych, konfigurując sieć VPN S2S między sieciami lokalnymi a bramą sieci VPN platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia lokacja lokacja.][S2S]

- **Usługa ExpressRoute:** Za pomocą prywatnej komunikacji równorzędnej usługi ExpressRoute można włączyć prywatną łączność między wdrożeniami platformy Azure a infrastrukturą lokalną lub infrastrukturą w usłudze kolokacji. Usługa ExpressRoute, za pośrednictwem komunikacji równorzędnej firmy Microsoft, umożliwia również dostęp do publicznych punktów końcowych w firmie Microsoft z sieci lokalnej. Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Oferują one bezpieczną łączność, niezawodność, wyższą przepustowość, z niższymi i spójnymi opóźnieniami niż typowe połączenia przez Internet. Aby uzyskać więcej informacji, zobacz [Omówienie usługi ExpressRoute][ExR]. Wykorzystanie istniejącego dostawcy sieci, który jest już częścią naszego [ekosystemu partnerów usługi ExpressRoute,][ExR-eco] może skrócić czas na uzyskanie połączeń o dużej przepustowości z firmą Microsoft.  Za pomocą [usługi ExpressRoute Direct][ExR-D] można bezpośrednio połączyć sieć lokalną z szkieletem firmy Microsoft. ExpressRoute Direct oferuje dwie różne opcje liniowej z dwoma 10 Gb/s lub 100 Gb/s. 

- **Wirtualna sieć WAN platformy Azure:** wirtualna sieć WAN platformy Azure umożliwia bezproblemową współdziałanie między połączeniami sieci VPN a obwodami usługi ExpressRoute. Jak wspomniano wcześniej, wirtualna sieć WAN platformy Azure obsługuje również połączenia między zasobami w różnych lokalizacjach globalnych na przedm, w różnych regionalnych centrach i sieciach wirtualnych

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Skalowanie łączności z klientem do zasobów frontendu

W czasach, gdy więcej osób przechodzi do trybu online, wiele firmowych witryn sieci Web doświadcza zwiększonego ruchu klientów. Usługa Azure Application Gateway może pomóc w zarządzaniu tym zwiększonym obciążeniem frontendu. Aby uzyskać więcej informacji, zobacz [Obsługa dużych ruchu bramy aplikacji](https://go.microsoft.com/fwlink/?linkid=2123940).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Pomoc techniczna firmy Microsoft w zakresie ruchu w wielu chmurach

W przypadku wdrożeń w innych chmurach publicznych firma Microsoft może zapewnić łączność globalną. Wirtualna sieć WAN platformy Azure, sieć VPN lub usługa ExpressRoute mogą pomóc w tym zakresie. Aby rozszerzyć łączność z platformy Azure do innych chmur, można skonfigurować sieć VPN S2S między dwiema chmurami. Można również ustanowić łączność z platformy Azure do innych chmur publicznych przy użyciu usługi ExpressRoute. Chmura Oracle jest częścią ekosystemu partnerów Usługi ExpressRoute. Można [skonfigurować bezpośrednie połączenie między platformą Azure a infrastrukturą Oracle Cloud Infrastructure.][Az-OCI] Większość dostawców usług, które są częścią ekosystemu partnerów usługi ExpressRoute również oferują prywatne połączenia z innymi chmurami publicznymi. Korzystając z tych dostawców usług, można ustanowić prywatną łączność między wdrożeniami na platformie Azure i innych chmur za pośrednictwem usługi ExpressRoute.

## <a name="next-steps"></a>Następne kroki

W poniższych artykułach omówiono, jak różne funkcje sieci platformy Azure mogą służyć do skalowania użytkowników do pracy zdalnej:

| **Artykułu** | **Ostatnia aktualizacja** |
| --- | --- |
| [Jak umożliwić użytkownikom pracę zdalną](https://go.microsoft.com/fwlink/?linkid=2123770) | 23 marca 2020 r. |
| [Starasz się zaspokoić potrzeby związane z pracą w domu? Oto, gdzie wirtualna sieć WAN platformy Azure może pomóc](https://go.microsoft.com/fwlink/?linkid=2123769) | 23 marca 2020 r. |
| [Obsługa dużych ruchu bramy aplikacji](https://go.microsoft.com/fwlink/?linkid=2123940) | 23 marca 2020 r. |
| [Zagadnienia dotyczące sieciowego urządzenia wirtualnego (NVA) dla pracy zdalnej](https://go.microsoft.com/fwlink/?linkid=2123771)| 23 marca 2020 r. |
| [Przejście do protokołu OpenVPN lub IKEv2 z protokołu SSTP](https://go.microsoft.com/fwlink/?linkid=2124112) | 23 marca 2020 r. |
| [Praca zdalna przy użyciu usługi Azure Bastion](https://go.microsoft.com/fwlink/?linkid=2123939) | 23 marca 2020 r. |
| [Tworzenie łączności hybrydowej przy użyciu usługi Azure ExpressRoute w celu obsługi użytkowników zdalnych](https://go.microsoft.com/fwlink/?linkid=2123768) | 23 marca 2020 r. |
| [Obsługa pracy zdalnej w zaporze platformy Azure](../firewall/remote-work-support.md)|25 marca 2020 r.|

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
