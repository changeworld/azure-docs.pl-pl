---
title: Usługa Azure Operational najlepsze rozwiązania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw najlepszych rozwiązań dla usługi Azure Operational zabezpieczeń.
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
ms.openlocfilehash: 75f0d7ffaf92558b16605c29cd99ee439ab2e3d8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="azure-operational-security-best-practices"></a>Usługa Azure Operational najlepsze rozwiązania
Azure bezpieczeństwa operacyjnego odwołuje się do usług, formanty i funkcje dostępne dla użytkowników do ochrony danych, aplikacji i innych zasobów na platformie Microsoft Azure. Operacyjne zabezpieczeń platformy Azure w oparciu o strukturę, która zawiera wiedzę za pośrednictwem różnych funkcji, które są unikatowe dla firmy Microsoft, w tym Microsoft Security Development Lifecycle (SDL), program Microsoft Security Response Center i głębokie pogłębianie wiedzy na temat zagrożeń bezpieczeństwa.

W tym artykule omówiono kolekcja najlepszych rozwiązań dotyczących zabezpieczeń bazy danych platformy Azure. Następujące najlepsze rozwiązania są uzyskiwane z wiemy z doświadczenia z zabezpieczeń bazy danych platformy Azure i doświadczenia klientów, takich jak samodzielnie.

Dla każdego najlepszym rozwiązaniem prezentujemy zasady:
-   Co to jest najlepszym rozwiązaniem
-   Dlaczego chcesz włączyć tej najlepsze praktyki
-   Jeśli nie zostanie włączone najlepszym rozwiązaniem, co może być skutkiem
- Jak można znaleźć umożliwiające najlepsze praktyki

W tym artykule Azure zabezpieczeń najlepsze rozwiązanie w zakresie jest na podstawie opinii konsensu i funkcji platformy Azure i zestawy funkcji istniejących w chwili ten artykuł dotyczy. Opinie i technologie ulegną zmianom, a ten artykuł będzie aktualizowany na bieżąco w celu odzwierciedlenia tych zmian.

Usługa Azure Operational najlepsze rozwiązania omówione w tym artykule obejmują:

-   Monitorowanie, zarządzanie i ochrona infrastruktury chmury
-   Zarządzanie tożsamościami i implementować rejestracji jednokrotnej (SSO)
-   Śledzenie żądań, analizować trendy użycia i diagnozowanie problemów
-   Monitorowanie usług za pomocą scentralizowanego rozwiązania monitorowania
-   Zapobiegania, wykrywania i reagowania na zagrożenia
-   Monitorowanie sieci oparta na scenariuszu end-to-end
-   Zabezpieczanie wdrożenia przy użyciu narzędzia do opracowywania oprogramowania sprawdzoną

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>Monitorowanie, zarządzanie i ochrona infrastruktury chmury
Dział operacji IT jest odpowiedzialny za zarządzanie infrastruktury centrum danych, aplikacji i danych, w tym stabilności i bezpieczeństwa tych systemów. Jednak uzyskania szczegółowych informacji o zabezpieczeniach przez zwiększenie złożonych środowiskach IT często wymaga organizacjom cobble razem danych z wielu systemów zabezpieczeń i zarządzania.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) firmy Microsoft w chmurze IT rozwiązanie do zarządzania ułatwiające zarządzanie i ochrona lokalnej infrastruktury w chmurze.

[Rozwiązania pakietu OMS zabezpieczeń i inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) umożliwiają działowi IT aktywnie monitoruje wszystkie zasoby, które mogą pomóc zminimalizować wpływ zdarzeń zabezpieczeń. Zabezpieczenia OMS i inspekcji ma domen zabezpieczeń, które mogą służyć do monitorowania zasobów.

