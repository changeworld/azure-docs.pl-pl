---
title: Wprowadzenie do usługi Azure Red Hat OpenShift
description: Poznaj funkcje i zalety programu Microsoft Azure Red Hat OpenShift do wdrażania aplikacji opartych na kontenerach i zarządzania nimi.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 4be388eec3851154fd0f6af37d03a468fc52197b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76276070"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Usługa Microsoft *Azure Red Hat OpenShift* umożliwia wdrażanie w pełni zarządzanych klastrów [OpenShift.](https://www.openshift.com/)

Usługa Azure Red Hat OpenShift rozszerza [usługę Kubernetes](https://kubernetes.io/). Uruchamianie kontenerów w wersji produkcyjnej za pomocą narzędzia Kubernetes wymaga dodatkowych narzędzi i zasobów, takich jak rejestr obrazów, zarządzanie magazynem, rozwiązania sieciowe oraz narzędzia do rejestrowania i monitorowania, które muszą być wersjona i przetestowane razem. Tworzenie aplikacji opartych na kontenerach wymaga jeszcze większej integracji z oprogramowaniem pośredniczącym, strukturami, bazami danych i narzędziami ciągłej integracji/ciągłego wdrażania. Usługa Azure Red Hat OpenShift łączy to wszystko w jedną platformę, ułatwiając operacje zespołom IT, jednocześnie dając zespołom aplikacji to, czego potrzebują do wykonania.

Usługa Azure Red Hat OpenShift jest wspólnie zaprojektowana, obsługiwana i obsługiwana przez firmę Red Hat i microsoft, aby zapewnić zintegrowaną pomoc techniczną. Nie ma żadnych maszyn wirtualnych do pracy i nie jest wymagane łatanie. Węzły główne, infrastruktury i aplikacji są załatane, aktualizowane i monitorowane w Twoim imieniu przez firmę Red Hat i Microsoft. Klastry usługi Azure Red Hat OpenShift są wdrażane w ramach subskrypcji platformy Azure i są uwzględniane na rachunku platformy Azure.

Można wybrać własne rozwiązania rejestru, sieci, magazynu i ciągłej integracji/ciągłego wdrażania lub użyć wbudowanych rozwiązań do automatycznego zarządzania kodem źródłowym, kompilacji kontenerów i aplikacji, wdrożeń, skalowania, zarządzania kondycją i innych. Usługa Azure Red Hat OpenShift zapewnia zintegrowane środowisko logowania za pośrednictwem usługi Azure Active Directory.

Aby rozpocząć, wykonaj samouczek [tworzenia klastra Azure Red Hat OpenShift.](tutorial-create-cluster.md)

## <a name="access-security-and-monitoring"></a>Dostęp, zabezpieczenia i monitorowanie

Aby zwiększyć bezpieczeństwo i zarządzanie, usługa Azure Red Hat OpenShift umożliwia integrację z usługą Azure Active Directory (Azure AD) i korzystanie z kontroli dostępu opartej na rolach kubernetes (RBAC). Możesz także monitorować kondycję klastra i zasobów.

## <a name="cluster-and-node"></a>Klaster i węzeł

Węzły OpenShift usługi Azure Red Hat są uruchamiane na maszynach wirtualnych platformy Azure. Możesz połączyć magazyn z węzłami i zasobnikami, uaktualnić składniki klastra oraz używać procesorów GPU.

## <a name="virtual-networks-and-ingress"></a>Sieci wirtualne i ruch przychodzący

Klaster OpenShift usługi Azure Red Hat można połączyć z [istniejącą siecią wirtualną](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) za pośrednictwem komunikacji równorzędnej. W tej konfiguracji zasobników można połączyć się z innymi usługami w sieci wirtualnej równorzędnej.

Aby uzyskać szczegółowe informacje, zobacz [Łączenie sieci wirtualnej klastra z istniejącą siecią wirtualną.](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network)

## <a name="kubernetes-certification"></a>Certyfikat platformy Kubernetes

Usługa Azure Red Hat OpenShift została certyfikowana przez CNCF jako firma Kubernetes conformant.

## <a name="next-steps"></a>Następne kroki

Poznaj wymagania wstępne dotyczące usługi Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Konfigurowanie środowiska deweloperskiego](howto-setup-environment.md)
