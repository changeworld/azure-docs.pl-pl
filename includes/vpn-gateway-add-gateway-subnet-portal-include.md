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
ms.openlocfilehash: a4101f3bfe83859eea525370b5eebcaa6e193a2d
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44169135"
---
1. W portalu przejdź do sieci wirtualnej, dla której chcesz utworzyć bramę sieci wirtualnej.
2. W sekcji **Ustawienia** na stronie sieci wirtualnej kliknij pozycję **Podsieci**, aby rozwinąć stronę Podsieci.
3. Na stronie **Podsieci** kliknij pozycję **+Podsieć bramy** u góry, aby otworzyć stronę **Dodaj podsieć**.

  ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Dodawanie podsieci bramy")
  
4. **Nazwa** dla podsieci zostanie automatycznie wypełniona wartością „GatewaySubnet”. Wartość elementu GatewaySubnet jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy. Dostosuj automatycznie wypełnione wartości w polu **Zakres adresów** do wymagań konfiguracji.

  ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Dodawanie podsieci bramy")
  
5. Aby utworzyć podsieć, kliknij przycisk **OK** u dołu strony.
