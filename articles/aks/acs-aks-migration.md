---
title: Migrowanie z usługi Azure Container Service (ACS) do usługi Azure Kubernetes Service (AKS)
description: Migrowanie z usługi Azure Container Service (ACS) do usługi Azure Kubernetes Service (AKS)
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 910c96988ec0a8b8aa7b6ac8ce287c4fdc59e177
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649972"
---
# <a name="migrating-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrowanie z usługi Azure Container Service (ACS) do usługi Azure Kubernetes Service (AKS)

Celem tego dokumentu jest ułatwiający zaplanowanie i wykonanie pomyślną migrację między Azure Container Service przy użyciu rozwiązania Kubernetes (ACS) i Azure Kubernetes Service (AKS). Ten przewodnik szczegółowo różnice między usług ACS i AKS, zawiera omówienie procesu migracji i powinny ułatwić podejmowanie kluczowych decyzji.

## <a name="differences-between-acs-and-aks"></a>Różnice między usług ACS i usługi AKS

Usługi ACS oraz AKS różnią się w niektórych kluczowych obszarów, które mają wpływ na migrację. Należy przejrzeć i zaplanować następujące różnice, przed migracją żadnych adresów.

* Użyj węzłów AKS [Managed Disks](../virtual-machines/windows/managed-disks-overview.md)
    * Usługa Unmanaged disks będzie muszą zostać przekonwertowane, przed ich może zostać dołączony do węzłów AKS
    * Niestandardowe `StorageClass` obiektów dla usługi Azure disks należy można zmienić z `unmanaged` do `managed`
    * Wszelkie `PersistentVolumes` będą musieli używać `kind: Managed`
