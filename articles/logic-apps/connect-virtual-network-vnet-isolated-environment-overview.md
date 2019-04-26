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
ms.date: 03/11/2019
ms.openlocfilehash: b53cd54afdf6243769602971ab77145cfa9ba9cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506839"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Dostęp do zasobów sieci wirtualnej platformy Azure z usługi Azure Logic Apps za pomocą środowiska usług integracji (ISEs)

> [!NOTE]
> Ta funkcja jest w [ *publicznej wersji zapoznawczej*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Czasami usługi logic apps i kont integracji muszą mieć dostęp do zabezpieczonych zasobów, takich jak maszyny wirtualne (VM) i innych systemów lub usług, w [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md). Aby skonfigurować ten dostęp, możesz [tworzenie *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) do uruchamiania aplikacji logiki i konta integracji. Podczas tworzenia środowiska ISE, platforma Azure wdroży prywatnych i izolowane wystąpienie usługi Logic Apps do sieci wirtualnej platformy Azure. To wystąpienie prywatnej wykorzystuje dedykowane zasoby, takie jak storage i jest uruchamiana niezależnie od publicznego "global" Usługa Logic Apps. Oddzielanie izolowanym wystąpieniem prywatnym i publicznym wystąpieniu globalnego pomaga również zmniejszyć wpływ, jaki innych dzierżaw usługi Azure może mieć na wydajność Twojej aplikacji, która jest również znana jako [efektu "hałaśliwym sąsiadów"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

Po utworzeniu usługi ISE, po przejściu do utworzenia konta aplikacji lub Integracja z usługą Logic Apps, możesz wybrać swoje ISE jako lokalizację logiki aplikacji lub integracji konta:

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Twoja aplikacja logiki można teraz bezpośrednio uzyskiwać dostęp do systemów, które znajdują się wewnątrz lub połączony z siecią wirtualną przy użyciu dowolnego z tych elementów:

* Określonej wersji środowiska ISE łącznik dla tego systemu, na przykład SQL Server
* Wbudowane wyzwalacza lub akcji, takich jak HTTP, wyzwalacza lub akcji
* Łącznik niestandardowy

W tym omówieniu opisano więcej szczegółowych informacji o jak ISE zapewnia aplikacji logiki i integracji kont bezpośredni dostęp do sieci wirtualnej platformy Azure i porównuje różnice między ISE i globalną usługę Logic Apps.
Dla systemów lokalnych, które nie są połączone z siecią wirtualną lub nie masz wersji środowiska ISE łączników, możesz nawiązać połączenie tych systemów, [Konfigurowanie i używanie lokalnej bramy danych](../logic-apps/logic-apps-gateway-install.md).

> [!IMPORTANT]
> Logic apps, wbudowanych akcji i łączników, działających w Twojej platformy ISE przy użyciu innego planu cen nie na podstawie użycia planu cenowego zawiera. Aby uzyskać więcej informacji, zobacz [Logic Apps — cennik](../logic-apps/logic-apps-pricing.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Odizolowane w porównaniu z globalnego

Po utworzeniu środowiska zintegrowana usługa (ISE) na platformie Azure można wybrać sieci wirtualnej platformy Azure, której chcesz *wstrzyknąć* Twojego środowiska ISE. Azure wprowadza lub wdraża wystąpieniem prywatnym usługi Logic Apps do sieci wirtualnej. Ta akcja powoduje utworzenie środowiska izolowanego, na którym można tworzyć i aplikacje logiki są uruchamiane na dedykowanych zasobów. Podczas tworzenia aplikacji logiki, możesz wybrać swoje ISE jako lokalizacja Twojej aplikacji, co umożliwia aplikacji logiki bezpośredniego dostępu do sieci wirtualnej i zasobów w tej sieci.

Logic apps w środowisku ISE Podaj tego samego środowiska użytkowników i możliwości podobne do tych jako globalne usługi Logic Apps. Nie tylko można użyć tych samych wbudowanych akcji i łączników usługi Logic Apps globalnych, ale możesz również użyć łączników specyficzne dla środowiska ISE. Na przykład poniżej przedstawiono niektóre łączniki standardowe, które oferują wersje, które są uruchamiane w środowisku ISE:

* Usługi Azure Blob Storage, File Storage i Table Storage
* Kolejki platformy Azure, usługi Azure Service Bus, Azure Event Hubs i IBM MQ
* Protokołu FTP i SFTP — SSH
* SQL Server, SQL Data Warehouse i Azure Cosmos DB
* AS2, X 12 i EDIFACT

Różnica między ISE i innych ISE łączników jest w lokalizacjach, w którym uruchamiane wyzwalacze i akcje:

* ISE, wbudowane wyzwalacze i akcje, takie jak HTTP, które są zawsze uruchamiane w tej samej platformy ISE jako aplikację logiki.

* Łączniki, które oferują dwie wersje jednej wersji uruchamiane w środowisku ISE, gdy inna wersja działa w globalną usługę Logic Apps.  

  Łączniki, które mają **ISE** zawsze etykietowanie działania w tym samym środowisku ISE jako aplikację logiki. Łączniki bez **ISE** uruchamianych w usłudze Logic Apps globalne etykiety.

  ![Wybieranie łączników środowiska ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Łączniki, które są uruchamiane w środowisku ISE są również dostępne w globalną usługę Logic Apps.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Konta integracji przy użyciu środowiska ISE

Można użyć konta integracji z usługą logic apps w środowisku usługi integracji (ISE). Jednakże, należy użyć tych kont integracji *tego samego środowiska ISE* co usługa połączona logic apps. Logic apps w środowisku ISE można odwoływać się tylko te konta integracji, które znajdują się w tym samym środowisku ISE. Podczas tworzenia konta integracji Twojego środowiska ISE można wybrać jako lokalizację dla konta integracji.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum usługi Azure Logic Apps</a>.
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź <a href="https://aka.ms/logicapps-wish" target="_blank">witrynę opinii użytkowników usługi Logic Apps</a>.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [nawiązać połączenie z sieciami wirtualnymi platformy Azure z aplikacji logiki izolowane](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Dowiedz się więcej o [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md)
* Dowiedz się więcej o [Integracja sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
