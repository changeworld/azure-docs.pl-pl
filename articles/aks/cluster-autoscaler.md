---
title: Użyj skalowania automatycznego klastra w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak za pomocą skalowania automatycznego klastra automatycznie Skaluj klaster w taki sposób, aby spełniać wymagania aplikacji w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/29/2019
ms.author: iainfou
ms.openlocfilehash: 1ab31662b55f89b23d6aba8ebe4394d1e78092dc
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437530"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>W wersji zapoznawczej — automatyczne skalowanie klastra, aby spełniać wymagania aplikacji w usłudze Azure Kubernetes Service (AKS)

Na bieżąco z zapotrzebowaniem na zasoby aplikacji w usłudze Azure Kubernetes Service (AKS), może być konieczne dostosowanie liczby węzłów, działających obciążeń. Składnik skalowanie klastra można śledzić zasobników w klastrze, do której nie można zaplanować z powodu ograniczeń zasobów. Gdy zostaną wykryte problemy, aby sprostać potrzebom aplikacji zwiększa się liczba węzłów. Węzły są regularnie sprawdzane pod kątem braku uruchomionych zasobników, z liczbą węzłów zmniejszy zgodnie z potrzebami. Ta możliwość automatycznego skalowania w górę lub w dół liczbę węzłów w klastrze AKS umożliwia uruchomienie klastra wydajne, ekonomiczne.

W tym artykule pokazano, jak włączyć i zarządzać nimi skalowanie klastra w klastrze AKS.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej usługi AKS są samoobsługi i opcjonalnych. Wersje zapoznawcze są udostępniane do zbierania opinii i błędy z naszej społeczności. Nie są one jednak obsługiwane przez pomoc techniczną systemu Azure. Jeśli tworzenie klastra lub Dodaj następujące funkcje do istniejących klastrów tego klastra jest obsługiwany, dopóki ta funkcja nie jest już dostępna w wersji zapoznawczej i absolwentów, które są ogólnie dostępne (GA).
>
> Jeśli wystąpią problemy związane z wersji zapoznawczej, [Otwórz problem w repozytorium GitHub usługi AKS] [ aks-github] o nazwie funkcja w wersji zapoznawczej w tytuł usterki.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten artykuł wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.55 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia w wersji zapoznawczej usługi aks

Klastry AKS, które obsługują skalowanie klastra należy użyć zestawów skalowania maszyn wirtualnych i uruchamianie wersję rozwiązania Kubernetes *1.12.4* lub nowszej. Ta obsługa zestawu skalowania jest w wersji zapoznawczej. Aby włączyć i tworzenie klastrów, które używają zestawów skalowania, należy najpierw zainstalować *podglądu usługi aks* rozszerzenie interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [Dodaj rozszerzenie az] [ az-extension-add] polecenia, jak pokazano w następującym przykład:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Jeśli wcześniej zainstalowano *podglądu usługi aks* rozszerzenia, zainstaluj dostępne aktualizacje, przy użyciu `az extension update --name aks-preview` polecenia.

### <a name="register-scale-set-feature-provider"></a>Zarejestruj dostawcę funkcji zestawu skalowania

Aby utworzyć usługi AKS, który używa skalowania zestawów, należy również włączyć flagi funkcji w ramach Twojej subskrypcji. Aby zarejestrować *VMSSPreview* flagę funkcji, należy użyć [az feature register] [ az-feature-register] polecenia, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Zajmuje kilka minut, zanim stan wyświetlany *zarejestrowanej*. Można sprawdzić stan rejestracji przy użyciu [lista funkcji az] [ az-feature-list] polecenia:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestracji *Microsoft.ContainerService* dostawcę zasobów przy użyciu [az provider register] [ az-provider-register] polecenia:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="about-the-cluster-autoscaler"></a>Skalowanie klastra — informacje

Aby dostosować do zmieniających się wymagań aplikacji, takich jak między produktem workday i wieczorami lub w weekendy, klastry często muszą mieć możliwość automatycznego skalowania. Klastry usługi AKS można skalować w jeden z dwóch sposobów:

* **Klastra skalowania automatycznego** zegarki dla zasobników, których nie można zaplanować na węzłach, z powodu ograniczeń zasobów. Zwiększa automatycznie następnie klastra liczbę węzłów.
* **Skalowania automatycznego zasobników w poziomie** korzysta z serwera metryki w klastrze Kubernetes do monitorowania zapotrzebowania na zasoby zasobników. Jeśli usługa wymaga więcej zasobów, aby sprostać automatycznie zwiększa się liczba zasobników.

![Skalowanie klastra i skalowania automatycznego zasobników w poziomie często współpracują ze sobą do obsługi wymagań wymagana aplikacja](media/autoscaler/cluster-autoscaler.png)

Skalowanie zasobników w poziomie i skalowanie klastra można także zmniejszyć liczbę zasobników i węzłów stosownie do potrzeb. Skalowanie klastra zmniejsza liczbę węzłów, gdy nastąpiła nieużywaną pojemność w okresie czasu. Zasobników w węźle ma zostać usunięty przez skalowanie klastra bezpiecznie są zaplanowane w innym miejscu w klastrze. Skalowanie klastra może być, nie można skalować w dół, jeśli nie można przenieść zasobników, na przykład w następujących sytuacjach:

* Zasobnik bezpośrednio utworzony i nie jest obsługiwana przez zestaw obiektów, takie wdrożenie lub repliki kontrolera.
* Zasobnik przerw w działaniu budżetu (PDB) jest zbyt restrykcyjna i nie dopuszcza się liczba zasobników można spadnie poniżej określonego progu.
* Zasobnik używa selektory węzła lub antykoligację, które nie mogą być uwzględniane, jeśli zaplanowane w innym węźle.

Aby uzyskać więcej informacji na temat sposobu skalowanie klastra może być niemożliwe skalować w dół, zobacz [jakiego rodzaju zasobników może uniemożliwić usunięcie węzła skalowanie klastra?][autoscaler-scaledown]

Skalowanie klastra używa Parametry uruchamiania dla elementów, takich jak przedziałów czasu między imprez i progi zasobów. Te parametry są definiowane przez platformę Azure, a obecnie nie są widoczne dla dostosowania. Aby uzyskać więcej informacji na temat parametry używa skalowanie klastra, zobacz [co to są parametry skalowanie klastra?] [autoscaler-parameters].

Dwa autoscalers mogą współpracować ze sobą, i są często wdrożone w klastrze. W połączeniu, skalowania automatycznego zasobników w poziomie koncentruje się na temat uruchamiania Liczba zasobników wymaganych w celu spełnienia określonych wymagań aplikacji. Skalowanie klastra koncentruje się na prowadzeniu liczbę węzłów, wymagane do obsługi zaplanowanych zasobników.

