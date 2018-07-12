---
title: Wdrażanie zasobów przy użyciu programu PowerShell i szablonu | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Resource Manager i programu Azure PowerShell do wdrażania zasobów platformy Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2017
ms.author: tomfitz
ms.openlocfilehash: 714f5f2d4aa1635da8aa62139be5c80e7761ae27
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38486744"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)

W tym artykule wyjaśniono, jak używać programu Azure PowerShell przy użyciu szablonów usługi Resource Manager do wdrażania zasobów na platformie Azure. Jeśli nie znasz pojęć dotyczących wdrażania i zarządzania Twoich rozwiązań platformy Azure, zobacz [Omówienie usługi Azure Resource Manager](resource-group-overview.md).

Szablon usługi Resource Manager możesz wdrożyć, mogą być plikiem lokalnym na komputerze lub zewnętrznego pliku, który znajduje się w repozytorium, takich jak GitHub. Szablon wdrożenia w tym artykule jest dostępny w [przykładowy szablon](#sample-template) sekcji lub jako [szablon konta magazynu w usłudze GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

W razie potrzeby zainstaluj moduł Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

<a id="deploy-local-template" />

## <a name="deploy-a-template-from-your-local-machine"></a>Wdróż szablon z komputera lokalnego

Podczas wdrażania zasobów na platformie Azure, możesz:

1. Zaloguj się do konta platformy Azure
2. Utwórz grupę zasobów, która służy jako kontener dla wdrożonych zasobów. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Może być maksymalnie 90 znaków. Nie może kończyć się kropką.
3. Wdrożyć szablon który definiuje zasoby do utworzenia grupy zasobów

Szablon może zawierać parametrów, które umożliwiają dostosowanie wdrożenia. Na przykład możesz podać wartości, które są dostosowane dla określonego środowiska (na przykład deweloperskim, testowym i produkcyjnym). Przykładowy szablon definiuje parametr dla jednostki SKU konta magazynu.

Poniższy przykład tworzy grupę zasobów i służy do wdrażania szablonu z komputera lokalnego:

```powershell
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat, który zawiera wynik:

```powershell
ProvisioningState       : Succeeded
```

## <a name="deploy-a-template-from-an-external-source"></a>Wdróż szablon z zewnętrznego źródła

Zamiast przechowywać szablonów usługi Resource Manager na komputerze lokalnym, użytkownik może chcieć przechowywać je w lokalizacji zewnętrznej. Szablony można przechowywać w repozytorium kontroli źródła (na przykład GitHub). Lub można przechowywać na koncie magazynu platformy Azure w celu zapewnienia dostępu współdzielonego w Twojej organizacji.

Aby wdrożyć szablon zewnętrznego, użyj **TemplateUri** parametru. Użyj identyfikatora URI w przykładzie, aby wdrożyć przykładowy szablon z serwisu GitHub.

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS
```

Poprzedni przykład wymaga publicznie identyfikator URI dla szablonu, który działa w przypadku większości scenariuszy, ponieważ szablon nie może zawierać dane poufne. Jeśli musisz określić dane poufne (na przykład hasło administratora), należy przekazać tę wartość jako parametru secure. Jednak jeśli chcesz, aby szablon był dostępny publicznie, można go chronić dzięki przechowywaniu go w kontenerze magazynu prywatnego. Aby uzyskać informacji o wdrażaniu szablonu, który wymaga tokenu (SAS) sygnatury dostępu współdzielonego, zobacz [wdrażanie prywatnego szablonu przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-powershell-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

W usłudze Cloud Shell Użyj następujących poleceń:

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri <copied URL> `
  -storageAccountType Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Wdrażanie na więcej niż jednej grupy zasobów lub subskrypcji

Zazwyczaj można wdrażać wszystkich zasobów w szablonie do pojedynczej grupy zasobów. Jednak istnieją scenariusze, w której chcesz wdrożyć zestaw zasobów ze sobą, ale umieścić je w różnych grupach zasobów lub subskrypcji. Można wdrożyć tylko pięć grup zasobów w ramach pojedynczego wdrożenia. Aby uzyskać więcej informacji, zobacz [wdrażania zasobów platformy Azure do więcej niż jedną subskrypcję lub grupę zasobów](resource-manager-cross-resource-group-deployment.md).

<a id="parameter-file" />

## <a name="parameter-files"></a>Pliki parametrów

Zamiast przekazywania parametrów jako wartości wbudowanych w skrypcie, użytkownik może okazać się łatwiejszy w obsłudze pliku JSON, który zawiera wartości parametrów. Plik parametrów musi być w następującym formacie:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Należy zauważyć, że w sekcji parametrów zawiera nazwę parametru, która pasuje do parametrów zdefiniowanych w szablonie (storageAccountType). Plik parametrów zawiera wartość dla parametru. Ta wartość jest automatycznie przekazanych do szablonu podczas wdrażania. Możesz utworzyć wiele plików parametru dla różnych scenariuszy wdrażania i następnie przekazać plik odpowiednich parametrów. 

Poprzedni przykład skopiuj i zapisz go jako plik o nazwie `storage.parameters.json`.

Aby przekazać plik parametrów lokalnych, należy użyć **TemplateParameterFile** parametru:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Aby przekazać plik parametrów zewnętrznego, użyj **TemplateParameterUri** parametru:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

Można użyć wbudowanego parametrów i pliku parametrów lokalnych w tej samej operacji wdrożenia. Na przykład można określić niektóre wartości w pliku parametrów lokalnych i dodać inne wbudowane wartości podczas wdrażania. Jeśli możesz podać wartości parametrów w pliku parametrów lokalnych i wbudowane, pierwszeństwo ma wartość wbudowanej.

Jednak użycie pliku parametrów zewnętrzne, nie można przekazać wartości innych jako wbudowane lub z pliku lokalnego. Po określeniu w pliku parametrów w **TemplateParameterUri** parametru, wszystkie parametry są ignorowane w wierszach. Podaj wszystkie wartości parametrów w pliku zewnętrznym. Jeśli szablon zawiera poufne wartość, która nie może zawierać w pliku parametrów, Dodaj tę wartość do magazynu kluczy lub dynamicznego udostępniania wszystkie wbudowane wartości parametru.

Jeśli szablon zawiera parametr o nazwie identycznej z nazwą jednego z parametrów polecenia programu PowerShell, program PowerShell wyświetli parametr z szablonu z przyrostkowa **FromTemplate**. Na przykład parametr o nazwie **ResourceGroupName** w swojej szablonu jest w konflikcie z **ResourceGroupName** parametru w [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) polecenie cmdlet. Zostanie wyświetlony monit o podanie wartości **ResourceGroupNameFromTemplate**. Ogólnie rzecz biorąc należy unikać tego pomyłek przez nie nazywanie parametrów o takiej samej nazwie jako parametry używane dla operacji wdrożenia.

## <a name="test-a-template-deployment"></a>Testowanie wdrażania szablonu

Aby przetestować wartości szablonu oraz parametrów bez faktycznego wdrażania zasobów, użyj [Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment). 

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Jeśli zostaną wykryte żadne błędy, polecenie zakończy się bez odpowiedzi. Jeśli zostanie wykryty błąd, to polecenie zwraca komunikat o błędzie. Na przykład próba przekazania niepoprawną wartość dla konta magazynu jednostki SKU i zwraca następujący błąd:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Jeśli szablon zawiera błąd składniowy, polecenie zwraca komunikat o błędzie informujący, że nie można przeanalizować szablonu. Komunikat wskazuje, numer wiersza i położenie błąd analizy.

```powershell
Test-AzureRmResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Aby użyć w trybie, użyj `Mode` parametru:

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```

## <a name="sample-template"></a>Przykładowy szablon

Następujący szablon jest używany w przykładach w tym artykule. Skopiuj i zapisz go jako plik o nazwie storage.json. Aby zrozumieć sposób tworzenia tego szablonu, zobacz [Tworzenie pierwszego szablonu usługi Azure Resource Manager](resource-manager-create-first-template.md).  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>Kolejne kroki
* Przykłady w niniejszym artykule wdrażanie zasobów w grupie zasobów w subskrypcji domyślnej. Aby użyć innej subskrypcji, zobacz [Zarządzanie wieloma subskrypcjami platformy Azure](/powershell/azure/manage-subscriptions-azureps).
* Aby uzyskać kompletny przykładowy skrypt, który służy do wdrażania szablonu, zobacz [skrypt wdrażania szablonu usługi Resource Manager](resource-manager-samples-powershell-deploy.md).
* Aby dowiedzieć się, jak zdefiniować parametry w szablonie, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby uzyskać porady dotyczące rozwiązywania typowych problemów wdrażania, zobacz [Rozwiązywanie typowych problemów wdrażania na platformie Azure przy użyciu usługi Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Aby uzyskać informacje o wdrażaniu szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego, zobacz [wdrażanie prywatnego szablonu przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-powershell-sas-token.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).

