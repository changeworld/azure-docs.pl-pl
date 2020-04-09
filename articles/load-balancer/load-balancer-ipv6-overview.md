---
title: Omówienie ipv6 — Równoważenie obciążenia platformy Azure
description: Dzięki tej ścieżce szkoleniowej rozpocznij pracę z obsługą IPv6 dla modułu Azure Load Balancer i maszyn wirtualnych ze zrównoważonym obciążeniem.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, lazurowy moduł równoważenia obciążenia, podwójny stos, publiczny adres IP, natywny ipv6, mobilny, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: d2966f32fcf7f8be2a93d1639f0a63f49768c306
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981855"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Omówienie modułu IPv6 dla modułu równoważenia obciążenia platformy Azure


>[!NOTE] 
>Ta zawartość została zastąpiona przez [IPv6 dla usługi Azure VNet Overview](https://docs.microsoft.com/azure/virtual-network/ipv6-overview). Platforma Azure zaleca nowe wdrożenia IPv6 przy użyciu nowych funkcji IPv6 dla sieci wirtualnych platformy Azure.

>[!NOTE]
>Usługa Azure Load Balancer obsługuje dwie warstwy: Podstawowa i Standardowa. W tym artykule omówiono usługę Load Balancer w warstwie Podstawowa. Aby uzyskać więcej informacji na temat standardowego modułu równoważenia obciążenia, zobacz [omówienie standardowego modułu równoważenia obciążenia](load-balancer-standard-overview.md).

Podstawowe moduły równoważenia obciążenia z internetem przeznaczone dla jednostki SKU można wdrożyć z adresem IPv6. Oprócz łączności IPv4 umożliwia to następujące możliwości:

* Natywna kompleksowa łączność IPv6 między publicznymi klientami internetowymi a maszynami wirtualnymi platformy Azure za pośrednictwem modułu równoważenia obciążenia.
* Natywna kompleksowa łączność wychodząca IPv6 między maszynami wirtualnymi a publicznymi klientami obsługującymi protokół IPv6 w Internecie.

Na poniższej ilustracji przedstawiono funkcję IPv6 dla modułu Równoważenia obciążenia platformy Azure.

![Moduł równoważenia obciążenia platformy Azure z ipv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Po wdrożeniu klient internetowy obsługujący protokół IPv4 lub IPv6 może komunikować się z publicznymi adresami IPv4 lub IPv6 (lub nazwami hostów) modułu równoważenia obciążenia opartego na Internecie. Moduł równoważenia obciążenia kieruje pakiety IPv6 na prywatne adresy IPv6 maszyn wirtualnych przy użyciu translacji adresów sieciowych (NAT). Klient internetowy IPv6 nie może komunikować się bezpośrednio z adresem IPv6 maszyn wirtualnych.

## <a name="features"></a>Funkcje

Natywna obsługa protokołu IPv6 dla maszyn wirtualnych wdrożonych za pośrednictwem usługi Azure Resource Manager zapewnia:

1. Usługi IPv6 z równoważenia obciążenia dla klientów IPv6 w Internecie
2. Natywne punkty końcowe IPv6 i IPv4 na maszynach wirtualnych ("podwójny skumulowany")
3. Natywne połączenia IPv6 inbound i wychodzące
4. Obsługiwane protokoły, takie jak TCP, UDP i HTTP(S) umożliwiają pełny zakres architektur usług

## <a name="benefits"></a>Zalety

Ta funkcja umożliwia następujące kluczowe korzyści:

* Spełniaj przepisy rządowe wymagające, aby nowe aplikacje były dostępne dla klientów tylko w IPv6
* Umożliwienie deweloperom urządzeń mobilnych i internetu rzeczy (IOT) korzystania z maszyn wirtualnych platformy Azure z dwoma stosami (IPv4+IPv6) w celu rozwiązania problemu rosnących rynków mobilnych & IOT

## <a name="details-and-limitations"></a>Szczegóły i ograniczenia

Szczegóły

* Usługa Azure DNS zawiera rekordy nazw IPv4 A i IPv6 AAAA i odpowiada za pomocą obu rekordów dla modułu równoważenia obciążenia. Klient wybiera adres (IPv4 lub IPv6) do komunikowania się z.
* Gdy maszyna wirtualna inicjuje połączenie z publicznym urządzeniem podłączonym do Internetu IPv6, źródłowy adres IPv6 maszyny Wirtualnej jest adresem sieciowym przetłumaczonym (NAT) na publiczny adres IPv6 modułu równoważenia obciążenia.
* Maszyny wirtualne z systemem operacyjnym Linux muszą być skonfigurowane do odbierania adresu IP Protokołu IPv6 za pośrednictwem usługi DHCP. Wiele obrazów systemu Linux w galerii Azure Są już skonfigurowane do obsługi IPv6 bez modyfikacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie maszyn wirtualnych DHCPv6 dla systemu Linux](load-balancer-ipv6-for-linux.md)
* Jeśli zdecydujesz się użyć sondy kondycji z modułem równoważenia obciążenia, utwórz sondę IPv4 i użyj jej zarówno z punktami końcowymi IPv4, jak i IPv6. Jeśli usługa na maszynie wirtualnej ustępuje, punkty końcowe IPv4 i IPv6 są wyjęte z obrotu.

Ograniczenia

* Nie można dodać reguł równoważenia obciążenia IPv6 w witrynie Azure Portal. Reguły można tworzyć tylko za pośrednictwem szablonu, interfejsu wiersza polecenia, programu PowerShell.
* Nie można uaktualnić istniejących maszyn wirtualnych do używania adresów IPv6. Należy wdrożyć nowe maszyny wirtualne.
* Jeden adres IPv6 można przypisać do jednego interfejsu sieciowego w każdej maszynie wirtualnej.
* Publicznych adresów IPv6 nie można przypisać do maszyny Wirtualnej. Można je przypisać tylko do modułu równoważenia obciążenia.
* Nie można skonfigurować odwrotnego wyszukiwania DNS dla publicznych adresów IPv6.
* Maszyny wirtualne z adresami IPv6 nie mogą być członkami usługi Azure Cloud Service. Mogą być połączone z siecią wirtualną platformy Azure i komunikować się ze sobą za pośrednictwem ich adresów IPv4.
* Prywatne adresy IPv6 można wdrożyć na poszczególnych maszynach wirtualnych w grupie zasobów, ale nie można ich wdrożyć w grupie zasobów za pomocą zestawów skalowania.
* Maszyny wirtualne platformy Azure nie mogą łączyć się za pomocą usługi IPv6 z innymi maszynami wirtualnymi, innymi usługami platformy Azure lub urządzeniami lokalnymi. Mogą komunikować się tylko z modułem równoważenia obciążenia platformy Azure za pomocą usługi IPv6. Mogą jednak komunikować się z tymi innymi zasobami przy użyciu IPv4.
* Ochrona sieciowej grupy zabezpieczeń (NSG) dla IPv4 jest obsługiwana we wdrożeniach z dwoma stosami (IPv4+IPv6). NSG nie mają zastosowania do punktów końcowych IPv6.
* Punkt końcowy IPv6 na maszynie Wirtualnej nie jest narażony bezpośrednio do Internetu. Znajduje się za modułem równoważenia obciążenia. Tylko porty określone w regułach modułu równoważenia obciążenia są dostępne za pośrednictwem IPv6.
* Zmiana parametru IdleTimeout dla IPv6 nie jest **obecnie obsługiwana**. Wartość domyślna to cztery minuty.
* Zmiana parametru loadDistributionMethod dla IPv6 nie jest **obecnie obsługiwana.**
* Zarezerwowane adresy IPPv6 (gdzie IPAllocationMethod = static) nie są **obecnie obsługiwane**.
* NAT64 (tłumaczenie IPv6 na IPv4) nie jest obsługiwany.
* Dołączanie pomocniczej karty sieciowej, która odwołuje się do podsieci IPv6 do puli **zaplecza, nie**jest obecnie obsługiwane .

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wdrożyć moduł równoważenia obciążenia za pomocą iPv6.

* [Dostępność IPv6 według regionów](https://go.microsoft.com/fwlink/?linkid=828357)
* [Wdrażanie modułu równoważenia obciążenia za pomocą iPv6 przy użyciu szablonu](load-balancer-ipv6-internet-template.md)
* [Wdrażanie modułu równoważenia obciążenia za pomocą usługi IPv6 przy użyciu programu Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Wdrażanie modułu równoważenia obciążenia za pomocą funkcji IPv6 przy użyciu interfejsu wiersza polecenia platformy Azure](load-balancer-ipv6-internet-cli.md)
