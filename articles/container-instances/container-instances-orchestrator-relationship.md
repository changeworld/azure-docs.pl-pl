---
title: Azure Container Instances i aranżacja kontenera
description: Dowiedz się, jak usługa Azure Container Instances współdziała z koordynatorami kontenerów.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 044b74e1a8683c6beb0220c1cf9fb97403286a95
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972246"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container Instances i koordynatorów kontenerów

Ze względu na ich mały rozmiar i orientację aplikacji, kontenery są dobrze dopasowane do środowisk dostarczania Agile i architektury opartych na mikrousługach. Zadanie automatyzacji i zarządzania dużą liczbą kontenerów oraz ich współdziałaniem jest znane jako aranżacja. Popularne Koordynatory kontenerów obejmują Kubernetes, DC/OS i Docker Swarm.

Azure Container Instances oferuje podstawowe możliwości planowania platform aranżacji. Chociaż nie obejmują one usług wyższego poziomu, które udostępniają te platformy, Azure Container Instances mogą być uzupełniane do nich. W tym artykule opisano zakres dojścia Azure Container Instances i sposób, w jaki pełne Koordynatory kontenerów mogą z nimi korzystać.

## <a name="traditional-orchestration"></a>Tradycyjna aranżacja

Standardowa definicja aranżacji obejmuje następujące zadania:

- **Planowanie**: Mając obraz kontenera i żądanie zasobu, Znajdź odpowiednią maszynę, na której ma zostać uruchomiony kontener.
- **Koligacja/** koligacja: Określ, że zestaw kontenerów powinien działać w pobliżu siebie (dla wydajności) lub wystarczająco daleko (na potrzeby dostępności).
- **Monitorowanie kondycji**: Obejrzyj błędy kontenera i automatycznie Zaplanuj je ponownie.
- **Tryb failover**: Śledź działania wykonywane na poszczególnych maszynach i ponownie Zaplanuj kontenery z uszkodzonych maszyn do węzłów w dobrej kondycji.
- **Skalowanie**: Dodaj lub usuń wystąpienia kontenerów w celu dopasowania do żądania ręcznie lub automatycznie.
- **Sieć**: Zapewnianie sieci nakładki do koordynowania kontenerów w celu komunikacji między wieloma maszynami hosta.
- **Odnajdowanie usługi**: Włącz automatyczne lokalizowanie kontenerów, nawet w przypadku przechodzenia między maszynami hosta i zmiany adresów IP.
- **Skoordynowane uaktualnienia aplikacji**: Zarządzaj uaktualnieniami kontenerów, aby uniknąć przestojów aplikacji, i Włącz wycofywanie, jeśli coś się nie stało.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Aranżacja z Azure Container Instances: Podejście warstwowe

Azure Container Instances włącza podejście warstwowe do aranżacji, dostarczając wszystkie możliwości planowania i zarządzania wymagane do uruchomienia jednego kontenera, jednocześnie umożliwiając platformom Orchestrator zarządzanie zadaniami wielokontenerowymi na tym komputerze.

Ze względu na to, że podstawowa infrastruktura dla wystąpień kontenerów jest zarządzana przez platformę Azure, platforma Orchestrator nie musi się zachodzić z znalezieniem odpowiedniej maszyny hosta, na której ma zostać uruchomiony pojedynczy kontener. Elastyczność chmury gwarantuje, że jest ona zawsze dostępna. Zamiast tego Koordynator może skupić się na zadaniach, które upraszczają opracowywanie architektur obejmujących wiele kontenerów, w tym skalowanie i skoordynowane uaktualnienia.

## <a name="scenarios"></a>Scenariusze

Chociaż integracja programu Orchestrator z Azure Container Instances jest nadal Nascent, przewidujemy, że zostaną naliczone kilka różnych środowisk:

### <a name="orchestration-of-container-instances-exclusively"></a>Organizowanie wystąpień kontenera wyłącznie

Ponieważ rozpoczynają one szybkie i rozliczanie według drugiego, środowisko oparte wyłącznie na Azure Container Instances oferuje najszybszy sposób na rozpoczęcie pracy i zaradzenie sobie z wysoce zmiennymi obciążeniami.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombinacja wystąpień kontenerów i kontenerów w Virtual Machines

W przypadku długotrwałych, stabilnych obciążeń, organizowanie kontenerów w klastrze dedykowanych maszyn wirtualnych jest zwykle tańsze niż uruchamianie tych samych kontenerów z Azure Container Instances. Jednak wystąpienia kontenerów oferują doskonałe rozwiązanie umożliwiające szybkie rozwinięcie i zagwarantowanie ogólnej pojemności do pracy z nieoczekiwanymi lub krótkotrwałymi wzrostami użycia.

Zamiast skalować liczbę maszyn wirtualnych w klastrze, a następnie wdrożyć dodatkowe kontenery na tych maszynach, program Orchestrator może po prostu zaplanować dodatkowe kontenery w Azure Container Instances i usunąć je, gdy nie są już wymagana.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Przykładowa implementacja: węzły wirtualne usługi Azure Kubernetes Service (AKS)

Aby szybko skalować obciążenia aplikacji w klastrze [usługi Azure Kubernetes Service](../aks/intro-kubernetes.md) (AKS), można użyć *węzłów wirtualnych* tworzonych dynamicznie w Azure Container Instances. Węzły wirtualne umożliwiają komunikację sieciową między jednostkami, które działają w ACI i klastrze AKS. 

Węzły wirtualne obecnie obsługują wystąpienia kontenera systemu Linux. Rozpocznij pracę z węzłami wirtualnymi za pomocą [interfejsu wiersza polecenia platformy Azure](https://go.microsoft.com/fwlink/?linkid=2047538) lub [Azure Portal](https://go.microsoft.com/fwlink/?linkid=2047545).

Węzły wirtualne używają [Kubelet wirtualnej][aci-connector-k8s] typu open source do naśladowania Kubernetes [Kubelet][kubelet-doc] przez zarejestrowanie jako węzeł z nieograniczoną pojemnością. Wirtualna Kubelet wysyła do grupy kontenerów tworzenie w [][pod-doc] Azure Container Instances.

Zobacz [wirtualny projekt Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) , aby uzyskać dodatkowe przykłady rozszerzania interfejsu API Kubernetes na platformy kontenerów bezserwerowych.

## <a name="next-steps"></a>Następne kroki

Utwórz swój pierwszy kontener za pomocą Azure Container Instances przy użyciu [przewodnika Szybki Start](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
