---
title: Integracja zaporę platformy Azure z usługą Azure standardowego modułu równoważenia obciążenia
description: Dowiedz się, jak zintegrować zapory usługi Azure za pomocą usługi Azure Standard Load Balancer
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: 7ee92a7508918635849caafab4632bbba81ee628
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805248"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integracja zaporę platformy Azure z usługą Azure standardowego modułu równoważenia obciążenia

Możesz zintegrować zapory usługi Azure w sieci wirtualnej za pomocą usługi Azure Standard Load Balancer (publiczny lub wewnętrzny). 

To preferowane rozwiązanie jest integracja wewnętrznego modułu równoważenia obciążenia z Zaporą systemu Azure jest znacznie prostsza projektu. Jeśli masz już wdrożone, a użytkownik chce go przechowywać w miejscu, można użyć publicznego modułu równoważenia obciążenia. Jednak należy pamiętać o problem z routingiem asymetrycznym które może spowodować przerwanie działania funkcji przy użyciu scenariusz modułu równoważenia obciążenia publiczny.

Aby uzyskać więcej informacji na temat usługi Azure Load Balancer, zobacz [co to jest usługa Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Publiczny moduł równoważenia obciążenia

Za pomocą publicznego modułu równoważenia obciążenia moduł równoważenia obciążenia jest wdrażane za pomocą publicznego adresu IP frontonu.

### <a name="asymmetric-routing"></a>Routing asymetryczny

Routing asymetryczny jest którym pakiet ma jedną ścieżkę do miejsca docelowego, a ma inną ścieżkę, wracając do źródła. Ten problem występuje, gdy w podsieci zawiera trasy domyślnej, przechodząc na prywatny adres IP zapory i używasz publicznego modułu równoważenia obciążenia. W tym przypadku ruchu przychodzącego modułu równoważenia obciążenia jest odebranych za pośrednictwem publicznego adresu IP, ale ścieżki zwrotu przechodzi przez zaporę, prywatny adres IP. Ponieważ zapory stanowe, odrzuci zwracanie pakietów, ponieważ Zapora nie ma informacji o ustanowienie sesji.

### <a name="fix-the-routing-issue"></a>Rozwiąż problem z routingiem

Podczas wdrażania zapory usługi Azure w podsieci, co krokiem jest utworzenie domyślną trasę dla pakietów kierowanie podsieci za pośrednictwem zapory znajdujących się na AzureFirewallSubnet prywatnego adresu IP. Aby uzyskać więcej informacji, zobacz [Samouczek: Wdróż i Skonfiguruj zaporę platformy Azure przy użyciu witryny Azure portal](tutorial-firewall-deploy-portal.md#create-a-default-route).

Po wprowadzeniu zapory do danego scenariusza modułu równoważenia obciążenia ma ruch internetowy do pochodzić publiczny adres IP zapory. W tym miejscu Zapora dotyczy jego reguły zapory i translatorami adresów sieciowych pakiety publiczny adres IP modułu równoważenia obciążenia. Jest to, gdzie występuje problem. Pakiety pojawić się na publiczny adres IP zapory, ale powróci do zapory przy użyciu prywatnego adresu IP (przy użyciu trasy domyślnej).
Aby uniknąć tego problemu, należy utworzyć dodatkowej trasy hosta za publiczny adres IP zapory. Pakiety, przechodząc na publiczny adres IP zapory są kierowane za pośrednictwem Internetu. Umożliwia to uniknięcie przełączania trasy domyślnej na prywatny adres IP zapory.

![Routing asymetryczny](media/integrate-lb/Firewall-LB-asymmetric.png)

Na przykład następujące trasy są zaporę na publiczny adres IP 13.86.122.41 i prywatnego adresu IP 10.3.1.4.

![Tabela tras](media/integrate-lb/route-table.png)

## <a name="internal-load-balancer"></a>Wewnętrzny moduł równoważenia obciążenia

Przy użyciu wewnętrznego modułu równoważenia obciążenia moduł równoważenia obciążenia jest wdrażane za pomocą prywatnego adresu IP frontonu.

Nie ma żadnych problem z routingiem asymetrycznym w tym scenariuszu. Pakiety przychodzące przyjeździe do biura publiczny adres IP zapory, Pobierz przetłumaczyć prywatny adres IP modułu równoważenia obciążenia i powraca do zapory przy użyciu tej samej ścieżki zwrotu prywatny adres IP.

Tak można wdrożyć ten scenariusz podobny do scenariusza modułu równoważenia obciążenia publiczny, ale bez konieczności stosowania zapory publiczny adres IP hosta trasy.

## <a name="additional-security"></a>Dodatkowe zabezpieczenia

Aby dodatkowo zwiększyć bezpieczeństwo scenariusza ze zrównoważonym obciążeniem, można użyć grup zabezpieczeń sieci (NSG).

Na przykład można utworzyć sieciowej grupy zabezpieczeń w podsieci wewnętrznej bazy danych, gdzie znajdują się maszyny wirtualne ze zrównoważonym obciążeniem. Zezwalaj na ruch przychodzący pochodzące z adresu IP adres/port zapory.

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [grup zabezpieczeń](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [wdrażanie i konfigurowanie zapory usługi Azure](tutorial-firewall-deploy-portal.md).