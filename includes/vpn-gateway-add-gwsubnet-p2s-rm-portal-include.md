---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b044912fd8f52f3f4fdbe1b3a74b64f9b565ddf0
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673463"
---
1. W [portalu](https://portal.azure.com) przejdź do sieci wirtualnej usługi Resource Manager, dla której chcesz utworzyć bramę sieci wirtualnej.
2. W sekcji **Ustawienia** na stronie sieci wirtualnej kliknij pozycję **Podsieci**, aby rozwinąć stronę **Podsieci**.
3. Na stronie **Podsieci** kliknij pozycję **+Podsieć bramy**, aby otworzyć stronę **Dodaj podsieć**. 

   ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Dodawanie podsieci bramy")
4. **Nazwa** dla podsieci zostanie automatycznie wypełniona wartością „GatewaySubnet”. Ta wartość jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy. Dostosuj automatycznie wypełnione wartości w polu **Zakres adresów** do wymagań konfiguracji. Nie należy konfigurować tabeli tras lub punkty końcowe usługi.

   ![Dodawanie podsieci](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Dodawanie podsieci")
5. Kliknij przycisk **OK** w dolnej części strony Aby utworzyć podsieć.
