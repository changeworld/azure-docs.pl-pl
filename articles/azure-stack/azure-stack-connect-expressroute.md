---
title: Stos Azure połączenia z platformą Azure za pomocą usługi ExpressRoute
description: Jak nawiązać sieci wirtualnych Azure stosu sieci wirtualnych na platformie Azure przy użyciu usługi ExpressRoute.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 544fc1bcc9212fd38938d58447f5050df2a08796
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29844925"
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>Stos Azure połączenia z platformą Azure za pomocą usługi ExpressRoute

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Istnieją dwie metody obsługiwanych nawiązać sieci wirtualnych Azure stosu sieci wirtualnych na platformie Azure:
   * **Site-to-Site**

     Połączenie sieci VPN za pośrednictwem protokołu IPsec (IKE v1 i IKE v2). Ten typ połączenia wymaga urządzenia VPN lub usługi RRAS. Aby uzyskać więcej informacji, zobacz [połączyć stosu Azure na platformie Azure przy użyciu sieci VPN](azure-stack-connect-vpn.md).
   * **ExpressRoute**

     Połączenie bezpośrednie na platformie Azure przez wdrożenie programu Azure stosu. Jest ExpressRoute **nie** połączenia sieci VPN za pośrednictwem publicznej sieci Internet. Aby uzyskać więcej informacji na temat usługi Azure ExpressRoute, zobacz [omówienie ExpressRoute](../expressroute/expressroute-introduction.md).

W tym artykule przedstawiono przykład przy użyciu usługi ExpressRoute z usługą Azure stosu Azure.
## <a name="requirements"></a>Wymagania
Poniżej przedstawiono wymagania nawiązać stosu Azure i przy użyciu usługi Azure:
* Subskrypcja platformy Azure można utworzyć obwodu usługi expressroute i sieci wirtualnych na platformie Azure.
* A udostępnione obwodu ExpressRoute za pośrednictwem [dostawca połączenia](../expressroute/expressroute-locations.md).
* Router, który ma obwodu ExpressRoute podłączone do jego portów sieci WAN.
* Strona sieci LAN routera jest połączony z Azure stosu wielodostępnej bramy.
* Router musi obsługiwać połączenia sieci VPN typu lokacja-lokacja między jego interfejs sieci LAN i stosu wielodostępnej bramy usługi Azure.
* Jeśli więcej niż jednej dzierżawy zostanie dodany w danym wdrożeniu Azure stosu, router musi być możliwość tworzenia wielu VRFs (wirtualny routingu i przesyłania dalej).

Na poniższym diagramie przedstawiono koncepcję sieci po zakończeniu konfigurowania:

![Diagram koncepcyjny](media/azure-stack-connect-expressroute/Conceptual.png)

**Wykres 1**

Na poniższym diagramie architektury przedstawiono, jak wiele dzierżaw z infrastruktury stosu Azure za pośrednictwem routera ExpressRoute połączenia z platformą Azure na krawędzi firmy Microsoft:

![Diagram architektury](media/azure-stack-connect-expressroute/Architecture.png)

**Diagram 2**

Przykład pokazany w tym artykule używa taką samą architekturę połączenia na platformie Azure za pomocą prywatnej komunikacji równorzędnej ExpressRoute. Została ona wysłana przy użyciu połączenia sieci VPN typu lokacja-lokacja z bramą sieci wirtualnej w stosie Azure ExpressRoute router. Poniższe kroki w tym artykule pokazano, jak utworzyć połączenie na trasie między sieciami wirtualnymi dwa z dwóch różnych dzierżawców w stosie Azure do ich odpowiednich sieci wirtualnych na platformie Azure. Można dodać wiele sieci wirtualnych dzierżawców i replikowane kroki dla każdego dzierżawcy lub w tym przykładzie umożliwiają wdrożenie tylko jednego dzierżawcy sieci wirtualnej.

## <a name="configure-azure-stack"></a>Konfigurowanie usługi Azure stosu
Teraz możesz tworzyć zasoby, które należy ustawić zapasowej środowiska Azure stosu dzierżawcy. Poniższe kroki przedstawiają, co należy zrobić. Poniższe instrukcje przedstawiają sposób tworzenie zasobów przy użyciu portalu Azure stosu, ale można również użyć środowiska PowerShell.

