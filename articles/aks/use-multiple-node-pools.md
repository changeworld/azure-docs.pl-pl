---
title: Korzystanie z wielu pul węzłów w usłudze Azure Kubernetes Service (AKS)
description: Informacje na temat tworzenia pul węzłów i zarządzania nimi dla klastra w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/17/2019
ms.author: mlearned
ms.openlocfilehash: 4ba9840d745995fdf7b8b14889a0c021917f0ec3
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278173"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Wersja zapoznawcza — tworzenie i zarządzanie wieloma pulami węzłów dla klastra w usłudze Azure Kubernetes Service (AKS)

W usłudze Azure Kubernetes Service (AKS) węzły tej samej konfiguracji są pogrupowane w *Pule węzłów*. Te pule węzłów zawierają bazowe maszyny wirtualne, na których działają aplikacje. Początkowa liczba węzłów i ich rozmiar (SKU) są definiowane podczas tworzenia klastra AKS, który tworzy *domyślną pulę węzłów*. Aby można było obsługiwać aplikacje, które mają różne wymagania dotyczące obliczeń lub magazynu, możesz utworzyć dodatkowe pule węzłów. Na przykład użyj tych dodatkowych pul węzłów, aby udostępnić procesory GPU dla aplikacji intensywnie korzystających z mocy obliczeniowej, lub dostęp do magazynu SSD o wysokiej wydajności.

W tym artykule pokazano, jak utworzyć wiele pul węzłów i zarządzać nimi w klastrze AKS. Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje AKS w wersji zapoznawczej to samoobsługowe i niezgodne. Są one dostarczane w celu zebrania opinii i usterek z naszej społeczności. W wersji zapoznawczej te funkcje nie są przeznaczone do użytku produkcyjnego. Funkcje w publicznej wersji zapoznawczej są objęte wsparciem "najlepsze wysiłki". Pomoc dla zespołów pomocy technicznej AKS jest dostępna w godzinach pracy w czasie pracy (PST). Dodatkowe informacje można znaleźć w następujących artykułach pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia AKS-Preview

