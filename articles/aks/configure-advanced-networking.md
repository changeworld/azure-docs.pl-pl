---
title: Zaawansowane funkcje sieciowe skonfigurowane w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skonfigurować zaawansowane sieci w usłudze Azure Kubernetes Service (AKS), takich jak wdrażanie klastra usługi AKS w istniejącej sieci wirtualnej i podsieci.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/11/2018
ms.author: iainfou
ms.openlocfilehash: a46989ea197166065b4ca482200a0d30e1def7c9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723095"
---
# <a name="configure-advanced-networking-in-azure-kubernetes-service-aks"></a>Skonfiguruj zaawansowane funkcje sieciowe w usłudze Azure Kubernetes Service (AKS)

Domyślnie AKS klastrów użyj *podstawowe* networking, które tworzy i konfiguruje sieć wirtualną i podsieć do użycia z klastrem. Aby uzyskać dodatkową kontrolę te opcje sieciowe, takie jak zakresy adresów IP, należy użyć *zaawansowane* sieci. Z zaawansowany siecią można również utworzyć klaster usługi AKS w istniejącej sieci wirtualnej i podsieci. To istniejąca sieć wirtualna często zapewnia łączność z siecią lokalną przy użyciu usługi Azure ExpressRoute lub sieci VPN typu lokacja-lokacja.

W tym artykule dowiesz się, jak używać zaawansowane funkcje sieciowe do tworzenia i używania sieci wirtualnej z klastra usługi AKS. Aby uzyskać więcej informacji na temat sieci, zobacz [sieci pojęcia dla platformy Kubernetes i AKS][aks-network-concepts].

## <a name="prerequisites"></a>Wymagania wstępne

