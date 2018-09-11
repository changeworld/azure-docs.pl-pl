---
title: Konfiguracja sieci w usłudze Azure Kubernetes Service (AKS)
description: Więcej informacji o konfiguracji sieci podstawowe i Zaawansowane w usłudze Azure Kubernetes Service (AKS).
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/31/2018
ms.author: marsma
ms.openlocfilehash: 16349af5932987cc0db4295355a0365c8579fcbf
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44345744"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Konfiguracja sieci w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia klastra usługi Azure Kubernetes Service (AKS), możesz wybrać spośród dwóch opcji sieci: **podstawowe** lub **zaawansowane**.

## <a name="basic-networking"></a>Podstawowe operacje sieciowe

**Podstawowe** sieci — opcja jest domyślnie skonfigurowany do tworzenia klastra AKS. Konfigurację sieci klastra i jego zasobników jest całkowicie zarządzana przez platformę Azure i jest odpowiednia w przypadku wdrożeń, które nie wymagają niestandardowej konfiguracji sieci wirtualnej. Nie masz kontrolę nad konfiguracją sieci, takie jak podsieci lub adres IP zakresów przypisane do klastra, po wybraniu podstawowe operacje sieciowe.

Węzły w klastrze AKS skonfigurowany dla podstawowych sieci [wtyczki kubenet] [ kubenet] wtyczka platformy Kubernetes.

## <a name="advanced-networking"></a>Zaawansowane funkcje sieciowe

**Zaawansowane** sieci umieszcza zasobników w usługi Azure Virtual Network (VNet), można skonfigurować, dostarczając im automatyczne połączenie z zasobami sieci wirtualnej i integracji bogaty zbiór możliwości tę ofertę sieci wirtualnych. Zaawansowane funkcje sieciowe jest dostępna podczas wdrażania usługi AKS klastrów przy użyciu [witryny Azure portal][portal], wiersza polecenia platformy Azure lub przy użyciu szablonu usługi Resource Manager.

Węzły w klastrze AKS skonfigurowane dla zaawansowanych sieci [wtyczki Azure Container Networking interfejsu (CNI)] [ cni-networking] wtyczka platformy Kubernetes.

