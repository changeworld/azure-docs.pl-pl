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
ms.openlocfilehash: ea616786d69d41435be2a46e90d4973b21270935
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
1. Przejdź do strony dla bramy sieci wirtualnej i otwórz ją. Istnieje wiele metod nawigacji. W tym przykładzie nastąpiło przejście do bramy „VNet1GW” po wybraniu pozycji **TestVNet1 -> Przegląd -> Połączone urządzenia -> VNet1GW**.
2. Na stronie bramy VNet1GW kliknij pozycję **Połączenia**. W górnej części strony Połączenia kliknij pozycję **+Dodaj**, aby otworzyć stronę **Dodawanie połączenia**.

    ![Utwórz połączenie lokacja-lokacja](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection1.png)

3. Na stronie **Dodawanie połączenia** wypełnij wartości, aby utworzyć połączenie.

  - **Nazwa:** nazwij połączenie. W tym przykładzie użyto nazwy **VNet1toSite2**.
  - **Typ połączenia:** wybierz pozycję **lokacja-lokacja(IPSec)**.
  - **Brama sieci wirtualnej:** wartość jest stała, ponieważ połączenie jest nawiązywane z tej bramy.
  - **Brama sieci lokalnej**: kliknij przycisk **Wybierz bramę sieci lokalnej** i wybierz bramę sieci lokalnej, która ma być używana. W tym przykładzie użyto bramy **Site2**.
  - **Klucz wspólny:** wartość musi być zgodna z wartością używaną dla lokalnego urządzenia sieci VPN. W tym przykładzie użyliśmy wartości „abc123”, ale można (i należy) użyć bardziej złożonej wartości. Pamiętaj o tym, że wartość podana w tym miejscu musi być taka sama, jak wartość podana podczas konfigurowania urządzenia sieci VPN.
  - Pozostałe wartości w polach **Subskrypcja**, **Grupa zasobów** i **Lokalizacja** są ustalone.

4. Kliknij przycisk **OK**, aby utworzyć połączenie. Na ekranie zostanie wyświetlony napis *Tworzenie połączenia*.
5. Połączenie będzie widoczne na stronie **Połączenia** bramy sieci wirtualnej. Stan zmieni się z *Nieznany* na *Łączenie*, a następnie na *Powiodło się*.