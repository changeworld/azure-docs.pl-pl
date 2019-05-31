---
title: SSH do węzłów klastra Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć połączenie SSH z węzłów klastra Azure Kubernetes Service (AKS) dla zadania rozwiązywania problemów i konserwacji.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: 57eacca75d711c5125a2856a7b6219cd2ec5306b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242032"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Połącz przy użyciu protokołu SSH do usługi Azure Kubernetes Service (AKS) węzłów klastra z powodu konserwacji lub rozwiązywania problemów

W całym cyklu życia klastra Azure Kubernetes Service (AKS) może być konieczne do uzyskania dostępu do węzła usługi AKS. Dostęp może być konserwacji, zbieranie danych dziennika lub inne operacje dotyczące rozwiązywania problemów. Możesz uzyskać dostęp do węzłów AKS przy użyciu protokołu SSH, w tym dla węzłów systemu Windows Server (obecnie dostępna w wersji zapoznawczej w usłudze AKS). Możesz również [łączyć się z węzłami systemu Windows Server przy użyciu połączeń protokołu remote desktop protocol (RDP)][aks-windows-rdp]. Ze względów bezpieczeństwa węzłów AKS nie są połączone z Internetem.

W tym artykule pokazano, jak utworzyć połączenie SSH z węzłem AKS za pomocą prywatnych adresów IP.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

Możesz również muszą wiersza polecenia platformy Azure w wersji 2.0.64 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="add-your-public-ssh-key"></a>Dodaj klucz publiczny SSH

Domyślnie klucze SSH są uzyskany, lub wygenerowane, a następnie dodane do węzłów, podczas tworzenia klastra usługi AKS. Jeśli potrzebujesz określić różne klucze SSH, niż te używane podczas tworzenia klastra usługi AKS, Dodaj klucz publiczny SSH do węzłów systemu Linux w usłudze AKS. Jeśli to konieczne, można utworzyć usługi SSH klucza za pomocą [z systemem macOS lub Linux] [ ssh-nix] lub [Windows][ssh-windows]. Jeśli używasz programu PuTTY ogólnego do tworzenia pary kluczy, Zapisz pary kluczy w OpenSSH formatu zamiast domyślnego programu PuTTy formatem klucza prywatnego (plik ppk).

