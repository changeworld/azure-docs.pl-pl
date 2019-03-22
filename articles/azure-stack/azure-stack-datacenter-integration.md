---
title: Integracja datacenter ogólne informacje dotyczące usługi Azure Stack zintegrowane systemy | Dokumentacja firmy Microsoft
description: Dowiedz się, co można zrobić, aby teraz zaplanowanie i przygotowanie się do integracji centrum danych z wieloma węzłami usługi Azure Stack.
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
ms.date: 03/14/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2018
ms.openlocfilehash: 1c21cc778ed398c28077ef1a20ebbb7b295c2baa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012388"
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Integracja z centrum danych informacje dotyczące usługi Azure Stack zintegrowane systemy
Jeśli interesuje Cię to system zintegrowany z usługi Azure Stack, należy zrozumieć główne zagadnienia dotyczące planowania wdrożenia i jak system znajdzie się w centrum danych. Ten artykuł zawiera ogólne omówienie tych zagadnień, aby łatwiej podejmować decyzje dotyczące infrastruktury ważne w systemie Azure Stack wieloma węzłami. Zrozumienie tych zagadnień pomaga podczas pracy z dostawcą sprzętu OEM, zgodnie z ich wdrażanie usługi Azure Stack w swoim centrum danych.  

> [!NOTE]
> Systemy wielowęzłowego usługi Azure Stack można kupić tylko od dostawców autoryzowanym sprzętem. 

Aby wdrożyć usługę Azure Stack, należy podać informacje dotyczące planowania do dostawcy rozwiązań, przed rozpoczęciem wdrażania ułatwić proces szybko i sprawnie. Informacje wymagane zakresów adresów w sieci, zabezpieczeń i informacje o tożsamościach z wielu ważnych decyzji, które mogą wymagać wiedzy z wielu różnych obszarów, jak i osób podejmujących decyzje. W związku z tym trzeba będzie ściągać osoby z wielu zespołów w organizacji, aby upewnić się, że masz wszystkie wymagane informacje, przed rozpoczęciem wdrażania. Może to pomóc na komunikowanie się z dostawcą sprzętu podczas zbierania tych informacji, o których skończona, gdyż może być przydatne do podejmowania decyzji usługi doradztwa.

Podczas badania i zbierania wymaganych informacji, może być konieczne pewnych zmian konfiguracji przed wdrożeniem w środowisku sieciowym. Może to obejmować rezerwacji przestrzeni adresów IP dla rozwiązania usługi Azure Stack konfigurowanie Twojego routery, przełączniki i zapory, aby przygotować się do łączności do nowych przełączników rozwiązania usługi Azure Stack. Upewnij się, że masz obszar podmiotu eksperta wyrównane do pomagają w planowaniu.

## <a name="capacity-planning-considerations"></a>Zagadnienia dotyczące planowania pojemności
Podczas oceny usługi Azure Stack rozwiązanie służące do pozyskiwania, opcje konfiguracji sprzętu należy które mają bezpośredni wpływ na ogólną pojemność swoje rozwiązanie usługi Azure Stack. Obejmują one klasycznego opcje procesora CPU, pamięci gęstości, konfiguracji magazynu i ogólne rozwiązanie skali (np. liczby serwerów). W przeciwieństwie do rozwiązania tradycyjną wirtualizację proste operacje arytmetyczne na tych składników w celu ustalenia pojemności do wykorzystania nie ma zastosowania. Pierwszym powodem jest to, że usługi Azure Stack została zaprojektowana do obsługi składników infrastruktury ani zarządzania w obrębie samego rozwiązania. Drugi przyczyna jest część pojemności tego rozwiązania jest zarezerwowana w odniesieniu do odporności; Aktualizowanie oprogramowania rozwiązania w taki sposób, aby zminimalizować zakłócenia w działaniu obciążenia dzierżaw. 

