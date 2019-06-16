---
title: Wprowadzenie do zabezpieczeń platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zabezpieczeń platformy Azure, usługach i jak to działa.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: ed57d72d32ba82a37036c9af77590bd4e93db8d9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610521"
---
# <a name="introduction-to-azure-security"></a>Wprowadzenie do zabezpieczeń platformy Azure
## <a name="overview"></a>Omówienie
Wiemy, że bezpieczeństwo to zadanie, jeden w chmurze i jak ważne jest, że możesz znaleźć dokładne i aktualne informacje o zabezpieczeniach platformy Azure. Jedną z najważniejszych powodów, dla aplikacji i usług za pomocą platformy Azure jest korzystanie z zalet jej szeroką gamę narzędzi zabezpieczeń i możliwości. Te narzędzia i możliwości pomocy, umożliwiają tworzenie bezpiecznych rozwiązań na platformie Azure jest bezpieczne. Microsoft Azure oferuje poufność, integralność i dostępność danych klientów, pozwalając również przezroczyste odpowiedzialności.

Aby umożliwić użytkownikom lepsze rozumienie zbiór zabezpieczeń formanówy implementowanych w systemie Microsoft Azure z perspektywy operacji firmy Microsoft i klienta, tym oficjalnym dokumencie "Wprowadzenie do usługi Azure Security", są zapisywane do zapewniają pełny w zabezpieczeń dostępne z platformą Microsoft Azure.

### <a name="azure-platform"></a>Platforma Azure
Platforma Azure to platforma usługi w chmurze publicznej obsługuje szeroki zakres systemów operacyjnych, języków programowania, struktur, narzędzia, baz danych i urządzeń. Jej uruchamianie kontenerów systemu Linux z integracją Docker; Tworzenie aplikacji w językach JavaScript, Python, .NET, PHP, Java i Node.js; Tworzenie zapleczy dla systemów iOS, Android i Windows urządzenia.

Usługi chmury publicznej platformy Azure obsługują te same technologie przez miliony deweloperów i specjalistów IT, już używane i wypróbowane. Podczas tworzenia na lub migracji zasobów informatycznych do dostawcy usług w chmurze publicznej polegasz na możliwości w organizacji można chronić aplikacje i dane za pomocą usług i formanty zapewniają do zarządzania zabezpieczeniami zasobów opartych na chmurze.

Infrastruktura platformy Azure została zaprojektowana od funkcji aplikacje hostujące jednocześnie miliony klientów, a jego zapewnia wiarygodną podstawę firm mogą spełnienie rządowych wymagań dotyczących zabezpieczeń.

Ponadto platforma Azure udostępnia szeroki zakres konfigurowalnych opcji zabezpieczeń oraz możliwość sterowania nimi, dzięki czemu możesz dostosować zabezpieczenia, aby spełniały unikatowe wymagania wdrożenia w organizacji. Ten dokument pomoże Ci zrozumieć, jak usługa Azure security możliwości pomaga spełnić te wymagania.