Aby można było używać wielu nodepools, wymagany jest interfejs wiersza polecenia *AKS-Preview* w wersji 0.4.1 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej AKS* , wykorzystując polecenie [AZ Extension Add][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-multiple-node-pool-feature-provider"></a>Zarejestruj dostawcę funkcji puli wielu węzłów

Aby utworzyć klaster AKS, który może korzystać z wielu pul węzłów, należy najpierw włączyć dwie flagi funkcji w ramach subskrypcji. Klastry puli wielowęzłowej używają zestawu skalowania maszyn wirtualnych (VMSS) do zarządzania wdrożeniem i konfiguracją węzłów Kubernetes. Zarejestruj flagi funkcji *MultiAgentpoolPreview* i *VMSSPreview* za pomocą polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji nie można obecnie wyrejestrować tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej można użyć wartości domyślnych dla wszystkich klastrów AKS utworzonych w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Korzystaj z oddzielnej subskrypcji, aby testować funkcje w wersji zapoznawczej i zbierać opinie.

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Wszystkie klastry AKS utworzone po pomyślnym zarejestrowaniu *MultiAgentpoolPreview* korzystają z tego środowiska w wersji zapoznawczej. Aby kontynuować tworzenie regularnych, w pełni obsługiwanych klastrów, nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Użyj oddzielnej subskrypcji testowej lub deweloperskiej platformy Azure do testowania funkcji w wersji zapoznawczej.

Wyświetlenie stanu *rejestracji*może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Ograniczenia

Następujące ograniczenia są stosowane podczas tworzenia klastrów AKS i zarządzania nimi, które obsługują pule wielu węzłów:

* Pule wielu węzłów są dostępne tylko dla klastrów utworzonych po pomyślnym zarejestrowaniu funkcji *MultiAgentpoolPreview* i *VMSSPreview* dla Twojej subskrypcji. Nie można dodać pul węzłów i zarządzać nimi z istniejącym klastrem AKS utworzonym przed pomyślnym zarejestrowaniem tych funkcji.
* Nie można usunąć pierwszej puli węzłów.
* Nie można użyć dodatku routingu aplikacji protokołu HTTP.
* Nie można dodać/zaktualizować/usunąć pul węzłów przy użyciu istniejącego szablonu Menedżer zasobów, tak jak w przypadku większości operacji. Zamiast tego należy [użyć oddzielnego szablonu Menedżer zasobów](#manage-node-pools-using-a-resource-manager-template) , aby wprowadzić zmiany pul węzłów w klastrze AKS.

Chociaż ta funkcja jest dostępna w wersji zapoznawczej, obowiązują następujące dodatkowe ograniczenia:

* Klaster AKS może mieć maksymalnie osiem pul węzłów.
* Klaster AKS może mieć maksymalnie 400 węzłów w ramach tych ośmiu pul węzłów.
* Wszystkie pule węzłów muszą znajdować się w tej samej podsieci

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Aby rozpocząć, Utwórz klaster AKS z pulą jednego węzła. W poniższym przykładzie za pomocą polecenia [AZ Group Create][az-group-create] można utworzyć grupę zasobów o nazwie Moja *zasobów* w regionie *wschodnim* . Klaster AKS o nazwie *myAKSCluster* jest tworzony przy użyciu polecenia [AZ AKS Create][az-aks-create] . A *--Kubernetes-Version* of *1.13.5* służy do pokazywania sposobu aktualizowania puli węzłów w następnym kroku. Można określić dowolną [obsługiwaną wersję Kubernetes][supported-versions].

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.13.5
```

Utworzenie klastra trwa kilka minut.

Gdy klaster jest gotowy, użyj polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] , aby uzyskać poświadczenia klastra do użycia z programem `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Dodawanie puli węzłów

Klaster utworzony w poprzednim kroku ma pulę jednego węzła. Dodajmy drugą pulę węzłów za pomocą polecenia [AZ AKS Node Pool Add][az-aks-nodepool-add] . Poniższy przykład tworzy pulę węzłów o nazwie *mynodepool* , która uruchamia *3* węzły:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

Aby wyświetlić stan pul węzłów, użyj polecenia [AZ AKS Node Pool list][az-aks-nodepool-list] i określ grupę zasobów i nazwę klastra:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

Następujące przykładowe dane wyjściowe pokazują, że *mynodepool* został pomyślnie utworzony z trzema węzłami w puli węzłów. Gdy klaster AKS został utworzony w poprzednim kroku, utworzono domyślny *nodepool1* z liczbą węzłów *1*.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

> [!TIP]
> Jeśli podczas dodawania puli węzłów nie określono *OrchestratorVersion* ani *VmSize* , węzły są tworzone na podstawie ustawień domyślnych klastra AKS. W tym przykładzie Kubernetes w wersji *1.13.5* i rozmiarze węzła *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Uaktualnianie puli węzłów

Po utworzeniu klastra AKS w pierwszym kroku został określony element `--kubernetes-version` *1.13.5* . Uaktualnimy *mynodepool* do Kubernetes *1.13.7*. Użyj polecenia [AZ AKS Node Pool upgrade][az-aks-nodepool-upgrade] , aby uaktualnić pulę węzłów, jak pokazano w następującym przykładzie:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.7 \
    --no-wait
```

Ponownie utwórz listę stan pul węzłów za pomocą polecenia [AZ AKS Node Pool list][az-aks-nodepool-list] . Poniższy przykład pokazuje, że *mynodepool* jest w stanie *uaktualnienia* do *1.13.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.13.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Uaktualnienie węzłów do określonej wersji może potrwać kilka minut.

Najlepszym rozwiązaniem jest uaktualnienie wszystkich pul węzłów w klastrze AKS do tej samej wersji Kubernetes. Możliwość uaktualnienia poszczególnych pul węzłów umożliwia przeprowadzenie uaktualnienia stopniowego i zaplanowanie między pulami węzłów, aby zachować czas działania aplikacji.

## <a name="scale-a-node-pool"></a>Skalowanie puli węzłów

Gdy obciążenie aplikacji wymaga zmiany, może być konieczne skalowanie liczby węzłów w puli węzłów. Liczbę węzłów można skalować w górę lub w dół.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Aby skalować liczbę węzłów w puli węzłów, użyj polecenia [AZ AKS Node Pool Scale][az-aks-nodepool-scale] . Poniższy przykład skaluje liczbę węzłów w *mynodepool* do *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Ponownie utwórz listę stan pul węzłów za pomocą polecenia [AZ AKS Node Pool list][az-aks-nodepool-list] . Poniższy przykład pokazuje, że *mynodepool* jest w stanie *skalowania* z nową liczbą *5* węzłów:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.13.7                 100             Linux     Scaling              myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Ukończenie operacji skalowania może potrwać kilka minut.

## <a name="delete-a-node-pool"></a>Usuwanie puli węzłów

Jeśli pula nie jest już potrzebna, można ją usunąć i usunąć źródłowe węzły maszyn wirtualnych. Aby usunąć pulę węzłów, użyj polecenia [AZ AKS Node Pool Delete][az-aks-nodepool-delete] i określ nazwę puli węzłów. Poniższy przykład usuwa *mynoodepool* utworzone w poprzednich krokach:

> [!CAUTION]
> Brak opcji odzyskiwania dla utraty danych, które mogą wystąpić po usunięciu puli węzłów. Jeśli nie można zaplanować puli dla innych pul węzłów, te aplikacje są niedostępne. Upewnij się, że nie usuniesz puli węzłów, gdy aplikacje w użyciu nie mają kopii zapasowych danych ani nie mogą być uruchamiane na innych pulach węzłów w klastrze.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Następujące przykładowe dane wyjściowe z polecenia [AZ AKS Node Pool list][az-aks-nodepool-list] pokazują, że *mynodepool* jest w stanie *usuwania* :

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.13.7                 100             Linux     Deleting             myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Usunięcie węzłów i puli węzłów może potrwać kilka minut.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Określanie rozmiaru maszyny wirtualnej dla puli węzłów

W poprzednich przykładach w celu utworzenia puli węzłów dla węzłów utworzonych w klastrze użyto domyślnego rozmiaru maszyny wirtualnej. Bardziej typowy scenariusz polega na tworzeniu pul węzłów o różnych rozmiarach i możliwościach maszyn wirtualnych. Można na przykład utworzyć pulę węzłów zawierającą węzły z dużą ilością procesora lub pamięci albo pulę węzłów, która zapewnia obsługę procesora GPU. W następnym kroku należy użyć przystawek [i tolerowania](#schedule-pods-using-taints-and-tolerations) , aby poinformować usługę Kubernetes Scheduler, jak ograniczyć dostęp do zasobników, które mogą być uruchamiane w tych węzłach.

W poniższym przykładzie Utwórz pulę węzłów opartą na procesorze GPU, która używa rozmiaru maszyny wirtualnej *Standard_NC6* . Te maszyny wirtualne są obsługiwane przez kartę NVIDIA Tesla K80. Aby uzyskać informacje na temat dostępnych rozmiarów maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych z systemem Linux na platformie Azure][vm-sizes].

Utwórz pulę węzłów za pomocą polecenia [AZ AKS Node Pool Add][az-aks-nodepool-add] . Tym razem Określ nazwę *gpunodepool*i Użyj `--node-vm-size` parametru, aby określić rozmiar *Standard_NC6* :

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Następujące przykładowe dane wyjściowe z polecenia [AZ AKS Node Pool list][az-aks-nodepool-list] pokazują, że *gpunodepool* *tworzy* węzły o określonym *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.13.5                 100             Linux     Creating             myResourceGroup  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Pomyślne utworzenie *gpunodepool* może potrwać kilka minut.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Zaplanuj użycie zasobników z zmianami i tolerowaniem

W klastrze znajdują się teraz dwa pule węzłów — domyślna pula węzłów została początkowo utworzona i Pula węzłów oparta na procesorze GPU. Użyj [polecenia kubectl Pobierz węzły][kubectl-get] polecenie, aby wyświetlić węzły w klastrze. Następujące przykładowe dane wyjściowe pokazują jeden węzeł w każdej puli węzłów:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.5
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.5
```

Harmonogram Kubernetes może używać przyniesień i tolerowanych elementów w celu ograniczenia obciążeń, które mogą być uruchamiane w węzłach.

* Do  węzła jest stosowany obiekt, który wskazuje na ich zaplanowanie tylko określonych zasobników.
* **Tolerowana** jest następnie stosowana do elementu, który umożliwia im tolerowanie  kształtu węzła.

Aby uzyskać więcej informacji na temat korzystania z zaawansowanych funkcji usługi Kubernetes, zobacz [najlepsze rozwiązania dotyczące zaawansowanych funkcji usługi Scheduler w AKS][taints-tolerations]

W tym przykładzie Zastosuj przybarwienie do węzła opartego na procesorze GPU przy użyciu polecenia [Node polecenia kubectl][kubectl-taint] . Określ nazwę węzła opartego na procesorze GPU z danych wyjściowych poprzedniego `kubectl get nodes` polecenia. Ten obiekt jest stosowany jako *klucz: wartość* , a następnie opcja planowania. W poniższym przykładzie używa pary *SKU = GPU* i definiujemy w inny *sposób możliwości* noscheduler:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Poniższy podstawowy przykład manifestu YAML korzysta z tolerowania, aby umożliwić usłudze Kubernetes Scheduler uruchamianie NGINX na węźle opartym na procesorze GPU. Aby uzyskać bardziej odpowiedni, ale czasochłonny Przykładowo, aby uruchomić zadanie Tensorflow w odniesieniu do zestawu danych MNIST ręcznie, zobacz [Korzystanie z procesorów GPU na potrzeby obciążeń intensywnie korzystających z obliczeń na AKS][gpu-cluster].

Utwórz plik o nazwie `gpu-toleration.yaml` i skopiuj w poniższym przykładzie YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Zaplanuj przy użyciu `kubectl apply -f gpu-toleration.yaml` polecenia:

```console
kubectl apply -f gpu-toleration.yaml
```

Zaplanowanie i ściągnięcie obrazu NGINX może potrwać kilka sekund. Użyj polecenia [polecenia kubectl opisz pod][kubectl-describe] , aby wyświetlić stan pod. Następujące wąskie przykładowe dane wyjściowe pokazują, że jest stosowane tolerowanie *jednostki SKU = GPU: NoSchedule* . W sekcji Events Scheduler przypisano do węzła *AKS-gpunodepool-28993262-vmss000000* procesora GPU:

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Na węzłach w *gpunodepool*można zaplanować tylko te zasobniki, do których zastosowano ten przedział. Wszystkie inne na stronie zaplanowano w puli węzłów *nodepool1* . W przypadku tworzenia dodatkowych pul węzłów można użyć dodatkowych przydziałów i tolerowania, aby ograniczyć liczbę elementów, które można zaplanować w tych zasobach węzła.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Zarządzanie pulami węzłów przy użyciu szablonu Menedżer zasobów

W przypadku tworzenia i zarządzania zasobami przy użyciu szablonu Azure Resource Manager można zwykle zaktualizować ustawienia w szablonie i wdrożyć je ponownie w celu zaktualizowania zasobu. W przypadku pul węzłów w AKS nie można zaktualizować profilu początkowej puli węzłów po utworzeniu klastra AKS. To zachowanie oznacza, że nie można zaktualizować istniejącego szablonu Menedżer zasobów, wprowadzić zmiany w pulach węzłów i ponownie wdrożyć. Zamiast tego należy utworzyć oddzielny szablon Menedżer zasobów, który aktualizuje tylko pule agentów dla istniejącego klastra AKS.

Utwórz szablon, taki jak `aks-agentpools.json` i wklej następujący przykładowy manifest. Ten przykładowy szablon służy do konfigurowania następujących ustawień:

* Aktualizuje pulę agentów systemu *Linux* o nazwie *myagentpool* , aby uruchamiać trzy węzły.
* Ustawia węzły w puli węzłów do uruchomienia Kubernetes w wersji *1.13.5*.
* Definiuje rozmiar węzła jako *Standard_DS2_v2*.

W razie potrzeby należy edytować te wartości jako wymagające aktualizacji, dodania lub usunięcia pul węzłów:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of your existing AKS cluster."
      }
    },
    "location": {
      "type": "string",
      "metadata": {
        "description": "The location of your existing AKS cluster."
      }
    },
    "agentPoolName": {
      "type": "string",
      "defaultValue": "myagentpool",
      "metadata": {
        "description": "The name of the agent pool to create or update."
      }
    },
    "vnetSubnetId": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The Vnet subnet resource ID for your existing AKS cluster."
      }
    }
  },
  "variables": {
    "apiVersion": {
      "aks": "2019-04-01"
    },
    "agentPoolProfiles": {
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]"
    }
  },
  "resources": [
    {
      "apiVersion": "2019-04-01",
      "type": "Microsoft.ContainerService/managedClusters/agentPools",
      "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
      "location": "[parameters('location')]",
      "properties": {
            "maxPods": "[variables('agentPoolProfiles').maxPods]",
            "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
            "count": "[variables('agentPoolProfiles').agentCount]",
            "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
            "osType": "[variables('agentPoolProfiles').osType]",
            "storageProfile": "ManagedDisks",
      "type": "VirtualMachineScaleSets",
            "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
            "orchestratorVersion": "1.13.5"
      }
    }
  ]
}
```

Wdróż ten szablon przy użyciu polecenia [AZ Group Deployment Create][az-group-deployment-create] , jak pokazano w poniższym przykładzie. Zostanie wyświetlony monit o istniejącą nazwę i lokalizację klastra AKS:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Zaktualizowanie klastra AKS może potrwać kilka minut, w zależności od ustawień puli węzłów i operacji zdefiniowanych w szablonie Menedżer zasobów.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule opisano tworzenie klastra AKS zawierającego węzły oparte na procesorach GPU. Aby zmniejszyć niepotrzebny koszt, warto usunąć *gpunodepool*lub cały klaster AKS.

Aby usunąć pulę węzłów opartą na procesorze GPU, użyj polecenia [AZ AKS nodepool Delete][az-aks-nodepool-delete] , jak pokazano w poniższym przykładzie:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Aby usunąć klaster, użyj polecenia [AZ Group Delete][az-group-delete] , aby usunąć grupę zasobów AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia wielu pul węzłów w klastrze AKS i zarządzania nimi. Aby uzyskać więcej informacji na temat sterowania zestawami w puli węzłów, zobacz [najlepsze rozwiązania dotyczące zaawansowanych funkcji usługi Scheduler w AKS][operator-best-practices-advanced-scheduler].

Aby utworzyć i użyć pul węzłów kontenera systemu Windows Server, zobacz [Tworzenie kontenera systemu Windows Server w AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
