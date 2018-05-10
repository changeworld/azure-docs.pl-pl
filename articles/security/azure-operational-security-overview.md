---
title: Omówienie usługi Azure operational zabezpieczeń | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie usługi Azure operational zabezpieczeń.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 20dfbd2c5c2c38abddf332dc6a20ddb271c95ace
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="azure-operational-security-overview"></a>Omówienie usługi Azure operational zabezpieczeń
Azure bezpieczeństwa operacyjnego odwołuje się do usług, formanty i funkcje dostępne dla użytkowników do ochrony danych, aplikacji i innych zasobów na platformie Microsoft Azure. [Azure bezpieczeństwa operacyjnego](https://docs.microsoft.com/azure/security/azure-operational-security) jest platforma, która zawiera informacje o wiedzy uzyskane przy użyciu różnych funkcji, które są unikatowe dla firmy Microsoft, w tym Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center program i głębokie świadomości bezpieczeństwa przez zagrożeń.

W tym artykule operacyjne Omówienie zabezpieczeń usługi Azure skupia się wokół następujących obszarów:

- Azure Operations Management Suite
-   Azure Security Center
-   Azure Monitor
-   Azure obserwatora sieciowego
-   Analizy usługi Azure Storage
-   Usługa Azure Active directory

## <a name="azure-operations-management-suite"></a>Azure Operations Management Suite
Dział operacji IT jest odpowiedzialny za zarządzanie infrastruktury centrum danych, aplikacji i danych, w tym stabilności i bezpieczeństwa tych systemów. Jednak uzyskania szczegółowych informacji o zabezpieczeniach przez zwiększenie złożonych środowiskach IT często wymaga organizacjom cobble razem danych z wielu systemów zabezpieczeń i zarządzania.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) firmy Microsoft w chmurze IT rozwiązanie do zarządzania ułatwiające zarządzanie i ochrona lokalnej infrastruktury w chmurze.

OMS jest oparta na chmurze IT rozwiązania do zarządzania z wielu oferty, takie jak ich automatyzacji, zabezpieczeń i zgodności, analizy dzienników i tworzenia kopii zapasowej i odzyskiwania. W efekcie jest doskonałą pomoc do zarządzania i chronić infrastrukturę informatyczną — lokalnie i w chmurze.

Podstawowe funkcje pakietu OMS są oferowane jako zbiór usług działających na platformie Azure. Każda usługa udostępnia określoną funkcję zarządzania. Możesz łączyć usługi, aby realizować różne scenariusze zarządzania. W tym:

-   Log Analytics
-   Automatyzacja
-   Backup
-   Site Recovery

### <a name="log-analytics"></a>Log Analytics
Usługa [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) umożliwia monitorowanie pakietu OMS przez zbieranie danych z zarządzanych zasobów w centralnym repozytorium. Te dane mogą obejmować zdarzenia, dane wydajności i niestandardowe dane dostarczane przez interfejs API. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu. Ta metoda umożliwia konsolidowanie danych z różnych źródeł, dzięki czemu można połączyć dane z usług platformy Azure z istniejącym środowiskiem lokalnym. Ponadto wprowadza wyraźny podział między zbieraniem danych a akcjami wykonanymi na tych danych, tak aby wszystkie akcje były dostępne dla wszystkich typów danych.

### <a name="automation"></a>Automatyzacja
Microsoft [usługi Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-intro) umożliwia użytkownikom Automatyzowanie ręcznego, długotrwałą podatne na błędy i często powtarzanych zadań, które są często wykonywane w środowisku cloud i enterprise. Można dzięki niej zaoszczędzić czas i zwiększyć niezawodność regularnie wykonywanych zadań administracyjnych, a nawet zaplanować ich wykonywanie w regularnych odstępach czasu. Można automatyzować procesy za pomocą elementów Runbook lub automatyzować zarządzanie konfiguracją za pomocą konfiguracji żądanego stanu.

