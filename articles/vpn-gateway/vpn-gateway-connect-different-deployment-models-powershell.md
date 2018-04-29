---
title: 'Połącz klasycznych sieci wirtualnych do sieci wirtualnych Menedżera zasobów Azure: programu PowerShell | Dokumentacja firmy Microsoft'
description: Utwórz połączenie sieci VPN między klasycznych sieci wirtualnych i sieci wirtualnych Menedżera zasobów przy użyciu bramy sieci VPN i programu PowerShell.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: f17c3bf0-5cc9-4629-9928-1b72d0c9340b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2018
ms.author: cherylmc
ms.openlocfilehash: 65faf1a4f78244d9fdd03b6415bf2cadac923504
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Łączenie sieci wirtualnych z różnych modeli wdrażania za pomocą programu PowerShell

W tym artykule opisano, jak połączyć klasycznych sieci wirtualnych do sieci wirtualnych Menedżera zasobów, aby umożliwić zasobów znajdujących się w modelach wdrażania oddzielne do komunikowania się ze sobą. Kroki opisane w tym artykule przy użyciu programu PowerShell, ale można również utworzyć w tej konfiguracji przy użyciu portalu Azure, wybierając artykułu z tej listy.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Program PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Łączenie klasycznej sieci wirtualnej z Menedżera zasobów sieci wirtualnej jest podobny do łączenia sieci wirtualnej do lokalizacji lokalnej. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE. Można utworzyć połączenia między sieciami wirtualnymi, które są w różnych subskrypcji i w różnych regionach. Można również połączyć sieci wirtualnych, które mają już połączenia z sieciami lokalnymi tak długo, jak jest bramy, które zostały skonfigurowane z dynamicznego lub oparte na trasach. Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi](#faq) na końcu tego artykułu. 

Jeśli nie masz już bramy sieci wirtualnej i nie chcesz utworzyć, możesz zamiast tego należy rozważyć połączenie Twojej sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnej. W przypadku komunikacji równorzędnej sieci wirtualnych nie jest używana brama sieci VPN. Aby uzyskać więcej informacji, zobacz temat [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).

## <a name="before"></a>Przed rozpoczęciem

W poniższych krokach objaśniono za pomocą ustawienia wymagane do skonfigurowania bramy dynamiczne lub oparte na trasach dla każdej sieci wirtualnej i Utwórz połączenie sieci VPN między bramami. Ta konfiguracja nie obsługuje bramy statyczne lub oparte na zasadach.

### <a name="pre"></a>Wymagania wstępne

* Obie sieci wirtualne zostały już utworzone.
* Zakresy adresów dla sieci wirtualne nie pokrywają się ze sobą lub pokrywają się z jednym z zakresów dla innych połączeń, które może być połączone bramy.
* Zainstalowano najnowsze poleceń cmdlet programu PowerShell. Zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) Aby uzyskać więcej informacji. Upewnij się, że zostaną zainstalowane zarówno usługi zarządzania (ko), jak i poleceń cmdlet Menedżera zasobów (RM). 

### <a name="exampleref"></a>Przykładowe ustawienia

Tych wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do nich, aby lepiej zrozumieć przykłady w niniejszym artykule.

**Klasycznych ustawień sieci wirtualnej**

Nazwa sieci wirtualnej = ClassicVNet <br>
Lokalizacja = zachodnie stany USA <br>
Przestrzeniami adresów sieci wirtualnej = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nazwa sieci lokalnej = RMVNetLocal <br>
GatewayType = DynamicRouting

**Ustawienia Menedżera zasobów w sieci wirtualnej**

Nazwa sieci wirtualnej = RMVNet <br>
Grupa zasobów = RG1 <br>
Przestrzeni adresów IP sieci wirtualnej = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Lokalizacja = wschodnie stany USA <br>
Nazwa publicznego adresu IP bramy = gwpip <br>
Brama sieci lokalnej = ClassicVNetLocal <br>
Nazwa bramy sieci wirtualnej = RMGateway <br>
Konfiguracja adresów IP bramy = gwipconfig

