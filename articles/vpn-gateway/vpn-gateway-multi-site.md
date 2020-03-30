---
title: 'Łączenie sieci wirtualnej z wieloma witrynami przy użyciu bramy sieci VPN: Classic'
description: Połącz wiele lokalnych lokacji lokalnych z klasyczną siecią wirtualną przy użyciu bramy sieci VPN.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/11/2020
ms.author: yushwang
ms.openlocfilehash: a95cd6ea85a16b0e0bf5f67f5dfc20d57f11463b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198103"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Dodawanie połączenia lokacja lokacja do sieci wirtualnej z istniejącym połączeniem bramy sieci VPN (klasyczny)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Portal Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasyczny)](vpn-gateway-multi-site.md)
>
>

W tym artykule otrzymasz od korzystania z programu PowerShell w celu dodania połączeń lokacja lokacja (S2S) do bramy sieci VPN, która ma istniejące połączenie. Ten typ połączenia jest często określany jako konfiguracja "wielowitamierowa". Kroki opisane w tym artykule dotyczą sieci wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania (znanego również jako zarządzanie usługami). Te kroki nie mają zastosowania do konfiguracji połączeń współistniejących w układzie typu Site-To-Site.

### <a name="deployment-models-and-methods"></a>Modele i metody wdrażania

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Aktualizujemy tę tabelę, gdy nowe artykuły i dodatkowe narzędzia stają się dostępne dla tej konfiguracji. Gdy artykuł jest dostępny, łączymy się bezpośrednio z nim z tej tabeli.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Informacje o łączeniu

Do jednej sieci wirtualnej można połączyć wiele lokacji lokalnych. Jest to szczególnie atrakcyjne w przypadku tworzenia rozwiązań chmury hybrydowej. Tworzenie połączenia z wieloma lokacjami z bramą sieci wirtualnej platformy Azure jest podobne do tworzenia innych połączeń lokacja-lokacja. W rzeczywistości można użyć istniejącej bramy sieci VPN platformy Azure, o ile brama jest dynamiczna (oparta na trasie).

Jeśli masz już bramę statyczną połączoną z siecią wirtualną, możesz zmienić typ bramy na dynamiczny bez konieczności przebudowywania sieci wirtualnej w celu uwzględnienia wielu lokacji. Przed zmianą typu routingu upewnij się, że lokalna brama sieci VPN obsługuje konfiguracje sieci VPN oparte na trasach.

![diagram wielomiejscowy](./media/vpn-gateway-multi-site/multisite.png "wielomiejscowy")

## <a name="points-to-consider"></a>Kwestie do rozważenia

**Nie będzie można użyć portalu do wprowadzania zmian w tej sieci wirtualnej.** Należy wprowadzić zmiany w pliku konfiguracji sieci zamiast korzystać z portalu. Jeśli wniesiesz zmiany w portalu, zastąpią one ustawienia odwołania do wielu lokacji dla tej sieci wirtualnej.

Powinieneś czuć się komfortowo przy użyciu pliku konfiguracji sieci do czasu ukończenia procedury wielowitamierowej. Jeśli jednak w konfiguracji sieci pracuje wiele osób, musisz się upewnić, że wszyscy wiedzą o tym ograniczeniu. Nie oznacza to, że w ogóle nie możesz korzystać z portalu. Można go używać do wszystkiego innego, z wyjątkiem wprowadzania zmian konfiguracji do tej konkretnej sieci wirtualnej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy masz następujące elementy:

