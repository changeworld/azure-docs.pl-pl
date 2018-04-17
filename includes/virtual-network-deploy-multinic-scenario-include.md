---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 5fc9412713bc57c3fd7753e133b2587ea7a68938
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>Scenariusz
Ten dokument przeprowadzi Cię przez wdrożenia korzystającego z wielu kart sieciowych w maszynach wirtualnych w danego scenariusza. W tym scenariuszu należy obciążenia IaaS dwuwarstwowej hostowana na platformie Azure. Każda warstwa została wdrożona w jego własnej podsieci w sieci wirtualnej (VNet). Warstwy frontonu składa się z kilku serwerów sieci web, zgrupowane w ustawić wysoką dostępność usługi równoważenia obciążenia. Warstwę wewnętrzne składa się z kilku serwerów baz danych. Serwery bazy danych są wdrażane z dwiema kartami sieciowymi, jeden dla dostępu do bazy danych, innych do zarządzania. Scenariusz obejmuje także grup zabezpieczeń sieci (NSG), aby kontrolować, jaki ruch jest dozwolony w każdej podsieci i karty Sieciowej w ramach wdrożenia. Na poniższej ilustracji przedstawiono podstawowej architektury tego scenariusza:  

![Scenariusz MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

