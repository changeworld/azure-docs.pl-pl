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
ms.openlocfilehash: a929149f115d716bf7f9d850abe5ba97bd5a8189
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30198098"
---
### <a name="gwipnoconnection"></a> Aby zmodyfikować adres IP bramy sieci lokalnej — Brak połączenia bramy

Użyj przykładu, aby zmodyfikować bramę sieci lokalnej bez połączenia bramy. Podczas modyfikowania tej wartości możesz również zmodyfikować prefiksy adresów.

1. Dla zasobu bramy sieci lokalnej w **ustawienia** kliknij **konfiguracji**.
2. W **adres IP** zmodyfikuj adres IP.
3. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

### <a name="gwipwithconnection"></a>Aby zmodyfikować sieci lokalnej bramy adresu IP bramy - istniejące połączenie z bramą

Aby zmodyfikować bramy sieci lokalnej, która ma połączenie, należy najpierw usunąć połączenie. Po usunięciu połączenia możesz zmodyfikować adres IP bramy i ponownie utworzyć nowe połączenie. Jednocześnie możesz również zmodyfikować prefiksy adresów. Spowoduje to pewien przestój połączenia sieci VPN. W przypadku modyfikowania adresu IP bramy nie musisz usuwać bramy sieci VPN. Musisz usunąć tylko połączenie.
 
#### <a name="1-remove-the-connection"></a>1. Usuń połączenie.

1. Dla zasobu bramy sieci lokalnej w **ustawienia** kliknij **połączenia**.
2. Kliknij przycisk **...**  w wierszu dla połączenia, następnie kliknij przycisk **usunąć**.
3. Kliknij przycisk **zapisać** Aby zapisać ustawienia.

#### <a name="2-modify-the-ip-address"></a>2. Zmodyfikuj adres IP.

Jednocześnie możesz również zmodyfikować prefiksy adresów.

1. W **adres IP** zmodyfikuj adres IP.
2. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

#### <a name="3-recreate-the-connection"></a>3. Utwórz ponownie połączenie.

1. Przejdź do bramy sieci wirtualnej sieci wirtualnej. (Nie bramy sieci lokalnej.)
2. W bramie sieci wirtualnej w **ustawienia** kliknij **połączenia**.
3. Kliknij przycisk **+ Dodaj** otworzyć **Dodaj połączenie** bloku.
4. Utwórz ponownie połączenie.
5. Kliknij przycisk **OK** do utworzenia połączenia.