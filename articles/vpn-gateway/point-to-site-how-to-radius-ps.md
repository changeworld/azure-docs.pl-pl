---
title: 'Połącz komputer sieć wirtualną przy użyciu uwierzytelniania punkt-lokacja i RADIUS: programu PowerShell | Azure'
description: Bezpieczne łączenie klientów systemu Windows i Mac OS X z wirtualnych sieć adresu przy użyciu uwierzytelniania P2S i usługi RADIUS.
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
ms.date: 02/12/2018
ms.author: anzaman
ms.openlocfilehash: df7afe9324831ffb8e79d7320f2c716ed18a7b4f
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267353"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Skonfiguruj połączenie punkt-lokacja sieci wirtualnej przy użyciu uwierzytelniania usługi RADIUS: środowiska PowerShell

W tym artykule przedstawiono sposób tworzenia sieci wirtualnej z połączenie punkt-lokacja, które jest używane uwierzytelnianie usługi RADIUS. Ta konfiguracja jest dostępna tylko dla modelu wdrażania usługi Resource Manager.

Brama sieci VPN typu punkt-lokacja (P2S, Point-to-Site) pozwala utworzyć bezpieczne połączenie z siecią wirtualną z poziomu komputera klienckiego. Połączenia sieci VPN punkt-lokacja są przydatne do nawiązania połączenia z lokalizacji zdalnej, np. gdy są Niezale¿nie z domu lub konferencji sieci wirtualnej. Połączenie sieci VPN typu punkt-lokacja jest również przydatne zamiast połączenia sieci VPN typu lokacja-lokacja w przypadku niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną.

Połączenie sieci VPN typu punkt-lokacja jest uruchamiane z urządzeń z systemem Windows i urządzeń Mac. Przy łączeniu klientów mogą być używane następujące metody uwierzytelniania:

* Serwer RADIUS
* Uwierzytelnianie certyfikatu natywnego bramy sieci VPN

W tym artykule opisano, jak skonfigurować konfiguracji P2S z uwierzytelniania za pomocą serwera usługi RADIUS. Jeśli chcesz uwierzytelniać zamiast wygenerowane certyfikaty i uwierzytelnianie natywnego certyfikatu bramy sieci VPN, zobacz [skonfigurowano połączenia punkt-lokacja z sieci wirtualnej przy użyciu uwierzytelniania natywnego certyfikatu bramy sieci VPN](vpn-gateway-howto-point-to-site-rm-ps.md).

![Diagram połączenia — RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Połączenia typu punkt-lokacja nie wymagają urządzenia sieci VPN ani publicznego adresu IP. Połączenie typu punkt-lokacja tworzy połączenie sieci VPN nawiązywane za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol) lub IKEv2.

* Protokół SSTP to tunel sieci VPN bazujący na protokole SSL, który jest obsługiwany wyłącznie na platformach klienckich Windows. Może przechodzić przez zapory, co czyni go idealnym do nawiązywania połączenia z platformą Azure z dowolnego miejsca. Po stronie serwera obsługiwany jest protokół SSTP w wersji 1.0, 1.1 i 1.2. Klient decyduje o wyborze wersji do użycia. W przypadku systemu Windows 8.1 i nowszych protokół SSTP domyślnie używa wersji 1.2.

* Sieć VPN z protokołem IKEv2 to oparte na standardach rozwiązanie sieci VPN korzystające z protokołu IPsec. Sieci VPN z protokołem IKEv2 można używać do łączenia z urządzeniami Mac (z systemem OSX 10.11 lub nowszym).

Dla połączeń punkt-lokacja wymagane są następujące elementy:

* Brama sieci VPN oparta na trasie. 
* Serwer usługi RADIUS do obsługi uwierzytelniania użytkownika. Serwer usługi RADIUS może być wdrożone w infrastrukturze lokalnej, lub w sieci wirtualnej platformy Azure.
* Pakiet konfiguracji klienta sieci VPN dla urządzeń z systemem Windows, które będą łączyć się z siecią wirtualną. Do konfiguracji pakietu klienta VPN zawiera ustawienia wymagane dla klientów sieci VPN łączyć za pośrednictwem P2S.

