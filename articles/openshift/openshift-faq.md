---
title: Często zadawane pytania dotyczące usługi Azure Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Poniżej przedstawiono odpowiedzi na często zadawane pytania dotyczące programu Microsoft Azure Red Hat OpenShift
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 77e0e11582808901b10877d0d9284637145aa6f2
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078673"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift — często zadawane pytania

W tym artykule opisano często zadawanych pytań (FAQ) dotyczących programu Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Które regiony platformy Azure są obsługiwane?

Zobacz [obsługiwanych zasobów](supported-resources.md#azure-regions) listę regionów globalnych, w których jest obsługiwana Azure Red Hat OpenShift.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Czy można wdrożyć klaster w istniejącej sieci wirtualnej?

Tak. Podczas tworzenia klastra, można wdrożyć klaster usługi Azure Red Hat OpenShift w istniejącej sieci wirtualnej. Zobacz [łączenie sieci wirtualnej klastra z istniejącej sieci wirtualnej ](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) Aby uzyskać szczegółowe informacje.

## <a name="what-cluster-operations-are-available"></a>Jakie operacje klastra są dostępne?

Możesz tylko skalować w górę lub w dół liczbę węzłów obliczeniowych. Nie inne modyfikacje mogą być `Microsoft.ContainerService/openShiftManagedClusters` zasobów po utworzeniu. Maksymalna liczba węzłów obliczeniowych jest ograniczona do 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Jakie rozmiary maszyn wirtualnych można użyć?

Zobacz [rozmiarów maszyn wirtualnych platformy Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) listę rozmiarów maszyn wirtualnych, które można używać z klastrem Azure Red Hat OpenShift.

## <a name="is-data-on-my-cluster-encrypted"></a>Czy dane na mój klaster szyfrowane?

Domyślnie jest szyfrowanie danych magazynowanych. Platforma Azure Storage automatycznie szyfruje dane przed utrwaleniem i odszyfrowywane są dane przed pobierania. Zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) Aby uzyskać szczegółowe informacje.

## <a name="can-i-use-prometheusgrafana-to-monitor-containers-and-manage-capacity"></a>Do monitorowania kontenerów i zarządzanie wydajnością można użyć Prometheus/Grafana?

Nie, nie bieżącej godziny.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Rejestr platformy Docker jest dostępna, zewnętrznie, dzięki czemu można używać narzędzi, takich jak Jenkins?

Rejestr platformy Docker jest dostępne z `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` jednak gwarancja trwałości silne magazynu jest niedostępna. Można również użyć [usługi Azure Container Registry](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Przestrzeń nazw dla wielu sieci jest obsługiwana?

Klienta i Administratorzy pojedynczego projektu, można dostosować przestrzeni nazw dla wielu sieci (w tym odmawia go) na podstawę projektu przy użyciu `NetworkPolicy` obiektów.

## <a name="can-an-admin-manage-users-and-quotas"></a>Można Administrator zarządzania użytkownikami i przydziały?

Tak. Administrator usługi Azure Red Hat OpenShift mogą zarządzać użytkownikami i przydziały oprócz uzyskiwania dostępu do wszystkich użytkowników utworzone projektów.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Czy można ograniczyć klaster w niektórych użytkowników usługi Azure AD?

Tak. Można ograniczyć platformy Azure AD użytkownicy mogą się logować do klastra, konfigurując aplikacji usługi Azure AD. Aby uzyskać więcej informacji, zobacz [jak: Ograniczenia aplikacji do zbioru użytkowników](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Klaster może mieć węzłów obliczeniowych w wielu regionach platformy Azure?

Nie. Wszystkie węzły w klastrze Azure Red Hat OpenShift muszą pochodzić z tego samego regionu systemu Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Węzły główne i infrastruktury wyodrębnione natychmiast są one za pomocą usługi Azure Kubernetes Service (AKS)?

Nie. Uruchom wszystkie zasoby, w tym wzorcu klastra w ramach subskrypcji klienta. Te typy zasobów są umieszczane w grupie zasobów tylko do odczytu.
