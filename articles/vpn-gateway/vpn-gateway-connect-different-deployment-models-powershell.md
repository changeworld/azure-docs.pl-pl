---
title: 'Łączenie klasycznych sieci wirtualnych do sieci wirtualnych Menedżera zasobów platformy Azure: Program PowerShell | Dokumentacja firmy Microsoft'
description: Utwórz połączenie sieci VPN między klasycznych sieci wirtualnych i sieci wirtualnych Menedżera zasobów przy użyciu programu PowerShell i bramy sieci VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 2263996b84b17f7de9826c07eb28e4b7668cd915
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095592"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Łączenie sieci wirtualnych z różnych modeli wdrażania za pomocą programu PowerShell

Ten artykuł ułatwia łączenie klasycznych sieci wirtualnych usługi Resource Manager umożliwia zasobów znajdujących się w oddzielnych modelach wdrażania do komunikowania się ze sobą. Kroki opisane w tym artykule przy użyciu programu PowerShell, ale można również utworzyć tę konfigurację przy użyciu witryny Azure portal, wybierając w artykule z tej listy.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Program PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Łączenie klasycznej sieci wirtualnej z siecią wirtualną usługi Resource Manager jest podobny do procesu łączenia sieci z lokacją lokalną. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE. Można utworzyć połączenia między sieciami wirtualnymi, które są w różnych subskrypcjach i w różnych regionach. Można też połączyć sieci wirtualnych, które mają już połączenia z sieciami lokalnymi, tak długo, jak bramy, które zostały skonfigurowane jest dynamiczny lub oparte na trasach. Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi](#faq) na końcu tego artykułu. 

Jeśli nie masz jeszcze bramy sieci wirtualnej, a nie chcesz utworzyć, warto zamiast tego warto rozważyć połączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych. W przypadku komunikacji równorzędnej sieci wirtualnych nie jest używana brama sieci VPN. Aby uzyskać więcej informacji, zobacz temat [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).

## <a name="before"></a>Przed rozpoczęciem

W poniższych krokach objaśniono za pomocą ustawień koniecznych do skonfigurowania bramy dynamicznej lub oparte na trasach w każdej sieci wirtualnej i tworzenie połączenia sieci VPN między bramami. Ta konfiguracja nie obsługuje bramy statyczne lub oparte na zasadach.

### <a name="pre"></a>Wymagania wstępne

* Obie sieci wirtualne zostały już utworzone. Jeśli potrzebujesz utworzyć sieć wirtualną Menedżera zasobów, zobacz [Utwórz grupę zasobów i sieć wirtualna](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). Aby utworzyć klasyczną sieć wirtualną, zobacz [utworzyć klasyczną sieć wirtualną](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic).
* Zakresy adresów dla sieci wirtualnych nie pokrywają się ze sobą, ani nie pokrywa się z żadnym z zakresów dla innych połączeń, które bramy mogą być połączone z.
* Zainstalowano najnowsze polecenia cmdlet programu PowerShell. Zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview) Aby uzyskać więcej informacji. Upewnij się, że należy zainstalować zarówno Service Management (SM), jak i poleceń cmdlet Menedżera zasobów (RM). 

### <a name="exampleref"></a>Przykładowe ustawienia

Tych wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do nich, aby lepiej zrozumieć przykłady w niniejszym artykule.

**Klasyczne ustawienia sieci wirtualnej**

Nazwa sieci wirtualnej = ClassicVNet <br>
Lokalizacja = zachodnie stany USA <br>
Przestrzenie adresów sieci wirtualnej = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nazwa sieci lokalnej = RMVNetLocal <br>
GatewayType = DynamicRouting

**Ustawienia sieci wirtualnej usługi Resource Manager**

Nazwa sieci wirtualnej = RMVNet <br>
Grupa zasobów = RG1 <br>
Przestrzenie adresów IP w sieci wirtualnej = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Lokalizacja = wschodnie stany USA <br>
Publiczna nazwa adresu IP bramy = gwpip <br>
Brama sieci lokalnej = ClassicVNetLocal <br>
Nazwa bramy sieci wirtualnej = RMGateway <br>
Konfiguracji adresowania IP bramy = gwipconfig

