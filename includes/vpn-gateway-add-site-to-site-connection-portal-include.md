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
ms.openlocfilehash: 5149973fe63f867b49e55c970779c005e12536b9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780181"
---
1. Otwórz stronę bramy sieci wirtualnej. Istnieje wiele metod nawigacji. Możesz przejść do bramy, przechodząc do **nazwy sieci wirtualnej — omówienie >-> podłączone urządzenia-> nazwę bramy**.
2. Na stronie bramy kliknij pozycję **połączenia**. W górnej części strony Połączenia kliknij pozycję **+Dodaj**, aby otworzyć stronę **Dodawanie połączenia**.

   ![Utwórz połączenie lokacja-lokacja](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. Na stronie **Dodawanie połączenia** skonfiguruj wartości dla swojego połączenia.

   - **Nazwa:** Nazwij połączenie.
   - **Typ połączenia:** Wybierz pozycję **lokacja-lokacja (IPSec)** .
   - **Brama sieci wirtualnej:** Wartość jest stała, ponieważ nawiązujesz połączenie z tej bramy.
   - **Brama sieci lokalnej:** Kliknij pozycję **Wybierz bramę sieci lokalnej** i wybierz bramę sieci lokalnej, która ma być używana.
   - **Klucz wspólny:** wartość musi być zgodna z wartością używaną dla lokalnego urządzenia sieci VPN. W przykładzie użyto wartości „abc123”, ale można (i należy) użyć bardziej złożonej wartości. Pamiętaj o tym, że wartość podana w tym miejscu musi być taka sama, jak wartość podana podczas konfigurowania urządzenia sieci VPN.
   - Pozostałe wartości w polach **Subskrypcja**, **Grupa zasobów** i **Lokalizacja** są ustalone.

4. Kliknij przycisk **OK**, aby utworzyć połączenie. Na ekranie zostanie wyświetlony napis *Tworzenie połączenia*.
5. Połączenie będzie widoczne na stronie **Połączenia** bramy sieci wirtualnej. Stan zmieni się z *Nieznany* na *Łączenie*, a następnie na *Powiodło się*.
