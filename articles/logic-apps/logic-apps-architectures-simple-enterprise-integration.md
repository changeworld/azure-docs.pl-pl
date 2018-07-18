---
title: Architektura referencyjna integracji platformy Azure usług Integration Services proste przedsiębiorstwa
description: W tym artykule opisano architekturę referencyjną, która pokazuje sposób implementacji wzorca integracji przedsiębiorstw prosty, za pomocą usługi Azure Logic Apps i usługi Azure API Management.
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
ms.openlocfilehash: 982a5eabf8c6c3012a9b3e8fdbe2ff32ba439972
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113596"
---
# <a name="reference-architecture-simple-enterprise-integration"></a>Architektura referencyjna: integracja w przedsiębiorstwie prosty

Następujące architektura referencyjna pokazuje zestaw sprawdzonych rozwiązań, które można zastosować do integracji aplikacji, która korzysta z usług integracji platformy Azure. Architektura może służyć jako podstawa wielu wzorców innej aplikacji, które wymagają interfejsów API protokołu HTTP, przepływu pracy i aranżacji.

![Diagram architektury — integracja w przedsiębiorstwie prosty](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

*Istnieje wiele możliwych aplikacji w technologii integracji. Mogą należeć do zakresu z poziomu aplikacji point-to-point proste do pełnej wersji enterprise aplikacji usługi Azure Service Bus. Seria architektury opisano części komponentów wielokrotnego użytku, które mogą być stosowane do tworzenia aplikacji ogólnego integracji. Architekci należy wziąć pod uwagę składniki, które potrzebują do wykonania dla swoich aplikacji i infrastruktury.*

## <a name="architecture"></a>Architektura

Architektura zawiera następujące składniki:

- **Grupa zasobów**. [Grupa zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) to kontener logiczny dla zasobów platformy Azure.
- **Usługa Azure API Management**. [Usługa API Management](https://docs.microsoft.com/azure/api-management/) to w pełni zarządzana platforma, która umożliwia publikowanie, zabezpieczanie i Przekształć interfejsy API protokołu HTTP.
- **Azure portal dla deweloperów programu interfejsu API zarządzania**. Każde wystąpienie usługi Azure API Management jest dostarczany z dostępem do [portalu dla deweloperów](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). Portalu dla deweloperów programu interfejsu API zarządzania zapewnia dostęp do dokumentacji i przykładowy kod. Interfejsy API można sprawdzić w portalu dla deweloperów.
- **Usługi Azure Logic Apps**. [Usługa Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) jest platformą bez użycia serwera, który jest używany do tworzenia przepływów pracy przedsiębiorstwa i integracji.
- **Łączniki**. Usługa Logic Apps używa [łączników](https://docs.microsoft.com/azure/connectors/apis-list) połączyć się z często używane usługi. Usługa Logic Apps jest już setki różne łączniki, ale można również utworzyć łącznik niestandardowy.
- **Adres IP**. Usługa Azure API Management ma stały publicznego [adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) i nazwy domeny. Nazwa domeny jest poddomeną azure-api.net, takich jak contoso.azure-api.net. Logic Apps i Service Bus również mieć publiczny adres IP. Jednak w ramach tej architektury, będziemy ograniczać przydziału obowiązków dostępu do wywoływania aplikacji logiki punktów końcowych tylko adres IP usługi API Management (dla zabezpieczeń). Wywołania do usługi Service Bus są chronione przez sygnaturę dostępu współdzielonego (SAS).
- **Azure DNS**. [Usługa Azure DNS](https://docs.microsoft.com/azure/dns/) to usługa hostingowa przeznaczona dla domen DNS. Usługa Azure DNS udostępnia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Hostowanie domen na platformie Azure, pozwala na zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, że używasz innych usług platformy Azure. Aby użyć nazwy domeny niestandardowej, np. contoso.com, Utwórz rekordy DNS mapujące niestandardową nazwę domeny na adres IP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny w usłudze API Management](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD)**. Użyj [usługi Azure AD](https://docs.microsoft.com/azure/active-directory/) lub innego dostawcy tożsamości do uwierzytelniania. Usługa Azure AD umożliwia uwierzytelnianie do uzyskiwania dostępu do punktów końcowych interfejsu API, przekazując [JSON Web Token dla usługi API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) do sprawdzania poprawności. Usługa Azure AD można zabezpieczyć dostęp do portalu dla deweloperów programu interfejsu API zarządzania (tylko warstw standardowa i Premium).

Architektura zawiera niektóre wzorce, które mają podstawowe znaczenie jego działania:

- Złożone interfejsy API są tworzone za pomocą aplikacji logiki. Organizowanie ich wywołania do oprogramowania jako usługi (SaaS) systemów, usług platformy Azure i wszystkie interfejsy API, które są publikowane w usłudze API Management. [Aplikacje logiki są ponadto publikowane](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) za pośrednictwem portalu dla deweloperów programu interfejsu API zarządzania.
- Aplikacje używają usługi Azure AD do [uzyskać token zabezpieczający uwierzytelniania OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) , są wymagane do uzyskania dostępu do interfejsu API.
- Usługa Azure API Management [weryfikuje token zabezpieczający](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) i następnie przekazuje żądanie do aplikacji logiki i interfejsów API zaplecza.

## <a name="recommendations"></a>Zalecenia

Konkretne wymagania mogą różnić się od ogólnej architektury opisanych w tym artykule. Użyj zaleceń w tej sekcji jako punktu wyjścia.

### <a name="azure-api-management-tier"></a>Warstwy usługi Azure API Management

Użyj warstwy interfejsu API zarządzania podstawowa, standardowa lub Premium. Warstw oferuje produkcji Umowa dotycząca poziomu usług (SLA) i obsługuje skalowalnego w poziomie w obrębie regionu świadczenia usługi Azure (liczbę jednostek zależy od warstwy). Warstwa Premium obsługuje również skalowalnego w poziomie w wielu regionach platformy Azure. Bazowy warstwy, wybranych na poziom przepływności wymaganej i zestawu funkcji. Aby uzyskać więcej informacji, zobacz [API Management — cennik](https://azure.microsoft.com/pricing/details/api-management/).

Opłata jest naliczana dla wszystkich wystąpień usługi API Management, gdy są uruchomione. Jeśli skalowany w górę i nie potrzebujesz tego poziomu wydajności cały czas, należy rozważyć skorzystanie z usługi API Management, rozliczeń godzinowych i Skaluj w dół.

### <a name="logic-apps-pricing"></a>Ceny usługi Logic Apps

Usługa Logic Apps używa [bezserwerowe](logic-apps-serverless-overview.md) modelu. Rozliczanie jest obliczany na podstawie wykonywania akcji i łączników. Aby uzyskać więcej informacji, zobacz [Logic Apps — cennik](https://azure.microsoft.com/pricing/details/logic-apps/). Obecnie nie istnieją żadne uwagi warstwy dla usługi Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Usługa Logic Apps dla asynchronicznego wywołań interfejsu API

Usługa Logic Apps działa najlepiej w scenariuszach, które nie wymagają małych opóźnień. Na przykład działa najlepiej dla asynchronicznego lub długotrwałe o częściowej lub interfejsu API. Jeśli z małymi opóźnieniami jest wymagana (na przykład, wywołanie, która blokuje interfejs użytkownika), zaleca się implementowania tego interfejsu API lub operacji przy użyciu różnych technologii. Na przykład użyć usługi Azure Functions lub internetowych interfejsów API, który można wdrożyć przy użyciu usługi Azure App Service. Nadal zaleca się frontonu konsumentów interfejsu API do interfejsu API za pomocą usługi API Management.

### <a name="region"></a>Region

Aprowizacja usługi API Management i Logic Apps, w tym samym regionie, aby zminimalizować opóźnienie sieci. Ogólnie rzecz biorąc wybierz region, który jest najbliżej Twoich użytkowników.

Grupa zasobów również ma region. Region określa miejsce przechowywania metadanych wdrażania i gdzie jest wykonywany z szablonu wdrożenia. Umieść grupę zasobów i jej zasoby w tym samym regionie, zwiększyć dostępność podczas wdrażania.

## <a name="scalability"></a>Skalowalność

Administratorzy usługi API Management należy dodać [zasad buforowania](../api-management/api-management-howto-cache.md) w razie potrzeby w celu zwiększenia skalowalności usługi. Pamięć podręczna pomaga również zmniejszyć obciążenie usług zaplecza.

Warstwy usługi Azure API Management podstawowa, standardowa i Premium można skalować w poziomie w regionie platformy Azure, która oferuje większą pojemność. Administratorzy mogą używać **metryki pojemności** opcji **metryki** menu do analizowania użycia usługi i skalowanie w górę lub w dół odpowiednio.

Zalecenia dotyczące skalowania usługi API Management:

- Skalowanie potrzeb w celu uwzględnienia wzorców ruchu. Klienci z bardziej lotnych wzorców ruchu mają większy potrzebę wyższej wydajności.
- Spójne pojemność powyżej 66% może wskazywać na potrzebę skalowanie w górę.
- Spójną wydajność spadnie poniżej 20% może oznaczać możliwość skalowania w dół.
- Zawsze zaleca się test obciążenia usługi API Management z językiem obciążenia przed włączeniem obciążenia w środowisku produkcyjnym.

Usług w warstwie Premium może być skalowana w wielu regionach platformy Azure. Klienci, którzy wdrażają przy użyciu skalowania usługi w wielu regionach platformy Azure uzyskać wyższy poziom umowy SLA (99,95% w porównaniu ze 99,9%) i umożliwia obsługę usług w pobliżu użytkowników w wielu regionach.

Aplikacje logiki, które bez użycia serwera modelu oznacza, że administratorzy nie muszą planowanie skalowalność usługi. Usługa automatycznie skaluje w celu spełnienia określonych wymagań.

## <a name="availability"></a>Dostępność

Obecnie umowa SLA dla usługi Azure API Management jest 99,9% w przypadku warstw Basic, Standard i Premium. Konfiguracje warstwy Premium przy użyciu wdrożenia co najmniej jedna jednostka w co najmniej dwóch regionach mają umową SLA gwarantującą dostępność przez 99,95%.

Obecnie umowa SLA dla usługi Azure Logic Apps jest 99,9%.

### <a name="backups"></a>Tworzenie kopii zapasowych

Konfiguracji usługi Azure API Management powinien być [regularnie wykonywana kopia zapasowa](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (oparte na prawidłowość zmian). Pliki kopii zapasowej powinny być przechowywane w lokalizacji lub regionu platformy Azure, która różni się od gdzie usługa się znajduje. Klienci mogą następnie wybrać jedną z dwóch opcji dla ich strategii odzyskiwania po awarii:

- W przypadku odzyskiwania po awarii nowe wystąpienie usługi API Management jest aprowizowany, kopia zapasowa zostanie przywrócona do nowego wystąpienia i rekordy DNS są repointed.
- Klienci przechowywać kopię pasywną swoje usługi w innym regionie platformy Azure (ponoszenia dodatkowych kosztów). Tworzenie kopii zapasowych regularnie zostaną przywrócone do skopiowania. W przypadku odzyskiwania po awarii tylko rekordy DNS muszą repointed przywrócenia usługi.

Ponieważ aplikacje logiki można ponownie utworzyć szybko i są bez użycia serwera, ich kopię zapasową, zapisując kopię skojarzonego szablonu usługi Azure Resource Manager. Szablony można zapisywać do kontroli źródła, a ich można zintegrować z klienta ciągłej integracji/ciągłego wdrażania (CI/CD) procesu.

Jeśli aplikacja logiki, która została opublikowana przy użyciu usługi API Management zostanie przeniesiony do innego centrum danych, należy zaktualizować lokalizację aplikacji. Aby zaktualizować lokalizację aplikacji, użyj podstawowe skrypt programu PowerShell, aby zaktualizować **zaplecza** właściwości interfejsu API.

## <a name="manageability"></a>Możliwości zarządzania

Tworzenie oddzielnych grup zasobów w środowisku produkcyjnym, rozwoju i środowisk testowych. Przy użyciu oddzielnych grup zasobów ułatwia to zarządzanie wdrożeniami, usuwanie wdrożeń testowych i przypisywanie praw dostępu.

Podczas przypisywania zasobów do grup zasobów, należy wziąć pod uwagę następujące czynniki:

- **Cykl życia**. Ogólnie rzecz biorąc umieszczaj zasoby, które mają taki sam cykl życia w tej samej grupie zasobów.
- **Dostęp do**. Możesz użyć [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) (RBAC), aby zastosować zasady dostępu do zasobów w grupie.
- **Rozliczenia**. Można wyświetlić pakiet zbiorczy kosztów dla grupy zasobów.
- **Warstwę cenową dla usługi API Management**. Firma Microsoft zaleca się używanie warstwy Deweloper do tworzenia aplikacji i środowisk testowych. W środowisku przedprodukcyjnym zalecamy wdrożenie repliki środowiska produkcyjnego, uruchamiania testów, a następnie wyłączając zminimalizować koszty.

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Wdrożenie

Firma Microsoft zaleca użycie [szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) wdrażanie usługi API Management i Logic Apps. Szablony ułatwiają zautomatyzowanie wdrożeń za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Zaleca się umieszczenie w usłudze Azure API Management i wszystkie inne aplikacje logiki poszczególnych w swoje własne, oddzielnym szablonów usługi Resource Manager. Korzystając z osobnymi szablonami, można przechowywać zasoby w systemów kontroli źródła. Można także wdrożyć zasoby, razem lub oddzielnie w ramach procesu wdrażania ciągłej integracji/ciągłego wdrażania.

### <a name="versions"></a>Wersje

Każdorazowo, gdy konfiguracja Zmień aplikacji logiki (lub wdrażania aktualizacji za pomocą szablonu usługi Resource Manager), kopię tej wersji są przechowywane dla Twojej wygody (przechowywane są wszystkie wersje, które mają historii uruchamiania). Te wersje służy do śledzenia zmian historycznych i podwyższenie poziomu wersji do bieżącej konfiguracji aplikacji logiki. Na przykład można skutecznie wycofać aplikację logiki.

Usługa API Management ma dwóch odrębnych (ale bezpłatnych) [pojęcia versioning](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

- Wersje, które służą do zapewniania klientów interfejsu API za pomocą wybranego interfejsu API mogą one wykorzystywać zależnie od ich potrzeb (na przykład v1, v2 lub beta, produkcja).
- Poprawki, które umożliwiają administratorom bezpieczne wprowadzanie zmian do interfejsu API interfejsu API, a następnie wdrożyć zmiany dla użytkowników z opcjonalny komentarz.

W kontekście wdrażania jest dobrym rozwiązaniem, należy wziąć pod uwagę poprawki interfejsu API zarządzania, aby bezpiecznie wprowadzić zmiany, Zachowaj historię zmian i należy pamiętać o tych zmianach konsumentów interfejsu API. Zmiany mogą być tworzone w środowisku deweloperskim, a także wdrożyć między innymi środowiskami przy użyciu szablonów usługi Resource Manager.

Chociaż poprawek można używać do testowania interfejsu API, zanim wprowadzisz "bieżący" i jest dostępny dla użytkowników, nie zalecamy używania ten mechanizm dla obciążenia lub testów integracji. Zamiast tego należy użyć oddzielnych testu lub środowisk produkcji wstępnej.

### <a name="configuration"></a>Konfigurowanie

Nigdy nie zaewidencjonowania haseł, kluczy dostępu lub parametry połączenia do kontroli źródła. Jeśli te wartości są wymagane, należy użyć odpowiednie techniki, aby wdrożyć i zabezpieczyć te wartości. 

W usłudze Logic Apps żadnych poufnych wartości wymagane w aplikacji logiki (co oznacza, że nie można utworzyć w formie połączenia) należy przechowywanych w usłudze Azure Key Vault i określone za pomocą szablonu usługi Resource Manager. Zalecamy również za pomocą parametrów szablonu wdrożenia oraz pliki parametrów dla każdego środowiska. Aby uzyskać więcej informacji, zobacz [zabezpieczanie parametrów i danych wejściowych w przepływie pracy](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

W usłudze API Management, wpisów tajnych są zarządzane przy użyciu obiektów o nazwie *nazwane wartości* lub *właściwości*. Obiekty bezpiecznie przechowywać wartości, które mogą być udostępniane w ramach zasad usługi API Management. Aby uzyskać więcej informacji, zobacz [zarządzania wpisami tajnymi w usłudze API Management](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnostyka i monitorowanie

[Usługa API Management](../api-management/api-management-howto-use-azure-monitor.md) i [Logic Apps](logic-apps-monitor-your-logic-apps.md) zarówno, obsługują monitorowanie operacyjnych za pośrednictwem [usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Usługa Azure Monitor udostępnia informacje w zależności od metryki, które są skonfigurowane dla każdej usługi. Usługa Azure Monitor jest domyślnie włączona.

Te opcje są również dostępne dla każdej usługi:

- Dzienniki aplikacji Logic Apps mogą być wysyłane do [usługi Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) bardziej analizy i dashboarding.
- Usługa API Management obsługuje konfigurowanie usługi Azure Application Insights do monitorowania infrastruktury DevOps.
- Usługa API Management obsługuje [szablon rozwiązania Power BI dla niestandardowego interfejsu API analizy](http://aka.ms/apimpbi). Klientów można użyć szablonu rozwiązania utworzyć własne rozwiązania niestandardowe analizy. Raporty są dostępne w usłudze Power BI dla użytkowników biznesowych.

## <a name="security"></a>Bezpieczeństwo

W tej sekcji przedstawiono zagadnienia dotyczące zabezpieczeń, które są specyficzne dla usług platformy Azure, które są opisane w tym artykule i w których są wdrażane w architekturze zgodnie z opisem. Nie jest to pełna lista najlepszych rozwiązań dotyczących zabezpieczeń.

- Użyj kontroli dostępu opartej na rolach (RBAC) w celu zapewnienia właściwych poziomów dostępu dla użytkowników.
- Zabezpiecz publiczne punkty końcowe interfejsu API w usłudze API Management, za pomocą uwierzytelniania OAuth/OpenID Connect. Aby zabezpieczyć publiczne punkty końcowe interfejsu API, skonfigurować dostawcę tożsamości i Dodaj zasady walidacji tokenu Web JSON (JWT).
- Połączyć się z usługami zaplecza z usługi API Management przy użyciu wzajemnymi certyfikatami.
- Zabezpieczanie aplikacji logiki na podstawie wyzwalaczy HTTP przez utworzenie listy dozwolonych adresów IP, wskazujący na adres IP zarządzania interfejsu API. Adres IP na liście dozwolonych zapobiega wywoływanie aplikacji logiki z publicznej sieci internet bez pośrednictwa usługi API Management.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [integracja w przedsiębiorstwie z kolejkami i zdarzenia](logic-apps-architectures-enterprise-integration-with-queues-events.md).
