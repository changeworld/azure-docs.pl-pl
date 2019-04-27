---
title: Wiele frontonów dla modułu równoważenia obciążenia platformy Azure
titlesuffix: Azure Load Balancer
description: Omówienie wielu frontonów w module równoważenia obciążenia platformy Azure
services: load-balancer
documentationcenter: na
author: chkuhtz
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: chkuhtz
ms.openlocfilehash: b9a140314b8eba6386c37bdbcf2bb3de58589335
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594200"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Wiele frontonów dla modułu równoważenia obciążenia platformy Azure

Usługa Azure Load Balancer pozwala załadować saldo usług na wielu portach i/lub wiele adresów IP. Public i internal load balancer definicje umożliwia obciążenia równoważenie przepływów zestawu maszyn wirtualnych.

W tym artykule opisano podstawowe informacje dotyczące tej możliwości, ważnych pojęć i ograniczeń. Jeśli zamierzasz udostępnić usługi na jeden adres IP, możesz znaleźć uproszczone instrukcje dotyczące [publicznych](load-balancer-get-started-internet-portal.md) lub [wewnętrzny](load-balancer-get-started-ilb-arm-portal.md) konfiguracji modułu równoważenia obciążenia. Dodawanie wielu frontonów jest dodawane do konfiguracji pojedynczego serwera sieci Web. Przy użyciu koncepcji, w tym artykule, można rozwinąć uproszczona konfiguracja w dowolnym momencie.

Podczas definiowania usługi Azure Load Balancer frontonu i konfigurację puli zaplecza są połączone z zasadami. Sonda kondycji przywoływanego przez regułę służy do określania sposobu nowych przepływów są wysyłane do węzłów w puli zaplecza. (Alias VIP) frontonu jest definiowany przez 3-krotka składające się z adresu IP (wewnętrznego lub publicznego), protokół transportu (UDP lub TCP) i numer portu z regułą równoważenia obciążenia. Pula zaplecza jest kolekcją konfiguracji adresu IP maszyny wirtualnej (część zasobów kart Sieciowych), które odwołują się do puli zaplecza modułu równoważenia obciążenia.

Poniższa tabela zawiera niektóre przykładowe konfiguracje frontonu:

| Fronton | Adres IP | protokół | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

W tabeli przedstawiono cztery różne frontonów. Frontonów nr 1, #2 i #3 są pojedynczego serwera sieci Web za pomocą wielu reguł. Ten sam adres IP jest używany, ale portu lub protokół różni się dla każdego frontonu. Frontonów nr 1 i #4 są przykładem wielu frontonów, gdy ten sam protokół frontonu i portu są ponownie używane przez wiele frontonów.

Usługa Azure Load Balancer zapewnia elastyczność podczas definiowania reguły równoważenia obciążenia. Reguła deklaruje, jak adres i port we frontonie jest mapowany na docelowy adres i port zaplecza. Określa, czy porty wewnętrznej bazy danych są ponownie używane przez reguły zależy od typu reguły. Każdy typ reguły ma specyficzne wymagania, które mogą mieć wpływ na projekt konfiguracji i sondowania hosta. Istnieją dwa typy zasad:

1. Domyślną regułę za pomocą nie ponownego użycia portu wewnętrznej bazy danych
2. Reguła pływającego adresu IP, gdzie są ponownie używane porty wewnętrznej bazy danych

Usługa Azure Load Balancer można mieszać oba typy reguł w tej samej konfiguracji usługi równoważenia obciążenia. Moduł równoważenia obciążenia można ich używać jednocześnie danej maszyny Wirtualnej lub dowolnej kombinacji, tak długo, jak przestrzega ograniczeń reguły. Jakiego typu reguły, możesz wybrać zależy od wymagań aplikacji i złożoność obsługi tej konfiguracji. Należy ocenić, które typy reguł są najlepsze w przypadku danego scenariusza.

Te scenariusze dalsze — omówimy, zaczynając od zachowanie domyślne.

## <a name="rule-type-1-no-backend-port-reuse"></a>Typ reguły #1: Nie ponownego użycia portu wewnętrznej bazy danych

