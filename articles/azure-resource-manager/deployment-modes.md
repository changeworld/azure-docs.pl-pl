---
title: Tryby wdrażania usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Opisuje sposób określenia, czy ma być używany tryb pełną lub przyrostową wdrożenia za pomocą usługi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2019
ms.author: tomfitz
ms.openlocfilehash: 9120e5f283f8d8da8da2c80959a335965a643409
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903897"
---
# <a name="azure-resource-manager-deployment-modes"></a>Tryby wdrażania usługi Azure Resource Manager

Podczas wdrażania zasobów, należy określić, że wdrożenie jest aktualizację przyrostową lub ukończenia aktualizacji.  Główną różnicą między tymi dwoma trybami jest sposób obsługiwania przez istniejące zasoby w grupie zasobów, które nie są w szablonie usługi Resource Manager. Domyślny tryb jest przyrostowy.

## <a name="incremental-and-complete-deployments"></a>Przyrostowe i kompletne wdrożeń

Dla obu trybów usługi Resource Manager spróbuje utworzyć wszystkie zasoby, które określono w szablonie. Jeśli zasób już istnieje w grupie zasobów i jego ustawienia są bez zmian, żadna operacja nie zostanie podjęte dla tego zasobu. Po zmianie wartości właściwości zasobu, zasób jest aktualizowana o te nowe wartości. Jeśli próbujesz zaktualizować lokalizację i typ istniejącego zasobu, wdrożenie zakończy się niepowodzeniem z powodu błędu. Zamiast tego należy wdrożyć nowy zasób z lokalizacją lub typu, należy.

W trybie, Menedżer zasobów **usuwa** zasoby, które istnieją w grupie zasobów, ale nie są określone w szablonie. Zasoby, które są określone w szablonie, ale nie wdrożona, ponieważ [warunek](resource-manager-templates-resources.md#condition) zwróci wartość false, nie są usuwane.

W trybie przyrostowym, Menedżer zasobów **pozostawia niezmienione** zasoby, które istnieją w grupie zasobów, ale nie są określone w szablonie. Podczas ponownego wdrażania zasobów w trybie przyrostowym, należy określić wszystkie wartości właściwości dla zasobu, a nie tylko te, które aktualizujesz. Jeśli nie określisz niektórych właściwości usługi Resource Manager interpretuje update jako zastąpienie tych wartości.

## <a name="example-result"></a>Przykład wyniku

Aby zilustrować różnica między trybami przyrostowe i kompletne, rozważmy następujący scenariusz.

**Grupa zasobów** zawiera:

* Zasobu A
* Zasób B
* Zasób języka C

**Szablon** zawiera:

* Zasobu A
* Zasób B
* Zasób, D

Po wdrożeniu w **przyrostowe** tryb, grupa zasobów ma:

* Zasobu A
* Zasób B
* Zasób języka C
* Zasób, D

Po wdrożeniu w **pełną** trybie C zasób zostanie usunięty. Grupa zasobów zawiera:

* Zasobu A
* Zasób B
* Zasób, D

## <a name="set-deployment-mode"></a>Ustaw tryb wdrożenia

Aby ustawić tryb wdrożenia, w przypadku wdrażania przy użyciu programu PowerShell, użyj `Mode` parametru.

```azurepowershell-interactive
New-AzureRmResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Aby ustawić tryb wdrożenia, w przypadku wdrażania przy użyciu wiersza polecenia platformy Azure, użyj `mode` parametru.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Korzystając z [połączonych lub zagnieżdżonych szablonów](resource-group-linked-templates.md), należy ustawić `mode` właściwość `Incremental`. Tylko szablony z katalogu głównego obsługują tryb całego procesu wdrażania.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat tworzenia szablonów usługi Resource Manager, zobacz [tworzenia usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby dowiedzieć się więcej na temat wdrażania zasobów, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](resource-group-template-deploy.md).
* Aby wyświetlić operacje dla dostawcy zasobów, zobacz [interfejsu API REST usługi Azure](/rest/api/).
