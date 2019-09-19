---
title: Migrowanie z Azure Container Service (ACS) do usługi Azure Kubernetes Service (AKS)
description: Migrowanie z Azure Container Service (ACS) do usługi Azure Kubernetes Service (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 66f76a8a706f60df786786cbd1ce00b7eafd8d7e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097886"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrowanie z Azure Container Service (ACS) do usługi Azure Kubernetes Service (AKS)

W tym artykule opisano planowanie i wykonywanie pomyślnej migracji między Azure Container Service (ACS) za pomocą usług Kubernetes i Azure Kubernetes Service (AKS). Aby ułatwić podejmowanie najważniejszych decyzji, ten przewodnik zawiera szczegółowe informacje o różnicach między usługami ACS i AKS oraz przegląd procesu migracji.

## <a name="differences-between-acs-and-aks"></a>Różnice między usługami ACS i AKS

Usługi ACS i AKS różnią się w niektórych kluczowych obszarach, które mają wpływ na migrację. Przed rozpoczęciem migracji należy przejrzeć i zaplanować rozwiązanie następujących różnic:

* Węzły AKS używają [dysków zarządzanych](../virtual-machines/windows/managed-disks-overview.md).
    * Dyski niezarządzane muszą zostać przekonwertowane przed dołączeniem ich do węzłów AKS.
    * Obiekty `StorageClass` niestandardowe dla dysków platformy Azure należy zmienić z `unmanaged` na `managed`.
    * Każdy `PersistentVolumes` z nich `kind: Managed`powinien być używany.
* AKS obsługuje [wiele pul węzłów](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (obecnie w wersji zapoznawczej).
* Węzły oparte na systemie Windows Server są obecnie dostępne w [wersji zapoznawczej w AKS](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* AKS obsługuje ograniczoną liczbę [regionów](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* AKS to zarządzana usługa z hostowaną płaszczyzną kontroli Kubernetes. W przypadku wcześniejszej modyfikacji konfiguracji wzorców usług ACS może zajść potrzeba zmodyfikowania aplikacji.

## <a name="differences-between-kubernetes-versions"></a>Różnice między wersjami Kubernetes

W przypadku migrowania do nowszej wersji programu Kubernetes zapoznaj się z poniższymi zasobami, aby poznać strategie obsługi wersji Kubernetes:

* [Zasady obsługi wersji Kubernetes i pochylania wersji](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

### <a name="agent-pools"></a>Pule agenta

Chociaż AKS zarządza płaszczyzną kontroli Kubernetes, nadal definiuje się rozmiar i liczbę węzłów do uwzględnienia w nowym klastrze. Przy założeniu, że chcesz, aby mapowanie 1:1 z usług ACS AKS, chcesz przechwycić istniejące informacje o węźle ACS. Użyj tych danych podczas tworzenia nowego klastra AKS.

Przykład:

| Name | Count | Rozmiar maszyny wirtualnej | System operacyjny |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Maszyna wirtualna Standard_D2_v2 | Windows |

Ponieważ dodatkowe maszyny wirtualne zostaną wdrożone w ramach subskrypcji podczas migracji, należy sprawdzić, czy limity przydziału i ograniczenia są wystarczające dla tych zasobów. 

Aby uzyskać więcej informacji, zobacz [limity subskrypcji i usług platformy Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits). Aby sprawdzić bieżące przydziały, w Azure Portal przejdź do [bloku subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), wybierz subskrypcję, a następnie wybierz pozycję **użycie i przydziały**.

### <a name="networking"></a>Networking

W przypadku złożonych aplikacji zwykle przeprowadzana jest migracja w czasie, a nie wszystkie jednocześnie. Oznacza to, że stare i nowe środowiska mogą wymagać komunikacji za pośrednictwem sieci. Aplikacje, które wcześniej `ClusterIP` korzystały z usług do komunikacji, mogą być narażone `LoadBalancer` jako typ i być odpowiednio zabezpieczone.

Aby ukończyć migrację, należy wskazać klientom nowe usługi, które działają w systemie AKS. Zalecamy przekierowanie ruchu przez zaktualizowanie systemu DNS w taki sposób, aby wskazywał Load Balancer, które znajduje się przed klastrem AKS.

### <a name="stateless-applications"></a>Bezstanowe aplikacje

Migracja aplikacji bezstanowych jest najłatwiej. Możesz zastosować definicje YAML do nowego klastra, upewnić się, że wszystko działa zgodnie z oczekiwaniami, i Przekieruj ruch, aby aktywować nowy klaster.

### <a name="stateful-applications"></a>Aplikacje stanowe

Starannie zaplanuj migrację aplikacji stanowych, aby uniknąć utraty danych lub nieoczekiwanego przestoju.

#### <a name="highly-available-applications"></a>Aplikacje o wysokiej dostępności

Niektóre aplikacje stanowe można wdrożyć w konfiguracji o wysokiej dostępności. Te aplikacje mogą kopiować dane między replikami. W przypadku użycia tego sortowania wdrożenia może być możliwe utworzenie nowego elementu członkowskiego w nowym klastrze AKS, a następnie przeprowadzenie migracji z minimalnym wpływem na wywołania podrzędne. Ogólnie rzecz biorąc, kroki migracji w tym scenariuszu są następujące:

1. Utwórz nową replikę pomocniczą w AKS.
2. Poczekaj na replikację danych.
3. Przejdź w tryb failover, aby utworzyć replikę pomocniczą nowej bazy.
4. Wskaż ruch do klastra AKS.

#### <a name="migrating-persistent-volumes"></a>Migrowanie woluminów trwałych

W przypadku migrowania istniejących woluminów trwałych do AKS na ogół wykonaj następujące kroki:

1. Przełączenie w stan spoczynku zapis do aplikacji. (Ten krok jest opcjonalny i wymaga przestoju).
2. Wykonaj migawki dysków.
3. Utwórz nowe dyski zarządzane na podstawie migawek.
4. Utwórz woluminy trwałe w AKS.
5. Zaktualizuj specyfikacje pod, aby [korzystać z istniejących woluminów](https://docs.microsoft.com/azure/aks/azure-disk-volume) zamiast PersistentVolumeClaims (statycznej aprowizacji).
6. Wdróż aplikację w usłudze AKS.
7. Legalizacj.
8. Wskaż ruch do klastra AKS.

> [!IMPORTANT]
> Jeśli nie zdecydujesz się na przełączenie w stan spoczynku, musisz zreplikować dane do nowego wdrożenia. W przeciwnym razie trafisz dane zapisane po wykonaniu migawek dysków.

Niektóre narzędzia Open Source mogą pomóc w tworzeniu dysków zarządzanych i migracji woluminów między klastrami Kubernetes:

* [Rozszerzenie do kopiowania dysków interfejsu wiersza polecenia platformy Azure](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopiuje i konwertuje dyski między grupami zasobów i regionami świadczenia usługi Azure.
* [Rozszerzenie interfejsu wiersza polecenia platformy Azure polecenia](https://github.com/yaron2/azure-kube-cli) wylicza woluminy usługi ACS Kubernetes i migruje je do klastra AKS.

#### <a name="azure-files"></a>Azure Files

W przeciwieństwie do dysków Azure Files można instalować na wielu hostach współbieżnie. W klastrze AKS platforma Azure i usługa Kubernetes nie uniemożliwiają tworzenia usługi na tym, że nadal używa klastra ACS. Aby zapobiec utracie danych i nieoczekiwanym zachowaniu, należy się upewnić, że klastry nie zapisują jednocześnie do tych samych plików.

Jeśli aplikacja może hostować wiele replik, które wskazują ten sam udział plików, postępuj zgodnie z krokami migracji bezstanowej i Wdróż definicje YAML w nowym klastrze. W przeciwnym razie jedno z możliwych rozwiązań migracji obejmuje następujące kroki:

1. Wdróż aplikację w AKS z liczbą replik równą 0.
2. Skaluj aplikację w usłudze ACS do wartości 0. (Ten krok wymaga przestoju).
3. Skaluj aplikację w AKS do 1.
4. Legalizacj.
5. Wskaż ruch do klastra AKS.

Jeśli chcesz rozpocząć od pustego udziału i utworzyć kopię danych źródłowych, możesz użyć [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) poleceń, aby przeprowadzić migrację danych.

### <a name="deployment-strategy"></a>Strategia wdrażania

Zalecamy używanie istniejącego potoku ciągłej integracji/ciągłego wdrażania, aby wdrożyć znaną dobrą konfigurację do AKS. Sklonuj istniejące zadania wdrażania i upewnij się, `kubeconfig` że wskazuje nowy klaster AKS.

Jeśli nie jest to możliwe, należy wyeksportować definicje zasobów z usługi ACS, a następnie zastosować je do AKS. Można użyć `kubectl` do eksportowania obiektów.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Kilka narzędzi typu "open source" może pomóc, w zależności od potrzeb wdrożenia:

* [Velero](https://github.com/heptio/ark) (To narzędzie wymaga Kubernetes 1,7).
* [Rozszerzenie interfejsu wiersza polecenia platformy Azure polecenia](https://github.com/yaron2/azure-kube-cli)
* [Przesunięcia](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Kroki migracji

1. [Utwórz klaster AKS](https://docs.microsoft.com/azure/aks/create-cluster) , korzystając Azure Portal z szablonu interfejsu wiersza polecenia platformy Azure lub Azure Resource Manager.

   > [!NOTE]
   > Znajdź przykładowe szablony Azure Resource Manager dla AKS w repozytorium [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) w witrynie GitHub.

2. Wprowadź wszelkie niezbędne zmiany w definicjach YAML. Na przykład Zastąp `apps/v1beta1` ciąg `apps/v1` opcją `Deployments`for.

3. [Migrowanie woluminów](#migrating-persistent-volumes) (opcjonalnie) z klastra usługi ACS do klastra AKS.

4. Użyj systemu ciągłej integracji/ciągłego wdrażania aplikacji do AKS. Lub użyj polecenia kubectl, aby zastosować definicje YAML.

5. Legalizacj. Upewnij się, że aplikacje działają zgodnie z oczekiwaniami i że wszystkie migrowane dane zostały skopiowane.

6. Przekieruj ruch. Zaktualizuj system DNS, aby wskazywał klientom wdrożenie AKS.

7. Zakończ zadania po migracji. Jeśli przeprowadzono migrację woluminów i nie chcesz, aby zapisywać w spoczynku, skopiuj te dane do nowego klastra.
