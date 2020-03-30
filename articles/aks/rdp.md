---
title: RDP do węzłów klastra usługi Azure Kubernetes (AKS) w systemie Windows Server
description: Dowiedz się, jak utworzyć połączenie RDP z węzłami systemu Windows Server klastra usługi Azure Kubernetes (AKS) w celu rozwiązywania problemów i konserwacji.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 897504aa9902d0feaf4245c719d3a4a3c6fd2241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594485"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Łączenie się z węzłami klastra windows server usługi Azure Kubernetes (Azure Kubernetes) w celu konserwacji lub rozwiązywania problemów

W całym cyklu życia klastra usługi Azure Kubernetes (AKS) może być konieczne uzyskiwać dostęp do węzła systemu AKS windows server. Ten dostęp może być do konserwacji, zbierania dzienników lub innych operacji rozwiązywania problemów. Dostęp do węzłów systemu AKS w systemie Windows Server można uzyskać przy użyciu protokołu RDP. Alternatywnie, jeśli chcesz użyć SSH, aby uzyskać dostęp do węzłów AKS Windows Server i masz dostęp do tego samego pęczka klucza, który był używany podczas tworzenia klastra, możesz wykonać kroki opisane w [SSH w węzłach klastra usługi Azure Kubernetes Service (AKS).][ssh-steps] Ze względów bezpieczeństwa węzły usługi AKS nie są udostępniane w Internecie.

Obsługa węzłów systemu Windows Server jest obecnie w wersji zapoznawczej w systemie AKS.

W tym artykule pokazano, jak utworzyć połączenie RDP z węzłem AKS przy użyciu ich prywatnych adresów IP.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS z węzłem systemu Windows Server. Jeśli potrzebujesz klastra AKS, zapoznaj się z artykułem o [tworzeniu klastra AKS z kontenerem systemu Windows przy użyciu interfejsu wiersza polecenia platformy Azure][aks-windows-cli]. Potrzebna jest nazwa użytkownika i hasło administratora systemu Windows dla węzła systemu Windows Server, który chcesz rozwiązać problem. Potrzebny jest również klient RDP, taki jak [Pulpit zdalny firmy Microsoft.][rdp-mac]

Potrzebne są również zainstalowane i skonfigurowane i skonfigurowane narzędzia Azure CLI w wersji 2.0.61 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Wdrażanie maszyny wirtualnej w tej samej podsieci co klaster

Węzły systemu Windows Server klastra AKS nie mają adresów IP dostępnych z zewnątrz. Aby nawiązać połączenie RDP, można wdrożyć maszynę wirtualną z publicznie dostępnym adresem IP w tej samej podsieci co węzły systemu Windows Server.

Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* w grupie zasobów *myResourceGroup.*

Najpierw pobierz podsieć używane przez pulę węzłów systemu Windows Server. Aby uzyskać identyfikator podsieci, potrzebujesz nazwy podsieci. Aby uzyskać nazwę podsieci, potrzebujesz nazwy sieci wirtualnej. Pobierz nazwę sieci wirtualnej, odpytując klaster pod kątem jego listy sieci. Aby zbadać klaster, potrzebna jest jego nazwa. Wszystkie te elementy można uzyskać, uruchamiając następujące w usłudze Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Teraz, gdy masz SUBNET_ID, uruchom następujące polecenie w tym samym oknie usługi Azure Cloud Shell, aby utworzyć maszynę wirtualną:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

Poniższy przykładowy wynik pokazuje, że maszyna wirtualna została pomyślnie utworzona i wyświetla publiczny adres IP maszyny wirtualnej.

```console
13.62.204.18
```

Rejestrowanie publicznego adresu IP maszyny wirtualnej. Użyjesz tego adresu w późniejszym kroku.

## <a name="allow-access-to-the-virtual-machine"></a>Zezwalaj na dostęp do maszyny wirtualnej

Podsieci puli węzłów AKS są domyślnie chronione za pomocą sieciowych grup zabezpieczeń (network security groups). Aby uzyskać dostęp do maszyny wirtualnej, musisz włączyć dostęp w sieciowej sieciowej.

> [!NOTE]
> NSG są kontrolowane przez usługę AKS. Wszelkie zmiany w organizacji pozarządowej zostaną zastąpione w dowolnym momencie przez płaszczyznę sterowania.
>

Najpierw pobierz grupę zasobów i nazwę nsg nsg, aby dodać regułę do:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Następnie utwórz regułę nsg:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Pobierz adres węzła

Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes — narzędzia [kubectl][kubectl]. Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Aby zainstalować narzędzie `kubectl` lokalnie, użyj polecenia [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Wyświetl wewnętrzny adres IP węzłów systemu Windows Server za pomocą polecenia [kubectl get:][kubectl-get]

```console
kubectl get nodes -o wide
```

Poniższe przykładowe dane wyjściowe pokazują wewnętrzne adresy IP wszystkich węzłów w klastrze, w tym węzłów systemu Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Zapisz wewnętrzny adres IP węzła systemu Windows Server, którego problem chcesz rozwiązać. Użyjesz tego adresu w późniejszym kroku.

## <a name="connect-to-the-virtual-machine-and-node"></a>Łączenie się z maszyną wirtualną i węzłem

Połącz się z publicznym adresem IP maszyny wirtualnej utworzonej wcześniej przy użyciu klienta RDP, takiego jak [Pulpit zdalny firmy Microsoft][rdp-mac].

![Obraz połączenia z maszyną wirtualną przy użyciu klienta RDP](media/rdp/vm-rdp.png)

Po nawiązaniu połączenia z maszyną wirtualną połącz się z *wewnętrznym adresem IP* węzła systemu Windows Server, którego problem chcesz rozwiązać przy użyciu klienta RDP z poziomu maszyny wirtualnej.

![Obraz łączenia się z węzłem systemu Windows Server przy użyciu klienta RDP](media/rdp/node-rdp.png)

Teraz masz połączenie z węzłem systemu Windows Server.

![Obraz okna cmd w węźle systemu Windows Server](media/rdp/node-session.png)

Teraz można uruchomić wszystkie polecenia rozwiązywania problemów w oknie *cmd.* Ponieważ węzły systemu Windows Server używają systemu Windows Server Core, nie ma pełnego interfejsu użytkownika ani innych narzędzi graficznych podczas łączenia się z węzłem systemu Windows Server przez rdp.

## <a name="remove-rdp-access"></a>Usuwanie dostępu do PROTOKOŁU RDP

Po zakończeniu zamknij połączenie RDP z węzłem systemu Windows Server, a następnie zamknij sesję RDP na maszynie wirtualnej. Po zamknięciu obu sesji RDP usuń maszynę wirtualną za pomocą polecenia [az vm delete:][az-vm-delete]

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

I zasada NSG:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz dodatkowych danych dotyczących rozwiązywania problemów, możesz [wyświetlić dzienniki węzłów głównych kubernetes][view-master-logs] lub [usługę Azure Monitor][azure-monitor-containers].

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/insights/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md
