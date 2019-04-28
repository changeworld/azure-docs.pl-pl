---
title: Przykład — inspekcja przezroczystego szyfrowania danych w usłudze SQL Database
description: Te przykładowe definicje zasad sprawdzają, czy usługa SQL Database nie ma włączonego przezroczystego szyfrowania danych.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
origin.date: 04/27/2018
ms.date: 03/11/2019
ms.author: v-biyu
ms.openlocfilehash: e8ee800ff9f286f901a84a039e3c433442ae11b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60923356"
---
# <a name="sample---audit-sql-database-encryption"></a>Przykład — Inspekcja szyfrowania w usłudze SQL Database

Te wbudowane zasady sprawdzają, czy usługa SQL Database nie ma włączonego przezroczystego szyfrowania danych.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Przykładowy szablon

```json
{
   "properties": {
      "displayName": "Audit transparent data encryption status",
      "description": "Audit transparent data encryption status for SQL databases",
      "mode": "Indexed",
      "parameters": {
         "effect": {
            "type": "string",
            "defaultValue": "AuditIfNotExists",
            "allowedValues": [
               "AuditIfNotExists",
               "Disabled"
            ],
            "metadata": {
               "displayName": "Effect",
               "description": "Enable or disable the execution of the policy"
            }
         }
      },
      "policyRule": {
         "if": {
            "allOf": [
               {
                  "field": "type",
                  "equals": "Microsoft.Sql/servers/databases"
               },
               {
                  "field": "name",
                  "notEquals": "master"
               }
            ]
         },
         "then": {
            "effect": "[parameters('effect')]",
            "details": {
               "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
               "name": "current",
               "existenceCondition": {
                  "allOf": [
                     {
                        "field": "Microsoft.Sql/transparentDataEncryption.status",
                        "equals": "enabled"
                     }
                  ]
               }
            }
         }
      }
   }
}
```

Ten szablon można wdrożyć przy użyciu [witryny Azure Portal](#deploy-with-the-portal) lub [programu PowerShell](#deploy-with-powershell) albo [interfejsu wiersza polecenia platformy Azure](#deploy-with-azure-cli). Aby uzyskać dostęp do wbudowanych zasad, użyj identyfikatora `17k78e20-9358-41c9-923c-fb736d382a12`.

## <a name="deploy-with-the-portal"></a>Wdrażanie przy użyciu portalu

Podczas przypisywania zasad wybierz pozycję **Przeprowadź inspekcję stanu przezroczystego szyfrowania danych** z dostępnych wbudowanych definicji.

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```powershell
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/17k78e20-9358-41c9-923c-fb736d382a12

New-AzPolicyAssignment -name "SQL TDE Audit" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>Czyszczenie po wdrożeniu przy użyciu PowerShell

Uruchom następujące polecenie, aby usunąć przypisanie zasad.

```powershell
Remove-AzPolicyAssignment -Name "SQL TDE Audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```cli
az policy assignment create --scope <scope> --name "SQL TDE Audit" --policy 17k78e20-9358-41c9-923c-fb736d382a12
```

### <a name="clean-up-azure-cli-deployment"></a>Czyszczenie wdrożenia przeprowadzonego za pomocą interfejsu wiersza polecenia platformy Azure

Uruchom następujące polecenie, aby usunąć przypisanie zasad.

```cli
az policy assignment delete --name "SQL TDE Audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

- Zobacz więcej przykładów w witrynie [Przykłady dla usługi Azure Policy](index.md)