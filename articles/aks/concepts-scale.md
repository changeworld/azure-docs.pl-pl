---
title: Pojęcia — skalowanie aplikacji w usłudze Azure Kubernetes usługi (AKS)
description: Dowiedz się więcej na temat skalowania w usłudze Azure Kubernetes Service (AKS), takich jak skalowanie zasobników w poziomie, skalowanie klastra i łącznik usługi Azure Container Instances.
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: conceptual
origin.date: 02/28/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: d7df4d2c7e824f143201e2c6af220730bcd38fb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466956"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Opcje skalowania aplikacji w usłudze Azure Kubernetes Service (AKS)

Podczas uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS) może być konieczne zwiększenie lub zmniejszenie ilości zasobów obliczeniowych. Jako liczba wystąpień aplikacji należy zmienić numer podstawowy Kubernetes węzłów może być konieczna zmiana. Również może być konieczne można szybko inicjować obsługę dużej liczby wystąpień dodatkowych aplikacji.

W tym artykule przedstawiono podstawowe pojęcia, które ułatwiają skalowanie aplikacji w usłudze AKS:

- [Ręczne skalowanie](#manually-scale-pods-or-nodes)
- [Skalowanie zasobników w poziomie (HPA)](#horizontal-pod-autoscaler)
- [Skalowanie klastra](#cluster-autoscaler)
- [Integracja usługi Azure Container wystąpienia (ACI) za pomocą usługi AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Ręczne skalowanie zasobników albo przez węzły

Można ręcznie skalować repliki (zasobników) i węzły do testowania, jak aplikacja reaguje na zmianę dostępnych zasobów i stanu. Ręczne skalowanie zasobów umożliwia także zdefiniowanie określonej ilości zasobów na potrzeby obsługi na stałą kwotę, takie jak liczba węzłów. Ręczne skalowanie, zdefiniuj replik lub liczba węzłów i harmonogramy interfejsu API rozwiązania Kubernetes, tworząc dodatkowe zasobniki lub opróżniania węzłów.

Aby rozpocząć ręczne skalowanie zasobników i węzły zobacz [skalowanie aplikacji w usłudze AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Skalowanie zasobników w poziomie

Kubernetes używa skalowania automatycznego zasobników w poziomie (HPA), aby monitorować zapotrzebowanie na zasoby i automatyczne skalowanie liczby replik określa liczbę. Domyślnie skalowanie zasobników w poziomie sprawdza, czy interfejs API metryk co 30 sekund, wymaga zmian liczba replik. Gdy wymagane są zmiany, liczba replik zwiększania lub zmniejszania odpowiednio. Skalowanie zasobników w poziomie działa z klastrami usługi AKS wdrożyć serwer metryki dla rozwiązania Kubernetes 1.8 +.

![Automatyczne skalowanie zasobników w poziomie rozwiązania Kubernetes](media/concepts-scale/horizontal-pod-autoscaling.png)

Po skonfigurowaniu skalowanie zasobników w poziomie dla danego wdrożenia, należy zdefiniować minimalna i maksymalna liczba replik, które można uruchomić. Możesz również definiować metrykę do monitorowania i oprzeć podejmuje skalowania, takich jak użycie procesora CPU.

Aby rozpocząć pracę przy użyciu skalowania automatycznego zasobników w poziomie w usłudze AKS, zobacz [automatyczne skalowanie zasobników w usłudze AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Ochładzania skalowania zdarzenia

Ponieważ skalowanie zasobników w poziomie sprawdza, czy interfejs API metryk co 30 sekund, poprzednie zdarzenia skalowania może nie została pomyślnie ukończona przed dokonaniem wyboru innej. To zachowanie może powodować skalowanie zasobników w poziomie zmienić liczbę replik, przed poprzednie zdarzenie skalowania mógł odbierać obciążenie aplikacji i zapotrzebowanie na zasoby można odpowiednio dostosować.

Aby zminimalizować te zdarzenia wyścigu, można ustawić wartości ochładzania lub opóźnienia. Wartości te definiują, jak długo skalowania automatycznego zasobników w poziomie musi czekać po zdarzeniu skalowania, wywoła inne zdarzenie skalowania. Takie zachowanie umożliwia nową replikę liczba zostały wprowadzone i interfejsu API metryki odzwierciedlają rozproszonego obciążenia pracą. Domyślnie opóźnienia w skali zdarzenia jest więcej niż trzy minuty, a opóźnienie w dół zdarzenia wynosi 5 minut

Może być konieczne Dostosowywanie tych wartości ochładzania. Domyślne wartości ochładzania może dać wrażenie, że skalowanie zasobników w poziomie nie jest wystarczająco szybko skalować liczbę replik. Na przykład, aby szybciej zwiększenia liczby replik w użyciu, zmniejszyć `--horizontal-pod-autoscaler-upscale-delay` utworzenie zasobników w poziomie definicje skalowania automatycznego za pomocą `kubectl`.

## <a name="cluster-autoscaler"></a>Skalowanie klastra

Aby reagować na zmieniające się potrzeby zasobników, Kubernetes ma skalowanie klastra (obecnie dostępna w wersji zapoznawczej w usłudze AKS), która dostosowuje liczbę węzłów na podstawie zasobów żądanego obliczeniowych w puli węzłów. Domyślnie skalowanie klastra umożliwia sprawdzenie serwera interfejsu API co 10 sekund, wymaga zmian w liczby węzłów. Jeśli automatyczne skalowanie klastra okaże się, że zmiana jest wymagane, liczbę węzłów w klastrze AKS zwiększania lub zmniejszania odpowiednio. Skalowanie klastra działa z klastrami usługi AKS z włączoną funkcją RBAC, systemem Kubernetes 1.10.x lub nowszej.

![Skalowanie klastra usługi Kubernetes](media/concepts-scale/cluster-autoscaler.png)

Skalowanie klastra jest zwykle używana wraz z skalowania automatycznego zasobników w poziomie. W połączeniu, skalowanie zasobników w poziomie zwiększa lub zmniejsza liczbę zasobników, w zależności od potrzeb aplikacji, a skalowanie klastra dostosowuje liczbę węzłów, zgodnie z potrzebami, aby uruchomić te dodatkowe zasobniki odpowiednio.

Aby rozpocząć skalowanie klastra w usłudze AKS, zobacz [skalowanie klastra w usłudze AKS][aks-cluster-autoscaler].

### <a name="scale-up-events"></a>Skalowanie w górę zdarzenia

Jeśli węzeł nie ma wystarczające zasoby obliczeniowe, aby uruchomić zasobnik żądanego, którym następnie nie postępu przez proces planowania. Nie można uruchomić zasobnik, chyba że dodatkowe zasoby obliczeniowe są dostępne w ramach puli węzłów.

Podczas skalowania automatycznego klastra powiadomienia zasobników, które nie może zostać zaplanowane z powodu ograniczeń zasobów puli węzeł liczbę węzłów w ramach puli węzłów zwiększa się do zapewnienia dodatkowych zasobów obliczeniowych. W przypadku pomyślnie wdrożone i dostępne do użycia w puli węzłów te dodatkowe węzły zasobników są planowane do uruchomienia na nich.

Jeśli aplikacja wymaga błyskawicznie, niektóre zasobniki może pozostają w stanie oczekujących na zaplanowanie pozostałe węzły, które są wdrażane przez skalowanie klastra mogą akceptować zaplanowane zasobników. W przypadku aplikacji, które mają wysoką odporność zapotrzebowania można skalować węzły wirtualne i usługi Azure Container Instances.

### <a name="scale-down-events"></a>Skaluj w dół do zdarzenia

Skalowanie klastra monitoruje również zasobnik stan dla węzłów, które nie odebrały ostatnio nowych żądań dotyczących planowania planowania. W tym scenariuszu wskazuje, że puli węzeł ma więcej zasobów obliczeniowych nie są wymagane i zmniejszyć liczbę węzłów.

Węzeł, który przekazuje próg nie jest potrzebna na 10 minut, domyślnie jest zaplanowane do usunięcia. Jeśli taka sytuacja ma miejsce, zasobników są planowane do uruchomienia w innych węzłach w puli węzłów i skalowanie klastra zmniejsza liczbę węzłów.

Aplikacje mogą występować niektóre przerw w działaniu, zgodnie z zasobników zaplanowano na różnych węzłach podczas skalowania klastra automatycznego zmniejsza liczbę węzłów. Aby zminimalizować zakłócenia, należy unikać aplikacje, które używają wystąpienia pojedynczy zasobnik.

## <a name="burst-to-azure-container-instances"></a>Przejścia na usłudze Azure Container Instances

Szybkie skalowanie klastra usługi AKS, można zintegrować z usługi Azure Container Instances (ACI). Usługi Kubernetes ma wbudowane składniki skalowania liczby replik i języka node. Jednak jeśli Twoja aplikacja potrzebuje do szybkie skalowanie, skalowanie zasobników w poziomie może zaplanować zasobników więcej niż może być udostępniane przez istniejące zasoby obliczeniowe w puli węzłów. Jeśli skonfigurowana, w tym scenariuszu następnie powodowało skalowania automatycznego klastra, aby wdrożyć dodatkowe węzły w puli węzłów, ale może potrwać kilka minut, zanim te węzły do pomyślnej aprowizacji i umożliwić harmonogram Kubernetes, aby uruchomić zasobników na nich.

![Skalowanie w usłudze aci Uzyskaj — seria rozwiązania Kubernetes](media/concepts-scale/burst-scaling.png)

ACI umożliwia szybkie wdrażanie wystąpień kontenera bez zwiększania dodatkowej infrastruktury. Po nawiązaniu połączenia za pomocą usługi AKS, ACI staje się zabezpieczonej logiczne rozszerzenie klastra usługi AKS. Składnik rozwiązania Virtual Kubelet jest zainstalowany w klastrze AKS prezentuje ACI jako wirtualnej węzeł rozwiązania Kubernetes. Kubernetes można następnie zaplanować zasobników działających jako wystąpień usługi ACI za pośrednictwem wirtualnych węzłów, a nie jako zasobników w węzłach maszyny Wirtualnej bezpośrednio w klastrze AKS. Wirtualne węzły są obecnie dostępne w wersji zapoznawczej w usłudze AKS.

Aplikacja nie wymaga żadnych modyfikacji, aby użyć wirtualnych węzłów. Wdrożeń można skalować w usłudze AKS i ACI, i bez opóźnień jako klaster skalowania automatycznego wdrażania nowych węzłów w klastrze AKS.

Węzły wirtualne są wdrażane na dodatkowe podsieci w tej samej sieci wirtualnej co klaster AKS. Ta konfiguracja sieci wirtualnej zezwala na ruch między usługami ACI i AKS, która ma zostać zabezpieczony. Podobnie jak klaster usługi AKS wystąpienia usługi ACI jest zasobu obliczeniowego bezpieczne, logiczną, która jest odizolowana od innych użytkowników.

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć skalowanie aplikacji, najpierw należy wykonać [szybkiego startu, aby utworzyć klaster usługi AKS przy użyciu wiersza polecenia platformy Azure][aks-quickstart]. Następnie można uruchomić ręcznie lub automatycznie skalować aplikacje w klastrze AKS:

- Ręczne skalowanie [zasobników] [ aks-manually-scale-pods] lub [węzłów][aks-manually-scale-nodes]
- Użyj [skalowania automatycznego zasobników w poziomie][aks-hpa]
- Użyj [klastra skalowania automatycznego][aks-cluster-autoscaler]

Więcej informacji na temat podstawowej platformy Kubernetes oraz pojęcia zostały przedstawione z usługi AKS zobacz następujące artykuły:

- [Kubernetes / klastrów usługi AKS i obciążeń][aks-concepts-clusters-workloads]
- [Kubernetes dostępu w usłudze AKS i zarządzania tożsamościami][aks-concepts-identity]
- [Kubernetes / zabezpieczeń usługi AKS][aks-concepts-security]
- [Kubernetes / sieci wirtualne w usłudze AKS][aks-concepts-network]
- [Kubernetes / magazynu usługi AKS][aks-concepts-storage]

<!-- LINKS - external -->

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