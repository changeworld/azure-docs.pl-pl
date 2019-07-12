---
title: Połącz protokół RDP węzłów systemu Windows Server klaster Azure Kubernetes Service (AKS)
description: Dowiedz się, jak i Utwórz połączenie RDP z klastrem usługi Azure Kubernetes Service (AKS) węzłów systemu Windows Server dla zadań rozwiązywania problemów i konserwacji.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/04/2019
ms.author: mlearned
ms.openlocfilehash: 0238278b81255d735f8a950ca307d0e05100cfec
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614562"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Połącz przy użyciu protokołu RDP do usługi Azure Kubernetes Service (AKS) węzłach systemu Windows Server z powodu konserwacji lub rozwiązywania problemów

W całym cyklu życia klastra Azure Kubernetes Service (AKS) konieczne może być dostęp do węzłów AKS systemu Windows Server. Dostęp może być konserwacji, zbieranie danych dziennika lub inne operacje dotyczące rozwiązywania problemów. Możesz uzyskać dostęp do węzłów AKS systemu Windows Server przy użyciu protokołu RDP. Alternatywnie, jeśli mają dostęp do węzłów AKS systemu Windows Server za pomocą protokołu SSH i że masz dostęp do tego samego pary, który został użyty podczas tworzenia klastra, możesz wykonać kroki opisane w [SSH do węzłów klastra Azure Kubernetes Service (AKS)][ssh-steps]. Ze względów bezpieczeństwa węzłów AKS nie są połączone z Internetem.

Obsługa węzła systemu Windows Server jest obecnie w wersji zapoznawczej w usłudze AKS.

W tym artykule pokazano, jak utworzyć połączenie RDP z węzłem AKS za pomocą prywatnych adresów IP.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster AKS za pomocą węzła systemu Windows Server. Jeśli potrzebujesz klastra AKS, zobacz artykuł [tworzenia klastra usługi AKS przy użyciu kontenera Windows przy użyciu wiersza polecenia platformy Azure][aks-windows-cli]. You need the Windows administrator username and password for the Windows Server node you want to troubleshoot. You also need an RDP client such as [Microsoft Remote Desktop][rdp-mac].

Możesz również muszą wiersza polecenia platformy Azure w wersji 2.0.61 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli potrzebujesz instalacja lub uaktualnienie, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Wdrażanie maszyny wirtualnej do tej samej podsieci co klaster

Węzły systemu Windows Server klastra usługi AKS nie mają dostępną z zewnątrz adresów IP. Aby utworzyć połączenie RDP, można wdrożyć maszynę wirtualną z publicznie dostępny adres IP tej samej podsieci co węzły systemu Windows Server.

Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* w *myResourceGroup* grupy zasobów.

Najpierw pobierz podsieci używane przez pula węzłów systemu Windows Server. Aby uzyskać identyfikator podsieci, potrzebna jest nazwa podsieci. Aby uzyskać nazwę podsieci, potrzebna jest nazwa sieci wirtualnej. Wykonywanie zapytań klastra dla listy sieci, aby uzyskać nazwę sieci wirtualnej. Aby wysłać zapytanie do klastra, potrzebna jest jego nazwa. Możesz uzyskać wszystkie te, wykonując następujące czynności w usłudze Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Teraz, gdy masz SUBNET_ID, uruchom następujące polecenie w tym samym oknie usługi Azure Cloud Shell, aby utworzyć maszynę Wirtualną:

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

Następujące przykładowe dane wyjściowe pokazuje maszyna wirtualna została pomyślnie utworzona i wyświetla publiczny adres IP maszyny wirtualnej.

```console
13.62.204.18
```

Zapisz publiczny adres IP maszyny wirtualnej. Ten adres zostanie użyty w kolejnym kroku.

## <a name="get-the-node-address"></a>Uzyskaj adres węzła

Aby zarządzać klastrem Kubernetes, należy użyć [kubectl][kubectl], czyli klienta wiersza polecenia usługi Kubernetes. Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Aby zainstalować `kubectl` lokalnie, użyj [az aks install-cli][az-aks-install-cli] polecenia:
    
```azurecli-interactive
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Wewnętrzny adres IP węzłów systemu Windows Server przy użyciu listy [kubectl get-][kubectl-get] polecenia:

```console
kubectl get nodes -o wide
```

Przykładowe dane wyjściowe poniżej pokazuje wewnętrznych adresów IP wszystkich węzłów w klastrze, w tym dla węzłów systemu Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Zapisz wewnętrzny adres IP węzła systemu Windows Server, którą chcesz rozwiązać. Ten adres zostanie użyty w kolejnym kroku.

## <a name="connect-to-the-virtual-machine-and-node"></a>Łączenie się z maszyny wirtualnej i węzła

Łączenie do publicznego adresu IP maszyny wirtualnej zostały utworzone wcześniej takich jak za pomocą klienta RDP [Pulpit zdalny Microsoft][rdp-mac].

![Obraz przedstawiający połączenie z maszyną wirtualną za pomocą klienta RDP](media/rdp/vm-rdp.png)

Po nawiązaniu połączenia z maszyną wirtualną, połączyć się z *wewnętrzny adres IP* węzła systemu Windows Server, użytkownik chce Rozwiązywanie problemów za pomocą klienta RDP, z poziomu Twojej maszyny wirtualnej.

![Obraz przedstawiający łączenie z węzłem systemu Windows Server za pomocą klienta RDP](media/rdp/node-rdp.png)

Masz teraz połączenie do węzła usługi Windows Server.

![Obraz okna polecenia w węźle w systemie Windows Server](media/rdp/node-session.png)

Możesz teraz uruchomić dowolne polecenia dotyczące rozwiązywania problemów *cmd* okna. Ponieważ węzłów systemu Windows Server używa systemu Windows Server Core, nie ma pełnym interfejsem GUI lub innych narzędzi z graficznym interfejsem użytkownika podczas łączenia z węzłem systemu Windows Server za pośrednictwem protokołu RDP.

## <a name="remove-rdp-access"></a>Usuń dostęp RDP

Gdy skończysz, zamknij połączenie RDP do węzła systemu Windows Server, a następnie Zamknij sesję RDP z maszyną wirtualną. Po zamknięciu obie sesje protokołu RDP, należy usunąć maszynę wirtualną za pomocą [Usuń az vm][az-vm-delete] polecenia:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebne są dodatkowe dane dotyczące rozwiązywania problemów, możesz to zrobić [wyświetlanie dzienników węzła głównego Kubernetes][view-master-logs] or [Azure Monitor][azure-monitor-containers].

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