[Arkusz kalkulacyjny planowania pojemności usługi Azure Stack](https://aka.ms/azstackcapacityplanner) pomaga wprowadzeniu poinformowany decyzje w odniesieniu do planowania pojemności na dwa sposoby: albo wybierając ofertę sprzętu i próby dopasowania połączonych zasobów lub, definiując Obciążenie Azure Stack jest przeznaczony do uruchamiania do wyświetlenia sprzętu dostępnych jednostek SKU, które może obsługiwać go. Na koniec arkusza kalkulacyjnego jest przeznaczony jako wskazówki pomagające w podejmowaniu decyzji związanych z usługi Azure Stack planowania i konfiguracji. 

Arkusz kalkulacyjny nie mają służyć jako substytut dla własnych analizy i analizy.  Firma Microsoft udziela się ani nie udziela gwarancji, jawnych ani dorozumianych, w związku z informacjami przedstawionymi w arkuszu kalkulacyjnym.



## <a name="management-considerations"></a>Zagadnienia dotyczące zarządzania
Usługa Azure Stack jest zapieczętowany systemu, gdy infrastruktura jest zablokowana zarówno z poziomu uprawnień i sieciowych perspektywy. Listy kontroli dostępu do sieci (ACL) są stosowane do blokowania całego ruchu przychodzącego do nieautoryzowanego i wszystkie zbędne komunikacji między składnikami infrastruktury. Utrudnia to nieautoryzowanym użytkownikom dostęp do tego systemu.

Bieżące zarządzanie i operacje jest bez dostępu administratora bez ograniczeń infrastruktury. Operatorzy usługi Azure Stack muszą zarządzać systemu za pośrednictwem portalu administratora lub za pośrednictwem usługi Azure Resource Manager (za pośrednictwem programu PowerShell lub interfejsu API REST). Brak dostępu do systemu narzędzia do zarządzania, takich jak Menedżer funkcji Hyper-V lub Menedżera klastra trybu Failover. Aby chronić system, nie można zainstalować oprogramowania innych firm (np. agentów), wewnątrz składniki infrastruktury usługi Azure Stack. Współdziałanie z zewnętrznego oprogramowania do zarządzania i zabezpieczeń występuje za pomocą programu PowerShell lub interfejsu API REST.

Gdy wyższy poziom dostępu jest potrzebny do rozwiązywania problemów, które nie są rozpoznawane za pomocą alertów pośrednictwa kroki, należy skontaktować się z Microsoft Support. Dzięki obsłudze ma metodę, aby zapewnić tymczasowe pełne uprawnienia dostępu administratora do systemu, aby wykonywać bardziej zaawansowane operacje. 

## <a name="identity-considerations"></a>Zagadnienia dotyczące tożsamości

### <a name="choose-identity-provider"></a>Wybierz dostawcę tożsamości
Należy wziąć pod uwagę którego dostawcy tożsamości, którego chcesz użyć dla wdrożenia usługi Azure Stack, Azure AD lub AD FS. Nie można przełączyć dostawców tożsamości, po wdrożeniu bez ponownego wdrażania pełnej wersji systemu. Jeśli nie jesteś właścicielem konta usługi Azure AD i korzystania z konta dostarczonego przez dostawcę usługi w chmurze, a jeśli zdecydujesz się przełączyć dostawcę i używać różnych usługi Azure AD konto, w tym momencie należy skontaktować się z dostawcą rozwiązań do ponownego wdrożenia rozwiązania f lub na koszt.

Wybranego dostawcy tożsamości nie ma żadnego wpływu na maszyny wirtualne dzierżawcy, system obsługi tożsamości i kont, których używają, czy mogły one dołączyć do domeny usługi Active Directory itp. Jest to oddzielne.

Dowiedz się więcej na temat wybierania dostawcy tożsamości w [artykułu modeli połączenie zintegrowane systemy usługi Azure Stack](./azure-stack-connection-models.md).

### <a name="ad-fs-and-graph-integration"></a>Integracja usług AD FS i wykres
Jeśli zdecydujesz się wdrożyć usługę Azure Stack przy użyciu usług AD FS jako dostawcy tożsamości, możesz zintegrować wystąpienia usług AD FS w usłudze Azure Stack przy użyciu istniejącego wystąpienia usług AD FS za pomocą zaufania federacyjnego. Dzięki temu tożsamości w istniejącym lesie usługi Active Directory do uwierzytelniania przy użyciu zasobów w usłudze Azure Stack.

Usługa programu Graph w usłudze Azure Stack można również zintegrować z istniejącą usługą Active Directory. Pozwala na zarządzanie opartej na rolach kontrola dostępu (RBAC) w usłudze Azure Stack. Dostęp do zasobu jest delegowane, składnik programu Graph wyszukuje konta użytkownika w istniejącym lesie usługi Active Directory przy użyciu protokołu LDAP.

Na poniższym diagramie przedstawiono przepływ ruchu zintegrowanych usług AD FS i wykres.
![Diagram przedstawiający przepływ ruchu usług AD FS i wykres](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Model licencjonowania
Należy zdecydować, które modelu licencjonowania, którego chcesz użyć. Dostępne opcje zależą od tego, czy wdrożyć połączony z Internetem w usłudze Azure Stack:
- Dla [połączone wdrożenia](azure-stack-connected-deployment.md), możesz wybrać płatność za użycie lub oparty na pojemności licencji. Płatność za użycie wymaga połączenia do platformy Azure do użycia raportów, które następnie będą rozliczane za pośrednictwem platformy Azure commerce. 
- Licencjonowanie tylko na podstawie pojemności jest obsługiwany, jeśli możesz [wdrażanie odłączony](azure-stack-disconnected-deployment.md) z Internetu. 

Aby uzyskać więcej informacji na temat modeli licencjonowania, zobacz [Microsoft Azure Stack, pakowania i ceny](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Decyzje dotyczące nazewnictwa

Należy zastanowić się, jak chcesz zaplanować przestrzeni nazw usługi Azure Stack, szczególnie nazwa regionu i nazwa domeny zewnętrznej. Zewnętrzne w pełni kwalifikowaną nazwę domeny (FQDN) wdrożenia usługi Azure Stack dla publicznych punktów końcowych jest kombinacja tych dwóch nazw: &lt; *region*&gt;.&lt; *fqdn*&gt;. Na przykład *east.cloud.fabrikam.com*. W tym przykładzie portali usługi Azure Stack będą dostępne w następujących adresów URL:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Nazwa regionu, wybrany dla wdrożenia usługi Azure Stack musi być unikatowa i będą wyświetlane w portalu adresów. 

W poniższej tabeli przedstawiono te decyzje nazewnictwa domeny.

| Name (Nazwa) | Opis | 
| -------- | ------------- | 
|Nazwa regionu | Nazwa pierwszego regionu Azure Stack. Ta nazwa jest używana jako część nazwy FQDN dla publiczne wirtualne adresy IP (VIP), którymi zarządza usługa Azure Stack. Zazwyczaj nazwa regionu powinien być identyfikator lokalizacji fizycznej, takie jak lokalizacja centrum danych.<br><br>Nazwa regionu musi zawierać tylko litery i cyfry od 0 do 9. Znaki specjalne, takie jak "-" lub "#", itp., są dozwolone.| 
| Nazwa domeny zewnętrznej | Nazwa strefy systemu nazw domen (DNS, Domain Name System) dla punktów końcowych z zewnątrz adresów VIP. Używane w pełni kwalifikowaną nazwę domeny dla tych publicznych adresów VIP. | 
| Nazwy prywatnych domeny (wewnętrzny) | Nazwa domeny (i wewnętrznej strefy DNS), utworzone w usłudze Azure Stack dla zarządzania infrastrukturą. 
| | |

## <a name="certificate-requirements"></a>Wymagania dotyczące certyfikatów

W przypadku wdrożenia należy udostępniania certyfikatów Secure Sockets Layer (SSL) dla publicznych punktów końcowych. Na wysokim poziomie certyfikaty mają następujące wymagania:

- Można użyć pojedynczego symbolu wieloznacznego certyfikat lub użyć zestawu dedykowanych certyfikatów i używać symboli wieloznacznych tylko dla punktów końcowych, takich jak storage i usługi Key Vault.
- Certyfikaty mogą być wystawiane przez publiczny zaufanego urzędu certyfikacji (CA) lub urzędu certyfikacji przez klienta.

Aby uzyskać więcej informacji o jakie infrastruktury kluczy publicznych certyfikatów są wymagane do wdrożenia usługi Azure Stack i sposób ich uzyskania, znajduje się [wymagania dotyczące certyfikatów infrastruktury kluczy publicznych usługi Azure Stack](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> Podane informacje o certyfikacie PKI powinny służyć jako ogólne wskazówki. Przed nabyciem wszystkie certyfikaty PKI dla usługi Azure Stack, pracować z partnerem sprzętowe OEM. Zapewniają one co bardziej szczegółowe wskazówki dotyczące certyfikatów i wymagania.


## <a name="time-synchronization"></a>Synchronizacja czasu
Musisz wybrać określony czas, serwera przy użyciu służy do synchronizacji usługi Azure Stack.  Synchronizacja czasu jest mają kluczowe znaczenie dla usługi Azure Stack i jego role infrastruktury, ponieważ jest używany do generowania bilety protokołu Kerberos, które są używane do uwierzytelniania wewnętrznych usług ze sobą.

Należy określić, że adres IP dla serwera synchronizacji czasu, mimo że większość składników infrastruktury może rozpoznać adresu URL, niektóre może obsługiwać tylko adresy IP. Jeśli używasz opcji wdrażania odłączonego należy określić, że serwer czasu w sieci firmowej, którego jesteś można połączyć się z siecią infrastruktury w usłudze Azure Stack.

## <a name="connect-azure-stack-to-azure"></a>Łączenie usługi Azure Stack z platformą Azure

W przypadku scenariuszy chmury hybrydowej należy zaplanować, jak chcesz się połączyć usługę Azure Stack na platformie Azure. Istnieją dwie obsługiwane metody łączenia sieci wirtualnych w usłudze Azure Stack z sieciami wirtualnymi na platformie Azure: 
- **Site-to-site**. Połączenie wirtualnej sieci prywatnej (VPN) za pośrednictwem protokołu IPsec (IKE v1 i IKE v2). Ten typ połączenia wymaga urządzenia sieci VPN lub usługi Routing i dostęp zdalny (RRAS). Aby uzyskać więcej informacji na temat bram sieci VPN na platformie Azure, zobacz [VPN Gateway — informacje](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Komunikacja przez ten tunel jest zaszyfrowany i jest bezpieczna. Jednak przepustowość jest ograniczona przez maksymalną przepływność tuneli (100 – 200 MB/s).
- **NAT dla ruchu wychodzącego**. Domyślnie wszystkie maszyny wirtualne w usłudze Azure Stack będą mieć łączność z sieciami zewnętrznymi za pośrednictwem z wychodzących reguł NAT. Każda sieć wirtualna, który jest tworzony w usłudze Azure Stack pobiera publiczny adres IP przypisany do niego. Czy maszyna wirtualna jest bezpośrednio przypisany publiczny adres IP lub jest za modułem równoważenia obciążenia z publicznym adresem IP, będzie miał dostęp ruchu wychodzącego za pośrednictwem NAT dla ruchu wychodzącego, korzystając z adresu VIP w sieci wirtualnej. Działa to tylko do komunikacji, który jest inicjowane przez maszynę wirtualną i skierowany do sieci zewnętrznej (internet lub intranet). Nie można nawiązać połączenia z maszyną wirtualną spoza.

### <a name="hybrid-connectivity-options"></a>Opcjom łączności hybrydowej

Potrzeby łączności hybrydowej należy wziąć pod uwagę rodzaj wdrożenia mają być oferowane i którym zostanie wdrożony. Należy wziąć pod uwagę, czy trzeba izolować ruch sieciowy na dzierżawę i tego, czy będziesz mieć wdrożenia intranetu lub Internetu.

- **Pojedynczej dzierżawy usługi Azure Stack**. Wdrożenie usługi Azure Stack, które wydaje się, co najmniej z punktu widzenia sieci, tak jak w przypadku jednej dzierżawy. Może być dzierżawy wiele subskrypcji, ale jak intranetową usługę, cały ruch sieciowy przesyłany za pośrednictwem tej samej sieci. Ruch sieciowy z jedną subskrypcją sieciowy przesyłany za pośrednictwem tego samego połączenia sieciowego w postaci innej subskrypcji i nie muszą być izolowane przez szyfrowany tunel.

- **Usługi Azure Stack w wielodostępnym**. Wdrożenia usługi Azure Stack, gdzie ruch każdej subskrypcji dzierżawcy, który jest powiązany dla sieci, które są zewnętrzne w stosunku do usługi Azure Stack musi być odizolowany od ruchu sieciowego innych dzierżaw.
 
- **Wdrażanie sieci intranet**. Wdrażanie usługi Azure Stack, który znajduje się w firmowej sieci intranet, zwykle na przestrzeń prywatnych adresów IP i za zaporami co najmniej jeden. Publiczne adresy IP nie są naprawdę publicznych, ponieważ nie może być kierowany bezpośrednio przez publiczny internet.

- **Wdrożenie internetowe**. Wdrożenia usługi Azure Stack, który jest podłączony do publicznego Internetu i używa routingu internetowego publiczne adresy IP dla publicznej zakresu adresów VIP. Wdrożenie nadal może się znajdować się za zaporą, ale publiczny zakres adresów VIP jest bezpośrednio osiągalny z publicznego Internetu i platformy Azure.
 
Poniższa tabela zawiera podsumowanie hybrydowe scenariusze łączności, z informatykami, wadach i przypadkami użycia.

| Scenariusz | Metoda połączenia | Specjaliści | Wady | Dobre dla |
| -- | -- | --| -- | --|
| Pojedynczy dzierżawy usługi Azure Stack, wdrożenie w sieci intranet | NAT dla ruchu wychodzącego | Lepszą przepustowość na potrzeby szybszych transferów. Prosta implementacja; Brak bramy nie jest wymagane. | Ruch nie jest zaszyfrowany; Brak izolacji i szyfrowania poza stosem. | Gdzie są równie zaufanych wszystkich dzierżaw wdrożeń w przedsiębiorstwach.<br><br>Przedsiębiorstw, które mają obwodu usługi ExpressRoute systemu Azure do platformy Azure. |
| Wielu dzierżaw usługi Azure Stack, wdrożenie w sieci intranet | Sieci VPN typu lokacja lokacja | Ruch z sieci wirtualnej dzierżawcy do lokalizacji docelowej jest bezpieczna. | Przepustowość jest ograniczona przez tunel sieci VPN typu lokacja lokacja.<br><br>Wymaga bramy sieci wirtualnej i urządzeniem sieci VPN w sieci docelowej. | Gdy niektóre ruch dzierżawców wdrożeń w przedsiębiorstwach musi zostać zabezpieczony z innych dzierżaw. |
| Pojedynczy dzierżawy usługi Azure Stack, wdrożenia internetowego | NAT dla ruchu wychodzącego | Lepszą przepustowość na potrzeby szybszych transferów. | Ruch nie jest zaszyfrowany; Brak izolacji i szyfrowania poza stosem. | Hosting scenariuszy, w której dzierżawa pobiera własne wdrożenia usługi Azure Stack i obwodu dedykowanego środowiska usługi Azure Stack. Na przykład usługi ExpressRoute i przełączanie etykiety wieloprotokołowym (MPLS).
| Wielu dzierżaw usługi Azure Stack, wdrożenia internetowego | Sieci VPN typu lokacja lokacja | Ruch z sieci wirtualnej dzierżawcy do lokalizacji docelowej jest bezpieczna. | Przepustowość jest ograniczona przez tunel sieci VPN typu lokacja lokacja.<br><br>Wymaga bramy sieci wirtualnej i urządzeniem sieci VPN w sieci docelowej. | Scenariuszach, w którym chce oferty z wielodostępnej chmury dostawcy hostingu, gdzie dzierżawcy nie ufa siebie nawzajem i ruch sieciowy musi być szyfrowana.
|  |  |  |  |  |

### <a name="using-expressroute"></a>Przy użyciu usługi ExpressRoute

Możesz nawiązać połączenie usługi Azure Stack platformy Azure za pośrednictwem [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) dla jednej dzierżawy w sieci intranet i scenariuszy z wieloma dzierżawami. Musisz mieć aprowizowane obwód usługi ExpressRoute za pośrednictwem [dostawcy łączności](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Na poniższym diagramie przedstawiono usługi ExpressRoute dla scenariusza pojedynczej dzierżawy (gdzie "Połączenie klienta" to obwód usługi ExpressRoute).

![Diagram przedstawiający pojedynczej dzierżawy usługi ExpressRoute scenariusza](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

Na poniższym diagramie przedstawiono usługi ExpressRoute dla scenariusza z wieloma dzierżawcami.

![Diagram przedstawiający scenariusza usługi ExpressRoute z wieloma dzierżawcami](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Monitorowanie zewnętrznych
Można uzyskać pojedynczy widok wszystkich alertów z wdrożenia usługi Azure Stack i urządzeń oraz Integruj alerty do istniejących IT service management przepływów pracy dla tworzenia biletów, można [Zintegruj usługę Azure Stack przy użyciu zewnętrznego monitorowania rozwiązańCentrumdanych](azure-stack-integrate-monitor.md).

Dołączone do rozwiązania usługi Azure Stack, host cyklu życia sprzętu jest komputera znajdującego się poza systemem OEM narzędzia do zarządzania udostępnionych przez dostawcę sprzętu usługi Azure Stack. Można użyć tych narzędzi ani innych rozwiązań, które integrują się bezpośrednio z istniejącymi rozwiązaniami monitorowania w centrum danych.

W poniższej tabeli przedstawiono listę dostępnych opcji.

| Obszar | Zewnętrzne rozwiązania do monitorowania |
| -- | -- |
| Usługa Azure Stack oprogramowania | [Usługa Azure Stack pakiet administracyjny programu Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Wtyczka Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>Wywołania interfejsu API opartego na protokole REST | 
| Serwery fizyczne (kontrolery bmc za pośrednictwem interfejsu IPMI) | Sprzęt OEM — pakiet administracyjny programu Operations Manager dostawcy<br>Rozwiązanie udostępnionych przez dostawcę sprzętu producenta OEM<br>Dostawca sprzętu Nagios wtyczek.<br>Obsługiwany przez producentów OEM partnera — rozwiązanie (dołączony) do monitorowania | 
| Urządzenia sieciowe (SNMP) | Odnajdywanie urządzeń sieciowych programu Operations Manager<br>Rozwiązanie udostępnionych przez dostawcę sprzętu producenta OEM<br>Nagios przełączanie wtyczki |
| Monitorowanie kondycji subskrypcji dzierżawcy | [Pakiet administracyjny programu System Center dla systemu Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Należy uwzględnić następujące wymagania:
- Rozwiązania, którego używasz, musi być bez wykorzystania agentów. Nie można zainstalować agentów firm wewnątrz składników usługi Azure Stack. 
- Jeśli chcesz użyć programu System Center Operations Manager, Operations Manager 2012 R2 lub Operations Manager 2016 jest wymagany.

## <a name="backup-and-disaster-recovery"></a>Kopia zapasowa i odzyskiwanie po awarii

Planowanie kopii zapasowych i odzyskiwania po awarii obejmuje planowanie zarówno podstawowej usługi Azure Stack infrastrukturą służącą do hostowania maszyn wirtualnych IaaS i PaaS, usług i aplikacji dzierżawy i danych. Należy zaplanować je oddzielnie.

### <a name="protect-infrastructure-components"></a>Ochrona składników infrastruktury

Możesz [wykonywanie kopii zapasowych usługi Azure Stack](azure-stack-backup-back-up-azure-stack.md) składników infrastruktury dla protokołu SMB, udostępnianie, należy określić:

- Potrzebna będzie zewnętrznych udziału plików SMB na istniejącym serwerze plików z systemem Windows lub urządzeń innych firm.
- Należy użyć tego samego udziału kopii zapasowej przełączników sieciowych i sprzętu hosta cyklu życia. Z dostawcą sprzętu OEM pomoże zapewnić wskazówki dotyczące tworzenia kopii zapasowych i przywracania te składniki są one zewnętrznego do usługi Azure Stack. Jesteś odpowiedzialny za działanie kopii zapasowej przepływów pracy, oparta na zaleceniach producenta OEM.

Jeśli dojdzie do utraty danych krytycznego, można użyć kopii zapasowej infrastruktury do reseed wdrożenia danych, takich jak wdrożenia danych wejściowych oraz identyfikatorów kont usług, certyfikat główny urzędu certyfikacji, zasobów federacyjnych (w przypadku wdrożeń o odłączony), plany, oferty, Subskrypcje, limity przydziału, przypisania zasad i rolę RBAC i wpisy tajne usługi Key Vault.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Ochrona aplikacji dzierżawy na maszynach wirtualnych IaaS

Usługa Azure Stack nie należy wykonywać kopii zapasowej dzierżawy aplikacji i danych. Należy zaplanować kopia zapasowa i odzyskiwanie po awarii ochrony na potrzeby odzyskiwania elementu docelowego zewnętrznego do usługi Azure Stack. Ochrona dzierżawy jest czynnością oparte na dzierżawę. W przypadku maszyn wirtualnych IaaS dzierżawcy mogą używać technologii gościa do ochrony plików, folderów, danych aplikacji i stanu systemu. Jednak jako dostawca usług lub enterprise można oferować rozwiązanie kopii zapasowych i odzyskiwania w jednym centrum danych lub zewnętrznie w chmurze.

Do tworzenia kopii zapasowych maszyn wirtualnych systemu Linux lub Windows IaaS, musisz podać kopii zapasowej produktów dzięki dostępowi do systemu operacyjnego gościa do ochrony pliku, folderu, stan systemu operacyjnego i danych aplikacji. Za pomocą usługi Azure Backup, System Center Data Center Protection Manager, lub obsługiwanych produktów innych firm.

Do replikowania danych do lokalizacji dodatkowej i organizowanie aplikacji w tryb failover w przypadku poważnej awarii, można użyć usługi Azure Site Recovery lub obsługiwanych produktów innych firm. Ponadto aplikacje, które obsługują natywnych replikacji, takich jak Microsoft SQL Server, można replikować dane do innej lokalizacji, w którym aplikacja jest uruchomiona.

## <a name="learn-more"></a>Dowiedz się więcej

- Aby uzyskać informacji dotyczących przypadków użycia, zakupów, partnerami i dostawcami sprzętu OEM, zobacz [usługi Azure Stack](https://azure.microsoft.com/overview/azure-stack/) stronę produktu.
- Uzyskać informacje na temat planu i udostępnienia georegionu dla usługi Azure Stack zintegrowanych systemów, zobacz oficjalny dokument: [Azure Stack: Rozszerzenie platformy Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Kolejne kroki
[Modele połączenia wdrażania w usłudze Azure Stack](azure-stack-connection-models.md)
