---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eb2555cdc163ee0f88149248b9e7d83a51d34a1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150268"
---
1. W [witryny Azure portal](http://portal.azure.com), wybierz sieć wirtualną usługi Resource Manager, dla której chcesz utworzyć bramę sieci wirtualnej.

2. W **ustawienia** sekcji stronie sieci wirtualnej, wybierz opcję **podsieci** rozszerzania **podsieci** strony.

3. Na **podsieci** wybierz opcję **podsieci bramy** otworzyć **Dodaj podsieć** strony.

   ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Dodawanie podsieci bramy")

4. **Nazwa** dla podsieci zostanie automatycznie autofilled wartością *GatewaySubnet*. Ta wartość jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy. Dostosuj autofilled **zakres adresów** wartości są zgodne z wymaganiami konfiguracji, następnie wybierz pozycję **OK** utworzyć podsieć.

   ![Dodawanie podsieci](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Dodawanie podsieci")