---
title: Użyj usługi Resource Manager szablonów w usłudze Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i tworzenie jednostek usługi Data Factory za pomocą szablonów usługi Azure Resource Manager.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: ca8b3930b9d9f708d83dc760be3ee89737b074dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583370"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Szablony umożliwiają tworzenie jednostki usługi Azure Data Factory
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. 

## <a name="overview"></a>Omówienie
Podczas używania usługi Azure Data Factory na potrzeby integracji danych, może się okazać samodzielnie ponowne użycie tego samego wzorca co w różnych środowiskach lub wdrażanie tego samego zadania kilkukrotnie w obrębie tego samego rozwiązania. Szablony ułatwiają implementować oraz zarządzać nimi tych scenariuszy w łatwy sposób. Szablony usługi Azure Data Factory jest idealny dla scenariuszy, które obejmują możliwość ponownego wykorzystania i powtórzenie.

Rozważmy sytuację, w którym organizacja ma 10 zakładów produkcyjnych na całym świecie. Dzienniki z każdego zakładu są przechowywane w oddzielnych lokalnej bazy danych programu SQL Server. Firma chce kompilacji pojedynczego magazynu danych w chmurze na potrzeby analizy ad-hoc. Również chce mieć tę samą logikę, ale różnych konfiguracji dla środowisk programowania, testowania i produkcji.

W tym przypadku zadania konieczne należy powtórzyć w tym samym środowisku, ale z różnymi wartościami dla fabryki danych 10 dla każdego zakładu produkcyjnego. W efekcie **powtórzenia** jest obecny. Tworzenie szablonów pozwala pozyskiwania ten ogólny przepływ (czyli potoki o tych samych czynności w każda fabryka danych), ale korzysta z pliku parametrów osobne dla każdego zakładu produkcyjnego.

Ponadto organizacja chce wdrożyć te fabryk danych 10 wielokrotnie w różnych środowiskach, szablony może użyć tego **możliwość ponownego wykorzystania** wykorzystując oddzielne pliki parametrów do programowania, testowania, i środowisk produkcyjnych.