![Diagram przedstawiający dwa węzły o mostki łączenia każdego do pojedynczej sieci wirtualnej platformy Azure][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>Zaawansowane funkcje sieciowe

Zaawansowane funkcje sieciowe zapewnia następujące korzyści:

* Wdrażanie klastra usługi AKS w istniejącej sieci wirtualnej lub utworzyć nowe sieci wirtualnej i podsieci dla klastra.
* Każdy zasobnik w klastrze ma przypisany adres IP w sieci wirtualnej i może komunikować się bezpośrednio z innymi zasobników w klastrze i innych węzłów w sieci wirtualnej.
* Zasobnik można połączyć z innymi usługami w wirtualnej sieci równorzędnej i sieciami lokalnymi za pośrednictwem usługi ExpressRoute i lokacja lokacja (S2S) połączenia sieci VPN. Zasobniki są również dostępne ze środowiska lokalnego.
* Udostępnianie zewnętrznie lub wewnętrznie usługa Kubernetes za pomocą usługi Azure Load Balancer. Także funkcja podstawowe operacje sieciowe.
* Zasobników w podsieci, które mają włączone punkty końcowe usługi można bezpiecznie łączyć się z usługami platformy Azure, na przykład Azure Storage i bazą danych SQL.
* Użyj trasy zdefiniowane przez użytkownika (UDR) do kierowania ruchu z zasobników do wirtualnego urządzenia sieciowego.
* Zasobników dostęp do zasobów w publicznym Internecie. Także funkcja podstawowe operacje sieciowe.

## <a name="advanced-networking-prerequisites"></a>Zaawansowane sieci wymagań wstępnych

* Sieć wirtualną dla klastra usługi AKS muszą zezwalać na łączność z Internetem ruchu wychodzącego.
* Nie należy tworzyć więcej niż jednego klastra AKS w tej samej podsieci.
* Nie wolno korzystać z klastrów AKS `169.254.0.0/16`, `172.30.0.0/16`, lub `172.31.0.0/16` dla rozwiązania Kubernetes usługi zakresu adresów.
* Jednostki usługi używany przez klaster AKS musi mieć co najmniej [Współautor sieci](../role-based-access-control/built-in-roles.md#network-contributor) uprawnień w podsieci w ramach sieci wirtualnej. Jeśli chcesz zdefiniować [roli niestandardowej](../role-based-access-control/custom-roles.md) zamiast wbudowana rola Współautor sieci, wymagane są następujące uprawnienia:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>Planowanie adresowania IP dla klastra

Klastrach skonfigurowanych przy użyciu zaawansowane funkcje sieciowe wymagają dodatkowego planowania. Rozmiar Twojej sieci wirtualnej i jej podsieci musi obsłużyć zarówno liczbę zasobników, które mają być uruchamiane, jak i liczby węzłów klastra.

Adresy IP dla zasobników i węzły klastra są przypisywane z określonej podsieci w sieci wirtualnej. Każdy węzeł jest skonfigurowany za pomocą podstawowego adresu IP, czyli adres IP węzła i 30 dodatkowych adresów IP wstępnie skonfigurowane przez wtyczki Azure CNI, które są przypisane do zasobników zaplanowane do węzła. Gdy możesz skalować w klastrze, każdy węzeł podobnie jest skonfigurowany z adresów IP z podsieci.

Plan adresów IP dla klastra usługi AKS, który składa się z wirtualnej sieci, co najmniej jedną podsieć dla węzłów i zasobników i zakres adresów usługi Kubernetes.

| Zakres adresów / Azure zasobów | Limity i zmianę rozmiaru |
| --------- | ------------- |
| Sieć wirtualna | Sieć wirtualna platformy Azure może być tak duże jak /8, ale jest ograniczona do 65 536 skonfigurowanych adresów IP. |
| Podsieć | Musi być wystarczająco duży, aby pomieścić węzłów, zasobników i wszystkich Kubernetes i Azure zasoby, które mogą być udostępniane w klastrze. Na przykład w przypadku wdrożenia wewnętrznego modułu równoważenia obciążenia platformy Azure, jego frontonu adresy IP są przydzielane z podsieci klastra nie publicznych adresów IP. <p/>Aby obliczyć *minimalne* rozmiar podsieci: `(number of nodes) + (number of nodes * pods per node)` <p/>Przykład klaster z węzłami 50: `(50) + (50 * 30) = 1,550` (/ 21 lub większej) |
| Zakres adresów usługi Kubernetes | Ten zakres nie należy używanych przez dowolny element sieci lub połączone z tą siecią wirtualną. Usługa adres CIDR musi być mniejszy niż /12. |
| Adres IP usługi DNS platformy Kubernetes | Adres IP w ramach rozwiązania Kubernetes usługi zakres adresów, który będzie używany przez odnajdywanie usługi klastrowania (klastra kubernetes w usłudze dns). |
| Adres mostka platformy docker | Adres IP (w notacji CIDR), używane jako mostka platformy Docker adresu IP w węzłach. Domyślnie 172.17.0.1/16. |

## <a name="maximum-pods-per-node"></a>Maksymalna zasobników na węzeł

Domyślne maksymalną liczbę zasobników w każdym węźle w klastrze AKS waha się między podstawowe i zaawansowane sieci i metody wdrażania klastra.

### <a name="default-maximum"></a>Domyślna wartość maksymalna

Są to *domyślne* maksymalne wartości podczas wdrażania usługi AKS klastra bez określania maksymalna liczba zasobników w czasie wdrażania:

| Metoda wdrażania | Podstawowa | Advanced | Możliwość konfiguracji podczas wdrażania |
| -- | :--: | :--: | -- |
| Interfejs wiersza polecenia platformy Azure | 110 | 30 | Yes |
| Szablon usługi Resource Manager | 110 | 30 | Yes |
| Portal | 110 | 30 | Nie |

### <a name="configure-maximum---new-clusters"></a>Konfigurowanie maksimum – nowych klastrów

Aby określić różne maksymalna liczba zasobników w każdym węźle, w przypadku wdrażania klastra usługi AKS:

* **Wiersza polecenia platformy Azure**: Określ `--max-pods` argumentu, w przypadku wdrażania klastra za pomocą [tworzenie az aks] [ az-aks-create] polecenia.
* **Szablon usługi Resource Manager**: Określ `maxPods` właściwość [ManagedClusterAgentPoolProfile] obiektu w przypadku wdrażania klastra za pomocą szablonu usługi Resource Manager.
* **Witryna Azure portal**: nie można zmodyfikować maksymalna liczba zasobników w każdym węźle, w przypadku wdrażania klastra za pomocą witryny Azure portal. Zaawansowane sieci klastrów mogą zawierać maksymalnie 30 zasobniki w każdym węźle po wdrożeniu w witrynie Azure portal.

### <a name="configure-maximum---existing-clusters"></a>Konfigurowanie maksimum – istniejących klastrów

Nie można zmienić maksymalną zasobników w każdym węźle w istniejącym klastrze usługi AKS. Można dostosować liczbę, tylko wtedy, gdy początkowo wdrożenia klastra.

## <a name="deployment-parameters"></a>Parametry wdrożenia

Podczas tworzenia klastra usługi AKS, można skonfigurować, aby uzyskać zaawansowane funkcje sieciowe są następujące parametry:

**Sieć wirtualna**: sieci wirtualnej, w której chcesz wdrożyć klaster usługi Kubernetes. Aby utworzyć nową sieć wirtualną dla klastra, należy zaznaczyć *Utwórz nową* i postępuj zgodnie z instrukcjami w *Utwórz sieć wirtualną* sekcji. Aby uzyskać informacji na temat limity i przydziały dla sieci wirtualnej platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Podsieci**: podsieci w sieci wirtualnej, której chcesz wdrożyć w klastrze. Jeśli chcesz utworzyć nową podsieć w sieci wirtualnej dla klastra, wybierz opcję *Utwórz nową* i postępuj zgodnie z instrukcjami w *Utwórz podsieć* sekcji.

**Zakres adresów usługi platformy Kubernetes**: jest to zestaw wirtualnych adresów IP, który przypisuje Kubernetes [usług] [ services] w klastrze. Można użyć dowolnego zakresu prywatnego adresu, który spełnia następujące wymagania:

* Nie może być w zakresie adresów IP sieci wirtualnej klastra
* Nie może pokrywać z innymi sieciami wirtualnymi, z którymi łączy równorzędnie sieć wirtualna klastra
* Nie nakładać się na wszystkie adresy IP w środowisku lokalnym
* Nie może być z zakresu `169.254.0.0/16`, `172.30.0.0/16`, lub `172.31.0.0/16`

Chociaż jest technicznie możliwe określić zakres adresów usługi, w ramach tej samej sieci wirtualnej co klaster, to nie jest to zalecane. Jeśli nakładających się zakresów adresów IP są używane, może spowodować nieprzewidywalne zachowanie. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania](#frequently-asked-questions) dalszej części tego artykułu. Aby uzyskać więcej informacji na temat usługi Kubernetes, zobacz [usług] [ services] w dokumentacji platformy Kubernetes.

**Adres IP usługi DNS platformy Kubernetes**: adres IP usługi DNS klastra. Adres ten musi być *zakresu adresów usługi platformy Kubernetes*.

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
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
