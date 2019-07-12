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
ms.openlocfilehash: 42e983ead6f7562c6a31cf9ef4ad2d97d0ff9707
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673458"
---
1. W [witryny Azure portal](https://portal.azure.com), wybierz sieć wirtualną usługi Resource Manager, dla której chcesz utworzyć bramę sieci wirtualnej.

2. W **ustawienia** sekcji stronie sieci wirtualnej, wybierz opcję **podsieci** rozszerzania **podsieci** strony.

3. Na **podsieci** wybierz opcję **podsieci bramy** otworzyć **Dodaj podsieć** strony.

   ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Dodawanie podsieci bramy")

4. **Nazwa** dla podsieci zostanie automatycznie autofilled wartością *GatewaySubnet*. Ta wartość jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy. Dostosuj autofilled **zakres adresów** wartości są zgodne z wymaganiami konfiguracji, następnie wybierz pozycję **OK** utworzyć podsieć.

   ![Dodawanie podsieci](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Dodawanie podsieci")
