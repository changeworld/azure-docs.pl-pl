---
title: Typy adresów IP na platformie Azure (klasyczne)
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej na temat publicznych i prywatnych adresów IP (klasycznych) na platformie Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: 207e728d25df9192f8a600b13d86330af8311700
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058924"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>Typy adresów IP i metody alokacji (klasyczne) na platformie Azure
Do zasobów platformy Azure można przypisać adresy IP w celu komunikowania się z innymi zasobami platformy Azure, siecią lokalną i Internetem. Istnieją dwa typy adresów IP, których można używać na platformie Azure: publiczne i prywatne.

Publiczne adresy IP są używane do komunikacji z Internetem, w tym usług publicznych platformy Azure.

Prywatne adresy IP są używane do komunikacji w ramach sieci wirtualnej platformy Azure, usługi w chmurze i sieci lokalnej w przypadku korzystania z bramy sieci VPN lub obwodu ExpressRoute w celu rozbudowania sieci na platformę Azure.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../resource-manager-deployment-model.md).  Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń używać Menedżer zasobów. Dowiedz się więcej o adresach IP w Menedżer zasobów, odczytując artykuł dotyczący [adresów IP](virtual-network-ip-addresses-overview-arm.md) .

## <a name="public-ip-addresses"></a>Publiczne adresy IP
Publiczne adresy IP umożliwiają zasobom platformy Azure komunikowanie się z Internetem i publicznymi usługami platformy Azure, takimi jak [usługa Azure cache for Redis](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL](../sql-database/sql-database-technical-overview.md)Database i [Azure Storage](../storage/common/storage-introduction.md).

Publiczny adres IP jest skojarzony z następującymi typami zasobów:

* Usługi w chmurze
* IaaS Virtual Machines (maszyny wirtualne)
* Wystąpienia roli PaaS
* Bramy sieci VPN
* Bramy aplikacji

