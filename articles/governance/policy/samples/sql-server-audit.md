---
title: Przykład — Przeprowadzanie inspekcji ustawień inspekcji programu SQL Server
description: Te przykładowe zasady przeprowadzają inspekcję ustawień inspekcji programu SQL Server.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 8f1c34be9b16830ef8e09c018a7971347ce9c3f8
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847870"
---
# <a name="audit-sql-server-audit-settings"></a>Przeprowadzanie inspekcji ustawień inspekcji programu SQL Server

Te wbudowane zasady przeprowadzają inspekcję programu SQL Server w zależności od tego, czy włączono ustawienia inspekcji.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Przykładowy szablon

```json
{
  "if": {
    "field": "type",
    "equals": "Microsoft.SQL/servers"
  },
  "then": {
    "effect": "auditIfNotExists",
    "details": {
      "type": "Microsoft.SQL/servers/auditingSettings",
      "name": "default",
      "existenceCondition": {
        "allOf": [
          {
            "field": "Microsoft.Sql/auditingSettings.state",
            "equals": "[parameters('setting')]"
          }
        ]
      }
    }
  }
}
```

Ten szablon można wdrożyć przy użyciu [witryny Azure Portal](#deploy-with-the-portal) lub [programu PowerShell](#deploy-with-powershell) albo [interfejsu wiersza polecenia platformy Azure](#deploy-with-azure-cli). Aby uzyskać dostęp do wbudowanych zasad, użyj identyfikatora `a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9`.

## <a name="parameters"></a>Parametry

Aby przekazać wartości parametru, użyj następującego formatu:

```json
{"setting": {"value":"enabled"}}
```

## <a name="deploy-with-the-portal"></a>Wdrażanie przy użyciu portalu

Podczas przypisywania zasad wybierz pozycję **Przeprowadź inspekcję ustawienia inspekcji na poziomie programu SQL Server** z dostępnych wbudowanych definicji.

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9

New-AzPolicyAssignment -name "SQL Audit audit" -PolicyDefinition $definition -PolicyParameter '{"setting": {"value":"enabled"}}' -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Czyszczenie po wdrożeniu przy użyciu PowerShell

Uruchom następujące polecenie, aby usunąć przypisanie zasad.

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "SQL Audit audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL Audit audit" --policy a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9 --params '{"setting": {"value":"enabled"}}'
```

### <a name="clean-up-azure-cli-deployment"></a>Czyszczenie wdrożenia przeprowadzonego za pomocą interfejsu wiersza polecenia platformy Azure

Uruchom następujące polecenie, aby usunąć przypisanie zasad.

```azurecli-interactive
az policy assignment delete --name "SQL Audit audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

- Zobacz więcej przykładów w witrynie [Przykłady dla usługi Azure Policy](index.md)