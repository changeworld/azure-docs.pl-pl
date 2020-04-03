---
title: Używanie wielu pul węzłów w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak tworzyć wiele pul węzłów dla klastra w usłudze Azure Kubernetes (AKS) i zarządzać nimi.
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 87f066ed17e5274439082956803d269bdd5853f5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616519"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Tworzenie wielu pul węzłów dla klastra w usłudze Azure Kubernetes (AKS) i zarządzanie nimi

W usłudze Azure Kubernetes (AKS) węzły tej samej konfiguracji są grupowane w *pule węzłów.* Te pule węzłów zawierają podstawowe maszyny wirtualne, które uruchamiają aplikacje. Początkowa liczba węzłów i ich rozmiar (SKU) jest definiowana podczas tworzenia klastra AKS, który tworzy *domyślną pulę węzłów*. Aby obsługiwać aplikacje, które mają różne wymagania obliczeniowe lub magazynowe, można utworzyć dodatkowe pule węzłów. Na przykład użyj tych dodatkowych pul węzłów, aby zapewnić procesory GPU dla aplikacji intensywnie korzystających z obliczeń lub dostęp do magazynu dysków SSD o wysokiej wydajności.

> [!NOTE]
> Ta funkcja umożliwia większą kontrolę nad tym, jak tworzyć i zarządzać wieloma pulami węzłów. W rezultacie do tworzenia/aktualizowania/usuwania wymagane są oddzielne polecenia. Wcześniej operacje klastra za pośrednictwem `az aks create` zarządzanegogównajadu `az aks update` interfejsu API i były jedyną opcją, aby zmienić płaszczyznę sterowania i puli pojedynczego węzła. Ta funkcja udostępnia oddzielny zestaw operacji dla pul agenta za `az aks nodepool` pośrednictwem interfejsu API agentpool i wymaga użycia zestawu poleceń do wykonywania operacji w puli poszczególnych węzłów.

W tym artykule pokazano, jak tworzyć i zarządzać wieloma pulami węzłów w klastrze AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz zainstalowanego i skonfigurowany interfejsu wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów AKS i zarządzania nimi obowiązują następujące ograniczenia:

