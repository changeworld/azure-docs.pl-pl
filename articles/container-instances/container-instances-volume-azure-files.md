---
title: Instalowanie woluminu usługi Azure Files do grupy kontenerów
description: Dowiedz się, jak zainstalować wolumin usługi Azure Files, aby zachowywać stan w przypadku wystąpień kontenerów platformy Azure
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f66890c503de8de9160f11fb28795012ae57daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561341"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances

Domyślnie usługa Azure Container Instances jest bezstanowa. Po awarii lub wyłączeniu kontenera cały jego stan zostanie utracony. Aby zachować stan również wtedy, należy zainstalować wolumin z magazynu zewnętrznego. Jak pokazano w tym artykule, wystąpienia kontenerów platformy Azure mogą montować udział plików platformy Azure utworzony za pomocą [usługi Azure Files](../storage/files/storage-files-introduction.md). Usługa Azure Files oferuje w pełni zarządzane udziały plików hostowane w usłudze Azure Storage, które są dostępne za pośrednictwem standardowego protokołu bloku komunikatów serwera (SMB). Przy użyciu udziału plików platformy Azure z wystąpieniami kontenera platformy Azure zapewnia funkcje udostępniania plików podobne do korzystania z udziału plików platformy Azure z maszynami wirtualnymi platformy Azure.

