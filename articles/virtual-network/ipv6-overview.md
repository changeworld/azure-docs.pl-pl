---
title: Omówienie protokołu IPv6 dla sieci wirtualnej platformy Azure (wersja zapoznawcza)
titlesuffix: Azure Virtual Network
description: Opis protokołu IPv6 ścieżek punktów końcowych i danych protokołu IPv6 w sieci wirtualnej platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 0ec650880a45f6383b24b5ac810fc2ee745806b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62131042"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Co to jest protokół IPv6 dla usługi Azure Virtual Network? (Wersja zapoznawcza)

Protokół IPv6 dla usługi Azure Virtual Network (VNET) umożliwia hostowanie aplikacji na platformie Azure przy użyciu protokołu IPv6 i IPv4 łączność w sieci wirtualnej i do i z Internetu. Ze względu na wyczerpanie publicznych adresów IPv4 nowym sieciom mobilności i Internetu rzeczy (IoT) często są tworzone w przypadku protokołu IPv6. Long nawet ustalona przekształcanego usługodawców internetowych i mobilnych sieci IPv6. Tylko protokół IPv4 usługi można znajdują się w rzeczywistych wadą istniejących aplikacji i pojawiających się rynkach. Podwójnego stosu łączność IPv4/IPv6 umożliwia korzystanie z usług hostowanych na platformie Azure, przechodzenie technologii luki za pomocą usługi dostępnej globalnie, skumulowany podwójne, które łatwo łączyć się z istniejącego protokołu IPv4 i te nowe IPv6 i urządzeń sieci.

Łączność IPv6 z oryginalnego systemu Azure ułatwia zapewnienie podwójnego stosu łączności z Internetem (IPv4/IPv6) dla aplikacji hostowanych na platformie Azure. Umożliwia ona proste wdrożenie maszyn wirtualnych przy użyciu łączności IPv6 równoważenia obciążenia dla ruchu przychodzącego i wychodzącego połączenia inicjowane. Ta funkcja jest nadal dostępny i więcej informacji znajduje się [tutaj](../load-balancer/load-balancer-ipv6-overview.md).
Protokół IPv6 dla sieci wirtualnej platformy Azure jest znacznie bardziej pełnej polecane — Włączanie pełnej IPv6 architektury rozwiązań wdrażanych na platformie Azure.

> [!Important]
> Protokół IPv6 dla usługi Azure Virtual Network jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Poniższy diagram przedstawia wdrożenie proste podwójny stos (IPv4/IPv6) na platformie Azure:

![Diagram wdrażania sieci IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Korzyści

Korzyści z platformy Azure wirtualnych sieci IPv6:

- Pomaga Rozszerz zasięg hostowanymi na platformie Azure aplikacje do powiększania mobilnych i rynków Internetu rzeczy.
- Podwójna skumulowany maszyny wirtualne z protokołów IPv4/IPv6 zapewniają usługi maksymalną elastyczność wdrażania. Wystąpienie jednej usługi mogą łączyć się z klientami IPv4 i Internet obsługujące protokół IPv6.
- Opiera się na długotrwałej i stabilne platformy Azure maszyna wirtualna z Internetem łączność IPv6.
- Zabezpiecz domyślnie, ponieważ łączności z Internetem IPv6 jest ustanowiona tylko wtedy, gdy jawnie żądają w danym wdrożeniu.

## <a name="capabilities"></a>Możliwości

Obsługa protokołu IPv6 dla maszyn wirtualnych obejmuje następujące funkcje:

- Klienci platformy Azure można zdefiniować własne przestrzeń adresową sieci wirtualnej IPv6, aby zaspokoić potrzeby swoich aplikacji klientów, lub bezproblemowo zintegrować z ich lokalnej przestrzeni adresów IP.
- Podwójny stos (IPv4 i IPv6) sieci wirtualnych z podsieciami podwójnego stosu umożliwiają aplikacjom nawiązywanie połączenia z zasobami IPv4 i IPv6 w swoich sieciach wirtualnych lub — przez Internet.
- Ochrona zasobów przy użyciu protokołu IPv6 reguły dla sieciowych grup zabezpieczeń
- Dostosowywanie, routing ruchu IPv6 w sieci wirtualnej trasy — za pomocą zdefiniowane przez użytkownika, szczególnie w przypadku korzystanie z wirtualnych urządzeń sieciowych do Wzbogać swoje aplikacje.
- Obsługa modułu równoważenia obciążenia protokołu IPv6 do tworzenia odpornych, skalowalnych aplikacji z obsługą usługi Azure DNS rekordów AAAA publicznych adresów IP protokołu IPv6.
- Łatwe dodawanie łączności IPv6 do istniejącego wdrożenia obsługującym tylko protokół IPv4, za pomocą uaktualnienia w miejscu.
- Twórz aplikacje podwójnego stosu, które automatycznie skalować się do obciążenia z zestawami skalowania maszyn wirtualnych.

## <a name="limitations"></a>Ograniczenia
Wersja zapoznawcza IPv6 sieci wirtualnej platformy Azure ma następujące ograniczenia:
- Protokół IPv6 dla sieci wirtualnej platformy Azure (wersja zapoznawcza) jest dostępny w wszystkich globalnych regionów platformy Azure, ale tylko w globalnej platformy Azure — nie chmury dla instytucji rządowych.   
- Portal pomocy technicznej dla wersji zapoznawczej jest ograniczona do wyświetlenia tylko w przypadku konfiguracji protokołu IPv6 wiele, ale nie wszystkie, jednak protokół IPv6 dla sieci wirtualnej ma pełną pomoc techniczną i dokumentację (z przykładami) do wdrożenia protokołu IPv6, przy użyciu programu Azure Powershell i interfejsu wiersza polecenia (CLI).
- Obsługa obserwatora sieci w wersji zapoznawczej jest ograniczona do dzienników przepływu sieciowych grup zabezpieczeń i przechwytywanie pakietów sieciowych.
- Obsługa równoważenia obciążenia dla wersji zapoznawczej będzie początkowo ograniczona do podstawowego modułu równoważenia obciążenia.
- Na poziomie wystąpienia publiczne adresy IP (publiczne adresy IP bezpośrednio na maszynie Wirtualnej) nie jest obsługiwana w wersji zapoznawczej.  
- (Komunikacja równorzędna sieci wirtualnych regionalnie lub globalnie) nie jest obsługiwana w wersji zapoznawczej. 

## <a name="pricing"></a>Cennik

Zasoby platformy Azure IPv6 oraz przepustowość są naliczane przy użyciu stawek stosowanych w przypadku protokołu IPv4. Opłaty nie będą dodatkowym lub innym dla protokołu IPv6. Można znaleźć szczegółowe informacje na temat cen [publiczne adresy IP](https://azure.microsoft.com/pricing/details/ip-addresses/), [przepustowość sieci](https://azure.microsoft.com/pricing/details/bandwidth/), lub [modułu równoważenia obciążenia](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [wdrożyć aplikację podwójnego stosu protokołu IPv6, przy użyciu programu Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Dowiedz się, jak [wdrożyć aplikację podwójnego stosu protokołu IPv6, przy użyciu wiersza polecenia platformy Azure](virtual-network-ipv4-ipv6-dual-stack-cli.md).
