---
title: Konfigurowanie sieci kubenet w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skonfigurować sieć kubenet (podstawową) w usłudze Azure Kubernetes Service (AKS) do wdrażania klastra AKS w istniejącej sieci wirtualnej i podsieci.
services: container-service
ms.topic: article
ms.date: 06/26/2019
ms.reviewer: nieberts, jomore
ms.openlocfilehash: 3fe1d36b859884ab19a645e5693c7e7931fe5c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368472"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Użyj sieci kubenet z własnymi zakresami adresów IP w usłudze Azure Kubernetes Service (AKS)

Domyślnie klastry AKS używają [kubenetu,][kubenet]a sieć wirtualna i podsieć platformy Azure są tworzone dla Ciebie. Za *pomocą kubenet*węzły otrzymują adres IP z podsieci sieci wirtualnej platformy Azure. Zasobniki uzyskują adresy IP z przestrzeni adresowej, która jest logicznie różna od podsieci sieci wirtualnej platformy Azure, używanej przez węzły. Dzięki skonfigurowaniu translatora adresów sieciowych (NAT) zasobniki mogą uzyskać dostęp do zasobów w sieci wirtualnej platformy Azure. Źródłowy adres IP ruchu jest NAT'd do węzła podstawowy adres IP. Takie podejście znacznie zmniejsza liczbę adresów IP, które należy zarezerwować w przestrzeni sieciowej dla zasobników do użycia.

Za pomocą [interfejsu azure container networking interface (CNI)][cni-networking]każdy zasobnik otrzymuje adres IP z podsieci i jest dostępny bezpośrednio. Te adresy IP muszą być unikatowe w całej przestrzeni sieciowej i muszą być planowane z wyprzedzeniem. Każdy węzeł ma parametr konfiguracji dla maksymalnej liczby zasobników, które obsługuje. Równoważna liczba adresów IP na węzeł są następnie zarezerwowane z góry dla tego węzła. Takie podejście wymaga większego planowania i często prowadzi do wyczerpania adresów IP lub konieczności odbudowy klastrów w większej podsieci w miarę wzrostu zapotrzebowania aplikacji.

W tym artykule pokazano, jak używać sieci *kubenet* do tworzenia i używania podsieci sieci wirtualnej dla klastra AKS. Aby uzyskać więcej informacji na temat opcji sieci i zagadnień, zobacz [Pojęcia dotyczące sieci dla kubernetów i usługi AKS][aks-network-concepts].

## <a name="prerequisites"></a>Wymagania wstępne

