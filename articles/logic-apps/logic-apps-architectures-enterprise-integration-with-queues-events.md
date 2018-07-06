---
title: Architektura odwołań usługi Azure Integration Services
description: Architektura referencyjna pokazujący sposób implementacji wzorca Integracja przedsiębiorstwa z Logic Apps, API Management, Usługa Service Bus i Event Grid
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
ms.openlocfilehash: 7d14bbd587b5086348026c41f6551b10809b939a
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859646"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>Integracja w przedsiębiorstwie z kolejkami i zdarzenia: Architektura referencyjna

## <a name="overview"></a>Przegląd

Ta architektura referencyjna pokazuje zestaw sprawdzonych rozwiązań dotyczących integracji aplikacji, która korzysta z usług integracji platformy Azure. Ta architektura może służyć jako tej podstawie wiele wzorców innej aplikacji wymagających interfejsów API protokołu HTTP, przepływu pracy i aranżacji.

*Istnieje wiele możliwych aplikacji technologii integracji z prostego punktu do punktu aplikacji do usługi Service bus pełną enterprise. W tej serii architektury określa części komponentów wielokrotnego użytku, które mogą mieć zastosowanie do tworzenia aplikacji ogólnego integracji — architektów należy wziąć pod uwagę określonych składników, należy zaimplementować dla swoich aplikacji i infrastruktury.*

