---
title: 'Łączenie komputera z siecią wirtualną przy użyciu punkt-lokacja i uwierzytelniania usługi RADIUS: PowerShell | Azure'
description: Bezpieczne łączenie klientów z systemami Windows i Mac OS X z siecią wirtualną przy użyciu uwierzytelniania P2S i usługi RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: cherylmc
ms.openlocfilehash: 1096c120b4e7731fabd574c4096e70fe02b6272d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147009"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Konfigurowanie połączenia punkt-lokacja z siecią wirtualną przy użyciu uwierzytelniania usługi RADIUS: PowerShell

W tym artykule pokazano, jak utworzyć sieć wirtualną za pomocą połączenia punkt-lokacja, która korzysta z uwierzytelniania usługi RADIUS. Ta konfiguracja jest dostępna tylko dla modelu wdrażania usługi Resource Manager.

Brama sieci VPN typu punkt-lokacja (P2S, Point-to-Site) pozwala utworzyć bezpieczne połączenie z siecią wirtualną z poziomu komputera klienckiego. Połączenia sieci VPN typu punkt-lokacja są przydatne, jeśli chcesz nawiązać połączenie z siecią wirtualną z lokalizacji zdalnej, np. gdy są użytkownika z domu lub z konferencji. Połączenie sieci VPN typu punkt-lokacja jest również przydatne zamiast połączenia sieci VPN typu lokacja-lokacja w przypadku niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną.

Połączenie sieci VPN typu punkt-lokacja jest uruchamiane z urządzeń z systemem Windows i urządzeń Mac. Przy łączeniu klientów mogą być używane następujące metody uwierzytelniania: 

* Serwer RADIUS
* Brama sieci VPN natywne Uwierzytelnianie certyfikatów

Ten artykuł ułatwia konfigurowanie konfiguracji P2S przy użyciu uwierzytelniania za pomocą serwera usługi RADIUS. Aby uwierzytelnianie za pomocą wygenerowanych certyfikatów i sieci VPN bramy natywnego uwierzytelniania certyfikatu zamiast niego, zobacz [Konfigurowanie połączenia punkt-lokacja z siecią wirtualną przy użyciu sieci VPN bramy natywnego uwierzytelniania certyfikatu](vpn-gateway-howto-point-to-site-rm-ps.md).

![Diagram połączenia - RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Połączenia typu punkt-lokacja nie wymagają urządzenia sieci VPN ani publicznego adresu IP. Połączenie typu punkt-lokacja tworzy połączenie sieci VPN nawiązywane za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol) lub IKEv2.

* Protokół SSTP to tunel sieci VPN bazujący na protokole SSL, który jest obsługiwany wyłącznie na platformach klienckich Windows. Może przechodzić przez zapory, co czyni go idealnym do nawiązywania połączenia z platformą Azure z dowolnego miejsca. Po stronie serwera obsługiwany jest protokół SSTP w wersji 1.0, 1.1 i 1.2. Klient decyduje o wyborze wersji do użycia. W przypadku systemu Windows 8.1 i nowszych protokół SSTP domyślnie używa wersji 1.2.

* Sieć VPN z protokołem IKEv2 to oparte na standardach rozwiązanie sieci VPN korzystające z protokołu IPsec. Sieci VPN z protokołem IKEv2 można używać do łączenia z urządzeniami Mac (z systemem OSX 10.11 lub nowszym).

Dla połączeń punkt-lokacja wymagane są następujące elementy:

* Brama sieci VPN oparta na trasie. 
* Serwer usługi RADIUS do obsługi uwierzytelniania użytkowników. Serwer RADIUS może być wdrożone lokalnie lub w sieci wirtualnej platformy Azure.
* Pakiet konfiguracyjny klienta VPN dla urządzeń Windows, które będą łączyć się z siecią wirtualną. Pakiet konfiguracyjny klienta VPN zawiera ustawienia wymagane do nawiązania połączenia za pośrednictwem połączenia P2S klienta sieci VPN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="aboutad"></a>Uwierzytelnianie domeny usługi Active Directory (AD) dla sieci VPN typu P2S — informacje

