---
title: Tworzenie kont integracyjnych B2B lub zarządzanie nimi
description: Tworzenie kont integracji, tworzenie łączy i zarządzania nimi w celu integracji przedsiębiorstwa z aplikacjami Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 083ed0001adb5524c124295eb3bc31f4afad99cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270331"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Tworzenie kont integracji dla integracji przedsiębiorstw B2B w usłudze Azure Logic Apps i zarządzanie nimi

Zanim będzie możliwe utworzenie [rozwiązań integracji dla przedsiębiorstw i B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) przy użyciu usługi [Azure Logic Apps](../logic-apps/logic-apps-overview.md), należy utworzyć konto integracji, czyli osobny zasób platformy Azure, który zapewnia bezpieczny, skalowalny i możliwy do zarządzania kontener na artefakty integracji, które są definiowane i używane z przepływami pracy aplikacji logiki.

Na przykład można tworzyć, przechowywać i zarządzać artefaktami B2B, takimi jak partnerzy handlowi, umowy, mapy, schematy, certyfikaty i konfiguracje wsadowe. Ponadto zanim aplikacja logiki może pracować z tymi artefaktami i używać łączników B2B aplikacji logiki, należy [połączyć konto integracji](#link-account) z aplikacją logiki. Konto integracji i aplikacja logiki musi istnieć w *tej samej* lokalizacji lub regionie.

> [!TIP]
> Aby utworzyć konto integracji w [środowisku usługi integracji,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)zobacz [Tworzenie kont integracji w środowisku ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

W tym temacie pokazano, jak wykonać następujące zadania:

* Utwórz konto integracyjne.
* Połącz swoje konto integracji z aplikacją logiki.
* Zmień warstwę cenową dla konta integracji.
* Odłącz konto integracji od aplikacji logiki.
* Przenieś konto integracji do innej grupy zasobów platformy Azure lub subskrypcji.
* Usuń swoje konto integracyjne.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Tworzenie konta integracji

W tym zadaniu można użyć portalu Azure, wykonując kroki opisane w tej sekcji, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)lub [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create).

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. Z głównego menu platformy Azure wybierz pozycję **Utwórz zasób**. W polu wyszukiwania wpisz jako filtr "konto integracji" i wybierz pozycję **Konto integracji**.

   ![Tworzenie nowego konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. W obszarze **Konto integracji**wybierz pozycję **Utwórz**.

   ![Wybierz "Dodaj", aby utworzyć konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Podaj te informacje o koncie integracji:

   ![Podaj szczegóły konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa** | Tak | <*nazwa konta integracji*> | Nazwa konta integracyjnego, która może zawierać tylko litery,`-`cyfry,`_`łączniki ( ),`(` `)`podkreślenia`.`( ), nawiasy ( , ), i kropki ( ). W tym przykładzie użyto "Fabrikam-Integration". |
   | **Subskrypcja** | Tak | <*Nazwa subskrypcji platformy Azure*> | Nazwa subskrypcji platformy Azure |
   | **Grupa zasobów** | Tak | <*Nazwa-grupa zasobów platformy Azure*> | Nazwa grupy [zasobów platformy Azure](../azure-resource-manager/management/overview.md) do użycia do organizowania powiązanych zasobów. W tym przykładzie należy utworzyć nową grupę zasobów o nazwie "FabrikamIntegration-RG". |
   | **Warstwa cenowa** | Tak | <*na poziomie cen*> | Warstwa cenowa dla konta integracji, którą można zmienić później. W tym przykładzie wybierz opcję **Bezpłatne**. Więcej informacji można znaleźć w następujących tematach: <p>- [Model cenowy aplikacji logiki](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Ograniczenia i konfiguracja aplikacji logiki](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Cennik aplikacji logiki](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Lokalizacja** | Tak | <*Region platformy Azure*> | Region, w którym mają być przechowywane metadane konta integracji. Wybierz tę samą lokalizację co aplikacja logiki lub utwórz aplikacje logiki w tej samej lokalizacji co konto integracji. W tym przykładzie należy użyć "Zachodnie stany USA". <p>**Uwaga:** Aby utworzyć konto integracji wewnątrz [środowiska usługi integracji (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wybierz tę ise jako lokalizację. Aby uzyskać więcej informacji, zobacz [Tworzenie kont integracji w ise](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Analiza dzienników** | Nie | Wył., wł. | Zachowaj **ustawienie Wyłączone** w tym przykładzie. |
   |||||

1. Po zakończeniu wybierz pozycję **Utwórz**.

   Po zakończeniu wdrażania platforma Azure otwiera konto integracji.

   ![Platforma Azure otwiera konto integracji](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Zanim aplikacja logiki będzie mogła korzystać z konta integracji, wykonaj następne kroki, aby połączyć konto integracji i aplikację logiki.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Łączenie z aplikacją logiki

Aby nadać aplikacjom logiki dostęp do konta integracji zawierającego artefakty B2B, należy najpierw połączyć konto integracji z aplikacją logiki. Zarówno aplikacja logiki i konta integracji musi istnieć w tym samym regionie. Aby wykonać to zadanie, można użyć witryny Azure portal. Jeśli używasz programu Visual Studio, a aplikacja logiki znajduje się w [projekcie grupy zasobów platformy Azure,](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)możesz [połączyć aplikację logiki z kontem integracji przy użyciu programu Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. W witrynie Azure portal znajdź i otwórz aplikację logiki.

1. W [witrynie Azure portal](https://portal.azure.com)otwórz istniejącą aplikację logiki lub utwórz nową aplikację logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Ustawienia przepływu pracy**. W obszarze **Konto integracji**otwórz listę **Wybierz konto integracji.** Wybierz konto integracji, aby połączyć się z aplikacją logiki.

   ![Wybierz konto integracyjne](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Aby zakończyć łączenie, wybierz pozycję **Zapisz**.

   ![Wybierz konto integracyjne](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Po pomyślnym połączeniu konta integracji platforma Azure wyświetla komunikat potwierdzający.

   ![Platforma Azure potwierdza pomyślne łącze](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Teraz aplikacja logiki można użyć artefaktów na koncie integracji oraz łączników B2B, takich jak sprawdzanie poprawności XML i kodowania plików płaskich lub dekodowania.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Zmiana warstwy cenowej

Aby zwiększyć [limity](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) dla konta integracji, można [uaktualnić do wyższej warstwy cenowej](#upgrade-pricing-tier), jeśli są dostępne. Na przykład można uaktualnić z warstwy bezpłatnej do warstwy podstawowej lub warstwy standardowej. Można również [obniżyć do niższej warstwy](#downgrade-pricing-tier), jeśli są dostępne. Aby uzyskać więcej informacji na temat cen, zobacz następujące tematy:

* [Cennik aplikacji logiki](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Model cen aplikacji logiki](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Uaktualnianie warstwy cenowej

Aby wprowadzić tę zmianę, można użyć witryny Azure portal, wykonując kroki opisane w tej sekcji lub [interfejsu wiersza polecenia platformy Azure.](#upgrade-tier-azure-cli)

#### <a name="azure-portal"></a>Portal Azure

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W głównym polu wyszukiwania platformy Azure wprowadź jako filtr "konta integracji" i wybierz pozycję **Konta integracji**.

   ![Znajdowanie konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Platforma Azure pokazuje wszystkie konta integracji w subskrypcjach platformy Azure.

1. W obszarze **Konta integracji**wybierz konto integracji, które chcesz przenieść. W menu konta integracyjnego wybierz pozycję **Przegląd**.

   ![W menu konta integracyjnego wybierz "Przegląd"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. W okienku Przegląd wybierz pozycję **Uaktualnij warstwę cenową**, która zawiera listę dostępnych wyższych warstw. Po wybraniu warstwy zmiana natychmiast staje się skuteczna.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Jeśli jeszcze tego nie zrobiono, [zainstaluj wymagania wstępne interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

1. W witrynie Azure portal otwórz środowisko usługi Azure [**Cloud Shell.**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

   ![Otwieranie usługi Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. W wierszu polecenia wprowadź polecenie [ **az** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) `skuName` resource i ustaw wyższą warstwę, którą chcesz.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Na przykład, jeśli masz warstwę Podstawową, możesz ustawić na: `skuName` `Standard`

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Warstwa cenowa na starszą wersję produktu

Aby wprowadzić tę zmianę, użyj [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Jeśli jeszcze tego nie zrobiono, [zainstaluj wymagania wstępne interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

1. W witrynie Azure portal otwórz środowisko usługi Azure [**Cloud Shell.**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

   ![Otwieranie usługi Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. W wierszu polecenia wprowadź polecenie [ **zasobu az** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) i ustaw `skuName` niższą warstwę, którą chcesz.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Na przykład, jeśli masz warstwę Standardową, możesz ustawić `skuName` na: `Basic`

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Odłączanie od aplikacji logiki

Jeśli chcesz połączyć aplikację logiki z innym kontem integracji lub nie używasz już konta integracji z aplikacją logiki, usuń łącze przy użyciu Eksploratora zasobów platformy Azure.

1. Otwórz okno przeglądarki i przejdź do [Eksploratora zasobów platformy Azure (https://resources.azure.com)](https://resources.azure.com). Zaloguj się przy użyciu tych samych poświadczeń konta platformy Azure.

   ![Eksplorator zasobów Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. W polu wyszukiwania wprowadź nazwę aplikacji logiki, aby można było znaleźć i wybrać aplikację logiki.

   ![Znajdowanie i wybieranie aplikacji logiki](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Na pasku tytułu eksploratora wybierz pozycję **Odczyt/Zapis**.

   ![Włączanie trybu "Odczyt/zapis"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Na karcie **Dane** wybierz pozycję **Edytuj**.

   ![Na karcie "Dane" wybierz "Edytuj"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. W edytorze znajdź `integrationAccount` obiekt i usuń tę właściwość, która ma ten format:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Przykład:

   ![Znajdź obiekt "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Na karcie **Dane** wybierz pozycję **Umieść,** aby zapisać zmiany.

   ![Aby zapisać zmiany, wybierz "Put"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. W witrynie Azure portal znajdź i wybierz aplikację logiki. W **ustawieniach przepływu pracy**aplikacji sprawdź, czy właściwość konto **integracji** jest teraz pusta.

   ![Sprawdź, czy konto integracji nie jest połączone](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Przenoszenie konta integracji

Konto integracji można przenieść do innej grupy zasobów platformy Azure lub subskrypcji platformy Azure. Podczas przenoszenia zasobów platforma Azure tworzy nowe identyfikatory zasobów, więc upewnij się, że zamiast tego używasz nowych identyfikatorów i aktualizuj wszystkie skrypty lub narzędzia skojarzone z przeniesionymi zasobami. Aby zmienić subskrypcję, należy również określić istniejącą lub nową grupę zasobów.

W tym zadaniu można użyć witryny Azure portal, wykonując kroki opisane w tej sekcji lub [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move)

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W głównym polu wyszukiwania platformy Azure wprowadź jako filtr "konta integracji" i wybierz pozycję **Konta integracji**.

   ![Znajdowanie konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Platforma Azure pokazuje wszystkie konta integracji w subskrypcjach platformy Azure.

1. W obszarze **Konta integracji**wybierz konto integracji, które chcesz przenieść. W menu konta integracyjnego wybierz pozycję **Przegląd**.

   ![W menu konta integracyjnego wybierz "Przegląd"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Obok grupy **zasobów** lub **nazwy subskrypcji**wybierz pozycję **Zmień**.

   ![Zmienianie grupy zasobów lub subskrypcji](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Wybierz wszystkie powiązane zasoby, które również chcesz przenieść.

1. Na podstawie wyboru wykonaj następujące kroki, aby zmienić grupę zasobów lub subskrypcję:

   * Grupa zasobów: na liście **Grupa zasobów** wybierz docelową grupę zasobów. Aby utworzyć inną grupę zasobów, wybierz pozycję **Utwórz nową grupę zasobów**.

   * Subskrypcja: z listy **Subskrypcja** wybierz subskrypcję docelową. Z listy **Grupa zasobów** wybierz docelową grupę zasobów. Aby utworzyć inną grupę zasobów, wybierz pozycję **Utwórz nową grupę zasobów**.

1. Aby potwierdzić, że wszystkie skrypty lub narzędzia skojarzone z przeniesionymi zasobami nie będą działać, dopóki nie zaktualizujesz ich za pomocą nowych identyfikatorów zasobów, zaznacz pole potwierdzenia, a następnie wybierz **przycisk OK**.

1. Po zakończeniu upewnij się, że zaktualizujesz wszystkie skrypty za pomocą nowych identyfikatorów zasobów dla przeniesionych zasobów.  

## <a name="delete-integration-account"></a>Usuwanie konta integracji

W tym zadaniu można użyć portalu Azure, wykonując kroki opisane w tej sekcji, [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)lub programu Azure [PowerShell.](/powershell/module/az.logicapp/remove-azintegrationaccount)

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W głównym polu wyszukiwania platformy Azure wprowadź jako filtr "konta integracji" i wybierz pozycję **Konta integracji**.

   ![Znajdowanie konta integracji](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Platforma Azure pokazuje wszystkie konta integracji w subskrypcjach platformy Azure.

1. W obszarze **Konta integracji**wybierz konto integracji, które chcesz usunąć. W menu konta integracyjnego wybierz pozycję **Przegląd**.

   ![W menu konta integracyjnego wybierz "Przegląd"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. W okienku Przegląd wybierz pozycję **Usuń**.

   ![W okienku "Przegląd" wybierz "Usuń"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Aby potwierdzić, że chcesz usunąć konto integracyjne, wybierz pozycję **Tak**.

   ![Aby potwierdzić usunięcie, wybierz "Tak"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie partnerów handlowych na koncie integracyjnym](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Zawieranie umów między partnerami na koncie integracji](../logic-apps/logic-apps-enterprise-integration-agreements.md)