> [!Note]
> Głównym celem tego dokumentu jest kontrolek przeznaczonych dla klientów, których można użyć w celu dostosowania i zwiększyć bezpieczeństwo aplikacji i usług.
>
> Firma Microsoft zapewnia pewne informacje, ale szczegółowe informacje na temat jak firma Microsoft zabezpiecza samą platformę Azure, zobacz informacje zawarte w [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

### <a name="abstract"></a>Abstrakcyjny
Początkowo migracjach do chmury publicznej zostały wynika oszczędności kosztów i elastyczność na innowacjach. Zabezpieczeń został uznany za główną kwestią w przypadku trochę czasu, a nawet całkowicie uniemożliwia pracę, do migracji do chmury publicznej. Jednak zabezpieczeń chmury publicznej zmienił się z głównym problemem na jeden z sterowniki dla migracji do chmury. Uzasadnieniem to doskonałe możliwości dostawców usług w chmurze publicznej dużych chronić aplikacje i dane zasobów w chmurze.

Infrastruktura platformy Azure została zaprojektowana kompleksowo, począwszy od obiektu po aplikacje hostujące jednocześnie miliony klientów, i zapewnia wiarygodną podstawę zaspokajania potrzeb firm w zakresie bezpieczeństwa. Ponadto platforma Azure zapewnia szeroką gamę konfigurowalnych opcji zabezpieczeń oraz możliwość sterowania nimi, dzięki czemu możesz dostosować zabezpieczenia, aby spełniały unikatowe wymagania realizowanych wdrożeń do spełnienia dział IT zasady kontroli i stosować się do zewnętrznego przepisy.

W tym dokumencie opisano podejście firmy Microsoft do zabezpieczenia w chmurze platformy Microsoft Azure:
* Funkcje zabezpieczeń implementowane przez firmę Microsoft do zabezpieczania infrastruktury platformy Azure, danych klienta i aplikacji.
* Usługi platformy Azure i funkcji zabezpieczeń dostępnych w celu zarządzania zabezpieczeniami usług i danych w ramach subskrypcji platformy Azure.

## <a name="summary-azure-security-capabilities"></a>Możliwości Podsumowanie zabezpieczeń platformy Azure
Poniższa tabela Podaj krótki opis funkcji zabezpieczeń implementowane przez firmę Microsoft w celu zabezpieczenia infrastruktury platformy Azure, danych klienta i bezpiecznych aplikacji.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Funkcje zabezpieczeń, wdrożone w celu zabezpieczenia platformy Azure:
Funkcje wymienione w następstwie są funkcje, które można przeglądać w celu zapewnienia, że platforma Azure jest zarządzana w bezpieczny sposób. Linki zostały przewidziane w dalsze Przechodzenie do szczegółów w sposób Microsoft adresy pytania zaufanie klientów w czterech obszarów: Zabezpieczenia platformy, ochrony prywatności & kontroli, zgodności i przezroczystości.


| [Bezpieczna platforma](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Prywatność i kontrolek](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Zgodność](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Przezroczystości](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Cykl projektowania zabezpieczeń](https://www.microsoft.com/en-us/sdl/)wewnętrznej inspekcji | [Zarządzanie danymi przez cały czas](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Centrum zaufania](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Jak firma Microsoft zabezpiecza dane klienta z usługami platformy Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Obowiązkowe szkolenia w zakresie zabezpieczeń, sprawdzanie w tle](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Kontrolowanie lokalizacji danych](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Typowe kontrolki Centrum](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Jak zarządzać lokalizacji danych w usługach platformy Azure przez firmy Microsoft](https://azuredatacentermap.azurewebsites.net/)|
| [Testy penetracyjne](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [włamań i DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [inspekcji i rejestrowania](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Zapewnia dostęp do danych na swoich warunkach](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Usługi w chmurze powodu starannością listy kontrolnej](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[Kto w firmie Microsoft uzyskiwać dostęp do danych, na jakich zasadach](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Centrum danych najnowocześniejsze](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), bezpieczeństwo fizyczne [zabezpieczenia sieci](https://docs.microsoft.com/azure/security/security-network-overview) | [Odpowiadanie na organom ścigania](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Zgodność z przepisami według usługi, lokalizacji i przemysł](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Jak firma Microsoft zabezpiecza dane klienta z usługami platformy Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Reagowania na zdarzenia zabezpieczeń](https://aka.ms/SecurityResponsepaper), [udostępnione odpowiedzialności](https://aka.ms/sharedresponsibility) |[Rygorystyczne standardy ochrony prywatności](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Przejrzyj certyfikaty związane z usługami platformy Azure, Centrum przezroczystości](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Funkcje zabezpieczeń oferowanych przez platformę Azure w celu zabezpieczenia danych i aplikacji
W zależności od modelem usługi chmury ma zmiennych odpowiedzialność, kto jest odpowiedzialny za zarządzanie zabezpieczeniami aplikacji lub usługi. Możliwości są dostępne na platformie Azure, aby pomóc w osiągnięciu obowiązków, za pomocą wbudowanych funkcji, a także za pośrednictwem rozwiązania partnerskie, które mogą zostać wdrożone w subskrypcji platformy Azure.

Wbudowane funkcje są zorganizowane w obszarach funkcjonalnych sześć (6): Operacje, aplikacji, magazynu, sieci, obliczeń i tożsamości. Dodatkowe informacje szczegółowe na temat funkcji i możliwości dostępne w następujących obszarach sześć (6) na platformie Azure są realizowane za pośrednictwem informacje podsumowujące.

## <a name="operations"></a>Operacje
Ta sekcja zawiera dodatkowe informacje na temat kluczowych funkcji zabezpieczeń i podsumowanie informacji o tych możliwości.

### <a name="security-and-audit-dashboard"></a>Zabezpieczenia i inspekcja pulpitu nawigacyjnego
[Rozwiązania zabezpieczenia i inspekcja](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) zapewnia kompleksowy wgląd w Twojej organizacji stan zabezpieczeń IT za pomocą [wbudowanych zapytań](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) wyszukujących istotne problemy, które wymagają Twojej uwagi. [Zabezpieczenia i inspekcja](https://technet.microsoft.com/library/mt484091.aspx) pulpit nawigacyjny jest głównym ekranem, aby wszystkie elementy związane z bezpieczeństwem dzienniki usługi Azure Monitor. Zawiera ogólne informacje o stanie zabezpieczeń komputerów. Daje również możliwość wyświetlenia wszystkich zdarzeń z ostatnich 24 godzin, 7 dni lub dowolnego innego przedziału czasu.

Ponadto można skonfigurować zabezpieczenia i zgodność w celu [automatyczne wykonywanie czynności określonych](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) po wykryciu określonego zdarzenia.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Usługa Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) umożliwia pracę z zasobami tworzącymi rozwiązanie w formie grupy. Wszystkie zasoby danego rozwiązania można wdrożyć, zaktualizować lub usunąć w ramach jednej skoordynowanej operacji. Możesz użyć [szablonu usługi Azure Resource Manager](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) dla wdrożenia szablonu, którego może pracować w różnych środowiskach, takich jak testowanie, etap przejściowy i produkcja. Usługa Resource Manager zapewnia funkcje zabezpieczeń, inspekcji i znakowania ułatwiające zarządzanie zasobami po wdrożeniu.

Wdrożeń platformy Azure na podstawie szablonu usługi Resource Manager poprawić zabezpieczenia rozwiązań wdrożonych na platformie Azure, ponieważ standardowych zabezpieczeń kontrolowania ustawień i może można zintegrować we wdrożeniach opartych na szablonach standardowych. Zmniejsza to ryzyko błędów konfiguracji zabezpieczeń, które mogą mieć miejsce podczas ręcznego wdrożenia.

### <a name="application-insights"></a>Application Insights
[Usługa Application Insights](https://docs.microsoft.com/azure/application-insights/) to rozszerzalna Usługa zarządzania wydajnością aplikacji (APM) dla deweloperów sieci web. Za pomocą usługi Application Insights można monitorować aplikacje sieci web na żywo i automatycznie wykrywać anomalie wydajność. Obejmuje ona zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów i aby zrozumieć, jak użytkownicy w rzeczywistości korzystają z aplikacjami. Monitoruje ona cały czas, którym jest uruchomiona, podczas testowania i po jej wdrożeniu lub opublikowanych aplikacji.

Usługa Application Insights tworzy wykresy i tabele, w których znajdziesz informacje, na przykład, jakich porach dnia otrzymasz większość użytkowników, jak szybko reagujących aplikacji jest i jak dobrze jest obsługiwany przez zewnętrzne usługi, których jest zależna.

Jeśli występują awarie, błędy lub problemy z wydajnością, możesz wyszukać dane telemetryczne szczegółowo przyczynę. I Usługa wyśle do Ciebie wiadomości e-mail w przypadku zmiany w dostępności i wydajności aplikacji. Application Insights związku z tym staje się to narzędzie zabezpieczeń cenne, ponieważ ułatwia dzięki dostępności w poufność, integralność i dostępność, Triada zabezpieczeń.

### <a name="azure-monitor"></a>Azure Monitor
[Usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) oferuje wizualizacji, zapytania, routingu, alerty, automatyczne skalowanie i automatyzacja danych zarówno od infrastruktury platformy Azure ([dziennika aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)) i każdego pojedynczego zasobu platformy Azure ([diagnostyczne Rejestruje](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)). Za pomocą usługi Azure Monitor ostrzega zdarzeń dotyczących zabezpieczeń, które są generowane w dziennikach platformy Azure.

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor
[Dzienniki platformy Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics/) — stanowi rozwiązanie zarządzania IT dla zarówno lokalnych, jak i innych firm infrastruktury chmurowej (np. usług AWS) oprócz zasobów platformy Azure. Można je skierować dane z usługi Azure Monitor bezpośrednio do usługi Azure Monitor dzienników, dzięki czemu można przeglądać dzienniki i metryki dla całego środowiska, w tym miejscu.

Dzienniki monitora platformy Azure mogą być przydatne narzędzie analizy śledczej i innych zabezpieczeń, jak to narzędzie pozwala szybko przeszukiwać dużych ilości wpisy związane z zabezpieczeniami dzięki podejściu elastyczne możliwości tworzenia zapytań. Ponadto w środowisku lokalnym [dzienniki zapory i serwera proxy można eksportować do platformy Azure i udostępniane dla analizy przy użyciu dzienników usługi Azure Monitor.](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)

### <a name="azure-advisor"></a>Azure Advisor
[Usługa Azure Advisor](https://docs.microsoft.com/azure/advisor/) to spersonalizowany konsultant ds. chmury, która pomaga w celu optymalizacji wdrożeń platformy Azure. Analizuje on konfigurację zasobów i dane telemetryczne użycia. Następnie zaleca rozwiązania, aby pomóc w ulepszaniu [wydajności](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), [zabezpieczeń](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations), i [wysokiej dostępności](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations) Twoich zasobów, a także szuka możliwości [zmniejszyć ogólną Azure spend](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations). Usługa Azure Advisor zawiera zalecenia dotyczące zabezpieczeń, które może znacznie zwiększyć ogólną strukturę bezpieczeństwa dla rozwiązania, które można wdrożyć na platformie Azure. Te zalecenia są pobierane z analizy zabezpieczeń, wykonywane przez [usługi Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-intro)

### <a name="azure-security-center"></a>Azure Security Center
Usługa [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

Ponadto usługa Azure Security Center pomaga w operacji zabezpieczeń podając pojedynczy pulpit nawigacyjny tej powierzchni alertów i zaleceń, które mogą być przetwarzane od razu. Często może rozwiązać problemy związane z jednym kliknięciem w konsoli usługi Azure Security Center.
## <a name="applications"></a>Aplikacje
W sekcji przedstawiono dodatkowe informacje na temat kluczowe funkcje zabezpieczeń i podsumowanie informacji o aplikacji dotyczące tych możliwości.

### <a name="web-application-vulnerability-scanning"></a>Sieci Web aplikacji wyszukiwaniu luk w zabezpieczeniach
Jednym z najprostszych sposobów, aby rozpocząć testowanie pod kątem luk w zabezpieczeniach na Twoje [aplikację usługi app Service](https://docs.microsoft.com/azure/app-service/overview) jest użycie [integracji przy użyciu usługi Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) przeprowadzić jednym kliknięciem wyszukiwaniu luk w zabezpieczeniach w aplikacji. Możesz wyświetlić wyniki testów w raporcie o łatwych do zrozumienia i Dowiedz się, jak naprawić każdą usterkę z instrukcjami krok po kroku.

### <a name="penetration-testing"></a>Testy penetracyjne
Jeśli wolisz do przeprowadzenia testów penetracyjnych lub chcesz użyć innego dostawcy lub skaner pakietu, należy wykonać [platformy Azure proces zatwierdzania testów penetracyjnych](https://docs.microsoft.com/azure/security/azure-security-pen-testing ) i uzyskać wcześniejsze zatwierdzenia do przeprowadzenia testów penetracyjnych żądaną.

### <a name="web-application-firewall"></a>Zapora aplikacji sieci Web
Zapora aplikacji sieci web (WAF) w [usługi Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) pomaga chronić aplikacje sieci web przed typowymi atakami opartego na sieci web, takimi jak iniekcja SQL, ataki z użyciem skryptów między witrynami i przejęcie kontroli sesji. Ma wstępnie skonfigurowany ochrony przed zagrożeniami zidentyfikowanymi przez [Otwórz sieci Web aplikacji Security Project (OWASP) jako pierwszych 10 najpopularniejszych luk w zabezpieczeniach](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Uwierzytelnianie i autoryzacja w usłudze Azure App Service
[Uwierzytelnianie usługi App Service / autoryzacji](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) to funkcja, która umożliwia aplikacji do logowania użytkowników, tak aby nie trzeba zmieniać kodu zaplecza aplikacji. Zapewnia prosty sposób ochrona aplikacji i Praca z danymi użytkownika.

### <a name="layered-security-architecture"></a>Warstwowej architektury zabezpieczeń
Ponieważ [środowisk usługi App Service](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro) zapewniają odizolowanym środowisku uruchomieniowym wdrożone w [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), deweloperzy mogą tworzyć warstwowej architektury zabezpieczeń podając różne poziomy dostęp do sieci dla każdej warstwy aplikacji. Wspólne dążenie jest ukryć interfejsu API zaplecza z ogólnego dostępu do Internetu i zezwolić tylko na interfejsy API ma zostać wywołana przez nadrzędne web apps. [Sieciowe grupy zabezpieczeń (NSG)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) może służyć w podsieciach sieci wirtualnej platformy Azure zawierającego środowisk usługi App Service do ograniczenia dostępu publicznego do aplikacji interfejsu API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnostyka serwera sieci Web i diagnostyki aplikacji
Aplikacje sieci web usługi App Service zapewnia funkcja diagnostyki dla rejestrowanie informacji z serwera sieci web i aplikacji sieci web. Są one logicznie oddzielone w [diagnostyką serwerów w sieci web](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs) i [programu application diagnostics](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Serwer sieci Web obejmuje dwa główne postępu, diagnozowanie i rozwiązywanie problemów z witrynami i aplikacjami.

Pierwszy nową funkcją jest stanu w czasie rzeczywistym informacji na temat pul aplikacji, procesów, lokacje, domeny aplikacji i uruchomionych żądań. Drugie nowe korzyści są zdarzenia śledzenia szczegółowe, które śledzą żądania w całym procesie ukończone żądania i odpowiedzi.

Aby włączyć zbieranie tych śledzenia zdarzeń, usługi IIS 7 można skonfigurować do automatyczne Przechwytywanie pełną dzienników w formacie XML, w przypadku każdego żądania określoną na podstawie czasu lub kody odpowiedzi błędu.

#### <a name="web-server-diagnostics"></a>Diagnostyka serwera sieci Web
Można włączyć lub wyłączyć następujące rodzaje dzienników:

-   Szczegółowe rejestrowanie błędów — szczegółowe informacje o błędzie dla kodów stanu HTTP, które wskazują błędu (kod stanu 400 lub nowszej). Może zawierać informacje, które mogą pomóc ustalić, dlaczego serwer zwrócił kod błędu.

-   Śledzenie nieudanych żądań — szczegółowe informacje dotyczące żądań zakończonych niepowodzeniem, w tym śledzenia komponenty używani do przetwarzania żądania i czasu trwania w poszczególnych składnikach. Może to być przydatne, jeśli chcesz zwiększyć wydajność witryny lub określić, co powoduje określonego błędu HTTP, który ma zostać zwrócona.

-   Rejestrowanie serwera — informacje o transakcji HTTP za pomocą rozszerzonym formacie W3C dziennika plików w sieci Web. Jest to przydatne podczas określania ogólnego metryki witryn, takie jak liczba żądań obsłużonych lub ile żądań pochodzących z określonego adresu IP.

#### <a name="application-diagnostics"></a>Usługa Application diagnostics
[Usługa Application diagnostics](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs) umożliwia przechwytywanie informacji generowanych przez aplikację sieci web. Aplikacje ASP.NET mogą używać [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) klasy do rejestrowania informacji w dzienniku diagnostyki aplikacji. W diagnostyce aplikacji istnieją dwa główne typy zdarzeń, te związane z wydajnością aplikacji i te związane z awariami i błędami aplikacji. Awarie i błędy można podzielić na problemy dotyczące łączności, bezpieczeństwa i błędów. Problemy dotyczące awarii zazwyczaj są związane z problemem z kodu aplikacji.

W diagnostyce aplikacji można wyświetlać zdarzenia pogrupowane w następujący sposób:

-   Wszystkie (wyświetla wszystkie zdarzenia)
-   Błędy aplikacji (Wyświetla zdarzenia wyjątków)
-   Wydajność (zdarzenia dotyczące wydajności)

## <a name="storage"></a>Magazyn
W sekcji przedstawiono dodatkowe informacje na temat kluczowych funkcji usługi Azure storage security i podsumowanie uzyskać informacje na temat tych funkcji.

### <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)
Możesz zabezpieczyć swoje konto magazynu przy użyciu kontroli dostępu opartej na rolach (RBAC). Ograniczanie dostępu na podstawie [trzeba znać](https://en.wikipedia.org/wiki/Need_to_know) i [najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) jest zasad zabezpieczeń dla organizacji, które chcesz wymuszać zasady zabezpieczeń dostępu do danych. Te prawa dostępu są przyznawane, przypisując odpowiednie role RBAC do grup i aplikacji w określonym zakresie. Możesz użyć [wbudowane role kontroli RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles), np. Współautor konta magazynu, aby przypisać uprawnienia do użytkowników. Dostęp do magazynu kluczy dla konta magazynu przy użyciu [usługi Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) modelu mogą być kontrolowane za pomocą kontroli dostępu opartej na rolach (RBAC).

### <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego
[Sygnatura dostępu współdzielonego (SAS, shared access signature)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) zapewnia delegowany dostęp do zasobów w ramach konta magazynu. Sygnatura dostępu Współdzielonego oznacza, że możliwe jest przyznanie klientowi ograniczonych uprawnień do obiektów na koncie magazynu określonym przedziale czasu i z określonym zestawem uprawnień. Te ograniczone uprawnienia można przyznać bez konieczności udostępniania kluczy dostępu do Twojego konta.

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Szyfrowanie podczas transferu jest mechanizm ochrony danych podczas ich przesyłania w sieciach. Za pomocą usługi Azure Storage można zabezpieczyć dane przy użyciu:
-   [Szyfrowanie na poziomie transportu](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), taki jak HTTPS podczas przesyłania danych do i z usługi Azure Storage.

-   [Połączenie szyfrowania](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), takich jak [szyfrowania protokołu SMB 3.0](https://docs.microsoft.com/azure/storage/storage-security-guide) dla [udziałów plików platformy Azure](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files).

-   Szyfrowanie po stronie klienta, aby szyfrować dane, zanim zostanie przekazany do magazynu oraz do odszyfrowania danych, gdy zostanie przeniesiona poza magazynu.

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
W przypadku wielu organizacji szyfrowanie danych magazynowanych stanowi krok obowiązkowy kierunku danych ochrony prywatności, zgodności i niezależność danych. Istnieją trzy funkcje zabezpieczeń usługi Azure storage, które zapewnia szyfrowanie danych, która wynosi "rest":

-   [Szyfrowanie usługi Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption) umożliwia zażądanie, czy usługi storage automatyczne szyfrowanie danych podczas zapisywania jej do usługi Azure Storage.

-   [Szyfrowanie po stronie klienta](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) udostępnia również funkcję szyfrowanie danych magazynowanych.

-   [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszynę wirtualną IaaS.

### <a name="storage-analytics"></a>Analityka magazynu
[Analizy usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) umożliwia rejestrowanie i dostarcza danych metryk dotyczących konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu. Usługa Storage Analytics rejestruje szczegółowe informacje na temat udane i nieudane żądania do usługi storage. Informacja ta może służyć do monitorowania poszczególnych żądań i diagnozowanie problemów z usługą magazynu. Żądania są rejestrowane na zasadzie największej staranności. Rejestrowane są następujące typy żądań uwierzytelnionych:
-   Żądania zakończone powodzeniem.

-   Żądania zakończone niepowodzeniem oraz tym limit czasu, ograniczanie przepustowości, sieci, autoryzacji i inne błędy.

-   Żądania przy użyciu sygnatury dostępu współdzielonego (SAS), w tym żądania nie powiodło się, jak i pomyślnie.

-   Żądania do analizy danych.

### <a name="enabling-browser-based-clients-using-cors"></a>Włączanie klientów oparte na przeglądarce, przy użyciu mechanizmu CORS
[Udostępnianie zasobów między źródłami (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) to mechanizm, który umożliwia domen zezwolić wzajemnie do uzyskiwania dostępu do zasobów siebie nawzajem. Agent użytkownika wysyła dodatkowe nagłówki, aby upewnić się, że kod JavaScript, ładowane z określonej domeny może uzyskiwać dostęp do zasobów znajdujących się w innej domenie. Ostatnie domeny, która jest następnie odpowiada za pomocą dodatkowe nagłówki udzielanie lub odmawianie dostępu do oryginalnej domeny do swoich zasobów.

Tak, aby po ustawieniu reguł CORS dla usługi odpowiednio uwierzytelnione żądanie wykonane względem usługi z innej domeny jest oceniany w celu określenia, czy jest dozwolone zgodnie z regułami, które określono, usługi magazynu platformy Azure obsługują teraz mechanizmu CORS.
## <a name="networking"></a>Networking
W sekcji przedstawiono dodatkowe informacje na temat kluczowych funkcji usługi Azure network zabezpieczeń i podsumowanie uzyskać informacje na temat tych funkcji.

### <a name="network-layer-controls"></a>Formanty warstwy sieci
Kontrola dostępu do sieci jest ograniczenie łączności do i z określonymi urządzeniami lub podsieci i stanowi podstawę zabezpieczeń sieci. Celem kontroli dostępu do sieci jest upewnij się, że maszyny wirtualne i usługi są dostępne tylko użytkowników i urządzeń, do których mają dostęp.

#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
A [grupy zabezpieczeń sieci (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jest podstawowe stanowego filtrowania pakietów zapory i pozwala na kontrolowanie dostępu na podstawie [5-elementowe spójne kolekcje](https://www.techopedia.com/definition/28190/5-tuple). Sieciowe grupy zabezpieczeń nie są oferowane kontroli warstwy aplikacji lub uwierzytelniony za pomocą kontroli dostępu. One może służyć do kontrolowania ruchu przenoszenia między podsieciami w ramach usługi Azure Virtual Network i ruch sieciowy między siecią wirtualną platformy Azure i Internetem.

#### <a name="route-control-and-forced-tunneling"></a>Formant trasy i wymuszonego tunelowania
Możliwość kontrolowania zachowania routingu w sieciach wirtualnych platformy Azure jest możliwość sterowania zabezpieczeniami i dostępem sieci krytyczne. Na przykład jeśli chcesz upewnić się, że cały ruch do i z siecią wirtualną platformy Azure przechodzi przez urządzenia wirtualne zabezpieczeń, musisz mieć możliwość kontrolowania i dostosowywanie zachowania routingu. Można to zrobić, konfigurując trasy zdefiniowane przez użytkownika na platformie Azure.

[Trasy zdefiniowane przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) umożliwiają dostosowanie ścieżki ruchu przychodzącego i wychodzącego ruchu do i z poszczególnymi maszynami wirtualnymi lub podsieciami na ułatwieniu zapewnienia najbardziej bezpieczny możliwe trasy. [Wymuszone tunelowanie](https://www.petri.com/azure-forced-tunneling) to mechanizm, można użyć, aby upewnić się, że usługi nie mogą inicjować połączenie w celu urządzeń w Internecie.

To różni się od możliwość akceptowania połączeń przychodzących, a następnie odpowiadać ich. Serwery frontonu sieci web muszą odpowiadać na żądania z hostami w Internecie, a więc źródło Internet ruch jest dozwolony dla ruchu przychodzącego na tych serwerach sieci web i serwery sieci web może reagować.

Wymuszanie tunelowania jest najczęściej używany do wymuszenia ruch wychodzący do Internetu za pośrednictwem zabezpieczeń lokalnych w serwery proxy i zapory.

#### <a name="virtual-network-security-appliances"></a>Urządzenia zabezpieczeń w sieci wirtualnej
Gdy sieciowych grup zabezpieczeń, trasy zdefiniowane przez użytkownika i tunelowania zapewniają poziom zabezpieczeń na poziomie warstwy sieci i mechanizm transportu [OSI model](https://en.wikipedia.org/wiki/OSI_model), mogą wystąpić sytuacje, gdy chcesz włączyć zabezpieczenia na wyższy poziom stos. Te funkcje zabezpieczeń sieci rozszerzone dostęp przy użyciu rozwiązań urządzenie zabezpieczeń sieciowych partnerów platformy Azure. Można znaleźć najbardziej aktualne sieci partnerów platformy Azure rozwiązania w zakresie bezpieczeństwa, odwiedzając [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/) i wyszukując "zabezpieczenia" i "" zabezpieczenia sieciowe.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Sieć wirtualna Azure odzwierciedla w chmurze Twoją sieć. Jest to logiczna izolacja sieci szkieletowej sieci platformy Azure dedykowanej dla Twojej subskrypcji. W ramach tej sieci można w pełni kontrolować bloki adresów IP, ustawienia DNS, zasady zabezpieczeń i tabele tras. Można podzielić sieć wirtualną na podsieci i umieszczania maszyn wirtualnych IaaS platformy Azure (VM) i/lub [Cloud services (wystąpienia roli PaaS)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) w sieciach wirtualnych platformy Azure.

Dodatkowo można połączyć sieć wirtualną z siecią lokalną przy użyciu jednej z [opcji łączności](https://docs.microsoft.com/azure/vpn-gateway/) dostępnej na platformie Azure. W zasadzie można rozbudować swoją sieć do sieci Azure, zachowując pełną kontrolę nad blokami adresów IP i wykorzystując zapewnianą przez platformę Azure skalowalność klasy korporacyjnej.

Sieć platformy Azure obsługuje różne scenariusze bezpiecznego dostępu zdalnego. Oto niektóre z nich:

-   [Poszczególne stacje robocze nawiązać połączenie z usługą Azure Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [Łączenie sieci lokalnej z siecią wirtualną platformy Azure przy użyciu sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [Łączenie sieci lokalnej z siecią wirtualną platformy Azure za pomocą dedykowanego łącza sieci WAN](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Łączenie sieci wirtualnych platformy Azure ze sobą](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>VPN Gateway
Aby wysyłać ruch sieciowy między siecią wirtualną platformy Azure i lokacją lokalną, należy utworzyć bramę sieci VPN dla sieci wirtualnej platformy Azure. A [bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) jest typem bramy sieci wirtualnej, który wysyła zaszyfrowany ruch sieciowy przez połączenie publiczne. Za pomocą bram sieci VPN można także wysyłać ruch sieciowy między sieciami wirtualnymi platformy Azure za pośrednictwem sieci szkieletowej sieci platformy Azure.

### <a name="express-route"></a>ExpressRoute
Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) jest dedykowany łącza sieci WAN, która umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem specjalnego połączenia prywatnego wspieranego przez dostawcę połączenia.

![ExpressRoute](./media/azure-security/azure-security-fig1.png)

Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i CRM Online. Połączenie może być z sieci typu dowolna-dowolna (IP VPN), sieci Ethernet typu punkt-punkt lub przy użyciu łączności obejmującej wiele połączeń wirtualnych przez dostawcę połączenia w ramach infrastruktury współlokacji.

Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznej sieci Internet i w związku z tym można uznać za bardziej bezpieczne niż w przypadku rozwiązań opartych na sieci VPN. Dzięki temu oferują one większą niezawodność i szybkość oraz mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet.


### <a name="application-gateway"></a>Application Gateway
Microsoft [usługi Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) zapewnia [kontrolera dostarczania aplikacji (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) jako usługi, która oferuje różnorodne możliwości aplikacji równoważenia obciążenia warstwy 7.

![Application Gateway](./media/azure-security/azure-security-fig2.png)

Umożliwia optymalizowanie wydajności farmy sieci web dzięki przeniesieniu procesor CPU o znacznym wykorzystaniu kończenia żądań SSL na bramie aplikacji (tzw. "Odciążanie protokołu SSL" lub "Mostkowania SSL"). Zapewnia także inne możliwości routingu warstwy 7, takie jak działanie okrężne Dystrybucja ruchu przychodzącego, koligacja sesji na podstawie plików cookie, routing oparty na ścieżkach URL i możliwość hostowania wielu witryn sieci Web za pojedynczą usługą Application Gateway. Usługa Azure Application Gateway to moduł równoważenia obciążenia warstwy 7.

Udostępnia tryb failover, oparty na wydajności routing żądań HTTP między różnymi serwerami — w chmurze i lokalnymi.

Aplikacja udostępnia wiele funkcji kontrolera dostarczania aplikacji (ADC), w tym HTTP Równoważenie obciążenia, na podstawie plików cookie sesji koligację, [Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell) odciążania, niestandardowych sond kondycji, obsługa wielu witryn i wiele innych.

### <a name="web-application-firewall"></a>Zapora aplikacji internetowej
Zapora aplikacji sieci Web jest funkcją [usługi Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) zapewniająca ochronę aplikacjom sieci web, których użyto usługi application gateway dla standardowych funkcji kontroli dostarczania aplikacji (ADC). Zapora aplikacji internetowej realizuje ten cel, chroniąc je przed większością z 10 najpopularniejszych luk w zabezpieczeniach w Internecie OWASP.

![Zapora aplikacji internetowej](./media/azure-security/azure-security-fig1.png)

-   Ochrona przed atakami polegającymi na iniekcji SQL

-   Częste ataki w ramach sieci Web polegające na iniekcji poleceń, przemycaniu żądań HTTP, rozdzielaniu odpowiedzi HTTP i zdalnym dołączaniu plików

-   Ochrona przed naruszeniami protokołu HTTP

-   Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków

-   Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów

-   Wykrywanie typowych błędów konfiguracji aplikacji (czyli Apache, usługi IIS itp.)


Scentralizowana zapora aplikacji internetowej chroniąca przed atakami z Internetu sprawia, że zarządzanie zabezpieczeniami jest znacznie prostsze oraz zapewnia lepszą ochronę aplikacji przed intruzami. Zapora aplikacji internetowej może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach internetowych. Istniejące bramy aplikacji można łatwo przekonwertować na bramę aplikacji z zaporą aplikacji internetowej.
### <a name="traffic-manager"></a>Traffic Manager
Microsoft [usługi Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) umożliwia kontrolowanie dystrybucji ruchu użytkowników do punktów końcowych usługi w różnych centrach danych. Punkty końcowe usługi obsługiwane przez usługę Traffic Manager obejmują maszyny wirtualne platformy Azure, aplikacje sieci Web i usług w chmurze. Usługi Traffic Manager można również używać z zewnętrznymi punktami końcowymi poza platformą Azure. Usługa Traffic Manager przy użyciu systemu nazw domen (DNS) kieruje żądania klientów do najbardziej odpowiednich punktów końcowych, na podstawie [metody routingu ruchu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) i kondycji punktów końcowych.

Usługa Traffic Manager udostępnia szereg metod routingu ruchu do potrzeb różnych aplikacji, punkt końcowy kondycji [monitorowania](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)i automatycznej pracy awaryjnej. Usługa Traffic Manager jest odporna na awarie, w tym awarię całego regionu platformy Azure.
### <a name="azure-load-balancer"></a>Azure Load Balancer
Usługa [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) zapewnia aplikacjom wysoką dostępność i wydajność sieci. Jest rozkładający ruch przychodzący między dobrej kondycji wystąpień usługi zdefiniowane w zestawie z równoważeniem obciążenia modułu równoważenia obciążenia warstwy 4 (TCP, UDP). Usługa Azure Load Balancer można skonfigurować w celu:

-   Obciążenia równoważenia ruchu internetowego przychodzącego do maszyn wirtualnych. Ta konfiguracja jest określana jako [równoważenia obciążenia dostępnego z Internetu](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Równoważenie obciążenia ruchu między maszynami wirtualnymi w sieci wirtualnej, między maszynami wirtualnymi w usługach w chmurze lub między komputerami w środowisku lokalnym i maszyn wirtualnych w sieci wirtualnej między lokalizacjami. Ta konfiguracja jest określana jako [wewnętrzne Równoważenie obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview). 

- Przekazywanie ruchu zewnętrznego do określonej maszyny wirtualnej

### <a name="internal-dns"></a>Wewnętrznego serwera DNS
Można zarządzać listą serwerami DNS używanymi w sieci wirtualnej w portalu zarządzania lub w pliku konfiguracji sieci. Klienta można dodać maksymalnie 12 serwery DNS dla każdej sieci wirtualnej. Określenie serwerów DNS, jest ważne, aby sprawdzić, czy listy serwerów DNS przez klienta w odpowiedniej kolejności dla środowiska klienta. Listy serwerów DNS nie działają, działanie okrężne. Są one używane w kolejności, w jakiej zostały określone. Jeśli pierwszy serwer DNS na liście jest niedostępny, klient korzysta z tego serwera DNS, niezależnie od tego, czy serwer DNS działa poprawnie. Aby zmienić kolejność serwera DNS dla sieci wirtualnej klienta, Usuń serwery DNS z listy i dodaj je ponownie w kolejności tego klienta potrzebuje. Usługa DNS obsługuje aspektów dostępności Triada zabezpieczeń "CIA".

### <a name="azure-dns"></a>System DNS platformy Azure
[Systemu nazw domen](https://technet.microsoft.com/library/bb629410.aspx), lub w systemie DNS jest odpowiedzialny za tłumaczenia (lub rozpoznawanie) nazwę witryny sieci Web lub usługi na jej adres IP. [Usługa Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) to usługa hostingowa przeznaczona dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure. Usługa DNS obsługuje aspektów dostępności Triada zabezpieczeń "CIA".
### <a name="azure-monitor-logs-nsgs"></a>Usługa Azure Monitor dzienniki sieciowych grup zabezpieczeń
Należy włączyć następujące kategorie dzienniki diagnostyczne dla sieciowych grup zabezpieczeń:
-   Zdarzenie: Zawiera wpisy, dla których sieciowej grupy zabezpieczeń reguły są stosowane do maszyn wirtualnych i ról wystąpień na podstawie adresu MAC. Stan reguły te są gromadzone co 60 sekund.

-   Licznik reguły: Zawiera wpisy dla tyle razy, każda reguła sieciowej grupy zabezpieczeń są stosowane do odmowy lub zezwolić na ruch.

### <a name="azure-security-center"></a>Azure Security Center
Usługa Security Center pomaga zapobiegać zagrożeniom, wykrywanie i reagowanie na zagrożenia, zapewnia większą widoczność i kontrolę nad zabezpieczeń zasobów platformy Azure. Zapewnia zintegrowane zabezpieczenia monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających. Centrum zalecenia sieciowe dotyczące zapór, sieciowe grupy zabezpieczeń, konfigurowanie reguł ruchu przychodzącego i.

Zalecenia dotyczące sieci dostępne są następujące:

-   [Dodaj zaporę następnej generacji](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) zaleca się dodawania następnej generacji zapory nowej (NGFW) od partnera firmy Microsoft, aby zwiększyć swoje zabezpieczenia

-   [Kierowanie ruchu przez zaporę nowej generacji](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only) zaleca skonfigurowanych sieci reguły Sieciowej grupy zabezpieczeń, które wymuszają ruch przychodzący do maszyny Wirtualnej za pośrednictwem swojej zapory nowej generacji.

-   [Włącz sieciowe grupy zabezpieczeń na maszynie wirtualnej lub podsieci](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups) zaleca się włączenie sieciowe grupy zabezpieczeń dla podsieci lub maszyn wirtualnych.

-   [Ogranicz dostęp za pośrednictwem punktu końcowego z Internetem](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints) zaleca, aby skonfigurować reguły ruchu przychodzącego dla sieciowych grup zabezpieczeń.


## <a name="compute"></a>Wystąpienia obliczeniowe

W sekcji przedstawiono dodatkowe informacje na temat kluczowych funkcji tego obszaru i podsumowanie uzyskać informacje na temat tych funkcji.

### <a name="antimalware--antivirus"></a>Ochrony przed złośliwym oprogramowaniem i wirusami
Za pomocą modelu IaaS platformy Azure można użyć ochrony przed złośliwym oprogramowaniem z dostawców zabezpieczeń, takich jak Microsoft, Symantec, Trend Micro, McAfee i Kaspersky do ochrony maszyn wirtualnych przed złośliwymi plikami, oprogramowaniem reklamowym i innymi zagrożeniami. [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla usług Azure Cloud Services i maszyn wirtualnych jest możliwość ochrony, która ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania. Microsoft Antimalware zapewnia konfigurowalnych alertów, gdy znany prób złośliwego lub niechcianego oprogramowania do zainstalowania lub uruchomienia w systemie Azure. Microsoft Antimalware można także wdrożyć za pomocą usługi Azure Security Center

### <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń
Szyfrowania i uwierzytelniania nie poprawia bezpieczeństwo, o ile nie są chronione samych kluczy. Można uprościć zarządzanie i bezpieczeństwo, wpisami tajnymi i kluczami dzięki przechowywaniu ich w [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Key Vault oferuje opcję przechowywania kluczy w sprzętowych modułach zabezpieczeń (HSM) certyfikowane zgodnych ze standardami FIPS 140-2 poziom 2 standardy. Klucze szyfrowania programu SQL Server, do utworzenia kopii zapasowej lub [technologii transparent data encryption](https://msdn.microsoft.com/library/bb934049.aspx) można przechowywać w usłudze Key Vault przy użyciu dowolne klucze i wpisy tajne z aplikacji. Uprawnienia i dostęp do tych chronionych elementów są zarządzane za pośrednictwem [usługi Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Kopia zapasowa maszyny wirtualnej
[Usługa Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) jest to rozwiązanie chroniące dane aplikacji za pomocą zero inwestycji kapitałowych i minimalne koszty operacyjne. Błędy aplikacji może spowodować uszkodzenie danych i błędami ludzkimi wprowadzić usterek w aplikacjach, które mogą prowadzić do problemów z zabezpieczeniami. W usłudze Azure Backup są chronione, maszynach wirtualnych z systemem Windows i Linux.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Ważną częścią organizacji [firm ciągłości działania i odzyskiwania po awarii (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) strategii jest ustalenie, jak zapewnić poprawne obciążeń i aplikacji firmowych uruchomionych podczas planowanych i nieplanowanych przestojów. [Usługa Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) ułatwia organizowanie replikacji, trybu failover i odzyskiwania obciążeń i aplikacji, tak aby były dostępne z lokalizacji dodatkowej, jeśli Twoja lokalizacja główna przestanie działać.

### <a name="sql-vm-tde"></a>FUNKCJA TDE MASZYNY WIRTUALNEJ SQL
[Przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) i szyfrowanie na poziomie kolumny (CLE) są funkcje szyfrowania serwera SQL. Ta forma szyfrowania wymaga od klientów w celu przechowywania kluczy kryptograficznych używanego do szyfrowania i zarządzania nimi.

Usługa Azure Key Vault (AKV) zaprojektowano w celu poprawy zabezpieczeń i zarządzania tych kluczy w bezpiecznych i wysoko dostępnych lokalizacji. Łącznik programu SQL Server umożliwia SQL Server, aby użyć tych kluczy z usługi Azure Key Vault.

Jeśli używasz programu SQL Server na maszynach w środowisku lokalnym, istnieją kroki, które można wykonać, aby uzyskać dostęp do usługi Azure Key Vault z komputera programu SQL Server w środowisku lokalnym. Ale programu SQL Server na maszynach wirtualnych platformy Azure, możesz zaoszczędzić czas, korzystając z funkcji integracji magazynu kluczy Azure. Za pomocą kilka poleceń cmdlet programu Azure PowerShell, aby włączyć tę funkcję możesz zautomatyzować konfiguracji niezbędne do maszyny Wirtualnej SQL w celu dostępu do magazynu kluczy.

### <a name="vm-disk-encryption"></a>Szyfrowanie dysku maszyny Wirtualnej
[Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) to nowa funkcja, która ułatwia szyfrowanie dysków maszyn wirtualnych IaaS systemu Linux i Windows. Ma to zastosowanie branżowy standard funkcji BitLocker Windows oraz funkcji DM-Crypt systemu Linux, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi. To rozwiązanie jest zintegrowany z usługi Azure Key Vault, aby pomóc Ci kontrolować i zarządzanie kluczami szyfrowania dysku i wpisów tajnych w subskrypcji usługi Key Vault. Rozwiązanie zapewnia również, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane, gdy w usłudze Azure storage.

### <a name="virtual-networking"></a>Sieć wirtualna
Maszyny wirtualne muszą łączność sieciową. W celu spełnienia tego wymagania, platforma Azure wymaga połączenia z siecią wirtualną Azure maszyny wirtualne. Usługi Azure Virtual Network jest konstrukcją logiczną, zbudowany na podstawie sieci szkieletowej sieci fizycznej platformy Azure. Każdy logiczne [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) jest odizolowana od wszystkich innych sieci wirtualnych platformy Azure. Izolacja pomaga upewnić się, że ruch sieciowy we wdrożeniach nie jest dostępna dla innych klientów firmy Microsoft Azure.

### <a name="patch-updates"></a>Poprawek i aktualizacji
Poprawek i aktualizacji stanowić podstawę znajdowaniem i naprawianiem potencjalnych problemów i uprościć proces zarządzania aktualizacjami oprogramowania, zarówno dzięki zmniejszeniu liczby aktualizacji oprogramowania, które należy wdrożyć w przedsiębiorstwie, jak i przez odpowiednie zwiększenie możliwość monitorowania zgodności.

### <a name="security-policy-management-and-reporting"></a>Zarządzanie zasadami zabezpieczeń i raportowanie
[Usługa Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) pomaga zapobiec, wykrywania i reagować na zagrożeń i zapewnia większą widoczność i skuteczniejszą kontrolę ich zabezpieczeń zasobów platformy Azure. Zapewnia zintegrowane zabezpieczenia monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

### <a name="azure-security-center"></a>Azure Security Center
Usługa Security Center ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

Zabezpieczanie systemów, aplikacji i danych rozpoczyna się przy użyciu kontroli dostępu na podstawie tożsamości. Funkcje zarządzania tożsamościami i dostępem, które są wbudowane w produktów firmy Microsoft i usługi chronić organizacyjne i osobiste informacje przed nieautoryzowanym dostępem jednocześnie przyspieszając dostępna dla uprawnionych użytkowników zawsze i wszędzie tam, gdzie one potrzebny.

### <a name="secure-identity"></a>Zabezpieczanie tożsamości
Firma Microsoft używa wielu rozwiązania z zakresu bezpieczeństwa i technologii w swoich produktów i usług do zarządzania tożsamościami i dostępem.
-   [Uwierzytelnianie wieloskładnikowe](https://azure.microsoft.com/services/multi-factor-authentication/) wymaga od użytkowników do użycia na wiele sposobów, aby uzyskać dostęp, w środowisku lokalnym i w chmurze. Zapewnia silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji, podczas ograniczanych użytkowników za pomocą prostego procesu logowania.

-   [Microsoft Authenticator](https://aka.ms/authenticator) zapewnia przyjazny dla użytkownika usługi Multi-Factor Authentication w programach Microsoft Azure Active Directory i konta Microsoft, która obejmuje obsługę wearables i zatwierdzenia na podstawie linii papilarnych.

-   [Wymuszanie zasad haseł](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) zwiększa bezpieczeństwo tradycyjnych haseł przez nałożenie na długość i złożoność, wymuszone okresowych rotacji i blokady konta po nieudanych prób uwierzytelnienia.

-   [Uwierzytelnianie oparte na tokenie](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) umożliwia uwierzytelnianie za pomocą usługi Azure Active Directory.

-   [Kontrola dostępu oparta na rolach (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) umożliwia przyznawanie dostępu opartego na przypisaną rolę użytkownika, co ułatwia użytkownikom tylko takiego dostępu, których potrzebują do wykonywania ich obowiązków. RBAC można dostosować na modelu biznesowego w Twojej organizacji i tolerancji ryzyka.

-   [Zintegrowane zarządzanie tożsamości (tożsamość hybrydowa)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) pozwala zachować kontrolę dostępu użytkowników na platformach wewnętrznego centrów danych i w chmurze, tworzenia pojedynczej tożsamości użytkownika do uwierzytelniania i autoryzacji do wszystkich zasobów.

### <a name="secure-apps-and-data"></a>Zabezpieczanie aplikacji i danych
[Usługa Azure Active Directory](https://azure.microsoft.com/services/active-directory/), kompleksowe tożsamość i dostęp do chmury rozwiązanie do zarządzania, pomaga w zabezpieczeniu dostępu do danych w aplikacji w witrynie i w chmurze i ułatwia zarządzanie użytkownikami i grupami. On oferuje podstawowe usługi katalogowe, zaawansowane funkcje zarządzania tożsamościami, zabezpieczeń i zarządzanie dostępem do aplikacji i ułatwia deweloperom tworzenie zarządzania tożsamościami oparta na zasadach w swoich aplikacjach. Aby zwiększyć możliwości usługi Azure Active Directory i uzyskać dostęp do płatnych funkcji, możesz zdecydować się na wykupienie usługi Azure Active Directory w wersji Podstawowej, Premium P1 lub Premium P2.

| Bezpłatne / typowych funkcji     | Podstawowe funkcje    |Funkcje w warstwie Premium P1 |Funkcje w warstwie Premium P2 | Usługa Azure Active Directory Join — system Windows 10 tylko funkcji związanych z|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Obiekty katalogu](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Zarządzanie użytkownikami/grupami (Dodawanie/aktualizowanie/usuwanie) / oparte na użytkownikach Inicjowanie obsługi administracyjnej, Rejestracja urządzenia](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [pojedynczego logowania jednokrotnego (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [samoobsługi hasła Zmiany użytkowników w chmurze](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [łączenie (aparat synchronizacji rozszerzający katalogi lokalne do usługi Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [zabezpieczeń / Raporty użycia](https://docs.microsoft.com/azure/active-directory/active-directory-editions)       |     [Zarządzanie dostępem oparte na grupach / inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [samoobsługowego resetowania hasła dla użytkowników chmury](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [firmy marką (logowania stron/panelu dostępu)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [ Serwer Proxy aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [SLA 99,9%](https://docs.microsoft.com/azure/active-directory/active-directory-editions) |  [Grupami i aplikacjami zarządzania/Samoobsługowe aplikacji dodatki/dynamiczne grupy](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [hasło Samoobsługowe Resetowanie/zmiana/odblokowywanie za pomocą zapisu lokalnych](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Multi-Factor Authentication Uwierzytelnianie (chmura oraz środowisko lokalne (serwer MFA))](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [CAL programu MIM i serwera MIM](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [odnajdywania aplikacji w chmurze](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Connect Health](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Automatyczne przenoszenie haseł dla kont grup](https://docs.microsoft.com/azure/active-directory/active-directory-editions)|  [Ochrona tożsamości](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|  [Przyłącz urządzenie w usłudze Azure AD, logowania jednokrotnego na komputerze, Microsoft Passport dla Azure AD oraz odzyskiwania administracyjnego funkcją BitLocker](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [MDM automatycznej rejestracji, Samoobsługowe odzyskiwanie funkcją BitLocker oraz dodawanie administratorów lokalnych do urządzeń z systemem Windows 10 za pośrednictwem usługi Azure AD Dołącz do](https://docs.microsoft.com/azure/active-directory/active-directory-editions)|


- [Odnajdywania aplikacji w chmurze](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) jest funkcją premium usługi Azure Active Directory, która pozwala na identyfikowanie aplikacji chmurowych, które są używane przez pracowników w Twojej organizacji.

- [Usługa Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) to usługa zabezpieczeń, która używa funkcji wykrywania anomalii w usłudze Azure Active Directory, aby zapewnić skonsolidowany wgląd w zdarzenia o podwyższonym ryzyku i potencjalnych luk w zabezpieczeniach, które mogą wpłynąć na Twoje tożsamości w organizacji.

- [Usługa Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umożliwia przyłączanie maszyn wirtualnych platformy Azure do domeny bez konieczności wdrażania kontrolerów domeny. Użytkownicy zalogować się do tych maszyn wirtualnych przy użyciu firmowych poświadczeń usługi Active Directory i bezproblemowo można uzyskiwać dostęp do zasobów.

- [Usługa Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) to usługa zarządzania tożsamościami w wysoce dostępnej, globalnej dla aplikacji przeznaczonych dla konsumentów, które skalowanie do setek milionów tożsamości i integrację z platformami mobilnymi i platformami sieci web. Klientów można zalogować się do swoich aplikacji za pośrednictwem pełni dostosowywanego procesu, korzystających z istniejących kont społecznościowych lub można tworzyć nowe poświadczenia autonomicznych.

- [Azure Active Directory funkcje współpracy B2B](https://aka.ms/aad-b2b-collaboration) jest rozwiązaniem integracji bezpiecznego partnerów, które obsługuje relacje między firmami, pozwalając partnerom dostęp do danych i aplikacji firmy selektywnie przy użyciu ich samodzielnego zarządzania tożsamości.

- [Azure Active Directory przyłączyć](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) umożliwia rozszerzanie możliwości chmury do urządzeń z systemem Windows 10 dla scentralizowanego zarządzania. Umożliwia użytkownikom nawiązywanie połączeń z chmury firmy lub organizacji za pomocą usługi Azure Active Directory, a upraszcza dostęp do aplikacji i zasobów.

- [Usługa Azure Active Directory serwera Proxy aplikacji](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) zapewnia logowania jednokrotnego i bezpieczny dostęp zdalny dla aplikacji sieci web hostowanych lokalnie.

## <a name="next-steps"></a>Następne kroki
- [Wprowadzenie do zabezpieczeń platformy Microsoft Azure](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Usługi i funkcje platformy Azure, które służą do zabezpieczania usług i danych w ramach systemu Azure

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

Zapobiegaj zagrożeniom, wykrywaj je i odpowiadaj na nie dzięki lepszemu wglądowi w zabezpieczenia zasobów platformy Azure i lepszej kontroli nad nimi

- [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

Możliwości monitorowania w usłudze Azure Security Center w celu monitorowania zgodności z zasadami.
