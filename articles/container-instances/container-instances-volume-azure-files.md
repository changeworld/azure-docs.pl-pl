---
title: Instalacji woluminu plików platformy Azure w usłudze Azure Container Instances
description: Dowiedz się, jak można zainstalować woluminu plików platformy Azure, aby utrwalanie stanu przy użyciu usługi Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bc09aa500743d608c0a3a7a379fe9584c9c55e9b
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657613"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances

Domyślnie usługi Azure Container Instances są bezstanowe. Kontener ulegnie awarii lub przestanie, wszystkie jego stan zostanie utracone. Aby zachować stan poza okres istnienia kontenera, można zainstalować woluminu z magazynu zewnętrznego. W tym artykule pokazano, jak zainstalować udział plików platformy Azure utworzonych za pomocą [usługi Azure Files](../storage/files/storage-files-introduction.md) do użycia z usługą Azure Container Instances. Usługa Pliki Azure oferuje w pełni zarządzane udziały plików w chmurze, dostępne za pośrednictwem protokołu Server Message Block (SMB) będącego standardem branżowym. Za pomocą udziału plików platformy Azure przy użyciu usługi Azure Container Instances zapewnia funkcji udostępniania plików, podobne do udziału plików platformy Azure przy użyciu maszyn wirtualnych platformy Azure.

