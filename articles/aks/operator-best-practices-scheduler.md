---
title: Operator najlepsze rozwiązania — funkcje podstawowe usługi scheduler w usłudze Azure Kubernetes usługi (AKS)
description: Dowiedz się, operator klastra najlepsze rozwiązania dotyczące używania harmonogramu podstawowe funkcje, takie jak limity przydziałów zasobów i zasobnika budżetów przerw w działaniu w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: f6e370442c9c359a38025762fb90269119ec0ea6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "65074125"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące harmonogramu podstawowe funkcje w usłudze Azure Kubernetes Service (AKS)

W przypadku zarządzania klastrów w usłudze Azure Kubernetes Service (AKS), często jest konieczne do izolowania obciążeń i zespołów. Harmonogram Kubernetes zapewnia funkcje, które pozwalają na kontrolowanie dystrybucji zasoby obliczeniowe lub ograniczyć wpływ zdarzeń związanych z konserwacją.

Najlepsze rozwiązania dotyczące tej koncentruje się na Kubernetes podstawowe funkcje planowania dla operatorów klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Limity przydziałów zasobów użycia w celu zapewnienia stałej ilości zasobów, aby zespoły lub obciążeń
> * Ogranicza wpływ zaplanowanej konserwacji za pomocą budżetów przerw w działaniu zasobników
> * Wyszukaj brakujące żądania zasobu pod oraz liczby wywołań przy użyciu `kube-advisor` narzędzia

## <a name="enforce-resource-quotas"></a>Wymuszanie limitów przydziałów zasobów

**Najważniejsze wskazówki** — planowanie i zastosować limity przydziałów zasobów na poziomie przestrzeni nazw. Jeśli nie Definiuj zasobników, zasobów i limity, odrzucić wdrożenie. Monitorowanie użycia zasobów i Dostosowywanie przydziałów, zgodnie z potrzebami.

Żądania zasobów i limity są umieszczane w specyfikacji zasobników. Limity te są używane przez harmonogram Kubernetes w czasie wdrażania można znaleźć dostępnego węzła w klastrze. Te ograniczenia i żądań działania na poziomie poszczególnych zasobników. Aby uzyskać więcej informacji na temat sposobu definiowania tych wartości, zobacz [Definiowanie zasobów zasobników i limity][resource-limits]

Aby zapewnić możliwość rezerwowania i ograniczania zasobów dla rozwoju zespołu lub projektu, należy użyć *limity przydziałów zasobów*. Te przydziały są zdefiniowane w przestrzeni nazw i może służyć do ustawiania przydziałów zgodnie z następującymi zasadami:

* **Zasoby obliczeniowe**, takich jak procesor CPU i pamięci lub procesorów GPU.
* **Zasoby magazynu**, zawiera łączną liczbę woluminów lub ilość miejsca na dysku dla klasy magazynowania.
* **Liczba obiektów**, takie jak maksymalna liczba wpisów tajnych, można utworzyć usługi lub zadania.

Rozwiązanie Kubernetes nie overcommit zasobów. Po pomyślnej łączna liczba żądań zasobów lub limitów przydziału przypisanych nie dalszych wdrożeń to się powiedzie.

Podczas definiowania limity przydziałów zasobów zasobników wszystkie utworzone w przestrzeni nazw należy podać limity lub żądań w ich specyfikacje zasobników. Jeśli nie zapewniają one te wartości, można odrzucić wdrożenie. Zamiast tego można [skonfigurować domyślne i limity dla przestrzeni nazw][configure-default-quotas].

Następujące manifest YAML przykład o nazwie *dev aplikacji zespołu quotas.yaml* ustawia stały limit daje w sumie *10* procesorów CPU, *20Gi* pamięci i *10*zasobników:

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

Ten limit przydziału zasobów mogą być stosowane przez określenie obszaru nazw, takich jak *dev-apps*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Praca z deweloperów aplikacji i właścicieli do zrozumienia potrzeb i zastosować limity przydziałów odpowiednich zasobów.

