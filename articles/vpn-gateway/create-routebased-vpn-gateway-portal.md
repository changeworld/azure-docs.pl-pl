---
title: 'Utwórz bramę sieci VPN opartej na trasach: Witryna Azure portal | Dokumentacja firmy Microsoft'
description: Tworzenie bramy sieci VPN opartej na trasach przy użyciu witryny Azure portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/18/2018
ms.author: cherylmc
ms.openlocfilehash: ddc42023bae3403e7778327a40316462c85222c0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60390074"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Tworzenie bramy sieci VPN opartej na trasach przy użyciu witryny Azure portal

Ten artykuł ułatwia szybkie tworzenie bramy sieci VPN platformy Azure opartej na trasach przy użyciu witryny Azure portal.  Tworzenie bramy sieci VPN jest używany podczas tworzenia połączenia sieci VPN z siecią lokalną. Łączenie sieci wirtualnych umożliwia także tworzenie bramy sieci VPN. 

Kroki opisane w tym artykule spowoduje utworzenie sieci wirtualnej, podsieci, podsieć bramy i bramy sieci VPN opartej na trasach (Brama sieci wirtualnej). Po zakończeniu tworzenia bramy będzie można utworzyć połączenia. Te kroki wymagają subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="vnet"></a>Tworzenie sieci wirtualnej

1. Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **Utwórz zasób**. W polu **Szukaj w witrynie Marketplace** wpisz „Sieć wirtualna”. Znajdź pozycję **Sieć wirtualna** na liście wyników i kliknij, aby otworzyć stronę **Sieć wirtualna**.
3. W dolnej części strony sieć wirtualna z **wybierz model wdrożenia** listy, upewnij się, że **usługi Resource Manager** wybrania z listy rozwijanej, a następnie kliknij przycisk **Utwórz**. Spowoduje to otwarcie **Utwórz sieć wirtualną** strony.
4. Na stronie **Tworzenie sieci wirtualnej** skonfiguruj ustawienia sieci wirtualnej. Po wypełnieniu pól czerwony wykrzyknik zmieni się na zielony znacznik wyboru, jeśli znaki wprowadzone w polu są prawidłowe. Użyj następujących wartości:

   - **Nazwa**: TestVNet1
   - **Przestrzeń adresowa**: 10.1.0.0/16
   - **Subskrypcja**: Sprawdź subskrypcję na liście, którego chcesz użyć. Subskrypcje można zmieniać, korzystając z listy rozwijanej.
   - **Grupa zasobów**: TestRG1
   - **Lokalizacja**: Wschodnie stany USA
   - **Podsieć**: Frontonu
   - **Zakres adresów**: 10.1.0.0/24

   ![Strona Tworzenie sieci wirtualnej](./media/create-routebased-vpn-gateway-portal/create-virtual-network.png "Strona Tworzenie sieci wirtualnej")
5. Po wprowadzeniu wartości, wybierz **Przypnij do pulpitu nawigacyjnego** ułatwia znajdowanie sieci wirtualnej na pulpicie nawigacyjnym, a następnie kliknij przycisk **Utwórz**. Po kliknięciu przycisku **Utwórz**, zobaczysz Kafelek na pulpicie nawigacyjnym, który będzie odzwierciedlać postęp Twojej sieci wirtualnej. Wygląd kafelka zmienia się w trakcie tworzenia sieci wirtualnej.

## <a name="gwsubnet"></a>Dodawanie podsieci bramy

Podsieć bramy zawiera zastrzeżone adresy IP, z których korzystają usługi bramy sieci wirtualnej. Tworzenie podsieci bramy.

1. W portalu przejdź do sieci wirtualnej, dla której chcesz utworzyć bramę sieci wirtualnej.
2. Na stronie sieci wirtualnej, kliknij przycisk **podsieci** rozszerzania **VNet1 - podsieci** strony.
3. Kliknij przycisk **+ podsieć bramy** u góry, aby otworzyć **Dodaj podsieć** strony.

   ![Dodawanie podsieci bramy](./media/create-routebased-vpn-gateway-portal/gateway-subnet.png "Dodawanie podsieci bramy")