* AKS aktualnie obsługuje tylko jeden agent puli
* Węzły na serwerze z systemem Windows jest obecnie oferowana [prywatnej wersji zapoznawczej](https://azure.microsoft.com/blog/kubernetes-on-azure/)
* Zapoznaj się z listą AKS [obsługiwane regiony](https://docs.microsoft.com/azure/aks/container-service-quotas)
* AKS to usługa zarządzana przy płaszczyznę kontroli hostowanej platformy Kubernetes. Może być konieczne zmodyfikowanie aplikacji, jeśli wcześniej zmodyfikowano konfigurację serwerów głównych usługi ACS

### <a name="differences-between-kubernetes-versions"></a>Różnice między wersjami usługi Kubernetes

Jeśli przenosisz do nowszej wersji rozwiązania Kubernetes (np: 1.7.x do 1.9.x), istnieją pewne zmiany w interfejsie API k8s, które wymagają Twojej uwagi.

* [Migrowanie ThirdPartyResource do CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Zmiany obciążenia interfejsu API w wersji 1.8 i 1.9](https://kubernetes.io/docs/reference/workloads-18-19/).

## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

### <a name="agent-pools"></a>Pule agentów

Gdy AKS zarządza na płaszczyźnie kontroli rozwiązania Kubernetes, nadal definiujesz rozmiaru i liczby węzłów, które mają zostać uwzględnione w nowego klastra. Przy założeniu, że chcesz, aby mapowanie 1:1 z usługi ACS do usługi AKS, można przechwycić informacje istniejącego węzła usługi ACS. Podczas tworzenia nowego klastra AKS, użyjemy tych danych.

Przykład:

| Name (Nazwa) | Licznik | Rozmiar maszyny wirtualnej | System operacyjny |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standardowa_D2_v2 | Windows |

Ponieważ dodatkowe maszyny wirtualne zostaną wdrożone w ramach subskrypcji, podczas migracji, należy sprawdzić, czy limity i przydziały są wystarczające dla tych zasobów. Dowiedz się więcej, przeglądając [limity usług i subskrypcji platformy Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits). Aby sprawdzić bieżące limity przydziału, przejdź do [bloku subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure portal wybierz swoją subskrypcję, a następnie wybierz `Usage + quotas`.

### <a name="networking"></a>Networking

W kontekście aplikacji złożonych będzie zazwyczaj migrować wraz z upływem czasu, a nie w całości. Oznacza to stare i nowe środowiska może być konieczne do komunikowania się za pośrednictwem sieci. Aplikacje, które zostały wcześniej mógł używać `ClusterIP` usługi do komunikacji może muszą być udostępniane jako typ `LoadBalancer` i odpowiednio zabezpieczony.

Aby ukończyć migrację, należy wskazać klientom nowe usługi działające w usłudze AKS. Jest to zalecany sposób przekierowywania ruchu sieciowego, aktualizując DNS, aby wskazywał modułu równoważenia obciążenia, który znajduje się przed klastra usługi AKS.

### <a name="stateless-applications"></a>Bezstanowe aplikacji

Migracja aplikacji bezstanowych jest najprostszym przypadku. Będzie zastosowanie do definicji YAML do nowego klastra, zweryfikuj, czy wszystko działa zgodnie z oczekiwaniami i przekierowywania ruchu sieciowego, aby uaktywnić nowego klastra.

### <a name="stateful-applications"></a>Aplikacje stanowe

Stanowa migrowanie aplikacji wymaga starannego planowania w celu uniknięcia utraty danych lub nieoczekiwany Przestój.

#### <a name="highly-available-applications"></a>Aplikacje o wysokiej dostępności

Niektóre aplikacje stanowe można wdrożyć w konfiguracji o wysokiej dostępności i może kopiować dane między replikami. Opisuje w bieżącym wdrożeniu, może być możliwe, aby utworzyć nowy element członkowski w nowym klastrze AKS i migracja z minimalnym wpływem na wywoływania podrzędnego. Kroki migracji, w tym scenariuszu zazwyczaj są:

1. Utwórz nową replikę pomocniczą w usłudze AKS
2. Oczekiwanie na replikowanie danych
3. Się kończyć niepowodzeniem ponad replikę pomocniczą nowego podstawowego
4. Wskaż ruchu klastra AKS

#### <a name="migrating-persistent-volumes"></a>Migrowanie woluminów stałych

Istnieje kilka czynników, które należy wziąć pod uwagę, jeśli przenosisz istniejące woluminy trwałego AKS. Ogólnie rzecz biorąc etapy są:

1. (Opcjonalnie) Przełączanie w stan spoczynku zapisuje do aplikacji (wymagany jest przestój)
2. Migawki dysków
3. Tworzenie nowych dysków zarządzanych na podstawie migawki
4. Utwórz trwałe woluminy w usłudze AKS
5. Specyfikacje zasobników, aby zaktualizować [korzysta z istniejących woluminów](https://docs.microsoft.com/azure/aks/azure-disk-volume) zamiast PersistentVolumeClaims (udostępnianie statyczne)
6. Wdrażanie aplikacji w usłudze AKS
7. Walidacja
8. Wskaż ruchu klastra AKS

> **Ważne**: Jeśli nie chcesz przełączyć w stan spoczynku zapisy, konieczne będzie replikowania danych do nowego wdrożenia, jak będzie brakować danych, który został napisany od migawki dysku

Istnieją narzędzi typu open source, które mogą pomóc w twórz dyski Managed Disks i Migrowanie woluminów między klastrów Kubernetes.

* [noelbundick/azure rozszerzenie interfejsu wiersza polecenia dysk](https://github.com/noelbundick/azure-cli-disk-copy-extension) — kopiowanie i konwersji dysków w różnych grupach zasobów i regiony platformy Azure
* [yaron2 /-kubernetes — interfejs wiersza polecenia azure](https://github.com/yaron2/azure-kube-cli) — wyliczanie woluminy ACS Kubernetes i przeprowadzić ich migrację do klastra usługi AKS

#### <a name="azure-files"></a>Azure Files

W przeciwieństwie do dysków usługi Azure Files mogą być instalowane na wielu hostach równolegle. Azure ani Kubernetes nie uniemożliwia tworzenie zasobnik w klastrze AKS, do której jest nadal używana przez Twój klaster usługi ACS. Aby zapobiec utracie danych i nieoczekiwane zachowanie, należy upewnić się, że oba klastry nie są zapisywanie do tych samych plików w tym samym czasie.

Jeśli Twoja aplikacja może obsługiwać wiele replik wskazuje ten sam udział plików, można postępuj zgodnie z instrukcjami bezstanowe migracji i wdrażać swoje definicje YAML do nowego klastra.

W przeciwnym razie jedno z podejść możliwej migracji obejmuje następujące czynności:

1. Wdrażanie aplikacji w usłudze AKS repliki liczba 0
2. Skalowanie aplikacji na ACS 0 (wymaga przestojów)
3. Skalowanie aplikacji w usłudze AKS maksymalnie 1
4. Walidacja
5. Wskaż ruchu klastra AKS

W przypadkach, w którym ma się rozpoczynać udział pusty, a następnie utwórz kopię źródła danych, można użyć [ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) polecenia, aby przeprowadzić migrację danych.

### <a name="deployment-strategy"></a>Strategia wdrażania

Zalecaną metodą jest istniejącym potokiem ciągłej integracji/ciągłego wdrażania umożliwia wdrażanie znanej dobrej konfiguracji w usłudze AKS. Można sklonować istniejące zadania wdrażania i upewnij się, że Twoje `kubeconfig` wskazuje na nowy klaster AKS.

W przypadkach, gdzie to nie to możliwe należy wyeksportować definicję zasobu z usługi ACS, a następnie zastosować je do usługi AKS. Możesz użyć `kubectl` eksportu.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Dostępne są również kilka narzędzi typu open source, które mogą pomóc w zależności od potrzeb:

* [heptio/ark](https://github.com/heptio/ark) -wymaga k8s 1.7
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli)
* [mhausenblas/reshifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Kroki migracji

### <a name="1-create-an-aks-cluster"></a>1. Tworzenie klastra AKS

Możesz wykonać dokumenty pomocne przy [Tworzenie klastra AKS](https://docs.microsoft.com/azure/aks/create-cluster) przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub szablonu usługi Resource Manager.

> Możesz znaleźć przykładowe szablony usługi Azure Resource Manager dla usługi AKS w [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) repozytorium w witrynie GitHub

### <a name="2-modify-applications"></a>2. Korekty w aplikacjach

Wprowadzać żadnych zmian niezbędnych do definicji kodu YAML. Przykład: zamienianie `apps/v1beta1` z `apps/v1` dla `Deployments`

### <a name="3-optional-migrate-volumes"></a>3. (Opcjonalnie) Migrowanie woluminów

Migrowanie woluminów z klastra usługi ACS do klastra usługi AKS. Więcej szczegółów można znaleźć w [Migrowanie woluminów trwałego](#migrating-persistent-volumes) sekcji.

### <a name="4-deploy-applications"></a>4. Wdrażanie aplikacji

System ciągłej integracji/ciągłego wdrażania umożliwia wdrażanie aplikacji w usłudze AKS lub użyj narzędzia kubectl, aby zastosować definicje kodu YAML.

### <a name="5-validate"></a>5. Walidacja

Zweryfikuj, czy aplikacje działają zgodnie z oczekiwaniami oraz że wszystkie zmigrowane dane zostały skopiowane.

### <a name="6-redirect-traffic"></a>6. Przekierowywanie ruchu

Aktualizowanie systemu DNS, aby wskazać klientom wdrożeniu usługi AKS.

### <a name="7-post-migration-tasks"></a>7. Zadania po migracji

Jeśli migracji woluminów i nieinstalowanie zapisów w stan spoczynku, należy te dane są kopiowane do nowego klastra.
