---
title: Rozwiązywanie problemów z wdrożeniami
description: Learn how to monitor and troubleshoot Azure Resource Manager template deployments. Shows activity logs and deployment history.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 32f3f55df2b87b35672d811c63a21b956a8a4b52
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325355"
---
# <a name="tutorial-troubleshoot-resource-manager-template-deployments"></a>Tutorial: Troubleshoot Resource Manager template deployments

Dowiedz się, jak rozwiązywać problemy z błędami wdrażania szablonów usługi Resource Manager. W tym samouczku skonfigurujesz dwa błędy w szablonie i dowiesz się, jak korzystać z dzienników aktywności i historii wdrażania, aby rozwiązać problemy.

Istnieją dwa typy błędów związanych z wdrażaniem szablonu:

- **Błędy weryfikacji** wynikają z sytuacji, które można rozpoznać przed przystąpieniem do wdrożenia. Są to na przykład błędy składniowe w szablonie lub próby wdrożenia zasobów, które przekraczają limity przydziału w ramach subskrypcji.
- **Błędy wdrażania** wynikają z warunków, które mają miejsce podczas procesu wdrażania. Obejmują one próby uzyskania dostępu do zasobu, który jest wdrażany równolegle.

Oba rodzaje błędów zwracają kod błędu, którego należy użyć do rozwiązania problemów z wdrożeniem. Oba rodzaje błędów są wyświetlane w dzienniku aktywności. Błędy weryfikacji nie są jednak wyświetlane w historii wdrażania, ponieważ wdrożenie nie jest w takim przypadku rozpoczynane.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie problematycznego szablonu
> * Rozwiązywanie problemów z błędami weryfikacji
> * Usuwanie błędów związanych z wdrażaniem
> * Oczyszczanie zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).

## <a name="create-a-problematic-template"></a>Tworzenie problematycznego szablonu

Otwórz szablon o nazwie [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Tworzenie standardowego konta magazynu) w obszarze [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) i skonfiguruj dwa problemy z szablonem.

1. W programie Visual Studio Code wybierz pozycję **File (Plik)** >**Open File (Otwórz plik)** .
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)** , aby otworzyć plik.
4. Zmień wiersz **apiVersion** na następujący:

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - Ciąg **apiVersion1** to nieprawidłowa nazwa elementu. Jest to błąd weryfikacji.
    - Zmień wersję interfejsu API na „2018-07-01”.  Jest to błąd wdrażania.

5. Wybierz pozycję **File (Plik)** >**Save As (Zapisz jako)** , aby zapisać plik jako **azuredeploy.json** na komputerze lokalnym.

## <a name="troubleshoot-the-validation-error"></a>Rozwiązywanie problemów z błędami weryfikacji

Zapoznaj się sekcją [Wdrażanie szablonu](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template), aby wdrożyć szablon.

Otrzymasz mniej więcej taki komunikat o błędzie z poziomu powłoki:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Komunikat o błędzie wskazuje na problem dotyczący nazwy **apiVersion1**.

Użyj programu Visual Studio Code, aby naprawić ten problem, zmieniając nazwę **apiVersion1** na **apiVersion**, a następnie zapisz szablon.

## <a name="troubleshoot-the-deployment-error"></a>Rozwiązywanie problemów związanych z błędami wdrażania

Zapoznaj się sekcją [Wdrażanie szablonu](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template), aby wdrożyć szablon.

Otrzymasz mniej więcej taki komunikat o błędzie z poziomu powłoki:

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Błąd wdrażania można odnaleźć w witrynie Azure Portal, korzystając z następującej procedury:

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Otwórz grupę zasobów, wybierając pozycję **Grupa zasobów**, a następnie wybierz nazwę grupy zasobów. Zostanie wyświetlony komunikat **1 niepowodzenie** w obszarze **Wdrożenia**.

    ![Samouczek dotyczący rozwiązywania problemów w usłudze Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Wybierz pozycję **Szczegóły błędu**.

    ![Samouczek dotyczący rozwiązywania problemów w usłudze Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Komunikat o błędzie jest taki sam jak ten pokazany wcześniej:

    ![Samouczek dotyczący rozwiązywania problemów w usłudze Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Ten błąd można również znaleźć w dzienniku aktywności:

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
2. Wybierz pozycję **Monitorowanie** > **Dziennik aktywności**.
3. Użyj filtrów, aby znaleźć dziennik.

    ![Samouczek dotyczący rozwiązywania problemów w usłudze Resource Manager](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Użyj programu Visual Studio Code, aby naprawić ten błąd, a następnie ponownie wdróż szablon.

Aby zapoznać się z listą typowych błędów, zobacz [Troubleshoot common Azure deployment errors with Azure Resource Manager](./resource-manager-common-deployment-errors.md) (Rozwiązywanie typowych błędów z wdrożeniem na platformie Azure w usłudze Azure Resource Manager).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak rozwiązywać problemy z błędami wdrażania szablonów usługi Resource Manager.  Aby uzyskać więcej informacji, zobacz [Troubleshoot common Azure deployment errors with Azure Resource Manager](./resource-manager-common-deployment-errors.md) (Rozwiązywanie typowych błędów z wdrożeniem na platformie Azure w usłudze Azure Resource Manager).
