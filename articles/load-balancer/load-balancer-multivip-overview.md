---
title: Moduł równoważenia obciążenia wielu Frontends dla platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie Frontends wielu w module równoważenia obciążenia Azure
services: load-balancer
documentationcenter: na
author: chkuhtz
manager: narayan
editor: ''
ms.assetid: 748e50cd-3087-4c2e-a9e1-ac0ecce4f869
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: chkuhtz
ms.openlocfilehash: cf8fa396e0518e1c847225dfc1d8f91c3421bd11
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30181493"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Wiele Frontends dla usługi równoważenia obciążenia Azure

Moduł równoważenia obciążenia Azure pozwala załadować zrównoważyć usług w wielu portów i/lub wiele adresów IP. Definicje modułu równoważenia obciążenia publicznych oraz wewnętrznych umożliwia obciążenie przepływów saldo zestaw maszyn wirtualnych.

W tym artykule opisano podstawowe informacje dotyczące tej możliwości, ważne pojęcia i ograniczeń. Jeśli planujesz do udostępnienia usług na jeden adres IP, można znaleźć uproszczony instrukcje dotyczące [publicznego](load-balancer-get-started-internet-portal.md) lub [wewnętrzny](load-balancer-get-started-ilb-arm-portal.md) konfiguracji usługi równoważenia obciążenia. Dodawanie wielu frontends jest przyrostowe konfiguracji pojedynczego serwera sieci Web. Przy użyciu pojęć, w tym artykule, możesz rozszerzyć uproszczona konfiguracja w dowolnym momencie.

Podczas definiowania modułu równoważenia obciążenia Azure frontonu i Konfiguracja puli wewnętrznej bazy danych są połączone z zasadami. Przywoływany przez regułę sondy kondycji służy do określania nowych przepływów są wysyłane do węzła w puli zaplecza. (Alias VIP) serwera sieci Web jest definiowany przez krotka 3 składającej się z adresu IP (wewnętrzny lub publiczny), protokół transportu (UDP lub TCP) i numer portu z reguły równoważenia obciążenia. Puli wewnętrznej bazy danych jest kolekcją konfiguracje adresów IP maszyny wirtualnej (część zasobów kart interfejsu Sieciowego), które odwołują się do puli zaplecza modułu równoważenia obciążenia.

W poniższej tabeli przedstawiono niektóre przykładowe konfiguracje serwera sieci Web:

| Fronton | Adres IP | protokół | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

W tabeli przedstawiono cztery różne frontends. Frontends #1, #2 i #3 są pojedynczego serwera sieci Web przy użyciu wielu reguł. Jest używany ten sam adres IP, ale portu lub protokół jest różne dla każdego serwera sieci Web. Frontends #1 i #4 są przykładem wielu frontends, gdzie tego samego protokołu frontonu i port są ponownie przez wiele frontends.

Moduł równoważenia obciążenia Azure zapewnia elastyczność podczas definiowania reguł równoważenia obciążenia. Reguła deklaruje, jak adres i port na frontonie jest mapowane na docelowy adres i port do wewnętrznej bazy danych. Określa, czy porty wewnętrznej bazy danych są ponownie przez zasady, zależy od typu reguły. Każdy typ reguły ma szczególne wymagania, które mogą mieć wpływ na projekt konfiguracji i badania hosta. Istnieją dwa typy zasad:

1. Domyślna reguła z nie ponownego użycia portu zaplecza
2. Reguły pływającego adresu IP, gdzie są ponownie używane porty wewnętrznej bazy danych

Moduł równoważenia obciążenia Azure można mieszać obu typów reguł w tej samej konfiguracji usługi równoważenia obciążenia. Moduł równoważenia obciążenia można używać ich równocześnie dla danej maszyny Wirtualnej lub dowolną kombinację, pod warunkiem przestrzegania ograniczeń reguły. Typ reguły, który można wybrać, zależy od wymagań aplikacji i złożoność obsługi tej konfiguracji. Należy ocenić, jakie typy reguł są najlepsze w przypadku danego scenariusza.

Firma Microsoft Poznaj te dodatkowe scenariusze, zaczynając od zachowanie domyślne.