> [!NOTE]
> Instalowanie udziału usługi Azure Files jest obecnie ograniczone do kontenerów systemu Linux. Znajdź aktualne różnice między platformami w [przeglądzie](container-instances-overview.md#linux-and-windows-containers).
>
> Instalowanie udziału usługi Azure Files w wystąpieniu kontenera jest podobne do [instalacji powiązania](https://docs.docker.com/storage/bind-mounts/)platformy Docker. Należy pamiętać, że jeśli zostanie zamontowany udział w katalogu kontenera, w którym istnieją pliki lub katalogi, te pliki lub katalogi są zasłonięte przez instalację i nie są dostępne podczas pracy kontenera.
>

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Przed rozpoczęciem korzystania z udziału plików platformy Azure za pomocą usługi Azure Container Instances należy go utworzyć. Uruchom następujący skrypt, aby utworzyć konto magazynu do obsługi udziału plików i samego udziału. Nazwa konta magazynu musi być globalnie unikatowa, więc skrypt dodaje losową wartość do podstawowego ciągu.

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
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Uzyskiwanie poświadczeń magazynu

Aby zainstalować udział plików platformy Azure jako wolumin w usłudze Azure Container Instances, potrzebujesz trzech wartości: nazwy konta magazynu, nazwy udziału i klucza dostępu do magazynu.

* **Nazwa konta magazynu** — jeśli użyto poprzedniego skryptu, nazwa `$ACI_PERS_STORAGE_ACCOUNT_NAME` konta magazynu była przechowywana w zmiennej. Aby wyświetlić nazwę konta, wpisz:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Nazwa udziału** — ta wartość jest `acishare` już znana (zdefiniowana jak w poprzednim skrypcie)

* **Klucz konta magazynu** — tę wartość można znaleźć za pomocą następującego polecenia:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Wdrażanie kontenera i woluminu instalacji — cli

Aby zainstalować udział plików platformy Azure jako wolumin w kontenerze przy użyciu interfejsu wiersza polecenia platformy Azure, określ punkt instalacji udziału i woluminu podczas tworzenia kontenera za pomocą [kontenera az create][az-container-create]. Jeśli po wykonaniu poprzednich kroków można zainstalować utworzony wcześniej udział przy użyciu następującego polecenia do utworzenia kontenera:

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

Wartość `--dns-name-label` musi być unikatowa w regionie platformy Azure, w którym utworzysz wystąpienie kontenera. Zaktualizuj wartość w poprzednim poleceniu, jeśli podczas wykonywania polecenia zostanie wyświetlony komunikat o błędzie **etykiety nazwy DNS.**

## <a name="manage-files-in-mounted-volume"></a>Zarządzanie plikami w woluminie zainstalowanym

Po uruchomieniu kontenera można użyć prostej aplikacji sieci web wdrożonej za pośrednictwem obrazu Microsoft [aci-hellofiles,][aci-hellofiles] aby utworzyć małe pliki tekstowe w udziale plików platformy Azure w określonej ścieżce instalacji. Uzyskaj w pełni kwalifikowaną nazwę domeny aplikacji sieci web (FQDN) za pomocą polecenia [az container show:][az-container-show]

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Po zapisaniu tekstu za pomocą aplikacji można użyć [witryny Azure portal][portal] lub narzędzia, takiego jak [Eksplorator usług Microsoft Azure Storage,][storage-explorer] aby pobrać i sprawdzić plik lub pliki zapisane w udziale plików.

## <a name="deploy-container-and-mount-volume---yaml"></a>Wdrażanie kontenera i instalowania woluminu — YAML

Można również wdrożyć grupę kontenerów i zainstalować wolumin w kontenerze za pomocą interfejsu wiersza polecenia platformy Azure i [szablonu YAML.](container-instances-multi-container-yaml.md) Wdrażanie przez szablon YAML jest preferowaną metodą podczas wdrażania grup kontenerów składających się z wielu kontenerów.

Poniższy szablon YAML definiuje grupę kontenerów `aci-hellofiles` z jednym kontenerem utworzonym za pomocą obrazu. Kontener instaluje *acishare* udziału pliku platformy Azure utworzony wcześniej jako wolumin. Jeśli jest to wskazane, wprowadź nazwę i klucz magazynu dla konta magazynu, na którym znajduje się udział plików. 

Podobnie jak w przykładzie interfejsu wiersza `dnsNameLabel` polecenia wartość musi być unikatowa w regionie platformy Azure, w którym utworzysz wystąpienie kontenera. W razie potrzeby zaktualizuj wartość w pliku YAML.

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

Aby wdrożyć za pomocą szablonu YAML, zapisz `deploy-aci.yaml`poprzedni YAML w pliku o `--file` nazwie , a następnie wykonaj polecenie [tworzenia kontenera az][az-container-create] z parametrem:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Wdrażanie kontenera i instalowania woluminu — Menedżer zasobów

Oprócz wdrożenia interfejsu wiersza polecenia i YAML można wdrożyć grupę kontenerów i zainstalować wolumin w kontenerze przy użyciu [szablonu usługi](/azure/templates/microsoft.containerinstance/containergroups)Azure Resource Manager .

Najpierw wypełnij tablicę `volumes` w sekcji `properties` grupy kontenerów szablonu. 

Następnie dla każdego kontenera, w którym chcesz zainstalować wolumin, `volumeMounts` wypełnij `properties` tablicę w sekcji definicji kontenera.

Poniższy szablon Menedżera zasobów definiuje grupę kontenerów `aci-hellofiles` z jednym kontenerem utworzonym za pomocą obrazu. Kontener instaluje *acishare* udziału pliku platformy Azure utworzony wcześniej jako wolumin. Jeśli jest to wskazane, wprowadź nazwę i klucz magazynu dla konta magazynu, na którym znajduje się udział plików. 

Podobnie jak w poprzednich `dnsNameLabel` przykładach wartość musi być unikatowa w regionie platformy Azure, w którym utworzysz wystąpienie kontenera. W razie potrzeby zaktualizuj wartość w szablonie.

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

Aby wdrożyć za pomocą szablonu Menedżera zasobów, zapisz `deploy-aci.json`poprzedni json w pliku o `--template-file` nazwie , a następnie wykonaj polecenie tworzenia wdrożenia grupy [AZ][az-group-deployment-create] z parametrem:

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Montowanie wielu woluminów

Aby zainstalować wiele woluminów w wystąpieniu kontenera, należy wdrożyć przy użyciu [szablonu usługi Azure Resource Manager,](/azure/templates/microsoft.containerinstance/containergroups)pliku YAML lub innej metody programowej. Aby użyć szablonu lub pliku YAML, podaj szczegóły udziału `volumes` i zdefiniuj woluminy, zapełniając tablicę `properties` w sekcji pliku. 

Na przykład jeśli utworzono dwa udziały usługi Azure Files o nazwie *share1* i *share2* na koncie magazynu *myStorageAccount,* `volumes` tablica w szablonie Menedżera zasobów będzie wyglądać podobnie do następujących:

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

Następnie dla każdego kontenera w grupie kontenerów, w którym chcesz zainstalować `volumeMounts` woluminy, `properties` wypełnij tablicę w sekcji definicji kontenera. Na przykład, to montuje dwa woluminy, *myvolume1* i *myvolume2*, wcześniej zdefiniowane:

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

Dowiedz się, jak zainstalować inne typy woluminów w wystąpieniach kontenera platformy Azure:

* [Instalowanie woluminu pustegodiru w wystąpieniach kontenera platformy Azure](container-instances-volume-emptydir.md)
* [Instalowanie woluminu gitRepo w wystąpieniach kontenera platformy Azure](container-instances-volume-gitrepo.md)
* [Instalowanie woluminu tajnego w wystąpieniach kontenera platformy Azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create