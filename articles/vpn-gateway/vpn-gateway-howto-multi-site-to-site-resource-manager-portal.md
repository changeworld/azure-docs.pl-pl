---
title: 'Dodawanie wielu połączeń lokacja-lokacja bramy sieci VPN z siecią wirtualną: Azure Portal: usługi Resource Manager | Dokumentacja firmy Microsoft'
description: Dodawanie połączeń S2S połączenia obejmujące wiele lokacji do bramy sieci VPN, który ma istniejące połączenie
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: a814834be3225764c3b6f237bd515ca087f975a7
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873125"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Dodawanie połączenia lokacja-lokacja z siecią wirtualną przy użyciu istniejącego połączenia bramy sieci VPN

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasyczny)](vpn-gateway-multi-site.md)
>
> 

W tym artykule ułatwiają dodawanie połączenia lokacja-lokacja (S2S) do bramy sieci VPN, z istniejącego połączenia przy użyciu witryny Azure portal. Ten typ połączenia jest często nazywane konfiguracji "obejmującego wiele lokacji". Można dodać połączenie S2S z siecią wirtualną, który ma już połączenie S2S, połączenia punkt-lokacja lub połączenia sieć wirtualna-sieć wirtualna. Istnieją pewne ograniczenia, podczas dodawania połączenia. Sprawdź [przed rozpoczęciem](#before) sekcję w tym artykule, aby sprawdzić przed rozpoczęciem korzystania z konfiguracji. 

Ten artykuł dotyczy sieci wirtualnych Menedżera zasobów, które mają bramę sieci VPN typu RouteBased. Te kroki nie dotyczą współistniejące konfiguracje połączeń usługi ExpressRoute/lokacja-lokacja. Zobacz [współistniejących połączeń usługi ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) uzyskać informacji na temat współistniejących połączeń.

### <a name="deployment-models-and-methods"></a>Modele i metody wdrażania
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Aktualizujemy ją jako nowych artykułów i dodatkowych narzędzi stają się dostępne dla tej konfiguracji. Jeśli artykuł jest dostępny, można przejść do niego bezpośrednio z tej tabeli.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Przed rozpoczęciem
Sprawdź następujące elementy:

* Współistniejące połączenia usługi ExpressRoute/S2S są nietworzenie.
* Masz sieć wirtualną, który został utworzony przy użyciu modelu wdrażania usługi Resource Manager przy użyciu istniejącego połączenia.
* Brama sieci wirtualnej dla sieci wirtualnej jest typu RouteBased. Jeśli brama sieci VPN PolicyBased, należy usunąć bramy sieci wirtualnej i utworzyć nową bramę sieci VPN, jako typu RouteBased.
* Żaden z zakresów adresów nakładać się na każdą z sieci wirtualnych, które łączy się z tą siecią wirtualną.
* Masz zgodne urządzenie sieci VPN i osoba, która umie je skonfigurować. Zobacz artykuł [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Urządzenia sieci VPN — informacje). Jeśli nie dysponujesz wiedzą niezbędną do skonfigurowania urządzenia sieci VPN lub nie znasz zakresu adresów IP ujętego w konfiguracji sieci lokalnej, skontaktuj się z osobą, która może podać Ci te dane.
* Masz dostępny zewnętrznie publiczny adres IP urządzenia sieci VPN. Ten adres IP nie może się znajdować za translatorem adresów sieciowych.

## <a name="part1"></a>Część 1 — Konfigurowanie połączenia
1. W przeglądarce przejdź do witryny [Azure Portal](http://portal.azure.com) i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Kliknij przycisk **wszystkie zasoby** i Znajdź swoje **bramy sieci wirtualnej** z listy zasobów i kliknij ją.
3. Na **bramy sieci wirtualnej** kliknij **połączeń**.
   
    ![Strona Połączenia](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Strona Połączenia")<br>
4. Na **połączeń** kliknij **+ Dodaj**.
   
    ![Przycisk połączenia Dodaj](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Dodaj przycisk połączenia")<br>
5. Na **Dodaj połączenie** strony, wypełnij następujące pola:
   
   * **Nazwa:** nazwę, która ma zostać przypisany do lokacji tworzysz połączenie.
   * **Typ połączenia:** wybierz **lokacja lokacja (IPsec)**.
     
     ![Dodaj strony połączenia](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Dodaj strona połączenia")<br>

## <a name="part2"></a>Część 2 — Dodawanie bramy sieci lokalnej
1. Kliknij przycisk **bramy sieci lokalnej** ***wybierz bramę sieci lokalnej***. Spowoduje to otwarcie **Wybieranie bramy sieci lokalnej** strony.
   
    ![Wybieranie bramy sieci lokalnej](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Wybieranie bramy sieci lokalnej")<br>
2. Kliknij przycisk **Utwórz nową** otworzyć **Utwórz bramę sieci lokalnej** strony.
   
    ![Utwórz stronę bramy sieci lokalnej](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Utwórz bramę sieci lokalnej")<br>
3. Na **Utwórz bramę sieci lokalnej** strony, wypełnij następujące pola:
   
   * **Nazwa:** nazwę, która ma zostać przypisany do zasobu bramy sieci lokalnej.
   * **Adres IP:** publiczny adres IP urządzenia sieci VPN w witrynie, którą chcesz nawiązać połączenie.
   * **Przestrzeń adresowa:** przestrzeni adresowej, które mają być kierowane do nowej lokacji sieci lokalnej.
4. Kliknij przycisk **OK** na **Utwórz bramę sieci lokalnej** strony, aby zapisać zmiany.

## <a name="part3"></a>Część 3 — Dodaj klucz współużytkowany i Utwórz połączenie
1. Na **Dodaj połączenie** strony, należy dodać klucz współużytkowany, którego chcesz użyć, aby utworzyć połączenie. Można pobrać klucza wstępnego z urządzeniem sieci VPN lub wprowadzić tutaj i następnie skonfigurować urządzenie sieci VPN, aby użyć tego samego klucza współużytkowanego. Ważne jest, że klucze są dokładnie takie same.
   
    ![Klucz współużytkowany](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Klucz współużytkowany")<br>
2. W dolnej części strony kliknij **OK** do utworzenia połączenia.

## <a name="part4"></a>Część 4 — weryfikowanie połączenia sieci VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Zobacz [ścieżka szkoleniowa dla usługi maszyny wirtualne](/learn/paths/deploy-a-website-with-azure-virtual-machines/) Aby uzyskać więcej informacji.
