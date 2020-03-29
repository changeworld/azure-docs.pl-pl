---
title: Łączenie bramy sieci wirtualnej z wirtualną siecią WAN platformy Azure
description: Ten artykuł ułatwia łączenie bramy sieci wirtualnej platformy Azure z bramą sieci VPN wirtualnej sieci WAN platformy Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: 688183bc07aa14d5e5df182d7de0897cec93f0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066229"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Łączenie bramy sieci VPN (bramy sieci wirtualnej) z wirtualną siecią WAN

Ten artykuł ułatwia konfigurowanie łączności z bramy sieci VPN platformy Azure (bramy sieci wirtualnej) do wirtualnej sieci WAN platformy Azure (bramy sieci VPN). Tworzenie połączenia z bramy sieci VPN (bramy sieci wirtualnej) do wirtualnej bramy sieci WAN (BRAMY SIECI VPN) jest podobne do konfigurowania łączności z wirtualną siecią WAN z lokacji sieci VPN w oddziale.

Aby zminimalizować możliwe zamieszanie między dwiema funkcjami, poprzemy bramę nazwą funkcji, do których się odnosimy. Na przykład brama sieci wirtualnej bramy sieci VPN i wirtualna brama sieci VPN WAN.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem należy utworzyć następujące zasoby:

Wirtualna sieć WAN platformy Azure

