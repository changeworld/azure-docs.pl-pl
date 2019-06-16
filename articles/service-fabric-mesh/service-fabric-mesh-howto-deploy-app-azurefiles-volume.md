---
title: Użyj usługi Azure Files na podstawie woluminu w aplikacji usługi Service Fabric siatki | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przechowywać stanu w aplikacji usługi Azure Service Fabric siatki przez zainstalowanie woluminu plików platformy Azure na podstawie wewnątrz usługi przy użyciu wiersza polecenia platformy Azure.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: fa078f17768d4885403f2f3e3d6b91251f0aaced
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60419377"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Instalowanie usługi Azure Files na podstawie woluminu w aplikacji usługi Service Fabric siatki 

W tym artykule opisano sposób instalacji woluminu plików platformy Azure na podstawie usługi do aplikacji usługi Service Fabric siatki.  Usługi Azure Files woluminu sterownik jest Docker wolumin używany do instalowania udziału plików platformy Azure do kontenera, którego używasz, aby zachować stan usługi. Woluminy pozwalają magazynu plików ogólnego przeznaczenia i pozwala na odczyt/zapis plików za pomocą dysku normalnych operacji We/Wy plikowych interfejsów API.  Aby dowiedzieć się więcej na temat woluminów i opcje przechowywania danych aplikacji, przeczytaj [przechowywania stanu](service-fabric-mesh-storing-state.md).

Aby zainstalować wolumin w ramach usługi, utworzyć zasób woluminu w aplikacji usługi Service Fabric siatki, a następnie Odwołaj tego woluminu w usłudze.  Deklarowanie zasobu woluminu i odwoływanie się do zasobów usługa może odbywać się albo [pliki zasobów w formacie YAML](#declare-a-volume-resource-and-update-the-service-resource-yaml) lub [Szablon wdrożenia oparte na notacji JSON](#declare-a-volume-resource-and-update-the-service-resource-json). Przed odinstalowaniem woluminu, należy najpierw utworzyć konto usługi Azure storage i [udziału plików w usłudze Azure Files](/azure/storage/files/storage-how-to-create-file-share).

## <a name="prerequisites"></a>Wymagania wstępne

Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure służy do ukończenia tego artykułu. 

Za pomocą wiersza polecenia platformy Azure lokalnie w tym artykule, upewnij się, że `az --version` zwraca co najmniej `azure-cli (2.0.43)`.  Zainstalować (lub zaktualizować) modułu rozszerzenia usługi Service Fabric siatki wiersza polecenia platformy Azure, wykonując te [instrukcje](service-fabric-mesh-howto-setup-cli.md).

Logowanie do platformy Azure i ustawić subskrypcję:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Tworzenie konta i pliku udziału magazynu (opcjonalnie)
Instalowanie woluminów plików platformy Azure wymaga konta i pliku udziału magazynu.  Możesz użyć istniejącego konta i pliku udziału magazynu platformy Azure lub tworzyć zasoby:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Nazwa konta magazynu i klucz i nazwa udziału plików
Nazwa konta magazynu, klucz konta magazynu i nazwa udziału plików, które są określone jako `<storageAccountName>`, `<storageAccountKey>`, i `<fileShareName>` w poniższych sekcjach. 

Lista kont magazynu i uzyskać nazwę konta magazynu z udziałem plików, że chcesz użyć:
```azurecli-interactive
az storage account list
```

Pobierz nazwę udziału plików:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Pobierz klucz konta magazynu ("key1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Możesz również znaleźć te wartości w [witryny Azure portal](https://portal.azure.com):
* `<storageAccountName>` -W obszarze **kont magazynu**, nazwa konta magazynu używany do utworzenia udziału plików.
* `<storageAccountKey>` -Wybierz swoje konto magazynu, w obszarze **kont magazynu** , a następnie wybierz **klucze dostępu** i użyj wartości w obszarze **klucz1**.
* `<fileShareName>` -Wybierz swoje konto magazynu, w obszarze **kont magazynu** , a następnie wybierz **pliki**. Nazwa do użycia jest nazwa udziału plików, który został utworzony.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Deklarowanie zasobu woluminu i zaktualizuj zasób usługi (JSON)

Dodaj parametry `<fileShareName>`, `<storageAccountName>`, i `<storageAccountKey>` wartości znajdujące się w poprzednim kroku. 

Utwórz zasób woluminu jako element równorzędny zasobów aplikacji. Określ nazwę i dostawcy ("SFAzureFile" Aby korzystać z usługi Azure Files na podstawie woluminu). W `azureFileParameters`, określ parametry w celu `<fileShareName>`, `<storageAccountName>`, i `<storageAccountKey>` wartości znajdujące się w poprzednim kroku.

Aby zainstalować wolumin w usłudze, należy dodać `volumeRefs` do `codePackages` element usługi.  `name` jest identyfikator zasobu dla woluminu (lub parametr szablonu wdrażania dla zasobu woluminu) i nazwa woluminu zadeklarowanych w pliku zasobów volume.yaml.  `destinationPath` to katalog lokalny, który będzie instalowany woluminu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Deklarowanie zasobu woluminu i zaktualizuj zasób usługi (YAML)

Dodaj nową *volume.yaml* w pliku *zasobów aplikacji* katalogu aplikacji.  Określ nazwę i dostawcy ("SFAzureFile" Aby korzystać z usługi Azure Files na podstawie woluminu). `<fileShareName>`, `<storageAccountName>`, i `<storageAccountKey>` są na wartości znajdujące się w poprzednim kroku.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Aktualizacja *service.yaml* w pliku *zasobów usługi* katalogu można zainstalować woluminu w usłudze.  Dodaj `volumeRefs` elementu `codePackages` elementu.  `name` jest identyfikator zasobu dla woluminu (lub parametr szablonu wdrażania dla zasobu woluminu) i nazwa woluminu zadeklarowanych w pliku zasobów volume.yaml.  `destinationPath` to katalog lokalny, który będzie instalowany woluminu.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Kolejne kroki

- Wyświetlanie usługi Azure Files woluminu przykładowej aplikacji na [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Aby dowiedzieć się więcej o modelu zasobów usługi Service Fabric, zobacz [Model zasobów usługi Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).
