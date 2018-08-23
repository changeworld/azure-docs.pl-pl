---
title: Usługa Azure architektury referencyjnej integracji przedsiębiorstwa usług Integration Services
description: W tym artykule opisano architekturę referencyjną, która pokazuje sposób implementacji wzorca integracji przedsiębiorstwa przy użyciu Logic Apps, API Management, Usługa Service Bus i Event Grid.
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
ms.openlocfilehash: 9eef382ea264bcf9e59dcc408d14a59355b0369b
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2018
ms.locfileid: "42445681"
---
# <a name="reference-architecture-enterprise-integration-with-queues-and-events"></a>Architektura referencyjna: integracja w przedsiębiorstwie z kolejkami i zdarzenia

Następujące architektura referencyjna pokazuje zestaw sprawdzonych rozwiązań, które można zastosować do integracji aplikacji, która korzysta z usług integracji platformy Azure. Architektura może służyć jako podstawa wielu wzorców innej aplikacji, które wymagają interfejsów API protokołu HTTP, przepływu pracy i aranżacji.

![Diagram architektury — integracja w przedsiębiorstwie z kolejkami i zdarzenia](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

*Istnieje wiele możliwych aplikacji w technologii integracji. Mogą należeć do zakresu z poziomu aplikacji point-to-point proste do pełnej wersji enterprise aplikacji usługi Azure Service Bus. Seria architektury opisano części komponentów wielokrotnego użytku, które mogą być stosowane do tworzenia aplikacji ogólnego integracji. Architekci należy wziąć pod uwagę składniki, które potrzebują do wykonania dla swoich aplikacji i infrastruktury.*

## <a name="architecture"></a>Architektura

Architektura *opiera się na* [integracji dla przedsiębiorstw proste](logic-apps-architectures-simple-enterprise-integration.md) architektury. [Zalecenia dotyczące architektury przedsiębiorstwa proste](logic-apps-architectures-simple-enterprise-integration.md#recommendations) zgłosić się tutaj. Zostaną one pominięte [zalecenia](#recommendations) w tym artykule w celu skrócenia programu. 

Architektura zawiera następujące składniki:

- **Grupa zasobów**. [Grupa zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) to kontener logiczny dla zasobów platformy Azure.
- **Usługa Azure API Management**. [Usługa API Management](https://docs.microsoft.com/azure/api-management/) to w pełni zarządzana platforma, która umożliwia publikowanie, zabezpieczanie i Przekształć interfejsy API protokołu HTTP.
- **Azure portal dla deweloperów programu interfejsu API zarządzania**. Każde wystąpienie usługi Azure API Management jest dostarczany z dostępem do [portalu dla deweloperów](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). Portalu dla deweloperów programu interfejsu API zarządzania zapewnia dostęp do dokumentacji i przykładowy kod. Interfejsy API można sprawdzić w portalu dla deweloperów.
- **Usługi Azure Logic Apps**. [Usługa Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) jest platformą bez użycia serwera, który jest używany do tworzenia przepływów pracy przedsiębiorstwa i integracji.
- **Łączniki**. Usługa Logic Apps używa [łączników](https://docs.microsoft.com/azure/connectors/apis-list) połączyć się z często używane usługi. Usługa Logic Apps jest już setki różne łączniki, ale można również utworzyć łącznik niestandardowy.
- **Usługi Azure Service Bus**. [Usługa Service Bus](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) umożliwia bezpieczną i niezawodną obsługę komunikatów. Komunikatów może służyć do rozdzielania aplikacji i integrację z innymi systemami oparta na komunikatach.
- **Usługi Azure Event Grid**. [Usługa Event Grid](https://docs.microsoft.com/azure/event-grid/overview) bezserwerowa platforma, która jest używana do publikowania i dostarczać zdarzeń aplikacji.
- **Adres IP**. Usługa Azure API Management ma stały publicznego [adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) i nazwy domeny. Domyślna nazwa domeny jest poddomeną azure-api.net, takich jak contoso.azure-api.net, ale [domen niestandardowych](https://docs.microsoft.com/azure/api-management/configure-custom-domain) można również skonfigurować. Logic Apps i Service Bus również mieć publiczny adres IP. Jednak w ramach tej architektury, będziemy ograniczać przydziału obowiązków dostępu do wywoływania aplikacji logiki punktów końcowych tylko adres IP usługi API Management (dla zabezpieczeń). Wywołania do usługi Service Bus są chronione przez sygnaturę dostępu współdzielonego (SAS).
- **Azure DNS**. [Usługa Azure DNS](https://docs.microsoft.com/azure/dns/) to usługa hostingowa przeznaczona dla domen DNS. Usługa Azure DNS udostępnia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Hostowanie domen na platformie Azure, pozwala na zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, że używasz innych usług platformy Azure. Aby użyć niestandardowej nazwy domeny (np. contoso.com), należy utworzyć rekordy DNS mapujące niestandardową nazwę domeny na adres IP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie niestandardowej nazwy domeny w usłudze API Management](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD)**. Użyj [usługi Azure AD](https://docs.microsoft.com/azure/active-directory/) lub innego dostawcy tożsamości do uwierzytelniania. Usługa Azure AD umożliwia uwierzytelnianie do uzyskiwania dostępu do punktów końcowych interfejsu API, przekazując [JSON Web Token dla usługi API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) do sprawdzania poprawności. Usługa Azure AD można zabezpieczyć dostęp do portalu dla deweloperów programu interfejsu API zarządzania (tylko warstw standardowa i Premium).

Architektura zawiera niektóre wzorce, które mają podstawowe znaczenie jego działania:

* Istniejący serwer zaplecza interfejsów API protokołu HTTP są publikowane za pośrednictwem portalu dla deweloperów programu interfejsu API zarządzania. W portalu deweloperów (albo wewnętrzne dla Twojej organizacji i/lub zewnętrznego) można zintegrować wywołań do tych interfejsów API aplikacji.
* Złożone interfejsy API są tworzone za pomocą usługi logic apps i poprzez organizowanie wywołania do oprogramowania jako systemy usługi (SaaS), usług platformy Azure i wszystkie interfejsy API, które są publikowane w usłudze API Management. [Aplikacje logiki są ponadto publikowane](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) za pośrednictwem portalu dla deweloperów programu interfejsu API zarządzania.
- Aplikacje używają usługi Azure AD do [uzyskać token zabezpieczający uwierzytelniania OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) , są wymagane do uzyskania dostępu do interfejsu API.
- Usługa API Management [weryfikuje token zabezpieczający](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) i następnie przekazuje żądanie do aplikacji logiki i interfejsów API zaplecza.
- Kolejki usługi Service Bus są używane do [rozdzielenie](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) działania aplikacji i [smooth wzrostów obciążenia](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Komunikaty są dodawane do kolejki przez usługę logic apps, aplikacje innych firm lub (bez ilustracji), publikując kolejki jako interfejsu API protokołu HTTP, za pomocą usługi API Management.
- Gdy komunikaty są dodawane do kolejki usługi Service Bus, generowane są zdarzenia. Aplikacja logiki jest wyzwalana przez zdarzenie. Aplikacja logiki następnie przetwarza wiadomości.
- Innych usług platformy Azure (na przykład usługi Azure Blob storage i Azure Event Hubs) również publikować zdarzeń usługi Event Grid. Te usługi wyzwalacza aplikacji logiki w celu odbierania zdarzeń, a następnie wykonać kolejne czynności.

## <a name="recommendations"></a>Zalecenia

Konkretne wymagania mogą różnić się od ogólnej architektury, który jest opisany w tym artykule. Użyj zaleceń w tej sekcji jako punktu wyjścia.

### <a name="service-bus-tier"></a>Warstwy usługi Service Bus

Przy użyciu warstwy usługi Service Bus w warstwie Premium. Premier warstwa obsługuje powiadomienia usługi Event Grid. Aby uzyskać więcej informacji, zobacz [cennika usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Event Grid — cennik

Usługa Event Grid korzysta z modelu bez użycia serwera. Rozliczanie jest obliczany na podstawie liczby operacji (wykonywanie zdarzeń). Aby uzyskać więcej informacji, zobacz [cen usługi Event Grid](https://azure.microsoft.com/pricing/details/event-grid/). Obecnie nie istnieją żadne uwagi warstwy dla usługi Event Grid.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>Użyj PeekLock korzystanie z komunikatów usługi Service Bus

Podczas tworzenia aplikacji logiki, korzystanie z komunikatów usługi Service Bus używa [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) w aplikacji logiki dostępu do grupy komunikatów. Korzystając z blokadą PeekLock, aplikacja logiki może wykonać kroki, aby sprawdzić poprawność każdego komunikatu przed ukończenie lub porzucenie komunikatu. To podejście chroni przed utratą przypadkowym wiadomości.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Sprawdź dla wielu obiektów, po aktywowaniu wyzwalacza usługi Event Grid

Po aktywowaniu wyzwalacza usługi Event Grid, oznacza to po prostu czy "co najmniej jeden z tych spraw się stało." Na przykład kiedy usługi Event Grid wyzwala aplikacji logiki na komunikat, który pojawia się w kolejce usługi Service Bus, aplikacji logiki powinien zawsze zakładaj, że że może być co najmniej jeden komunikat do przetworzenia.

### <a name="region"></a>Region

Aprowizacja usługi API Management, Logic Apps i Service Bus, w tym samym regionie, aby zminimalizować opóźnienie sieci. Ogólnie rzecz biorąc wybierz region, który jest najbliżej Twoich użytkowników.

Grupa zasobów również ma region. Region określa miejsce przechowywania metadanych wdrażania i gdzie jest wykonywany z szablonu wdrożenia. Umieść grupę zasobów i jej zasoby w tym samym regionie, zwiększyć dostępność podczas wdrażania.

## <a name="scalability"></a>Skalowalność

Warstwy usługi Service Bus w warstwie Premium można skalować liczbę jednostek obsługi komunikatów, osiągnięcie skalowalności. Konfiguracje warstwy Premium może zawierać jedno, dwa lub cztery jednostki obsługi komunikatów. Aby uzyskać więcej informacji na temat skalowania usługi Service Bus, zobacz [najlepsze rozwiązania zwiększające wydajność przy użyciu komunikatów usługi Service Bus](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Dostępność

Obecnie umowę dotyczącą poziomu usług (SLA) dla usługi Azure API Management jest 99,9% w przypadku warstw Basic, Standard i Premium. Konfiguracje warstwy Premium przy użyciu wdrożenia co najmniej jedna jednostka w co najmniej dwóch regionach mają umową SLA gwarantującą dostępność przez 99,95%.

Obecnie umowa SLA dla usługi Azure Logic Apps jest 99,9%.

### <a name="disaster-recovery"></a>Odzyskiwanie po awarii

Rozważ zaimplementowanie odzyskiwania po awarii geograficznej w Service Bus w warstwie Premium w celu włączenia trybu failover w przypadku poważnej awarii. Aby uzyskać więcej informacji, zobacz [odzyskiwania po awarii na geograficznej usługi Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Możliwości zarządzania

Tworzenie oddzielnych grup zasobów w środowisku produkcyjnym, rozwoju i środowisk testowych. Oddzielnych grup zasobów ułatwia to zarządzanie wdrożeniami, usuwanie wdrożeń testowych i przypisywanie praw dostępu.

Podczas przypisywania zasobów do grup zasobów, należy wziąć pod uwagę następujące czynniki:

- **Cykl życia**. Ogólnie rzecz biorąc umieszczaj zasoby, które mają taki sam cykl życia w tej samej grupie zasobów.
- **Dostęp do**. Możesz użyć [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) (RBAC), aby zastosować zasady dostępu do zasobów w grupie.
- **Rozliczenia**. Można wyświetlić pakiet zbiorczy kosztów dla grupy zasobów.
- **Warstwę cenową dla usługi API Management**. Firma Microsoft zaleca się używanie warstwy Deweloper do tworzenia aplikacji i środowisk testowych. W środowisku przedprodukcyjnym zalecamy wdrożenie repliki środowiska produkcyjnego, uruchamiania testów, a następnie wyłączając zminimalizować koszty.

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Wdrożenie

Firma Microsoft zaleca użycie [szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) do wdrożenia usługi API Management, Logic Apps, usługa Event Grid i Service Bus. Szablony ułatwiają zautomatyzowanie wdrożeń za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Zaleca się umieszczenie usługi API Management, wszystkie aplikacje logiki poszczególnych, tematy usługi Event Grid i przestrzeni nazw usługi Service Bus w swoje własne, oddzielnym szablonów usługi Resource Manager. Korzystając z osobnymi szablonami, można przechowywać zasoby w systemów kontroli źródła. Następnie można wdrożyć te szablony, razem lub oddzielnie w ramach procesu ciągłej integracji/ciągłego wdrażania (CI/CD).

### <a name="diagnostics-and-monitoring"></a>Diagnostyka i monitorowanie

Podobnie jak usługa API Management i Logic Apps można monitorować usługi Service Bus przy użyciu usługi Azure Monitor. Usługa Azure Monitor udostępnia informacje w zależności od metryki, które są skonfigurowane dla każdej usługi. Usługa Azure Monitor jest domyślnie włączona.

## <a name="security"></a>Bezpieczeństwo

Zabezpieczanie usługi Service Bus przy użyciu sygnatury dostępu Współdzielonego. Możesz użyć [uwierzytelniania sygnatury dostępu Współdzielonego](../service-bus-messaging/service-bus-sas.md) udzielić użytkownikowi dostępu do zasobów usługi Service Bus przy użyciu określonych praw. Aby uzyskać więcej informacji, zobacz [usługi Service Bus, uwierzytelnianie i autoryzacja](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Jeśli kolejki usługi Service Bus musi być udostępniane jako punktu końcowego HTTP (umożliwiające publikowanie nowych wiadomości), należy użyć usługi API Management zabezpieczyć go przez głównym punktem końcowym. Punkt końcowy może następnie zostać zabezpieczony za pomocą certyfikatów i autoryzacji OAuth zgodnie z potrzebami. Najprostszym sposobem bezpiecznego punktu końcowego jest za pomocą aplikacji logiki z wyzwalaczem żądania/odpowiedzi HTTP jako pośrednik.

Usługa Event Grid zabezpiecza dostarczania zdarzeń za pomocą kodu sprawdzania poprawności. Jeśli używasz aplikacji logiki z zdarzenia sprawdzania poprawności jest wykonywane automatycznie. Aby uzyskać więcej informacji, zobacz [usługi Event Grid zabezpieczeń i uwierzytelniania](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [integracji dla przedsiębiorstw proste](logic-apps-architectures-simple-enterprise-integration.md).
