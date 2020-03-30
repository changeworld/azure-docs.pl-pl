---
title: Konfigurowanie publicznego wychodzącego adresu IP dla ise
description: Dowiedz się, jak skonfigurować jeden publiczny wychodzący adres IP dla środowisk usług integracji (ISE) w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191476"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Konfigurowanie jednego adresu IP dla co najmniej jednego środowiska usługi integracji w usłudze Azure Logic Apps

Podczas pracy z usługą Azure Logic Apps można skonfigurować [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) do hostingu aplikacji logiki, które wymagają dostępu do zasobów w [sieci wirtualnej platformy Azure.](../virtual-network/virtual-networks-overview.md) Jeśli masz wiele wystąpień środowiska ISE, które wymagają dostępu do innych punktów końcowych, które mają ograniczenia adresów IP, należy [wdrożyć zaporę platformy Azure](../firewall/overview.md) lub [wirtualne urządzenie sieciowe](../virtual-network/virtual-networks-overview.md#filter-network-traffic) w sieci wirtualnej i kierować ruch wychodzący za pośrednictwem tej zapory lub wirtualnego urządzenia sieciowego. Następnie można mieć wszystkie wystąpienia środowiska ISE w sieci wirtualnej używać jednego, publicznego, statycznego i przewidywalnego adresu IP do komunikowania się z systemami docelowymi. W ten sposób nie trzeba konfigurowania dodatkowych otworów zapory w tych systemach docelowych dla każdej ise.

W tym temacie pokazano, jak rozsyłać ruch wychodzący za pośrednictwem zapory platformy Azure, ale można zastosować podobne pojęcia do wirtualnego urządzenia sieciowego, takiego jak zapora innej firmy z portalu Azure Marketplace. W tym temacie skupiono się na konfiguracji dla wielu wystąpień ISE, można również użyć tej metody dla pojedynczego ise, gdy scenariusz wymaga ograniczenia liczby adresów IP, które wymagają dostępu. Należy wziąć pod uwagę, czy dodatkowe koszty dla zapory lub urządzenia sieci wirtualnej mają sens dla twojego scenariusza. Dowiedz się więcej o [cenach zapory platformy Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Wymagania wstępne

* Zapora platformy Azure, która działa w tej samej sieci wirtualnej co środowisko ISE. Jeśli nie masz zapory, najpierw [dodaj podsieć](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) o nazwie `AzureFirewallSubnet` do sieci wirtualnej. Następnie można [utworzyć i wdrożyć zaporę](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) w sieci wirtualnej.

* [Tabela marszrut](../virtual-network/manage-route-table.md)platformy Azure . Jeśli go nie masz, najpierw [utwórz tabelę tras](../virtual-network/manage-route-table.md#create-a-route-table). Aby uzyskać więcej informacji na temat routingu, zobacz [Routing ruchu sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Ustawianie tabeli tras

1. W [witrynie Azure portal](https://portal.azure.com)wybierz tabelę tras, na przykład:

   ![Wybieranie tabeli tras z regułą kierowania ruchem wychodzącym](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Aby [dodać nową trasę](../virtual-network/manage-route-table.md#create-a-route), w menu tabeli tras wybierz polecenie **Trasy** > **Dodaj**.

   ![Dodawanie trasy do kierowania ruchem wychodzącym](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. W okienku **Dodaj trasę** ustaw [nową trasę](../virtual-network/manage-route-table.md#create-a-route) z regułą określającą, że cały ruch wychodzący do systemu docelowego jest zgodny z tym zachowaniem:

   * Używa [**urządzenia wirtualnego**](../virtual-network/virtual-networks-udr-overview.md#user-defined) jako następnego typu przeskoku.

   * Przechodzi do prywatnego adresu IP dla wystąpienia zapory jako następny adres przeskoku.

     Aby znaleźć ten adres IP, w menu zapory wybierz pozycję **Przegląd**, znajdź adres w obszarze **Prywatny adres IP**, na przykład:

     ![Znajdź prywatny adres IP zapory](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Oto przykład, który pokazuje, jak taka reguła może wyglądać:

   ![Konfigurowanie reguły kierowania ruchem wychodzącym](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa trasy** | <*nazwa trasy unikatowej*> | Unikatowa nazwa trasy w tabeli tras |
   | **Prefiks adresu** | <*adres docelowy*> | Adres systemu docelowego, w którym ma się odejść ruch. Upewnij się, że dla tego adresu używasz [notacji CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) |
   | **Typ następnego skoku** | **Urządzenie wirtualne** | [Typ przeskoku](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) używany przez ruch wychodzący |
   | **Adres następnego skoku** | <*firewall-prywatny-adres IP*> | Prywatny adres IP zapory |
   |||

## <a name="set-up-network-rule"></a>Konfigurowanie reguły sieciowej

1. W witrynie Azure portal znajdź i wybierz zaporę. W menu zapory w obszarze **Ustawienia**wybierz pozycję **Reguły**. W okienku reguł wybierz pozycję **Kolekcja** > reguł**sieciowych Dodaj kolekcję reguł sieciowych**.

   ![Dodawanie kolekcji reguł sieciowych do zapory](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. W kolekcji dodaj regułę, która zezwala na ruch do systemu docelowego.

   Załóżmy na przykład, że masz aplikację logiki, która działa w ise i musi komunikować się z systemem SFTP. Tworzysz kolekcję reguł sieciowych `LogicApp_ISE_SFTP_Outbound`o nazwie , `ISE_SFTP_Outbound`która zawiera regułę sieciową o nazwie . Ta reguła zezwala na ruch z adresu IP dowolnej podsieci, w której środowisko ISE jest uruchamiane w sieci wirtualnej, do docelowego systemu SFTP przy użyciu prywatnego adresu IP zapory.

   ![Konfigurowanie reguły sieciowej zapory](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Właściwości kolekcji reguł sieciowych**

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*nazwa kolekcji reguł sieciowych*> | Nazwa kolekcji reguł sieciowych |
   | **Priorytet** | <*poziom priorytetu*> | Kolejność priorytetów do użycia do uruchamiania kolekcji reguł. Aby uzyskać więcej informacji, zobacz [Jakie są pojęcia zapory platformy Azure?](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts) |
   | **Akcja** | **Umożliwić swobodne otworzenie** | Typ akcji do wykonania dla tej reguły |
   |||

   **Właściwości reguły sieciowej**

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*nazwa reguły sieciowej*> | Nazwa reguły sieciowej |
   | **Protokół** | <*protokoły połączeń*> | Protokoły połączeń do użycia. Na przykład, jeśli używasz reguł sieciowych sieciowych, wybierz zarówno **TCP,** jak i **UDP**, nie tylko **TCP**. |
   | **Adresy źródłowe** | <*Adresy podsieci ISE*> | Adresy IP podsieci, gdzie działa ise i skąd pochodzi ruch z aplikacji logiki |
   | **Adresy docelowe** | <*adres docelowy-adres IP*> | Adres IP systemu docelowego, w którym ma się odbywać ruch |
   | **Porty docelowe** | <*porty docelowe*> | Wszystkie porty używane przez system docelowy do komunikacji przychodzącej |
   |||

   Aby uzyskać więcej informacji na temat reguł sieciowych, zobacz następujące artykuły:

   * [Konfigurowanie reguły sieci](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Logika przetwarzania reguł usługi Azure Firewall](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Zapora platformy Azure – często zadawane pytania](../firewall/firewall-faq.md)
   * [Azure PowerShell: Nowa reguła sieci AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Interfejs wiersza polecenia platformy Azure: reguła sieciowa zapory sieciowej az](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Następne kroki

* [Łączenie się z sieciami wirtualnymi platformy Azure za pomocą aplikacji Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)