### <a name="allocation-method"></a>Metoda alokacji
Jeśli publiczny adres IP musi zostać przypisany do zasobu platformy Azure, jest on *dynamicznie* przydzielany z puli dostępnych publicznych adresów IP w lokalizacji, w której jest tworzony zasób. Ten adres IP jest wydawany, gdy zasób zostanie zatrzymany. W przypadku usługi w chmurze dzieje się tak po zatrzymaniu wszystkich wystąpień roli, które można uniknąć przy użyciu statycznego (zastrzeżonego) adresu IP (zobacz [Cloud Services](#cloud-services)).

> [!NOTE]
> Lista zakresów adresów IP, z których publiczne adresy IP są przypisywane do zasobów platformy Azure, jest publikowana w [zakresach adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Rozpoznawanie nazw hostów DNS
Podczas tworzenia usługi w chmurze lub maszyny wirtualnej IaaS należy podać nazwę DNS usługi w chmurze, która jest unikatowa dla wszystkich zasobów na platformie Azure. Spowoduje to utworzenie mapowania na serwerach DNS zarządzanych przez platformę Azure dla *dnsname*. cloudapp.NET na publiczny adres IP zasobu. Na przykład podczas tworzenia usługi w chmurze z nazwą DNS usługi w chmurze **contoso**, w pełni kwalifikowana nazwa domeny (FQDN) **contoso.cloudapp.NET** zostanie rozwiązany jako publiczny adres IP (VIP) usługi w chmurze. Tej nazwy FQDN możesz użyć do utworzenia rekordu CNAME domeny niestandardowej wskazującego na publiczny adres IP na platformie Azure.

### <a name="cloud-services"></a>Usługi w chmurze
Usługa w chmurze zawsze ma publiczny adres IP, nazywany wirtualnym adresem IP (VIP). Można utworzyć punkty końcowe w usłudze w chmurze, aby skojarzyć różne porty w adresie VIP z portami wewnętrznymi na maszynach wirtualnych i wystąpieniach ról w ramach usługi w chmurze. 

Usługa w chmurze może zawierać wiele maszyn wirtualnych IaaS lub PaaS wystąpień ról, które są udostępniane za pośrednictwem tego samego adresu VIP usługi w chmurze. Możesz również przypisać [wiele adresów VIP do usługi w chmurze](../load-balancer/load-balancer-multivip.md), która umożliwia scenariusze z wieloma adresami IP, takie jak środowisko wielodostępne z witrynami sieci Web opartymi na protokole SSL.

Można upewnić się, że publiczny adres IP usługi w chmurze pozostaje taki sam, nawet jeśli wszystkie wystąpienia roli zostaną zatrzymane, przy użyciu *statycznego* publicznego adresu IP, zwanego [zastrzeżony adres IP](virtual-networks-reserved-public-ip.md). Można utworzyć statyczny (zarezerwowany) zasób IP w określonej lokalizacji i przypisać go do dowolnej usługi w chmurze w tej lokalizacji. Nie można określić rzeczywistego adresu IP dla zastrzeżonego adresu IP, który jest przypisywany z puli dostępnych adresów IP w tworzonej lokalizacji. Ten adres IP nie jest wydawany, dopóki nie zostanie jawnie usunięty.

Statyczne (zastrzeżone) publiczne adresy IP są często używane w scenariuszach, w których usługa w chmurze:

* Program wymaga, aby reguły zapory zostały skonfigurowane przez użytkowników końcowych.
* zależy od zewnętrznego rozpoznawania nazw DNS, a dynamiczny adres IP wymagał aktualizacji rekordów.
* korzysta z zewnętrznych usług sieci Web, które korzystają z modelu zabezpieczeń opartego na protokole IP.
* używa certyfikatów SSL połączonych z adresem IP.

> [!NOTE]
> Podczas tworzenia klasycznej maszyny wirtualnej *Usługa kontenera usługi w chmurze* jest tworzona przez platformę Azure, która ma wirtualny adres IP (VIP). Gdy tworzenie odbywa się za pośrednictwem portalu, domyślny *punkt końcowy* protokołu RDP lub SSH jest konfigurowany przez portal, dzięki czemu można nawiązać połączenie z maszyną wirtualną za pośrednictwem adresu VIP usługi w chmurze. Ten adres VIP usługi w chmurze może być zarezerwowany, co efektywnie zapewnia zarezerwowany adres IP do łączenia się z maszyną wirtualną. Dodatkowe porty można otworzyć, konfigurując więcej punktów końcowych.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS maszyny wirtualne i wystąpienia roli PaaS
Publiczny adres IP można przypisać bezpośrednio do IaaS [maszyny wirtualnej](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub wystąpienia roli PaaS w ramach usługi w chmurze. Jest to określane jako publiczny adres IP na poziomie wystąpienia ([ILPIP](virtual-networks-instance-level-public-ip.md)). Ten publiczny adres IP może być tylko dynamiczny.

> [!NOTE]
> Różni się to od adresu VIP usługi w chmurze, który jest kontenerem dla maszyn wirtualnych IaaS lub wystąpień roli PaaS, ponieważ usługa w chmurze może zawierać wiele maszyn wirtualnych IaaS lub PaaS wystąpienia roli, które są udostępniane za pośrednictwem tego samego adresu VIP usługi w chmurze.
> 
> 

### <a name="vpn-gateways"></a>Bramy sieci VPN
Za pomocą [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) można połączyć sieć wirtualną platformy Azure z innymi sieciami Azure sieci wirtualnych lub lokalnymi. Do bramy sieci VPN jest przypisywany *dynamicznie*publiczny adres IP, który umożliwia komunikację z siecią zdalną.

### <a name="application-gateways"></a>Bramy aplikacji
Usługa Azure [Application Gateway](../application-gateway/application-gateway-introduction.md) może służyć do równoważenia obciążenia warstwy 7 w celu kierowania ruchu sieciowego na podstawie protokołu HTTP. Brama aplikacji ma *dynamicznie*przypisany publiczny adres IP, który służy jako adres VIP ze zrównoważonym obciążeniem.

### <a name="at-a-glance"></a>Błyskawicznie
W poniższej tabeli przedstawiono każdy typ zasobu z możliwymi metodami alokacji (dynamiczną/statyczną) i możliwość przypisania wielu publicznych adresów IP.

| Resource | Dynamiczne | Static | Wiele adresów IP |
| --- | --- | --- | --- |
| Usługa w chmurze |Tak |Yes |Tak |
| IaaS maszyny wirtualnej lub wystąpienia roli PaaS |Tak |Nie |Nie |
| Brama sieci VPN |Tak |Nie |Nie |
| Brama aplikacji |Tak |Nie |Nie |

## <a name="private-ip-addresses"></a>Prywatne adresy IP
Prywatne adresy IP umożliwiają zasobom platformy Azure komunikowanie się z innymi zasobami w usłudze w chmurze lub [sieci wirtualnej](virtual-networks-overview.md)(VNET) lub w sieci lokalnej (za pośrednictwem bramy sieci VPN lub obwodu usługi ExpressRoute) bez użycia adresu IP dostępnego z Internetu.

W klasycznym modelu wdrażania platformy Azure prywatny adres IP można przypisać do następujących zasobów platformy Azure:

* IaaS maszyny wirtualne i wystąpienia roli PaaS
* Wewnętrzny moduł równoważenia obciążenia
* Brama aplikacji

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS maszyny wirtualne i wystąpienia roli PaaS
Maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania są zawsze umieszczane w usłudze w chmurze, podobnie jak wystąpienia roli PaaS. Zachowanie prywatnych adresów IP jest podobne do tych zasobów.

Należy pamiętać, że usługa w chmurze może zostać wdrożona na dwa sposoby:

* Jako *autonomiczną* usługę w chmurze, w której nie znajduje się w sieci wirtualnej.
* Jako część sieci wirtualnej.

#### <a name="allocation-method"></a>Metoda alokacji
W przypadku autonomicznej usługi w chmurze zasoby otrzymują prywatny adres IP przydzielony *dynamicznie* z zakresu prywatnego adresu IP centrum danych platformy Azure. Może być używany tylko do komunikacji z innymi maszynami wirtualnymi w ramach tej samej usługi w chmurze. Ten adres IP może się zmieniać, gdy zasób jest zatrzymany i uruchomiony.

W przypadku usługi w chmurze wdrożonej w ramach sieci wirtualnej zasoby otrzymują prywatne adresy IP przydzieloną z zakresu adresów skojarzonych podsieci (zgodnie z konfiguracją sieci). Te prywatne adresy IP mogą być używane do komunikacji między wszystkimi maszynami wirtualnymi w sieci wirtualnej.

Ponadto w przypadku usług w chmurze w sieci wirtualnej prywatny adres IP jest przydzielany *dynamicznie* (domyślnie przy użyciu protokołu DHCP). Może ona ulec zmianie, gdy zasób zostanie zatrzymany i uruchomiony. Aby zapewnić, że adres IP pozostaje taki sam, należy ustawić metodę alokacji na statycznąi podać prawidłowy adres IP w ramach odpowiedniego zakresu adresów.

Statyczne prywatne adresy IP są powszechnie używane do:

* Maszyn wirtualnych, które działają jako kontrolery domeny lub serwery DNS.
* Maszyny wirtualne, które wymagają reguł zapory przy użyciu adresów IP.
* Maszyny wirtualne z usługami dostępnymi przez inne aplikacje za pośrednictwem adresu IP.

#### <a name="internal-dns-hostname-resolution"></a>Wewnętrzne rozpoznawanie nazwy hosta DNS
Wszystkie maszyny wirtualne platformy Azure i wystąpienia roli PaaS są domyślnie konfigurowane przy użyciu [serwerów DNS zarządzanych przez platformę Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) , chyba że jawnie skonfigurujesz niestandardowe serwery DNS. Te serwery DNS zapewniają rozpoznawanie nazw wewnętrznych dla maszyn wirtualnych i wystąpień ról znajdujących się w ramach tej samej sieci wirtualnej lub usługi w chmurze.

Podczas tworzenia maszyny wirtualnej mapowanie nazwy hosta na jego prywatny adres IP jest dodawane do serwerów DNS zarządzanych przez platformę Azure. W przypadku maszyn wirtualnych z obsługą wiele kart nazwa hosta jest mapowana na prywatny adres IP podstawowej karty sieciowej. Jednak te informacje mapowania są ograniczone do zasobów w ramach tej samej usługi w chmurze lub sieci wirtualnej.

W przypadku autonomicznej usługi w chmurze będzie można rozpoznać nazwy hostów wszystkich maszyn wirtualnych/wystąpień ról tylko w ramach tej samej usługi w chmurze. W przypadku usługi w chmurze w sieci wirtualnej będzie można rozpoznać nazwy hostów wszystkich maszyn wirtualnych/wystąpień ról w ramach połączenia sieci wirtualnej.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Wewnętrzne moduły równoważenia obciążenia (ILB) i bramy aplikacji
Prywatny adres IP możesz przypisać do konfiguracji **frontonu** [wewnętrznego modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) lub [bramy aplikacji platformy Azure](../application-gateway/application-gateway-introduction.md). Ten prywatny adres IP służy jako wewnętrzny punkt końcowy dostępny tylko dla zasobów w ramach jego sieci wirtualnej i sieci zdalnych podłączonych do sieci wirtualnej. Do konfiguracji frontonu możesz przypisać dynamiczny albo statyczny prywatny adres IP. Możesz również przypisać wiele prywatnych adresów IP, aby umożliwić scenariusze z wieloma adresami VIP.

### <a name="at-a-glance"></a>Błyskawicznie
W poniższej tabeli przedstawiono każdy typ zasobu z możliwymi metodami alokacji (dynamiczną/statyczną) i możliwość przypisania wielu prywatnych adresów IP.

| Resource | Dynamiczne | Static | Wiele adresów IP |
| --- | --- | --- | --- |
| Maszyna wirtualna (w *autonomicznej* usłudze w chmurze lub sieci wirtualnej) |Tak |Yes |Tak |
| Wystąpienie roli PaaS (w *autonomicznej* usłudze w chmurze lub sieci wirtualnej) |Tak |Nie |Nie |
| Fronton wewnętrznego modułu równoważenia obciążenia |Tak |Yes |Tak |
| Fronton bramy aplikacji |Tak |Yes |Tak |

## <a name="limits"></a>Limity
W poniższej tabeli przedstawiono limity nałożone na adresowanie IP na platformie Azure na subskrypcję. [Kontaktując się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), możesz zwiększyć domyślne limity do maksimum w zależności od potrzeb biznesowych.

|  | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Publiczne adresy IP (dynamiczne) |5 |Skontaktuj się z pomocą techniczną |
| Zastrzeżone publiczne adresy IP |20 |Skontaktuj się z pomocą techniczną |
| Publiczne adresy VIP na wdrożenie (usługa w chmurze) |5 |Skontaktuj się z pomocą techniczną |
| Prywatny adres VIP (ILB) na wdrożenie (usługa w chmurze) |1 |1 |

Należy zapoznać się z pełnym zestawem [limitów sieci](../azure-subscription-service-limits.md#networking-limits) na platformie Azure.

## <a name="pricing"></a>Cennik
W większości przypadków publiczne adresy IP są bezpłatne. Istnieje nominalna opłata za używanie dodatkowych i/lub statycznych publicznych adresów IP. Upewnij się, że znasz [strukturę cenową publicznych adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Różnice między wdrożeniami Menedżer zasobów i klasycznymi
Poniżej znajduje się porównanie funkcji adresowania IP w Menedżer zasobów i klasycznym modelu wdrażania.

|  | Resource | Klasyczny | Resource Manager |
| --- | --- | --- | --- |
| **Publiczny adres IP** |***VM*** |Określany jako ILPIP (tylko dynamiczny) |Określany jako publiczny adres IP (dynamiczny lub statyczny) |
|  ||Przypisane do maszyny wirtualnej IaaS lub wystąpienia roli PaaS |Skojarzone z kartą sieciową maszyny wirtualnej |
|  |***Moduł równoważenia obciążenia połączony z Internetem*** |Określany jako adres VIP (dynamiczny) lub Zastrzeżony adres IP (statyczny) |Określany jako publiczny adres IP (dynamiczny lub statyczny) |
|  ||Przypisane do usługi w chmurze |Skojarzone z konfiguracją frontonu modułu równoważenia obciążenia |
|  | | | |
| **Prywatny adres IP** |***VM*** |Określany jako DIP |Określany jako prywatny adres IP |
|  ||Przypisane do maszyny wirtualnej IaaS lub wystąpienia roli PaaS |Przypisane do karty sieciowej maszyny wirtualnej |
|  |***Wewnętrzny moduł równoważenia obciążenia (ILB)*** |Przypisane do ILB (dynamiczny lub statyczny) |Przypisane do konfiguracji frontonu ILB (dynamiczny lub statyczny) |

## <a name="next-steps"></a>Następne kroki
* [Wdróż maszynę wirtualną ze statycznym prywatnym adresem IP](virtual-networks-static-private-ip-classic-pportal.md) przy użyciu Azure Portal.

