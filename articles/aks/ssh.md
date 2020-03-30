---
title: Połączenie SSH z węzłami klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć połączenie SSH z węzłami klastra usługi Azure Kubernetes (AKS) do rozwiązywania problemów i konserwacji zadań.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: dfdcda40a24142f85bbeb360aacf0971d72d181f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593635"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Nawiązywanie połączenia przy użyciu protokołu SSH z węzłami klastra usługi Azure Kubernetes Service w celu konserwacji lub rozwiązywania problemów

W całym cyklu życia klastra usługi Azure Kubernetes (AKS) może być konieczne uzyskiwać dostęp do węzła AKS. Ten dostęp może być do konserwacji, zbierania dzienników lub innych operacji rozwiązywania problemów. Dostęp do węzłów usługi AKS można uzyskać przy użyciu funkcji SSH, w tym węzłów systemu Windows Server (obecnie w wersji zapoznawczej w systemie AKS). Można również [łączyć się z węzłami systemu Windows Server przy użyciu połączeń protokołu RDP (Remote desktop Protocol).][aks-windows-rdp] Ze względów bezpieczeństwa węzły usługi AKS nie są udostępniane w Internecie. Aby SSH do węzłów AKS, należy użyć prywatnego adresu IP.

W tym artykule pokazano, jak utworzyć połączenie SSH z węzłem AKS przy użyciu ich prywatnych adresów IP.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

Domyślnie klucze SSH są uzyskiwane lub generowane, a następnie dodawane do węzłów podczas tworzenia klastra AKS. W tym artykule pokazano, jak określić różne klucze SSH niż klucze SSH używane podczas tworzenia klastra AKS. W tym artykule pokazano również, jak określić prywatny adres IP węzła i połączyć się z nim za pomocą protokołu SSH. Jeśli nie trzeba określić inny klucz SSH, a następnie można pominąć krok dodawania klucza publicznego SSH do węzła.

W tym artykule przyjęto również założenie, że masz klucz SSH. Klucz SSH można utworzyć za pomocą [systemu macOS lub Linux][ssh-nix] lub [Windows][ssh-windows]. Jeśli używasz PuTTY Gen do utworzenia pary kluczy, zapisz parę kluczy w formacie OpenSSH, a nie domyślnym formacie klucza prywatnego PuTTy (plik ppk).

Potrzebne są również zainstalowane i skonfigurowane i skonfigurowane narzędzia Azure CLI w wersji 2.0.64 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Konfigurowanie klastrów AKS opartych na zestawie skalowania maszyny wirtualnej dla dostępu SSH

Aby skonfigurować zestaw skalowania maszyny wirtualnej oparty na dostępie do SSH, znajdź nazwę zestawu skalowania maszyny wirtualnej klastra i dodaj klucz publiczny SSH do tego zestawu skalowania.

