---
title: Najlepsze rozwiązania do ochrony zasobów — Microsoft Azure
description: Ten artykuł zawiera zestaw najlepsze rozwiązanie w zakresie ochrony danych, aplikacji i innych zasobów na platformie Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 0418d325f3b3719549181a48fc0432a677f695d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65795905"
---
# <a name="azure-operational-security-best-practices"></a>Najlepsze rozwiązania kwestii bezpieczeństwa platformy Azure
Ten artykuł zawiera zestaw najlepsze rozwiązanie w zakresie ochrony danych, aplikacji i innych zasobów na platformie Azure.

Najlepsze rozwiązania są oparte na konsensus opinii i pracować z aktualnymi możliwościami platformy Azure, a zestawy funkcji. Opinie i technologii zmieniają się wraz z upływem czasu, a w tym artykule jest aktualizowana w regularnych odstępach czasu, aby odzwierciedlać wprowadzone zmiany.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definiuj i wdrażaj rozwiązania silne bezpieczeństwo działania
Zabezpieczenia usługi Azure operational odnosi się do usługi, formanty i funkcje dostępne dla użytkowników na potrzeby ochrony danych, aplikacji i innych zasobów na platformie Azure. Bezpieczeństwa platformy Azure jest oparta na strukturę, która zawiera wiedzy uzyskanej dzięki możliwości, które są unikatowe dla firmy Microsoft, w tym [cykl projektowania zabezpieczeń (SDL)](https://www.microsoft.com/sdl), [firmy Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) program oraz głębokiej świadomości krajobraz zagrożeń cyberbezpieczeństwa.

## <a name="manage-and-monitor-user-passwords"></a>Zarządzanie i monitorowanie haseł użytkowników
W poniższej tabeli wymieniono niektóre najlepsze rozwiązania związane z zarządzaniem hasłami użytkowników:

**Najlepsze rozwiązanie**: Upewnij się, że aby zapewnić odpowiedni poziom ochrony haseł w chmurze.   
**Szczegóły**: Postępuj zgodnie ze wskazówkami w [wskazówki dotyczące hasła Microsoft](https://www.microsoft.com/research/publication/password-guidance/), który jest ograniczone do użytkowników platformy tożsamości firmy Microsoft (konto usługi Azure Active Directory, Active Directory i Microsoft).

**Najlepsze rozwiązanie**: Monitoruj podejrzane akcje powiązane z kontami użytkowników.   
**Szczegóły**: Monitor wykrywający sytuacje, [narażeni użytkownicy](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk) i [ryzykownych logowań](../active-directory/reports-monitoring/concept-risk-events.md) przy użyciu zabezpieczeń usługi Azure AD raportów.

**Najlepsze rozwiązanie**: Automatyczne wykrywanie i korygowanie hasła o wysokim ryzyku.   
**Szczegóły**: [Usługa Azure AD Identity Protection](../active-directory/identity-protection/overview.md) to funkcja wersji Azure AD Premium P2, która pozwala na:

- Wykrywanie potencjalnych luk w zabezpieczeniach wpływających na tożsamości w organizacji
- Skonfigurowanie automatycznych odpowiedzi na wykryte podejrzane działania, that are related to tożsamości w organizacji
- Badaj podejrzane zdarzenia i podjąć odpowiednie działania, aby je rozwiązać

## <a name="receive-incident-notifications-from-microsoft"></a>Otrzymywanie powiadomień zdarzenia z firmy Microsoft
Upewnij się, że zespołowi operacyjnemu zabezpieczeń otrzymuje powiadomienia zdarzeń usługi Azure firmy Microsoft. Umożliwia powiadomienie zdarzenia zespołem zabezpieczeń i o tym, że zostały naruszone zasobów platformy Azure, dzięki czemu mogą szybko reagować na i skoryguj potencjalne zagrożenia bezpieczeństwa.

W portalu rejestracji na platformie Azure upewnij się, że informacje kontaktowe administratora zawiera szczegółowe informacje, które powiadamiają o operacje zabezpieczeń. Informacje kontaktowe są e-mail adres i numer telefonu.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organizowanie subskrypcji platformy Azure do grup zarządzania
Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposób wydajne zarządzanie dostępem, zasad i zgodności dla tych subskrypcji. [Grupy zarządzania systemu Azure](../governance/management-groups/create.md) zapewniają poziom zakres, który jest powyżej subskrypcji. Organizowanie subskrypcji do kontenerów o nazwie grupy zarządzania i zastosować warunkach nadzoru do grup zarządzania. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do tej grupy zarządzania.

Można tworzyć elastyczne struktury grupy zarządzania i subskrypcji w katalogu. Każdy katalog jest podana przez pojedynczą najwyższego poziomu grupę zarządzania o nazwie głównej grupy zarządzania. Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Grupa zarządzania głównego umożliwia zasad globalnych i przypisania RBAC, które mają być stosowane na poziomie katalogu.

Poniżej przedstawiono najlepsze rozwiązania dotyczące korzystania z grup zarządzania:

**Najlepsze rozwiązanie**: Upewnij się, że nowe subskrypcje mają zastosowanie nadzoru elementów, takich jak zasady i uprawnienia dodanych.   
**Szczegóły**: Aby przypisać elementów zabezpieczeń całego przedsiębiorstwa, które są stosowane do wszystkich zasobów platformy Azure, użyj głównej grupy zarządzania. Zasady i uprawnienia są przykłady elementów.

**Najlepsze rozwiązanie**: Wyrównaj najwyższego poziomy grup zarządzania za pomocą segmentacji strategia zapewnia punkt kontroli i zasady sprawdzania spójności w ramach każdego segmentu.   
**Szczegóły**: Tworzenie pojedynczej grupy zarządzania dla każdego segmentu w głównej grupy zarządzania. Nie należy tworzyć innych grup zarządzania w katalogu głównym.

**Najlepsze rozwiązanie**: Ogranicz głębokość grupy zarządzania, aby uniknąć nieporozumień, który hamuje zarówno operacje, jak i zabezpieczeń.   
**Szczegóły**: Ograniczenia hierarchii do trzech poziomów, w tym katalogu głównego.

**Najlepsze rozwiązanie**: Starannie wybrać elementy, które dotyczą w całym przedsiębiorstwie za pomocą głównej grupy zarządzania.   
**Szczegóły**: Upewnij się, elementów grupy zarządzania głównego ma Wyczyść, należy zastosować na wszystkich zasobów i że są one mały wpływ.

Nadaje obejmują:

- Wymagania prawne, które mają wyraźne wpływ na działalność (na przykład ograniczenia związane z niezależność danych)
- Wymagania przy bliskich zeru potencjał ujemny wpływ na operacje, takie jak zasady przy użyciu efektu audytu lub RBAC przypisania uprawnień, które dokładnie sprawdzane

**Najlepsze rozwiązanie**: Należy dokładnie zaplanować i testowanie wszystkich zmian dla przedsiębiorstw w głównej grupie zarządzania przed zastosowaniem ich (zasady, modelu RBAC i tak dalej).   
**Szczegóły**: Zmiany w grupie zarządzania głównym może mieć wpływ na każdy zasób na platformie Azure. Gdy zapewniają wydajnym sposobem zapewnienia spójności w całym przedsiębiorstwie, błędy lub nieprawidłowe użycie może negatywnie wpłynąć na operacje w środowisku produkcyjnym. Testowanie wszystkich zmian do głównej grupy zarządzania w laboratorium testowym lub produkcyjnym pilotażu.

## <a name="streamline-environment-creation-with-blueprints"></a>Usprawnij tworzenie środowisk z plany
[Plany usługi Azure](../governance/blueprints/overview.md) usługa umożliwia architektów i chmury centralnej informacji o technologii grupy do zdefiniowania powtarzalne zestawu zasobów platformy Azure, które implementuje i standardami, wzorców i wymagania w organizacji jest zgodna. Plany platformy Azure umożliwia zespołom programistycznym utworzyć i wdrożyć nowe środowiska z zestawem wbudowane składniki i pewność, że tworzą tych środowisk, w ramach zgodności organizacyjnej.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorowanie usług magazynu nieoczekiwane zachowanie
Diagnozowanie i rozwiązywanie problemów w aplikacji rozproszonej hostowanej w środowisku chmury może być bardziej skomplikowane, niż w tradycyjnych środowiskach. Aplikacje można wdrożyć w infrastrukturze PaaS lub IaaS w środowisku lokalnym, na urządzeniu przenośnym lub w kombinacji tych środowisk. Ruch sieciowy aplikacji może przechodzić przez sieci publicznych i prywatnych, a aplikacja może używać wielu technologii magazynowania.

Należy stale monitorować usługi magazynu, których Twoja aplikacja używa nieoczekiwane zmiany w zachowaniu (na przykład czasy odpowiedzi wolniejsze). Użyj rejestrowania do zbierania bardziej szczegółowych danych i analizy problemu w głębi. Informacje diagnostyczne, którą można uzyskać od monitorowanie i rejestrowanie pomaga ustalić główną przyczynę problemu, który napotkano aplikacji. Następnie można rozwiązać ten problem i określić odpowiednie kroki w celu jego rozwiązania.

[Analizy usługi Azure Storage](../storage/storage-analytics.md) umożliwia rejestrowanie i dostarcza danych metryk dotyczących konta usługi Azure storage. Zaleca się użyć tych danych do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z konta magazynu.

## <a name="prevent-detect-and-respond-to-threats"></a>Zapobieganie, wykrywanie ich i reagowanie na zagrożenia
[Usługa Azure Security Center](../security-center/security-center-intro.md) pomaga zapobiegać zagrożeniom, wykrywać i reagowanie na zagrożenia, zapewniając lepszy wgląd w (i kontrolę nad) zabezpieczeń zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z różnymi rozwiązaniami zabezpieczeń.

Usługa Security Center w warstwie bezpłatna zapewnia ograniczone bezpieczeństwo jedynie zasobów platformy Azure. Warstwa standardowa rozszerza te możliwości na lokalnych i w innych chmurach. Standardowa usługi Security Center ułatwia znajdowanie i naprawianiu luk w zabezpieczeniach, zastosowanie kontroli dostępu i aplikacji do blokowania złośliwych działań, wykrywaniu zagrożeń przy użyciu analizy i analizy oraz szybko reagować podczas ataku. Standardową warstwę cenową usługi Security Center możesz wypróbować bezpłatnie przez pierwsze 60 dni. Zaleca się, że możesz [uaktualnić swoją subskrypcję platformy Azure do standardowej usługi Security Center](../security-center/security-center-get-started.md).

Umożliwia usłudze Security Center daje pełny widok stanu bezpieczeństwa wszystkich Twoich zasobów platformy Azure. Na pierwszy rzut oka Sprawdź, czy odpowiednie środki zabezpieczające są stosowane i poprawnie skonfigurowane i szybko rozpoznać zasoby wymagające uwagi.

Usługa Security Center integruje się również z [Windows Defender Advanced Threat Protection (ATP)](../security-center/security-center-wdatp.md), która udostępnia kompleksowe funkcje do punktu końcowego wykrywania i odpowiedzi (EDR). Dzięki integracji z usługą Windows Defender ATP można wykryć nieprawidłowości. Można również wykrywanie oraz reagowanie na zaawansowane ataki na punkty końcowe serwera monitorowane przez usługę Security Center.

Prawie wszystkie przedsiębiorstwa to organizacje mają zabezpieczeń informacjami i zdarzeniami (SIEM) system zarządzania ułatwiające identyfikację potencjalnych zagrożeń dzięki konsolidacji informacji dziennika z urządzeń zbieranie różnych sygnału. Dzienniki są następnie analizowane przez system analizy danych ułatwiają określenie, co to jest "interesujący" informacji istotnych od nieistotnych, który jest nieuniknione we wszystkich dzienników zbierania i rozwiązań do analizy.

[Azure wartownik](../sentinel/overview.md) jest skalowalne i natywnych dla chmury, informacjami i zdarzeniami Zarządzanie zabezpieczeń (SIEM) i rozwiązanie w zakresie zabezpieczeń aranżacji, automatyczne odpowiedzi (WZRASTAĆ). Wartownik platformy Azure udostępnia usługę intelligent security analizę zagrożeń za pomocą alertów wykrywania, widoczność zagrożeń, aktywne myślistwo i reagowania na zagrożenia automatycznych.

Poniżej przedstawiono najlepsze rozwiązania dotyczące zapobiegania, wykrywanie ich i reagowanie na zagrożenia:

**Najlepsze rozwiązanie**: Zwiększ szybkość i skalowalność rozwiązania SIEM przy użyciu rozwiązania SIEM oparte na chmurze.   
**Szczegóły**: Zbadaj funkcje i możliwości [Azure przez wartownika](../sentinel/overview.md) i porównaj je z funkcjami aktualnie jesteś przy użyciu lokalnej. Należy rozważyć przyjęcie przez wartownika platformy Azure, jeśli dana jednostka spełnia wymagania rozwiązania SIEM w Twojej organizacji.

**Najlepsze rozwiązanie**: Znajdź najpoważniejsze luk w zabezpieczeniach, dzięki czemu można określić priorytet badania.   
**Szczegóły**: Przegląd usługi [Azure wynik bezpiecznego](../security-center/security-center-secure-score.md) Aby wyświetlić zalecenia wynikające z zasad platformy Azure i inicjatyw wbudowane w usłudze Azure Security Center. Zalecenia te pomagają adres najważniejszych czynników ryzyka, takie jak aktualizacje zabezpieczeń, program endpoint protection, szyfrowanie, konfiguracji zabezpieczeń, brak zapory aplikacji sieci Web, połączonych z Internetem maszyn wirtualnych i wiele innych.

Bezpieczne wynik, który jest oparty na Centrum dla formantów Internet Security (CIS), umożliwia testu porównawczego zabezpieczeń platformy Azure w Twojej organizacji względem źródeł zewnętrznych. Walidacja zewnętrznych pomaga sprawdzania poprawności i wzbogacanie strategii zabezpieczeń Twojego zespołu.

**Najlepsze rozwiązanie**: Monitoruj stan zabezpieczeń maszyn, sieci, magazynu i usług danych i aplikacji do odnajdywania i ustalić ich priorytety potencjalnych problemów z zabezpieczeniami.  
**Szczegóły**: Postępuj zgodnie z [zalecenia dotyczące zabezpieczeń](../security-center/security-center-recommendations.md) podczas uruchamiania usługi Security Center, za pomocą elementów o najwyższym priorytecie.

**Najlepsze rozwiązanie**: Alertów usługi Security Center można zintegrować z informacjami i zdarzeniami (SIEM) rozwiązanie do zarządzania zabezpieczeniami.   
**Szczegóły**: Większość organizacji z rozwiązaniem SIEM używać go jako centralna clearinghouse alerty zabezpieczeń, które wymagają odpowiedzi analityka. Przetworzone zdarzenia generowane przez usługę Security Center są publikowane w dzienniku aktywności platformy Azure, jednym z dzienników, które są dostępne za pośrednictwem usługi Azure Monitor. Usługa Azure Monitor udostępnia skonsolidowany potoku na potrzeby routingu jakichkolwiek danych monitorowania do narzędzia SIEM. Zobacz [integracja rozwiązań zabezpieczeń w usłudze Security Center](../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) instrukcje. Jeśli używasz platformy Azure przez wartownika zobacz [Połącz usługi Azure Security Center](../sentinel/connect-azure-security-center.md).

**Najlepsze rozwiązanie**: Integrowanie usługi Azure dzienników za pomocą rozwiązania SIEM.   
**Szczegóły**: Użyj [usługi Azure Monitor umożliwia zbieranie danych i eksportowanie](../azure-monitor/overview.md#integrate-and-export-data). Tej praktyką jest krytyczne dotyczących włączania badania zdarzeń zabezpieczeń i przechowywania online dziennika jest ograniczone. Jeśli używasz platformy Azure przez wartownika zobacz [połączyć źródeł danych](../sentinel/connect-data-sources.md).

**Najlepsze rozwiązanie**: Przyspiesz swoje badania i myślistwo procesy i redukować liczbę fałszywych alarmów dzięki zintegrowaniu możliwości wykrywania punktu końcowego i odpowiedzi (EDR) do badania ataków.   
**Szczegóły**: [Włączanie integracji usługi Windows Defender ATP](../security-center/security-center-wdatp.md#enable-windows-defender-atp-integration) za pomocą zasad zabezpieczeń usługa Security Center. Należy rozważyć użycie platformy Azure przez wartownika myślistwo zagrożeń i reagowania na zdarzenia.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitorowanie monitorowania sieci opartych na scenariuszach end-to-end
Klienci kompilacji sieci end-to-end na platformie Azure, łącząc zasobów sieciowych, takich jak sieci wirtualnej usługi ExpressRoute, Application Gateway i moduły równoważenia obciążenia. Monitorowanie jest dostępne na każdym z zasobów sieciowych.

[Usługa Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) to usługa regionalna. Jego narzędzia do diagnostyki i wizualizacji umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci w, do i z platformy Azure.

Poniżej przedstawiono najlepsze rozwiązania dotyczące narzędzia do monitorowania i dostępnych sieci.

**Najlepsze rozwiązanie**: Automatyzowanie zdalnego monitorowania sieci przez Przechwytywanie pakietów.  
**Szczegóły**: Monitorowanie i diagnozowanie problemów z siecią bez konieczności logowania się do maszyn wirtualnych przy użyciu usługi Network Watcher. Wyzwalacz [przechwytywania pakietów](../network-watcher/network-watcher-alert-triggered-packet-capture.md) przez ustawianie alertów i uzyskać dostęp do informacji o wydajności w czasie rzeczywistym na poziomie pakietów. Możesz szczegółowo analizować problemy w celu lepszego ich diagnozowania.

**Najlepsze rozwiązanie**: Uzyskiwanie szczegółowych informacji za pomocą dzienników przepływu ruchu sieciowego.  
**Szczegóły**: Tworzenie wzorców ruchu lepiej zrozumieć sieci za pomocą [dzienników przepływu grupy zabezpieczeń sieci](../network-watcher/network-watcher-nsg-flow-logging-overview.md). Informacje w dziennikach przepływu ułatwiają gromadzenie danych pod kątem zgodności, inspekcji i monitorowania profilu zabezpieczeń sieci.

**Najlepsze rozwiązanie**: Diagnozowanie problemów z połączeniem sieci VPN.  
**Szczegóły**: Usługa Network Watcher na [diagnozowanie najczęściej spotykanych problemów bramy sieci VPN i połączenia](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Można nie tylko zidentyfikować problem, ale również użyć szczegółowych dzienników w celu dalszego zbadania problemu.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Zabezpieczanie wdrożenia przy użyciu sprawdzonych narzędzi DevOps
Użyj następujące najlepsze rozwiązania metodyki DevOps, aby upewnić się, że przedsiębiorstwa i zespoły są wydajnych i efektywnych.

**Najlepsze rozwiązanie**: Automatyzuj kompilowanie i wdrażanie usług.  
**Szczegóły**: [Infrastruktura jako kod](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) to zestaw metod i praktyk, które ułatwiają specjalistom IT Usuń obciążeń związanych z typowymi kompilacji i zarządzania infrastrukturą moduły. Umożliwia on informatykom do tworzenia i utrzymywania ich środowiska nowoczesnych serwera w sposób przypominający jak deweloperom oprogramowania tworzenie i zarządzanie nimi kodu aplikacji.

Możesz użyć [usługi Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) można inicjować obsługę aplikacji za pomocą szablonu deklaratywnego. Pojedynczy szablon umożliwia wdrożenie wielu usług wraz z ich zależnościami. Używasz tego samego szablonu można wdrażać aplikację na każdym etapie cyklu życia aplikacji.

**Najlepsze rozwiązanie**: Automatycznie Utwórz i wdróż do aplikacji sieci web platformy Azure lub usług w chmurze.  
**Szczegóły**: Można skonfigurować swoje projekty DevOps platformy Azure, aby [automatycznie twórz i wdrażaj](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) do aplikacji sieci web platformy Azure lub usług w chmurze. DevOps platformy Azure automatycznie wdraża pliki binarne po wykonaniu tej kompilacji na platformie Azure po każdym zaewidencjonowaniu kodu. Proces kompilacji pakietu jest odpowiednikiem polecenia pakietów w programie Visual Studio i kroki publikowania są równoważne polecenia Opublikuj w programie Visual Studio.

**Najlepsze rozwiązanie**: Zautomatyzuj program release management.  
**Szczegóły**: [Potoki usługi Azure](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) to rozwiązanie do automatyzacji wielu etap wdrażania i zarządzania nimi procesu tworzenia wersji. Twórz potoki zarządzanego ciągłe wdrażanie do wydania szybko, łatwo i często. W przypadku potoków usługi Azure można zautomatyzować proces tworzenia wydań i można wstępnie zdefiniowanych przepływów pracy zatwierdzania. Wdrażanie lokalnej do chmury, należy rozszerzyć i dostosować zgodnie z potrzebami.

**Najlepsze rozwiązanie**: Sprawdź wydajność Twojej aplikacji przed jej uruchamiania lub wdrażania aktualizacji w środowisku produkcyjnym.  
**Szczegóły**: Uruchom oparte na chmurze [testy obciążeniowe](/azure/devops/test/load-test/overview#alternatives) do:

- Znajdowanie problemów z wydajnością w aplikacji.
- Poprawić jakość wdrożenia.
- Upewnij się, że aplikacja jest zawsze dostępny.
- Upewnij się, że aplikacja może obsługiwać ruch na potrzeby następnego uruchomienia lub marketing kampanię z użyciem.

[Apache JMeter](https://jmeter.apache.org/) to narzędzie bezpłatne, popularne typu open source społeczności silne kopii.

**Najlepsze rozwiązanie**: Monitorowanie wydajności aplikacji.  
**Szczegóły**: [Usługa Azure Application Insights](../azure-monitor/app/app-insights-overview.md) to usługa zarządzania (APM) aplikacji rozszerzalnej wydajności dla deweloperów sieci web na wielu platformach. Usługa Application Insights umożliwia monitorowanie aplikacji sieci web na żywo. Funkcja automatycznie wykrywa anomalie wydajność. Obejmuje narzędzia analityczne, dzięki którym możesz diagnozować problemy i zrozumieć, jak użytkownicy w rzeczywistości korzystają z aplikacją. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań.

## <a name="mitigate-and-protect-against-ddos"></a>Eliminowanie i chronić przed atakami DDoS
Rozproszona odmowa usługi (DDoS) jest typem ataku, który próbuje wyczerpaniu zasobów aplikacji. Celem jest wpływ na dostępność aplikacji i jego zdolność do obsługi żądań uzasadnione. Te ataki stają się coraz bardziej złożone i większy rozmiar i wpływu. Mogą one być celem dowolnego punktu końcowego, który jest publicznie dostępny za pośrednictwem Internetu.

Projektowanie i tworzenie aplikacji dla ochrony przed atakami DDoS wymaga planowania i projektowania dla różnych trybów awarii. Poniżej przedstawiono najlepsze rozwiązania dotyczące tworzenia odpornych przed atakami DDoS usług na platformie Azure.

**Najlepsze rozwiązanie**: Upewnij się, że bezpieczeństwo to priorytet w całym cyklu życia aplikacji, od projektowania i implementacji, wdrażania i operacji. Aplikacje mogą mieć usterki, które umożliwiają stosunkowo małą liczbą żądań używa dużej ilości zasobów, co powoduje przerwa w działaniu usługi.  
**Szczegóły**: Aby zabezpieczyć usługi uruchomionej na Microsoft Azure, należy dysponować dobrą znajomością architektury aplikacji i skoncentrować się na [pięciu filarów jakości oprogramowania](https://docs.microsoft.com/azure/architecture/guide/pillars). Typowe ruchu woluminów, należy wiedzieć modelu łączności między aplikacją i innymi aplikacjami i punktów końcowych usługi, które są dostępne do publicznego Internetu.

Zapewnia, że aplikacja jest odporność obsługi typu "odmowa usługi, która jest przeznaczona dla aplikacji" jest dla Ciebie najważniejsza. Bezpieczeństwo i ochrona prywatności są wbudowane w platformy Azure, począwszy od [cykl projektowania zabezpieczeń (SDL)](https://www.microsoft.com/sdl). Proces SDL, zapewnia bezpieczeństwo na każdym etapie programowania i gwarantuje, że Azure jest stale aktualizowana się jeszcze bardziej bezpieczne.

**Najlepsze rozwiązanie**: Projektowanie aplikacji [skalowanie w poziomie](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) do spełnienia określonych wymagań obciążenia namnożonego, szczególnie w przypadku ataków DDoS. Jeśli aplikacja jest zależna od pojedynczego wystąpienia usługi, tworzy pojedynczy punkt awarii. Inicjowanie obsługi administracyjnej wielu wystąpień sprawia, że system bardziej skalowalny i bardziej odporne na błędy.  
**Szczegóły**: Aby uzyskać [usługi Azure App Service](../app-service/app-service-value-prop-what-is.md), wybierz pozycję [planu usługi App Service](../app-service/overview-hosting-plans.md) oferująca wiele wystąpień.

W przypadku usług Azure Cloud Services skonfigurować każdej z ról w taki sposób, aby użyć [wiele wystąpień](../cloud-services/cloud-services-choose-me.md).

Aby uzyskać [maszyn wirtualnych platformy Azure](../virtual-machines/windows/overview.md), upewnij się, że architektury maszyny Wirtualnej zawiera więcej niż jednej maszyny Wirtualnej i że każda maszyna wirtualna znajduje się w [zestaw dostępności](../virtual-machines/virtual-machines-windows-manage-availability.md). Firma Microsoft zaleca zestawów za pomocą skalowania maszyn wirtualnych dla funkcji skalowania automatycznego.

**Najlepsze rozwiązanie**: Układanie warstwowo poziom ochrony w aplikacji zmniejsza prawdopodobieństwo udanego ataku. Implementowanie bezpiecznej projekty dla aplikacji za pomocą wbudowanych możliwości platformy Azure.  
**Szczegóły**: Zwiększa ryzyko ataku o rozmiarze (prawdopodobieństwo) aplikacji. Można zmniejszyć obszar powierzchni przy użyciu listy dozwolonych zamknięcia w narażonych przestrzeń adresów IP i nasłuchiwać portów, które nie są wymagane na usług równoważenia obciążenia ([usługi Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) i [usługi Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)).

[Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) to inny sposób, aby zmniejszyć obszar narażony na ataki. Możesz użyć [tagów usług](../virtual-network/security-overview.md#service-tags) i [grupy zabezpieczeń aplikacji](../virtual-network/security-overview.md#application-security-groups) aby zminimalizować złożoność tworzenia reguły zabezpieczeń i konfigurowania zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji.

Należy wdrożyć usług platformy Azure w [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) zawsze, gdy jest to możliwe. To rozwiązanie umożliwia zasobów usługi do komunikacji przy użyciu prywatnych adresów IP. Domyślnie ruch usługi platformy Azure z sieci wirtualnej używa publicznych adresów IP jako źródłowych adresów IP.

Za pomocą [punkty końcowe usługi](../virtual-network/virtual-network-service-endpoints-overview.md) przełączników usługi ruchu na używanie prywatnych adresów sieci wirtualnej jako źródłowych adresów IP w przypadku ich uzyskujesz dostęp do usługi platformy Azure z sieci wirtualnej.

Widzimy często klientów lokalnych zasobów wprowadzenie zaatakowane wraz z ich zasobów na platformie Azure. Jeśli łączysz się w środowisku lokalnym, na platformie Azure, należy zminimalizować narażenie zasobów lokalnych do publicznej sieci internet.

Platforma Azure ma dwa przed atakami DDoS [oferty usług](../virtual-network/ddos-protection-overview.md) zapewniające ochronę przed atakami sieciowymi:

- Podstawowa ochrona jest zintegrowana z platformą Azure domyślnie bez ponoszenia dodatkowych kosztów. Skalowanie i pojemność wdrożonej globalnie sieci platformy Azure zapewnia obrony przed typowymi atakami warstwy sieci poprzez ograniczenie monitorowania i w czasie rzeczywistym zawsze w ruchu. Podstawowa nie wymaga żadnych zmian konfiguracji lub aplikacji użytkownika, i pomagają w ochronie wszystkich usług platformy Azure, w tym usług PaaS, takich jak usługi Azure DNS.
- Standardowa protection oferuje zaawansowane możliwości zapobiegania atakom DDoS przed atakami z sieci. Jest on automatycznie dostosowany do ochrony określonych zasobów platformy Azure. Ochrona jest proste włączyć podczas tworzenia sieci wirtualnych. Jego można również wykonać po utworzeniu i nie wymaga żadnych zmian w aplikacji lub zasobu.

## <a name="enable-azure-policy"></a>Włączanie usługi Azure Policy
[Usługa Azure Policy](../governance/policy/overview.md) usługa na platformie Azure, która umożliwia tworzenie i przypisywanie oraz zarządzanie zasadami. Te zasady wymuszają zasady i wpływ dotyczące zasobów, dzięki czemu zasoby te pozostają zgodne ze standardami firmy i umów dotyczących poziomu usług. Usługa Azure Policy spełnia to wymaganie, oceniając zasoby pod kątem niezgodności z przypisanymi zasadami.

Włączanie usługi Azure Policy, monitorowanie i wymuszanie zasad w organizacji. Pozwoli to zagwarantować zgodność z firmowymi lub prawnymi wymaganiami dotyczącymi zabezpieczeń przez centralne zarządzanie zasadami zabezpieczeń w obciążeniach chmury hybrydowej. Dowiedz się, jak [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](../governance/policy/tutorials/create-and-manage.md). Zobacz [struktura definicji usługi Azure Policy](../governance/policy/concepts/definition-structure.md) Przegląd elementy zasad.

Poniżej przedstawiono niektóre najlepsze rozwiązania do wykonania po przyjęcie zasad platformy Azure:

**Najlepsze rozwiązanie**: Zasady obsługuje kilka typów efektów. Informacje o nich w [struktura definicji usługi Azure Policy](../governance/policy/concepts/definition-structure.md#policy-rule). Operacje biznesowe mogą mieć negatywny wpływ **Odmów** efektu i **korygowanie** efektu, więc rozpoczynać **inspekcji** efekt, aby ograniczyć ryzyko negatywnego wpływu z zasady.   
**Szczegóły**: [Rozpocznij wdrażanie zasad w trybie inspekcji](../governance/policy/concepts/definition-structure.md#policy-rule) i następnie później postępu do **Odmów** lub **korygowanie**. Testowanie i przejrzeć wyniki efektu audytu, przed przejściem do **Odmów** lub **korygowanie**.

Aby uzyskać więcej informacji, zobacz [Tworzenie zasad w celu wymuszania zgodności i zarządzania nimi](../governance/policy/tutorials/create-and-manage.md).

**Najlepsze rozwiązanie**: Zidentyfikuj odpowiedzialny za monitorowanie pod kątem naruszeń zasad i zapewnienie, że szybko zostanie podjęta Akcja korygowania odpowiednie role.   
**Szczegóły**: Ma przypisaną rolę monitorowanie zgodności za pośrednictwem [witryny Azure portal](../governance/policy/how-to/get-compliance-data.md#portal) lub za pośrednictwem [wiersza polecenia](../governance/policy/how-to/get-compliance-data.md#command-line).

**Najlepsze rozwiązanie**: Usługa Azure Policy jest techniczne reprezentacja zasady napisane w organizacji. Wszystkie zasady usługi Azure są mapowane na zasady organizacyjne pomyłek i poprawia spójność.   
**Szczegóły**: Mapowanie dokumentu w dokumentacji danej organizacji lub usługa Azure policy, samego, dodając odwołanie do zasad organizacji na platformie Azure [opis zasad](../governance/policy/concepts/definition-structure.md#display-name-and-description) lub usługa Azure policy [inicjatywy](../governance/policy/concepts/definition-structure.md#initiatives) Opis.

## <a name="monitor-azure-ad-risk-reports"></a>Raporty o podwyższonym ryzyku monitora usługi Azure AD
Większość naruszenia zabezpieczeń ma miejsce, gdy osoby atakujące uzyskują dostęp do środowiska, kradzież tożsamości użytkownika. Wykrywanie tożsamości ze złamanymi zabezpieczeniami jest nie łatwym zadaniem. Usługa Azure AD używa adaptacyjnych algorytmów uczenia maszynowego i algorytmy heurystyczne wykryć podejrzane akcje, które są powiązane z kontami użytkowników. Każdej wykrytej podejrzanych działań są przechowywane w rekord nazywany [zdarzenie o podwyższonym ryzyku](../active-directory/reports-monitoring/concept-risk-events.md). Zdarzenia o podwyższonym ryzyku są rejestrowane w zabezpieczeń usługi Azure AD raportów. Aby uzyskać więcej informacji, przeczytaj o [raport zabezpieczeń dotyczący narażonych użytkowników](../active-directory/reports-monitoring/concept-user-at-risk.md) i [raport zabezpieczeń dotyczący ryzykownych logowań](../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Kolejne kroki
Zobacz [zabezpieczeń platformy Azure najlepsze rozwiązania i wzorce](security-best-practices-and-patterns.md) dla więcej najważniejsze wskazówki dotyczące zabezpieczeń do użycia podczas one projektowanie, wdrażanie i zarządzanie rozwiązań w chmurze, korzystając z platformy Azure.

Do dyspozycji więcej ogólnych informacji na temat zabezpieczeń platformy Azure i powiązane usługi firmy Microsoft są następujące zasoby:
* [Blog zespołu usługi Azure Security](https://blogs.msdn.microsoft.com/azuresecurity/) — aktualne instrukcje dotyczące najnowszych zabezpieczeń platformy Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — w przypadku, gdy luk w zabezpieczeniach firmy Microsoft, w tym problemów z platformą Azure, mogą zostać zgłoszone lub za pośrednictwem poczty e-mail do secure@microsoft.com