Aby uzyskać więcej informacji na temat pakietu OMS, przeczytaj artykuł [Omówienie pakietu Operations Management Suite (OMS)](https://technet.microsoft.com/library/mt484091.aspx).

Aby zapobiec, wykrywania i reagowania na zagrożenia, [zabezpieczeń Operations Management Suite (OMS) i rozwiązania inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) zbiera i przetwarza dane dotyczące zasobów, w tym:

-   Dziennik zdarzeń zabezpieczeń
-   Zdarzenia funkcji Śledzenie zdarzeń systemu Windows (ETW)
-   Zdarzenia inspekcji funkcji AppLocker
-   Dziennik zapory systemu Windows
-   Zdarzenia rozwiązania Advanced Threat Analytics
-   Wyniki oceny linii bazowej
-   Wyniki oceny oprogramowania chroniącego przed złośliwym kodem
-   Wyniki oceny aktualizacji/poprawek
-   Strumienie SYSLOG, które zostały jawnie włączone w agencie


## <a name="manage-identity-and-implement-single-sign-on"></a>Zarządzanie tożsamościami i implementowanie logowania jednokrotnego
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jest katalog wielu dzierżawców w chmurze firmy Microsoft i tożsamość usługi zarządzania.

[Usługi Azure AD](https://azure.microsoft.com/services/active-directory/) zawiera również pełny pakiet [Zarządzanie tożsamościami](https://docs.microsoft.com/azure/security/security-identity-management-overview) możliwości, w tym [uwierzytelnianie wieloskładnikowe](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), rejestracji urządzenia, zarządzanie hasłami samoobsługi, Samoobsługowe zarządzanie grupami, uprzywilejowane konto zarządzania, kontroli dostępu opartej na rolach, monitorowania, sformatowanego inspekcji i zabezpieczeń, monitorowanie i alerty użycia aplikacji.

Następujące możliwości można zabezpieczenia aplikacji opartej na chmurze, usprawnić procesów IT, zmniejszyć koszty i zapewnienia, że są spełnione cele zgodność z zasadami firmowymi:

-   Zarządzanie tożsamościami i dostępem w chmurze
-   Prosty dostęp użytkowników do dowolnej aplikacji w chmurze
-   Ochrona cennych danych i aplikacji
-   Samoobsługa dostępna dla wszystkich pracowników
-   Integracja z usługą Azure Active Directory

### <a name="identity-and-access-management-for-the-cloud"></a>Zarządzanie tożsamościami i dostępem w chmurze
Azure Active Directory (Azure AD) jest zaawansowane [rozwiązanie chmury zarządzania tożsamościami i dostępem](https://www.microsoft.com/cloud-platform/identity-management), które zapewnia rozbudowany zestaw funkcji do zarządzania użytkownikami i grupami. Pomaga bezpieczny dostęp do lokalnych aplikacji i w chmurze, takich jak usługi sieci web firmy Microsoft, takich jak Office 365 i znacznie oprogramowania firmy Microsoft jako aplikacje usługi (SaaS).
Aby dowiedzieć się więcej włączania ochrony tożsamości w usłudze Azure AD, zobacz [włączania usługi Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).

### <a name="simplify-user-access-to-any-cloud-app"></a>Prosty dostęp użytkowników do dowolnej aplikacji w chmurze
[Włącz rejestrację jednokrotną](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) uprościć dostęp użytkownika do tysięcy aplikacji w chmurze z urządzeń z systemem Windows, Mac, Android i iOS. Użytkownicy, można uruchomić aplikacji z poziomu panelu spersonalizowany dostęp za pośrednictwem sieci web lub aplikacji mobilnej przy użyciu swoich poświadczeń firmowych. Moduł serwera Proxy aplikacji usługi Azure AD umożliwia wykracza poza aplikacji SaaS i publikowanie aplikacji sieci web lokalnie zapewnienie wysokiej bezpieczny dostęp zdalny oraz logowanie jednokrotne.

### <a name="protect-sensitive-data-and-applications"></a>Ochrona cennych danych i aplikacji
Włącz [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) aby uniemożliwić nieautoryzowany dostęp do lokalnych i aplikacji w chmurze, zapewniając dodatkowy poziom uwierzytelniania. Chroń swoją firmę i eliminuj potencjalne zagrożenia dzięki funkcji monitorowania zabezpieczeń, alertom oraz raportom opartym na uczeniu maszynowym, które identyfikują niespójne wzorce dostępu.

### <a name="enable-self-service-for-your-employees"></a>Samoobsługa dostępna dla wszystkich pracowników
Możesz zlecać pracownikom ważne zadania, takie jak resetowanie haseł oraz tworzenie grup i zarządzanie nimi. [Włącz zmianę hasła samoobsługi](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), Resetuj i samoobsługi grupy zarządzania z usługą Azure AD.

### <a name="integrate-with-azure-active-directory"></a>Integracja z usługą Azure Active Directory
Rozszerzanie [usługi Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) i wszystkimi innymi lokalnymi katalogi do usługi Azure AD, aby włączyć logowanie jednokrotne dla wszystkich aplikacji opartej na chmurze. Atrybuty użytkowników można automatycznie synchronizować z katalogiem w chmurze ze wszystkich rodzajów katalogów lokalnych.

Aby dowiedzieć się więcej na temat integracji Azure Active Directory oraz jak je włączyć, przeczytaj artykuł [integrację katalogów lokalnych z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>Śledzenie żądań, analizować trendy użycia i diagnozowanie problemów
[Analizy usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-analytics) wykonuje rejestrowanie i udostępnia metryki danych dla konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu.

Metryki analityka magazynu są domyślnie włączone dla nowego konta magazynu. Można włączyć i skonfigurować metryki i rejestrowanie w portalu Azure; Aby uzyskać więcej informacji, zobacz [monitorować konto magazynu w portalu Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Można również włączyć analityka magazynu programowo przy użyciu interfejsu API REST lub biblioteka klienta. Włącz analizy magazynu osobno dla każdej usługi za pomocą operacji ustawić właściwości usługi.

Aby uzyskać szczegółowy przewodnik przy użyciu analizy magazynu i innych narzędzi do identyfikacji, diagnozowanie i rozwiązywanie problemów związanych z usługą Azure Storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Magazyn Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Aby dowiedzieć się więcej na temat integracji Azure Active Directory oraz jak je włączyć, przeczytaj artykuł na temat [Włączanie i konfigurowanie analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN).

## <a name="monitoring-services"></a>Monitorowanie usług
Aplikacje w chmurze są złożonych z wielu części ruchu. Monitorowanie zawiera danych, aby upewnić się, że aplikacja pozostaje w górę i działa w dobrej kondycji. Pomaga również umożliwia stave potencjalne problemy i rozwiązywanie problemów w przeszłości te. Ponadto można użyć danych monitorowania w celu uzyskania szczegółowych informacji o aplikacji. Wiedzy może pomóc zwiększyć wydajność aplikacji lub utrzymania lub automatyzować czynności, które w przeciwnym razie wymagają ręcznej interwencji.

### <a name="monitor-azure-resources"></a>Monitorowanie zasobów platformy Azure
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) to usługa platformy, która zapewnia jednego źródła do monitorowania zasobów platformy Azure. Dzięki usłudze Azure Monitor możesz wykonywać wizualizacje i zapytania, ustalać trasy, archiwizować i podejmować działania dotyczące metryk i dzienników pochodzących z zasobów na platformie Azure. Możesz pracować z tymi danymi przy użyciu bloku portalu Monitor, [poleceń cmdlet programu PowerShell usługi Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), [międzyplatformowego interfejsu wiersza polecenia](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) lub [interfejsów API REST usługi Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).

### <a name="enable-autoscale-with-azure-monitor"></a>Włączanie automatycznego skalowania z Monitorem systemu Azure
Włącz [skalowania automatycznego Monitor Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started) dotyczy tylko zestawy skalowania maszyny wirtualnej (VMSS), usługi w chmurze, planów usługi aplikacji i środowiska usługi app service.

### <a name="manage-roles-permissions-and-security"></a>Zarządzaj rolami uprawnień i zabezpieczeń
Wiele zespołów należy ściśle [regulowania dostępu do monitorowania](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security) danych i ustawień. Na przykład, jeśli masz członków zespołu, którzy używają wyłącznie na monitorowanie (pracowników działu pomocy technicznej, metodyki devops engineers) lub jeśli korzystasz z dostawcą usługi zarządzanej, można przyznać im dostęp do danych tylko do monitorowania podczas ograniczania możliwość tworzenia, modyfikowania, lub Usuwanie zasobów.

To pokazano, jak szybko zastosować wbudowane monitorowania roli RBAC użytkownikowi na platformie Azure lub tworzenie własnych niestandardowych ról dla użytkownika, który wymaga ograniczonych uprawnień monitorowania. Następnie zawiera omówienie zagadnienia dotyczące zabezpieczeń dla zasobów związanych z monitora Azure i jak można ograniczyć dostęp do danych, które zawierają.

## <a name="prevent-detect-and-respond-to-threats"></a>Zapobiegania, wykrywania i reagowania na zagrożenia
Wykrywanie zagrożeń Centrum zabezpieczeń polega na automatyczne zbieranie informacji o zabezpieczeniach z zasobów platformy Azure, sieci i rozwiązań partnerskich połączonych. Te informacje, często korelowanie informacji z wielu źródeł, aby zidentyfikować zagrożenia jego analizy. Alerty zabezpieczeń wraz z zaleceniami dotyczącymi usuwania zagrożeń są traktowane przez usługę Security Center priorytetowo.

-   [Konfigurowanie zasad zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-policies) dla subskrypcji platformy Azure.
-   Użyj [zalecenia w Centrum zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-recommendations) ułatwiają ochronę zasobów platformy Azure.
-   Przejrzyj i bieżące zarządzanie [alerty zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).

Usługa [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

Centrum zabezpieczeń zapewnia łatwe i skuteczne zapobieganie zagrożeniom, ich wykrywanie oraz wbudowane w platformę Azure możliwości reakcji na nie. Do najważniejszych możliwości należą:

-   Zrozumienie stan zabezpieczeń chmury
-   Przejmij kontrolę nad zabezpieczeniami chmury
-   Łatwe wdrażanie zintegrowanych rozwiązań zabezpieczeń w chmurze
-   Wykrywanie zagrożeń i szybkie reagowanie

### <a name="understand-cloud-security-state"></a>Zrozumienie stan zabezpieczeń chmury
Usługa Azure Security Center daje pełny widok stanu bezpieczeństwa wszystkich Twoich zasobów platformy Azure. Jeden rzut oka Sprawdź, czy odpowiednie środki zabezpieczające są stosowane i poprawnie skonfigurowana i szybką identyfikację wszelkich zasobów, które wymagają uwagi.

### <a name="take-control-of-cloud-security"></a>Przejmij kontrolę nad zabezpieczeniami chmury
Zdefiniuj [zasady zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-policies) dla Twojej subskrypcji platformy Azure zgodnie z potrzebami zabezpieczeń chmury firmy dostosowane do typem aplikacji oraz poufności danych w każdej subskrypcji. Używaj rekomendacji opartych na zasadach w celu prowadzenia właścicieli zasobów przez proces implementowania wymaganych punktów kontrolnych — usuwając wątpliwości podczas pracy z zabezpieczeniami chmury.

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>Łatwe wdrażanie zintegrowanych rozwiązań zabezpieczeń w chmurze
[Włącz rozwiązań zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) firmy Microsoft i jej partnerów, łącznie z branży zapory i ochrony przed złośliwym oprogramowaniem. Użyj sprawnych funkcji aprowizowania przy wdrażaniu rozwiązań z zakresu zabezpieczeń — nawet zmiany w sieci zostaną skonfigurowane. Zdarzenia zabezpieczeń pochodzące od rozwiązań partnerów zostaną automatycznie zebrane na potrzeby analiz i alertów.

### <a name="detect-threats-and-respond-fast"></a>Wykrywanie zagrożeń i szybkie reagowanie
Wyprzedzanie bieżących i pojawiających się zagrożeń w chmurze dzięki zintegrowanemu podejściu, którego podstawą są analizy. Łącząc Microsoft globalne [analizy zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) i doświadczenia z informacjami dotyczącymi zdarzeń związanych z zabezpieczeniami w chmurze między wdrożeń platformy Azure, Centrum zabezpieczeń pomaga wykrywać zagrożenia rzeczywiste wczesne i redukować liczbę fałszywych alarmów. Alerty zabezpieczeń chmurze zapewniają wgląd w ataku kampanii zdarzenia powiązane i wpływ na zasoby i zaproponuje sposoby rozwiązywania problemów i szybkie odzyskiwanie.

## <a name="end-to-end-scenario-based-network-monitoring"></a>Monitorowanie sieci oparta na scenariuszu end-to-end
Klienci kompilacji na trasie sieci na platformie Azure poprzez organizowanie i tworzenia różnych zasobów poszczególnych sieci, takich jak sieci wirtualnej, ExpressRoute, bramy aplikacji i usług równoważenia obciążenia. Monitorowanie jest dostępne na każdym z zasobów sieciowych.

[Monitor sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) jest regionalnych usługa, która umożliwia monitorowanie i diagnozowanie warunki w scenariuszu sieci, do i z platformy Azure. Diagnostyka sieci i narzędzi wizualizacji dostępnych z obserwatora sieciowego pomagają zrozumieć, diagnozowanie i Uzyskaj wgląd do sieci na platformie Azure.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>Automatyzowanie zdalnego monitorowania sieci przez przechwytywanie pakietów
Usługa Network Watcher umożliwia monitorowanie i diagnozowanie problemów z siecią bez konieczności logowania się na maszynach wirtualnych. Wyzwalacz [przechwytywania pakietów](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture) przez ustawienia alertów i uzyskanie dostępu do informacji o wydajności w czasie rzeczywistym na poziomie pakietów. Możesz szczegółowo analizować problemy w celu lepszego ich diagnozowania.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>Uzyskiwanie szczegółowych informacji dotyczących ruchu sieciowego przy użyciu dzienników przepływu
Tworzenie lepiej zrozumieć sieci ruchu wzorzec przy użyciu [dzienniki przepływu sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Informacje o dziennikach przepływu pomaga zbierania danych dotyczących zgodności, inspekcja i monitorowanie profilu zabezpieczeń sieci.

### <a name="diagnose-vpn-connectivity-issues"></a>Diagnozowanie problemów z połączeniem sieci VPN
Obserwatora sieciowego zapewnia możliwość [diagnozowanie najbardziej typowe problemy bramy sieci VPN i połączeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity). Teraz możesz nie tylko aby zidentyfikować problem, ale również umożliwia szczegółowe dzienniki utworzone w celu ułatwienia dalszego badania.

Aby dowiedzieć się więcej na temat sposobu konfigurowania obserwatora sieciowego oraz jak je włączyć, przeczytaj artykuł [skonfigurować obserwatora sieciowego](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="secure-deployment-using-proven-devops-tools"></a>Zabezpieczanie wdrożenia przy użyciu narzędzia do opracowywania oprogramowania sprawdzoną
Są to listy z Azure DevOps praktyk w tym miejscu Microsoft Cloud, co pozwala firmom i zespołom produktywności i wydajne.

-   **Infrastruktura jako kodu (IaC):** infrastruktury jako kod to zestaw metod i rozwiązań, które pomagają specjalistom IT Usuń obciążeń związanych z dnia na dzień kompilacji i zarządzanie infrastrukturą moduły. Pozwala specjalistom IT do tworzenia i utrzymywania środowisku nowoczesnych serwera w sposób przypominający ten sposób deweloperzy oprogramowania tworzenia i obsługi kodu aplikacji. Dla platformy Azure, mamy [usługi Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) umożliwia inicjowanie obsługi aplikacji przy użyciu szablonu deklaracyjnego. Pojedynczy szablon umożliwia wdrożenie wielu usług wraz z ich zależnościami. Za pomocą tego samego szablonu możesz wdrażać aplikację na każdym etapie jej cyklu życia.
-   **Ciągłej integracji i wdrażania:** projektów zespołowych Visual Studio Team Services (VSTS) można skonfigurować [automatycznie twórz i wdrażaj](https://www.visualstudio.com/docs/build/overview) do aplikacji sieci web platformy Azure lub usługi w chmurze. VSTS automatycznie wdraża pliki binarne po wykonaniu tej kompilacji do platformy Azure po każdym ewidencjonowania kodu. Proces kompilacji pakietu opisane w tym miejscu jest odpowiednikiem polecenia pakietu w programie Visual Studio i kroki publikowania są równoważne polecenia Opublikuj w programie Visual Studio.
-   **Zarządzanie zleceniami:** programu Visual Studio [Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) to doskonałe rozwiązanie do automatyzacji wdrażania wieloetapowym i zarządzanie procesem wersji. Utwórz potoki zarządzanych ciągłe wdrażanie zwolnienia szybkie, łatwe i często. Możemy znacznie automatyzacji procesu wersji Release Management i firma Microsoft może wstępnie zdefiniowanych przepływów pracy. Wdrażanie lokalnej i w chmurze, należy rozszerzyć i dostosować zgodnie z wymaganiami.
-   **Monitorowanie wydajności aplikacji:** wykrywania problemów, rozwiązywania problemów i stale poprawiaj swoje aplikacje. Szybko diagnozuj problemy w działającej aplikacji. Dowiedz się, do czego używają jej użytkownicy. Konfiguracja polega łatwe dodawanie kodu Javascript i wpis webconfig i wyświetlić wyniki w ciągu minut w portalu ze wszystkimi szczegółami. [App insights](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) pomaga firmom dla szybsze wykrywanie problemów i korygowanie.
-   **Ładowanie testowanie & skalowania automatycznego:** znaleźliśmy problemy z wydajnością w naszej aplikacji w celu poprawy jakości wdrożenia i upewnij się, że aplikacji jest zawsze tworzona lub dostępne w celu zaspokojenia biznesową potrzeb. Upewnij się, że aplikacja może obsługiwać ruch kampanii następnego uruchomienia lub marketing. Uruchamianie oparte na chmurze [testy obciążenia](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) prawie czas z usługi VSTS.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [bezpieczeństwa operacyjnego Azure](https://docs.microsoft.com/azure/security/azure-operational-security).
- Aby dowiedzieć się więcej [Operations Management Suite | Bezpieczeństwo i zgodność](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Wprowadzenie do programu Operations Management Suite zabezpieczeń i rozwiązanie inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started).
