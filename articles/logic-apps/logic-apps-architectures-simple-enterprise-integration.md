---
title: Wzorzec architektury integracji przedsiębiorstw proste — usługi integracji platformy Azure
description: Odwołanie do tej architektury pokazuje, jak można zaimplementować wzorzec integracji przedsiębiorstw proste przy użyciu usługi Azure Logic Apps i usługi Azure API Management
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 7081c9e4f6e6deee196255f04180a8f2cc792876
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122499"
---
# <a name="simple-enterprise-integration-architecture"></a>Architektura integracji przedsiębiorstw prosty

W tym artykule opisano architekturę integracji przedsiębiorstwa, która używa sprawdzonych rozwiązań, które można zastosować do integracji aplikacji, korzystając z usług integracji platformy Azure. Tej architektury można użyć jako podstawa wielu wzorców innej aplikacji, które wymagają interfejsów API protokołu HTTP, przepływu pracy i aranżacji.

![Diagram architektury — integracja w przedsiębiorstwie prosty](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

W tej serii opisano części komponentów wielokrotnego użytku, które mogą być stosowane do tworzenia aplikacji ogólnego integracji. Ponieważ technologia integracji ma wiele możliwych zastosowań — od aplikacji point-to-point prostych aplikacji usługi Azure Service Bus pełną przedsiębiorstwa, należy wziąć pod uwagę składniki, które należy wdrożyć dla aplikacji i infrastruktury.

## <a name="architecture-components"></a>Składniki architektury

Ta architektura integracji przedsiębiorstwa obejmuje następujące składniki:

- **Grupa zasobów**: A [grupy zasobów](../azure-resource-manager/resource-group-overview.md) to kontener logiczny dla zasobów platformy Azure.

- **Usługa Azure API Management**: [usługi API Management](https://docs.microsoft.com/azure/api-management/) service to w pełni zarządzana platforma publikowanie, zabezpieczanie i przekształcanie interfejsów API protokołu HTTP.

- **Azure portal dla deweloperów programu interfejsu API zarządzania**: każde wystąpienie usługi Azure API Management zapewnia dostęp do [portalu dla deweloperów](../api-management/api-management-customize-styles.md). Ten portal zapewnia dostęp do dokumentacji i przykładowy kod. Możesz również przetestować interfejsów API w portalu dla deweloperów.

- **Usługa Azure Logic Apps**: [Logic Apps](../logic-apps/logic-apps-overview.md) bezserwerowa platforma do tworzenia przepływów pracy przedsiębiorstwa i integracji.

- **Łączniki**: Logic Apps używa [łączników](../connectors/apis-list.md) nawiązywania połączenia z najczęściej używanych usług. Usługa Logic Apps oferuje setki łączników, ale można również utworzyć łącznik niestandardowy.

- **Adres IP**: Usługa Azure API Management ma stały publicznego [adresu IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) i nazwy domeny. Domyślna nazwa domeny jest poddomeną azure-api.net, na przykład contoso.azure-api.net, ale można również skonfigurować [domen niestandardowych](../api-management/configure-custom-domain.md). Logic Apps i Service Bus również mieć publiczny adres IP. Dla bezpieczeństwa w tej architekturze ogranicza dostęp wywoływanie punktów końcowych aplikacji logiki, tylko adres IP usługi API Management. Wywołania do usługi Service Bus są chronione przez sygnaturę dostępu współdzielonego (SAS).

- **Usługa Azure DNS**: [system DNS Azure](https://docs.microsoft.com/azure/dns/) to usługa hostingowa przeznaczona dla domen DNS. Usługa Azure DNS udostępnia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Hostowanie domen na platformie Azure, pozwala na zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, że używasz innych usług platformy Azure. Aby używać niestandardowej nazwy domeny, taką jak contoso.com, Utwórz rekordy DNS mapujące niestandardową nazwę domeny na adres IP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny w usłudze API Management](../api-management/configure-custom-domain.md).

- **Azure Active Directory (Azure AD)**: możesz użyć [usługi Azure AD](https://docs.microsoft.com/azure/active-directory/) lub innego dostawcy tożsamości do uwierzytelniania. Usługa Azure AD umożliwia uwierzytelnianie do uzyskiwania dostępu do punktów końcowych interfejsu API, przekazując [JSON Web Token dla usługi API Management](../api-management/policies/authorize-request-based-on-jwt-claims.md) do sprawdzania poprawności. W przypadku warstw Standard i Premium usługi Azure AD można zabezpieczyć dostęp do portalu dla deweloperów programu interfejsu API zarządzania.

## <a name="patterns"></a>Wzorce 

Ta architektura używa niektóre wzorce, które mają podstawowe znaczenie operacji:

* Złożone interfejsy API są tworzone za pomocą aplikacji logiki, które możesz odpowiednio organizować wywołania do oprogramowania jako systemy usługi (SaaS), usług platformy Azure i wszystkie interfejsy API, które są publikowane w usłudze API Management. Aplikacje logiki, również są [opublikowane za pośrednictwem portalu dla deweloperów programu interfejsu API zarządzania](../api-management/import-logic-app-as-api.md).

* Aplikacje używają usługi Azure AD dla [uzyskiwanie tokenu zabezpieczeń protokołu OAuth 2.0](../api-management/api-management-howto-protect-backend-with-aad.md) , są wymagane do uzyskania dostępu do interfejsu API.

* Usługa Azure API Management [weryfikuje token zabezpieczający](../api-management/api-management-howto-protect-backend-with-aad.md) i następnie przekazuje żądanie do aplikacji logiki i interfejsów API zaplecza.

## <a name="recommendations"></a>Zalecenia

Konkretne wymagania mogą różnić się od ogólnej architektury, który jest opisany w tym artykule. Użyj zaleceń w tej sekcji jako punktu wyjścia.

### <a name="azure-api-management-tier"></a>Warstwy usługi Azure API Management

Użyj warstwy interfejsu API zarządzania podstawowa, standardowa lub Premium. Te warstwy oferuje produkcji Umowa dotycząca poziomu usług (SLA) i obsługuje skalowania w poziomie w obrębie regionu świadczenia usługi Azure. Liczba jednostek jest zależna od warstwy. Warstwa Premium obsługuje również skalowania w poziomie w wielu regionach platformy Azure. Wybieranie warstwy na podstawie zestawu funkcji i poziomu przepływności wymagane. Aby uzyskać więcej informacji, zobacz [API Management — cennik](https://azure.microsoft.com/pricing/details/api-management/).

Opłata jest naliczana dla wszystkich wystąpień usługi API Management, gdy są uruchomione. Jeśli skalowany w górę i nie potrzebujesz tego poziomu wydajności cały czas, należy rozważyć skorzystanie z usługi API Management, rozliczeń godzinowych i Skaluj w dół.

### <a name="logic-apps-pricing"></a>Ceny usługi Logic Apps

Usługa Logic Apps używa [bezserwerowe](../logic-apps/logic-apps-serverless-overview.md) modelu. Rozliczanie jest obliczany na podstawie wykonywania akcji i łączników. Aby uzyskać więcej informacji, zobacz [Logic Apps — cennik](https://azure.microsoft.com/pricing/details/logic-apps/). Obecnie nie istnieją żadne uwagi warstwy dla usługi Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Usługa Logic Apps dla asynchronicznego wywołań interfejsu API

Usługa Logic Apps działa najlepiej w scenariuszach, które nie wymagają małych opóźnień. Na przykład aplikacje logiki sprawdza się najlepiej w asynchronicznej lub długotrwałe o częściowej lub interfejsu API. Jeśli z małymi opóźnieniami jest wymagane, na przykład, wywołanie, która blokuje interfejsu użytkownika, implementować operacji lub interfejsu API przy użyciu różnych technologii. Na przykład użyć usługi Azure Functions lub internetowych interfejsów API, który można wdrożyć przy użyciu usługi Azure App Service. Usługa API Management umożliwia frontonu interfejsu API do klientów interfejsu API.

### <a name="region"></a>Region

Aby zminimalizować opóźnienie sieci, należy wybrać dla usługi API Management, Logic Apps i Service Bus tym samym regionie. Ogólnie rzecz biorąc wybierz region, który jest najbliżej Twoich użytkowników.

Grupa zasobów również ma region. Ten region określa miejsce przechowywania metadanych wdrażania i gdzie można wykonać szablon wdrożenia. Aby zwiększyć dostępność podczas wdrażania, umieść grupę zasobów i zasoby, w tym samym regionie.

## <a name="scalability"></a>Skalowalność

W celu zwiększenia skalowalności, podczas administrowania usługi API Management, należy dodać [zasad buforowania](../api-management/api-management-howto-cache.md) zgodnie z wymaganiami. Pamięć podręczna pomaga również zmniejszyć obciążenie usług zaplecza.

Do dyspozycji większą pojemność, można skalować w poziomie warstwy Azure API Management w warstwie podstawowa, standardowa i Premium w regionie platformy Azure. Do analizowania użycia dla usługi na **metryki** menu, wybierz opcję **metryki pojemności** opcji i skalowanie w górę lub w dół odpowiednio.

Zalecenia dotyczące skalowania usługi API Management:

- Podczas skalowania, należy wziąć pod uwagę wzorców ruchu. Klienci z bardziej lotnych wzorców ruchu muszą większej pojemności.

- Spójne pojemność, która jest większa niż 66% może wskazywać na potrzebę skalowanie w górę.

- Spójne pojemność, która jest mniejsza niż 20% może oznaczać możliwość skalowania w dół.

- Przed włączeniem obciążenia w środowisku produkcyjnym zawsze testu obciążenia usługi API Management z językiem obciążenia.

Usług w warstwie Premium można skalować w wielu regionach platformy Azure. W przypadku wdrożenia przy użyciu skalowania usługi w wielu regionach platformy Azure, możesz uzyskać wyższy poziom umowy SLA (99,95% w porównaniu ze 99,9%) i usługi aprowizacji w pobliżu użytkowników w wielu regionach.

Aplikacje logiki, które bez użycia serwera modelu oznacza, że administratorzy nie muszą planowanie skalowalność usługi. Usługa automatycznie skaluje w celu spełnienia określonych wymagań.

## <a name="availability"></a>Dostępność

* W przypadku warstw Basic, Standard i Premium Umowa dotycząca poziomu usług (SLA) dla usługi Azure API Management jest aktualnie przez 99,9%. W przypadku konfiguracji warstwy premium przy użyciu wdrażania, który ma co najmniej jedna jednostka w co najmniej dwóch regionach umowy SLA jest 99,95% czasu.

* Umowa SLA dla usługi Azure Logic Apps jest obecnie przez 99,9%.

### <a name="backups"></a>Tworzenie kopii zapasowych

Oparte na prawidłowość zmiany [regularnie Twórz kopie zapasowe](../api-management/api-management-howto-disaster-recovery-backup-restore.md) konfigurację usługi Azure API Management. Store kopii zapasowej plików w lokalizacji lub regionu platformy Azure, która różni się od zawierającej usługi. Następnie możesz wybrać jedną z opcji w ramach strategii odzyskiwania po awarii:

* W przypadku odzyskiwania po awarii udostępnienie nowego wystąpienia usługi API Management, przywrócenia kopii zapasowej do nowego wystąpienia i repoint rekordów DNS.

* Zachowaj kopię pasywną z usługą w innym regionie platformy Azure, która spowoduje naliczenie dodatkowych kosztów. Regularnie przywracanie kopii zapasowych do tej kopii. Aby przywrócić usługę podczas zdarzenia odzyskiwania po awarii, należy tylko repoint rekordów DNS.

Ponieważ można szybko odtworzyć aplikacji logiki, które są bez użycia serwera, przeznaczenie je, zapisując kopię skojarzonego szablonu usługi Azure Resource Manager. Szablony można zapisywać w kontroli źródła, a szablony można zintegrować z procesem ciągłej integracji/ciągłego wdrażania (CI/CD).

Jeśli publikujesz aplikację logiki, za pomocą usługi Azure API Management, a ta aplikacja logiki przechodzi do innego centrum danych, należy zaktualizować lokalizację aplikacji. Można zaktualizować interfejsu API **zaplecza** właściwości przy użyciu podstawowych skryptu programu PowerShell.

## <a name="manageability"></a>Możliwości zarządzania

Tworzenie oddzielnych grup zasobów w środowisku produkcyjnym, rozwoju i środowisk testowych. Oddzielnych grup zasobów można ułatwić zarządzanie wdrożeniami, usuwanie wdrożeń testowych i przypisywanie praw dostępu.

Podczas przypisywania zasobów do grup zasobów, należy wziąć pod uwagę następujące czynniki:

* **Cykl życia**: ogólnie rzecz biorąc, umieść zasoby, które mają taki sam cykl życia w tej samej grupie zasobów.

* **Dostęp do**: Aby zastosować zasady dostępu do zasobów w grupie, można użyć [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md).

* **Rozliczenia**: można wyświetlić pakiet zbiorczy kosztów dla grupy zasobów.

* **Warstwę cenową dla usługi API Management**: Użyj warstwa Deweloper przeznaczona dla środowisk programowania i testowania. Aby zminimalizować koszty w środowisku przedprodukcyjnym, wdrożenie repliki środowiska produkcyjnego, uruchom testy i następnie zamknij.

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="deployment"></a>Wdrożenie

* Aby wdrożyć usługi API Management i Logic Apps, użyj [szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Szablony ułatwiają automatyzację wdrożenia przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

* Umieść usługi API Management i wszystkie inne aplikacje logiki poszczególnych w ich własnych osobnymi szablonami usługi Resource Manager. Korzystając z osobnymi szablonami, można przechowywać zasoby w systemów kontroli źródła. Następnie można wdrożyć te szablony, razem lub oddzielnie w ramach procesu ciągłej integracji/ciągłego wdrażania (CI/CD).

### <a name="versions"></a>Wersje

Za każdym razem zmiany konfiguracji aplikacji logiki lub wdrożenia aktualizacji przy użyciu szablonu usługi Resource Manager, Azure przechowuje kopie tej wersji dla Twojej wygody i zachowuje wszystkie wersje, które mają historii uruchamiania. Te wersje służy do śledzenia zmian historycznych lub podwyższanie poziomu wersji jako bieżącą konfigurację aplikacji logiki. Na przykład można skutecznie wycofać aplikację logiki.

Usługa Azure API Management ma są różne, ale uzupełniające się [pojęcia versioning](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

* Wersje, które zapewniają klientów interfejsu API, możliwość wybierz wersję interfejsu API na podstawie swoich potrzeb, na przykład, v1, v2, beta lub produkcji

* Poprawki, które umożliwiają administratorom bezpiecznie wprowadzić zmiany w interfejsie API, a następnie wdrożyć tych zmian do użytkowników z opcjonalny komentarz dotyczący interfejsu API

W przypadku wdrożenia należy wziąć pod uwagę poprawki usługi API Management, jak można bezpiecznie dokonać zmian, historia zmian i komunikują się te zmiany do konsumentów interfejsu API. Można wprowadzić zmiany w środowisku deweloperskim i wdrażanie tej zmiany w innych środowiskach przy użyciu szablonów usługi Resource Manager.

Chociaż poprawek można używać do testowania interfejsu API, przed wprowadzeniem tych zmian "bieżący" i jest dostępny dla użytkowników, ta metoda nie jest zalecane w przypadku obciążenia lub testów integracji. Zamiast tego należy użyć oddzielnych testu lub środowisk produkcji wstępnej.

### <a name="configuration-and-sensitive-information"></a>Konfiguracja i poufne informacje

Nigdy nie ewidencjonuj haseł, kluczy dostępu ani parametrów połączenia w systemie kontroli kodu źródłowego. Jeśli te wartości są wymagane, zabezpieczanie i wdrażanie te wartości przy użyciu odpowiednich technik. 

W usłudze Logic Apps Jeśli aplikacja logiki wymaga poufnych wartości, których nie można utworzyć w ramach połączenia, przechowywania tych wartości w usłudze Azure Key Vault i odwoływać się do nich za pomocą szablonu usługi Resource Manager. Użyj parametrów szablonu wdrożenia oraz pliki parametrów dla każdego środowiska. Aby uzyskać więcej informacji, zobacz [zabezpieczanie parametrów i danych wejściowych w przepływie pracy](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

Usługa API Management zarządza wpisy tajne przy użyciu obiektów o nazwie *nazwane wartości* lub *właściwości*. Te obiekty są bezpiecznie przechowywać wartości, które są dostępne za pośrednictwem zasad usługi API Management. Aby uzyskać więcej informacji, zobacz [zarządzania wpisami tajnymi w usłudze API Management](../api-management/api-management-howto-properties.md).

## <a name="diagnostics-and-monitoring"></a>Diagnostyka i monitorowanie

Możesz użyć [usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) operacyjnej monitorowania w obu [usługi API Management](../api-management/api-management-howto-use-azure-monitor.md) i [Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md). Usługa Azure Monitor udostępnia informacje na podstawie metryk skonfigurowane dla każdej usługi i jest domyślnie włączona.

Każda usługa ma również następujące opcje:

* Lepiej analizy i dashboarding, możesz wysłać Dzienniki aplikacji logiki do [usługi Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md).

* Do monitorowania infrastruktury DevOps, można skonfigurować usługi Azure Application Insights dla usługi API Management.

* Usługa API Management obsługuje [szablon rozwiązania Power BI dla niestandardowego interfejsu API analizy](http://aka.ms/apimpbi). Ten szablon rozwiązania służy do tworzenia rozwiązania analizy. Dla użytkowników biznesowych usługa Power BI udostępnia raporty.

## <a name="security"></a>Bezpieczeństwo

Mimo że ta lista nie zawiera opisu całkowicie wszystkie najlepsze rozwiązania dotyczące zabezpieczeń, poniżej przedstawiono niektóre zagadnienia dotyczące zabezpieczeń, które mają zastosowanie szczególnie w przypadku usług platformy Azure wdrożonych w ramach architektury, który jest opisany w tym artykule:

* Aby upewnić się, że użytkownicy mają odpowiedni dostęp poziomy, użyj kontroli dostępu opartej na rolach (RBAC).

* Zabezpiecz publiczne punkty końcowe interfejsu API w usłudze API Management za pomocą uwierzytelniania OAuth lub OpenID Connect. Aby zabezpieczyć publiczne punkty końcowe interfejsu API, skonfigurować dostawcę tożsamości, a następnie dodaj zasady walidacji tokenu Web JSON (JWT).

* Połączyć się z usługami zaplecza z usługi API Management przy użyciu wzajemnymi certyfikatami.

* Zabezpieczanie aplikacji logiki na podstawie wyzwalaczy HTTP przez utworzenie listy dozwolonych adresów IP, wskazujący na adres IP zarządzania interfejsu API. Adres IP na liście dozwolonych zapobiega wywoływanie aplikacji logiki z publicznej sieci internet bez pośrednictwa usługi API Management.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [integracja w przedsiębiorstwie z kolejkami i zdarzenia](../logic-apps/logic-apps-architectures-enterprise-integration-with-queues-events.md)
