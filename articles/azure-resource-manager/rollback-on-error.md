---
title: Wycofywanie z powodu błędu do pomyślnego wdrożenia platformy Azure
description: Określ, że niepowodzenie wdrożenia powinno zostać przywrócone do pomyślnego wdrożenia.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: tomfitz
ms.openlocfilehash: 035b684bb9f5fbc10eb13e642c3fd5945b85c561
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975448"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Wycofywanie z powodu błędu do pomyślnego wdrożenia

W przypadku niepowodzenia wdrożenia można automatycznie ponownie wdrożyć wcześniejsze, pomyślne wdrożenie z historii wdrożenia. Ta funkcja jest przydatna, jeśli masz znany dobry stan wdrożenia infrastruktury i chcesz przywrócić ten stan. Istnieje wiele zastrzeżeń i ograniczeń:

- Ponowne wdrożenie jest uruchamiane dokładnie tak, jak zostało wcześniej uruchomione z tymi samymi parametrami. Nie można zmienić parametrów.
- Poprzednie wdrożenie jest uruchamiane przy użyciu [trybu kompletnego](./deployment-modes.md#complete-mode). Wszystkie zasoby, które nie są uwzględnione w poprzednim wdrożeniu, są usuwane, a wszystkie konfiguracje zasobów są ustawione na ich poprzedni stan. Upewnij się, że w pełni rozumiesz [tryby wdrażania](./deployment-modes.md).
- Ponowne wdrożenie ma wpływ tylko na zasoby, a zmiany danych nie są modyfikowane.
- Tej funkcji można używać tylko z wdrożeniami grup zasobów, a nie z wdrożeniami na poziomie grupy lub grup zarządzania. Aby uzyskać więcej informacji na temat wdrażania na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](./deploy-to-subscription.md).
- Tej opcji można używać tylko z wdrożeniami na poziomie głównym. Wdrożenia z zagnieżdżonego szablonu nie są dostępne do ponownego wdrożenia.

Aby użyć tej opcji, wdrożenia muszą mieć unikatowe nazwy, aby można je było zidentyfikować w historii. Jeśli nie masz unikatowych nazw, bieżące wdrożenie nie powiodło się w historii.

## <a name="powershell"></a>PowerShell

Aby ponownie wdrożyć ostatnie pomyślne wdrożenie, należy dodać parametr `-RollbackToLastDeployment` jako flagę.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Aby ponownie wdrożyć określone wdrożenie, użyj parametru `-RollBackDeploymentName` i podaj nazwę wdrożenia. Określone wdrożenie musi się powieść.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby ponownie wdrożyć ostatnie pomyślne wdrożenie, należy dodać parametr `--rollback-on-error` jako flagę.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Aby ponownie wdrożyć określone wdrożenie, użyj parametru `--rollback-on-error` i podaj nazwę wdrożenia. Określone wdrożenie musi się powieść.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>Interfejs API REST

Aby ponownie wdrożyć ostatnie pomyślne wdrożenie, jeśli bieżące wdrożenie zakończy się niepowodzeniem, użyj:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Aby ponownie wdrożyć określone wdrożenie, jeśli bieżące wdrożenie nie powiedzie się, użyj:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Określone wdrożenie musi się powieść.

## <a name="next-steps"></a>Następne kroki

- Aby bezpiecznie wdrożyć usługę w więcej niż jednym regionie, zobacz [Azure Menedżer wdrażania](deployment-manager-overview.md).
- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [Azure Resource Manager trybami wdrożenia](deployment-modes.md).
- Aby zrozumieć, jak definiować parametry w szablonie, zobacz [Opis struktury i składni szablonów Azure Resource Manager](resource-group-authoring-templates.md).
- Informacje o wdrażaniu szablonu wymagającego tokenu SAS można znaleźć w temacie [Deploy Private Template with SAS token](resource-manager-powershell-sas-token.md).
