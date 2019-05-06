---
title: Wprowadzenie do platformy Azure, Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Dowiedz się, funkcje i korzyści z programu Microsoft Azure Red Hat OpenShift i wdrażać aplikacje oraz zarządzać nimi opartych na kontenerach.
services: container-service
author: tylermsft
ms.author: twhitney
ms.service: container-service
manager: jeconnoc
ms.topic: overview
ms.date: 05/06/2019
ms.custom: mvc
ms.openlocfilehash: 6121c0f654a61a147e84f0697f3ddb06b7c5db92
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65081045"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft *Azure Red Hat OpenShift* service pozwala na wdrażanie w pełni zarządzana [OpenShift](https://www.openshift.com/) klastrów.

Azure Red Hat OpenShift rozszerza [Kubernetes](https://kubernetes.io/). Uruchamianie kontenerów w środowisku produkcyjnym przy użyciu rozwiązania Kubernetes wymaga dodatkowych narzędzi i zasobów, takich jak rejestr obrazów, zarządzanie magazynem, rozwiązań sieci i rejestrowania i narzędzia do monitorowania, które muszą znajdować się wersjonowana i przetestowane. Tworzenie aplikacji opartych na kontenerach wymaga nawet więcej pracy integracji za pomocą oprogramowania pośredniczącego, struktur, baz danych i narzędzi ciągłej integracji/ciągłego wdrażania. Azure Red Hat OpenShift łączy wszystkie zespoły na pojedynczej platformy, łączy łatwość operacje, aby zespoły IT, zapewniając aplikacji, to, czego potrzebują do wykonania.

Azure Red Hat OpenShift wspólnie odtwarzane, obsługiwane i obsługiwanych przez firmy Red Hat i Microsoft, aby zapewnić zintegrowaną pomoc techniczną. Brak maszyn wirtualnych do działania i poprawek nie jest wymagana. Węzły główne, infrastruktury i aplikacji są poprawek, aktualizacji i monitorowane w imieniu użytkownika przez firmy Red Hat i Microsoft. Klastry usługi Azure Red Hat OpenShift są wdrażane w ramach subskrypcji platformy Azure i są uwzględniane na rachunku dotyczącym platformy Azure.

Możesz wybrać własnego rejestru, sieci, magazynu i rozwiązań do ciągłej integracji/ciągłego wdrażania, lub użyj kompilacji z wbudowanymi rozwiązaniami do zarządzania kodem źródłowym automatycznych, kontenerów i aplikacji, wdrożeń, skalowanie, zarządzanie stanem zdrowia i więcej. Azure Red Hat OpenShift udostępnia zintegrowane środowisko logowania jednokrotnego za pośrednictwem usługi Azure Active Directory.

Aby rozpocząć pracę, należy wykonać [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) samouczka.

## <a name="access-security-and-monitoring"></a>Dostęp, zabezpieczenia i monitorowanie

Lepsze zabezpieczenia i zarządzanie Azure Red Hat OpenShift umożliwia integrację z usługą Azure Active Directory (Azure AD) i korzystanie z kontroli dostępu opartej na rolach Kubernetes (RBAC). Możesz także monitorować kondycję klastra i zasobów.

## <a name="cluster-and-node"></a>Klaster i węzeł

Węzły Red Hat OpenShift na platformie Azure uruchomić na maszynach wirtualnych platformy Azure. Możesz połączyć magazyn z węzłami i zasobnikami, uaktualnić składniki klastra oraz używać procesorów GPU.

## <a name="virtual-networks-and-ingress"></a>Sieci wirtualne i ruch przychodzący

Można wdrożyć klaster usługi Azure Red Hat OpenShift w istniejącej sieci wirtualnej. W tej konfiguracji co zasobnik w klastrze ma przypisany adres IP w sieci wirtualnej i może komunikować się bezpośrednio z innymi zasobników w klastrze i innych węzłów w sieci wirtualnej. Zasobników można również łączyć z innymi usługami w równorzędnej sieci wirtualnej i sieciami lokalnymi za pośrednictwem [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) lub lokacja lokacja (S2S) połączenia sieci VPN.

Aby uzyskać więcej informacji, zobacz [Tworzenie klastra Microsoft Red Hat OpenShift na platformie Azure](tutorial-create-cluster.md).

## <a name="kubernetes-certification"></a>Certyfikat platformy Kubernetes

Usługa Azure Red Hat OpenShift została CNCF uzyskała certyfikat zgodności platformy Kubernetes.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, wymagania wstępne dotyczące usługi Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Konfigurowanie środowiska deweloperskiego](howto-setup-environment.md)