> [!NOTE]
> Ręczne skalowanie jest wyłączone, gdy używasz skalowanie klastra. Umożliwiają skalowanie klastra, określić wymaganą liczbę węzłów. Jeśli chcesz ręcznie skalowania klastra, [Wyłącz skalowanie klastra](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Tworzenie klastra AKS i włączyć skalowanie klastra

Jeśli musisz utworzyć klaster usługi AKS, użyj [tworzenie az aks] [ az-aks-create] polecenia. Określ *wersji rozwiązania kubernetes —* , spełnia lub przekracza minimalny numer wersji wymagane zgodnie z opisem w poprzednim [przed rozpoczęciem](#before-you-begin) sekcji. Aby włączyć i skonfigurować klaster skalowania automatycznego, należy użyć *--enable klastra — skalowanie* parametru i określ węzeł *— minimalna liczba* i *— maksymalna liczba*.

> [!IMPORTANT]
> Skalowanie klastra jest składnikiem usługi Kubernetes. Mimo że klaster AKS używa skalowania maszyn wirtualnych dla węzłów, nie ręcznie włączyć lub zmienić ustawienia w witrynie Azure portal lub przy użyciu wiersza polecenia platformy Azure, automatyczne skalowanie zestawu skalowania. Umożliwiają skalowanie klastra Kubernetes Zarządzanie ustawieniami wymagane skali. Aby uzyskać więcej informacji, zobacz [można zmodyfikować zasoby usługi AKS w grupie zasobów MC_?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc_-resource-group)

Poniższy przykład tworzy klaster usługi AKS z zestawu skalowania maszyn wirtualnych i włączone skalowanie klastra i korzysta z co najmniej *1* i maksymalna liczba *3* węzłów:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location canadaeast

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --kubernetes-version 1.12.6 \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Trwa kilka minut, aby utworzyć klaster, a następnie skonfiguruj ustawienia skalowania automatycznego klastra.

### <a name="enable-the-cluster-autoscaler-on-an-existing-aks-cluster"></a>Włącz skalowanie klastra w istniejącym klastrze usługi AKS

Możesz włączyć skalowanie klastra w klastrze AKS, który spełnia wymagania opisane w poprzednim [przed rozpoczęciem](#before-you-begin) sekcji. Użyj [aktualizacji az aks] [ az-aks-update] polecenia i zdecydować o *--enable klastra — skalowanie*, następnie określ węzeł *— minimalna liczba* i *— maksymalna liczba*. Poniższy przykład umożliwia skalowanie klastra w istniejącym klastrze, który używa co najmniej *1* i maksymalna liczba *3* węzłów:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Jeśli minimalnej liczbie węzłów jest większa niż liczba istniejących węzłów w klastrze, zajmuje kilka minut, aby utworzyć dodatkowe węzły.

## <a name="change-the-cluster-autoscaler-settings"></a>Zmień ustawienia skalowania automatycznego klastra

W poprzednim kroku, aby utworzyć lub zaktualizować istniejący klaster AKS, ustawiono klastra skalowania automatycznego minimalnej liczbie węzłów *1*, i maksymalnej liczby węzłów została ustawiona na *3*. Jak aplikacja zmianie zapotrzebowania, może być konieczne dostosowanie liczby węzłów klastra skalowania automatycznego.

Aby zmienić liczbę węzłów, użyj [aktualizacji az aks] [ az-aks-update] polecenia, a następnie określić wartość minimalną i maksymalną. Poniższy przykład ustawia *— minimalna liczba* do *1* i *— maksymalna liczba* do *5*:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

> [!NOTE]
> W trakcie okresu zapoznawczego nie można ustawić wyższe minimalnej liczbie węzłów niż jest aktualnie ustawiona dla klastra. Na przykład, jeśli masz obecnie minimalna liczba równa *1*, nie można zaktualizować liczby min do *3*.

Monitorowanie wydajności aplikacji i usług, a następnie dostosowanie liczby węzłów klastra skalowania automatycznego dopasowania wymaganych parametrów.

## <a name="disable-the-cluster-autoscaler"></a>Wyłącz skalowanie klastra

Jeśli nie chcesz już używać skalowanie klastra, można je wyłączyć za pomocą [aktualizacji az aks] [ az-aks-update] polecenia. Węzły nie są usuwane po wyłączeniu skalowanie klastra.

Aby usunąć skalowanie klastra, określ *— disable klastra — skalowanie* parametru, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Można ręcznie skalować przy użyciu klastra [az aks skalowania] [ az-aks-scale] polecenia. Jeśli używasz skalowania automatycznego zasobników w poziomie, funkcja będzie nadal działać z skalowanie klastra wyłączone, ale zasobników może pozostać mógł zostać zaplanowana, jeżeli zasoby węzła są wszystkie używane.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule pokazano, jak automatycznie skalować liczbę węzłów AKS. Aby automatycznie dostosowywać liczbę zasobników, na których działa Twoja aplikacja umożliwia także skalowanie zasobników w poziomie. Aby uzyskać instrukcje na temat korzystania ze skalowania automatycznego zasobników w poziomie, zobacz [skalowanie aplikacji w usłudze AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-github]: https://github.com/azure/aks/issues]

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
