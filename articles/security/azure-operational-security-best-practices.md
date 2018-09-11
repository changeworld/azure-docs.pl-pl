---
title: Najlepsze rozwiązania kwestii bezpieczeństwa platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw najlepszych rozwiązań dla bezpieczeństwa platformy Azure.
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
ms.openlocfilehash: d8f6ad48c62ff2021c91e593cee44dd6f5551247
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297912"
---
# <a name="azure-operational-security-best-practices"></a>Najlepsze rozwiązania kwestii bezpieczeństwa platformy Azure
Zabezpieczenia usługi Azure Operational odnosi się do usługi, formanty i funkcje dostępne dla użytkowników na potrzeby ochrony danych, aplikacji i innych zasobów na platformie Microsoft Azure. Bezpieczeństwa platformy Azure działa w oparciu o strukturę, która zawiera wiedzy uzyskanej dzięki różnych funkcji, które są unikatowe w firmie Microsoft, w tym Microsoft cykl projektowania zabezpieczeń (SDL), program Microsoft Security Response Center oraz głębokiej świadomości krajobraz zagrożeń cyberbezpieczeństwa.

W tym artykule omawiane jest kolekcją najlepsze rozwiązania dotyczące zabezpieczeń bazy danych Azure. Następujące najlepsze rozwiązania są uzyskiwane z naszych doświadczeń z usługą Azure database security i procesy, przez klientów, takie jak samodzielnie.

Dla każdego najlepszym rozwiązaniem jest Wyjaśnijmy:
-   Co to jest najlepsze rozwiązanie
-   Dlaczego chcesz włączyć na tym najlepszym rozwiązaniem jest
-   W przypadku awarii umożliwiające najlepszym rozwiązaniem, co może być skutkiem
- Jak można dowiesz się umożliwić najlepszym rozwiązaniem jest

W tym artykule Azure Operational Security Best Practices opiera się na opinii consensus i funkcji platformy Azure i zestawy funkcji występujących w czasie, który został zapisany w tym artykule. Opinie i technologii zmieniają się wraz z upływem czasu, a w tym artykule zostanie zaktualizowana w regularnych odstępach czasu, aby odzwierciedlać wprowadzone zmiany.

Usługa Azure Operational najlepszych rozwiązań dotyczących zabezpieczeń omówionych w tym artykule obejmują:

-   Monitorowanie, zarządzanie i ochronę infrastruktury w chmurze
-   Zarządzanie tożsamościami i implementowanie logowania jednokrotnego (SSO)
-   Śledzenie żądań, analizy tendencji użycia i diagnozowanie problemów
-   Monitorowanie usług za pomocą scentralizowanego rozwiązania do monitorowania
-   Zapobieganie, wykrywanie ich i reagowanie na zagrożenia
-   Monitorowanie sieci opartych na scenariuszach end-to-end
-   Zabezpieczanie wdrożenia przy użyciu sprawdzonych narzędzi DevOps

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>Monitorowanie, zarządzanie i ochronę infrastruktury w chmurze
Dział operacji IT jest odpowiedzialny za zarządzanie infrastrukturę centrów danych, aplikacji i danych, w tym stabilność i bezpieczeństwo tych systemów. Jednak zyskuje wgląd w zabezpieczenia przez zwiększenie złożonych środowisk IT często wymaga organizacjom cobble razem dane z różnych systemów zabezpieczeń i zarządzania.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) jest firmy Microsoft oparte na chmurze rozwiązanie zarządzania IT, która ułatwia zarządzanie i chronić lokalne i infrastruktury chmury.

[Rozwiązanie pakietu OMS, zabezpieczenia i inspekcja](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) umożliwiają IT aktywne monitorowanie wszystkich zasobów, które mogą pomóc zminimalizować wpływ związane z bezpieczeństwem. Zabezpieczenia w pakiecie OMS i inspekcji mają domeny zabezpieczeń, które mogą służyć do monitorowania zasobów.

