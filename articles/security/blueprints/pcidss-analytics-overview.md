---
title: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — analiza danych dla PCI DSS
description: Strategia zabezpieczeń i zgodności z przepisami platformy Azure — analiza danych dla PCI DSS
services: security
author: meladie
ms.assetid: 6c48cd8e-c548-44e8-a21a-17316c152cbb
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 42ce8bfa78cfa40e147ee90de28c1ac1430070f1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259748"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-pci-dss"></a>Strategia zabezpieczeń i zgodności z przepisami platformy Azure: Analiza dla PCI DSS

## <a name="overview"></a>Przegląd

Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zawiera wskazówki dotyczące wdrażania architektury analizy danych na platformie Azure, które ułatwiają wymagania standardów bezpieczeństwa danych branżowych dla kart płatniczych (PCI DSS 3,2). Zawarto w nim wspólną architekturę referencyjną i demonstruje właściwą obsługę danych karty kredytowej (w tym numeru karty, daty ważności i danych weryfikacyjnych) w bezpiecznym, zgodnym i wielowarstwowym środowisku. Ten plan przedstawia sposób, w jaki klienci mogą spełniać określone wymagania dotyczące zabezpieczeń i zgodności, a także stanowią podstawę dla klientów do tworzenia i konfigurowania własnych rozwiązań do analizy danych na platformie Azure.

Ta architektura referencyjna, przewodnik implementacji i model zagrożeń stanowią podstawę dla klientów spełniających wymagania PCI DSS 3,2. To rozwiązanie udostępnia linię bazową, która ułatwia klientom wdrażanie obciążeń na platformie Azure w ramach zgodności z PCI DSS 3,2. jednak tego rozwiązania nie należy używać w środowisku produkcyjnym, ponieważ wymagana jest dodatkowa konfiguracja.

Osiągnięcie PCI DSS-zgodność wymaga, aby akredytowany wykwalifikowany podmiot zabezpieczeń (QSA) zaświadczy produkcyjne rozwiązanie dla klientów. Klienci są odpowiedzialni za przeprowadzenie odpowiednich ocen dotyczących zabezpieczeń i zgodności wszystkich rozwiązań utworzonych przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od implementacji poszczególnych klientów.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki

Ten Strategia zabezpieczeń i zgodności z przepisami platformy Azure zawiera platformę analityczną, na której klienci mogą tworzyć własne narzędzia analityczne. Architektura referencyjna przedstawia ogólny przypadek użycia, w którym klienci wprowadzają dane przy użyciu zbiorczych danych importu przez administratora SQL/danych lub za pośrednictwem aktualizacji danych operacyjnych za pośrednictwem użytkownika operacyjnego. Oba strumienie służbowe zawierają Azure Functions do importowania danych do Azure SQL Database. Azure Functions musi być skonfigurowany przez klienta za pomocą Azure Portal w celu obsługi zadań importu unikatowych dla potrzeb analizy poszczególnych klientów.

Platforma Azure oferuje klientom różne usługi raportowania i analizy. To rozwiązanie obejmuje Azure Machine Learning usług w połączeniu z Azure SQL Database, aby szybko przeglądać dane i dostarczać szybsze wyniki dzięki inteligentniejszemu modelowaniu. Azure Machine Learning zwiększa szybkość zapytań, odnajdując nowe relacje między zestawami danych. Po przeszkoleniu danych za pomocą kilku funkcji statystycznych, do 7 dodatkowych pul zapytań (łącznie z serwerem klienta) można synchronizować z tymi samymi modelami tabelarycznymi w celu rozproszenia obciążeń zapytań i skrócenia czasu odpowiedzi.

