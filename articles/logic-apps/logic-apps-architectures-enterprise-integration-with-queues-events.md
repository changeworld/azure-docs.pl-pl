---
title: Wzorzec architektury integracji przedsiębiorstwa — usługi integracji platformy Azure
description: Odwołanie do tej architektury pokazuje, jak można zaimplementować wzorzec integracji przedsiębiorstwa za pomocą usługi Azure Logic Apps, Azure API Management, Azure Service Bus i usługi Azure Event Grid
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 5b58f4b71d8d9f3ca91d8cefc6215073fd836765
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093670"
---
# <a name="enterprise-integration-architecture-with-queues-and-events"></a>Architektura integracji przedsiębiorstwa z kolejkami i zdarzenia

W tym artykule opisano architekturę integracji przedsiębiorstwa, która używa sprawdzonych rozwiązań, które można zastosować do integracji aplikacji, korzystając z usług integracji platformy Azure. Tej architektury można użyć jako podstawa wielu wzorców innej aplikacji, które wymagają interfejsów API protokołu HTTP, przepływu pracy i aranżacji.

![Diagram architektury — integracja w przedsiębiorstwie z kolejkami i zdarzenia](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

W tej serii opisano części komponentów wielokrotnego użytku, które mogą być stosowane do tworzenia aplikacji ogólnego integracji. Ponieważ technologia integracji ma wiele możliwych zastosowań — od aplikacji point-to-point prostych aplikacji usługi Azure Service Bus pełną przedsiębiorstwa, należy wziąć pod uwagę składniki, które należy wdrożyć dla aplikacji i infrastruktury.

## <a name="architecture-components"></a>Składniki architektury

Ta architektura bazuje na architekturze opisanej w artykule, [odwołanie do architektury: integracja w przedsiębiorstwie proste](/azure/architecture/reference-architectures/enterprise-integration/simple-enterprise-integration). Danej architekturze [zalecenia](/azure/architecture/reference-architectures/enterprise-integration/simple-enterprise-integration#recommendations) mają zastosowanie również w tym miejscu, ale w celu skrócenia programu, w tym artykule pomija tych zaleceń z [zalecenia](#recommendations) sekcji. Ta architektura integracji przedsiębiorstwa obejmuje następujące składniki:

- **Grupa zasobów**: A [grupy zasobów](../azure-resource-manager/resource-group-overview.md) to kontener logiczny dla zasobów platformy Azure.

- **Usługa Azure API Management**: [usługi API Management](https://docs.microsoft.com/azure/api-management/) service to w pełni zarządzana platforma publikowanie, zabezpieczanie i przekształcanie interfejsów API protokołu HTTP.

- **Azure portal dla deweloperów programu interfejsu API zarządzania**: każde wystąpienie usługi Azure API Management zapewnia dostęp do [portalu dla deweloperów](../api-management/api-management-customize-styles.md). Ten portal zapewnia dostęp do dokumentacji i przykładowy kod. Możesz również przetestować interfejsów API w portalu dla deweloperów.

- **Usługa Azure Logic Apps**: [Logic Apps](../logic-apps/logic-apps-overview.md) bezserwerowa platforma do tworzenia przepływów pracy przedsiębiorstwa i integracji.

- **Łączniki**: Logic Apps używa [łączników](../connectors/apis-list.md) nawiązywania połączenia z najczęściej używanych usług. Usługa Logic Apps oferuje setki łączników, ale można również utworzyć łącznik niestandardowy.

- **Usługa Azure Service Bus**: [usługi Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) umożliwia bezpieczną i niezawodną obsługę komunikatów. Można użyć komunikatów oddzielenie aplikacji i integrowanie z innymi systemami oparta na komunikatach.

- **Usługa Azure Event Grid**: [usługi Event Grid](../event-grid/overview.md) bezserwerowa platforma publikowania i dostarczać zdarzeń aplikacji.

- **Adres IP**: Usługa Azure API Management ma stały publicznego [adresu IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) i nazwy domeny. Domyślna nazwa domeny jest poddomeną azure-api.net, na przykład contoso.azure-api.net, ale można również skonfigurować [domen niestandardowych](../api-management/configure-custom-domain.md). Logic Apps i Service Bus również mieć publiczny adres IP. Dla bezpieczeństwa w tej architekturze ogranicza dostęp wywoływanie punktów końcowych aplikacji logiki, tylko adres IP usługi API Management. Wywołania do usługi Service Bus są chronione przez sygnaturę dostępu współdzielonego (SAS).

- **Usługa Azure DNS**: [system DNS Azure](https://docs.microsoft.com/azure/dns/) to usługa hostingowa przeznaczona dla domen DNS. Usługa Azure DNS udostępnia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Hostowanie domen na platformie Azure, pozwala na zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, że używasz innych usług platformy Azure. Aby używać niestandardowej nazwy domeny, taką jak contoso.com, Utwórz rekordy DNS mapujące niestandardową nazwę domeny na adres IP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny w usłudze API Management](../api-management/configure-custom-domain.md).

- **Azure Active Directory (Azure AD)**: możesz użyć [usługi Azure AD](https://docs.microsoft.com/azure/active-directory/) lub innego dostawcy tożsamości do uwierzytelniania. Usługa Azure AD umożliwia uwierzytelnianie do uzyskiwania dostępu do punktów końcowych interfejsu API, przekazując [JSON Web Token dla usługi API Management](../api-management/policies/authorize-request-based-on-jwt-claims.md) do sprawdzania poprawności. W przypadku warstw Standard i Premium usługi Azure AD można zabezpieczyć dostęp do portalu dla deweloperów programu interfejsu API zarządzania.

## <a name="patterns"></a>Wzorce 

Ta architektura używa niektóre wzorce, które mają podstawowe znaczenie operacji:

* Istniejący serwer zaplecza interfejsów API protokołu HTTP są publikowane za pośrednictwem portalu dla deweloperów programu interfejsu API zarządzania. W portalu deweloperów, którzy są wewnętrzne dla Twojej organizacji i/i zewnętrzne  
można zintegrować wywołań do tych interfejsów API aplikacji.

* Złożone interfejsy API są tworzone za pomocą aplikacji logiki, które możesz odpowiednio organizować wywołania do oprogramowania jako systemy usługi (SaaS), usług platformy Azure i wszystkie interfejsy API, które są publikowane w usłudze API Management. Aplikacje logiki, również są [opublikowane za pośrednictwem portalu dla deweloperów programu interfejsu API zarządzania](../api-management/import-logic-app-as-api.md).

* Aplikacje używają usługi Azure AD dla [uzyskiwanie tokenu zabezpieczeń protokołu OAuth 2.0](../api-management/api-management-howto-protect-backend-with-aad.md) , są wymagane do uzyskania dostępu do interfejsu API.

* Usługa Azure API Management [weryfikuje token zabezpieczający](../api-management/api-management-howto-protect-backend-with-aad.md) i następnie przekazuje żądanie do aplikacji logiki i interfejsów API zaplecza.

* Kolejki usługi Service Bus platformy Azure są używane do [oddzielenie](../service-bus-messaging/service-bus-messaging-overview.md) działania aplikacji i [wygładzanie wzrostów obciążenia](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Komunikaty są dodawane do kolejki przez usługę logic apps, aplikacje firm trzecich lub (niewyświetlany), publikując kolejki jako interfejsu API protokołu HTTP, za pomocą usługi API Management.

* Gdy komunikaty są dodawane do kolejki usługi Service Bus, generowane są zdarzenia. Zdarzenie jest wyzwalane aplikację logiki, która następnie przetwarza wiadomości.

* Innymi usługami platformy Azure, takich jak Azure Blob Storage i Azure Event Hubs, również publikować zdarzeń usługi Event Grid. Te usługi wyzwalacza aplikacji logiki w celu odbierania zdarzeń, a następnie wykonać kolejne czynności.

## <a name="recommendations"></a>Zalecenia

Konkretne wymagania mogą różnić się od ogólnej architektury, który jest opisany w tym artykule. Użyj zaleceń w tej sekcji jako punktu wyjścia.

### <a name="service-bus-tier"></a>Warstwy usługi Service Bus

Przy użyciu warstwy usługi Service Bus w warstwie Premium, która obsługuje powiadomienia usługi Event Grid. Aby uzyskać więcej informacji, zobacz [cennika usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Event Grid — cennik

Usługa Event Grid korzysta z modelu bez użycia serwera. Rozliczanie jest obliczany na podstawie liczby operacji (zdarzenie wykonania). Aby uzyskać więcej informacji, zobacz [cen usługi Event Grid](https://azure.microsoft.com/pricing/details/event-grid/). Obecnie nie istnieją żadne uwagi warstwy dla usługi Event Grid.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>Użyj PeekLock korzystanie z komunikatów usługi Service Bus

Podczas tworzenia aplikacji logiki, korzystanie z komunikatów usługi Service Bus ma swoją aplikację logiki, użyj [PeekLock](../service-bus-messaging/service-bus-messaging-overview.md#queues) do uzyskiwania dostępu do grupy komunikatów. Korzystając z blokadą PeekLock, aplikacja logiki może wykonać kroki, aby sprawdzić poprawność każdego komunikatu przed ukończenie lub porzucenie komunikatu. To podejście chroni przed utratą przypadkowym wiadomości.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Sprawdź dla wielu obiektów, po aktywowaniu wyzwalacza usługi Event Grid

Po aktywowaniu wyzwalacza usługi Event Grid, ta akcja po prostu oznacza, że "co najmniej jeden z tych spraw wystąpiło zdarzenie." Na przykład kiedy usługi Event Grid wyzwala aplikacji logiki na komunikat, który pojawia się w kolejce usługi Service Bus, aplikacji logiki powinien zawsze zakładaj, że że może być co najmniej jeden komunikat do przetworzenia.

### <a name="region"></a>Region

Aby zminimalizować opóźnienie sieci, należy wybrać dla usługi API Management, Logic Apps i Service Bus tym samym regionie. Ogólnie rzecz biorąc wybierz region, który jest najbliżej Twoich użytkowników.

Grupa zasobów również ma region. Ten region określa miejsce przechowywania metadanych wdrażania i gdzie można wykonać szablon wdrożenia. Aby zwiększyć dostępność podczas wdrażania, umieść grupę zasobów i zasoby, w tym samym regionie.

## <a name="scalability"></a>Skalowalność

Aby osiągnąć skalowalności, warstwy usługi Service Bus w warstwie Premium można skalować liczbę jednostek obsługi komunikatów. Konfiguracje warstwy Premium może zawierać jedno, dwa lub cztery jednostki obsługi komunikatów. Aby uzyskać więcej informacji na temat skalowania usługi Service Bus, zobacz [najlepsze rozwiązania zwiększające wydajność przy użyciu komunikatów usługi Service Bus](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Dostępność

* W przypadku warstw Basic, Standard i Premium Umowa dotycząca poziomu usług (SLA) dla usługi Azure API Management jest aktualnie przez 99,9%. W przypadku konfiguracji warstwy premium przy użyciu wdrażania, który ma co najmniej jedna jednostka w co najmniej dwóch regionach umowy SLA jest 99,95% czasu.

* Umowa SLA dla usługi Azure Logic Apps jest obecnie przez 99,9%.

### <a name="disaster-recovery"></a>Odzyskiwanie po awarii

Aby włączyć trybu failover w przypadku poważnej awarii, należy rozważyć zaimplementowanie odzyskiwania po awarii geograficznej w Service Bus w warstwie Premium. Aby uzyskać więcej informacji, zobacz [odzyskiwania po awarii na geograficznej usługi Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Możliwości zarządzania

Tworzenie oddzielnych grup zasobów w środowisku produkcyjnym, rozwoju i środowisk testowych. Oddzielnych grup zasobów można ułatwić zarządzanie wdrożeniami, usuwanie wdrożeń testowych i przypisywanie praw dostępu.

Podczas przypisywania zasobów do grup zasobów, należy wziąć pod uwagę następujące czynniki:

* **Cykl życia**: ogólnie rzecz biorąc, umieść zasoby, które mają taki sam cykl życia w tej samej grupie zasobów.

* **Dostęp do**: Aby zastosować zasady dostępu do zasobów w grupie, można użyć [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/overview.md).

* **Rozliczenia**: można wyświetlić pakiet zbiorczy kosztów dla grupy zasobów.

* **Warstwę cenową dla usługi API Management**: Użyj warstwa Deweloper przeznaczona dla środowisk programowania i testowania. Aby zminimalizować koszty w środowisku przedprodukcyjnym, wdrożenie repliki środowiska produkcyjnego, uruchom testy i następnie zamknij.

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="deployment"></a>Wdrożenie

* Aby wdrożyć usługi API Management, Logic Apps, usługa Event Grid i Service Bus, użyj [szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Szablony ułatwiają automatyzację wdrożenia przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

* Umieść usługi API Management, wszystkie aplikacje logiki poszczególnych, tematy usługi Event Grid i przestrzeni nazw usługi Service Bus w ich własnych osobnymi szablonami usługi Resource Manager. Korzystając z osobnymi szablonami, można przechowywać zasoby w systemów kontroli źródła. Następnie można wdrożyć te szablony, razem lub oddzielnie w ramach procesu ciągłej integracji/ciągłego wdrażania (CI/CD).

## <a name="diagnostics-and-monitoring"></a>Diagnostyka i monitorowanie

Podobnie jak usługa API Management i Logic Apps można monitorować usługi Service Bus przy użyciu usługi Azure Monitor jest domyślnie włączona. Usługa Azure Monitor udostępnia informacje w zależności od metryki, które są skonfigurowane dla każdej usługi. 

## <a name="security"></a>Bezpieczeństwo

Aby zabezpieczyć usługi Service Bus, należy użyć sygnatury dostępu współdzielonego (SAS). Na przykład, można udzielić użytkownikowi dostępu do zasobów usługi Service Bus przy użyciu określonych praw przy użyciu [uwierzytelniania sygnatury dostępu Współdzielonego](../service-bus-messaging/service-bus-sas.md). Aby uzyskać więcej informacji, zobacz [usługi Service Bus, uwierzytelnianie i autoryzacja](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Jeśli zachodzi potrzeba uwidocznienie kolejki usługi Service Bus jako punktu końcowego HTTP, na przykład, można opublikować nowe wiadomości, usługa API Management umożliwia zabezpieczanie kolejki przy głównym punktem końcowym. Następnie można zabezpieczyć punkt końcowy z certyfikatów lub uwierzytelniania OAuth, zgodnie z potrzebami. Najprościej można zabezpieczyć punkt końcowy używa aplikacji logiki z wyzwalaczem żądania/odpowiedzi HTTP jako pośrednik.

Usługa Event Grid zabezpiecza dostarczania zdarzeń za pomocą kodu sprawdzania poprawności. Jeśli używasz aplikacji logiki z zdarzenia sprawdzania poprawności jest wykonywane automatycznie. Aby uzyskać więcej informacji, zobacz [usługi Event Grid zabezpieczeń i uwierzytelniania](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [integracji dla przedsiębiorstw prosty](/azure/architecture/reference-architectures/enterprise-integration/simple-enterprise-integration)