> [!NOTE]
> Instalowanie udziału plików platformy Azure jest obecnie ograniczone do kontenerów systemu Linux. Podczas gdy pracujemy, aby udostępnić wszystkie funkcje dostępne w kontenerach Windows, można znaleźć bieżące różnice dotyczące platform w [Przegląd](container-instances-overview.md#linux-and-windows-containers).

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Przed rozpoczęciem korzystania z udziału plików platformy Azure, usługa Azure Container Instances, należy go utworzyć. Uruchom następujący skrypt, aby utworzyć konto magazynu do obsługi udziału plików i udziału. Nazwa konta magazynu musi być globalnie unikatowa, więc skrypt ten dodaje wartość losowe do podstawowego ciągu.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Pobierz poświadczenia magazynu

Aby zainstalować udział plików platformy Azure jako woluminu w usłudze Azure Container Instances, trzy wartości będą potrzebne: Nazwa konta magazynu, nazwę udziału i klucza dostępu do magazynu.

Jeśli używany jest skrypt powyżej, nazwa konta magazynu została zapisana w zmiennej ACI_PERS_STORAGE_ACCOUNT_NAME $. Aby wyświetlić nazwę konta, wpisz:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Nazwa udziału jest już znany (zdefiniowany jako *acishare* w skrypcie powyżej), więc wszystkie, czy nadal jest klucz konta magazynu, w którym można znaleźć, używając następującego polecenia:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>Wdrażanie kontenera i zainstalować woluminu — interfejs wiersza polecenia

Aby zainstalować udział plików platformy Azure jako wolumin w kontenerze przy użyciu wiersza polecenia platformy Azure, należy określić, udział i wolumin punktu instalacji podczas tworzenia kontenera za pomocą [utworzyć kontener az][az-container-create]. Po wykonaniu poprzednich kroków możesz zainstalować udział, utworzonego wcześniej przy użyciu następującego polecenia, aby utworzyć kontener:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

`--dns-name-label` Wartość musi być unikatowa w obrębie regionu świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. Zaktualizuj tę wartość w poprzednim poleceniu, jeśli zostanie wyświetlony **etykiety nazwy DNS** komunikat o błędzie podczas wykonywania polecenia.

## <a name="manage-files-in-mounted-volume"></a>Zarządzanie plikami w woluminie

Po uruchomieniu kontenera, można użyć prostej aplikacji sieci web wdrażane za pomocą programu Microsoft [aci hellofiles][aci-hellofiles] image to create small text files in the Azure file share at the mount path you specified. Obtain the web app's fully qualified domain name (FQDN) with the [az container show][az-container-show] polecenia:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

Po zapisaniu tekstu przy użyciu aplikacji, można użyć [witryny Azure portal][portal] or a tool like the [Microsoft Azure Storage Explorer][storage-explorer] do pobierania i sprawdź plik zapisywane do udziału plików.

## <a name="deploy-container-and-mount-volume---yaml"></a>Wdrażanie kontenera i zainstalować woluminu - YAML

Możesz również wdrożyć grupę kontenerów i zainstalować wolumin w kontenerze za pomocą wiersza polecenia platformy Azure i [szablonów YAML](container-instances-multi-container-yaml.md). Wdrażanie szablonów YAML jest preferowaną metodą w przypadku wdrażania grup kontenerów składające się z wielu kontenerów.

Następujący szablon YAML definiuje grupę kontenerów za pomocą jednego kontenera utworzone za pomocą `aci-hellofiles` obrazu. Kontener instaluje udziału plików platformy Azure *acishare* został wcześniej utworzony jako wolumin. W przypadku, gdy wskazane, wprowadź nazwę i magazynu klucz konta magazynu, który jest hostem udziału plików. 

Tak jak w przykładzie interfejsu wiersza polecenia `dnsNameLabel` wartość musi być unikatowa w obrębie regionu świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. Zaktualizuj tę wartość w pliku YAML, jeśli to konieczne.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Wdrożyć za pomocą szablonu YAML, należy zapisać w pliku o nazwie poprzedniego YAML `deploy-aci.yaml`, a następnie wykonaj [utworzyć kontener az][az-container-create] polecenia `--file` parametru:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Wdrażanie kontenerów i instalacji woluminu — Resource Manager

Oprócz interfejsu wiersza polecenia i YAML wdrożenia, można wdrażać grupy kontenerów oraz zainstalować wolumin w kontenerze usługi Azure [szablonu usługi Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Najpierw należy wypełnić `volumes` tablicy w grupie kontenerów `properties` części szablonu. 

Następnie dla każdego kontenera, w którym chcesz zainstalować wolumin należy wypełnić `volumeMounts` tablicy w `properties` sekcja definicji kontenera.

Następujący szablon usługi Resource Manager definiuje grupę kontenerów za pomocą jednego kontenera utworzone za pomocą `aci-hellofiles` obrazu. Kontener instaluje udziału plików platformy Azure *acishare* został wcześniej utworzony jako wolumin. W przypadku, gdy wskazane, wprowadź nazwę i magazynu klucz konta magazynu, który jest hostem udziału plików. 

Tak jak w poprzednich przykładach `dnsNameLabel` wartość musi być unikatowa w obrębie regionu świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. Jeśli to konieczne, należy zaktualizować wartości w szablonie.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Wdrażanie przy użyciu szablonu usługi Resource Manager, należy zapisać w pliku o nazwie powyższego kodu JSON `deploy-aci.json`, a następnie wykonaj [Utwórz wdrożenie grupy az][az-group-deployment-create] polecenia `--template-file` parametru:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Zainstaluj wiele woluminów

Aby zainstalować wiele woluminów w wystąpieniu kontenera, należy wdrożyć przy użyciu [szablonu usługi Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) lub pliku YAML. Aby użyć szablonu lub pliku YAML, podaj szczegóły udziału, a następnie zdefiniować woluminy przy wprowadzaniu `volumes` tablicy w `properties` części szablonu. 

Na przykład, jeśli utworzono dwa udziałów plików platformy Azure o nazwie *share1* i *share2* na koncie magazynu *myStorageAccount*, `volumes` tablicy w usłudze Resource Manager szablon zostanie wyświetlony podobny do następującego:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Następnie dla każdego kontenera w grupie kontenerów, w którym chcesz zainstalować woluminów należy wypełnić `volumeMounts` tablicy w `properties` sekcja definicji kontenera. Na przykład instaluje to dwa woluminy *myvolume1* i *myvolume2*, poprzednio zdefiniowanymi:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak zainstalować inne typy woluminu w usłudze Azure Container Instances:

* [Zainstalować emptyDir woluminu w wystąpień kontenera platformy Azure](container-instances-volume-emptydir.md)
* [Zainstalować wolumin gitRepo w wystąpień kontenera platformy Azure](container-instances-volume-gitrepo.md)
* [Zainstaluj wolumin tajny w usłudze Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create