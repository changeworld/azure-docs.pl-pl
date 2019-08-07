---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52ab0413dffeee59cb9d34c6276a0c806a4d0322
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780137"
---
Podsieć bramy zawiera zastrzeżone adresy IP, z których korzystają usługi bramy sieci wirtualnej. Utwórz podsieć bramy.

1. W portalu przejdź do sieci wirtualnej, dla której chcesz utworzyć bramę sieci wirtualnej.
2. Na stronie Sieć wirtualna kliknij pozycję **podsieci** , aby rozwinąć stronę **VNet1-Subnets** .
3. Kliknij pozycję **+ podsieć bramy** u góry, aby otworzyć stronę **Dodaj podsieć** .

   ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Dodawanie podsieci bramy")
4. **Nazwa** podsieci jest wypełniana automatycznie przy użyciu wymaganej wartości "GatewaySubnet". Dopasuj automatycznie wypełniony **zakres adresów (blok CIDR)** w celu dopasowania do następującej wartości:

   **Zakres adresów (blok CIDR)** : 10.1.255.0/27

   ![Dodawanie podsieci bramy](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet1.png "Dodawanie podsieci bramy")
5. Pozostaw pozostałe ustawienia jako domyślne wartości **Brak** lub **0 zaznaczone**. Następnie kliknij przycisk **OK** , aby utworzyć podsieć bramy.