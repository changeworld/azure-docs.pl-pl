---
title: Azure architektura referencyjna struktury usług Integration Services
description: Architektura referencyjna przedstawiająca sposób implementacji wzorca integracji przedsiębiorstwa z Logic Apps, zarządzanie interfejsami API usługi Service Bus i siatki zdarzeń
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
ms.openlocfilehash: db3350b3c70f6e6f35949e8423334061849b7b37
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232491"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>Integracja przedsiębiorstwa z kolejki i zdarzenia: odwołanie architektury

## <a name="overview"></a>Przegląd

Informacje o architekturze wzorcowej zawiera zestaw sprawdzonych rozwiązań dotyczących integracji aplikacji, która używa usług Azure Integration Services. Taka architektura może służyć jako tej podstawie wzorców innej aplikacji wymagających interfejsy API protokołu HTTP, przepływu pracy i aranżacji.

*Istnieje wiele aplikacji możliwych technologii integracji z prostego punktu do punktu aplikacji usługi service bus pełne przedsiębiorstwa. Ta seria architektura określa części komponentów wielokrotnego użytku do tworzenia aplikacji integracji — architektów należy wziąć pod uwagę składniki, których potrzebują dla aplikacji i infrastruktury.*

![Diagram architektury - integracji przedsiębiorstwa z kolejki i zdarzenia](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>Architektura

Taka architektura opiera się na taki, jaki podano w [integracji przedsiębiorstwa proste](logic-apps-architectures-simple-enterprise-integration.md). Zawiera ona następujące składniki:

- Grupy zasobów. Grupa zasobów jest kontenerem logicznym dla zasobów platformy Azure.
- Zarządzanie interfejsami API Azure. Azure API Management jest w pełni zarządzana platforma publikowania, zabezpieczanie i przekształcanie interfejsy API protokołu HTTP.
- Azure API Management Developer Portal. Każde wystąpienie usługi Azure API Management jest dostarczany z Developer Portal zapewniające dostęp do dokumentacji, przykłady kodu i możliwość testowania interfejsu API.
- Aplikacje logiki platformy Azure. Logic Apps to niekorzystającą platforma do tworzenia przepływów pracy przedsiębiorstwa i integracji.
- Łączniki. Łączniki są używane przez aplikacje logiki do łączenia się najczęściej używanymi usługami. Logic Apps ma już setki różnych łączniki, ale ich można również tworzyć przy użyciu niestandardowego łącznika.
- Usługa Azure Service Bus. Usługa Service Bus zapewnia bezpieczny i niezawodny wiadomości. Do obsługi komunikatów można cofnąć połączyć aplikacje od siebie oraz integrują się z innych systemów opartych na procesorze komunikatów.
- Siatka zdarzeń platformy Azure. Siatka zdarzeń jest niekorzystającą platforma publikowania i dostarczania zdarzeń aplikacji.
- Adres IP. Usługa Azure API Management ma publiczny adres IP i nazwy domeny. Nazwa domeny jest poddomeną azure api.net, takich jak contoso.azure api.net. Aplikacje logiki i usługi Service Bus również mieć publicznego adresu IP; Jednak w ramach tej architektury możemy ograniczyć dostęp do wywołania punktów końcowych aplikacji logiki do tylko Zarządzanie adresami IP z interfejsu API (dla zabezpieczeń). Wywołania usługi Service Bus są zabezpieczone przez sygnaturę dostępu współdzielonego.
- Usługa DNS platformy Azure. Usługa DNS platformy Azure to Usługa hostingu domen DNS, rozpoznawania nazw przy użyciu infrastruktury Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure. Aby użyć niestandardowej nazwy domeny (np. contoso.com) należy utworzyć rekordy DNS, które mapują nazwy domeny niestandardowej z adresem IP. Aby uzyskać więcej informacji zobacz Konfigurowanie niestandardowej nazwy domeny w usłudze Azure API Management.
- Azure Active Directory (Azure AD). Użyj usługi Azure AD lub innego dostawcy tożsamości w celu uwierzytelniania. Usługi Azure AD umożliwia uwierzytelnianie do punktów końcowych dostępu do interfejsu API (przez przekazanie żetonu Web JSON dla interfejsu API zarządzania w celu zweryfikowania) i może uniemożliwić dostęp do interfejsu API zarządzania portalu dla deweloperów (tylko warstwy standardowa i Premium).

Taka architektura ma niektórych podstawowych wzorców w celu jego działania:

1. Interfejsy API protokołu HTTP są publikowane za pośrednictwem interfejsu API zarządzania portalu dla deweloperów, co umożliwia deweloperom istniejącej wewnętrznej bazy danych (albo wewnętrzne dla organizacji, zewnętrzne lub obie) do integracji wywołań do tych interfejsów API w aplikacjach.
2. Złożone interfejsy API są tworzone przy użyciu logiki aplikacji. organizowanie wywołania do systemów SAAS, usług platformy Azure i wszystkie interfejsy API opublikowane na zarządzanie interfejsami API. Aplikacje logiki są też publikowane za pośrednictwem interfejsu API zarządzania Developer Portal.
3. Aplikacje uzyskania tokenu zabezpieczającego OAuth 2.0 niezbędne do uzyskania dostępu do interfejsu API przy użyciu usługi Azure Active Directory.
4. Zarządzanie interfejsami API Azure weryfikuje token zabezpieczający, a następnie przekazuje żądanie do aplikacji interfejsu API/logiki wewnętrznej bazy danych.
5. Kolejki usługi Service Bus są używane do oddziel działania aplikacji i smooth największego obciążenia. Wiadomości są dodawane do kolejki przez logikę aplikacji, aplikacje firm 3, lub (nie przedstawianych) publikując kolejki jako interfejs API HTTP za pośrednictwem interfejsu API zarządzania.
6. Po dodaniu wiadomości do kolejki usługi Service Bus, generowane zdarzenie. Aplikacja logiki jest wyzwalane przez to zdarzenie i przetwarza wiadomość.
7. Podobnie innymi usługami Azure (np. magazyn obiektów Blob, Centrum zdarzeń) również publikować zdarzenia do zdarzenia siatki. Wyzwolenie tych aplikacji logiki, aby odbierać zdarzenia i wykonać kolejne czynności.

## <a name="recommendations"></a>Zalecenia

Wymagania rzeczywiste mogą różnić się od dotyczących opisanej tu architektury. Użyj zaleceń w tej sekcji jako punktu wyjścia.

### <a name="service-bus-tier"></a>Warstwy usługi Service Bus

Użyj warstwy premium usługi Service Bus to obsługuje aktualnie powiadomień o zdarzeniach siatki (oczekiwana jest obsługa przez wszystkie warstwy). Zobacz [cennik usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Cennik siatki zdarzeń

Siatka zdarzeń działa przy użyciu modelu niekorzystającą — rozliczeń jest obliczany na podstawie liczby operacji (wykonywania zdarzenia). Zobacz [cennik siatki zdarzeń](https://azure.microsoft.com/pricing/details/event-grid/) Aby uzyskać więcej informacji. Obecnie nie istnieją żadne uwagi dotyczące warstwy siatki zdarzeń.

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>Użyj PeekLock, gdy korzystanie z komunikatów usługi Service Bus

Podczas tworzenia aplikacji logiki, aby korzystać z komunikatów usługi Service Bus, użyj [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) w aplikacji logiki dostępu do grupy wiadomości. Aplikację logiki można następnie wykonaj kroki, aby przed Kończenie lub porzucanie poprawność każdego komunikatu. To rozwiązanie chroni przed utratą przypadkowemu wiadomości.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Sprawdź, czy wiele obiektów, kiedy wyzwalacza zdarzenia siatki

Wyzwalacze zdarzeń siatki, wyzwalania po prostu oznacza, że "Wystąpił co najmniej 1 elementy". Na przykład kiedy siatki zdarzeń wyzwala aplikacji logiki w komunikacie w kolejki usługi Service Bus, aplikację logiki powinien zawsze przyjęto założenie, że może to być jeden lub więcej komunikatów do przetworzenia.

### <a name="region"></a>Region

Dostarczać interfejs API zarządzania Logic Apps i usługi Service Bus, w tym samym regionie, aby zminimalizować opóźnienie sieci. W większości przypadków należy wybrać region najbliżej Twoich użytkowników.

Grupa zasobów ma również region, który określa, gdzie są przechowywane metadane wdrażania, i w którym szablon wdrożenia jest przeprowadzana z. Umieść grupę zasobów i jej zasoby w tym samym regionie. Może to zwiększyć dostępność podczas wdrażania.

## <a name="scalability-considerations"></a>Zagadnienia dotyczące skalowalności

Warstwy premium usługi Azure Service Bus można skalowalnego w poziomie liczbę jednostek obsługi komunikatów do osiągnięcia zwiększenia skalowalności. Premium mogą mieć 1, 2 lub 4 jednostki do obsługi komunikatów. Aby uzyskać dalsze informacje na temat skalowania usługi Azure Service Bus, zobacz [najlepsze rozwiązania dotyczące poprawy wydajności przy użyciu usługi magistrali komunikatów](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

W momencie pisania umowę dotyczącą poziomu usług (SLA) dla usługi Azure API Management jest 99,9% w warstwach podstawowa, standardowa i Premium. Konfiguracje warstwy Premium z wdrożeniem co najmniej jedną jednostkę w co najmniej dwóch regionach mają SLA 99,95%.

W momencie pisania umowę dotyczącą poziomu usług (SLA) dla usługi Azure Logic Apps jest 99,9%.

### <a name="disaster-recovery"></a>Odzyskiwanie po awarii

Rozważ zaimplementowanie odzyskiwania po awarii geograficznie w usługi Service Bus premium w celu włączenia trybu failover w przypadku poważnej awarii. Przeczytaj więcej na temat [Azure Service Bus geograficznie-odzyskiwaniem](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability-considerations"></a>Zagadnienia dotyczące możliwości zarządzania

Tworzenie oddzielnych grup zasobów w środowisku produkcyjnym, projektowanie i testowanie środowisk. Ułatwia to zarządzanie wdrożeniami, usuwanie wdrożeń testowych i przypisywanie praw dostępu.
Podczas przydzielania zasobów do grup zasobów rozważ następujące kwestie:

- Cykl życia. Ogólnie rzecz biorąc, umieszczaj zasoby z tym samym cyklem życia w tej samej grupie zasobów.
- Dostęp. Można użyć [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) (RBAC), aby zastosować zasady dostępu do zasobów w grupie.
- Rozliczenia. Możesz wyświetlać zestawienia kosztów dla grupy zasobów.
- Warstwa cenowa dla interfejsu API zarządzania — zaleca się używanie deweloperów warstwy dla środowisk projektowania i testowania. Do produkcji wstępnej zalecamy wdrażanie repliki środowiska produkcyjnego, testów i zamykanie do minimum kosztów.

Aby uzyskać więcej informacji, zobacz [grupy zasobów](../azure-resource-manager/resource-group-overview.md) omówienie.

### <a name="deployment"></a>Wdrożenie

Firma Microsoft zaleca użycie [szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) do wdrożenia usługi Azure API Management, Logic Apps, siatki zdarzeń i usługi Service Bus. Szablony ułatwiają Automatyzowanie wdrożeń za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure (CLI).

Zaleca się umieszczenie Azure API Management, wszelkie poszczególnych Logic Apps, tematy zdarzeń siatki i nazw magistrali usług w własne oddzielne szablony Menedżera zasobów. Pozwoli to przechowywania ich do systemów kontroli źródła. Szablony te można następnie wdrożyć razem lub oddzielnie w ramach procesu wdrażania ciągłej integracji/ciągłego (CI/CD).

### <a name="diagnostics-and-monitoring"></a>Diagnostyka i monitorowanie

Usługi Service Bus, takich jak zarządzanie interfejsami API i Logic Apps można monitorować za pomocą monitora Azure. Azure Monitor jest domyślnie włączona i przekazuje informacje na podstawie różnych metryk skonfigurowane dla każdej usługi.

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Zabezpieczenia usługi Service Bus przy użyciu sygnaturę dostępu współdzielonego. [Uwierzytelniania sygnatury dostępu Współdzielonego](../service-bus-messaging/service-bus-sas.md) umożliwia udzielić użytkownikowi dostępu do zasobów usługi Service Bus, przy użyciu określonych praw. Przeczytaj więcej na temat [magistrali usług uwierzytelniania i autoryzacji](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Ponadto należy kolejki usługi Service Bus muszą być udostępniany jako punkt końcowy HTTP (aby umożliwić publikowanie nowych wiadomości) interfejsu API zarządzania powinien być używany do bezpiecznego go przez fronting punktu końcowego. To następnie można zabezpieczyć za pomocą certyfikatów lub OAuth zależnie od potrzeb. Najprostszym sposobem, w tym celu używanej aplikacji logiki z wyzwalaczem żądań i odpowiedzi HTTP jako pośrednik.

Zdarzenie siatki zabezpiecza dostarczania zdarzeń za pomocą kodu walidacji. Użycie LogicApps użycie zdarzenia, jest to wykonywane automatycznie. Dowiedz się więcej [siatki zdarzeń zabezpieczeń i uwierzytelniania](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Kolejne kroki

* [Integracja proste Enterprise](logic-apps-architectures-simple-enterprise-integration.md)
