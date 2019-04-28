---
title: Tworzenie wirtualnych węzłów przy użyciu portalu Azure usługi Kubernetes (AKS)
description: Dowiedz się, jak utworzyć klaster usługi Kubernetes usługi Azure (AKS), korzystającą z wirtualnych węzłów w celu uruchomienia zasobników za pomocą witryny Azure portal.
services: container-service
author: iainfoulds
ms.topic: conceptual
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: 4b9e9aeab6ed24dd2179f853def02ad194fe1b67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61025186"
---
# <a name="preview---create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>W wersji zapoznawczej — tworzenie i konfigurowanie klastra usługi Azure Kubernetes usługi (AKS) do użycia wirtualnych węzłów w witrynie Azure portal

Aby szybko wdrożyć obciążenia w klastrze usługi Azure Kubernetes Service (AKS), można użyć wirtualnych węzłów. Wirtualne węzły, możesz mieć szybka aprowizacja zasobników i płacić tylko na sekundę na czas wykonywania uległ. W przypadku skalowania nie trzeba czekać na skalowanie klastra Kubernetes do wdrożenia węzłów obliczeniowych maszyn wirtualnych, aby uruchomić dodatkowe zasobniki. W tym artykule pokazano, jak tworzyć i konfigurować zasoby sieci wirtualnej i klastra usługi AKS przy użyciu wirtualnych węzłów włączone.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługi i opcjonalnych. Wersje zapoznawcze są udostępniane do zbierania opinii i błędy z naszej społeczności. Nie są one jednak obsługiwane przez pomoc techniczną systemu Azure. Jeśli tworzenie klastra lub Dodaj następujące funkcje do istniejących klastrów tego klastra jest obsługiwany, dopóki ta funkcja nie jest już dostępna w wersji zapoznawczej i absolwentów, które są ogólnie dostępne (GA).
>
> Jeśli wystąpią problemy związane z wersji zapoznawczej, [Otwórz problem w repozytorium GitHub usługi AKS] [ aks-github] o nazwie funkcja w wersji zapoznawczej w tytuł usterki.

## <a name="regional-availability"></a>Dostępność regionalna

Następujące regiony są obsługiwane dla wdrożeń wirtualnego węzła:

* Australia Wschodnia (australiaeast)
* Wschodnie stany USA (eastus)
* Zachodnio-środkowe stany USA (westcentralus)
* Europa Zachodnia (westeurope)
* Zachodnie stany USA (westus)

## <a name="known-limitations"></a>Znane ograniczenia
Działanie węzłów wirtualnej jest stopniu zależą od zestawu funkcji usługi ACI firmy. Następujące scenariusze nie są jeszcze obsługiwane za pomocą wirtualnych węzłów

* Przy użyciu jednostki usługi do obrazów ACR ściągnięcia. [Obejście](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) jest użycie [wpisy tajne usługi Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Ograniczenia sieci wirtualnej](../container-instances/container-instances-vnet.md) tym komunikacja równorzędna sieci wirtualnych, Kubernetes zasad sieciowych i ruch wychodzący do Internetu za pomocą sieciowych grup zabezpieczeń.
* Kontenery init
* [Aliasy hosta](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumenty](../container-instances/container-instances-exec.md#restrictions) dla exec w usłudze ACI
* [Daemonsets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) nie wdroży zasobników wirtualnego węzła

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób** > **Usługa Kubernetes**.

Na **podstawy** strony, skonfiguruj następujące opcje:

- *SZCZEGÓŁY PROJEKTU*: Wybierz subskrypcję platformy Azure, a następnie wybierz lub utwórz grupę zasobów platformy Azure, taką jak *myResourceGroup*. Wprowadź **nazwę klastra Kubernetes**, taką jak *myAKSCluster*.
- *SZCZEGÓŁY KLASTRA*: Wybierz region, wersję platformy Kubernetes i prefiks nazwy DNS dla klastra usługi AKS.
- *SKALA*: Wybierz rozmiar maszyny wirtualnej dla węzłów usługi AKS. Rozmiar maszyny wirtualnej **nie może** zostać zmieniony po wdrożeniu klastra AKS.
    - Wybierz liczbę węzłów do wdrożenia w klastrze. W tym artykule, należy ustawić **liczby węzłów** do *1*. Liczbę węzłów **można** dostosować po wdrożeniu klastra.
    - W obszarze **węzłów wirtualnej**, wybierz opcję *włączone*.

![Tworzenie klastra AKS i Włącz wirtualne węzły](media/virtual-nodes-portal/enable-virtual-nodes.png)

Domyślnie jest tworzone jednostki usługi Azure Active Directory. Ta jednostka usługi jest używana do komunikacji klastra i integracja z innymi usługami platformy Azure.

Klaster jest również skonfigurowana dla zaawansowane funkcje sieciowe. Węzły wirtualne są skonfigurowane do używania swojej własnej podsieci sieci wirtualnej platformy Azure. Ta podsieć ma delegowane uprawnienia do połączenia z zasobami platformy Azure między klastrem AKS. Jeśli nie masz jeszcze delegowanego podsieci, witryny Azure portal tworzy i konfiguruje sieć wirtualna platformy Azure i podsieć, do użytku z wirtualnych węzłów.

Wybierz pozycję **Przegląd + utwórz**. Po zakończeniu walidacji wybierz **Utwórz**.

Utworzenie klastra usługi AKS i przygotowanie go do użycia trwa kilka minut.

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes — narzędzia [kubectl][kubectl]. Klient `kubectl` jest instalowany wstępnie wraz z usługą Azure Cloud Shell.

Aby otworzyć usłudze Cloud Shell, wybierz **wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Użyj polecenia [az aks get-credentials][az-aks-get-credentials] w celu skonfigurowania narzędzia `kubectl` w celu nawiązania połączenia z klastrem Kubernetes. Poniższy przykład umożliwia pobranie poświadczeń dla nazwy klastra *myAKSCluster* w grupie zasobów *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby zweryfikować połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get] w celu zwrócenia listy węzłów klastra.

