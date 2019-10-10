---
title: Zainstaluj wolumin Azure Files w Azure Container Instances
description: Dowiedz się, jak zainstalować wolumin Azure Files, aby zachować stan utrwalania z Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 686c20aeb22c16298967aa6f73ee829472f4ea0c
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72175934"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Instalowanie udziału plików platformy Azure w Azure Container Instances

Domyślnie Azure Container Instances są bezstanowe. Jeśli kontener ulegnie awarii lub zostanie zatrzymany, jego stan zostanie utracony. Aby zachować stan poza okres istnienia kontenera, należy zainstalować wolumin ze sklepu zewnętrznego. Jak pokazano w tym artykule, Azure Container Instances może zainstalować udział plików platformy Azure utworzony przy użyciu [Azure Files](../storage/files/storage-files-introduction.md). Azure Files oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem protokołu Server Message Block (SMB). Korzystanie z udziału plików platformy Azure z Azure Container Instances zapewnia funkcje udostępniania plików podobne do korzystania z udziału plików platformy Azure z maszynami wirtualnymi platformy Azure.

> [!NOTE]
> Instalowanie udziału Azure Files jest obecnie ograniczone do kontenerów systemu Linux. Znajdź bieżące różnice w platformie w [przeglądzie](container-instances-overview.md#linux-and-windows-containers).
>
> Instalowanie udziału Azure Files w wystąpieniu kontenera jest podobne do instalacji programu Docker [bind](https://docs.docker.com/storage/bind-mounts/). Należy pamiętać, że w przypadku zainstalowania udziału w katalogu kontenera, w którym znajdują się pliki lub katalogi, te pliki lub katalogi są zasłonięte przez instalację i nie są dostępne podczas działania kontenera.
>

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Przed rozpoczęciem korzystania z udziału plików platformy Azure z Azure Container Instances należy go utworzyć. Uruchom następujący skrypt, aby utworzyć konto magazynu, które będzie hostować udział plików, i sam udział. Nazwa konta magazynu musi być globalnie unikatowa, więc skrypt dodaje losowo wartość do ciągu bazowego.

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

Aby zainstalować udział plików platformy Azure jako wolumin w Azure Container Instances, potrzebne są trzy wartości: nazwa konta magazynu, nazwa udziału i klucz dostępu do magazynu.

Jeśli użyto powyższego skryptu, nazwa konta magazynu była przechowywana w zmiennej $ACI _PERS_STORAGE_ACCOUNT_NAME. Aby wyświetlić nazwę konta, wpisz:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Nazwa udziału jest już znana (zdefiniowana jako *acishare* w powyższym skrypcie), więc wszystko, co pozostanie, jest kluczem konta magazynu, który można znaleźć za pomocą następującego polecenia:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>Wdrażanie kontenera i woluminu instalacji — interfejs wiersza polecenia

Aby zainstalować udział plików platformy Azure jako wolumin w kontenerze przy użyciu interfejsu wiersza polecenia platformy Azure, określ udział i punkt instalacji woluminu podczas tworzenia kontenera za pomocą [AZ Container Create][az-container-create]. Jeśli wykonano poprzednie kroki, można zainstalować utworzony wcześniej udział przy użyciu następującego polecenia, aby utworzyć kontener:

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

Wartość `--dns-name-label` musi być unikatowa w regionie świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. Zaktualizuj wartość w poprzednim poleceniu, jeśli podczas wykonywania polecenia zostanie wyświetlony komunikat o błędzie **etykiety nazwy DNS** .

## <a name="manage-files-in-mounted-volume"></a>Zarządzanie plikami w zainstalowanym woluminie

Po rozpoczęciu kontenera możesz użyć prostej aplikacji internetowej wdrożonej za pośrednictwem obrazu Microsoft [ACI-hellofiles][aci-hellofiles] , aby utworzyć małe pliki tekstowe w udziale plików platformy Azure w podanej ścieżce instalacji. Uzyskaj w pełni kwalifikowaną nazwę domeny (FQDN) aplikacji sieci Web za pomocą polecenia [AZ Container show][az-container-show] :

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

Po zapisaniu tekstu przy użyciu aplikacji można użyć [Azure Portal][portal] lub narzędzia, takiego jak [Eksplorator usługi Microsoft Azure Storage][storage-explorer] , aby pobrać i sprawdzić plik zapisany w udziale plików.

## <a name="deploy-container-and-mount-volume---yaml"></a>Wdróż kontener i wolumin instalacji — YAML

Możesz również wdrożyć grupę kontenerów i zainstalować wolumin w kontenerze za pomocą interfejsu wiersza polecenia platformy Azure i [szablonu YAML](container-instances-multi-container-yaml.md). Wdrażanie przy użyciu szablonu YAML jest preferowaną metodą wdrażania grup kontenerów składających się z wielu kontenerów.

Poniższy szablon YAML definiuje grupę kontenerów z jednym kontenerem utworzonym za pomocą obrazu `aci-hellofiles`. Kontener instaluje *acishare* udział plików platformy Azure utworzony wcześniej jako wolumin. Gdzie to wskazane, wprowadź nazwę i klucz magazynu dla konta magazynu, które obsługuje udział plików. 

Podobnie jak w przykładzie interfejsu wiersza polecenia wartość `dnsNameLabel` musi być unikatowa w regionie świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. W razie konieczności zaktualizuj wartość w pliku YAML.

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

Aby wdrożyć z szablonem YAML, Zapisz poprzedni YAML do pliku o nazwie `deploy-aci.yaml`, a następnie wykonaj polecenie [AZ Container Create][az-container-create] z parametrem `--file`:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Wdróż kontener i wolumin instalacji — Menedżer zasobów

Oprócz wdrożenia interfejsu wiersza polecenia i YAML można wdrożyć grupę kontenerów i zainstalować wolumin w kontenerze przy użyciu [szablonu Menedżer zasobów](/azure/templates/microsoft.containerinstance/containergroups)platformy Azure.

Najpierw Wypełnij tablicę `volumes` w sekcji @no__t grupy kontenerów szablonu. 

Następnie dla każdego kontenera, w którym chcesz zainstalować wolumin, Wypełnij tablicę `volumeMounts` w sekcji `properties` definicji kontenera.

Poniższy szablon Menedżer zasobów definiuje grupę kontenerów z jednym kontenerem utworzonym za pomocą obrazu `aci-hellofiles`. Kontener instaluje *acishare* udział plików platformy Azure utworzony wcześniej jako wolumin. Gdzie to wskazane, wprowadź nazwę i klucz magazynu dla konta magazynu, które obsługuje udział plików. 

Jak w poprzednich przykładach wartość `dnsNameLabel` musi być unikatowa w regionie świadczenia usługi Azure, w którym tworzysz wystąpienie kontenera. W razie konieczności zaktualizuj wartość w szablonie.

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

Aby wdrożyć z szablonem Menedżer zasobów, Zapisz poprzedni kod JSON w pliku o nazwie `deploy-aci.json`, a następnie uruchom polecenie [AZ Group Deployment Create][az-group-deployment-create] z parametrem `--template-file`:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Instalowanie wielu woluminów

Aby zainstalować wiele woluminów w wystąpieniu kontenera, należy wdrożyć przy użyciu [szablonu Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) lub pliku YAML. Aby użyć szablonu lub pliku YAML, podaj szczegóły udziału i zdefiniuj woluminy, wypełniając tablicę `volumes` w sekcji `properties` szablonu. 

Jeśli na przykład utworzono dwa Azure Files udziały o nazwie *share1* i *Share2* na koncie magazynu *mojekontomagazynu*, tablica `volumes` w szablonie Menedżer zasobów będzie wyglądać podobnie do następującej:

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

Następnie dla każdego kontenera w grupie kontenerów, w której chcesz zainstalować woluminy, Wypełnij tablicę `volumeMounts` w sekcji `properties` definicji kontenera. Na przykład spowoduje to zainstalowanie dwóch woluminów, *myvolume1* i *myvolume2*, wcześniej zdefiniowanych:

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

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zainstalować inne typy woluminów w Azure Container Instances:

* [Zainstaluj wolumin emptyDir w Azure Container Instances](container-instances-volume-emptydir.md)
* [Zainstaluj wolumin gitRepo w Azure Container Instances](container-instances-volume-gitrepo.md)
* [Zainstaluj wolumin tajny w Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create