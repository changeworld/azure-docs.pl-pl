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
ms.openlocfilehash: d20ef44fd5c117e4e3a568542bb022c451ac23fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170822"
---
## <a name="scenario"></a>Scenariusz
Ten dokument przeprowadzi wdrożenia, który używa wielu kart sieciowych na maszynach wirtualnych w konkretnego scenariusza. W tym scenariuszu masz obciążenia IaaS dwuwarstwowej hostowanej na platformie Azure. Każda warstwa jest wdrażana we własnej podsieci w sieci wirtualnej (VNet). Warstwy frontonu składa się z kilku serwerów sieci web, zgrupowane razem w module równoważenia obciążenia ustawiony w celu zapewnienia wysokiej dostępności. Warstwa zaplecza składa się z kilku serwerów baz danych. Serwery bazy danych są wdrażane z dwiema kartami sieciowymi, jedną dla dostępu do bazy danych, druga do zarządzania. Scenariusz obejmuje także sieciowych grup zabezpieczeń (NSG) w celu kontrolowania, jaki ruch jest dozwolony w każdej podsieci i karty Sieciowej we wdrożeniu. Na poniższej ilustracji przedstawiono podstawowej architektury tego scenariusza:

![Scenariusz MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

