---
title: Zagadnienia dotyczące integracji ogólne centrum danych Azure stosu zintegrowane systemy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak planować teraz i przygotowania do integracji centrum danych z wieloma węzłami Azure stosu.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 55243ead4f088f7a2b3d54c0581c604f0dc63d07
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Zagadnienia dotyczące integracji centrum danych Azure stosu zintegrowane systemy
Jeśli interesuje Cię systemu Azure stosu zintegrowane, należy poznać niektóre z najważniejszych kwestii dotyczących planowania wdrożenia i jak system dopasowuje się do centrum danych. Ten artykuł zawiera omówienie te zagadnienia dotyczące ułatwiającym podejmowanie decyzji ważne infrastruktury systemu Azure stosu wieloma węzłami. Opis tych zagadnień pomaga podczas pracy z dostawcą sprzętu OEM zgodnie z wdrożeniem Azure stosu w centrum danych.  

> [!NOTE]
> Azure systemów wielowęzłowego stosu zakupienia tylko autoryzowanym sprzętem dostawców. 

Aby wdrożyć stosu Azure, konieczne jest zapewnienie informacje na temat planowania dostawcy rozwiązań, przed rozpoczęciem wdrażania ułatwić proces Przejdź szybkie i sprawne. Informacje wymagane zakresów w sieci, zabezpieczeń i informacje o tożsamości z wiele istotnych decyzji, które mogą wymagać wiedzy z wielu różnych obszarów i inne osoby podejmujące decyzje. W związku z tym może być konieczne ściągnąć osób z wielu zespoły w organizacji, aby upewnić się, że wszystkie wymagane informacje gotowe, przed rozpoczęciem wdrażania. Ułatwia komunikować się z dostawcą sprzętu podczas zbierania tych informacji, jak mogą pojawić się porady przydatne do tworzenia swoje decyzje dotyczące.

Podczas badania i zbieranie wymaganych informacji, może być konieczne dokonanie pewnych zmian w konfiguracji przed wdrożeniem w środowisku sieciowym. Mogą one obejmować rezerwacji przestrzeni adresów IP dla rozwiązania Azure stosu, konfigurowanie Twojego routery, przełączniki i zapór, aby przygotować się do łączność nowych przełączników rozwiązania Azure stosu. Upewnij się, że ma obszaru podmiotu ekspert wyrównany do pomóc w planowaniu.

## <a name="capacity-planning-considerations"></a>Zagadnienia związane z planowaniem pojemności
Podczas oceniania rozwiązania stosu Azure nabycia, opcje konfiguracji sprzętu muszą być wprowadzane które mają bezpośredni wpływ na ogólną pojemność ich rozwiązania Azure stosu. Obejmują one klasycznego wyboru procesora CPU, gęstość pamięci konfiguracji magazynu i skalowania rozwiązania ogólnej (np. liczba serwerów). W odróżnieniu od rozwiązanie wirtualizacji tradycyjnych proste operacje arytmetyczne ustalenia pojemności można używać tych składników nie ma zastosowania. Pierwszy dzieje się tak że stosu Azure została zaprojektowana do obsługi składników infrastruktury i zarządzania w ramach samego rozwiązania. Drugi przyczyna jest niektóre pojemności rozwiązania jest zarezerwowana wesprzeć odporności; Aktualizowanie oprogramowania rozwiązania w taki sposób, aby zminimalizować przerwy w świadczeniu obciążeń dzierżawców. 