## <a name="templating-with-azure-resource-manager"></a>Tworzenie szablonów przy użyciu usługi Azure Resource Manager
[Szablony usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md#template-deployment) to doskonały sposób, aby osiągnąć szablonów usługi Azure Data Factory. Szablony usługi Resource Manager służy do definiowania infrastruktury i konfiguracji rozwiązania platformy Azure za pomocą pliku JSON. Ponieważ szablony usługi Azure Resource Manager współdziała z usługami platformy Azure, które są wszystkie/nabardziej, może być powszechnie używane w prosty sposób zarządzać wszystkie zasoby Twoich zasobów platformy Azure. Zobacz [tworzenia usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) Aby dowiedzieć się więcej na temat szablonów usługi Resource Manager ogólnie rzecz biorąc.

## <a name="tutorials"></a>Samouczki
Zobacz następujące samouczki krok po kroku dotyczące tworzenia jednostki usługi Data Factory przy użyciu szablonów usługi Resource Manager:

* [Samouczek: Tworzenie potoku w celu kopiowania danych przy użyciu szablonu usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Samouczek: Tworzenie potoku w celu przetwarzania danych przy użyciu szablonu usługi Azure Resource Manager](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Szablony fabryki danych w usłudze GitHub
Zapoznaj się z następujących szablonów Azure: Szybki start w witrynie GitHub:

* [Tworzenie fabryki danych, aby skopiować dane z usługi Azure Blob Storage do usługi Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Tworzenie fabryki danych za pomocą działania Hive w klastrze Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Tworzenie fabryki danych w celu skopiowania danych z usług Salesforce do obiektów blob platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Tworzenie fabryki danych, który tworzy powiązanie działań: kopiuje dane z serwera FTP do obiektów blob platformy Azure, wywołuje skrypt hive w klastrze HDInsight na żądanie do przekształcania danych i kopiuje wynik do usługi Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Możesz także udostępniać swoje szablony usługi Azure Data Factory w [Azure szybki start](https://azure.microsoft.com/documentation/templates/). Zapoznaj się [przewodniku po współtworzeniu](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) podczas tworzenia szablonów, które mogą być udostępniane za pośrednictwem tego repozytorium.

Poniższe sekcje zawierają szczegółowe informacje na temat definiowania zasoby usługi Data Factory w szablonie usługi Resource Manager.

## <a name="defining-data-factory-resources-in-templates"></a>Definiowanie zasobów usługi Data Factory w szablonach
Szablon najwyższego poziomu dla zdefiniowanie fabryki danych jest:

```JSON
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": { ...
},
"variables": { ...
},
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
    "resources": [
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Definiowanie fabryki danych
Fabrykę danych definiuje się w szablonie usługi Resource Manager jak pokazano w następującym przykładzie:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
Parametr dataFactoryName jest zdefiniowany w "zmienne" jako:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Zdefiniuj połączone usługi

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Zobacz [połączona usługa Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service) lub [usługi połączone usługi Compute](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) szczegółowe informacje na temat właściwości kodu JSON dla określonego połączonej usługi, chcesz wdrożyć. Parametr "dependsOn" Określa nazwę odpowiedniej usługi data Factory. W poniższej definicji JSON przedstawiono przykładowy definiowania połączonej usługi dla usługi Azure Storage:

### <a name="define-datasets"></a>Definiowanie zestawów danych

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Zapoznaj się [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) szczegółowe informacje na temat właściwości kodu JSON dla typu określonego zestawu danych chcesz wdrożyć. Należy pamiętać, że parametr "dependsOn" Określa nazwę odpowiedniej usługi fabryka danych i magazynu połączoną usługę. W poniższej definicji JSON przedstawiono przykładowy zdefiniowania typu zestawu danych usługi Azure blob storage:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>Zdefiniuj potoków

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

Zapoznaj się [Definiowanie potoki](data-factory-create-pipelines.md#pipeline-json) szczegółowe informacje na temat właściwości kodu JSON do definiowania określonego potoku i działania, które chcesz wdrożyć. Należy pamiętać, parametr "dependsOn" Określa nazwę fabryki danych i wszelkie powiązane połączone usługi lub zestawów danych. W poniższym fragmencie kodu JSON przedstawiono przykładowy potok, który kopiuje dane z usługi Azure Blob Storage do usługi Azure SQL Database:

```JSON
"type": "datapipelines",
"name": "[variables('pipelineName')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('azureSqlLinkedServiceName')]",
    "[variables('blobInputDatasetName')]",
    "[variables('sqlOutputDatasetName')]"
],
"apiVersion": "2015-10-01",
"properties": {
    "activities": [
    {
        "name": "CopyFromAzureBlobToAzureSQL",
        "description": "Copy data frm Azure blob to Azure SQL",
        "type": "Copy",
        "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
        ],
        "outputs": [
            {
                "name": "[variables('sqlOutputDatasetName')]"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
            },
            "translator": {
                "type": "TabularTranslator",
                "columnMappings": "Column0:FirstName,Column1:LastName"
            }
        },
        "Policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 3,
            "timeout": "01:00:00"
        }
    }
    ],
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Ustawianie szablonu usługi Data Factory
Aby uzyskać najlepsze rozwiązania dotyczące parametryzacja, zobacz [najlepsze rozwiązania dotyczące tworzenia szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-manager-template-best-practices.md). Ogólnie rzecz biorąc użycia parametru można zmniejszyć do minimum, zwłaszcza, jeśli zmienne, które mogą być używane zamiast tego. Podaj tylko parametrów w następujących scenariuszach:

* Ustawienia zależą od środowiska (przykład: rozwoju, testowania i produkcji)
* Klucze tajne (takie jak hasła)

Jeśli chcesz ściągnąć wpisy tajne z [usługi Azure Key Vault](../../key-vault/key-vault-overview.md) podczas wdrażania jednostek usługi fabryka danych Azure przy użyciu szablonów, określ **usługi key vault** i **Nazwa wpisu tajnego** pokazany na Poniższy przykład:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Podczas eksportowania szablonów dla istniejącej fabryki danych obecnie nie jest jeszcze obsługiwana, jest w działa.
>
>
