---
title: Użyj usługi Azure Key Vault z aplikacji zarządzanych | Dokumentacja firmy Microsoft
description: Ilustruje sposób używania wpisów tajnych dostępu w usłudze Azure Key Vault, wdrażając Managed Applications
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 55410250ccd4dfceac8ac9ae5b81d4736de0d91a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60588298"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Dostęp do klucza tajnego usługi Key Vault, podczas wdrażania usługi Azure Managed Applications

Gdy potrzebujesz przekazać wartość bezpieczną (na przykład hasło) jako parametr podczas wdrażania można pobrać wartości z [usługi Azure Key Vault](../key-vault/key-vault-whatis.md). Aby uzyskać dostęp do usługi Key Vault, podczas wdrażania aplikacji zarządzanych, musi udzielić dostępu do **dostawcy zasobów urządzenia** nazwy głównej usługi. Usługa Managed Applications używa tej tożsamości do uruchamiania operacji. Aby pomyślnie pobrać wartość z usługi Key Vault podczas wdrażania, nazwa główna usługi musi być mogli korzystać z usługi Key Vault.

W tym artykule opisano sposób konfigurowania usługi Key Vault do pracy z aplikacji zarządzanych.

## <a name="enable-template-deployment"></a>Włącz wdrożenie szablonu

1. W portalu wybierz magazyn kluczy.

1. Wybierz pozycję **Zasady dostępu**.   

   ![Wybierz zasady dostępu](./media/key-vault-access/select-access-policies.png)

1. Wybierz pozycję **Kliknij, aby wyświetlić zaawansowane zasady dostępu**.

   ![Pokaż zaawansowane zasady dostępu](./media/key-vault-access/advanced.png)

1. Wybierz **włączyć dostęp do usługi Azure Resource Manager dla wdrożenia szablonu**. Następnie wybierz pozycję **Zapisz**.

   ![Włącz wdrożenie szablonu](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Dodaj usługę jako współautor

1. Wybierz **kontrola dostępu (IAM)** .

   ![Wybierz kontroli dostępu](./media/key-vault-access/access-control.png)

1. Wybierz **Dodaj przypisanie roli**.

   ![Wybieranie opcji dodawania](./media/key-vault-access/add-access-control.png)

1. Wybierz **Współautor** dla roli. Wyszukaj **dostawcy zasobów urządzenia** i wybierz ją z listy dostępnych opcji.

   ![Wyszukiwanie dostawcy](./media/key-vault-access/search-provider.png)

1. Wybierz pozycję **Zapisz**.

## <a name="reference-key-vault-secret"></a>Odwołanie wpisu tajnego z usługi Key Vault

Aby przekazać wpisu tajnego z usługi Key Vault do szablonu w zarządzanej aplikacji, należy użyć [połączony szablon](../azure-resource-manager/resource-group-linked-templates.md) i odwoływać się do usługi Key Vault za pomocą parametrów szablonu połączone. Podaj identyfikator zasobu usługi Key Vault i nazwę klucza tajnego.

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>Kolejne kroki

Skonfigurowano usługi Key Vault była dostępna podczas wdrażania aplikacji zarządzanych.

* Aby uzyskać informacji o przekazywaniu wartości z magazynu kluczy, jako parametr szablonu, zobacz [użycia usługi Azure Key Vault do przekazywania wartości parametru secure podczas wdrażania](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* Przykłady aplikacji zarządzanych, zobacz [przykładowych projektów dla platformy Azure zarządzane aplikacje](sample-projects.md).
* Aby dowiedzieć się, jak utworzyć plik definicji interfejsu użytkownika dla aplikacji zarządzanej, zobacz [Rozpoczynanie pracy z plikiem CreateUiDefinition](create-uidefinition-overview.md).