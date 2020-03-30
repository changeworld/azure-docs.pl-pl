---
title: Tworzenie połączenia danych siatki zdarzeń dla Eksploratora danych platformy Azure przy użyciu szablonu usługi Azure Resource Manager
description: W tym artykule dowiesz się, jak utworzyć połączenie danych usługi Event Grid dla Usługi Azure Data Explorer przy użyciu szablonu usługi Azure Resource Manager.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/28/2019
ms.openlocfilehash: a2e29b9811ab163642e7f65ded584c4889c199db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74669261"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-azure-resource-manager-template"></a>Tworzenie połączenia danych siatki zdarzeń dla Eksploratora danych platformy Azure przy użyciu szablonu usługi Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Szablon usługi Azure Resource Manager](data-connection-event-grid-resource-manager.md)


Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Data Explorer oferuje pozyskiwania (ładowanie danych) z centrów zdarzeń, usługi IoT Hubs i obiektów blob zapisanych w kontenerach obiektów blob. W tym artykule utworzysz połączenie danych usługi Event Grid dla Eksploratora danych platformy Azure przy użyciu szablonu Usługi Azure Resource Manager.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Tworzenie [klastra i bazy danych](create-cluster-database-portal.md)
* Tworzenie [mapowania tabel i kolumn](ingest-data-event-grid.md#create-a-target-table-in-azure-data-explorer)
* Tworzenie [centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)
* Utwórz [konto magazynu z subskrypcją usługi Event Grid](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

## <a name="azure-resource-manager-template-for-adding-an-event-grid-data-connection"></a>Szablon usługi Azure Resource Manager do dodawania połączenia danych usługi Event Grid

W poniższym przykładzie przedstawiono szablon usługi Azure Resource Manager do dodawania połączenia danych usługi Event Grid.  Szablon można [edytować i wdrażać w witrynie Azure portal](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) przy użyciu formularza.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "namespaces_eventhubns_name": {
            "type": "string",
            "defaultValue": "eventhubns",
            "metadata": {
                "description": "Specifies the Event Hub Namespace name."
            }
        },
        "EventHubs_eventhubdemo_name": {
            "type": "string",
            "defaultValue": "eventhubdemo",
            "metadata": {
                "description": "Specifies the Event Hub name."
            }
        },
        "consumergroup_default_name": {
            "type": "string",
            "defaultValue": "$Default",
            "metadata": {
                "description": "Specifies the consumer group of the Event Hub."
            }
        },
        "StorageAccounts_storagedemo_name": {
            "type": "string",
            "defaultValue": "storagedemo",
            "metadata": {
                "description": "Specifies the storage account name"
            }
        },
        "Clusters_kustocluster_name": {
            "type": "string",
            "defaultValue": "kustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "databases_kustodb_name": {
            "type": "string",
            "defaultValue": "kustodb",
            "metadata": {
                "description": "Specifies the name of the database"
            }
        },
        "tables_kustotable_name": {
            "type": "string",
            "defaultValue": "kustotable",
            "metadata": {
                "description": "Specifies the name of the table"
            }
        },
        "mapping_kustomapping_name": {
            "type": "string",
            "defaultValue": "kustomapping",
            "metadata": {
                "description": "Specifies the name of the mapping rule"
            }
        },
        "dataformat_type": {
            "type": "string",
            "defaultValue": "csv",
            "metadata": {
                "description": "Specifies the data format"
            }
        },
        "dataconnections_kustodc_name": {
            "type": "string",
            "defaultValue": "kustodc",
            "metadata": {
                "description": "Name of the data connection to create"
            }
        },
        "subscriptionId": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "Specifies the subscriptionId of the resources"
            }
        },
        "resourceGroup": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "metadata": {
                "description": "Specifies the resourceGroup of the resources"
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
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/Databases/DataConnections",
            "apiVersion": "2019-09-07",
            "name": "[concat(parameters('Clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'), '/', parameters('dataconnections_kustodc_name'))]",
            "location": "[parameters('location')]",
            "kind": "EventGrid",
            "properties": {
                "storageAccountResourceId": "[resourceId(parameters('subscriptionId'), parameters('resourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('StorageAccounts_storagedemo_name'))]",
                "eventHubResourceId": "[resourceId(parameters('subscriptionId'), parameters('resourceGroup'), 'Microsoft.EventHub/namespaces/eventhubs', parameters('namespaces_eventhubns_name'), parameters('EventHubs_eventhubdemo_name'))]",
                "consumerGroup": "[parameters('consumergroup_default_name')]",
                "tableName": "[parameters('tables_kustotable_name')]",
                "mappingRuleName": "[parameters('mapping_kustomapping_name')]",
                "dataFormat": "[parameters('dataformat_type')]"
            }
        }
    ]
}
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]