* Kompatybilny sprzęt sieci VPN dla każdej lokalizacji lokalnej. Sprawdź [informacje o urządzeniach sieci VPN pod kątem łączności sieci wirtualnej,](vpn-gateway-about-vpn-devices.md) aby sprawdzić, czy urządzenie, którego chcesz użyć, jest czymś, co jest znane jako zgodne.
* Zewnętrznie publiczny adres IP IPv4 dla każdego urządzenia sieci VPN. Adres IP nie może znajdować się za translatorem adresów sieciowych. Jest to wymagane.
* Ktoś, kto jest biegły w konfigurowaniu sprzętu VPN. Musisz mieć silne zrozumienie, jak skonfigurować urządzenie VPN lub pracować z kimś, kto to robi.
* Zakresy adresów IP, których chcesz użyć w sieci wirtualnej (jeśli jeszcze go nie utworzono).
* Zakresy adresów IP dla każdej lokacji sieci lokalnej, z którą będziesz się łączyć. Należy upewnić się, że zakresy adresów IP dla każdej lokacji sieci lokalnej, z którą chcesz się połączyć, nie nakładają się na siebie. W przeciwnym razie portal lub interfejs API REST odrzuci przekazywanych konfiguracji.<br>Na przykład jeśli masz dwie lokacje sieci lokalnej, które zawierają zakres adresów IP 10.2.3.0/24 i masz pakiet o adresie docelowym 10.2.3.3, platforma Azure nie wie, do której lokacji chcesz wysłać pakiet, ponieważ zakresy adresów nakładają się na siebie. Aby zapobiec problemom z routingiem, platforma Azure nie zezwala na przekazywanie pliku konfiguracji, który ma nakładające się zakresy.

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="1-create-a-site-to-site-vpn"></a>1. Tworzenie sieci VPN typu lokacja lokacja
Jeśli masz już sieć VPN typu lokacja-lokacja z dynamiczną bramą routingu, świetnie! Można przejść do [eksportu ustawień konfiguracji sieci wirtualnej](#export). Jeśli nie, wykonaj następujące czynności:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Jeśli masz już sieć wirtualną lokacja lokacja, ale ma ona statyczną (opartą na zasadach) bramę routingu:
1. Zmień typ bramy na routing dynamiczny. Sieć VPN z wieloma lokacjami wymaga dynamicznej bramy routingu (znanej również jako routing. Aby zmienić typ bramy, musisz najpierw usunąć istniejącą bramę, a następnie utworzyć nową.
2. Skonfiguruj nową bramę i utwórz tunel VPN. Aby uzyskać instrukcje, Aby uzyskać instrukcje, zobacz [Określanie typu jednostki SKU i sieci VPN](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Upewnij się, że typ marszruty został określony jako "Dynamiczny".

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Jeśli nie masz sieci wirtualnej lokacja lokacja:
1. Utwórz sieć wirtualną lokacja-lokacja, korzystając z następujących instrukcji: [Utwórz sieć wirtualną z połączeniem sieci VPN między lokacjami.](vpn-gateway-site-to-site-create.md)  
2. Konfigurowanie bramy routingu dynamicznego przy użyciu następujących instrukcji: [Konfigurowanie bramy sieci VPN](vpn-gateway-configure-vpn-gateway-mp.md). Pamiętaj, aby wybrać **routing dynamiczny** dla typu bramy.

## <a name="2-export-the-network-configuration-file"></a><a name="export"></a>2. Wyeksportuj plik konfiguracji sieci

Otwórz konsolę programu PowerShell z podwyższonymi prawami. Aby przełączyć się do zarządzania usługami, użyj tego polecenia:

```powershell
azure config mode asm
```

Połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Add-AzureAccount
```

Eksportuj plik konfiguracji sieci platformy Azure, uruchamiając następujące polecenie. W razie potrzeby można zmienić lokalizację pliku, aby wyeksportować go do innej lokalizacji.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Otwórz plik konfiguracji sieci
Otwórz plik konfiguracji sieci pobrany w ostatnim kroku. Użyj dowolnego edytora xml, który Ci się podoba. Plik powinien wyglądać podobnie do następującego:

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

## <a name="4-add-multiple-site-references"></a>4. Dodaj wiele odwołań do witryny
Po dodaniu lub usunięciu informacji o numerze witryny wprowadzone zostaną zmiany konfiguracji w witrynie ConnectionsToLocalNetwork/LocalNetworkSiteRef. Dodanie nowego odwołania do lokacji lokalnej wyzwala platformę Azure w celu utworzenia nowego tunelu. W poniższym przykładzie konfiguracja sieci jest dla połączenia z jedną lokacją. Zapisz plik po zakończeniu wprowadzania zmian.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Aby dodać dodatkowe odwołania do lokacji (utworzyć konfigurację wielu lokacji), wystarczy dodać dodatkowe wiersze "LocalNetworkSiteRef", jak pokazano w poniższym przykładzie:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Zaimportuj plik konfiguracji sieci
Zaimportuj plik konfiguracji sieci. Po zaimportowaniu tego pliku ze zmianami zostaną dodane nowe tunele. Tunele będą używać bramy dynamicznej utworzonej wcześniej. Do zaimportowania pliku można użyć programu PowerShell.

## <a name="6-download-keys"></a>6. Pobierz klucze
Po dodaniu nowych tuneli użyj polecenia cmdlet programu PowerShell "Get-AzureVNetGatewayKey", aby uzyskać klucze wstępne IPsec/IKE dla każdego tunelu.

Przykład:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Jeśli wolisz, możesz również użyć interfejsu API REST dostępu do *klucza udostępnionego bramy sieci wirtualnej,* aby uzyskać klucze wstępnie udostępnione.

## <a name="7-verify-your-connections"></a>7. Sprawdź swoje połączenia
Sprawdź stan tunelu wielomiejscowego. Po pobraniu kluczy dla każdego tunelu należy zweryfikować połączenia. Użyj "Get-AzureVnetConnection", aby uzyskać listę tuneli sieci wirtualnej, jak pokazano w poniższym przykładzie. VNet1 to nazwa sieci wirtualnej.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Przykład zwrotu:

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

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o bramach sieci VPN, zobacz [Informacje o bramach sieci VPN](vpn-gateway-about-vpngateways.md).
