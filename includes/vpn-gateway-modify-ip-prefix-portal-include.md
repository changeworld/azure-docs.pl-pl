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
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183067"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — brak połączenia bramy

#### <a name="to-add-additional-address-prefixes"></a>Aby dodać dodatkowe prefiksy adresów:

1. W zasobie Brama sieci lokalnej w sekcji **Ustawienia** kliknij pozycję **Konfiguracja**.
2. Dodaj przestrzeń adresową IP w polu *Dodaj zakres dodatkowych adresów.*
3. Kliknij **przycisk Zapisz,** aby zapisać ustawienia.

#### <a name="to-remove-address-prefixes"></a>Aby usunąć prefiksy adresów:

1. W zasobie Brama sieci lokalnej w sekcji **Ustawienia** kliknij pozycję **Konfiguracja**.
2. Kliknij **"...",** w wierszu zawierającym prefiks, który chcesz usunąć.
3. Kliknij pozycję **Usuń**.
4. Kliknij **przycisk Zapisz,** aby zapisać ustawienia.

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — istniejące połączenie bramy

Jeśli istnieje już połączenie bramy i chcesz dodać lub usunąć prefiksy adresów IP zawarte w bramie Twojej sieci lokalnej, wykonaj kolejno następujące kroki. Spowoduje to pewien przestój połączenia sieci VPN. W przypadku modyfikowania prefiksów adresów IP nie musisz usuwać bramy sieci VPN. Musisz usunąć tylko połączenie.

#### <a name="1-remove-the-connection"></a>1. Usuń połączenie.

1. W zasobie Brama sieci lokalnej w sekcji **Ustawienia** kliknij pozycję **Połączenia**.
2. Kliknij **...** w wierszu dla każdego połączenia, a następnie kliknij przycisk **Usuń**.
3. Kliknij **przycisk Zapisz,** aby zapisać ustawienia.

#### <a name="2-modify-the-address-prefixes"></a>2. Zmodyfikuj prefiksy adresu.

Aby dodać dodatkowe prefiksy adresów:

1. W zasobie Brama sieci lokalnej w sekcji **Ustawienia** kliknij pozycję **Konfiguracja**.
2. Dodaj przestrzeń adresową IP.
3. Kliknij **przycisk Zapisz,** aby zapisać ustawienia.

Aby usunąć prefiksy adresów:

1. W zasobie Brama sieci lokalnej w sekcji **Ustawienia** kliknij pozycję **Konfiguracja**.
2. Kliknij **...** w wierszu zawierającym prefiks, który chcesz usunąć.
3. Kliknij pozycję **Usuń**.
4. Kliknij **przycisk Zapisz,** aby zapisać ustawienia.

#### <a name="3-recreate-the-connection"></a>3. Odtwórz połączenie.

1. Przejdź do bramy sieci wirtualnej dla sieci wirtualnej. (Nie brama sieci lokalnej).
2. Na bramie sieci wirtualnej w sekcji **Ustawienia** kliknij pozycję **Połączenia**.
3. Kliknij **przycisk + Dodaj,** aby otworzyć blok **Dodaj połączenie.**
4. Odtwórz połączenie.
5. Kliknij **przycisk OK,** aby utworzyć połączenie.