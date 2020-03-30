---
title: Konfigurowanie kluczy zarządzanych przez klienta w Eksploratorze danych platformy Azure przy użyciu szablonu usługi Azure Resource Manager
description: W tym artykule opisano sposób konfigurowania szyfrowania kluczy zarządzanych przez klienta na danych w Eksploratorze danych platformy Azure przy użyciu szablonu Usługi Azure Resource Manager.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 454a80089b5f74d4a70015ffcd03d0212e8c08a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297905"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Konfigurowanie kluczy zarządzanych przez klienta przy użyciu szablonu usługi Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Szablon usługi Azure Resource Manager](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurowanie szyfrowania za pomocą kluczy zarządzanych przez klienta

W tej sekcji można skonfigurować klucze zarządzane przez klienta przy użyciu szablonów usługi Azure Resource Manager. Domyślnie szyfrowanie usługi Azure Data Explorer używa kluczy zarządzanych przez firmę Microsoft. W tym kroku skonfiguruj klaster usługi Azure Data Explorer do używania kluczy zarządzanych przez klienta i określ klucz do skojarzenia z klastrem.

Szablon usługi Azure Resource Manager można wdrożyć przy użyciu portalu Azure lub za pomocą programu PowerShell.

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

## <a name="update-the-key-version"></a>Aktualizowanie wersji klucza

Podczas tworzenia nowej wersji klucza należy zaktualizować klaster, aby użyć nowej wersji. Najpierw zadzwoń, `Get-AzKeyVaultKey` aby uzyskać najnowszą wersję klucza. Następnie zaktualizuj właściwości magazynu kluczy klastra, aby użyć nowej wersji klucza, jak pokazano w [temacie Konfigurowanie szyfrowania za pomocą kluczy zarządzanych przez klienta](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie klastrów eksploratora danych platformy Azure na platformie Azure](security.md)
* [Konfigurowanie tożsamości zarządzanych dla klastra usługi Azure Data Explorer](managed-identities.md)
* [Zabezpiecz swój klaster w usłudze Azure Data Explorer — witryna Azure portal,](manage-cluster-security.md) włączając szyfrowanie w spoczynku.
* [Konfigurowanie kluczy zarządzanych przez klienta przy użyciu języka C #](customer-managed-keys-csharp.md)

