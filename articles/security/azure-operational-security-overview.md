---
title: Omówienie kwestii bezpieczeństwa działania platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie kwestii bezpieczeństwa działania platformy Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: tomsh
ms.openlocfilehash: ca9feb226a077484ba43bef7cd14d962ea95b37a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653423"
---
# <a name="azure-operational-security-overview"></a>Omówienie kwestii bezpieczeństwa działania platformy Azure

[Azure operational security](https://docs.microsoft.com/azure/security/azure-operational-security) odwołuje się do usługi, formanty i funkcje dostępne dla użytkowników dotyczące ochrony danych, aplikacji i innych zasobów na platformie Microsoft Azure. To struktura, która zawiera wiedzy uzyskanej dzięki różnym możliwości, które są unikatowe dla firmy Microsoft. Te funkcje obejmują cykl projektowania zabezpieczeń (SDL) firmy Microsoft, program Microsoft Security Response Center oraz głębokiej świadomości krajobraz zagrożeń cyberbezpieczeństwa.

## <a name="azure-management-services"></a>Usługi zarządzania platformą Azure

Dział operacji IT jest odpowiedzialny za zarządzanie infrastrukturę centrów danych, aplikacji i danych, w tym stabilność i bezpieczeństwo tych systemów. Jednak zyskuje wgląd w zabezpieczenia przez zwiększenie złożonych środowisk IT często wymaga organizacjom cobble razem dane z różnych systemów zabezpieczeń i zarządzania.

[Dzienniki systemu Microsoft Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) jest oparta na chmurze rozwiązanie zarządzania IT, która ułatwia zarządzanie i chronić lokalne i infrastruktury chmury. Jego podstawowych funkcji jest zapewniana przez następujących usług, które działają na platformie Azure. System Azure zawiera wiele usług, które ułatwiają zarządzanie, chronić lokalne i infrastruktury chmury. Każda usługa udostępnia określoną funkcję zarządzania. Możesz łączyć usługi, aby realizować różne scenariusze zarządzania. 

### <a name="azure-monitor"></a>Azure Monitor

[Usługa Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) zbiera dane z zarządzanych źródeł do magazynów danych w centralnym. Te dane mogą obejmować zdarzenia, dane dotyczące wydajności i niestandardowe dane dostarczane za pośrednictwem interfejsu API. Po zebraniu danych, jest dostępna dla alertów, analizy i eksportu. 

Można skonsolidować dane z różnych źródeł i połączyć dane z usług platformy Azure z istniejącym środowiskiem w środowisku lokalnym. Dzienniki platformy Azure Monitor również wyraźnie oddziela zbierania danych z akcji wykonywanych na tych danych, aby wszystkie akcje są dostępne dla wszystkich typów danych.

### <a name="automation"></a>Automatyzacja

[Usługa Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) umożliwia automatyzowanie ręcznych, długotrwałych, podatne na błędy i często powtarzanych zadań, które najczęściej są wykonywane w chmurze i środowisku przedsiębiorstwa. Jego oszczędza czas i zwiększyć niezawodność zadań administracyjnych. Planuje ona nawet te zadania mogą być wykonywane automatycznie w regularnych odstępach czasu. Można automatyzować procesy przy użyciu elementów runbook lub automatyzować zarządzanie konfiguracją przy użyciu Desired State Configuration.

### <a name="backup"></a>Backup

[Usługa Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) to usługa opartych na platformie Azure, która umożliwia tworzenie kopii zapasowej (lub ochronę) i przywracanie danych w Microsoft Cloud. Usługa Azure Backup dotychczasowe istniejące lokalne lub rozwiązania tworzenia kopii zapasowych poza siedzibą firmy za pomocą rozwiązania oparte na chmurze, które jest niezawodne, bezpieczne i konkurencyjne cenowo. 

Usługa Azure Backup oferuje składniki, które możesz pobrać i wdrożyć na odpowiednim komputerze lub serwerze lub w chmurze. Wdrażany składnik lub agent zależy od tego, co ma być chronione. Wszystkie składniki usługi Azure Backup (zarówno w przypadku ochrony danych w sieci lokalnej lub w chmurze) może służyć do tworzenia kopii zapasowych w magazynie usług odzyskiwania Azure na platformie Azure. 

Aby uzyskać więcej informacji, zobacz [tabeli składników usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-backup-agent-should-i-use).

### <a name="site-recovery"></a>Site Recovery

[Usługa Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) zapewnia ciągłość działania firmy poprzez organizowanie replikacji lokalnych maszyn wirtualnych i fizycznych do platformy Azure lub lokacji dodatkowej. Jeśli lokacja podstawowa jest niedostępna, przełączysz do lokalizacji dodatkowej, dzięki czemu użytkownicy mogą kontynuować pracę. Możesz zakończyć się niepowodzeniem, gdy systemy wznowią działanie. Usługa Azure Security Center do wykonywania bardziej inteligentne i efektywne wykrywania zagrożeń.

## <a name="azure-active-directory"></a>Usługa Azure Active Directory

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) jest tożsamość kompleksową usługę:

-   Umożliwia zarządzanie tożsamościami i dostępem (IAM) jako usługa w chmurze.
-   Udostępnia centralnego zarządzania dostępem, logowanie jednokrotne (SSO) i raportowania.
-   Obsługuje zarządzanie zintegrowanego dostępu [tysiące aplikacji](https://azure.microsoft.com/marketplace/active-directory/) w witrynie Azure Marketplace, w tym Salesforce, Google Apps, Box i Concur.

Usługa Azure AD zawiera również całą gamę [możliwości zarządzania tożsamościami](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), tym następujące:

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)
- [Samoobsługowe zarządzanie hasłami](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Samoobsługowe zarządzanie grupami](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Zarządzanie uprzywilejowanego konta](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Monitorowanie użycia aplikacji](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Zaawansowane inspekcji](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Monitorowanie zabezpieczeń i alertów](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)

Za pomocą usługi Azure Active Directory wszystkie aplikacje, które publikują dla partnerów i klientów (firmy lub konsumenta) ma inną tożsamość i dostęp do możliwości zarządzania. Dzięki temu można znacznie zmniejszyć koszty operacyjne.

## <a name="azure-security-center"></a>Azure Security Center

[Usługa Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) pomaga zapobiegać zagrożeniom, wykrywać i odpowiadanie na nie dzięki lepszemu wglądowi w (i kontrolę nad) zabezpieczeń zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami dla Twojej subskrypcji. Pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

[Chronić dane maszyny wirtualnej (VM)](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) na platformie Azure, zapewniając wgląd w ustawienia zabezpieczeń maszyny wirtualnej i monitorując zagrożenia. Usługa Security Center może monitorować maszyny wirtualne pod kątem następujących elementów:

- Ustawienia zabezpieczeń systemu operacyjnego z zalecanymi regułami konfiguracji.
- Zabezpieczenia systemu i aktualizacji krytycznych, których nie ma.
- Zalecenia dotyczące ochrony punktu końcowego.
- Weryfikowanie szyfrowania dysków.
- Ataki sieciowe.

Usługa Security Center używa [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). Udostępnia RBAC [wbudowane role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) które można przypisać do użytkowników, grup i usług na platformie Azure.

Usługa Security Center ocenia konfigurację zasobów, aby zidentyfikować problemy dotyczące zabezpieczeń i luki w zabezpieczeniach. W usłudze Security Center możesz zobaczyć informacje związane z zasobem, tylko wtedy, gdy masz przypisaną rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, do której należy zasób.

>[!Note]
>Aby dowiedzieć się więcej o rolach i dozwolonych akcji w usłudze Security Center, zobacz [uprawnień w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-permissions).

Usługa Security Center używa programu Microsoft Monitoring Agent. Jest to ten sam agent, który używa usługi Azure Monitor. Dane zbierane z tego agenta są przechowywane w istniejących analizy dzienników [obszaru roboczego](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) skojarzony z subskrypcją platformy Azure lub nowy obszar roboczy, biorąc pod uwagę geograficzną maszyny Wirtualnej.

## <a name="azure-monitor"></a>Azure Monitor

