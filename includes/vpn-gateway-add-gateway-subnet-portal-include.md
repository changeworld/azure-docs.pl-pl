---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 158400645423d2e9fe92768786b570e917907d98
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
1. W portalu przejdź do sieci wirtualnej, dla której chcesz utworzyć bramę sieci wirtualnej.
2. W sekcji **Ustawienia** na stronie sieci wirtualnej kliknij pozycję **Podsieci**, aby rozwinąć stronę Podsieci.
3. Na stronie **Podsieci** kliknij pozycję **+Podsieć bramy** u góry, aby otworzyć stronę **Dodaj podsieć**.

  ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Dodawanie podsieci bramy")
4. **Nazwa** dla podsieci zostanie automatycznie wypełniona wartością „GatewaySubnet”. Wartość elementu GatewaySubnet jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy. Dostosuj automatycznie wypełnione wartości w polu **Zakres adresów** do wymagań konfiguracji.

  ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Dodawanie podsieci bramy")
5. Aby utworzyć podsieć, kliknij przycisk **OK** u dołu strony.