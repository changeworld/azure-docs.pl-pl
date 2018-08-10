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
ms.date: 08/07/2018
ms.author: tomfitz
ms.openlocfilehash: 00800cb233776878e1fa330ce72cb067b8c698f3
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39634620"
---
# <a name="azure-resource-manager-deployment-modes"></a>Tryby wdrażania usługi Azure Resource Manager
Podczas wdrażania zasobów, należy określić, że wdrożenie jest aktualizację przyrostową lub ukończenia aktualizacji.  Główną różnicą między tymi dwoma trybami jest sposób obsługiwania przez istniejące zasoby w grupie zasobów, które nie są w szablonie usługi Resource Manager.
Domyślny tryb jest przyrostowy.

## <a name="incremental-and-complete-deployments"></a>Przyrostowe i kompletne wdrożeń
Podczas wdrażania zasobów:

* W trybie, Menedżer zasobów **usuwa** zasoby, które istnieją w grupie zasobów, ale nie są określone w szablonie. 
* W trybie przyrostowym, Menedżer zasobów **pozostawia niezmienione** zasoby, które istnieją w grupie zasobów, ale nie są określone w szablonie.

Dla obu trybów próbuje aprowizację wszystkich zasobów określone w szablonie usługi Resource Manager. Jeśli zasób już istnieje w grupie zasobów, a jego ustawienia są bez zmian, wykonanie tej operacji skutkuje bez zmian. Jeśli zmienisz ustawienia dla zasobu, zasób był zaopatrzony te nowe ustawienia. Jeśli próbujesz zaktualizować lokalizację i typ istniejącego zasobu, wdrożenie zakończy się niepowodzeniem z powodu błędu. Zamiast tego należy wdrożyć nowy zasób z lokalizacją lub typu, należy.

## <a name="example-result"></a>Przykład wyniku

Aby zilustrować różnica między trybami przyrostowe i kompletne, rozważmy następujący scenariusz.

**Istniejącą grupę zasobów** zawiera:

* Zasobu A
* Zasób B
* Zasób języka C

**Szablon** definiuje:

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

```powershell
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

Aby ustawić tryb wdrożenia [połączonych lub zagnieżdżonych szablonów](resource-group-linked-templates.md), użyj `mode` właściwości.

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

