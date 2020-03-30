---
title: Omówienie zabezpieczeń operacyjnych platformy Azure| Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie zabezpieczeń operacyjnych platformy Azure.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: bcaf45af4c02dad22e2cc611fa7ea5a32ad3a853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443877"
---
# <a name="azure-operational-security-overview"></a>Omówienie zabezpieczeń operacyjnych platformy Azure

[Zabezpieczenia operacyjne platformy Azure](/azure/security/fundamentals/operational-security) odnoszą się do usług, formantów i funkcji dostępnych dla użytkowników w celu ochrony ich danych, aplikacji i innych zasobów na platformie Microsoft Azure. Jest to struktura, która zawiera wiedzę zdobytą za pośrednictwem różnych możliwości, które są unikatowe dla firmy Microsoft. Te funkcje obejmują cykl życia rozwoju zabezpieczeń firmy Microsoft (SDL), program Microsoft Security Response Center i głęboką świadomość krajobrazu zagrożeń dla cyberbezpieczeństwa.

## <a name="azure-management-services"></a>Usługi zarządzania platformy Azure

Zespół operacyjny IT jest odpowiedzialny za zarządzanie infrastrukturą, aplikacjami i danymi centrów danych, w tym stabilność i bezpieczeństwo tych systemów. Jednak uzyskiwanie szczegółowych informacji o zabezpieczeniach w coraz bardziej złożonych środowiskach IT często wymaga od organizacji tworzenia danych z wielu systemów zabezpieczeń i zarządzania.

[Dzienniki usługi Microsoft Azure Monitor](/azure/operations-management-suite/operations-management-suite-overview) to oparte na chmurze rozwiązanie do zarządzania it, które pomaga zarządzać infrastrukturą lokalną i chmurową oraz chronić ją. Jego podstawowe funkcje są dostarczane przez następujące usługi, które są uruchamiane na platformie Azure. Platforma Azure zawiera wiele usług, które ułatwiają zarządzanie infrastrukturą lokalną i chmurową oraz ich ochronę. Każda usługa zapewnia określoną funkcję zarządzania. Można połączyć usługi, aby osiągnąć różne scenariusze zarządzania. 

### <a name="azure-monitor"></a>Azure Monitor

[Usługa Azure Monitor](/azure/azure-monitor/overview) zbiera dane ze źródeł zarządzanych do centralnych magazynów danych. Dane te mogą obejmować zdarzenia, dane dotyczące wydajności lub dane niestandardowe dostarczane za pośrednictwem interfejsu API. Po zebraniu danych jest on dostępny do alertów, analizy i eksportu.

Można konsolidować dane z różnych źródeł i łączyć dane z usług platformy Azure z istniejącym środowiskiem lokalnym. Dzienniki usługi Azure Monitor również wyraźnie oddziela zbieranie danych od akcji podjętej na tych danych, dzięki czemu wszystkie akcje są dostępne dla wszystkich rodzajów danych.

### <a name="automation"></a>Automatyzacja

[Usługa Azure Automation](/azure/automation/automation-intro) umożliwia automatyzację ręcznych, długotrwałych, podatnych na błędy i często powtarzanych zadań, które są często wykonywane w środowisku chmury i przedsiębiorstwa. Oszczędza czas i zwiększa niezawodność zadań administracyjnych. Planuje nawet te zadania do automatycznego wykonywania w regularnych odstępach czasu. Procesy można zautomatyzować za pomocą śmigieł lub zautomatyzować zarządzanie konfiguracją przy użyciu konfiguracji żądanego stanu.

### <a name="backup"></a>Tworzenie kopii zapasowych

[Usługa Azure Backup](/azure/backup/backup-introduction-to-azure-backup) to usługa oparta na platformie Azure, której można użyć do tworzenia kopii zapasowych (lub ochrony) i przywracania danych w chmurze Microsoft Cloud. Usługa Azure Backup zastępuje istniejące lokalne lub poza siedzibą firmy rozwiązanie do tworzenia kopii zapasowych rozwiązaniem chmurowym, które jest niezawodne, bezpieczne i konkurencyjne pod względem kosztów.

