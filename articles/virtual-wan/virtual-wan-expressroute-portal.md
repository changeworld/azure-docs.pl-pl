---
title: Samouczek — tworzenie połączeń usługi ExpressRoute przy użyciu wirtualnej sieci WAN platformy Azure
description: W tym samouczku pokazano, jak używać usługi Azure Virtual WAN do tworzenia połączeń usługi ExpressRoute z platformą Azure i środowiskami lokalnymi.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 35ca071cd8495611f0f350511ef9406f82c5be23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77209430"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Samouczek: Tworzenie skojarzenia usługi ExpressRoute przy użyciu wirtualnej sieci WAN platformy Azure

W tym samouczku pokazano, jak używać wirtualnej sieci WAN do łączenia się z zasobami na platformie Azure za pomocą obwodu usługi ExpressRoute. Aby uzyskać więcej informacji na temat zasobów wirtualnej sieci WAN i wirtualnej sieci WAN, zobacz [omówienie wirtualnej sieci WAN](virtual-wan-about.md).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie wirtualnej sieci WAN
> * Tworzenie koncentratora i bramy
> * Łączenie sieci wirtualnej z koncentratorem
> * Podłączanie obwodu do bramy koncentratora
> * Testowanie łączności
> * Zmienianie rozmiaru bramy
> * Anonsowanie trasy domyślnej

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Masz sieć wirtualną, z którą chcesz się połączyć. Sprawdź, czy żadna z podsieci sieci lokalnych nie pokrywa się z sieciami wirtualnymi, z którymi chcesz się połączyć. Aby utworzyć sieć wirtualną w witrynie Azure portal, zobacz [przewodnik Szybki start](../virtual-network/quick-create-portal.md).

* Sieć wirtualna nie ma żadnych bram sieci wirtualnej. Jeśli sieć wirtualna ma bramę (sieć VPN lub usługę ExpressRoute), należy usunąć wszystkie bramy. Ta konfiguracja wymaga połączenia sieci wirtualnych z bramą koncentratora wirtualnej sieci WAN.

* Uzyskaj zakres adresów IP w regionie koncentratora. Koncentrator jest siecią wirtualną, która jest tworzona i używana przez wirtualną sieć WAN. Zakres adresów określony dla koncentratora nie może pokrywać się z żadną z istniejących sieci wirtualnych, z którymi nawiązujesz połączenie. Nie może również pokrywać się z zakresami adresów, z którymi łączysz się lokalnie. Jeśli nie znasz zakresów adresów IP znajdujących się w konfiguracji sieci lokalnej, należy koordynować z kimś, kto może podać te szczegóły dla Ciebie.

* Obwód usługi ExpressRoute musi być obwodem Premium, aby można było połączyć się z bramą koncentratora.

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Tworzenie wirtualnej sieci WAN

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

