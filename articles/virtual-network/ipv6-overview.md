---
title: Omówienie usługi IPv6 dla sieci wirtualnej platformy Azure (wersja zapoznawcza)
titlesuffix: Azure Virtual Network
description: Opis IPv6 punktów końcowych IPv6 i ścieżek danych w sieci wirtualnej platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/19/2019
ms.author: kumud
ms.openlocfilehash: 9214886f468a4a052328a99289845361a059b650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780083"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Co to jest IPv6 dla sieci wirtualnej platformy Azure? (Wersja zapoznawcza)

IPv6 dla sieci wirtualnej platformy Azure (VNet) umożliwia hostowania aplikacji na platformie Azure z łącznością IPv6 i IPv4 zarówno w sieci wirtualnej, jak i do i z Internetu. Ze względu na wyczerpanie publicznych adresów IPv4, nowe sieci mobilności i Internetu rzeczy (IoT) są często budowane na IPv6. Nawet od dawna isp i sieci komórkowe są przekształcane w IPv6. Usługi iPv4 mogą znaleźć się w rzeczywistej niekorzystnej sytuacji zarówno na istniejących, jak i wschodzących rynkach. Łączność Z dwoma stosami IPv4/IPv6 umożliwia usługom hostowanym na platformie Azure przechodzenie przez tę lukę technologiczną dzięki dostępnym na całym świecie usługom z dwoma stosami, które łatwo łączą się zarówno z istniejącymi urządzeniami i sieciami IPv4, jak i tymi nowymi urządzeniami i sieciami IPv6.

Oryginalna łączność IPv6 platformy Azure ułatwia zapewnienie łączności internetowej z dwoma stosami (IPv4/IPv6) dla aplikacji hostowanych na platformie Azure. Umożliwia proste wdrażanie maszyn wirtualnych z łącznością IPv6 z równoważeniem obciążenia dla połączeń inbound i wychodzących. Ta funkcja jest nadal dostępna, a więcej informacji można znaleźć [tutaj.](../load-balancer/load-balancer-ipv6-overview.md)
Protokół IPv6 dla sieci wirtualnej platformy Azure jest znacznie bardziej funkcjonalny, umożliwiając wdrożenie pełnych architektur rozwiązań IPv6 na platformie Azure.

> [!Important]
> IPv6 dla usługi Azure Virtual Network jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Na poniższym diagramie przedstawiono proste wdrożenie podwójnego stosu (IPv4/IPv6) na platformie Azure:

![Diagram wdrażania sieci IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Korzyści

IPv6 dla korzyści sieci wirtualnej platformy Azure:

- Pomaga rozszerzyć zasięg aplikacji hostowanych na platformie Azure na rozwijające się rynki telefonii komórkowej i Internetu rzeczy.
- Maszyny wirtualne Z dwoma skumulowanymi procesorami IPv4/IPv6 zapewniają maksymalną elastyczność wdrażania usług. Pojedyncze wystąpienie usługi może łączyć się z klientami internetowymi obsługującymi iPv4 i IPv6.
- Opiera się na ugruntowanej, stabilnej łączności IPv6 platformy Azure VM-to-Internet.
- Bezpieczne domyślnie, ponieważ łączność IPv6 z Internetem jest ustanawiana tylko wtedy, gdy jawnie zażądać go w wdrożeniu.

## <a name="capabilities"></a>Możliwości

Usługa IPv6 dla sieci wirtualnej platformy Azure zawiera następujące możliwości:

- Klienci platformy Azure mogą definiować własną przestrzeń adresów sieci wirtualnej IPv6, aby zaspokoić potrzeby swoich aplikacji, klientów lub bezproblemowo zintegrować się z lokalną przestrzenią IP.
- Sieci wirtualne z dwoma stosami (IPv4 i IPv6) z podsieciami z dwoma stosami umożliwiają aplikacjom łączenie się zarówno z zasobami IPv4, jak i IPv6 w sieci wirtualnej lub - z Internetem.
    > [!IMPORTANT]
    > Podsieci dla IPv6 musi mieć dokładnie /64 w rozmiarze.  Zapewnia to przyszłą zgodność, jeśli zdecydujesz się włączyć routing podsieci do sieci lokalnej, ponieważ niektóre routery mogą akceptować tylko /64 trasy IPv6.  
- Chroń swoje zasoby za pomocą reguł IPv6 dla sieciowych grup zabezpieczeń.
    - Zabezpieczenia rozproszonej odmowy odmowy usługi (DDoS) platformy Azure są rozszerzane na publiczne adresy IP skierowane do Internetu
- Dostosuj routing ruchu IPv6 w sieci wirtualnej za pomocą tras zdefiniowanych przez użytkownika, szczególnie w przypadku wykorzystania wirtualnych urządzeń sieciowych w celu rozszerzenia aplikacji.
- Maszyny wirtualne systemu Linux i Windows mogą używać funkcji IPv6 dla sieci wirtualnej platformy Azure
- [Standardowa obsługa publicznego modułu równoważenia obciążenia IPv6](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) w celu tworzenia odpornych, skalowalnych aplikacji, w tym:
    - Opcjonalna sonda kondycji IPv6, aby określić, które wystąpienia puli wewnętrznej bazy danych są kondycją, a tym samym mogą odbierać nowe przepływy.
    - Opcjonalne reguły ruchu wychodzącego, które zapewniają pełną kontrolę deklaratywną nad łącznością wychodzącą, aby skalować i dostroić tę możliwość do określonych potrzeb.
    - Opcjonalnie wiele konfiguracji front-end, które umożliwiają jeden moduł równoważenia obciążenia do korzystania z wielu publicznych adresów IP IPv6- ten sam protokół frontendu i portu mogą być ponownie używane w adresach frontendu.
    - Opcjonalne porty IPv6 mogą być ponownie używane w wystąpieniach wewnętrznej bazy danych przy użyciu funkcji *Przestawne ip* reguł równoważenia obciążenia 
- [Standardowa obsługa wewnętrznego modułu równoważenia obciążenia IPv6](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) w celu tworzenia odpornych aplikacji wielowarstwowych w ramach sieci wirtualnych platformy Azure.  
- Podstawowa obsługa publicznego modułu równoważenia obciążenia IPv6 w celu zapewnienia zgodności z starszymi wdrożeniami
- [Zarezerwowane publiczne adresy IP i zakresy adresów IPv6](ipv6-public-ip-address-prefix.md) zapewniają stabilne i przewidywalne adresy IPv6, które ułatwiają umieszczanie aplikacji hostowanych na platformie azure dla twojej firmy i klientów.
- Publiczny adres IP na poziomie wystąpienia zapewnia łączność z Internetem IPv6 bezpośrednio do poszczególnych maszyn wirtualnych.
- [Dodaj IPv6 do istniejących wdrożeń tylko dla IPv4](ipv6-add-to-existing-vnet-powershell.md)— ta funkcja umożliwia łatwe dodawanie łączności IPv6 do istniejących wdrożeń tylko IPv4 bez konieczności ponownego tworzenia wdrożeń.  Ruch sieciowy IPv4 nie ma wpływu podczas tego procesu, więc w zależności od aplikacji i systemu operacyjnego można dodać IPv6 nawet do usług na żywo.    
- Pozwól klientom internetowym bezproblemowo uzyskiwać dostęp do aplikacji z dwoma stosami przy użyciu wybranego protokołu za pomocą usługi Azure DNS obsługującej rekordy protokołu IPv6 (AAAA). 
- Tworzenie aplikacji z dwoma stosami, które automatycznie skalują się do obciążenia za pomocą zestawów skalowania maszyn wirtualnych z IPv6.
- [Komunikacja równorzędna sieci wirtualnej (VNET)](virtual-network-peering-overview.md) — zarówno w obrębie regionalnym, jak i globalnej komunikacji równorzędnej — umożliwia bezliorowe łączenie wirtualnych sieci wirtualnych z dwoma stosami — zarówno punkty końcowe IPv4, jak i IPv6 na maszynach wirtualnych w sieciach równorzędnych będą mogły komunikować się ze sobą. Podczas przenoszenia wdrożeń na podwójny stos można nawet współrzędnych z wirtualnymi procesorami IPv4. 
- Rozwiązywanie problemów i diagnostyka IPv6 są dostępne z metryki/alerty modułu równoważenia obciążenia i funkcji obserwatora sieci, takich jak przechwytywanie pakietów, dzienniki przepływu sieciowej grupy zabezpieczeń, rozwiązywanie problemów z połączeniami i monitorowanie połączeń.   

## <a name="scope"></a>Zakres
IPv6 for Azure VNET to podstawowy zestaw funkcji, który umożliwia klientom hostowania aplikacji z dwoma stosami (IPv4+ IPv6) na platformie Azure.  Firma Microsoft zamierza dodać obsługę protokołu IPv6 do większej liczby funkcji sieci platformy Azure w czasie i ostatecznie do zaoferowania dwóch wersji stosu usług PaaS platformy Azure, ale w międzyczasie wszystkie usługi PaaS platformy Azure są dostępne za pośrednictwem punktów końcowych IPv4 na dwóch komputerach wirtualnych stosu.   

## <a name="limitations"></a>Ograniczenia
Bieżąca wersja usługi IPv6 dla sieci wirtualnej platformy Azure ma następujące ograniczenia:
- Usługa IPv6 dla sieci wirtualnej platformy Azure (Wersja zapoznawcza) jest dostępna we wszystkich globalnych regionach platformy Azure, ale tylko w globalnej platformie Azure — jeszcze nie w chmurach rządowych.
- Bramy usługi ExpressRoute i VPN nie mogą być używane w sieci wirtualnej z włączoną funkcją Protokołu IPv6, bezpośrednio lub w równorzędnie z "UseRemoteGateway". 
- Platforma Azure (AKS itp.) nie obsługuje komunikacji IPv6 dla kontenerów.  

## <a name="pricing"></a>Cennik

Zasoby i przepustowość platformy IPv6 platformy Azure są naliczane według tych samych stawek co IPv4. Nie ma żadnych dodatkowych lub różnych opłat za protokół IPv6. Szczegółowe informacje na temat cen [publicznych adresów IP,](https://azure.microsoft.com/pricing/details/ip-addresses/) [przepustowości sieci](https://azure.microsoft.com/pricing/details/bandwidth/)lub [modułu równoważenia obciążenia](https://azure.microsoft.com/pricing/details/load-balancer/)można znaleźć.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [wdrożyć aplikację podwójnego stosu IPv6 przy użyciu programu Azure PowerShell.](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)
- Dowiedz się, jak [wdrożyć aplikację podwójnego stosu IPv6 przy użyciu interfejsu wiersza polecenia platformy Azure.](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)
- Dowiedz się, jak [wdrożyć aplikację podwójnego stosu IPv6 przy użyciu szablonów Menedżera zasobów (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
