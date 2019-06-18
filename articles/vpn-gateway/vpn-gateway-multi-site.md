---
title: 'Łączenie sieci wirtualnej z wieloma lokacjami przy użyciu bramy sieci VPN i programu PowerShell: Klasyczny | Dokumentacja firmy Microsoft'
description: Łączenie wielu lokacji lokalnej do klasycznej sieci wirtualnej przy użyciu bramy sieci VPN.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: 77f8b7094c96e507eef1d360a26240627bc0e350
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60836100"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Dodawanie połączenia lokacja-lokacja z siecią wirtualną przy użyciu istniejącego połączenia bramy sieci VPN (model klasyczny)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasyczny)](vpn-gateway-multi-site.md)
>
>

Ten artykuł przeprowadzi Dodawanie połączenia lokacja-lokacja (S2S) do bramy sieci VPN, który ma istniejące połączenie przy użyciu programu PowerShell. Ten typ połączenia jest często nazywane konfiguracji "obejmującego wiele lokacji". Kroki opisane w tym artykule dotyczą sieciami wirtualnymi utworzonymi za pomocą klasycznego modelu wdrażania (znany także jako Service Management). Te kroki nie dotyczą współistniejące konfiguracje połączeń usługi ExpressRoute/lokacja-lokacja.

### <a name="deployment-models-and-methods"></a>Modele i metody wdrażania

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Aktualizujemy ją jako nowych artykułów i dodatkowych narzędzi stają się dostępne dla tej konfiguracji. Jeśli artykuł jest dostępny, można przejść do niego bezpośrednio z tej tabeli.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>O łączeniu z

Możesz połączyć wiele lokacji lokalnych do jednej sieci wirtualnej. Jest to szczególnie atrakcyjne do budowania hybrydowych rozwiązań w chmurze. Tworzenie połączenia obejmujące wiele lokacji do bramy sieci wirtualnej platformy Azure jest podobne do tworzenia innych połączeń lokacja-lokacja. W rzeczywistości, można użyć istniejącej bramy sieci VPN platformy Azure, tak długo, jak bramy jest dynamiczny (oparte na trasach).

Już brama statyczna, która jest podłączony do sieci wirtualnej, można zmienić typu bramy na dynamiczne, bez konieczności połączenia obejmujące wiele lokacji tak, aby ponownie utworzyć sieci wirtualnej. Przed zmianą typu routingu, upewnij się, że usługi lokalnej bramy sieci VPN obsługuje konfiguracje sieci VPN opartej na trasach.

![diagram połączenia obejmujące wiele lokacji](./media/vpn-gateway-multi-site/multisite.png "obejmujące wiele lokacji")

## <a name="points-to-consider"></a>Kwestie do rozważenia

**Nie można wprowadzić zmiany w tej sieci wirtualnej za pomocą portalu.** Musisz wprowadzić zmiany w pliku konfiguracji sieci, a nie przy użyciu portalu. Jeśli wprowadzisz zmiany w portalu zastępują ustawienia obejmujące wiele lokacji odniesienia dla tej sieci wirtualnej.

Należy czujesz się, przy użyciu pliku konfiguracji sieci do czasu, zakończyła się procedury obejmujące wiele lokacji. Jednak jeśli masz wiele osób pracuje w konfiguracji sieci, należy się upewnić, że wszyscy zna tego ograniczenia. Nie oznacza to, że nie możesz użyć portalu na wszystkich. Służy on do wszystkie inne czynności, z wyjątkiem wprowadzania zmian konfiguracji do tej konkretnej sieci wirtualnej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji Sprawdź, czy dysponujesz następującymi elementami:

* Zgodny sprzęt sieci VPN dla każdej lokalizacji lokalnej. Sprawdź [informacje o urządzeniach sieci VPN dla łączność sieciowa](vpn-gateway-about-vpn-devices.md) można sprawdzić, czy urządzenie, którego chcesz używać jest coś, co jest znane jako zgodne.
* Dostępny zewnętrznie publiczny adres IP protokołu IPv4 dla każdego urządzenia sieci VPN. Adres IP nie może znajdować się za translatorem adresów sieciowych. Jest to wymagane.
* Niezbędne jest zainstalowanie najnowszej wersji poleceń cmdlet programu Azure PowerShell. Upewnij się, że możesz zainstalować wersję Service Management (SM) oprócz wersji Menedżera zasobów. Zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview) Aby uzyskać więcej informacji.
* Osoba, która jest biegły w konfigurowaniu sprzętem sieci VPN. Musisz mieć wiedzą, jak skonfigurować urządzenie sieci VPN lub osobą, która jest pracować.
* Zakresy adresów IP, które chcesz użyć dla sieci wirtualnej (Jeśli nie utworzono jeszcze jeden).
* Zakresy adresów IP dla każdej lokacji sieci lokalnej, które będą łączyć się z. Należy się upewnić, że zakresy adresów IP dla każdej lokacji sieci lokalnej, które chcesz połączyć się z nie pokrywają się. W przeciwnym razie portalu lub interfejsu API REST odrzuci konfiguracji są przekazywane.<br>Na przykład jeśli dwie lokacje sieci lokalnej, że oba zawierają 10.2.3.0/24 zakres adresów IP i ma pakietu przy użyciu adresu docelowego 10.2.3.3, Azure w takich sytuacjach przydałaby znać lokacji, do której chcesz wysłać pakietu, ponieważ wszystkie nakładające się zakresy adresów. Aby uniknąć problemów z routingiem, Azure nie zezwala na przekazywanie pliku konfiguracji, który ma nakładające się zakresy.

