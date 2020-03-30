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
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183066"
---
### <a name="to-modify-the-local-network-gateway-ip-address---no-gateway-connection"></a><a name="gwipnoconnection"></a>Aby zmodyfikować adres IP bramy sieci lokalnej — brak połączenia bramy

Użyj przykładu, aby zmodyfikować bramę sieci lokalnej bez połączenia bramy. Podczas modyfikowania tej wartości możesz również zmodyfikować prefiksy adresów.

1. W zasobie Brama sieci lokalnej w sekcji **Ustawienia** kliknij pozycję **Konfiguracja**.
2. W polu **Adres IP** zmodyfikuj adres IP.
3. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

### <a name="to-modify-the-local-network-gateway-ip-address---existing-gateway-connection"></a><a name="gwipwithconnection"></a>Aby zmodyfikować adres IP bramy sieci lokalnej — istniejące połączenie bramy

Aby zmodyfikować bramę sieci lokalnej, która ma połączenie, należy najpierw usunąć połączenie. Po usunięciu połączenia możesz zmodyfikować adres IP bramy i ponownie utworzyć nowe połączenie. Jednocześnie możesz również zmodyfikować prefiksy adresów. Spowoduje to pewien przestój połączenia sieci VPN. W przypadku modyfikowania adresu IP bramy nie musisz usuwać bramy sieci VPN. Musisz usunąć tylko połączenie.
 
#### <a name="1-remove-the-connection"></a>1. Usuń połączenie.

1. W zasobie Brama sieci lokalnej w sekcji **Ustawienia** kliknij pozycję **Połączenia**.
2. Kliknij **...** w wierszu połączenia, a następnie kliknij przycisk **Usuń**.
3. Kliknij **przycisk Zapisz,** aby zapisać ustawienia.

#### <a name="2-modify-the-ip-address"></a>2. Zmodyfikuj adres IP.

Jednocześnie możesz również zmodyfikować prefiksy adresów.

1. W polu **Adres IP** zmodyfikuj adres IP.
2. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

#### <a name="3-recreate-the-connection"></a>3. Odtwórz połączenie.

1. Przejdź do bramy sieci wirtualnej dla sieci wirtualnej. (Nie brama sieci lokalnej).
2. Na bramie sieci wirtualnej w sekcji **Ustawienia** kliknij pozycję **Połączenia**.
3. Kliknij **przycisk + Dodaj,** aby otworzyć blok **Dodaj połączenie.**
4. Odtwórz połączenie.
5. Kliknij **przycisk OK,** aby utworzyć połączenie.