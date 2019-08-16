---
title: Omówienie protokołu IPv6 dla usługi Azure Virtual Network (wersja zapoznawcza)
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
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 33078439e8f055d746fad9949a9b0d7651e120f7
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543811"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Co to jest protokół IPv6 dla usługi Azure Virtual Network? (wersja zapoznawcza)

Protokół IPv6 dla usługi Azure Virtual Network (VNet) umożliwia hostowanie aplikacji na platformie Azure z łącznością IPv6 i IPv4 zarówno w ramach sieci wirtualnej, jak i z Internetu. Ze względu na wyczerpanie publicznych adresów IPv4, nowe sieci na potrzeby mobilności i Internet rzeczy (IoT) są często kompilowane w protokole IPv6. Nawet długi ustanowiony usługodawca internetowy i sieci komórkowe są przekształcane na protokół IPv6. Usługi obsługujące tylko protokół IPv4 mogą znajdować się w realnej niekorzystnych wadach na istniejących i powstających rynkach. Dwustosowa łączność z protokołem IPv4/IPv6 umożliwia usługom hostowanym przez platformę Azure przechodzenie między tymi różnicami w ramach tej technologii dzięki globalnie dostępnym usługom z podwójnym stosem, które łatwo łączą się z istniejącym protokołem IPv4 i tymi nowymi urządzeniami IPv6

Oryginalna łączność z protokołem IPv6 na platformie Azure ułatwia Zapewnianie łączności z Internetem przy zastosowaniu podwójnego stosu (IPv4/IPv6) dla aplikacji hostowanych na platformie Azure. Umożliwia proste wdrażanie maszyn wirtualnych z funkcją łączności IPv6 z równoważeniem obciążenia dla połączeń przychodzących i wychodzących. Ta funkcja jest nadal dostępna i więcej informacji można znaleźć [tutaj](../load-balancer/load-balancer-ipv6-overview.md).
Protokół IPv6 dla usługi Azure Virtual Network jest znacznie bardziej zaoferowany — włączenie pełnej architektury rozwiązań IPv6 do wdrożenia na platformie Azure.

> [!Important]
> Protokół IPv6 dla usługi Azure Virtual Network jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Na poniższym diagramie przedstawiono proste wdrożenie podwójnego stosu (IPv4/IPv6) na platformie Azure:

![Diagram wdrażania sieci IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Korzyści

Korzyści z używania usługi Azure Virtual Network IPv6:

- Ułatwia rozszerzanie zasięgów aplikacji hostowanych na platformie Azure na rosnące rynki przenośne i Internet rzeczy.
- Podwójne stosy maszyn wirtualnych IPv4/IPv6 zapewniają największą elastyczność wdrażania usług. Pojedyncze wystąpienie usługi może łączyć się z klientami internetowymi obsługującymi zarówno protokół IPv4, jak i IPv6.
- Kompilacja oparta na długotrwałej, stabilnej łączności z maszyną wirtualną na platformie Azure.
- Domyślnie bezpieczne, ponieważ połączenie IPv6 z Internetem jest nawiązywane tylko wtedy, gdy użytkownik jawnie zażąda go we wdrożeniu.

## <a name="capabilities"></a>Możliwości

Protokół IPv6 dla sieci wirtualnej obejmuje następujące możliwości:

- Klienci platformy Azure mogą definiować własną przestrzeń adresową sieci wirtualnej IPv6, aby sprostać wymaganiom aplikacji, klientom lub bezproblemowo integrować się z lokalną przestrzenią adresów IP.
- Dwie sieci wirtualne (IPv4 i IPv6) z dwoma podsieciami stosu umożliwiają aplikacjom łączenie się z zasobami IPv4 i IPv6 w sieci wirtualnej lub w Internecie.
    > [!IMPORTANT]
    > Podsieci dla protokołu IPv6 muszą mieć wartość dokładnie/64.  Zapewnia to zgodność w przypadku podjęcia decyzji o włączeniu routingu podsieci do sieci lokalnej, ponieważ niektóre routery mogą akceptować tylko trasy IPv6/64.  
- Ochrona zasobów przy użyciu reguł IPv6 dla sieciowych grup zabezpieczeń.
- Dostosuj Routing ruchu IPv6 w sieci wirtualnej przy użyciu tras zdefiniowanych przez użytkownika — szczególnie w przypadku korzystania z wirtualnych urządzeń sieciowych w celu rozszerzenia aplikacji.
- Zezwól klientom internetowym na bezproblemowy dostęp do aplikacji dwustosowej przy użyciu dowolnie wybranego protokołu z obsługą Azure DNS dla rekordów IPv6 (AAAA). 
- Obsługa publicznego modułu równoważenia obciążenia IPv6 w warstwie Standardowa na potrzeby tworzenia odpornych skalowalnych aplikacji, która obejmuje następujące elementy:
    - Opcjonalna sonda kondycji IPv6 w celu określenia, które wystąpienia puli zaplecza są kondycją i w ten sposób mogą odbierać Nowe przepływy. .  
    - Opcjonalne reguły ruchu wychodzącego zapewniające pełną kontrolę deklaratywną nad łącznością wychodzącą w celu skalowania i dostrajania tej możliwości do konkretnych potrzeb.
    - Opcjonalna wiele konfiguracji frontonu, które umożliwiają pojedynczemu modułowi równoważenia obciążenia używanie wielu publicznych adresów IP IPv6 — ten sam protokół frontonu i port mogą być ponownie używane między adresami frontonu.
- Publiczny adres IP na poziomie wystąpienia zapewnia łączność z Internetem IPv6 bezpośrednio z poszczególnymi maszynami wirtualnymi.
- Łatwo dodawaj łączność IPv6 do istniejących wdrożeń obsługujących tylko protokół IPv4 z uaktualnieniem w miejscu.
- Twórz podwójne aplikacje stosujące automatyczne skalowanie do obciążenia przy użyciu zestawów skalowania maszyn wirtualnych.
- Obsługa portalu w wersji zapoznawczej obejmuje interaktywne tworzenie/Edytowanie/usuwanie podwójnych sieci wirtualnych (IPv4 + IPv6) i podsieci, reguły grupy zabezpieczeń sieci IPv6, trasy zdefiniowane przez użytkownika IPv6 i publiczne adresy IP protokołu IPv6.  

## <a name="limitations"></a>Ograniczenia
Wersja zapoznawcza protokołu IPv6 dla usługi Azure Virtual Network ma następujące ograniczenia:
- Protokół IPv6 dla usługi Azure Virtual Network (wersja zapoznawcza) jest dostępny we wszystkich globalnych regionach platformy Azure, ale tylko na platformie Azure — nie w chmurach dla instytucji rządowych.
- Obsługa portalu dla składników usługa Load Balancer w warstwie Standardowa jest tylko do wyświetlania.  Dostępna jest jednak Pełna pomoc techniczna i dokumentacja (z przykładami) dla wdrożeń usługa Load Balancer w warstwie Standardowa przy użyciu programu Azure PowerShell i interfejsu wiersza polecenia (CLI).   
- Obsługa Network Watcher w wersji zapoznawczej jest ograniczona do dzienników przepływów sieciowej grupy zabezpieczeń i przechwytywania pakietów sieciowych.
- Wirtualne sieci równorzędne (w regionach lub globalnie) nie są obsługiwane w wersji zapoznawczej.
- W przypadku korzystania z zewnętrznej Load Balancer zewnętrznego IPv6 stosowane są następujące ograniczenia: 
  - Reguły wychodzące mogą odwoływać się do wielu publicznych adresów IP frontonu, ale **nie** mogą odwoływać się do publicznego prefiksu IPv6. Publiczny prefiks adresu IP obsługuje tylko prefiksy IPv4.
  - Reguły równoważenia obciążenia IPv6 **nie** mogą korzystać z funkcji *zmiennoprzecinkowego adresu IP* . Ponowne użycie portów w wystąpieniach zaplecza jest obsługiwane tylko z protokołem IPv4.
- Funkcja prefiksu publicznego adresu IP platformy Azure nie obsługuje bloku adresów IPv6 dostępnych z Internetu.

## <a name="pricing"></a>Cennik

Opłaty za zasoby i przepustowość platformy IPv6 są naliczone według stawki IPv4. Nie są naliczane żadne dodatkowe ani inne opłaty za protokół IPv6. Możesz znaleźć szczegółowe informacje o cenach [publicznych adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/), [przepustowości sieci](https://azure.microsoft.com/pricing/details/bandwidth/)lub [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [wdrożyć aplikację podwójnego stosu IPv6 przy użyciu Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Dowiedz się, jak [wdrożyć aplikację podwójnego stosu IPv6 przy użyciu interfejsu wiersza polecenia platformy Azure](virtual-network-ipv4-ipv6-dual-stack-cli.md).
