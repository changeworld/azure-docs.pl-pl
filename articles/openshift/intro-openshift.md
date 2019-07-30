---
title: Wprowadzenie do usługi Azure Red Hat OpenShift | Microsoft Docs
description: Poznaj funkcje i zalety Microsoft Azure Red Hat OpenShift do wdrażania aplikacji opartych na kontenerach i zarządzania nimi.
services: container-service
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: f76f5d4dc84d1f8827248ab8399c1ae450a643a0
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620158"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Usługa *Red Hat OpenShift* systemu Microsoft Azure umożliwia wdrażanie w pełni zarządzanych klastrów [OpenShift](https://www.openshift.com/) .

Azure Red Hat OpenShift rozszerza [Kubernetes](https://kubernetes.io/). Uruchomione kontenery w środowisku produkcyjnym z programem Kubernetes wymagają dodatkowych narzędzi i zasobów, takich jak rejestr obrazów, zarządzanie magazynem, rozwiązania sieciowe i narzędzia do rejestrowania i monitorowania, które muszą być poddane kontroli wersji i ze sobą. Tworzenie aplikacji opartych na kontenerach wymaga jeszcze większej integracji z programem pośredniczącym, strukturami, bazami danych i narzędziami do tworzenia i ciągłego tworzenia dysków. W systemie Azure Red Hat OpenShift wszystkie te elementy są połączone z jedną platformą, dzięki czemu można ułatwić zespołom IT wykonywanie prostych operacji, jednocześnie dostarczając zespoły aplikacji, których potrzebują.

W systemie Red Hat OpenShift jest wspólnie projektowana, obsługiwana i wspierana przez rozwiązania Red Hat i firmę Microsoft w celu zapewnienia zintegrowanego środowiska pomocy technicznej. Brak maszyn wirtualnych do działania i nie jest wymagane stosowanie poprawek. Węzły główne, infrastruktury i aplikacji są poprawione, aktualizowane i monitorowane w imieniu firmy Red Hat i Microsoft. Twoje klastry usługi Azure Red Hat OpenShift są wdrażane w ramach subskrypcji platformy Azure i są uwzględnione na rachunku na korzystanie z platformy Azure.

Możesz wybrać własne rozwiązania dotyczące rejestru, sieci, magazynu i ciągłej integracji/ciągłego dostarczania lub użyć wbudowanych rozwiązań do automatycznego zarządzania kodem źródłowym, kompilacji kontenerów i aplikacji, wdrożeń, skalowania, zarządzania kondycją i innych. Usługa Azure Red Hat OpenShift zapewnia zintegrowane środowisko logowania za pomocą Azure Active Directory.

Aby rozpocząć pracę, Wypełnij samouczek [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="access-security-and-monitoring"></a>Dostęp, zabezpieczenia i monitorowanie

W celu zwiększenia bezpieczeństwa i zarządzania usługa Azure Red Hat OpenShift umożliwia integrację z usługą Azure Active Directory (Azure AD) i korzystanie z kontroli dostępu opartej na rolach (RBAC) Kubernetes. Możesz także monitorować kondycję klastra i zasobów.

## <a name="cluster-and-node"></a>Klaster i węzeł

Węzły usługi Azure Red Hat OpenShift są uruchamiane na maszynach wirtualnych platformy Azure. Możesz połączyć magazyn z węzłami i zasobnikami, uaktualnić składniki klastra oraz używać procesorów GPU.

## <a name="virtual-networks-and-ingress"></a>Sieci wirtualne i ruch przychodzący

[Klaster Red Hat OpenShift platformy Azure można połączyć z istniejącą siecią wirtualną](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) za pośrednictwem komunikacji równorzędnej. W tej konfiguracji, w przypadku połączeń z innymi usługami w równorzędnej sieci wirtualnej.

Aby uzyskać szczegółowe informacje [, zobacz Łączenie sieci wirtualnej klastra z istniejącą siecią wirtualną](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) .

## <a name="kubernetes-certification"></a>Certyfikat platformy Kubernetes

Usługa Azure Red Hat OpenShift została CNCF certyfikowaną jako Kubernetes.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z wymaganiami wstępnymi dotyczącymi usługi Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Konfigurowanie środowiska deweloperskiego](howto-setup-environment.md)
