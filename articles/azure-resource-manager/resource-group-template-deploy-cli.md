---
title: Wdrażanie zasobów przy użyciu wiersza polecenia platformy Azure i szablon | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Resource Manager i wiersza polecenia platformy Azure, aby wdrożyć zasobów na platformie Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
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
ms.openlocfilehash: ee8ce8453c24d19b3912d0f6cc506b3d3e72548f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603008"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Deploy resources with Resource Manager templates and Azure CLI (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure)

W tym artykule opisano sposób użycia 2.0 interfejsu wiersza polecenia platformy Azure z szablonami usługi Resource Manager do wdrażania zasobów platformy Azure. Jeśli nie jesteś znasz koncepcji wdrażania i zarządzania rozwiązań platformy Azure, zobacz [Omówienie usługi Azure Resource Manager](resource-group-overview.md).  

Szablon usługi Resource Manager wdrażania może być pliku lokalnego na komputerze lub plik znajdujący się w repozytorium, takich jak usługi GitHub. Szablon wdrożenia w tym artykule jest dostępny w [przykładowy szablon](#sample-template) sekcji lub jako [szablon konta magazynu w usłudze GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Jeśli nie masz zainstalowanych wiersza polecenia platformy Azure, możesz użyć [powłoki chmury](#deploy-template-from-cloud-shell).

## <a name="deploy-local-template"></a>Wdrażanie lokalnego szablonu

W przypadku wdrażania zasobów na platformie Azure, możesz:

1. Zaloguj się do konta platformy Azure
2. Utwórz grupę zasobów, która służy jako kontener dla wdrożonych zasobów. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Można go do 90 znaków. Nie może kończyć się kropką.
3. Wdrożyć szablon, który definiuje zasoby do utworzenia grupy zasobów

Szablon może zawierać parametrów, które umożliwiają dostosowanie wdrożenia. Na przykład można podać wartości dostosowanych określonym środowisku (na przykład deweloperów, testowego i produkcyjnego). Przykładowy szablon definiuje parametru dla konta magazynu wersji. 

Poniższy przykład tworzy grupę zasobów, a następnie wdraża szablonu z komputera lokalnego:

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat zawierający wynik:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>Wdrażanie szablonu zewnętrznych

Zamiast szablony Menedżera zasobów są przechowywane na komputerze lokalnym, można przechowywać je w lokalizacji zewnętrznej. Szablony można przechowywać w repozytorium kontroli źródła, (na przykład GitHub). Lub przechowywać w koncie magazynu platformy Azure dla dostępu współdzielonego w Twojej organizacji.

Aby wdrożyć szablon zewnętrznego, użyj **szablon uri** parametru. Użyj identyfikatora URI w przykładzie, aby wdrożyć przykładowy szablon z usługi GitHub.
   
```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
    --parameters storageAccountType=Standard_GRS
```

Powyższy przykład wymaga publicznie identyfikatora URI dla szablonu, który działa w przypadku większości scenariuszy, ponieważ szablon nie może zawierać dane poufne. Jeśli trzeba określić poufne dane (takie jak hasło administratora), należy przekazać tę wartość jako parametr bezpieczne. Jednak jeśli nie chcesz, aby szablon był publicznie dostępny, można chronić przez zapisanie jej w kontenerze prywatnego magazynu. Aby uzyskać informacje o wdrażaniu szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego dostępu współdzielonego, zobacz [wdrażanie szablonu prywatnej przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

W powłoce chmury Użyj następujących poleceń:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Wdrażanie na więcej niż jednej grupy zasobów lub subskrypcji

Zazwyczaj jest wdrażany, wszystkie zasoby w szablonie do pojedynczej grupy zasobów. Istnieją jednak scenariuszy, w której chcesz wdrożyć razem zestaw zasobów, ale umieszczenie ich w różnych grupach zasobów lub subskrypcji. Można wdrożyć tylko pięć grup zasobów w ramach jednego wdrożenia. Aby uzyskać więcej informacji, zobacz [zasobów Azure wdrożyć więcej niż jedną subskrypcję lub grupy zasobów](resource-manager-cross-resource-group-deployment.md).

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

Aby przekazać pliku lokalnego parametrów, należy użyć `@` do określenia pliku lokalnego o nazwie storage.parameters.json.

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>Testowanie wdrażania szablonu

Aby przetestować z szablonu i wartości parametrów bez faktycznie wdrażania zasobów, wpisz [zweryfikować wdrożenie grupy az](/cli/azure/group/deployment#az_group_deployment_validate). 

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

Jeśli nie wykryto żadnych błędów, polecenie zwraca informacje dotyczące wdrażania testu. W szczególności należy zauważyć, że **błąd** ma wartość null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Jeśli zostanie wykryty błąd, polecenie zwróci komunikat o błędzie. Na przykład próba przekazania nieprawidłową wartość dla konta magazynu jednostka SKU, zwraca następujący błąd:

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

Jeśli szablon zawiera błąd składniowy, polecenie zwróci błąd wskazujący, że nie można przetworzyć szablonu. Komunikat wskazuje numer wiersza i umieść je błąd analizy.

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

Aby użyć trybu pełną, użyj `mode` parametru:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
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
* Przykłady w tym artykule wdrożenie zasobów do grupy zasobów w ramach subskrypcji domyślne. Aby użyć innej subskrypcji, zobacz [zarządzać wieloma subskrypcjami platformy Azure](/cli/azure/manage-azure-subscriptions-azure-cli).
* Zakończenie przykładowego skryptu, który wdraża szablonu, zobacz [skrypt wdrożenia szablonu usługi Resource Manager](resource-manager-samples-cli-deploy.md).
* Aby poznać sposób definiowania parametry w szablonie, zobacz [poznać strukturę i składni szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby uzyskać wskazówki dotyczące rozwiązania typowych błędów wdrażania, zobacz [Rozwiąż typowe błędy wdrożenia usługi Azure z usługą Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Aby uzyskać informacje o wdrażaniu szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego, zobacz [wdrażanie szablonu prywatnej przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-cli-sas-token.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).