* [Utwórz wirtualną sieć WAN](virtual-wan-site-to-site-portal.md#openvwan).
* [Tworzenie koncentratora](virtual-wan-site-to-site-portal.md#hub). Koncentrator wirtualny zawiera bramę sieci VPN wirtualnej sieci WAN.

Azure Virtual Network

* Utwórz sieć wirtualną bez żadnych bram sieci wirtualnej. Sprawdź, czy żadna z podsieci sieci lokalnych nie pokrywa się z sieciami wirtualnymi, z którymi chcesz się połączyć. Aby utworzyć sieć wirtualną w witrynie Azure portal, zobacz [przewodnik Szybki start](../virtual-network/quick-create-portal.md).

## <a name="1-create-an-azure-virtual-network-gateway"></a><a name="vnetgw"></a>1. Tworzenie bramy sieci wirtualnej platformy Azure

Utwórz bramę sieci wirtualnej bramy sieci VPN dla sieci wirtualnej w trybie aktywnym i aktywnym dla sieci wirtualnej. Podczas tworzenia bramy można użyć istniejących publicznych adresów IP dla dwóch wystąpień bramy lub utworzyć nowe publiczne adresy IP. Te publiczne adresy IP są używane podczas konfigurowania witryn wirtualnej sieci WAN. Aby uzyskać więcej informacji o trybie aktywnym i aktywnym, zobacz [Konfigurowanie połączeń aktywnych i aktywnych](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Ustawienie trybu aktywny-aktywny

![aktywny-aktywny](./media/connect-virtual-network-gateway-vwan/active.png "aktywne/aktywne")

### <a name="bgp-setting"></a><a name="BGP"></a>Ustawienie BGP

BGP ASN nie może być 65515. 66515 będzie używany przez wirtualną sieć WAN platformy Azure.

![BGP](./media/connect-virtual-network-gateway-vwan/bgp.png "Bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>Publiczne adresy IP

Po utworzeniu bramy przejdź do strony **Właściwości.** Właściwości i ustawienia konfiguracji będą podobne do poniższego przykładu. Zwróć uwagę na dwa publiczne adresy IP, które są używane dla bramy.

![Właściwości](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. Tworzenie wirtualnych witryn SIECI VPN w sieci WAN

Aby utworzyć wirtualne witryny SIECI VPN w sieci WAN, przejdź do wirtualnej sieci WAN, a w obszarze **Łączność**wybierz **witrynę VPN**. W tej sekcji utworzysz dwie wirtualne witryny sieci VPN sieci WAN, które odpowiadają bramom sieci wirtualnej utworzonym w poprzedniej sekcji.

1. Wybierz **+Utwórz witrynę**.
2. Na stronie **Tworzenie witryn sieci VPN** wpisz następujące wartości:

   * **Region** — (ten sam region co brama sieci wirtualnej bramy sieciowej usługi Azure VPN Gateway)
   * **Dostawca urządzenia** — wprowadź dostawcę urządzenia (dowolną nazwę)
   * **Prywatna przestrzeń adresowa** - (Wprowadź wartość lub pozostaw puste, gdy BGP jest włączone)
   * **Protokół Border Gateway Protocol** — (Ustaw, aby **włączyć,** jeśli brama sieci wirtualnej bramy sieciowej usługi Azure ma włączoną usługę BGP)
   * **Połącz się z koncentratorami** (Wybierz koncentrator utworzony w wymaganiach wstępnych z listy rozwijanej)
3. W obszarze **Łącza**wprowadź następujące wartości:

   * **Nazwa dostawcy** — wprowadź nazwę łącza i nazwę dostawcy (dowolną nazwę)
   * **Prędkość** - Prędkość (dowolna liczba)
   * **Adres IP** — wprowadź adres IP (taki sam jak pierwszy publiczny adres IP wyświetlany pod właściwościami bramy sieci wirtualnej (Brama sieci VPN)IP - Enter IP address (same as the first public IP address shown under the (VPN Gateway) virtual network gateway properties)
   * **Adres BGP** i **ASN** - adres BGP i ASN. Muszą one być takie same jak jeden z elementów równorzędnych adresów IP protokołu BGP i numer ASN z bramy sieci wirtualnej bramy sieci VPN skonfigurowanej w [kroku 1](#vnetgw).
4. Przejrzyj i wybierz **pozycję Potwierdź,** aby utworzyć witrynę.
5. Powtórz poprzednie kroki, aby utworzyć drugą lokację, aby dopasować je do drugiego wystąpienia bramy sieci wirtualnej bramy sieciowej bramy sieciowej sieci vpn. Zachowasz te same ustawienia, z wyjątkiem korzystania z drugiego publicznego adresu IP i drugiego równorzędnego adresu IP BGP z konfiguracji bramy sieci VPN.
6. Masz teraz dwie lokacje pomyślnie aprowizowana i można przejść do następnej sekcji, aby pobrać pliki konfiguracyjne.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. Pobierz pliki konfiguracyjne VPN

W tej sekcji należy pobrać plik konfiguracji sieci VPN dla każdej witryny utworzonej w poprzedniej sekcji.

1. U góry strony **Wirtualne witryny sieci VPN** w sieci WAN wybierz **pozycję Witryna**, a następnie wybierz **pozycję Pobierz konfigurację sieci VPN typu lokacja lokacja.** Platforma Azure tworzy plik konfiguracyjny z ustawieniami.

   ![pobierz plik konfiguracyjny](./media/connect-virtual-network-gateway-vwan/download.png "pobieranie")
2. Pobierz i otwórz plik konfiguracyjny.
3. Powtórz te kroki dla drugiej witryny. Po otwarciu obu plików konfiguracyjnych można przejść do następnej sekcji.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. Tworzenie bram sieci lokalnej

W tej sekcji utworzysz dwie bramy sieci lokalnej usługi Azure VPN Gateway. Pliki konfiguracyjne z poprzedniego kroku zawierają ustawienia konfiguracji bramy. Te ustawienia służą do tworzenia i konfigurowania bram sieci lokalnej bramy sieciowej usługi Azure VPN Gateway.

1. Utwórz bramę sieci lokalnej przy użyciu tych ustawień. Aby uzyskać informacje dotyczące tworzenia bramy sieci lokalnej bramy sieciowej bramy sieciowej, zobacz artykuł Brama sieci VPN [Tworzenie bramy sieci lokalnej](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway).

   * **Adres IP** — użyj adresu IP Instance0 wyświetlanego dla *konfiguracji bramy* z pliku konfiguracji.
   * **BGP** - Jeśli połączenie jest ponad BGP, wybierz **Konfiguruj ustawienia BGP** i wprowadź ASN "65515". Wprowadź adres IP elementu równorzędnego BGP. Użyj "Instance0 BgpPeeringAddresses" dla *konfiguracji bramy* z pliku konfiguracji.
   * **Subskrypcja, grupa zasobów i lokalizacja** są takie same jak w przypadku centrum wirtualnej sieci WAN.
2. Przejrzyj i utwórz bramę sieci lokalnej. Brama sieci lokalnej powinna wyglądać podobnie do tego przykładu.

   ![pobierz plik konfiguracyjny](./media/connect-virtual-network-gateway-vwan/lng1.png "instancja0")
3. Powtórz te kroki, aby utworzyć inną bramę sieci lokalnej, ale tym razem użyj wartości "Wystąpienie1" zamiast wartości "Wystąpienie0" z pliku konfiguracji.

   ![pobierz plik konfiguracyjny](./media/connect-virtual-network-gateway-vwan/lng2.png "instancja1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. Tworzenie połączeń

W tej sekcji utworzysz połączenie między bramami sieci lokalnej bramy sieci vpn a bramą sieci wirtualnej. Aby uzyskać instrukcje dotyczące tworzenia połączenia bramy sieci VPN, zobacz [Konfigurowanie połączenia](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection).

1. W portalu przejdź do bramy sieci wirtualnej i kliknij pozycję **Połączenia**. W górnej części strony Połączenia kliknij pozycję **+Dodaj**, aby otworzyć stronę **Dodawanie połączenia**.
2. Na stronie **Dodawanie połączenia** skonfiguruj następujące wartości połączenia:

   * **Nazwa:** nazwij połączenie.
   * **Typ połączenia:** Wybierz **pozycję Lokacja lokacja(IPSec)**
   * **Brama sieci wirtualnej:** wartość jest stała, ponieważ połączenie jest nawiązywane z tej bramy.
   * **Brama sieci lokalnej:** To połączenie połączy bramę sieci wirtualnej z bramą sieci lokalnej. Wybierz jedną z utworzonych wcześniej bram sieci lokalnej.
   * **Klucz udostępniony:** Wprowadź klucz udostępniony.
   * **Protokół IKE:** Wybierz protokół IKE.
   * **BGP:** Wybierz **pozycję Włącz BGP,** jeśli połączenie jest za przenajmowanie protokołu BGP.
3. Kliknij przycisk **OK**, aby utworzyć połączenie.
4. Połączenie będzie widoczne na stronie **Połączenia** bramy sieci wirtualnej.

   ![Połączenia](./media/connect-virtual-network-gateway-vwan/connect.png "połączenie")
5. Powtórz poprzednie kroki, aby utworzyć drugie połączenie. W przypadku drugiego połączenia wybierz inną utworzoną bramę sieci lokalnej.

## <a name="6-test-connections"></a><a name="test"></a>6. Połączenia testowe

Łączność można przetestować, tworząc dwie maszyny wirtualne, jedną z boku bramy sieci wirtualnej bramy sieci VPN i jedną w sieci wirtualnej dla wirtualnej sieci WAN, a następnie pingując dwie maszyny wirtualne.

1. Utwórz maszynę wirtualną w sieci wirtualnej (Test1-VNet) dla bramy sieci VPN platformy Azure (Test1-VNG). Nie należy tworzyć maszyny wirtualnej w gatewaysubnet.
2. Utwórz inną sieć wirtualną, aby połączyć się z wirtualną siecią WAN. Utwórz maszynę wirtualną w podsieci tej sieci wirtualnej. Ta sieć wirtualna nie może zawierać żadnych bram sieci wirtualnej. Można szybko utworzyć sieć wirtualną przy użyciu kroków programu PowerShell w artykule [połączenie lokacja-lokacja.](virtual-wan-site-to-site-portal.md#vnet) Pamiętaj, aby zmienić wartości przed uruchomieniem poleceń cmdlet.
3. Podłącz sieć wirtualną do koncentratora wirtualnej sieci WAN. Na stronie wirtualnej sieci WAN wybierz pozycję **Połączenia sieci wirtualnej**, a następnie **+Dodaj połączenie**. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć istniejącej bramy sieci wirtualnej.
4. Kliknij **przycisk OK,** aby utworzyć połączenie sieci wirtualnej.
5. Łączność jest teraz ustawiona między maszynami wirtualnymi. Powinieneś mieć możliwość pingowania jednej maszyny wirtualnej z drugiej, chyba że istnieją zapory lub inne zasady blokujące komunikację.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać instrukcje konfigurowania niestandardowych zasad protokołu IPsec, zobacz [Konfigurowanie niestandardowych zasad protokołu IPsec dla wirtualnej sieci WAN](virtual-wan-custom-ipsec-portal.md).
Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [Informacje o wirtualnej sieci WAN platformy Azure](virtual-wan-about.md) i często [zadawanych pytaniach dotyczących wirtualnej sieci WAN platformy Azure.](virtual-wan-faq.md)