Problemy z wydajnością w aplikacji w chmurze mogą mieć wpływ na działalność. Za pomocą wielu powiązanych elementów i wersji częste spadku wydajności może nastąpić w dowolnym momencie. I Jeżeli projektujesz aplikację użytkownicy zazwyczaj Odkryj problemy, które nie udało się znaleźć w zakresie testowania. Możesz od razu wiedzieć o tych problemów, a powinien mieć narzędzia do diagnozowania i rozwiązywania problemów.

[Usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) to podstawowe narzędzie do monitorowania usługi działające na platformie Azure. Oferuje on danych na poziomie infrastruktury dotyczące przepływności usługi i otaczającego środowiska. Jeśli zarządzasz swoje aplikacje w platformie Azure i podejmowania decyzji, czy wykonać skalowanie w górę lub w dół zasobów usługi Azure Monitor jest miejscem do rozpoczęcia.

Dane monitorowania umożliwia również uzyskiwanie szczegółowych informacji o aplikacji. Tę wiedzę można pomóc zwiększyć wydajność aplikacji lub łatwość konserwacji lub Automatyzuj akcje, które w przeciwnym razie wymagają ręcznej interwencji. 

Usługa Azure Monitor obejmuje następujące składniki.

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure

[Dziennika aktywności platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewnia wgląd w operacje, które zostały wykonane na komputerze zasobów w ramach subskrypcji. Była ona wcześniej znana jako "Dziennik inspekcji" lub "Dziennik operacyjny", ponieważ wysyła raporty zdarzeń płaszczyznę kontroli dla subskrypcji.

### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

[Dzienniki diagnostyczne platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) są emitowane przez zasób i zapewniają bogate, często dane o działaniu tego zasobu. Zawartość tych dzienników jest zależna od typu zasobu.

Dzienniki systemu zdarzeń Windows są jedną kategorię dzienniki diagnostyczne na potrzeby maszyn wirtualnych. Obiekt blob, tabel i Dzienniki kolejek są kategorie dzienników diagnostycznych dla kont magazynu.

Dzienniki diagnostyczne różnią się od [dziennika aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Dziennik aktywności zapewnia wgląd w operacje, które zostały wykonane na komputerze zasobów w ramach subskrypcji. Dzienniki diagnostyczne udostępniają szczegółowe dane operacji zasobu wykonywane sam.

### <a name="metrics"></a>Metryki

Usługa Azure Monitor udostępnia dane telemetryczne, które zapewnia wgląd w wydajność i kondycja obciążeń na platformie Azure. Typ najważniejszych danych telemetrycznych platformy Azure jest [metryki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (również o nazwie liczników) wyemitowane przez zasoby najbardziej platformy Azure. Usługa Azure Monitor zapewnia kilka sposobów na konfigurowanie i korzystanie z tych metryk monitorowania i rozwiązywania problemów.

### <a name="azure-diagnostics"></a>Diagnostyka Azure

Narzędzie diagnostyczne systemu Azure umożliwia zbieranie danych diagnostycznych na rozmieszczonej aplikacji. Rozszerzenie diagnostyki można użyć z różnych źródeł. Obecnie obsługiwane wartości to [ról usługi w chmurze Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) systemem Microsoft Windows, a [usługi Azure Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).

## <a name="azure-network-watcher"></a>Azure Network Watcher

Klienci kompilacji sieci end-to-end na platformie Azure, poprzez organizowanie i tworzenie sieci poszczególne zasoby, takie jak sieci wirtualne, usługi Azure ExpressRoute, Azure Application Gateway i moduły równoważenia obciążenia. Monitorowanie jest dostępne na każdym z zasobów sieciowych.

Sieć end-to-end może mieć, złożonych konfiguracji i interakcje między zasobami. Wynik jest złożone scenariusze wymagające oparte na scenariuszach monitorowania za pomocą [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

Usługa Network Watcher ułatwia monitorowanie i diagnozowanie sieci platformy Azure. Można użyć narzędzia diagnostyki i wizualizacji w usługi Network Watcher, aby:

- Przechwytywać pakiety zdalne, na maszynie wirtualnej platformy Azure.
- Uzyskaj wgląd w ruchu sieciowego przy użyciu dzienników przepływu.
- Diagnozowanie usługi Azure VPN Gateway i połączeniami.

Usługa Network Watcher jest obecnie ma następujące możliwości:

- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Zapewnia widok różnych połączeń i skojarzenia między zasobami sieci w grupie zasobów.
- [Przechwytywanie pakietów zmiennych](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Umożliwia przechwytywanie danych pakietów do i z maszyny wirtualnej. Bardzo przydatne są zaawansowane opcje filtrowania i dostosowywania ustawień, takie jak określanie czasu i limitów rozmiaru. Dane pakietu mogą być przechowywane w magazynie obiektów blob lub na dysku lokalnym w formacie .cap.
- [Weryfikowanie przepływu protokołu IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): Sprawdza, czy pakiet jest dozwolony lub zablokowany na podstawie 5-elementowe spójne kolekcje pakietów parametrów dla informacji o przepływie (docelowy adres IP, źródłowy adres IP, port docelowy, port źródłowy i protokół). Jeśli grupa zabezpieczeń nie zezwala na pakiet, zwracane są reguły i grupy, która odrzuciła pakiet.
- [Dla następnego przeskoku](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Określa następny przeskok dla pakiety przesyłane w sieci szkieletowej sieci platformy Azure, więc można zdiagnozować wszelkie nieprawidłowo trasy zdefiniowane przez użytkownika.
- [Widok grup zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): Pobiera reguły zabezpieczeń efektywny i zastosowane, które są stosowane na maszynie Wirtualnej.
- [Dzienniki przepływu sieciowych grup zabezpieczeń dla sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview): Umożliwiają przechwytywanie dzienniki dotyczące ruchu, który jest dozwolony lub zablokowany przez zasady zabezpieczeń w grupie. Przepływ jest definiowany przez 5-elementowe spójne kolekcje informacji: źródłowego adresu IP, docelowy adres IP, portu źródłowego, port docelowy i protokołu.
- [Brama sieci wirtualnej i rozwiązywanie problemów z połączeniem](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Umożliwia Rozwiązywanie problemów z bramy sieci wirtualnej i połączenia.
- [Limity subskrypcji sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Umożliwia wyświetlenie użycia zasobów sieciowych limitów.
- [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Zapewnia jedną taflę, aby włączyć lub wyłączyć dzienniki diagnostyczne na potrzeby zasobów sieciowych w grupie zasobów.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie usługi Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="cloud-service-provider-access-transparency"></a>Jawności dostępu do dostawcy usług chmury

[Skrytki klienta dla systemu Microsoft Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/) jest usługą zintegrowana z witryny Azure portal zapewnia jawną kontrolę w wystąpieniu rzadkich gdy inżynier pomocy technicznej firmy Microsoft mogą potrzebować dostępu do danych w celu rozwiązania problemu. Istnieje bardzo niewiele wystąpieniach — na przykład problemem dostępu zdalnego debugowania, gdzie inżynier pomocy technicznej firmy Microsoft wymaga podniesionych uprawnień, aby rozwiązać ten problem. W takich przypadkach inżynierów firmy Microsoft, użyj usługi Dostęp just in time, który zapewnia ograniczone, czasowo autoryzacji dostęp ograniczony do usługi.  
Podczas gdy Microsoft zawsze uzyskał zgody na dostęp, skrytki klienta teraz udostępnia możliwość przejrzeć i zatwierdzić lub odrzucić takie żądania z witryny Azure Portal. Inżynierowie pomocy technicznej firmy Microsoft nie uzyska dostęp do czasu zatwierdzenia żądania.

## <a name="standardized-and-compliant-deployments"></a>Standardowe i zgodne wdrożeń

[Plany usługi Azure](../governance/blueprints/overview.md) Włącz architektów i chmury centralnej informacji o technologii grupy do zdefiniowania powtarzalne zestawu zasobów platformy Azure, które implementuje i stosować się do standardów, wzorców i wymagania w organizacji.  
Dzięki temu możliwe dla zespołów DevOps utworzyć i wdrożyć nowe środowiska i ufać, że jest tworzone je z infrastrukturą, która utrzymuje zgodności organizacyjnej. Plany zapewniają deklaratywną metodę do organizowania wdrożenia różnych szablonów zasobów i innych artefaktów, takich jak: 

- Przypisania ról
- Przypisania zasad
- Szablony usługi Azure Resource Manager
- Grupy zasobów

## <a name="devops"></a>DevOps

Przed [operacji dewelopera (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) rozwoju aplikacji, zespoły zostały odpowiedzialnym za zbieranie wymagania biznesowe w zakresie oprogramowania i pisania kodu. Następnie oddzielny zespół ds. kontroli jakości przetestowano program w środowisku izolowanym rozwoju. Jeśli wymagania zostały spełnione, zespół ds. kontroli jakości wydane kod operacji do wdrożenia. Zespoły są dodatkowo zróżnicowane w grupach, takich jak sieć i baza danych. Każdorazowo oprogramowania "zgłoszono za pośrednictwem ściana" zespołowi niezależnych dodane wąskich gardeł.

W metodyce DevOps zespoły mogą dostarczać bezpieczniejsze, wyższej jakości rozwiązania szybciej i po niższej cenie. Klienci oczekują dynamicznego i niezawodnego działania podczas korzystania z oprogramowania i usług. Zespoły muszą szybkie powtarzanie czynności w aktualizacji oprogramowania i szacować wpływ tych aktualizacji. Klienci muszą szybko reagować iteracje do rozwiązywania problemów lub większą wartość.  

Cloud platform, takich jak Microsoft Azure mają tradycyjne wąskie gardła i ujednolicić postrzeganie infrastruktury. Oprogramowanie reigns każdej firmy klucza wyróżnikiem i czynnikiem. Nie organizacji dla deweloperów i pracowników można lub należy unikać przenoszenia metodyki DevOps.

Dojrzała badaczy DevOps przyjąć kilka z następujących wskazówek. Praktyki te [udział ludzi](https://www.visualstudio.com/learn/what-is-devops-culture/) do formularza tworzeniu strategii na podstawie scenariuszy biznesowych. Narzędzia można zautomatyzować poszczególne praktyki.

- [Zwinne planowanie i zarządzanie projektami](https://www.visualstudio.com/learn/what-is-agile/) umożliwia planowanie i organizowanie pracy w formie przebiegów, zarządzanie wydajnością zespołu i także szybkie przystosowywanie zespołów do zmieniających się potrzeb biznesowych.
- [Kontrola wersji, zwykle za pomocą narzędzia Git](https://www.visualstudio.com/learn/what-is-git/), zapewnia zespołom znajdującym się w dowolnym miejscu na świecie współdzielenie kodu źródłowego i integrację z narzędziami programistycznymi do automatyzacji potoku tworzenia wersji.
- [Ciągła integracja](https://www.visualstudio.com/learn/what-is-continuous-integration/) dysków, scalanie i testowanie kodu, pozwalając wcześnie usterek.  Inne zalety mniej czasu na to, problemy scalania i szybkie opinie dla zespołów deweloperskich, ze.
- [Ciągłe dostarczanie](https://www.visualstudio.com/learn/what-is-continuous-delivery/) rozwiązań programowych do produkcji i testowania pomaga w środowiskach organizacjom szybkie usuwanie usterek i reagowanie na kolejne zmiany wymagań biznesowych.
- [Monitorowanie](https://www.visualstudio.com/learn/what-is-monitoring/) uruchomionych aplikacji — w tym środowisku produkcyjnym środowisk dla kondycji aplikacji, a także użycia przez klientów — umożliwia organizacjom hipotez i szybkie sprawdzanie poprawności lub odrzucanie strategii.  Szczegółowe dane są przechwytywane i przechowywane w różnych formatach.
- [Infrastruktura jako kod (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) jest praktyka, która umożliwia automatyzację i weryfikację tworzenia i usuwania sieci oraz maszyn wirtualnych, aby pomóc w dostarczaniu bezpieczne i stabilne platformy hostowania aplikacji.
- [Mikrousługi](https://www.visualstudio.com/learn/what-are-microservices/) architektury jest używane do izolowania przypadków użycia biznesowego usług wielokrotnego użytku.  Ona w uzyskaniu skalowalności i wydajności.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rozwiązania zabezpieczenia i inspekcja, zobacz następujące artykuły:

- [Zabezpieczenia i zgodność](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)
