---
title: Korzystanie z automatycznego skalowania klastra w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak za pomocą automatycznego skalowania klastra automatycznie skalować klaster w celu spełnienia wymagań aplikacji w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: dc5e862109a766f708338ebddb91a75ffc550306
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031919"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Wersja zapoznawcza — automatyczne skalowanie klastra w celu spełnienia wymagań aplikacji w usłudze Azure Kubernetes Service (AKS)

Aby zachować wymagania dotyczące aplikacji w usłudze Azure Kubernetes Service (AKS), może być konieczne dostosowanie liczby węzłów, na których działają obciążenia. Składnik automatycznego skalowania klastra można obejrzeć w przypadku zasobników w klastrze, których nie można zaplanować ze względu na ograniczenia zasobów. W przypadku wykrycia problemów liczba węzłów w puli węzłów zostanie zwiększona w celu spełnienia wymagań aplikacji. Węzły są również regularnie sprawdzane pod kątem braku uruchomionych zasobników, a liczba węzłów zmniejszyła się w miarę potrzeby. Możliwość automatycznego skalowania w górę lub w dół liczby węzłów w klastrze AKS umożliwia uruchamianie wydajnego, ekonomicznego klastra.

W tym artykule opisano sposób włączania automatycznego skalowania klastra i zarządzania nim w klastrze AKS. Automatyczne skalowanie klastra powinno być testowane tylko w wersji zapoznawczej w klastrach AKS.

> [!IMPORTANT]
> Funkcja AKS w wersji zapoznawczej to samoobsługowe uczestnictwo. Wersje zapoznawcze są udostępniane w postaci "AS-IS" i "jako dostępne" i są wyłączone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte obsługą klienta w oparciu o najlepszy nakład pracy. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby dowiedzieć się więcej, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS][aks-support-policies]
> * [Pomoc techniczna platformy Azure — często zadawane pytania][aks-faq]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Zainstaluj rozszerzenie interfejsu wiersza polecenia AKS-Preview

Aby można było korzystać z automatycznego skalowania klastra, wymagane jest rozszerzenie interfejsu wiersza polecenia *AKS-Preview* w wersji 0.4.4 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji* zapoznawczej przy użyciu poleceń [AZ Extension Add][az-extension-add] , a następnie wyszukaj wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-scale-set-feature-provider"></a>Zarejestruj dostawcę funkcji zestawu skalowania

Aby utworzyć element AKS, który korzysta z zestawów skalowania, należy również włączyć flagę funkcji w ramach subskrypcji. Aby zarejestrować flagę funkcji *VMSSPreview* , użyj polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji nie można obecnie wyrejestrować tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej można użyć wartości domyślnych dla wszystkich klastrów AKS utworzonych w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Korzystaj z oddzielnej subskrypcji, aby testować funkcje w wersji zapoznawczej i zbierać opinie.

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Wyświetlenie stanu *rejestracji*może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów AKS i zarządzania nimi przy użyciu automatycznego skalowania klastra obowiązują następujące ograniczenia:

* Nie można użyć dodatku routingu aplikacji protokołu HTTP.

## <a name="about-the-cluster-autoscaler"></a>Informacje o automatycznym skalowaniu klastra

Aby dostosować się do zmieniających się wymagań aplikacji, takich jak między dniem i wieczór lub w weekendie, klastry często potrzebują sposobu na automatyczne skalowanie. Klastry AKS można skalować na jeden z dwóch sposobów:

* **Automatyczne skalowanie klastra** jest obserwujące dla zasobników, których nie można zaplanować w węzłach ze względu na ograniczenia zasobów. Klaster automatycznie zwiększa liczbę węzłów.
* Skalowanie w **poziomie** poniżej używa serwera metryk w klastrze Kubernetes do monitorowania zapotrzebowania na zasoby. Jeśli usługa wymaga więcej zasobów, liczba numerów jest automatycznie zwiększana, aby sprostać zapotrzebowaniu.

![Automatyczne skalowanie klastra i skalowanie w poziomie, które często współpracują ze sobą, obsługują wymagane wymagania aplikacji](media/autoscaler/cluster-autoscaler.png)

