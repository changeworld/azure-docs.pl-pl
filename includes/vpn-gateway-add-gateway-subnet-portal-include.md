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
ms.openlocfilehash: 9132cf438cab518e20e6c2ddfdb7d0928753bd19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170168"
---
1. W portalu przejdź do sieci wirtualnej, dla której chcesz utworzyć bramę sieci wirtualnej.
2. W sekcji **Ustawienia** na stronie sieci wirtualnej kliknij pozycję **Podsieci**, aby rozwinąć stronę Podsieci.
3. Na stronie **Podsieci** kliknij pozycję **+Podsieć bramy** u góry, aby otworzyć stronę **Dodaj podsieć**.

   ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Dodawanie podsieci bramy")
  
4. **Nazwa** dla podsieci zostanie automatycznie wypełniona wartością „GatewaySubnet”. Wartość elementu GatewaySubnet jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy. Dostosuj automatycznie wypełnione wartości w polu **Zakres adresów** do wymagań konfiguracji.

   ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Dodawanie podsieci bramy")
  
5. Aby utworzyć podsieć, kliknij przycisk **OK** u dołu strony.
