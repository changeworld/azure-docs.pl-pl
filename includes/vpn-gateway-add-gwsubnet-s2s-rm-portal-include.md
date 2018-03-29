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
ms.openlocfilehash: 3e02242822283ec8597a7be217228cb95e748761
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
1. W portalu przejdź do sieci wirtualnej, dla której chcesz utworzyć bramę sieci wirtualnej.
2. W sekcji **Ustawienia** na stronie sieci wirtualnej kliknij pozycję **Podsieci**, aby rozwinąć stronę Podsieci.
3. Na stronie **Podsieci** kliknij pozycję **+Podsieć bramy** u góry, aby otworzyć stronę **Dodaj podsieć**.

  ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addgwsub.png "Dodawanie podsieci bramy")
4. **Nazwa** dla podsieci zostanie automatycznie wypełniona wartością „GatewaySubnet”. Wartość elementu GatewaySubnet jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy. Dostosuj automatycznie wypełnione wartości w polu **Zakres adresów** do wymagań konfiguracji.

  ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addsub2.png "Dodawanie podsieci bramy")
5. Aby utworzyć podsieć, kliknij przycisk **OK** u dołu strony.