Zarówno Skalowanie automatyczne, jak i automatyczne skalowanie klastra mogą również zmniejszać liczbę numerów i węzłów w zależności od wymagań. Automatyczne skalowanie klastra zmniejsza liczbę węzłów, gdy w danym okresie czasu wykorzystano nieużywaną pojemność. W węźle, który ma zostać usunięty przez automatyczne skalowanie klastra, są bezpiecznie zaplanowane w innym miejscu w klastrze. Automatyczne skalowanie klastra może nie być w stanie skalować w dół, jeśli nie można przenieść zasobników, na przykład w następujących sytuacjach:

* Bezpośrednio utworzone i nie są obsługiwane przez obiekt kontrolera, takie jak wdrożenie lub zestaw replik.
* Budżet (PDB) jest zbyt restrykcyjny i nie zezwala na spadek liczby numerów w wysokości poniżej określonego progu.
* Przy użyciu selektorów węzłów lub zapobiegania, których nie można uznać, jeśli zaplanowano w innym węźle.

Aby uzyskać więcej informacji o tym, jak może być niemożliwe skalowanie automatycznego skalowania klastra, zobacz, [jakie typy zasobników mogą uniemożliwić usunięcie węzła przez automatyczne skalowanie klastra?][autoscaler-scaledown]

Automatyczne skalowanie klastra używa parametrów uruchamiania dla elementów, takich jak przedziały czasu między zdarzeniami skalowania i progami zasobów. Te parametry są definiowane przez platformę Azure i nie są obecnie udostępniane, aby można było je dostosować. Aby uzyskać więcej informacji na temat parametrów używanych przez automatyczne skalowanie klastra, zobacz [co to są parametry automatycznego skalowania klastra?][autoscaler-parameters].

Dwa autoskale mogą współdziałać ze sobą i często są wdrażane w klastrze. Po połączeniu, skalowanie w poziomie na pionie jest ukierunkowane na uruchamianie liczby numerów wymaganych do spełnienia wymagań aplikacji. Automatyczne skalowanie klastra koncentruje się na uruchamianiu liczby węzłów wymaganych do obsługi zaplanowanych zasobników.

