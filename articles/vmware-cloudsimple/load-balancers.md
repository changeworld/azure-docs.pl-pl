---
title: Rozwiązanie Azure VMware firmy CloudSimple — wybierz rozwiązanie do równoważenia obciążenia dla chmur prywatnych CloudSimple
description: Zawiera opis opcji równoważenia obciążenia wdrażających aplikację w chmurze prywatnej
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6c98b699b1d3aba15ce69c519d35d7ce3e90d123
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014882"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Wybierz rozwiązanie do równoważenia obciążenia dla chmur prywatnych CloudSimple

Podczas wdrażania aplikacji w cloudsimple private cloud, można wybrać jedną z kilku opcji równoważenia obciążenia.

Możesz wybrać wirtualny lub programowy moduł równoważenia obciążenia w chmurze prywatnej CloudSimple lub nawet użyć modułu równoważenia obciążenia usługi Azure L7 uruchomionego w ramach subskrypcji platformy Azure, aby zakończyć swoje maszyny wirtualne warstwy sieci Web uruchomione w chmurze prywatnej CloudSimple. Tutaj wymieniamy kilka opcji:

## <a name="virtual-load-balancers"></a>Wirtualne moduły równoważenia obciążenia

Urządzenia wirtualnego modułu równoważenia obciążenia można wdrożyć w środowisku VMware za pośrednictwem interfejsu vCenter i skonfigurować je do frontowego ruchu aplikacji.

Niektórzy popularni dostawcy to: http://nginx.org/en/docs/http/load_balancing.html NginX: F5- https://www.f5.com/products/big-ip-services/virtual-editions BigIP - Traffic Manager: Citrix ADC:https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Moduł równoważenia obciążenia usługi Azure L7

Korzystając z usługi Azure Application Gateway jako modułu równoważenia obciążenia L7 dla aplikacji uruchomionej w chmurze prywatnej, nie trzeba zarządzać oprogramowaniem modułu równoważenia obciążenia. Oprogramowanie modułu równoważenia obciążenia jest zarządzane przez platformę Azure. Wszystkie maszyny wirtualne warstwy sieci web w chmurze prywatnej używają prywatnych adresów IP i nie wymagają dodatkowych reguł translatora adresów SIECIowych ani publicznych adresów IP w celu rozpoznawania nazw. Maszyny wirtualne warstwy sieci Web komunikują się z bramą aplikacji platformy Azure za pośrednictwem prywatnego połączenia o niskiej przepustowości i dużej przepustowości.

Aby dowiedzieć się więcej na temat konfigurowania tego rozwiązania, zapoznaj się z przewodnikiem po rozwiązaniu przy użyciu bramy aplikacji platformy Azure jako modułu równoważenia obciążenia L7.

## <a name="azure-internal-load-balancer"></a>Wewnętrzny moduł równoważenia obciążenia platformy Azure

Jeśli zdecydujesz się uruchomić aplikację we wdrożeniu hybrydowym, w którym warstwa frontonu sieci Web jest uruchomiona w sieci Web vNet w ramach subskrypcji platformy Azure, a warstwa DB aplikacji jest uruchomiona w maszynach wirtualnych VMware w chmurze Private Cloud Cloud, możesz użyć wewnętrznego obciążenia platformy Azure balanser (moduł równoważenia obciążenia L4) przed maszynami wirtualnymi warstwy DB do zarządzania ruchem.

Aby dowiedzieć się więcej, zobacz dokumentację [modułu wewnętrznego równoważenia obciążenia](../load-balancer/concepts-limitations.md#internalloadbalancer) platformy Azure.

## <a name="global-server-load-balancer"></a>Globalny moduł równoważenia obciążenia serwera

Jeśli szukasz modułu równoważenia obciążenia opartego na systemie DNS, możesz użyć rozwiązań innych firm dostępnych w portalu Azure Marketplace lub przejść do natywnego rozwiązania platformy Azure.

Usługa Azure Traffic Manager to moduł równoważenia obciążenia ruchu opartego na systemie DNS, który umożliwia optymalną dystrybucję ruchu do usług w globalnych regionach platformy Azure i lokalnie, zapewniając jednocześnie wysoką dostępność i szybkość reakcji. Aby dowiedzieć się więcej, zobacz dokumentację usługi Azure [Traffic Manager.](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)
