---
title: Użyj Menedżera zasobów szablonów w fabryce danych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i szablony usługi Azure Resource Manager umożliwia tworzenie jednostek fabryki danych.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0a03bb836f789a2a1cb58705a6758000c07036d1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621313"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Szablony umożliwiają utworzenie jednostek fabryki danych Azure
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). 

## <a name="overview"></a>Przegląd
Podczas używania fabryki danych Azure potrzeby integracji danych może okazać się ponowne użycie tego samego wzorca w różnych środowiskach lub wykonania tego samego zadania kilkukrotnie w tym samym rozwiązaniu. Szablony ułatwiają wdrożenia i zarządzania tych scenariuszy w łatwy sposób. Szablony w fabryce danych Azure idealnie nadają się do scenariusze, które obejmują możliwość ponownego wykorzystania i powtórzenia.

Rozważmy sytuację, w którym organizacja ma 10 zakładu produkcyjnego całym świecie. Dzienniki z każdego zakładu są przechowywane w bazie danych programu SQL Server oddzielny lokalny. Firma chce utworzyć magazynu danych w chmurze na potrzeby analizy ad hoc. Również chce mieć tej samej logiki, ale różne konfiguracje dla środowiska deweloperskie, badanie i produkcji.

W takim przypadku zadania konieczne jest powtórzenie w tym samym środowisku, ale o różnych wartościach między fabryk danych 10 dla każdego zakładu produkcyjnego. W efekcie **powtarzania** jest obecny. Tworzenia szablonów umożliwia pozyskiwania to ogólny przepływ (to znaczy potoki o tych samych czynności z każdej fabryki danych), ale używa pliku osobny parametr dla każdego zakładu produkcyjnego.

Ponadto, ponieważ organizacja chce wdrożyć te fabryki danych 10 wielokrotnie różnych środowiskach, szablony, mogą używać tego **możliwość ponownego wykorzystania** przy użyciu plików osobny parametr do tworzenia, testowania, i środowisk produkcyjnych.

## <a name="templating-with-azure-resource-manager"></a>Tworzenia szablonów z usługą Azure Resource Manager
[Szablony usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md#template-deployment) to doskonały sposób, aby osiągnąć tworzenia szablonów w fabryce danych Azure. Szablony Menedżera zasobów zdefiniuj infrastrukturze i konfiguracji rozwiązania Azure za pomocą pliku JSON. Ponieważ szablonów usługi Azure Resource Manager działa z usługami Azure all/większość, może być powszechnie używane w prosty sposób zarządzać wszystkie zasoby zasobów platformy Azure. Zobacz [szablonów Authoring Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) Aby dowiedzieć się więcej o szablonach usługi Resource Manager ogólnie.

## <a name="tutorials"></a>Samouczki
Zobacz następujące samouczki krok po kroku instrukcje dotyczące tworzenia jednostek fabryki danych przy użyciu szablonów usługi Resource Manager:

* [Samouczek: Tworzenie potoku, aby skopiować dane przy użyciu szablonu usługi Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Samouczek: Tworzenie potoku do przetwarzania danych przy użyciu szablonu usługi Azure Resource Manager](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Szablony fabryki danych w witrynie GitHub
Wyewidencjonuj następujących szablonów szybki start Azure w serwisie GitHub:

* [Tworzenie fabryki danych, aby skopiować dane z magazynu obiektów Blob Azure do bazy danych SQL Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Tworzenie fabryki danych działania Hive w usłudze Azure HDInsight klastra](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Tworzenie fabryki danych można skopiować danych z usług Salesforce do obiektów blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Tworzenie fabryki danych, w którym jest powiązany działań: kopiuje dane z serwera FTP do obiektów blob Azure, wywołuje skrypt hive w klastrze usługi HDInsight na żądanie do przekształcania danych i kopiuje wynik do bazy danych SQL Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Możesz także udostępnić szablony fabryki danych Azure na [Azure szybki start](https://azure.microsoft.com/documentation/templates/). Zapoznaj się [przewodnik wkład](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) podczas tworzenia szablonów, które mogą być współużytkowane przez to repozytorium.

Poniższe sekcje zawierają szczegółowe informacje na temat definiowania zasoby fabryki danych w szablonie usługi Resource Manager.

## <a name="defining-data-factory-resources-in-templates"></a>Definiowanie zasoby fabryki danych w szablonach
Szablon najwyższego poziomu do definiowania fabryki danych jest:

```JSON
"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
DataFactoryName jest zdefiniowana w "zmiennych" jako:

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

Zobacz [połączonej usługi magazynu](data-factory-azure-blob-connector.md#azure-storage-linked-service) lub [obliczeniowe połączonych usług](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) szczegółowe informacje o właściwościach JSON dla określonej usługi połączonej chcesz wdrożyć. Parametr "dependsOn" Określa nazwę odpowiedniego fabryki danych. Przykład Definiowanie połączonej usługi magazynu Azure znajduje się w następującej definicji JSON:

### <a name="define-datasets"></a>Zdefiniuj zbiory danych

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
Zapoznaj się [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) szczegółowe informacje o właściwościach JSON dla określonego zestawu danych typu chcesz wdrożyć. Należy pamiętać, że parametr "dependsOn" Określa nazwę odpowiedniego fabryki danych i magazynu połączonej usługi. Przykład definiujący typ zestawu danych z magazynu obiektów blob Azure znajduje się w następującej definicji JSON:

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

### <a name="define-pipelines"></a>Zdefiniuj potoki

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

Zapoznaj się [Definiowanie potoki](data-factory-create-pipelines.md#pipeline-json) szczegółowe informacje na temat właściwości JSON definiujący określone potoku i działań, które chcesz wdrożyć. Należy pamiętać, parametr "dependsOn" Nazwa fabryki danych i wszystkie odpowiednie połączonych usług lub zestawów danych. Przykład potok, który kopiuje dane z magazynu obiektów Blob Azure do bazy danych SQL Azure znajduje się w następujący fragment kodu JSON:

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
## <a name="parameterizing-data-factory-template"></a>Ustawianie szablonu usługi fabryka danych
Najlepsze rozwiązania na ustawianie, zobacz [najlepszych rozwiązań dotyczących tworzenia szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-manager-template-best-practices.md). Ogólnie rzecz biorąc użycia parametru można zmniejszyć do minimum, zwłaszcza jeśli zmienne, które mogą być używane zamiast tego. Tylko podać parametry w następujących scenariuszach:

* Ustawienia zależą od środowiska (przykład: Programowanie, testowe i produkcyjne)
* Klucze tajne (takie jak hasła)

Jeśli chcesz ściągnąć kluczy tajnych ze [usługi Azure Key Vault](../../key-vault/key-vault-get-started.md) wdrażając jednostek fabryki danych Azure za pomocą szablonów, należy określić **magazynu kluczy** i **nazwa klucza tajnego** opisane w Poniższy przykład:

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
> Podczas eksportowania szablonów dla fabryki danych istniejących obecnie nie jest jeszcze obsługiwana, jest działa.
>
>
