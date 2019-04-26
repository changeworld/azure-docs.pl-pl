---
title: Wskaż firmowej domeny internetowej nazwy domeny usługi Azure Traffic Manager
description: Ten artykuł pomoże Ci skonfigurować nazwę domeny firmowej w taki sposób, aby wskazywała nazwę domeny usługi Traffic Manager.
services: traffic-manager
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 77a5fbab6ecda910750ab2b8bae987e77607223a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60329704"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Ustawianie firmowej domeny internetowej tak, aby wskazywała domenę usługi Azure Traffic Manager

Podczas tworzenia profilu usługi Traffic Manager platforma Azure automatycznie przypisuje nazwę DNS dla danego profilu. Aby użyć nazwy z bieżącej strefy DNS, należy utworzyć rekord DNS CNAME, który będzie mapowany na nazwę domeny profilu usługi Traffic Manager. Nazwa domeny usługi Traffic Manager jest wyświetlana w sekcji **Ogólne** na stronie konfiguracji profilu usługi Traffic Manager.

Na przykład aby nazwa domeny `www.contoso.com` wskazywała nazwę DNS `contoso.trafficmanager.net` usługi Traffic Manager, należy utworzyć następujący rekord zasobu DNS:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Wszystkie żądania ruchu do *www\.contoso.com* będą kierowane do *contoso.trafficmanager.net*.

> [!IMPORTANT]
> Domena drugiego poziomu, na przykład *contoso.com*, nie może wskazywać domeny usługi Traffic Manager. Standardy protokołu DNS nie zezwalają na ustanawianie rekordów CNAME dla nazw domen drugiego poziomu.

## <a name="next-steps"></a>Kolejne kroki

* [Metody routingu w usłudze Traffic Manager](traffic-manager-routing-methods.md)
* [Traffic Manager — wyłączanie, włączanie lub usuwanie profilu](disable-enable-or-delete-a-profile.md)
* [Traffic Manager — wyłączanie lub włączanie punktu końcowego](disable-or-enable-an-endpoint.md)