> [!NOTE]
> Skalowanie ręczne jest wyłączone w przypadku korzystania z automatycznego skalowania klastra. Pozwól, aby automatyczne skalowanie klastra określiło wymaganą liczbę węzłów. Jeśli chcesz ręcznie skalować klaster, [Wyłącz automatyczne skalowanie klastra](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Tworzenie klastra AKS i Włączanie automatycznego skalowania klastra

Jeśli musisz utworzyć klaster AKS, użyj polecenia [AZ AKS Create][az-aks-create] . Aby włączyć i skonfigurować automatyczne skalowanie klastra w puli węzłów klastra, należy użyć parametru *--enable-Cluster-* autoscaleer i określić węzeł *--min-Count* i *--Max-Count*.

> [!IMPORTANT]
> Automatyczne skalowanie klastra to składnik Kubernetes. Chociaż klaster AKS korzysta z zestawu skalowania maszyn wirtualnych dla węzłów, nie włączaj ręcznie ani nie edytuj ustawień skalowania automatycznego skalowania w Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure. Zezwól automatycznemu skalowaniu klastra Kubernetes na zarządzanie wymaganymi ustawieniami skalowania. Aby uzyskać więcej informacji, zobacz [czy można modyfikować zasoby AKS w grupie zasobów węzła?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

W poniższym przykładzie jest tworzony klaster AKS z pulą jednego węzła, które są obsługiwane przez zestaw skalowania maszyn wirtualnych. Włącza również automatyczne skalowanie klastra w puli węzłów klastra i ustawia co najmniej *1* i maksymalnie *3* węzły:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!NOTE]
> Jeśli zostanie określona wartość *--Kubernetes-Version* w trakcie `az aks create`działania, ta wersja musi spełniać lub przekroczyć numer wersji minimalnej wymaganej w powyższej sekcji [przed rozpoczęciem](#before-you-begin) .

Utworzenie klastra i skonfigurowanie ustawień automatycznego skalowania klastra trwa kilka minut.

### <a name="update-the-cluster-autoscaler-on-an-existing-node-pool-in-a-cluster-with-a-single-node-pool"></a>Aktualizowanie automatycznego skalowania klastra w istniejącej puli węzłów w klastrze z pulą jednego węzła

Ustawienia poprzedniego automatycznego skalowania klastra można zaktualizować w klastrze, który spełnia wymagania opisane w poprzedniej sekcji [przed rozpoczęciem](#before-you-begin) . Użyj polecenia [AZ AKS Update][az-aks-update] , aby włączyć automatyczne skalowanie klastra w klastrze przy użyciu puli z *pojedynczym* węzłem.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Następnie automatyczne skalowanie klastra można włączyć lub wyłączyć za pomocą `az aks update --enable-cluster-autoscaler` polecenia lub. `az aks update --disable-cluster-autoscaler`

### <a name="enable-the-cluster-autoscaler-on-an-existing-node-pool-in-a-cluster-with-multiple-node-pools"></a>Włączanie automatycznego skalowania klastra w istniejącej puli węzłów w klastrze z wieloma pulami węzłów

Automatyczne skalowanie klastra może być również używane z włączoną [funkcją wersji zapoznawczej pul wielu węzłów](use-multiple-node-pools.md) . Można włączyć automatyczne skalowanie klastra dla poszczególnych pul węzłów w klastrze AKS zawierającym wiele pul węzłów i spełnić wymagania, które opisano w poprzedniej sekcji [przed rozpoczęciem](#before-you-begin) . Użyj polecenia [AZ AKS nodepool Update][az-aks-nodepool-update] , aby włączyć automatyczne skalowanie klastra w puli poszczególnych węzłów.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Następnie automatyczne skalowanie klastra można włączyć lub wyłączyć za pomocą `az aks nodepool update --enable-cluster-autoscaler` polecenia lub. `az aks nodepool update --disable-cluster-autoscaler`

## <a name="change-the-cluster-autoscaler-settings"></a>Zmień ustawienia automatycznego skalowania klastra

W poprzednim kroku, aby utworzyć klaster AKS lub zaktualizować istniejącą pulę węzłów, minimalna liczba węzłów w ramach automatycznego skalowania klastra została ustawiona na *1*, a maksymalna liczba węzłów została ustawiona na wartość *3*. Gdy aplikacja wymaga zmiany, może być konieczne dostosowanie liczby węzłów automatycznego skalowania klastra.

Aby zmienić liczbę węzłów, użyj polecenia [AZ AKS nodepool Update][az-aks-nodepool-update] .

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Powyższy przykład aktualizuje automatyczne skalowanie klastra w puli węzłów *mynodepool* w *myAKSCluster* do co najmniej *1* i maksymalnie *5* węzłów.

> [!NOTE]
> W trakcie okresu zapoznawczego nie można ustawić wyższej minimalnej liczby węzłów niż jest ona obecnie ustawiona dla puli węzłów. Na przykład w przypadku, gdy liczba minimalna została ustawiona na *1*, nie można zaktualizować liczby min do *3*.

Monitoruj wydajność aplikacji i usług, a następnie dostosuj liczbę węzłów automatycznego skalowania klastra, aby dopasować ją do wymaganej wydajności.

## <a name="disable-the-cluster-autoscaler"></a>Wyłącz automatyczne skalowanie klastra

Jeśli nie chcesz już używać automatycznego skalowania klastra, możesz je wyłączyć za pomocą polecenia [AZ AKS nodepool Update][az-aks-nodepool-update] , określając parametr *--disable-Cluster-* autoscaleer. Węzły nie są usuwane, gdy automatyczne skalowanie klastra jest wyłączone.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --disable-cluster-autoscaler
```

Możesz ręcznie skalować klaster przy użyciu polecenia [AZ AKS Scale][az-aks-scale] . W przypadku korzystania z funkcji automatycznego skalowania w poziomie, ta funkcja będzie nadal uruchamiana z wyłączonym automatycznym skalowaniem klastra, ale w przypadku, gdy zasoby węzła są używane, nie można zaplanować użycia.

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak automatycznie skalować liczbę węzłów AKS. Możesz również użyć skalowania w poziomie w pionie, aby automatycznie dostosować liczbę zasobników z uruchomioną aplikacją. Aby zapoznać się z instrukcjami dotyczącymi używania automatycznego skalowania w poziomie, zobacz [skalowanie aplikacji w AKS][aks-scale-apps].

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
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
