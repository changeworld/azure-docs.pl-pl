---
title: Zabezpieczeń platformy Azure i plan zgodności - automatyzacji aplikacji sieci Web FedRAMP
description: Zabezpieczeń platformy Azure i plan zgodności - automatyzacji aplikacji sieci Web FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 10ed297180f68fcaf006f2778990879be02f994d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31418934"
---
# <a name="azure-security-and-compliance-blueprint---fedramp-web-applications-automation"></a>Zabezpieczeń platformy Azure i plan zgodności - automatyzacji aplikacji sieci Web FedRAMP

## <a name="overview"></a>Przegląd

[Federalne ryzyka i Program do zarządzania autoryzacji (FedRAMP)](https://www.fedramp.gov), to program całej dla instytucji rządowych USA, który zawiera standardowe podejście do oceny zabezpieczeń, autoryzacji i ciągłego monitorowania produktów chmury i usługi. Ten Azure zabezpieczeń i zgodności plan automatyzacji zawiera wskazówki dotyczące wdrożenia FedRAMP zgodności infrastrukturę jako usługę (IaaS) środowiska odpowiednie dla prostej aplikacji sieci web skierowane do Internetu. To rozwiązanie pozwala zautomatyzować, wdrażanie i konfigurowanie zasobów Azure dla typowych architektura referencyjna prezentacja sposoby, w którym klienci mogą spełnić szczególne wymagania dotyczące zabezpieczeń i zgodności i służy jako podstawę do klientów do tworzenia i Skonfiguruj swoje własne rozwiązania na platformie Azure. Rozwiązanie implementuje podzbiór formantów z linii bazowej wysokiej FedRAMP, oparte na SP NIST 800-53. Aby uzyskać więcej informacji na temat wymagań FedRAMP wysokiej i to rozwiązanie, zobacz [FedRAMP wysokiego — omówienie wysokiego szczebla](fedramp-controls-overview.md). ***Uwaga: To rozwiązanie wdraża Azure dla instytucji rządowych.***

Taka architektura ma służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym. Wdrażanie aplikacji w tym środowisku bez żadnych modyfikacji nie wystarcza do całkowicie spełniają wymagania wysokiej FedRAMP linii bazowej. Pamiętaj o następujących kwestiach:
- Taka architektura umożliwia linii bazowej, aby ułatwić klientom używać platformy Azure w sposób zgodny z FedRAMP.
- Klienci są zobowiązani do przeprowadzania odpowiednich ustawień zabezpieczeń i zgodności oceny dowolnego rozwiązania utworzone przy użyciu tej architektury, ponieważ wymagania mogą się różnić oparte na szczegółowe informacje na temat wdrażania poszczególnych klientów. 

Aby uzyskać szybki przegląd sposobu działania tego rozwiązania, obejrzyj ten [wideo](https://aka.ms/fedrampblueprintvideo) wyjaśniający i prezentowania jego wdrażania.

Kliknij przycisk [tutaj](https://aka.ms/fedrampblueprintrepo) instrukcje wdrożenia.

## <a name="solution-components"></a>Składniki rozwiązania

Ten Azure zabezpieczeń i zgodności plan automatyzacji automatycznie wdraża IaaS architekturę odwołania aplikacji sieci web z wstępnie skonfigurowane zabezpieczenia, aby ułatwić klientom zapewnienia zgodności z wymaganiami FedRAMP. Rozwiązania składa się z szablonów usługi Azure Resource Manager i skryptów programu PowerShell, które przewodnik dotyczący wdrażania zasobów i konfiguracji. Towarzyszące [dokumentacji zgodności](#compliance-documentation) podano wskazujący dziedziczenia kontroli zabezpieczeń z platformy Azure i wdrożonych zasobów i konfiguracje, które są wyrównane z NIST SP kontroli bezpieczeństwa 800 53, udostępniając organizacje mogą szybka ścieżka zgodności.

## <a name="architecture-diagram"></a>Diagram architektury

To rozwiązanie wdraża architektura referencyjna dla aplikacji sieci web IaaS z wewnętrznej bazie danych. Architektura obejmują warstwa sieci web, dane warstwy infrastruktura usługi Active Directory, bramy aplikacji i usługi równoważenia obciążenia. Maszyny wirtualne wdrażane warstwy web i danych są skonfigurowane w zestawie dostępności i wystąpień programu SQL Server są konfigurowane w zawsze włączonej grupy dostępności, wysokiej dostępności. Maszyny wirtualne są przyłączone do domeny, a zasady grupy usługi Active Directory są używane do wymuszania konfiguracji zgodności i zabezpieczeń na poziomie systemu operacyjnego. Jumpbox zarządzania (host bastionu) zapewnia bezpieczne połączenie, Administratorzy mogą wdrożyć dostęp do zasobów.

![tekst alternatywny](images/fedramp-architectural-diagram.png?raw=true "zabezpieczeń platformy Azure i plan zgodności - automatyzacji aplikacji sieci Web FedRAMP")

To rozwiązanie wymaga następujących usług platformy Azure. Szczegółowe informacje o architekturze wdrażania znajdują się w [wdrożenia architecture](#deployment-architecture) sekcji.

* **Azure Virtual Machines**
    - (1) zarządzania/bastionu (Windows Server 2016 Datacenter)
    - (2) usługi active Directory kontrolera domeny (z systemem Windows Server Datacenter 2016)
    - (2) węzeł klastra programu SQL Server (SQL Server 2016 w systemie Windows Server 2012 R2)
    - (1) Monitor programu SQL Server (Windows Server 2016 Datacenter)
    - (2) w sieci web/usług IIS (Windows Server 2016 Datacenter)
* **Zestawy dostępności**
    - (1) kontrolery domeny active Directory
    - (1) węzły klastra SQL i Monitor
    - (1) w sieci web/usług IIS
* **Azure Virtual Network**
    - ((1) / 16 do / sieci wirtualnych
    - podsieci o prefiksie/24 (5)
    - Ustawienia DNS są skonfigurowane do obu kontrolerów domeny
* **Azure Load Balancer**
    - (1) SQL moduł równoważenia obciążenia
* **Brama aplikacji Azure**
    - (1) brama aplikacji zapory aplikacji sieci Web jest włączona
      - Tryb zapory: zapobieganie
      - Zestaw reguł: OWASP 3.0
      - Odbiornika: Port 443
* **Azure Storage**
    - (7) konta magazynu geograficznie nadmiarowego
* **Azure Backup**
    - (1) Magazyn usług odzyskiwania
* **Usługa Azure Key Vault**
    - (1) magazyn kluczy
* **Azure Active Directory**
* **Azure Resource Manager**
* **Program Azure Log Analytics**
    - (1) obszar roboczy analizy dzienników
* **Azure Automation**
    - (1) konto automatyzacji

## <a name="deployment-architecture"></a>Architektura wdrażania

Poniższa sekcja zawiera szczegóły dotyczące projektowania i wdrażania elementów.

### <a name="network-segmentation-and-security"></a>W przypadku segmentacji sieci i zabezpieczeń

#### <a name="application-gateway"></a>Application Gateway

Architektura zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji z zapory aplikacji sieci web (WAF) i zestaw reguł OWASP włączone. Dodatkowe funkcje obejmują:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Włącz [odciążanie protokołu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Wyłącz [TLS 1.0 i 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Zapora aplikacji sieci Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Tryb zapory aplikacji sieci Web)
- [Tryb zapobiegania](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) z ruleset OWASP 3.0

#### <a name="virtual-network"></a>Sieć wirtualna

Architektura definiuje prywatnej sieci wirtualnej z 10.200.0.0/16 przestrzeni adresowej.

#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

To rozwiązanie obejmuje zasoby w architekturze podsieci WWW, podsieci bazy danych, podsieci usługi Active Directory i zarządzania podsieci w sieci wirtualnej. Podsieci są logicznie oddzielone zastosować do poszczególnych podsieci, aby ograniczyć ruch między podsieciami, aby tylko to konieczne do systemu oraz funkcji zarządzania reguły grupy zabezpieczeń sieci.

Zobacz konfiguracji [grup zabezpieczeń sieci](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) wdrożone w tym rozwiązaniu. Organizacje można skonfigurować grupy zabezpieczeń sieci, edytując plik powyżej [tej dokumentacji](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako przewodnika.

Każdą z tych podsieci ma dedykowany sieciowej grupy zabezpieczeń (NSG):
- 1 NSG dla bramy aplikacji (LBNSG)
- 1 NSG dla Jumpbox (MGTNSG)
- 1 NSG dla kontrolerów domeny podstawowego i zapasowego (ADNSG)
- 1 NSG serwerów SQL i monitora udostępniania plików (SQLNSG)
- 1 NSG dla warstwy sieci Web (WEBNSG)

#### <a name="subnets"></a>Podsieci

Każda podsieć jest skojarzona z jej odpowiednie grupy NSG.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane przechowywane przy użyciu kilku środków szyfrowania.

#### <a name="azure-storage"></a>Azure Storage

Aby spełnić wymagania dotyczące szyfrowania danych na rest, użyj wszystkich kont magazynu [szyfrowanie usługi Magazyn](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

#### <a name="sql-database"></a>SQL Database

Baza danych SQL jest skonfigurowana do używania [funkcji przezroczystego szyfrowania danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), który wykonuje w czasie rzeczywistym szyfrowania i odszyfrowywania danych i plików dziennika do ochrony informacji w stanie spoczynku. Funkcji TDE zawiera gwarantują, że przechowywanych danych nie zostało poddane nieautoryzowanego dostępu. 

#### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption

Szyfrowanie dysków Azure umożliwia zaszyfrowanych dysków maszyny wirtualnej systemu Windows IaaS. [Szyfrowanie dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów systemu operacyjnego i dysków z danymi. Rozwiązanie jest zintegrowany z usługą Azure Key Vault, aby kontrolować i zarządzać kluczami szyfrowania dysków.

### <a name="logging-and-auditing"></a>Rejestrowanie i inspekcja

[Zaloguj się Analytics](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) zapewnia szczegółowe rejestrowanie działania systemu i użytkownika, a także kondycji systemu. 

- **Dzienniki aktywności:**[Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacje wykonywane na zasobów w ramach subskrypcji.
- **Dzienniki diagnostyczne:**[dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) są wszystkie dzienniki emitowane przez każdego zasobu. Dzienniki te obejmują dzienniki systemu zdarzeń systemu Windows, dzienniki usługi Azure storage, dzienników inspekcji usługi Key Vault i dzienniki bramy aplikacji dostępu i zapory.
- **Archiwizacja dziennika:** Dzienniki aktywności platformy Azure i dzienników diagnostycznych możliwe połączenie usługi Analiza dzienników Azure do przetwarzania, przechowywania i dashboarding. Przechowywania jest użytkownika do skonfigurowania zapasowej dzień 730 spełnia wymagania dotyczące przechowywania w danej organizacji.

### <a name="secrets-management"></a>Klucze tajne zarządzania

Rozwiązanie używa usługi Azure Key Vault do zarządzania kluczy i kluczy tajnych.

- [Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pomaga w zabezpieczaniu kluczy kryptograficznych i kluczy tajnych używanych przez usługi i aplikacje w chmurze. 
- Rozwiązanie jest zintegrowany z usługą Azure Key Vault, aby zarządzać kluczami szyfrowania dysków maszyn wirtualnych IaaS i kluczy tajnych.

### <a name="identity-management"></a>Zarządzanie tożsamościami

Następujące technologie potwierdzenie tożsamości możliwości zarządzania w środowisku platformy Azure.
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) jest wielodostępne oparte na chmurze katalogami i tożsamościami zarządzania usługi Microsoft.
- Używanie programu Azure AD można wykonać uwierzytelniania do aplikacji sieci web wdrożonych przez klienta. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Azure opartej na rolach kontroli dostępu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umożliwia precyzyjne zarządzanie dostępem ukierunkowanych na platformie Azure. Subskrypcja dostęp jest ograniczony do administratora subskrypcji, a dostęp do zasobów można ograniczyć na podstawie roli użytkownika.
- Wdrożone wystąpienie IaaS usługi Active Directory umożliwia zarządzanie tożsamości na poziomie systemu operacyjnego dla wdrożonych maszyn wirtualnych IaaS.
   
### <a name="compute-resources"></a>Zasoby obliczeniowe

#### <a name="web-tier"></a>Warstwa sieci Web

Maszyny wirtualne warstwy sieci web w wdraża rozwiązanie [zestawu dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Zestawy dostępności upewnij się, że maszyny wirtualne rozproszone na wielu klastrów izolowanego sprzętu, aby zwiększyć dostępność.

#### <a name="database-tier"></a>Warstwy bazy danych

Maszyny wirtualne warstwy bazy danych w zestawie dostępności jako wdraża rozwiązanie [grupy dostępności funkcji AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Zawsze włączone dostępności grupy funkcja zapewnia możliwości wysokiej dostępności i odzyskiwania po awarii. 

#### <a name="active-directory"></a>Usługa Active Directory

Wszystkich maszyn wirtualnych wdrożonych przez rozwiązanie są przyłączone do domeny, a zasady grupy usługi Active Directory są używane do wymuszania konfiguracji zgodności i zabezpieczeń na poziomie systemu operacyjnego. Maszyny wirtualne w usłudze Active Directory są wdrażane w zestawie dostępności.


#### <a name="jumpbox-bastion-host"></a>Jumpbox (bastionu hosta)

Jumpbox zarządzania (host bastionu) zapewnia bezpieczne połączenie, Administratorzy mogą wdrożyć dostęp do zasobów. NSG skojarzoną z podsiecią zarządzania, w którym znajduje się maszyna wirtualna jumpbox zezwala na połączenia tylko na portu 3389 protokołu TCP dla protokołu RDP. 

### <a name="malware-protection"></a>Ochrona przed złośliwym oprogramowaniem

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla maszyn wirtualnych zapewnia możliwość ochrony w czasie rzeczywistym, która pomaga w identyfikacji i usuwania wirusy, programy szpiegujące lub inne złośliwe oprogramowanie, można skonfigurować alerty, gdy znane złośliwego lub niechcianego oprogramowania próbuje zainstalować lub uruchomić na chronionych maszyn wirtualnych.

### <a name="patch-management"></a>Zarządzanie poprawkami

Maszyny wirtualne systemu Windows wdrożone przez Azure zabezpieczeń i zgodności plan automatyzacji są domyślnie skonfigurowane, odbierania automatycznych aktualizacji z usługi Windows Update. To rozwiązanie również wdraża rozwiązanie Automatyzacja Azure za pomocą których można utworzyć wdrożenia aktualizacji do wdrożenia poprawki na serwerach z systemem Windows w razie potrzeby.

### <a name="operations-management"></a>Zarządzanie operacjami

#### <a name="log-analytics"></a>Log Analytics

[Zaloguj się Analytics](https://azure.microsoft.com/services/log-analytics/) to usługa, która umożliwia zbieranie i analiza danych wygenerowanych przez zasobami na platformie Azure i lokalnego środowiska.

#### <a name="management-solutions"></a>Rozwiązania do zarządzania

Następujące rozwiązania do zarządzania są wstępnie zainstalowane w ramach tego rozwiązania:
- [Ocena usługi AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)
- [Ocena oprogramowania chroniącego przed złośliwym kodem](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)
- [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [Zabezpieczenia i inspekcja](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
- [Ocena serwera SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)
- [Zarządzanie aktualizacjami](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)
- [Kondycja agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)
- [Dzienniki aktywności platformy Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)
- [Śledzenie zmian](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)

## <a name="compliance-documentation"></a>Dokumentacja zgodności

### <a name="customer-responsibility-matrix"></a>Macierz odpowiedzialność klienta

[Macierzy obowiązki klienta](https://aka.ms/blueprinthighcrm) (skoroszyt programu Excel) zawiera wszystkie opcje zabezpieczeń wymagane przez wysokiej FedRAMP linii bazowej. Macierz oznacza dla każdego formantu (lub podsekcja sterowania), czy implementacja odpowiedzialne dla formantu jest odpowiedzialność firmy Microsoft, odbiorcy, lub udostępniane między nimi. 

### <a name="control-implementation-matrix"></a>Macierzy implementacji kontroli

[Macierzy implementacji kontroli](https://aka.ms/blueprintwacim) (skoroszyt programu Excel) zawiera wszystkie opcje zabezpieczeń wymagane przez wysokiej FedRAMP linii bazowej. Określa macierzy, dla każdego formantu (lub podsekcja sterowania) wyznaczony klienta odpowiedzialne w macierzy obowiązki klienta, 1) Jeśli automatyzacji planu implementuje formantu i 2) a opis sposobu implementacji wyrównana z zdefiniowanymi formantu. Ta zawartość jest również dostępna [tutaj](fedramp-controls-overview.md).

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

To Azure zabezpieczeń i zgodności plan automatyzacji składa się z pliki konfiguracji w formacie JSON i skryptów programu PowerShell, które są obsługiwane przez usługi interfejsu API usługi Azure Resource Manager do wdrażania zasobów w systemie Azure. Szczegółowe instrukcje są dostępne [tutaj](https://aka.ms/fedrampblueprintrepo). ***Uwaga: To rozwiązanie wdraża Azure dla instytucji rządowych.***

#### <a name="quickstart"></a>Szybki start
1. Klonuj lub pobrać [to](https://aka.ms/fedrampblueprintrepo) repozytorium GitHub, aby na lokalnej stacji roboczej.

2. Uruchom skrypt programu PowerShell przed wdrożeniem: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Kliknij przycisk poniżej, zaloguj się do portalu Azure, wprowadź wymagane parametry szablonu ARM i kliknij przycisk **zakupu**.

    [![Wdrażanie na platformie Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="disclaimer"></a>Zrzeczenie odpowiedzialności

- Ten dokument jest tylko do celów informacyjnych. MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH LUB USTAWOWYCH, ODNOŚNIE DO INFORMACJI W TYM DOKUMENCIE. Niniejszy dokument jest udostępniany "jako — jest." Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i innymi odwołaniami do witryn internetowych, mogą ulec zmianie bez uprzedzenia. Klienci odczytu ten dokument ponosi ryzyko związane z użyciem jej.  
- Ten dokument nie zawiera klientów z żadnych praw do jakiejkolwiek własności intelektualnej w dowolnym produktem firmy Microsoft lub rozwiązania.  
- Klienci mogą kopiować i używać tego dokumentu do celów referencyjnych wewnętrzny.  
- Zastosowanie niektórych zaleceń zamieszczonych w tym dokumencie może spowodować zwiększenie danych, sieci lub użycia zasobów obliczeniowych na platformie Azure i może zwiększyć Azure kosztów licencji lub subskrypcji klienta.  
- Taka architektura ma służyć jako podstawę do klientów dopasować do swoich specyficznych wymagań i nie powinna być używana jako — znajduje się w środowisku produkcyjnym.
- Ten dokument został utworzony jako odwołanie i nie należy używać do definiowania oznacza, że wszystkie za pomocą których klient może spełniać określone wymagania i przepisami. Klienci powinien poszukuje prawne z organizacji w implementacjach klienta zatwierdzone.
