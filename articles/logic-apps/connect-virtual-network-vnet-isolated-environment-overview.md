---
title: Dostęp do sieci wirtualnych platformy Azure z usługi Azure Logic Apps przy użyciu środowisk usługi integracji (ISEs)
description: W tym omówieniu opisano, jak pomóc w środowiskach usług integracji (ISEs) aplikacji logiki dostępu do sieci wirtualnych platformy Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: f21af23cf0b7b121441b1433f382db60ef7f13fe
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408663"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Dostęp do zasobów sieci wirtualnej platformy Azure z usługi Azure Logic Apps za pomocą środowiska usług integracji (ISEs)

> [!NOTE]
> Ta funkcja jest w *prywatnej wersji zapoznawczej*. Aby zażądać dostępu, [utworzyć Twoje żądanie dołączenia do tutaj](https://aka.ms/iseprivatepreview).

Czasami usługi logic apps i kont integracji muszą mieć dostęp do zabezpieczonych zasobów, takich jak maszyny wirtualne (VM) i innych systemów lub usługi w [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md). Aby skonfigurować ten dostęp, możesz [tworzenie *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) używanej jako lokalizację dla usługi logic apps i konta integracji. 

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Tworzenie środowiska ISE wdraża prywatne i izolowane wystąpienie aplikacji logiki do sieci wirtualnej platformy Azure. Wystąpieniem prywatnym wykorzystuje dedykowane zasoby, takie jak storage i jest uruchamiana niezależnie od publicznego "global" Usługa Logic Apps. Ta separacja pomaga również zmniejszyć wpływ, jaki innych dzierżaw usługi Azure może mieć na wydajność Twojej aplikacji lub [efektu "hałaśliwym sąsiadów"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). 

Ten przegląd zawiera opis sposobu tworzenia środowiska ISE pomaga logic apps i kont integracji uzyskać bezpośredni dostęp do zasobów w obrębie sieci wirtualnej platformy Azure i porównuje różnice między ISE i globalną usługę Logic Apps.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Odizolowane w porównaniu z globalnego

Po utworzeniu środowiska zintegrowana usługa (ISE) na platformie Azure można wybrać sieci wirtualnej platformy Azure jako *równorzędnej* dla danego środowiska. Platforma Azure wdroży wystąpieniem prywatnym usługi Logic Apps do sieci wirtualnej, co w izolowanym środowisku, w którym można tworzyć i aplikacje logiki są uruchamiane na dedykowanych zasobów. Podczas tworzenia aplikacji logiki, możesz wybrać tego środowiska jako lokalizacji Twojej aplikacji również umożliwia aplikacji logiki bezpośredni dostęp do zasobów w sieci wirtualnej.  

Logic apps w środowisku ISE Podaj tego samego środowiska użytkowników i możliwości podobne do tych jako globalne usługi Logic Apps. Nie można użyć tego samego elementy wbudowane i łączników, udostępniane przez usługę Logic Apps globalne tylko możesz korzystać z łączników, które zapewniają wersje środowiska ISE. Na przykład poniżej przedstawiono niektóre łączniki standardowe, które oferują wersje, które są uruchamiane w środowisku ISE:
 
* Usługi Azure Blob Storage, File Storage i Table Storage
* Azure Queues
* Azure Service Bus
* FTP
* SSH FTP (SFTP)
* Oprogramowanie SQL Server
* AS2, X 12 i EDIFACT

Różnica między ISE i innych ISE łączników jest w lokalizacjach, w którym uruchamiane wyzwalacze i akcje:

* Jeśli korzystasz z wbudowanych wyzwalaczy i akcji, takich jak HTTP w swoje środowiska ISE, te wyzwalacze i akcje zawsze uruchamiane w tym samym środowisku ISE jako aplikację logiki. 

* Dla łączników, które oferują dwie wersje: jedna wersja jest uruchamiany w środowisku ISE, gdy druga wersja działa w globalną usługę Logic Apps.  

  Łączniki, które mają **ISE** zawsze etykietowanie działania w tym samym środowisku ISE jako aplikację logiki. Łączniki bez **ISE** uruchamianych w usłudze Logic Apps globalne etykiety. 

  ![Wybieranie łączników środowiska ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Łączniki, które należy skonfigurować w swoje środowiska ISE są również dostępne w globalną usługę Logic Apps. 

> [!IMPORTANT]
> Logic apps, wbudowanych akcji i łączników, działających w Twojej platformy ISE przy użyciu innego planu cen nie na podstawie użycia planu cenowego zawiera. Aby uzyskać więcej informacji, zobacz [Logic Apps — cennik](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Uprawnienia dostępu do sieci wirtualnej

Podczas tworzenia środowiska usług integracji (ISE) można wybrać sieci wirtualnej platformy Azure jako *równorzędnej* dla danego środowiska. Można jednak *tylko* tworzenia tej relacji lub *komunikacji równorzędnej*, podczas tworzenia usługi ISE. Ta relacja daje dostęp środowiska ISE do zasobów w sieci wirtualnej, która następnie umożliwia aplikacjom logiki, w tym środowisku ISE bezpośrednio łączyć się z zasobami w sieci wirtualnej. Systemów lokalnych w sieci wirtualnej, która jest połączona z ISE aplikacje logiki można uzyskać dostęp do tych systemów, przy użyciu dowolnego z tych elementów: 

* Łącznik platformy ISE dla tego systemu, na przykład SQL Server

* Akcja HTTP 

* Łącznik niestandardowy

Dla systemów lokalnych, które nie znajdują się w sieci wirtualnej lub nie masz ISE łączniki, można wciąż mogą się łączyć po [konfigurowania i używania lokalnej bramy danych](../logic-apps/logic-apps-gateway-install.md).

Aby można było wybrać sieć wirtualną platformy Azure jako element równorzędny w danym środowisku, należy skonfigurować uprawnień kontroli dostępu opartej na rolach (RBAC) w Twojej sieci wirtualnej dla usługi Azure Logic Apps. To zadanie wymaga, aby przypisać **Współautor sieci** i **współautora klasycznej** ról w usłudze Azure Logic Apps. Aby uzyskać więcej informacji na temat uprawnień roli wymagane w przypadku komunikacji równorzędnej, zobacz [uprawnienia sekcji Tworzenie, zmienianie, lub usunąć komunikację równorzędną sieci wirtualnej](../virtual-network/virtual-network-manage-peering.md#permissions).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Konta integracji przy użyciu środowiska ISE

Możesz użyć kont integracji z usługą logic apps, które działają w środowisku usługi integracji (ISE), ale również użyć tych kont integracji *tego samego środowiska ISE* co usługa połączona logic apps. Logic apps w środowisku ISE można odwoływać się tylko te konta integracji, które znajdują się w tym samym środowisku ISE. Podczas tworzenia konta integracji Twojego środowiska ISE można wybrać jako lokalizację dla konta integracji.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum usługi Azure Logic Apps</a>.
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź <a href="http://aka.ms/logicapps-wish" target="_blank">witrynę opinii użytkowników usługi Logic Apps</a>.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [nawiązać połączenie z sieciami wirtualnymi platformy Azure z aplikacji logiki izolowane](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Dowiedz się więcej o [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md)
* Dowiedz się więcej o [Integracja sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