### <a name="backup"></a>Backup
[Kopia zapasowa Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) jest — usługa Azure umożliwia tworzenie kopii zapasowej (lub ochrona) i przywracanie danych w chmurze firmy Microsoft. Usługa Azure Backup pozwala zastąpić dotychczasowe rozwiązania tworzenia kopii zapasowych, istniejące lokalnie lub poza siedzibą firmy, rozwiązaniem opartym na chmurze, które jest niezawodne, bezpieczne i konkurencyjne cenowo. Usługa Azure Backup oferuje wiele składników, które możesz pobrać i wdrożyć na odpowiednim komputerze, serwerze lub w chmurze. Wdrażany składnik lub agent zależy od tego, co ma być chronione. Wszystkie składniki usługi Azure Backup (niezależnie od tego, czy dane są chronione lokalnie, czy w chmurze) mogą służyć do tworzenia kopii zapasowych danych w magazynie usługi Recovery Services na platformie Azure. Zobacz [tabeli składników usługi Kopia zapasowa Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Usługa Site recovery
Usługa [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) umożliwia zachowanie ciągłości działania przez organizowanie replikacji lokalnych maszyn wirtualnych i fizycznych do platformy Azure lub lokacji dodatkowej. Jeśli lokacja podstawowa jest niedostępna, możesz przejść w tryb failover do lokalizacji dodatkowej, aby użytkownicy mogli kontynuować pracę, i wrócić po awarii, gdy systemy wznowią działanie. Korzystanie z Centrum zabezpieczeń Azure przeprowadzić bardziej inteligentne i skuteczne wykrywanie zagrożeń.

## <a name="azure-active-directory"></a>Usługa Azure Active Directory
[Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) jest kompleksowe tożsamość firmy Microsoft jako rozwiązaniem Service (IDaaS) który:

-   Włącza IAM jako usługa w chmurze
-   Umożliwia zarządzanie dostępu, jednokrotnego (SSO) i raportowania
-   Obsługuje zarządzanie zintegrowanego dostępu dla [tysięcy aplikacji](https://azure.microsoft.com/marketplace/active-directory/) w galerii aplikacji w tym usług Salesforce, Google Apps, pole i Concur.

Usługi Azure AD zawiera również pełny pakiet [możliwości zarządzania tożsamościami](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports) tym [uwierzytelnianie wieloskładnikowe](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), [Rejestracja urządzenia]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview), [zarządzania hasłami samoobsługi](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/), [Samoobsługowe zarządzanie grupami](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), [uprzywilejowane konto zarządzania](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview), [monitorowania użycia aplikacji](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health), [sformatowanego inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), i [monitorowania i alertów zabezpieczeń](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).

Z usługą Azure Active Directory wszystkie aplikacje publikowania dla partnerów i klientów (biznesowe lub klienta) ma taką samą tożsamość oraz dostęp do możliwości zarządzania. Dzięki temu można znacznie zmniejszyć koszty operacyjne.

## <a name="azure-security-center"></a>Azure Security Center
Usługa [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started) ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Umożliwia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami dla wszystkich subskrypcji, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

[Centrum zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) pomaga chronić danych maszyny wirtualnej na platformie Azure, zapewniając wgląd w ustawienia zabezpieczeń na komputerze wirtualnym i monitorowanie zagrożeń. Usługa Security Center może monitorować maszyny wirtualne pod kątem następujących elementów:

-   Ustawienia zabezpieczeń systemu operacyjnego z zalecanymi regułami konfiguracji
-   Informacje o brakujących zabezpieczeniach systemu i aktualizacjach krytycznych
-   Zalecenia dotyczące ochrony punktów końcowych
-   Weryfikowanie szyfrowania dysków
-   Ataki sieciowe

Centrum zabezpieczeń Azure używa [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), która zapewnia [wbudowane role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) który można przypisać do użytkowników, grup i usług Azure.

Centrum zabezpieczeń ocenia konfiguracji zasobów, aby zidentyfikować problemy z zabezpieczeniami i luk w zabezpieczeniach. W Centrum zabezpieczeń widoczne są tylko informacje związane z zasobem, jeśli przypisano rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, do której należy zasób.

>[!Note]
>Zobacz [uprawnienia w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-permissions) Aby dowiedzieć się więcej o rolach i akcji dozwolonych w Centrum zabezpieczeń.

Centrum zabezpieczeń używa programu Microsoft Monitoring Agent — jest to tego samego agenta używane przez usługę Operations Management Suite i analizy dzienników. Dane zbierane z tego agenta są przechowywane w istniejących analizy dzienników [obszaru roboczego](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) skojarzone z subskrypcją platformy Azure lub nowych obszarów roboczych, biorąc pod uwagę używanie funkcji geolokalizacji maszyny wirtualnej.

## <a name="azure-monitor"></a>Azure Monitor
Problemy z wydajnością w Twojej aplikacji w chmurze może wpłynąć na Twojej firmy. Z wielu powiązanych elementów i częste wersjach degradations może nastąpić w dowolnej chwili. I w przypadku tworzenia aplikacji, użytkowników, zazwyczaj odnajdywanie problemy, które nie udało się znaleźć podczas testowania. Należy od razu wiedzieć o tych problemów i narzędzia do diagnozowania i rozwiązywania problemów.

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) jest podstawowym narzędziem do monitorowania usługi działające na platformie Azure. Udostępnia poziomie infrastruktury danych o przepływności usługi i otaczającego środowiska. W przypadku zarządzania aplikacjami w usłudze Azure podjęcie decyzji o skalować w górę lub w dół zasobów, Azure Monitor daje służy do zainicjowania.

