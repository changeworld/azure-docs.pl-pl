---
title: Najlepsze rozwiązania kwestii bezpieczeństwa platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw najlepszych rozwiązań dla bezpieczeństwa platformy Azure.
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
ms.date: 09/20/2018
ms.author: terrylan
ms.openlocfilehash: e2678eb7d75921f43a1e51b6a8cefc9925a9adc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587023"
---
# <a name="azure-operational-security-best-practices"></a>Najlepsze rozwiązania kwestii bezpieczeństwa platformy Azure
Zabezpieczenia usługi Azure operational odnosi się do usługi, formanty i funkcje dostępne dla użytkowników na potrzeby ochrony danych, aplikacji i innych zasobów na platformie Azure. Bezpieczeństwa platformy Azure jest oparta na strukturę, która zawiera wiedzy uzyskanej dzięki możliwości, które są unikatowe dla firmy Microsoft, w tym [cykl projektowania zabezpieczeń (SDL)](https://www.microsoft.com/sdl), [firmy Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) program oraz głębokiej świadomości krajobraz zagrożeń cyberbezpieczeństwa.

W tym artykule omawiane jest kolekcja najlepszych rozwiązań dotyczących zabezpieczeń. Następujące najlepsze rozwiązania są uzyskiwane z naszych doświadczeń z usługą Azure database security i procesy, przez klientów, takie jak samodzielnie.

Dla każdego najlepszym rozwiązaniem jest Wyjaśnijmy:
-   Co to jest najlepsze rozwiązanie
-   Dlaczego chcesz włączyć na tym najlepszym rozwiązaniem jest
-   W przypadku awarii umożliwiające najlepszym rozwiązaniem, co może być skutkiem
- Jak można dowiesz się umożliwić najlepszym rozwiązaniem jest

W tym artykule Azure Operational Security Best Practices opiera się na opinii consensus i funkcji platformy Azure i zestawy funkcji występujących w czasie, który został zapisany w tym artykule. Opinie i technologii zmieniają się wraz z upływem czasu, a w tym artykule zostanie zaktualizowana w regularnych odstępach czasu, aby odzwierciedlać wprowadzone zmiany.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorowanie usług magazynu nieoczekiwane zachowanie
Diagnozowanie i rozwiązywanie problemów w aplikacji rozproszonej hostowanej w środowisku chmury może być bardziej skomplikowane, niż w tradycyjnych środowiskach. Aplikacje można wdrożyć w infrastrukturze PaaS lub IaaS w środowisku lokalnym, na urządzeniu przenośnym lub w kombinacji tych środowisk. Ruch sieciowy aplikacji może przechodzić przez sieci publicznych i prywatnych, a aplikacja może używać wielu technologii magazynowania.

Należy stale monitorować usługi magazynu, których Twoja aplikacja używa nieoczekiwane zmiany w zachowaniu (na przykład czasy odpowiedzi wolniejsze). Użyj rejestrowania do zbierania bardziej szczegółowych danych i analizy problemu w głębi. Informacje diagnostyczne, którą można uzyskać od monitorowanie i rejestrowanie pomaga ustalić główną przyczynę problemu, który napotkano aplikacji. Następnie można rozwiązać ten problem i określić odpowiednie kroki w celu jego rozwiązania.

[Analizy usługi Azure Storage](../storage/storage-analytics.md) umożliwia rejestrowanie i dostarcza danych metryk dotyczących konta usługi Azure storage. Zaleca się użyć tych danych do śledzenia żądań, analizy tendencji użycia oraz diagnozowania problemów z konta magazynu.

## <a name="prevent-detect-and-respond-to-threats"></a>Zapobieganie, wykrywanie ich i reagowanie na zagrożenia
[Usługa Azure Security Center](../security-center/security-center-intro.md) pomaga zapobiegać zagrożeniom, wykrywać i odpowiadanie na nie dzięki lepszemu wglądowi w (i kontrolę nad) zabezpieczeń zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z różnymi rozwiązaniami zabezpieczeń.

Security Center w warstwie bezpłatna zapewnia ograniczone bezpieczeństwo jedynie zasobów platformy Azure. Warstwa standardowa rozszerza te możliwości na lokalnych i w innych chmurach. Usługa Security Center w warstwie Standardowa pomaga w wyszukiwaniu i naprawianiu luk w zabezpieczeniach, stosowaniu kontroli dostępu i aplikacji w celu blokowania złośliwych działań, wykrywaniu zagrożeń przy użyciu analizy oraz szybkim reagowaniu podczas ataku. Standardową warstwę cenową usługi Security Center możesz wypróbować bezpłatnie przez pierwsze 60 dni. Zaleca się, że możesz [dołączanie subskrypcji platformy Azure do standardowej usługi Security Center](../security-center/security-center-get-started.md).

Umożliwia usłudze Security Center daje pełny widok stanu bezpieczeństwa wszystkich Twoich zasobów platformy Azure. Na pierwszy rzut oka Sprawdź, czy odpowiednie środki zabezpieczające są stosowane i poprawnie skonfigurowane i szybko rozpoznać zasoby wymagające uwagi.

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
**Szczegóły**: Możesz użyć potoki usługi Azure, aby [automatycznie twórz i wdrażaj](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts) do aplikacji sieci web platformy Azure lub usług w chmurze. Potoki usługi Azure automatycznie wdraża pliki binarne po wykonaniu tej kompilacji na platformie Azure po każdym zaewidencjonowaniu kodu. Proces kompilacji pakietu jest odpowiednikiem polecenia pakietów w programie Visual Studio i kroki publikowania są równoważne polecenia Opublikuj w programie Visual Studio.

**Najlepsze rozwiązanie**: Użyj ciągłego wdrażania.  
**Szczegóły**: [Potoki usługi Azure](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts) to rozwiązanie do automatyzacji wielu etap wdrażania i zarządzania nimi procesu tworzenia wersji. Twórz potoki zarządzanego ciągłe wdrażanie do wydania szybko, łatwo i często. W przypadku potoków usługi Azure można zautomatyzować proces tworzenia wydań i można wstępnie zdefiniowanych przepływów pracy zatwierdzania. Wdrażanie lokalnej do chmury, należy rozszerzyć i dostosować zgodnie z potrzebami.

**Najlepsze rozwiązanie**: Sprawdź wydajność Twojej aplikacji przed jej uruchamiania lub wdrażania aktualizacji w środowisku produkcyjnym.  
**Szczegóły**: Uruchom oparte na chmurze [testy obciążeniowe](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) przy użyciu platformy Azure do planów testów:

- Znajdowanie problemów z wydajnością w aplikacji.
- Poprawić jakość wdrożenia.
- Upewnij się, że aplikacja jest zawsze dostępny.
- Upewnij się, że aplikacja może obsługiwać ruch na potrzeby następnego uruchomienia lub marketing kampanię z użyciem.

**Najlepsze rozwiązanie**: Monitorowanie wydajności aplikacji.  
**Szczegóły**: [Usługa Azure Application Insights](../azure-monitor/app/app-insights-overview.md) to usługa zarządzania (APM) aplikacji rozszerzalnej wydajności dla deweloperów sieci web na wielu platformach. Usługa Application Insights umożliwia monitorowanie aplikacji sieci web na żywo. Funkcja automatycznie wykrywa anomalie wydajność. Obejmuje narzędzia analityczne, dzięki którym możesz diagnozować problemy i zrozumieć, jak użytkownicy w rzeczywistości korzystają z aplikacją. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań.

## <a name="mitigate-and-protect-against-ddos"></a>Eliminowanie i chronić przed atakami DDoS
Rozproszona odmowa usługi (DDoS) jest typem ataku, który próbuje wyczerpaniu zasobów aplikacji. Celem jest wpływ na dostępność aplikacji i jego zdolność do obsługi żądań uzasadnione. Te ataki stają się coraz bardziej złożone i większy rozmiar i wpływu. Mogą one być celem dowolnego punktu końcowego, który jest publicznie dostępny za pośrednictwem Internetu.

Projektowanie i tworzenie aplikacji dla ochrony przed atakami DDoS wymaga planowania i projektowania dla różnych trybów awarii.

Poniżej przedstawiono najlepsze rozwiązania dotyczące tworzenia odpornych przed atakami DDoS usług na platformie Azure.

**Najlepsze rozwiązanie**: Upewnij się, że bezpieczeństwo to priorytet w całym cyklu życia aplikacji, od projektowania i implementacji, wdrażania i operacji. Aplikacje mogą mieć usterki, które umożliwiają stosunkowo małą liczbą żądań używa dużej ilości zasobów, co powoduje przerwa w działaniu usługi.  
**Szczegóły**: Aby zabezpieczyć usługi uruchomionej na Microsoft Azure, należy dysponować dobrą znajomością architektury aplikacji i skoncentrować się na [pięciu filarów jakości oprogramowania](https://docs.microsoft.com/azure/architecture/guide/pillars). Typowe ruchu woluminów, należy wiedzieć modelu łączności między aplikacją i innymi aplikacjami i punktów końcowych usługi, które są dostępne do publicznego Internetu.

Zapewnia, że aplikacja jest odporność obsługi typu "odmowa usługi, która jest przeznaczona dla aplikacji" jest dla Ciebie najważniejsza. Bezpieczeństwo i ochrona prywatności są wbudowane w platformy Azure, począwszy od [cykl projektowania zabezpieczeń (SDL)](https://www.microsoft.com/en-us/sdl). Proces SDL, zapewnia bezpieczeństwo na każdym etapie programowania i gwarantuje, że Azure jest stale aktualizowana się jeszcze bardziej bezpieczne.

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

## <a name="next-steps"></a>Kolejne kroki
Zobacz [zabezpieczeń platformy Azure najlepsze rozwiązania i wzorce](security-best-practices-and-patterns.md) dla więcej najważniejsze wskazówki dotyczące zabezpieczeń do użycia podczas one projektowanie, wdrażanie i zarządzanie rozwiązań w chmurze, korzystając z platformy Azure.

Do dyspozycji więcej ogólnych informacji na temat zabezpieczeń platformy Azure i powiązane usługi firmy Microsoft są następujące zasoby:
* [Blog zespołu usługi Azure Security](https://blogs.msdn.microsoft.com/azuresecurity/) — aktualne instrukcje dotyczące najnowszych zabezpieczeń platformy Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — w przypadku, gdy luk w zabezpieczeniach firmy Microsoft, w tym problemów z platformą Azure, mogą zostać zgłoszone lub za pośrednictwem poczty e-mail do secure@microsoft.com
