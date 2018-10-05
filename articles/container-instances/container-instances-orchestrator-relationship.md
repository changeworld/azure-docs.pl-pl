---
title: Usługa Azure Container Instances i aranżacji kontenerów
description: Dowiedz się, jak usługa Azure container wystąpień wchodzić w interakcje z koordynatorów kontenerów.
services: container-instances
author: seanmck
ms.service: container-instances
ms.topic: article
ms.date: 10/05/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c17bdb5a81640a7162ae735a4633a31cdfffbb1d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803515"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Usługa Azure Container Instances i koordynatorów kontenerów

Ze względu na niewielki rozmiar i orientację aplikacji kontenery są dobrze nadaje się dla środowisk agile dostarczania i architektury oparte na mikrousługach. Zadania automatyzacji i zarządzania dużą liczbą kontenerów i ich interakcji jest znany jako *aranżacji*. Popularnych koordynatorów kontenerów uwzględnić Kubernetes, DC/OS i Docker Swarm.

Usługa Azure Container Instances zapewnia niektóre podstawowe możliwości planowania platform aranżacji. I gdy nie obejmują usługi wyższa wartość, które dostarczają te platformy, usługi Azure Container Instances można uzupełniające się do nich. W tym artykule opisano zakresu usługi Azure Container Instances obsługuje i jak koordynatorów kontenerów może wchodzić w interakcję z nią.

## <a name="traditional-orchestration"></a>Tradycyjne aranżacji

Standardowa definicji aranżacji obejmuje następujące zadania:

- **Planowanie**: podany obraz kontenera i żądania zasobu, Znajdź odpowiedni komputerze, na którym do uruchamiania kontenera.
- **Koligacja/przed-affinity**: Określ, że zestaw kontenerów powinny być uruchamiane w pobliżu innych (na wydajność) lub wystarczająco między liniami (dostępność).
- **Monitorowanie kondycji**: Obejrzyj dla kontenera, błędy i automatycznie zmienić ich harmonogram.
- **Tryb failover**: Śledź co działa na każdym komputerze i ponownie zaplanować kontenerów z maszyn nie powiodło się do węzłów w dobrej kondycji.
- **Skalowanie**: Dodawanie lub usuwanie wystąpienia kontenera, aby dopasować żądanie, ręcznie lub automatycznie.
- **Sieć**: podanie koordynowanie kontenerów, aby komunikować się między wieloma maszynami hostów sieci nakładki.
- **Odnajdowanie usługi**: Włączanie kontenerów, aby zlokalizować sobie nawzajem automatycznie, nawet przy wzroście przenoszeniu ich do komputerów-hostów, a zmiana adresów IP.
- **Skoordynowanych uaktualnień aplikacji**: zarządzania uaktualnieniami kontenera, aby uniknąć przerwy w działaniu aplikacji, a następnie włącz wycofywania, jeśli coś pójdzie nie tak.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Organizowanie dzięki usłudze Azure Container Instances: warstwowego podejścia

Usługa Azure Container Instances umożliwia warstwowego podejścia do aranżacji, zapewniając wszystkie funkcje zarządzania i planowania wymagane do uruchomienia jednym kontenerze, zapewniając platformy programu orchestrator, aby zarządzać zadaniami wielokontenerowych na jego podstawie.

Ponieważ podstawowej infrastruktury dla container instances jest zarządzana przez platformę Azure, platformą orchestrator nie musi dotyczyć samej znalezienie maszyny do odpowiedniego hosta, na którym należy uruchomić jeden kontener. Elastyczność chmury gwarantuje, że ten właśnie jest zawsze dostępna. Zamiast tego koordynatora skupić się na zadania, które upraszczają proces tworzenia architektury obsługującej wiele kontenerów, na przykład, skalowania i skoordynowanych uaktualnień.

## <a name="potential-scenarios"></a>Możliwe scenariusze

Podczas integracji programu orchestrator za pomocą usługi Azure Container Instances jest nadal narodzin, zamierzamy, mogą pojawić się w kilku różnych środowiskach:

### <a name="orchestration-of-container-instances-exclusively"></a>Organizowanie kontenerów wystąpień wyłącznie

Ponieważ szybkie rozpoczynanie pracy i są naliczane za sekundę, środowisko wyłącznie w oparciu o usługi Azure Container Instances oferuje najszybszy sposób na rozpoczęcie pracy i radzenia sobie z bardzo zmienną obciążeń.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombinacja wystąpienia kontenerów i kontenery na maszynach wirtualnych

Dla obciążeń długotrwałych i stabilne i organizowanie kontenerów w klastrze składającym się z dedykowanych maszynach wirtualnych jest zazwyczaj tańszy niż uruchamianie tych samych kontenerów za pomocą usługi Azure Container Instances. Jednak container instances oferuje doskonałe rozwiązanie dla szybkiego rozwijania i instytucje łączna pojemność radzenia sobie z nieoczekiwanymi lub krótkotrwałych skoków użycia.

Zamiast skalowania w poziomie liczby maszyn wirtualnych w klastrze, a następnie wdrażać dodatkowe kontenery na tych komputerach, koordynatora można po prostu zaplanować dodatkowe kontenery w usłudze Azure Container Instances i usuwać je, gdy są one już wymagane.

## <a name="sample-implementation-virtual-kubelet-for-kubernetes"></a>Przykładowe zastosowanie: rozwiązania Virtual Kubelet dla rozwiązania Kubernetes

[Rozwiązania Virtual Kubelet] [ aci-connector-k8s] projekt pokazuje, jak platform aranżacji kontenerów można zintegrować z usługą Azure Container Instances.

Rozwiązania Virtual Kubelet naśladuje Kubernetes [agenta kubelet] [ kubelet-doc] przez rejestrowanie jako węzeł z nieograniczoną pojemność i wysyła tworzenie [zasobników] [ pod-doc] jako grupy kontenerów w usłudze Azure Container Instances.

Łączniki dla innych orkiestratorów mogą być zbudowane analogicznie zintegrowanych z platform podstawowych połączyć możliwości programu orchestrator API szybkość i łatwość zarządzania kontenerami w usłudze Azure Container Instances.

## <a name="next-steps"></a>Kolejne kroki

Utwórz swój pierwszy kontener przy użyciu usługi Azure Container Instances [przewodnika Szybki Start](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/