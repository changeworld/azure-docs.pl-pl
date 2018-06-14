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
ms.openlocfilehash: 3b8049515f753cbcf8ca068c1790f716f02d30b6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197945"
---
### <a name="noconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — brak połączenia bramy

#### <a name="to-add-additional-address-prefixes"></a>Aby dodać dodatkowe prefiksy adresów:

1. Dla zasobu bramy sieci lokalnej w **ustawienia** kliknij **konfiguracji**.
2. Dodawanie przestrzeni adresów IP w *Dodaj dodatkowy zakres adresów* pole.
3. Kliknij przycisk **zapisać** Aby zapisać ustawienia.

#### <a name="to-remove-address-prefixes"></a>Aby usunąć prefiksy adresów:

1. Dla zasobu bramy sieci lokalnej w **ustawienia** kliknij **konfiguracji**.
2. Kliknij przycisk **"..."** na wiersz zawierający prefiks do usunięcia.
3. Kliknij przycisk **Usuń**.
4. Kliknij przycisk **zapisać** Aby zapisać ustawienia.

### <a name="withconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — istniejące połączenie bramy

Jeśli istnieje już połączenie bramy i chcesz dodać lub usunąć prefiksy adresów IP zawarte w bramie Twojej sieci lokalnej, wykonaj kolejno następujące kroki. Spowoduje to pewien przestój połączenia sieci VPN. W przypadku modyfikowania prefiksów adresów IP nie musisz usuwać bramy sieci VPN. Musisz usunąć tylko połączenie.

#### <a name="1-remove-the-connection"></a>1. Usuń połączenie.

1. Dla zasobu bramy sieci lokalnej w **ustawienia** kliknij **połączenia**.
2. Kliknij przycisk **...**  wiersza dla każdego połączenia, następnie kliknij przycisk **usunąć**.
3. Kliknij przycisk **zapisać** Aby zapisać ustawienia.

#### <a name="2-modify-the-address-prefixes"></a>2. Zmodyfikuj prefiksy adresów.

Aby dodać dodatkowe prefiksy adresów:

1. Dla zasobu bramy sieci lokalnej w **ustawienia** kliknij **konfiguracji**.
2. Dodawanie przestrzeni adresów IP.
3. Kliknij przycisk **zapisać** Aby zapisać ustawienia.

Aby usunąć prefiksy adresów:

1. Dla zasobu bramy sieci lokalnej w **ustawienia** kliknij **konfiguracji**.
2. Kliknij przycisk **...**  na wiersz zawierający prefiks do usunięcia.
3. Kliknij przycisk **Usuń**.
4. Kliknij przycisk **zapisać** Aby zapisać ustawienia.

#### <a name="3-recreate-the-connection"></a>3. Utwórz ponownie połączenie.

1. Przejdź do bramy sieci wirtualnej sieci wirtualnej. (Nie bramy sieci lokalnej.)
2. W bramie sieci wirtualnej w **ustawienia** kliknij **połączenia**.
3. Kliknij przycisk **+ Dodaj** otworzyć **Dodaj połączenie** bloku.
4. Utwórz ponownie połączenie.
5. Kliknij przycisk **OK** do utworzenia połączenia.