4. **Nazwa** dla podsieci zostanie automatycznie wypełniona wymagane wartością "GatewaySubnet". Dostosuj automatycznie wypełniane **zakres adresów** wartości, aby dopasować następujące wartości:

   **Zakres adresów (blok CIDR)** : 10.1.255.0/27

   ![Dodawanie podsieci bramy](./media/create-routebased-vpn-gateway-portal/add-gateway-subnet.png "Dodawanie podsieci bramy")
5. Aby utworzyć podsieć bramy, kliknij przycisk **OK** w dolnej części strony.

## <a name="gwvalues"></a>Konfigurowanie ustawień bramy

1. W lewej części strony portalu kliknij pozycję **+ Utwórz zasób** i wpisz "Brama sieci wirtualnej" w polu wyszukiwania, następnie naciśnij klawisz **Enter**. W obszarze **Wyniki** zlokalizuj i kliknij pozycję **Brama sieci wirtualnej**.
2. W dolnej części strony "Brama sieci wirtualnej" kliknij **Utwórz** otworzyć **Tworzenie bramy sieci wirtualnej** strony.
3. Na stronie **Tworzenie bramy sieci wirtualnej** określ wartości dla swojej bramy sieci wirtualnej.

   - **Nazwa**: Vnet1GW
   - **Typ bramy**: Sieć VPN 
   - **Typ sieci VPN**: Oparte na trasach
   - **SKU**: VpnGw1
   - **Lokalizacja**: Wschodnie stany USA
   - **Sieć wirtualna**: Kliknij przycisk **sieci wirtualnej/wybierz sieć wirtualną** otworzyć **wybierz sieć wirtualną** strony. Wybierz **VNet1**.
   - **Publiczny adres IP**: To ustawienie określa obiektu publicznego adresu IP, który zostaje skojarzony z bramą sieci VPN. Publiczny adres IP jest dynamicznie przypisywany do tego obiektu podczas tworzenia bramy sieci VPN. Brama sieci VPN aktualnie obsługuje tylko *dynamiczne* przypisywanie publicznych adresów IP. Nie oznacza to jednak, że adres IP zmienia się po przypisaniu go do bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

     - Pozostaw wybraną opcję **Utwórz nowy**.
     - W polu tekstowym wprowadź **nazwę** publicznego adresu IP. Na potrzeby tego ćwiczenia, użyj **VNet1GWIP**.<br>

     ![Konfigurowanie ustawień bramy](./media/create-routebased-vpn-gateway-portal/gw.png "Konfigurowanie ustawień bramy")

## <a name="creategw"></a>Tworzenie bramy sieci VPN

1. Sprawdź ustawienia na **Tworzenie bramy sieci wirtualnej** strony. Dostosuj wartości, jeśli to konieczne.
2. Kliknij przycisk **Utwórz** w dolnej części strony.

   Po kliknięciu **Utwórz**, ustawienia zostaną zweryfikowane i **wdrażanie bramy sieci wirtualnej** pojawi się Kafelek na pulpicie nawigacyjnym. Tworzenie bramy sieci VPN może zająć do 45 minut. Być może będzie trzeba odświeżyć stronę portalu, aby zobaczyć, czy tworzenie zostało ukończone.

## <a name="viewgw"></a>Wyświetl bramy sieci VPN

1. Po utworzeniu bramy, przejdź do sieci VNet1, w portalu. Bramy sieci VPN jest wyświetlana na stronie Przegląd jako urządzenie podłączone.

   ![Połączone urządzenia](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "połączone urządzenia")

2. Na liście urządzeń kliknij **VNet1GW** Aby wyświetlić więcej informacji.

   ![Wyświetl bramy sieci VPN](./media/create-routebased-vpn-gateway-portal/view-gateway.png "bramy sieci VPN widoku")

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu bramy podczas tworzenia, można utworzyć połączenie między siecią wirtualną a inną siecią wirtualną. Lub Utwórz połączenie między siecią wirtualną a lokalizacją lokalną.

> [!div class="nextstepaction"]
> [Utwórz połączenie lokacja lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Utwórz połączenie punkt lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Utwórz połączenie z inną siecią wirtualną](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