## <a name="rule-type-1-no-backend-port-reuse"></a>Typ #1 reguły: nie ponownemu portu zaplecza

![Wiele ilustracji frontonu z zielonym i purpurowa frontonu](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

W tym scenariuszu frontends są skonfigurowane w następujący sposób:

| Fronton | Adres IP | protokół | port |
| --- | --- | --- | --- |
| ![zielony frontonu](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![purpurowa frontonu](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP jest miejscem docelowym przepływu ruchu przychodzącego. W puli zaplecza każdej maszyny Wirtualnej przedstawia żądanej usługi na porcie unikatowy na DIP. Ta usługa jest skojarzony z serwera sieci Web za pośrednictwem definicji reguły.

Definiujemy dwie reguły:

| Reguła | Mapa frontonu | Do puli wewnętrznej bazy danych |
| --- | --- | --- |
| 1 |![zielony frontonu](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![wewnętrznej bazy danych](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![wewnętrznej bazy danych](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![wewnętrznej bazy danych](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![wewnętrznej bazy danych](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Zakończenie mapowanie w usłudze równoważenia obciążenia Azure jest teraz w następujący sposób:

| Reguła | Adres IP frontonu | protokół | port | Element docelowy | port |
| --- | --- | --- | --- | --- | --- |
| ![zielony reguły](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Adres IP DIP |80 |
| ![Reguła purpurowa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Adres IP DIP |81 |

Każda reguła musi mieć przepływ z unikatowych kombinacji adresu IP i port docelowy. Przez zróżnicowanie port docelowy przepływu, wiele reguł mogą dostarczać przepływów do tego samego adresu DIP na różnych portów.

Sondy kondycji zawsze są kierowane do DIP maszyny wirtualnej. Należy zapewnić, że czy Twoje sondowania odzwierciedla kondycji maszyny wirtualnej.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Typ #2 reguły: ponowne użycie portu zaplecza przy użyciu pływającego adresu IP

Moduł równoważenia obciążenia Azure zapewnia elastyczność do ponownego użycia portów frontonu w wielu frontends bez względu na używany typ reguły. Ponadto niektóre scenariusze aplikacji preferowane lub wymagają tego samego portu, który będzie używany przez wiele wystąpień aplikacji na jednej maszynie Wirtualnej w puli zaplecza. Typowe przykłady ponownemu portu obejmują klastrowanie wysokiej dostępności, sieci wirtualnych urządzeń i udostępnia wiele TLS punktów końcowych bez ponownego szyfrowania.

Jeśli chcesz ponownie użyć portu zaplecza przez wiele reguł, należy włączyć pływającego adresu IP w definicji reguły.

"Pływającego adresu IP" jest terminologii platformy Azure dla części określane jako bezpośrednie serwer zwraca DSR (). DSR składa się z dwóch części: Topologia przepływu i adresów IP schematu mapowania. Na poziomie platformy usługi równoważenia obciążenia Azure zawsze działa w topologii przepływu DSR niezależnie od tego, czy pływający adres IP jest włączone. Oznacza to, że część ruchu wychodzącego przepływu jest zawsze poprawnie ulegną przepływ bezpośrednio do źródła.

Z typem reguły domyślne Azure udostępnia tradycyjne schemat mapowania adresów IP dla łatwość użycia z równoważeniem obciążenia. Włączanie pływający adres IP zmienia schemat mapowania adresów IP do przyznania dodatkowa elastyczność, co zostało opisane poniżej.

Na poniższym diagramie przedstawiono tę konfigurację:

![Wiele ilustracji frontonu z zielonym i purpurowa frontonu z DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

W tym scenariuszu dla każdej maszyny Wirtualnej w puli zaplecza ma trzy interfejsów sieciowych:

* DIP: wirtualnej karty Sieciowej skojarzonych z maszyną Wirtualną (Konfiguracja IP zasobów kart platformy Azure)
* Frontonu 1: interfejsu sprzężenia zwrotnego gościu systemu operacyjnego, który jest skonfigurowany z adresem IP frontonu 1
* Frontonu 2: interfejsu sprzężenia zwrotnego gościu systemu operacyjnego, który jest skonfigurowany z adresem IP frontonu 2

> [!IMPORTANT]
> Konfiguracja interfejsów sprzężenia zwrotnego jest wykonywane w ramach systemu operacyjnego gościa. Ta konfiguracja nie jest wykonywane lub zarządzane przez usługę Azure. Bez tej konfiguracji zasad nie będzie działać. Definicje sondy kondycji używać DIP maszyny Wirtualnej, a nie reprezentujący frontonu DSR interfejsu sprzężenia zwrotnego. W związku z tym usługa podać sondowania odpowiedzi na porcie DIP, która odzwierciedla stan usługi są oferowane w reprezentujący frontonu DSR interfejsu sprzężenia zwrotnego.

Załóżmy, że w tej samej konfiguracji serwera sieci Web, jak w poprzednim scenariuszu:

| Fronton | Adres IP | protokół | port |
| --- | --- | --- | --- |
| ![zielony frontonu](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![purpurowa frontonu](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Definiujemy dwie reguły:

| Reguła | Fronton | Mapowanie do puli wewnętrznej bazy danych |
| --- | --- | --- |
| 1 |![zasada](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![wewnętrznej bazy danych](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (w VM1 i maszyny VM2) |
| 2 |![zasada](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![wewnętrznej bazy danych](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (w VM1 i maszyny VM2) |

W poniższej tabeli przedstawiono pełną mapowanie w usłudze równoważenia obciążenia:

| Reguła | Adres IP frontonu | protokół | port | Element docelowy | port |
| --- | --- | --- | --- | --- | --- |
| ![zielony reguły](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |identyczny frontonu (65.52.0.1) |identyczny frontonu (80) |
| ![Reguła purpurowa](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |identyczny frontonu (65.52.0.2) |identyczny frontonu (80) |

Lokalizacja docelowa przepływu ruchu przychodzącego jest adres IP frontonu dla interfejsu sprzężenia zwrotnego na maszynie wirtualnej. Każda reguła musi mieć przepływ z unikatowych kombinacji adresu IP i port docelowy. Przez zróżnicowanie przepływu docelowy adres IP, portu ponownemu jest możliwe w tej samej maszyny Wirtualnej. Usługa jest narażony na usługi równoważenia obciążenia przez powiązanie adresu IP i portu interfejsu sprzężenia zwrotnego odpowiedniego serwera sieci Web.

Zwróć uwagę, że w tym przykładzie nie zmieniają port docelowy. Mimo że jest to scenariusz pływającego adresu IP, usługi równoważenia obciążenia Azure obsługuje również definiowania reguły ponownego zapisywania port docelowy wewnętrznej bazy danych i odróżnia go od portu docelowego serwera sieci Web.

Typ reguły pływający adres IP jest podstawą kilka wzorców konfiguracji usługi równoważenia obciążenia. Przykładem, która jest obecnie dostępna jest [funkcji SQL AlwaysOn z wiele odbiorników](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) konfiguracji. Wraz z upływem czasu firma Microsoft będzie dokumentu jeden z tych scenariuszy.

## <a name="limitations"></a>Ograniczenia

* Wiele konfiguracji serwera sieci Web są obsługiwane tylko z maszyn wirtualnych IaaS.
* Z regułą pływającego adresu IP aplikacja musi używać podstawową konfigurację protokołu IP przepływów wychodzących. Jeśli aplikacja jest powiązany z adresu IP frontonu, które są skonfigurowane na sprzężenia zwrotnego interfejsu SNAT system operacyjny gościa, Azure w nie są dostępne do edycji przepływu wychodzącego, a przepływ nie powiedzie się.
* Publiczne adresy IP mają wpływ na rozliczenia. Aby uzyskać więcej informacji, zobacz [cennik adresu IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Zastosuj limity subskrypcji. Aby uzyskać więcej informacji, zobacz [usługi limity](../azure-subscription-service-limits.md#networking-limits) szczegółowe informacje.

## <a name="next-steps"></a>Kolejne kroki

- Przegląd [połączeń wychodzących](load-balancer-outbound-connections.md) zrozumienie wpływu wielu frontends na zachowanie połączenia wychodzącego.
