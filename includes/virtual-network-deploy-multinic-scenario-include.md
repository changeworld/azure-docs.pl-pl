---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: d20ef44fd5c117e4e3a568542bb022c451ac23fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743281"
---
## <a name="scenario"></a>Scenariusz
Ten dokument przeprowadzi wdrożenia, który używa wielu kart sieciowych na maszynach wirtualnych w konkretnego scenariusza. W tym scenariuszu masz obciążenia IaaS dwuwarstwowej hostowanej na platformie Azure. Każda warstwa jest wdrażana we własnej podsieci w sieci wirtualnej (VNet). Warstwy frontonu składa się z kilku serwerów sieci web, zgrupowane razem w module równoważenia obciążenia ustawiony w celu zapewnienia wysokiej dostępności. Warstwa zaplecza składa się z kilku serwerów baz danych. Serwery bazy danych są wdrażane z dwiema kartami sieciowymi, jedną dla dostępu do bazy danych, druga do zarządzania. Scenariusz obejmuje także sieciowych grup zabezpieczeń (NSG) w celu kontrolowania, jaki ruch jest dozwolony w każdej podsieci i karty Sieciowej we wdrożeniu. Na poniższej ilustracji przedstawiono podstawowej architektury tego scenariusza:

![Scenariusz MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)