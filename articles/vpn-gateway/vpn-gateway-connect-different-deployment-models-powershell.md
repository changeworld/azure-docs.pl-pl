---
title: 'Łączenie klasycznych sieci wirtualnych z sieciami wirtualnymi usługi Azure Resource Manager: Program PowerShell'
description: Utwórz połączenie sieci VPN między klasycznymi sieciami wirtualnymi i sieciami wirtualnymi Menedżera zasobów przy użyciu bramy sieci VPN i programu PowerShell.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 1dc0eec6178420976181b05a059e9f8b4859ec2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152010"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Łączenie sieci wirtualnych z różnych modeli wdrażania za pomocą programu PowerShell

Ten artykuł ułatwia łączenie klasycznych sieci wirtualnych z sieciami wirtualnymi Menedżera zasobów, aby umożliwić komunikację z zasobami znajdującymi się w oddzielnych modelach wdrażania. Kroki opisane w tym artykule używają programu PowerShell, ale można również utworzyć tę konfigurację przy użyciu portalu Azure, wybierając artykuł z tej listy.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Powershell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Łączenie klasycznej sieci wirtualnej z siecią wirtualną Menedżera zasobów jest podobne do łączenia sieci wirtualnej z lokalną lokalizacją lokacji. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE. Można utworzyć połączenie między sieciami wirtualnymi, które znajdują się w różnych subskrypcji i w różnych regionach. Można również połączyć sieci wirtualne, które mają już połączenia z sieciami lokalnymi, o ile brama, z którą zostały skonfigurowane, jest dynamiczna lub oparta na trasie. Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi](#faq) na końcu tego artykułu. 

Jeśli nie masz jeszcze bramy sieci wirtualnej i nie chcesz jej tworzyć, warto zamiast tego rozważyć połączenie sieci wirtualnych przy użyciu komunikacji równorzędnej sieci wirtualnej. W przypadku komunikacji równorzędnej sieci wirtualnych nie jest używana brama sieci VPN. Aby uzyskać więcej informacji, zobacz temat [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).

## <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem

Poniższe kroki przebiegają przez ustawienia niezbędne do skonfigurowania bramy dynamicznej lub opartej na trasie dla każdej sieci wirtualnej i utworzenia połączenia sieci VPN między bramami. Ta konfiguracja nie obsługuje bram statycznych ani opartych na zasadach.

### <a name="prerequisites"></a><a name="pre"></a>Wymagania wstępne

* Obie sieci wirtualne zostały już utworzone. Jeśli chcesz utworzyć sieć wirtualną menedżera zasobów, zobacz [Tworzenie grupy zasobów i sieci wirtualnej](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). Aby utworzyć klasyczną sieć [wirtualną, zobacz Tworzenie klasycznej sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic).
* Zakresy adresów sieci wirtualnych nie nakładają się na siebie ani nie pokrywają się z żadnymi zakresami dla innych połączeń, z którymi mogą być połączone bramy.
* Zainstalowano najnowsze polecenia cmdlet programu PowerShell. Aby uzyskać więcej [informacji, zobacz Jak zainstalować i skonfigurować program Azure PowerShell.](/powershell/azure/overview) Upewnij się, że zainstalowano zarówno polecenia cmdlet zarządzania usługami (SM), jak i Menedżera zasobów (RM). 

### <a name="example-settings"></a><a name="exampleref"></a>Przykładowe ustawienia

Tych wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do nich, aby lepiej zrozumieć przykłady w niniejszym artykule.

**Klasyczne ustawienia sieci wirtualnej**

Nazwa sieci wirtualnej = ClassicVNet <br>
Lokalizacja = Zachodnie stany USA <br>
Przestrzenie adresowe sieci wirtualnej = 10.0.0.0/24 <br>
Podsieć-1 = 10,0,0,0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nazwa sieci lokalnej = RMVNetLocal <br>
Typ bramy = DynamiczneRouting

**Ustawienia sieci wirtualnej Menedżera zasobów**

Nazwa sieci wirtualnej = RMVNet <br>
Grupa zasobów = RG1 <br>
Przestrzenie adresów IP sieci wirtualnej = 192.168.0.0/16 <br>
Podsieć-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Lokalizacja = wschodnie stany USA <br>
Publiczna nazwa IP bramy = gwpip <br>
Brama sieci lokalnej = ClassicVNetLocal <br>
Nazwa bramy sieci wirtualnej = RMGateway <br>
Konfiguracja adresowania IP bramy = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>Sekcja 1 - Konfigurowanie klasycznej sieci wirtualnej
### <a name="1-download-your-network-configuration-file"></a>1. Pobierz plik konfiguracji sieci
1. Zaloguj się do konta platformy Azure w konsoli programu PowerShell z podwyższonym poziomem uprawnień. Następujące polecenie cmdlet monituje o poświadczenia logowania dla konta platformy Azure. Po zalogowaniu pobiera ono ustawienia konta, aby były dostępne dla programu Azure PowerShell. Klasyczne polecenia cmdlet usługi Azure PowerShell usługi Azure są używane w tej sekcji.

   ```azurepowershell
   Add-AzureAccount
   ```

   Uzyskaj subskrypcję platformy Azure.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Jeśli masz więcej niż jedną subskrypcję, wybierz tę, której chcesz użyć.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Eksportuj plik konfiguracji sieci platformy Azure, uruchamiając następujące polecenie. W razie potrzeby można zmienić lokalizację pliku, aby wyeksportować go do innej lokalizacji.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Otwórz pobrany plik xml, aby go edytować. Na przykład pliku konfiguracji sieci zobacz [Schemat konfiguracji sieci](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Sprawdź podsieć bramy
W **VirtualNetworkSites** element, dodaj podsieć bramy do sieci wirtualnej, jeśli nie został jeszcze utworzony. Podczas pracy z plikiem konfiguracji sieci podsieci bramy MUSI być nazwany "GatewaySubnet" lub platforma Azure nie może rozpoznać i używać go jako podsieci bramy.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Przykład:**

    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>

### <a name="3-add-the-local-network-site"></a>3. Dodaj witrynę sieci lokalnej
Dodana lokacja sieci lokalnej reprezentuje sieć wirtualną RM, z którą chcesz się połączyć. Dodaj **LocalNetworkSites** element do pliku, jeśli jeszcze nie istnieje. W tym momencie w konfiguracji VPNGatewayAddress może być dowolny prawidłowy publiczny adres IP, ponieważ nie utworzyliśmy jeszcze bramy dla sieci wirtualnej Menedżera zasobów. Po utworzeniu bramy zastępujemy ten zastępczy adres IP poprawnym publicznym adresem IP, który został przypisany do bramy RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Skojarz sieć wirtualną z witryną sieci lokalnej
W tej sekcji określamy lokalną lokację sieciową, z którą chcesz połączyć sieć wirtualną. W takim przypadku jest to sieci wirtualnej Menedżera zasobów, do której odwołuje się wcześniej. Upewnij się, że nazwy są zgodne. Ten krok nie tworzy bramy. Określa sieć lokalną, z którą brama będzie się łączyć.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Zapisz plik i prześlij
Zapisz plik, a następnie zaimportuj go na platformę Azure, uruchamiając następujące polecenie. Upewnij się, że ścieżka pliku jest zmieniana w razie potrzeby w twoim środowisku.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Zobaczysz podobny wynik pokazujący, że importowanie zakończyło się pomyślnie.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Tworzenie bramy

Przed uruchomieniem tego przykładu zapoznaj się z pobranym plikiem konfiguracji sieci, aby uzyskać dokładne nazwy, których oczekuje platforma Azure. Plik konfiguracji sieci zawiera wartości dla klasycznych sieci wirtualnych. Czasami nazwy klasycznych sieci wirtualnych są zmieniane w pliku konfiguracji sieci podczas tworzenia klasycznych ustawień sieci wirtualnej w witrynie Azure portal ze względu na różnice w modelach wdrażania. Na przykład jeśli użyto witryny Azure portal do utworzenia klasycznej sieci wirtualnej o nazwie "Klasyczna sieć wirtualna" i utworzono ją w grupie zasobów o nazwie "ClassicRG", nazwa zawarta w pliku konfiguracji sieci jest konwertowana na "Group ClassicRG Classic VNet". Określając nazwę sieci wirtualnej zawierającej spacje, należy użyć cudzysłowów wokół wartości.


Aby utworzyć bramę routingu dynamicznego, użyj następującego przykładu:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Stan bramy można sprawdzić przy użyciu polecenia cmdlet **Get-AzureVNetGateway.**

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>Sekcja 2 — konfigurowanie bramy sieci wirtualnej RM



Wymagania wstępne zakładają, że utworzono już sieci wirtualnej RM. W tym kroku utworzysz bramę sieci VPN dla sieci wirtualnej rm. Nie uruchamiaj tych kroków, dopóki nie po pobraniu publicznego adresu IP bramy klasycznej sieci wirtualnej. 

1. Zaloguj się do konta platformy Azure w konsoli programu PowerShell. Następujące polecenie cmdlet monituje o poświadczenia logowania dla konta platformy Azure. Po zalogowaniu się ustawienia konta są pobierane, dzięki czemu są dostępne dla programu Azure PowerShell. Opcjonalnie można użyć funkcji "Wypróbuj", aby uruchomić usługę Azure Cloud Shell w przeglądarce.

   Jeśli używasz usługi Azure Cloud Shell, pomiń następujące polecenie cmdlet:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Aby sprawdzić, czy używasz odpowiedniej subskrypcji, uruchom następujące polecenie cmdlet:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, której chcesz użyć.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Utwórz bramę sieci lokalnej. W sieci wirtualnej brama sieci lokalnej zazwyczaj odwołuje się do lokalizacji lokalnej. W takim przypadku brama sieci lokalnej odwołuje się do klasycznej sieci wirtualnej. Nadaj mu nazwę, za pomocą której platforma Azure może się do niej odwoływać, a także określ prefiks przestrzeni adresowej. Platforma Azure używa prefiksu adresu IP w celu określenia, który ruch danych należy skierować do lokalizacji lokalnej. Jeśli musisz dostosować informacje w tym miejscu później, przed utworzeniem bramy, można zmodyfikować wartości i uruchomić próbkę ponownie.
   
   **-Name** to nazwa, którą chcesz przypisać w odniesieniu do bramy sieci lokalnej.<br>
   **-AddressPrefix** to przestrzeń adresowa dla klasycznej sieci wirtualnej.<br>
   **-GatewayIpAddress** jest publicznym adresem IP bramy klasycznej sieci wirtualnej. Pamiętaj, aby zmienić poniższy przykładowy tekst "n.n.n.n", aby odzwierciedlić poprawny adres IP.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Zażądaj publicznego adresu IP, który ma zostać przydzielony do bramy sieci wirtualnej dla sieci wirtualnej Menedżera zasobów. Nie można określić adresu IP, którego chcesz użyć. Adres IP jest dynamicznie przydzielany do bramy sieci wirtualnej. Nie oznacza to jednak, że adres IP się zmienia. Zmiana adresu IP bramy sieci wirtualnej jest tylko wtedy, gdy brama jest usuwana i odtwarzana. Nie zmienia się w całej zmiany rozmiaru, resetowania lub innych wewnętrznych konserwacji / uaktualnień bramy.

   W tym kroku ustawiamy również zmienną, która jest używana w późniejszym kroku.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Sprawdź, czy sieć wirtualna ma podsieć bramy. Jeśli nie istnieje żadna podsieć bramy, dodaj jedną. Upewnij się, że podsieć bramy nosi nazwę *GatewaySubnet*.
5. Pobierz podsieć używaną dla bramy, uruchamiając następujące polecenie. W tym kroku ustawiamy również zmienną, która ma być używana w następnym kroku.
   
   **-Nazwa** to nazwa sieci wirtualnej Menedżera zasobów.<br>
   **-ResourceGroupName** jest grupą zasobów, z którą jest skojarzona sieci wirtualnej. Podsieć bramy musi już istnieć dla tej sieci wirtualnej i musi mieć nazwę *GatewaySubnet,* aby działała poprawnie.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Utwórz konfigurację adresowania IP bramy. W ramach konfiguracji bramy zostaje zdefiniowana podsieć i publiczny adres IP do użycia. Poniższy przykład umożliwia utworzenie konfiguracji bramy.

   W tym kroku parametry **-SubnetId** i **-PublicIpAddressId** muszą być przekazywane właściwości id z podsieci i obiektów adresu IP, odpowiednio. Nie można użyć prostego ciągu. Te zmienne są ustawione w kroku, aby zażądać publicznego adresu IP i kroku, aby pobrać podsieć.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Utwórz bramę sieci wirtualnej Menedżera zasobów, uruchamiając następujące polecenie. Musi `-VpnType` być *RouteBased*. Utworzenie bramy może potrwać 45 minut lub więcej.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Skopiuj publiczny adres IP po utworzeniu bramy sieci VPN. Jest on używany podczas konfigurowania ustawień sieci lokalnej dla klasycznej sieci wirtualnej. Do pobrania publicznego adresu IP można użyć następującego polecenia cmdlet. Publiczny adres IP jest wymieniony w powrocie jako *IpAddress*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>Sekcja 3 - Modyfikowanie klasycznych ustawień lokacji lokalnej sieci wirtualnej

W tej sekcji pracujesz z klasyczną siecią wirtualną. Zastępczy adres IP używany jest podczas określania ustawień lokacji lokalnej, które będą używane do łączenia się z bramą sieci wirtualnej Menedżera zasobów. Ponieważ pracujesz z klasycznej sieci wirtualnej, należy użyć programu PowerShell zainstalowany lokalnie na komputerze, a nie usługi Azure Cloud Shell TryIt.

1. Wyeksportuj plik konfiguracji sieci.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Za pomocą edytora tekstu zmodyfikuj wartość vpngatewayAddress. Zastąp zastępczy adres IP publicznym adresem IP bramy Menedżera zasobów, a następnie zapisz zmiany.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Zaimportuj zmodyfikowany plik konfiguracji sieci na platformę Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>Sekcja 4 - Tworzenie połączenia między bramami
Tworzenie połączenia między bramami wymaga programu PowerShell. Może być konieczne dodanie konta platformy Azure, aby użyć klasycznej wersji poleceń cmdlet programu PowerShell. Aby to zrobić, należy użyć **add-AzureAccount**.

1. W konsoli programu PowerShell ustaw klucz udostępniony. Przed uruchomieniem poleceń cmdlet należy zapoznać się z pobranym plikiem konfiguracji sieci, aby uzyskać dokładne nazwy, których oczekuje platforma Azure. Określając nazwę sieci wirtualnej zawierającej spacje, należy użyć pojedynczych cudzysłowów wokół wartości.<br><br>W poniższym przykładzie **-VNetName** jest nazwą klasycznej sieci wirtualnej i **-LocalNetworkSiteName** jest nazwą określoną dla lokacji sieci lokalnej. **-SharedKey** jest wartością, którą generujesz i określasz. W przykładzie użyliśmy "abc123", ale można wygenerować i użyć czegoś bardziej złożonego. Ważne jest to, że wartość, którą określisz w tym miejscu, musi być tą samą wartością, którą określisz w następnym kroku podczas tworzenia połączenia. Zwrot powinien być pokaźny **Status: Udany**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Utwórz połączenie sieci VPN, uruchamiając następujące polecenia:
   
   Ustaw zmienne.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Utwórz połączenie. Należy zauważyć, że **-ConnectionType** jest IPsec, a nie Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>Sekcja 5 - Sprawdź swoje połączenia

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Aby zweryfikować połączenie z klasycznej sieci wirtualnej z siecią wirtualną Menedżera zasobów

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Portal Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Aby zweryfikować połączenie z sieci wirtualnej Menedżera zasobów z klasyczną siecią wirtualną

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Portal Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