![Kroki zasobów sieciowych](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>Przed rozpoczęciem
Przed rozpoczęciem konfiguracji potrzebne są:
* Wdrożenia stosu Azure.

   Informacje o wdrażaniu Azure stosu Development Kit, zobacz [— Szybki Start Azure stosu Development Kit wdrożenia](azure-stack-deploy-overview.md).
* Oferta na stosie Azure, którą mogą subskrybować użytkownika.

  Aby uzyskać instrukcje, zobacz [udostępnić użytkownikom stosu Azure maszyny wirtualne](azure-stack-tutorial-tenant-vm.md).

### <a name="create-network-resources-in-azure-stack"></a>Tworzenie zasobów sieciowych w stosie Azure

Poniższe procedury umożliwiają utworzenie wymaganych zasobów sieciowych w stosie Azure dla każdego dzierżawcy:

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Tworzenie sieci wirtualnej i podsieci maszyny wirtualnej
1. Zaloguj się w aplikacji portal użytkowników przy użyciu konta użytkownika (dzierżawcy).

2. W portalu kliknij **nowy**.

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. Wybierz pozycję **Sieć** z menu portalu Marketplace.

4. Kliknij element **Sieć wirtualna** w menu.

5. Wpisz wartości w odpowiednich polach przy użyciu poniższej tabeli:

   |Pole  |Wartość  |
   |---------|---------|
   |Name (Nazwa)     |Tenant1VNet1         |
   |Przestrzeń adresowa     |10.1.0.0/16|
   |Nazwa podsieci     |Tenant1 Sub1|
   |Zakres adresów podsieci     |10.1.1.0/24|

6. W polu **Subskrypcja** powinna znajdować się subskrypcja utworzona wcześniej.

    a. Dla grupy zasobów, można utworzyć grupę zasobów lub jeśli już istnieje, wybierz **Użyj istniejącego**.

    b. Sprawdź lokalizację domyślną.

    c. Kliknij przycisk **Przypnij do pulpitu nawigacyjnego**.

    d. Kliknij przycisk **Utwórz**.



#### <a name="create-the-gateway-subnet"></a>Tworzenie podsieci bramy
1. Otwórz zasobów sieci wirtualnej (Tenant1VNet1) został utworzony z poziomu pulpitu nawigacyjnego.
2. W obszarze Ustawienia zaznacz **podsieci**.
3. Kliknij przycisk **Podsieć bramy**, aby dodać podsieć bramy do sieci wirtualnej.
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. Domyślna nazwa podsieci to **GatewaySubnet**.
   Podsieci bramy to specjalne podsieci i muszą mieć dokładnie tę nazwę, aby działać prawidłowo.
5. W **zakres adresów** pola, sprawdź adres jest **10.1.0.0/24**.
6. Kliknij przycisk **OK** można utworzyć podsieci bramy.

#### <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej
1. W portalu Azure stosu użytkownika kliknij **nowy**.
   
2. Wybierz pozycję **Sieć** z menu portalu Marketplace.
3. Wybierz pozycję **Brama sieci wirtualnej** z listy zasobów sieciowych.
4. W polu **Nazwa** wpisz wartość **GW1**.
5. Kliknij pozycję **Sieć wirtualna**, aby wybrać sieć wirtualną.
   Wybierz **Tenant1VNet1** z listy.
6. Kliknij element menu **Publiczny adres IP**. Gdy **wybierz publiczny adres IP** sekcji otwiera kliknij **Utwórz nowy**.
7. W polu **Nazwa** wpisz wartość **GW1-PiP** i kliknij przycisk **OK**.
8. W polu **Typ sieci VPN** powinna być domyślnie wybrana pozycja **Oparta na trasach**.
    Zachowaj to ustawienie.
9. Upewnij się, że wartości w polach **Subskrypcja** i **Lokalizacja** są poprawne. Jeśli chcesz można przypiąć zasobów do pulpitu nawigacyjnego. Kliknij przycisk **Utwórz**.

#### <a name="create-the-local-network-gateway"></a>Tworzenie bramy sieci lokalnej

Zasób bramy sieci lokalnej ma na celu wskazać bramy zdalnego na drugim końcu połączenia sieci VPN. Na przykład strona zdalna jest identyfikator sieci LAN routera ExpressRoute. 1 dzierżawy w tym przykładzie adres zdalny jest 10.60.3.255, jak pokazano na diagramie 2.

1. Zaloguj się do maszyny fizycznej usługi Azure Stack.
2. Zaloguj się do portalu użytkowników z określonym kontem użytkownika, a następnie kliknij przycisk **nowy**.
3. Wybierz pozycję **Sieć** z menu portalu Marketplace.
4. Wybierz pozycję **brama sieci lokalnej** z listy zasobów.
5. W **nazwa** typ pola **ER-Router-GW**.
6. Aby uzyskać **adres IP** pola, zajrzyj do 2 diagramu. Adres IP routera ExpressRoute identyfikator sieci LAN dla dzierżawy 1 jest 10.60.3.255. Dla własnego środowiska wpisz adres IP routera odpowiedniego interfejsu.
7. W **przestrzeni adresowej** wpisz przestrzeni adresowej sieci wirtualnych, który chcesz połączyć się na platformie Azure. W tym przykładzie należy odwoływać się do 2 diagramu. Dla dzierżawcy 1, należy zauważyć, że są wymagane podsieci **192.168.2.0/24** (jest to Centrum sieci wirtualnej na platformie Azure) i **10.100.0.0/16** (jest to Gwiazda sieci wirtualnej na platformie Azure). Wpisz odpowiednie podsieci dla własnego środowiska.
   > [!IMPORTANT]
   > W tym przykładzie przyjęto założenie, że używasz trasy statyczne dla połączenia sieci VPN typu lokacja-lokacja między bramą stosu Azure i ExpressRoute router.

8. Upewnij się, że Twoje **subskrypcji**, **grupy zasobów**, i **lokalizacji** są wszystkie prawidłowe, a następnie kliknij przycisk **Utwórz**.

#### <a name="create-the-connection"></a>Tworzenie połączenia
1. W portalu Azure stosu użytkownika kliknij **nowy**.
2. Wybierz pozycję **Sieć** z menu portalu Marketplace.
3. Wybierz pozycję **Połączenie** z listy zasobów.
4. W **podstawy** w sekcji Ustawienia, wybierz **lokacja lokacja (IPSec)** jako **typ połączenia**.
5. Wybierz **subskrypcji**, **grupy zasobów**, i **lokalizacji** i kliknij przycisk **OK**.
6. W **ustawienia** kliknij **Brama sieci wirtualnej** kliknij **GW1**.
7. Kliknij przycisk **bramy sieci lokalnej**i kliknij przycisk **ER Router GW**.
8. W **nazwa połączenia** wpisz **ConnectToAzure**.
9. W **klucz udostępniony (PSK)** wpisz **abc123** i kliknij przycisk **OK**.
10. Na **Podsumowanie** kliknij **OK**.

    Po utworzeniu połączenia można wyświetlić publiczny adres IP używany przez bramę sieci wirtualnej. Aby znaleźć adres w portalu Azure stosu, przejdź do Brama sieci wirtualnej. W **omówienie**, Znajdź **publicznego adresu IP**. Należy pamiętać, ten adres; zostanie użyty jako *wewnętrznego adresu IP* w następnej sekcji (jeśli ma zastosowanie do wdrożenia).

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Aby sprawdzić poprawność danych przesyłanych za pośrednictwem połączenia sieci VPN, należy maszyn wirtualnych do wysyłania i odbierania danych w sieci wirtualnej Azure stosu. Teraz Utwórz maszynę wirtualną i umieszcza je w podsieci maszyny Wirtualnej w Twojej sieci wirtualnej.

1. W portalu Azure stosu użytkownika kliknij **nowy**.
2. Wybierz pozycję **Maszyny wirtualne** z menu portalu Marketplace.
3. Na liście obrazów maszyny wirtualnej, wybierz **Eval centrum danych systemu Windows Server 2016** obrazu, a następnie kliknij przycisk **Utwórz**.
4. Na **podstawy** sekcji w **nazwa** typ pola **VM01**.
5. Wpisz prawidłową nazwę użytkownika i hasło. To konto będzie używane w celu logowania się do maszyny wirtualnej po jej utworzeniu.
6. Podaj **subskrypcji**, **grupy zasobów**, i **lokalizacji** , a następnie kliknij przycisk **OK**.
7. Na **rozmiar** sekcji, kliknij rozmiar maszyny wirtualnej dla tego wystąpienia, a następnie kliknij przycisk **wybierz**.
8. Na **ustawienia** sekcji można zaakceptować wartości domyślne. Ale upewnij się, wybrana sieć wirtualna jest **Tenant1VNet1** i podsieci ma ustawioną wartość **10.1.1.0/24**. Kliknij przycisk **OK**.
9. Sprawdź ustawienia na **Podsumowanie** sekcji, a następnie kliknij przycisk **OK**.

Dla każdego dzierżawcy sieci wirtualnej chcesz się połączyć, powtórz poprzednie kroki z **tworzenie sieci wirtualnej i podsieci maszyny Wirtualnej** za pośrednictwem **Utwórz maszynę wirtualną** sekcje.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Skonfiguruj maszynę wirtualną NAT dla bramy przechodzenie
> [!IMPORTANT]
> Ta sekcja dotyczy tylko wdrożenia usługi Azure stosu Development Kit. Translator NAT nie jest potrzebna w przypadku wdrożeń z wieloma węzłami.

Azure stosu Development Kit jest niezależna i odizolowane od sieci, na którym wdrożono hosta fizycznego. Tak bramy są podłączone do sieci adresu VIP "External" nie jest zewnętrzna, ale zamiast tego jest ukryty za routerem podczas translacji adresów sieciowych (NAT).
 
Router jest maszynę wirtualną systemu Windows Server (**AzS BGPNAT01**) roli usługi Routing i dostęp zdalny (RRAS) w infrastrukturze Azure stosu Development Kit. Należy skonfigurować translatora adresów Sieciowych w maszynie wirtualnej AzS BGPNAT01 zezwalały na połączenie VPN lokacja-lokacja na obu końcach połączenie.

#### <a name="configure-the-nat"></a>Konfigurowanie translatora adresów Sieciowych

1. Zaloguj się do komputera fizycznego stosu Azure przy użyciu konta administratora.
2. Skopiuj i edytuj poniższy skrypt programu PowerShell i uruchom w z podwyższonym poziomem uprawnień programu Windows PowerShell ISE. Zamień hasło administratora. Zwrócony adres Twojej *BGPNAT zewnętrzny adres*.

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. Aby skonfigurować translatora adresów Sieciowych, skopiuj i edytuj poniższy skrypt programu PowerShell i uruchom w z podwyższonym poziomem uprawnień programu Windows PowerShell ISE. Przeprowadź edycję pliku skryptu, aby zastąpić *BGPNAT zewnętrzny adres* i *wewnętrznego adresu IP* (zapisane wcześniej w **utworzyć połączenie** sekcji).

   Na diagramach przykład *adres zewnętrzny BGPNAT* jest 10.10.0.62 i *wewnętrznego adresu IP* jest 192.168.102.1.

   ```
   $ExtBgpNat = '<External BGPNAT address>'
   $IntBgpNat = '<Internal IP address>'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Konfigurowanie usługi Azure
Teraz, gdy konfiguracja stosu Azure została ukończona, można wdrożyć niektórych zasobów platformy Azure. Na poniższym diagramie przedstawiono przykładowe dzierżawcy sieci wirtualnej na platformie Azure. Można użyć dowolnej nazwy i schemat adresowania sieci wirtualnej na platformie Azure. Jednak zakres adresów sieci wirtualnych Azure i stosu Azure muszą być unikatowe i nie mogą się pokrywać.

![Sieci wirtualne platformy Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**Diagram 3**

Zasoby, które można wdrożyć na platformie Azure są podobne do zasoby, które są wdrożone w stosie Azure. Podobnie wdrażania:
* Sieci wirtualne i podsieci
* Podsieć bramy
* Brama sieci wirtualnej
* Połączenie
* Obwodu usługi ExpressRoute

Przykład infrastruktury sieci platformy Azure jest konfigurowana w następujący sposób:
* Standardowe (192.168.2.0/24) gwiazdy (10.100.0.0./16) modelu sieci wirtualnej jest używany.
* Obciążeń wdrożonych w gwiazdy sieć wirtualną i obwodem usługi ExpressRoute jest podłączone do koncentratora sieci wirtualnej.
* Dwie sieci wirtualne są połączone za pomocą funkcji komunikacji równorzędnej sieci wirtualnej.

### <a name="configure-vnets"></a>Konfigurowanie sieci wirtualnych
1. Zaloguj się do portalu Azure przy użyciu poświadczeń platformy Azure.
2. Tworzenie sieci wirtualnej za pomocą przestrzeni adresowej 192.168.2.0/24 koncentratora. Tworzenie przy użyciu 192.168.2.0/25 zakres adresów podsieci, a następnie dodaj przy użyciu 192.168.2.128/27 zakres adresów podsieci bramy.
3. Utwórz gwiazdy zakres adresów sieci wirtualnej i podsieci przy użyciu 10.100.0.0/16.


Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnych na platformie Azure, zobacz [utworzyć sieć wirtualną](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Skonfigurować obwód usługi ExpressRoute

1. Przeglądanie wymagań wstępnych usługi ExpressRoute w [ExpressRoute wymagania wstępne & Lista kontrolna](../expressroute/expressroute-prerequisites.md).
2. Postępuj zgodnie z instrukcjami [tworzenia i modyfikowania obwodu usługi expressroute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) utworzyć obwodu usługi ExpressRoute, przy użyciu subskrypcji platformy Azure.
3. Dostawca usług hostingowych/dostawcą, aby udostępnić obwodu usługi ExpressRoute w celu ich udostępniać klucza usługi z poprzedniego kroku.
4. Postępuj zgodnie z instrukcjami [tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md) skonfigurować prywatną komunikację równorzędną w obwodzie usługi ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej

* Postępuj zgodnie z instrukcjami [należy skonfigurować bramę sieci wirtualnej dla usługi przy użyciu programu PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) utworzyć bramę sieci wirtualnej dla usługi ExpressRoute w Centrum w sieci wirtualnej.

### <a name="create-the-connection"></a>Tworzenie połączenia

* Aby połączyć obwodu ExpressRoute Centrum sieci wirtualnej, postępuj zgodnie z instrukcjami [połączyć sieć wirtualną z obwodem usługi ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Elementu równorzędnego sieci wirtualne

* Elementu równorzędnego koncentratora i gwiazda sieci wirtualnych za pomocą kroków w [tworzenie sieci wirtualnej komunikacji równorzędnej przy użyciu portalu Azure](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). Podczas konfigurowania sieci równorzędne —, upewnij się, że możesz wybrać następujące opcje:
   * Z Centrum do gwiazdy: **Zezwalaj przesyłania bramy**
   * Z gwiazdy do koncentratora: **Użyj zdalnego bramy**

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

* Wdrożenie obciążenia maszyn wirtualnych w gwiazdy sieci wirtualnej.

Powtórz te kroki dla dowolnej dzierżawy dodatkowe sieci wirtualnych, należy nawiązać na platformie Azure za pośrednictwem ich odpowiednich obwody usługi ExpressRoute.

## <a name="configure-the-router"></a>Skonfigurować router

Na poniższym diagramie infrastruktury end-to-end służy do przeprowadzenia konfiguracji routera ExpressRoute. Ten diagram przedstawia dwa dzierżawców (dzierżawy 1 i 2 dzierżawcy) z ich odpowiednich obwody Express Route. Każdy jest połączony z własnych VRF (wirtualny routingu i przekazywania) po stronie router ExpressRoute, aby upewnić się na trasie izolacji między dzierżawcami dwóch sieci LAN i WAN. Należy pamiętać, adresy IP używane w interfejsach routera, jak wykonać Przykładowa konfiguracja.

![Końcowy do końca diagramu](media/azure-stack-connect-expressroute/EndToEnd.png)

**Diagram 4**

Można użyć dowolnego routera, który obsługuje protokół IKEv2 sieci VPN i protokołu BGP zakończenie połączenia sieci VPN typu lokacja-lokacja z stosu Azure. Router w tym samym służy do nawiązania połączenia platformy Azure przy użyciu obwodu usługi ExpressRoute. 

Oto przykładowa konfiguracja z 1000 ASR Cisco, obsługujący infrastruktury sieciowej wyświetlane na diagramie 4:

```
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2 
description IKEv2 proposal for Tenant 1 
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2 
description IKEv2 proposal for Tenant 2 
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2 
description IKEv2 Policy for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2 
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256 
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256 
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2 
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2 
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2 
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF 
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF 
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Testowanie połączenia

Przetestuj połączenie po ustanowieniu połączenia lokacja-lokacja i obwodem usługi ExpressRoute. To zadanie jest proste.  Zaloguj się do jednej z maszyn wirtualnych utworzonych w sieci wirtualnej platformy Azure i zbadaj maszyny wirtualnej utworzone w środowisku Azure stosu lub na odwrót. 

Aby zapewnić, że w przypadku wysyłania ruchu przez lokacja-lokacja i połączeń ExpressRoute, należy wysyłać polecenia ping dedykowany adres IP (DIP) na obu końcach maszyny wirtualnej, a nie adres VIP maszyny wirtualnej. Tak należy znaleźć i Zapisz adres w drugim zakończeniu połączenia.

### <a name="allow-icmp-in-through-the-firewall"></a>Zezwalaj na protokół ICMP w przez zaporę
Domyślnie system Windows Server 2016 nie zezwala na pakiety ICMP w przez zaporę. Tak dla każdej maszyny wirtualnej, używanych w teście, uruchom następujące polecenie cmdlet w oknie programu PowerShell z podwyższonym poziomem uprawnień:


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>Polecenie ping maszyny wirtualnej Azure stosu

1. Zaloguj się do portalu użytkowników stosu Azure przy użyciu konta dzierżawcy.
2. Kliknij pozycję **Maszyny wirtualne** na lewym pasku nawigacyjnym.
3. Znaleźć maszyny wirtualnej, która została wcześniej utworzona, a następnie kliknij go.
4. W sekcji dla maszyny wirtualnej, kliknij polecenie **Connect**.
5. Otwórz program PowerShell z podwyższonym poziomem uprawnień okno i wpisz **ipconfig/all**.
6. Znajdź adres IPv4 w danych wyjściowych i notatki. Polecenie ping ten adres z maszyny wirtualnej w sieci wirtualnej platformy Azure. W środowisku przykład adres znajduje się w podsieci 10.1.1.x/24. W danym środowisku adres może być inna. Należy w podsieci, w której został utworzony dla podsieci sieci wirtualnej dzierżawcy.


### <a name="view-data-transfer-statistics"></a>Wyświetlanie statystyk transferu danych

Jeśli chcesz wiedzieć, ile ruchu jest przekazanie za pośrednictwem połączenia, te informacje w sekcji połączenia można znaleźć w portalu Azure stosu użytkownika. Informacje te są również innego dobry sposób, aby sprawdzić, czy polecenie ping, które zostały wysłane faktycznie nawiązaniem połączenia sieci VPN i ExpressRoute.

1. Zaloguj się do portalu użytkownika Microsoft Azure stosu przy użyciu swojego konta dzierżawy.
2. Przejdź do grupy zasobów, w której został utworzony bramy sieci VPN i wybierz **połączeń** typu obiektu.
3. Kliknij przycisk **ConnectToAzure** połączenie na liście.
4. Na **połączenia** sekcji można zobaczyć statystyki dla **danych w** i **dla danych wychodzących**. Te statystyki powinny mieć wartości inne niż zero.

   ![Dane w danych wychodzących](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Kolejne kroki
[Wdrażanie aplikacji na platformie Azure oraz Azure stosu](azure-stack-solution-pipeline.md)