Aby uzyskać ulepszoną analizę i raportowanie, bazy danych SQL platformy Azure można skonfigurować przy użyciu indeksów magazynu kolumn. Zarówno Azure Machine Learning, jak i bazy danych SQL platformy Azure można skalować w górę lub w dół lub całkowicie wyłączać w odpowiedzi na użycie klientów. Cały ruch SQL jest szyfrowany przy użyciu protokołu SSL w ramach dołączania certyfikatów z podpisem własnym. Najlepszym rozwiązaniem jest to, że platforma Azure zaleca korzystanie z zaufanego urzędu certyfikacji w celu zwiększenia bezpieczeństwa.

Gdy dane zostaną przekazane do Azure SQL Database i przeszkolone przez Azure Machine Learning, zostanie ono rozszyfrowane przez zarówno użytkownika operacyjnego, jak i administratora SQL/danych z Power BI. Power BI wyświetla dane intuicyjnie i pobiera razem informacje w wielu zestawach danych w celu uzyskania większego wglądu. Wysoki stopień adaptacji i łatwej integracji z Azure SQL Database zapewnia klientom możliwość skonfigurowania jej do obsługi szerokiej gamy scenariuszy, zgodnie z potrzebami biznesowymi.

Rozwiązanie korzysta z kont usługi Azure Storage, które mogą być szyfrowanie usługi Storage konfigurowane przez klientów w celu zachowania poufności danych przechowywanych w spoczynku. Na platformie Azure są przechowywane trzy kopie danych w wybranym centrach w celu zapewnienia odporności. Geograficznie nadmiarowy magazyn zapewnia, że dane będą replikowane do dodatkowego centrum danych w setkach kilometrów i ponownie przechowywane jako trzy kopie w tym centrum. uniemożliwi to niekorzystne zdarzenie w podstawowym centrum dane klienta, co spowoduje utratę Data.

W celu zwiększenia bezpieczeństwa wszystkie zasoby w tym rozwiązaniu są zarządzane jako Grupa zasobów za pomocą Azure Resource Manager. Azure Active Directory kontroli dostępu opartej na rolach służy do kontrolowania dostępu do wdrożonych zasobów, w tym ich kluczy w Azure Key Vault. Kondycja systemu jest monitorowana za poorednictwem Azure Security Center i Azure Monitor. Klienci konfigurują obie usługi monitorowania do przechwytywania dzienników i wyświetlania kondycji systemu w jednym, łatwym do nawigacji Pulpitie nawigacyjnym.

Azure SQL Database jest często zarządzany za pośrednictwem SQL Server Management Studio (SSMS), który działa z komputera lokalnego skonfigurowanego do uzyskiwania dostępu do Azure SQL Database za pośrednictwem bezpiecznego połączenia VPN lub ExpressRoute. **Firma Microsoft zaleca skonfigurowanie połączenia sieci VPN lub ExpressRoute na potrzeby zarządzania i importowania danych do grupy zasobów architektura referencyjna**.

![Analiza dla PCI DSS diagram architektury referencyjnej](images/pcidss-analytics-architecture.png "Analiza dla PCI DSS diagram architektury referencyjnej")

To rozwiązanie używa następujących usług platformy Azure. Szczegóły architektury wdrożenia znajdują się w sekcji [Architektura wdrażania](#deployment-architecture) .

- Application Insights
- Usługa Azure Active Directory
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- W usłudze Azure Key Vault
- Azure Machine Learning
- Azure Monitor
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1)/16 sieci
    - (2)/24 sieci
    - (2) sieciowe grupy zabezpieczeń
- Pulpit nawigacyjny usługi Power BI

## <a name="deployment-architecture"></a>Architektura wdrożenia

W poniższej sekcji znajdują się szczegółowe informacje dotyczące elementów wdrażania i implementacji.

