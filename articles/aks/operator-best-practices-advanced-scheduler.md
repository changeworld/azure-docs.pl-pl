---
title: Operator najlepsze rozwiązania — zaawansowane funkcje usługi scheduler w usłudze Azure Kubernetes usługi (AKS)
description: Dowiedz się, operator klastra najlepsze rozwiązania dotyczące używania harmonogramu zaawansowane funkcje, takie jak nasłonecznieniem i tolerations, selektory węzła i koligacji lub koligację między pod i konfiguracji zapobiegającej koligacji w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: conceptual
origin.date: 11/26/2018
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: 27c9c872f4dfb82b4a1389189d62c4e1f06ee272
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60464972"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące harmonogramu zaawansowanych funkcji w usłudze Azure Kubernetes Service (AKS)

W przypadku zarządzania klastrów w usłudze Azure Kubernetes Service (AKS), często jest konieczne do izolowania obciążeń i zespołów. Harmonogram Kubernetes zapewnia zaawansowane funkcje umożliwiające kontrolowanie zasobników, które mogą być planowane na niektóre węzły lub sposób dystrybucji wielu zasobników, które aplikacje mogą odpowiednio w klastrze. 

Najlepsze rozwiązania dotyczące tej koncentruje się na Kubernetes zaawansowane funkcje planowania dla operatorów klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Użyj nasłonecznieniem oraz tolerations, aby ograniczyć zasobników, jakie mogą być planowane w węzłach
> * Nadaj preferencji do zasobników do uruchamiania na niektóre węzły z selektory węzła lub koligacji węzłów
> * Podziel od siebie lub grupy zasobników razem z koligację między zasobnika lub konfiguracji zapobiegającej koligacji

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Podaj przy użyciu nasłonecznieniem i tolerations węzły dedykowane

**Najważniejsze wskazówki** -ograniczania dostępu dla aplikacji intensywnie korzystających z zasobów, takich jak kontrolery ruch przychodzący do określonych węzłów. Aktualizowanie węzła zasoby dostępne dla obciążeń wymagających ich i nie zezwala na planowanie innych obciążeń w węzłach.

Podczas tworzenia klastra usługi AKS możesz wdrożyć węzły z obsługą procesorów GPU lub dużej liczby wydajne procesory CPU. Węzły te są często używane do przetwarzania danych dużych obciążeń, takich jak usługi machine learning (ML) lub sztucznej inteligencji (AI). Ten typ sprzętu jest zwykle zasobem kosztowne węzła do wdrożenia, należy ograniczyć obciążeń, które mogą być planowane na tych węzłach. Zamiast tego możesz przeznaczyć niektóre węzły w klastrze, aby uruchomić usługami transferem danych przychodzących i zapobiec innych obciążeń.

Harmonogram Kubernetes nasłonecznieniem i umożliwia tolerations ograniczyć, jakie obciążenia mogą być uruchamiane w węzłach.

* A **skażenia** jest stosowany do węzła, który wskazuje na nich można zaplanować tylko określonych zasobników.
* A **toleration** jest następnie stosowane do zasobników, które umożliwia im *tolerować* zmiany barwy węzła.

Podczas wdrażania zasobnika klaster AKS rozwiązania Kubernetes tylko planuje zasobników w węzłach, w którym toleration jest powiązana z zmiany barwy. Na przykład załóżmy, że masz nodepool klastra AKS węzłów z procesora GPU, pomocy technicznej. Zdefiniuj nazwę, taką jak *procesora gpu*, następnie wartość do planowania. Jeśli ta wartość jest ustawiona na *NoSchedule*, harmonogram Kubernetes nie można zaplanować zasobników w węźle, jeśli Zasobnik nie zdefiniowano toleration odpowiednie.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Za pomocą zmiany barwy zastosowane do węzłów należy zdefiniować toleration w specyfikacji zasobnik, która umożliwia planowanie w węzłach. W poniższym przykładzie zdefiniowano `sku: gpu` i `effect: NoSchedule` tolerować zmiany barwy zastosowany do węzła w poprzednim kroku:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: dockerhub.azk8s.cn/microsoft/samples-tf-mnist-demo:gpu
  resources:
    requests:
      cpu: 0.5
      memory: 2Gi
    limits:
      cpu: 4.0
      memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Podczas wdrażania tego zasobnika, takiej jak `kubectl apply -f gpu-toleration.yaml`, Kubernetes pomyślnie zaplanować zasobnik w węzłach o zmiany barwy stosowane. Ta izolacja logiczna pozwala kontrolować dostęp do zasobów w ramach klastra.

Po zastosowaniu nasłonecznieniem współpracować z Twojej aplikacji deweloperzy i właściciele umożliwia im do definiowania tolerations wymagane w bieżących wdrożeń.

Aby uzyskać więcej informacji na temat nasłonecznieniem i tolerations zobacz [stosowanie nasłonecznieniem i tolerations][k8s-taints-tolerations].

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Zasobnik kontroli, tworzenie harmonogramów przy użyciu koligacji i selektorów węzła

**Najważniejsze wskazówki** — kontrolować planowania zasobników w węzłach za pomocą węzła selektorów, koligacji węzłów lub komunikacja między pod koligacji. Te ustawienia umożliwiają harmonogramu Kubernetes do logicznie izolowania obciążeń, takich jak przez sprzęt, w węźle.