1. Przejdź do strony Wirtualna sieć WAN. W portalu kliknij pozycję **+Utwórz zasób**. Wpisz **wirtualną sieć WAN** w polu wyszukiwania i wybierz pozycję Enter.
2. Wybierz **wirtualną sieć WAN** z wyników. Na stronie Wirtualna sieć WAN kliknij pozycję **Utwórz,** aby otworzyć stronę Utwórz sieć WAN.
3. Na stronie **Tworzenie wan** na karcie **Podstawy** wypełnij następujące pola:

   ![Tworzenie sieci WAN](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Subskrypcja** — wybierz subskrypcję, która ma być używana.
   * **Grupa zasobów**— utwórz nową lub użyj istniejącej.
   * **Lokalizacja grupy zasobów** — wybierz lokalizację zasobu z listy rozwijanej. Sieć WAN to zasób globalny i nie znajduje się w konkretnym regionie. Konieczne jest jednak wybranie regionu, aby łatwiej zarządzać utworzonym zasobem sieci WAN i go lokalizować.
   * **Nazwa** — wpisz nazwę, którą chcesz wywołać w wan.
   * **Typ** - Wybierz **standard**. Nie można utworzyć bramy usługi ExpressRoute przy użyciu podstawowej jednostki SKU.
4. Po zakończeniu wypełniania pól wybierz pozycję **Przejrzyj +Utwórz**.
5. Po przejściu weryfikacji wybierz **pozycję Utwórz,** aby utworzyć wirtualną sieć WAN.

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Tworzenie wirtualnego koncentratora i bramy

Koncentrator wirtualny to sieć wirtualna, która jest tworzona i używana przez wirtualną sieć WAN. Może zawierać różne bramy, takie jak VPN i ExpressRoute. W tej sekcji utworzysz bramę usługi ExpressRoute dla centrum wirtualnego. Bramę można utworzyć podczas [tworzenia nowego koncentratora wirtualnego](#newhub)lub utworzyć bramę w [istniejącym centrum,](#existinghub) edytując ją. 

Bramy usługi ExpressRoute są aprowizowane w jednostkach o przełęcze 2 Gb/s. Jednostka skali 1 = 2 Gb/s z obsługą do 10 jednostek skali = 20 Gb/s. Pełne utworzenie wirtualnego koncentratora i bramy zajmuje około 30 minut.

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>Aby utworzyć nowe centrum wirtualne i bramę

Utwórz nowe centrum wirtualne. Po utworzeniu koncentratora zostanie naliczona opłata za koncentrator, nawet jeśli nie dołączysz żadnych witryn.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>Aby utworzyć bramę w istniejącym centrum

Bramę można również utworzyć w istniejącym centrum, edytując ją.

1. Przejdź do centrum wirtualnego, które chcesz edytować, i wybierz go.
2. Na stronie **Edytowanie centrum wirtualnego** zaznacz pole wyboru **Uwzględnij bramę usługi ExpressRoute**.
3. Wybierz **pozycję Potwierdź,** aby potwierdzić zmiany. Pełne utworzenie zasobów koncentratora i koncentratora zajmuje około 30 minut.

   ![istniejący koncentrator](./media/virtual-wan-expressroute-portal/edithub.png "edytowanie koncentratora")

### <a name="to-view-a-gateway"></a>Aby wyświetlić bramę

Po utworzeniu bramy usługi ExpressRoute można wyświetlić szczegóły bramy. Przejdź do koncentratora, wybierz pozycję **ExpressRoute**i wyświetl bramę.

![Wyświetl bramę](./media/virtual-wan-expressroute-portal/viewgw.png "brama widoku")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>Podłączanie sieci wirtualnej do koncentratora

W tej sekcji utworzysz połączenie komunikacji równorzędnej między koncentratorem a siecią wirtualną. Powtórz te czynności dla każdej sieci wirtualnej, z którą chcesz się połączyć.

1. Na stronie usługi Virtual WAN kliknij pozycję **Połączenie sieci wirtualnej**.
2. Na stronie połączenia sieci wirtualnej kliknij polecenie **+ Dodaj połączenie**.
3. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć już istniejącej bramy sieci wirtualnej (ani sieci VPN, ani usługi ExpressRoute).

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>Podłączanie obwodu do bramy koncentratora

Po utworzeniu bramy można podłączyć do niej [obwód usługi ExpressRoute.](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) Obwody usługi ExpressRoute Premium, które znajdują się w lokalizacjach obsługiwanych przez globalny zasięg usługi ExpressRoute, mogą łączyć się z bramą wirtualnej usługi WAN ExpressRoute.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Aby podłączyć obwód do bramy koncentratora

W portalu przejdź do **virtual hub -> Connectivity -> ExpressRoute** strony. Jeśli masz dostęp w ramach subskrypcji do obwodu usługi ExpressRoute, zobaczysz obwód, którego chcesz użyć, na liście obwodów. Jeśli nie widzisz żadnych obwodów, ale zostały dostarczone z kluczem autoryzacji i obwodu równorzędnego URI, można wykupić i podłączyć obwód. Zobacz [Aby połączyć się, realizując klucz autoryzacji](#authkey).

1. Wybierz obwód.
2. Wybierz **opcję Połącz obwody .**

   ![obwody łączące](./media/virtual-wan-expressroute-portal/cktconnect.png "obwody łączące")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>Aby połączyć się, realizując klucz autoryzacji

Użyj klucza autoryzacji i obwodu URI, które zostały dostarczone w celu połączenia.

1. Na stronie Usługi ExpressRoute kliknij pozycję **+Zrealizuj klucz autoryzacji**

   ![Zrealizuj](./media/virtual-wan-expressroute-portal/redeem.png "Zrealizuj")
2. Na stronie Przycisk zrealizuj autoryzację wypełnij wartości.

   ![realizowanie wartości kluczy](./media/virtual-wan-expressroute-portal/redeemkey2.png "realizowanie wartości kluczy")
3. Wybierz **pozycję Dodaj,** aby dodać klucz.
4. Zobacz obwód. Wykupiony obwód pokazuje tylko nazwę (bez typu, dostawcy i innych informacji), ponieważ jest w innej subskrypcji niż użytkownik.

## <a name="to-test-connectivity"></a>Aby przetestować łączność

Po nawiązaniu połączenia obwodu stan połączenia koncentratora wskazuje "ten koncentrator", co oznacza, że połączenie zostanie nawiązane z bramą usługi ExpressRoute. Zaczekaj około 5 minut, zanim przetestujesz łączność z klientem za obwodem usługi ExpressRoute, na przykład maszyną wirtualną w sieci wirtualnej, która została utworzona wcześniej.

Jeśli masz witryny połączone z wirtualną bramą sieci VPN w sieci VPN w tym samym centrum co brama usługi ExpressRoute, możesz mieć dwukierunkową łączność między punktami końcowymi sieci VPN i ExpressRoute. Routing dynamiczny (BGP) jest obsługiwany. Asn bram w centrum jest stała i nie można edytować w tej chwili.

## <a name="to-change-the-size-of-a-gateway"></a>Aby zmienić rozmiar bramy

Jeśli chcesz zmienić rozmiar bramy usługi ExpressRoute, zlokalizuj bramę usługi ExpressRoute wewnątrz koncentratora i wybierz jednostki skalowania z listy rozwijanej. Zapisz swoją zmianę. Aktualizacja bramy koncentratora zajmie około 30 minut.

![zmienianie rozmiaru bramy](./media/virtual-wan-expressroute-portal/changescale.png "zmienianie rozmiaru bramy")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Aby anonsować domyślną trasę 0.0.0.0/0 do punktów końcowych

Jeśli chcesz, aby centrum wirtualne platformy Azure anonsować domyślną trasę 0.0.0.0/0 do punktów końcowych usługi ExpressRoute, należy włączyć "Propagować trasę domyślną".

1. Wybierz **połączenie Obwód ->...-> Edytuj połączenie**.

   ![Edytowanie połączenia](./media/virtual-wan-expressroute-portal/defaultroute1.png "Edytowanie połączenia")

2. Wybierz **włącz,** aby propagować trasę domyślną.

   ![Propagowanie trasy domyślnej](./media/virtual-wan-expressroute-portal/defaultroute2.png "Propagowanie trasy domyślnej")

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
