---
title: Wystąpień kontenera Azure i kontener aranżacji
description: Dowiedz się, jak Azure kontener wystąpień interakcję z orchestrators kontenera.
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/23/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: e1455cba004facfa03dca21544eec754f5dc60be
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165565"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Wystąpień kontenera Azure i orchestrators kontenera

Ze względu na ich niewielki rozmiar i orientację aplikacji kontenerów dobrze nadają w środowiskach agile dostarczania i architektury mikrousługi systemem. Zadanie automatyzacji i zarządzanie dużą liczbę kontenerów i sposób ich interakcji nosi nazwę *aranżacji*. Kontener popularnych orchestrators obejmują Kubernetes DC/OS i Docker Swarm.

Wystąpień kontenera Azure zawiera niektóre z podstawowych możliwości planowania aranżacji platform. I gdy nie obejmują usługi wyższa wartość, które zapewniają tych platform, wystąpień kontenera Azure może być uzupełniające się do nich. W tym artykule opisano zakres wystąpień kontenera Azure obsługuje i jakiego orchestrators kontener może korzystać z niego.

## <a name="traditional-orchestration"></a>Tradycyjny aranżacji

Standardowa definicji aranżacji obejmuje następujące zadania:

- **Planowanie**: danego obrazu kontenera i żądanie zasobu, Znajdź odpowiedniego komputera na którym ma być uruchamiany kontenera.
- **Koligacja/przeciwko-affinity**: określić, że zestaw kontenery powinien uruchomione w pobliżu innych (na wydajność) lub wystarczająco liniami (dostępności).
- **Monitorowanie kondycji**: Obejrzyj niepowodzeń kontenera i automatycznie Zaplanuj je ponownie.
- **Tryb failover**: Śledź co działa na każdym komputerze i Zaplanuj ponownie kontenerów z maszyn nie powiodło się do węzłów w prawidłowym stanie.
- **Skalowanie**: Dodawanie lub usuwanie wystąpień kontenera, aby dopasować żądanie, ręcznie lub automatycznie.
- **Sieć**: zapewnienia koordynacji kontenerów do komunikacji między wieloma maszynami hosta sieci nakładki.
- **Odnajdowanie usługi**: Włączanie kontenerów do zlokalizowania wzajemnie automatycznie, nawet podczas przenoszenia między komputerami hostów i zmiana adresów IP.
- **Koordynowane uaktualnień aplikacji**: Zarządzanie uaktualnień kontenera, aby uniknąć przestoju aplikacji i włączyć wycofywanie, jeśli jakaś nieprawidłowość.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orchestration z wystąpień kontenera platformy Azure: warstwowego podejścia

Wystąpień kontenera Azure umożliwia warstwowego podejścia do aranżacji, podając wszystkie funkcje zarządzania i planowania wymagane do uruchomienia jeden kontener, umożliwiając platformy orchestrator do zarządzania zadaniami wielu kontenera na nim.

Ponieważ podstawowej infrastruktury dla wystąpień kontenera jest zarządzana przez platformę Azure, platformy orchestrator nie trzeba zajęcie się z znajdowanie maszyny do odpowiedniego hosta, na którym ma być uruchamiany jeden kontener. Elastyczność chmury gwarantuje, że co jest zawsze dostępna. Zamiast tego orchestrator można skoncentrować się na zadaniach, które upraszczają programowanie architektury usługi kontenera, w tym skalowanie i skoordynowany sposób uaktualnienia.

## <a name="potential-scenarios"></a>Potencjalne scenariusze

Podczas integracji programu orchestrator z wystąpień kontenera Azure jest nadal rodzącego, przewidujemy, że mogą pojawić się w kilku różnych środowiskach:

### <a name="orchestration-of-container-instances-exclusively"></a>Orchestration kontenera wyłącznie wystąpień

Ponieważ szybkie rozpoczęcie i naliczać opłaty przez drugą, środowisko wyłącznie w oparciu o wystąpień kontenera Azure oferuje najszybszy sposób rozpoczęcie pracy i postępowania w przypadku obciążeń wysokiej zmiennej.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombinacja wystąpień kontenera i kontenerów na maszynach wirtualnych

Długotrwałe stabilna obciążeń organizowanie kontenery w klastrze dedykowanych maszyn wirtualnych jest zwykle tańsze niż uruchomienie tego samego kontenery z wystąpień kontenera platformy Azure. Jednak wystąpień kontenera oferuje doskonałe rozwiązanie do szybkiego rozszerzania i instytucje ogólną wydajność na wypadek nieoczekiwanych lub krótkim okresie wzrostów użycia.

Zamiast skalowania liczby maszyn wirtualnych w klastrze, wdrażanie dodatkowych kontenerów na tych komputerach, orchestrator można po prostu zaplanować dodatkowe kontenery w wystąpień kontenera platformy Azure, a następnie usuwania ich, gdy są one już wymagane.

## <a name="sample-implementation-azure-container-instances-connector-for-kubernetes"></a>Przykładowe zastosowanie: Azure łącznik wystąpień kontenera dla Kubernetes

Aby zademonstrować, jak kontenera platformy orchestration można zintegrować z wystąpień kontenera platformy Azure, możemy Rozpoczęto tworzenie [łącznik próbki dla Kubernetes][aci-connector-k8s].

Naśladuje łącznika Kubernetes [kubelet] [ kubelet-doc] przez zarejestrowanie jako węzeł o nieograniczonej pojemności i wysyła tworzenie [stanowiskami] [ pod-doc] jako kontenera grupy wystąpień kontenera platformy Azure.

Łączniki dla innych orchestrators mogą być zbudowane integrujące się podobnie z podstawowych platform połączyć możliwości programu orchestrator interfejsu API z szybkość i łatwość zarządzania kontenerami w wystąpień kontenera platformy Azure.

> [!WARNING]
> Łącznik ACI dla Kubernetes *eksperymentalne* i nie powinna być używana w środowisku produkcyjnym.

## <a name="next-steps"></a>Kolejne kroki

Tworzenie Twojego pierwszego kontenera z wystąpień kontenera Azure za pomocą [Przewodnik Szybki Start](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/