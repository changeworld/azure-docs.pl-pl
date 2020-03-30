---
title: 'Dodawanie wielu połączeń lokacji bramy sieci VPN do sieci wirtualnej: Usługa Azure Portal'
description: Dodawanie połączeń S2S z wieloma lokacjami do bramy sieci VPN, która ma istniejące połączenie
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 7b438f2b966dc43d41b91a138b39193d230d5546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779692"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Dodawanie połączenia lokacja lokacja do sieci wirtualnej z istniejącym połączeniem bramy sieci VPN

> [!div class="op_single_selector"]
> * [Portal Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasyczny)](vpn-gateway-multi-site.md)
>
> 

Ten artykuł ułatwia dodawanie połączeń lokacja lokacja (S2S) do bramy sieci VPN, która ma istniejące połączenie przy użyciu witryny Azure portal. Ten typ połączenia jest często określany jako konfiguracja "wielowitamierowa". Połączenie S2S można dodać do sieci wirtualnej, która ma już połączenie S2S, połączenie typu punkt-lokacja lub połączenie sieci wirtualnej z siecią wirtualną. Istnieją pewne ograniczenia podczas dodawania połączeń. Przed rozpoczęciem upewnij się, że przed rozpoczęciem konfiguracji należy zapoznać się z sekcją [Przed rozpoczęciem.](#before) 

Ten artykuł dotyczy sieci wirtualnych Menedżera zasobów, które mają bramę sieci VPN opartą na routistywnie. Te kroki nie mają zastosowania do nowych konfiguracji połączeń współistniejących w układzie typu Site-to-Site. Jeśli jednak dodajesz tylko nowe połączenie sieci VPN do już istniejącej konfiguracji współistnienia, możesz wykonać następujące kroki. Aby uzyskać informacje na temat współistniejących połączeń, zobacz [Połączenia współistniejące usługi ExpressRoute/S2S.](../expressroute/expressroute-howto-coexist-resource-manager.md)

### <a name="deployment-models-and-methods"></a>Modele i metody wdrażania
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Aktualizujemy tę tabelę, gdy nowe artykuły i dodatkowe narzędzia stają się dostępne dla tej konfiguracji. Gdy artykuł jest dostępny, łączymy się bezpośrednio z nim z tej tabeli.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem
Sprawdź następujące elementy:

* Nie konfigurujesz nowej współistniejącej konfiguracji usługi ExpressRoute i bramy sieci VPN.
* Masz sieć wirtualną, która została utworzona przy użyciu modelu wdrażania Menedżera zasobów z istniejącym połączeniem.
* Brama sieci wirtualnej sieci wirtualnej jest RouteBased. Jeśli masz bramę sieci VPN opartą na zasadach, musisz usunąć bramę sieci wirtualnej i utworzyć nową bramę sieci VPN jako RouteBased.
* Żaden z zakresów adresów nakłada się na żadnej z sieci wirtualnych, z którymi łączy się ta sieci wirtualnej.
* Masz kompatybilne urządzenie VPN i kogoś, kto jest w stanie go skonfigurować. Zobacz artykuł [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Urządzenia sieci VPN — informacje). Jeśli nie dysponujesz wiedzą niezbędną do skonfigurowania urządzenia sieci VPN lub nie znasz zakresu adresów IP ujętego w konfiguracji sieci lokalnej, skontaktuj się z osobą, która może podać Ci te dane.
* Masz zewnętrznie publiczny adres IP urządzenia VPN. Ten adres IP nie może się znajdować za translatorem adresów sieciowych.

## <a name="part-1---configure-a-connection"></a><a name="part1"></a>Część 1 - Konfigurowanie połączenia
1. W przeglądarce przejdź do witryny [Azure Portal](https://portal.azure.com) i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Kliknij **pozycję Wszystkie zasoby** i znajdź **bramę sieci wirtualnej** z listy zasobów i kliknij ją.
3. Na stronie **Brama sieci wirtualnej** kliknij pozycję **Połączenia**.
   
    ![Strona Połączenia](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Strona Połączenia")<br>
4. Na stronie **Połączenia** kliknij pozycję **+Dodaj**.
   
    ![Przycisk Dodaj połączenie](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Przycisk Dodaj połączenie")<br>
5. Na stronie **Dodawanie połączenia** wypełnij następujące pola:
   
   * **Nazwa:** Nazwa, którą chcesz nadać witrynie, do której tworzysz połączenie.
   * **Typ połączenia:** Wybierz **opcję Lokacja lokacja (IPsec)**.
     
     ![Dodawanie strony połączenia](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Dodawanie strony połączenia")<br>

## <a name="part-2---add-a-local-network-gateway"></a><a name="part2"></a>Część 2 - Dodawanie bramy sieci lokalnej
1. Kliknij **pozycję Brama sieci lokalnej** Wybierz ***bramę sieci lokalnej***. Spowoduje to otwarcie strony **Wybierz bramę sieci lokalnej.**
   
    ![Wybieranie bramy sieci lokalnej](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Wybieranie bramy sieci lokalnej")<br>
2. Kliknij **pozycję Utwórz nowy,** aby otworzyć stronę **Utwórz bramę sieci lokalnej.**
   
    ![Tworzenie strony bramy sieci lokalnej](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Tworzenie bramy sieci lokalnej")<br>
3. Na stronie **Tworzenie bramy sieci lokalnej** wypełnij następujące pola:
   
   * **Nazwa:** Nazwa, którą chcesz nadać zasobowi bramy sieci lokalnej.
   * **Adres IP:** Publiczny adres IP urządzenia sieci VPN w witrynie, z którą chcesz się połączyć.
   * **Przestrzeń adresowa:** Przestrzeń adresowa, która ma być kierowana do nowej lokacji sieci lokalnej.
4. Kliknij **przycisk OK** na stronie Tworzenie bramy sieci **lokalnej,** aby zapisać zmiany.

## <a name="part-3---add-the-shared-key-and-create-the-connection"></a><a name="part3"></a>Część 3 - Dodaj klucz udostępniony i utwórz połączenie
1. Na stronie **Dodawanie połączenia** dodaj klucz udostępniony, którego chcesz użyć do utworzenia połączenia. Możesz uzyskać klucz udostępniony z urządzenia SIECI VPN lub zrobić go tutaj, a następnie skonfigurować urządzenie SIECI VPN do używania tego samego klucza udostępnionego. Ważne jest to, że klucze są dokładnie takie same.
   
    ![Klucz współużytkowany](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Klucz współużytkowany")<br>
2. U dołu strony kliknij przycisk **OK,** aby utworzyć połączenie.

## <a name="part-4---verify-the-vpn-connection"></a><a name="part4"></a>Część 4 - Sprawdź połączenie sieci VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Następne kroki

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [ścieżkę uczenia maszyn wirtualnych.](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
