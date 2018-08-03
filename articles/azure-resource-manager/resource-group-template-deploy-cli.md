---
title: Wdrażanie zasobów przy użyciu wiersza polecenia platformy Azure i szablonu | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Resource Manager i interfejsu wiersza polecenia platformy Azure do wdrażania zasobów platformy Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: tomfitz
ms.openlocfilehash: 510cba0c2e27ab56ea26a476258fd7480b80e0d2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420407"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Deploy resources with Resource Manager templates and Azure CLI (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure)

W tym artykule wyjaśniono, jak używać wiersza polecenia platformy Azure przy użyciu szablonów usługi Resource Manager do wdrażania zasobów na platformie Azure. Jeśli nie znasz pojęć dotyczących wdrażania i zarządzania Twoich rozwiązań platformy Azure, zobacz [Omówienie usługi Azure Resource Manager](resource-group-overview.md).  

Szablon usługi Resource Manager możesz wdrożyć, mogą być plikiem lokalnym na komputerze lub zewnętrznego pliku, który znajduje się w repozytorium, takich jak GitHub. Szablon wdrożenia w tym artykule jest dostępny w [przykładowy szablon](#sample-template) sekcji lub jako [szablon konta magazynu w usłudze GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Jeśli nie masz zainstalowany interfejs wiersza polecenia platformy Azure, możesz użyć [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deploy-local-template"></a>Wdrażanie lokalnego szablonu

Podczas wdrażania zasobów na platformie Azure, możesz:

1. Zaloguj się do konta platformy Azure
2. Utwórz grupę zasobów, która służy jako kontener dla wdrożonych zasobów. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Może być maksymalnie 90 znaków. Nie może kończyć się kropką.
3. Wdrożyć szablon który definiuje zasoby do utworzenia grupy zasobów

Szablon może zawierać parametrów, które umożliwiają dostosowanie wdrożenia. Na przykład możesz podać wartości, które są dostosowane dla określonego środowiska (na przykład deweloperskim, testowym i produkcyjnym). Przykładowy szablon definiuje parametr dla jednostki SKU konta magazynu. 

Poniższy przykład tworzy grupę zasobów i służy do wdrażania szablonu z komputera lokalnego:

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat, który zawiera wynik:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>Wdrażanie szablonu zewnętrznych

Zamiast przechowywać szablonów usługi Resource Manager na komputerze lokalnym, użytkownik może chcieć przechowywać je w lokalizacji zewnętrznej. Szablony można przechowywać w repozytorium kontroli źródła (na przykład GitHub). Lub można przechowywać na koncie magazynu platformy Azure w celu zapewnienia dostępu współdzielonego w Twojej organizacji.

Aby wdrożyć szablon zewnętrznego, użyj **identyfikator uri szablonu** parametru. Użyj identyfikatora URI w przykładzie, aby wdrożyć przykładowy szablon z serwisu GitHub.
   
```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
    --parameters storageAccountType=Standard_GRS
```

Poprzedni przykład wymaga publicznie identyfikator URI dla szablonu, który działa w przypadku większości scenariuszy, ponieważ szablon nie może zawierać dane poufne. Jeśli musisz określić dane poufne (na przykład hasło administratora), należy przekazać tę wartość jako parametru secure. Jednak jeśli chcesz, aby szablon był dostępny publicznie, można go chronić dzięki przechowywaniu go w kontenerze magazynu prywatnego. Aby uzyskać informacji o wdrażaniu szablonu, który wymaga tokenu (SAS) sygnatury dostępu współdzielonego, zobacz [wdrażanie prywatnego szablonu przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

W usłudze Cloud Shell Użyj następujących poleceń:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Wdrażanie na więcej niż jednej grupy zasobów lub subskrypcji

Zazwyczaj można wdrażać wszystkich zasobów w szablonie do pojedynczej grupy zasobów. Jednak istnieją scenariusze, w której chcesz wdrożyć zestaw zasobów ze sobą, ale umieścić je w różnych grupach zasobów lub subskrypcji. Można wdrożyć tylko pięć grup zasobów w ramach pojedynczego wdrożenia. Aby uzyskać więcej informacji, zobacz [wdrażania zasobów platformy Azure do więcej niż jedną subskrypcję lub grupę zasobów](resource-manager-cross-resource-group-deployment.md).

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

Aby przekazać plik parametrów lokalnych, należy użyć `@` do określenia pliku lokalnego o nazwie storage.parameters.json.

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>Testowanie wdrażania szablonu

Aby przetestować wartości szablonu oraz parametrów bez faktycznego wdrażania zasobów, użyj [zweryfikować wdrożenie grupy az](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

Jeśli zostaną wykryte żadne błędy, polecenie zwraca informacje o wdrożeniu testu. W szczególności zwróć uwagę, że **błąd** ma wartość null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Jeśli zostanie wykryty błąd, to polecenie zwraca komunikat o błędzie. Na przykład próba przekazania niepoprawną wartość dla konta magazynu jednostki SKU i zwraca następujący błąd:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Jeśli szablon zawiera błąd składniowy, polecenie zwraca komunikat o błędzie informujący, że nie można przeanalizować szablonu. Komunikat wskazuje, numer wiersza i położenie błąd analizy.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Aby użyć w trybie, użyj `mode` parametru:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
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
* Przykłady w niniejszym artykule wdrażanie zasobów w grupie zasobów w subskrypcji domyślnej. Aby użyć innej subskrypcji, zobacz [Zarządzanie wieloma subskrypcjami platformy Azure](/cli/azure/manage-azure-subscriptions-azure-cli).
* Aby uzyskać kompletny przykładowy skrypt, który służy do wdrażania szablonu, zobacz [skrypt wdrażania szablonu usługi Resource Manager](resource-manager-samples-cli-deploy.md).
* Aby dowiedzieć się, jak zdefiniować parametry w szablonie, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby uzyskać porady dotyczące rozwiązywania typowych problemów wdrażania, zobacz [Rozwiązywanie typowych problemów wdrażania na platformie Azure przy użyciu usługi Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Aby uzyskać informacje o wdrażaniu szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego, zobacz [wdrażanie prywatnego szablonu przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-cli-sas-token.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).
