---
title: Łączność publicznych punktów końcowych dla maszyn wirtualnych platformy Azure&standardowych równoważenia obciążenia sieciowego w scenariuszach sap a-ha
description: Łączność publicznych punktów końcowych dla maszyn wirtualnych przy użyciu standardowego modułu równoważenia obciążenia platformy Azure w scenariuszach wysokiej dostępności SAP
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: radeltch
ms.openlocfilehash: 4fd01764c183098a8bd78d502eea7ab173fa22cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293911"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Łączność publicznych punktów końcowych dla maszyn wirtualnych przy użyciu standardowego modułu równoważenia obciążenia platformy Azure w scenariuszach wysokiej dostępności SAP

Zakres tego artykułu jest opisanie konfiguracji, które umożliwią połączenia wychodzące z publicznymi punktami końcowymi. Konfiguracje są głównie w kontekście wysokiej dostępności z rozrusznikiem serca dla SUSE / RHEL.  

Jeśli używasz rozrusznika serca z agentem ogrodzenia platformy Azure w rozwiązaniu o wysokiej dostępności, maszyny wirtualne muszą mieć łączność wychodzącą z interfejsem API zarządzania platformy Azure.  
W artykule przedstawiono kilka opcji, aby umożliwić wybranie opcji, która jest najlepiej nadaje się do scenariusza.  

## <a name="overview"></a>Omówienie