Ponadto można użyć danych monitorowania w celu uzyskania szczegółowych informacji o aplikacji. Wiedzy może pomóc zwiększyć wydajność aplikacji lub utrzymania lub automatyzować czynności, które w przeciwnym razie wymagają ręcznej interwencji. Obejmuje on:

-   Dziennik aktywności platformy Azure
-   Dzienniki diagnostyczne platformy Azure
-   Metryki
-   Diagnostyka Azure

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure
Jest dziennika, który zapewnia wgląd w operacje wykonywane na zasobów w ramach subskrypcji. [Dziennik aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) była wcześniej znana jako "Dzienników inspekcji" lub "Operacyjne dzienniki", ponieważ zgłasza płaszczyzny kontroli zdarzeń dla subskrypcji.

### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure
[Azure dzienników diagnostycznych](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) są emitowane przez zasób i udostępnia rozbudowane, często danych o działaniu tego zasobu. Zawartość tych dzienników zależy od typu zasobu.

Na przykład dzienniki systemu zdarzeń systemu Windows są jedną kategorię dzienników diagnostycznych dla maszyn wirtualnych i obiektów blob, tabeli, a dzienniki kolejki są kategorii dzienników diagnostycznych dla kont magazynu.

Dzienniki diagnostyczne różnią się od [dziennik aktywności (wcześniej znane jako dziennik inspekcji lub dziennik operacyjny)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Dziennik aktywności zapewnia wgląd w operacje wykonywane na zasobów w ramach subskrypcji. Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonanych przez sam zasób.

### <a name="metrics"></a>Metryki
Azure Monitor umożliwia korzystanie z telemetrię, aby uzyskać wgląd w wydajności i kondycji obciążeń na platformie Azure. Typ najważniejszych danych telemetrycznych platformy Azure jest metryki (nazywanych również liczniki wydajności) emitowane przez zasoby najbardziej platformy Azure. Azure Monitor udostępnia kilka sposobów konfigurowania i korzystania z tych [metryki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) do monitorowania i rozwiązywania problemów.

### <a name="azure-diagnostics"></a>Diagnostyka Azure
Istnieje możliwość w ramach platformy Azure, która umożliwia zbieranie danych diagnostycznych na wdrożonej aplikacji. Można użyć rozszerzenia diagnostyki z różnych różnych źródeł. Obecnie obsługiwane są [Azure Cloud Service w sieci Web i proces roboczy](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [maszyny wirtualne Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) systemu Microsoft Windows i [sieci szkieletowej usług](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).


## <a name="network-watcher"></a>Network Watcher
Klienci kompilacji na trasie sieci na platformie Azure poprzez organizowanie i tworzenia różnych zasobów poszczególnych sieci, takich jak sieci wirtualnej, ExpressRoute, bramy aplikacji i usług równoważenia obciążenia. Monitorowanie jest dostępne na każdym z zasobów sieciowych.

Kompleksowe sieci może mieć złożonych konfiguracji i interakcje między zasobami, tworzenie złożonych scenariuszy, które wymagają oparta na scenariuszu monitorowanie za pomocą Monitora sieci.

[Monitor sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) będzie upraszcza monitorowania i diagnozowania sieci platformy Azure. Z obserwatora sieciowego Włącz dostępnych narzędzi diagnostycznych i wizualizacji podjęcia odpowiednich pakietów zdalnego znajdują się na maszynie wirtualnej platformy Azure, ruchu przy użyciu dzienników przepływu korzyści wgląd w sieci oraz przeprowadzania jego diagnostyki bramy sieci VPN i połączeń.

Obserwatora sieciowego ma obecnie następujące możliwości:

- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) -Wyświetla sieci poziomu pokazywanie różnych połączeń i skojarzenia między zasobami sieci w grupie zasobów.
-   [Zmienna przechwytywania pakietów](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) -przechwytuje pakiet danych do i z maszyny wirtualnej. Zaawansowane opcje filtrowania i dostosować formanty, takie jak ustawianie czasu i rozmiaru ograniczenia zapewniają wszechstronność. Dane pakietu mogą być przechowywane w magazynie obiektów blob lub na dysku lokalnym w formacie CAP.
-   [Sprawdź przepływów IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) — sprawdza, czy pakiet jest dozwolony lub niedozwolony na podstawie przepływ informacji 5-elementowej pakietu parametrów (docelowy adres IP, źródłowy adres IP, Port docelowy, Port źródłowy i Protocol). Jeśli pakiet jest zabroniony przez grupę zabezpieczeń, zwracany jest reguła i grupy, którego pakiet.
-   [Następny przeskok](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) — określa następnego skoku dla pakietów przesyłane w sieci szkieletowej Azure, dzięki któremu można zdiagnozować wszelkie błędnie skonfigurowane trasy zdefiniowane przez użytkownika.
-   [Widok grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) -pobiera reguły skuteczne i zastosowane zabezpieczeń, które są stosowane na maszynie Wirtualnej.
-   [Rejestrowanie przepływu NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) — dzienniki przepływu dla grup zabezpieczeń sieci umożliwiają przechwytywanie dzienniki związane z ruchu, który ma być dozwolony lub odrzucany przez zasady zabezpieczeń w grupie. Przepływ został zdefiniowany przez informacji 5-elementowej — źródłowy adres IP, docelowy adres IP, Port źródłowy, Port docelowy i protokołu.
-   [Brama sieci wirtualnej i rozwiązywanie problemów z połączenia](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) — umożliwia rozwiązywanie problemów z bramy sieci wirtualnej i połączenia.
-   [Limity subskrypcji sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) — umożliwia wyświetlenie wykorzystania zasobów sieci ograniczeń.
-   [Konfigurowanie dziennika diagnostyki](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) — zapewnia jeden Aby włączyć lub wyłączyć dzienników diagnostycznych do zasobów sieciowych w grupie zasobów.

Aby dowiedzieć się więcej sposobu konfigurowania sieci, zobacz obserwatora [skonfigurować obserwatora sieciowego](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="developer-operations-devops"></a>Operacje Developer (DevOps)
Przed DevOps projektowanie aplikacji zespoły zostały odpowiedzialnym za gromadzenie wymagań firmy dotyczących oprogramowania i pisanie kodu. Następnie oddzielny zespół pytań i odpowiedzi testy program w środowisku projektowym izolowanym, jeśli wymagania zostały spełnione i zwalnia kod operacji wdrażania. Zespoły są dodatkowo pofragmentowane ze grup, takich jak sieci i bazy danych. Zawsze oprogramowania jest "zgłoszony przez ściany" zespołowi niezależne go dodaje wąskich gardeł.

[DevOps](https://www.visualstudio.com/learn/what-is-devops/) umożliwia zespołom bardziej bezpieczne, wyższej jakości rozwiązań szybsze i tańsze. Klienci oczekują dynamiczne i niezawodne środowisko w przypadku uzyskiwania dostępu do oprogramowania i usług.  Zespoły musi szybko przejść na aktualizacji oprogramowania, mierzymy jego wpływ na aktualizacje oraz szybko reagować z nowego rozwoju iteracji, aby rozwiązać problemy lub zapewnić większą wartość.  Platformami chmury, takich jak Microsoft Azure zostały usunięte tradycyjnych wąskich gardeł i pomogła commoditize infrastruktury. Reigns oprogramowania w każdym firm jako główną różnicą i czynnikiem wyników biznesowych. Nie organizacji, developer lub procesu roboczego IT można lub należy unikać przenoszenia DevOps.

Dojrzała lekarze DevOps przyjmuje kilka z następujących wskazówek. Praktyki te [obejmują osób](https://www.visualstudio.com/learn/what-is-devops-culture/) strategie formularza oparte na scenariuszy biznesowych.  Narzędzia ułatwiają Automatyzowanie różnych rozwiązań:

-   [Elastyczne planowanie i zarządzanie projektami](https://www.visualstudio.com/learn/what-is-agile/) techniki są używane do plan i izolowanie pracy do przebiegów, zarządzanie wydajnością zespołu i pomoc zespoły szybko dostosowanie do zmieniających się potrzeb biznesowych.
-   [Kontrola wersji, zwykle za pomocą narzędzia Git](https://www.visualstudio.com/learn/what-is-git/), umożliwia zespołom zlokalizowanych w dowolnym miejscu na świecie do udostępnienia źródła i integracja z narzędziami programistycznymi oprogramowania do automatyzacji procesu wersji.
-   [Ciągła integracja](https://www.visualstudio.com/learn/what-is-continuous-integration/) dyski trwającą scalanie i testowanie kodu, co prowadzi do znajdowania wady wcześniej.  Inne zalety oszczędności na walka scalania problemy i szybkie opinii dla zespołów deweloperów czasu.
-   [Ciągłego dostarczania](https://www.visualstudio.com/learn/what-is-continuous-delivery/) rozwiązań oprogramowania produkcyjnego i testowania środowiska Pomoc organizacje szybko błędów i reagowanie na kiedykolwiek zmieniających się potrzeb biznesowych.
-   [Monitorowanie](https://www.visualstudio.com/learn/what-is-monitoring/) uruchomionych aplikacji, również w środowiskach produkcyjnych dla kondycji aplikacji jako także formularz organizacji pomocy użycia klienta hipoteza i szybko sprawdza, czy lub disprove strategii.  Zaawansowana dane zostaną zebrane i przechowywane w różnych formatach rejestrowania.
-   [Infrastruktura jako kodu (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) jest rozwiązaniem, dzięki czemu automatyzacji i sprawdzanie poprawności tworzenia i usuwania sieci i maszyn wirtualnych ułatwiają dostarczanie aplikacji bezpiecznego, stabilna hostingu platformami.
-   [Mikrousług](https://www.visualstudio.com/learn/what-are-microservices/) architektury jest wykorzystywana do izolowania przypadków użycia biznesowego w małych usług wielokrotnego użytku.  Taka architektura umożliwia stosowanie skalowalność i wydajność.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o rozwiązaniu OMS zabezpieczeń i inspekcji, zobacz następujące artykuły:

- [Operations Management Suite | Bezpieczeństwo i zgodność](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Monitorowanie i reagowanie na alerty zabezpieczeń w Operations Management Suite zabezpieczeń i rozwiązanie inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).
- [Monitorowanie zasobów Operations Management Suite zabezpieczeń i rozwiązanie inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources).
