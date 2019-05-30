---
title: Wprowadzenie do platformy Azure, Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Dowiedz się, funkcje i korzyści z programu Microsoft Azure Red Hat OpenShift i wdrażać aplikacje oraz zarządzać nimi opartych na kontenerach.
services: container-service
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 7cabedaec1190437aa9f225397afa8871cb06e88
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306375"
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

Do istniejącej sieci wirtualnej za pomocą komunikacji równorzędnej, możesz połączyć z klastra usługi Azure Red Hat OpenShift. W tej konfiguracji zasobników można łączyć z innymi usługami w równorzędnej sieci wirtualnej i sieciami lokalnymi za pośrednictwem [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) lub lokacja lokacja (S2S) połączenia sieci VPN.

Zobacz [łączenie sieci wirtualnej klastra z istniejącej sieci wirtualnej](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) Aby uzyskać szczegółowe informacje.

## <a name="kubernetes-certification"></a>Certyfikat platformy Kubernetes

Usługa Azure Red Hat OpenShift została CNCF uzyskała certyfikat zgodności platformy Kubernetes.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, wymagania wstępne dotyczące usługi Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Konfigurowanie środowiska deweloperskiego](howto-setup-environment.md)