---
title: Pojęcia — skalowanie aplikacji w usługach Azure Kubernetes Services (AKS)
description: Dowiedz się więcej o skalowaniu w usłudze Azure Kubernetes Service (AKS), w tym skalowanie w poziomie zasobnika automatycznego, skalowanie automatyczne klastra i łącznika wystąpienia kontenerów platformy Azure.
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 396e5bc31723768ada334dd5043bca724af5e84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595862"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Opcje skalowania aplikacji w usłudze Azure Kubernetes Service 

Podczas uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS) może być konieczne zwiększenie lub zmniejszenie ilości zasobów obliczeniowych. Ponieważ liczba wystąpień aplikacji, które są potrzebne, może ulec zmianie, liczba podstawowych węzłów kubernetes może również ulec zmianie. Może być również konieczne szybkie aprowizycja dużej liczby dodatkowych wystąpień aplikacji.

W tym artykule przedstawiono podstawowe pojęcia, które pomagają skalować aplikacje w uzywki AKS:

- [Ręczne skalowanie](#manually-scale-pods-or-nodes)
- [Skalowanie w poziomie (HPA)](#horizontal-pod-autoscaler)
- [Skalowanie automatyczne klastra](#cluster-autoscaler)
- [Integracja wystąpienia kontenera platformy Azure (ACI) z usługą AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Ręczne skalowanie zasobników lub węzłów

Można ręcznie skalować repliki (zasobników) i węzłów, aby sprawdzić, jak aplikacja reaguje na zmianę dostępnych zasobów i stanu. Ręczne skalowanie zasobów umożliwia również zdefiniowanie określonej ilości zasobów do użycia w celu utrzymania kosztu stałego, takiego jak liczba węzłów. Aby ręcznie skalować, należy zdefiniować liczbę replik lub węzłów. Interfejs API usługi Kubernetes następnie planuje tworzenie dodatkowych zasobników lub opróżniania węzłów na podstawie tej liczby replik lub węzłów.

Podczas skalowania w dół węzłów interfejsu API usługi Kubernetes wywołuje odpowiedni interfejs API obliczeń platformy Azure powiązany z typem obliczeniowym używanym przez klaster. Na przykład dla klastrów zbudowanych na skali maszyny Wirtualnej Ustawia logikę wyboru węzłów do usunięcia jest określana przez interfejs API zestawów skalowania maszyny Wirtualnej. Aby dowiedzieć się więcej o tym, jak węzły są wybierane do usuwania przy skalowaniu w dół, zobacz [często zadawane pytania dotyczące usługi VMSS](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed).

Aby rozpocząć pracę z ręcznym skalowaniem zasobników i węzłów, zobacz [Skalowanie aplikacji w AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Skalowanie w poziomie

Kubernetes używa skalowania automatycznego zasobu poziomego (HPA) do monitorowania zapotrzebowania na zasoby i automatycznego skalowania liczby replik. Domyślnie skalowanie zasobników poziomych sprawdza interfejs API metryki co 30 sekund pod kątem wszelkich wymaganych zmian w liczbie replik. Gdy wymagane są zmiany, liczba replik jest odpowiednio zwiększana lub zmniejszana. Skalowanie zasobników poziomych współpracuje z klastrami AKS, które wdrożyły serwer metryk dla kubernetes 1.8+.

![Skalowanie automatyczne zasobników w poziomie kubernetes](media/concepts-scale/horizontal-pod-autoscaling.png)

Podczas konfigurowania skalowania automatycznego zasobnika poziomego dla danego wdrożenia, należy zdefiniować minimalną i maksymalną liczbę replik, które można uruchomić. Można również zdefiniować metryki do monitorowania i podstawy wszelkie decyzje skalowania na, takich jak użycie procesora CPU.

Aby rozpocząć pracę z poziomym skalowaniem automatycznym zasobnika w UDOS, zobacz [Zasobniki skalowania automatycznego w programie AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Czas odnowienia zdarzeń skalowania

Ponieważ skalowanie automatyczne zasobników w poziomie sprawdza interfejs API metryki co 30 sekund, poprzednie zdarzenia skali mogą nie zostać pomyślnie ukończone przed dokonaniem kolejnego sprawdzenia. To zachowanie może spowodować poziome zasobu skalowania automatycznego, aby zmienić liczbę replik przed poprzednim zdarzeniem skali może odbierać obciążenia aplikacji i zapotrzebowania zasobów, aby odpowiednio dostosować.

Aby zminimalizować te zdarzenia wyścigu, ustawiono wartości czasu odnowienia lub opóźnienia. Te wartości określają, jak długo skalowanie automatyczne zasobnika poziomego musi czekać po zdarzeniu skali, zanim może zostać wyzwolone inne zdarzenie skali. To zachowanie umożliwia nowe licznik replik, aby rozpocząć działanie i Metrics API, aby odzwierciedlić rozproszonego obciążenia. Domyślnie opóźnienie skalowania w górę wynosi 3 minuty, a opóźnienie zdarzeń skalowania w dół wynosi 5 minut

Obecnie nie można dostroić tych wartości czasu odnowienia z wartości domyślnych.

## <a name="cluster-autoscaler"></a>Skalowanie automatyczne klastra

Aby odpowiedzieć na zmieniające się wymagania zasobników, kubernetes ma skalowanie automatyczne klastra, który dostosowuje liczbę węzłów na podstawie żądanych zasobów obliczeniowych w puli węzłów. Domyślnie skalowanie automatyczne klastra sprawdza serwer interfejsu API metryki co 10 sekund pod kątem wszelkich wymaganych zmian w liczbie węzłów. Jeśli skalowanie automatyczne klastra określa, że zmiana jest wymagana, liczba węzłów w klastrze AKS jest odpowiednio zwiększana lub zmniejszana. Skalowanie automatyczne klastra współpracuje z klastrami AKS z włączoną funkcją RBAC, które uruchamiają protokół Kubernetes 1.10.x lub nowszą.

![Skalowanie automatyczne klastra kubernetes](media/concepts-scale/cluster-autoscaler.png)

Skalowanie automatyczne klastra jest zwykle używany obok skalowania automatycznego zasobnika poziomego w poziomie. Po połączeniu skalowanie zasobników w poziomie zwiększa lub zmniejsza liczbę zasobników na podstawie zapotrzebowania na aplikacje, a skalowanie automatyczne klastra dostosowuje liczbę węzłów w razie potrzeby, aby odpowiednio uruchomić te dodatkowe zasobniki.

Aby rozpocząć pracę z skalowaniem automatycznym klastra w uzywniu AKS, zobacz [Skalowanie automatyczne klastra w u usługi AKS][aks-cluster-autoscaler].

### <a name="scale-up-events"></a>Skalowanie zdarzeń w górę

Jeśli węzeł nie ma wystarczających zasobów obliczeniowych do uruchomienia żądanego zasobnika, ten zasobnik nie może przejść przez proces planowania. Zasobnik nie można uruchomić, chyba że dodatkowe zasoby obliczeniowe są dostępne w puli węzłów.

Gdy skalowanie automatyczne klastra powiadamia zasobników, które nie mogą być zaplanowane z powodu ograniczeń zasobów puli węzłów, liczba węzłów w puli węzłów jest zwiększana w celu zapewnienia dodatkowych zasobów obliczeniowych. Gdy te dodatkowe węzły są pomyślnie wdrożone i dostępne do użycia w puli węzłów, zasobników są następnie zaplanowane do uruchomienia na nich.

Jeśli aplikacja musi szybko skalować, niektóre zasobników może pozostać w stanie oczekiwania na zaplanowane, dopóki dodatkowe węzły wdrożone przez skalowanie automatyczne klastra można zaakceptować zaplanowane zasobników. W przypadku aplikacji, które mają wysokie wymagania serii, można skalować za pomocą węzłów wirtualnych i wystąpień kontenera platformy Azure.

### <a name="scale-down-events"></a>Skalowanie w dół zdarzeń

Skalowanie automatyczne klastra monitoruje również stan planowania zasobników dla węzłów, które nie otrzymały ostatnio nowych żądań planowania. Ten scenariusz wskazuje, że pula węzłów ma więcej zasobów obliczeniowych niż są wymagane, a liczba węzłów może zostać zmniejszona.

Węzeł, który przekracza próg, który nie jest już potrzebny przez 10 minut domyślnie jest zaplanowane do usunięcia. W takiej sytuacji zasobników są zaplanowane do uruchomienia na innych węzłów w puli węzłów, a skalowanie automatyczne klastra zmniejsza liczbę węzłów.

Aplikacje mogą wystąpić pewne zakłócenia, jak zasobników są zaplanowane w różnych węzłach, gdy skalowanie automatyczne klastra zmniejsza liczbę węzłów. Aby zminimalizować zakłócenia, należy unikać aplikacji, które używają wystąpienia pojedynczego zasobnika.

## <a name="burst-to-azure-container-instances"></a>Burst do wystąpienia kontenera platformy Azure

Aby szybko skalować klaster AKS, można zintegrować z wystąpieniami kontenerów platformy Azure (ACI). Kubernetes ma wbudowane składniki do skalowania liczby replik i węzłów. Jeśli jednak aplikacja musi szybko skalować, skalowanie automatyczne zasobników poziomych może zaplanować więcej zasobników niż może być dostarczone przez istniejące zasoby obliczeniowe w puli węzłów. Jeśli skonfigurowany, ten scenariusz następnie wyzwoli skalowanie automatyczne klastra do wdrażania dodatkowych węzłów w puli węzłów, ale może upłynąć kilka minut dla tych węzłów pomyślnie aprowizować i zezwolić harmonogramowi Kubernetes na uruchomienie zasobników na nich.

![Kubernetes rozerwać skalowanie do ACI](media/concepts-scale/burst-scaling.png)

ACI umożliwia szybkie wdrażanie wystąpień kontenerów bez dodatkowego obciążenia infrastruktury. Po nawiązaniu połączenia z aksem, ACI staje się zabezpieczone, logiczne rozszerzenie klastra AKS. Składnik [węzłów wirtualnych,][virtual-nodes-cli] który jest oparty na [wirtualnym kubelet,][virtual-kubelet]jest zainstalowany w klastrze AKS, który przedstawia ACI jako wirtualny węzeł Kubernetes. Kubernetes można następnie zaplanować zasobników, które są uruchamiane jako wystąpienia ACI za pośrednictwem węzłów wirtualnych, a nie jako zasobników w węzłach maszyn wirtualnych bezpośrednio w klastrze AKS. Węzły wirtualne są obecnie w wersji zapoznawczej w uzywce AKS.

Aplikacja nie wymaga modyfikacji do korzystania z węzłów wirtualnych. Wdrożenia można skalować w różnych aks i ACI i bez opóźnień, jak skalowanie automatyczne klastra wdraża nowe węzły w klastrze AKS.

Węzły wirtualne są wdrażane w dodatkowej podsieci w tej samej sieci wirtualnej co klaster AKS. Ta konfiguracja sieci wirtualnej umożliwia zabezpieczenie ruchu między ACI i AKS. Podobnie jak w przypadku klastra AKS, wystąpienie usługi ACI jest bezpiecznym, logicznym zasobem obliczeniowym, który jest odizolowany od innych użytkowników.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę ze skalowaniem aplikacji, należy najpierw wykonać [przewodnik Szybki start, aby utworzyć klaster AKS z platformą Azure CLI][aks-quickstart]. Następnie można rozpocząć ręczne lub automatyczne skalowanie aplikacji w klastrze AKS:

- Ręczne skalowanie [zasobników][aks-manually-scale-pods] lub [węzłów][aks-manually-scale-nodes]
- Użyj [skalowania automatycznego zasobnika poziomego][aks-hpa]
- Korzystanie z [skalowania automatycznego klastra][aks-cluster-autoscaler]

Aby uzyskać więcej informacji na temat podstawowych pojęć kubernetów i usługi AKS, zobacz następujące artykuły:

- [Klastry i obciążenia Kubernetes / AKS][aks-concepts-clusters-workloads]
- [Dostęp i tożsamość Kubernetes / AKS][aks-concepts-identity]
- [Zabezpieczenia Kubernetes / AKS][aks-concepts-security]
- [Sieci wirtualne Kubernetes / AKS][aks-concepts-network]
- [Kubernetes / AKS pamięci masowej][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md