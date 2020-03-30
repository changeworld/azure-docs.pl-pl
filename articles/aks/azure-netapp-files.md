---
title: Integruj pliki NetApp platformy Azure z usługą Azure Kubernetes
description: Dowiedz się, jak zintegrować pliki NetApp platformy Azure z usługą Azure Kubernetes
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 1c4996df66d475c63110e3d2797f55598fd85b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273751"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integruj pliki NetApp platformy Azure z usługą Azure Kubernetes

[Usługa Azure NetApp Files][anf] to usługa magazynu plików klasy korporacyjnej o wysokiej wydajności, działająca na platformie Azure. W tym artykule pokazano, jak zintegrować pliki NetApp platformy Azure z usługą Azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Przed rozpoczęciem
W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

> [!IMPORTANT]
> Klaster AKS musi również znajdować się [w regionie obsługującym pliki NetApp platformy Azure.][anf-regions]

Potrzebne są również zainstalowane i skonfigurowane i skonfigurowane narzędzia Azure CLI w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

### <a name="limitations"></a>Ograniczenia

Podczas korzystania z plików NetApp usługi Azure obowiązują następujące ograniczenia:

* Usługa Azure NetApp Files jest dostępna tylko [w wybranych regionach platformy Azure.][anf-regions]
* Aby można było korzystać z usługi Azure NetApp Files, należy uzyskać dostęp do usługi Azure NetApp Files. Aby ubiegać się o dostęp, można użyć [formularza przesyłania listy oczekujących plików Usługi Azure NetApp][anf-waitlist]. Nie można uzyskać dostępu do usługi Azure NetApp Files, dopóki nie otrzymasz oficjalnego e-maila z potwierdzeniem od zespołu plików NetApp platformy Azure.
* Usługa Azure NetApp Files musi być utworzona w tej samej sieci wirtualnej co klaster AKS.
* Po początkowym wdrożeniu klastra AKS obsługiwane jest tylko statyczne inicjowanie obsługi administracyjnej dla plików NetApp usługi Azure.
* Aby korzystać z dynamicznego inicjowania obsługi administracyjnej za pomocą plików NetApp, należy zainstalować i skonfigurować [program NetApp Trident](https://netapp-trident.readthedocs.io/) w wersji 19.07 lub nowszej.

## <a name="configure-azure-netapp-files"></a>Konfigurowanie plików netapp platformy Azure

> [!IMPORTANT]
> Aby można było zarejestrować dostawcę zasobów *Microsoft.NetApp,* należy wypełnić [formularz przesyłania listy oczekujących plików usługi Azure NetApp][anf-waitlist] dla subskrypcji. Nie można zarejestrować udostępnić zasób, dopóki nie otrzymasz oficjalnego e-maila z potwierdzeniem od zespołu plików NetApp Platformy Azure.

Zarejestruj dostawcę zasobów *Microsoft.NetApp:*

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Może to zająć trochę czasu.

Podczas tworzenia konta NetApp platformy Azure do użytku z usługą AKS należy utworzyć konto w grupie zasobów **węzła.** Najpierw pobierz nazwę grupy zasobów za pomocą polecenia [az aks show][az-aks-show] i dodaj parametr kwerendy. `--query nodeResourceGroup` W poniższym przykładzie pobiera się grupę zasobów węzła dla klastra AKS o nazwie *myAKSCluster* o nazwie grupy zasobów *myResourceGroup:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Utwórz konto usługi Azure NetApp Files w grupie zasobów **węzła** i w tym samym regionie co klaster AKS przy użyciu [konta az netappfiles .][az-netappfiles-account-create] Poniższy przykład tworzy konto o nazwie *myaccount1* w grupie zasobów *MC_myResourceGroup_myAKSCluster_eastus* i *regionie eastus:*

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Utwórz nową pulę pojemności przy użyciu [az netappfiles puli utworzyć][az-netappfiles-pool-create]. Poniższy przykład tworzy nową pulę pojemności o nazwie *mypool1* o rozmiarze 4 TB i poziomie usługi *Premium:*

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Utwórz podsieć, [aby delegować do usługi Azure NetApp Files][anf-delegate-subnet] przy użyciu [sieci az sieci sieci tworzenia podsieci .][az-network-vnet-subnet-create] *Ta podsieć musi znajdować się w tej samej sieci wirtualnej co klaster AKS.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Utwórz wolumin przy użyciu [woluminu az netappfiles .][az-netappfiles-volume-create]

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Tworzenie trwałegowolu

Wyświetl szczegóły woluminu za pomocą [programu az netappfiles volume show][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Utwórz `pv-nfs.yaml` definiujący persistentVolume. Zamień `path` *na creationToken* i `server` *ipAddress* z poprzedniego polecenia. Przykład:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Zaktualizuj *serwer* i *ścieżkę* do wartości woluminu NFS (Network File System) utworzonego w poprzednim kroku. Utwórz persistentVolume za pomocą polecenia [zastosuj kubectl:][kubectl-apply]

```console
kubectl apply -f pv-nfs.yaml
```

Sprawdź *stan* persistentVolume jest *dostępna* za pomocą [polecenia kubectl describe:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Tworzenie persistentVolumeClaim

Utwórz `pvc-nfs.yaml` definiujący persistentVolume. Przykład:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Utwórz persistentVolumeClaim za pomocą [polecenia zastosuj kubectl:][kubectl-apply]

```console
kubectl apply -f pvc-nfs.yaml
```

Sprawdź *stan* PersistentVolumeClaim jest *związany* za pomocą [polecenia kubectl describe:][kubectl-describe]

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Montaż z kapsułą

Utwórz `nginx-nfs.yaml` definiujący zasobnik, który używa PersistentVolumeClaim. Przykład:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Utwórz zasobnik za pomocą polecenia [zastosuj kubectl:][kubectl-apply]

```console
kubectl apply -f nginx-nfs.yaml
```

Sprawdź, czy zasobnik jest *uruchomiony* za pomocą [polecenia kubectl describe:][kubectl-describe]

```console
kubectl describe pod nginx-nfs
```

Sprawdź, czy wolumin został zamontowany w zasobniku za pomocą `df -h` [kubectl exec,][kubectl-exec] aby połączyć się z zasobnikiem, a następnie sprawdzić, czy wolumin jest zamontowany.

```console
$ kubectl exec -it nginx-nfs -- bash
```

```output
root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat plików NetApp platformy Azure, zobacz [Co to są pliki NetApp platformy Azure][anf]. Aby uzyskać więcej informacji na temat korzystania z systemu plików NFS z usługą AKS, zobacz [Ręczne tworzenie i używanie woluminu serwera linux (NFS) z usługą Azure Kubernetes Service (AKS)][aks-nfs].


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
