---
title: Integracja usługi Azure Firewall z usługą Azure Load Balancer w warstwie Standardowa
description: Zaporę platformy Azure można zintegrować z siecią wirtualną przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa (publicznej lub wewnętrznej).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 91f34d06532b2d7f56d293df40939212a4f3d68c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167073"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integracja usługi Azure Firewall z usługą Azure Load Balancer w warstwie Standardowa

Zaporę platformy Azure można zintegrować z siecią wirtualną przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa (publicznej lub wewnętrznej). 

Preferowanym projektem jest zintegrowanie wewnętrznego modułu równoważenia obciążenia z zaporą platformy Azure, ponieważ jest to znacznie prostsze projektowanie. Możesz użyć publicznego modułu równoważenia obciążenia, jeśli masz już wdrożenie i chcesz zachować jego miejsce. Należy jednak pamiętać o problemie związanym z routingiem, który może spowodować przerwanie działania z scenariuszem publicznego modułu równoważenia obciążenia.

Aby uzyskać więcej informacji na temat Azure Load Balancer, zobacz [co to jest Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Publiczny moduł równoważenia obciążenia

W przypadku publicznego modułu równoważenia obciążenia moduł równoważenia obciążenia jest wdrażany przy użyciu publicznego adresu IP frontonu.

### <a name="asymmetric-routing"></a>Routing asymetryczny

Routing asymetryczny polega na tym, że pakiet przyjmuje jedną ścieżkę do miejsca docelowego i pobiera kolejną ścieżkę podczas powrotu do źródła. Ten problem występuje, gdy podsieć ma domyślną trasę do prywatnego adresu IP zapory i korzysta z publicznego modułu równoważenia obciążenia. W takim przypadku ruch przychodzącego modułu równoważenia obciążenia jest odbierany za pośrednictwem jego publicznego adresu IP, ale ścieżka zwrotna przechodzi przez prywatny adres IP zapory. Ponieważ Zapora jest stanowa, opuszcza pakiet zwracany, ponieważ Zapora nie wie o takiej ustanowionej sesji.

### <a name="fix-the-routing-issue"></a>Rozwiąż problem z routingiem

Po wdrożeniu zapory platformy Azure w podsieci, jeden krok to utworzenie trasy domyślnej dla podsieci kieruje pakiety przez prywatny adres IP zapory znajdujący się w AzureFirewallSubnet. Aby uzyskać więcej informacji, zobacz [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure Portal](tutorial-firewall-deploy-portal.md#create-a-default-route).

Gdy wprowadzisz zaporę do scenariusza modułu równoważenia obciążenia, chcesz, aby Twój ruch internetowy był używany przez publiczny adres IP zapory. Z tego miejsca Zapora stosuje reguły zapory i translator adresów sieciowych pakietów na publiczny adres IP modułu równoważenia obciążenia. Jest to miejsce, w którym występuje problem. Pakiety docierają do publicznego adresu IP zapory, ale Wróć do zapory za pomocą prywatnego adresu IP (przy użyciu trasy domyślnej).
Aby uniknąć tego problemu, należy utworzyć dodatkową trasę hosta dla publicznego adresu IP zapory. Pakiety przechodzące do publicznego adresu IP zapory są kierowane przez Internet. Pozwala to uniknąć przełączenia trasy domyślnej do prywatnego adresu IP zapory.

![Routing asymetryczny](media/integrate-lb/Firewall-LB-asymmetric.png)

Na przykład następujące trasy dotyczą zapory pod publicznym adresem IP 13.86.122.41 i prywatnym adresem IP 10.3.1.4.

![Tabela tras](media/integrate-lb/route-table.png)

## <a name="internal-load-balancer"></a>Wewnętrzny moduł równoważenia obciążenia

W przypadku wewnętrznego modułu równoważenia obciążenia moduł równoważenia obciążenia jest wdrażany przy użyciu prywatnego adresu IP frontonu.

W tym scenariuszu nie ma żadnego asymetrycznego problemu z routingiem. Pakiety przychodzące docierają do publicznego adresu IP zapory, są tłumaczone na prywatny adres IP usługi równoważenia obciążenia, a następnie wraca do prywatnego adresu IP zapory przy użyciu tej samej ścieżki zwracanej.

Tak więc można wdrożyć ten scenariusz podobny do scenariusza publicznego modułu równoważenia obciążenia, ale bez potrzeby trasy hosta publicznego adresu IP zapory.

## <a name="additional-security"></a>Dodatkowe zabezpieczenia

Aby dodatkowo zwiększyć bezpieczeństwo scenariusza z równoważeniem obciążenia, możesz użyć sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń).

Można na przykład utworzyć sieciowej grupy zabezpieczeń w podsieci zaplecza, w której znajdują się maszyny wirtualne o zrównoważonym obciążeniu. Zezwalaj na ruch przychodzący pochodzący z adresu IP/portu zapory.

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [grupy zabezpieczeń](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md).