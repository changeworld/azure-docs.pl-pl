---
title: Integracja usługi Azure Firewall z usługą Azure Load Balancer w warstwie Standardowa
description: Zaporę platformy Azure można zintegrować z siecią wirtualną za pomocą standardowego modułu równoważenia obciążenia platformy Azure (publicznego lub wewnętrznego).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/28/2020
ms.author: victorh
ms.openlocfilehash: ab9a500d9535b55702b8baff15f8cc47e6ac2c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196725"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integracja usługi Azure Firewall z usługą Azure Load Balancer w warstwie Standardowa

Zaporę platformy Azure można zintegrować z siecią wirtualną za pomocą standardowego modułu równoważenia obciążenia platformy Azure (publicznego lub wewnętrznego). 

Preferowanym projektem jest zintegrowanie wewnętrznego modułu równoważenia obciążenia z zaporą platformy Azure, ponieważ jest to znacznie prostszy projekt. Można użyć publicznego modułu równoważenia obciążenia, jeśli już go wdrożono i chcesz zachować go w miejscu. Należy jednak pamiętać o asymetrycznym problemie routingu, który może przerwać funkcjonalność w scenariuszu publicznego modułu równoważenia obciążenia.

Aby uzyskać więcej informacji na temat modułu równoważenia obciążenia platformy Azure, zobacz [Co to jest moduł równoważenia obciążenia platformy Azure?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Publiczny moduł równoważenia obciążenia

W publicznej moduł równoważenia obciążenia moduł równoważenia obciążenia jest wdrażany z publicznym adresem IP frontona.

### <a name="asymmetric-routing"></a>Routing asymetryczny

Routing asymetryczny to miejsce, w którym pakiet przenosi jedną ścieżkę do miejsca docelowego i przyjmuje inną ścieżkę po powrocie do źródła. Ten problem występuje, gdy podsieć ma domyślną trasę przechodzącą do prywatnego adresu IP zapory i używasz publicznego modułu równoważenia obciążenia. W takim przypadku ruch przychodzącego modułu równoważenia obciążenia jest odbierany za pośrednictwem publicznego adresu IP, ale ścieżka powrotu przechodzi przez prywatny adres IP zapory. Ponieważ zapora jest stanowa, porzuca powracający pakiet, ponieważ zapora nie jest świadoma takiej ustalonej sesji.

### <a name="fix-the-routing-issue"></a>Rozwiązywanie problemu z routingiem

Podczas wdrażania zapory platformy Azure w podsieci jednym z kroków jest utworzenie domyślnej trasy dla podsieci kierującej pakiety za pośrednictwem prywatnego adresu IP zapory znajdującego się w sieci AzureFirewallSubnet. Aby uzyskać więcej informacji, zobacz [Samouczek: Wdrażanie i konfigurowanie Zapory platformy Azure przy użyciu witryny Azure Portal](tutorial-firewall-deploy-portal.md#create-a-default-route).

Po wprowadzeniu zapory do scenariusza modułu równoważenia obciążenia, chcesz, aby ruch internetowy był wprowadzany za pośrednictwem publicznego adresu IP zapory. Stamtąd zapora stosuje swoje reguły zapory i nat pakiety do publicznego adresu IP modułu równoważenia obciążenia. W tym miejscu występuje problem. Pakiety docierają do publicznego adresu IP zapory, ale wracają do zapory za pośrednictwem prywatnego adresu IP (przy użyciu trasy domyślnej).
Aby uniknąć tego problemu, należy utworzyć dodatkową trasę hosta dla publicznego adresu IP zapory. Pakiety przechodzące do publicznego adresu IP zapory są kierowane przez Internet. Pozwala to uniknąć podejmowania domyślnej trasy do prywatnego adresu IP zapory.

![Routing asymetryczny](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Przykład tabeli tras

Na przykład następujące trasy są dla zapory pod publicznym adresem IP 20.185.97.136 i prywatny adres IP 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Tabela tras](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Przykład reguły TRANSLATORA

W poniższym przykładzie reguła NAT przekłada ruch RDP na zaporę w 20.185.97.136 na moduł równoważenia obciążenia o godzinie 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![Reguła TRANSLATORA](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Sondy kondycji

Pamiętaj, że musisz mieć usługę sieci web uruchomianą na hostach w puli modułu równoważenia obciążenia, jeśli używasz sond kondycji TCP do portu 80 lub sond HTTP/HTTPS.

## <a name="internal-load-balancer"></a>Wewnętrzny moduł równoważenia obciążenia

Dzięki wewnętrznej równoważnikowi obciążenia moduł równoważenia obciążenia jest wdrażany z prywatnym adresem IP wewnętrznej bazy danych.

W tym scenariuszu nie ma problemu z routingiem asymetrycznym. Przychodzące pakiety docierają do publicznego adresu IP zapory, są tłumaczone na prywatny adres IP modułu równoważenia obciążenia, a następnie wracają do prywatnego adresu IP zapory przy użyciu tej samej ścieżki powrotu.

Dzięki temu można wdrożyć ten scenariusz podobny do scenariusza modułu równoważenia obciążenia publicznego, ale bez konieczności publicznego adresu IP trasy hosta zapory.

## <a name="additional-security"></a>Dodatkowe zabezpieczenia

Aby jeszcze bardziej zwiększyć bezpieczeństwo scenariusza z równoważenia obciążenia, można użyć sieciowych grup zabezpieczeń (NSG).

Na przykład można utworzyć sieciowej sieciowej sieciowej w podsieci wewnętrznej bazy danych, w której znajdują się maszyny wirtualne z równoważenia obciążenia. Zezwalaj na ruch przychodzący pochodzący z adresu/portu IP zapory.

![Sieciowa grupa zabezpieczeń](media/integrate-lb/nsg-01.png)

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Grupy zabezpieczeń](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [wdrożyć i skonfigurować Zaporę platformy Azure](tutorial-firewall-deploy-portal.md).