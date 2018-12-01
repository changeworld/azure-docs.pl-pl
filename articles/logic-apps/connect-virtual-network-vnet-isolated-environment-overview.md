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
ms.date: 11/29/2018
ms.openlocfilehash: eb296a436f6c09a4f592ba3a26ee1c3a0f8e18bb
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678836"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Dostęp do zasobów sieci wirtualnej platformy Azure z usługi Azure Logic Apps za pomocą środowiska usług integracji (ISEs)

> [!NOTE]
> Ta funkcja jest w *prywatnej wersji zapoznawczej*. Aby zażądać dostępu, [utworzyć Twoje żądanie dołączenia do tutaj](https://aka.ms/iseprivatepreview).

Czasami usługi logic apps i kont integracji muszą mieć dostęp do zabezpieczonych zasobów, takich jak maszyny wirtualne (VM) i innych systemów lub usług, w [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md). Aby skonfigurować ten dostęp, możesz [tworzenie *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) do uruchamiania aplikacji logiki i konta integracji. 

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Tworzenie środowiska ISE wdraża prywatne i izolowane wystąpienie aplikacji logiki do sieci wirtualnej platformy Azure. To wystąpienie prywatnej wykorzystuje dedykowane zasoby, takie jak storage i jest uruchamiana niezależnie od publicznego "global" Usługa Logic Apps. Ta separacja pomaga również zmniejszyć wpływ, jaki innych dzierżaw usługi Azure może mieć na wydajność Twojej aplikacji lub [efektu "hałaśliwym sąsiadów"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). 

W tym omówieniu opisano, jak ISE umożliwia aplikacji usługi logic apps i kont integracji bezpośredni dostęp do sieci wirtualnej platformy Azure i porównuje różnice między ISE i globalną usługę Logic Apps.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Odizolowane w porównaniu z globalnego

Po utworzeniu środowiska zintegrowana usługa (ISE) na platformie Azure, wybierz sieć wirtualną platformy Azure gdzie Twoje *wstrzyknąć* środowiska. Platforma Azure wdroży wystąpieniem prywatnym usługi Logic Apps do sieci wirtualnej. Ta akcja powoduje utworzenie środowiska izolowanego, na którym można tworzyć i aplikacje logiki są uruchamiane na dedykowanych zasobów. Podczas tworzenia aplikacji logiki, możesz wybrać tego środowiska jako lokalizacja Twojej aplikacji, co umożliwia aplikacji logiki bezpośredni dostęp do zasobów w sieci wirtualnej. 

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

> [!IMPORTANT]
> Logic apps, wbudowanych akcji i łączników, działających w Twojej platformy ISE przy użyciu innego planu cen nie na podstawie użycia planu cenowego zawiera. Aby uzyskać więcej informacji, zobacz [Logic Apps — cennik](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Uprawnienia dostępu do sieci wirtualnej

Podczas tworzenia środowiska usług integracji (ISE) wybierzesz siecią wirtualną platformy Azure, gdzie możesz *wstrzyknąć* środowiska. Iniekcja wdraża wystąpieniem prywatnym usługi Logic Apps do sieci wirtualnej. Ta akcja powoduje w izolowanym środowisku, w którym można tworzyć i aplikacje logiki są uruchamiane na dedykowanych zasobów. Po utworzeniu aplikacji logiki wybierz swoje ISE jako lokalizację swoje aplikacje. Te aplikacje logiki można bezpośrednio uzyskać dostęp do sieci wirtualnej i połączenia się z zasobami w danej sieci. 

Systemów lokalnych w sieci wirtualnej, która jest połączona z ISE aplikacje logiki można uzyskać dostęp do tych systemów, przy użyciu dowolnego z tych elementów: 

* Łącznik platformy ISE dla tego systemu, na przykład SQL Server
* Akcja HTTP 
* Łącznik niestandardowy

Dla systemów lokalnych, które nie znajdują się w sieci wirtualnej lub nie masz ISE łączniki, możesz nawiązać połączenie tych systemów po [konfigurowania i używania lokalnej bramy danych](../logic-apps/logic-apps-gateway-install.md).

Aby można było wybrać sieci wirtualnej platformy Azure wstrzykiwania środowiska, należy skonfigurować uprawnień kontroli dostępu opartej na rolach (RBAC) w sieci wirtualnej dla usługi Azure Logic Apps. To zadanie wymaga, aby przypisać **Współautor sieci** i **współautora klasycznej** ról w usłudze Azure Logic Apps.
Aby ustawić te uprawnienia, zobacz [Połącz z sieciami wirtualnymi platformy Azure z aplikacji logiki](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#vnet-access)

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