> [!NOTE]
> Może klucze SSH obecnie można dodawać tylko do węzłów systemu Linux przy użyciu wiersza polecenia platformy Azure. Jeśli używasz węzłów systemu Windows Server, używanie kluczy SSH, podane podczas tworzenia klastra AKS, a następnie przejdź do kroku na [jak uzyskać adres węzła AKS](#get-the-aks-node-address). Ewentualnie [łączyć się z węzłami systemu Windows Server przy użyciu połączeń protokołu remote desktop protocol (RDP)][aks-windows-rdp].

Kroki, aby uzyskać prywatny adres IP węzłów AKS jest inny, na podstawie typu klastra AKS, po uruchomieniu:

* W przypadku większości klastrów usługi AKS, wykonaj kroki, aby [uzyskać adres IP w przypadku regularnego klastrów AKS](#add-ssh-keys-to-regular-aks-clusters).
* Jeśli używasz funkcji w wersji zapoznawczej w usłudze AKS, używanego przez zestawy skalowania maszyn wirtualnych, takich jak wiele pule węzłów lub obsługa kontenerów systemu Windows Server, [postępuj zgodnie z instrukcjami dla klastrów AKS opartych na zestawie skali maszyny wirtualnej](#add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="add-ssh-keys-to-regular-aks-clusters"></a>Dodawanie kluczy SSH do regularnego klastrów usługi AKS

Aby dodać klucz SSH do węzłów systemu Linux w usłudze AKS, wykonaj następujące czynności:

1. Pobierz nazwę grupy zasobów dla zasobów klastra usługi AKS przy użyciu [az aks show][az-aks-show]. Podaj własne podstawowej grupy zasobów i nazwę klastra AKS. Nazwa klastra jest przypisany do zmiennej o nazwie *CLUSTER_RESOURCE_GROUP*:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Listy maszyn wirtualnych za pomocą grupy zasobów klastra usługi AKS [az vm list] [ az-vm-list] polecenia. Te maszyny wirtualne są węzłów AKS:

    ```azurecli-interactive
    az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
    ```

    Następujące przykładowe dane wyjściowe pokazuje węzłów AKS:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Aby dodać kluczy SSH z węzłem, użyj [az vm użytkownik aktualizację] [ az-vm-user-update] polecenia. Podaj nazwę grupy zasobów, a następnie jeden z węzłów AKS uzyskanego w poprzednim kroku. Domyślnie, nazwa użytkownika dla węzłów AKS jest *azureuser*. Podaj lokalizację własne SSH ogólnodostępnej lokalizacji kluczy, takie jak *~/.ssh/id_rsa.pub*, lub Wklej zawartość klucza publicznego SSH:

    ```azurecli-interactive
    az vm user update \
      --resource-group $CLUSTER_RESOURCE_GROUP \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

### <a name="add-ssh-keys-to-virtual-machine-scale-set-based-aks-clusters"></a>Dodawanie kluczy SSH do klastrów usługi AKS opartych na zestawie skali maszyny wirtualnej

Aby dodać klucz SSH do węzła usługi AKS w systemie Linux, który jest częścią zestawu skalowania maszyn wirtualnych, wykonaj następujące czynności:

1. Pobierz nazwę grupy zasobów dla zasobów klastra usługi AKS przy użyciu [az aks show][az-aks-show]. Podaj własne podstawowej grupy zasobów i nazwę klastra AKS. Nazwa klastra jest przypisany do zmiennej o nazwie *CLUSTER_RESOURCE_GROUP*:

    ```azurecli-interactive
    CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
    ```

1. Następnie Pobierz zestawu dla klastra usługi AKS przy użyciu skalowania maszyn wirtualnych [az vmss list] [ az-vmss-list] polecenia. Nazwa zestawu skalowania maszyn wirtualnych jest przypisany do zmiennej o nazwie *SCALE_SET_NAME*:

    ```azurecli-interactive
    SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
    ```

1. Aby dodać kluczy SSH do węzłów w zestawie skalowania maszyn wirtualnych, użyj [az vmss rozszerzenia zestawu] [ az-vmss-extension-set] polecenia. Grupa zasobów klastra i nazwy zestawu skalowania maszyn wirtualnych są dostarczane przy użyciu poprzednich poleceń. Domyślnie, nazwa użytkownika dla węzłów AKS jest *azureuser*. W razie potrzeby zaktualizuj lokalizację własne SSH ogólnodostępnej lokalizacji kluczy, takich jak *~/.ssh/id_rsa.pub*:

    ```azurecli-interactive
    az vmss extension set  \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --vmss-name $SCALE_SET_NAME \
        --name VMAccessForLinux \
        --publisher Microsoft.OSTCExtensions \
        --version 1.4 \
        --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"
    ```

1. Zastosuj klucz SSH do węzłów przy użyciu [az vmss update-instances] [ az-vmss-update-instances] polecenia:

    ```azurecli-interactive
    az vmss update-instances --instance-ids '*' \
        --resource-group $CLUSTER_RESOURCE_GROUP \
        --name $SCALE_SET_NAME
    ```

## <a name="get-the-aks-node-address"></a>Uzyskaj adres węzła usługi AKS

Węzłów AKS nie są widoczne publicznie w Internecie. Aby SSH do węzłów AKS należy użyć prywatnego adresu IP. W następnym kroku utworzysz zasobnik pomocnika w klastra usługi AKS, która umożliwia SSH ten prywatny adres IP węzła. Kroki, aby uzyskać prywatny adres IP węzłów AKS jest inny, na podstawie typu klastra AKS, po uruchomieniu:

* W przypadku większości klastrów usługi AKS, wykonaj kroki, aby [uzyskać adres IP w przypadku regularnego klastrów AKS](#ssh-to-regular-aks-clusters).
* Jeśli używasz funkcji w wersji zapoznawczej w usłudze AKS, używanego przez zestawy skalowania maszyn wirtualnych, takich jak wiele pule węzłów lub obsługa kontenerów systemu Windows Server, [postępuj zgodnie z instrukcjami dla klastrów AKS opartych na zestawie skali maszyny wirtualnej](#ssh-to-virtual-machine-scale-set-based-aks-clusters).

### <a name="ssh-to-regular-aks-clusters"></a>Protokół SSH z regularnych klastrów usługi AKS

Wyświetl prywatny adres IP w usłudze AKS klastra węzła przy użyciu [az vm-— adresy ip] [ az-vm-list-ip-addresses] polecenia. Podaj własny AKS klastra Nazwa grupy zasobów uzyskane w ramach poprzedniego [az-aks-show] [ az-aks-show] krok:

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Następujące przykładowe dane wyjściowe pokazuje prywatnych adresów IP węzłów AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

### <a name="ssh-to-virtual-machine-scale-set-based-aks-clusters"></a>Protokół SSH z klastrami AKS opartych na zestawie skali maszyny wirtualnej

Wewnętrzny adres IP węzłów przy użyciu listy [kubectl get-polecenia][kubectl-get]:

```console
kubectl get nodes -o wide
```

Przykładowe dane wyjściowe poniżej pokazuje wewnętrznych adresów IP wszystkich węzłów w klastrze, w tym węzeł systemu Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Rekord wewnętrzny adres IP węzła, do którego chcesz rozwiązać. Ten adres zostanie użyty w kolejnym kroku.

## <a name="create-the-ssh-connection"></a>Utwórz połączenie SSH

Aby utworzyć połączenie SSH z węzłem AKS, uruchamiasz zasobnik pomocnika w klastrze AKS. Pod tym pomocnika zapewnia dostęp protokołu SSH w klastrze i następnie dodatkowy dostęp do węzła SSH. Aby utworzyć i korzystać z tego zasobnika pomocnika, wykonaj następujące czynności:

1. Uruchom `debian` kontenera obrazów i do niej dołączyć sesji terminalowej. Ten kontener można utworzyć sesję SSH z dowolnego węzła w klastrze AKS:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Jeśli używasz węzłów systemu Windows Server (obecnie dostępna w wersji zapoznawczej w usłudze AKS), należy dodać selektora węzła do polecenia, aby zaplanować Debian kontenera w węźle systemu Linux w następujący sposób:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Podstawowego obrazu systemu Debian nie zawiera składników protokołu SSH. Po sesji terminalowej jest podłączony do kontenera, należy zainstalować klienta SSH za pomocą `apt-get` w następujący sposób:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. W nowym oknie terminala nie jest podłączony do kontenera, listy zasobników przy użyciu klastra usługi AKS [kubectl get pods-] [ kubectl-get] polecenia. Zasobnik utworzony w poprzednim kroku, który zaczyna się od nazwy *aks — ssh*, jak pokazano w poniższym przykładzie:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. W pierwszym kroku w tym artykule dodano publiczny klucz SSH węzłów AKS. Teraz skopiuj klucz prywatny SSH do zasobników. Ten klucz prywatny jest używany do tworzenia protokołu SSH do węzłów AKS.

    Podaj własny *aks — ssh* nazwy zasobnika uzyskanego w poprzednim kroku. W razie potrzeby zmień *~/.ssh/id_rsa* lokalizacji klucz prywatny SSH:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. W sesji terminalowej do kontenera, należy zaktualizować uprawnienia na skopiowany `id_rsa` klucza prywatnego SSH, tak, aby użytkownik tylko do odczytu:

    ```console
    chmod 0600 id_rsa
    ```

1. Teraz Utwórz połączenie SSH z węzłem usługi AKS. Ponownie jest domyślna nazwa użytkownika dla węzłów AKS *azureuser*. Zaakceptuj monit z połączeniem jest kontynuowane zgodnie z klucza SSH jest pierwszy zaufanych. Następnie otrzymują wierszu polecenia powłoki bash węzła usługi AKS:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4
    
    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.
    
    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    [...]
    
    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Usuń dostęp SSH

Po zakończeniu `exit` sesji SSH i następnie `exit` sesji interaktywnych kontenera. Po zamknięciu sesji to kontener, zasobnika używane dla dostępu SSH z klastrem AKS został usunięty.

## <a name="next-steps"></a>Kolejne kroki

Jeśli potrzebne są dodatkowe dane dotyczące rozwiązywania problemów, możesz to zrobić [wyświetlanie dzienników agenta kubelet] [ view-kubelet-logs] lub [wyświetlanie dzienników węzła głównego Kubernetes][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances
