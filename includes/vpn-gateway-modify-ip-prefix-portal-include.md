---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419605"
---
### <a name="noconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — brak połączenia bramy

#### <a name="to-add-additional-address-prefixes"></a>Aby dodać dodatkowe prefiksy adresów:

1. W zasobie bramy sieci lokalnej w **ustawienia** kliknij **konfiguracji**.
2. Dodaj przestrzeń adresów IP w *Dodaj dodatkowy zakres adresów* pole.
3. Kliknij przycisk **Zapisz** można zapisać ustawień.

#### <a name="to-remove-address-prefixes"></a>Aby usunąć prefiksy adresów:

1. W zasobie bramy sieci lokalnej w **ustawienia** kliknij **konfiguracji**.
2. Kliknij przycisk **"..."** w wierszu zawierającym prefiks do usunięcia.
3. Kliknij pozycję **Usuń**.
4. Kliknij przycisk **Zapisz** można zapisać ustawień.

### <a name="withconnection"></a>Aby zmodyfikować prefiksy adresów IP bramy sieci lokalnej — istniejące połączenie bramy

Jeśli istnieje już połączenie bramy i chcesz dodać lub usunąć prefiksy adresów IP zawarte w bramie Twojej sieci lokalnej, wykonaj kolejno następujące kroki. Spowoduje to pewien przestój połączenia sieci VPN. W przypadku modyfikowania prefiksów adresów IP nie musisz usuwać bramy sieci VPN. Musisz usunąć tylko połączenie.

#### <a name="1-remove-the-connection"></a>1. Usuń połączenie.

1. W zasobie bramy sieci lokalnej w **ustawienia** kliknij **połączeń**.
2. Kliknij przycisk **...**  wiersza dla każdego połączenia, następnie kliknij przycisk **Usuń**.
3. Kliknij przycisk **Zapisz** można zapisać ustawień.

#### <a name="2-modify-the-address-prefixes"></a>2. Modyfikowanie prefiksów adresów.

Aby dodać dodatkowe prefiksy adresów:

1. W zasobie bramy sieci lokalnej w **ustawienia** kliknij **konfiguracji**.
2. Dodaj przestrzeń adresów IP.
3. Kliknij przycisk **Zapisz** można zapisać ustawień.

Aby usunąć prefiksy adresów:

1. W zasobie bramy sieci lokalnej w **ustawienia** kliknij **konfiguracji**.
2. Kliknij przycisk **...**  w wierszu zawierającym prefiks do usunięcia.
3. Kliknij pozycję **Usuń**.
4. Kliknij przycisk **Zapisz** można zapisać ustawień.

#### <a name="3-recreate-the-connection"></a>3. Ponowne utworzenie połączenia.

1. Przejdź do bramy sieci wirtualnej dla sieci wirtualnej. (Nie brama sieci lokalnej.)
2. Na bramy sieci wirtualnej w **ustawienia** kliknij **połączeń**.
3. Kliknij przycisk **+ Dodaj** otworzyć **Dodaj połączenie** bloku.
4. Utwórz ponownie połączenie.
5. Kliknij przycisk **OK** do utworzenia połączenia.