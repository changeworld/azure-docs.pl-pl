---
title: Konfigurowanie wtyczki kubenet sieci w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skonfigurować wtyczki kubenet (podstawowe) sieci w usłudze Azure Kubernetes Service (AKS) do wdrożenia klastra usługi AKS w istniejącej sieci wirtualnej i podsieci.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: iainfou
ms.reviewer: nieberts, jomore
ms.openlocfilehash: f57c1af4c497b51f5289559737fad5ce4cf2e85b
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2019
ms.locfileid: "67358046"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Użyj wtyczki kubenet sieci przy użyciu własnych zakresów adresów IP w usłudze Azure Kubernetes Service (AKS)

Domyślnie AKS klastrów użyj [wtyczki kubenet][kubenet], a siecią wirtualną platformy Azure i podsieć są tworzone dla Ciebie. Za pomocą *wtyczki kubenet*, węzły Uzyskaj adres IP z podsieci sieci wirtualnej platformy Azure. Zasobników otrzymują adres IP z logicznie różnymi przestrzeniami adresowymi w podsieci sieci wirtualnej platformy Azure z węzłów. Translator adresów sieciowych (NAT) jest następnie konfigurowana, tak aby zasobników może dotrzeć do zasobów w sieci wirtualnej platformy Azure. Źródłowy adres IP ruchu jest translatora adresów Sieciowych do podstawowego adresu IP węzła zajmie się. To podejście znacznie zmniejsza liczbę adresów IP, które należy zarezerwować w przestrzeń sieci dla zasobników do użycia.

Za pomocą [wtyczki Azure Container Networking interfejsu (CNI)][cni-networking], co zasobnika pobiera adres IP z podsieci i są dostępne bezpośrednio. Te adresy IP musi być unikatowa w przestrzeń sieci i muszą być planowane z góry. Każdy węzeł ma parametr konfiguracji maksymalna liczba zasobników, które obsługuje. Równoważną liczbę adresów IP w każdym węźle następnie są zarezerwowane na początku dla tego węzła. Takie podejście wymaga więcej planowania i często prowadzi do wyczerpania adresu IP lub jest potrzebna ponowna kompilacja klastrów w większej podsieci, w miarę wzrostu wymagań aplikacji.

W tym artykule dowiesz się, jak używać *wtyczki kubenet* sieci w celu tworzenia i używania podsieci sieci wirtualnej dla klastra usługi AKS. Aby uzyskać więcej informacji na temat opcji sieci i zagadnienia, zobacz [sieci pojęcia dla platformy Kubernetes i AKS][aks-network-concepts].

> [!WARNING]
> Aby użyć pule węzłów systemu Windows Server (obecnie dostępna w wersji zapoznawczej w usłudze AKS), należy użyć wtyczki Azure CNI. Korzystanie z wtyczki kubenet jako model sieci nie jest dostępna dla kontenerów systemu Windows Server.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz wiersza polecenia platformy Azure w wersji 2.0.65 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli potrzebujesz instalacja lub uaktualnienie, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Omówienie wtyczki kubenet sieci przy użyciu swojej własnej podsieci

W wielu środowiskach zdefiniowano sieci wirtualne i podsieci z przydzielone zakresy adresów IP. Te zasoby sieci wirtualnej są używane do obsługi wielu usług i aplikacji. Zapewnienie łączności sieciowej, można użyć klastrów AKS *wtyczki kubenet* (podstawowe operacje sieciowe) lub wtyczki Azure CNI (*zaawansowany siecią*).

Za pomocą *wtyczki kubenet*tylko węzły, otrzymują adresy IP w podsieci sieci wirtualnej. Zasobników nie mogą komunikować się ze sobą bezpośrednio. Zamiast tego przekazywania zdefiniowaną przez użytkownika routingu (UDR) i adres IP jest używany dla połączenia między zasobników w węzłach. Można także wdrożyć zasobników za to usługa, która otrzymuje przypisany adres IP i równoważy obciążenie dla aplikacji. Na poniższym diagramie przedstawiono, jak węzłów AKS, otrzymują adresy IP w podsieci sieci wirtualnej, ale nie zasobników:

![Model sieciowych wtyczki Kubenet z klastrem usługi AKS](media/use-kubenet/kubenet-overview.png)

Platforma Azure obsługuje maksymalnie 400 tras przez użytkownika, więc nie może być większa niż 400 węzły klastra usługi AKS. AKS funkcje, takie jak [wirtualnych węzłów][virtual-nodes] lub zasady sieciowe nie są obsługiwane za pomocą *wtyczki kubenet*.

Za pomocą *wtyczki Azure CNI*, pod każdym otrzymuje adres IP w podsieci IP i może komunikować się bezpośrednio z innymi zasobników i usługami. Klastry usługi może być większy niż zakres adresów IP, które określisz. Jednak zakres adresów IP, które muszą być planowane z góry, a wszystkie adresy IP są używane przez węzłów AKS na podstawie maksymalnej liczby zasobników, które obsługują one. Zaawansowane funkcje sieciowe i scenariuszy, takich jak [wirtualnych węzłów][virtual-nodes] lub zasady sieciowe są obsługiwane za pomocą *wtyczki Azure CNI*.

### <a name="ip-address-availability-and-exhaustion"></a>Dostępność adresu IP i wyczerpania

Za pomocą *wtyczki Azure CNI*, typowym problemem jest przypisany zakres adresów IP jest zbyt mała, aby następnie dodać dodatkowe węzły, podczas uaktualniania klastra lub skalowania. Sieci zespół może również nie będzie w stanie do wystawiania wystarczająco duży zakres adresów IP do obsługi wymagań oczekiwanego aplikacji.

Jako naruszenia zabezpieczeń, można utworzyć klaster AKS, która używa *wtyczki kubenet* i nawiąż połączenie z istniejącą podsieć sieci wirtualnej. Takie podejście umożliwia węzłów odbieranie określonych adresów IP, bez konieczności rezerwowania dużej liczby adresów IP na początku wszystkich potencjalnych zasobników, które mogą być uruchamiane w klastrze.

Za pomocą *wtyczki kubenet*, możesz użyć znacznie mniejszy zakres adresów IP i mieć możliwość obsługi dużych klastrach i wymagań aplikacji. Na przykład, nawet w przypadku */27* zakresu adresów IP, może uruchomić klaster z węzłami 20 – 25 z wystarczająco dużo miejsca do skalowania lub uaktualnienia. Ten rozmiar klastra będzie obsługiwać maksymalnie *2,200 2750* zasobników (przy użyciu domyślnej maksymalnej 110 zasobniki w każdym węźle). Maksymalna liczba zasobników na węzeł, który można skonfigurować za pomocą *wtyczki kubenet* w usłudze AKS to 250.

Następujące podstawowe obliczenia porównania różnic w modelach sieci:

- **wtyczki kubenet** — prosty */24* zakres adresów IP może obsługiwać maksymalnie *251* węzłów w klastrze (każdej podsieci sieci wirtualnej platformy Azure rezerwuje pierwszych trzech adresów IP dla operacji zarządzania)
  - Ta liczba węzłów może obsługiwać maksymalnie *27,610* zasobników (przy użyciu domyślnej maksymalnej liczby zasobników 110 za węzeł za pomocą *wtyczki kubenet*)
- **Wtyczki Azure CNI** — tej samej basic */24* zakres podsieci tylko może obsługiwać maksymalnie *8* węzłów w klastrze
  - Ta liczba węzłów może obsługiwać tylko do *240* zasobników (przy użyciu domyślnej maksymalnej liczby zasobników 30 za węzeł za pomocą *wtyczki Azure CNI*)

> [!NOTE]
> Te maksymalne wartości nie uwzględniać uaktualnienie konta lub operacje skalowania. W praktyce nie można uruchomić maksymalną liczbę węzłów, które obsługuje zakresu adresów IP podsieci. Podczas skalowania operacji uaktualnienia, należy pozostawić pewnych adresów IP dostępne do użycia.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Komunikacja równorzędna sieci wirtualnej i połączeń usługi ExpressRoute

