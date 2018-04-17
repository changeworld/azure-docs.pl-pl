---
title: Wprowadzenie do zabezpieczeń platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zabezpieczeń platformy Azure, jego usług i jak działa.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 51d0f1f6d2689e37c1c39bf4a5e8078a2482c76d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="introduction-to-azure-security"></a>Wprowadzenie do zabezpieczeń platformy Azure
## <a name="overview"></a>Przegląd
Wiemy, że zabezpieczenia są zadania, co w chmurze i jak ważne jest aby znaleźć dokładne i aktualne informacje na temat zabezpieczeń platformy Azure. Najważniejsze przyczyny na potrzeby aplikacji i usług Azure ma korzystać z jego szerokiej gamy narzędzi zabezpieczeń i możliwości. Te narzędzia i funkcje pomocy umożliwiają tworzenie bezpiecznych rozwiązań na bezpiecznej platformie Azure. Microsoft Azure udostępnia poufność, integralność i dostępność danych klienta, jednoczesnym accountability przezroczysty.

Aby lepiej zrozumieć kolekcji zabezpieczeń formanty implementowane w systemie Microsoft Azure z perspektywy programu Microsoft operations i klienta, tym oficjalnym dokumencie "Wprowadzenie do usługi Azure zabezpieczenia", jest zapisywany na podać pełny w zabezpieczeń dostępnych w systemie Microsoft Azure.

### <a name="azure-platform"></a>Platformy Azure
Azure to platforma usługi chmury publicznej, która obsługuje szeroki wybór systemów operacyjnych, programowania języków, struktury, narzędzia, baz danych i urządzeń. Kontenery systemu Linux można uruchamiać z integracją rozwiązania Docker; Tworzenie aplikacji za pomocą języka JavaScript, Python, .NET, PHP, Java i Node.js; Kompilacja zapleczy dla systemu iOS, Android i Windows urządzeń.

Usługi w chmurze publicznej Azure obsługuje te same technologie miliony deweloperów i informatyków już zależne i zaufania. Podczas tworzenia lub migracji zasobów informatycznych do dostawcy usług chmury publicznej, są zależne możliwości Twojej organizacji do ochrony aplikacji i danych z usług i formanty zapewniają do zarządzania zabezpieczeniami elementów zawartości opartej na chmurze.

Infrastruktury platformy Azure umożliwiają z obiektu aplikacji do obsługi jednocześnie miliony klientów i zapewnia foundation godne zaufania, na którym firmy mogą spełnić ich wymagań dotyczących zabezpieczeń.

Ponadto Azure udostępnia szereg opcji zabezpieczeń można skonfigurować oraz możliwość ich kontroli, dzięki czemu można dostosować zabezpieczeń, aby spełnić unikatowe wymagania danej organizacji wdrożeń. Ten dokument pomaga w zrozumieniu sposobu Azure zabezpieczeń możliwości mogą pomóc spełnić te wymagania.

