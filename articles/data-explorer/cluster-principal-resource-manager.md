---
title: Dodawanie podmiotów klastra dla Eksploratora danych platformy Azure przy użyciu szablonu usługi Azure Resource Manager
description: W tym artykule dowiesz się, jak dodać podmioty klastra dla Eksploratora danych platformy Azure przy użyciu szablonu usługi Azure Resource Manager.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 22423568ab0b3b55d8d9566df4829eb6070b5f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965049"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-an-azure-resource-manager-template"></a>Dodawanie podmiotów klastra dla Eksploratora danych platformy Azure przy użyciu szablonu usługi Azure Resource Manager

> [!div class="op_single_selector"]
> * [C #](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Szablon usługi Azure Resource Manager](cluster-principal-resource-manager.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. W tym artykule można dodać podmioty klastra dla Usługi Azure Data Explorer przy użyciu szablonu Usługi Azure Resource Manager.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Tworzenie klastra](create-cluster-database-portal.md).

## <a name="azure-resource-manager-template-for-adding-a-cluster-principal"></a>Szablon usługi Azure Resource Manager do dodawania podmiotu klastra

W poniższym przykładzie przedstawiono szablon usługi Azure Resource Manager do dodawania podmiotu klastra.  Szablon można [edytować i wdrażać w witrynie Azure portal](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) przy użyciu formularza.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterPrincipalAssignmentName": {
            "type": "string",
            "defaultValue": "principalAssignment1",
            "metadata": {
                "description": "Specifies the name of the principal assignment"
            }
        },
        "clusterName": {
            "type": "string",
            "defaultValue": "mykustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "principalIdForCluster": {
            "type": "string",
            "metadata": {
                "description": "Specifies the principal id. It can be user email, application (client) ID, security group name"
            }
        },
        "roleForClusterPrincipal": {
            "type": "string",
            "defaultValue": "AllDatabasesViewer",
            "metadata": {
                "description": "Specifies the cluster principal role. It can be 'AllDatabasesAdmin', 'AllDatabasesViewer'"
            }
        },
        "tenantIdForClusterPrincipal": {
            "type": "string",
            "metadata": {
                "description": "Specifies the tenantId of the principal"
            }
        },
        "principalTypeForCluster": {
            "type": "string",
            "defaultValue": "User",
            "metadata": {
                "description": "Specifies the principal type. It can be 'User', 'App', 'Group'"
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/principalAssignments",
            "apiVersion": "2019-11-09",
            "name": "[concat(parameters('clusterName'), '/', parameters('clusterPrincipalAssignmentName'))]",
            "properties": {
                "principalId": "[parameters('principalIdForCluster')]",
                "role": "[parameters('roleForClusterPrincipal')]",
                "tenantId": "[parameters('tenantIdForClusterPrincipal')]",
                "principalType": "[parameters('principalTypeForCluster')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki

* [Dodawanie podmiotów bazy danych](database-principal-resource-manager.md)
