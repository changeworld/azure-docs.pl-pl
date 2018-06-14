---
title: Sieć wirtualna platformy Azure dla kontenerów | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wtyczki dla klastrów Kubernetes CNI, dzięki czemu kontenerów do komunikowania się ze sobą i innych zasobów w sieci wirtualnej.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2017
ms.locfileid: "26766402"
---
# <a name="container-networking"></a>Kontener sieci

Platforma Azure udostępnia [wtyczki kontenera sieci interfejsu (CNI)](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md) umożliwiająca wdrażanie i zarządzanie nimi własnego klastra Kubernetes z natywna możliwość sieci platformy Azure. Wtyczka jest włączona, domyślnie podczas wdrażania Kubernetes klastry [aparat usługi kontenera platformy Azure](https://github.com/Azure/acs-engine) (lub aparat ACS).

## <a name="networking-capabilities"></a>Funkcje sieci

Kontenery mogą wykorzystywać bogaty zestaw funkcji, które oferuje sieci wirtualnej, takie jak:
-   Tworzenie oddzielnych sieci wirtualnej dla klastra lub wdrożyć klaster w istniejącej sieci wirtualnej. 
-   Każdy pod w klastrze odbiera adresu IP w sieci wirtualnej i może komunikować się bezpośrednio z innych stanowiskami w klastrze i żadnej maszyny wirtualnej w sieci wirtualnej. 
-   Pod mogą łączyć się dla innych maszyn wirtualnych w połączyć za pomocą sieci wirtualnych i stanowiskami i sieciami lokalnymi przez ExpressRoute, jak i połączeń sieci VPN lokacja lokacja. Zasobów lokalnych można przekazać do stanowiskami. 
-   Usługa Kubernetes z Internetem za pośrednictwem usługi równoważenia obciążenia Azure mogą uwidaczniać.  
-   Stanowiskami w podsieci, które ma włączone punkty końcowe usługi można bezpiecznie łączyć się z usługami platformy Azure (magazynu i bazy danych SQL, na przykład).
-   Trasy zdefiniowane przez użytkownika umożliwia przekierowujący ruch z stanowiskami do urządzenie sieci wirtualnej. 
-   Stanowiskami dostęp do publicznego zasobów w Internecie.
-   Można przypisać pod publiczny adres IP, które mogą być skojarzone z nazwą DNS.
 
## <a name="limits"></a>Limity
Maksymalnie 4000 węzłów w klastrze Kubernetes i do 250 stanowiskami na węzeł z ogólnym limicie 16 000 stanowiskami dla klastra, można wdrożyć przy użyciu wtyczki.

## <a name="constraints"></a>Ograniczenia
- Wtyczka nie jest włączone w przypadku wdrażania klastra Kubernetes z [usługi kontenera platformy Azure (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) klaster z Kubernetes.
- Brak obsługi natywnych dla zasad sieci Kubernetes, w tym zasad DNS lub dostępu.
- Wtyczka nie obsługuje zasad sieciowych na pod.

## <a name="pricing"></a>Cennik
Brak bezpłatnie za pomocą wtyczki CNI.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrożyć klaster Kubernetes](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md) w Twojej [własnych sieci wirtualnej](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni).
