---
title: Wdrażanie zasobów przy użyciu programu PowerShell i szablonu | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Resource Manager i programu Azure PowerShell, aby wdrożyć zasobów na platformie Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2017
ms.author: tomfitz
ms.openlocfilehash: eb4ebe0b1c0e4799aea6401b068d881e5aa47026
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)

W tym artykule wyjaśniono, jak wdrażanie zasobów na platformie Azure przy użyciu programu Azure PowerShell z szablonami usługi Resource Manager. Jeśli nie jesteś znasz koncepcji wdrażania i zarządzania rozwiązań platformy Azure, zobacz [Omówienie usługi Azure Resource Manager](resource-group-overview.md).

Szablon usługi Resource Manager wdrażania może być pliku lokalnego na komputerze lub plik znajdujący się w repozytorium, takich jak usługi GitHub. Szablon wdrożenia w tym artykule jest dostępny w [przykładowy szablon](#sample-template) sekcji lub jako [szablon konta magazynu w usłudze GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

W razie potrzeby Zainstaluj moduł Azure PowerShell, korzystając z instrukcji w [Przewodnik programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom `Login-AzureRmAccount` można utworzyć połączenia z platformą Azure.

<a id="deploy-local-template" />

## <a name="deploy-a-template-from-your-local-machine"></a>Wdrażanie szablonu z komputera lokalnego

W przypadku wdrażania zasobów na platformie Azure, możesz:

1. Zaloguj się do konta platformy Azure
2. Utwórz grupę zasobów, która służy jako kontener dla wdrożonych zasobów. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Można go do 90 znaków. Nie może kończyć się kropką.
3. Wdrożyć szablon, który definiuje zasoby do utworzenia grupy zasobów

Szablon może zawierać parametrów, które umożliwiają dostosowanie wdrożenia. Na przykład można podać wartości dostosowanych określonym środowisku (na przykład deweloperów, testowego i produkcyjnego). Przykładowy szablon definiuje parametru dla konta magazynu wersji.

Poniższy przykład tworzy grupę zasobów, a następnie wdraża szablonu z komputera lokalnego:

```powershell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat zawierający wynik:

```powershell
ProvisioningState       : Succeeded
```

## <a name="deploy-a-template-from-an-external-source"></a>Wdrażanie szablonu z zewnętrznego źródła

Zamiast szablony Menedżera zasobów są przechowywane na komputerze lokalnym, można przechowywać je w lokalizacji zewnętrznej. Szablony można przechowywać w repozytorium kontroli źródła, (na przykład GitHub). Lub przechowywać w koncie magazynu platformy Azure dla dostępu współdzielonego w Twojej organizacji.

Aby wdrożyć szablon zewnętrznego, użyj **TemplateUri** parametru. Użyj identyfikatora URI w przykładzie, aby wdrożyć przykładowy szablon z usługi GitHub.

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS
```

Powyższy przykład wymaga publicznie identyfikatora URI dla szablonu, który działa w przypadku większości scenariuszy, ponieważ szablon nie może zawierać dane poufne. Jeśli trzeba określić poufne dane (takie jak hasło administratora), należy przekazać tę wartość jako parametr bezpieczne. Jednak jeśli nie chcesz, aby szablon był publicznie dostępny, można chronić przez zapisanie jej w kontenerze prywatnego magazynu. Aby uzyskać informacje o wdrażaniu szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego dostępu współdzielonego, zobacz [wdrażanie szablonu prywatnej przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-powershell-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

W powłoce chmury Użyj następujących poleceń:

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri <copied URL> `
  -storageAccountType Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Wdrażanie na więcej niż jednej grupy zasobów lub subskrypcji

Zazwyczaj jest wdrażany, wszystkie zasoby w szablonie do pojedynczej grupy zasobów. Istnieją jednak scenariuszy, w której chcesz wdrożyć razem zestaw zasobów, ale umieszczenie ich w różnych grupach zasobów lub subskrypcji. Można wdrożyć tylko pięć grup zasobów w ramach jednego wdrożenia. Aby uzyskać więcej informacji, zobacz [zasobów Azure wdrożyć więcej niż jedną subskrypcję lub grupy zasobów](resource-manager-cross-resource-group-deployment.md).

<a id="parameter-file" />

## <a name="parameter-files"></a>Pliki parametrów

Zamiast przekazywanie parametrów jako wartości wbudowany w skrypcie, może okazać łatwiejsze w pliku JSON, który zawiera wartości parametrów. Plik parametru musi być w następującym formacie:

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

Zwróć uwagę, że w sekcji parametrów zawiera nazwę parametru, która odpowiada parametrowi zdefiniowanych w szablonie (storageAccountType). Plik parametrów zawiera wartość dla parametru. Ta wartość jest automatycznie przekazywane do szablonu podczas wdrażania. Można utworzyć wiele plików parametru dla różnych scenariuszy wdrażania i przekaż plik odpowiedni parametr. 

Skopiuj poprzedniego przykładu i zapisz go jako plik o nazwie `storage.parameters.json`.

Aby przekazać pliku lokalnego parametrów, należy użyć **TemplateParameterFile** parametru:

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

Można użyć wbudowanej parametrów i pliku parametrów lokalne w tej samej operacji wdrażania. Na przykład możesz określić niektóre wartości w pliku lokalnym parametru i dodać inne wbudowane wartości podczas wdrażania. Podaj wartości dla parametru w pliku lokalnym parametrów i wbudowany, pierwszeństwo ma wartość wbudowanego.

Jednak użycie pliku parametrów zewnętrznych, nie można przekazać wartości innych albo wbudowanego lub z pliku lokalnego. Po określeniu pliku parametrów w **TemplateParameterUri** parametr, wszystkie parametry są ignorowane w wierszach. Podaj wszystkie wartości parametrów w pliku zewnętrznym. Jeśli szablon zawiera poufne wartość, która w pliku parametrów nie można uwzględnić, Dodaj tę wartość do magazynu kluczy lub dynamicznie Podaj wartości wszystkich parametrów w tekście.

Jeśli szablon zawiera parametru o takiej samej nazwie jak jeden z parametrów polecenia programu PowerShell, programu PowerShell będzie zawierał z przyrostek parametru z szablonu **FromTemplate**. Na przykład parametr o nazwie **ResourceGroupName** Twojego konflikty szablonu z **ResourceGroupName** parametru w [AzureRmResourceGroupDeployment nowy](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) polecenia cmdlet. Zostanie wyświetlony monit o podać wartości **ResourceGroupNameFromTemplate**. Ogólnie rzecz biorąc należy unikać to pomyłka nie nadawanie nazw parametrów o takiej samej nazwie jako parametry używane dla operacji wdrożenia.

## <a name="test-a-template-deployment"></a>Testowanie wdrażania szablonu

Aby przetestować z szablonu i wartości parametrów bez faktycznie wdrażania zasobów, wpisz [AzureRmResourceGroupDeployment testu](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment). 

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Jeśli nie wykryto żadnych błędów, polecenie kończy działanie bez odpowiedzi. Jeśli zostanie wykryty błąd, polecenie zwróci komunikat o błędzie. Na przykład próba przekazania nieprawidłową wartość dla konta magazynu jednostka SKU, zwraca następujący błąd:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Jeśli szablon zawiera błąd składniowy, polecenie zwróci błąd wskazujący, że nie można przetworzyć szablonu. Komunikat wskazuje numer wiersza i umieść je błąd analizy.

```powershell
Test-AzureRmResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Aby użyć trybu pełną, użyj `Mode` parametru:

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```

## <a name="sample-template"></a>Przykładowy szablon

Następujący szablon jest używany w przykładach w niniejszym artykule. Skopiuj i zapisz go jako plik o nazwie storage.json. Aby poznać sposobu tworzenia tego szablonu, zobacz [Tworzenie pierwszego szablonu usługi Azure Resource Manager](resource-manager-create-first-template.md).  

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
* Przykłady w tym artykule wdrożenie zasobów do grupy zasobów w ramach subskrypcji domyślne. Aby użyć innej subskrypcji, zobacz [zarządzać wieloma subskrypcjami platformy Azure](/powershell/azure/manage-subscriptions-azureps).
* Zakończenie przykładowego skryptu, który wdraża szablonu, zobacz [skrypt wdrożenia szablonu usługi Resource Manager](resource-manager-samples-powershell-deploy.md).
* Aby poznać sposób definiowania parametry w szablonie, zobacz [poznać strukturę i składni szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby uzyskać wskazówki dotyczące rozwiązania typowych błędów wdrażania, zobacz [Rozwiąż typowe błędy wdrożenia usługi Azure z usługą Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Aby uzyskać informacje o wdrażaniu szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego, zobacz [wdrażanie szablonu prywatnej przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-powershell-sas-token.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).

