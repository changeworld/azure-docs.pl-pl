---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cab04a7eafbc21e0d26cd5a287f3dbee8d3d22b7
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "31805108"
---
## <a name="scenario"></a>Scenariusz
Ten dokument przeprowadzi Cię przez wdrożenia korzystającego z wielu kart sieciowych w maszynach wirtualnych w danego scenariusza. W tym scenariuszu należy obciążenia IaaS dwuwarstwowej hostowana na platformie Azure. Każda warstwa została wdrożona w jego własnej podsieci w sieci wirtualnej (VNet). Warstwy frontonu składa się z kilku serwerów sieci web, zgrupowane w ustawić wysoką dostępność usługi równoważenia obciążenia. Warstwę wewnętrzne składa się z kilku serwerów baz danych. Serwery bazy danych są wdrażane z dwiema kartami sieciowymi, jeden dla dostępu do bazy danych, innych do zarządzania. Scenariusz obejmuje także grup zabezpieczeń sieci (NSG), aby kontrolować, jaki ruch jest dozwolony w każdej podsieci i karty Sieciowej w ramach wdrożenia. Na poniższej ilustracji przedstawiono podstawowej architektury tego scenariusza:

![Scenariusz MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