## <a name="aboutad"></a>Uwierzytelnianie domeny usługi Active Directory (AD) dla sieci VPN P2S — informacje

Uwierzytelnianie domeny AD umożliwia użytkownikom logowanie do platformy Azure przy użyciu swoich poświadczeń domeny organizacji. Wymaga serwera usługi RADIUS, która integruje się z serwera usługi AD. Organizacje mogą również korzystać z ich istniejące wdrożenie usługi RADIUS.
 
Serwer usługi RADIUS może znajdować się na lokalnym lub w sieci wirtualnej platformy Azure. Podczas uwierzytelniania bramy sieci VPN działa jako przekazujących i przesyła dalej wiadomości uwierzytelniania pomiędzy serwer usługi RADIUS i łączącego się urządzenia. Jest ważne dla bramy sieci VPN można było uzyskać dostęp do serwera RADIUS. Jeśli serwer RADIUS jest znajdujących się lokalnie, wymagane jest połączenie VPN lokacja-lokacja z platformy Azure do lokacji lokalnej.

Oprócz usługi Active Directory serwer usługi RADIUS można również zintegrować z innymi systemami zarządzania tożsamościami zewnętrznych. Spowoduje to otwarcie się bardzo dużo opcji uwierzytelniania sieci VPN punkt-lokacja, włącznie z opcjami MFA. Zajrzyj do dokumentacji dostawcy serwera usługi RADIUS, aby uzyskać listę systemów tożsamości, które umożliwia integrację z.