* Zobacz [Przydziały, ograniczenia rozmiaru maszyny wirtualnej i dostępność regionu w usłudze Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Nie można usunąć puli węzłów systemowych, domyślnie pierwszej puli węzłów.
* Klaster AKS musi używać standardowego modułu równoważenia obciążenia jednostki SKU do używania wielu pul węzłów, funkcja nie jest obsługiwana w przypadku podstawowych modułów równoważenia obciążenia jednostki SKU.
* Klaster AKS musi używać zestawów skalowania maszyny wirtualnej dla węzłów.
* Nazwa puli węzłów może zawierać tylko małe litery alfanumeryczne i musi zaczynać się od małych liter. W przypadku pul węzłów systemu Linux długość musi wynosić od 1 do 12 znaków, w przypadku puli węzłów systemu Windows długość musi wynosić od 1 do 6 znaków.
* Wszystkie pule węzłów muszą znajdować się w tej samej sieci wirtualnej.
* Podczas tworzenia wielu pul węzłów w czasie tworzenia klastra wszystkie wersje kubernetes używane przez pule węzłów muszą być zgodne z wersją ustawioną dla płaszczyzny sterowania. Można to zaktualizować po zainicjowaniu obsługi administracyjnej klastra przy użyciu operacji puli na węzeł.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Aby rozpocząć, należy utworzyć klaster AKS z pulą jednego węzła. W poniższym przykładzie użyto polecenia [create grupy az][az-group-create] do utworzenia grupy zasobów o nazwie *myResourceGroup* w regionie *eastus.* Klaster AKS o nazwie *myAKSCluster* jest następnie tworzony za pomocą polecenia [az aks create.][az-aks-create] *Wersja --kubernetes* *1.15.7* służy do pokazywalenia sposobu aktualizowania puli węzłów w następnym kroku. Można określić dowolną [obsługiwani wersję kubernetes][supported-versions].

> [!NOTE]
> *Podstawowa jednostka* SKU modułu równoważenia obciążenia nie jest **obsługiwana** podczas korzystania z wielu pul węzłów. Domyślnie klastry AKS są tworzone przy pomocą *standardowej* jednostki SKU równoważenia obciążenia z interfejsu wiersza polecenia platformy Azure i witryny Azure portal.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

Utworzenie klastra trwa kilka minut.

> [!NOTE]
> Aby upewnić się, że klaster działa niezawodnie, należy uruchomić co najmniej 2 (dwa) węzły w domyślnej puli węzłów, ponieważ podstawowe usługi systemowe są uruchomione w tej puli węzłów.

Gdy klaster jest gotowy, użyj polecenia [az aks get-credentials,][az-aks-get-credentials] `kubectl`aby uzyskać poświadczenia klastra do użycia z:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Dodawanie puli węzłów

Klaster utworzony w poprzednim kroku ma pulę pojedynczego węzła. Dodajmy drugą pulę węzłów za pomocą polecenia [az aks nodepool add.][az-aks-nodepool-add] Poniższy przykład tworzy pulę węzłów o nazwie *mynodepool,* która uruchamia *3* węzły:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> Nazwa puli węzłów musi zaczynać się od małych liter i może zawierać tylko znaki alfanumeryczne. W przypadku pul węzłów systemu Linux długość musi wynosić od 1 do 12 znaków, w przypadku puli węzłów systemu Windows długość musi wynosić od 1 do 6 znaków.

Aby wyświetlić stan pul węzłów, użyj polecenia [listy puli węzłów az aks][az-aks-nodepool-list] i określ nazwę grupy zasobów i klastra:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Poniższe przykładowe dane wyjściowe pokazują, że *mynodepool* został pomyślnie utworzony z trzech węzłów w puli węzłów. Podczas tworzenia klastra AKS w poprzednim kroku utworzono domyślny *nodepool1* z liczbą węzłów *2*.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Jeśli podczas dodawania puli węzłów nie określono rozmiaru *VmSize,* domyślny rozmiar jest *Standard_DS2_v3* dla pul węzłów systemu Windows i *Standard_DS2_v2* dla pul węzłów systemu Linux. Jeśli nie *OrchestratorVersion* jest określony, domyślnie do tej samej wersji co płaszczyzna sterowania.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Dodawanie puli węzłów z unikatową podsiecią (wersja zapoznawcza)

Obciążenie może wymagać podziału węzłów klastra na oddzielne pule dla izolacji logicznej. Ta izolacja może być obsługiwana za pomocą oddzielnych podsieci dedykowanych do każdej puli węzłów w klastrze. Może to rozwiązać wymagania, takie jak posiadanie nieciągłej przestrzeni adresowej sieci wirtualnej do podziału między pulami węzłów.

#### <a name="limitations"></a>Ograniczenia

* Wszystkie podsieci przypisane do węzłów muszą należeć do tej samej sieci wirtualnej.
* Zasobniki systemu muszą mieć dostęp do wszystkich węzłów w klastrze, aby zapewnić krytyczne funkcje, takie jak rozpoznawanie DNS za pośrednictwem coreDNS.
* Przypisanie unikatowej podsieci na pulę węzłów jest ograniczone do usługi Azure CNI podczas podglądu.
* Korzystanie z zasad sieciowych z unikatową pulą podsieci na węzeł nie jest obsługiwane podczas podglądu.

Aby utworzyć pulę węzłów z dedykowaną podsiecią, przekaż identyfikator zasobu podsieci jako dodatkowy parametr podczas tworzenia puli węzłów.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Uaktualnianie puli węzłów

> [!NOTE]
> Operacje uaktualniania i skalowania w puli klastra lub węzła nie mogą wystąpić jednocześnie, jeśli zostanie zwrócona próba wystąpienia błędu. Zamiast tego każdy typ operacji musi zakończyć się na zasób docelowy przed następnym żądaniem w tym samym zasobie. Przeczytaj więcej na ten temat w naszym [przewodniku rozwiązywania problemów.](https://aka.ms/aks-pending-upgrade)

Gdy klaster usługi AKS został pierwotnie `--kubernetes-version` utworzony w pierwszym kroku, określono *1.15.7.* W ten sposób ustawiono wersję kubernetes dla płaszczyzny sterowania i domyślnej puli węzłów. Polecenia w tej sekcji wyjaśniają, jak uaktualnić pulę pojedynczego węzła określonego.

Relacja między uaktualnieniem wersji kubernetes płaszczyzny sterowania a pulą węzłów jest wyjaśniona w [poniższej sekcji](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> Wersja obrazu systemu operacyjnego puli węzłów jest powiązana z wersją klastra w ubraciach kubernetes. Uaktualnienia obrazu systemu operacyjnego będą ujmowane tylko po uaktualnieniu klastra.

Ponieważ istnieją dwa węzły pul w tym przykładzie, musimy użyć [az aks nodepool uaktualnienia][az-aks-nodepool-upgrade] do uaktualnienia puli węzłów. Uaktualnijmy *mynodepool* do Kubernetes *1.15.7*. Użyj polecenia [az aks nodepool upgrade,][az-aks-nodepool-upgrade] aby uaktualnić pulę węzłów, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

Ponownie wyświetl stan pul węzłów za pomocą polecenia [az aks node pool list.][az-aks-nodepool-list] Poniższy przykład pokazuje, że *mynodepool* jest w stanie *uaktualniania* do *1.15.7:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Uaktualnienie węzłów do określonej wersji zajmuje kilka minut.

Najlepszym rozwiązaniem jest uaktualnienie wszystkich pul węzłów w klastrze AKS do tej samej wersji usługi Kubernetes. Domyślnym zachowaniem `az aks upgrade` jest uaktualnienie wszystkich pul węzłów wraz z płaszczyzną kontrolną, aby osiągnąć to wyrównanie. Możliwość uaktualniania poszczególnych pul węzłów umożliwia wykonywanie uaktualniania stopniowego i planowania zasobników między pulami węzłów w celu utrzymania czasu pracy aplikacji w ramach wyżej wymienionych ograniczeń.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Uaktualnianie płaszczyzny sterowania klastra z wieloma pulami węzłów

> [!NOTE]
> Kubernetes używa standardowego schematu przechowywania [wersji semantycznych.](https://semver.org/) Numer wersji jest wyrażony jako *x.y.z*, gdzie *x* jest wersją główną, *y* jest wersją pomocniczą, a *z* jest wersją patcha. Na przykład w wersji *1.12.6*, 1 jest wersja główna, 12 jest wersja pomocnicza, a 6 jest wersja poprawki. Wersja Kubernetes płaszczyzny sterowania i początkowej puli węzłów są ustawiane podczas tworzenia klastra. Wszystkie dodatkowe pule węzłów mają ich wersji Kubernetes zestaw po dodaniu do klastra. Wersje kubernetes mogą się różnić między pulami węzłów, a także między pulą węzłów a płaszczyzną sterowania.

Klaster AKS ma dwa obiekty zasobów klastra z skojarzonymi wersjami usługi Kubernetes.

1. Wersja kubernetes płaszczyzny sterowania klastra.
2. Pula węzłów z wersją kubernetes.

Płaszczyzna sterowania jest mapowana na jedną lub wiele pul węzłów. Zachowanie operacji uaktualniania zależy od tego, które polecenie interfejsu wiersza polecenia platformy Azure jest używane.

Modernizacja płaszczyzny sterowania AKS wymaga użycia `az aks upgrade`. To polecenie uaktualnia wersję płaszczyzny sterowania i wszystkie pule węzłów w klastrze.

Wydanie `az aks upgrade` polecenia z `--control-plane-only` flagą uaktualnia tylko płaszczyznę sterowania klastra. Żadna z pul skojarzonych węzłów w klastrze nie została zmieniona.

Uaktualnianie poszczególnych pul węzłów wymaga użycia programu `az aks nodepool upgrade`. To polecenie uaktualnia tylko pulę węzłów docelowych z określoną wersją programu Kubernetes

### <a name="validation-rules-for-upgrades"></a>Reguły sprawdzania poprawności uaktualnień

Prawidłowe uaktualnienia kubernetes dla płaszczyzny sterowania klastra i pul węzłów są sprawdzane przez następujące zestawy reguł.

* Reguły dla prawidłowych wersji do uaktualniania pul węzłów:
   * Wersja puli węzłów musi mieć taką samą wersję *główną* jak płaszczyzna kontrolna.
   * Wersja *pomocnicza* puli węzłów musi mieszczeć się w dwóch *wersjach pomocniczych* wersji płaszczyzny sterowania.
   * Wersja puli węzłów nie może `major.minor.patch` być większa niż wersja formantu.

* Zasady przesyłania operacji uaktualniania:
   * Nie można obniżyć płaszczyzny sterowania lub wersji kubernetes puli węzłów.
   * Jeśli wersja kubernetes puli węzłów nie jest określona, zachowanie zależy od używanego klienta. Deklaracja w szablonach Menedżera zasobów powraca do istniejącej wersji zdefiniowanej dla puli węzłów, jeśli jest używana, jeśli nie jest ustawiona wersja płaszczyzny sterowania jest używana do powrotu.
   * Można uaktualnić lub skalować płaszczyznę kontrolną lub pulę węzłów w danym czasie, nie można przesłać wielu operacji na jednej płaszczyźnie kontrolnej lub zasobu puli węzłów jednocześnie.

## <a name="scale-a-node-pool-manually"></a>Ręczne skalowanie puli węzłów

W miarę jak zapotrzebowanie na obciążenie aplikacji wymaga zmiany, może być konieczne skalowanie liczby węzłów w puli węzłów. Liczbę węzłów można skalować w górę lub w dół.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Aby skalować liczbę węzłów w puli węzłów, użyj polecenia [skali puli węzłów az aks.][az-aks-nodepool-scale] Poniższy przykład skaluje liczbę węzłów w *mynodepool* do *5:*

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Ponownie wyświetl stan pul węzłów za pomocą polecenia [az aks node pool list.][az-aks-nodepool-list] Poniższy przykład pokazuje, że *mynodepool* jest w stanie *skalowania* z nową liczbą *5* węzłów:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Trwa kilka minut, aby zakończyć operację skalowania.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Automatyczne skalowanie określonej puli węzłów przez włączenie skalowania automatycznego klastra

Usługa AKS oferuje oddzielną funkcję automatycznego skalowania pul węzłów za pomocą funkcji zwanej [skalowaniem automatycznym klastra.](cluster-autoscaler.md) Ta funkcja może być włączona na pulę węzłów z unikatową minimalną i maksymalną liczbą skali na pulę węzłów. Dowiedz się, jak [używać skalowania automatycznego klastra na pulę węzłów](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Usuwanie puli węzłów

Jeśli nie potrzebujesz już puli, można ją usunąć i usunąć podstawowe węzły maszyny Wirtualnej. Aby usunąć pulę węzłów, użyj polecenia [usuwania puli węzłów az aks][az-aks-nodepool-delete] i określ nazwę puli węzłów. Poniższy przykład usuwa *mynoodepool* utworzony w poprzednich krokach:

> [!CAUTION]
> Nie ma żadnych opcji odzyskiwania dla utraty danych, które mogą wystąpić po usunięciu puli węzłów. Jeśli zasobników nie można zaplanować w innych pulach węzłów, te aplikacje są niedostępne. Upewnij się, że nie usuniesz puli węzłów, gdy aplikacje w użyciu nie mają kopii zapasowych danych lub możliwość uruchamiania w innych pulach węzłów w klastrze.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Poniższy przykład danych wyjściowych z [az aks node pool list][az-aks-nodepool-list] polecenia pokazuje, że *mynodepool* jest w stanie *usuwania:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Usunięcie węzłów i puli węzłów zajmuje kilka minut.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Określanie rozmiaru maszyny Wirtualnej dla puli węzłów

W poprzednich przykładach tworzenia puli węzłów dla węzłów użyto domyślnego rozmiaru maszyny Wirtualnej dla węzłów utworzonych w klastrze. Bardziej typowym scenariuszem jest tworzenie pul węzłów o różnych rozmiarach i możliwościach maszyn wirtualnych. Na przykład można utworzyć pulę węzłów, która zawiera węzły z dużymi ilościami procesora CPU lub pamięci lub puli węzłów, która zapewnia obsługę procesora GPU. W następnym kroku [należy użyć taints i tolerancji,](#schedule-pods-using-taints-and-tolerations) aby poinformować harmonogram Kubernetes, jak ograniczyć dostęp do zasobników, które można uruchomić w tych węzłach.

W poniższym przykładzie utwórz pulę węzłów opartych na procesorze GPU, która używa *Standard_NC6* rozmiaru maszyny Wirtualnej. Te maszyny wirtualne są zasilane przez kartę NVIDIA Tesla K80. Aby uzyskać informacje na temat dostępnych rozmiarów maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych systemu Linux na platformie Azure][vm-sizes].

Utwórz ponownie pulę węzłów przy użyciu [polecenia dodaj węzeł az aks.][az-aks-nodepool-add] Tym razem określ nazwę *gpunodepool* `--node-vm-size` i użyj parametru, aby określić *rozmiar Standard_NC6:*

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Poniższy przykładowy wynik z polecenia [az aks node pool list][az-aks-nodepool-list] pokazuje, że *gpunodepool* jest *Tworzenie* węzłów o określonej *VmSize:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Trwa kilka minut dla *gpunodepool,* które mają zostać pomyślnie utworzone.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Zaplanuj strąki za pomocą skaz i tolerancji

Masz teraz dwie pule węzłów w klastrze — domyślna pula węzłów została pierwotnie utworzona i pula węzłów oparta na procesorze GPU. Użyj [polecenia kubectl get nodes,][kubectl-get] aby wyświetlić węzły w klastrze. Następujące przykładowe dane wyjściowe pokazuje węzły:

```console
kubectl get nodes
```

```output
NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

Harmonogram Kubernetes można używać taints i tolerancji, aby ograniczyć, jakie obciążenia można uruchomić w węzłach.

* **Skazy** jest stosowany do węzła, który wskazuje tylko określone zasobników mogą być zaplanowane na nich.
* **Tolerancja** jest następnie stosowana do zasobnika, który pozwala im *tolerować skazy* węzła.

Aby uzyskać więcej informacji na temat korzystania z zaawansowanych zaplanowanych funkcji kubernetes, zobacz [Najważniejsze wskazówki dotyczące zaawansowanych funkcji harmonogramu w ustroju AKS][taints-tolerations]

W tym przykładzie zastosuj skazę do węzła opartego na procesorze GPU za pomocą polecenia --node-taints. Określ nazwę węzła opartego na procesorze `kubectl get nodes` GPU z danych wyjściowych poprzedniego polecenia. Skażenie jest stosowane jako para *klucz=wartość,* a następnie opcja planowania. W poniższym przykładzie użyto pary *sku=gpu* i definiuje zasobników, w przeciwnym razie mają zdolność *NoSchedule:*

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Poniższy podstawowy przykład yaml manifest używa tolerancji, aby umożliwić harmonogramu Kubernetes do uruchomienia zasobnika NGINX w węźle opartym na procesorze GPU. Aby uzyskać bardziej odpowiedni, ale czasochłonny przykład uruchamiania zadania Tensorflow względem zestawu danych MNIST, zobacz [Używanie procesorów GPU dla obciążeń intensywnie korzystających z obliczeń w udziale AKS][gpu-cluster].

Utwórz plik `gpu-toleration.yaml` o nazwie i skopiuj w poniższym przykładzie YAML:

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

Zaplanuj `kubectl apply -f gpu-toleration.yaml` zasobnik za pomocą polecenia:

```console
kubectl apply -f gpu-toleration.yaml
```

To trwa kilka sekund, aby zaplanować zasobnika i wyciągnąć obraz NGINX. Użyj [polecenia kubectl describe pod,][kubectl-describe] aby wyświetlić stan zasobnika. Następujące skondensowane przykładowe dane wyjściowe pokazuje *sku = gpu:NoSchedule* tolerancji jest stosowana. W sekcji zdarzenia harmonogram przypisał zasobnik do *węzła opartego na procesorze gpu aks-gpunodepool-28993262-vmss0000000:*

```console
kubectl describe pod mypod
```

```output
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

Tylko zasobników, które mają tę tolerancję stosowane mogą być zaplanowane w węzłach w *gpunodepool*. Każdy inny zasobnik zostanie zaplanowany w puli węzłów *nodepool1.* Jeśli utworzysz dodatkowe pule węzłów, można użyć dodatkowych skazy i tolerancji, aby ograniczyć, jakie zasobniki można zaplanować w tych zasobach węzła.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Określanie skazy, etykiety lub znacznika dla puli węzłów

Podczas tworzenia puli węzłów można dodawać skazy, etykiety lub znaczniki do tej puli węzłów. Po dodaniu skazy, etykiety lub znacznika wszystkie węzły w tej puli węzłów również uzyskać ten taint, etykiety lub tagu.

Aby utworzyć pulę węzłów z skazy, użyj [az aks nodepool add][az-aks-nodepool-add]. Określ nazwę *taintnp* `--node-taints` i użyj parametru, aby określić *sku=gpu:NoSchedule* dla skażenia.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

Poniższy przykładowy wynik z polecenia [az aks nodepool][az-aks-nodepool-list] list pokazuje, że *taintnp* jest *Tworzenie* węzłów z określonym *nodeTaints:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  {
      "sku": "gpu:NoSchedule"
    },
    ...
  },
 ...
]
```

Informacje o skazy są widoczne w umotywanie do obsługi reguł planowania dla węzłów.

Można również dodać etykiety do puli węzłów podczas tworzenia puli węzłów. Etykiety ustawione w puli węzłów są dodawane do każdego węzła w puli węzłów. Te [etykiety są widoczne w umięsienia][kubernetes-labels] do obsługi reguł planowania dla węzłów.

Aby utworzyć pulę węzłów z etykietą, użyj [az aks nodepool add][az-aks-nodepool-add]. Określ nazwę *labelnp* `--labels` i użyj parametru, aby określić *dept=IT* i *costcenter=9999* dla etykiet.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Etykieta może być ustawiona tylko dla pul węzłów podczas tworzenia puli węzłów. Etykiety muszą być również parą klucz/wartość i mieć [prawidłową składnię][kubernetes-label-syntax].

Poniższy przykładowy wynik z [az aks nodepool polecenie][az-aks-nodepool-list] pokazuje, że *labelnp* jest *Tworzenie* węzłów z określonym *nodeLabels:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

Tag platformy Azure można zastosować do pul węzłów w klastrze AKS. Tagi stosowane do puli węzłów są stosowane do każdego węzła w puli węzłów i są utrwalone za pomocą uaktualnień. Znaczniki są również stosowane do nowych węzłów dodanych do puli węzłów podczas operacji skalowania w poziomie. Dodanie tagu może pomóc w zadaniach, takich jak śledzenie zasad lub szacowanie kosztów.

Utwórz pulę węzłów przy użyciu [az aks nodepool add][az-aks-nodepool-add]. Określ nazwę *tagnodepool* `--tag` i użyj parametru, aby określić *dept=IT* i *costcenter=9999* dla tagów.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Można również użyć `--tags` tego parametru podczas używania polecenia [aktualizacji az aks nodepool,][az-aks-nodepool-update] a także podczas tworzenia klastra. Podczas tworzenia klastra `--tags` parametr stosuje znacznik do początkowej puli węzłów utworzonej za pomocą klastra. Wszystkie nazwy tagów muszą być zgodne z ograniczeniami w [użyciu tagów do organizowania zasobów platformy Azure][tag-limitation]. Aktualizowanie puli węzłów `--tags` za pomocą parametru aktualizuje wszystkie istniejące wartości tagów i dołącza wszystkie nowe znaczniki. Na przykład, jeśli pula węzłów miała *dept=IT* i *costcenter=9999* dla tagów i zaktualizowałeś ją za pomocą *team=dev* i *costcenter=111* dla tagów, nodepool miałby *dept=IT*, *costcenter=111*i *team=dev* dla tagów.

Poniższy przykładowy wynik z polecenia [az aks nodepool list][az-aks-nodepool-list] pokazuje, że *tagnodepool* jest *Tworzenie* węzłów z określonym *tagiem:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Zarządzanie pulami węzłów przy użyciu szablonu Menedżera zasobów

Korzystając z szablonu usługi Azure Resource Manager do tworzenia zasobów i zarządzanych zasobów, zazwyczaj można zaktualizować ustawienia w szablonie i ponownie wdrożyć, aby zaktualizować zasób. W pulach węzłów w uzywniu usługi AKS nie można zaktualizować początkowego profilu puli węzłów po utworzeniu klastra AKS. To zachowanie oznacza, że nie można zaktualizować istniejącego szablonu Menedżera zasobów, wprowadzić zmiany w pulach węzłów i ponownie wdrożyć. Zamiast tego należy utworzyć oddzielny szablon Menedżera zasobów, który aktualizuje tylko pule węzłów dla istniejącego klastra AKS.

Utwórz szablon, `aks-agentpools.json` taki jak i wklej następujący przykładowy manifest. W tym przykładowym szablonie skonfigurowano następujące ustawienia:

* Aktualizuje pulę węzłów *systemu Linux* o nazwie *myagentpool,* aby uruchomić trzy węzły.
* Ustawia węzły w puli węzłów do uruchamiania aplikacji Kubernetes w wersji *1.15.7*.
* Definiuje rozmiar węzła jako *Standard_DS2_v2*.

W razie potrzeby edytuj te wartości w celu zaktualizowania, dodania lub usunięcia pul węzłów:

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
            "aks": "2020-01-01"
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
            "apiVersion": "2020-01-01",
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
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Wdrożyć ten szablon za pomocą polecenia [tworzenia wdrożenia grupy az,][az-group-deployment-create] jak pokazano w poniższym przykładzie. Zostanie wyświetlony monit o podanie istniejącej nazwy i lokalizacji klastra AKS:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Tag można dodać do puli węzłów, dodając właściwość *tagu* w szablonie, jak pokazano w poniższym przykładzie.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Aktualizacja klastra usługi AKS może potrwać kilka minut w zależności od ustawień puli węzłów i operacji zdefiniowanych w szablonie Menedżera zasobów.

## <a name="assign-a-public-ip-per-node-for-a-node-pool-preview"></a>Przypisywanie publicznego adresu IP na węzeł dla puli węzłów (wersja zapoznawcza)

> [!WARNING]
> Podczas podglądu przypisywania publicznego adresu IP na węzeł nie można go używać z *jednostką SKU standardowego modułu równoważenia obciążenia w utoka* z powodu możliwych reguł modułu równoważenia obciążenia, które są sprzeczne z inicjowania obsługi administracyjnej maszyny Wirtualnej. W wyniku tego ograniczenia pule agentów systemu Windows nie są obsługiwane za pomocą tej funkcji w wersji zapoznawczej. W wersji zapoznawczej należy użyć *jednostki SKU podstawowego modułu równoważenia obciążenia,* jeśli konieczne jest przypisanie publicznego adresu IP na węzeł.

Węzły AKS nie wymagają własnych publicznych adresów IP do komunikacji. Jednak scenariusze mogą wymagać węzłów w puli węzłów do odbierania własnych dedykowanych publicznych adresów IP. Typowy scenariusz dotyczy obciążeń związanych z grami, w których konsola musi nawiązać bezpośrednie połączenie z maszyną wirtualną w chmurze, aby zminimalizować przeskoki. Ten scenariusz można osiągnąć w urzędzie AKS, rejestrując się w celu wyświetlenia funkcji w wersji zapoznawczej, Node Public IP (wersja zapoznawcza).

Zarejestruj się dla funkcji publiczny adres IP węzła, wydając następujące polecenie interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Po pomyślnej rejestracji wdrożyć szablon usługi Azure [above](#manage-node-pools-using-a-resource-manager-template) Resource Manager zgodnie z `enableNodePublicIP` tymi samymi instrukcjami, jak powyżej i dodać właściwość logiczną do agentpoolprofiles. Ustaw wartość `true` jako domyślnie jest `false` ustawiona tak, jakby nie została określona. 

Ta właściwość jest właściwością tylko do tworzenia i wymaga minimalnej wersji interfejsu API 2019-06-01. Można to zastosować zarówno do pul węzłów systemu Linux, jak i Windows.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule utworzono klaster AKS, który zawiera węzły oparte na procesorze GPU. Aby zmniejszyć niepotrzebne koszty, można usunąć *gpunodepool*lub cały klaster AKS.

Aby usunąć pulę węzłów opartych na procesorze GPU, użyj polecenia [az aks nodepool,][az-aks-nodepool-delete] jak pokazano na poniższym przykładzie:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Aby usunąć sam klaster, użyj polecenia [usuń grupę AZ,][az-group-delete] aby usunąć grupę zasobów AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak tworzyć i zarządzać wieloma pulami węzłów w klastrze AKS. Aby uzyskać więcej informacji na temat kontrolowania zasobników w pulach węzłów, zobacz [Najważniejsze wskazówki dotyczące zaawansowanych funkcji harmonogramu w ustroju AKS][operator-best-practices-advanced-scheduler].

Aby utworzyć i używać pul węzłów kontenerów systemu Windows Server, zobacz [Tworzenie kontenera systemu Windows Server w programie AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md