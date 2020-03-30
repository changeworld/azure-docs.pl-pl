---
title: Wystąpienia kontenera i aranżacji kontenera
description: Dowiedz się, jak wystąpienia kontenerów platformy Azure współdziałają z koordynatorami kontenerów.
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: f3f8693d1a9a12e7c35d126ab3e3ca53448e5e40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533659"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Wystąpienia kontenerów platformy Azure i koordynatorzy kontenerów

Ze względu na ich mały rozmiar i orientację aplikacji kontenery są dobrze dostosowane do środowisk dostarczania agile i architektur opartych na mikrousługach. Zadanie automatyzacji i zarządzania dużą liczbą kontenerów i sposobu ich interakcji jest znane jako *aranżacja*. Popularne orchestrators kontenera obejmują Kubernetes, DC/OS i Docker Swarm.

Wystąpienia kontenera platformy Azure udostępnia niektóre z podstawowych możliwości planowania platform aranżacji. I chociaż nie obejmuje usług o wyższej wartości, które zapewniają te platformy, wystąpienia kontenera platformy Azure mogą być uzupełniające się do nich. W tym artykule opisano zakres obsługi wystąpień kontenera platformy Azure i sposób, w jaki koordynatorzy pełnego kontenera mogą z nim współpracować.

## <a name="traditional-orchestration"></a>Tradycyjna aranżacja

Standardowa definicja aranżacji obejmuje następujące zadania:

- **Planowanie:** Biorąc pod uwagę obraz kontenera i żądanie zasobu, znajdź odpowiedni komputer, na którym ma zostać uruchomiony kontener.
- **Koligacja/Nie koligacja:** Określ, że zestaw kontenerów powinien działać w pobliżu siebie (dla wydajności) lub wystarczająco daleko od siebie (dla dostępności).
- **Monitorowanie stanu**zdrowia: Obserwuj awarie kontenerów i automatycznie prześledz je.
- **Tryb failover:** Śledzić, co jest uruchomione na każdym komputerze i ponownie wyśl kontenery z maszyn, które uległy awarii do zdrowych węzłów.
- **Skalowanie:** Dodaj lub usuń wystąpienia kontenera, aby dopasować je ręcznie lub automatycznie.
- **Sieć:** Udostępnij sieć nakładek do koordynowania kontenerów do komunikowania się na wielu komputerach hosta.
- **Odnajdowanie usługi:** Włącz kontenery, aby automatycznie lokalizowali się nawzajem, nawet gdy przemieszczają się między komputerami hosta i zmieniają adresy IP.
- **Skoordynowane uaktualnienia aplikacji:** Zarządzanie uaktualnieniami kontenerów w celu uniknięcia przestojów aplikacji i włączanie wycofywania, jeśli coś pójdzie nie tak.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Aranżacja za pomocą wystąpień kontenera platformy Azure: podejście warstwowe

Wystąpienia kontenera platformy Azure umożliwia warstwowe podejście do aranżacji, zapewniając wszystkie możliwości planowania i zarządzania wymagane do uruchomienia pojedynczego kontenera, umożliwiając jednocześnie platformom aranżatora zarządzanie zadaniami wielu kontenerów.

Ponieważ podstawowa infrastruktura dla wystąpień kontenerów jest zarządzana przez platformę Azure, platforma programu orchestrator nie musi zajmować się znajdowaniem odpowiedniego komputera hosta, na którym można uruchomić pojedynczy kontener. Elastyczność chmury zapewnia, że jest zawsze dostępna. Zamiast tego orchestrator można skupić się na zadania, które upraszczają rozwój architektur wielu kontenerów, w tym skalowanie i skoordynowane uaktualnienia.

## <a name="scenarios"></a>Scenariusze

Podczas gdy integracja koordynatora z wystąpieniami kontenerów platformy Azure wciąż się rozwija, przewidujemy, że pojawi się kilka różnych środowisk:

### <a name="orchestration-of-container-instances-exclusively"></a>Aranżacja wystąpień kontenerów wyłącznie

Ponieważ uruchamiają się szybko i rozliczają się po drugim, środowisko oparte wyłącznie na wystąpieniach kontenera platformy Azure oferuje najszybszy sposób na rozpoczęcie pracy i radzenie sobie z bardzo zmiennymi obciążeniami.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombinacja wystąpień kontenerów i kontenerów w maszynach wirtualnych

W przypadku długotrwałych, stabilnych obciążeń organizowanie kontenerów w klastrze dedykowanych maszyn wirtualnych jest zazwyczaj tańsze niż uruchamianie tych samych kontenerów z wystąpieniami kontenera platformy Azure. Jednak wystąpienia kontenerów oferują doskonałe rozwiązanie do szybkiego rozszerzania i kontraktowania ogólnej pojemności, aby poradzić sobie z nieoczekiwanymi lub krótkotrwałymi skokami użycia.

Zamiast skalować liczbę maszyn wirtualnych w klastrze, a następnie wdrażając dodatkowe kontenery na tych komputerach, koordynator może po prostu zaplanować dodatkowe kontenery w wystąpieniach kontenerów platformy Azure i usunąć je, gdy już nie są, Potrzebne.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Przykładowa implementacja: węzły wirtualne usługi Azure Kubernetes Service (AKS)

Aby szybko skalować obciążenia aplikacji w klastrze [usługi Azure Kubernetes](../aks/intro-kubernetes.md) (AKS), można użyć *węzłów wirtualnych utworzonych* dynamicznie w wystąpieniach kontenera platformy Azure. Węzły wirtualne umożliwiają komunikację sieciową między zasobnikami uruchamianym w aci i klastrze AKS. 

Węzły wirtualne obsługują obecnie wystąpienia kontenera systemu Linux. Wprowadzenie do węzłów wirtualnych przy użyciu [interfejsu wiersza polecenia platformy Azure](https://go.microsoft.com/fwlink/?linkid=2047538) lub [witryny Azure portal](https://go.microsoft.com/fwlink/?linkid=2047545).

Węzły wirtualne używają [wirtualnego kubelet][aci-connector-k8s] open source do [naśladowania kubelet][kubelet-doc] Kubernetes, rejestrując się jako węzeł o nieograniczonej pojemności. Wirtualny Kubelet wywołuje tworzenie zasobników jako grup [kontenerów][pod-doc] w wystąpieniach kontenera platformy Azure.

Zobacz projekt [Wirtualny Kubelet,](https://github.com/virtual-kubelet/virtual-kubelet) aby uzyskać dodatkowe przykłady rozszerzania interfejsu API usługi Kubernetes na platformy kontenerów bezserwerowych.

## <a name="next-steps"></a>Następne kroki

Utwórz pierwszy kontener za pomocą wystąpień kontenerów platformy Azure za pomocą [przewodnika szybki start](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
