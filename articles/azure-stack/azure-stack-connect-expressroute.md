---
title: Łączenie usługi Azure Stack na platformie Azure przy użyciu usługi ExpressRoute
description: Dowiedz się, jak połączyć sieci wirtualne w usłudze Azure Stack z sieciami wirtualnymi na platformie Azure przy użyciu usługi ExpressRoute.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: bd5e5a3b6fa72698f04969219b1db3cdb0bde3a5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486703"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>Łączenie usługi Azure Stack na platformie Azure przy użyciu usługi Azure ExpressRoute

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

W tym artykule opisano sposób nawiązywania połączeń z siecią wirtualną usługi Azure Stack z siecią wirtualną platformy Azure przy użyciu [Microsoft Azure ExpressRoute](/azure/expressroute/) bezpośrednie połączenie.

Można skorzystaj z tego artykułu w ramach samouczka i przykładowych używać do konfigurowania tego samego środowiska testowego. Alternatywnie możesz użyć tego artykułu jako przewodnik, który przeprowadzi Cię przez proces konfigurowania środowiska usługi ExpressRoute.

## <a name="overview-assumptions-and-prerequisites"></a>Omówienie założeń i wymagań wstępnych

Usługa Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego, dostarczone przez dostawcę połączenia. Usługa ExpressRoute jest połączenie sieci VPN za pośrednictwem publicznej sieci internet.

Aby uzyskać więcej informacji na temat usługi Azure ExpressRoute, zobacz [Omówienie usługi ExpressRoute](../expressroute/expressroute-introduction.md).

### <a name="assumptions"></a>Założenia

W tym artykule założono, że:

* Masz praktyczną wiedzę na temat platformy Azure.
* Masz podstawową wiedzę na temat usługi Azure Stack.
* Masz podstawową wiedzę na temat pracy w sieci.

### <a name="prerequisites"></a>Wymagania wstępne

Aby połączyć z usługi Azure Stack i platformą Azure przy użyciu usługi ExpressRoute, musi spełniać następujące wymagania:

* Elastycznie [obwód usługi ExpressRoute](../expressroute/expressroute-circuit-peerings.md) za pośrednictwem [dostawcy łączności](../expressroute/expressroute-locations.md).
* Subskrypcja platformy Azure, aby utworzyć obwód usługi ExpressRoute i sieci wirtualnych na platformie Azure.
* Router, który musi:
  * Obsługują połączenia sieci VPN typu lokacja-lokacja między jego interfejs sieci LAN i wielodostępnej bramy usługi Azure Stack.
  * Obsługa tworzenia wielu VRFs (wirtualny routingu i przekazywania), jeśli istnieje więcej niż jednej dzierżawy w ramach wdrożenia usługi Azure Stack.
* Router, który zawiera:
  * WAN port połączone z obwodem usługi ExpressRoute.
  * Portu LAN połączone wielodostępnej bramy usługi Azure Stack.

### <a name="expressroute-network-architecture"></a>Architektura sieci usługi ExpressRoute

Na poniższym diagramie przedstawiono usługi Azure Stack i środowisk platformy Azure po zakończeniu konfigurowania usługi ExpressRoute przy użyciu przykładów w tym artykule:

*Rysunek 1. ExpressRoute network*

![Sieć usługi ExpressRoute](media/azure-stack-connect-expressroute/Conceptual.png)

Na poniższym diagramie przedstawiono, jak wiele dzierżaw łączenie z infrastruktury Azure Stack za pośrednictwem routera usługi ExpressRoute z platformą Azure w przeglądarce Microsoft edge:

*Rysunek 2. Wielodostępne połączenia*

![Wielodostępne połączenia z usługą ExpressRoute](media/azure-stack-connect-expressroute/Architecture.png)

