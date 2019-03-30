---
title: Typy adresów IP na platformie Azure (wersja klasyczna)
titlesuffix: Azure Virtual Network
description: Więcej informacji na temat publicznych i prywatnych adresów IP (klasyczny) na platformie Azure.
services: virtual-network
documentationcenter: na
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: 9e7a5772dd1e10abf43eddf0548833d625ecfb24
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652171"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>Typy adresów IP i metody alokacji (model klasyczny) na platformie Azure
Do zasobów platformy Azure można przypisać adresy IP w celu komunikowania się z innymi zasobami platformy Azure, siecią lokalną i Internetem. Istnieją dwa typy adresów IP, można użyć na platformie Azure: prywatnych i publicznych.

Publiczne adresy IP są używane do komunikacji z Internetem łącznie z usługami platformy Azure dostępnego publicznie.

Prywatne adresy IP są używane do komunikacji w obrębie sieci wirtualnej platformy Azure (VNet), usługi w chmurze i sieci lokalnej, gdy używasz bramy sieci VPN lub obwodu usługi ExpressRoute w celu rozszerzenia swojej sieci na platformie Azure.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../resource-manager-deployment-model.md).  Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca się, że większości nowych wdrożeń korzystać usługi Resource Manager. Dowiedz się więcej o adresach IP w usłudze Resource Manager, zapoznając się [adresów IP](virtual-network-ip-addresses-overview-arm.md) artykułu.