**Azure Event Grid**: [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) umożliwia klientom łatwe tworzenie aplikacji przy użyciu architektur opartych na zdarzeniach. Użytkownicy wybierają zasób platformy Azure, które chcą subskrybować, i umożliwiają obsługę zdarzeń lub element webhook, do którego zostanie wysłane zdarzenie. Klienci mogą zabezpieczyć punkty końcowe elementu webhook, dodając parametry zapytania do adresu URL elementu webhook podczas tworzenia subskrypcji zdarzeń. Azure Event Grid obsługuje tylko punkty końcowe elementu webhook protokołu HTTPS. Azure Event Grid umożliwia klientom kontrolę poziomu dostępu nadawanego różnym użytkownikom w celu wykonywania różnych operacji zarządzania, takich jak subskrypcje zdarzeń listy, tworzenie nowych i generowanie kluczy. Event Grid korzysta z kontroli dostępu opartej na rolach platformy Azure.

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) to usługa obliczeniowa bez serwera, która umożliwia użytkownikom uruchamianie kodu na żądanie, nie wymagając jawnie aprowizacji infrastruktury ani zarządzania nią. Za pomocą usługi Azure Functions można uruchamiać skrypty lub fragmenty kodu w reakcji na różne zdarzenia.

**Usługa Azure Machine Learning**: [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) jest techniką analizy danych, która umożliwia komputerom używanie istniejących danych do prognozowania przyszłych zachowań, rezultatów i trendów.

**Azure Data Catalog**: [Data Catalog](../../data-catalog/overview.md) umożliwia łatwe odnajdywanie źródeł danych i zrozumienie ich przez użytkowników, którzy zarządzają danymi. Typowe źródła danych mogą być rejestrowane, otagowane i przeszukiwane pod kątem danych finansowych. Dane pozostają w istniejącej lokalizacji, ale kopia jej metadanych zostanie dodana do Data Catalog wraz z odwołaniem do lokalizacji źródła danych. Te metadane są również indeksowane, aby można było je łatwo odnaleźć za pomocą wyszukiwania oraz uczynić je zrozumiałymi dla użytkowników, którzy je odnajdą.

### <a name="virtual-network"></a>Sieć wirtualna

Architektura definiuje prywatną sieć wirtualną z przestrzenią adresową 10.200.0.0/16.

**Sieciowe grupy zabezpieczeń**: [Sieciowe grupy zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md) zawierają listy kontroli dostępu, które zezwalają na ruch lub go odmawiają w ramach sieci wirtualnej. Sieciowe grupy zabezpieczeń mogą służyć do zabezpieczania ruchu w podsieci lub na poziomie poszczególnych maszyn wirtualnych. Istnieją następujące sieciowe grupy zabezpieczeń:

  - Sieciowa Grupa zabezpieczeń dla Active Directory
  - Sieciowa Grupa zabezpieczeń dla obciążenia

Każda z sieciowych grup zabezpieczeń ma otwarte określone porty i protokoły, dzięki czemu rozwiązanie może być bezpieczne i poprawne. Ponadto dla każdej sieciowej grupy zabezpieczeń są włączone następujące konfiguracje:

- [Dzienniki diagnostyczne i zdarzenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) są włączone i przechowywane na koncie magazynu
- Dzienniki Azure Monitor są połączone z [dziennikami diagnostycznymi&#39;sieciowych grup zabezpieczeń](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsieci**: Każda podsieć jest skojarzona z odpowiednią sieciową grupą zabezpieczeń.

### <a name="data-in-transit"></a>Dane przesyłane

Platforma Azure domyślnie szyfruje całą komunikację do i z centrów danych platformy Azure. Wszystkie transakcje do usługi Azure Storage za pośrednictwem Azure Portal odbywają się za pośrednictwem protokołu HTTPS.

### <a name="data-at-rest"></a>Dane magazynowane

Architektura chroni dane przechowywane przez szyfrowanie, inspekcję bazy danych i inne miary.

**Azure Storage**: Aby zaspokoić zaszyfrowane dane w wymaganiach dotyczących REST, wszystkie [usługi Azure Storage](https://azure.microsoft.com/services/storage/) używają [szyfrowanie usługi Storage](../../storage/common/storage-service-encryption.md).  Pomaga to w ochronie i ochronie danych posiadaczy kart w celu wspierania zobowiązań w zakresie zabezpieczeń organizacji i wymagań dotyczących zgodności zdefiniowanych przez PCI DSS 3,2.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) korzysta z funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dla dysków danych. Rozwiązanie integruje się z Azure Key Vault w celu ułatwienia kontroli kluczy szyfrowania dysków i zarządzania nimi.

**Azure SQL Database**: W wystąpieniu Azure SQL Database są stosowane następujące miary zabezpieczeń bazy danych:

- [Active Directory uwierzytelnianie i autoryzacja](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwiają zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
- [Inspekcja bazy danych SQL](../../sql-database/sql-database-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.
- Azure SQL Database jest skonfigurowany do korzystania z funkcji [przezroczystego szyfrowania danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), która wykonuje szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji, aby chronić informacje przechowywane w czasie rzeczywistym. Przezroczyste szyfrowanie danych zapewnia gwarancję, że przechowywane dane nie podlegają nieautoryzowanemu dostępowi.
- [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów baz danych do momentu udzielenia odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
- [Wykrywanie zagrożeń SQL](../../sql-database/sql-database-threat-detection.md) umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działań bazy danych, potencjalne luki w zabezpieczeniach, ataki z iniekcją SQL i nietypowe wzorce dostępu do bazy danych.
- [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zapewniają, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
- [Właściwości rozszerzone](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) mogą służyć do zaprzestania przetwarzania podmiotów danych, ponieważ umożliwia użytkownikom dodawanie niestandardowych właściwości do obiektów bazy danych i oznaczanie ich jako "nieobsługiwanych" w celu zapewnienia obsługi logiki aplikacji w celu zapobiegania przetwarzaniu skojarzonych danych finansowych Data.
- [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umożliwiają użytkownikom Definiowanie zasad ograniczających dostęp do danych w celu ich dalszego przetwarzania.
- [SQL Database Dynamiczne maskowanie danych](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza narażenie na dane poufne przez zamaskowanie danych do użytkowników nieuprzywilejowanych lub aplikacji. Dynamiczne maskowanie danych może automatycznie odnajdywać potencjalnie poufne dane i zasugerować odpowiednie maski, które mają zostać zastosowane. Pomaga to identyfikować i ograniczać dostęp do danych w taki sposób, aby nie zamykał bazy danych za pośrednictwem nieautoryzowanego dostępu. Klienci są odpowiedzialni za dostosowanie ustawień dynamicznego maskowania danych, aby były zgodne ze schematem bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami

Następujące technologie zapewniają możliwości zarządzania dostępem do danych w środowisku platformy Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) to usługa&#39;zarządzania katalogiem i tożsamościami opartymi na chmurze z wieloma dzierżawcami firmy Microsoft. Wszyscy użytkownicy tego rozwiązania są tworzone w Azure Active Directory, w tym użytkownicy uzyskujący dostęp do Azure SQL Database.
- Uwierzytelnianie w aplikacji odbywa się przy użyciu Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ponadto szyfrowanie kolumny bazy danych używa Azure Active Directory do uwierzytelniania aplikacji w Azure SQL Database. Aby uzyskać więcej informacji, zobacz jak [chronić poufne dane w Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Kontrola dostępu oparta na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md) umożliwia administratorom definiowanie szczegółowych uprawnień dostępu w celu udzielania dostępu tylko użytkownikom potrzebnym do wykonywania swoich zadań. Zamiast zapewniać każdemu użytkownikowi nieograniczony dostęp do zasobów platformy Azure, Administratorzy mogą zezwalać na dostęp do danych tylko określonym akcjom. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) pozwala klientom zminimalizować liczbę użytkowników, którzy mają dostęp do określonych informacji. Administratorzy mogą używać Azure Active Directory Privileged Identity Management do odnajdywania, ograniczania i monitorowania uprzywilejowanych tożsamości oraz ich dostępu do zasobów. Tej funkcji można także użyć do wymuszenia dostępu administracyjnego na żądanie, w miarę potrzeb, w razie potrzeby.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) wykrywa potencjalne luki w zabezpieczeniach,&#39;które mają wpływ na tożsamości organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane działania związane z tożsamościami organizacji&#39;i bada podejrzane zdarzenia w celu podjęcia odpowiednich działań w celu ich rozwiązania.

### <a name="security"></a>Bezpieczeństwo

**Zarządzanie wpisami tajnymi**: Rozwiązanie używa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje Azure Key Vault pomagają klientom chronić i uzyskiwać dostęp do tych danych:

- Zaawansowane zasady dostępu są konfigurowane w zależności od potrzeb.
- Zasady dostępu Key Vault są zdefiniowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w Key Vault mają daty wygaśnięcia.
- Wszystkie klucze w Key Vault są chronione przez wyspecjalizowane sprzętowe moduły zabezpieczeń. Typ klucza to chroniony przez moduł HSM 2048-bitowy klucz RSA.
- Wszyscy użytkownicy i tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu kontroli dostępu opartej na rolach.
- Dzienniki diagnostyczne dla Key Vault są włączone z okresem przechowywania wynoszącym co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych, które są wymagane.

**Azure Security Center**: Dzięki [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w ramach obciążeń, ograniczać zagrożenie dla zagrożeń oraz wykrywać ataki i reagować na nie. Ponadto Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia konfiguracji i zaleceń dotyczących usług w celu zwiększenia bezpieczeństwa i ochrony danych.

Azure Security Center korzysta z różnych funkcji wykrywania, aby wysyłać alerty klientom potencjalnych ataków ukierunkowanych na swoje środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Azure Security Center ma zestaw [wstępnie zdefiniowanych alertów zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), które są wyzwalane w przypadku wystąpienia zagrożenia lub podejrzanego działania. [Niestandardowe reguły alertów](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) w Azure Security Center umożliwiają klientom definiowanie nowych alertów zabezpieczeń na podstawie danych, które zostały już zebrane z ich środowiska.

Azure Security Center zapewnia priorytetowe alerty zabezpieczeń i zdarzenia, dzięki czemu klienci mogą łatwiej wykrywać i rozwiązywać potencjalne problemy z zabezpieczeniami. [Raport analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jest generowany dla każdego wykrytego zagrożenia, aby pomóc zespołom reagowania na incydenty w trakcie badania i korygowaniem zagrożeń.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji

Usługi platformy Azure w szerokim zakresie rejestrują aktywność systemu i użytkownika, a także kondycję systemu:
- **Dzienniki aktywności**: [Dzienniki aktywności](../../azure-monitor/platform/activity-logs-overview.md) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w ustaleniu inicjatora, czasu wystąpienia i stanu operacji.
- **Dzienniki diagnostyczne**: [Dzienniki diagnostyczne](../../azure-monitor/platform/resource-logs-overview.md) obejmują wszystkie dzienniki wyemitowane przez każdy zasób. Dzienniki te obejmują Dzienniki systemu Windows, dzienniki usługi Azure Storage, dzienniki inspekcji Key Vault i Application Gateway dostępu i dzienników zapory. Wszystkie dzienniki diagnostyczne zapisu na scentralizowanym i zaszyfrowanym koncie usługi Azure Storage w celu archiwizacji. Przechowywanie jest możliwe do skonfigurowania przez użytkownika, do 730 dni, w celu spełnienia wymagań dotyczących przechowywania specyficznych dla organizacji.

**Azure monitor dzienników**: Te dzienniki są konsolidowane w [Azure monitor dziennikach](https://azure.microsoft.com/services/log-analytics/) na potrzeby przetwarzania, przechowywania i raportowania na pulpicie nawigacyjnym. Po zebraniu dane są zorganizowane w oddzielne tabele dla każdego typu danych w obszarze obszary robocze Log Analytics, co umożliwia analizowanie wszystkich danych razem niezależnie od oryginalnego źródła. Ponadto Azure Security Center integruje się z dziennikami Azure Monitor, dzięki czemu klienci mogą korzystać z zapytań Kusto w celu uzyskania dostępu do danych zdarzeń zabezpieczeń i połączyć je z danymi z innych usług.

Następujące rozwiązania do [monitorowania](../../monitoring/monitoring-solutions.md) platformy Azure są dostępne w ramach tej architektury:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): Rozwiązanie Active Directory Sprawdzanie kondycji ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Rozwiązanie Sprawdzanie kondycji SQL ocenia ryzyko i kondycję środowisk serwera w regularnych odstępach czasu oraz zapewnia klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Rozwiązanie Agent Health zgłasza liczbę wdrożonych agentów i ich dystrybucję geograficzną, a także liczbę agentów, które nie odpowiadają, oraz liczby agentów przesyłających dane operacyjne.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Activity Log Analytics rozwiązanie pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) przechowuje elementy Runbook, uruchamia je i zarządza nimi. W tym rozwiązaniu elementy Runbook ułatwiają zbieranie dzienników z Azure SQL Database. Rozwiązanie [Change Tracking](../../automation/change-tracking.md) Automation umożliwia klientom łatwe identyfikowanie zmian w środowisku.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ułatwia użytkownikom śledzenie wydajności, zachowanie bezpieczeństwa i identyfikowanie trendów przez umożliwienie organizacjom inspekcji, tworzenia alertów i archiwizowania danych, w tym śledzenia wywołań interfejsu API w swoich zasobach platformy Azure.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/) to rozszerzalna usługa zarządzania wydajnością aplikacji (APM) dla deweloperów sieci Web na wielu platformach. Wykrywa anomalie wydajności i oferuje zaawansowane narzędzia analityczne, które ułatwiają diagnozowanie problemów i zrozumienie, jakie użytkownicy faktycznie robią z aplikacją. Zaprojektowano, aby pomóc użytkownikom w ciągłym ulepszaniu wydajności i użyteczności.

## <a name="threat-model"></a>Model zagrożeń

Diagram przepływu danych dla tej architektury referencyjnej jest dostępny do [pobrania](https://aka.ms/PCIAnalyticsTM) lub można go znaleźć poniżej. Ten model może pomóc klientom zrozumieć punkty potencjalnego ryzyka w infrastrukturze systemu podczas wprowadzania modyfikacji.

![Analiza dla PCI DSS diagram architektury referencyjnej](images/pcidss-analytics-threat-model.png "Analiza dla modelu zagrożeń PCI DSS")

## <a name="compliance-documentation"></a>Dokumentacja dotycząca zgodności
[Macierz odpowiedzialna za Strategia zabezpieczeń i zgodności z przepisami platformy Azure PCI DSS klienta](https://aka.ms/pcidss-analytics-tm) zawiera listę obowiązków związanych ze wszystkimi wymaganiami PCI DSS 3,2.

[Macierz implementacji analizy danych Strategia zabezpieczeń i zgodności z przepisami platformy Azure PCI DSS](https://aka.ms/pcidss-analytics-cim) zawiera informacje o tym, które wymagania PCI DSS 3,2 są rozwiązywane przez architekturę analizy danych, w tym szczegółowe opisy tego, jak Implementacja spełnia wymagania poszczególnych formantów objętych usługą.


## <a name="guidance-and-recommendations"></a>Wskazówki i zalecenia

### <a name="vpn-and-expressroute"></a>Sieci VPN i ExpressRoute

Bezpieczny tunel VPN lub [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowany do bezpiecznego nawiązywania połączenia z zasobami wdrożonymi w ramach tej architektury referencyjnej analizy danych. Przez odpowiednie skonfigurowanie sieci VPN lub ExpressRoute klienci mogą dodać warstwę ochrony danych do przetworzenia.

Implementując bezpieczny tunel VPN z platformą Azure, można utworzyć wirtualne połączenie prywatne między siecią lokalną a Virtual Network platformy Azure. To połączenie odbywa się za pośrednictwem Internetu i umożliwia klientom bezpieczne informacje "tunel" w ramach zaszyfrowanego połączenia między siecią klienta i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. [Tryb tunelowania IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jest używany w tej opcji jako mechanizm szyfrowania.

Ze względu na to, że ruch w ramach tunelu sieci VPN przechodzi przez Internet przy użyciu sieci VPN typu lokacja-lokacja, firma Microsoft oferuje kolejną, jeszcze bardziej bezpieczną opcję połączenia. Azure ExpressRoute to dedykowany link sieci WAN między platformą Azure i lokalizacją lokalną lub dostawcą hostingu programu Exchange. Ponieważ połączenia ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Ponadto, ponieważ jest to bezpośrednie połączenie z dostawcą telekomunikacyjnym klienta, dane nie podróżują przez Internet i w związku z tym nie są dla niego ujawniane.

[Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najlepsze rozwiązania dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

### <a name="extract-transform-load-process"></a>Proces wyodrębniania i ładowania
[Baza](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) danych może ładować dane do Azure SQL Database bez potrzeby oddzielnego narzędzia wyodrębniania, przekształcania, ładowania i importowania. Baza danych wielobase umożliwia dostęp do nich za poorednictwem zapytań T-SQL. Stos analizy biznesowej i analizy firmy Microsoft oraz narzędzia innych firm zgodne z SQL Server mogą być używane z bazą danych.

### <a name="azure-active-directory-setup"></a>Konfiguracja Azure Active Directory
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) ma zasadnicze znaczenie dla zarządzania wdrożeniem i zapewnianiem dostępu do pracowników, którzy współpracują ze środowiskiem. Istniejące Active Directory systemu Windows Server można zintegrować z Azure Active Directory za pomocą [czterech kliknięć](../../active-directory/hybrid/how-to-connect-install-express.md). Klienci mogą również powiązać wdrożoną infrastrukturę Active Directory (kontrolery domeny) z istniejącym Azure Active Directory przez nadanie wdrożonej infrastruktury Active Directory poddomeny lasu Azure Active Directory.

## <a name="disclaimer"></a>Zastrzeżenie

 - Ten dokument jest przeznaczony wyłącznie do celów informacyjnych. FIRMA MICROSOFT NIE UDZIELA ŻADNYCH GWARANCJI, WYRAŹNYCH, DOROZUMIANYCH ANI USTAWOWYCH, W ODNIESIENIU DO INFORMACJI ZAWARTYCH W TYM DOKUMENCIE. Ten dokument jest dostarczany "w takiej postaci, w jakim jest". Informacje i poglądy wyrażone w tym dokumencie, w tym adresy URL i inne odwołania do witryn internetowych, mogą ulec zmianie bez powiadomienia. Klienci czytający ten dokument mają ryzyko związane z jego użyciem.
 - Niniejszy dokument nie zapewnia klientom żadnych praw do jakiejkolwiek własności intelektualnej w jakichkolwiek produktach lub rozwiązaniach firmy Microsoft.
 - Klienci mogą kopiować i używać tego dokumentu do wewnętrznych celów referencyjnych.
 - Niektóre zalecenia zawarte w tym dokumencie mogą spowodować zwiększenie użycia zasobów, sieci lub obliczeń na platformie Azure oraz zwiększyć koszty związane z licencją lub subskrypcją platformy Azure klienta.
 - Ta architektura jest przeznaczona dla klientów w celu dostosowania ich do konkretnych wymagań i nie powinna być używana w środowisku produkcyjnym.
 - Niniejszy dokument jest opracowywany jako odwołanie i nie należy go używać do definiowania wszystkich środków, za pomocą których klient może spełnić określone wymagania i regulacje dotyczące zgodności. Klienci powinni zwrócić się z pomocą techniczną od organizacji na zatwierdzone implementacje klientów.