W przykładzie w tym artykule używa tego samego architektury wielodostępnej przedstawiona na rysunku 2 do łączenia usługi Azure Stack na platformie Azure za pomocą prywatnej komunikacji równorzędnej usługi ExpressRoute. Połączenie jest gotowe do routera usługi ExpressRoute za pomocą połączenia sieci VPN lokacja lokacja z bramą sieci wirtualnej w usłudze Azure Stack.

Kroki opisane w tym artykule opisano do tworzenia połączenia end-to-end między dwiema sieciami wirtualnymi w dwóch różnych dzierżawach w usłudze Azure Stack do odpowiednich sieci wirtualnych na platformie Azure. Konfigurowanie dwóch dzierżawców jest opcjonalna. Umożliwia także następujące czynności dla jednej dzierżawy.

## <a name="configure-azure-stack"></a>Konfigurowanie usługi Azure Stack

Aby skonfigurować środowisko usługi Azure Stack, pierwszy dzierżawy, wykonaj kroki w poniższym diagramie, jako wskazówki. Jeśli konfigurujesz więcej niż jednej dzierżawy, powtórz te kroki:

>[!NOTE]
>Te kroki pokazują, jak tworzyć zasoby za pomocą portalu usługi Azure Stack, ale można również użyć programu PowerShell.

