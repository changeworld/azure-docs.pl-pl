---
title: Korzystając z wielu pul węzłów w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak tworzyć i zarządzać nimi wiele pul węzłów klastra w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/17/2019
ms.author: iainfou
ms.openlocfilehash: 679d91da774b3e4d2c53c70cdc0abfd4da9c6953
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059636"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>W wersji zapoznawczej — tworzenie i zarządzanie nimi wiele pul węzłów klastra w usłudze Azure Kubernetes Service (AKS)

W konsoli usługi Azure Kubernetes Service (AKS) węzłów o tej samej konfiguracji są grupowane w *pule węzłów*. Te pule węzłów zawierają podstawowych maszyn wirtualnych, które uruchamiaj swoje aplikacje. Początkowa liczba węzłów i ich rozmiaru (SKU) są definiowane podczas tworzenia klastra usługi AKS, która tworzy *domyślnej puli węzeł*. Aby zapewnić obsługę aplikacji, które mają różne obliczenia lub wymagania związane z przechowywaniem, można utworzyć pule węzłów dodatkowe. Na przykład użyć te pule dodatkowego węzła o podanie procesorów GPU dla aplikacji intensywnie korzystających z obliczeń lub uzyskania dostępu do magazynu SSD o wysokiej wydajności.

W tym artykule przedstawiono sposób tworzenia i zarządzania nimi wiele pul węzłów w klastrze AKS. Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługi, uczestnictwo. Są one udostępniane do zbierania opinii i błędy z naszej społeczności. W wersji zapoznawczej te funkcje nie są przeznaczone do użytku produkcyjnego. Funkcje w wersji zapoznawczej objęte "starań" pomocy technicznej. Pomoc od zespołów pomocy technicznej usługi AKS jest dostępna w godzinach pracy i Pacyfik, część strefy czasowej (PST) tylko. Aby uzyskać więcej informacji zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej usługi AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz wiersza polecenia platformy Azure w wersji 2.0.61 lub później zainstalowane i skonfigurowane. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia w wersji zapoznawczej usługi aks

Polecenia interfejsu wiersza polecenia do tworzenia i obsługi wielu pul węzłów są dostępne w *podglądu usługi aks* rozszerzenie interfejsu wiersza polecenia. Zainstaluj *podglądu usługi aks* rozszerzenie interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [Dodaj rozszerzenie az] [ az-extension-add] polecenia, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Jeśli wcześniej zainstalowano *podglądu usługi aks* rozszerzenia, zainstaluj dostępne aktualizacje, przy użyciu `az extension update --name aks-preview` polecenia.

### <a name="register-multiple-node-pool-feature-provider"></a>Zarejestruj dostawcę funkcji w wielu puli węzeł

Aby utworzyć klaster usługi AKS, można użyć wielu pul węzłów, należy najpierw włączyć dwie flagi funkcji w ramach subskrypcji. Klastry w wielu węzłach puli Użyj zestawu skalowania maszyn wirtualnych (zestawu skalowania maszyn wirtualnych) do zarządzania wdrażaniem i konfiguracji węzłów rozwiązania Kubernetes. Zarejestruj *MultiAgentpoolPreview* i *VMSSPreview* przy użyciu flagi funkcji [az feature register] [ az-feature-register] polecenia, jak pokazano w Poniższy przykład:

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Każdy klaster AKS możesz utworzyć po pomyślnym zarejestrowaniu *MultiAgentpoolPreview* Użyj to środowisko klastra w wersji zapoznawczej. Aby kontynuować tworzenie klastrów regularnych, w pełni obsługiwane, nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji w środowisku produkcyjnym. Użyj oddzielnych testowym lub deweloperskim subskrypcji platformy Azure do testowania funkcji w wersji zapoznawczej.

Zajmuje kilka minut, zanim stan wyświetlany *zarejestrowanej*. Można sprawdzić stan rejestracji przy użyciu [lista funkcji az] [ az-feature-list] polecenia:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestracji *Microsoft.ContainerService* dostawcę zasobów przy użyciu [az provider register] [ az-provider-register] polecenia:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Ograniczenia

Podczas tworzenia i zarządzania klastrami usługi AKS, które obsługują wiele pul węzłów obowiązują następujące ograniczenia:

