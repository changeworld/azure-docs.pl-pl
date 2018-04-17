---
title: Typy adresów IP na platformie Azure (klasyczne) | Dokumentacja firmy Microsoft
description: Więcej informacji na temat publiczne i prywatne adresy IP (klasyczne) na platformie Azure.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: 2f8664ab-2daf-43fa-bbeb-be9773efc978
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: f9e36abd44c2012d96a6ead8659197dcf66c6032
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>Typy adresów IP i metod alokacji (klasyczne) na platformie Azure
Do zasobów platformy Azure można przypisać adresy IP w celu komunikowania się z innymi zasobami platformy Azure, siecią lokalną i Internetem. Istnieją dwa typy adresów IP można używać w Azure: prywatnych i publicznych.

Publiczne adresy IP są używane do komunikacji z Internetem, łącznie z usługami Azure publicznych.

Prywatne adresy IP są używane do komunikacji w ramach sieci wirtualnej platformy Azure (VNet), usługa w chmurze i sieci lokalnej, korzystając z bramy sieci VPN lub obwód ExpressRoute rozszerzenie sieci Azure.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../resource-manager-deployment-model.md).  Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby większości nowych wdrożeń korzystać Menedżera zasobów. Więcej informacji na temat adresów IP w Menedżerze zasobów odczytując [adresów IP](virtual-network-ip-addresses-overview-arm.md) artykułu.

## <a name="public-ip-addresses"></a>Publiczne adresy IP
Publiczne adresy IP umożliwiają zasobom platformy Azure komunikację z Internetem i publicznymi usługami Azure, takimi jak [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) [SQL Database](../sql-database/sql-database-technical-overview.md) i [Azure Storage](../storage/common/storage-introduction.md).

Publiczny adres IP jest skojarzony z następujących zasobów:

* Usługi w chmurze
* Maszyny wirtualne IaaS (VM)
* Wystąpienia roli PaaS
* Bramy sieci VPN
* Bramy aplikacji