## <a name="createsmgw"></a>Sekcja 1 — Konfigurowanie klasycznej sieci wirtualnej
### <a name="1-download-your-network-configuration-file"></a>1. Pobieranie pliku konfiguracji sieci
1. Zaloguj się do konta platformy Azure, w konsoli programu PowerShell z podwyższonym poziomem uprawnień. Następujące polecenie cmdlet monituje o poświadczenia logowania dla konta platformy Azure. Po zalogowaniu pobiera ono ustawienia konta, aby były dostępne dla programu Azure PowerShell. Klasycznym poleceń cmdlet programu Azure PowerShell dla usługi zarządzania (SM) są używane w tej sekcji.

  ```powershell
  Add-AzureAccount
  ```

  Uzyskiwanie subskrypcji platformy Azure.

  ```powershell
  Get-AzureSubscription
  ```

  Jeśli masz więcej niż jedną subskrypcję, wybierz tę, której chcesz użyć.

  ```powershell
  Select-AzureSubscription -SubscriptionName "Name of subscription"
  ```
2. Wyeksportuj do pliku konfiguracji sieci platformy Azure, uruchamiając następujące polecenie. Możesz zmienić lokalizację pliku, aby wyeksportować ją w innej lokalizacji, w razie potrzeby.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
3. Otwórz plik XML, aby go edytować. Na przykład pliku konfiguracji sieci, zobacz [schemat konfiguracji sieci](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Sprawdź podsieć bramy
W **VirtualNetworkSites** elementu, Dodaj podsieć bramy sieci wirtualnej, jeśli nie została jeszcze utworzona. Podczas pracy z pliku konfiguracji sieci, podsieć bramy musi być o nazwie "GatewaySubnet" lub Azure nie może rozpoznać i używać go jako podsieć bramy.

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
Lokacja sieci lokalnej, które można dodać reprezentuje RM sieci wirtualnej, z którym chcesz się połączyć. Dodaj **LocalNetworkSites** elementu do pliku, jeśli jeszcze nie istnieje. W tym momencie w konfiguracji VPNGatewayAddress może być dowolnym prawidłowego publicznego adresu IP, ponieważ możemy jeszcze nie utworzono bramę sieci wirtualnej Menedżera zasobów. Po utworzymy bramy możemy zastąpić ten adres IP — symbol zastępczy poprawne publiczny adres IP przypisany do bramy protokołu RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Kojarzenie z lokacją sieci lokalnej sieci wirtualnej
W tej sekcji możemy określić lokację sieci lokalnej, który chcesz połączyć sieć wirtualną do. W takim przypadku jest sieć wirtualną Resource Manager, która wcześniej odwołaniu. Upewnij się, że nazwy są zgodne. Ten krok nie powoduje utworzenia bramy. Określa bramy będą łączyć się z sieci lokalnej.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Zapisz plik i przekazać
Zapisz plik, a następnie zaimportuj go do platformy Azure, uruchamiając następujące polecenie. Upewnij się, że w przypadku zmiany ścieżki pliku jako wymaganych w danym środowisku.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Widoczny będzie podobny wynik pokazujący, że importowanie zakończyło się pomyślnie.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Tworzenie bramy

Przed uruchomieniem w tym przykładzie należy odwoływać się do pobranego pliku konfiguracji sieci dla nazw dokładne Azure spodziewa się. Plik konfiguracji sieci zawiera wartości klasyczne sieci wirtualne. Czasami nazwy dla klasyczne sieci wirtualne są zmieniane w pliku konfiguracji sieci, podczas tworzenia klasycznych ustawień sieci wirtualnej w portalu Azure, z powodu różnic w modelach wdrażania. Na przykład jeśli używasz portalu Azure do utworzenia klasyczny sieć wirtualną o nazwie "Klasycznej sieci wirtualnej" i utworzony w grupie zasobów o nazwie "ClassicRG", nazwa, która jest zawarta w pliku konfiguracji sieci jest konwertowana na "Grupy ClassicRG klasycznej sieci wirtualnej". Podczas określania nazwy sieci wirtualnej, która zawiera spacje, użyj wartości w cudzysłowie.


Skorzystaj z następującego przykładu, aby utworzyć bramę routingu dynamicznego:

```powershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Stan bramy można sprawdzić za pomocą **Get-AzureVNetGateway** polecenia cmdlet.

## <a name="creatermgw"></a>Sekcja 2 — Konfigurowanie bramy sieci wirtualnej RM
Aby utworzyć bramę sieci VPN RM sieci wirtualnej, wykonaj poniższe instrukcje. Nie uruchamiaj kroki do czasu, po pobraniu publicznego adresu IP dla bramy klasycznej sieci wirtualnej. 

1. Zaloguj się do konta platformy Azure, w konsoli programu PowerShell. Następujące polecenie cmdlet monituje o poświadczenia logowania dla konta platformy Azure. Po zalogowaniu ustawienia konta są pobierane, tak aby były dostępne dla programu Azure PowerShell.

  ```powershell
  Connect-AzureRmAccount
  ``` 
   
  Pobierz listę subskrypcji platformy Azure.

  ```powershell
  Get-AzureRmSubscription
  ```
   
  Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, która ma być używany.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Utwórz bramę sieci lokalnej. W sieci wirtualnej brama sieci lokalnej zazwyczaj odwołuje się do lokalizacji lokalnej. W takim przypadku bramy sieci lokalnej odwołuje się do klasycznej sieci wirtualnej. Nadaj nazwę za pomocą którego Azure można odwołuje się do niego, a także określ prefiks przestrzeni adresów. Platforma Azure używa prefiksu adresu IP w celu określenia, który ruch danych należy skierować do lokalizacji lokalnej. Jeśli musisz dostosować informacje w tym miejscu później, przed utworzeniem bramy sieci można zmodyfikować wartości i ponownie uruchomić próbki.
   
   **-Nazwa** to nazwa ma zostać przypisany do odwoływania się do bramy sieci lokalnej.<br>
   **-AddressPrefix** jest klasycznej sieci wirtualnej przestrzeni adresowej.<br>
   **-GatewayIpAddress** jest publiczny adres IP bramy klasycznej sieci wirtualnej. Należy pamiętać o zmianie w poniższym przykładzie, aby odzwierciedlić poprawny adres IP.<br>

  ```powershell
  New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
  -Location "West US" -AddressPrefix "10.0.0.0/24" `
  -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
  ```
3. Żądaj publicznego adresu IP do przydzielenia do bramy sieci wirtualnej sieci wirtualnej Menedżera zasobów. Nie można określić adresu IP, którego chcesz użyć. Adres IP jest dynamicznie przydzielane bramy sieci wirtualnej. Nie oznacza to jednak, że adres IP się zmienia. Tylko zmiany adresu IP bramy sieci wirtualnej jest, gdy brama zostanie usunięta i utworzona ponownie. Nie zmienia ono całej zmiany rozmiaru, resetowania lub innych wewnętrzny konserwacji/uaktualnienia bramy.

  W tym kroku będziemy również ustawić zmiennej używanej w kolejnym kroku.

  ```powershell
  $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
  -ResourceGroupName RG1 -Location 'EastUS' `
  -AllocationMethod Dynamic
  ```

4. Sprawdź, czy sieci wirtualnej ma podsieci bramy. Jeśli istnieje podsieć bramy, dodaj je. Upewnij się, że nosi nazwę podsieci bramy *GatewaySubnet*.
5. Pobieranie w podsieci używanej bramy, uruchamiając następujące polecenie. W tym kroku będziemy również ustawić zmienną do użycia w następnym kroku.
   
   **-Nazwa** jest nazwą sieci wirtualnej Menedżera zasobów.<br>
   **-ResourceGroupName** jest to grupa zasobów skojarzonego z sieci wirtualnej. Podsieć bramy musi już istnieć dla tej sieci wirtualnej i musi mieć nazwę *GatewaySubnet* działała poprawnie.<br>

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
  -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
  ``` 

6. Tworzenie konfiguracji adresów IP bramy. W ramach konfiguracji bramy zostaje zdefiniowana podsieć i publiczny adres IP do użycia. Poniższy przykład umożliwia utworzenie konfiguracji bramy.

  W tym kroku **- SubnetId** i **- PublicIpAddressId** parametry muszą być przekazywane właściwość identyfikatora podsieci i obiekty adresu IP, odpowiednio. Nie można użyć prostego ciągu. Te zmienne są ustawiane w kroku żądanie publicznego adresu IP i kroku pobrać podsieci.

  ```powershell
  $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
  -Name gwipconfig -SubnetId $subnet.id `
  -PublicIpAddressId $ipaddress.id
  ```
7. Utwórz bramę sieci wirtualnej Menedżera zasobów, uruchamiając następujące polecenie. `-VpnType` Musi być *RouteBased*. Może upłynąć 45 minut lub więcej bramy do utworzenia.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
  -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
  -IpConfigurations $gwipconfig `
  -EnableBgp $false -VpnType RouteBased
  ```
8. Po utworzeniu bramy sieci VPN, należy skopiować publicznego adresu IP. Można użyć podczas konfigurowania ustawień sieci lokalnej w klasycznej sieci wirtualnej. Następujące polecenie cmdlet umożliwia pobrać publicznego adresu IP. Publiczny adres IP na liście jest zwracany jako *IpAddress*.

  ```powershell
  Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1
  ```

## <a name="localsite"></a>Sekcja 3 - zmodyfikuj ustawienia klasycznego lokalnej lokacji sieci wirtualnej

W tej sekcji możesz pracować z klasycznej sieci wirtualnej. Można zastąpić adres IP symbolu zastępczego, używanego podczas określania ustawień lokacji lokalnej, które będą używane do łączenia się z bramą sieci wirtualnej Menedżera zasobów. 

1. Eksportowanie plików konfiguracji sieci.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. Za pomocą edytora tekstu, zmodyfikuj wartość dla VPNGatewayAddress. Zastąp symbol zastępczy adres IP publiczny adres IP bramy usługi Resource Manager, a następnie zapisz zmiany.

  ```
  <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  ```
3. Zaimportuj plik konfiguracji sieci zmodyfikowane na platformie Azure.

  ```powershell
  Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
  ```

## <a name="connect"></a>Sekcja 4 - Tworzenie połączenia między bramami
Tworzenie połączenia między bramami wymaga środowiska PowerShell. Może być konieczne dodanie konta platformy Azure, aby użyć wersji klasycznej poleceń cmdlet programu PowerShell. Aby to zrobić, użyj **Add-AzureAccount**.

1. W konsoli programu PowerShell należy ustawić klucz udostępniony. Przed uruchomieniem poleceń cmdlet należy odwoływać się do pobranego pliku konfiguracji sieci dla nazw dokładne Azure spodziewa się. Podczas określania nazwy sieci wirtualnej, która zawiera spacje, użyj pojedynczy znaki cudzysłowu otaczające wartość.<br><br>W poniższym przykładzie **- VNetName** jest nazwą klasycznej sieci wirtualnej i **- LocalNetworkSiteName** jest nazwa określona dla lokacji sieci lokalnej. **- SharedKey** to Generowanie i określ wartość. W tym przykładzie użyliśmy "abc123", ale można wygenerować i korzystać z bardziej złożonych. Ważne jest, że wartość określone w tym miejscu musi być taka sama jak wartość zostanie w następnym kroku podczas tworzenia połączenia. Zwracany powinien być wyświetlony **stanu: pomyślnie**.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName ClassicVNet `
  -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
  ```
2. Utwórz połączenie sieci VPN, uruchamiając następujące polecenia:
   
  Ustaw zmienne.

  ```powershell
  $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
  $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
  ```
   
  Utwórz połączenie. Zwróć uwagę, że **- ConnectionType** jest IPsec, nie Vnet2Vnet.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
  -Location "East US" -VirtualNetworkGateway1 `
  $vnet02gateway -LocalNetworkGateway2 `
  $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

## <a name="verify"></a>Sekcja 5 - Sprawdź połączenia

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Aby sprawdzić połączenie z klasycznej sieci wirtualnej do sieci wirtualnej Resource Manager

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Aby sprawdzić połączenie z sieci wirtualnej Menedżera zasobów klasycznych sieci wirtualnej

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
