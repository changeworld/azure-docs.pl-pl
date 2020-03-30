---
title: Najważniejsze wskazówki dotyczące operatora — podstawowe funkcje harmonogramu w usługach Azure Kubernetes (AKS)
description: Poznaj najważniejsze wskazówki dotyczące korzystania z podstawowych funkcji harmonogramu, takich jak przydziały zasobów i budżety zakłóceń zasobników w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cccc476a944b28d24c53a947e434d465c94f94ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126587"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Najważniejsze wskazówki dotyczące podstawowych funkcji harmonogramu w usłudze Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w usłudze Azure Kubernetes Service (AKS) często należy izolować zespoły i obciążenia. Harmonogram Kubernetes udostępnia funkcje, które umożliwiają kontrolowanie dystrybucji zasobów obliczeniowych lub ograniczanie wpływu zdarzeń konserwacji.

Ten artykuł dotyczący najlepszych rozwiązań koncentruje się na podstawowych funkcjach planowania kubernetes dla operatorów klastrów. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Użyj przydziałów zasobów, aby zapewnić określoną ilość zasobów zespołom lub obciążeniom
> * Ogranicz wpływ zaplanowanej konserwacji za pomocą budżetów na zakłócenia w zasobach
> * Sprawdzanie brakujących żądań zasobów `kube-advisor` zasobu zasobu zasobu za pomocą narzędzia

## <a name="enforce-resource-quotas"></a>Wymuszanie przydziałów zasobów

**Wskazówki dotyczące najlepszych rozwiązań** — planowanie i stosowanie przydziałów zasobów na poziomie obszaru nazw. Jeśli zasobniki nie definiują żądań i limitów zasobów, należy odrzucić wdrożenie. Monitoruj użycie zasobów i w razie potrzeby dopasowuj przydziały.

Żądania zasobów i limity są umieszczane w specyfikacji zasobu. Te limity są używane przez harmonogram Kubernetes w czasie wdrażania, aby znaleźć dostępny węzeł w klastrze. Te limity i żądania działają na poziomie poszczególnych zasobników. Aby uzyskać więcej informacji na temat definiowania tych wartości, zobacz [Definiowanie żądań i limitów zasobów zasobu zasobu zasobu zasobu zasobu zasobu zasobu zasobu][resource-limits]

Aby zapewnić sposób rezerwowania i ograniczania zasobów w zespole lub projekcie *deweloperów,* należy użyć przydziałów zasobów . Te przydziały są zdefiniowane w obszarze nazw i mogą służyć do ustawiania przydziałów na następujących zasadach:

* **Zasoby obliczeniowe**, takie jak procesor i pamięć, lub procesory gpu.
* **Zasoby magazynu**, zawiera całkowitą liczbę woluminów lub ilość miejsca na dysku dla danej klasy magazynu.
* Można utworzyć **liczbę obiektów,** takich jak maksymalna liczba wpisów tajnych, usług lub zadań.

Kubernetes nie nadmiernie przywiązywaj zasobów. Po przekształceniu łącznej sumy żądań lub limitów zasobów przypisany przydział, żadne dalsze wdrożenia nie są pomyślne.

Podczas definiowania przydziałów zasobów wszystkie zasobniki utworzone w obszarze nazw muszą zawierać limity lub żądania w specyfikacjach zasobników. Jeśli nie podadzą tych wartości, można odrzucić wdrożenie. Zamiast tego można [skonfigurować domyślne żądania i limity dla obszaru nazw][configure-default-quotas].

Poniższy przykładowy manifest YAML o nazwie *dev-app-team-quotas.yaml* ustawia twardy limit łącznie *10* procesorów, *20G* pamięci i *10* zasobników:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Ten przydział zasobów można zastosować, określając obszar nazw, na przykład *aplikacje deweloperów:*

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Praca z deweloperami aplikacji i właścicielami, aby zrozumieć ich potrzeby i zastosować odpowiednie przydziały zasobów.

