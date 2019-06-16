---
title: Omówienie protokołu IPv6 dla modułu równoważenia obciążenia platformy Azure
titlesuffix: Azure Load Balancer
description: Omówienie obsługi protokołu IPv6 dla usługi Azure Load Balancer i równoważenia obciążenia maszyn wirtualnych.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: Protokół IPv6, usługa azure load balancer, podwójnego stosu, publiczny adres ip, natywnego protokołu ipv6, aplikację mobilną, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: kumud
ms.openlocfilehash: 894a56c2e51e8fa8a2d72253563d218416ace4cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60861963"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Omówienie protokołu IPv6 dla modułu równoważenia obciążenia platformy Azure


>[!NOTE] 
>Usługa Azure Load Balancer obsługuje dwa różne typy: Podstawowa i Standardowa. W tym artykule omówiono usługę Load Balancer w warstwie Podstawowa. Aby uzyskać więcej informacji na temat Balancer w warstwie standardowa, zobacz [omówienie Standard Load Balancer](load-balancer-standard-overview.md).

Moduły równoważenia obciążenia dostępnego z Internetu, można wdrożyć przy użyciu adresu IPv6. Oprócz protokołu IPv4 połączenia dzięki temu następujące możliwości:

* Natywne end-to-end łączność IPv6 od publicznego klientów internetowych i usłudze Azure Virtual Machines (VMs) za pośrednictwem modułu równoważenia obciążenia.
* Natywne end-to-end IPv6 wychodzącym między maszynami wirtualnymi i publicznych klientom Internet IPv6.

Poniższy obraz przedstawia działanie protokołu IPv6 dla usługi Azure Load Balancer.

![Usługa Azure Load Balancer przy użyciu protokołu IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Po wdrożeniu IPv4 lub Internet włączony protokół IPv6 klient może komunikować się z publicznych adresów IPv4 lub IPv6 (lub nazw hostów) usługi równoważenia obciążenia dostępnego z Internetu na platformie Azure. Moduł równoważenia obciążenia kieruje pakiety IPv6 do prywatnymi adresami IPv6 maszyn wirtualnych przy użyciu translatora adresów sieciowych (NAT). IPv6 Internet klienta nie może komunikować się bezpośrednio z adresu IPv6 maszyn wirtualnych.

## <a name="features"></a>Funkcje

Oferuje natywnej obsługi protokołu IPv6 dla maszyn wirtualnych wdrożonych za pośrednictwem usługi Azure Resource Manager:

1. Równoważenia obciążenia protokołu IPv6 usług dla klientów protokołu IPv6 w Internecie
2. Natywnego protokołu IPv6 i IPv4 punkty końcowe na maszynach wirtualnych ("podwójnego skumulowany")
3. Natywne połączenia protokołu IPv6 dla ruchu przychodzącego i wychodzącego zainicjowane
4. Obsługiwane protokoły, takie jak TCP, UDP i HTTP (S) Włącz pełnego zakresu architektur usług

## <a name="benefits"></a>Korzyści

Ta funkcja umożliwia następujące kluczowe korzyści:

* Spełnia regulacjom rządowym wymagające nowych aplikacji i udostępniane klientom obsługującym tylko protokół IPv6
* Włącz mobilnych i Internetu rzeczy (IOT) deweloperów na potrzeby rozwiązania mobilne rosnące i rynków IOT procesory, stos (IPv4 + IPv6) usługi Azure Virtual Machines

## <a name="details-and-limitations"></a>Szczegółowe informacje i ograniczenia

Szczegóły

* Usługa Azure DNS zawiera element IPv4 i IPv6 AAAA rekordy nazwy i odpowiada za pomocą obu rekordów dla modułu równoważenia obciążenia. Klient wybiera adresów (IPv4 lub IPv6) do komunikacji z.
* Po maszyny Wirtualnej inicjuje połączenie z publicznych urządzeniu połączonym z Internetem IPv6, źródłowej maszyny Wirtualnej adres IPv6 jest adres sieciowy translacji (NAT) do publicznego adresu protokołu IPv6 modułu równoważenia obciążenia.
* Maszyny wirtualne z systemem operacyjnym Linux musi być skonfigurowany do otrzymywać adresy IP protokołu IPv6 za pośrednictwem protokołu DHCP. Wiele obrazów systemu Linux w galerii systemu Azure są już skonfigurowane do obsługi protokołu IPv6 bez żadnych modyfikacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie protokołu DHCPv6 dla maszyn wirtualnych z systemem Linux](load-balancer-ipv6-for-linux.md)
* Jeśli zdecydujesz się sondę kondycji za pomocą modułu równoważenia obciążenia, Utwórz sondę IPv4 i korzystania z punktów końcowych IPv4 i IPv6. Jeśli usługa na maszynie Wirtualnej ulegnie awarii, punkty końcowe IPv4 i IPv6 są pobierane z rotacji.

