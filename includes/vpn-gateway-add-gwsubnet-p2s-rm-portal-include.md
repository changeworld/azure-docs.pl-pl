---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a8dec00373d991a7aeaf11f1a4d95463ab71d9a2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197082"
---
1. W [portalu](http://portal.azure.com) przejdź do sieci wirtualnej usługi Resource Manager, dla której chcesz utworzyć bramę sieci wirtualnej.
2. W sekcji **Ustawienia** na stronie sieci wirtualnej kliknij pozycję **Podsieci**, aby rozwinąć stronę **Podsieci**.
3. Na stronie **Podsieci** kliknij pozycję **+Podsieć bramy**, aby otworzyć stronę **Dodaj podsieć**. 

  ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Dodawanie podsieci bramy")
4. **Nazwa** dla podsieci zostanie automatycznie wypełniona wartością „GatewaySubnet”. Ta wartość jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy. Dostosuj automatycznie wypełniane wartości ustawienia **Zakres adresów** do wymagań dotyczących konfiguracji, a następnie kliknij przycisk **OK** w dolnej części strony, aby utworzyć podsieć.

  ![Dodawanie podsieci](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Dodawanie podsieci")