---
title: Tworzenie obszaru roboczego przy użyciu szablonu Azure Resource Manager
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć nowy obszar roboczy Azure Machine Learning przy użyciu szablonu Azure Resource Manager.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: b8584a77eaf9f8db13c948051c34b18236e2cc1a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986380"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Użyj szablonu Azure Resource Manager, aby utworzyć obszar roboczy dla Azure Machine Learning

W tym artykule przedstawiono kilka sposobów tworzenia obszaru roboczego Azure Machine Learning przy użyciu szablonów Azure Resource Manager. Szablon Menedżer zasobów ułatwia tworzenie zasobów jako jednej, skoordynowanej operacji. Szablon to dokument JSON, który definiuje zasoby, które są zbędne dla wdrożenia. Może również określać parametry wdrożenia. Parametry są używane do udostępniania wartości wejściowych przy użyciu szablonu.

Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcji platformy Azure**. Jeśli go nie masz, wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

* Aby użyć szablonu z interfejsu wiersza polecenia, musisz mieć [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) lub [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Następujący szablon Menedżer zasobów może służyć do tworzenia obszaru roboczego Azure Machine Learning i skojarzonych zasobów platformy Azure:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Ten szablon umożliwia utworzenie następujących usług platformy Azure:

* Grupa zasobów platformy Azure
* Konto usługi Azure Storage
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Obszar roboczy usługi Azure Machine Learning

Grupa zasobów to kontener, w którym znajdują się usługi. Różne usługi są wymagane przez obszar roboczy Azure Machine Learning.

Przykładowy szablon ma dwa parametry:

* **Lokalizacja** , w której zostaną utworzone grupy zasobów i usługi.

    Szablon będzie korzystać z lokalizacji wybranej dla większości zasobów. Wyjątkiem jest usługa Application Insights, która nie jest dostępna we wszystkich lokalizacjach, w których znajdują się inne usługi. W przypadku wybrania lokalizacji, w której jest ona niedostępna, usługa zostanie utworzona w lokalizacji Południowo-środkowe stany USA.

* **Nazwa obszaru roboczego**, która jest przyjazną nazwą obszaru roboczego Azure Machine Learning.

    > [!NOTE]
    > W nazwie obszaru roboczego nie jest rozróżniana wielkość liter.

    Nazwy innych usług są generowane losowo.

> [!TIP]
> Chociaż szablon skojarzony z tym dokumentem tworzy nowy Azure Container Registry, można również utworzyć nowy obszar roboczy bez tworzenia rejestru kontenerów. Jeśli w obszarze roboczym jest obecny rejestr kontenerów, zostanie on utworzony podczas wykonywania operacji wymagającej rejestru kontenerów. Na przykład szkolenie lub wdrożenie modelu.
>
> Możesz również odwoływać się do istniejącego rejestru kontenerów lub konta magazynu w szablonie Azure Resource Manager, zamiast tworzyć nowe.

Aby uzyskać więcej informacji na temat szablonów, zobacz następujące artykuły:

* [Tworzenie szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Wdrażanie aplikacji za pomocą szablonów Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Typy zasobów Microsoft. MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Wykonaj kroki opisane w sekcji [wdrażanie zasobów z szablonu niestandardowego](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Po nadejściu ekranu __Edytuj szablon__ wklej szablon z tego dokumentu.
1. Wybierz pozycję __Zapisz__ , aby użyć szablonu. Podaj następujące informacje i zaakceptuj wymienione warunki i postanowienia:

   * Subskrypcja: wybierz subskrypcję platformy Azure, która ma być używana dla tych zasobów.
   * Grupa zasobów: wybierz lub Utwórz grupę zasobów zawierającą usługi.
   * Nazwa obszaru roboczego: Nazwa do użycia dla obszaru roboczego Azure Machine Learning, który zostanie utworzony. Nazwa obszaru roboczego musi zawierać od 3 do 33 znaków. Może zawierać tylko znaki alfanumeryczne i znak "-".
   * Lokalizacja: Wybierz lokalizację, w której zostaną utworzone zasoby.

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów z szablonu niestandardowego](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

W tym przykładzie przyjęto założenie, że szablon został zapisany w pliku o nazwie `azuredeploy.json` w bieżącym katalogu:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) i [wdrażanie prywatnego szablonu Menedżer zasobów z tokenem SAS i Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W tym przykładzie przyjęto założenie, że szablon został zapisany w pliku o nazwie `azuredeploy.json` w bieżącym katalogu:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md) oraz [wdrażanie szablonu prywatnego Menedżer zasobów z tokenem SAS i interfejsem wiersza polecenia platformy Azure](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="resource-provider-errors"></a>Błędy dostawcy zasobów

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault zasad dostępu i szablonów Azure Resource Manager

Gdy używasz szablonu Azure Resource Manager do tworzenia obszaru roboczego i skojarzonych zasobów (w tym Azure Key Vault), wiele razy. Przykładowo wielokrotne użycie szablonu z tymi samymi parametrami w ramach potoku ciągłej integracji i wdrażania.

Większość operacji tworzenia zasobów za pomocą szablonów to idempotentne, ale Key Vault czyści zasady dostępu za każdym razem, gdy szablon jest używany. Wyczyszczenie zasad dostępu powoduje przerwanie dostępu do Key Vault wszystkich istniejących obszarów roboczych, które go używają. Na przykład funkcje zatrzymywania/tworzenia maszyny wirtualnej Azure Notebooks mogą zakończyć się niepowodzeniem.  

Aby uniknąć tego problemu, zalecamy zastosowanie jednej z następujących metod:

* Nie Wdrażaj szablonu więcej niż raz dla tych samych parametrów. Lub Usuń istniejące zasoby przed użyciem szablonu, aby utworzyć je ponownie.

* Przejrzyj zasady dostępu Key Vault a następnie użyj tych zasad, aby ustawić właściwość `accessPolicies` szablonu. Aby wyświetlić zasady dostępu, użyj następującego polecenia platformy Azure:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Aby uzyskać więcej informacji na temat używania `accessPolicies` sekcji szablonu, zobacz odwołanie do [obiektu AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Sprawdź, czy zasób Key Vault już istnieje. Jeśli tak, nie należy go ponownie tworzyć za pomocą szablonu. Na przykład, aby użyć istniejącego Key Vault zamiast tworzenia nowego, wprowadź następujące zmiany w szablonie:

    * **Dodaj** parametr, który akceptuje identyfikator istniejącego zasobu Key Vault:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Usuń** sekcję, która tworzy zasób Key Vault:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Usuń** wiersz `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` z sekcji `dependsOn` obszaru roboczego. **Zmień** również wpis `keyVault` w sekcji `properties` obszaru roboczego, aby odwołać się do parametru `keyVaultId`:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Po wprowadzeniu tych zmian możesz określić identyfikator istniejącego zasobu Key Vault podczas uruchamiania szablonu. Następnie szablon ponownie użyje Key Vault, ustawiając właściwość `keyVault` obszaru roboczego na jego identyfikator.

    Aby uzyskać identyfikator Key Vault, można odwoływać się do danych wyjściowych oryginalnego szablonu lub użyć interfejsu wiersza polecenia platformy Azure. Poniższe polecenie stanowi przykład użycia interfejsu wiersza polecenia platformy Azure w celu pobrania identyfikatora zasobu Key Vault:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    To polecenie zwraca wartość podobną do następującego tekstu:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Menedżer zasobów interfejsu API REST](../azure-resource-manager/templates/deploy-rest.md).
* [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