* Sieć wirtualna klastra AKS musi zezwalać na wychodzące połączenie z Internetem.
* Nie należy tworzyć więcej niż jeden klaster AKS w tej samej podsieci.
* Klastry usługi AKS `169.254.0.0/16` `172.30.0.0/16`mogą `172.31.0.0/16`nie `192.0.2.0/24` używać , lub dla zakresu adresów usługi Kubernetes.
* Podmiot zabezpieczeń usługi używany przez klaster AKS musi mieć co najmniej uprawnienia [współautora sieci](../role-based-access-control/built-in-roles.md#network-contributor) w podsieci w sieci wirtualnej. Jeśli chcesz zdefiniować [rolę niestandardową](../role-based-access-control/custom-roles.md) zamiast wbudowanej roli współautora sieci, wymagane są następujące uprawnienia:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Aby korzystać z pul węzłów systemu Windows Server (obecnie w wersji zapoznawczej w usłudze AKS), należy użyć usługi Azure CNI. Użycie kubenet jako modelu sieciowego nie jest dostępne dla kontenerów systemu Windows Server.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz zainstalowanego i skonfigurowany interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Omówienie sieci kubenet z własną podsiecią

W wielu środowiskach zdefiniowano sieci wirtualne i podsieci z przydzielonymi zakresami adresów IP. Te zasoby sieci wirtualnej są używane do obsługi wielu usług i aplikacji. Aby zapewnić łączność sieciową, klastry AKS mogą używać *kubenet* (sieć podstawowa) lub Azure CNI *(sieć zaawansowana).*

W *przypadku kubenetu*tylko węzły odbierają adres IP w podsieci sieci wirtualnej. Zasobniki nie mogą komunikować się bezpośrednio ze sobą. Zamiast tego routing zdefiniowany przez użytkownika (UDR) i przekazywanie adresów IP jest używany do łączności między zasobnikami między węzłami. Można również wdrożyć zasobników za usługą, która odbiera przypisany adres IP i obciążenia równoważe ruch dla aplikacji. Na poniższym diagramie pokazano, jak węzły usługi AKS odbierają adres IP w podsieci sieci wirtualnej, ale nie zasobniki:

![Model sieci Kubenet z klastrem AKS](media/use-kubenet/kubenet-overview.png)

Platforma Azure obsługuje maksymalnie 400 tras w udr, więc nie można mieć klastra AKS większego niż 400 węzłów. [Węzły wirtualne][virtual-nodes] usługi AKS i zasady sieci azure nie są obsługiwane przez *kubenet*.  Można użyć [Calico Network Policies][calico-network-policies], ponieważ są one obsługiwane przez kubenet.

Za pomocą *usługi Azure CNI*każdy zasobnik otrzymuje adres IP w podsieci IP i może bezpośrednio komunikować się z innymi zasobnikami i usługami. Klastry mogą być tak duże, jak określony zakres adresów IP. Jednak zakres adresów IP musi być zaplanowany z wyprzedzeniem, a wszystkie adresy IP są używane przez węzły AKS na podstawie maksymalnej liczby zasobników, które mogą obsługiwać. Zaawansowane funkcje sieciowe i scenariusze, takie jak [węzły wirtualne][virtual-nodes] lub zasady sieci (azure lub calico) są obsługiwane przez *usługę Azure CNI.*

### <a name="ip-address-availability-and-exhaustion"></a>Dostępność i wyczerpanie adresów IP

W *przypadku usługi Azure CNI*częstym problemem jest to, że zakres adresów IP jest zbyt mały, aby następnie dodać dodatkowe węzły podczas skalowania lub uaktualniania klastra. Zespół sieciowy może również nie być w stanie wystawić wystarczająco dużego zakresu adresów IP, aby obsługiwać oczekiwane wymagania aplikacji.

W celu naruszenia zabezpieczeń można utworzyć klaster AKS, który używa *kubenet* i połączyć się z istniejącą podsiecią sieci wirtualnej. Takie podejście umożliwia węzłom odbieranie zdefiniowanych adresów IP, bez konieczności rezerwowania dużej liczby adresów IP z góry dla wszystkich potencjalnych zasobników, które mogą działać w klastrze.

Z *kubenet*, można użyć znacznie mniejszy zakres adresów IP i być w stanie obsługiwać duże klastry i wymagania aplikacji. Na przykład nawet w zakresie adresów IP */27* można uruchomić klaster węzłów 20-25 z wystarczającą ilością miejsca do skalowania lub uaktualniania. Ten rozmiar klastra będzie obsługiwać do *2200-2750* zasobników (z domyślnym maksimum 110 zasobników na węzeł). Maksymalna liczba zasobników na węzeł, które można skonfigurować za pomocą *kubenet* w AKS jest 110.

Następujące podstawowe obliczenia porównują różnicę w modelach sieciowych:

- **kubenet** - prosty */24* zakres adresów IP może obsługiwać do *251* węzłów w klastrze (każda podsieć sieci wirtualnej platformy Azure rezerwuje pierwsze trzy adresy IP dla operacji zarządzania)
  - Ta liczba węzłów może obsługiwać do *27 610* zasobników (z domyślnym maksimum 110 zasobników na węzeł z *kubenetem)*
- **Azure CNI** — ten sam podstawowy */24* zakres podsieci może obsługiwać tylko maksymalnie *8* węzłów w klastrze
  - Ta liczba węzłów może obsługiwać tylko maksymalnie *240* zasobników (z domyślną maksymalną maksymalną liczbą zasobników na węzeł za pomocą *usługi Azure CNI)*

> [!NOTE]
> Te wartości maksymalne nie uwzględniają operacji uaktualniania ani skalowania. W praktyce nie można uruchomić maksymalną liczbę węzłów, które obsługuje zakres adresów IP podsieci. Należy pozostawić niektóre adresy IP dostępne do użycia podczas skalowania operacji uaktualniania.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Połączenia komunikacji równorzędnej sieci wirtualnej i usługi ExpressRoute

Aby zapewnić łączność lokalną, zarówno *metody sieci kubenet,* jak i *sieć Azure-CNI* mogą używać [komunikacji równorzędnej sieci wirtualnej platformy Azure][vnet-peering] lub połączeń Usługi [ExpressRoute.][express-route] Starannie zaplanuj zakresy adresów IP, aby zapobiec nakładaniu się i nieprawidłowemu routingowi ruchu. Na przykład wiele sieci lokalnych korzysta z zakresu adresów *10.0.0.0/8,* który jest anonsowany za pośrednictwem połączenia Usługi ExpressRoute. Zaleca się utworzenie klastrów AKS w podsieciach sieci wirtualnej platformy Azure poza tym zakresem adresów, takich jak *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Wybieranie modelu sieciowego, którego chcesz użyć

Wybór wtyczki sieciowej do użycia w klastrze AKS jest zwykle równowaga między elastycznością i zaawansowanych potrzeb konfiguracji. Poniższe zagadnienia pomagają określić, kiedy każdy model sieci może być najbardziej odpowiednie.

Użyj *kubenet,* gdy:

- Masz ograniczoną przestrzeń adresową IP.
- Większość komunikacji zasobnika znajduje się w klastrze.
- Nie potrzebujesz zaawansowanych funkcji usługi AKS, takich jak węzły wirtualne lub zasady sieci platformy Azure.  Użyj [zasad sieci Calico][calico-network-policies].

Użyj *usługi Azure CNI,* gdy:

- Masz dostępną przestrzeń adresową IP.
- Większość komunikacji zasobnika jest do zasobów poza klastrem.
- Nie chcesz zarządzać UDRs.
- Potrzebujesz zaawansowanych funkcji AKS, takich jak węzły wirtualne lub zasady sieci platformy Azure.  Użyj [zasad sieci Calico][calico-network-policies].

Aby uzyskać więcej informacji ułatwiające podjęcie decyzji o modelu sieci, można znaleźć w temacie [Porównanie modeli sieci i ich zakresu pomocy technicznej.][network-comparisons]

## <a name="create-a-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Aby rozpocząć korzystanie z *kubenetu* i własnej podsieci sieci wirtualnej, należy najpierw utworzyć grupę zasobów za pomocą polecenia [tworzenie grupy az.][az-group-create] Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Jeśli nie masz istniejącej sieci wirtualnej i podsieci do użycia, utwórz te zasoby sieciowe za pomocą polecenia [tworzenie sieci az.][az-network-vnet-create] W poniższym przykładzie sieć wirtualna nosi nazwę *myVnet* z prefiksem adresu *192.168.0.0/16*. Zostanie utworzona podsieć o nazwie *myAKSSubnet* z prefiksem adresu *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Tworzenie jednostki usługi i przypisywanie uprawnień

Jednostka usługi Azure Active Directory zezwala klastrowi usługi AKS na interakcje z innymi zasobami platformy Azure. Podmiot zabezpieczeń usługi musi mieć uprawnienia do zarządzania siecią wirtualną i podsiecią, których używają węzły AKS. Aby utworzyć jednostkę usługi, użyj polecenia [az ad sp create-for-rbac:][az-ad-sp-create-for-rbac]

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Poniższy przykładowy wynik przedstawia identyfikator aplikacji i hasło dla jednostki usługi. Te wartości są używane w dodatkowych krokach, aby przypisać rolę do jednostki usługi, a następnie utworzyć klaster AKS:

```azurecli
az ad sp create-for-rbac --skip-assignment
```

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Aby przypisać poprawne delegacje w pozostałych krokach, użyj [polecenia programu AZ Network vnet show][az-network-vnet-show] i [az network vnet show,][az-network-vnet-subnet-show] aby uzyskać wymagane identyfikatory zasobów. Te identyfikatory zasobów są przechowywane jako zmienne i odwołuje się w pozostałych krokach:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Teraz przypisz jednostkę usługi dla uprawnień *współautora klastra* AKS w sieci wirtualnej za pomocą [polecenia tworzenia przypisania roli az.][az-role-assignment-create] Podaj własny * \<>appId,* jak pokazano w danych wyjściowych z poprzedniego polecenia, aby utworzyć jednostkę usługi:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Tworzenie klastra AKS w sieci wirtualnej

Utworzono teraz sieć wirtualną i podsieć oraz utworzono i przypisano uprawnienia jednostki usługi do korzystania z tych zasobów sieciowych. Teraz utwórz klaster AKS w sieci wirtualnej i podsieci za pomocą polecenia [az aks create.][az-aks-create] Zdefiniuj własną * \<jednostkę* usługi AppId>i * \<hasło>*, jak pokazano w danych wyjściowych z poprzedniego polecenia, aby utworzyć jednostkę usługi.

Następujące zakresy adresów IP są również definiowane jako część procesu tworzenia klastra:

* *--service-cidr* służy do przypisywania usług wewnętrznych w klastrze AKS adresu IP. Ten zakres adresów IP powinien być przestrzenią adresową, która nie jest używana w innym miejscu w środowisku sieciowym. Ten zakres obejmuje wszystkie lokalne zakresy sieci, jeśli łączysz się lub planujesz połączyć się z sieciami wirtualnymi platformy Azure przy użyciu trasy ekspresowej lub połączenia sieci VPN lokacja lokacja.

* Adres *--dns-service-ip* powinien być adresem *.10* zakresu adresów IP usługi.

* *--pod-cidr* powinien być dużą przestrzenią adresową, która nie jest używana w innym miejscu w środowisku sieciowym. Ten zakres obejmuje wszystkie lokalne zakresy sieci, jeśli łączysz się lub planujesz połączyć się z sieciami wirtualnymi platformy Azure przy użyciu trasy ekspresowej lub połączenia sieci VPN lokacja lokacja.
    * Ten zakres adresów musi być wystarczająco duży, aby pomieścić liczbę węzłów, które mają zostać przeskalowane do. Nie można zmienić tego zakresu adresów po wdrożeniu klastra, jeśli potrzebujesz więcej adresów dla dodatkowych węzłów.
    * Zakres adresów IP zasobnika służy do przypisywania */24* przestrzeni adresowej do każdego węzła w klastrze. W poniższym przykładzie *--pod-cidr* *10.244.0.0/16* przypisuje pierwszy węzeł *10.244.0.0/24*, drugi węzeł *10.244.1.0/24*, a trzeci węzeł *10.244.2.0/24*.
    * W miarę skalowania lub uaktualniania klastra platforma Azure nadal przypisuje zakres adresów IP zasobnika do każdego nowego węzła.
    
* *Adres --docker-bridge umożliwia* węzłom usługi AKS komunikowanie się z podstawową platformą zarządzania. Ten adres IP nie może mieszcząć się w zakresie adresów IP sieci wirtualnej klastra i nie powinien pokrywać się z innymi zakresami adresów używanymi w sieci.

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

> [!Note]
> Jeśli chcesz włączyć klaster AKS do uwzględnienia [zasad sieciowych Calico,][calico-network-policies] możesz użyć następującego polecenia.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Podczas tworzenia klastra AKS tworzone są sieciowe grupy zabezpieczeń i tabela tras. Te zasoby sieciowe są zarządzane przez płaszczyznę sterowania AKS. Sieciowa grupa zabezpieczeń jest automatycznie skojarzona z wirtualnymi kartami sieciowymi w węzłach. Tabela tras jest automatycznie skojarzona z podsiecią sieci wirtualnej. Reguły sieciowej grupy zabezpieczeń i tabele tras i są automatycznie aktualizowane podczas tworzenia i ujawniania usług.

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu klastra AKS w istniejącej podsieci sieci wirtualnej można teraz normalnie używać klastra. Rozpocznij tworzenie [aplikacji przy użyciu usługi Azure Dev Spaces][dev-spaces] lub przy użyciu wersji [roboczej][use-draft]lub [wdrażania aplikacji przy użyciu aplikacji Helm][use-helm].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

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