Podczas wdrażania wysokiej dostępności rozwiązań SAP za pośrednictwem klastrowania, jednym z niezbędnych składników jest [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Platforma Azure oferuje dwa jednostki SKU równoważenia obciążenia: standardowe i podstawowe.

Standardowy moduł równoważenia obciążenia platformy Azure oferuje pewne zalety w stosunku do modułu równoważenia obciążenia podstawowego. Na przykład działa w strefach dostępności platformy Azure, ma lepsze możliwości monitorowania i rejestrowania dla łatwiejszego rozwiązywania problemów, mniejsze opóźnienie. Funkcja "Porty ha" obejmuje wszystkie porty, to znaczy, że nie jest już konieczne, aby wyświetlić listę wszystkich pojedynczych portów.  

Istnieją pewne istotne różnice między podstawową i standardową jednostką SKU modułu równoważenia obciążenia platformy Azure. Jednym z nich jest obsługa ruchu wychodzącego do publicznego punktu końcowego. Aby uzyskać pełne porównanie modułu równouprawnienia obciążenia basic versus Standard SKU, zobacz [Porównanie jednostki SKU modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).  
 
Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli wewnętrznej bazy danych wewnętrznego (bez publicznego adresu IP) Standardowy moduł równoważenia obciążenia platformy Azure, nie ma łączności wychodzącej z publicznymi punktami końcowymi, chyba że zostanie wykonana dodatkowa konfiguracja.  

Jeśli maszynie wirtualnej jest przypisany publiczny adres IP lub maszyna wirtualna znajduje się w puli wewnętrznej bazy danych modułu równoważenia obciążenia z publicznym adresem IP, będzie miała łączność wychodzącą z publicznymi punktami końcowymi.  

Systemy SAP często zawierają poufne dane biznesowe. Rzadko jest dopuszczalne dla maszyn wirtualnych obsługujących systemy SAP mieć publiczne adresy IP. W tym samym czasie istnieją scenariusze, które wymagają połączenia wychodzącego z maszyny Wirtualnej do publicznych punktów końcowych.  

Przykłady scenariuszy wymagających dostępu do publicznego punktu końcowego platformy Azure to:  
- Używanie agenta ogrodzenia platformy Azure jako mechanizmu ogrodzenia w klastrach rozrusznika serca
- Azure Backup
- Azure Site Recovery  
- Używanie publicznego repozytorium do poprawiania systemu operacyjnego
- Przepływ danych aplikacji SAP może wymagać połączenia wychodzącego z publicznym punktem końcowym

Jeśli wdrożenie SAP nie wymaga połączenia wychodzącego z publicznymi punktami końcowymi, nie trzeba implementować dodatkowej konfiguracji. Jest wystarczające do utworzenia wewnętrznego standardu SKU Azure Load Balancer dla scenariusza wysokiej dostępności, przy założeniu, że nie ma również potrzeby połączeń przychodzących z publicznych punktów końcowych.  

> [!Note]
> Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli wewnętrznej bazy danych wewnętrznego (bez publicznego adresu IP) Standardowy moduł równoważenia obciążenia platformy Azure, nie będzie żadnych wychodzących połączeń z Internetem, chyba że zostanie wykonana dodatkowa konfiguracja, aby umożliwić routing do publicznych punktów końcowych.  
>Jeśli maszyny wirtualne mają publiczne adresy IP lub znajdują się już w puli wewnętrznej bazy danych modułu równoważenia obciążenia platformy Azure z publicznym adresem IP, maszyna wirtualna będzie już miała łączność wychodzącą z publicznymi punktami końcowymi.


Przeczytaj najpierw następujące dokumenty:

* Standardowy moduł równoważenia obciążenia platformy Azure
  * [Omówienie modułu równoważenia obciążenia standardowego platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) — kompleksowe omówienie modułu równoważenia obciążenia standardowego platformy Azure, ważnych zasad, koncepcji i samouczków 
  * [Połączenia wychodzące na platformie Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) — scenariusze dotyczące sposobu osiągnięcia łączności wychodzącej na platformie Azure
  * [Reguły ruchu wychodzącego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)— wyjaśniono pojęcia reguł wychodzących modułu równoważenia obciążenia oraz sposób tworzenia reguł ruchu wychodzącego
* Azure Firewall
  * [Omówienie zapory platformy Azure](https://docs.microsoft.com/azure/firewall/overview)— omówienie Zapory platformy Azure
  * [Samouczek: Wdrażanie i konfigurowanie Zapory platformy Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) — instrukcje dotyczące konfigurowania zapory platformy Azure za pośrednictwem witryny Azure portal
* [Sieci wirtualne — reguły zdefiniowane przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) — pojęcia i reguły routingu platformy Azure  
* [Tagi usług grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) — jak uprościć konfigurację sieciowych grup zabezpieczeń i zapory za pomocą tagów usług

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Dodatkowy zewnętrzny moduł równoważenia obciążenia azure standard dla połączeń wychodzących z Internetem

Jedną z opcji osiągnięcia łączności wychodzącej z publicznymi punktami końcowymi, bez zezwalania na łączność przychodzącą z maszyną wirtualną z publicznego punktu końcowego, jest utworzenie drugiego modułu równoważenia obciążenia z publicznym adresem IP, dodanie maszyn wirtualnych do puli wewnętrznej bazy danych drugiego modułu równoważenia obciążenia i zdefiniowanie tylko [reguł wychodzących](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).  
[Sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview) służą do kontrolowania publicznych punktów końcowych, które są dostępne dla połączeń wychodzących z maszyny Wirtualnej.  
Aby uzyskać więcej informacji, zobacz Scenariusz 2 w dokumencie [Połączenia wychodzące](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios).  
Konfiguracja będzie wyglądać następująco:  

![Sterowanie łącznością z publicznymi punktami końcowymi za pomocą grup zabezpieczeń sieci](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Istotne zagadnienia

- Można użyć jednego dodatkowego modułu równoważenia obciążenia publicznego dla wielu maszyn wirtualnych w tej samej podsieci, aby uzyskać łączność wychodzącą z publicznym punktem końcowym i zoptymalizować koszt  
- Użyj [sieciowych grup zabezpieczeń,](https://docs.microsoft.com/azure/virtual-network/security-overview) aby kontrolować, które publiczne punkty końcowe są dostępne z maszyn wirtualnych. Grupę zabezpieczeń sieci można przypisać do podsieci lub do każdej maszyny wirtualnej. Jeśli to możliwe, użyj [tagów usługi,](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) aby zmniejszyć złożoność reguł zabezpieczeń.  
- Standardowy moduł równoważenia obciążenia platformy Azure z publicznym adresem IP i regułami wychodzącymi umożliwia bezpośredni dostęp do publicznego punktu końcowego. Jeśli masz wymagania dotyczące zabezpieczeń firmy, aby wszystkie wychodzące przejść ruchu za pośrednictwem scentralizowanego rozwiązania firmowego do inspekcji i rejestrowania, może nie być w stanie spełnić wymagania w tym scenariuszu.  

>[!TIP]
>Tam, gdzie to możliwe, użyj [tagów usługi,](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) aby zmniejszyć złożoność sieciowej grupy zabezpieczeń . 

### <a name="deployment-steps"></a>Kroki wdrażania

1. Tworzenie modułu równoważenia obciążenia  
   1. W [witrynie Azure portal](https://portal.azure.com) kliknij pozycję Wszystkie zasoby, Dodaj, a następnie **wyszukaj moduł równoważenia obciążenia**  
   1. Kliknij **przycisk Utwórz** 
   1. Nazwa modułu równoważenia obciążenia **MyPublicILB**  
   1. Wybierz **opcję Publiczne** jako typ, **standard** jako jednostka SKU  
   1. Wybierz **pozycję Utwórz publiczny adres IP** i określ jako nazwę **MyPublicILBFrondEndIP**  
   1. Wybierz **strefę nadmiarową** jako strefę dostępności  
   1. Kliknij pozycję Recenzja i utwórz, a następnie kliknij pozycję Utwórz  
2. Utwórz pulę zaplecza **MyBackendPoolOfPublicILB** i dodaj maszyny wirtualne.  
   1. Wybierz sieć wirtualną  
   1. Wybierz maszyny wirtualne i ich adresy IP i dodaj je do puli wewnętrznej bazy danych  
3. [Tworzenie reguł ruchu wychodzącego](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule). Obecnie nie jest możliwe tworzenie reguł ruchu wychodzącego z witryny Azure portal. Reguły ruchu wychodzącego można tworzyć za pomocą [narzędzia Azure CLI](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Utwórz reguły grupy zabezpieczeń sieciowych, aby ograniczyć dostęp do określonych publicznych punktów końcowych. Jeśli istnieje istniejąca grupa zabezpieczeń sieciowych, można ją dostosować. W poniższym przykładzie pokazano, jak włączyć dostęp do interfejsu API zarządzania platformy Azure: 
   1. Przejdź do sieciowej grupy zabezpieczeń
   1. Kliknij pozycję Wychodzące reguły zabezpieczeń
   1. Dodaj regułę do **odmów** wszystkim dostępom wychodzącym do **Internetu**.
   1. Dodaj regułę **zezwalaj na** dostęp do **usługi AzureCloud**, z priorytetem niższym niż priorytet reguły, aby odmówić całego dostępu do Internetu.


   Reguły zabezpieczeń wychodzących będą wyglądać następująco: 

   ![Połączenie wychodzące z drugim modułem równoważenia obciążenia z publicznym adresem IP](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Aby uzyskać więcej informacji na temat grup zabezpieczeń usługi Azure Network, zobacz [Grupy zabezpieczeń ](https://docs.microsoft.com/azure/virtual-network/security-overview). 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Zapora azure dla połączeń wychodzących z Internetem

Inną opcją osiągnięcia łączności wychodzącej do publicznych punktów końcowych, bez zezwalania na łączność przychodzącą do maszyny Wirtualnej z publicznych punktów końcowych, jest zapora azure. Zapora azure to usługa zarządzana z wbudowaną wysoką dostępnością i może obejmować wiele stref dostępności.  
Należy również wdrożyć [trasę zdefiniowaną przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes), skojarzoną z podsiecią, w której są wdrażane maszyny wirtualne i moduł równoważenia obciążenia platformy Azure, wskazując zaporę platformy Azure, aby kierować ruch za pośrednictwem Zapory azure.  
Aby uzyskać szczegółowe informacje na temat wdrażania Zapory platformy Azure, zobacz [Wdrażanie i konfigurowanie Zapory platformy Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal).  

Architektura będzie wyglądać następująco:

![Połączenie wychodzące z Zaporą platformy Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Istotne zagadnienia

- Zapora azure jest usługą natywną dla chmury, z wbudowaną wysoką dostępnością i obsługuje wdrażanie strefowe.
- Wymaga dodatkowej podsieci, która musi mieć nazwę AzureFirewallSubnet. 
- Jeśli przeniesienie dużych zestawów danych wychodzących sieci wirtualnej, w której znajdują się maszyny wirtualne SAP, do maszyny Wirtualnej w innej sieci wirtualnej lub do publicznego punktu końcowego, może nie być opłacalne rozwiązanie. Jednym z takich przykładów jest kopiowanie dużych kopii zapasowych w sieciach wirtualnych. Aby uzyskać szczegółowe informacje, zobacz Cennik zapory platformy Azure.  
- Jeśli rozwiązanie zapory firmowej nie jest zaporą platformy Azure i masz wymagania dotyczące zabezpieczeń, aby wszystkie wychodzące przekazać ruch chociaż scentralizowane rozwiązanie firmowe, to rozwiązanie może nie być praktyczne.  

>[!TIP]
>Jeśli to możliwe, użyj [tagów usługi,](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) aby zmniejszyć złożoność reguł zapory platformy Azure.  

### <a name="deployment-steps"></a>Kroki wdrażania

1. Kroki wdrażania zakładają, że masz już zdefiniowaną sieć wirtualną i podsieć dla maszyn wirtualnych.  
2. Utwórz podsieć **AzureFirewallSubnet** w tej samej sieci wirtualnej, w której są wdrażane maszyny wirtualne i moduł równoważenia obciążenia standardowego.  
   1. W witrynie Azure portal przejdź do sieci wirtualnej: kliknij pozycję Wszystkie zasoby, wyszukaj sieć wirtualną, kliknij sieć wirtualną i wybierz podsieci.  
   1. Kliknij pozycję Dodaj podsieć. Wprowadź **AzureFirewallSubnet** jako nazwa. Wprowadź odpowiedni zakres adresów. Zapisz zmiany.  
3. Utwórz zaporę platformy Azure.  
   1. W portalu Azure wybierz pozycję Wszystkie zasoby, kliknij pozycję Dodaj, Zapora, Utwórz. Wybierz grupę zasobów (wybierz tę samą grupę zasobów, w której znajduje się sieć wirtualna).  
   1. Wprowadź nazwę zasobu Zapory platformy Azure. Na przykład **MyAzureFirewall**.  
   1. Wybierz region i wybierz co najmniej dwie strefy dostępności, wyrównane ze strefami dostępności, w których są wdrażane maszyny wirtualne.  
   1. Wybierz sieć wirtualną, w której są wdrażane maszyny wirtualne SAP i moduł równoważenia obciążenia standardowego platformy Azure.  
   1. Publiczny adres IP: kliknij pozycję Utwórz i wprowadź nazwę. Na przykład **MyFirewallPublicIP**.  
4. Utwórz regułę zapory platformy Azure, aby zezwolić na łączność wychodzącą z określonymi publicznymi punktami końcowymi. W przykładzie pokazano, jak zezwolić na dostęp do publicznego punktu końcowego interfejsu API usługi Azure Management.  
   1. Wybierz pozycję Reguły, kolekcja reguł sieciowych, a następnie kliknij pozycję Dodaj kolekcję reguł sieciowych.  
   1. Nazwa: **MyOutboundRule**, wprowadź priorytet, Wybierz **akcję Zezwalaj**.  
   1. Usługa: Name **ToAzureaPI**.  Protokół: Wybierz **dowolny**. Adres źródłowy: wprowadź zakres podsieci, w którym są wdrażane maszyny wirtualne i standardowy moduł równoważenia obciążenia, na przykład: **11.97.0.0/24**. Porty docelowe: wprowadź <b>*</b>.  
   1. Zapisz
   1. Ponieważ nadal znajdują się na Zaporze platformy Azure, Wybierz omówienie. Zanotuj prywatny adres IP zapory platformy Azure.  
5. Tworzenie trasy do Zapory platformy Azure  
   1. W witrynie Azure portal wybierz pozycję Wszystkie zasoby, a następnie kliknij pozycję Dodaj, Tabela tras, Utwórz.  
   1. Wprowadź nazwę MyRouteTable, wybierz pozycję Subskrypcja, Grupa zasobów i Lokalizacja (pasująca do lokalizacji sieci wirtualnej i zapory sieciowej).  
   1. Zapisz  

   Reguła zapory będzie ![wyglądać: Połączenie wychodzące z Zaporą platformy Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Utwórz trasę zdefiniowaną przez użytkownika z podsieci maszyn wirtualnych do prywatnego adresu IP **myazurefirewall**.
   1. Po umieszczeniu w tabeli tras kliknij pozycję Trasy. Wybierz pozycję Dodaj. 
   1. Nazwa trasy: ToMyAzureFirewall, Prefiks adresu: **0.0.0.0/0**. Następny typ przeskoku: wybierz urządzenie wirtualne. Następny adres przeskoku: wprowadź prywatny adres IP skonfigurowanej zapory: **11.97.1.4**.  
   1. Zapisz

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Korzystanie z serwera proxy dla wywołań rozrusznika do interfejsu API usługi Azure Management

Można użyć serwera proxy, aby zezwolić na wywołania programu Pacemaker do publicznego punktu końcowego interfejsu API zarządzania platformy Azure.  

### <a name="important-considerations"></a>Istotne zagadnienia

  - Jeśli istnieje już firmowy serwer proxy w miejscu, można kierować połączenia wychodzące do publicznych punktów końcowych za pośrednictwem niego. Połączenia wychodzące do publicznych punktów końcowych będą przechodzić przez korporacyjny punkt kontrolny.  
  - Upewnij się, że konfiguracja serwera proxy umożliwia łączność wychodzącą z interfejsem API zarządzania usługą Azure:`https://management.azure.com`  
  - Upewnij się, że istnieje trasa z maszyn wirtualnych do serwera proxy  
  - Serwer proxy będzie obsługiwał tylko wywołania HTTP/HTTPS. Jeśli istnieje dodatkowa potrzeba wywołania wychodzącego do publicznego punktu końcowego w różnych protokołach (takich jak RFC), potrzebne będzie alternatywne rozwiązanie  
  - Rozwiązanie proxy musi być wysoce dostępne, aby uniknąć niestabilności w klastrze rozrusznika  
  - W zależności od lokalizacji serwera proxy może wprowadzić dodatkowe opóźnienia w wywołaniach z agenta ogrodzenia platformy Azure do interfejsu API usługi Azure Management. Jeśli serwer proxy firmy jest nadal w środowisku lokalnym, podczas gdy klaster rozrusznika serca jest na platformie Azure, zmierz opóźnienie i należy wziąć pod uwagę, jeśli to rozwiązanie jest odpowiednie dla Ciebie  
  - Jeśli nie ma jeszcze wysoce dostępnego korporacyjnego serwera proxy, nie zalecamy tej opcji, ponieważ klient ponosiłby dodatkowe koszty i złożoność. Niemniej jednak, jeśli zdecydujesz się wdrożyć dodatkowe rozwiązanie proxy, w celu umożliwienia łączności wychodzącej z programu Pacemaker do publicznego interfejsu API usługi Azure Management, upewnij się, że serwer proxy jest wysoce dostępny, a opóźnienie od maszyn wirtualnych do serwera proxy jest niskie.  

### <a name="pacemaker-configuration-with-proxy"></a>Konfiguracja rozrusznika z serwerem proxy 

Istnieje wiele różnych opcji proxy dostępnych w branży. Instrukcje krok po kroku dotyczące wdrażania serwera proxy są poza zakresem tego dokumentu. W poniższym przykładzie zakładamy, że twój serwer proxy odpowiada na **MyProxyService** i słucha portu **MyProxyPort**.  
Aby umożliwić rozrusznikowi serca komunikowanie się z interfejsem API zarządzania platformy Azure, wykonaj następujące kroki we wszystkich węzłach klastra:  

1. Edytuj plik konfiguracyjny rozrusznika serca /etc/sysconfig/pacemaker i dodaj następujące wiersze (wszystkie węzły klastra):

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Uruchom ponownie usługę rozrusznika serca we **wszystkich** węzłach klastra.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak skonfigurować rozrusznik serca na szesnasce SUSE na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [Dowiedz się, jak skonfigurować rozrusznik serca na czerwonej czapce na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