```azurecli-interactive
kubectl get nodes
```

Następujące przykładowe dane wyjściowe zawiera jeden węzeł maszyny Wirtualnej utworzone, a następnie wirtualnego węzła dla systemu Linux, *wirtualnego node-aci-linux*:

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Wdróż przykładową aplikację

W usłudze Azure Cloud Shell, Utwórz plik o nazwie `virtual-node.yaml` i skopiuj do poniższego kodu YAML. Aby zaplanować uruchamianie kontenera w węźle [nodeSelector] [ node-selector] i [toleration] [ toleration] są zdefiniowane. Te ustawienia umożliwiają zasobnika można zaplanować na wirtualny węzeł i upewnij się, że ta funkcja jest włączona pomyślnie.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Uruchom aplikację za pomocą [zastosować kubectl] [ kubectl-apply] polecenia.

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Użyj [kubectl get pods-] [ kubectl-get] polecenia `-o wide` argument służący do wypełniania wyjściowego listę zasobników i zaplanowane węzła. Należy zauważyć, że `virtual-node-helloworld` zasobnika została zaplanowana na `virtual-node-linux` węzła.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Zasobnik jest przypisany wewnętrznego adresu IP z podsieci sieci wirtualnej platformy Azure, delegowane do użytku z wirtualnych węzłów.

> [!NOTE]
> Jeśli używasz obrazów przechowywanych w usłudze Azure Container Registry, [Konfigurowanie i używanie wpisie tajnym rozwiązania Kubernetes][acr-aks-secrets]. To aktualne ograniczenie wirtualnym węzłami (wersja zapoznawcza) to, że nie używasz zintegrowane uwierzytelnianie jednostki usługi Azure AD. Jeśli nie używasz klucza tajnego, zasobników zaplanowane na wirtualnych węzłów nie można uruchomić i zgłoś błąd `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testowanie pod węzłem wirtualnym

Aby przetestować uruchomiony na wirtualny węzeł zasobnik, przejdź do aplikacji demonstracyjnej z klienta sieci web. Zasobnik jest przypisany wewnętrznego adresu IP, możesz szybko przetestować tego połączenia z poziomu innego zasobnik w klastrze AKS. Utwórz zasobnik testu i do niej dołączyć sesję terminalu:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Zainstaluj `curl` w zasobnik przy użyciu `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Teraz uzyskiwać dostęp do sieci za pomocą pod adres `curl`, takich jak *http://10.241.0.4*. Podaj własne wewnętrzny adres IP, które są wyświetlane w ciągu poprzednich `kubectl get pods` polecenia:

```azurecli-interactive
curl -L http://10.241.0.4
```

Wyświetlane są aplikacji pokazowej, jak pokazano w następujących danych wyjściowych skróconego przykładu:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zamknij sesję terminala tym zasobniku testu za pomocą `exit`. Gdy sesja zostanie zakończona, zasobnika ustawiany jest usunięte.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule zasobnik została zaplanowana na wirtualny węzeł i przypisać prywatny, wewnętrzny adres IP. Można zamiast tego utworzyć wdrożenie usługi i kierowanie ruchem na tym zasobniku za pośrednictwem modułu równoważenia obciążenia lub kontrolera danych przychodzących. Aby uzyskać więcej informacji, zobacz [utworzyć kontroler podstawowy ruch przychodzący w usłudze AKS][aks-basic-ingress].

Węzły wirtualne są jeden składnik skalowania rozwiązania w usłudze AKS. Aby uzyskać więcej informacji na temat skalowania rozwiązań zobacz następujące artykuły:

- [Użyj skalowania automatycznego zasobników w poziomie rozwiązania Kubernetes][aks-hpa]
- [Użyj skalowania automatycznego klastra Kubernetes][aks-cluster-autoscaler]
- [Zapoznaj się z przykładowych skalowania automatycznego dla wirtualnych węzłów][virtual-node-autoscale]
- [Więcej informacji na temat rozwiązania Virtual Kubelet biblioteki typu open source][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