Uwierzytelnianie domeny AD umożliwia użytkownikom logowanie do platformy Azure, przy użyciu swoich poświadczeń domeny organizacji. Wymaga serwera usługi RADIUS, która integruje się z serwerem usługi AD. Organizacje także korzystać z ich istniejące wdrożenie usługi RADIUS.
 
Serwer RADIUS może znajdować się lokalnie lub w sieci wirtualnej platformy Azure. Podczas uwierzytelniania bramy sieci VPN działa jako komunikaty uwierzytelnianie przekazujących i przekazuje pomiędzy serwerem usługi RADIUS i łączącego się urządzenia. Jest ważne dla bramy sieci VPN można było uzyskać dostęp do serwera RADIUS. Jeśli serwer RADIUS jest znajdujących się lokalnie, a następnie wymagane jest połączenie sieci VPN lokacja-lokacja na platformie Azure do lokacji lokalnej.

Oprócz usługi Active Directory serwer usługi RADIUS, można również zintegrować z innymi systemami tożsamości zewnętrznej. Spowoduje to otwarcie mnóstwo opcji uwierzytelniania sieci VPN punkt-lokacja, włącznie z opcjami usługi MFA. Zajrzyj do dokumentacji dostawcy serwera RADIUS, aby uzyskać listę systemów tożsamości, które można zintegrować go z.