![Konfiguracja sieci w usłudze Azure Stack](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfigurowania usługi Azure Stack, potrzebne są:

* Wdrożenie systemu Azure Stack zintegrowane lub wdrożenia usługi Azure Stack Development Kit (ASDK). Aby uzyskać informacje o wdrażaniu ASDK, zobacz [Przewodnik Szybki Start Azure Stack Development Kit wdrożenia](azure-stack-deploy-overview.md).
* Oferty w usłudze Azure Stack, który może być subskrybowana przez użytkowników. Aby uzyskać więcej informacji, zobacz [plany, oferty i subskrypcje](azure-stack-plan-offer-quota-overview.md).

### <a name="create-network-resources-in-azure-stack"></a>Tworzenie zasobów sieciowych w usłudze Azure Stack

Użyj poniższych procedur do tworzenia wymaganych zasobów sieciowych w usłudze Azure Stack dla dzierżawy.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Tworzenie sieci wirtualnej i podsieci maszyny wirtualnej

1. Zaloguj się do portalu użytkowników przy użyciu konta użytkownika (dzierżawa).

2. W portalu, wybierz **+ Utwórz zasób**.

3. W obszarze **portalu Azure Marketplace**, wybierz opcję **sieć**.

4. W obszarze **polecane**, wybierz opcję **sieć wirtualna**.

5. W obszarze **Utwórz sieć wirtualną**, wprowadź wartości podanych w poniższej tabeli w odpowiednich polach:

   |Pole  |Wartość  |
   |---------|---------|
   |Name (Nazwa)     |Tenant1VNet1         |
   |Przestrzeń adresowa     |10.1.0.0/16|
   |Nazwa podsieci     |Tenant1 Sub1|
   |Zakres adresów podsieci     |10.1.1.0/24|

6. Powinien zostać wyświetlony subskrypcja utworzona wcześniej w **subskrypcji** pola. Dla pozostałych pól:

    * W obszarze **grupy zasobów**, wybierz opcję **Utwórz nową** do tworzenia nowej grupy zasobów lub jeśli już masz, wybierz **Użyj istniejącej**.
    * Zweryfikowanie domyślnego **lokalizacji**.
    * Kliknij pozycję **Utwórz**.
    * (Opcjonalnie) Kliknij przycisk **Przypnij do pulpitu nawigacyjnego**.

#### <a name="create-the-gateway-subnet"></a>Tworzenie podsieci bramy

1. W obszarze **sieć wirtualna**, wybierz opcję **Tenant1VNet1**.
1. W obszarze **USTAWIENIA** wybierz pozycję **Podsieci**.
1. Wybierz **+ podsieć bramy** dodać podsieć bramy do sieci wirtualnej.
1. Domyślna nazwa podsieci to **GatewaySubnet**. Podsieci bramy są szczególny przypadek i muszą używać tej nazwy do poprawnego działania.
1. Upewnij się, że **zakres adresów** jest **10.1.0.0/24**.
1. Kliknij przycisk **OK** utworzyć podsieć bramy.

#### <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej

1. W aplikacji portal użytkowników usługi Azure Stack, kliknij przycisk **+ Utwórz zasób**.
1. W obszarze **portalu Azure Marketplace**, wybierz opcję **sieć**.
1. Wybierz pozycję **Brama sieci wirtualnej** z listy zasobów sieciowych.
1. W **nazwa** wprowadź **GW1**.
1. Wybierz pozycję **Sieć wirtualna**.
1. Wybierz **Tenant1VNet1** z listy rozwijanej.
1. Wybierz **publiczny adres IP**, następnie **wybierz publiczny adres IP**, a następnie kliknij przycisk **Utwórz nową**.
1. W **nazwa** wpisz **GW1-PiP**, a następnie kliknij przycisk **OK**.
1. W polu **Typ sieci VPN** powinna być domyślnie wybrana pozycja **Oparta na trasach**. Zachowaj to ustawienie.
1. Upewnij się, że wartości w polach **Subskrypcja** i **Lokalizacja** są poprawne. Kliknij pozycję **Utwórz**.

#### <a name="create-the-local-network-gateway"></a>Tworzenie bramy sieci lokalnej

Zasobu Brama sieci lokalnej Określa bramę zdalną po drugiej stronie połączenia sieci VPN. W tym przykładzie drugim końcu połączenia jest podrzędny interfejs sieci LAN routera usługi ExpressRoute. 1 dzierżawy, przedstawiona na rysunku 2 adres zdalny jest 10.60.3.255.

1. Zaloguj się do portalu użytkowników usługi Azure Stack, przy użyciu konta użytkownika, a następnie wybierz pozycję **+ Utwórz zasób**.
1. W obszarze **portalu Azure Marketplace**, wybierz opcję **sieć**.
1. Wybierz pozycję **brama sieci lokalnej** z listy zasobów.
1. W **nazwa** wpisz **ER-Router-GW**.
1. Aby uzyskać **adresu IP** pola, zobacz rysunek 2. Adres IP usługi ExpressRoute routera sieci LAN podrzędnych interfejs 1 dzierżawy jest 10.60.3.255. Wprowadź adres IP routera odpowiedniego interfejsu dla Twojego środowiska.
1. W **przestrzeni adresowej** wprowadź przestrzeń adresową sieci wirtualnych, które chcesz się połączyć na platformie Azure. Podsieci dzierżawy 1 w *na rysunku 2* są następujące:

   * 192.168.2.0/24 jest piastą platformy Azure.
   * 10.100.0.0/16 jest szprychy sieci wirtualnej na platformie Azure.

   > [!IMPORTANT]
   > W tym przykładzie przyjęto założenie, że używasz tras statycznych dla połączenia sieci VPN typu lokacja-lokacja między bramą usługi Azure Stack i routera usługi ExpressRoute.

1. Upewnij się, że Twoje **subskrypcji**, **grupy zasobów**, i **lokalizacji** są poprawne. Następnie wybierz przycisk **Utwórz**.

#### <a name="create-the-connection"></a>Tworzenie połączenia

1. W aplikacji portal użytkowników usługi Azure Stack, wybierz **+ Utwórz zasób**.
1. W obszarze **portalu Azure Marketplace**, wybierz opcję **sieć**.
1. Wybierz pozycję **Połączenie** z listy zasobów.
1. W obszarze **podstawy**, wybierz **lokacja lokacja (IPSec)** jako **typu połączenia**.
1. Wybierz **subskrypcji**, **grupy zasobów**, i **lokalizacji**. Kliknij przycisk **OK**.
1. W obszarze **ustawienia**, wybierz opcję **bramy sieci wirtualnej**, a następnie wybierz pozycję **GW1**.
1. Wybierz **bramy sieci lokalnej**, a następnie wybierz pozycję **ER routera bramy**.
1. W **nazwa połączenia** wprowadź **ConnectToAzure**.
1. W **klucz współużytkowany (PSK)** wprowadź **abc123** , a następnie wybierz **OK**.
1. W obszarze **Podsumowanie**, wybierz opcję **OK**.

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>Brama na publiczny adres IP sieci wirtualnej

Po utworzeniu bramy sieci wirtualnej można uzyskać publicznego adresu IP bramy. Zanotuj ten adres w przypadku, gdy będą potrzebne później dla danego wdrożenia. W zależności od wdrożenia, ten adres jest używany jako **wewnętrzny adres IP**.

1. W aplikacji portal użytkowników usługi Azure Stack, wybierz **wszystkie zasoby**.
1. W obszarze **wszystkie zasoby**, wybierz bramę sieci wirtualnej, która jest **GW1** w przykładzie.
1. W obszarze **bramy sieci wirtualnej**, wybierz opcję **Przegląd** z listy zasobów. Alternatywnie, można wybrać **właściwości**.
1. Adres IP, który ma zostać należy pamiętać, znajduje się w obszarze **publiczny adres IP**. Dla przykładowej konfiguracji ten adres jest 192.68.102.1.

#### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby sprawdzić ruch danych za pośrednictwem połączenia VPN, należy maszyn wirtualnych w celu wysyłania i odbierania danych w sieci wirtualnej usługi Azure Stack. Utwórz maszynę wirtualną, a następnie wdrożyć ją z podsiecią maszyny Wirtualnej dla sieci wirtualnej.

1. W aplikacji portal użytkowników usługi Azure Stack, wybierz **+ Utwórz zasób**.
1. W obszarze **portalu Azure Marketplace**, wybierz opcję **obliczenia**.
1. Z listy obrazów maszyn wirtualnych wybierz **systemu Windows Server 2016 Datacenter — wersja próbna** obrazu.

   >[!NOTE]
   >Jeśli obraz używany na potrzeby tego artykułu jest niedostępny, skontaktuj się z operatorem usługi Azure Stack, aby podać inny obraz systemu Windows Server.

1. W **Utwórz maszynę wirtualną**, wybierz opcję **podstawy**, a następnie wpisz **VM01** jako **nazwa**.
1. Wprowadź prawidłową nazwę użytkownika i hasło. Użyjesz tego konta do logowania się do maszyny Wirtualnej, po jego utworzeniu.
1. Podaj **subskrypcji**, **grupy zasobów**, a **lokalizacji**. Kliknij przycisk **OK**.
1. W obszarze **wybierz rozmiar**wybierz rozmiar maszyny wirtualnej dla tego wystąpienia, a następnie wybierz pozycję **wybierz**.
1. W obszarze **ustawienia**, upewnij się, że:

   * Sieć wirtualna jest **Tenant1VNet1**.
   * Podsieć jest ustawiona na **10.1.1.0/24**.

   Użyj ustawień domyślnych, a następnie kliknij przycisk **OK**.

1. W obszarze **Podsumowanie**, sprawdź konfigurację maszyny Wirtualnej, a następnie kliknij przycisk **OK**.

Aby dodać więcej dzierżaw, powtórz kroki, które zostały wykonane w tych sekcjach:

* [Tworzenie sieci wirtualnej i podsieci maszyny Wirtualnej](#create-the-virtual-network-and-vm-subnet)
* [Tworzenie podsieci bramy](#create-the-gateway-subnet)
* [Tworzenie bramy sieci wirtualnej](#create-the-virtual-network-gateway)
* [Utwórz bramę sieci lokalnej](#create-the-local-network-gateway)
* [Tworzenie połączenia](#create-the-connection)
* [Tworzenie maszyny wirtualnej](#create-a-virtual-machine)

Jeśli na przykład używane są 2 dzierżawy, pamiętaj, aby zmienić adresów IP, aby uniknąć nakładania się.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Konfigurowanie maszyny wirtualnej translatora adresów Sieciowych w przypadku przechodzenia przez bramy

> [!IMPORTANT]
> Ta sekcja dotyczy tylko w przypadku wdrożeń usługi Azure Stack Development Kit (ASDK). Translatora adresów Sieciowych nie jest wymagana w przypadku wdrożeń z wieloma węzłami.

Azure Stack Development Kit to autonomiczne i odizolowane od sieci, w której jest wdrożony host fizyczny. Sieć adresów VIP, który bramy są podłączone do nie jest zewnętrzna; jest ukryta za routerem translacji adresów sieciowych (NAT) wykonywania.

Router jest uruchomienie roli usługi Routing i usługi Dostęp zdalny (RRAS) maszyny wirtualnej (AzS-BGPNAT01) systemu Windows Server. Należy skonfigurować translatora adresów Sieciowych na maszynie wirtualnej AzS-BGPNAT01, aby umożliwić nawiązanie połączenia na obu końcach połączenia sieci VPN lokacja lokacja.

#### <a name="configure-the-nat"></a>Konfigurowanie translatora adresów Sieciowych

1. Zaloguj się do komputera hosta usługi Azure Stack przy użyciu konta administratora.
1. Skopiuj i Edytuj następujący skrypt programu PowerShell. Zastąp `your administrator password` o hasło administratora, a następnie uruchom skrypt w podwyższonym środowisku PowerShell ISE. Ten skrypt zwraca swoje **adres zewnętrzny BGPNAT**.

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "your administrator password" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```

1. Aby skonfigurować translatora adresów Sieciowych, skopiuj i Edytuj następujący skrypt programu PowerShell. Edytuj skrypt, aby zastąpić `External BGPNAT address` i `Internal IP address` z następujących wartości przykładowych:

   * Aby uzyskać *BGPNAT zewnętrzny adres* użyj 10.10.0.62
   * Aby uzyskać *wewnętrzny adres IP* Użyj 192.168.102.1

   Uruchom poniższy skrypt z podwyższonym poziomem uprawnień środowiska PowerShell ISE:

   ```powershell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

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
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
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

## <a name="configure-azure"></a>Konfigurowanie platformy Azure

Po skonfigurowaniu usługi Azure Stack można wdrożyć zasoby platformy Azure. Poniższa ilustracja przedstawia przykład sieci wirtualnej dzierżawcy na platformie Azure. Można użyć dowolnej nazwy i schematu adresowania dla sieci wirtualnej na platformie Azure. Jednak zakres adresów sieci wirtualnych na platformie Azure i usługi Azure Stack muszą być unikatowe i nie może nakładać:

*Rysunek 3. Sieci wirtualnych platformy Azure*

![Sieci wirtualnych platformy Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

Zasoby, które można wdrożyć na platformie Azure są podobne do zasobów, które są wdrożone w usłudze Azure Stack. Możesz wdrożyć następujące składniki:

* Sieci wirtualne i podsieci
* Podsieć bramy
* Brama sieci wirtualnej
* Połączenie
* Obwód usługi ExpressRoute

Przykład infrastruktury sieci platformy Azure jest skonfigurowane w następujący sposób:

* Standardowa usługi centra (192.168.2.0/24) i model sieci wirtualnej gwiazdy (10.100.0.0./16). Aby uzyskać więcej informacji na temat topologii sieci piasty i szprych zobacz [zaimplementować topologii sieci piasty i szprych na platformie Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
* Obciążenia są wdrażane w sieć wirtualną będącą szprychą i obwód usługi ExpressRoute jest podłączony do sieci wirtualnej serwera centralnego.
* Dwie sieci wirtualne są połączone za pomocą komunikacji równorzędnej sieci wirtualnych.

### <a name="configure-the-azure-vnets"></a>Konfigurowanie sieci wirtualnych platformy Azure

1. Zaloguj się do witryny Azure portal przy użyciu swoich poświadczeń platformy Azure.
1. Tworzenie sieci wirtualnej przy użyciu zakresu adresów 192.168.2.0/24 serwera centralnego.
1. Tworzenie przy użyciu 192.168.2.0/25 zakresu adresów podsieci, a następnie dodać podsieć bramy przy użyciu zakresu adresów 192.168.2.128/27.
1. Utwórz szprychy zakres adresów sieci wirtualnej i podsieci za pomocą 10.100.0.0/16.

Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnych na platformie Azure, zobacz [tworzenie sieci wirtualnej](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Skonfiguruj obwód usługi ExpressRoute

1. Przejrzyj wymagania wstępne usługi ExpressRoute w [ExpressRoute wymagań wstępnych i Lista kontrolna](../expressroute/expressroute-prerequisites.md).

1. Postępuj zgodnie z instrukcjami w [tworzenie i modyfikowanie obwodu ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) utworzyć obwód usługi ExpressRoute przy użyciu subskrypcji platformy Azure.

   >[!NOTE]
   >Podaj klucz usługi obwodu z usługą, aby można zdefiniować obwód usługi ExpressRoute, po ich stronie.

1. Postępuj zgodnie z instrukcjami w [Utwórz i zmodyfikować komunikację równorzędną dla obwodu usługi ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md) do skonfigurowania prywatnej komunikacji równorzędnej na obwód usługi ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej

Postępuj zgodnie z instrukcjami w [Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute za pomocą programu PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) utworzyć bramę sieci wirtualnej dla usługi ExpressRoute w sieci wirtualnej serwera centralnego.

### <a name="create-the-connection"></a>Tworzenie połączenia

Aby połączyć z obwodem usługi ExpressRoute z piastą, wykonaj kroki opisane w [łączenie sieci wirtualnej z obwodem usługi ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Łączenie sieci wirtualnych przy użyciu komunikacji równorzędnej

Komunikacja równorzędna koncentratora i przy użyciu kroków w sieci wirtualne będące szprychami [tworzenia wirtualnej sieci równorzędnej przy użyciu witryny Azure portal](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). Podczas konfigurowania komunikacji równorzędnej sieci wirtualnej, upewnij się, że można użyć następujących opcji:

* Z koncentratora do szprychy **Zezwalaj na tranzyt bramy**.
* Ze szprychy do koncentratora **Użyj bramy zdalnej**.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Wdróż obciążenie maszyn wirtualnych na sieć wirtualną będącą szprychą.

Powtórz te czynności dla dowolnej dzierżawy dodatkowe sieci wirtualne, którą chcesz połączyć na platformie Azure za pośrednictwem ich odpowiednich obwodów usługi ExpressRoute.

## <a name="configure-the-router"></a>Konfigurowanie routera

Na poniższym diagramie konfiguracji routera usługi ExpressRoute można użyć jako wskazówki dotyczące konfigurowania routera usługi ExpressRoute. Ten diagram przedstawia dwóch dzierżawców (1 dzierżawy i dzierżawy 2) z ich odpowiednimi obwodów usługi ExpressRoute. Każda dzierżawa jest połączona własne VRF (wirtualny routingu i przekazywania) w sieci LAN i WAN stronie routera usługi ExpressRoute. Ta konfiguracja gwarantuje izolację end-to-end w dwóch dzierżawców. Zwróć uwagę na adresy IP używane w interfejsach routera, postępując zgodnie ze wskazówkami w przykładzie konfiguracji.

*Rysunek 4. Konfiguracja routera usługi ExpressRoute*

![Konfiguracja routera usługi ExpressRoute](media/azure-stack-connect-expressroute/EndToEnd.png)

Możesz użyć dowolnego routera, który obsługuje protokół IKEv2 sieci VPN i protokołu BGP, aby zakończyć połączenie sieci VPN lokacja lokacja, z usługi Azure Stack. Tym samym router jest używany do połączenia z platformą Azure przy użyciu obwodu usługi ExpressRoute.

W poniższym przykładzie konfiguracji routera usługi agregacji serii Cisco ASR 1000 obsługuje infrastrukturę sieci, objętego *Konfiguracja routera ExpressRoute* diagramu.

```shell
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
 description PRIMARY ExpressRoute Link to AZURE over Equinix
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
 description BACKUP ExpressRoute Link to AZURE over Equinix
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

Po ustanowieniu połączenia lokacja lokacja i obwód usługi ExpressRoute, należy przetestować połączenie.

Wykonaj poniższe testów ping:

* Zaloguj się do jednej z maszyn wirtualnych w sieci wirtualnej platformy Azure i wykonać polecenie ping do maszyny wirtualnej utworzonej w usłudze Azure Stack.
* Zaloguj się do jednej z maszyn wirtualnych, który został utworzony w usłudze Azure Stack i ping maszyny wirtualnej utworzonej w sieci wirtualnej platformy Azure.

>[!NOTE]
>Aby upewnić się, że ruch jest wysyłana za pośrednictwem lokacja-lokacja i połączeń usługi ExpressRoute, należy wysłać polecenie ping dedykowany adres IP (DIP) maszyny wirtualnej na końcach zarówno, a nie adres VIP maszyny wirtualnej.

### <a name="allow-icmp-in-through-the-firewall"></a>Zezwalaj na protokół ICMP w przez zaporę

Domyślnie system Windows Server 2016 nie zezwala na pakiety przychodzące protokołu ICMP przez zaporę. Dla każdej maszyny wirtualnej, którego używasz w przypadku testów ping muszą zezwalać na przychodzące pakiety protokołu ICMP. Aby utworzyć regułę zapory dla protokołu ICMP, uruchom następujące polecenie cmdlet w oknie programu PowerShell z podwyższonym poziomem uprawnień:

```powershell
# Create ICMP firewall rule.
New-NetFirewallRule `
  –DisplayName “Allow ICMPv4-In” `
  –Protocol ICMPv4
```

### <a name="ping-the-azure-stack-virtual-machine"></a>Polecenie ping maszyny wirtualnej usługi Azure Stack

1. Zaloguj się do aplikacji portal użytkowników usługi Azure Stack przy użyciu konta dzierżawy.

1. Znajdź maszynę wirtualną, która została utworzona, a następnie wybierz maszynę wirtualną.

1. Wybierz przycisk **Połącz**.

1. Windows lub programu PowerShell wierszu polecenia z podwyższonym, wprowadź **ipconfig/all**. Zanotuj adres IPv4, zwracany w danych wyjściowych.

1. Polecenie ping do adresu IPv4 z maszyny wirtualnej w sieci wirtualnej platformy Azure.

   W środowisku przykładowym adres IPv4 jest z podsieci 10.1.1.x/24. W danym środowisku adres mogą się różnić, ale powinien znajdować się w podsieci, w której został utworzony dla dzierżawy podsieci sieci wirtualnej.

### <a name="view-data-transfer-statistics"></a>Wyświetlanie statystyk transferu danych

Jeśli chcesz wiedzieć, ile ruch przechodzi przez połączenie, należy te informacje można znaleźć w portalu użytkowników usługi Azure Stack. Jest to również dobry sposób, aby dowiedzieć się, czy dane testu ping nawiązaniem połączenia sieci VPN i ExpressRoute:

1. Zaloguj się do portalu użytkowników usługi Azure Stack przy użyciu konta dzierżawy, a następnie wybierz pozycję **wszystkie zasoby**.
1. Przejdź do grupy zasobów dla bramy sieci VPN i wybierz **połączenia** typ obiektu.
1. Wybierz **ConnectToAzure** połączenia z listy.
1. W obszarze **połączeń** > **Przegląd**, będą wyświetlane statystyki dla **dane w** i **dane wyjściowe**. Powinien zostać wyświetlony niektóre wartości różna od zera.

   ![Dane i danych wyjściowych](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie aplikacji na platformie Azure i usługi Azure Stack](azure-stack-solution-pipeline.md)