Aby uzyskać więcej informacji na temat pakietu OMS, przeczytaj artykuł [Omówienie pakietu Operations Management Suite (OMS)](https://technet.microsoft.com/library/mt484091.aspx).

Aby zapobiec, wykrywanie ich i reagowanie na zagrożenia, [rozwiązanie Operations Management Suite (OMS), zabezpieczenia i inspekcja](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) zbiera i przetwarza dane dotyczące zasobów, która obejmuje:

-   Dziennik zdarzeń zabezpieczeń
-   Zdarzenia funkcji Śledzenie zdarzeń systemu Windows (ETW)
-   Zdarzenia inspekcji funkcji AppLocker
-   Dziennik zapory systemu Windows
-   Zdarzenia rozwiązania Advanced Threat Analytics
-   Wyniki oceny linii bazowej
-   Wyniki oceny oprogramowania chroniącego przed złośliwym kodem
-   Wyniki oceny aktualizacji/poprawek
-   Strumienie SYSLOG, które są jawnie włączone dla agenta


## <a name="manage-identity-and-implement-single-sign-on"></a>Zarządzanie tożsamościami i implementowanie logowania jednokrotnego
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) katalogu oparte na chmurze z wieloma dzierżawami firmy Microsoft i Usługa zarządzania tożsamościami.