Aby uzyskać więcej informacji na temat obiektów dostępnych zasobów, zakresy i priorytetów, zobacz [limity przydziałów zasobów w usłudze Kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Planowanie dostępności przy użyciu budżetów przerw w działaniu zasobników

**Najważniejsze wskazówki** — w celu zapewnienia dostępności aplikacji, zdefiniuj budżetów przerw w działaniu zasobnika (PDB), aby upewnić się, że minimalna liczba zasobników są dostępne w klastrze.

Istnieją dwa szkodliwe zdarzenia, które powodują zasobników do usunięcia:

* *Niedobrowolnego przerw w działaniu* zdarzeń poza kontrolą typowe operatora klastra lub właściciela aplikacji.
  * Te przerw w działaniu niedobrowolnego obejmują awarii sprzętu komputera fizycznego, jądra lub usuwania węzłów maszyny Wirtualnej
* *Dobrowolna przerw w działaniu* są zdarzenia, żądaniem operator klastra lub właściciela aplikacji.
  * Te przerw w działaniu dobrowolne to Uaktualnianie klastra, szablon wdrażania aktualizacji lub przypadkowego usunięcia zasobnik.

Niedobrowolnego przerw w działaniu można zminimalizować przy użyciu wielu replik zasobników we wdrożeniu. Uruchamianie wielu węzłów w klastrze AKS pomaga również te niedobrowolnego przerw w działaniu. Dobrowolna przerw w działaniu, usługa Kubernetes zapewnia *zasobnika budżetów przerw w działaniu* , umożliwić operator klastra, zdefiniować liczba minimalne dostępne lub maksymalnej zasobów niedostępny. Te budżetów przerw w działaniu zasobnika umożliwiają planowanie sposobu wdrożenia lub zestawów replik reagowanie po wystąpieniu zdarzenia dobrowolne przerw w działaniu.

Jeśli klaster jest uaktualniany lub zaktualizowany szablon wdrożenia, harmonogram Kubernetes sprawia, że się, że dodatkowe zasobników są planowane w innych węzłach, zanim będzie można kontynuować zdarzenia dobrowolne przerw w działaniu. Harmonogram czeka przed węzłem jest uruchamiany ponownie, dopóki pomyślnie zaplanowano zdefiniowanej liczby zasobników w innych węzłach w klastrze.

Przyjrzyjmy się przykładem zestawu z pięciu zasobników, systemem NGINX replik. Zasobników w replice ustawione jako przypisana etykieta `app: nginx-frontend`. Podczas zdarzenia dobrowolne przerw w działaniu, np. w przypadku uaktualniania klastra ma upewnij się, że co najmniej trzech zasobników w dalszym ciągu uruchamiać. Poniższego kodu YAML manifestu dla *PodDisruptionBudget* obiektu określa następujące wymagania:

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

Można również zdefiniować wartości procentowej, takich jak *60%* , co pozwala na automatyczne kompensuje repliki zestawu skalowania liczby zasobników.

Maksymalna liczba wystąpień niedostępny można zdefiniować zestawu replik. Ponownie można także definiować procent maksymalnej zasobników niedostępny. Manifest YAML budżetu przerw w działaniu w usłudze następujące zasobnika definiuje, nie więcej niż dwa zasobników w replice zestawu jest niedostępny:

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

Po zdefiniowaniu budżetu przerw w działaniu pod utworzysz ją w klastrze AKS podobnie jak w przypadku innych obiektów w usłudze Kubernetes:

```console
kubectl apply -f nginx-pdb.yaml
```

Praca z deweloperów aplikacji i właścicieli do zrozumienia ich potrzeb i zastosować odpowiednie zasobnika budżetów przerw w działaniu.

Aby uzyskać więcej informacji o używaniu budżetów przerw w działaniu zasobników, zobacz [Określ budżet przerw w działaniu aplikacji][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Regularne sprawdzanie problemów z klastrem przy użyciu klastra kubernetes w usłudze advisor

**Najważniejsze wskazówki** -regularnie uruchomić najnowszą wersję `kube-advisor` narzędzia typu open source do wykrywania problemów w klastrze. Jeśli zastosujesz limity przydziałów zasobów w istniejącym klastrze usługi AKS, uruchom `kube-advisor` najpierw po to, aby znaleźć zasobników, które nie mają żądania zasobów i ograniczeń.

[Klastra kubernetes w usłudze advisor] [ kube-advisor] narzędzie jest skojarzone projekt typu open source AKS, które skanuje klastra Kubernetes i raporty dotyczące problemów, które znajdzie. Jest jeden wyboru przydatne do identyfikowania zasobników, które nie mają i limity zasobów w miejscu.

Narzędzia klastra kubernetes w usłudze advisor mogą być przedstawione na żądanie zasobów i limity Brak w aplikacji PodSpecs dla Windows, a także aplikacje dla systemu Linux, ale samo narzędzie klastra kubernetes w usłudze klasyfikatora musi być zaplanowane na zasobnik systemu Linux. Można zaplanować zasobnika do uruchamiania na pulę węzłów przy użyciu określonego systemu operacyjnego [selektor węzła] [ k8s-node-selector] w zasobniku konfiguracji.

W klastrze AKS, który hostuje wiele zespołów deweloperów i aplikacji może być trudne do śledzenia zasobników, bez tych zasobów żądania i ogranicza zestaw. Najlepszym rozwiązaniem jest regularne uruchamianie `kube-advisor` w klastrach usługi AKS, zwłaszcza, jeśli limity przydziałów zasobów nie przypisuj do przestrzeni nazw.

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł koncentruje się na podstawowych funkcji usługi scheduler Kubernetes. Aby uzyskać więcej informacji na temat operacji klastra w usłudze AKS zobacz poniższe najlepsze rozwiązania:

* [Izolację wielodostępu i klastra][aks-best-practices-cluster-isolation]
* [Zaawansowane funkcje usługi scheduler rozwiązania Kubernetes][aks-best-practices-advanced-scheduler]
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