## <a name="1-create-a-site-to-site-vpn"></a>1. Tworzenie sieci VPN typu lokacja-lokacja
Jeśli masz już sieć VPN lokacja-lokacja z bramą routingu dynamicznego świetne! Możesz przejść do [Eksportuj ustawienia konfiguracji sieci wirtualnej](#export). W przeciwnym razie wykonaj następujące czynności:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Jeśli masz już sieć wirtualną lokacja-lokacja, ale ma statycznego bramy routingu (oparte na zasadach):
1. Typ bramy można zmienić do routingu dynamicznego. Połączenia obejmujące wiele lokacji sieci VPN wymaga (znany także jako oparta na trasach) bramy o dynamicznym routingu. Aby zmienić typu bramy, należy najpierw usunąć istniejącą bramę, a następnie utwórz nową.
2. Konfigurowanie nowej bramy i tworzenia tunelu sieci VPN. Aby uzyskać instrukcje, aby uzyskać instrukcje, zobacz [Określ typ jednostki SKU i sieci VPN](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Upewnij się, że należy określić typ routingu jako "Dynamic".

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Jeśli nie masz lokacja-lokacja sieci wirtualnej:
1. Tworzenie sieci wirtualnej lokacja-lokacja przy użyciu tych instrukcji: [Tworzenie sieci wirtualnej z połączeniem sieci VPN typu lokacja lokacja](vpn-gateway-site-to-site-create.md).  
2. Konfigurowanie bramy o dynamicznym routingu przy użyciu tych instrukcji: [Konfigurowanie bramy sieci VPN](vpn-gateway-configure-vpn-gateway-mp.md). Pamiętaj o wybraniu **routingu dynamicznego** dla danego typu bramy.

## <a name="export"></a>2. Eksportuj plik konfiguracji sieci
Eksportuj do pliku konfiguracji sieci platformy Azure, uruchamiając następujące polecenie. Można zmienić lokalizację pliku do wyeksportowania do innej lokalizacji, w razie potrzeby.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Otwórz plik konfiguracji sieci
Otwórz plik konfiguracji sieci, który został pobrany w poprzednim kroku. Użyj dowolnego edytora xml, który chcesz. Plik powinien wyglądać podobnie do następującego:

        <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. Dodaj odwołania do wielu witryn
Po dodaniu lub usunięciu informacji o lokacji odniesienia, wprowadzisz zmiany w konfiguracji do ConnectionsToLocalNetwork/elementu LocalNetworkSiteRef. Dodawanie nowych wyzwalaczy odwołanie do lokacji lokalnej platformy Azure, aby utworzyć nowy tunel. W poniższym przykładzie konfiguracja sieci jest połączenie pojedynczej lokacji. Po zakończeniu wprowadzania zmian, należy zapisać plik.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Aby dodać odwołania do dodatkowej lokacji (Tworzenie konfiguracji połączenia obejmujące wiele lokacji), po prostu dodaj dodatkowe wiersze "Elementu LocalNetworkSiteRef", jak pokazano w poniższym przykładzie:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importuj plik konfiguracji sieci
Importuj plik konfiguracji sieci. Podczas importowania tego pliku ze zmianami, zostaną dodane nowe tuneli. Tunele będą używać bramy dynamicznej, która została utworzona wcześniej. Można użyć programu PowerShell, aby zaimportować plik.

## <a name="6-download-keys"></a>6. Pobierz klucze
Po dodaniu usługi nowych tuneli użyj polecenia cmdlet programu PowerShell "Get-AzureVNetGatewayKey", aby pobrać klucze wstępne protokołu IPsec/IKE dla każdego tunelu.

Na przykład:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Jeśli wolisz, możesz także użyć *uzyskać wirtualnych sieci bramy klucza wspólnego* interfejsu API REST, aby uzyskać klucze wstępne.

## <a name="7-verify-your-connections"></a>7. Sprawdź połączenia
Sprawdź stan tunel połączenia obejmujące wiele lokacji. Po pobraniu klucze dla każdego tunelu, będziesz chciał sprawdzić połączenia. Użyj "Get-AzureVnetConnection" w celu uzyskania listy tuneli sieci wirtualnej, jak pokazano w poniższym przykładzie. Sieć VNet1 jest nazwa sieci wirtualnej.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Przykład zwracany:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat bram sieci VPN, zobacz [o bramach sieci VPN](vpn-gateway-about-vpngateways.md).
