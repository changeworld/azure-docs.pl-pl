---
title: 'Tworzenie bramy sieci VPN opartej na trasach: portalu Azure | Dokumentacja firmy Microsoft'
description: Szybko utworzyć bramę sieci VPN opartej na trasach przy użyciu portalu Azure
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: cherylmc
ms.openlocfilehash: 2d6133e974e24c8c4f769995d8245b30a29a3983
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Utwórz bramę sieci VPN opartej na trasach przy użyciu portalu Azure

Ten artykuł pomaga szybko utworzyć bramę sieci VPN platformy Azure opartej na trasach przy użyciu portalu Azure.  Brama sieci VPN jest używany podczas tworzenia połączenia sieci VPN do sieci lokalnej. Aby połączyć sieci wirtualnych umożliwia także bramy sieci VPN. 

Kroki opisane w tym artykule spowoduje utworzenie sieci wirtualnej, podsieci, podsieć bramy i bramy sieci VPN opartej na trasach (bramy sieci wirtualnej). Po zakończeniu tworzenia bramy następnie można utworzyć połączenia. Kroki te wymagają subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="vnet"></a>Tworzenie sieci wirtualnej

1. Przejdź w przeglądarce do witryny [Azure Portal](http://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **Utwórz zasób**. W polu **Szukaj w witrynie Marketplace** wpisz „Sieć wirtualna”. Znajdź pozycję **Sieć wirtualna** na liście wyników i kliknij, aby otworzyć stronę **Sieć wirtualna**.
3. W dolnej części strony sieci wirtualnej z **wybierz model wdrożenia** listy, upewnij się, że **Resource Manager** wybrany z listy rozwijanej, a następnie kliknij przycisk **Utwórz**. Spowoduje to otwarcie **Utwórz sieć wirtualną** strony.
4. Na stronie **Tworzenie sieci wirtualnej** skonfiguruj ustawienia sieci wirtualnej. Po wypełnieniu pól czerwony wykrzyknik zmieni się na zielony znacznik wyboru, jeśli znaki wprowadzone w polu są prawidłowe. Wprowadź następujące wartości:

  - **Nazwa**: TestVNet1
  - **Przestrzeń adresowa**: 10.1.0.0/16
  - **Subskrypcja**: Sprawdź, czy wymienionej subskrypcji, którego chcesz użyć. Subskrypcje można zmieniać, korzystając z listy rozwijanej.
  - **Grupa zasobów**: TestRG1
  - **Lokalizacja**: wschodnie stany USA
  - **Podsieci**: frontonu
  - **Zakres adresów**: 10.1.0.0/24

  ![Strona Tworzenie sieci wirtualnej](./media/create-routebased-vpn-gateway-portal/vnet1.png "Strona Tworzenie sieci wirtualnej")
5. Po wprowadzeniu wartości, wybierz **Przypnij do pulpitu nawigacyjnego** ułatwia znajdowanie sieci wirtualnej na pulpicie nawigacyjnym, a następnie kliknij przycisk **Utwórz**. Po kliknięciu przycisku **Utwórz**, zobacz kafelka na pulpicie nawigacyjnym, które odzwierciedla postęp Twojej sieci wirtualnej. Wygląd kafelka zmienia się w trakcie tworzenia sieci wirtualnej.

## <a name="gwsubnet"></a>Dodaj podsieć bramy

Podsieć bramy zawiera zastrzeżone adresy IP, które używają usługi bramy sieci wirtualnej. Utwórz podsieć bramy.

1. W portalu przejdź do sieci wirtualnej, dla której chcesz utworzyć bramę sieci wirtualnej.
2. Na stronie sieci wirtualnej, kliknij przycisk **podsieci** rozszerzenia **VNet1 - podsieci** strony.
3. Kliknij przycisk **+ podsieci bramy** u góry, aby otworzyć **Dodaj podsieć** strony.

  ![Dodawanie podsieci bramy](./media/create-routebased-vpn-gateway-portal/gateway_subnet.png "Dodawanie podsieci bramy")
4. **Nazwa** dla podsieci jest automatycznie wypełniane wymagana wartość "GatewaySubnet". Dostosuj automatycznie wypełnianej **zakres adresów** wartości odpowiadające następujące wartości:

  **Zakres (blok CIDR) adresów**: 10.1.255.0/27

  ![Dodawanie podsieci bramy](./media/create-routebased-vpn-gateway-portal/add_gw_subnet.png "Dodawanie podsieci bramy")
5. Aby utworzyć podsieć bramy, kliknij przycisk **OK** w dolnej części strony.

## <a name="gwvalues"></a>Konfigurowanie ustawień bramy

1. W lewej części strony portalu kliknij **+ Utwórz zasób** i w polu wyszukiwania wpisz "Brama sieci wirtualnej". W obszarze **Wyniki** zlokalizuj i kliknij pozycję **Brama sieci wirtualnej**.
2. W dolnej części strony "Brama sieci wirtualnej", kliknij przycisk **Utwórz** otworzyć **Utwórz bramę sieci wirtualnej** strony.
3. Na stronie **Tworzenie bramy sieci wirtualnej** określ wartości dla swojej bramy sieci wirtualnej.

  - **Nazwa**: Vnet1GW
  - **Typ bramy**: sieci VPN 
  - **Typ sieci VPN**: opartej na trasach
  - **Jednostka SKU**: VpnGw1
  - **Lokalizacja**: wschodnie stany USA
  - **Sieć wirtualna**: kliknij **wirtualnej sieci/wybierz sieć wirtualną** otworzyć **wybierz sieć wirtualną** strony. Wybierz **VNet1**.

  ![Konfigurowanie ustawień bramy](./media/create-routebased-vpn-gateway-portal/configure_gw.png "Konfigurowanie ustawień bramy")

## <a name="pip"></a>Utwórz publiczny adres IP

Brama sieci VPN musi mieć dynamicznie przydzielonego publicznego adresu IP. Podczas tworzenia połączenia z bramą sieci VPN jest adres IP, który łączy się z urządzenia lokalnego.

1. Wybierz **konfiguracji IP bramy pierwszy adres IP konfiguracji Utwórz** żądania publicznego adresu IP.

  ![Pierwszy konfiguracji IP](./media/create-routebased-vpn-gateway-portal/ip.png "konfiguracji pierwszy adres IP")
2. Na **wybierz publicznego adresu IP strony**, kliknij przycisk **+ Utwórz nowy** otworzyć **tworzenie publicznego adresu IP** strony.
3. Skonfiguruj ustawienia, z następującymi wartościami:

  - **Nazwa**: **VNet1GWPIP**
  - **Jednostka SKU**: **podstawowe**

  ![Tworzenie publicznego adresu IP](./media/create-routebased-vpn-gateway-portal/gw_ip.png "Tworzenie adresu PIP")
4. Kliknij przycisk **OK** u dołu tej strony, aby zapisać zmiany.

## <a name="creategw"></a>Tworzenie bramy sieci VPN

1. Sprawdź ustawienia na **Utwórz bramę sieci wirtualnej** strony. Dostosuj wartości, jeśli to konieczne.

  ![Utwórz bramę sieci VPN](./media/create-routebased-vpn-gateway-portal/create_gw.png "bramy tworzenie sieci VPN")
2. Kliknij przycisk **Utwórz** w dolnej części strony.

Po kliknięciu **Utwórz**, ustawienia zostaną sprawdzone i **Brama sieci wirtualnej wdrażanie** na pulpicie nawigacyjnym pojawi się Kafelek. Brama sieci VPN może potrwać do 45 minut. Być może będzie trzeba odświeżyć stronę portalu, aby zobaczyć, czy tworzenie zostało ukończone.

## <a name="viewgw"></a>Widok bramy sieci VPN

1. Po utworzeniu bramy, przejdź do VNet1 w portalu. Brama sieci VPN zostanie wyświetlony na stronie Przegląd jako urządzenie podłączone.

  ![Połączone urządzenia](./media/create-routebased-vpn-gateway-portal/connected_devices.png "podłączone urządzenia")

2. Na liście urządzeń kliknij **VNet1GW** Aby wyświetlić więcej informacji.

  ![Brama sieci VPN widoku](./media/create-routebased-vpn-gateway-portal/view_gw2.png "bramy sieci VPN widoku")

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu bramy podczas tworzenia, można utworzyć połączenie między sieci wirtualnej i innej sieci wirtualnej. Lub Utwórz połączenie między sieci wirtualnej i lokalizacji lokalnej.

> [!div class="nextstepaction"]
> [Utwórz połączenie lokacja lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> [utworzyć połączenie punkt lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> [utworzyć połączenie do innej sieci wirtualnej](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)