---
title: Konfigurowanie sieci CNI platformy Azure w usłudze Azure Kubernetes (AKS)
description: Dowiedz się, jak skonfigurować sieć Azure CNI (zaawansowane) w usłudze Azure Kubernetes Service (AKS), w tym wdrażanie klastra AKS w istniejącej sieci wirtualnej i podsieci.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.openlocfilehash: 400d5a46ad62f8ac391c573eb64a7eb22dc4062c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047991"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Konfigurowanie sieci CNI platformy Azure w usłudze Azure Kubernetes (AKS)

Domyślnie klastry AKS używają [kubenetu,][kubenet]a sieć wirtualna i podsieć są tworzone dla Ciebie. W *przypadku kubenet*węzły otrzymują adres IP z podsieci sieci wirtualnej. Translacja adresów sieciowych (NAT) jest następnie konfigurowana w węzłach, a zasobniki otrzymują adres IP "ukryty" za adresem IP węzła. Takie podejście zmniejsza liczbę adresów IP, które należy zarezerwować w przestrzeni sieciowej dla zasobników do użycia.

Za pomocą [interfejsu azure container networking interface (CNI)][cni-networking]każdy zasobnik otrzymuje adres IP z podsieci i jest dostępny bezpośrednio. Te adresy IP muszą być unikatowe w całej przestrzeni sieciowej i muszą być planowane z wyprzedzeniem. Każdy węzeł ma parametr konfiguracji dla maksymalnej liczby zasobników, które obsługuje. Równoważna liczba adresów IP na węzeł są następnie zarezerwowane z góry dla tego węzła. Takie podejście wymaga większego planowania i często prowadzi do wyczerpania adresów IP lub konieczności odbudowy klastrów w większej podsieci w miarę wzrostu zapotrzebowania aplikacji.

W tym artykule pokazano, jak używać sieci *CNI platformy Azure* do tworzenia i używania podsieci sieci wirtualnej dla klastra AKS. Aby uzyskać więcej informacji na temat opcji sieci i zagadnień, zobacz [Pojęcia dotyczące sieci dla kubernetów i usługi AKS][aks-network-concepts].

## <a name="prerequisites"></a>Wymagania wstępne