![Diagram połączenia - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Połączenie sieci VPN lokacja-lokacja może służyć do łączenia się z serwerem usługi RADIUS w środowisku lokalnym. Nie można użyć połączenia usługi ExpressRoute.
>
>

## <a name="before"></a>Przed rozpoczęciem

Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example"></a>Przykładowe wartości

Wartości przykładowych możesz użyć do tworzenia środowiska testowego lub odwoływać się do tych wartości, aby lepiej zrozumieć przykłady w niniejszym artykule. Można postępować zgodnie z opisanymi krokami i użyć przedstawionych wartości bez ich zmieniania lub zmienić je, aby odzwierciedlały dane środowisko.

* **Nazwa: VNet1**
* **Przestrzeń adresowa: 192.168.0.0/16** i **10.254.0.0/16**<br>W tym przykładzie używamy więcej niż jednej przestrzeni adresowej, aby zilustrować, że ta konfiguracja współpracuje z wieloma przestrzeniami adresowymi. Jednak ta konfiguracja nie wymaga wielu przestrzeni adresowych.
* **Nazwa podsieci: Frontonu**
  * **Zakres adresów podsieci: 192.168.1.0/24**
* **Nazwa podsieci: BackEnd**
  * **Zakres adresów podsieci: 10.254.1.0/24**
* **Nazwa podsieci: GatewaySubnet**<br>Nazwa podsieci *GatewaySubnet* jest obowiązkowa, aby brama VPN mogła działać.
  * **Zakres adresów podsieci: 192.168.200.0/24** 
* **Pula adresów klienta sieci VPN: 172.16.201.0/24**<br>Klienci sieci VPN łączący się z siecią wirtualną, którzy korzystają z tego połączenia punkt-lokacja, otrzymują adresy IP z puli adresów klientów sieci VPN.
* **Subskrypcja:** Jeśli masz więcej niż jedną subskrypcję, sprawdź, czy używane są poprawne.
* **Grupa zasobów: TestRG**
* **Lokalizacja: Wschodnie stany USA**
* **Serwer DNS: Adres IP** serwera DNS, który ma być używany do rozpoznawania nazw dla sieci wirtualnej. (opcjonalnie)
* **Nazwa GW: Vnet1GW**
* **Publiczna nazwa adresu IP: VNet1GWPIP**
* **VpnType: RouteBased**


## <a name="signin"></a>Zaloguj się i Ustawianie zmiennych

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

### <a name="declare-variables"></a>Zadeklaruj zmienne

Zadeklaruj zmienne, których chcesz użyć. Użyj poniższego przykładu, podstawiając własne wartości tam, gdzie to konieczne. Po zamknięciu sesji programu PowerShell/Cloud Shell w dowolnym momencie podczas wykonywania po prostu skopiuj i Wklej wartości ponownie, aby ponownie zadeklarować zmienne.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## 1. <a name="vnet"></a>Utwórz grupę zasobów, sieć wirtualna i publiczny adres IP adres

Poniższe kroki umożliwiają utworzenie grupy zasobów i sieć wirtualną w tej grupie zasobów, z trzema podsieciami. Podczas zastępowania wartości ważne jest, że należy zawsze nazywać podsieć bramy specjalnie "GatewaySubnet". Jeśli w przypadku nadania jej innej nazwy, tworzenia bramy zakończy się niepowodzeniem;

1. Utwórz grupę zasobów.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. Utwórz konfiguracje podsieci dla sieci wirtualnej, nadając im nazwy *FrontEnd*, *BackEnd* i *GatewaySubnet*. Prefiksy te muszą być częścią zadeklarowanej przestrzeni adresowej sieci wirtualnej.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. Utwórz sieć wirtualną.

   W tym przykładzie parametr serwera -DnsServer jest opcjonalny. Określenie wartości nie powoduje utworzenia nowego serwera DNS. Określony adres IP serwera DNS powinien być adresem serwera będącego w stanie rozpoznawać nazwy zasobów, z którymi nawiązywane jest połączenie z Twojej sieci wirtualnej. W tym przykładzie użyto prywatnego adresu IP, ale może to nie być adres IP Twojego serwera DNS. Pamiętaj, aby użyć własnych wartości. Podaną wartość jest używana przez zasoby wdrażane w sieci wirtualnej, nie za połączenia P2S.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Brama sieci VPN musi mieć publiczny adres IP. Najpierw żąda się zasobu adresu IP, a następnie odwołuje do niego podczas tworzenia bramy sieci wirtualnej. Adres IP jest dynamicznie przypisywany do zasobu podczas tworzenia bramy sieci VPN. Brama sieci VPN aktualnie obsługuje tylko *dynamiczne* przypisywanie publicznych adresów IP. Nie można zażądać przypisania statycznego publicznego adresu IP. Nie oznacza to jednak, że adres IP zmienia się po przypisaniu go do bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

   Określ zmienne, aby zażądać dynamicznie przydzielanego publicznego adresu IP.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## 2. <a name="radius"></a>Konfigurowanie serwera RADIUS

Przed utworzeniem i konfigurowanie bramy sieci wirtualnej, serwera RADIUS powinny być prawidłowo skonfigurowane do uwierzytelniania.

1. Jeśli nie masz wdrożony serwer usługi RADIUS, Wdróż jedną. Kroki wdrażania można znaleźć w podręczniku instalacji dostarczanych przez dostawcę usługi RADIUS.  
2. Konfigurowanie bramy sieci VPN, jako klienta RADIUS na promienia. Podczas dodawania tego klienta RADIUS, należy określić tę sieć GatewaySubnet, który został utworzony. 
3. Po skonfigurowaniu serwera RADIUS, Uzyskaj adres IP serwera RADIUS i wspólny klucz tajny usługi RADIUS, klienci powinni używać do komunikacji z serwerem usługi RADIUS. Jeśli serwer RADIUS znajduje się w sieci wirtualnej platformy Azure, użyj adresów IP urzędu certyfikacji z maszyną Wirtualną serwera RADIUS.

[Serwera zasad sieciowych (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) artykuł zawiera wskazówki dotyczące konfigurowania serwer Windows RADIUS (NPS) na potrzeby uwierzytelniania domeny usługi AD.

## 3. <a name="creategw"></a>Tworzenie bramy sieci VPN

Konfigurowanie i Tworzenie bramy sieci VPN dla sieci wirtualnej.

* Zmienna-GatewayType musi być "Vpn" i typ-VpnType musi być "RouteBased".
* Tworzenie bramy sieci VPN może zająć do 45 minut, w zależności od [jednostki SKU bramy](vpn-gateway-about-vpn-gateway-settings.md#gwsku) wybierzesz.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 4. <a name="addradius"></a>Dodawanie puli adresów klienta i serwera RADIUS
 
* Można określić RadiusServer — według nazwy lub adresu IP. Jeśli należy określić nazwę, a serwer znajduje się w środowisku lokalnym, następnie bramy sieci VPN nie można rozpoznać nazwy. Jeśli tak jest rzeczywiście, następnie lepiej jest określenie adresu IP serwera. 
* -RadiusSecret powinien odpowiadać, co jest skonfigurowane na serwerze usługi RADIUS.
* VpnClientAddressPool — jest to zakres, z którego klientów nawiązujących połączenie sieci VPN otrzymują adres IP. Używaj zakresu prywatnych adresów IP nienakładającego się na lokalizację lokalną, z której będziesz się łączyć, ani na sieć wirtualną, z którą chcesz się łączyć. Upewnij się, że masz pulę adresów wystarczająco duży, skonfigurowane.  

1. Utwórz bezpieczny ciąg dla promienia wpisu tajnego.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. Monit o podanie klucza tajnego usługi RADIUS. Znaki, które należy wprowadzić nie będą wyświetlane i zamiast tego zostanie zastąpione przez "*" znaków.

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Dodawanie puli adresów klienta sieci VPN i informacje o serwerze RADIUS.

   W przypadku konfiguracji z protokołu SSTP:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   W przypadku konfiguracji z protokołem IKEv2:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Dla protokołu SSTP i IKEv2

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## 5. <a name="vpnclient"></a>Pobieranie pakietu konfiguracyjnego klienta sieci VPN i skonfigurować klienta sieci VPN

Konfiguracja klienta sieci VPN umożliwia połączenie z siecią wirtualną za pośrednictwem połączenia P2S urządzeń. Aby wygenerować pakiet konfiguracyjny klienta VPN i skonfigurować klienta sieci VPN, zobacz [Tworzenie konfiguracji klienta sieci VPN dla uwierzytelniania usługi RADIUS](point-to-site-vpn-client-configuration-radius.md).

## <a name="connect"></a>6. Nawiązywanie połączenia z usługą Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Aby połączyć się z klienta sieci VPN w systemie Windows

1. Aby nawiązać połączenie z siecią wirtualną na komputerze klienckim, przejdź do połączeń sieci VPN i wyszukaj wcześniej utworzone połączenie sieci VPN. Połączenie będzie miało taką samą nazwę jak sieć wirtualna. Wprowadź swoje poświadczenia domeny, a następnie kliknij przycisk "Połącz". Pojawi się komunikat podręczny żądania z podwyższonym poziomem uprawnień. Zaakceptuj je, a następnie wprowadź poświadczenia.

   ![Łączenie klienta sieci VPN z platformą Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. Połączenie zostało ustanowione.

   ![Ustanowiono połączenie](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Nawiązywanie połączenia z klienta Mac w sieci VPN

W oknie dialogowym Sieć znajdź profil klienta, którego chcesz użyć, a następnie kliknij polecenie **Połącz**.

  ![Połączenie z komputerem Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>Aby zweryfikować połączenie

1. Aby sprawdzić, czy połączenie sieci VPN jest aktywne, otwórz wiersz polecenia z podwyższonym poziomem uprawnień, a następnie uruchom polecenie *ipconfig/all*.
2. Przejrzyj wyniki. Zwróć uwagę na fakt, że otrzymany adres IP należy do określonej podczas konfiguracji połączenia punkt-lokacja puli adresów klienta sieci VPN. Wyniki są podobne, jak w następującym przykładzie:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

Aby rozwiązać problemy połączenia P2S, zobacz [połączeń punkt lokacja Rozwiązywanie problemów z Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="connectVM"></a>Nawiązywanie połączenia z maszyną wirtualną

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>Często zadawane pytania

Często zadawane pytania dotyczą P2S przy użyciu uwierzytelniania usługi RADIUS

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [Virtual Machines](https://docs.microsoft.com/azure/) (Maszyny wirtualne). Aby dowiedzieć się więcej o sieci i maszynach wirtualnych, zobacz [Azure and Linux VM network overview](../virtual-machines/linux/azure-vm-network-overview.md) (Omówienie sieci maszyny wirtualnej z systemem Linux i platformy Azure).
