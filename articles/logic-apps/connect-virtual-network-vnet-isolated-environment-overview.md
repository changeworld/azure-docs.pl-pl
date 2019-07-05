---
title: Dostęp do sieci wirtualnych platformy Azure z usługi Azure Logic Apps przy użyciu środowisk usługi integracji (ISEs)
description: W tym omówieniu opisano, jak pomóc w środowiskach usług integracji (ISEs) aplikacji logiki dostępu do sieci wirtualnych platformy Azure (Vnet)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: f981452b06ec06f2be1b8fe0319cd49a678ccfe0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441582"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Dostęp do zasobów sieci wirtualnej platformy Azure z usługi Azure Logic Apps za pomocą środowiska usług integracji (ISEs)

Czasami usługi logic apps i kont integracji muszą mieć dostęp do zabezpieczonych zasobów, takich jak maszyny wirtualne (VM) i innych systemów lub usługi, które znajdują się wewnątrz [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md). Aby skonfigurować ten dostęp, możesz [tworzenie *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) kont, który można uruchomić aplikacji usługi logic apps i utworzenie integracji usługi.

Podczas tworzenia środowiska ISE, platforma Azure wdroży prywatnych i izolowane wystąpienie usługi Logic Apps do sieci wirtualnej platformy Azure. To wystąpienie prywatnej wykorzystuje dedykowane zasoby, takie jak storage i jest uruchamiana niezależnie od publicznego "global" Usługa Logic Apps. Oddzielanie izolowanym wystąpieniem prywatnym i publicznym wystąpieniu globalnego pomaga również zmniejszyć wpływ, jaki innych dzierżaw usługi Azure może mieć na wydajność Twojej aplikacji, która jest również znana jako [efektu "hałaśliwym sąsiadów"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Po utworzeniu usługi ISE, po przejściu do utworzenia konta aplikacji lub Integracja z usługą Logic Apps, możesz wybrać swoje ISE jako lokalizację logiki aplikacji lub integracji konta:

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Twoja aplikacja logiki można teraz bezpośrednio uzyskiwać dostęp do systemów, które znajdują się wewnątrz lub połączony z siecią wirtualną przy użyciu dowolnego z tych elementów:

* **ISE**-etykietą łącznika dla tego systemu, takie jak SQL Server
* A **Core**-etykietą wbudowanych wyzwalacza lub akcji, takich jak HTTP, wyzwalacza lub akcji
* Łącznik niestandardowy

W tym omówieniu opisano więcej szczegółowych informacji o jak ISE zapewnia aplikacji logiki i integracji kont bezpośredni dostęp do sieci wirtualnej platformy Azure i porównuje różnice między ISE i globalną usługę Logic Apps.

> [!NOTE]
> Logic apps, wbudowane wyzwalaczy, wbudowanych akcji i łączników, działających w użytkowanie ISE planu cenowego różni się od planu cenowego na podstawie użycia. Aby uzyskać więcej informacji, zobacz [Logic Apps — cennik](../logic-apps/logic-apps-pricing.md). Twoje ISE również wzrosło limity na czas trwania testu, przechowywanie w magazynie, przepływności, żądania HTTP i limity czasu odpowiedzi, rozmiary wiadomości i żądań łącznika niestandardowego. Aby uzyskać więcej informacji, zobacz [limity i konfiguracja dla usługi Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Odizolowane w porównaniu z globalnego

Po utworzeniu środowiska zintegrowana usługa (ISE) na platformie Azure można wybrać sieci wirtualnej platformy Azure, której chcesz *wstrzyknąć* Twojego środowiska ISE. Azure wprowadza lub wdraża wystąpieniem prywatnym usługi Logic Apps do sieci wirtualnej. Ta akcja powoduje utworzenie środowiska izolowanego, na którym można tworzyć i aplikacje logiki są uruchamiane na dedykowanych zasobów. Podczas tworzenia aplikacji logiki, możesz wybrać swoje ISE jako lokalizacja Twojej aplikacji, co umożliwia aplikacji logiki bezpośredniego dostępu do sieci wirtualnej i zasobów w tej sieci.

Logic apps w środowisku ISE Podaj tego samego środowiska użytkowników i możliwości podobne do tych jako globalne usługi Logic Apps. Nie można użyć tych samych wbudowanych wyzwalaczy, wbudowanych akcji i łączników z globalnej usługi Logic Apps tylko specyficzne dla środowiska ISE łączników można również używać. Na przykład poniżej przedstawiono niektóre łączniki standardowe, które oferują wersje, które są uruchamiane w środowisku ISE:

* Usługi Azure Blob Storage, File Storage i Table Storage
* Kolejki platformy Azure, usługi Azure Service Bus, Azure Event Hubs i IBM MQ
* Protokołu FTP i SFTP — SSH
* SQL Server, SQL Data Warehouse i Azure Cosmos DB
* AS2, X 12 i EDIFACT

Różnica między ISE i innych ISE łączników jest w lokalizacjach, w którym uruchamiane wyzwalacze i akcje:

* W swojej ISE wbudowanych wyzwalaczy i akcji, takich jak HTTP, zawsze uruchamiane w tym samym środowisku ISE jako aplikację logiki i wyświetlania **Core** etykiety.

  ![Wybierz pozycję "Podstawowy" wbudowane wyzwalacze i akcje](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Łączniki, które są uruchamiane w środowisku ISE publicznie hostowały wersje dostępne w usłudze Logic Apps globalnego. Dla łączników, które oferują dwie wersje: łączniki z usługą **ISE** zawsze etykietowanie działania w tym samym środowisku ISE jako aplikację logiki. Łączniki bez **ISE** uruchamianych w usłudze Logic Apps globalne etykiety.

  ![Wybieranie łączników środowiska ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

ISE udostępnia także podwyższone limity czas trwania testu, przechowywanie w magazynie, przepływności, żądania HTTP i limity czasu odpowiedzi, rozmiary wiadomości i żądań łącznika niestandardowego. Aby uzyskać więcej informacji, zobacz [limity i konfiguracja dla usługi Azure Logic Apps](logic-apps-limits-and-config.md).

### <a name="access-to-on-premises-data-sources"></a>Dostęp do lokalnych źródeł danych

Dla systemów lokalnych, które są połączone z siecią wirtualną platformy Azure wstawić ISE do tej sieci, dzięki czemu aplikacje logiki można uzyskać dostęp do tych systemów przy użyciu dowolnego z tych elementów:

* Wersja środowiska ISE łącznika dla tego systemu, na przykład SQL Server
* Akcja HTTP
* Łącznik niestandardowy

  * Jeśli masz łączników niestandardowych, które wymagają lokalnej bramy danych, a utworzone łączniki poza ISE, logic apps w środowisku ISE łączników można również używać tych.
  
  * Łączników niestandardowych utworzonych w środowisku ISE nie działają z lokalną bramą danych. Jednak te łączniki można uzyskać dostęp do lokalnych źródeł danych, które są podłączone do sieci wirtualnej hostingu środowiska ISE. Tak logic apps w środowisku ISE najprawdopodobniej nie ma potrzeby bramy danych podczas komunikacji z tymi zasobami.

Dla systemów lokalnych, które nie są połączone z siecią wirtualną lub nie masz wersji środowiska ISE łączników, należy najpierw [skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md) przed logikę aplikacje umożliwiają połączenie z tych systemów.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Konta integracji przy użyciu środowiska ISE

Można użyć konta integracji z usługą logic apps w środowisku usługi integracji (ISE). Jednakże, należy użyć tych kont integracji *tego samego środowiska ISE* co usługa połączona logic apps. Logic apps w środowisku ISE można odwoływać się tylko te konta integracji, które znajdują się w tym samym środowisku ISE. Podczas tworzenia konta integracji Twojego środowiska ISE można wybrać jako lokalizację dla konta integracji.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [nawiązać połączenie z sieciami wirtualnymi platformy Azure z aplikacji logiki izolowane](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Dowiedz się więcej o [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md)
* Dowiedz się więcej o [Integracja sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
