---
title: Wiele frontendów — Azure Load Balancer
description: Dzięki tej ścieżce szkoleniowej rozpocznij przegląd wielu frontendów w usłudze Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 0a54416a70a8561edfad5915944100e0ce686bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771261"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Wiele frontendów dla modułu równoważenia obciążenia platformy Azure

Moduł równoważenia obciążenia platformy Azure umożliwia ładowanie usług równoważenia na wielu portach, wielu adresach IP lub obu tych. Publicznej i wewnętrznej definicji modułu równoważenia obciążenia można użyć do przepływu równoważenia obciążenia w zestawie maszyn wirtualnych.

W tym artykule opisano podstawy tej zdolności, ważne pojęcia i ograniczenia. Jeśli zamierzasz udostępnić usługi tylko na jeden adres IP, można znaleźć uproszczone instrukcje [dotyczące](load-balancer-get-started-internet-portal.md) publicznych lub [wewnętrznych](load-balancer-get-started-ilb-arm-portal.md) konfiguracji modułu równoważenia obciążenia. Dodawanie wielu frontendów jest przyrostowe do konfiguracji pojedynczego frontu. Korzystając z pojęć w tym artykule, można rozwinąć uproszczoną konfigurację w dowolnym momencie.

Podczas definiowania modułu równoważenia obciążenia platformy Azure, wewnętrznej bazy danych i konfiguracji puli wewnętrznej bazy danych są połączone z regułami. Sonda kondycji, do którego odwołuje się reguła, służy do określenia sposobu wysyłania nowych przepływów do węzła w puli wewnętrznej bazy danych. Frontend (aka VIP) jest zdefiniowany przez 3-krotka składa się z adresu IP (publicznego lub wewnętrznego), protokołu transportu (UDP lub TCP) i numer portu z reguły równoważenia obciążenia. Pula wewnętrznej bazy danych jest kolekcją konfiguracji IP maszyny wirtualnej (część zasobu karty sieciowej), które odwołują się do puli wewnętrznej bazy danych modułu równoważenia obciążenia.

Poniższa tabela zawiera kilka przykładowych konfiguracji frontendu:

| Fronton | Adres IP | Protokół | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

Tabela zawiera cztery różne frontendy. Frontendy #1, #2 i #3 są pojedynczym frontendem z wieloma regułami. Ten sam adres IP jest używany, ale port lub protokół jest inny dla każdego frontendu. Frontendy #1 i #4 są przykładem wielu frontendów, gdzie ten sam protokół frontendu i port są ponownie używane przez wiele frontendów.

Moduł równoważenia obciążenia platformy Azure zapewnia elastyczność w definiowaniu reguł równoważenia obciążenia. Reguła deklaruje, jak adres i port na wewnętrznej stronie jest mapowany na adres docelowy i port w wewnętrznej bazy danych. To, czy porty wewnętrznej bazy danych są ponownie zaużywane w regułach, zależy od typu reguły. Każdy typ reguły ma określone wymagania, które mogą mieć wpływ na konfigurację hosta i projekt sondy. Istnieją dwa typy reguł:

1. Reguła domyślna bez ponownego użycia portu wewnętrznej bazy danych
2. Reguła Zmiennoprzecinka IP, w której porty wewnętrznej bazy danych są ponownie

Moduł równoważenia obciążenia platformy Azure umożliwia mieszanie obu typów reguł w tej samej konfiguracji modułu równoważenia obciążenia. Moduł równoważenia obciążenia można używać ich jednocześnie dla danej maszyny Wirtualnej lub dowolnej kombinacji, tak długo, jak należy przestrzegać ograniczeń reguły. Typ reguły, który wybierzesz, zależy od wymagań aplikacji i złożoności obsługi tej konfiguracji. Należy ocenić, które typy reguł są najlepsze dla twojego scenariusza.

Możemy zbadać te scenariusze dalej, zaczynając od zachowania domyślnego.

## <a name="rule-type-1-no-backend-port-reuse"></a>Typ reguły #1: Brak ponownego użycia portu zaplecza

