---
title: Przywracanie błędu do pomyślnego wdrożenia
description: Określ, że wdrożenie nie powiodło się należy przywrócić do pomyślnego wdrożenia.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460147"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Wycofywanie błędu do pomyślnego wdrożenia

Gdy wdrożenie nie powiedzie się, można automatycznie wdrożyć wcześniejsze, pomyślne wdrożenie z historii wdrażania. Ta funkcja jest przydatna, jeśli masz znany dobry stan dla wdrożenia infrastruktury i chcesz powrócić do tego stanu. Istnieje wiele zastrzeżeń i ograniczeń:

- Ponowne wdrożenie jest uruchamiane dokładnie tak, jak zostało uruchomione wcześniej z tymi samymi parametrami. Nie można zmienić parametrów.
- Poprzednie wdrożenie jest uruchamiane w [trybie kompletnym](./deployment-modes.md#complete-mode). Wszystkie zasoby nie uwzględnione w poprzednim wdrożeniu są usuwane, a wszystkie konfiguracje zasobów są ustawione na ich poprzedni stan. Upewnij się, że w pełni rozumiesz [tryby wdrażania](./deployment-modes.md).
- Ponowne wdrożenie dotyczy tylko zasobów, nie ma to wpływu na wszelkie zmiany danych.
- Tej funkcji można używać tylko w przypadku wdrożeń grup zasobów, a nie wdrożeń na poziomie subskrypcji lub grupy zarządzania. Aby uzyskać więcej informacji na temat wdrażania na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](./deploy-to-subscription.md).
- Tej opcji można używać tylko w przypadku wdrożeń na poziomie głównym. Wdrożenia z szablonu zagnieżdżonego nie są dostępne do ponownego wdrożenia.

Aby użyć tej opcji, wdrożenia muszą mieć unikatowe nazwy, aby można je było zidentyfikować w historii. Jeśli nie masz unikatowych nazw, bieżące wdrożenie nie powiodło się może zastąpić wcześniej pomyślne wdrożenie w historii.

## <a name="powershell"></a>PowerShell

Aby ponownie wdrożyć ostatnie pomyślne `-RollbackToLastDeployment` wdrożenie, dodaj parametr jako flagę.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Aby ponownie wdrożyć określone wdrożenie, `-RollBackDeploymentName` należy użyć parametru i podać nazwę wdrożenia. Określone wdrożenie musi zakończyć się pomyślnie.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby ponownie wdrożyć ostatnie pomyślne `--rollback-on-error` wdrożenie, dodaj parametr jako flagę.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Aby ponownie wdrożyć określone wdrożenie, `--rollback-on-error` należy użyć parametru i podać nazwę wdrożenia. Określone wdrożenie musi zakończyć się pomyślnie.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>Interfejs API REST

Aby ponownie wdrożyć ostatnie pomyślne wdrożenie, jeśli bieżące wdrożenie zakończy się niepowodzeniem, należy użyć:

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

Aby ponownie wdrożyć określone wdrożenie, jeśli bieżące wdrożenie nie powiedzie się, należy użyć:

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

Określone wdrożenie musi zakończyć się pomyślnie.

## <a name="next-steps"></a>Następne kroki

- Aby bezpiecznie wdrożyć usługę w więcej niż jednym regionie, zobacz [Usługa Azure Deployment Manager](deployment-manager-overview.md).
- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [Tryby wdrażania usługi Azure Resource Manager](deployment-modes.md).
- Aby dowiedzieć się, jak zdefiniować parametry w szablonie, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](template-syntax.md).
- Aby uzyskać informacje dotyczące wdrażania szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego, zobacz [Wdrażanie szablonu prywatnego za pomocą tokenu sygnatury dostępu Współdzielonego](secure-template-with-sas-token.md).
