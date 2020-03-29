---
title: Używanie woluminu opartego na plikach platformy Azure w aplikacji siatki sieci szkieletowej usług
description: Dowiedz się, jak przechowywać stan w aplikacji usługi Azure Service Fabric Mesh, montując wolumin oparty na plikach platformy Azure wewnątrz usługi przy użyciu interfejsu wiersza polecenia platformy Azure.
author: dkkapur
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 5bb7ab6c861d958f6811ca852363c59cfced3940
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718824"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Instalowanie woluminu opartego na plikach platformy Azure w aplikacji siatki sieci szkieletowej usług 

W tym artykule opisano sposób instalowania woluminu opartego na plikach platformy Azure w usłudze aplikacji sieci szkieletowej usług.  Sterownik woluminu usługi Azure Files jest sterownikiem woluminu Platformy Docker używanym do instalowania udziału usługi Azure Files w kontenerze, którego używasz do utrwalania stanu usługi. Woluminy zapewniają magazyn plików ogólnego przeznaczenia i umożliwiają odczytywanie/zapisywanie plików przy użyciu zwykłych interfejsów API plików we/wy dysku.  Aby dowiedzieć się więcej o woluminach i opcjach przechowywania danych aplikacji, przeczytaj [stan przechowywania](service-fabric-mesh-storing-state.md).

Aby zainstalować wolumin w usłudze, utwórz zasób woluminu w aplikacji sieci szkieletowej usługi, a następnie odwołaj się do tego woluminu w usłudze.  Deklarowanie zasobu woluminu i odwoływanie się do niego w zasobie usługi można wykonać w [plikach zasobów opartych na YAML](#declare-a-volume-resource-and-update-the-service-resource-yaml) lub [szablonie wdrożenia opartym na języku JSON](#declare-a-volume-resource-and-update-the-service-resource-json). Przed zamontowaniem woluminu należy najpierw utworzyć konto magazynu platformy Azure i [udział plików w usłudze Azure Files](/azure/storage/files/storage-how-to-create-file-share).

## <a name="prerequisites"></a>Wymagania wstępne
> [!NOTE]
> **Znany problem z wdrożeniem na komputerze deweloperskim systemu Windows RS5:** Istnieje błąd otwarty z poleceniem cmdlet Programu Powershell New-SmbGlobalMapping na komputerach Z systemem Windows RS5, który uniemożliwia montaż woluminów pliku Azure. Poniżej znajduje się przykładowy błąd, który występuje, gdy wolumin oparty na usłudze AzureFile jest montowany na komputerze deweloperskim lokalnym.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
Obejście problemu jest 1)Uruchom poniżej polecenia jako administrator programu Powershell i 2)Uruchom ponownie komputer.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Aby ukończyć ten artykuł, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. 

Aby użyć interfejsu wiersza polecenia platformy `az --version` Azure lokalnie w tym artykule, upewnij się, że zwraca co najmniej `azure-cli (2.0.43)`.  Zainstaluj (lub zaktualizuj) moduł rozszerzenia interfejsu wiersza polecenia sieci szkieletowej usługi Azure, postępując zgodnie z tymi [instrukcjami](service-fabric-mesh-howto-setup-cli.md).

Aby zalogować się na platformę Azure i skonfigurować subskrypcję:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Tworzenie konta magazynu i udziału plików (opcjonalnie)
Instalowanie woluminu usługi Azure Files wymaga konta magazynu i udziału plików.  Można użyć istniejącego konta magazynu platformy Azure i udziału plików lub utworzyć zasoby:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Pobierz nazwę i klucz konta magazynu oraz nazwę udziału plików
Nazwa konta magazynu, klucz konta magazynu i nazwa udziału `<storageAccountName>` `<storageAccountKey>`plików `<fileShareName>` są określane jako , i w poniższych sekcjach. 

Wyświetl listę kont magazynu i uzyskaj nazwę konta magazynu w udziale plików, którego chcesz użyć:
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

Te wartości można również znaleźć w [witrynie Azure portal:](https://portal.azure.com)
* `<storageAccountName>`- W obszarze **Konta magazynu**nazwa konta magazynu użytego do utworzenia udziału plików.
* `<storageAccountKey>`- Wybierz swoje konto magazynu w obszarze **Konta magazynu,** a następnie wybierz **klawisze dostępu** i użyj wartości pod **key1**.
* `<fileShareName>`- Wybierz swoje konto pamięci w obszarze **Konta magazynu,** a następnie wybierz **pozycję Pliki**. Nazwa do użycia to nazwa utworzonego udziału plików.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Deklarowanie zasobu woluminu i aktualizowanie zasobu usługi (JSON)

Dodaj parametry `<fileShareName>`, `<storageAccountName>`i `<storageAccountKey>` wartości znalezione w poprzednim kroku. 

Utwórz zasób woluminu jako element równorzędny zasobu aplikacji. Określ nazwę i dostawcę ("SFAzureFile" do korzystania z woluminu opartego na plikach azure). W `azureFileParameters`obszarze , określ `<storageAccountName>`parametry `<storageAccountKey>` , `<fileShareName>`i wartości znalezione w poprzednim kroku.

Aby zainstalować wolumin w usłudze, dodaj a `volumeRefs` do `codePackages` elementu usługi.  `name`jest identyfikatorem zasobu dla woluminu (lub parametrem szablonu wdrożenia zasobu woluminu) i nazwą woluminu zadeklarowanego w pliku zasobu volume.yaml.  `destinationPath`to katalog lokalny, do który zostanie zainstalowany wolumin.

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

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Deklarowanie zasobu woluminu i aktualizowanie zasobu usługi (YAML)

Dodaj nowy plik *pliku volume.yaml* w katalogu *zasobów aplikacji* dla aplikacji.  Określ nazwę i dostawcę ("SFAzureFile" do korzystania z woluminu opartego na plikach azure). `<fileShareName>``<storageAccountName>`, i `<storageAccountKey>` są wartościami znalezionymi w poprzednim kroku.

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

Zaktualizuj plik *service.yaml* w katalogu *Zasoby usługi,* aby zainstalować wolumin w usłudze.  Dodaj `volumeRefs` element do `codePackages` elementu.  `name`jest identyfikatorem zasobu dla woluminu (lub parametrem szablonu wdrożenia zasobu woluminu) i nazwą woluminu zadeklarowanego w pliku zasobu volume.yaml.  `destinationPath`to katalog lokalny, do który zostanie zainstalowany wolumin.

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

## <a name="next-steps"></a>Następne kroki

- Wyświetl przykładową aplikację woluminu usługi Azure Files w [usłudze GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Aby dowiedzieć się więcej o modelu zasobów usługi Service Fabric, zobacz [Model zasobów usługi Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).
