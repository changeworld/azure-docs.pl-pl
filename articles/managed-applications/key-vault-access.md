---
title: Używanie Azure Key Vault z aplikacjami zarządzanymi | Microsoft Docs
description: Pokazuje, jak używać wpisów tajnych dostępu w Azure Key Vault podczas wdrażania aplikacji zarządzanych
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: a87066425845a7f1043576a858a361e601ba9cc8
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003412"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Dostęp do Key Vault klucza tajnego podczas wdrażania Azure Managed Applications

Gdy musisz przekazać bezpieczną wartość (na przykład hasło) jako parametr podczas wdrażania, możesz pobrać wartość z [Azure Key Vault](../key-vault/key-vault-overview.md). Aby uzyskać dostęp do Key Vault podczas wdrażania aplikacji zarządzanych, należy przyznać dostęp do jednostki usługi **dostawcy zasobów urządzenia** . Usługa Managed Applications używa tej tożsamości do uruchamiania operacji. Aby pomyślnie pobrać wartość z Key Vault podczas wdrażania, jednostka usługi musi mieć dostęp do Key Vault.

W tym artykule opisano sposób konfigurowania Key Vault do pracy z zarządzanymi aplikacjami.

## <a name="enable-template-deployment"></a>Włącz wdrożenie szablonu

1. W portalu wybierz Key Vault.

1. Wybierz pozycję **Zasady dostępu**.   

   ![Wybieranie zasad dostępu](./media/key-vault-access/select-access-policies.png)

1. Wybierz pozycję **Kliknij, aby wyświetlić zaawansowane zasady dostępu**.

   ![Pokaż zaawansowane zasady dostępu](./media/key-vault-access/advanced.png)

1. Wybierz pozycję **Włącz dostęp do Azure Resource Manager na potrzeby wdrożenia szablonu**. Następnie wybierz pozycję **Zapisz**.

   ![Włącz wdrożenie szablonu](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Dodaj usługę jako współautor

1. Wybierz **kontrola dostępu (IAM)** .

   ![Wybieranie kontroli dostępu](./media/key-vault-access/access-control.png)

1. Wybierz **Dodaj przypisanie roli**.

   ![Wybierz pozycję Dodaj](./media/key-vault-access/add-access-control.png)

1. Wybierz opcję **współautor** dla roli. Wyszukaj **dostawcę zasobów urządzenia** i wybierz go z dostępnych opcji.

   ![Wyszukaj dostawcę](./media/key-vault-access/search-provider.png)

1. Wybierz pozycję **Zapisz**.

## <a name="reference-key-vault-secret"></a>Odwołanie Key Vault wpis tajny

Aby przekazać klucz tajny z Key Vault do szablonu w aplikacji zarządzanej, należy użyć [połączonego szablonu](../azure-resource-manager/resource-group-linked-templates.md) i odwołać się do Key Vault w parametrach dla połączonego szablonu. Podaj identyfikator zasobu Key Vault i nazwę wpisu tajnego.

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

## <a name="next-steps"></a>Następne kroki

Key Vault skonfigurowany do dostępności podczas wdrażania aplikacji zarządzanej.

* Aby uzyskać informacje o przekazywaniu wartości z Key Vault jako parametru szablonu, zobacz [używanie Azure Key Vault do przekazywania zabezpieczonej wartości parametrów podczas wdrażania](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* Aby zapoznać się z przykładami aplikacji zarządzanych, zobacz [przykładowe projekty dla aplikacji zarządzanych przez platformę Azure](sample-projects.md).
* Aby dowiedzieć się, jak utworzyć plik definicji interfejsu użytkownika dla aplikacji zarządzanej, zobacz [Rozpoczynanie pracy z plikiem CreateUiDefinition](create-uidefinition-overview.md).