## <a name="public-ip-addresses"></a>Publiczne adresy IP
Publiczne adresy IP umożliwiają zasobom platformy Azure do komunikowania się z Internetem a platformą Azure publicznymi usługami, takie jak [pamięci podręcznej Redis Azure](https://azure.microsoft.com/services/cache/), [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [baz danych SQL](../sql-database/sql-database-technical-overview.md), i [usługi Azure storage](../storage/common/storage-introduction.md).

Publiczny adres IP jest skojarzona z następujących zasobów:

* Usługi w chmurze
* Maszyny wirtualne IaaS (VM)
* Wystąpienia roli PaaS
* Bramy sieci VPN
* Bramy aplikacji

### <a name="allocation-method"></a>Metoda alokacji
Publiczny adres IP musi być przypisany do zasobu platformy Azure, jest *dynamicznie* przydzielany z puli dostępnych w ramach lokalizacji zasób jest tworzony publiczny adres IP. Ten adres IP jest zwalniany, gdy zasób jest zatrzymana. Z usługą w chmurze tak się stanie po zatrzymaniu wszystkich wystąpień roli, które można uniknąć za pomocą *statyczne* (zastrzeżony) adres IP (zobacz [usług w chmurze](#cloud-services)).

> [!NOTE]
> Lista zakresów adresów IP, z której publiczne adresy IP są przydzielane do zasobów platformy Azure, jest opublikowana w [zakresów adresów IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Rozpoznawanie nazw hostów DNS
Podczas tworzenia usługi w chmurze lub Maszynie wirtualnej IaaS, musisz podać nazwę DNS usługi chmury, która jest unikatowa dla wszystkich zasobów na platformie Azure. To tworzy mapowanie na serwerach DNS zarządzanych platformy Azure dla *dnsname*. cloudapp.net publiczny adres IP zasobu. Na przykład po utworzeniu usługi w chmurze za pomocą nazwa DNS usługi w chmurze z **contoso**, w pełni kwalifikowana nazwa domeny (FQDN) **contoso.cloudapp.net** rozwiąże publiczny adres IP (VIP) chmury Usługa. Tej nazwy FQDN możesz użyć do utworzenia rekordu CNAME domeny niestandardowej wskazującego na publiczny adres IP na platformie Azure.

### <a name="cloud-services"></a>Usługi w chmurze
Usługa w chmurze ma zawsze na publiczny adres IP, które są określone jako wirtualny adres IP (VIP). Możesz utworzyć punkty końcowe w usłudze w chmurze do skojarzenia z różnymi portami w adresów VIP do wewnętrznych portów na maszynach wirtualnych i wystąpień roli w ramach usługi w chmurze. 

Usługa w chmurze może zawierać wiele maszyn wirtualnych IaaS lub wystąpień roli PaaS, wszystkie narażonych za pomocą tego samego adresu VIP usługi chmury. Można także przypisać [wielu adresów VIP do usługi w chmurze](../load-balancer/load-balancer-multivip.md), co pozwala wielu VIP scenariuszy, takich jak Środowisko wielodostępne z witrynami sieci Web z opartymi na protokole SSL.

Upewnij się publiczny adres IP usługi w chmurze pozostają takie same, nawet wtedy, gdy zostaną zatrzymane wszystkie wystąpienia roli, przy użyciu *statyczne* publiczny adres IP, nazywany [zastrzeżonego adresu IP](virtual-networks-reserved-public-ip.md). Można utworzyć zasób statyczny (zastrzeżony) adresu IP w określonej lokalizacji i przypisać ją do dowolnej usługi w chmurze w tej lokalizacji. Nie można określić rzeczywistego adresu IP dla zastrzeżonego adresu IP, jest on przydzielany z puli adresów IP dostępnych w lokalizacji, w którym zostały utworzone. Ten adres IP jest zwalniany dopiero jawnie usunąć.

Statyczny (zastrzeżony) publiczne adresy IP najczęściej są używane w scenariuszach, gdzie usługa w chmurze:

* wymaga reguł zapory, które można skonfigurować przez użytkowników końcowych.
* zależy od zewnętrznego rozpoznawania nazw DNS i dynamicznego adresu IP wymaga aktualizacji rekordów.
* wykorzystuje zewnętrznych usług internetowych korzystających z modelu zabezpieczeń opartych na adresie IP.
* korzysta z certyfikatów SSL połączonych z adresem IP.

> [!NOTE]
> Po utworzeniu klasyczna maszyna wirtualna kontener *usługi w chmurze* jest tworzony przez platformę Azure, która ma wirtualnego adresu IP (VIP). Jeśli tworzenie odbywa się za pośrednictwem portalu, domyślna RDP lub SSH *punktu końcowego* jest skonfigurowany przez portal, aby połączyć z maszyną wirtualną za pośrednictwem adresu VIP usługi chmury. Można zastrzec adresu VIP usługi w tej chmurze, zapewniającą skutecznie zastrzeżonego adresu IP, aby nawiązać połączenie z maszyną Wirtualną. Możesz otworzyć dodatkowe porty, konfigurując dodatkowe punkty końcowe.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>Maszyny wirtualne IaaS i PaaS wystąpień roli
Publiczny adres IP można przypisać bezpośrednio do IaaS [maszyny Wirtualnej](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub wystąpienia roli PaaS w ramach usługi w chmurze. Jest to określane jako publiczny adres IP na poziomie wystąpienia ([ILPIP](virtual-networks-instance-level-public-ip.md)). Ten publiczny adres IP może być dynamiczny tylko.

> [!NOTE]
> To różni się od adresu VIP usługi w chmurze, który jest kontenerem dla maszyn wirtualnych IaaS lub PaaS wystąpień roli, ponieważ usługa w chmurze może zawierać wiele maszyn wirtualnych IaaS lub wystąpień roli PaaS, wszystkie dostępne za pośrednictwem tego samego adresu VIP usługi chmury.
> 
> 

### <a name="vpn-gateways"></a>Bramy sieci VPN
A [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) może służyć do łączenia z siecią wirtualną platformy Azure z innymi sieciami sieciami wirtualnymi platformy Azure lub lokalnie. Tworzenie bramy sieci VPN jest przypisany publiczny adres IP *dynamicznie*, która umożliwia komunikację z siecią zdalną.

### <a name="application-gateways"></a>Bramy aplikacji
Azure [bramy Application gateway](../application-gateway/application-gateway-introduction.md) może służyć do Layer7 równoważenia obciążenia można kierować ruchem sieciowym na podstawie od protokołu HTTP. Usługa Application gateway jest przypisany publiczny adres IP *dynamicznie*, który służy jako adres VIP ze zrównoważonym obciążeniem.

### <a name="at-a-glance"></a>W skrócie
W poniższej tabeli przedstawiono każdy typ zasobu z ewentualne metody alokacji (dynamiczne/statyczne), a także przypisać wiele publicznych adresów IP.

| Zasób | Dynamiczny | Statyczny | Wiele adresów IP |
| --- | --- | --- | --- |
| Usługa w chmurze |Yes |Yes |Yes |
| Wystąpienia roli maszyny Wirtualnej IaaS lub PaaS |Yes |Nie |Nie |
| Brama sieci VPN |Yes |Nie |Nie |
| Brama aplikacji |Yes |Nie |Nie |

## <a name="private-ip-addresses"></a>Prywatne adresy IP
Prywatne adresy IP umożliwiają zasobom platformy Azure komunikację z innymi zasobami w usłudze w chmurze lub [sieci wirtualnej](virtual-networks-overview.md)(VNet) lub między siecią lokalną (za pośrednictwem bramy sieci VPN lub obwodu usługi ExpressRoute), bez użycia Internet dostępny adres IP.

W modelu klasycznym wdrożeniu platformy Azure prywatny adres IP można przypisać do następujących zasobów platformy Azure:

* Maszyny wirtualne IaaS i PaaS wystąpień roli
* Wewnętrzny moduł równoważenia obciążenia
* Brama aplikacji

### <a name="iaas-vms-and-paas-role-instances"></a>Maszyny wirtualne IaaS i PaaS wystąpień roli
Maszyny wirtualne (VM) utworzone za pomocą klasycznego modelu wdrażania są zawsze umieszczane w usłudze w chmurze, podobne do wystąpień roli PaaS. Ten sposób przypomina zachowanie prywatnych adresów IP dla tych zasobów.

Należy zauważyć, że usługa w chmurze można wdrożyć na dwa sposoby:

* Jako *autonomiczny* usługą w chmurze, której nie znajduje się w sieci wirtualnej.
* W ramach sieci wirtualnej.

#### <a name="allocation-method"></a>Metoda alokacji
W przypadku programu *autonomiczny* usługą w chmurze, get zasoby prywatny adres IP jest przydzielany *dynamicznie* z prywatnym adresem IP centrum danych platformy Azure z zakresu adresów. Może służyć tylko do komunikacji z innymi maszynami wirtualnymi w ramach jednej usługi w chmurze. Ten adres IP można zmienić, gdy zasób jest zatrzymana i uruchomiona.

W przypadku usługi w chmurze wdrożonej w ramach sieci wirtualnej zasoby Uzyskaj prywatne adresy IP, przydzielany z zakresu adresów skojarzone podsieci (jak określono w jego konfiguracji sieciowej). To prywatne adresy IP, może służyć do komunikacji między wszystkie maszyny wirtualne w sieci wirtualnej.

Ponadto w przypadku usług cloud services w sieci wirtualnej prywatny adres IP jest przydzielany *dynamicznie* (przy użyciu protokołu DHCP) domyślnie. Może zmienić, gdy zasób jest zatrzymana i uruchomiona. Aby upewnić się, adres IP pozostaje bez zmian, należy ustawić metodę alokacji *statyczne*i podaj prawidłowy adres IP do odpowiedniego zakresu adresów.

Statyczne prywatne adresy IP są powszechnie używane do:

* Maszyn wirtualnych, które działają jako kontrolery domeny lub serwery DNS.
* W przypadku maszyn wirtualnych, które wymagają reguł zapory przy użyciu adresów IP.
* Maszyny wirtualne z usługami uzyskiwał dostęp do innych aplikacji za pomocą adresu IP.

#### <a name="internal-dns-hostname-resolution"></a>Wewnętrzne rozpoznawanie nazwy hosta DNS
Wszystkie maszyny wirtualne platformy Azure i wystąpień roli PaaS są skonfigurowane przy użyciu [serwerami DNS zarządzanymi przez Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) domyślnie, chyba że jawnie skonfigurujesz niestandardowe serwery DNS. Te serwery DNS zapewniają rozpoznawanie nazw wewnętrznych dla maszyn wirtualnych i wystąpień roli, które znajdują się w tej samej sieci wirtualnej lub usługi w chmurze.

Podczas tworzenia maszyny wirtualnej mapowanie nazwy hosta na jego prywatny adres IP jest dodawane do serwerów DNS zarządzanych przez platformę Azure. Z maszyną Wirtualną z wieloma kartami nazwa hosta jest mapowana na prywatny adres IP podstawowej karty sieciowej. Informacje o mapowaniu jest jednak ograniczone do zasobów w ramach jednej usługi w chmurze lub sieci wirtualnej.

W przypadku programu *autonomiczny* usługą w chmurze, będą mieć możliwość rozpoznania nazwy hostów wszystkich wystąpień maszyn wirtualnych/ról w ramach jednej usługi w chmurze tylko. W przypadku usługi w chmurze w ramach sieci wirtualnej można rozwiązać nazwy hostów wszystkich wystąpień maszyn wirtualnych/ról w ramach sieci wirtualnej.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Wewnętrzne moduły równoważenia obciążenia (ILB) i bramy aplikacji
Prywatny adres IP możesz przypisać do konfiguracji **frontonu** [wewnętrznego modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) lub [bramy aplikacji platformy Azure](../application-gateway/application-gateway-introduction.md). Ten prywatny adres IP służy jako wewnętrzny punkt końcowy dostępny tylko dla zasobów w ramach jego sieci wirtualnej i sieci zdalnych podłączonych do sieci wirtualnej. Do konfiguracji frontonu możesz przypisać dynamiczny albo statyczny prywatny adres IP. Można także przypisać wiele prywatnych adresów IP adresu vip wielu scenariuszy.

### <a name="at-a-glance"></a>W skrócie
W poniższej tabeli przedstawiono każdy typ zasobu z ewentualne metody alokacji (dynamiczne/statyczne), a także przypisać wiele prywatnych adresów IP.

| Zasób | Dynamiczny | Statyczny | Wiele adresów IP |
| --- | --- | --- | --- |
| Maszyna wirtualna (w *autonomiczny* usług w chmurze lub sieci wirtualnej) |Yes |Yes |Yes |
| Wystąpienia roli PaaS (w *autonomiczny* usług w chmurze lub sieci wirtualnej) |Yes |Nie |Nie |
| Frontonu modułu równoważenia obciążenia wewnętrznego |Yes |Yes |Yes |
| Fronton aplikacji bramy |Yes |Yes |Yes |

## <a name="limits"></a>Limity
W poniższej tabeli przedstawiono limity dotyczące adresów IP adresowania na platformie Azure na subskrypcję. [Kontaktując się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), możesz zwiększyć domyślne limity do maksimum w zależności od potrzeb biznesowych.

|  | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Publiczne adresy IP (dynamiczne) |5 |kontakt z pomocą techniczną |
| Zastrzeżone publiczne adresy IP |20 |kontakt z pomocą techniczną |
| Publiczne adresy VIP na wdrożenie (usługi w chmurze) |5 |kontakt z pomocą techniczną |
| Prywatnych adresów VIP (ILB) na wdrożenie (usługi w chmurze) |1 |1 |

Upewnij się, przeczytaj pełny zestaw [limitów dla sieci](../azure-subscription-service-limits.md#networking-limits) na platformie Azure.

## <a name="pricing"></a>Cennik
W większości przypadków publiczne adresy IP są bezpłatne. Istnieje nominalna opłata za używania dodatkowych i/lub statycznych publicznych adresów IP. Upewnij się, że rozumiesz [struktury cen publicznych adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Różnice między Menedżera zasobów i wdrożeniami klasycznymi
Poniżej przedstawiono porównanie funkcji adresowania IP usługi Resource Manager i klasycznego modelu wdrażania.

|  | Zasób | Wdrożenie klasyczne | Resource Manager |
| --- | --- | --- | --- |
| **Publiczny adres IP** |***VM*** |Nazywane ILPIP (tylko dynamiczny) |Określane jako publiczny adres IP (dynamiczny lub statyczny) |
|  ||Przypisane do maszyn wirtualnych IaaS lub wystąpień roli PaaS |Skojarzone z karty Sieciowej maszyny Wirtualnej |
|  |***Moduł równoważenia obciążenia dostępnego z Internetu*** |Określone jako wirtualne adresy IP (dynamiczny) lub zastrzeżony adres IP (statyczne) |Określane jako publiczny adres IP (dynamiczny lub statyczny) |
|  ||Przypisane do usługi w chmurze |Skojarzone z konfiguracji frontonu modułu równoważenia obciążenia |
|  | | | |
| **Prywatny adres IP** |***VM*** |Nazywane DIP |Określane jako prywatny adres IP |
|  ||Przypisane do maszyn wirtualnych IaaS lub wystąpień roli PaaS |Przypisane do karty Sieciowej maszyny Wirtualnej |
|  |***Wewnętrzny moduł równoważenia obciążenia (ILB)*** |Przypisane do wewnętrznego modułu równoważenia obciążenia (dynamicznej lub statycznej) |Przypisane do konfiguracji frontonu modułem równoważenia obciążenia (dynamicznej lub statycznej) |

## <a name="next-steps"></a>Kolejne kroki
* [Wdrażanie maszyny Wirtualnej ze statycznym prywatnym adresem IP](virtual-networks-static-private-ip-classic-pportal.md) przy użyciu witryny Azure portal.