![Diagram połączenia — RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Połączenie sieci VPN lokacja-lokacja może służyć do łączenia się z serwerem RADIUS lokalnymi. Nie można używać połączenia ExpressRoute.
>
>

## <a name="before"></a>Przed rozpoczęciem

* Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

* Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

### <a name="log-in"></a>Logowanie się

[!INCLUDE [Log in](../../includes/vpn-gateway-ps-login-include.md)]

### <a name="example"></a>Przykładowe wartości

Wartości przykładowych możesz użyć do tworzenia środowiska testowego lub odwoływać się do tych wartości, aby lepiej zrozumieć przykłady w niniejszym artykule. Można postępować zgodnie z opisanymi krokami i użyć przedstawionych wartości bez ich zmieniania lub zmienić je, aby odzwierciedlały dane środowisko.

* **Nazwa: VNet1**
* **Przestrzeń adresowa: 192.168.0.0/16** i **10.254.0.0/16**<br>W tym przykładzie używamy więcej niż jednej przestrzeni adresowej, aby zilustrować, że ta konfiguracja współpracuje z wieloma przestrzeniami adresowymi. Jednak ta konfiguracja nie wymaga wielu przestrzeni adresowych.
* **Nazwa podsieci: FrontEnd**
  * **Zakres adresów podsieci: 192.168.1.0/24**
* **Nazwa podsieci: BackEnd**
  * **Zakres adresów podsieci: 10.254.1.0/24**
* **Nazwa podsieci: GatewaySubnet**<br>Nazwa podsieci *GatewaySubnet* jest obowiązkowa, aby brama VPN mogła działać.
  * **Zakres adresów podsieci bramy: 192.168.200.0/24** 
* **Pula adresów klienta sieci VPN: 172.16.201.0/24**<br>Klienci sieci VPN łączący się z siecią wirtualną, którzy korzystają z tego połączenia punkt-lokacja, otrzymują adresy IP z puli adresów klientów sieci VPN.
* **Subskrypcja:** jeśli masz więcej niż jedną subskrypcję, sprawdź, czy korzystasz z właściwej.
* **Grupa zasobów: TestRG**
* **Lokalizacja: Wschodnie stany USA**
* **Serwera DNS: Adres IP** serwera DNS, który ma być używany do rozpoznawania nazw dla sieci wirtualnej. (opcjonalnie)
* **Nazwa bramy: Vnet1GW**
* **Nazwa publicznego adresu IP: VNet1GWPIP**
* **VpnType: RouteBased** 

## 1. <a name="vnet"></a>Tworzenie grupy zasobów, sieć wirtualna i publiczny adres IP adresu

Poniższe kroki Utwórz grupę zasobów i sieć wirtualną w grupie zasobów z trzech podsieci. Zastępowanie wartości, jest ważne, aby zawsze nazwę podsieci bramy w szczególności "GatewaySubnet". Nazwy czegoś innego, tworzenie sieci bramy nie powiodło się;

1. Utwórz grupę zasobów.

  ```powershell
  New-AzureRmResourceGroup -Name "TestRG" -Location "East US"
  ```
2. Utwórz konfiguracje podsieci dla sieci wirtualnej, nadając im nazwy *FrontEnd*, *BackEnd* i *GatewaySubnet*. Prefiksy te muszą być częścią zadeklarowanej przestrzeni adresowej sieci wirtualnej.

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
  ```
3. Utwórz sieć wirtualną.

  W tym przykładzie parametr serwera -DnsServer jest opcjonalny. Określenie wartości nie powoduje utworzenia nowego serwera DNS. Określony adres IP serwera DNS powinien być adresem serwera będącego w stanie rozpoznawać nazwy zasobów, z którymi nawiązywane jest połączenie z Twojej sieci wirtualnej. W tym przykładzie użyto prywatnego adresu IP, ale może to nie być adres IP Twojego serwera DNS. Pamiętaj, aby użyć własnych wartości. Wartość, którą określisz jest używany przez zasoby wdrażane do sieci wirtualnej, nie za połączeń P2S.

  ```powershell
  New-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
  ```
4. Brama sieci VPN musi mieć publiczny adres IP. Najpierw żąda się zasobu adresu IP, a następnie odwołuje do niego podczas tworzenia bramy sieci wirtualnej. Adres IP jest dynamicznie przypisywany do zasobu podczas tworzenia bramy sieci VPN. Brama sieci VPN aktualnie obsługuje tylko *dynamiczne* przypisywanie publicznych adresów IP. Nie można zażądać przypisania statycznego publicznego adresu IP. Nie oznacza to jednak, że adres IP zmienia się po przypisaniu go do bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

  Określ zmienne, które mają być dynamicznie przypisywanego adres publiczny adres IP żądania.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
  $pip = New-AzureRmPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
  ```

## 2. <a name="radius"></a>Konfigurowanie serwera RADIUS

Przed tworzeniem i konfigurowaniem bramy sieci wirtualnej, serwer usługi RADIUS powinny być prawidłowo skonfigurowane do uwierzytelniania.

1. Jeśli nie masz serwera RADIUS wdrożone, należy wdrożyć jeden. Kroki wdrażania można znaleźć w podręczniku instalacji dostarczanych przez dostawcę usługi RADIUS.  
2. Konfigurowanie bramy sieci VPN jako klient usługi RADIUS na PROMIEŃ. Podczas dodawania tego klienta usługi RADIUS, należy określić GatewaySubnet utworzonego w sieci wirtualnej. 
3. Po skonfigurowaniu serwera usługi RADIUS, należy uzyskać adres IP serwera usługi RADIUS i wspólny klucz tajny, która powinna być używana przez klientów usługi RADIUS do serwera RADIUS, należy skontaktować. Jeśli serwer usługi RADIUS w sieci wirtualnej Azure, użyj IP CA, maszyna wirtualna serwera usługi RADIUS.

[Serwera zasad sieciowych (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) artykuł zawiera wskazówki dotyczące konfigurowania serwer RADIUS systemu Windows (NPS) na potrzeby uwierzytelniania domeny AD.

## 3. <a name="creategw"></a>Tworzenie bramy sieci VPN

Konfigurowanie i Tworzenie bramy sieci VPN dla sieci wirtualnej.

* Elementu GatewayType — musi być "Vpn" i VpnType — musi być "RouteBased".
* Brama sieci VPN może potrwać do 45 minut, w zależności od [jednostka SKU bramy](vpn-gateway-about-vpn-gateway-settings.md#gwsku) wybrania.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 4. <a name="addradius"></a>Dodaj pulę adresów serwera i klienta RADIUS
 
* RadiusServer — można określić według nazwy lub adresu IP. Należy określić nazwę serwera znajduje się lokalnie, następnie bramy sieci VPN nie można rozpoznać nazwę. Jeśli tak jest, następnie lepiej jest określenie adresu IP serwera. 
* -RadiusSecret powinna odpowiadać co to jest skonfigurowany na serwerze usługi RADIUS.
* VpnCientAddressPool — jest zakres, z którego klientów nawiązujących połączenie sieci VPN otrzymywać adresy IP. Używaj zakresu prywatnych adresów IP nienakładającego się na lokalizację lokalną, z której będziesz się łączyć, ani na sieć wirtualną, z którą chcesz się łączyć. Upewnij się, że masz pulę adresów wystarczająco duży, skonfigurowany.  

1. Utwórz bezpieczny ciąg dla PROMIEŃ tajny.

  ```powershell
  $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
  ```

2. Monit o wprowadzenie hasła usługi RADIUS. Znaki, które należy wprowadzić nie będą wyświetlane i zamiast tego zostanie zastąpione przez "*" znaków.

  ```powershell
  RadiusSecret:***
  ```
3. Dodaj pulę adresów klienta sieci VPN i informacje o serwerze RADIUS.

  W przypadku konfiguracji SSTP:

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  W przypadku protokołu IKEv2 konfiguracji:

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  Dla protokołu SSTP i IKEv2

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## 5. <a name="vpnclient"></a>Pobierz pakiet konfiguracji klienta sieci VPN i skonfigurować klienta sieci VPN

Konfiguracja klienta VPN umożliwia nawiązywanie połączeń sieci wirtualnej za pośrednictwem połączeń P2S urządzeń. Aby wygenerować pakietu konfiguracji klienta VPN i skonfigurować klienta sieci VPN, zobacz [utworzyć konfigurację klienta sieci VPN dla uwierzytelniania RADIUS](point-to-site-vpn-client-configuration-radius.md).

## <a name="connect"></a>6. Nawiązywanie połączenia z usługą Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Aby połączyć się z klienta sieci VPN w systemie Windows

1. Aby nawiązać połączenie z siecią wirtualną na komputerze klienckim, przejdź do połączeń sieci VPN i wyszukaj wcześniej utworzone połączenie sieci VPN. Połączenie będzie miało taką samą nazwę jak sieć wirtualna. Wprowadź poświadczenia domeny, a następnie kliknij przycisk "Połącz". Zostanie wyświetlony komunikat podręczny żądania z podwyższonym poziomem uprawnień. Zaakceptuj ją, a następnie wprowadź poświadczenia.

  ![Łączenie klienta sieci VPN z platformą Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. Połączenie zostało ustanowione.

  ![Ustanowiono połączenie](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Nawiązywanie połączenia z klienta Mac sieci VPN

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

Aby rozwiązać połączeń P2S, zobacz [połączenia punkt lokacja Azure Rozwiązywanie problemów z](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="connectVM"></a>Nawiązywanie połączenia z maszyną wirtualną

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>Często zadawane pytania

Często zadawane pytania dotyczą P2S przy użyciu uwierzytelniania usługi RADIUS

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) (Maszyny wirtualne). Aby dowiedzieć się więcej o sieci i maszynach wirtualnych, zobacz [Azure and Linux VM network overview](../virtual-machines/linux/azure-vm-network-overview.md) (Omówienie sieci maszyny wirtualnej z systemem Linux i platformy Azure).