Użyj polecenia [az aks show,][az-aks-show] aby uzyskać nazwę grupy zasobów klastra AKS, a następnie polecenie [listy az vmss,][az-vmss-list] aby uzyskać nazwę zestawu skalowania.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query [0].name -o tsv)
```

Powyższy przykład przypisuje nazwę grupy zasobów klastra dla *myAKSCluster* w *myResourceGroup* do *CLUSTER_RESOURCE_GROUP*. W przykładzie użyto *CLUSTER_RESOURCE_GROUP* do wyświetlenia nazwy zestawu skalowania i przypisania jej do *SCALE_SET_NAME*.

> [!IMPORTANT]
> W tej chwili należy zaktualizować klucze SSH tylko dla klastrów AKS opartych na zestawie na podstawie maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
> 
> W przypadku węzłów systemu Linux klucze SSH można obecnie dodawać tylko przy użyciu interfejsu wiersza polecenia platformy Azure. Jeśli chcesz połączyć się z węzłem systemu Windows Server przy użyciu funkcji SSH, użyj kluczy SSH dostarczonych podczas tworzenia klastra AKS i pomiń następny zestaw poleceń dodawania klucza publicznego SSH. Nadal będziesz potrzebować adresu IP węzła, który chcesz rozwiązać, co jest wyświetlane w końcowym poleceniu tej sekcji. Alternatywnie można [połączyć się z węzłami systemu Windows Server przy użyciu połączeń protokołu RDP (Remote desktop)][aks-windows-rdp] zamiast używać protokołu SSH.

Aby dodać klucze SSH do węzłów w zestawie skalowania maszyny wirtualnej, użyj [zestawu rozszerzeń az vmss][az-vmss-extension-set] i poleceń [az vmss update-instances.][az-vmss-update-instances]

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

W powyższym przykładzie użyto *zmiennych CLUSTER_RESOURCE_GROUP* i *SCALE_SET_NAME* z poprzednich poleceń. W powyższym przykładzie użyto również *~/.ssh/id_rsa.pub* jako lokalizacji klucza publicznego SSH.

> [!NOTE]
> Domyślnie nazwa użytkownika węzłów AKS jest *azureuser*.

Po dodaniu klucza publicznego SSH do zestawu skalowania można ssh do maszyny wirtualnej węzła w tym zestawie skalowania przy użyciu jego adresu IP. Wyświetlanie prywatnych adresów IP węzłów klastra AKS za pomocą [polecenia kubectl get][kubectl-get].

```console
kubectl get nodes -o wide
```

Poniższe przykładowe dane wyjściowe pokazują wewnętrzne adresy IP wszystkich węzłów w klastrze, w tym węzła systemu Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Zapisz wewnętrzny adres IP węzła, który chcesz rozwiązać.

Aby uzyskać dostęp do węzła przy użyciu funkcji SSH, wykonaj czynności opisane w [programie Utwórz połączenie SSH](#create-the-ssh-connection).

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Konfigurowanie klastrów AKS opartych na zestawie dostępności maszyny wirtualnej dla dostępu SSH

Aby skonfigurować klaster AKS oparty na zestawie dostępności na podstawie zestawu maszyn wirtualnych dla dostępu SSH, znajdź nazwę węzła systemu Linux klastra i dodaj klucz publiczny SSH do tego węzła.

Użyj polecenia [az aks show,][az-aks-show] aby uzyskać nazwę grupy zasobów klastra AKS, a następnie polecenie [listy az vm,][az-vm-list] aby wyświetlić nazwę maszyny wirtualnej węzła systemu Linux klastra.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Powyższy przykład przypisuje nazwę grupy zasobów klastra dla *myAKSCluster* w *myResourceGroup* do *CLUSTER_RESOURCE_GROUP*. W przykładzie użyto *CLUSTER_RESOURCE_GROUP* do wyświetlenia nazwy maszyny wirtualnej. Przykładowe dane wyjściowe pokazuje nazwę maszyny wirtualnej:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Aby dodać klucze SSH do węzła, użyj polecenia [az vm user update.][az-vm-user-update]

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

W powyższym przykładzie użyto zmiennej *CLUSTER_RESOURCE_GROUP* i nazwy maszyny wirtualnej węzła z poprzednich poleceń. W powyższym przykładzie użyto również *~/.ssh/id_rsa.pub* jako lokalizacji klucza publicznego SSH. Można również użyć zawartości klucza publicznego SSH zamiast określania ścieżki.

> [!NOTE]
> Domyślnie nazwa użytkownika węzłów AKS jest *azureuser*.

Po dodaniu klucza publicznego SSH do maszyny wirtualnej węzła można ssh do tej maszyny wirtualnej przy użyciu jego adresu IP. Wyświetlanie prywatnego adresu IP węzła klastra AKS przy użyciu polecenia [az vm list-ip-addresses.][az-vm-list-ip-addresses]

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

W powyższym przykładzie użyto *CLUSTER_RESOURCE_GROUP* zestawu zmiennych w poprzednich poleceniach. Poniższe przykładowe dane wyjściowe pokazują prywatne adresy IP węzłów AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Tworzenie połączenia SSH

Aby utworzyć połączenie SSH z węzłem AKS, należy uruchomić zasobnik pomocniczy w klastrze AKS. Ten zasobnik pomocnika zapewnia dostęp SSH do klastra, a następnie dodatkowy dostęp do węzła SSH. Aby utworzyć i używać tej zasobnika pomocnika, wykonaj następujące kroki:

1. Uruchom `debian` obraz kontenera i dołącz do niego sesję terminala. Ten kontener może służyć do tworzenia sesji SSH z dowolnym węzłem w klastrze AKS:

    ```console
    kubectl run --generator=run-pod/v1 -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Jeśli używasz węzłów systemu Windows Server (obecnie w wersji zapoznawczej w systemie AKS), dodaj selektor węzłów do polecenia, aby zaplanować kontener Debiana w węźle Systemu Linux:
    >
    > `kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"apps/v1","spec":{"template":{"spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}}}'`

1. Po podłączeniu sesji terminala do kontenera zainstaluj `apt-get`klienta SSH za pomocą:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Otwórz nowe okno terminala, niepodłączone do kontenera, skopiuj prywatny klucz SSH do zasobnika pomocnika. Ten klucz prywatny jest używany do tworzenia SSH w węźle AKS. 

   W razie potrzeby zmień *~/.ssh/id_rsa* na lokalizację prywatnego klucza SSH:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Wróć do sesji terminalu do kontenera, zaktualizuj uprawnienia do skopiowanego `id_rsa` prywatnego klucza SSH, tak aby był on tylko do odczytu użytkownika:

    ```console
    chmod 0600 id_rsa
    ```

1. Utwórz połączenie SSH z węzłem AKS. Ponownie domyślną nazwą użytkownika dla węzłów AKS jest *azureuser*. Zaakceptuj monit, aby kontynuować połączenie, ponieważ klucz SSH jest najpierw zaufany. Następnie zostanie wyświetlony monit bash węzła AKS:

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

## <a name="remove-ssh-access"></a>Usuwanie dostępu SSH

Po zakończeniu `exit` sesji SSH, `exit` a następnie sesji kontenera interaktywnego. Po zamknięciu tej sesji kontenera zasobnik używany do dostępu SSH z klastra AKS jest usuwany.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz dodatkowych danych dotyczących rozwiązywania problemów, możesz [wyświetlić dzienniki kubelet][view-kubelet-logs] lub [wyświetlić dzienniki węzłów głównych kubernetes][view-master-logs].

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