![Diagram architektury — integracja w przedsiębiorstwie z kolejkami i zdarzenia](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>Architektura

Ta architektura **opiera się na** [integracji dla przedsiębiorstw proste](logic-apps-architectures-simple-enterprise-integration.md) architektury. [Zalecenia dotyczące architektury przedsiębiorstwa proste](logic-apps-architectures-simple-enterprise-integration.md#recommendations) również zgłosić się tutaj, ale została pominięta z [zalecenia](#recommendations) w tym dokumencie w celu skrócenia programu. Zawiera ona następujące składniki:

- Grupa zasobów. [Grupa zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) to kontener logiczny dla zasobów platformy Azure.
- Usługa Azure API Management. [Usługa Azure API Management](https://docs.microsoft.com/azure/api-management/) to w pełni zarządzana platforma publikowanie, zabezpieczanie i przekształcanie interfejsów API protokołu HTTP.
- Portalu dla deweloperów programu usługi Azure API Management. Każde wystąpienie usługi Azure API Management zawiera [portalu dla deweloperów](https://docs.microsoft.com/azure/api-management/api-management-customize-styles), zapewniając dostęp do dokumentacji, przykładów kodu i możliwość testowania interfejsu API.
- Usługi Azure Logic Apps. [Usługa Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) bezserwerowa platforma do tworzenia przepływów pracy przedsiębiorstwa i integracji.
- Łączniki. [Łączniki](https://docs.microsoft.com/azure/connectors/apis-list) są używane przez usługę Logic Apps, połączyć się z często używanych usług. Usługa Logic Apps jest już setki różne łączniki, ale ich można również utworzyć za pomocą łącznika niestandardowego.
- Usługi Azure Service Bus. [Usługa Service Bus](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) umożliwia bezpieczną i niezawodną obsługę komunikatów. Komunikaty można cofnąć Połącz aplikacji od siebie nawzajem i integrację z innymi systemami oparta na komunikatach.
- Usługi Azure Event Grid. [Usługa Event Grid](https://docs.microsoft.com/azure/event-grid/overview) bezserwerowa platforma publikowania i dostarczać zdarzeń aplikacji.
- Adres IP. Usługa Azure API Management ma stały publicznego [adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) i nazwy domeny. Nazwa domeny jest poddomeną azure-api.net, takich jak contoso.azure-api.net. Logic Apps i Service Bus również mieć publiczny adres IP; Jednak w ramach tej architektury będziemy ograniczać przydziału obowiązków dostępu wywołania punktów końcowych aplikacji logiki, aby tylko Zarządzanie adresami IP dla interfejsu API (zabezpieczenia). Wywołania do usługi Service Bus są chronione przez sygnaturę dostępu współdzielonego.
- Usługa DNS platformy Azure. [Usługa Azure DNS](https://docs.microsoft.com/azure/dns/) to usługa hostingowa przeznaczona dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure. Aby używać niestandardowej nazwy domeny (np. contoso.com), Utwórz rekordy DNS mapujące niestandardową nazwę domeny na adres IP. Aby uzyskać więcej informacji zobacz Konfigurowanie niestandardowej nazwy domeny w usłudze Azure API Management.
- Azure Active Directory (Azure AD). Użyj [usługi Azure AD](https://docs.microsoft.com/azure/active-directory/) lub innego dostawcy tożsamości do uwierzytelniania. Usługa Azure AD umożliwia uwierzytelnianie do punktów końcowych dostęp do interfejsu API (przez przekazanie [JSON Web Token dla usługi API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) do sprawdzania poprawności) i można zabezpieczyć dostęp do portalu dla deweloperów interfejsu API zarządzania (tylko warstw standardowa i Premium).

Ta architektura ma kilka podstawowych wzorców w celu jego działania:

1. Istniejącego zaplecza interfejsów API protokołu HTTP są publikowane za pośrednictwem portalu dla deweloperów interfejsu API zarządzania, dzięki czemu deweloperzy mogą (albo wewnętrzne dla Twojej organizacji, zewnętrznego lub obie) do integracji wywołań do tych interfejsów API w aplikacjach.
2. Złożone interfejsy API są tworzone przy użyciu aplikacji logiki organizowanie wywołania z systemami SAAS, usług platformy Azure i dowolnych interfejsów API opublikowane usłudze API Management. [Logic Apps są ponadto publikowane](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) za pośrednictwem portalu dla deweloperów interfejsu API zarządzania.
3. Aplikacje [uzyskać token zabezpieczający uwierzytelniania OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) niezbędne do uzyskania dostępu do interfejsu API za pomocą usługi Azure Active Directory.
4. Usługa Azure API Management [weryfikuje token zabezpieczający](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)i przekazuje żądanie do aplikacji interfejsu API/logiki wewnętrznej bazy danych.
5. Kolejki usługi Service Bus są używane do [rozdzielenie](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) działania aplikacji i [smooth wzrostów obciążenia](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). Komunikaty są dodawane do kolejki przez usługę Logic Apps, 3 aplikacje innych firm, lub (bez ilustracji), publikując kolejki jako interfejsu API protokołu HTTP, za pomocą usługi API Management.
6. Gdy komunikaty są dodawane do kolejki usługi Service Bus, generowane są zdarzenia. Aplikacja logiki jest wyzwalana przez to zdarzenie i przetwarza wiadomość.
7. Podobnie innych usług platformy Azure (np. usługi Blob Storage, Centrum zdarzeń) również publikować zdarzeń usługi Event Grid. Te wyzwalacza aplikacji logiki, aby odbierać zdarzenia i wykonać kolejne czynności.

## <a name="recommendations"></a>Zalecenia

Konkretne wymagania mogą różnić się od ogólnego opisanej tu architektury. Użyj zaleceń w tej sekcji jako punktu wyjścia.

### <a name="service-bus-tier"></a>Warstwy usługi Service Bus

Użyj usługi Service Bus w warstwie premium, ponieważ w ten sposób realizowany powiadomienia o zdarzeniach siatki aktualnie (Obsługa we wszystkich warstwach oczekiwane zachowanie). Zobacz [cennika usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Event Grid — cennik

Usługa Event Grid działa przy użyciu modelu bez użycia serwera — rozliczeń jest obliczany na podstawie liczby operacji (wykonywanie zdarzeń). Zobacz [cen usługi Event Grid](https://azure.microsoft.com/pricing/details/event-grid/) Aby uzyskać więcej informacji. Obecnie nie są żadne uwagi warstwy dla usługi Event Grid.

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>Użyj PeekLock, jeśli korzystanie z komunikatów usługi Service Bus

Podczas tworzenia aplikacji logiki, aby używać komunikatów usługi Service Bus, użyj [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) w aplikacji logiki dostępu do grupy komunikatów. Aplikacja logiki może następnie wykonać kroki, aby sprawdzić poprawność każdego komunikatu przed ukończenie lub porzucenie. To podejście chroni przed utratą przypadkowym wiadomości.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Sprawdź dla wielu obiektów, po aktywowaniu wyzwalacza usługi Event Grid

Wyzwalanie po prostu "Wystąpił co najmniej 1 te rzeczy" oznacza wyzwalaczy siatki zdarzeń. Na przykład kiedy usługi Event Grid wyzwala aplikacji logiki w komunikacie w kolejce usługi Service Bus, aplikację logiki zawsze powinien zakłada, że co najmniej jeden komunikat do przetworzenia.

### <a name="region"></a>Region

Aprowizacja usługi API Management, Logic Apps i Service Bus, w tym samym regionie, aby zminimalizować opóźnienie sieci. W większości przypadków należy wybrać region najbliżej Twoich użytkowników.

Grupa zasobów również ma region, który określa, gdzie przechowywane są metadane wdrożenia, i gdzie jest wykonywane Szablon wdrożenia. Umieść grupę zasobów i jej zasoby w tym samym regionie. Może to zwiększyć dostępność podczas wdrażania.

## <a name="scalability-considerations"></a>Zagadnienia dotyczące skalowalności

Usługa Azure Service Bus w warstwie premium można skalowalnego w poziomie liczby jednostek obsługi komunikatów do osiągnięcia skalowalności. Warstwie Premium mogą zawierać 1, 2 lub 4 jednostki obsługi komunikatów. Aby uzyskać dalsze wskazówki na temat skalowania usługi Azure Service Bus, zobacz [najlepsze rozwiązania zwiększające wydajność przy użyciu komunikatów usługi Service Bus](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

W czasie pisania Umowa dotycząca poziomu usług (SLA) dla usługi Azure API Management ma wartość 99,9% w przypadku warstw Basic, Standard i Premium. Konfiguracje warstwy Premium przy użyciu wdrożenia co najmniej jedna jednostka w co najmniej dwóch regionach mają umową SLA gwarantującą dostępność przez 99,95%.

W czasie pisania Umowa dotycząca poziomu usług (SLA) dla usługi Azure Logic Apps jest 99,9%.

### <a name="disaster-recovery"></a>Odzyskiwanie po awarii

Rozważ zaimplementowanie odzyskiwania po awarii Georeplikacji w warstwie premium usługi Service Bus w celu włączenia trybu failover w przypadku poważnej awarii. Przeczytaj więcej na temat [odzyskiwania po awarii geograficznej usługi Azure Service Bus](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability-considerations"></a>Zagadnienia dotyczące możliwości zarządzania

Tworzenie oddzielnych grup zasobów w środowisku produkcyjnym, rozwoju i środowisk testowych. Ułatwia to zarządzanie wdrożeniami, usuwanie wdrożeń testowych i przypisywanie praw dostępu.
Podczas przydzielania zasobów do grup zasobów rozważ następujące kwestie:

- Cykl życia. Ogólnie rzecz biorąc, umieszczaj zasoby z tym samym cyklem życia w tej samej grupie zasobów.
- Dostęp. Możesz użyć [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) (RBAC), aby zastosować zasady dostępu do zasobów w grupie.
- Rozliczenia. Możesz wyświetlać zestawienia kosztów dla grupy zasobów.
- Warstwa cenowa zarządzania interfejsami API — zaleca się używanie warstwa Deweloper przeznaczona dla środowisk deweloperskich i testowych. Do produkcji wstępnej zalecamy wdrożenie repliki środowiska produkcyjnego, uruchamiania testów, a następnie wyłączając zminimalizować koszty.

Aby uzyskać więcej informacji, zobacz [grupy zasobów](../azure-resource-manager/resource-group-overview.md) Przegląd.

### <a name="deployment"></a>Wdrożenie

Firma Microsoft zaleca użycie [szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) do wdrożenia usługi Azure API Management, Logic Apps, usługa Event Grid i Service Bus. Szablony ułatwiają zautomatyzowanie wdrożeń za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia (CLI) platformy Azure.

Zaleca się umieszczenie usługi Azure API Management, wszelkie osobnych Logic Apps, tematy usługi Event Grid i przestrzeniami nazw magistrali usług w ich własnych osobnymi szablonami usługi Resource Manager. Umożliwi to ich przechowywania, systemów kontroli źródła. Szablony te można następnie wdrożyć, razem lub oddzielnie w ramach ciągłej integracji/ciągłego (CI/CD) procesu wdrażania.

### <a name="diagnostics-and-monitoring"></a>Diagnostyka i monitorowanie

Service Bus, podobnie jak usługa API Management i Logic Apps można monitorować za pomocą usługi Azure Monitor. Usługa Azure Monitor jest domyślnie włączona i będzie zapewniać informacje w oparciu o różne metryki skonfigurowane dla każdej usługi.

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Zabezpieczenia usługi Service Bus przy użyciu podpisu dostępu współdzielonego. [Uwierzytelniania sygnatury dostępu Współdzielonego](../service-bus-messaging/service-bus-sas.md) umożliwia udzielenie użytkownikom dostępu do zasobów usługi Service Bus, przy użyciu określonych praw. Przeczytaj więcej na temat [usługi Service Bus, uwierzytelnianie i autoryzacja](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Ponadto należy kolejki usługi Service Bus muszą być udostępniane jako punktu końcowego HTTP (aby umożliwić publikowanie nowych wiadomości), usługa API Management powinien być używany do jej zabezpieczeniem w głównym punktem końcowym. To może następnie zostać zabezpieczony za pomocą certyfikatów i autoryzacji OAuth zgodnie z potrzebami. Najprostszym sposobem na to jest przy użyciu aplikacji logiki z wyzwalaczem żądania/odpowiedzi HTTP jako pośrednik.

Usługa Event Grid zabezpiecza dostarczania zdarzeń za pomocą kodu sprawdzania poprawności. Użycie LogicApps zużyje zdarzenia, jest to wykonywane automatycznie. Zobacz więcej szczegółów na temat [usługi Event Grid zabezpieczeń i uwierzytelniania](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Kolejne kroki

- [Integracja dla przedsiębiorstw prosty](logic-apps-architectures-simple-enterprise-integration.md)