Ograniczenia

* Nie można dodać reguły równoważenia obciążenia protokołu IPv6 w witrynie Azure portal. Reguły można tworzyć tylko przy użyciu szablonu, interfejsu wiersza polecenia, programu PowerShell.
* Nie może uaktualnić istniejących maszyn wirtualnych do używania adresów IPv6. Należy wdrożyć nowe maszyny wirtualne.
* Pojedynczy adres IPv6 można przypisać do pojedynczym interfejsem sieciowym w każdej maszynie Wirtualnej.
* Publiczne adresy IPv6 nie można przypisać do maszyny Wirtualnej. Ich można przypisać tylko do modułu równoważenia obciążenia.
* Nie można skonfigurować wstecznego wyszukiwania DNS dla sieci publicznych adresów IPv6.
* Maszyny wirtualne przy użyciu adresów IPv6 nie mogą być członkami usługi w chmurze platformy Azure. One mogą być połączone z usługą Azure Virtual Network (VNet) i komunikują się ze sobą za pośrednictwem ich adresów IPv4.
* Prywatne adresy IPv6 można wdrożyć na poszczególnych maszynach wirtualnych w grupie zasobów, ale nie można wdrożyć w grupie zasobów za pomocą zestawów skalowania.
* Maszyny wirtualne platformy Azure nie można połączyć za pośrednictwem protokołu IPv6 do innych maszyn wirtualnych, innych usług platformy Azure lub lokalnymi urządzeniami. Można tylko komunikują się z modułem równoważenia obciążenia platformy Azure za pośrednictwem protokołu IPv6. Jednak mogą się komunikować inne zasoby przy użyciu protokołu IPv4.
* Ochrona sieciowa grupa zabezpieczeń (NSG) w przypadku protokołu IPv4 jest obsługiwana w przypadku wdrożeń z obsługą dwóch stosów (IPv4 + IPv6). Sieciowe grupy zabezpieczeń nie są stosowane do punktów końcowych protokołu IPv6.
* Punkt końcowy IPv6 na maszynie Wirtualnej nie jest widoczne bezpośrednio do Internetu. Jest za modułem równoważenia obciążenia. Tylko te porty, które są określone w regułach równoważenia obciążenia są dostępne za pośrednictwem protokołu IPv6.
* Zmiana parametrów IdleTimeout dla protokołu IPv6 jest **obecnie nieobsługiwane**. Wartość domyślna to cztery minuty.
* Zmiana parametrów elementu loadDistributionMethod dla protokołu IPv6 jest **obecnie nieobsługiwane**.
* Zastrzeżone adresy IP protokołu IPv6 (gdzie dynamiczną = statyczne) są **obecnie nieobsługiwane**.
* NAT64 (tłumaczenie IPv6 do IPv4) nie jest obsługiwane.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wdrożyć moduł równoważenia obciążenia przy użyciu protokołu IPv6.

* [Dostępność protokołu IPv6, uporządkowane według regionów](https://go.microsoft.com/fwlink/?linkid=828357)
* [Wdrażanie modułu równoważenia obciążenia przy użyciu protokołu IPv6 przy użyciu szablonu](load-balancer-ipv6-internet-template.md)
* [Wdrażanie modułu równoważenia obciążenia przy użyciu protokołu IPv6 przy użyciu programu Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Wdrażanie modułu równoważenia obciążenia przy użyciu protokołu IPv6 przy użyciu wiersza polecenia platformy Azure](load-balancer-ipv6-internet-cli.md)