Nasłonecznieniem i tolerations służą do logicznie izolowanie zasobów za pomocą twardych odcięcia — jeśli Zasobnik nie ma znaczenia zmiany barwy węzła, nie jest zaplanowane na węźle. Alternatywnym podejściu jest węzeł selektorów należy używać. Węzły, takie jak etykiety są do wskazywać magazyn SSD podłączonych lokalnie lub dużej ilości pamięci, a następnie zdefiniuj w specyfikacji zasobnika selektora węzła. Kubernetes następnie planuje tych zasobników w węźle dopasowania. W odróżnieniu od tolerations można zaplanować zasobników, bez pasującego selektor węzła w węzłach etykietami. Takie zachowanie umożliwia nieużywane zasoby w węzłach korzystać, ale daje priorytetu do zasobników, które definiują pasującego selektor węzła.

Spójrzmy na przykład węzły z dużej ilości pamięci. Te węzły można nadać preferencji do zasobników, które żądają dużej ilości pamięci. Aby upewnić się, że nie znajdują się zasoby bezczynności, również zezwolić innym zasobników uruchomić.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

Specyfikacja zasobnik, który jest następnie dodaje `nodeSelector` do definiowania selektor węzła, zgodną z etykietą właściwością w węźle:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: dockerhub.azk8s.cn/microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
    nodeSelector:
      hardware: highmem
```

Korzystając z tych opcji harmonogramu, pracy z Twojej aplikacji deweloperzy i właściciele umożliwia im poprawnie zdefiniować ich specyfikacje zasobników.

Aby uzyskać więcej informacji o korzystaniu z węzła selektorów, zobacz [przypisywanie zasobników do węzłów][k8s-node-selector].

### <a name="node-affinity"></a>Koligacja węzła

Selektor węzeł jest podstawowy sposób przypisać zasobników do danego węzła. Elastyczność jest dostępne za pośrednictwem *koligacji węzłów*. Za pomocą koligacji węzła należy zdefiniować, co się stanie, jeśli nie można dopasować zasobnik z węzłem. Możesz *wymagają* że Kubernetes harmonogram odpowiada zasobnik z etykietami hosta. Można też *Preferuj* dopasowanie, ale zezwalaj na zasobnik na zaplanowane na inny host, jeśli nie dopasowanie jest dostępna.

Poniższy przykład ustawia koligacji węzła *requiredDuringSchedulingIgnoredDuringExecution*. Ta koligacji wymaga harmonogramu Kubernetes węzła za pomocą odpowiadających im etykiet. Jeśli żaden węzeł nie jest dostępny, Zasobnik ma czekać na planowanie, aby kontynuować. Aby zezwolić na zasobnik być zaplanowane w innym węźle, można zamiast tego ustawić wartość na *preferredDuringScheduledIgnoreDuringExecution*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: dockerhub.azk8s.cn/microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: hardware
          operator: In
          values: highmem
```

*IgnoredDuringExecution* część ustawienie wskazuje, jeśli węzeł etykiety zmiany, Zasobnik nie powinien wykluczona z węzła. Harmonogram Kubernetes używa tylko etykiety zaktualizowany węzeł dla nowych zasobników zaplanowane, a nie do zasobników już zaplanowane w węzłach.

Aby uzyskać więcej informacji, zobacz [koligacji i antykoligację][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Koligację między zasobników i konfiguracji zapobiegającej koligacji

Jedno z podejść końcowe usługi scheduler Kubernetes do logicznie izolowania obciążeń używa koligację między zasobnika lub konfiguracji zapobiegającej koligacji. Ustawienia definiują tego zasobników *nie powinien* można zaplanować na węzeł, który ma istniejące odpowiadającego zasobnika lub, *powinien* zaplanowane. Domyślnie harmonogram Kubernetes próbuje zaplanować wielu zasobnikach w zestawie węzłów replik. Można zdefiniować bardziej szczegółowych reguł dotyczących tego zachowania.

Dobrym przykładem jest aplikacja sieci web, która również korzysta z usługi Azure Cache dla usługi Redis. Reguły konfiguracji zapobiegającej koligacji zasobnika służy do żądania, że harmonogram Kubernetes dystrybuuje replik w węzłach. Koligacja reguły umożliwiają następnie upewnij się, że każdy składnik aplikacji sieci web jest zaplanowane na tym samym hoście jako odpowiedniej pamięci podręcznej. Rozkład zasobników w węzłach wygląda następująco:

| **Węzeł 1** | **Węzeł 2** | **Węzeł 3** |
|------------|------------|------------|
| Aplikacja sieci Web 1   | Aplikacja sieci Web 2   | Aplikacja sieci Web 3   |
| pamięć podręczna-1    | pamięć podręczna 2    | pamięć podręczna 3    |

W tym przykładzie jest bardziej złożonym wdrożeniu, niż w przypadku selektorów węzła lub koligacji węzłów. Umożliwia wdrożenie kontrolę nad jak Kubernetes planuje zasobników w węzłach i logicznie można izolować zasoby. Aby uzyskać pełny przykład tej aplikacji sieci web za pomocą usługi Azure Cache, na przykład pamięci podręcznej Redis, zobacz [kolokacja zasobników, w tym samym węźle][k8s-pod-affinity].

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł koncentruje się na zaawansowane funkcje usługi scheduler Kubernetes. Aby uzyskać więcej informacji na temat operacji klastra w usłudze AKS zobacz poniższe najlepsze rozwiązania:

* [Izolację wielodostępu i klastra][aks-best-practices-scheduler]
* [Podstawowe funkcje usługi scheduler rozwiązania Kubernetes][aks-best-practices-scheduler]
* [Uwierzytelnianie i autoryzacja][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md