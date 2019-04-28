---
title: Przykład — Przeprowadzanie inspekcji ustawień inspekcji programu SQL Server
description: Ta przykładowa definicja zasad przeprowadza inspekcję ustawień inspekcji programu SQL Server przy użyciu efektu auditIfNotExists.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
origin.date: 04/27/2018
ms.date: 03/11/2019
ms.author: v-biyu
ms.openlocfilehash: c4510a316760fa948aa39627c9a9c517437b1d77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926732"
---
# <a name="sample---audit-sql-server-audit-settings"></a>Przykład — przeprowadzanie inspekcji ustawień inspekcji programu SQL Server

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

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```powershell
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9

New-AzPolicyAssignment -name "SQL Audit audit" -PolicyDefinition $definition -PolicyParameter '{"setting": {"value":"enabled"}}' -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Czyszczenie po wdrożeniu przy użyciu PowerShell

Uruchom następujące polecenie, aby usunąć przypisanie zasad.

```powershell
Remove-AzPolicyAssignment -Name "SQL Audit audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```cli
az policy assignment create --scope <scope> --name "SQL Audit audit" --policy a6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9 --params '{"setting": {"value":"enabled"}}'
```

### <a name="clean-up-azure-cli-deployment"></a>Czyszczenie wdrożenia przeprowadzonego za pomocą interfejsu wiersza polecenia platformy Azure

Uruchom następujące polecenie, aby usunąć przypisanie zasad.

```cli
az policy assignment delete --name "SQL Audit audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

- Zobacz więcej przykładów w witrynie [Przykłady dla usługi Azure Policy](index.md)