[Usługa Azure AD](https://azure.microsoft.com/services/active-directory/) obejmuje również całą gamę [Zarządzanie tożsamościami](https://docs.microsoft.com/azure/security/security-identity-management-overview) możliwości, takie jak [uwierzytelnianie wieloskładnikowe](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), rejestracji urządzeń, Samoobsługowe zarządzanie hasłami, Samoobsługowe zarządzanie grupami, uprzywilejowanego konta zarządzania kontroli dostępu opartej na rolach, użycia aplikacji, monitorowanie, zaawansowane inspekcji i monitorowania i alertów zabezpieczeń.

Następujące funkcje można zabezpieczenia aplikacji chmurowych, usprawnianie procesów IT, zmniejszaj koszty i pomaga zapewnić, że są spełnione cele zgodność z zasadami firmowymi:

-   Zarządzanie tożsamościami i dostępem w chmurze
-   Prosty dostęp użytkowników do dowolnej aplikacji w chmurze
-   Ochrona cennych danych i aplikacji
-   Samoobsługa dostępna dla wszystkich pracowników
-   Integracja z usługą Azure Active Directory

### <a name="identity-and-access-management-for-the-cloud"></a>Zarządzanie tożsamościami i dostępem w chmurze
Azure Active Directory (Azure AD) to kompleksowe [rozwiązanie w chmurze zarządzania tożsamościami i dostępem](https://www.microsoft.com/cloud-platform/identity-management), które zapewnia niezawodne funkcje zarządzania użytkownikami i grupami. Ułatwia zabezpieczenie dostępu do lokalnych i aplikacji, w tym usług sieci web firmy Microsoft, takich jak Office 365 i znacznie oprogramowania firmy Microsoft jako aplikacji usługi (SaaS) w chmurze.
Aby dowiedzieć się więcej jak włączyć usługę identity protection w usłudze Azure AD, zobacz [włączania usługi Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).

### <a name="simplify-user-access-to-any-cloud-app"></a>Prosty dostęp użytkowników do dowolnej aplikacji w chmurze
[Włącz logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) Aby uprościć użytkownikom dostęp do tysięcy aplikacji w chmurze z urządzeń, Windows, Mac, Android i iOS. Użytkownicy mogą uruchamiać aplikacje z panelu spersonalizowane dostępu opartego na sieci web lub aplikacji mobilnej przy użyciu swoich poświadczeń firmowych. Moduł serwera Proxy aplikacji usługa Azure AD można wykroczyć poza aplikacje SaaS i publikować lokalne aplikacje sieci web w celu zapewnienia bardzo bezpiecznego dostępu zdalnego i logowania jednokrotnego.

### <a name="protect-sensitive-data-and-applications"></a>Ochrona cennych danych i aplikacji
Włącz [usługi Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) zapobiegać nieautoryzowanemu dostępowi do środowiska lokalnego i w chmurze dzięki zapewnieniu dodatkowego poziomu uwierzytelnienia. Chroń swoją firmę i eliminuj potencjalne zagrożenia dzięki funkcji monitorowania zabezpieczeń, alertom oraz raportom opartym na uczeniu maszynowym, które identyfikują niespójne wzorce dostępu.

### <a name="enable-self-service-for-your-employees"></a>Samoobsługa dostępna dla wszystkich pracowników
Możesz zlecać pracownikom ważne zadania, takie jak resetowanie haseł oraz tworzenie grup i zarządzanie nimi. [Włącz zmienianie haseł](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), zresetuj i Samoobsługowe zarządzanie za pomocą usługi Azure AD grupy.

### <a name="integrate-with-azure-active-directory"></a>Integracja z usługą Azure Active Directory
Rozszerzanie [usługi Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) i wszelkich innych rozwiązań lokalnych katalogów do usługi Azure AD, aby włączyć logowanie jednokrotne do wszystkich aplikacji opartych na chmurze. Atrybuty użytkowników można automatycznie synchronizować z katalogiem w chmurze ze wszystkich rodzajów katalogów lokalnych.

Aby uzyskać więcej informacji na temat integracji Azure Active Directory i jak go włączyć, przeczytaj artykuł [integrowanie katalogów lokalnych z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>Śledzenie żądań, analizy tendencji użycia i diagnozowanie problemów
[Analizy usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-analytics) umożliwia rejestrowanie i dostarcza danych metryk dotyczących konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu.

Metryk usługi Storage Analytics są domyślnie włączone dla nowych kont magazynu. Można włączyć rejestrowanie i konfigurować metryki i rejestrowanie w usłudze Azure portal. Aby uzyskać więcej informacji, zobacz [monitorowania na koncie magazynu w witrynie Azure portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Można również włączyć analizy programowo za pośrednictwem interfejsu API REST lub biblioteka klienta usługi Storage. Umożliwiają operacji ustawiania właściwości usługi Storage Analytics osobno dla każdej usługi.

Aby uzyskać szczegółowy przewodnik na temat korzystania z usługi Storage Analytics i inne narzędzia do identyfikowania, diagnozowanie i rozwiązywanie problemów związanych z usługi Azure Storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Aby uzyskać więcej informacji na temat integracji Azure Active Directory i jak go włączyć, przeczytaj artykuł [Włączanie i konfigurowanie Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN).

## <a name="monitoring-services"></a>Monitorowanie usług
Aplikacje w chmurze są złożone z wielu ruchomych elementów. Monitorowanie zapewnia dane, aby upewnić się, że aplikacja stale się i działa w dobrej kondycji. Pomaga również stave potencjalnych problemów lub Rozwiązywanie problemów z przeszłości te. Ponadto można użyć danych monitorowania do uzyskania szczegółowych informacji o aplikacji. Tę wiedzę można pomóc zwiększyć wydajność aplikacji lub łatwość konserwacji lub Automatyzuj akcje, które w przeciwnym razie wymagają ręcznej interwencji.

### <a name="monitor-azure-resources"></a>Monitorowanie zasobów platformy Azure
[Usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) to usługa platformy, która zapewnia jedno źródło monitorowania zasobów platformy Azure. Dzięki usłudze Azure Monitor możesz wykonywać wizualizacje i zapytania, ustalać trasy, archiwizować i podejmować działania dotyczące metryk i dzienników pochodzących z zasobów na platformie Azure. Możesz pracować z tymi danymi przy użyciu bloku portalu Monitor, [poleceń cmdlet programu PowerShell usługi Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), [międzyplatformowego interfejsu wiersza polecenia](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) lub [interfejsów API REST usługi Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).

### <a name="enable-autoscale-with-azure-monitor"></a>Włącz Skalowanie automatyczne za pomocą usługi Azure monitor
Włącz [funkcja automatycznego skalowania usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started) ma zastosowanie tylko do zestawów skalowania maszyn wirtualnych (zestawu skalowania maszyn wirtualnych), cloud services, plany usługi app service i środowisk usługi app service.

### <a name="manage-roles-permissions-and-security"></a>Zarządzanie rolami uprawnieniami i zabezpieczeniami
Wiele zespołów potrzebuje do ściśle [regulowania dostępu do monitorowania](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security) danych i ustawień. Na przykład, jeśli posiadasz elementy członkowskie zespołu, którzy pracują wyłącznie na temat monitorowania (pracowników działu pomocy technicznej, inżynierom devops) lub korzystając z dostawcą usługi zarządzanej, można przyznać im dostęp do danych monitorowania tylko jednocześnie ograniczając możliwość tworzenia, modyfikowania, lub Usuń zasoby.

To pokazuje, jak szybko wbudowana rola RBAC monitorowania są stosowane do użytkownika na platformie Azure lub utworzyć własne niestandardowe rolę dla użytkownika, który musi mieć ograniczone uprawnienia monitorowania. Następnie omówiono zagadnienia dotyczące zabezpieczeń na zasoby dotyczące usługi Azure Monitor i jak można ograniczyć dostęp do danych, które zawierają.

## <a name="prevent-detect-and-respond-to-threats"></a>Zapobieganie, wykrywanie ich i reagowanie na zagrożenia
Działania funkcji wykrywania zagrożeń usługi Security Center polega na automatycznym zbieraniu informacji o zabezpieczeniach z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich. Analizuje ona te informacje, często zestawiając informacje z wielu źródeł, aby zidentyfikować zagrożenia. Alerty zabezpieczeń wraz z zaleceniami dotyczącymi usuwania zagrożeń są traktowane przez usługę Security Center priorytetowo.

-   [Konfigurowanie zasad zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-policies) dla subskrypcji platformy Azure.
-   Użyj [zalecenia w usłudze Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) pomagających chronić zasoby platformy Azure.
-   Przeglądanie i zarządzanie bieżących [alerty zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).

Usługa [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

Centrum zabezpieczeń zapewnia łatwe i skuteczne zapobieganie zagrożeniom, ich wykrywanie oraz wbudowane w platformę Azure możliwości reakcji na nie. Do najważniejszych możliwości należą:

-   Poznaj stan zabezpieczeń chmury
-   Przejmij kontrolę nad zabezpieczeniami chmury
-   Łatwe wdrażanie zintegrowanych rozwiązań zabezpieczeń w chmurze
-   Wykrywanie zagrożeń i szybkie reagowanie

### <a name="understand-cloud-security-state"></a>Poznaj stan zabezpieczeń chmury
Usługa Azure Security Center daje pełny widok stanu bezpieczeństwa wszystkich Twoich zasobów platformy Azure. Pierwszy rzut oka Sprawdź, czy odpowiednie środki zabezpieczające są stosowane i poprawnie skonfigurowane i szybko rozpoznać zasoby, które wymagają uwagi.

### <a name="take-control-of-cloud-security"></a>Przejmij kontrolę nad zabezpieczeniami chmury
Zdefiniuj [zasad zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-policies) dla subskrypcji platformy Azure zgodnie z potrzebami zabezpieczeń chmury firmy dostosowane do typu aplikacji lub wrażliwości danych w ramach każdej subskrypcji. Używaj rekomendacji opartych na zasadach w celu prowadzenia właścicieli zasobów przez proces implementowania wymaganych punktów kontrolnych — usuwając wątpliwości podczas pracy z zabezpieczeniami chmury.

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>Łatwe wdrażanie zintegrowanych rozwiązań zabezpieczeń w chmurze
[Włączanie rozwiązania w zakresie bezpieczeństwa](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) firmy Microsoft i jej partnerów, w tym wiodące w branży zapory i ochrony przed złośliwym oprogramowaniem. Użyj sprawnych funkcji aprowizowania przy wdrażaniu rozwiązań z zakresu zabezpieczeń — nawet zmiany w sieci zostaną skonfigurowane. Zdarzenia zabezpieczeń pochodzące od rozwiązań partnerów zostaną automatycznie zebrane na potrzeby analiz i alertów.

### <a name="detect-threats-and-respond-fast"></a>Wykrywanie zagrożeń i szybkie reagowanie
Wyprzedzanie bieżących i pojawiających się zagrożeń w chmurze dzięki zintegrowanemu podejściu, którego podstawą są analizy. Łącząc globalne Microsoft [analiza zagrożeń](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) i wiedzę, wnioski płynące ze zdarzeń dotyczących zabezpieczeń w chmurze wdrożeń platformy Azure, usługa Security Center ułatwia wczesne wykrywanie rzeczywistych zagrożeń i redukować liczbę fałszywych alarmów. Alerty zabezpieczeń w chmurze dają wgląd w kampanię ataków, w tym powiązane zdarzenia i zasoby nimi objęte, a także sugerują sposoby rozwiązywania problemów z szybkiego i przeprowadzenia odzyskiwania.

## <a name="end-to-end-scenario-based-network-monitoring"></a>Monitorowanie sieci opartych na scenariuszach end-to-end
Klienci tworzyć sieci end-to-end na platformie Azure, poprzez organizowanie i tworzenie różnych zasobów poszczególnych sieciowych, takich jak sieć wirtualna, usługi ExpressRoute, usługa Application Gateway i moduły równoważenia obciążenia. Monitorowanie jest dostępne na każdym z zasobów sieciowych.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) to regionalna usługa, która pozwala na monitorowanie i diagnozowanie warunków na scenariusz sieci poziomie, do i z platformy Azure. Diagnostyka sieci i narzędzi do wizualizacji dostępne w usłudze Network Watcher pomagają zrozumieć, diagnozowanie i uzyskiwanie szczegółowych informacji do sieci na platformie Azure.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>Automatyzowanie zdalnego monitorowania sieci przez przechwytywanie pakietów
Usługa Network Watcher umożliwia monitorowanie i diagnozowanie problemów z siecią bez konieczności logowania się na maszynach wirtualnych. Wyzwalacz [przechwytywania pakietów](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture) przez ustawianie alertów i uzyskać dostęp do informacji o wydajności w czasie rzeczywistym na poziomie pakietów. Możesz szczegółowo analizować problemy w celu lepszego ich diagnozowania.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>Uzyskiwanie szczegółowych informacji dotyczących ruchu sieciowego przy użyciu dzienników przepływu
Tworzenie lepiej zrozumieć sieci ruchu wzorca przy użyciu [dzienniki przepływu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Informacje o dziennikach przepływu ułatwiają gromadzenie danych, które pod kątem zgodności, inspekcji i monitorowania profilu zabezpieczeń sieci.

### <a name="diagnose-vpn-connectivity-issues"></a>Diagnozowanie problemów z połączeniem sieci VPN
Usługa Network Watcher oferuje możliwość [zdiagnozować typowe problemy VPN Gateway i połączeniami](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity). Umożliwia nie tylko do identyfikacji problemu, ale także do przy użyciu szczegółowych dzienników, które są tworzone w celu dalszego zbadania problemu.

Aby dowiedzieć się więcej na temat sposobu konfigurowania usługi Network watcher i jak go włączyć, przeczytaj artykuł [Konfigurowanie usługi Network watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="secure-deployment-using-proven-devops-tools"></a>Zabezpieczanie wdrożenia przy użyciu sprawdzonych narzędzi DevOps
Poniżej przedstawiono niektóre listy z platformy Azure metodyki DevOps w tej dziedzinie Microsoft Cloud, co sprawia, że przedsiębiorstwa i zespoły efektywny i wydajny.

-   **Infrastruktura jako kod (IaC):** infrastruktura jako kod to zestaw metod i praktyk, które pomagają specjalistom IT Usuń obciążenia skojarzonych z dnia na dzień kompilacji i zarządzania infrastrukturą moduły. Umożliwia specjalistom IT do tworzenia i utrzymywania ich środowiska nowoczesnych serwera w sposób przypominający jak deweloperom oprogramowania tworzenie i zarządzanie nimi kodu aplikacji. Dla platformy Azure, mamy [usługi Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) pozwala inicjować obsługę aplikacji za pomocą szablonu deklaratywnego. Pojedynczy szablon umożliwia wdrożenie wielu usług wraz z ich zależnościami. Za pomocą tego samego szablonu możesz wdrażać aplikację na każdym etapie jej cyklu życia.
-   **Ciągła integracja i ciągłe wdrażanie:** można skonfigurować swoje projekty DevOps platformy Azure, aby [automatycznie twórz i wdrażaj](https://www.visualstudio.com/docs/build/overview) do aplikacji sieci web platformy Azure lub usług w chmurze. DevOps platformy Azure automatycznie wdraża pliki binarne po wykonaniu tej kompilacji na platformie Azure po każdym zaewidencjonowaniu kodu. Proces kompilacji pakietu, które są opisane w tym miejscu jest odpowiednikiem polecenia pakietów w programie Visual Studio i kroki publikowania są równoważne polecenia Opublikuj w programie Visual Studio.
-   **Program Release Management:** programu Visual Studio [Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) to doskonałe rozwiązanie do automatyzacji wieloetapowych wdrażania i zarządzania nimi procesu tworzenia wersji. Twórz potoki zarządzanego ciągłe wdrażanie do wydania szybko, łatwo i często. Release Management nasz proces wydania można znacznie zautomatyzować i firma Microsoft może mieć wstępnie zdefiniowane przepływy pracy zatwierdzania. Wdrażanie lokalnej do chmury, należy rozszerzyć i dostosować zgodnie z potrzebami.
-   **Monitorowanie wydajności aplikacji:** wykrywaj problemy, rozwiązywania problemów i stale ulepszaj swoje aplikacje. Szybko diagnozuj problemy w działającej aplikacji. Dowiedz się, do czego używają jej użytkownicy. Konfiguracja jest łatwy kwestia Dodawanie kodu Javascript i wpis webconfig i zostaną wyświetlone wyniki w ciągu kilku minut w portalu, wszystkie szczegółowe informacje. [Usługi app insights](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) ułatwia przedsiębiorstwom na szybsze wykrywanie problemów i korygowanie.
-   **Obciążenia, testowanie i skalowania automatycznego:** możemy znaleźć problemy z wydajnością w naszej aplikacji, aby poprawić jakość wdrożenia i się upewnić, że nasza aplikacja jest zawsze w pracy lub będą dostępne do adresowanych do przedsiębiorstwa musi. Upewnij się, że aplikacja może obsługiwać ruch dalej kampanii uruchamiania lub obrotu. Rozpocznij uruchamianie oparte na chmurze [testy obciążeniowe](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) w prawie w krótkim czasie przy użyciu infrastruktury DevOps platformy Azure.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [bezpieczeństwa platformy Azure](https://docs.microsoft.com/azure/security/azure-operational-security).
- Aby dowiedzieć się więcej [pakietu Operations Management Suite | Security & Compliance](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Wprowadzenie do programu Operations Management Suite Security rozwiązaniu i inspekcja](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started).