![Wiele ilustrację frontonu zielony oraz purpurowej frontonu](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

W tym scenariuszu frontonów są skonfigurowane w następujący sposób:

| Fronton | Adres IP | protokół | port |
| --- | --- | --- | --- |
| ![zielony frontonu](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![purpurowa frontonu](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP jest miejscem docelowym przepływu ruchu przychodzącego. W puli zaplecza każda maszyna wirtualna udostępnia żądanej usługi na porcie unikatowy DIP. Ta usługa jest skojarzony z serwera sieci Web za pośrednictwem definicji reguły.

Zdefiniowano dwie reguły:

| Reguła | Mapowanie frontonu | Do puli zaplecza |
| --- | --- | --- |
| 1 |![zielony frontonu](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![Wewnętrznej bazy danych](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![Wewnętrznej bazy danych](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![Wewnętrznej bazy danych](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![Wewnętrznej bazy danych](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Pełne mapowania w module równoważenia obciążenia Azure jest teraz w następujący sposób:

| Reguła | Adres IP frontonu | protokół | port | Element docelowy | port |
| --- | --- | --- | --- | --- | --- |
| ![Reguła zielony](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Adres DIP |80 |
| ![purpurowa reguły](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Adres DIP |81 |

Każda reguła musi utworzyć przepływu przy użyciu unikatowego połączenia docelowy adres IP i port docelowy. Przez zróżnicowanie portu docelowego przepływu, wiele reguł mogą dostarczać przepływy do tego samego adresu DIP na inne porty.

Sondy kondycji są zawsze kierowane do DIP maszyny Wirtualnej. Należy upewnić się, możesz czy swoje badania odzwierciedlają kondycji maszyny Wirtualnej.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Typ #2 reguły: ponowne użycie portu wewnętrznej bazy danych, za pomocą pływającego adresu IP

Usługa Azure Load Balancer zapewnia elastyczność do ponownego użycia portu frontonu w wielu frontonów, niezależnie od typu reguły używane. Ponadto niektóre scenariusze aplikacji Preferuj lub wymagają tego samego portu, który będzie używany przez wiele wystąpień aplikacji na jednej maszynie Wirtualnej w puli zaplecza. Typowe przykłady ponowne użycie portu obejmują zapewniającymi wysoką dostępność, sieci wirtualnych urządzeń sieciowych i udostępnia wiele TLS punktów końcowych bez ponownego szyfrowania.

Jeśli chcesz ponownie użyć portu wewnętrznej bazy danych między wieloma regułami, należy włączyć pływającego adresu IP w definicji reguły.

"Pływającego adresu IP" jest terminologii platformy Azure przez pewną część co to jest znany jako bezpośrednie serwera Return (DSR). Żądanie praw podmiotu danych składa się z dwóch części: Topologia przepływu i adresów IP schemat mapowania. Na poziomie platformy Azure Load Balancer zawsze działa w topologii przepływu żądania podmiotu danych, niezależnie od tego, czy pływającego adresu IP jest włączony, czy nie. Oznacza to, że wychodzącego część przepływu jest zawsze poprawnie przepisany przepływ bezpośrednio do źródła.

Z domyślnym typem reguły Azure udostępnia punkt tradycyjnych schemat mapowania adresów IP w celu ułatwienia z równoważeniem obciążenia. Włączanie pływającego adresu IP zmienia schemat mapowania adresu IP, aby umożliwić większą elastyczność, co zostało opisane poniżej.

Na poniższym diagramie przedstawiono tę konfigurację:

![Wiele ilustrację frontonu zielony oraz purpurowej frontonu za pomocą DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

W tym scenariuszu każda maszyna wirtualna w puli zaplecza ma trzy interfejsy sieciowe:

* DIP: wirtualnej karty Sieciowej skojarzonych z maszyną Wirtualną (konfiguracja adresu IP zasobu kart Sieciowych platformy Azure)
* : 1 sprzężenia zwrotnego interfejsu frontonu w ramach systemu operacyjnego, który jest skonfigurowany przy użyciu adresu IP frontonu 1 gościa
* Fronton 2: interfejsu sprzężenia zwrotnego w ramach systemu operacyjnego, który jest skonfigurowany przy użyciu adresu IP frontonu 2 gościa

> [!IMPORTANT]
> Konfiguracja interfejsów sprzężenia zwrotnego jest wykonywane w ramach systemu operacyjnego gościa. Ta konfiguracja nie jest wykonywane lub zarządzane przez platformę Azure. Bez tej konfiguracji zasady nie będą działać. Definicje sondy kondycji używać DIP maszyny Wirtualnej, a nie interfejsu sprzężenia zwrotnego reprezentujący frontonu żądanie praw podmiotu danych. W związku z tym usługi, musisz podać odpowiedzi sond na porcie DIP, która odzwierciedla stan usługi oferowane na interfejsu sprzężenia zwrotnego reprezentujący frontonu żądanie praw podmiotu danych.

Załóżmy, że taką samą konfigurację frontonu, jak w poprzednim scenariuszu:

| Fronton | Adres IP | protokół | port |
| --- | --- | --- | --- |
| ![zielony frontonu](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![purpurowa frontonu](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Zdefiniowano dwie reguły:

| Reguła | Fronton | Mapowanie do puli zaplecza |
| --- | --- | --- |
| 1 |![zasada](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![Wewnętrznej bazy danych](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (w VM1 i VM2) |
| 2 |![zasada](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![Wewnętrznej bazy danych](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (w VM1 i VM2) |

W poniższej tabeli przedstawiono pełną mapowania w module równoważenia obciążenia:

| Reguła | Adres IP frontonu | protokół | port | Element docelowy | port |
| --- | --- | --- | --- | --- | --- |
| ![Reguła zielony](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |takie same jak serwera sieci Web (65.52.0.1) |takie same jak serwera sieci Web (80) |
| ![purpurowa reguły](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |takie same jak serwera sieci Web (65.52.0.2) |takie same jak serwera sieci Web (80) |

Miejsce docelowe w przepływie ruchu przychodzącego jest adres IP frontonu dla interfejsu sprzężenia zwrotnego na maszynie wirtualnej. Każda reguła musi utworzyć przepływu przy użyciu unikatowego połączenia docelowy adres IP i port docelowy. Przez zróżnicowanie docelowy adres IP przepływu, ponowne użycie portu jest możliwe w tej samej maszyny Wirtualnej. Usługa jest uwidaczniany do modułu równoważenia obciążenia przez powiązanie adresu IP frontonu i portu interfejsu sprzężenia zwrotnego odpowiedniego.

Należy zauważyć, że w tym przykładzie nie zmienia się port docelowy. Mimo że jest to scenariusz pływającego adresu IP, usługi Azure Load Balancer obsługuje również zdefiniować regułę, aby ponownie zapisuje docelowy port zaplecza i odróżnia go od portu docelowego serwera sieci Web.

Typ reguły pływającego adresu IP jest podstawą kilka wzorców konfiguracji modułu równoważenia obciążenia. Przykładem, która jest obecnie dostępna jest [funkcji SQL AlwaysOn z wielu odbiorników](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) konfiguracji. Wraz z upływem czasu będzie udokumentowaliśmy kilka z tych scenariuszy.

## <a name="limitations"></a>Ograniczenia

* Wiele konfiguracji frontonu są obsługiwane tylko maszyny wirtualne IaaS.
* Z regułą pływającego adresu IP aplikacji należy użyć podstawowa konfiguracja adresu IP dla ruchu wychodzącego przepływów. Jeśli aplikacja tworzy powiązanie z adresu IP frontonu, które są skonfigurowane na sprzężenia zwrotnego interfejs w SNAT gościa systemu operacyjnego, platformy Azure nie jest dostępny do przepisania przepływu wychodzącego, a przepływ nie powiedzie się.
* Publiczne adresy IP ma wpływ na rozliczenia. Aby uzyskać więcej informacji, zobacz [cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Mają zastosowanie limity subskrypcji. Aby uzyskać więcej informacji, zobacz [limitów usług](../azure-subscription-service-limits.md#networking-limits) Aby uzyskać szczegółowe informacje.

## <a name="next-steps"></a>Kolejne kroki

- Przegląd [połączeń wychodzących](load-balancer-outbound-connections.md) Aby zrozumieć wpływ wiele frontonów na zachowanie połączenia wychodzące.
