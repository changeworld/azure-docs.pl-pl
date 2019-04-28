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
ms.openlocfilehash: 19ad4e39ca4e402c37b2cfa69c7c306b6e5a2766
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407795"
---
1. Przejdź do strony dla bramy sieci wirtualnej i otwórz ją. Istnieje wiele metod nawigacji. Aby przejść do bramy „VNet1GW”, wybierz pozycję **TestVNet1 -> Przegląd -> Połączone urządzenia -> VNet1GW**.
2. Na stronie bramy VNet1GW kliknij pozycję **Połączenia**. W górnej części strony Połączenia kliknij pozycję **+Dodaj**, aby otworzyć stronę **Dodawanie połączenia**.

   ![Utwórz połączenie lokacja-lokacja](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. Na stronie **Dodawanie połączenia** skonfiguruj wartości dla swojego połączenia.

   - **Nazwa:** Nazwij połączenie.
   - **Typ połączenia:** Wybierz **lokacja-lokacja(IPSec)**.
   - **Brama sieci wirtualnej:** Wartość jest ustalona, ponieważ połączenie jest nawiązywane z tej bramy.
   - **Brama sieci lokalnej:** Kliknij przycisk **wybierz bramę sieci lokalnej** i wybierz bramę sieci lokalnej, do którego chcesz używać.
   - **Klucz wspólny:** wartość musi być zgodna z wartością używaną dla lokalnego urządzenia sieci VPN. W przykładzie użyto wartości „abc123”, ale można (i należy) użyć bardziej złożonej wartości. Pamiętaj o tym, że wartość podana w tym miejscu musi być taka sama, jak wartość podana podczas konfigurowania urządzenia sieci VPN.
   - Pozostałe wartości w polach **Subskrypcja**, **Grupa zasobów** i **Lokalizacja** są ustalone.

4. Kliknij przycisk **OK**, aby utworzyć połączenie. Na ekranie zostanie wyświetlony napis *Tworzenie połączenia*.
5. Połączenie będzie widoczne na stronie **Połączenia** bramy sieci wirtualnej. Stan zmieni się z *Nieznany* na *Łączenie*, a następnie na *Powiodło się*.