* Sieć wirtualną dla klastra usługi AKS muszą zezwalać na łączność z Internetem ruchu wychodzącego.
* Nie należy tworzyć więcej niż jednego klastra AKS w tej samej podsieci.
* Nie wolno korzystać z klastrów AKS `169.254.0.0/16`, `172.30.0.0/16`, lub `172.31.0.0/16` dla rozwiązania Kubernetes usługi zakresu adresów.
* Jednostki usługi używany przez klaster AKS musi mieć co najmniej [Współautor sieci](../role-based-access-control/built-in-roles.md#network-contributor) uprawnień w podsieci w ramach sieci wirtualnej. Jeśli chcesz zdefiniować [roli niestandardowej](../role-based-access-control/custom-roles.md) zamiast wbudowana rola Współautor sieci, wymagane są następujące uprawnienia:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>Planowanie adresowania IP dla klastra

Klastrach skonfigurowanych przy użyciu zaawansowane funkcje sieciowe wymagają dodatkowego planowania. Rozmiar Twojej sieci wirtualnej i jej podsieci należy uwzględnić liczbę zasobników, który ma zostać uruchomiona i liczby węzłów klastra.

Adresy IP dla zasobników i węzły klastra są przypisywane z określonej podsieci w sieci wirtualnej. Każdy węzeł jest skonfigurowany przy użyciu podstawowego adresu IP. Domyślnie 30 dodatkowe adresy IP są wstępnie skonfigurowane przez wtyczki Azure CNI, które są przypisane do zasobników zaplanowane w węźle. Gdy możesz skalować w klastrze, każdy węzeł podobnie jest skonfigurowany z adresów IP z podsieci. Można również wyświetlić [maksymalna zasobników w każdym węźle](#maximum-pods-per-node).

> [!IMPORTANT]
> Liczba adresów IP wymagana powinien zawierać uwagi dotyczące uaktualniania i operacji skalowania. Jeśli ustawiono zakres adresów IP obsługują tylko stała liczba węzłów, nie można uaktualnić lub skalowania klastra.
>
> - Po użytkownik **uaktualnienia** klastra usługi AKS, nowy węzeł jest wdrażana w klastrze. Usługi i obciążenia rozpoczyna się w nowym węźle, a starsze węzeł zostanie usunięty z klastra. Ten proces uaktualnienia stopniowego wymaga co najmniej jeden blok dodatkowych adresów IP, które mają być dostępne. Liczba węzłów jest następnie `n + 1`.
>
> - Po użytkownik **skalowania** wdrożono klaster AKS nowego węzła do klastra. Usługi i obciążenia rozpoczyna się w nowym węźle. Zakres adresów IP należy brać pod zagadnienia, jak możesz skalować liczbę węzłów i zasobników, które klaster może obsługiwać. Jeden dodatkowy węzeł dla operacji uaktualniania również powinny być włączone. Liczba węzłów jest następnie `n + number-of-additional-scaled-nodes-you-anticipate + 1`.

Jeśli oczekujesz, że węzły do uruchamiania maksymalna liczba zasobników i regularnie zniszczyć i wdrażania zasobników, możesz należy również wziąć pod uwagę pewne dodatkowe adresy IP na węzeł. Te dodatkowe adresy IP wziąć pod uwagę, który może potrwać kilka sekund dla usługi do usunięcia, a adres IP jest zwalniany dla nowej usługi do wdrożenia i uzyskać adres.

Plan adresów IP dla klastra usługi AKS, który składa się z wirtualnej sieci, co najmniej jedną podsieć dla węzłów i zasobników i zakres adresów usługi Kubernetes.

| Zakres adresów / Azure zasobów | Limity i zmianę rozmiaru |
| --------- | ------------- |
| Sieć wirtualna | Sieć wirtualna platformy Azure może być tak duże jak /8, ale jest ograniczona do 65 536 skonfigurowanych adresów IP. |
| Podsieć | Musi być wystarczająco duży, aby pomieścić węzłów, zasobników i wszystkich Kubernetes i Azure zasoby, które mogą być udostępniane w klastrze. Na przykład w przypadku wdrożenia wewnętrznego modułu równoważenia obciążenia platformy Azure, jego frontonu adresy IP są przydzielane z podsieci klastra nie publicznych adresów IP. Rozmiar podsieci powinien uwzględniać również operacje uaktualniania konta lub przyszłe zapotrzebowanie w zakresie skalowania.<p />Aby obliczyć *minimalne* rozmiar podsieci, w tym dodatkowym węźle dla operacji uaktualniania: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Przykład klaster z węzłami 50: `(51) + (51  * 30 (default)) = 1,581` (/ 21 lub większej)<p/>Przykład klastra 50 węzeł, który również uwzględnia aprowizacja, skalowanie w górę dodatkowe 10 węzłów: `(61) + (61 * 30 (default)) = 1,891` (/ 21 lub większej)<p>Jeśli nie określisz maksymalna liczba zasobników w każdym węźle podczas tworzenia klastra, maksymalna liczba zasobników w każdym węźle jest równa *30*. Minimalna liczba adresów IP, wymagane jest na podstawie tej wartości. Jeśli obliczane minimalne wymagania dotyczące adresów IP, na inną wartość maksymalna, zobacz [jak skonfigurować maksymalną liczbę zasobników w każdym węźle](#configure-maximum---new-clusters) ustawić tę wartość podczas wdrażania klastra. |
| Zakres adresów usługi Kubernetes | Ten zakres nie należy używanych przez dowolny element sieci lub połączone z tą siecią wirtualną. Usługa adres CIDR musi być mniejszy niż /12. |
| Adres IP usługi DNS platformy Kubernetes | Adres IP w ramach rozwiązania Kubernetes usługi zakres adresów, który będzie używany przez odnajdywanie usługi klastrowania (klastra kubernetes w usłudze dns). Nie używaj pierwszego adresu IP z zakresu adresów, takich jak.1. Pierwszy adres z zakresu podsieci jest używany do *kubernetes.default.svc.cluster.local* adresu. |
| Adres mostka platformy docker | Adres IP (w notacji CIDR), używane jako mostka platformy Docker adresu IP w węzłach. Domyślnie 172.17.0.1/16. |

## <a name="maximum-pods-per-node"></a>Maksymalna zasobników na węzeł

Maksymalna liczba zasobników w każdym węźle w klastrze AKS wynosi 110. *Domyślne* maksymalna liczba zasobników w każdym węźle, który waha się między podstawowe i zaawansowane sieci i metody wdrażania klastra.

| Metoda wdrażania | Podstawowe domyślne | Zaawansowane domyślne | Możliwość konfiguracji podczas wdrażania |
| -- | :--: | :--: | -- |
| Interfejs wiersza polecenia platformy Azure | 110 | 30 | Tak (maksymalnie 110) |
| Szablon usługi Resource Manager | 110 | 30 | Tak (maksymalnie 110) |
| Portal | 110 | 30 | Nie |

### <a name="configure-maximum---new-clusters"></a>Konfigurowanie maksimum – nowych klastrów

Możesz skonfigurować maksymalną liczbę zasobników w każdym węźle *tylko w czasie wdrażania klastra*. W przypadku wdrożenia przy użyciu wiersza polecenia platformy Azure lub przy użyciu szablonu usługi Resource Manager, można ustawić maksymalny zasobników na możliwie jak 110 wartość węzła.

* **Wiersza polecenia platformy Azure**: Określ `--max-pods` argumentu, w przypadku wdrażania klastra za pomocą [tworzenie az aks] [ az-aks-create] polecenia. Maksymalna wartość wynosi 110.
* **Szablon usługi Resource Manager**: Określ `maxPods` właściwość [ManagedClusterAgentPoolProfile] obiektu w przypadku wdrażania klastra za pomocą szablonu usługi Resource Manager. Maksymalna wartość wynosi 110.
* **Witryna Azure portal**: nie można zmienić maksymalną liczbę zasobników w każdym węźle, w przypadku wdrażania klastra za pomocą witryny Azure portal. Zaawansowane sieci klastrów są ograniczone do 30 zasobniki w każdym węźle, gdy wdrażasz za pomocą witryny Azure portal.

### <a name="configure-maximum---existing-clusters"></a>Konfigurowanie maksimum – istniejących klastrów

Nie można zmienić maksymalną zasobników w każdym węźle w istniejącym klastrze usługi AKS. Można dostosować liczbę, tylko wtedy, gdy początkowo wdrożenia klastra.

## <a name="deployment-parameters"></a>Parametry wdrożenia

Podczas tworzenia klastra usługi AKS, można skonfigurować, aby uzyskać zaawansowane funkcje sieciowe są następujące parametry:

**Sieć wirtualna**: sieci wirtualnej, w której chcesz wdrożyć klaster usługi Kubernetes. Aby utworzyć nową sieć wirtualną dla klastra, należy zaznaczyć *Utwórz nową* i postępuj zgodnie z instrukcjami w *Utwórz sieć wirtualną* sekcji. Aby uzyskać informacji na temat limity i przydziały dla sieci wirtualnej platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Podsieci**: podsieci w sieci wirtualnej, której chcesz wdrożyć w klastrze. Jeśli chcesz utworzyć nową podsieć w sieci wirtualnej dla klastra, wybierz opcję *Utwórz nową* i postępuj zgodnie z instrukcjami w *Utwórz podsieć* sekcji. Łączność hybrydowa zakres adresów nie mogą nakładać się z innymi sieciami wirtualnymi w danym środowisku.

**Zakres adresów usługi platformy Kubernetes**: jest to zestaw wirtualnych adresów IP, który przypisuje Kubernetes [usług] [ services] w klastrze. Można użyć dowolnego zakresu prywatnego adresu, który spełnia następujące wymagania:

* Nie może być w zakresie adresów IP sieci wirtualnej klastra
* Nie może pokrywać z innymi sieciami wirtualnymi, z którymi łączy równorzędnie sieć wirtualna klastra
* Nie nakładać się na wszystkie adresy IP w środowisku lokalnym
* Nie może być z zakresu `169.254.0.0/16`, `172.30.0.0/16`, lub `172.31.0.0/16`

Chociaż jest technicznie możliwe określić zakres adresów usługi, w ramach tej samej sieci wirtualnej co klaster, to nie jest to zalecane. Jeśli nakładających się zakresów adresów IP są używane, może spowodować nieprzewidywalne zachowanie. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania](#frequently-asked-questions) dalszej części tego artykułu. Aby uzyskać więcej informacji na temat usługi Kubernetes, zobacz [usług] [ services] w dokumentacji platformy Kubernetes.

**Adres IP usługi DNS platformy Kubernetes**: adres IP usługi DNS klastra. Adres ten musi być *zakresu adresów usługi platformy Kubernetes*. Nie używaj pierwszego adresu IP z zakresu adresów, takich jak.1. Pierwszy adres z zakresu podsieci jest używany do *kubernetes.default.svc.cluster.local* adresu.

**Adres mostka platformy docker**: adres IP i maska sieci można przypisać do mostka platformy Docker. Ten adres IP nie musi być w zakresie adresów IP sieci wirtualnej klastra.

## <a name="configure-networking---cli"></a>Konfigurowanie sieci — interfejs wiersza polecenia

Podczas tworzenia klastra usługi AKS przy użyciu wiersza polecenia platformy Azure, można również skonfigurować zaawansowane funkcje sieciowe. Użyj następujących poleceń, aby utworzyć nowy klaster AKS za pomocą zaawansowanych funkcji sieciowych włączone.

Najpierw Pobierz identyfikator zasobu podsieci dla podsieci istniejących, do której zostaną dołączone klaster AKS:

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Użyj [tworzenie az aks] [ az-aks-create] polecenia `--network-plugin azure` argumentu, aby utworzyć klaster z zaawansowany siecią. Aktualizacja `--vnet-subnet-id` wartość Identyfikatora podsieci zebranych w poprzednim kroku:

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Konfigurowanie sieci — portal

Poniższy zrzut ekranu z witryny Azure portal przedstawia przykład Konfigurowanie tych ustawień podczas tworzenia klastra AKS:

![Zaawansowana konfiguracja sieci w witrynie Azure portal][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Zestaw poniższych pytań i odpowiedzi dotyczą **zaawansowane** konfiguracji sieci.

* *W mojej podsieci klastra można wdrożyć maszyny wirtualne?*

  Nie. Wdrażanie maszyn wirtualnych w podsieci używane przez klaster Kubernetes nie jest obsługiwane. Maszyny wirtualne można wdrażać w tej samej sieci wirtualnej, ale w innej podsieci.

* *Można skonfigurować zasad sieciowych na zasobnik?*

  Nie. Zasady sieciowe na zasobnik są obecnie obsługiwane.

* *Maksymalna liczba zasobników jest możliwy do wdrożenia na węzeł można skonfigurować?*

  Tak, w przypadku wdrażania klastra przy użyciu wiersza polecenia platformy Azure lub w szablonie usługi Resource Manager. Zobacz [zasobników maksymalnie na węzeł](#maximum-pods-per-node).

  Nie można zmienić maksymalną liczbę zasobników w każdym węźle w istniejącym klastrze.

* *Jak skonfigurować dodatkowe właściwości dla podsieci, utworzonego podczas tworzenia klastra AKS Na przykład punktów końcowych usługi.*

  Pełną listę właściwości dla sieci wirtualnej i podsieci, które możesz utworzyć podczas tworzenia klastra AKS można skonfigurować na stronie konfiguracji standardowej sieci wirtualnej w witrynie Azure portal.

* *Czy mogę używać innej podsieci w sieci wirtualnej klastra dla* **zakresu adresów usługi platformy Kubernetes**?

  Nie jest to zalecane, ale ta konfiguracja jest możliwe. Zakres adresów usługi to zbiór wirtualnych adresów IP (VIP), który przypisuje usług w klastrze Kubernetes. Sieci platformy Azure ma nie widoczność zakresu adresów IP usługi klastra Kubernetes. Ze względu na brak widoczność zakresu adresów usługi klastra jest możliwość później utworzyć nową podsieć w sieci wirtualnej klastra, która nakłada się na zakres adresów usługi. W przypadku nakładania się Kubernetes można przypisać usługi adres IP, który jest już używany przez inny zasób w podsieci, powodując nieprzewidywalne zachowanie lub awarie. Przez zapewnienie, że używasz zakresu adresów spoza sieci wirtualnej klastra, możesz uniknąć tego ryzyka nakładają się na siebie.

## <a name="next-steps"></a>Kolejne kroki

### <a name="networking-in-aks"></a>Obsługa sieci w usłudze AKS

Dowiedz się więcej na temat sieci w usłudze AKS w następujących artykułach:

- [Używanie statycznego adresu IP z modułem równoważenia obciążenia Azure Kubernetes Service (AKS)](static-ip.md)
- [Użyj wewnętrznego modułu równoważenia obciążenia za pomocą usługi Azure Container Service (AKS)](internal-lb.md)

- [Tworzenie kontrolera podstawowego transferu danych przychodzących za pomocą połączenia z siecią zewnętrzną][aks-ingress-basic]
- [Włączyć dodatek routing aplikacji protokołu HTTP][aks-http-app-routing]
- [Tworzenie kontrolera danych przychodzących, korzystającą z sieci prywatne, wewnętrzne i adres IP][aks-ingress-internal]
- [Tworzenie kontrolera danych przychodzących z dynamicznym publicznym adresem IP i skonfiguruj teraz szyfrowania można automatycznie wygenerować certyfikaty protokołu TLS][aks-ingress-tls]
- [Tworzenie kontrolera danych przychodzących za pomocą statycznego publicznego adresu IP i skonfiguruj teraz szyfrowania można automatycznie wygenerować certyfikaty protokołu TLS][aks-ingress-static-tls]

### <a name="acs-engine"></a>Aparat ACS

[Aparat usługi kontenera platformy Azure (aparat ACS)] [ acs-engine] to projekt typu open source, który generuje szablony usługi Azure Resource Manager umożliwia wdrażanie klastrów z obsługą platformy Docker na platformie Azure. Kubernetes, DC/OS, Swarm tryb i Swarm koordynatorów można wdrożyć przy użyciu aparatu usługi ACS.

Klastry Kubernetes, utworzone za pomocą aparatu usługi ACS obsługują zarówno [wtyczki kubenet] [ kubenet] i [wtyczki Azure CNI] [ cni-networking] wtyczek. W efekcie podstawowe i zaawansowane scenariusze sieciowe są obsługiwane przez aparat ACS.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
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