Aby zapewnić łączność lokalna zarówno *wtyczki kubenet* i *wtyczki Azure CNI* podejścia sieci można użyć [komunikacji równorzędnej sieci wirtualnej platformy Azure][vnet-peering] or [ExpressRoute connections][express-route]. Planowanie zakresów adresów IP, aby uniknąć nakładania się i routing ruchu na nieprawidłowy. Na przykład użyć wieloma sieciami lokalnymi *10.0.0.0/8* adresów zakresu, która jest anonsowany za pośrednictwem połączenia usługi ExpressRoute. Zaleca się tworzenie klastrów usługi AKS w podsieciach sieci wirtualnej platformy Azure poza ten zakres adresów, takich jak *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Wybierz model sieci do użycia

Wybór, których wtyczka sieci na potrzeby usługi AKS klastra jest zwykle równowagi między elastycznością i potrzeb zaawansowanej konfiguracji. Następujące zagadnienia dotyczące pomóc konspektu, podczas każdego modelu sieci może być najbardziej odpowiednie.

Użyj *wtyczki kubenet* po:

- Masz ograniczone przestrzeń adresów IP.
- W większości komunikacji zasobnika jest w klastrze.
- Zaawansowane funkcje, takie jak wirtualne węzły lub zasad sieci nie jest konieczne.

Użyj *wtyczki Azure CNI* po:

- Masz dostępne przestrzeń adresów IP.
- W większości komunikacji zasobnika jest do zasobów spoza klastra.
- Nie chcesz zarządzać tras zdefiniowanych przez użytkownika.
- Należy zaawansowane funkcje, takie jak wirtualne węzły lub zasad sieciowych.

Aby uzyskać więcej informacji na ułatwią podjęcie decyzji o modelu sieci do użycia, zobacz [Porównaj modele sieciowych i ich zakres pomocy technicznej][network-comparisons].

> [!NOTE]
> Kuberouter sprawia, że można włączyć zasady sieci w przypadku korzystania z wtyczki kubenet i mogą być instalowane jako daemonset w klastrze AKS. Należy pamiętać, router rozwiązania kubernetes jest nadal w wersji beta i nie jest oferowana przez firmę Microsoft dla projektu.

## <a name="create-a-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Aby rozpocząć korzystanie z *wtyczki kubenet* i własnych podsieci sieci wirtualnej, najpierw utwórz grupę zasobów za pomocą [Tworzenie grupy az][az-group-create] polecenia. W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Jeśli nie masz istniejącej sieci wirtualnej i podsieci, aby użyć tworzenia tych zasobów sieciowych przy użyciu [tworzenie sieci wirtualnej sieci az][az-network-vnet-create] polecenia. W poniższym przykładzie sieć wirtualną o nazwie *myVnet* z prefiksem adresu *192.168.0.0/16*. Podsieć jest tworzony o nazwie *myAKSSubnet* z prefiksem adresu *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Tworzenie nazwy głównej usługi i przypisywanie uprawnień

Jednostka usługi Azure Active Directory zezwala klastrowi usługi AKS na interakcje z innymi zasobami platformy Azure. Jednostka usługi musi mieć uprawnienia do zarządzania sieć wirtualną i podsieć, używanego przez węzłów AKS. Aby utworzyć nazwę główną usługi, użyj [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] polecenia:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Następujące przykładowe dane wyjściowe zawiera identyfikator aplikacji i hasło dla jednostki usługi. Te wartości są używane dodatkowe czynności obejmują przypisywanie roli do jednostki usługi, a następnie utworzyć klaster AKS:

```console
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Aby przypisać poprawne delegacji w kolejnych krokach, użyj [az sieci vnet show][az-network-vnet-show] and [az network vnet subnet show][az-network-vnet-subnet-show] poleceń, aby uzyskać wymagane identyfikatorów zasobów. Tych identyfikatorów zasobów są przechowywane jako zmienne i do których odwołuje się w kolejnych krokach:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Teraz przypisać nazwę główną usługi dla klastra usługi AKS *Współautor* uprawnień w sieci wirtualnej przy użyciu [utworzenia przypisania roli az][az-role-assignment-create] polecenia. Podaj własny  *\<appId >* jak pokazano w danych wyjściowych poprzedniego polecenia, aby utworzyć nazwę główną usługi:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Tworzenie klastra AKS w sieci wirtualnej

Możesz teraz utworzyć sieć wirtualną i podsieć i utworzeniu i przypisane uprawnienia dla jednostki usługi, aby korzystać z tych zasobów sieciowych. Teraz tworzenie klastra AKS w sieci wirtualnej i podsieci przy użyciu [tworzenie az aks][az-aks-create] polecenia. Zdefiniuj jednostki usługi  *\<appId >* i  *\<hasło >* , jak pokazano w danych wyjściowych poprzedniego polecenia, aby utworzyć jednostkę usługi.

Poniższe zakresy adresów IP są również określone jako część klastra tworzenia procesu:

* *— Cidr usługi* jest używany do przypisywania adresów IP wewnętrznych usług w klastrze AKS. Ten zakres adresów IP powinna być przestrzeń adresową, która nie jest używana w innym miejscu w danym środowisku sieciowym. Ten zakres obejmuje wszystkie zakresy sieci lokalnej, łączenie, czy zamierzasz połączyć sieci wirtualne platformy Azure przy użyciu Expressroute lub połączenie sieci VPN typu lokacja-lokacja.

* *Adres ip usługi dns —* adres powinien być *.10* adres zakres adresów IP usługi.

* *— Cidr zasobnika* powinny być dużych adresowa, która nie jest używana w innym miejscu w danym środowisku sieciowym. Ten zakres obejmuje wszystkie zakresy sieci lokalnej, łączenie, czy zamierzasz połączyć sieci wirtualne platformy Azure przy użyciu Expressroute lub połączenie sieci VPN typu lokacja-lokacja.
    * Ten zakres adresów musi być wystarczająco duży, aby uwzględnić liczbę węzłów, które oczekują skalować do. Nie można zmienić tego zakresu adresów, po wdrożeniu klastra, jeśli potrzebujesz większej liczby adresów dla kolejnych węzłów.
    * Zakres adresów IP zasobnika jest używany do przypisywania */24* przestrzeń adresowa do każdego węzła w klastrze. W poniższym przykładzie *— cidr zasobnika* z *10.244.0.0/16* przypisuje pierwszego węzła *10.244.0.0/24*, drugi węzeł *10.244.1.0/24*, a trzeci węzła *10.244.2.0/24*.
    * Skaluje klaster lub uaktualnień platforma Azure w dalszym ciągu Przypisz zakres adresów IP zasobnika do każdego nowego węzła.
    
* *— Adres w przypadku mostka platformy docker* zezwala węzłów AKS komunikowanie się z podstawowej platformy zarządzania. Ten adres IP nie może być w zakresie adresów IP sieci wirtualnej klastra, a nie mogą nakładać się na inne zakresy adresów, używany w sieci.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Podczas tworzenia klastra usługi AKS tabeli sieci zabezpieczeń grupy i wyznaczać trasy są tworzone. Te zasoby sieciowe są zarządzane przez na płaszczyźnie kontroli usługi AKS. Sieciowa grupa zabezpieczeń jest automatycznie kojarzony z wirtualnymi kartami sieciowymi na węzłów. Tabela tras jest automatycznie kojarzony z podsieci sieci wirtualnej. Reguły sieciowych grup zabezpieczeń i tabel tras i są automatycznie aktualizowane, jak utworzyć i udostępnić usługi.

## <a name="next-steps"></a>Kolejne kroki

Z klastrem usługi AKS wdrażane w istniejącej podsieci sieci wirtualnej można teraz używać klastra zwykły. Rozpoczynanie pracy z usługą [tworzenia aplikacji przy użyciu usługi Azure Dev miejsca do magazynowania][dev-spaces] or [using Draft][use-draft], lub [wdrażać aplikacje za pomocą narzędzia Helm][helm użyj].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