* Sieć wirtualna klastra AKS musi zezwalać na wychodzące połączenie z Internetem.
* Klastry usługi AKS `169.254.0.0/16` `172.30.0.0/16`mogą `172.31.0.0/16`nie `192.0.2.0/24` używać , lub dla zakresu adresów usługi Kubernetes.
* Podmiot zabezpieczeń usługi używany przez klaster AKS musi mieć co najmniej uprawnienia [współautora sieci](../role-based-access-control/built-in-roles.md#network-contributor) w podsieci w sieci wirtualnej. Jeśli chcesz zdefiniować [rolę niestandardową](../role-based-access-control/custom-roles.md) zamiast wbudowanej roli współautora sieci, wymagane są następujące uprawnienia:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Zamiast jednostki usługi można użyć systemu przypisanego tożsamości zarządzanej dla uprawnień. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych](use-managed-identity.md).

## <a name="plan-ip-addressing-for-your-cluster"></a>Planowanie adresowania IP dla klastra

Klastry skonfigurowane przy pomocy sieci CNI platformy Azure wymagają dodatkowego planowania. Rozmiar sieci wirtualnej i jej podsieci musi uwzględniać liczbę zasobników, które mają być uruchamiane, oraz liczbę węzłów klastra.

Adresy IP zasobników i węzłów klastra są przypisywane z określonej podsieci w sieci wirtualnej. Każdy węzeł jest skonfigurowany z podstawowym adresem IP. Domyślnie 30 dodatkowych adresów IP są wstępnie skonfigurowane przez usługę Azure CNI, które są przypisane do zasobników zaplanowanych w węźle. Podczas skalowania w poziomie klastra każdy węzeł jest podobnie skonfigurowany z adresami IP z podsieci. Można również wyświetlić [maksymalną liczbę zasobników na węzeł](#maximum-pods-per-node).

> [!IMPORTANT]
> Liczba wymaganych adresów IP powinna obejmować zagadnienia dotyczące operacji uaktualniania i skalowania. Jeśli zakres adresów IP zostanie ustawiony tak, aby obsługiwał tylko określoną liczbę węzłów, nie można uaktualnić ani skalować klastra.
>
> - Podczas **uaktualniania** klastra AKS nowy węzeł jest wdrażany w klastrze. Usługi i obciążenia zaczynają działać w nowym węźle, a starszy węzeł jest usuwany z klastra. Ten proces uaktualniania stopniowego wymaga co najmniej jednego dodatkowego bloku adresów IP, który ma być dostępny. Liczba węzłów `n + 1`jest wtedy .
>   - Ta uwaga jest szczególnie ważna podczas korzystania z pul węzłów systemu Windows Server (obecnie w wersji zapoznawczej w usłudze AKS). Węzły systemu Windows Server w systemie AKS nie stosują automatycznie aktualizacji systemu Windows, zamiast tego należy przeprowadzić uaktualnienie w puli węzłów. To uaktualnienie wdraża nowe węzły z najnowszym obrazem węzła podstawowego systemu Windows Server 2019 i poprawkami zabezpieczeń. Aby uzyskać więcej informacji na temat uaktualniania puli węzłów systemu Windows Server, zobacz [Uaktualnianie puli węzłów w systemie AKS][nodepool-upgrade].
>
> - Podczas **skalowania** klastra AKS nowy węzeł jest wdrażany w klastrze. Usługi i obciążenia zaczynają działać w nowym węźle. Zakres adresów IP musi uwzględniać sposób skalowania w górę liczby węzłów i zasobników obsługiwanych przez klaster. Jeden dodatkowy węzeł dla operacji uaktualniania powinny być również uwzględnione. Liczba węzłów `n + number-of-additional-scaled-nodes-you-anticipate + 1`jest wtedy .

Jeśli oczekujesz, że węzły do uruchomienia maksymalnej liczby zasobników i regularnie zniszczyć i wdrożyć zasobników, należy również uwzględnić w niektórych dodatkowych adresów IP na węzeł. Te dodatkowe adresy IP wziąć pod uwagę może potrwać kilka sekund dla usługi do usunięcia i adres IP wydany dla nowej usługi do wdrożenia i uzyskać adres.

Plan adresu IP dla klastra AKS składa się z sieci wirtualnej, co najmniej jednej podsieci dla węzłów i zasobników oraz zakresu adresów usługi Kubernetes.

| Zakres adresów / zasób platformy Azure | Limity i rozmiary |
| --------- | ------------- |
| Sieć wirtualna | Sieć wirtualna platformy Azure może być tak duża, jak /8, ale jest ograniczona do 65 536 skonfigurowanych adresów IP. |
| Podsieć | Musi być wystarczająco duży, aby pomieścić węzły, zasobników i wszystkie zasoby platformy Kubernetes i platformy Azure, które mogą być aprowizacji w klastrze. Na przykład jeśli wdrożysz wewnętrzny moduł równoważenia obciążenia platformy Azure, jego front-end adresy IP są przydzielane z podsieci klastra, a nie publicznych adresów IP. Rozmiar podsieci należy również wziąć pod uwagę operacje uaktualniania lub przyszłych potrzeb skalowania.<p />Aby obliczyć *minimalny* rozmiar podsieci, w tym dodatkowy węzeł dla operacji uaktualniania:`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Przykład dla klastra 50 węzłów: `(51) + (51  * 30 (default)) = 1,581` (/21 lub większy)<p/>Przykład dla klastra 50 węzłów, który zawiera również aprowizowanie w celu skalowania dodatkowych 10 węzłów: `(61) + (61 * 30 (default)) = 1,891` (/21 lub większy)<p>Jeśli podczas tworzenia klastra nie określisz maksymalnej liczby zasobników na węzeł, maksymalna liczba zasobników na węzeł jest ustawiona na *30*. Minimalna wymagana liczba adresów IP jest oparta na tej wartości. Jeśli minimalne wymagania dotyczące adresu IP są obliczane na innej wartości maksymalnej, zobacz jak [skonfigurować maksymalną liczbę zasobników na węzeł,](#configure-maximum---new-clusters) aby ustawić tę wartość podczas wdrażania klastra. |
| Zakres adresów usługi Kubernetes | Ten zakres nie powinien być używany przez żaden element sieci na tej sieci wirtualnej lub podłączony do niej. Adres usługi CIDR musi być mniejszy niż /12. Ten zakres można ponownie wykorzystać w różnych klastrach usługi AKS. |
| Adres IP usługi DNS platformy Kubernetes | Adres IP w zakresie adresów usługi Kubernetes, który będzie używany przez odnajdowanie usługi klastrowania (kube-dns). Nie używaj pierwszego adresu IP w zakresie adresów, takiego jak .1. Pierwszy adres w zakresie podsieci jest używany dla adresu *kubernetes.default.svc.cluster.local.* |
| Adres mostka platformy Docker | Adres mostka platformy Docker to domyślny adres sieciowy mostka *docker0*, który jest obecny we wszystkich instalacjach platformy Docker. Gdy *mostek docker0* nie jest używany przez klastry AKS lub samych zasobników, należy ustawić ten adres, aby nadal obsługiwać scenariusze, takie jak *kompilacja platformy docker* w klastrze AKS. Wymagane jest wybranie cidr dla adresu sieciowego mostka platformy Docker, ponieważ w przeciwnym razie docker automatycznie wybierze podsieć, która może kolidować z innymi CIDR. Należy wybrać przestrzeń adresową, która nie zderza się z pozostałymi cidrami w sieciach, w tym z usługą CIDR klastra i cidrem zasobnym. Wartość domyślna 172.17.0.1/16. Ten zakres można ponownie wykorzystać w różnych klastrach usługi AKS. |

## <a name="maximum-pods-per-node"></a>Maksymalna liczba zasobników na węzeł

Maksymalna liczba zasobników na węzeł w klastrze AKS wynosi 250. *Domyślna* maksymalna liczba zasobników na węzeł różni się między *siecią kubenet* i *Azure CNI* oraz metodą wdrażania klastra.

| Metoda wdrażania | Domyślna wartość Kubenet | Domyślne cni platformy Azure | Konfigurowalne podczas wdrażania |
| -- | :--: | :--: | -- |
| Interfejs wiersza polecenia platformy Azure | 110 | 30 | Tak (do 250) |
| Szablon usługi Resource Manager | 110 | 30 | Tak (do 250) |
| Portal | 110 | 30 | Nie |

### <a name="configure-maximum---new-clusters"></a>Konfigurowanie maksymalnej — nowe klastry

Możesz skonfigurować maksymalną liczbę zasobników na węzeł *tylko w czasie wdrażania klastra.* W przypadku wdrażania za pomocą interfejsu wiersza polecenia platformy Azure lub szablonu Menedżera zasobów można ustawić maksymalną wartość zasobników na węzeł na poziomie 250.

Minimalna wartość maksymalnej zasobników na węzeł jest wymuszana, aby zagwarantować miejsce dla zasobników systemowych krytycznych dla kondycji klastra. Minimalna wartość, która może być ustawiona dla maksymalnej zasobników na węzeł wynosi 10, jeśli i tylko wtedy, gdy konfiguracja każdej puli węzłów ma miejsce dla co najmniej 30 zasobników. Na przykład ustawienie maksymalnej zasobników na węzeł na minimum 10 wymaga, aby każda pula poszczególnych węzłów miała co najmniej 3 węzły. To wymaganie ma zastosowanie dla każdej nowej puli węzłów utworzonych, jak również, więc jeśli 10 jest zdefiniowany jako maksymalna zasobników na węzeł każdej kolejnej puli węzłów dodane musi mieć co najmniej 3 węzłów.

| Obsługa sieci | Minimalne | Maksimum |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Okręg wyborczy Kubenet | 10 | 110 |

> [!NOTE]
> Minimalna wartość w powyższej tabeli jest ściśle wymuszana przez usługę AKS. Nie można ustawić wartości maxPods niższej niż minimalna wyświetlana, ponieważ może to uniemożliwić uruchomienie klastra.

* **Narzędzie CLI**platformy `--max-pods` Azure : Określ argument podczas wdrażania klastra za pomocą polecenia [az aks create.][az-aks-create] Maksymalna wartość wynosi 250.
* **Szablon Menedżera zasobów:** Określ `maxPods` właściwość w [obiekcie ManagedClusterAgentPoolProfile] podczas wdrażania klastra z szablonem Menedżera zasobów. Maksymalna wartość wynosi 250.
* **Azure portal:** Nie można zmienić maksymalną liczbę zasobników na węzeł podczas wdrażania klastra za pomocą witryny Azure portal. Klastry sieciowe usługi Azure CNI są ograniczone do 30 zasobników na węzeł podczas wdrażania przy użyciu witryny Azure portal.

### <a name="configure-maximum---existing-clusters"></a>Konfigurowanie maksymalnej — istniejące klastry

Nie można zmienić maksymalnej liczby zasobników na węzeł w istniejącym klastrze usługi AKS. Numer można dostosować tylko podczas początkowego wdrażania klastra.

## <a name="deployment-parameters"></a>Parametry wdrożenia

Podczas tworzenia klastra AKS można skonfigurować następujące parametry dla sieci CNI platformy Azure:

**Sieć wirtualna:** sieć wirtualna, w której chcesz wdrożyć klaster Kubernetes. Jeśli chcesz utworzyć nową sieć wirtualną dla klastra, wybierz pozycję *Utwórz nowy* i wykonaj kroki opisane w sekcji *Tworzenie sieci wirtualnej.* Aby uzyskać informacje na temat limitów i przydziałów dla sieci wirtualnej platformy Azure, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Podsieć**: Podsieć w sieci wirtualnej, w której chcesz wdrożyć klaster. Jeśli chcesz utworzyć nową podsieć w sieci wirtualnej dla klastra, wybierz pozycję *Utwórz nowy* i wykonaj kroki opisane w sekcji *Utwórz podsieć.* W przypadku łączności hybrydowej zakres adresów nie powinien pokrywać się z innymi sieciami wirtualnymi w danym środowisku.

**Zakres adresów usługi Kubernetes:** Jest to zestaw wirtualnych adresów IP, które usługa Kubernetes przypisuje do [usług][services] wewnętrznych w klastrze. Można użyć dowolnego prywatnego zakresu adresów, który spełnia następujące wymagania:

* Nie może znajdować się w zakresie adresów IP sieci wirtualnej klastra
* Nie może pokrywać się z innymi sieciami wirtualnymi, z którymi grupa wirtualna jest równorzędna
* Nie może pokrywać się z lokalnymi usługami IP
* Nie może znajdować się `169.254.0.0/16` `172.30.0.0/16`w `172.31.0.0/16`zakresach , , lub`192.0.2.0/24`

Chociaż jest technicznie możliwe określenie zakresu adresów usługi w tej samej sieci wirtualnej co klaster, nie jest to zalecane. Nieprzewidywalne zachowanie może spowodować, że nakładające się zakresy adresów IP są używane. Aby uzyskać więcej informacji, zobacz sekcję [często zadawanych pytań](#frequently-asked-questions) w tym artykule. Aby uzyskać więcej informacji na temat usług Kubernetes, zobacz [usługi][services] w dokumentacji kubernetes.

**Adres IP usługi DNS kubernetes:** Adres IP usługi DNS klastra. Ten adres musi znajdować się w *zakresie adresów usługi Kubernetes*. Nie używaj pierwszego adresu IP w zakresie adresów, takiego jak .1. Pierwszy adres w zakresie podsieci jest używany dla adresu *kubernetes.default.svc.cluster.local.*

**Adres mostka platformy Docker:** Adres sieciowy mostka platformy Docker reprezentuje domyślny adres *sieciowy mostka docker0* obecny we wszystkich instalacjach platformy Docker. Gdy *mostek docker0* nie jest używany przez klastry AKS lub samych zasobników, należy ustawić ten adres, aby nadal obsługiwać scenariusze, takie jak *kompilacja platformy docker* w klastrze AKS. Wymagane jest wybranie cidr dla adresu sieciowego mostka platformy Docker, ponieważ w przeciwnym razie docker automatycznie wybierze podsieć, która może kolidować z innymi CIDR. Należy wybrać przestrzeń adresową, która nie zderza się z pozostałymi cidrami w sieciach, w tym z usługą CIDR klastra i cidrem zasobnym.

## <a name="configure-networking---cli"></a>Konfigurowanie sieci — interfejs wiersza polecenia

Podczas tworzenia klastra AKS za pomocą interfejsu wiersza polecenia platformy Azure można również skonfigurować sieci Azure CNI. Użyj następujących poleceń, aby utworzyć nowy klaster AKS z włączoną siecią CNI platformy Azure.

Najpierw pobierz identyfikator zasobu podsieci dla istniejącej podsieci, do której zostanie połączony klaster AKS:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Polecenie [az aks create][az-aks-create] `--network-plugin azure` z argumentem służy do tworzenia klastra z zaawansowaną siecią. Zaktualizuj `--vnet-subnet-id` wartość za pomocą identyfikatora podsieci zebranego w poprzednim kroku:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Konfigurowanie sieci — portal

Poniższy zrzut ekranu z witryny Azure Portal przedstawia przykład konfigurowania tych ustawień podczas tworzenia klastra usługi AKS:

![Zaawansowana konfiguracja sieci w witrynie Azure portal][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Poniższe pytania i odpowiedzi dotyczą konfiguracji **sieciowej usługi Azure CNI.**

* *Czy można wdrażać maszyny wirtualne w podsieci klastra?*

  Nie. Wdrażanie maszyn wirtualnych w podsieci używanej przez klaster usługi Kubernetes nie jest obsługiwane. Maszyny wirtualne mogą być wdrażane w tej samej sieci wirtualnej, ale w innej podsieci.

* *Czy mogę skonfigurować zasady sieci dla zasobników?*

  Tak, zasady sieciowe Kubernetes są dostępne w u. Aby rozpocząć, zobacz [Zabezpieczanie ruchu między zasobnikami przy użyciu zasad sieciowych w uzywce AKS][network-policy].

* *Czy maksymalna liczba zasobników można wdrożyć w węźle konfigurowalnym?*

  Tak, podczas wdrażania klastra z interfejsu wiersza polecenia platformy Azure lub szablonu Menedżera zasobów. Zobacz [Maksymalna liczba zasobników na węzeł](#maximum-pods-per-node).

  Nie można zmienić maksymalnej liczby zasobników na węzeł w istniejącym klastrze.

* *Jak skonfigurować dodatkowe właściwości podsieci utworzonej podczas tworzenia klastra usługi AKS? Na przykład punkty końcowe usługi.*

  Pełną listę właściwości sieci wirtualnej i podsieci utworzonych podczas tworzenia klastra AKS można skonfigurować na standardowej stronie konfiguracji sieci wirtualnej w witrynie Azure portal.

* *Czy mogę używać innej podsieci w sieci wirtualnej klastra dla* **zakresu adresów usługi Kubernetes?**

  Nie jest to zalecane, ale ta konfiguracja jest możliwa. Zakres adresów usług to zestaw wirtualnych adresów IP (VIP), który program Kubernetes przypisuje do usług wewnętrznych w klastrze. Usługa Azure Networking nie ma wglądu w zakres adresów IP usługi klastra Kubernetes. Ze względu na brak wglądu w zakres adresów usługi klastra, można później utworzyć nową podsieć w sieci wirtualnej klastra, która pokrywa się z zakresem adresów usługi. Jeśli wystąpi takie nakładanie się, Kubernetes można przypisać usługę IP, który jest już używany przez inny zasób w podsieci, powodując nieprzewidywalne zachowanie lub błędy. Upewniając się, że używasz zakresu adresów poza siecią wirtualną klastra, można uniknąć tego ryzyka nakładania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sieci w aks w następujących artykułach:

- [Używanie statycznego adresu IP za pomocą modułu równoważenia obciążenia usługi Azure Kubernetes Service (AKS)](static-ip.md)
- [Użyj wewnętrznego modułu równoważenia obciążenia z usługą Azure Container Service (AKS)](internal-lb.md)

- [Tworzenie podstawowego kontrolera transferu danych przychodzących z zewnętrzną łącznością sieciową][aks-ingress-basic]
- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- [Tworzenie kontrolera transferu danych przychodzących z dynamicznym publicznym adresem IP i konfigurowanie szyfrowania let's do automatycznego generowania certyfikatów TLS][aks-ingress-tls]
- [Tworzenie kontrolera transferu danych przychodzących ze statycznym publicznym adresem IP i konfigurowanie szyfrowania let's do automatycznego generowania certyfikatów TLS][aks-ingress-static-tls]

### <a name="aks-engine"></a>Silnik AKS

[Aparat usług Azure Kubernetes (Aparat AKS)][aks-engine] to projekt typu open source, który generuje szablony usługi Azure Resource Manager, których można używać do wdrażania klastrów kubernetes na platformie Azure.

Klastry kubernetes utworzone za pomocą usługi AKS Engine obsługują [wtyczki kubenet][kubenet] i [Azure CNI.][cni-networking] W związku z tym oba scenariusze sieciowe są obsługiwane przez aparat AKS.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