* Wiele pul węzłów są dostępne tylko dla klastrów utworzonych po pomyślnym zarejestrowaniu *MultiAgentpoolPreview* i *VMSSPreview* funkcje dla Twojej subskrypcji. Nie można dodać lub Zarządzaj pulami węzła z istniejącego klastra AKS utworzone przed te funkcje zostały pomyślnie zarejestrowane.
* Nie można usunąć puli pierwszy węzeł.
* Nie można używać dodatku routing aplikacji protokołu HTTP.
* Nie można już pule węzłów Dodawanie/aktualizowanie/usuwanie, podobnie jak w przypadku większości operacji przy użyciu istniejącego szablonu usługi Resource Manager. Zamiast tego [korzystanie z oddzielnych szablonu usługi Resource Manager](#manage-node-pools-using-a-resource-manager-template) do wprowadzania zmian w pule węzłów w klastrze AKS.
* Nie można użyć skalowanie klastra (obecnie dostępna w wersji zapoznawczej w usłudze AKS).

Chociaż ta funkcja jest dostępna w wersji zapoznawczej, następujące dodatkowe ograniczenia:

* Klaster AKS może mieć maksymalnie osiem pule węzłów.
* Klaster AKS może mieć maksymalnie 400 węzłów w tych pulach osiem węzłów.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Aby rozpocząć, Utwórz klaster AKS z pulą jeden węzeł. W poniższym przykładzie użyto [Tworzenie grupy az] [ az-group-create] polecenie, aby utworzyć grupę zasobów o nazwie *myResourceGroup* w *eastus* region. Klaster AKS, o nazwie *myAKSCluster* zostanie utworzony przy użyciu [tworzenie az aks] [ az-aks-create] polecenia. A *wersji rozwiązania kubernetes —* z *1.12.6* służy do pokazywania, jak zaktualizować puli węzeł następnego kroku. Można określić dowolną [obsługiwana wersja programu Kubernetes][supported-versions].

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
    --kubernetes-version 1.12.6
```

Utworzenie klastra trwa kilka minut.

Gdy klaster będzie gotowy, użyj [az aks get-credentials] [ az-aks-get-credentials] polecenie, aby uzyskać poświadczenia klastra do użytku z programem `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Dodaj pulę węzłów

Klaster utworzony w poprzednim kroku ma puli jeden węzeł. Dodajmy, drugi węzeł puli przy użyciu [Dodaj pulę węzłów aks az] [ az-aks-nodepool-add] polecenia. Poniższy przykład tworzy pulę węzłów o nazwie *mynodepool* , które jest uruchamiane *3* węzłów:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

Aby wyświetlić stan puli węzeł, należy użyć [az aks węzła puli lista] [ az-aks-nodepool-list] polecenia i podaj nazwę grupy i klaster zasobów:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

Następujące przykładowe dane wyjściowe pokazuje, że *mynodepool* została pomyślnie utworzona za pomocą trzech węzłów w puli węzłów. Po utworzeniu klastra AKS w poprzednim kroku, domyślny *nodepool1* została utworzona wraz z liczbą węzłów *1*.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

> [!TIP]
> Jeśli nie *OrchestratorVersion* lub *VmSize* jest określony, podczas dodawania węzła puli, węzłów są tworzone w oparciu o ustawienia domyślne dla klastra AKS. W tym przykładzie było wersję rozwiązania Kubernetes *1.12.6* i rozmiar węzłów *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Uaktualnij pulę węzłów

Podczas tworzenia klastra usługi AKS w pierwszym kroku `--kubernetes-version` z *1.12.6* została określona. Uaktualnij teraz *mynodepool* usłudze Kubernetes *1.12.7*. Użyj [polecenia az aks węzła puli upgrade] [ az-aks-nodepool-upgrade] polecenie, aby uaktualnić pulę węzłów, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.12.7 \
    --no-wait
```

Wyświetl stan puli węzeł ponownie, używając [az aks węzła puli lista] [ az-aks-nodepool-list] polecenia. Poniższy przykład pokazuje, że *mynodepool* znajduje się w *uaktualnianie* do stanu *1.12.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Trwa kilka minut, aby uaktualnić węzły do określonej wersji.

Najlepszym rozwiązaniem należy uaktualnić wszystkie pule węzłów w klastrze AKS do tej samej wersji platformy Kubernetes. Możliwość podniesienia poziomu pule węzłów poszczególnych umożliwia uaktualnienie stopniowe i Zaplanuj zasobników między pule węzłów, aby zachować czas działania aplikacji.

## <a name="scale-a-node-pool"></a>Skalowanie puli węzeł

Jak aplikacja obciążenia do potrzeb, konieczne może być skalowanie liczby węzłów w puli węzłów. Liczba węzłów można skalować w górę lub w dół.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Aby skalować liczbę węzłów w puli węzeł, należy użyć [az aks węzła puli skalowania] [ az-aks-nodepool-scale] polecenia. W poniższym przykładzie Skala liczby węzłów w *mynodepool* do *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Wyświetl stan puli węzeł ponownie, używając [az aks węzła puli lista] [ az-aks-nodepool-list] polecenia. Poniższy przykład pokazuje, że *mynodepool* znajduje się w *skalowanie* stanu wraz z liczbą nowych *5* węzłów:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Scaling              myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Trwa kilka minut na zakończenie operacji skalowania.

## <a name="delete-a-node-pool"></a>Usuń pulę węzłów

Jeśli nie potrzebujesz już pulę, można ją usunąć i usuwanie węzłów podrzędnych maszyny Wirtualnej. Aby usunąć pulę węzłów, użyj [usunąć pulę węzłów aks az] [ az-aks-nodepool-delete] polecenia i określ nazwę puli węzeł. Poniższy przykład usuwa *mynoodepool* utworzony w poprzednich krokach:

> [!CAUTION]
> Istnieją opcje odzyskiwania nie utraty danych, które mogą wystąpić podczas usuwania puli węzeł. Jeśli nie można zaplanować zasobników w innych pulach węzła, te aplikacje są niedostępne. Upewnij się, że nie usuwaj puli węzeł, gdy aplikacje w użyciu braku kopie zapasowe danych i możliwość uruchamiania na innych pule węzłów w klastrze.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Następujące przykładowe dane wyjściowe z [az aks węzła puli lista] [ az-aks-nodepool-list] polecenie pokazuje, że *mynodepool* znajduje się w *usuwanie* stanu:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Deleting             myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Trwa kilka minut, aby usunąć węzły i pulę węzłów.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Określ rozmiar maszyny Wirtualnej dla puli węzłów

W poprzednich przykładach, aby utworzyć pulę węzłów domyślny rozmiar maszyny Wirtualnej został użyty dla węzłów utworzone w klastrze. Bardziej typowy scenariusz jest dla Ciebie utworzyć pule węzłów z różnych rozmiarów maszyn wirtualnych i możliwości. Może na przykład utworzyć pulę węzłów, który zawiera węzły z dużą ilością pamięci lub procesora CPU lub pulę węzłów, który zapewnia obsługę procesora GPU. W następnym kroku zostanie [nasłonecznieniem i tolerations](#schedule-pods-using-taints-and-tolerations) informowanie o harmonogramu Kubernetes, jak ograniczyć dostęp do zasobników, które można uruchomić na tych węzłach.

W poniższym przykładzie, należy utworzyć pulę węzłów opartą na procesorze GPU, który używa *maszyna wirtualna Standard_NC6* rozmiar maszyny Wirtualnej. Te maszyny wirtualne są obsługiwane przez procesory GPU NVIDIA Tesla K80 karty. Aby uzyskać informacji na temat dostępnych rozmiarów maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych systemu Linux na platformie Azure][vm-sizes].

Tworzenie puli węzłów przy użyciu [Dodaj pulę węzłów aks az] [ az-aks-nodepool-add] ponownie polecenie. Tym razem, określ nazwę *gpunodepool*i użyj `--node-vm-size` parametru do określenia *maszyna wirtualna Standard_NC6* rozmiar:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Następujące przykładowe dane wyjściowe z [az aks węzła puli lista] [ az-aks-nodepool-list] polecenie pokazuje, że *gpunodepool* jest *tworzenie* węzłów za pomocą określony *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.12.6                 100             Linux     Creating             myResourceGroupPools  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Zajmuje kilka minut *gpunodepool* ma zostać pomyślnie utworzony.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Zaplanuj zasobników przy użyciu nasłonecznieniem i tolerations

Masz teraz dwie pule węzłów w klastrze — domyślną pulę węzłów początkowo utworzona oraz pulę węzłów opartą na procesorze GPU. Użyj [kubectl get-węzły] [ kubectl-get] polecenie, aby wyświetlić węzły w klastrze. Następujące przykładowe dane wyjściowe zawiera jeden węzeł w każdej puli węzła:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.12.6
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.12.6
```

Harmonogram Kubernetes nasłonecznieniem i umożliwia tolerations ograniczyć, jakie obciążenia mogą być uruchamiane w węzłach.

* A **skażenia** jest stosowany do węzła, który wskazuje na nich można zaplanować tylko określonych zasobników.
* A **toleration** jest następnie stosowane do zasobników, które umożliwia im *tolerować* zmiany barwy węzła.

Więcej informacji na temat sposobu użycia zaawansowanych funkcji rozwiązania Kubernetes, zaplanowane, zobacz [najlepsze rozwiązania dotyczące harmonogramu zaawansowanych funkcji w usłudze AKS][taints-tolerations]

W tym przykładzie dotyczą zmiany barwy przy użyciu opartej na procesorze GPU węzła [węzła zmiany barwy kubectl] [ kubectl-taint] polecenia. Określ nazwę węzła usługi opartej na procesorze GPU z danych wyjściowych poprzedniego `kubectl get nodes` polecenia. Zastosowano zmiany barwy jako *klucz: wartość* a następnie opcję planowania. W poniższym przykładzie użyto *jednostki sku = gpu* Sparuj i definiuje zasobników w przeciwnym razie ma *NoSchedule* możliwości:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Następujące manifest YAML przykład podstawowy używa toleration umożliwienie harmonogram Kubernetes, aby uruchomić zasobnik NGINX na węźle opartą na procesorze GPU. Na przykład bardziej odpowiednie, ale czasochłonnymi do uruchomienia zadania Tensorflow względem zestawu danych mnist ręcznie ZAPISANYCH, zobacz [użycie procesorów GPU w przypadku obciążeń intensywnie korzystających z obliczeń w usłudze AKS][gpu-cluster].

Utwórz plik o nazwie `gpu-toleration.yaml` i skopiuj poniższy przykład kodu YAML:

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

Zaplanuj zasobnik przy użyciu `kubectl apply -f gpu-toleration.yaml` polecenia:

```console
kubectl apply -f gpu-toleration.yaml
```

Zajmuje kilka sekund, aby zaplanować zasobnik i ściąganie obrazu serwera NGINX. Użyj [kubectl opisują zasobnika] [ kubectl-describe] polecenie, aby wyświetlić stan zasobników. Dane wyjściowe poniższego, skróconego przykładu *jednostki sku = gpu:NoSchedule* toleration jest stosowany. W sekcji zdarzenia harmonogram został przypisany zasobnika do *aks-gpunodepool-28993262-vmss000000* opartą na procesorze GPU węzła:

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

Można zaplanować tylko zasobników, które mają tej zmiany barwy zastosowane w węzłach *gpunodepool*. Inne zasobnika może być zaplanowane w *nodepool1* pulę węzłów. Jeśli tworzysz pule dodatkowych węzłów, można użyć dodatkowych nasłonecznieniem i tolerations, aby ograniczyć zasobników, jakie można zaplanować na te zasoby węzła.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Zarządzanie pulami węzła przy użyciu szablonu usługi Resource Manager

Gdy używasz szablonu usługi Azure Resource Manager do tworzenia i zarządzanych zasobów, zazwyczaj można zaktualizować ustawień w szablonie i ponowne wdrażanie na aktualizację zasobu. Dzięki pulom węzła w usłudze AKS nie można zaktualizować profilu puli węzeł początkowy, po utworzeniu klastra AKS. To zachowanie oznacza, że nie można zaktualizować istniejący szablon usługi Resource Manager, wprowadzić zmiany w pule węzłów i ponownego wdrażania. Zamiast tego należy utworzyć oddzielne szablonu usługi Resource Manager, która aktualizuje tylko pule agentów do istniejącego klastra usługi AKS.

Tworzenie szablonu, takich jak `aks-agentpools.json` i wklej następujące manifest przykładu. Ten przykładowy szablon konfiguruje następujące ustawienia:

* Aktualizacje *Linux* puli agenta o nazwie *myagentpool* do uruchomienia trzy węzły.
* Zestawy węzłów w puli węzłów, aby uruchomić wersję rozwiązania Kubernetes *1.12.8*.
* Określa rozmiar węzła jako *Standard_DS2_v2*.

Edytuj te wartości, jak należy zaktualizować, dodawanie lub usuwanie pule węzłów stosownie do potrzeb:

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
            "orchestratorVersion": "1.12.8"
      }
    }
  ]
}
```

Wdrażanie przy użyciu tego szablonu [Utwórz wdrożenie grupy az] [ az-group-deployment-create] polecenia, jak pokazano w poniższym przykładzie. Zostanie wyświetlony monit o istniejącej nazwy klastra AKS i lokalizacji:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Może upłynąć kilka minut, aby zaktualizować klastra usługi AKS w taki sposób, w zależności od ustawień puli węzeł i operacje, które są zdefiniowane w szablonie usługi Resource Manager.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule utworzono klaster AKS, która zawiera węzły opartą na procesorze GPU. Aby zmniejszyć koszt niepotrzebne, można usunąć *gpunodepool*, lub całego klastra AKS.

Aby usunąć pulę węzłów opartą na procesorze GPU, użyj [Usuń az aks nodepool] [ az-aks-nodepool-delete] polecenia, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Aby usunąć klaster, użyj [usunięcie grupy az] [ az-group-delete] polecenie, aby usunąć grupę zasobów usługi AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób tworzenia i zarządzania nimi wiele pul węzłów w klastrze AKS. Aby uzyskać więcej informacji o sposobie kontrolowania zasobników w pule węzłów, zobacz [najlepsze rozwiązania dotyczące harmonogramu zaawansowanych funkcji w usłudze AKS][operator-best-practices-advanced-scheduler].

Aby utworzyć pule węzłów kontenerów systemu Windows Server, zobacz [kontener systemu Windows Server w usłudze AKS][aks-windows].

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