## <a name="createsmgw"></a>Sekcja 1 — Konfigurowanie klasycznej sieci wirtualnej
### <a name="1-download-your-network-configuration-file"></a>1. Pobierz plik konfiguracji sieci
1. Zaloguj się do konta platformy Azure, w konsoli programu PowerShell z podwyższonym poziomem uprawnień. Następujące polecenie cmdlet wyświetli monit o podanie poświadczeń logowania dla konta usługi Azure. Po zalogowaniu pobiera ono ustawienia konta, aby były dostępne dla programu Azure PowerShell. Klasyczne polecenia cmdlet programu PowerShell usługi Azure Service Management (SM) są używane w tej sekcji.

   ```azurepowershell
   Add-AzureAccount
   ```

   Uzyskiwanie subskrypcji platformy Azure.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Jeśli masz więcej niż jedną subskrypcję, wybierz tę, której chcesz użyć.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Eksportuj do pliku konfiguracji sieci platformy Azure, uruchamiając następujące polecenie. Można zmienić lokalizację pliku do wyeksportowania do innej lokalizacji, w razie potrzeby.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Otwórz plik XML, który został pobrany, aby go edytować. Na przykład plik konfiguracji sieci zobacz [schemat konfiguracji sieci](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Sprawdź podsieć bramy
W **VirtualNetworkSites** elementu Dodawanie podsieci bramy sieci wirtualnej, jeśli nie została jeszcze utworzona. Podczas pracy z pliku konfiguracji sieci, podsieć bramy musi mieć nazwę "GatewaySubnet" lub platformy Azure nie może rozpoznać i używać go jako podsieć bramy.

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

### <a name="3-add-the-local-network-site"></a>3. Dodawanie lokacji sieci lokalnej
Lokacja sieci lokalnej, które możesz dodać reprezentuje siecią wirtualną Menedżera zasobów, do którego chcesz się połączyć. Dodaj **LocalNetworkSites** element do pliku, jeśli jeszcze nie istnieje. W tym momencie w konfiguracji VPNGatewayAddress może być dowolny prawidłowy publiczny adres IP, ponieważ jeszcze nie utworzyliśmy bramy dla sieci wirtualnej usługi Resource Manager. Po możemy utworzyć bramę, firma Microsoft Zastąp ten adres IP — symbol zastępczy prawidłowy publiczny adres IP przypisany do bramy Menedżera zasobów.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Skojarz sieć wirtualną z lokacją sieci lokalnej
W tej sekcji określamy, którą chcesz połączyć sieć wirtualną do lokacji sieci lokalnej. W tym przypadku jest sieci wirtualnej usługi Resource Manager, która odwołanie do wcześniej. Upewnij się, że nazwy zgodne. Ten krok nie powoduje utworzenia bramy. Określa brama będzie łączyć się z sieci lokalnej.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Zapisz plik i przekaż
Zapisz plik, a następnie zaimportuj go do platformy Azure, uruchamiając następujące polecenie. Upewnij się, że zmiany ścieżki pliku jako wymaganych w danym środowisku.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Zostanie wyświetlony podobny efekt, pokazujący, że importowanie zakończyło się pomyślnie.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Tworzenie bramy

Przed uruchomieniem tego przykładu, zapoznaj się z pobranego pliku konfiguracji sieci dla dokładnej nazwy, które platforma Azure oczekuje. Plik konfiguracji sieci zawiera wartości dla klasycznych sieci wirtualnych. Czasami w pliku konfiguracji sieci są zmieniane nazwy dla klasycznych sieci wirtualnych, podczas tworzenia ustawień klasycznej sieci wirtualnej w witrynie Azure portal z powodu różnic w modelach wdrażania. Na przykład jeśli używasz witryny Azure portal utworzyć klasycznej sieci wirtualnej o nazwie klasyczną siecią wirtualną i utworzony w grupie zasobów o nazwie "ClassicRG" nazwę, która znajduje się w pliku konfiguracji sieci jest konwertowany na "Grupa ClassicRG klasycznej sieci wirtualnej". Podczas określania nazwy sieci wirtualnej, która zawiera spacje, użyj znaków cudzysłowu wokół wartości.


Poniższy przykład umożliwia utworzenie bramy o dynamicznym routingu:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Możesz sprawdzić stan bramy, używając **Get-AzureVNetGateway** polecenia cmdlet.

## <a name="creatermgw"></a>Sekcja 2 — Konfigurowanie bramy sieci wirtualnej Menedżera zasobów

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wymagania wstępne przyjęto założenie, że już utworzono siecią wirtualną Menedżera zasobów. W tym kroku utworzysz bramy sieci VPN dla sieci wirtualnych Menedżera zasobów. Nie uruchamiaj te kroki do czasu, po pobraniu publiczny adres IP dla bramy klasyczną siecią wirtualną. 

1. Zaloguj się do konta platformy Azure, w konsoli programu PowerShell. Następujące polecenie cmdlet wyświetli monit o podanie poświadczeń logowania dla konta usługi Azure. Po zarejestrowaniu się ustawienia konta są pobierane, aby były dostępne dla programu Azure PowerShell. Opcjonalnie można użyć funkcji "Try It", można uruchomić usługi Azure Cloud Shell w przeglądarce.

   Jeśli używasz usługi Azure Cloud Shell, pomiń następujące polecenie cmdlet:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Aby zweryfikować, że używasz odpowiednią subskrypcję, uruchom następujące polecenie cmdlet:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, dla której chcesz użyć.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Utwórz bramę sieci lokalnej. W sieci wirtualnej brama sieci lokalnej zazwyczaj odwołuje się do lokalizacji lokalnej. W tym przypadku bramy sieci lokalnej odwołuje się do klasycznej sieci wirtualnej. Nadaj nazwę za pomocą którego Azure mogą odwoływać się do niego, a także określić prefiks przestrzeni adresowej. Platforma Azure używa prefiksu adresu IP w celu określenia, który ruch danych należy skierować do lokalizacji lokalnej. Jeśli musisz dostosować informacje w tym miejscu później, przed utworzeniem bramy sieci można zmodyfikować wartości i ponownie uruchom przykład.
   
   **— Nazwa** to nazwa, którą chcesz przypisać do odwoływania się do bramy sieci lokalnej.<br>
   **-AddressPrefix** jest przestrzeń adresową dla klasycznej sieci wirtualnej.<br>
   **-GatewayIpAddress** jest publiczny adres IP bramy klasyczną siecią wirtualną. Pamiętaj zmienić następujący tekst przykładowy "n.n.n.n" w celu uwzględnienia prawidłowego adresu IP.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Żądanie publicznego adresu IP do przydzielenia do bramy sieci wirtualnej dla sieci wirtualnej usługi Resource Manager. Nie można określić adresu IP, którego chcesz użyć. Adres IP jest przydzielany dynamicznie do bramy sieci wirtualnej. Nie oznacza to jednak, że adres IP się zmienia. Jedyną sytuacją zmiana adresu IP bramy sieci wirtualnej jest, gdy usunięcie bramy i utworzona ponownie. Go nie zmienia się na zmianę rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy.

   W tym kroku będziemy również ustawić zmienną, która jest używana w późniejszym kroku.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Sprawdź, czy Twoja sieć wirtualna ma podsieć bramy. Jeśli podsieć bramy, nie istnieje, należy dodać jeden. Upewnij się, że podsieć bramy ma nazwę *GatewaySubnet*.
5. Pobierz w podsieci używanej bramy, uruchamiając następujące polecenie. W tym kroku będziemy również ustawić zmienną do użycia w następnym kroku.
   
   **— Nazwa** jest nazwą sieci wirtualnej usługi Resource Manager.<br>
   **-ResourceGroupName** jest grupa zasobów, skojarzonego z siecią wirtualną. Podsieć bramy musi już istnieć dla tej sieci wirtualnej i musi mieć nazwę *GatewaySubnet* działało poprawnie.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Tworzenie konfiguracji adresowania IP bramy. W ramach konfiguracji bramy zostaje zdefiniowana podsieć i publiczny adres IP do użycia. Poniższy przykład umożliwia utworzenie konfiguracji bramy.

   W tym kroku **- SubnetId** i **- PublicIpAddressId** parametry muszą być przekazywane właściwość id z podsieci, a następnie obiekty adresu IP, odpowiednio. Nie można użyć prostego ciągu. Te zmienne są ustawiane w kroku żądanie publicznego adresu IP i ten krok, aby pobrać podsieci.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Tworzenie bramy sieci wirtualnej usługi Resource Manager, uruchamiając następujące polecenie. `-VpnType` Musi być *RouteBased*. Może potrwać 45 minut lub więcej proces tworzenia bramy.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Skopiuj publiczny adres IP, po utworzeniu bramy sieci VPN. Możesz użyć go podczas konfigurowania ustawień sieci lokalnej dla klasycznej sieci wirtualnej. Za pomocą następującego polecenia cmdlet można pobrać publicznego adresu IP. Publiczny adres IP na liście jest zwracany jako *IpAddress*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="localsite"></a>Sekcja 3 — modyfikowanie klasyczne ustawienia sieci wirtualnej w lokacji lokalnej

W tej sekcji możesz pracować z klasyczną siecią wirtualną. Można zastąpić zastępczego adresu IP, która została użyta podczas określania ustawień lokacji lokalnej, które będą używane do połączenia z bramą sieci wirtualnej usługi Resource Manager. Ponieważ praca odbywa się za pomocą klasycznej sieci wirtualnej, należy użyć programu PowerShell, zainstalowane lokalnie na komputerze, nie TryIt powłoki chmury platformy Azure.

1. Wyeksportuj plik konfiguracji sieci.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Za pomocą edytora tekstu, zmodyfikuj wartość dla VPNGatewayAddress. Zamiana zastępczego adresu IP z publicznym adresem IP bramy usługi Resource Manager, a następnie zapisz zmiany.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importuj plik konfiguracji sieci zmodyfikowane na platformie Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="connect"></a>Sekcja 4 — Tworzenie połączenia między bramami
Tworzenie połączenia między bramami wymaga programu PowerShell. Może być konieczne dodanie konta usługi Azure, aby użyć klasycznej wersji poleceń cmdlet programu PowerShell. Aby to zrobić, należy użyć **Add-AzureAccount**.

1. W konsoli programu PowerShell należy ustawić klucz udostępnionych. Przed uruchomieniem polecenia cmdlet, zapoznaj się z pobranego pliku konfiguracji sieci dla dokładnej nazwy, które platforma Azure oczekuje. Podczas określania nazwy sieci wirtualnej, która zawiera spacje, należy użyć pojedynczego cudzysłowu wokół tej wartości.<br><br>W poniższym przykładzie **- VNetName** to nazwa klasycznej sieci wirtualnej i **- LocalNetworkSiteName** jest nazwa określona dla lokacji sieci lokalnej. **- SharedKey** jest wartością, która jest generowana i określana. W tym przykładzie użyliśmy wartości "abc123", ale może generować i użyć bardziej złożonej. Ważne jest, że wartość podana w tym miejscu musi być taka sama jak wartość można określić w następnym kroku podczas tworzenia połączenia. Zwracany powinien być wyświetlony **stanu: Powodzenie**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Tworzenie połączenia sieci VPN, uruchamiając następujące polecenia:
   
   Ustaw zmienne.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Utwórz połączenie. Należy zauważyć, że **- ConnectionType** protokołu IPsec lub Vnet2Vnet nie jest.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="verify"></a>Sekcja 5 — Sprawdź połączenia

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Aby sprawdzić połączenie z klasycznej sieci wirtualnej do sieci wirtualnej usługi Resource Manager

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Aby sprawdzić połączenie z siecią wirtualną usługi Resource Manager do klasycznej sieci wirtualnej

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
