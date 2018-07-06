---
title: Usług integracji platformy Azure — integracja w przedsiębiorstwie prosty
description: Architektura referencyjna pokazujący sposób implementacji wzorca integracji przedsiębiorstw prosty, za pomocą usługi Azure Logic Apps i usługi Azure API Management
author: mattfarm
manager: jonfan
editor: ''
services: logic-apps api-management
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/15/2018
ms.author: LADocs; estfan
ms.openlocfilehash: df86ca5aed405ab5eda05c1dd207f0b6656bfd96
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860201"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>Proste integracja dla przedsiębiorstw — architektury referencyjnej

## <a name="overview"></a>Przegląd

Ta architektura referencyjna pokazuje zestaw sprawdzonych rozwiązań dotyczących integracji aplikacji, która korzysta z usług integracji platformy Azure. Ta architektura może służyć jako tej podstawie wiele wzorców innej aplikacji wymagających interfejsów API protokołu HTTP, przepływu pracy i aranżacji.

*Istnieje wiele możliwych aplikacji technologii integracji z prostego punktu do punktu aplikacji do usługi Service bus pełną enterprise. W tej serii architektury określa części komponentów wielokrotnego użytku, które mogą mieć zastosowanie do tworzenia aplikacji ogólnego integracji — architektów należy wziąć pod uwagę określonych składników, należy zaimplementować dla swoich aplikacji i infrastruktury.*

   ![Diagram architektury — integracja w przedsiębiorstwie prosty](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>Architektura

Architektura zawiera następujące składniki:

- Grupa zasobów. [Grupa zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) to kontener logiczny dla zasobów platformy Azure.
- Usługa Azure API Management. [Usługa Azure API Management](https://docs.microsoft.com/azure/api-management/) to w pełni zarządzana platforma publikowanie, zabezpieczanie i przekształcanie interfejsów API protokołu HTTP.
- Portalu dla deweloperów programu usługi Azure API Management. Każde wystąpienie usługi Azure API Management zawiera [portalu dla deweloperów](https://docs.microsoft.com/azure/api-management/api-management-customize-styles), zapewniając dostęp do dokumentacji, przykładów kodu i możliwość testowania interfejsu API.
- Usługi Azure Logic Apps. [Usługa Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) bezserwerowa platforma do tworzenia przepływów pracy przedsiębiorstwa i integracji.
- Łączniki. [Łączniki](https://docs.microsoft.com/azure/connectors/apis-list) są używane przez usługę Logic Apps, połączyć się z często używanych usług. Usługa Logic Apps jest już setki różne łączniki, ale ich można również utworzyć za pomocą łącznika niestandardowego.
- Adres IP. Usługa Azure API Management ma stały publicznego [adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) i nazwy domeny. Nazwa domeny jest poddomeną azure-api.net, takich jak contoso.azure-api.net. Logic Apps i Service Bus również mieć publiczny adres IP; Jednak w ramach tej architektury będziemy ograniczać przydziału obowiązków dostępu wywołania punktów końcowych aplikacji logiki, aby tylko Zarządzanie adresami IP dla interfejsu API (zabezpieczenia). Wywołania do usługi Service Bus są chronione przez sygnaturę dostępu współdzielonego.
- Usługa DNS platformy Azure. [Usługa Azure DNS](https://docs.microsoft.com/azure/dns/) to usługa hostingowa przeznaczona dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure. Aby używać niestandardowej nazwy domeny (np. contoso.com), Utwórz rekordy DNS mapujące niestandardową nazwę domeny na adres IP. Aby uzyskać więcej informacji zobacz Konfigurowanie niestandardowej nazwy domeny w usłudze Azure API Management.
- Azure Active Directory (Azure AD). Użyj [usługi Azure AD](https://docs.microsoft.com/azure/active-directory/) lub innego dostawcy tożsamości do uwierzytelniania. Usługa Azure AD umożliwia uwierzytelnianie do punktów końcowych dostęp do interfejsu API (przez przekazanie [JSON Web Token dla usługi API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) do sprawdzania poprawności) i można zabezpieczyć dostęp do portalu dla deweloperów interfejsu API zarządzania (tylko warstw standardowa i Premium).

Ta architektura ma kilka podstawowych wzorców w celu jego działania:

2. Złożone interfejsy API są tworzone przy użyciu aplikacji logiki organizowanie wywołania z systemami SAAS, usług platformy Azure i dowolnych interfejsów API opublikowane usłudze API Management. [Logic Apps są ponadto publikowane](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) za pośrednictwem portalu dla deweloperów interfejsu API zarządzania.
3. Aplikacje [uzyskać token zabezpieczający uwierzytelniania OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) niezbędne do uzyskania dostępu do interfejsu API za pomocą usługi Azure Active Directory.
4. Usługa Azure API Management [weryfikuje token zabezpieczający](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)i przekazuje żądanie do aplikacji interfejsu API/logiki wewnętrznej bazy danych.

## <a name="recommendations"></a>Zalecenia

Konkretne wymagania mogą różnić się od ogólnego opisanej tu architektury. Użyj zaleceń w tej sekcji jako punktu wyjścia.

### <a name="azure-api-management-tier"></a>Warstwy usługi Azure API Management

Użyć podstawowa, standardowa lub Premium warstwy, ponieważ oferują umowy SLA w środowisku produkcyjnym i obsługi skalowalnego w poziomie w obrębie regionu świadczenia usługi Azure (liczbę jednostek zależy od warstwy). Warstwa Premium obsługuje również skalowalnego w poziomie w wielu regionach platformy Azure. Zestaw funkcji, wybranej na Twój poziom przepływności wymaganej warstwy podstawowa. Aby uzyskać więcej informacji, zobacz [API Management — cennik](https://azure.microsoft.com/pricing/details/api-management/).

Opłata jest naliczana dla wszystkich wystąpień usługi API Management, gdy są uruchomione. Jeśli skalowany w górę i nie potrzebujesz tego poziomu wydajności cały czas, należy wziąć pod uwagę wykorzystanie godzinowe rozliczeń usługi API Management i skalowanie w dół.

### <a name="logic-apps-pricing"></a>Ceny usługi Logic Apps

Logic Apps współpracuje także jako [bezserwerowe](logic-apps-serverless-overview.md) model — rozliczeń jest obliczana w oparciu o wykonanie akcji i łączników. Zobacz [Logic Apps — cennik](https://azure.microsoft.com/pricing/details/logic-apps/) Aby uzyskać więcej informacji. Obecnie nie są żadne uwagi warstwy dla usługi Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Usługa Logic Apps dla asynchronicznego wywołań interfejsu API

Usługa Logic Apps działa najlepiej w scenariusze, które nie wymagają małych opóźnień — np. asynchronicznego lub naczepa długo trwające wywołań interfejsu API. Jeśli wymagana jest niskie opóźnienia (np. wywołanie, która blokuje interfejs użytkownika) zalecamy zaimplementowanie tego interfejsu API lub operacji przy użyciu różnych technologii, np. usługi Azure Functions lub interfejsu API sieci Web wdrażane za pomocą usługi App Service. Nadal zaleca się tego interfejsu API były fronted konsumentom interfejsu API za pomocą usługi API Management.

### <a name="region"></a>Region

Aprowizacja usługi API Management i Logic Apps, w tym samym regionie, aby zminimalizować opóźnienie sieci. W większości przypadków należy wybrać region najbliżej Twoich użytkowników.

Grupa zasobów również ma region, który określa, gdzie przechowywane są metadane wdrożenia, i gdzie jest wykonywane Szablon wdrożenia. Umieść grupę zasobów i jej zasoby w tym samym regionie. Może to zwiększyć dostępność podczas wdrażania.

## <a name="scalability-considerations"></a>Zagadnienia dotyczące skalowalności

Administratorzy usługi API Management należy dodać [zasad buforowania](../api-management/api-management-howto-cache.md) w razie potrzeby zwiększyć skalowalność usługi i zmniejszyć obciążenie usług zaplecza.

Azure warstwy interfejsu API zarządzania podstawowa, standardowa i Premium można skalować w poziomie za pomocą w regionie platformy Azure, która oferuje większą pojemność. Administratorzy mogą używać metryki pojemności w menu metryk do analizowania użycia usługi i skalowanie w górę lub w dół odpowiednio.

Zalecenia dotyczące skalowania usługi API Management:

- Skalowanie potrzeb w celu uwzględnienia wzorców ruchu — klienci z bardziej lotnych wzorców ruchu będzie miał większy potrzebę wyższej wydajności.
- Spójne pojemność powyżej 66% może wskazywać na potrzebę skalowanie w górę.
- Spójną wydajność spadnie poniżej 20% może oznaczać możliwość skalowania w dół.
- Zawsze zaleca się załadować testowanie usługi API Management z językiem obciążenia przed włączeniem w środowisku produkcyjnym.

Usług w warstwie Premium może być skalowana w wielu regionach platformy Azure. Wdrażanie w ten sposób klienci uzyskają wyższy poziom umowy SLA (99,95%, a nie na poziomie 99,9%) i umożliwia obsługę usług się w pobliżu użytkowników w wielu regionach.

Aplikacje logiki bez użycia serwera modelu oznacza, że administratorzy nie muszą wprowadzić dodatkowe ważną kwestią dotyczącą skalowalność usługi; Usługa skaluje się automatycznie w celu spełnienia określonych wymagań.

## <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

W czasie pisania Umowa dotycząca poziomu usług (SLA) dla usługi Azure API Management ma wartość 99,9% w przypadku warstw Basic, Standard i Premium. Konfiguracje warstwy Premium przy użyciu wdrożenia co najmniej jedna jednostka w co najmniej dwóch regionach mają umową SLA gwarantującą dostępność przez 99,95%.

W czasie pisania Umowa dotycząca poziomu usług (SLA) dla usługi Azure Logic Apps jest 99,9%.

### <a name="backups"></a>Tworzenie kopii zapasowych

Konfiguracja usługi Azure API Management powinna być [regularnie wykonywana kopia zapasowa](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (odpowiednio na podstawie prawidłowości zmiany), a pliki kopii zapasowej, przechowywane w lokalizacji lub regionu platformy Azure jest inny, na którym działa usługa. Klienci mogą następnie wybrać jedną z dwóch opcji dla ich strategii odzyskiwania po awarii:

1. W przypadku odzyskiwania po awarii nowe wystąpienie usługi API Management jest aprowizowany, kopia zapasowa zostanie przywrócona do niego i rekordy DNS są repointed.
2. Klienci zachować kopię pasywną swoje usługi w innym regionie platformy Azure (ponoszenia dodatkowych kosztów) kopie zapasowe regularnie zostaną przywrócone do niego. W przypadku odzyskiwania po awarii tylko rekordy DNS muszą być repointed przywrócenia usługi.

Logic Apps może być odtworzona bardzo szybko i są bez użycia serwera, ich kopię zapasową, zapisując kopię skojarzonego szablonu usługi Azure Resource Manager. Mogą być one zapisywane do źródła kontrolki/zintegrowane klientów ciągłej integracji/ciągłego wdrażania (CI/CD) procesu.

Usługa Logic Apps, które zostały opublikowane za pośrednictwem usługi API Management, należy ich lokalizacji aktualizacji powinien przechodzą do centrum danych. Można to zrobić za pomocą prostego skryptu programu PowerShell do aktualizacji właściwości zaplecza interfejsu API.

## <a name="manageability-considerations"></a>Zagadnienia dotyczące możliwości zarządzania

Tworzenie oddzielnych grup zasobów w środowisku produkcyjnym, rozwoju i środowisk testowych. Ułatwia to zarządzanie wdrożeniami, usuwanie wdrożeń testowych i przypisywanie praw dostępu.
Podczas przydzielania zasobów do grup zasobów rozważ następujące kwestie:

- Cykl życia. Ogólnie rzecz biorąc, umieszczaj zasoby z tym samym cyklem życia w tej samej grupie zasobów.
- Dostęp. Możesz użyć [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) (RBAC), aby zastosować zasady dostępu do zasobów w grupie.
- Rozliczenia. Możesz wyświetlać zestawienia kosztów dla grupy zasobów.
- Warstwa cenowa zarządzania interfejsami API — zaleca się używanie warstwa Deweloper przeznaczona dla środowisk deweloperskich i testowych. Do produkcji wstępnej zalecamy wdrożenie repliki środowiska produkcyjnego, uruchamiania testów, a następnie wyłączając zminimalizować koszty.

Aby uzyskać więcej informacji, zobacz [grupy zasobów](../azure-resource-manager/resource-group-overview.md) Przegląd.

### <a name="deployment"></a>Wdrożenie

Firma Microsoft zaleca użycie [szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) do wdrożenia usługi Azure API Management i Azure Logic Apps. Szablony ułatwiają zautomatyzowanie wdrożeń za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia (CLI) platformy Azure.

Zaleca się umieszczenie usługi Azure API Management i wszystkich poszczególnych aplikacji logiki w ich własnych osobnymi szablonami usługi Resource Manager. Umożliwi to ich przechowywania, systemów kontroli źródła. Szablony te można następnie wdrożyć, razem lub oddzielnie w ramach ciągłej integracji/ciągłego (CI/CD) procesu wdrażania.

### <a name="versions"></a>Wersje

Każdorazowo, gdy konfiguracja Zmień aplikacji logiki (lub wdrażania aktualizacji za pomocą szablonu usługi Resource Manager), kopię tej wersji są przechowywane dla Twojej wygody (wszystkie wersje, które mają historii uruchamiania zostaną zachowane). Te wersje można użyć do śledzenia zmian historycznych i również przyczyniają się do wersji do bieżącej konfiguracji aplikacji logiki; ten sposób oznacza można skutecznie wycofać aplikację logiki, na przykład.

Usługa API Management ma dwóch odrębnych (ale bezpłatnych) [pojęcia versioning](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

- Używane do zapewnienia API klientów dzięki szerokiemu wyborowi interfejsu API, mogą oni korzystać z wersji na podstawie swoich potrzeb (np. v1, v2 lub beta, produkcja).
- Poprawki umożliwiające administratorom bezpiecznie dokonać zmian interfejsu API i wdrażać je dla użytkowników za pomocą opcjonalny komentarz dotyczący interfejsu API.

W kontekście wdrażania — API Management poprawki powinny być traktowane jako sposób, aby bezpiecznie, zmiany Historia zmian i należy pamiętać o tych zmianach konsumentów interfejsu API. Poprawkę można tworzyć w środowisku deweloperskim i wdrażane, między innymi środowiskami przy użyciu szablonów usługi Resource Manager.

Przy jednoczesnym poprawek można używane do testowania interfejsu API, zanim staje się "bieżący" i dostępny dla użytkowników, nie zaleca się przy użyciu tego mechanizmu obciążenia i testowanie integracji — oddzielne testu lub środowiska produkcji wstępnej należy użyć.

### <a name="configuration"></a>Konfigurowanie

Nigdy nie ewidencjonuj haseł, kluczy dostępu lub parametry połączenia w celu kontroli źródła. Jeżeli jest to konieczne, należy użyć odpowiednie techniki, aby wdrożyć i zabezpieczyć te wartości. 

W usłudze Logic Apps żadnych poufnych wartości potrzebnych w ramach aplikacji logiki (co oznacza, że nie można utworzyć w formie połączenia) należy przechowywanych w usłudze Azure Key Vault i określone za pomocą szablonu usługi Resource Manager. Zalecamy również za pomocą parametrów szablonu wdrożenia wraz z plikami parametrów dla każdego środowiska. Więcej wskazówek na [zabezpieczanie parametrów i danych wejściowych w przepływie pracy](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

W usłudze API Management wpisów tajnych są zarządzane przy użyciu obiektów o nazwie wartości/właściwości o nazwie. Te bezpiecznie przechowywać wartości, które mogą być udostępniane w ramach zasad usługi API Management. Zobacz jak [zarządzania wpisami tajnymi w usłudze API Management](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnostyka i monitorowanie

Zarówno [usługi API Management](../api-management/api-management-howto-use-azure-monitor.md) i [Logic Apps](logic-apps-monitor-your-logic-apps.md) obsługują monitorowanie operacyjnych za pośrednictwem [usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Usługa Azure Monitor jest domyślnie włączona i będzie zapewniać informacje w oparciu o różne metryki skonfigurowane dla każdej usługi.

Ponadto istnieją dodatkowe opcje dla poszczególnych usług:

- Dzienniki aplikacji Logic Apps mogą być wysyłane do [usługi Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) bardziej analizy i dashboarding.
- Usługa API Management obsługuje konfigurowania usługi Application Insights na potrzeby monitorowania operacji deweloperskich.
- Usługa API Management obsługuje [szablon rozwiązania Power BI dla niestandardowego interfejsu API analizy](http://aka.ms/apimpbi). Ten szablon rozwiązania umożliwia klientom tworzenie własnych rozwiązań niestandardowych analytics za pomocą raportów dostępnych w usłudze Power BI dla użytkowników biznesowych.

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

W tej sekcji przedstawiono zagadnienia dotyczące zabezpieczeń, które są specyficzne dla usług platformy Azure opisanych w tym artykule, wdrożone w architekturze, zgodnie z opisem. Nie jest to pełna lista najlepszych rozwiązań dotyczących zabezpieczeń.

- Użyj kontroli dostępu opartej na rolach (RBAC) w celu zapewnienia właściwych poziomów dostępu dla użytkowników.
- Zabezpiecz publiczne punkty końcowe interfejsu API w usłudze API Management przy użyciu uwierzytelniania OAuth lub Open IDConnect. W tym Konfigurowanie dostawcy tożsamości i dodając zasady walidacji tokenów JWT.
- Łączenie z usługami zaplecza z usługi API Management przy użyciu wzajemnymi certyfikatami
- Zabezpieczanie aplikacji logiki na podstawie wyzwalaczy HTTP, tworząc dozwolonych adresów IP, wskazujący na adres IP zarządzania interfejsu API. Zapobiega to wywoływanie aplikacji logiki z publicznej sieci internet bez pośrednictwa usługi API Management.

W tej architekturze referencyjnej przedstawiono sposób kompilowania platforma integracji przedsiębiorstw prosty, korzystający z usług integracji platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

- [Integracja w przedsiębiorstwie z kolejkami i zdarzenia](logic-apps-architectures-enterprise-integration-with-queues-events.md)
