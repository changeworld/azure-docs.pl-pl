---
title: Migrowanie z usługi Azure Container Service (ACS) do usługi Azure Kubernetes Service (AKS)
description: Migrowanie z usługi Azure Container Service (ACS) do usługi Azure Kubernetes Service (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: dcee8da943603fb0978caf9992be76347ca197d6
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65977711"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrowanie z usługi Azure Container Service (ACS) do usługi Azure Kubernetes Service (AKS)

Ten artykuł ułatwia planowanie i wykonywanie pomyślną migrację między usługi Azure Container Service (ACS) przy użyciu usługi Kubernetes i usługi Azure Kubernetes Service (AKS). Ułatwiające podejmowanie kluczowych decyzji, ten przewodnik szczegółowo różnice między usług ACS i usługi AKS i zawiera omówienie procesu migracji.

## <a name="differences-between-acs-and-aks"></a>Różnice między usług ACS i usługi AKS

Usługi ACS oraz AKS różnią się w niektórych kluczowych obszarów, które mają wpływ na migrację. Przed migrację należy przejrzeć i zamierzasz rozwiązać następujące różnice:

* Użyj węzłów AKS [usługi managed disks](../virtual-machines/windows/managed-disks-overview.md).
    * Dyski niezarządzane muszą zostać przekonwertowane, zanim będzie możliwe dołączenie ich do węzłów AKS.
    * Niestandardowe `StorageClass` obiektów dla usługi Azure disks zmieniła się z `unmanaged` do `managed`.
    * Wszelkie `PersistentVolumes` należy używać `kind: Managed`.
* Obsługuje AKS [wiele pul węzłów](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (obecnie w wersji zapoznawczej).
* Węzły oparte na systemie Windows Server jest obecnie oferowana [(wersja zapoznawcza) w usłudze AKS](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* AKS obsługuje ograniczony zbiór [regionów](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* AKS to usługa zarządzana przy płaszczyznę kontroli hostowanej platformy Kubernetes. Może być konieczne zmodyfikowanie aplikacji, jeśli wcześniej zmodyfikowano konfigurację serwerów głównych usługi ACS.

## <a name="differences-between-kubernetes-versions"></a>Różnice między wersjami usługi Kubernetes

W przypadku migrowania do nowszej wersji rozwiązania Kubernetes (na przykład z 1.7.x do 1.9.x), przejrzyj następujące zasoby, aby poznać pewne zmiany w interfejsie API rozwiązania Kubernetes:

* [Migrowanie ThirdPartyResource do CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Zmiany obciążenia interfejsu API w wersji 1.8 i 1.9](https://kubernetes.io/docs/reference/workloads-18-19/)

## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

### <a name="agent-pools"></a>Pule agenta

Mimo że AKS zarządza na płaszczyźnie kontroli rozwiązania Kubernetes, nadal definiujesz rozmiaru i liczby węzłów, które mają zostać objęte nowego klastra. Przy założeniu, że chcesz, aby mapowanie 1:1 z usługi ACS do usługi AKS, można przechwycić informacje istniejącego węzła usługi ACS. Podczas tworzenia nowego klastra AKS, należy użyć tych danych.

Przykład:

| Name (Nazwa) | Count | Rozmiar maszyny wirtualnej | System operacyjny |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standardowa_D2_v2 | Windows |

Ponieważ dodatkowe maszyny wirtualne zostaną wdrożone w ramach subskrypcji, podczas migracji, należy sprawdzić, czy limity i przydziały są wystarczające dla tych zasobów. 

Aby uzyskać więcej informacji, zobacz [limity usług i subskrypcji platformy Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits). Aby sprawdzić bieżące limity przydziału, w witrynie Azure portal przejdź do [bloku subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), wybierz swoją subskrypcję, a następnie wybierz **użycie i przydziały**.

### <a name="networking"></a>Networking

W kontekście aplikacji złożonych będzie zazwyczaj migrować wraz z upływem czasu, a nie w całości. Oznacza to stare i nowe środowiska może być konieczne komunikują się za pośrednictwem sieci. Aplikacje, które wcześniej używano `ClusterIP` usługi do komunikacji mogą wymagać uwidocznienia jako typ `LoadBalancer` i być odpowiednio zabezpieczony.

Aby ukończyć migrację, należy wskazać klientom nowe usługi, które są uruchomione w usłudze AKS. Firma Microsoft zaleca, przekierowywanie ruchu, aktualizując DNS, aby wskazywał modułu równoważenia obciążenia, który znajduje się przed klastra usługi AKS.

### <a name="stateless-applications"></a>Bezstanowe aplikacji

Migracja aplikacji bezstanowych jest najprostszym przypadku. Będzie zastosowanie do definicji YAML do nowego klastra, upewnij się, że wszystko działa zgodnie z oczekiwaniami i przekierowywanie ruchu do aktywowania nowego klastra.

### <a name="stateful-applications"></a>Aplikacje stanowe

Należy dokładnie zaplanować migrację aplikacji stanowych, aby uniknąć utraty danych lub nieoczekiwany Przestój.

#### <a name="highly-available-applications"></a>Aplikacje o wysokiej dostępności

Możesz wdrożyć niektóre aplikacje stanowe w konfiguracji o wysokiej dostępności. Te aplikacje można skopiować dane między replikami. Jeśli obecnie używasz tego rodzaju wdrożenia, można utworzyć nowy element członkowski w nowym klastrze AKS i następnie przeprowadzić migrację z minimalnym wpływem na podrzędne obiekty wywołujące. Ogólnie rzecz biorąc procedura migracji w tym scenariuszu jest następująca:

1. Utwórz nową replikę pomocniczą w usłudze AKS.
2. Poczekaj, aż replikowanie danych.
3. Zakończyć się niepowodzeniem wprowadzić nową podstawową replikę pomocniczą.
4. Przesyłały ruch do klastra usługi AKS.

#### <a name="migrating-persistent-volumes"></a>Migrowanie woluminów stałych

W przypadku migrowania istniejących woluminach trwałego usłudze AKS, będzie ogólnie wykonaj następujące kroki:

1. Przełączanie w stan spoczynku zapisuje do aplikacji. (Ten krok jest opcjonalny i wymaga przestoju).
2. Wykonywanie migawek dysków.
3. Utwórz nowe dyski zarządzane na podstawie migawki.
4. Utwórz woluminy trwałe w usłudze AKS.
5. Specyfikacje zasobników, aby zaktualizować [korzysta z istniejących woluminów](https://docs.microsoft.com/azure/aks/azure-disk-volume) zamiast PersistentVolumeClaims (udostępnianie statycznych).
6. Wdróż aplikację w usłudze AKS.
7. Sprawdzanie poprawności.
8. Przesyłały ruch do klastra usługi AKS.

> [!IMPORTANT]
> Jeśli nie chcesz przełączyć w stan spoczynku zapisy, należy do replikowania danych do nowego wdrożenia. W przeciwnym razie będzie przeoczyć dane, które zostały zapisane po trwało migawki dysków.

Niektóre narzędzia typu open-source może ułatwić tworzenie dysków zarządzanych i Migrowanie woluminów między klastrów Kubernetes:

* [Rozszerzenie kopii dyskowej interfejsu wiersza polecenia platformy Azure](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopiuje i konwertuje dyski między grupami zasobów i regiony platformy Azure.
* [Rozszerzenie interfejsu wiersza polecenia klastra kubernetes w usłudze Azure](https://github.com/yaron2/azure-kube-cli) wylicza woluminy ACS Kubernetes i wykonuje ich migrację do klastra usługi AKS.

#### <a name="azure-files"></a>Azure Files

W przeciwieństwie do dysków usługi Azure Files mogą być instalowane na wielu hostach równolegle. W klastrze AKS platformy Azure i Kubernetes nie uniemożliwiają tworzenie zasobników, która nadal korzysta z klastrem usługi ACS. Aby zapobiec utracie danych i nieoczekiwane zachowanie, upewnij się, że klastry nie zapisywać w tych samych plików w tym samym czasie.

Jeśli Twoja aplikacja może obsługiwać wiele replik, które wskazują na ten sam udział plików, wykonaj kroki migracji bezstanowe i wdrażać swoje definicje YAML do nowego klastra. W przeciwnym razie jedno z podejść możliwej migracji obejmuje następujące czynności:

1. Wdróż aplikację w usłudze AKS repliki liczba 0.
2. Skalowanie aplikacji na ACS 0. (Ten krok wymaga przestojów).
3. Skalowanie aplikacji w usłudze AKS maksymalnie 1.
4. Sprawdzanie poprawności.
5. Przesyłały ruch do klastra usługi AKS.

Jeśli chcesz uruchomić za pomocą pustego udziału i tworzy kopię źródła danych, możesz użyć [ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) polecenia, aby przeprowadzić migrację danych.

### <a name="deployment-strategy"></a>Strategia wdrażania

Zalecamy użycie istniejącym potokiem ciągłej integracji/ciągłego wdrażania na wdrażanie znanej dobrej konfiguracji w usłudze AKS. Klonowanie istniejących zadań wdrożenia i upewnij się, że `kubeconfig` wskazuje na nowy klaster AKS.

Jeśli nie jest to możliwe, wyeksportuj definicje zasobów z usługi ACS, a następnie zastosować je do usługi AKS. Możesz użyć `kubectl` eksportu.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Kilku narzędzi typu open-source mogą pomóc w zależności od potrzeb wdrożenia:

* [Velero](https://github.com/heptio/ark) (to narzędzie wymaga Kubernetes 1.7).
* [Rozszerzenie klastra kubernetes w usłudze interfejsu wiersza polecenia platformy Azure](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Kroki migracji

1. [Tworzenie klastra AKS](https://docs.microsoft.com/azure/aks/create-cluster) za pośrednictwem witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub szablonu usługi Azure Resource Manager.

   > [!NOTE]
   > Znajdź przykładowe szablony usługi Azure Resource Manager dla usługi AKS w [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) repozytorium w witrynie GitHub.

2. Wprowadź niezbędne zmiany do definicji kodu YAML. Na przykład Zastąp ciąg `apps/v1beta1` z `apps/v1` dla `Deployments`.

3. [Migrowanie woluminów](#migrating-persistent-volumes) (opcjonalnie) z klastra usługi ACS, do klastra usługi AKS.

4. System ciągłej integracji/ciągłego wdrażania umożliwia wdrażanie aplikacji w usłudze AKS. Lub użyj narzędzia kubectl, aby zastosować definicje kodu YAML.

5. Sprawdzanie poprawności. Upewnij się, że Twoje aplikacje działają zgodnie z oczekiwaniami oraz że wszystkie zmigrowane dane zostały skopiowane.

6. Przekierowywanie ruchu. Aktualizowanie systemu DNS, aby wskazać klientom wdrożeniu usługi AKS.

7. Zakończ zadania po migracji. Jeśli migracji woluminów i nieinstalowanie zapisów w stan spoczynku, te dane są kopiowane do nowego klastra.