![Ilustracja wielu frontendów z zieloną i fioletową frontendem](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

W tym scenariuszu frontendy są skonfigurowane w następujący sposób:

| Fronton | Adres IP | Protokół | port |
| --- | --- | --- | --- |
| ![zielona frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![fioletowa przednia](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP jest miejscem docelowym przepływu przychodzącego. W puli wewnętrznej bazy danych każda maszyna wirtualna udostępnia żądaną usługę na unikatowym porcie na DIP. Ta usługa jest skojarzona z frontend za pomocą definicji reguły.

Definiujemy dwie zasady:

| Reguła | Frontend mapy | Do puli wewnętrznej bazy danych |
| --- | --- | --- |
| 1 |![zielona frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![zaplecze](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![zaplecze](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![Adres VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![zaplecze](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![zaplecze](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Pełne mapowanie w usłudze Azure Load Balancer jest teraz w następujący sposób:

| Reguła | Adres IP frontu | Protokół | port | Element docelowy | port |
| --- | --- | --- | --- | --- | --- |
| ![zielona zasada](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Adres IP DIP |80 |
| ![fioletowa reguła](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Adres IP DIP |81 |

Każda reguła musi dawać przepływ z unikatową kombinacją docelowego adresu IP i portu docelowego. Zmieniając port docelowy przepływu, wiele reguł może dostarczać przepływy do tego samego DIP na różnych portach.

Sondy kondycji są zawsze kierowane do DIP maszyny Wirtualnej. Należy upewnić się, że sonda odzwierciedla kondycję maszyny Wirtualnej.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Typ reguły #2: ponowne użycie portu wewnętrznej bazy danych przy użyciu pływającego adresu IP

Moduł równoważenia obciążenia platformy Azure zapewnia elastyczność ponownego użycia portu frontendu w wielu frontendach, niezależnie od używanego typu reguły. Ponadto niektóre scenariusze aplikacji preferują lub wymagają tego samego portu, który ma być używany przez wiele wystąpień aplikacji na jednej maszynie Wirtualnej w puli wewnętrznej bazy danych. Typowe przykłady ponownego użycia portów obejmują klastrowanie w celu zapewnienia wysokiej dostępności, sieciowe urządzenia wirtualne i udostępnianie wielu punktów końcowych TLS bez ponownego szyfrowania.

Jeśli chcesz ponownie użyć portu wewnętrznej bazy danych w wielu regułach, należy włączyć przestawny adres IP w definicji reguły.

"Przestawny adres IP" to terminologia platformy Azure dla części tego, co jest znane jako direct server return (DSR). DSR składa się z dwóch części: topologii przepływu i schematu mapowania adresów IP. Na poziomie platformy moduł równoważenia obciążenia platformy Azure zawsze działa w topologii przepływu DSR, niezależnie od tego, czy przestawny adres IP jest włączony, czy nie. Oznacza to, że wychodząca część przepływu jest zawsze poprawnie przepisana, aby przepływła bezpośrednio z powrotem do źródła.

Przy użyciu domyślnego typu reguły platforma Azure udostępnia tradycyjny schemat mapowania adresów IP równoważenia obciążenia w celu ułatwienia użycia. Włączenie przestawnego adresu IP zmienia schemat mapowania adresów IP, aby zapewnić dodatkową elastyczność, jak wyjaśniono poniżej.

Na poniższym diagramie przedstawiono następującą konfigurację:

![Ilustracja wielu frontendów z zieloną i fioletową frontendem z DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

W tym scenariuszu każda maszyna wirtualna w puli wewnętrznej bazy danych ma trzy interfejsy sieciowe:

* DIP: wirtualna karta sieciowa skojarzona z maszyną wirtualną (konfiguracja IP zasobu karty sieciowej platformy Azure)
* Frontend 1: interfejs sprzężenia zwrotnego w trybie operacyjnym gościa, który jest skonfigurowany z adresem IP frontendu 1
* Frontend 2: interfejs sprzężenia zwrotnego w trybie operacyjnym gościa, który jest skonfigurowany z adresem IP frontendu 2

Dla każdej maszyny Wirtualnej w puli wewnętrznej bazy danych uruchom następujące polecenia w wierszu polecenia systemu Windows.

Aby uzyskać listę nazw interfejsów posiadanych na maszynie Wirtualnej, wpisz następujące polecenie:

    netsh interface show interface 

W przypadku karty sieciowej maszyny Wirtualnej (zarządzanej przez platformę Azure) wpisz to polecenie:

    netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
   (zastąp nazwę interfejsu nazwą tego interfejsu)

Dla każdego dodanego interfejsu sprzężenia zwrotnego powtórz następujące polecenia:

    netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
   (zastąp nazwę interfejsu nazwą tego interfejsu sprzężenia zwrotnego)
     
    netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
   (zastąp nazwę interfejsu nazwą tego interfejsu sprzężenia zwrotnego)

> [!IMPORTANT]
> Konfiguracja interfejsów sprzężenia zwrotnego jest wykonywana w ramach systemu operacyjnego gościa. Ta konfiguracja nie jest wykonywana lub zarządzana przez platformę Azure. Bez tej konfiguracji reguły nie będą działać. Definicje sondy kondycji używają DIP maszyny Wirtualnej, a nie interfejsu sprzężenia zwrotnego reprezentującego frontend DSR. W związku z tym usługa musi dostarczać odpowiedzi sondy na porcie DIP, które odzwierciedlają stan usługi oferowanej w interfejsie sprzężenia zwrotnego reprezentującego frontend DSR.


Załóżmy, że ta sama konfiguracja frontendu, jak w poprzednim scenariuszu:

| Fronton | Adres IP | Protokół | port |
| --- | --- | --- | --- |
| ![zielona frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![fioletowa przednia](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Definiujemy dwie zasady:

| Reguła | Fronton | Mapa do puli zaplecza |
| --- | --- | --- |
| 1 |![Reguły](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![zaplecze](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (w VM1 i VM2) |
| 2 |![Reguły](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![zaplecze](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (w VM1 i VM2) |

W poniższej tabeli przedstawiono pełne mapowanie w równoważeniu obciążenia:

| Reguła | Adres IP frontu | Protokół | port | Element docelowy | port |
| --- | --- | --- | --- | --- | --- |
| ![zielona zasada](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |tak samo jak frontend (65.52.0.1) |tak samo jak frontend (80) |
| ![fioletowa reguła](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |tak samo jak frontend (65.52.0.2) |tak samo jak frontend (80) |

Miejsce docelowe przepływu przychodzącego jest adresem IP frontendu w interfejsie sprzężenia zwrotnego na maszynie Wirtualnej. Każda reguła musi dawać przepływ z unikatową kombinacją docelowego adresu IP i portu docelowego. Zmieniając docelowy adres IP przepływu, ponowne użycie portu jest możliwe na tej samej maszynie wirtualnej. Usługa jest narażona na moduł równoważenia obciążenia przez powiązanie go z adresem IP i portem interfejsu sprzężenia zwrotnego odpowiedniego.

Należy zauważyć, że w tym przykładzie nie zmienia portu docelowego. Mimo że jest to scenariusz przestawnego adresu IP, moduł równoważenia obciążenia platformy Azure obsługuje również definiowanie reguły, aby przepisać port docelowy wewnętrznej bazy danych i aby go różnić od portu docelowego frontu.

Typ reguły Przestawny adres IP jest podstawą kilku wzorców konfiguracji modułu równoważenia obciążenia. Jednym z przykładów, który jest obecnie dostępny jest [SQL AlwaysOn z wielu odbiorników](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) konfiguracji. Z czasem będziemy dokumentować więcej z tych scenariuszy.

## <a name="limitations"></a>Ograniczenia

* Wiele konfiguracji frontendu są obsługiwane tylko z maszyn wirtualnych IaaS.
* W przypadku reguły przestawnego adresu IP aplikacja musi używać podstawowej konfiguracji IP dla wychodzących przepływów SNAT. Jeśli aplikacja wiąże się z adresem IP frontendu skonfigurowanym w interfejsie sprzężenia zwrotnego w systemie operacyjnym gościa, wychodzący SNAT platformy Azure nie jest dostępny do ponownego zapisu przepływu wychodzącego, a przepływ zakończy się niepowodzeniem.  Przejrzyj [scenariusze wychodzące](load-balancer-outbound-connections.md).
* Publiczne adresy IP mają wpływ na rozliczenia. Aby uzyskać więcej informacji, zobacz [Ceny adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Obowiązują limity subskrypcji. Aby uzyskać więcej informacji, zobacz [Limity usług, aby](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) uzyskać szczegółowe informacje.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [połączenia wychodzące,](load-balancer-outbound-connections.md) aby zrozumieć wpływ wielu frontendów na zachowanie połączenia wychodzącego.