> [!Note]
> Głównym celem niniejszego dokumentu jest w formantach skierowane do klienta, które służy do dostosowywania i zwiększyć bezpieczeństwo dla usług i aplikacji.
>
> Możemy podać niektóre informacje ogólne, ale szczegółowe informacje na temat sposobu Microsoft zabezpiecza platformą Azure, zobacz informacje zawarte w [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

### <a name="abstract"></a>Abstrakcyjny
Początkowo chmury publicznej migracje zostały regulowane przez kosztach i elastyczności do innowacji. Zabezpieczeń został uznany za istotny głównych trochę czasu, a nawet korkiem Pokaż, chmury publicznej migracji. Jednak zabezpieczeń chmury publicznej przeszła z głównym problemem z jednym sterowniki migracja do chmury. To uzasadnienie jest wyższego poziomu możliwości dostawcom usług chmur publicznych dużych chronić aplikacje i dane zasoby oparte na chmurze.

Infrastruktura platformy Azure została zaprojektowana kompleksowo, począwszy od obiektu po aplikacje hostujące jednocześnie miliony klientów, i zapewnia wiarygodną podstawę zaspokajania potrzeb firm w zakresie bezpieczeństwa. Ponadto platforma Azure udostępnia szereg opcji zabezpieczeń można konfigurować i możliwość ich kontroli, dzięki czemu można dostosować zabezpieczeń, aby spełnić unikatowe wymagania wdrożeń w celu spełnienia dział IT zasady kontroli i stosować się do zewnętrznego przepisami.

W tym dokumencie przedstawiono podejście firmy Microsoft do zabezpieczeń w ramach platformy Microsoft Azure w chmurze:
* Funkcje zabezpieczeń zaimplementowana przez firmę Microsoft do zabezpieczania infrastruktury platformy Azure, dane klientów i aplikacji.
* Usługi platformy Azure i funkcji zabezpieczeń dostępnych w celu zarządzania zabezpieczeniami usług i danych w ramach Twojej subskrypcji platformy Azure.

## <a name="summary-azure-security-capabilities"></a>Możliwości zabezpieczeń platformy Azure podsumowania
Poniższa tabela Podaj krótki opis funkcji zabezpieczeń zaimplementowana przez firmę Microsoft do zabezpieczania infrastruktury platformy Azure, dane klientów i bezpieczeństwo aplikacji.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Funkcje zabezpieczeń zaimplementowana do zabezpieczania platformy Azure:
Funkcje wymienione w następujących są funkcje, które można przejrzeć, aby zagwarantować, że platforma Azure jest zarządzana w bezpieczny sposób. Dostarczono łącza dla dalszego przechodzenia na jak Microsoft adresów pytania zaufania klientów w czterech obszarów: Secure platformy, ochrony prywatności & formantów, zgodności i przezroczystości.


| [Zabezpieczenia platformy](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Prywatność i formantów](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Zgodność](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Przezroczystość](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Cyklu programowania zabezpieczeń](https://www.microsoft.com/en-us/sdl/)wewnętrzny inspekcje zakończone | [Zarządzanie danymi cały czas](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Centrum zaufania](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Jak Microsoft zabezpiecza dane klienta w usługach Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Obowiązkowe szkolenia w zakresie zabezpieczeń, kontroli tła](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Kontrolowanie danych lokalizacji](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Wspólnego koncentratora formantów](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Jak Microsoft zarządzać lokalizacji danych w usługach Azure](http://azuredatacentermap.azurewebsites.net/)|
| [Testowanie penetracji](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [wykrywania nieautoryzowanego dostępu, DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [inspekcji i rejestrowanie](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Zapewnić dostęp do danych na własnych warunkach](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Usługi w chmurze powodu starannością Lista kontrolna](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[Na jakie warunki, który w programie Microsoft dostępu do danych](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Stan grafiki centra danych](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), zabezpieczenia fizyczne [zabezpieczenia sieci](https://docs.microsoft.com/azure/security/security-network-overview) | [Odpowiada organom ścigania](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Zgodności według lokalizacji & branży usługi](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Jak Microsoft zabezpiecza dane klienta w usługach Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Reagowania na zdarzenia zabezpieczeń](http://aka.ms/SecurityResponsepaper), [udostępnionych odpowiedzialności](http://aka.ms/sharedresponsibility) |[Standardy rygorystyczne prywatności](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Przejrzyj certyfikacji dla usług Azure, Centrum przezroczystości](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Funkcje zabezpieczeń oferowanych na platformie Azure w celu zabezpieczenia danych i aplikacji
W zależności od modelu usługi chmury Brak zmiennej odpowiedzialność za kto jest odpowiedzialny za zarządzanie zabezpieczeniami aplikacji lub usługi. Brak możliwości dostępnych na platformie Azure, aby pomóc w realizacji tych odpowiedzialności za pośrednictwem wbudowane funkcje oraz rozwiązań partnerskich, które można wdrożyć do subskrypcji platformy Azure.

Wbudowane funkcje są zorganizowane w obszarów funkcjonalnych sześć (6): operacje, aplikacji, magazynu, sieciowej, obliczeniowej i tożsamości. Więcej szczegółowych informacji o funkcji i możliwości są dostępne na platformie Azure w następujących obszarach sześć (6) są realizowane za pośrednictwem informacje podsumowujące.

## <a name="operations"></a>Operacje
Ta sekcja zawiera dodatkowe informacje na temat kluczowych funkcji w ramach operacji zabezpieczeń i podsumowanie informacji o tych funkcjach.

### <a name="operations-management-suite-security-and-audit-dashboard"></a>Operations Management Suite zabezpieczeń i inspekcji pulpit nawigacyjny
[Rozwiązania OMS zabezpieczeń i inspekcji](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) daje szczegółowy wgląd w Twojej organizacji IT stan zabezpieczeń z [zapytania wyszukiwania wbudowanych](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) dla godne uwagi problemy wymagające uwagi. Pulpit nawigacyjny [Zabezpieczenia i inspekcja](https://technet.microsoft.com/library/mt484091.aspx) jest głównym ekranem prezentujące wszystkie funkcje pakietu OMS związane z bezpieczeństwem. Zawiera ogólne wgląd w stan zabezpieczeń komputerów. Daje również możliwość wyświetlenia wszystkich zdarzeń z ostatnich 24 godzin, 7 dni lub dowolnego innego przedziału czasu.

Ponadto można skonfigurować zabezpieczeń OMS & zgodności do [automatycznie przeprowadzić określonych akcji](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) po wykryciu określonego zdarzenia.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Usługa Azure Resource Manager ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) umożliwia pracę z zasobami w rozwiązaniu jako grupa. Wszystkie zasoby danego rozwiązania można wdrożyć, zaktualizować lub usunąć w ramach jednej skoordynowanej operacji. Możesz użyć [szablonu usługi Azure Resource Manager](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) dla wdrożenia i że szablon można używać w różnych środowiskach testowania, przemieszczania i produkcji. Usługa Resource Manager zapewnia funkcje zabezpieczeń, inspekcji i znakowania ułatwiające zarządzanie zasobami po wdrożeniu.

Wdrożenia na podstawie szablonu usługi Resource Manager Azure poprawić zabezpieczenia rozwiązań wdrożona na platformie Azure, ponieważ standard zabezpieczeń Sterowanie ustawieniami i może być zintegrowane z znormalizowanych wdrożenia na podstawie szablonu. Zmniejsza ryzyko błędów konfiguracji zabezpieczeń, które mogą mieć miejsce podczas wdrożenia ręczne.

### <a name="application-insights"></a>Application Insights
[Usługa Application Insights](https://docs.microsoft.com/azure/application-insights/) jest rozszerzalną usługę zarządzania wydajności aplikacji (APM) dla deweloperów sieci web. Z usługi Application Insights można monitorować aplikacji sieci web na żywo i automatycznego wykrywania anomalii wydajności. Zawiera on analytics zaawansowane narzędzia ułatwiające diagnozowanie problemów i zrozumieć, co faktycznie zrobić użytkownicy z aplikacjami. Monitoruje aplikację cały czas, który jest uruchomiony podczas testowania i po jej wdrożeniu lub opublikowane.

Usługa Application Insights tworzy wykresów i tabel, które zawierają, na przykład, jakich porach dnia otrzymasz większość użytkowników jest stopień reakcji aplikacji i jak zaspokaja zewnętrznych usług, których ona zależy.

Jeśli występują awarie, awarii lub problemów z wydajnością, można przeszukiwać dane telemetryczne szczegółowo diagnozowanie przyczyn. I usługa wysyła wiadomości e-mail w przypadku zmiany w dostępności i wydajności aplikacji. Szczegółowe informacje o aplikacji w związku z tym staje się narzędzie cenne zabezpieczeń, ponieważ ułatwia z dostępnością w poufności, integralności i dostępności Triada zabezpieczeń.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) oferuje wizualizacji, zapytania, routingu, alerty, automatyczne skalowanie i automatyzacji na dane zarówno z infrastrukturą systemu Azure ([dziennik aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)) i każdego pojedynczego zasobu platformy Azure ([diagnostycznych Rejestruje](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)). Azure Monitor umożliwia ostrzec zdarzeń dotyczących zabezpieczeń, które są generowane w dziennikach Azure.

### <a name="log-analytics"></a>Log Analytics
[Analiza dziennika](https://azure.microsoft.com/documentation/services/log-analytics/) częścią [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) — stanowi rozwiązanie IT zarządzanie zarówno lokalnie, jak i innych firm oparte na chmurze infrastruktury (na przykład AWS) oprócz zasobów platformy Azure. Można go przekierować dane z monitora Azure bezpośrednio do analizy dzienników, pozwala zobaczyć dzienniki i metryki dla całego środowiska w jednym miejscu.

Analiza dzienników mogą być przydatne narzędzie analizy śledczej i innych zabezpieczeń narzędzie umożliwia szybkie wyszukiwanie z dużą ilością związanych z zabezpieczeniami wpisów z podejścia elastycznych możliwości tworzenia zapytań. Ponadto lokalnymi [dzienniki zapory i serwera proxy mogą być eksportowane do usługi Azure i dostępne do analizy przy użyciu analizy dzienników.](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)

### <a name="azure-advisor"></a>Azure Advisor
[Klasyfikator Azure](https://docs.microsoft.com/azure/advisor/) jest konsultanta spersonalizowane chmury, ułatwiająca zoptymalizować wdrożeń platformy Azure. Analizuje on konfigurację zasobów i dane telemetryczne użycia. Następnie sugeruje rozwiązania, aby pomóc w udoskonalaniu [wydajności](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), [zabezpieczeń](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations), i [wysokiej dostępności](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations) zasobów podczas wyszukiwania możliwości [zmniejszyć ogólną Azure spędzają](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations). Klasyfikator Azure udostępnia zalecenia dotyczące zabezpieczeń, który umożliwia znaczące poprawy ogólny stan zabezpieczeń, rozwiązań, które wdrażasz na platformie Azure. Zalecenia te są pobierane z analizy zabezpieczeń wykonywane przez [Centrum zabezpieczeń Azure.](https://docs.microsoft.com/azure/security-center/security-center-intro)

### <a name="azure-security-center"></a>Azure Security Center
Usługa [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

Ponadto Centrum zabezpieczeń Azure ułatwia operacje zabezpieczeń dzięki funkcjom pojedynczy pulpit nawigacyjny tego powierzchni alertów i zaleceń, które mogą być przetwarzane natychmiast. Często może rozwiązać problemy za pomocą jednego kliknięcia w konsoli Centrum zabezpieczeń Azure.
## <a name="applications"></a>Aplikacje
Sekcja ta zawiera dodatkowe informacje na temat kluczowych funkcji w aplikacji zabezpieczeń i podsumowanie informacji o tych funkcjach.

### <a name="web-application-vulnerability-scanning"></a>Skanowanie luki w zabezpieczeniach aplikacji w sieci Web
Jedną z najprostszych sposobów wprowadzenie testowanie pod kątem luk w zabezpieczeniach w Twojej [aplikacji usługi app Service](https://docs.microsoft.com/azure/app-service/app-service-web-overview) jest użycie [integrację z usługa Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) przeprowadzić jednym kliknięciem usterki skanowanie w aplikacji. Możesz wyświetlić wyniki testów w raporcie o łatwe do zrozumienia i Dowiedz się, jak rozwiązać każdy luki w zabezpieczeniach instrukcje krok po kroku.

### <a name="penetration-testing"></a>Testy penetracyjne
Jeśli chcesz przeprowadzić własne testy penetracji lub chcesz użyć innego dostawcę lub skanera pakietu, należy wykonać [Azure penetracji testowanie procesu zatwierdzania](https://security-forms.azure.com/penetration-testing/terms) i uzyskiwanie pobieraniu do wykonywania testów żądaną penetracji.

### <a name="web-application-firewall"></a>Zapora aplikacji sieci Web
Zapora aplikacji sieci web (WAF) w [brama aplikacji w usłudze Azure](https://azure.microsoft.com/services/application-gateway/) pomaga chronić aplikacje sieci web z typowych ataków opartych na sieci web takich jak iniekcja kodu SQL, ataki skryptów między witrynami i przejęcie kontroli sesji. Jest wstępnie skonfigurowane z ochrony przed zagrożeniami identyfikowane przez [Otwórz sieci Web aplikacji zabezpieczeń projektu (OWASP) jako znanych luk 10 pierwszych](https://msdn.microsoft.com/library/).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Uwierzytelnianie i autoryzacja w usłudze Azure App Service
[Aplikacja usługi uwierzytelniania / autoryzacji](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) jest funkcją, która umożliwia aplikacji do logowania użytkowników, dzięki czemu nie trzeba zmienić kodu w zapleczu aplikacji. Zapewnia prosty sposób ochrona aplikacji i pracować z danymi użytkownika.

### <a name="layered-security-architecture"></a>Architektura zabezpieczeń warstwowych
Ponieważ [środowiska usługi App Service](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro) Podaj odizolowanym środowisku uruchomieniowym wdrożonych w [sieci wirtualnej Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), deweloperzy mogą tworzyć architektury zabezpieczeń warstwowych, zapewniając różne poziomy dostęp do sieci dla każdej warstwy aplikacji. Wspólne dążenie jest Ukryj zapleczy interfejsu API z dostępem do Internetu ogólne i Zezwalaj tylko na interfejsy API do wywołania przez aplikacje sieci web nadrzędnego. [Sieciowe grupy zabezpieczeń (NSG)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) pozwala na podsieci sieci wirtualnej platformy Azure zawierającą środowiska usługi App Service ograniczenia publiczny dostęp do aplikacji interfejsu API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnostyka serwera sieci Web i diagnostyki aplikacji
Aplikacje sieci web usługi aplikacji udostępniają funkcje diagnostyczne dla rejestrowanie informacji z serwera sieci web i aplikacji sieci web. Są one logicznie podzielone na [sieci web diagnostyki serwera](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log) i [programu application diagnostics](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Serwer sieci Web zawiera dwa główne poprawę diagnozowanie i rozwiązywanie problemów z witryn i aplikacji.

Pierwszy nowej funkcji jest stan w czasie rzeczywistym informacje o puli aplikacji, procesów roboczych, lokacje, domeny aplikacji i uruchomionych żądań. Drugie nowe zalety są zdarzenia śledzenia szczegółowe, które śledzą żądania w całym procesie ukończone żądania i odpowiedzi.

Aby włączyć zbieranie zdarzeń śledzenia, usługi IIS 7 można skonfigurować automatycznie przechwytywania dzienników śledzenia pełna w formacie XML dla dowolnego konkretnego żądania na podstawie czasu lub błędu kody odpowiedzi.

#### <a name="web-server-diagnostics"></a>Diagnostyka serwera sieci Web
Można włączyć lub wyłączyć następujące rodzaje dzienników:

-   Szczegółowe rejestrowanie błędów — szczegółowe informacje o błędzie kodów stanu HTTP, które wskazania błędu (kod stanu 400 lub nowszej). Może zawierać informacje, które mogą pomóc ustalić, dlaczego serwer zwrócił kod błędu.

-   Śledzenie nieudanych żądań — szczegółowe informacje dotyczące żądań zakończonych niepowodzeniem, w tym śladu używane do przetwarzania żądania oraz godzinę w poszczególnych składników składników usług IIS. Może to być przydatne, jeśli chcesz zwiększyć wydajność witryny lub określić, co powoduje określonego błędu HTTP do zwrócenia.

-   Rejestrowanie serwera — informacje o transakcjach HTTP przy użyciu rozszerzonym formacie W3C dziennika plików w sieci Web. Jest to przydatne podczas określania ogólnego metryki lokacji, takie jak liczba żądań obsłużonych lub liczbę żądań pochodzą z określonego adresu IP.

#### <a name="application-diagnostics"></a>Diagnostyka aplikacji
[Usługa Application diagnostics](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log) umożliwia przechwytywanie informacji generowanych przez aplikację sieci web. Aplikacje ASP.NET mogą używać [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) klasy do rejestrowania informacji w dzienniku diagnostyki aplikacji. W diagnostyce aplikacji istnieją dwa główne typy zdarzeń, tych związanych z wydajnością i powiązane z awariami i błędami aplikacji. Awarie i błędy można podzielić na problemy dotyczące łączności, bezpieczeństwa i niepowodzenie. Problemy dotyczące awarii zazwyczaj są związane z problem z kodu aplikacji.

W diagnostyce aplikacji można wyświetlać zdarzenia pogrupowane w następujący sposób:

-   Wszystkie (wyświetla wszystkie zdarzenia)
-   Błędy aplikacji (Wyświetla zdarzenia wyjątków)
-   Wydajność (zdarzenia dotyczące wydajności)

## <a name="storage"></a>Magazyn
Sekcja ta zawiera dodatkowe informacje na temat kluczowych funkcji usługi Azure storage zabezpieczeń i podsumowanie informacji o tych funkcji.

### <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)
Można zabezpieczyć konto magazynu z kontroli dostępu opartej na rolach (RBAC). Ograniczanie dostępu na podstawie [, trzeba znać](https://en.wikipedia.org/wiki/Need_to_know) i [najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) jest zasad zabezpieczeń dla organizacji, które mają zostać wymuszone zasady zabezpieczeń dla dostępu do danych. Te prawa dostępu są udzielane przez przypisywanie odpowiednie role RBAC do grup i aplikacji w określonego zakresu. Można użyć [wbudowane role RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles), takich jak współautora konta magazynu, aby przypisać uprawnienia do użytkowników. Dostęp do magazynu kluczy dla konta magazynu za pomocą [usługi Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) modelu mogą być kontrolowane za pośrednictwem kontroli dostępu opartej na rolach (RBAC).

### <a name="shared-access-signature"></a>Sygnatury dostępu współdzielonego
[Sygnatura dostępu współdzielonego (SAS, shared access signature)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) zapewnia delegowany dostęp do zasobów w ramach konta magazynu. Sygnatury dostępu Współdzielonego oznacza, że można udzielać się, że klient ograniczone uprawnienia do obiektów na koncie magazynu w określonym przedziale i z określonym zestawem uprawnień. Ograniczone uprawnienia można przyznać bez konieczności udostępniania kluczy dostępu konta.

### <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania
Szyfrowanie podczas przesyłania jest mechanizm ochrony danych podczas ich przesyłania w sieciach. Z usługą Azure Storage można zabezpieczyć dane przy użyciu:
-   [Szyfrowanie na poziomie transportu](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), taki jak HTTPS podczas transferu danych do i z usługi Azure Storage.

-   [Połączenie szyfrowania](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), takich jak [szyfrowania protokołu SMB 3.0](https://docs.microsoft.com/azure/storage/storage-security-guide) dla [udziałów plików Azure](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files).

-   Szyfrowanie po stronie klienta, aby szyfrować dane, zanim zostanie przekazany do magazynu i do odszyfrowania danych po przeniesieniu poza magazynu.

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku
W przypadku wielu organizacji szyfrowanie danych magazynowanych jest obowiązkowy krok na drodze prywatność danych, zgodności i suwerenności danych. Istnieją trzy funkcje zabezpieczeń usługi Azure storage, zapewniające szyfrowania danych, która jest "w stanie spoczynku":

-   [Szyfrowanie usługi Magazyn](https://docs.microsoft.com/azure/storage/storage-service-encryption) umożliwia zażądanie, czy Usługa magazynu automatyczne szyfrowanie danych podczas zapisywania go do magazynu Azure.

-   [Szyfrowanie po stronie klienta](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) oferuje także funkcję szyfrowania w stanie spoczynku.

-   [Szyfrowanie dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszyny wirtualne IaaS.

### <a name="storage-analytics"></a>Analityka magazynu
[Analizy usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) wykonuje rejestrowanie i udostępnia metryki danych dla konta magazynu. Dane te mogą posłużyć do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z kontem magazynu. Analityka magazynu rejestruje szczegółowe informacje na temat udane i nieudane żądania do usługi magazynu. Te informacje może służyć do monitorowania poszczególnych żądań i diagnozowanie problemów z usługą magazynu. Żądania są rejestrowane w sposób optymalny. Rejestrowane są następujące typy żądań uwierzytelnionych:
-   Liczba pomyślnych żądań.

-   Nieudane żądania, w tym limitu czasu, ograniczania przepustowości sieci, autoryzacji i inne błędy.

-   Żądania przy użyciu dostępu sygnatury dostępu Współdzielonego, włącznie z żądaniami nie powiodło się i powiodło się.

-   Żądania do analizy danych.

### <a name="enabling-browser-based-clients-using-cors"></a>Włączanie przeglądarki klientów przy użyciu mechanizmu CORS
[Udostępnianie zasobów między źródłami (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) jest mechanizm umożliwiający domen tak, aby zezwolić wzajemnie do uzyskiwania dostępu do zasobów siebie nawzajem. Agent użytkownika wysyła dodatkowych nagłówków, aby upewnić się, czy kod JavaScript załadowane z określonej domeny może uzyskać dostęp do zasobów znajdujących się w innej domenie. Ostatnie domeny następnie odpowiedzi z dodatkowych nagłówków zezwalających lub nie zezwalających oryginalnego dostępu do domeny, do jej zasobów.

Usług Azure storage obsługuje teraz CORS tak, aby po ustawieniu zasad CORS dla usługi, odpowiednio uwierzytelnione żądania dotyczącego z usługą z innej domeny jest oszacowywane, aby określić, czy jest dozwolone zgodnie z regułami, które określono.
## <a name="networking"></a>Networking
Sekcja ta zawiera dodatkowe informacje na temat kluczowych funkcji w sieci Azure zabezpieczeń i podsumowanie informacji o tych funkcjach.

### <a name="network-layer-controls"></a>Formanty warstwy sieci
Kontrola dostępu do sieci jest czynnością ograniczenia łączności do i z określonymi urządzeniami lub podsieci i reprezentuje podstawowych zabezpieczeń sieci. Celem kontroli dostępu do sieci jest upewnienie się, że usług i maszyn wirtualnych, na których są dostępne tylko dla użytkowników i urządzeń, do których mają dostęp.

#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
A [grupy zabezpieczeń sieci (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jest filtrowanie zapory i podstawowe pakietów stanowe umożliwia kontrolowanie dostępu na podstawie [5-elementowej](https://www.techopedia.com/definition/28190/5-tuple). Grupy NSG nie udostępniają kontroli warstwy aplikacji lub uwierzytelnionego kontroli dostępu. One może służyć do kontroli ruchu przechodzenia między podsieci w sieci wirtualnej platformy Azure i ruchu między sieci wirtualnej platformy Azure i z Internetu.

#### <a name="route-control-and-forced-tunneling"></a>Formant trasy i wymuszanie tunelowania
Możliwość kontrolowania zachowania routingu w sieci wirtualnej platformy Azure jest krytyczny sieci możliwości kontroli dostępu i zabezpieczeń. Na przykład jeśli chcesz upewnić się, że cały ruch do i z sieci wirtualnej Azure przechodzi przez tego urządzenia wirtualnego zabezpieczeń, musisz mieć możliwość kontrolowania i dostosować zachowanie routingu. Można to zrobić, konfigurując trasy zdefiniowane przez użytkownika na platformie Azure.

[Zdefiniowane przez użytkownika tras](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) umożliwiają dostosowanie ścieżki przychodzący i wychodzący ruch do i poza poszczególnych maszyn wirtualnych i podsieci pozwalających na ułatwieniu zapewnienia najbardziej bezpieczne możliwe trasy. [Wymuszone tunelowanie](https://www.petri.com/azure-forced-tunneling) mechanizm służy do zapewnienia usług nie są dozwolone do nawiązania połączenia z urządzeniami przez Internet.

To jest inna niż możliwość akceptować połączeń przychodzących, a następnie odpowiada do nich. Serwerów frontonu sieci web muszą odpowiadać na żądania z hostami w Internecie, a więc Internet-powierzając jej ich konserwację ruch jest dozwolony przychodzące do tych serwerów sieci web i serwerów sieci web mogą odpowiadać.

Wymuszanie tunelowania jest najczęściej używany do wymusić ruch wychodzący do Internetu, aby przejść przez serwery proxy zabezpieczeń lokalnych i zapory.

#### <a name="virtual-network-security-appliances"></a>Urządzenia zabezpieczeń sieci wirtualnej
Gdy grup zabezpieczeń sieci, trasy zdefiniowane przez użytkownika i wymuszanie tunelowania zapewniają poziom zabezpieczeń na poziomie warstwy sieci i transportu [OSI model](https://en.wikipedia.org/wiki/OSI_model), czasami, jeśli chcesz włączyć zabezpieczeń na wyższych poziomach stos. Te funkcje zabezpieczeń rozszerzonych sieci mogą korzystać za pomocą rozwiązania urządzenia zabezpieczeń sieci Azure partnera. Można znaleźć najbardziej aktualne sieci Azure partnerów rozwiązań zabezpieczeń poprzez wizytę [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/) i wyszukując "zabezpieczenia" i "zabezpieczenia sieciowe".

### <a name="azure-virtual-network"></a>Azure Virtual Network

Sieć wirtualna Azure odzwierciedla w chmurze Twoją sieć. Jest logiczną izolacją sieci szkieletowej sieć platformy Azure przeznaczoną dla Twojej subskrypcji. W ramach tej sieci można w pełni kontrolować bloki adresów IP, ustawienia DNS, zasady zabezpieczeń i tabele tras. Można podzielić sieć na podsieci i umieścić maszynach wirtualnych Azure IaaS (VM) i/lub [chmury usługi (wystąpienia roli PaaS)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) w sieciach wirtualnych platformy Azure.

Dodatkowo można połączyć sieć wirtualną z siecią lokalną przy użyciu jednej z [opcji łączności](https://docs.microsoft.com/azure/vpn-gateway/) dostępnej na platformie Azure. W zasadzie można rozbudować swoją sieć do sieci Azure, zachowując pełną kontrolę nad blokami adresów IP i wykorzystując zapewnianą przez platformę Azure skalowalność klasy korporacyjnej.

Sieć platformy Azure obsługuje różne scenariusze bezpieczny dostęp zdalny. Oto niektóre z nich:

-   [Połącz stacjach roboczych do sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [Połączyć sieć lokalną sieć wirtualną platformy Azure z sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [Połączyć sieć lokalną sieć wirtualną platformy Azure dedykowane łącza sieci WAN](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Połączenia sieci wirtualnych platformy Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>VPN Gateway
Aby wysyłać ruch sieciowy między sieci wirtualnej platformy Azure i witryny lokalnej, należy utworzyć bramy sieci VPN dla sieci wirtualnej platformy Azure. A [bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) typ bramy sieci wirtualnej, która wysyła szyfrowanego ruchu sieciowego przez publicznego połączenia. Umożliwia także bramy sieci VPN na wysyłanie ruchu między sieciami wirtualnymi platformy Azure za pośrednictwem usługi Azure sieci szkieletowej.

### <a name="express-route"></a>ExpressRoute
Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) jest łącze WAN dedykowanych umożliwia rozszerzenie sieci lokalnej w chmurze firmy Microsoft za pośrednictwem dedykowanego połączenia prywatne w ramach dostawcy łączności.

![ExpressRoute](./media/azure-security/azure-security-fig1.png)

Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i CRM Online. Połączenie może być z sieci typu dowolna-dowolna (IP VPN), sieci Ethernet typu punkt-punkt lub przy użyciu łączności obejmującej wiele połączeń wirtualnych przez dostawcę połączenia w ramach infrastruktury współlokacji.

Połączenia ExpressRoute nie został przekroczony publicznej sieci Internet i w związku z tym może być uważany za bezpieczniejszy od rozwiązań opartych na sieci VPN. Dzięki temu oferują one większą niezawodność i szybkość oraz mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet.


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) zapewnia [kontrolera dostarczania aplikacji (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) jako usługa, oferty różne obciążenia warstwy 7 równoważenia możliwości aplikacji.

![Application Gateway](./media/azure-security/azure-security-fig2.png)

Umożliwia optymalizowanie wydajności kolektywu serwerów sieci web dzięki przeniesieniu Procesora znacznym kończenia żądań SSL na bramie aplikacji (nazywane również "Odciążanie protokołu SSL" lub "Mostkowania SSL"). Umożliwia także inne warstwy 7 routingu możliwości, takie jak rozdzielanie ruchu przychodzącego, koligacji na podstawie plików cookie sesji, routingu adresów URL na podstawie ścieżki i może obsługiwać wiele witryn sieci Web za pojedyncza brama aplikacji. Usługa Azure Application Gateway to moduł równoważenia obciążenia warstwy 7.

Udostępnia tryb failover, oparty na wydajności routing żądań HTTP między różnymi serwerami — w chmurze i lokalnymi.

Aplikacja udostępnia wiele funkcji kontrolera dostarczania aplikacji (ADC), w tym HTTP załadować równoważenia, na podstawie plików cookie koligacji sesji [Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell) sondy kondycji niestandardowych odciążania, obsługę wielu lokacji i wiele innych.

### <a name="web-application-firewall"></a>Zapora aplikacji sieci Web
Zapora aplikacji sieci Web jest funkcją [brama aplikacji w usłudze Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) który zapewnia ochronę do aplikacji sieci web, które używają bramy aplikacji dla standardowych funkcji kontroli dostarczania aplikacji (ADC). Zapora aplikacji sieci Web realizuje ten cel, chroniąc je przed większością z 10 najpopularniejszych luk w zabezpieczeniach sieci Web OWASP.

![Zapora aplikacji sieci Web](./media/azure-security/azure-security-fig1.png)

-   Ochrona przed atakami polegającymi na iniekcji SQL

-   Częste ataki w ramach sieci Web polegające na iniekcji poleceń, przemycaniu żądań HTTP, rozdzielaniu odpowiedzi HTTP i zdalnym dołączaniu plików

-   Ochrona przed naruszeniami protokołu HTTP

-   Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków

-   Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów

-   Wykrycie typowych błędów konfiguracji aplikacji (czyli Apache usług IIS, itp.)


Scentralizowana zapora aplikacji sieci Web chroniąca przed atakami w sieci Web sprawia, że zarządzanie zabezpieczeniami jest znacznie prostsze oraz zapewnia lepszą ochronę aplikacji przed intruzami. Zapora aplikacji sieci Web może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach sieci Web. Istniejące bramy aplikacji można łatwo przekonwertować na bramę aplikacji z zaporą aplikacji sieci Web.
### <a name="traffic-manager"></a>Traffic Manager
Microsoft [usługi Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) pozwala kontrolować dystrybucję ruchu użytkowników dla punktów końcowych usług w różnych centrach danych. Punkty końcowe usługi obsługiwane przez usługę Traffic Manager obejmują maszynach wirtualnych platformy Azure, aplikacji sieci Web i usługi w chmurze. Usługi Traffic Manager można również używać z zewnętrznymi punktami końcowymi poza platformą Azure. Menedżer ruchu używa systemu nazw domen (DNS) do klienta żądania kierowane do najbardziej odpowiedniego punktu końcowego na podstawie [metody routingu ruchu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) i kondycję punktów końcowych.

Menedżer ruchu udostępnia szereg metod routingu ruchu do potrzeb inną aplikację, kondycji punktu końcowego [monitorowania](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)i automatycznej pracy awaryjnej. Menedżer ruchu jest odporność na uszkodzenia, łącznie z awarią całej region platformy Azure.
### <a name="azure-load-balancer"></a>Azure Load Balancer
Usługa [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) zapewnia aplikacjom wysoką dostępność i wydajność sieci. Jest równoważenia obciążenia warstwy 4 (TCP, UDP), który rozdziela przychodzący ruch między dobrej kondycji wystąpień usługi zdefiniowane w zestawie o zrównoważonym obciążeniu. Moduł równoważenia obciążenia Azure można skonfigurować w celu:

-   Równoważenia obciążenia przychodzącego Internet ruchu do maszyn wirtualnych. Ta konfiguracja jest określana jako [równoważenia obciążenia internetowy](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Ruch Równoważenie obciążenia między maszynami wirtualnymi w sieci wirtualnej, między maszynami wirtualnymi w usługi w chmurze lub między komputerami lokalnymi i maszyn wirtualnych w sieci wirtualnej między lokalizacjami. Ta konfiguracja jest określana jako [równoważenia obciążenia wewnętrznego](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview). 

- Przesyłanie zewnętrznego ruchu sieciowego do określonej maszyny wirtualnej

### <a name="internal-dns"></a>Wewnętrzny serwer DNS
Można zarządzać listą serwery DNS używane w sieci wirtualnej w portalu zarządzania lub w pliku konfiguracji sieci. Klienta można dodać do 12 serwerów DNS dla każdej sieci wirtualnej. Podczas określania serwerów DNS, jest ważne, aby sprawdzić, czy lista serwerów DNS przez klienta w odpowiedniej kolejności dla środowiska klienta. Listy serwerów DNS nie działają okrężnego. Są one używane w kolejności, w jakiej zostały określone. Jeśli pierwszy serwer DNS na liście jest niedostępny, klient korzysta z tego serwera DNS, niezależnie od tego, czy serwer DNS działa prawidłowo lub nie. Aby zmienić kolejność serwera DNS dla sieci wirtualnych klienta, Usuń z listy serwerów DNS i dodaj je ponownie w kolejności klienta potrzebuje. Usługa DNS obsługuje aspekt dostępności Triada zabezpieczeń "CIA".

### <a name="azure-dns"></a>System DNS platformy Azure
[System nazw domen](https://technet.microsoft.com/library/bb629410.aspx), lub w systemie DNS jest odpowiedzialny za tłumaczenia (lub rozpoznawania) nazwę witryny sieci Web lub usługi na adres IP. [Usługa Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) jest Usługa hostingu domen DNS, rozpoznawania nazw przy użyciu infrastruktury Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure. Usługa DNS obsługuje aspekt dostępności Triada zabezpieczeń "CIA".
### <a name="log-analytics-nsgs"></a>Grupy NSG analizy dzienników
Grupy NSG można włączyć następujące kategorie dzienników diagnostycznych:
-   Zdarzenie: Zawiera wpisy, dla których NSG reguły są stosowane do maszyn wirtualnych i wystąpień ról na podstawie adresu MAC. Stan zasady te są gromadzone co 60 sekund.

-   Licznik reguł: zawiera wpisy dla ile razy każdej reguły NSG są stosowane do odmowy lub zezwolić na ruch.

### <a name="azure-security-center"></a>Azure Security Center
Centrum zabezpieczeń pomaga zapobiec, wykrywania i reagowania na zagrożenia, zapewnia się, że zwiększona wgląd w i skuteczniejszą kontrolę zabezpieczeń zasobów platformy Azure. Zapewnia zintegrowane zabezpieczenia monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających. Sieci Centrum zaleceń dotyczących zapór, grup zabezpieczeń sieci, konfigurowanie reguł ruchu przychodzącego i.

Zalecenia dotyczące sieci dostępne są następujące:

-   [Dodaj zaporę nowej generacji](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) zaleca dodanie dalej zapory generacji (NGFW) z partnerem firmy Microsoft, aby zwiększyć z ochrony zabezpieczeń

-   [Kierować ruchem tylko za pośrednictwem zapory nowej generacji](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only) zabezpieczeń (NSG) grupy reguł, które wymusić ruch przychodzący do maszyny Wirtualnej za pośrednictwem sieci NGFW sieci zaleca, które można skonfigurować.

-   [Włączenie grup zabezpieczeń sieci dla podsieci lub maszyn wirtualnych](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups) zaleca włączenie grup NSG dla podsieci lub maszyn wirtualnych.

-   [Ogranicz dostęp za pośrednictwem punktu końcowego internetowy](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints) zaleca, aby skonfigurować reguły ruchu przychodzącego dla grup NSG.


## <a name="compute"></a>Wystąpienia obliczeniowe

Sekcja ta zawiera dodatkowe informacje na temat kluczowych funkcji tych obszarów i podsumowanie informacji o tych funkcjach.

### <a name="antimalware--antivirus"></a>Ochrony przed złośliwym oprogramowaniem i wirusami
Z IaaS platformy Azure ochrony przed złośliwym oprogramowaniem z dostawcami zabezpieczeń, takich jak Microsoft, firmy Symantec, Trend Micro, McAfee i Kaspersky służy do ochrony maszyn wirtualnych z złośliwych plików, adware i innymi zagrożeniami. [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) dla usług Azure Cloud Services i maszyn wirtualnych jest możliwość ochrony, która pomaga w identyfikacji i usuwania wirusy, programy szpiegujące lub inne złośliwe oprogramowanie. Microsoft Antimalware udostępnia można skonfigurować alerty, gdy wiadomo, że złośliwego lub niechcianego oprogramowania próbuje się zainstalować lub uruchomić w systemie Azure. Microsoft Antimalware można także wdrożyć przy użyciu Centrum zabezpieczeń Azure

### <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń
Uwierzytelniania i szyfrowania nie zwiększyć poziom bezpieczeństwa, o ile nie są chronione samych kluczy. Można uprościć zarządzanie i bezpieczeństwo kluczy i kluczy tajnych krytyczne przez zapisanie ich w [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Magazyn kluczy udostępnia opcję, aby przechowywać klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane do FIPS 140-2 poziom 2 standardów. Klucze szyfrowania programu SQL Server do utworzenia kopii zapasowej lub [przezroczystego szyfrowania danych](https://msdn.microsoft.com/library/bb934049.aspx) wszystkie można przechowywać w magazynie kluczy z kluczy ani kluczy tajnych z aplikacji. Zarządzać uprawnień i dostępu do tych elementów chronionych za pomocą [usługi Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Kopia zapasowa maszyny wirtualnej
[Kopia zapasowa Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) jest rozwiązaniem, które chroni dane aplikacji z zero inwestycji kapitału i minimalne kosztów operacyjnych. Błędy aplikacji może spowodować uszkodzenie danych i błędów ludzkich można wprowadzać usterki do aplikacji, które mogą prowadzić do problemów z zabezpieczeniami. Kopia zapasowa Azure maszynach wirtualnych z systemem Windows i Linux są chronione.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Ważnym elementem organizacji [business continuity/odzyskiwania po awarii (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) strategii jest ustaleniem, jak zapewnić firmowych obciążeń i aplikacji do pracy podczas planowanych i nieplanowanych awarii. [Usługa Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) pomaga organizowania replikacji, trybu failover i odzyskiwania obciążeń i aplikacji, tak aby były dostępne z lokalizacji dodatkowej jeśli spadnie do lokalizacji głównej.

### <a name="sql-vm-tde"></a>FUNKCJI TDE MASZYNY WIRTUALNEJ SQL
[Przezroczystego szyfrowania danych (funkcji TDE)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) i szyfrowanie na poziomie kolumny (CLE) są funkcji szyfrowania serwera SQL. Ta forma szyfrowania wymaga klientów do przechowywania kluczy kryptograficznych używanego do szyfrowania i zarządzania nimi.

Usługa Azure Key Vault (AKV) zaprojektowano w celu poprawy zabezpieczeń i zarządzania tych kluczy w lokalizacji bezpieczne i wysokiej dostępności. Łącznik programu SQL Server umożliwia SQL Server do użycia tych kluczy z usługi Azure Key Vault.

Jeśli korzystasz z programu SQL Server z lokalnymi maszynami, istnieją kroki można wykonać, aby uzyskać dostęp do usługi Azure Key Vault z komputera lokalnego programu SQL Server. Jednak dla programu SQL Server na maszynach wirtualnych Azure, można oszczędzić czas, korzystając z funkcji integracji magazynu kluczy Azure. Kilka polecenia cmdlet programu Azure PowerShell do włączenia tej funkcji można zautomatyzować konfigurowanie niezbędne dla maszyny Wirtualnej SQL uzyskać dostępu do magazynu kluczy.

### <a name="vm-disk-encryption"></a>Szyfrowanie dysków maszyny Wirtualnej
[Szyfrowanie dysków Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) jest nowa funkcja, która ułatwia szyfrowania dysków maszyny wirtualnej systemu Windows i Linux IaaS. Ma to zastosowanie branży funkcje BitLocker standardowych systemu Windows i DM-Crypt systemu Linux w celu zapewnienia szyfrowania woluminów systemu operacyjnego i dysków z danymi. Rozwiązanie jest zintegrowany z usługą Azure Key Vault ułatwia kontrolowanie i zarządzanie nimi klucze szyfrowania dysku i kluczy tajnych w ramach subskrypcji usługi Key Vault. Rozwiązanie zapewnia również, że wszystkie dane na dyskach maszyny wirtualnej są szyfrowane, gdy w magazynie Azure.

### <a name="virtual-networking"></a>Sieć wirtualna
Maszyny wirtualne muszą łączność sieciową. W celu spełnienia tego wymagania, platforma Azure wymaga maszyn wirtualnych, które będą podłączone do sieci wirtualnej platformy Azure. Sieci wirtualnej platformy Azure jest konstrukcją logiczną, rozszerzający fizycznej Azure sieci szkieletowej. Każdy logicznej [sieci wirtualnej Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) jest odizolowana od wszystkich innych sieciach wirtualnych platformy Azure. Izolacja pomaga upewnić się, że ruch sieciowy we wdrożeniach nie jest dostępna dla innych klientów firmy Microsoft Azure.

### <a name="patch-updates"></a>Poprawek i aktualizacji
Poprawek i aktualizacji stanowić podstawę Znajdowanie i rozwiązywanie problemów i uprościć proces zarządzania aktualizacjami oprogramowania, zarówno dzięki zmniejszeniu liczby aktualizacji oprogramowania, które należy wdrożyć w przedsiębiorstwie, jak i przez odpowiednie zwiększenie możliwość monitorowania zgodności.

### <a name="security-policy-management-and-reporting"></a>Zarządzanie zasadami zabezpieczeń i raportowanie
[Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) pomaga zapobiec, wykrywania i odpowiadać na zagrożenia i udostępnia zwiększone wgląd w i skuteczniejszą kontrolę zabezpieczeń zasobów platformy Azure. Zapewnia zintegrowane zabezpieczenia monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

### <a name="azure-security-center"></a>Azure Security Center
Usługa Security Center ułatwia zapobieganie zagrożeniom, ich wykrywanie i reagowanie na nie, a przy tym zapewnia lepszy wgląd i większą kontrolę w zakresie bezpieczeństwa zasobów na platformie Azure. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań z zakresu zabezpieczeń.

## <a name="identify-and-access-management"></a>Identyfikowanie i zarządzanie dostępem

Zabezpieczanie systemów, aplikacji i danych rozpoczyna się od kontroli dostępu na podstawie tożsamości. Funkcje zarządzania tożsamościami i dostępem, które są wbudowane w usług i produktów firmy Microsoft chronić organizacyjne i osobiste informacje przed nieautoryzowanym dostępem podczas dostępnych do autoryzowanych użytkowników po każdej zmianie i co wszędzie tam, gdzie one potrzebny.

### <a name="secure-identity"></a>Zabezpieczanie tożsamości
Firma Microsoft używa wielu rozwiązania w zakresie zabezpieczeń i technologii w swoich produktów i usług do zarządzania tożsamościami i dostępem.
-   [Uwierzytelnianie wieloskładnikowe](https://azure.microsoft.com/services/multi-factor-authentication/) wymaga użytkownikom używanie wielu metod dostępu lokalnie i w chmurze. Zapewnia silne uwierzytelnianie za pomocą różnych opcji weryfikacji łatwe podczas Obsługa użytkownikom prosty proces logowania.

-   [Microsoft Authenticator](https://aka.ms/authenticator) zapewnia przyjazną dla użytkownika usługi Multi-Factor Authentication działa zarówno z usługi Microsoft Azure Active Directory i kont Microsoft, która obsługuje wearables i zatwierdzenia na podstawie linii papilarnych.

-   [Wymuszanie zasad haseł](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) zwiększa bezpieczeństwo hasła tradycyjnych poprzez nałożenie długość i złożoność, wymuszone okresowych rotacji i blokady konta po nieudanych prób uwierzytelnienia.

-   [Uwierzytelnianie na podstawie tokenu](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) umożliwia uwierzytelnianie za pomocą usługi Azure Active Directory.

-   [Kontrola dostępu oparta na rolach (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) umożliwia przyznanie dostępu na podstawie przypisanej roli użytkownika, ułatwiając Nadawanie użytkownikom tylko takiego dostępu, które są niezbędne do wykonywania ich obowiązków. RBAC można dostosować dla każdej organizacji modelu biznesowego i tolerancji ryzyka.

-   [Zintegrowane zarządzanie tożsamości (tożsamość hybrydowa)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) pozwala zachować kontrolę nad dostępem użytkowników na platformach wewnętrzny centrów danych i chmur, tworzenie tożsamością jednego użytkownika do uwierzytelniania i autoryzacji do wszystkich zasobów.

### <a name="secure-apps-and-data"></a>Zabezpieczanie aplikacji i danych
[Usługa Azure Active Directory](https://azure.microsoft.com/services/active-directory/), kompleksowe tożsamościami i dostępem rozwiązania do zarządzania chmury, zapewnia zabezpieczony dostęp do danych w aplikacjach w lokacji i w chmurze i ułatwia zarządzanie użytkownikami i grupami. Łączone podstawowych usług katalogowych, Zaawansowane zarządzanie tożsamościami, zabezpieczeń i zarządzania dostępem do aplikacji i ułatwia deweloperom tworzenie Zarządzanie tożsamościami oparte na zasadach w swoich aplikacjach. Aby ulepszyć usługi Azure Active Directory, możesz dodać płatnych funkcji przy użyciu wersji usługi Azure Active Directory — wersja podstawowa, Premium P1 i Premium P2.

| Wolny / typowe funkcje     | Podstawowe funkcje    |Funkcji Premium P1 |Funkcji Premium P2 | Usługa Azure Active Directory Join — Windows 10 tylko powiązane funkcje|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Obiekty katalogu](https://docs.microsoft.com/azure/active-directory/active-directory-editions#directory-objects), [użytkownika/grupy zarządzania (Dodaj/aktualizowania/usuwania) / oparta na użytkowniku inicjowania obsługi rejestracji urządzenia](https://docs.microsoft.com/azure/active-directory/active-directory-editions#usergroup-management-addupdatedelete-user-based-provisioning-device-registration), [pojedynczego logowania jednokrotnego (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#single-sign-on-sso), [samoobsługi hasła Zmiany dla użytkowników chmury](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-change-for-cloud-users), [Connect (aparatu synchronizacji, rozszerzający katalogów lokalnych do usługi Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory), [zabezpieczeń / Reports użycia](https://docs.microsoft.com/azure/active-directory/active-directory-editions#securityusage-reports)       |   [Zarządzanie dostępem na podstawie grupy / inicjowania obsługi administracyjnej](https://docs.microsoft.com/azure/active-directory/active-directory-editions#group-based-access-managementprovisioning), [samoobsługowego resetowania hasła dla użytkowników chmury](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-reset-for-cloud-users), [firmy Branding (Dostosowywanie panelu dostępu/stron logowania)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#company-branding-logon-pagesaccess-panel-customization), [ Serwer Proxy aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-editions#application-proxy), [SLA 99,9%](https://docs.microsoft.com/azure/active-directory/active-directory-editions#sla-999) |  [Samodzielne i aplikacji zarządzania/niezależne-eksploatacyjnych aplikacji dodatków/dynamicznie grup](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-group), [hasło samoobsługowego resetowania/Zmień/odblokowywanie z lokalnego zapisu](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-resetchangeunlock-with-on-premises-write-back), [Multi-Factor Uwierzytelnianie (chmury i lokalnych (serwer usługi MFA))](https://docs.microsoft.com/azure/active-directory/active-directory-editions#multi-factor-authentication-cloud-and-on-premises-mfa-server), [MIM CAL + serwera MIM](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mim-cal-mim-server), [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-editions#cloud-app-discovery), [Connect Health](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-health), [Przerzucania automatyczne hasło dla konta grupy](https://docs.microsoft.com/azure/active-directory/active-directory-editions#automatic-password-rollover-for-group-accounts)|   [Ochronę tożsamości](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|  [Dołączenie urządzenia do usługi Azure AD, logowanie Jednokrotne pulpitu, Microsoft Passport dla usługi Azure AD, odzyskiwania funkcji Bitlocker administratora](https://docs.microsoft.com/azure/active-directory/active-directory-editions#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery), [MDM autorejestrowania, odzyskiwanie Samoobsługowe funkcji Bitlocker, dodatkowe Administratorzy lokalni na urządzeniach z systemem Windows 10 za pomocą usługi Azure AD Dołącz](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mdm-auto-enrollment)|


- [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) jest funkcją premium usługi Azure Active Directory, który umożliwia identyfikowanie aplikacji w chmurze, które są używane przez pracowników w Twojej organizacji.

- [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) to usługa zabezpieczeń, która używa możliwości wykrywania anomalii w usłudze Azure Active Directory, aby zapewnić skonsolidowany wgląd w zdarzenia o podwyższonym ryzyku i potencjalnych lukach, które mogą wpłynąć na użytkownika tożsamości w organizacji.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) umożliwia dołączenie do domeny bez konieczności wdrażania kontrolerów domeny maszyny wirtualne platformy Azure. Użytkownicy, zaloguj się do tych maszyn wirtualnych za pomocą firmowych poświadczeń usługi Active Directory i może bezproblemowo uzyskiwać dostęp do zasobów.

- [Usługa Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) jest wysokiej dostępności, globalnych tożsamości usługi zarządzania dla aplikacji dla użytkownika, które można skalować setki milionów tożsamości i zintegrowanie różnych mobile i sieci web platformy. Klientów można zalogować się do wszystkich aplikacji za pomocą środowiska można dostosowywać, korzystających z istniejących kont społecznościowych lub można utworzyć nowe poświadczenia autonomicznych.

- [Azure współpracy B2B Active Directory](https://aka.ms/aad-b2b-collaboration) jest rozwiązaniem integracji bezpiecznego partnera, która obsługuje relacji między firmami, umożliwiając partnerom dostęp do danych i aplikacji firmy selektywnie przy użyciu ich samodzielnie zarządzanej tożsamości.

- [Azure Active Directory Join procesu](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) umożliwia rozszerzanie funkcji chmury na urządzeniach Windows 10 w celu scentralizowanego zarządzania. Umożliwia użytkownikom nawiązywanie połączenia z chmurą firmy lub organizacji za pomocą usługi Azure Active Directory, a ułatwiają dostęp do aplikacji i zasobów.

- [Azure Active Directory serwera Proxy aplikacji](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) udostępnia logowania jednokrotnego i bezpieczny dostęp zdalny dla aplikacji sieci web hostowane lokalnie.

## <a name="next-steps"></a>Następne kroki
- [Wprowadzenie do zabezpieczeń firmy Microsoft Azure](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Usługi i funkcje platformy Azure, które służą do zabezpieczania usług i danych w ramach systemu Azure

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

Zapobiegaj zagrożeniom, wykrywaj je i odpowiadaj na nie dzięki lepszemu wglądowi w zabezpieczenia zasobów platformy Azure i lepszej kontroli nad nimi

- [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

Możliwości monitorowania w Centrum zabezpieczeń Azure w celu monitorowania zgodności z zasadami.