Aby uzyskać więcej informacji na temat dostępnych obiektów zasobów, zakresów i priorytetów, zobacz [Przydziały zasobów w uliczkach Kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Planowanie dostępności przy użyciu budżetów zakłóceń zasobników

**Wskazówki dotyczące najlepszych rozwiązań** — aby zachować dostępność aplikacji, należy zdefiniować budżety zakłóceń zasobników (PDB), aby upewnić się, że minimalna liczba zasobników jest dostępna w klastrze.

Istnieją dwa destrukcyjne zdarzenia, które powodują usunięcie zasobników:

* *Mimowolne zakłócenia* są zdarzeniami pozostającymi poza typową kontrolą operatora klastra lub właściciela aplikacji.
  * Te mimowolne zakłócenia obejmują awarię sprzętu na komputerze fizycznym, błąd jądra lub usunięcie maszyny wirtualnej węzła
* *Dobrowolne zakłócenia* to zdarzenia wymagane przez operatora klastra lub właściciela aplikacji.
  * Te dobrowolne zakłócenia obejmują uaktualnienia klastra, zaktualizowany szablon wdrożenia lub przypadkowe usunięcie zasobnika.

Mimowolne zakłócenia można złagodzić przy użyciu wielu replik zasobników w wdrożeniu. Uruchamianie wielu węzłów w klastrze AKS pomaga również w tych mimowolnych zakłóceniach. W przypadku dobrowolnych zakłóceń usługa Kubernetes udostępnia *budżety zakłóceń zasobników,* które umożliwiają operatorowi klastra definiowanie minimalnej dostępnej lub maksymalnej liczby niedostępnych zasobów. Te budżety zakłóceń zasobnika pozwalają zaplanować, jak wdrożenia lub zestawy replik reagują, gdy wystąpi zdarzenie dobrowolnego zakłócenia.

Jeśli klaster ma zostać uaktualniony lub szablon wdrożenia zaktualizowany, harmonogram Kubernetes upewnia się, że dodatkowe zasobniki są zaplanowane w innych węzłach, zanim zdarzenia dobrowolnego zakłócenia mogą być kontynuowane. Harmonogram czeka przed ponownym uruchomieniem węzła, dopóki określona liczba zasobników nie zostanie pomyślnie zaplanowana w innych węzłach w klastrze.

Spójrzmy na przykład zestawu replik z pięciu zasobników, które uruchamiają NGINX. Zasobnikom w zestawie replik są `app: nginx-frontend`przypisywane etykiety . Podczas zdarzenia dobrowolnego zakłócenia, takiego jak uaktualnienie klastra, chcesz upewnić się, że co najmniej trzy zasobniki nadal będą działać. Następujący manifest YAML dla *obiektu PodDisruptionBudget* definiuje następujące wymagania:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

Można również zdefiniować wartość procentową, taką jak *60%,* która umożliwia automatyczne kompensowanie zestawu replik skalowania w górę liczby zasobników.

Można zdefiniować maksymalną liczbę niedostępnych wystąpień w zestawie replik. Ponownie można również zdefiniować wartość procentową dla maksymalnych niedostępnych zasobników. Następujący manifest YAML budżetu zakłócenia zasobników definiuje, że nie więcej niż dwa zasobników w zestawie replik być niedostępne:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

Po zdefiniowaniu budżetu zakłóceń zasobnika należy go utworzyć w klastrze usługi AKS, tak jak w przypadku każdego innego obiektu Kubernetes:

```console
kubectl apply -f nginx-pdb.yaml
```

Praca z deweloperami aplikacji i właścicielami, aby zrozumieć ich potrzeby i zastosować odpowiednie budżety zakłóceń zasobnika.

Aby uzyskać więcej informacji na temat korzystania z budżetów zakłóceń zasobników, zobacz [Określanie budżetu zakłóceń dla aplikacji][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Regularne sprawdzanie problemów z klastrem z kube-advisor

**Wskazówki dotyczące najlepszych praktyk** — `kube-advisor` regularnie uruchamiaj najnowszą wersję narzędzia open source w celu wykrywania problemów w klastrze. Jeśli zastosujesz przydziały zasobów do istniejącego `kube-advisor` klastra usługi AKS, uruchom najpierw, aby znaleźć zasobniki, które nie mają zdefiniowanych żądań zasobów i limitów.

Narzędzie [kube-advisor][kube-advisor] jest skojarzonym projektem open source usługi AKS, który skanuje klaster Kubernetes i raportuje problemy, które znajdzie. Jednym z przydatnych kontroli jest zidentyfikowanie zasobników, które nie mają żądań zasobów i limitów w miejscu.

Narzędzie kube-advisor może raportować żądanie zasobów i limity brakujące w PodSpecs dla aplikacji Windows, a także aplikacji Linux, ale samo narzędzie kube-advisor musi być zaplanowane na zasobniku Linux. Można zaplanować zasobnik do uruchomienia w puli węzłów z określonego systemu operacyjnego przy użyciu [selektora węzłów][k8s-node-selector] w konfiguracji zasobnika.

W klastrze AKS, który obsługuje wiele zespołów programistycznych i aplikacji, może być trudne do śledzenia zasobników bez tych żądań zasobów i limitów ustawionych. Najlepszym rozwiązaniem jest regularne `kube-advisor` uruchamianie w klastrach usługi AKS, zwłaszcza jeśli przydziały zasobów nie są przypisywane do obszarów nazw.

## <a name="next-steps"></a>Następne kroki

W tym artykule skupiono się na podstawowych funkcjach harmonogramu kubernetes. Aby uzyskać więcej informacji na temat operacji klastra w uzywce AKS, zobacz następujące najlepsze rozwiązania:

* [Wielodostęp i izolacja klastra][aks-best-practices-cluster-isolation]
* [Zaawansowane funkcje harmonogramu Kubernetes][aks-best-practices-advanced-scheduler]
* [Uwierzytelnianie i autoryzacja][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
