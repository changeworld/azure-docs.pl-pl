---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Wybierz rozwiązanie do równoważenia obciążenia na potrzeby automatycznej synchronizacji chmur prywatnych
description: Opisuje opcje równoważenia obciążenia wdrażanie aplikacji w chmurze prywatnej automatycznej synchronizacji
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 405bc9d95b8d82e2181e2fb828d6bcc00c8c4639
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014882"
---
# <a name="choose-a-load-balancing-solution-for-avs-private-clouds"></a>Wybierz rozwiązanie równoważenia obciążenia na potrzeby automatycznej synchronizacji chmur prywatnych

W przypadku wdrażania aplikacji w chmurze prywatnej automatycznej synchronizacji można wybrać dowolną z kilku opcji równoważenia obciążenia.

Możesz wybrać wirtualny lub oparty na oprogramowaniu moduł równoważenia obciążenia w chmurze prywatnej automatycznej synchronizacji, a nawet użyć modułu równoważenia obciążenia (P7) platformy Azure działającego w ramach subskrypcji platformy Azure w celu frontonu maszyn wirtualnych warstwy sieci Web działających w chmurze prywatnej automatycznej synchronizacji. Oto kilka opcji:

## <a name="virtual-load-balancers"></a>Wirtualne moduły równoważenia obciążenia

Wirtualne urządzenia usługi równoważenia obciążenia można wdrożyć w środowisku VMware za pomocą interfejsu vCenter i skonfigurować je do frontonu ruchu aplikacji.

Niektórzy popularni dostawcy to: NginX: http://nginx.org/en/docs/http/load_balancing.html F5-BigIP-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Moduł równoważenia obciążenia (P7) platformy Azure

W przypadku korzystania z usługi Azure Application Gateway jako modułu równoważenia obciążenia P7 dla aplikacji działającej w chmurze prywatnej w wersji załadunkowej nie trzeba zarządzać oprogramowaniem usługi równoważenia obciążenia. Oprogramowanie usługi równoważenia obciążenia jest zarządzane przez platformę Azure. Wszystkie maszyny wirtualne w warstwie sieci Web w chmurze prywatnej automatycznej wersji próbnej używają prywatnych adresów IP i nie wymagają dodatkowych reguł translatora adresów sieciowych ani publicznych adresów IP w celu rozpoznawania nazw. Maszyny wirtualne warstwy sieci Web komunikują się z Application Gateway platformy Azure za pośrednictwem połączenia prywatnego, o małym opóźnieniu i dużej przepustowości.

Aby dowiedzieć się więcej o konfigurowaniu tego rozwiązania, zapoznaj się z przewodnikiem po rozwiązaniu dotyczącym korzystania z usługi Azure Application Gateway jako modułu równoważenia obciążenia P7.

## <a name="azure-internal-load-balancer"></a>Wewnętrzny moduł równoważenia obciążenia platformy Azure

Jeśli zdecydujesz się uruchomić aplikację w ramach wdrożenia hybrydowego, w którym warstwa frontonu internetowego jest uruchomiona w sieci wirtualnej platformy Azure w ramach subskrypcji platformy Azure, a warstwa bazy danych aplikacji jest uruchomiona na maszynach wirtualnych VMware w ramach automatycznej synchronizacji chmury prywatnej, możesz użyć wewnętrznego modułu równoważenia obciążenia platformy Azure (L 4 moduł równoważenia obciążenia przed maszynami wirtualnymi warstwy bazy danych na potrzeby zarządzania ruchem.

Aby dowiedzieć się więcej, zobacz Dokumentacja [wewnętrzna Load Balancer](../load-balancer/concepts-limitations.md#internalloadbalancer) platformy Azure.

## <a name="global-server-load-balancer"></a>Moduł równoważenia obciążenia serwera globalnego

Jeśli szukasz modułu równoważenia obciążenia opartego na systemie DNS, możesz skorzystać z rozwiązań innych firm dostępnych w witrynie Azure Marketplace lub przejść na natywne rozwiązanie platformy Azure.

Azure Traffic Manager to oparty na systemie DNS moduł równoważenia obciążenia, który umożliwia optymalne dystrybuowanie ruchu do usług w globalnych regionach platformy Azure i lokalnych, zapewniając wysoką dostępność i czas odpowiedzi. Aby dowiedzieć się więcej, zobacz dokumentację usługi Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) .
