---
title: Dostęp do sieci wirtualnych platformy Azure
description: Omówienie sposobu, w jaki środowiska usług integracji (ISE) pomagają aplikacjom logiki w dostępie do sieci wirtualnych platformy Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127250"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Dostęp do zasobów sieci wirtualnej platformy Azure z usługi Azure Logic Apps przy użyciu środowisk usług integracji (ISE)

Czasami aplikacje logiki potrzebują dostępu do zabezpieczonych zasobów, takich jak maszyny wirtualne (maszyny wirtualne) i innych systemów lub usług, które znajdują się wewnątrz [sieci wirtualnej platformy Azure.](../virtual-network/virtual-networks-overview.md) Aby skonfigurować ten dostęp, można [utworzyć *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). ISE jest izolowanym wystąpieniem usługi Logic Apps, która używa dedykowanych zasobów i działa oddzielnie od "globalnej" usługi aplikacji logiki z wieloma dzierżawami.

Uruchamianie aplikacji logiki we własnym oddzielnym izolowanym wystąpieniu pomaga zmniejszyć wpływ innych dzierżaw platformy Azure na wydajność aplikacji, znany również jako [efekt "hałaśliwych sąsiadów".](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) Ise zapewnia również następujące korzyści:

* Własne statyczne adresy IP, które są niezależne od statycznych adresów IP, które są współużytkowane przez aplikacje logiki w usłudze wielu dzierżawców. Można również skonfigurować jeden publiczny, statyczny i przewidywalny wychodzący adres IP do komunikowania się z systemami docelowymi. W ten sposób nie trzeba konfigurowania dodatkowych otworów zapory w tych systemach docelowych dla każdej ise.

* Zwiększone limity czasu trwania uruchomienia, przechowywania magazynu, przepływności, limitów czasu żądania i odpowiedzi HTTP, rozmiarów wiadomości i niestandardowych żądań łączników. Aby uzyskać więcej informacji, zobacz [Limity i konfiguracja usługi Azure Logic Apps](logic-apps-limits-and-config.md).

> [!NOTE]
> Niektóre sieci wirtualne platformy Azure używają prywatnych punktów końcowych[(Azure Private Link)](../private-link/private-link-overview.md)do zapewniania dostępu do usług PaaS platformy Azure, takich jak Usługa Azure Storage, usługa Azure Cosmos DB lub usługa Azure SQL Database, usługi partnerskie lub usługi klienta hostowane na platformie Azure. Jeśli aplikacje logiki potrzebują dostępu do sieci wirtualnych korzystających z prywatnych punktów końcowych, należy utworzyć, wdrożyć i uruchomić te aplikacje logiki wewnątrz środowiska ISE.

Podczas tworzenia środowiska ISE platforma Azure *wstrzykuje* lub wdraża tę platformę ISE w sieci wirtualnej platformy Azure. Następnie można użyć tego ise jako lokalizacji dla aplikacji logiki i kont integracji, które wymagają dostępu.

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Aplikacje logiki mogą uzyskiwać dostęp do zasobów, które znajdują się wewnątrz sieci wirtualnej lub są połączone z nią przy użyciu tych elementów, które są uruchamiane w tym samym środowisku ISE, co aplikacje logiki:

* Wbudowany wyzwalacz lub akcja z etykietą **CORE,** taka jak wyzwalacz lub akcja HTTP
* Złącze oznaczone **ise**dla tego systemu lub usługi
* Łącznik niestandardowy

Nadal można również używać łączników, które nie mają etykiety **CORE** lub **ISE** z aplikacjami logiki w interfejsie ISE. Te łączniki są uruchamiane w usłudze aplikacji logiki z wieloma dzierżawami. Aby uzyskać więcej informacji, zobacz następujące sekcje:

* [Odizolowane i wielodostępne](#difference)
* [Łączenie się ze środowiska usługi integracji](../connectors/apis-list.md#integration-service-environment)
* [Złącza ISE](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Aplikacje logiki, wbudowane wyzwalacze, wbudowane akcje i łączniki uruchamiane w interfejsie ISE używają planu cenowego, który różni się od planu cenowego opartego na zużyciu. Aby uzyskać więcej informacji, zobacz [Model cen aplikacji logiki](../logic-apps/logic-apps-pricing.md#fixed-pricing). Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik aplikacji logiki](../logic-apps/logic-apps-pricing.md).

W tym omówieniu opisano więcej informacji na temat sposobu, w jaki środowisko ISE zapewnia aplikacjom logiki bezpośredni dostęp do sieci wirtualnej platformy Azure i porównuje różnice między środowiskiem ISE a usługą aplikacji logiki z wieloma dzierżawami.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Odizolowane i wielodostępne

Podczas tworzenia i uruchamiania aplikacji logiki w środowisku ISE, można uzyskać te same środowiska użytkownika i podobne możliwości, jak wielodostępne usługi aplikacje logiki. Można użyć tych samych wbudowanych wyzwalaczy, akcji i łączników zarządzanych, które są dostępne w usłudze aplikacji logiki z wieloma dzierżawami. Niektóre zarządzane łączniki oferują dodatkowe wersje ISE. Różnica między łącznikami ISE i łącznikami innych niż ISE istnieje w miejscu ich uruchomienia i etykiet, które mają w logic app designer podczas pracy w środowisku ISE.

![Złącza z etykietami i bez etykiet w ise](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Wbudowane wyzwalacze i akcje wyświetlają etykietę **CORE.** Zawsze działają w tym samym ISE co aplikacja logiki. Łączniki zarządzane, które wyświetlają etykietę **ISE,** działają również w tym samym ise co aplikacja logiki.

  Oto na przykład niektóre łączniki, które oferują wersje ise:

  * Usługa Azure Blob Storage, File Storage i Table Storage
  * Kolejki platformy Azure, usługa Azure Service Bus, usługi Azure Event Hubs i ibm mq
  * FTP i SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 i EDIFACT

* Łączniki zarządzane, które nie wyświetlają żadnych dodatkowych etykiet, są zawsze uruchamiane w usłudze aplikacji logiki z wieloma dzierżawami, ale nadal można używać tych łączników w aplikacji logiki hostowanej przez interfejs ISE.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Dostęp do systemów lokalnych

Aby uzyskać dostęp do systemów lokalnych lub źródeł danych, które są połączone z siecią wirtualną platformy Azure, aplikacje logiki w środowisku ISE mogą używać następujących elementów:

* Akcja HTTP

* Złącze oznaczone ise dla tego systemu

  > [!NOTE]
  > Aby użyć uwierzytelniania systemu Windows z łącznikiem programu SQL Server w [środowisku usługi integracji (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)należy użyć wersji programu łącznika innej niż ISE z [lokalną bramą danych](../logic-apps/logic-apps-gateway-install.md). Wersja z etykietą ISE nie obsługuje uwierzytelniania systemu Windows.

* Łącznik niestandardowy

  * Jeśli masz łączniki niestandardowe, które wymagają lokalnej bramy danych i utworzono te łączniki poza środowiskiem ISE, aplikacje logiki w środowisku ISE mogą również używać tych łączników.

  * Łączniki niestandardowe utworzone w środowisku ISE nie działają z lokalną bramą danych. Jednak te łączniki mogą bezpośrednio uzyskiwać dostęp do lokalnych źródeł danych, które są podłączone do sieci wirtualnej obsługującej środowisko ISE. Tak więc aplikacje logiki w ise najprawdopodobniej nie potrzebują bramy danych podczas komunikowania się z tymi zasobami.

W przypadku systemów lokalnych, które nie są połączone z siecią wirtualną lub nie mają łączników oznaczonych przez środowisko ISE, należy najpierw [skonfigurować lokalną bramę danych, zanim aplikacje logiki](../logic-apps/logic-apps-gateway-install.md) będą mogły łączyć się z tymi systemami.

<a name="ise-level"></a>

## <a name="ise-skus"></a>SKU ISE

Podczas tworzenia ise, można wybrać jednostkę SKU dewelopera lub premium. Oto różnice między tymi jednostkami SKU:

* **Developer**

  Zapewnia tańszą ise, który można użyć do eksperymentowania, programowania i testowania, ale nie do testowania produkcji lub wydajności. Jednostka SKU dewelopera zawiera wbudowane wyzwalacze i akcje, łączniki standardowe, łączniki enterprise i jedno konto integracji [warstwy bezpłatnej](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) za ustaloną cenę miesięczną. Jednak ta jednostka SKU nie zawiera żadnej umowy dotyczącej poziomu usług (SLA), opcji skalowania pojemności lub nadmiarowości podczas recyklingu, co oznacza, że mogą wystąpić opóźnienia lub przestoje.

* **Premium**

  Zapewnia środowiska ISE, którego można używać w produkcji i obejmuje obsługę umowy SLA, wbudowane wyzwalacze i akcje, łączniki standardowe, łączniki enterprise, jedno konto integracji [warstwy standardowej,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) opcje skalowania pojemności i nadmiarowość podczas recyklingu za ustaloną cenę miesięczną.

> [!IMPORTANT]
> Opcja SKU jest dostępna tylko w tworzeniu ISE i nie można jej później zmienić.

Aby uzyskać informacje o cenach, zobacz [Ceny aplikacji logiki](https://azure.microsoft.com/pricing/details/logic-apps/). Aby dowiedzieć się, jak działają ceny i rozliczenia dla firm ISE, zobacz [model cenowy Aplikacji logiki](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Dostęp do punktu końcowego ISE

Podczas tworzenia ise, można użyć wewnętrznych lub zewnętrznych punktów końcowych dostępu. Wybór określa, czy wyzwalacze żądania lub elementu webhook w aplikacjach logiki w środowisku ISE mogą odbierać połączenia spoza sieci wirtualnej.

Te punkty końcowe mają również wpływ na sposób, w jaki można uzyskać dostęp do danych wejściowych i wyjściowych w historii uruchamiania aplikacji logiki.

* **Wewnętrzne**: Prywatne punkty końcowe, które umożliwiają wywołania aplikacji logiki w środowisku ISE, gdzie można wyświetlać i uzyskiwać dostęp do danych wejściowych i wyjściowych aplikacji logiki w historii uruchamiania *tylko z sieci wirtualnej*

* **Zewnętrzne**: publiczne punkty końcowe, które umożliwiają wywołania aplikacji logicznych w środowisku ISE, gdzie można wyświetlać i uzyskiwać dostęp do danych wejściowych i wyjściowych aplikacji logiki w historii uruchamiania *spoza sieci wirtualnej.* Jeśli używasz sieciowych grup zabezpieczeń (NSG), upewnij się, że są one skonfigurowane z regułami przychodzącymi, aby umożliwić dostęp do danych wejściowych i wyjściowych historii uruchamiania. Aby uzyskać więcej informacji, zobacz [Włączanie dostępu dla ise](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> Opcja punktu końcowego dostępu jest dostępna tylko w tworzeniu ISE i nie można jej później zmienić.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Konta integracyjne z ISE

Można użyć kont integracji z aplikacjami logiki wewnątrz środowiska usługi integracji (ISE). Jednak te konta integracji należy używać *tego samego ISE* jako połączone aplikacje logiki. Aplikacje logiki w ise można odwoływać się tylko do tych kont integracji, które są w tym samym ISE. Podczas tworzenia konta integracji można wybrać ise jako lokalizację konta integracji. Aby dowiedzieć się, jak ceny i rozliczenia działają w przypadku kont integracyjnych z ise, zobacz [model cenowy Aplikacji logiki](../logic-apps/logic-apps-pricing.md#fixed-pricing). Aby uzyskać informacje o cenach, zobacz [Ceny aplikacji logiki](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Następne kroki

* [Łączenie się z sieciami wirtualnymi platformy Azure za pomocą aplikacji Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Dowiedz się więcej o [wirtualnej sieci platformy Azure](../virtual-network/virtual-networks-overview.md)
* Dowiedz się więcej o [integracji sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
