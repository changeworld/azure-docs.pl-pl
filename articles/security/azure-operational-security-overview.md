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
ms.openlocfilehash: c0413678aad16105f732ef23fb60c61fddcdad45
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="azure-operational-security-overview"></a>Omówienie usługi Azure operational zabezpieczeń
[Azure bezpieczeństwa operacyjnego](https://docs.microsoft.com/azure/security/azure-operational-security) odwołuje się do usług, formanty i funkcje dostępne dla użytkowników do ochrony danych, aplikacji i innych zasobów na platformie Microsoft Azure. To platforma, która zawiera wiedzę za pomocą różnych funkcji, które są specyficzne dla firmy Microsoft. Te funkcje obejmują Microsoft Security Development Lifecycle (SDL), program Microsoft Security Response Center i głębokie pogłębianie wiedzy na temat zagrożeń bezpieczeństwa.

## <a name="operations-management-suite"></a>Operations Management Suite
Dział operacji IT jest odpowiedzialny za zarządzanie infrastruktury centrum danych, aplikacji i danych, w tym stabilności i bezpieczeństwa tych systemów. Jednak uzyskania szczegółowych informacji o zabezpieczeniach przez zwiększenie złożonych środowiskach IT często wymaga organizacjom cobble razem danych z wielu systemów zabezpieczeń i zarządzania.

[Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) jest oparta na chmurze IT zarządzania rozwiązaniem, które pomaga zarządzać i chronić lokalną i infrastruktury w chmurze. Jego podstawowych funkcji są udostępniane przez następujące usługi działające na platformie Azure. Każda usługa udostępnia funkcję zarządzania określonymi. Można łączyć usługi, aby osiągnąć zarządzania różnych scenariuszy. 

### <a name="log-analytics"></a>Log Analytics
[Analiza dzienników Azure](http://azure.microsoft.com/documentation/services/log-analytics) umożliwia monitorowanie usługi Operations Management Suite przez zbieranie danych z zarządzanych zasobów w centralnym repozytorium. Dane te mogą obejmować zdarzeń, danych wydajności lub niestandardowe dane przekazane za pośrednictwem interfejsu API. Po zebraniu danych jest dostępna dla alertów, analizy i eksportowanie. 

Można skonsolidować danych z różnych źródeł i połączyć dane z usługami Azure z istniejącego środowiska lokalnego. Analiza dzienników również wyraźnie oddziela zbierania danych od akcję podejmowaną w odniesieniu do tych danych tak, aby wszystkie akcje są dostępne dla wszystkich typów danych.

### <a name="automation"></a>Automatyzacja
[Automatyzacja Azure](https://docs.microsoft.com/azure/automation/automation-intro) umożliwia automatyzowanie ręcznego, długotrwałą podatne na błędy i często powtarzanych zadań, które są często wykonywane w środowisku cloud i enterprise. Ona zaoszczędzić czas i zwiększa niezawodność zadań administracyjnych. Umożliwia zaplanowanie nawet te zadania mogą być wykonywane automatycznie w regularnych odstępach czasu. Można zautomatyzować procesy przy użyciu elementów runbook lub automatyzacji zarządzania konfiguracją przy użyciu konfiguracji żądanego stanu.

### <a name="backup"></a>Backup
[Kopia zapasowa Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) jest usługa bazujących na platformie Azure umożliwia tworzenie kopii zapasowej (lub ochrona) i przywracanie danych w Microsoft Cloud. Kopia zapasowa Azure z istniejącym lokalnym lub poza nim rozwiązania do tworzenia kopii zapasowych zastępowane rozwiązanie oparte na chmurze są niezawodne, bezpieczne i konkurencyjnych kosztów. 

Kopia zapasowa Azure oferuje składników, które możesz pobrać i wdrożyć na właściwym komputerze lub serwerze lub w chmurze. Wdrażany składnik lub agent zależy od tego, co ma być chronione. Wszystkie składniki usługi Kopia zapasowa Azure (czy są one chronione danych w sieci lokalnej lub w chmurze) można wykonać kopię zapasową danych w magazynie usług odzyskiwania Azure na platformie Azure. 

Aby uzyskać więcej informacji, zobacz [tabeli składników usługi Kopia zapasowa Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Site Recovery
[Usługa Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) zapewnia ciągłość prowadzenia działalności biznesowej poprzez organizowanie replikacji lokalnych maszyny wirtualne i fizyczne do platformy Azure lub lokacji dodatkowej. Jeśli w lokacji głównej jest niedostępny, należy przełączyć lokalizacji dodatkowej, dzięki czemu użytkownicy mogą kontynuować pracę. Nie zostanie ponownie, jeśli systemy przywrócić kolejność pracy. Korzystanie z Centrum zabezpieczeń Azure przeprowadzić bardziej inteligentne i skuteczne wykrywanie zagrożeń.

## <a name="azure-active-directory"></a>Usługa Azure Active Directory
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) jest kompleksowe tożsamości usługi zakończonych:

-   Umożliwia zarządzania tożsamościami i dostępem (IAM) jako usługa w chmurze.
-   Zapewnia zarządzanie dostępem centralnej, rejestracji jednokrotnej (SSO) i raportowania.
-   Obsługuje zarządzanie zintegrowanego dostępu dla [tysięcy aplikacji](https://azure.microsoft.com/marketplace/active-directory/) w portalu Azure Marketplace w tym usług Salesforce, Google Apps pole i Concur.

Usługi Azure AD zawiera również pełny pakiet [możliwości zarządzania tożsamościami](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), w tym te:

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)
- [Rejestracja urządzeń]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview)
- [Zarządzanie hasłami samoobsługi](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Samoobsługowe zarządzanie grupami](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Uprzywilejowane konto zarządzania](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Monitorowanie użycia aplikacji](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Zaawansowana inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Monitorowanie zabezpieczeń i alerty](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)

Usłudze Azure Active Directory, wszystkie aplikacje, które publikowania dla partnerów i klientów (biznesowe lub klienta) ma taką samą tożsamość oraz dostęp do możliwości zarządzania. Dzięki temu można znacznie zmniejszyć koszty operacyjne.

## <a name="azure-security-center"></a>Azure Security Center
[Centrum zabezpieczeń Azure](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) pomaga zapobiec, wykrywania i reagowania na zagrożenia, oferując większą widoczność (i kontrolę nad) zabezpieczeń zasobów platformy Azure. Zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami zapewnia wszystkich subskrypcji. Pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a jego współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

[Ochrona danych maszyny wirtualnej (VM)](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) na platformie Azure, zapewniając wgląd w ustawienia zabezpieczeń na komputerze wirtualnym i monitorowanie zagrożeń. Usługa Security Center może monitorować maszyny wirtualne pod kątem następujących elementów:

-   Ustawienia zabezpieczeń systemu operacyjnego przy użyciu reguł zalecanych konfiguracji.
-   Zabezpieczenia systemu i aktualizacje krytyczne, które nie są spełnione.
-   Zalecenia dotyczące ochrony punktu końcowego.
-   Sprawdzanie poprawności szyfrowania dysku.
-   Ataki sieciowe.

Centrum zabezpieczeń używa [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). Udostępnia RBAC [wbudowane role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) który można przypisać do użytkowników, grup i usług Azure.

Centrum zabezpieczeń ocenia konfiguracji zasobów, aby zidentyfikować problemy z zabezpieczeniami i luk w zabezpieczeniach. W Centrum zabezpieczeń można zobaczyć informacje powiązane z zasobem tylko wtedy, gdy są przydzielone rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, do której należy zasób.

>[!Note]
>Aby dowiedzieć się więcej o rolach i dozwolonych akcji w Centrum zabezpieczeń, zobacz [uprawnienia w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-permissions).

Centrum zabezpieczeń używa programu Microsoft Monitoring Agent. Jest to tego samego agenta, używanego przez usługi Operations Management Suite i usługą analizy dzienników. Dane zbierane z tego agenta są przechowywane w istniejących analizy dzienników [obszaru roboczego](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) skojarzone z subskrypcją platformy Azure lub nowy obszar roboczy, biorąc pod uwagę używanie funkcji geolokalizacji maszyny wirtualnej.

## <a name="azure-monitor"></a>Azure Monitor
Problemy z wydajnością w Twojej aplikacji w chmurze mogą wpłynąć na prowadzoną działalność. Z wielu powiązanych elementów i częste wersjach degradations może nastąpić w dowolnej chwili. I w przypadku tworzenia aplikacji, użytkowników, zazwyczaj odnajdywanie problemy, które nie udało się znaleźć podczas testowania. Informacje o tych problemów natychmiast, a powinien mieć narzędzia do diagnozowania i rozwiązywania problemów.

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) jest podstawowym narzędziem do monitorowania usługi działające na platformie Azure. Udostępnia poziomie infrastruktury danych o przepływności usługi i otaczającego środowiska. Jeśli zarządzasz aplikacji w Azure i podjęcie decyzji o skalować w górę lub w dół zasobów Azure Monitor jest miejscem umożliwiającym rozpoczęcie.

Umożliwia także dane monitorowania w celu uzyskania szczegółowych informacji o aplikacji. Wiedzy może pomóc zwiększyć wydajność aplikacji lub utrzymania lub automatyzować czynności, które w przeciwnym razie wymagają ręcznej interwencji. 

Azure Monitor obejmuje następujące składniki.

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure
[Dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewnia wgląd w operacje wykonywane na zasobów w ramach subskrypcji. Został on wcześniej znana jako "Dziennik inspekcji" lub "Dziennik operacyjny", ponieważ zgłasza płaszczyzny kontroli zdarzeń dla subskrypcji.

### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure
[Azure dzienników diagnostycznych](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) są emitowane przez zasób i udostępnia rozbudowane, często danych o działaniu tego zasobu. Zawartość tych dzienników zależy od typu zasobu.

Dzienniki systemu zdarzeń systemu Windows są jedną kategorię dzienników diagnostycznych dla maszyn wirtualnych. Obiekt blob, tabel i dzienniki kolejki są kategorii dzienników diagnostycznych dla kont magazynu.

Dzienniki diagnostyczne różnią się od [dziennik aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Dziennik aktywności zapewnia wgląd w operacje wykonywane na zasobów w ramach subskrypcji. Dzienniki diagnostyczne zapewniają wgląd w działania, które zostały zasobu wykonanie samego.

### <a name="metrics"></a>Metryki
Azure Monitor udostępnia dane telemetryczne, który zapewnia widoczność wydajności i kondycji obciążeń na platformie Azure. Typ najważniejszych danych telemetrycznych platformy Azure jest [metryki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (również liczniki wydajności o nazwie) emitowane przez zasoby najbardziej platformy Azure. Azure Monitor udostępnia kilka sposobów konfigurowania i korzystać z tych metryk do monitorowania i rozwiązywania problemów.

### <a name="azure-diagnostics"></a>Diagnostyka Azure
Diagnostyka Azure umożliwia zbieranie danych diagnostycznych na wdrożonej aplikacji. Można użyć rozszerzenia diagnostyki z różnych źródeł. Obecnie obsługiwane są [role usługi w chmurze Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) systemu Microsoft Windows i [sieć szkieletowa usług Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).


## <a name="azure-network-watcher"></a>Azure Network Watcher
Klienci tworzenie sieci end-to-end na platformie Azure poprzez organizowanie i tworzenie zasobów poszczególnych sieci, takich jak sieci wirtualnych Azure ExpressRoute, brama aplikacji w usłudze Azure i moduły równoważenia obciążenia. Monitorowanie jest dostępne na każdym z zasobów sieciowych.

Sieć end-to-end można mieć złożonych konfiguracji i interakcje między zasobami. Wynik jest złożonych scenariuszy, które muszą oparta na scenariuszu monitorowania za pomocą [obserwatora sieciowego Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

Obserwatora sieciowego upraszcza monitorowania i diagnozowania sieci platformy Azure. Można użyć narzędzi diagnostycznych i wizualizacji w obserwatora sieciowego do:
- Przełączyć przechwytywania pakietów zdalnego na maszynie wirtualnej platformy Azure.
- Korzyści wgląd w informacje przy użyciu dzienników przepływu ruchu sieciowego.
- Diagnozowanie Azure bramy sieci VPN i połączeń.

Obserwatora sieciowego ma obecnie następujące możliwości:

- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Wyświetla różnych połączeń i skojarzenia między zasobami sieci w grupie zasobów.
-   [Przechwytywania pakietów zmiennej](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): przechwytuje pakiet danych do i z maszyny wirtualnej. Zaawansowane opcje filtrowania i dostosować ustawienia, takie jak możliwość Ustawianie czasu i rozmiaru ograniczenia, podaj wszechstronność. Dane pakietu mogą być przechowywane w magazynie obiektów blob lub na dysku lokalnym w formacie CAP.
-   [Sprawdź przepływ IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): sprawdza, czy pakiet jest dozwolony lub niedozwolony na podstawie 5 parametrów pakietu parametrów przepływ informacji (docelowy adres IP, źródłowy adres IP, port docelowy, port źródłowy i protocol). Jeśli pakiet nie zezwala na grupę zabezpieczeń, zwracane są reguły i grupy, którego pakiet.
-   [Następny przeskok](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Określa następnego skoku dla pakietów przesyłane w sieci szkieletowej sieć platformy Azure, więc można zdiagnozować wszelkie błędnie skonfigurowane trasy zdefiniowane przez użytkownika.
-   [Widok grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): pobiera reguły skuteczne i zastosowane zabezpieczeń, które są stosowane na maszynie Wirtualnej.
-   [Dzienniki przepływu NSG dla grup zabezpieczeń sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview): umożliwiają przechwytywanie dzienniki związane z ruchem, który jest dozwolony lub odrzucany przez zasady zabezpieczeń w grupie. Przepływ jest definiowana za pomocą informacji 5 parametrów: źródłowego adresu IP, docelowy adres IP, port źródłowy, port docelowy i protokołu.
-   [Brama sieci wirtualnej i rozwiązywanie problemów z połączenia](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): umożliwia rozwiązywanie problemów z bram sieci wirtualnej i połączenia.
-   [Limity subskrypcji sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): umożliwia wyświetlenie wykorzystania zasobów sieci ograniczeń.
-   [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): zapewnia jeden Aby włączyć lub wyłączyć dzienników diagnostycznych do zasobów sieciowych w grupie zasobów.

Aby uzyskać więcej informacji, zobacz [skonfigurować obserwatora sieciowego](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="devops"></a>DevOps
Przed [operacji Developer (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) projektowanie aplikacji zespoły zostały odpowiedzialnym za gromadzenie wymagań firmy dotyczących oprogramowania i pisanie kodu. Następnie oddzielny zespół pytań i odpowiedzi przetestowane program w środowisku projektowym izolowanym. Jeśli wymagania zostały spełnione, zespół pytań i odpowiedzi opublikowała kod operacji wdrażania. Zespoły dalsze zostały fragmentacji do grupy, takie jak sieci i bazy danych. Zawsze oprogramowanie "zgłoszono za pośrednictwem ściany" zespołowi niezależne, dodać wąskich gardeł.

DevOps umożliwia zespołom bardziej bezpieczne, wyższej jakości rozwiązania umożliwiające szybsze i po niższej cenie. Klienci oczekują dynamiczne i niezawodne środowisko w przypadku uzyskiwania dostępu do oprogramowania i usług. Zespoły musi szybko przejść na aktualizacji oprogramowania i mierzymy jego wpływ na aktualizacje. Klienci muszą odpowiadać szybko przy użyciu nowego programowanie iteracji, aby rozwiązać problemy lub zapewnić większą wartość.  

Platformami chmury, takich jak Microsoft Azure zostały usunięte tradycyjnych wąskich gardeł i pomogła commoditize infrastruktury. Reigns oprogramowania w każdym firm jako główną różnicą i czynnikiem wyników biznesowych. Nie organizacji, developer lub procesu roboczego IT można lub należy unikać przenoszenia DevOps.

Dojrzała lekarze DevOps przyjmuje kilka z następujących wskazówek. Praktyki te [obejmują osób](https://www.visualstudio.com/learn/what-is-devops-culture/) strategie formularza oparte na scenariuszy biznesowych. Narzędzia ułatwiają Automatyzowanie różnych rozwiązań.

-   [Elastyczne planowanie i zarządzanie projektami](https://www.visualstudio.com/learn/what-is-agile/) techniki są używane do plan i izolowanie pracy do przebiegów, zarządzanie wydajnością zespołu i pomoc zespoły szybko dostosowanie do zmieniających się potrzeb biznesowych.
-   [Kontrola wersji, zwykle za pomocą narzędzia Git](https://www.visualstudio.com/learn/what-is-git/), umożliwia zespołom zlokalizowanych w dowolnym miejscu na świecie do udostępnienia źródła i integracja z narzędziami programistycznymi oprogramowania do automatyzacji procesu wersji.
-   [Ciągła integracja](https://www.visualstudio.com/learn/what-is-continuous-integration/) dyski trwającą scalanie i testowanie kodu, co prowadzi do znajdowania wady wcześniej.  Inne zalety oszczędności na walka scalania problemy i szybkie opinii dla zespołów deweloperów czasu.
-   [Ciągłego dostarczania](https://www.visualstudio.com/learn/what-is-continuous-delivery/) rozwiązań oprogramowania do produkcji i pomaga w środowiskach testowych organizacje szybko błędów i reagowanie na kiedykolwiek zmieniających się potrzeb biznesowych.
-   [Monitorowanie](https://www.visualstudio.com/learn/what-is-monitoring/) uruchomionej aplikacji — tym produkcyjnego środowiska kondycji aplikacji, a także użycia przez klientów — pomaga organizacjom tworzą hipoteza i szybko sprawdzić poprawność lub disprove strategii.  Zaawansowana dane zostaną zebrane i przechowywane w różnych formatach rejestrowania.
-   [Infrastruktura jako kodu (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) jest rozwiązaniem umożliwia automatyzacji i sprawdzanie poprawności tworzenia i usuwania sieci i maszyn wirtualnych ułatwiają dostarczanie aplikacji bezpiecznego, stabilna hostingu platformami.
-   [Mikrousług](https://www.visualstudio.com/learn/what-are-microservices/) architektury jest używane do izolowania przypadków użycia biznesowego w małych usług wielokrotnego użytku.  Taka architektura umożliwia stosowanie skalowalność i wydajność.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o rozwiązaniu Operations Management Suite zabezpieczeń i inspekcji, zobacz następujące artykuły:

- [Zabezpieczeń i zgodności](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Monitorowanie i reagowanie na alerty zabezpieczeń w rozwiązaniu Operations Management Suite zabezpieczeń i inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)
- [Monitorowanie zasobów w rozwiązaniu Operations Management Suite zabezpieczeń i inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources)
