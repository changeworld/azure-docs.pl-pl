---
title: Konfigurowanie kluczy zarządzanych przez klienta w usłudze Azure Eksplorator danych przy użyciu szablonu Azure Resource Manager
description: W tym artykule opisano sposób konfigurowania szyfrowania kluczy zarządzanych przez klienta na danych na platformie Azure Eksplorator danych przy użyciu szablonu Azure Resource Manager.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: bff8c50cdece803e030c0975a9b14ac5739baaf7
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725808"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Konfigurowanie kluczy zarządzanych przez klienta przy użyciu szablonu Azure Resource Manager

> [!div class="op_single_selector"]
> * [C#](create-cluster-database-csharp.md)
> * [Szablon usługi Azure Resource Manager](create-cluster-database-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta

W tej sekcji skonfigurujesz klucze zarządzane przez klienta za pomocą szablonów Azure Resource Manager. Domyślnie usługa Azure Eksplorator danych Encryption używa kluczy zarządzanych przez firmę Microsoft. W tym kroku skonfiguruj klaster Eksplorator danych platformy Azure pod kątem używania kluczy zarządzanych przez klienta i określ klucz do skojarzenia z klastrem.

Szablon Azure Resource Manager można wdrożyć za pomocą Azure Portal lub przy użyciu programu PowerShell.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>Zaktualizuj wersję klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować klaster tak, aby korzystał z nowej wersji. Najpierw Wywołaj `Get-AzKeyVaultKey`, aby uzyskać najnowszą wersję klucza. Następnie zaktualizuj właściwości magazynu kluczy klastra, aby użyć nowej wersji klucza, jak pokazano w temacie [Konfigurowanie szyfrowania z kluczami zarządzanymi przez klienta](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie klastrów usługi Azure Eksplorator danych na platformie Azure](security.md)
* [Konfigurowanie tożsamości zarządzanych dla klastra usługi Azure Eksplorator danych](managed-identities.md)
* [Zabezpiecz swój klaster na platformie Azure Eksplorator danych — Azure Portal](manage-cluster-security.md) przez włączenie szyfrowania w stanie spoczynku.
* [Konfigurowanie kluczy zarządzanych przez klienta przy użyciuC#](customer-managed-keys-csharp.md)