### <a name="allocation-method"></a>Metoda alokacji
Gdy publiczny adres IP musi być przypisane do zasobów platformy Azure, jest *dynamicznie* przydzielone z puli dostępne w lokalizacji zasób jest tworzony publiczny adres IP. Ten adres IP jest zwalniany, gdy zasób został zatrzymany. W przypadku usługi w chmurze tak się stanie, gdy zostały zatrzymane wszystkie wystąpienia roli, które można uniknąć za pomocą *statycznych* (zastrzeżony) adres IP (zobacz [usługi w chmurze](#Cloud-services)).

> [!NOTE]
> Lista zakresów adresów IP, z których publiczne adresy IP są przydzielane zasoby platformy Azure jest opublikowana w [zakresy IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Rozpoznawanie nazw hostów DNS
Podczas tworzenia usługi w chmurze lub maszyn wirtualnych IaaS, musisz podać nazwę DNS usługi chmury, która jest unikatowa dla wszystkich zasobów na platformie Azure. To tworzy mapowanie na serwerach zarządzanych Azure DNS dla *dnsname*. cloudapp.net publiczny adres IP zasobu. Na przykład po utworzeniu usługi w chmurze przy użyciu nazwy DNS usługi chmury z **contoso**, pełni kwalifikowaną nazwą domeny (FQDN) **contoso.cloudapp.net** rozwiąże do publicznego adresu IP (VIP) usługi w chmurze. Tej nazwy FQDN możesz użyć do utworzenia rekordu CNAME domeny niestandardowej wskazującego na publiczny adres IP na platformie Azure.

### <a name="cloud-services"></a>Usługi w chmurze
Usługi w chmurze ma zawsze publicznego adresu IP, określany jako wirtualnego adresu IP (VIP). Punkty końcowe można utworzyć w usłudze w chmurze można skojarzyć różne porty wirtualne adresy IP do wewnętrznych portów w maszynach wirtualnych i wystąpień roli w ramach usługi w chmurze. 

Usługi w chmurze może zawierać wiele maszyn wirtualnych IaaS lub wystąpień roli PaaS, wszystkie dostępnego za pośrednictwem tego samego adresu VIP usługi chmury. Można także przypisać [wielu adresów VIP usługi w chmurze](../load-balancer/load-balancer-multivip.md), który umożliwia obsługę scenariuszy wielu wirtualnych adresów IP, takich jak środowiska z wieloma dzierżawami z witrynami sieci Web oparta na protokole SSL.

Można zapewnić publicznego adresu IP usługi w chmurze jest taka sama, nawet wtedy, gdy zostały zatrzymane wszystkie wystąpienia roli, za pomocą *statycznych* publicznego adresu IP, określany jako [zastrzeżonego adresu IP](virtual-networks-reserved-public-ip.md). Można utworzyć zasób statycznego adresu IP (zastrzeżony) w określonej lokalizacji i przypisz je do dowolnej usługi w chmurze w tej lokalizacji. Nie można określić rzeczywistego adresu IP dla zastrzeżonego adresu IP, jest ona przydzielone z puli w lokalizacji, w której jest tworzona dostępnych adresów IP. Ten adres IP nie zostaje zwolniony, dopóki nie zostaną jawnie usunięte.

Statyczny (zastrzeżony) publiczne adresy IP są często używane w scenariuszach, w przypadku, gdy usługa w chmurze:

* wymaga reguły zapory, należy skonfigurować przez użytkowników końcowych.
* zależy od zewnętrznego rozpoznawania nazw DNS i aktualizowania rekordów wymagają dynamicznego adresu IP.
* korzysta z usługi zewnętrzne sieci web, które używają adresu IP na podstawie modelu zabezpieczeń.
* używa certyfikatów SSL powiązany adres IP.

> [!NOTE]
> Podczas tworzenia klasycznych maszyn wirtualnych, kontener *usługi w chmurze* jest tworzony przez platformę Azure, który ma wirtualnego adresu IP (VIP). Jeśli tworzenie odbywa się za pośrednictwem portalu, domyślne połączenie RDP lub SSH *punktu końcowego* jest konfigurowane przez portal, więc możesz połączyć się z maszyną Wirtualną za pośrednictwem usługi chmury wirtualne adresy IP. Tej usługi chmury wirtualne adresy IP może być zarezerwowana, co umożliwia efektywne zastrzeżonego adresu IP, aby nawiązać połączenie z maszyną Wirtualną. Należy otworzyć porty dodatkowe, konfigurując więcej punktów końcowych.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>Maszyny wirtualne IaaS i wystąpienia roli PaaS
Publiczny adres IP można przypisać bezpośrednio do IaaS [wirtualna](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub wystąpienia roli PaaS w ramach usługi w chmurze. Jest to określane jako poziomie wystąpienia publicznego adresu IP ([ILPIP](virtual-networks-instance-level-public-ip.md)). Ten publiczny adres IP może być dynamiczny tylko.

> [!NOTE]
> Różni się to od adresów VIP usługi w chmurze, która jest kontenerem dla maszyn wirtualnych IaaS i PaaS wystąpienia roli, ponieważ usługa w chmurze może zawierać wiele maszyn wirtualnych IaaS lub wystąpień roli PaaS wszystkie dostępne za pośrednictwem tego samego adresu VIP usługi chmury.
> 
> 

### <a name="vpn-gateways"></a>Bramy sieci VPN
A [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) można połączyć sieć wirtualną platformy Azure z innymi sieciami sieci wirtualnych platformy Azure lub lokalnie. Brama VPN przypisano publiczny adres IP *dynamicznie*, co pozwala komunikacji w sieci zdalnej.

### <a name="application-gateways"></a>Bramy aplikacji
Azure [brama aplikacji w](../application-gateway/application-gateway-introduction.md) może służyć do Layer7 równoważenia obciążenia można kierować ruchem sieciowym na podstawie od protokołu HTTP. Brama aplikacji w przypisano publiczny adres IP *dynamicznie*, która służy jako VIP równoważeniem obciążenia.

### <a name="at-a-glance"></a>W skrócie
W poniższej tabeli przedstawiono każdy typ zasobu z metod możliwe alokacji (dynamiczny/statyczny) i możliwość przypisania wiele publicznych adresów IP.

| Zasób | Dynamiczny | Statyczny | Wiele adresów IP |
| --- | --- | --- | --- |
| Usługa w chmurze |Yes |Yes |Yes |
| Wystąpienia roli maszyny Wirtualnej IaaS i PaaS |Yes |Nie |Nie |
| Brama sieci VPN |Yes |Nie |Nie |
| Brama aplikacji |Yes |Nie |Nie |

## <a name="private-ip-addresses"></a>Prywatne adresy IP
Prywatne adresy IP umożliwiają zasobom platformy Azure do komunikowania się z innymi zasobami w usłudze w chmurze lub [sieci wirtualnej](virtual-networks-overview.md)(VNet), lub do sieci lokalnej (za pośrednictwem bramy sieci VPN lub obwodu ExpressRoute), bez użycia Internet dostępny adres IP.

W modelu wdrażania klasycznego Azure prywatnego adresu IP można przypisać do następujących zasobów platformy Azure:

* Maszyny wirtualne IaaS i wystąpienia roli PaaS
* Wewnętrzny moduł równoważenia obciążenia
* Brama aplikacji

### <a name="iaas-vms-and-paas-role-instances"></a>Maszyny wirtualne IaaS i wystąpienia roli PaaS
Maszyny wirtualnej (VM) utworzone za pomocą klasycznym modelu wdrażania są zawsze umieszczane w usłudze chmury, podobnie jak wystąpień roli PaaS. Zachowanie prywatnych adresów IP w związku z tym są podobne do tych zasobów.

Należy pamiętać, że usługa w chmurze można wdrożyć na dwa sposoby:

* Jako *autonomiczny* usługi, której nie znajduje się w sieci wirtualnej w chmurze.
* W ramach sieci wirtualnej.

#### <a name="allocation-method"></a>Metoda alokacji
W przypadku liczby *autonomiczny* cloud service, get zasoby przydzielone prywatnego adresu IP *dynamicznie* z centrum danych Azure, prywatnego adresu IP z zakresu adresów. Może służyć tylko do komunikacji z innych maszyn wirtualnych w ramach tej samej usługi w chmurze. Ten adres IP można zmienić, gdy zasób jest zatrzymana i uruchomiona.

W przypadku usługi w chmurze wdrożyć w ramach sieci wirtualnej zasobów Pobierz prywatne adresy IP przydzielone z zakresu adresów skojarzony adresy podsieci używane (jak określono w jego konfiguracji sieciowej). Ta prywatnych adresów IP może służyć do komunikacji między wszystkich maszyn wirtualnych w sieci wirtualnej.

Ponadto w przypadku usług w chmurze w ramach sieci wirtualnej, prywatnego adresu IP jest przydzielany *dynamicznie* (przy użyciu protokołu DHCP) domyślnie. Można go zmienić, gdy zasobu jest zatrzymana i uruchomiona. Aby zapewnić adres IP jest taka sama, należy ustawić metodę alokacji *statycznych*i podaj prawidłowy adres IP w ramach odpowiedniego zakresu adresów.

Statyczne prywatne adresy IP są powszechnie używane do:

* Maszyn wirtualnych, które działają jako kontrolery domeny lub serwery DNS.
* Maszyny wirtualne wymagające reguł zapory przy użyciu adresów IP.
* Maszyny wirtualne uruchomione usługi używane przez inne aplikacje za pomocą adresu IP.

#### <a name="internal-dns-hostname-resolution"></a>Wewnętrzny rozpoznawania nazwy hosta DNS
Wszystkie maszyny wirtualne platformy Azure i wystąpienia roli PaaS są skonfigurowane przy użyciu [serwery zarządzane Azure DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) domyślnie, chyba że jawnie skonfigurować niestandardowych serwerów DNS. Te serwery DNS zapewniają rozpoznawania nazw wewnętrznych dla maszyn wirtualnych i wystąpień ról, które znajdują się w tej samej sieci wirtualnej lub w chmurze usługi.

Podczas tworzenia maszyny wirtualnej mapowanie nazwy hosta na jego prywatny adres IP jest dodawane do serwerów DNS zarządzanych przez platformę Azure. W przypadku maszyny Wirtualnej z wieloma kartami Sieciowymi nazwa hosta jest mapowany na prywatny adres IP podstawowego karty sieciowej. Jednak informacje o mapowaniu jest ograniczone do zasobów w ramach tej samej usługi w chmurze lub sieci wirtualnej.

W przypadku liczby *autonomiczny* cloud service, będzie można rozwiązać nazwy hostów wszystkich wystąpień maszyn wirtualnych/roli w ramach tej samej usługi w chmurze tylko. W przypadku usługi w chmurze w ramach sieci wirtualnej można rozwiązać nazwy hostów wszystkich wystąpień maszyn wirtualnych/roli w ramach sieci wirtualnej.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Wewnętrzne moduły równoważenia obciążenia (ILB) i bramy aplikacji
Prywatny adres IP możesz przypisać do konfiguracji **frontonu** [wewnętrznego modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) lub [bramy aplikacji platformy Azure](../application-gateway/application-gateway-introduction.md). Ten prywatny adres IP służy jako wewnętrzny punkt końcowy dostępny tylko dla zasobów w ramach jego sieci wirtualnej i sieci zdalnych podłączonych do sieci wirtualnej. Do konfiguracji frontonu możesz przypisać dynamiczny albo statyczny prywatny adres IP. Można również przypisać wiele prywatnych adresów IP na potrzeby scenariuszy z wielu wirtualnych adresów IP.

### <a name="at-a-glance"></a>W skrócie
W poniższej tabeli przedstawiono każdy typ zasobu z metod możliwe alokacji (dynamiczny/statyczny) i możliwość przypisania wiele prywatnych adresów IP.

| Zasób | Dynamiczny | Statyczny | Wiele adresów IP |
| --- | --- | --- | --- |
| Maszyna wirtualna (w *autonomiczny* usługi lub sieci wirtualnej w chmurze) |Yes |Yes |Yes |
| Wystąpienia roli PaaS (w *autonomiczny* usługi lub sieci wirtualnej w chmurze) |Yes |Nie |Nie |
| Frontonu modułu równoważenia obciążenia wewnętrznego |Yes |Yes |Yes |
| Frontonu bramy aplikacji |Yes |Yes |Yes |

## <a name="limits"></a>Limity
W poniższej tabeli przedstawiono ograniczenia narzucone na IP adresowania na platformie Azure dla subskrypcji. [Kontaktując się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), możesz zwiększyć domyślne limity do maksimum w zależności od potrzeb biznesowych.

|  | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Publiczne adresy IP (dynamiczne) |5 |kontakt z pomocą techniczną |
| Zastrzeżone publiczne adresy IP |20 |kontakt z pomocą techniczną |
| Publiczny adres VIP dla wdrożenia (usługi w chmurze) |5 |kontakt z pomocą techniczną |
| Prywatny adres VIP (ILB) dla wdrożenia (usługi w chmurze) |1 |1 |

Upewnij się, że odczytu pełny zestaw [ogranicza sieciach](../azure-subscription-service-limits.md#networking-limits) na platformie Azure.

## <a name="pricing"></a>Cennik
W większości przypadków mogą publicznych adresów IP. Brak opłat nominalnego umożliwia dodatkowe i/lub statyczne publiczne adresy IP. Upewnij się, że rozumiesz [ceny struktury publiczne adresy IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Różnice między Resource Manager i w przypadku wdrożeń klasycznych
Poniżej przedstawiono porównanie funkcji adresów IP w programie Resource Manager i klasycznym modelu wdrażania.

|  | Zasób | Wdrożenie klasyczne | Resource Manager |
| --- | --- | --- | --- |
| **Publiczny adres IP** |***VM*** |Określone jako ILPIP (tylko dynamiczny) |Określone jako publiczny adres IP (dynamiczna lub statyczna) |
|  ||Przypisane do maszyn wirtualnych IaaS lub wystąpień roli PaaS |Skojarzone do karty Sieciowej maszyny Wirtualnej | |
|  |***Internet równoważenia obciążenia*** |Nazywane VIP (dynamiczny) lub zastrzeżonego adresu IP (statyczny) |Określone jako publiczny adres IP (dynamiczna lub statyczna) | |
|  ||Przypisane do usługi w chmurze |Skojarzonego z konfiguracji frontonu modułu równoważenia obciążenia | |
|  | | | |
| **Prywatny adres IP** |***VM*** |Określone jako DIP |Określone jako prywatnego adresu IP |
|  ||Przypisane do maszyn wirtualnych IaaS lub wystąpień roli PaaS |Przypisane do karty Sieciowej maszyny Wirtualnej | |
|  |***Wewnętrzny moduł równoważenia obciążenia (ILB)*** |Przypisane do ILB (dynamiczna lub statyczna) |Przypisane do konfiguracji frontonu ILB (dynamiczna lub statyczna) | |

## <a name="next-steps"></a>Kolejne kroki
* [Wdróż maszynę Wirtualną za pomocą statycznego prywatnego adresu IP](virtual-networks-static-private-ip-classic-pportal.md) przy użyciu portalu Azure.