Usługa Azure Backup oferuje składniki pobierane i wdrażane na odpowiednim komputerze lub serwerze lub w chmurze. Wdrażany składnik lub agent zależy od tego, co ma być chronione. Wszystkie składniki usługi Azure Backup (niezależnie od tego, czy chronisz dane lokalnie, czy w chmurze) mogą służyć do tworzenia kopii zapasowych danych w magazynie usług Azure Recovery Services na platformie Azure.

Aby uzyskać więcej informacji, zobacz [tabelę składników usługi Azure Backup](/azure/backup/backup-overview#what-can-i-back-up).

### <a name="site-recovery"></a>Site Recovery

[Usługa Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) zapewnia ciągłość działania, organizując replikację lokalnych maszyn wirtualnych i fizycznych na platformie Azure lub w lokacji dodatkowej. Jeśli twoja lokacja główna jest niedostępna, przeładujesz ją w lokacji dodatkowej, aby użytkownicy mogli kontynuować pracę. Powrót awaryjny po powrocie systemów do zejście do pracy. Usługa Azure Security Center umożliwia bardziej inteligentne i skuteczne wykrywanie zagrożeń.

## <a name="azure-active-directory"></a>Usługa Azure Active Directory

[Usługa Azure Active Directory (Azure AD)](/azure/active-directory/active-directory-enable-sso-scenario) to kompleksowa usługa tożsamości, która:

-   Włącza zarządzanie tożsamością i dostępem (IAM) jako usługę w chmurze.
-   Zapewnia centralne zarządzanie dostępem, logowanie jednokrotne (logowanie jednokrotne) i raportowanie.
-   Obsługuje zintegrowane zarządzanie dostępem dla [tysięcy aplikacji](https://azure.microsoft.com/marketplace/active-directory/) w portalu Azure Marketplace, w tym Salesforce, Google Apps, Box i Concur.

Usługa Azure AD zawiera również pełny zestaw [funkcji zarządzania tożsamościami,](/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports)w tym następujące:

- [Uwierzytelnianie wieloskładnikowe](/azure/multi-factor-authentication/multi-factor-authentication)
- [Samoobsługowe zarządzanie hasłami](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Zarządzanie grupami samoobsługowymi](/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Zarządzanie kontami uprzywilejowanymi](/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Kontrola dostępu oparta na rolach](/azure/role-based-access-control/overview)
- [Monitorowanie użycia aplikacji](/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Rozbudowa inspekcji](/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Monitorowanie i ostrzeganie o bezpieczeństwie](/azure/operations-management-suite/oms-security-responding-alerts)

Dzięki usłudze Azure Active Directory wszystkie aplikacje publikowane dla partnerów i klientów (biznesowych lub konsumenckich) mają takie same funkcje zarządzania tożsamościami i dostępem. Pozwala to znacznie obniżyć koszty operacyjne.

## <a name="azure-security-center"></a>Azure Security Center

[Usługa Azure Security Center](/azure/security-center/security-center-intro) pomaga zapobiegać zagrożeniom, wykrywać ich i reagować na nie, zwiększając wgląd w zabezpieczenia zasobów platformy Azure (i kontrolę nad nim). Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji. Pomaga wykrywać zagrożenia, które w przeciwnym razie mogą przejść niezauważone, i współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

[Chroń dane maszyny wirtualnej na](/azure/security-center/security-center-linux-virtual-machine) platformie Azure, zapewniając wgląd w ustawienia zabezpieczeń maszyny wirtualnej i monitorowanie zagrożeń. Usługa Security Center może monitorować maszyny wirtualne pod kątem następujących elementów:

- Ustawienia zabezpieczeń systemu operacyjnego z zalecanymi regułami konfiguracji.
- Brak zabezpieczeń systemu i aktualizacji krytycznych.
- Zalecenia dotyczące ochrony punktów końcowych.
- Sprawdzanie poprawności szyfrowania dysku.
- Ataki sieciowe.

Usługa Security Center używa [kontroli dostępu opartej na rolach (RBAC).](/azure/role-based-access-control/role-assignments-portal) RBAC udostępnia [wbudowane role,](../../role-based-access-control/built-in-roles.md) które można przypisać do użytkowników, grup i usług na platformie Azure.

Usługa Security Center ocenia konfigurację zasobów w celu zidentyfikowania problemów z zabezpieczeniami i luk w zabezpieczeniach. W U centrum zabezpieczeń informacje związane z zasobem są wyświetlane tylko wtedy, gdy przypisano ci rolę właściciela, współautora lub czytelnika dla subskrypcji lub grupy zasobów, do której należy zasób.

>[!Note]
>Aby dowiedzieć się więcej o rolach i dozwolonych akcjach w Usłudze Security Center, zobacz [Uprawnienia w usłudze Azure Security Center](/azure/security-center/security-center-permissions).

Usługa Security Center używa agenta monitorowania firmy Microsoft. Jest to ten sam agent, którego używa usługa Azure Monitor. Dane zebrane z tego agenta są przechowywane w istniejącym [obszarze roboczym](/azure/log-analytics/log-analytics-manage-access) usługi Log Analytics skojarzonym z subskrypcją platformy Azure lub nowym obszarze roboczym, biorąc pod uwagę geolokalizacji maszyny Wirtualnej.

## <a name="azure-monitor"></a>Azure Monitor

Problemy z wydajnością w aplikacji w chmurze mogą mieć wpływ na Twoją firmę. W przypadku wielu połączonych ze sobą komponentów i częstych wydań degradacja może nastąpić w dowolnym momencie. A jeśli tworzysz aplikację, użytkownicy zwykle odkrywają problemy, których nie znalazłeś podczas testowania. Powinieneś wiedzieć o tych problemach natychmiast, i powinieneś mieć narzędzia do diagnozowania i rozwiązywania problemów.

[Usługa Azure Monitor](/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) to podstawowe narzędzie do monitorowania usług uruchomionych na platformie Azure. Zapewnia dane na poziomie infrastruktury dotyczące przepływności usługi i otaczającego środowiska. Jeśli zarządzasz aplikacjami na platformie Azure i decydujesz, czy chcesz skalować zasoby w górę czy w dół, usługa Azure Monitor jest miejscem, w które można rozpocząć.

Można również użyć danych monitorowania, aby uzyskać szczegółowe informacje na temat aplikacji. Ta wiedza może pomóc w zwiększce wydajności aplikacji lub łatwości konserwacji lub zautomatyzowaniu działań, które w przeciwnym razie wymagałyby ręcznej interwencji.

Usługa Azure Monitor zawiera następujące składniki.

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure

Dziennik [aktywności platformy Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewnia wgląd w operacje, które zostały wykonane na zasoby w ramach subskrypcji. Wcześniej był znany jako "Dziennik inspekcji" lub "Dziennik operacyjny", ponieważ raportuje zdarzenia płaszczyzny sterowania dla subskrypcji.

### <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

[Dzienniki diagnostyczne platformy Azure](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) są emitowane przez zasób i zapewniają bogate, częste dane dotyczące działania tego zasobu. Zawartość tych dzienników różni się w zależności od typu zasobu.

Dzienniki systemu zdarzeń systemu Windows są jedną kategorią dzienników diagnostycznych dla maszyn wirtualnych. Dzienniki obiektów blob, tabeli i kolejek są kategoriami dzienników diagnostycznych dla kont magazynu.

Dzienniki diagnostyczne różnią się od [dziennika aktywności](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Dziennik aktywności zapewnia wgląd w operacje, które zostały wykonane na zasoby w ramach subskrypcji. Dzienniki diagnostyczne zapewniają wgląd w operacje wykonywane przez zasób.

### <a name="metrics"></a>Metryki

Usługa Azure Monitor udostępnia dane telemetryczne, które zapewniają wgląd w wydajność i kondycję obciążeń na platformie Azure. Najważniejszym typem danych telemetrycznych platformy Azure są [metryki](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (nazywane również licznikami wydajności) emitowane przez większość zasobów platformy Azure. Usługa Azure Monitor udostępnia kilka sposobów konfigurowania i korzystania z tych metryk do monitorowania i rozwiązywania problemów.

### <a name="azure-diagnostics"></a>Diagnostyka Azure

Usługa Azure Diagnostics umożliwia zbieranie danych diagnostycznych w wdrożonej aplikacji. Rozszerzenie diagnostyki można użyć z różnych źródeł. Obecnie obsługiwane są [role usługi w chmurze platformy Azure,](/azure/vs-azure-tools-configure-roles-for-cloud-service)maszyny [wirtualne platformy Azure](/azure/vs-azure-tools-configure-roles-for-cloud-service) z systemem Microsoft Windows i sieć [szkieletowa usług Azure.](/azure/monitoring-and-diagnostics/azure-diagnostics)

## <a name="azure-network-watcher"></a>Azure Network Watcher

Klienci tworzą sieć end-to-end na platformie Azure, organizując i redagując poszczególne zasoby sieciowe, takie jak sieci wirtualne, usługa Azure ExpressRoute, usługa Azure Application Gateway i moduły równoważenia obciążenia. Monitorowanie jest dostępne w każdym zasobach sieciowych.

Sieć end-to-end może mieć złożone konfiguracje i interakcje między zasobami. Rezultatem są złożone scenariusze, które wymagają monitorowania opartego na scenariuszach za pośrednictwem [usługi Azure Network Watcher.](/azure/network-watcher/network-watcher-monitoring-overview)

Kontrola sieci upraszcza monitorowanie i diagnozowanie sieci platformy Azure. Narzędzia diagnostyczne i wizualizacji w funkcji Kontrola sieci umożliwiają:

- Przechwytywanie pakietów zdalnych na maszynie wirtualnej platformy Azure.
- Uzyskaj wgląd w ruch sieciowy za pomocą dzienników przepływu.
- Diagnozowanie bramy sieci VPN platformy Azure i połączeń.

Kontrola sieci ma obecnie następujące możliwości:

- [Topologia](/azure/network-watcher/network-watcher-topology-overview): Umożliwia widok różnych połączeń i skojarzeń między zasobami sieciowymi w grupie zasobów.
- [Przechwytywanie pakietów zmiennych:](/azure/network-watcher/network-watcher-packet-capture-overview)przechwytuje dane pakietów do i z maszyny wirtualnej. Bardzo przydatne są zaawansowane opcje filtrowania i dostosowywania ustawień, takie jak określanie czasu i limitów rozmiaru. Dane pakietów mogą być przechowywane w magazynie obiektów blob lub na dysku lokalnym w formacie .cap.
- [Weryfikacja przepływu IP:](/azure/network-watcher/network-watcher-ip-flow-verify-overview)Sprawdza, czy pakiet jest dozwolony lub odrzucony na podstawie 5-krotki parametrów pakietu dla informacji o przepływie (docelowy adres IP, źródłowy adres IP, port docelowy, port źródłowy i protokół). Jeśli grupa zabezpieczeń odrzuca pakiet, zwracana jest reguła i grupa, które odmówiły pakietu.
- [Następny przeskok:](/azure/network-watcher/network-watcher-next-hop-overview)Określa następny przeskok dla pakietów kierowanych w sieci szkieletowej platformy Azure, dzięki czemu można zdiagnozować wszystkie nieprawidłowo skonfigurowane trasy zdefiniowane przez użytkownika.
- [Widok grupy zabezpieczeń:](/azure/network-watcher/network-watcher-security-group-view-overview)Pobiera skuteczne i stosowane reguły zabezpieczeń, które są stosowane na maszynie Wirtualnej.
- [Dzienniki przepływu sieciowej grupy zabezpieczeń sieciowych:](/azure/network-watcher/network-watcher-nsg-flow-logging-overview)Umożliwia przechwytywanie dzienników związanych z ruchem dozwolonym lub odrzuconym przez reguły zabezpieczeń w grupie. Przepływ jest zdefiniowany przez 5-krotka informacji: źródłowy adres IP, docelowy adres IP, port źródłowy, port docelowy i protokół.
- [Rozwiązywanie problemów z bramą sieci wirtualnej i połączeniami:](/azure/network-watcher/network-watcher-troubleshoot-manage-rest)Umożliwia rozwiązywanie problemów z bramami i połączeniami sieci wirtualnej.
- [Limity subskrypcji sieciowych:](/azure/network-watcher/network-watcher-monitoring-overview)umożliwia wyświetlanie użycia zasobów sieciowych względem limitów.
- [Dzienniki diagnostyczne:](/azure/network-watcher/network-watcher-monitoring-overview)Udostępnia pojedyncze okienko umożliwiające włączenie lub wyłączenie dzienników diagnostycznych dla zasobów sieciowych w grupie zasobów.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie obserwatora sieciowego](/azure/network-watcher/network-watcher-create).

## <a name="cloud-service-provider-access-transparency"></a>Przejrzystość dostępu dostawcy usług w chmurze

[Skrytka klienta dla platformy Microsoft Azure](customer-lockbox-overview.md) to usługa zintegrowana z portalem Azure, która zapewnia wyraźną kontrolę w rzadkim wystąpieniu, gdy inżynier pomocy technicznej firmy Microsoft może potrzebować dostępu do danych, aby rozwiązać problem.
Istnieje bardzo niewiele wystąpień, takich jak debugowanie problemu dostępu zdalnego, gdzie Inżynier pomocy technicznej firmy Microsoft wymaga podwyższonych uprawnień, aby rozwiązać ten problem. W takich przypadkach inżynierowie firmy Microsoft korzystają z usługi dostępu just-in-time, która zapewnia ograniczoną, ograniczoną autoryzację z dostępem ograniczonym do usługi.  
Firma Microsoft zawsze uzyskała zgodę klienta na dostęp, ale skrytka klienta umożliwia teraz przeglądanie i zatwierdzanie lub odrzucanie takich żądań z witryny Azure Portal. Inżynierowie pomocy technicznej firmy Microsoft nie otrzymają dostępu, dopóki nie zatwierdzisz żądania.

## <a name="standardized-and-compliant-deployments"></a>Wdrożenia standardowe i zgodne

[Plany platformy Azure](/azure/governance/blueprints/overview) umożliwiają architektom chmury i centralnym grupom technologii informacyjnych definiowanie powtarzalny zestaw zasobów platformy Azure, które implementują i przestrzegają standardów, wzorców i wymagań organizacji.  
Dzięki temu zespoły DevOps mogą szybko tworzyć i wytrzymywać nowe środowiska i ufać, że budują je za pomocą infrastruktury, która utrzymuje zgodność z wymogami organizacyjnymi.
Plany zapewniają deklaratywny sposób organizowania wdrażania różnych szablonów zasobów i innych artefaktów, takich jak:

- Przypisania ról
- Przypisania zasad
- Szablony usługi Azure Resource Manager
- Grupy zasobów

## <a name="devops"></a>DevOps

Przed tworzeniem aplikacji [deweloperskich (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) zespoły były odpowiedzialne za zbieranie wymagań biznesowych dla programu i pisanie kodu. Następnie oddzielny zespół qa przetestował program w odizolowanym środowisku programistycznym. Jeśli wymagania zostały spełnione, zespół kontroli jakości wydał kod operacji do wdrożenia. Zespoły wdrożeniowe zostały dodatkowo podzielone na grupy, takie jak sieci i bazy danych. Za każdym razem, gdy program był "rzucany przez ścianę" do niezależnego zespołu, dodał wąskie gardła.

DevOps umożliwia zespołom szybsze i tańsze dostarczanie bezpieczniejszych, wyższej jakości rozwiązań. Klienci oczekują dynamicznego i niezawodnego środowiska podczas korzystania z oprogramowania i usług. Zespoły muszą szybko iterować aktualizacje oprogramowania i mierzyć wpływ aktualizacji. Muszą szybko reagować za pomocą nowych iteracji rozwoju, aby rozwiązać problemy lub zapewnić większą wartość.  

Platformy w chmurze, takie jak Microsoft Azure, usunęły tradycyjne wąskie gardła i pomogły w ykońcje infrastrukturę. Oprogramowanie króluje w każdej firmie jako kluczowy wyróżnik i czynnik w wynikach biznesowych. Żadna organizacja, deweloper lub pracownik IT nie może lub powinien unikać ruchu DevOps.

Starsi praktycy DevOps przyjmują kilka z następujących praktyk. Praktyki te [obejmują ludzi](https://www.visualstudio.com/learn/what-is-devops-culture/) do tworzenia strategii w oparciu o scenariusze biznesowe. Oprzyrządowanie może pomóc zautomatyzować różne praktyki.

- [Zwinne techniki planowania i zarządzania projektami](https://www.visualstudio.com/learn/what-is-agile/) są używane do planowania i izolowania pracy w sprinty, zarządzania pojemnościami zespołów i pomagania zespołom w szybkim dostosowywaniu się do zmieniających się potrzeb biznesowych.
- [Kontrola wersji, zwykle z Git,](https://www.visualstudio.com/learn/what-is-git/)umożliwia zespołom zlokalizowanym w dowolnym miejscu na świecie udostępnianie źródła i integrację z narzędziami do tworzenia oprogramowania w celu zautomatyzowania potoku wydań.
- [Ciągła integracja](https://www.visualstudio.com/learn/what-is-continuous-integration/) napędza trwające scalanie i testowanie kodu, co prowadzi do wczesnego znajdowania wad.  Inne korzyści obejmują mniej czasu zmarnowanego na walkę z problemami z fuzją i szybkie sprzężenie zwrotne dla zespołów programistycznych.
- [Ciągłe dostarczanie](https://www.visualstudio.com/learn/what-is-continuous-delivery/) rozwiązań programowych do środowisk produkcyjnych i testowych pomaga organizacjom szybko naprawiać błędy i reagować na stale zmieniające się wymagania biznesowe.
- [Monitorowanie](https://www.visualstudio.com/learn/what-is-monitoring/) uruchomionych aplikacji — w tym środowisk produkcyjnych dla kondycji aplikacji, a także użycia klienta — pomaga organizacjom tworzyć hipotezę i szybko sprawdzać lub obalać strategie.  Bogate dane są przechwytywane i przechowywane w różnych formatach rejestrowania.
- [Infrastruktura jako kod (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) to praktyka, która umożliwia automatyzację i walidację tworzenia i niszczenia sieci i maszyn wirtualnych, aby pomóc w dostarczaniu bezpiecznych, stabilnych platform hostingowych aplikacji.
- [Architektura mikrousług](https://www.visualstudio.com/learn/what-are-microservices/) służy do izolowania przypadków użycia biznesowego w małych usługach wielokrotnego użytku.  Ta architektura umożliwia skalowalność i wydajność.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o rozwiązaniu w zakresie zabezpieczeń i inspekcji, zobacz następujące artykuły:

- [Bezpieczeństwo i zgodność](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Security Center](/azure/security-center/security-center-intro)
- [Azure Monitor](/azure/azure-monitor/overview)