[Arkusz kalkulacyjny planowania pojemności stosu Azure](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pomaga wprowadzeniu informacji decyzji odnoszących się do planowania pojemności na dwa sposoby: albo wybierając oferty sprzętu i próby dopasowania połączonych zasobów lub definiując obciążenia Azure stosu jest przeznaczony do uruchomienia, aby wyświetlić dostępne jednostki SKU, które obsługują tę sprzęt. Na koniec arkusz kalkulacyjny służy jako przewodnik dotyczący pomoc w podejmowaniu decyzji związanych z stosu Azure planowania i konfiguracji. 

Arkusz kalkulacyjny nie jest przeznaczony do obsługi zamiast własnych dochodzenia i analizy.  Firma Microsoft nie udziela żadnych oświadczeń ani żadnych gwarancji, lub domniemanych, w odniesieniu do informacji dostępnych w arkuszu kalkulacyjnym.



## <a name="management-considerations"></a>Zagadnienia dotyczące zarządzania
Azure stos jest zapieczętowany system, gdzie infrastruktury jest zablokowana zarówno od uprawnień i sieciowych perspektywy. Listy kontroli dostępu do sieci (ACL) są stosowane do Zablokuj cały ruch przychodzący nieautoryzowanych i wszystkie zbędne komunikacji między składnikami infrastruktury. Utrudnia nieautoryzowanym użytkownikom dostęp do systemu.

Codzienne zarządzanie i operacje jest nie administratora nieograniczony dostęp do infrastruktury. Operatory stosu Azure muszą zarządzać systemu za pośrednictwem portalu administratora lub za pośrednictwem usługi Azure Resource Manager (za pośrednictwem programu PowerShell lub interfejsu API REST). Brak dostępu do systemu, narzędzia do zarządzania, takie jak Menedżer funkcji Hyper-V lub Menedżera klastra trybu Failover nie istnieje. Aby lepiej chronić systemu, oprogramowania innych firm (na przykład agenci) nie można zainstalować wewnątrz składników infrastruktury Azure stosu. Współdziałanie z zewnętrznego oprogramowania do zarządzania i zabezpieczeń odbywa się za pośrednictwem programu PowerShell lub interfejsu API REST.

Gdy wyższy poziom dostępu jest potrzebna do rozwiązywania problemów, które nie są rozpoznawane za pomocą kroków pośrednictwa alertu, należy skontaktować się z Microsoft Support. Za pomocą techniczną Brak metody zapewnienie tymczasowego pełne uprawnienia dostępu administratora systemu do bardziej zaawansowanych operacji. 

## <a name="identity-considerations"></a>Zagadnienia dotyczące tożsamości

### <a name="choose-identity-provider"></a>Wybierz dostawcę tożsamości
Należy wziąć pod uwagę dostawcy tożsamości, którego chcesz użyć dla wdrożenia stosu Azure, Azure AD lub AD FS. Nie można przełączyć dostawców tożsamości, po wdrożeniu bez ponownego wdrażania całego systemu. Jeśli nie ma konta usługi Azure AD i korzystania z konta dostarczonego przez dostawcę usługi w chmurze, a jeśli zdecydujesz się przełączyć dostawcy i użyj innej usługi Azure AD konto, w tym momencie, należy skontaktować się z dostawcą rozwiązań do ponownego wdrożenia rozwiązania f można także z kosztami.



Wybór dostawcy tożsamości nie ma wpływu na maszyny wirtualne dzierżawców, system obsługi tożsamości i kont, których używają, czy ich może dołączyć do domeny usługi Active Directory itp. To jest oddzielony.

Dowiedz się więcej na temat wybierania dostawcy tożsamości w [stosu Azure zintegrowanych systemów połączenia modeli artykułu](.\azure-stack-connection-models.md).

### <a name="ad-fs-and-graph-integration"></a>Integracja usług AD FS i wykres
Jeśli wybierzesz do wdrożenia stosu Azure za pomocą usług AD FS jako dostawca tożsamości, można zintegrować wystąpienia usług AD FS na stosie Azure z istniejącym wystąpieniem usług AD FS za pomocą zaufania federacyjnego. Dzięki temu tożsamości w istniejącym lesie usługi Active Directory do uwierzytelniania z zasobami w stosie Azure.

Można również zintegrować usługę wykresu w stosie Azure z istniejącej usługi Active Directory. Pozwala na zarządzanie oparte na rolach kontroli dostępu (RBAC) w stosie Azure. Po oddelegowaniu dostęp do zasobu składnika Wykres wyszukuje konta użytkownika w istniejącym lesie usługi Active Directory przy użyciu protokołu LDAP.

Na poniższym diagramie przedstawiono zintegrowane przepływu ruchu usługi AD FS i Graph.
![Diagram przedstawiający przepływ ruchu wykres i usług AD FS](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Model licencjonowania
Należy określić model licencjonowania, który ma być używany. Dostępne opcje zależą od tego, czy wdrożyć stosu Azure połączony z Internetem:
- Dla [połączone wdrożenia](azure-stack-connected-deployment.md), możesz wybrać płatności jako — użytkownik użycia lub na podstawie zdolności licencjonowania. Płatności — jako — możesz — użycie wymaga połączenia z platformą Azure raportowania użycia, następnie jest on rozliczany za pośrednictwem commerce platformy Azure. 
- Licencjonowanie tylko na podstawie zdolności produkcyjnych jest obsługiwane, jeśli można [wdrażanie odłączony](azure-stack-disconnected-deployment.md) z Internetu. 

Aby uzyskać więcej informacji na temat modeli licencjonowania, zobacz [Microsoft Azure stosu pakowania i cenach](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Decyzje dotyczące nazewnictwa

Należy się zastanowić, jak chcesz zaplanować nazw Azure stosu, szczególnie nazwa regionu i nazwy domeny zewnętrznej. Zewnętrzne pełną nazwę domeny (FQDN) dla publicznych punktów końcowych wdrożenia stosu Azure jest kombinacją te dwie nazwy: &lt; *region*&gt;.&lt; *fqdn*&gt;. Na przykład *east.cloud.fabrikam.com*. W tym przykładzie portali stosu Azure będzie dostępna w następujących adresów URL:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Nazwa regionu wybrane do wdrożenia usługi Azure stos musi być unikatowa i będą wyświetlane w portalu adresów. 

W poniższej tabeli przedstawiono te decyzje nazewnictwa domeny.

| Name (Nazwa) | Opis | 
| -------- | ------------- | 
|Nazwa regionu | Nazwa pierwszego regionu Azure stosu. Ta nazwa jest używana jako część nazwy FQDN dla publicznych wirtualne adresy IP (VIP) zarządzanych przez stos Azure. Zazwyczaj nazwa regionu powinien być identyfikator lokalizacji fizycznej, takie jak lokalizacja centrum danych. | 
| Nazwy domen zewnętrznych | Nazwa strefy systemu nazw domen (DNS, Domain Name System) dla punktów końcowych z zewnątrz adresów VIP. Używany w nazwie FQDN dla te publiczne adresy VIP. | 
| Nazwy prywatnych domeny (wewnętrzny) | Nazwa domeny (i wewnętrznej strefy DNS), utworzone na stosie Azure infrastruktury zarządzania. 
| | |

## <a name="certificate-requirements"></a>Wymagania dotyczące certyfikatów

Wdrożenia musisz podać certyfikatów Secure Sockets Layer (SSL) dla publicznych punktów końcowych. Na wysokim poziomie certyfikaty mają następujące wymagania:

- Można użyć certyfikatu jeden symbol wieloznaczny lub korzystanie z zestawu dedykowanych certyfikatów i użyć symboli wieloznacznych tylko dla punktów końcowych, takie jak magazyn i magazyn kluczy.
- Certyfikaty mogą być wystawiane przez publiczny zaufanego urzędu certyfikacji (CA) lub urzędu certyfikacji zarządzany przez klienta.

Aby uzyskać więcej informacji o jakie infrastruktury kluczy publicznych Certyfikaty są wymagane do wdrożenia stosu Azure i sposobu ich uzyskania, zobacz, [wymagania dotyczące certyfikatów infrastruktury kluczy publicznych stosu Azure](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> Podane informacje o certyfikacie PKI powinien służyć jako ogólne wytyczne. Przed nabyciem wszystkie certyfikaty PKI dla stosu Azure należy skontaktować się z partnerem sprzętu OEM. Użytkownicy muszą podać szczegółowe wskazówki dotyczące certyfikatu i wymagań.


## <a name="time-synchronization"></a>Czas synchronizacji
Musisz wybrać używanych do synchronizacji Azure stosu serwera o określonym czasie.  Symbolization czasu jest kluczowa stosu Azure i jej role infrastruktury, ponieważ jest używany do generowania biletów Kerberos, które są używane do uwierzytelniania wewnętrznego usługi ze sobą.

Należy określić adresu IP dla serwera synchronizacji czasu, mimo że większość składników w infrastrukturze może rozpoznać adresu URL, niektóre może obsługiwać tylko adresy IP. Jeśli jesteś są używania opcji wdrażania bez połączenia, należy określić czas server w sieci firmowej, którą można połączyć się, że od infrastruktury sieci w stosie usługi Azure.

## <a name="connect-azure-stack-to-azure"></a>Azure stosu połączenia z platformą Azure

W scenariuszach chmur hybrydowych należy zaplanować, jak chcesz się połączyć stosu Azure na platformie Azure. Istnieją dwie metody obsługiwanych nawiązać sieci wirtualnych Azure stosu sieci wirtualnych na platformie Azure: 
- **Lokacja lokacja**. Połączenie wirtualnej sieci prywatnej (VPN) za pośrednictwem protokołu IPsec (IKE v1 i IKE v2). Ten typ połączenia wymaga urządzenia VPN lub usługi Routing i dostęp zdalny (RRAS). Aby uzyskać więcej informacji na temat bram sieci VPN platformy Azure, zobacz [o bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Komunikacja przez ten tunel jest zaszyfrowany i jest bezpieczne. Jednak przepustowość jest ograniczone przez maksymalną przepływność tunelu (100 – 200 MB/s).
- **NAT ruchu wychodzącego**. Domyślnie wszystkie maszyny wirtualne Azure stosu będzie połączeniem do sieci zewnętrznej za pośrednictwem wychodzącego translatora adresów sieciowych. Każda sieć wirtualna, która jest tworzona w stosie usługi Azure pobiera publicznego adresu IP przypisane do niej. Czy maszyny wirtualnej bezpośrednio przypisano publiczny adres IP lub jest za modułem równoważenia obciążenia z publicznym adresem IP, jej wychodzący dostęp za pośrednictwem NAT ruchu wychodzącego przy użyciu adresu VIP sieci wirtualnej. To działanie jest tylko do komunikacji, który jest inicjowane przez maszynę wirtualną i przeznaczonych dla zewnętrznych sieci (w Internecie lub intranecie). Nie można nawiązać połączenia z maszyną wirtualną spoza.

### <a name="hybrid-connectivity-options"></a>Opcje połączeń hybrydowych

Połączenie hybrydowe z należy wziąć pod uwagę rodzaj wdrożenia, które chcesz zaoferować i którym zostanie wdrożony. Należy wziąć pod uwagę tego, czy należy do izolacji ruchu sieciowego dla poszczególnych dzierżawców i czy masz wdrożenia intranetu lub Internetu.

- **Pojedynczej dzierżawy Azure stosu**. Wdrożenia usługi Azure stosu wygląda, co najmniej z punktu widzenia sieci, jak w przypadku jednego dzierżawcy. Może być się, że dzierżawy wiele subskrypcji, ale jak intranetową usługę, cały ruch przesyłany tej samej sieci. Ruch sieciowy z jedną subskrypcją przesyłane w tym samym połączenia sieciowego w postaci innej subskrypcji i nie muszą być izolowane za pośrednictwem tunelu zaszyfrowane.

- **Wielodostępne Azure stosu**. Wdrożenia stosu Azure, w której ruch każdej subskrypcji dzierżawcy, przeznaczony dla sieci, które są zewnętrzne względem stosu Azure musi być odizolowany od ruchu sieciowego innych dzierżawców.
 
- **Intranet wdrożenia**. Wdrożenia stosu Azure, która znajduje się w intranecie firmowym, zwykle w prywatnej przestrzeni adresów IP i co najmniej jeden zapory. Publiczne adresy IP nie są naprawdę publiczne, ponieważ nie można go skierować bezpośrednio za pośrednictwem publicznej sieci internet.

- **Wdrożenie Internet**. Wdrożenia stosu Azure jest podłączony do publicznego Internetu i używa routingu internetowego publicznego adresu IP adresy dla publicznych zakresu adresów VIP. Wdrożenia można nadal znajdują się za zaporą, ale publicznego zakresu adresów VIP jest bezpośrednio dostępny z publicznego Internetu i Azure.
 
W poniższej tabeli przedstawiono scenariusze łączności hybrydowych, specjalistów, wad i przypadki użycia.

| Scenariusz | Połączenie — metoda | Specjaliści | Wady | Nadaje się do |
| -- | -- | --| -- | --|
| Pojedynczy dzierżawy Azure stosu, wdrożenie w sieci intranet | NAT ruchu wychodzącego | Lepszą przepustowość transferów szybsze. Prosta implementacja; nie wymaganych bram. | Ruch nie jest szyfrowany. Brak izolacji i szyfrowania poza TOR. | Gdzie są równie zaufane wszystkich dzierżaw wdrożeń w przedsiębiorstwie.<br><br>Przedsiębiorstw, które mają obwodu Azure ExpressRoute na platformie Azure. |
| Wielodostępne Azure stosu, wdrożenie w sieci intranet | Sieci VPN typu lokacja lokacja | Ruch z sieci wirtualnej dzierżawcy do lokalizacji docelowej jest bezpieczna. | Przepustowość jest ograniczona przez tunel VPN lokacja lokacja.<br><br>Wymaga bramy sieci wirtualnej i urządzenia sieci VPN w sieci docelowej. | Musi być zabezpieczona przedsiębiorstwach, gdzie niektóre dzierżawy ruchu od pozostałych dzierżawców. |
| Pojedynczy dzierżawy Azure stosu, internet wdrożenia | NAT ruchu wychodzącego | Lepszą przepustowość transferów szybsze. | Ruch nie jest szyfrowany. Brak izolacji i szyfrowania poza TOR. | Hosting scenariuszy, w którym dzierżawcy pobiera własnych wdrożenia stosu Azure i obwodu dedykowanego w środowisku Azure stosu. Na przykład ExpressRoute, jak i przełączania etykiety Multiprotocol (MPLS).
| Wielodostępne Azure stosu, internet wdrożenia | Sieci VPN typu lokacja lokacja | Ruch z sieci wirtualnej dzierżawcy do lokalizacji docelowej jest bezpieczna. | Przepustowość jest ograniczona przez tunel VPN lokacja lokacja.<br><br>Wymaga bramy sieci wirtualnej i urządzenia sieci VPN w sieci docelowej. | Scenariuszach, w którym chce oferują chmury wielodostępne dostawcy hostingu, gdzie dzierżawcy nie ufa siebie i ruchu muszą być szyfrowane.
|  |  |  |  |  |

### <a name="using-expressroute"></a>Przy użyciu usługi ExpressRoute

Możesz połączyć stosu Azure na platformie Azure za pośrednictwem [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) dla pojedynczej dzierżawy w sieci intranet i scenariuszy z wieloma dzierżawcami. Będziesz potrzebować elastycznie obwodu ExpressRoute za pośrednictwem [dostawca połączenia](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Na poniższym diagramie przedstawiono ExpressRoute dla scenariusza pojedynczej dzierżawy (gdzie "Połączenie klienta" jest obwodu ExpressRoute).

![Diagram przedstawiający pojedynczej dzierżawy usługi ExpressRoute scenariusza](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

Na poniższym diagramie przedstawiono ExpressRoute dla scenariusza wielodostępnej.

![Diagram przedstawiający wielodostępne ExpressRoute scenariusza](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Monitorowania zewnętrznych
Aby uzyskać pojedynczego widoku wszystkie alerty z urządzeń i wdrożenia stosu Azure i integrowania alerty istniejących IT usługi przepływu pracy zarządzania dla biletów, można [integracji stosu Azure z zewnętrznego datacenter monitorowanie rozwiązań](azure-stack-integrate-monitor.md).

Dołączone do rozwiązania Azure stosu, host cyklu życia sprzętu jest komputera znajdującego się poza stosu Azure, który uruchamia narzędzia do zarządzania dostarczonym przez producenta OEM dla sprzętu. Można użyć tych narzędzi lub innych rozwiązań, które bezpośrednio zintegrować z istniejącymi rozwiązaniami monitorowania w centrum danych.

W poniższej tabeli przedstawiono listę dostępnych opcji.

| Obszar | Rozwiązanie monitorowania zewnętrznych |
| -- | -- |
| Azure stos oprogramowania | [Pakiet administracyjny stosu Azure dla programu Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Wtyczka Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>Wywołania API opartego na interfejsie REST | 
| Serwery fizyczne (bmc za pomocą interfejsu IPMI) | Sprzęt OEM — pakiet administracyjny programu Operations Manager dostawcy<br>Rozwiązanie dostarczonym sprzętowe przez producenta OEM<br>Dostawca sprzętu wtyczek Nagios | Obsługiwany przez producentów OEM partnera — monitorowanie rozwiązania (włączone) | 
| Urządzenia sieciowe (SNMP) | Odnajdywanie urządzeń sieciowych programu Operations Manager<br>Rozwiązanie dostarczonym sprzętowe przez producenta OEM<br>Wtyczka przełącznika Nagios |
| Monitorowanie kondycji subskrypcji dzierżawcy | [Pakiet administracyjny programu System Center dla systemu Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Należy uwzględnić następujące wymagania:
- Rozwiązanie, którego używasz, musi być bez wykorzystania agentów. Nie można zainstalować agentów innych firm wewnątrz składniki stosu Azure. 
- Jeśli chcesz użyć programu System Center Operations Manager, Operations Manager 2012 R2 lub Operations Manager 2016 jest wymagany.

## <a name="backup-and-disaster-recovery"></a>Kopia zapasowa i odzyskiwanie po awarii

Planowanie tworzenia kopii zapasowej i odzyskiwanie po awarii obejmuje planowanie zarówno podstawowej stosu Azure infrastrukturą służącą do hostowania maszyn wirtualnych IaaS i PaaS usług i aplikacji dzierżawy i danych. Należy zaplanować je oddzielnie.

### <a name="protect-infrastructure-components"></a>Ochrona składników infrastruktury

Możesz [kopia zapasowa Azure stosu](azure-stack-backup-back-up-azure-stack.md) składników infrastruktury dla protokołu SMB udziału można określić:

- Będziesz potrzebować zewnętrznych udziału plików SMB na istniejącym serwerze plików z systemem Windows lub urządzenia innych firm.
- Należy używać tego samego udziału kopii zapasowych przełączników sieciowych i sprzętowych host cyklu życia. Dostawca sprzętu OEM pomoże zawierają wskazówki dotyczące tworzenia kopii zapasowej i przywracania tych składników, są one zewnętrznego stosu Azure. Możesz teraz odpowiada za uruchamianie tworzenia kopii zapasowej przepływy pracy na podstawie zalecenia producenta OEM.

Jeśli dojdzie do utraty danych w wyniku katastrofy, możesz użyć kopii zapasowej infrastruktury ponowne umieszczanie wdrożenia danych, takich jak wdrożenia danych wejściowych oraz identyfikatorów kont usług, a certyfikat główny urzędu certyfikacji, zasobów federacyjnych (w przypadku wdrożeń rozłączona), planów, oferty, Subskrypcje, przydziały RBAC roli zasad i przypisań i kluczy tajnych magazynu kluczy.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Ochrona aplikacji dzierżawy w przypadku maszyn wirtualnych IaaS

Azure stosu nie kopii dzierżawy aplikacji oraz danych. Należy zaplanować ochrony odzyskiwania kopii zapasowych i odzyskiwaniem po awarii do obiektu docelowego zewnętrznego stosu Azure. Ochrona dzierżawy jest czynnością oparte na dzierżawy. W przypadku maszyn wirtualnych IaaS dzierżawcy mogą używać technologii gościa do ochrony plików, folderów, stanu systemu oraz danych aplikacji. Jednak jako dostawcę enterprise lub usługi, może być konieczne stanowią rozwiązanie tworzenia kopii zapasowych i odzyskiwania w tym samym centrum danych lub zewnętrznie w chmurze.

Do tworzenia kopii zapasowych maszyn wirtualnych systemu Linux lub Windows IaaS, należy użyć kopii zapasowej produkty z dostępu do systemu operacyjnego gościa do ochrony pliku, folderu, stan systemu operacyjnego i danych aplikacji. Kopia zapasowa Azure, System Center Data Center Protection Manager, można użyć lub obsługiwane produkty innych firm.

Replikowanie danych w dodatkowej lokalizacji i organizowania aplikacji w tryb failover w przypadku wystąpienia awarii, można użyć usługi Azure Site Recovery lub obsługiwanych produkty innych firm. (W początkowej wersji systemów zintegrowane usługi Azure Site Recovery nie obsługuje powrót po awarii. Jednak można osiągnąć powrotu po awarii w procesie ręcznego.) Ponadto aplikacje, które obsługują macierzysty replikacji (takich jak Microsoft SQL Server) może replikować danych do innej lokalizacji, w którym jest uruchomiona aplikacja.

> [!IMPORTANT]
> W początkowej wersji systemów zintegrowane firma Microsoft będzie obsługiwać technologii ochrony, które działają na poziomie gościa maszyny wirtualnej IaaS. Nie można zainstalować agentów na serwerach infrastruktury podstawowej.

## <a name="learn-more"></a>Dowiedz się więcej

- Informacje o przypadki użycia, zakupu partnerami i dostawcami sprzętu OEM, zobacz [stosu Azure](https://azure.microsoft.com/overview/azure-stack/) stronę produktu.
- Informacji o plan i dostępności geograficznie stosu Azure zintegrowanych systemów, zobacz oficjalny dokument: [stosu Azure: rozszerzenie Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Kolejne kroki
[Modele połączenia wdrażania w usłudze Azure stosu](azure-stack-connection-models.md)