---
title: 'Połączenie wirtualnej sieci z wieloma lokacjami przy użyciu bramy sieci VPN i programu PowerShell: klasycznym | Dokumentacja firmy Microsoft'
description: Podłączyć wiele lokacji lokalnej lokalnego do klasycznej sieci wirtualnej przy użyciu bramy sieci VPN.
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
ms.openlocfilehash: c0af4271df0e88354edb717b8d6f4c99ab29e573
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399376"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Dodaj połączenie lokacja-lokacja z sieci wirtualnej z istniejącego połączenia bramy sieci VPN (klasyczne)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasyczny)](vpn-gateway-multi-site.md)
>
>

Ten artykuł przeprowadzi Cię przez dodawanie połączeń lokacja-lokacja (S2S) do bramy sieci VPN, który ma istniejącego połączenia przy użyciu programu PowerShell. Połączenia tego typu jest często określany jako "obejmujący wiele lokacji" konfiguracji. Kroki opisane w tym artykule dotyczą sieci wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania (znanej także jako Service Management). Te kroki nie dotyczą konfiguracji połączenia ważnych ExpressRoute/lokacja-lokacja.

### <a name="deployment-models-and-methods"></a>Modele i metody wdrażania

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Modyfikacjom w tej tabeli jako artykułów i dodatkowe narzędzia staną się dostępne dla tej konfiguracji. Po udostępnieniu artykułu możemy link bezpośrednio do niego z tej tabeli.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>O łączeniu

Można połączyć wiele lokacji lokalnej do jednej sieci wirtualnej. Jest to szczególnie atrakcyjne tworzenie hybrydowych rozwiązań w chmurze. Tworzenie obejmujący wiele lokacji połączenie bramy sieci wirtualnej platformy Azure jest podobny do tworzenia innych połączeń lokacja-lokacja. W rzeczywistości, można użyć istniejącej bramy sieci VPN platformy Azure, tak długo, jak bramy jest dynamiczny (opartej na trasach).

Jeśli masz już podłączony do sieci wirtualnej bramy statyczne, można zmienić typu bramy na dynamiczny, bez konieczności odbudować sieci wirtualnej, aby zmieścił się w wielu lokacjach. Przed zmianą typu routingu, upewnij się, że bramy sieci VPN, lokalnymi obsługuje konfiguracje sieci VPN opartej na trasach.

![diagram obejmujący wiele lokacji](./media/vpn-gateway-multi-site/multisite.png "obejmujący wiele lokacji")

## <a name="points-to-consider"></a>Kwestie do rozważenia

**Nie można korzystać z portalu, aby wprowadzić zmiany w tej sieci wirtualnej.** Należy wprowadzić zmiany w pliku konfiguracji sieci, zamiast korzystać z portalu. Jeśli wprowadzisz zmiany w portalu zastępują ustawienia odwołania obejmujący wiele lokacji w tej sieci wirtualnej.

Uważasz doświadczenia, do czasu zakończenia procedury obejmujący wiele lokacji przy użyciu pliku konfiguracji sieci. Jednak jeśli masz wiele osób pracuje w konfiguracji sieci, należy upewnij się, że wszyscy zna tego ograniczenia. To nie oznacza, że nie możesz użyć portalu w ogóle. Można go dla wszystkich innych urządzeń, z wyjątkiem zmiany konfiguracji tej określonej sieci wirtualnej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfigurowania należy sprawdzić, czy następujące czynności:

* Zgodny sprzęt sieci VPN dla każdej lokalizacji lokalnej. Sprawdź [o urządzeniach sieci VPN do łączności sieciowej wirtualnego](vpn-gateway-about-vpn-devices.md) Aby sprawdzić, czy urządzenie, którego chcesz użyć czegoś, co jest uznany za zgodny.
* Zewnętrznie połączonej publiczny adres IPv4 dla każdego urządzenia sieci VPN. Adres IP nie może znajdować się za urządzeniem NAT To wymaganie.
* Niezbędne jest zainstalowanie najnowszej wersji poleceń cmdlet programu Azure PowerShell.  Upewnij się, że oprócz wersji usługi Resource Manager w wersji usługi zarządzania (ko). Zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) Aby uzyskać więcej informacji.
* Osoby, która jest wykorzystać Konfigurowanie sprzętu sieci VPN. Będzie konieczne silne wiedzę jak skonfigurować urządzenie sieci VPN lub pracować z osobą, która jest.
* Zakresy adresów IP, które ma być używany dla sieci wirtualnej (jeśli jeszcze nie utworzono jeden).
* Zakresy adresów IP dla każdej lokacji sieci lokalnej, które będą łączyć się z. Należy się upewnić, czy zakresy adresów IP dla każdej lokacji sieci lokalnej, które chcesz się połączyć, aby nie pokrywają się. W przeciwnym razie konfiguracja przekazywanych spowoduje odrzucenie portalu lub interfejsu API REST.<br>Na przykład jeśli masz dwie lokacje sieci lokalnej, czy zawierają 10.2.3.0/24 zakres adresów IP i czy masz pakiet o docelowy adres 10.2.3.3 Azure nie wiadomo, witryn, które chcesz wysyłać pakietów do, ponieważ nakładają się na zakresy adresów. Aby zapobiec problemom routingu, Azure nie zezwala na przekazywanie pliku konfiguracji, który ma nakładające się zakresy.

## <a name="1-create-a-site-to-site-vpn"></a>1. Tworzenie sieci VPN typu lokacja-lokacja
Jeśli masz już sieć VPN lokacja-lokacja z bramą routingu dynamicznego ponosić! Możesz przejść do [Eksportuj ustawienia konfiguracji sieci wirtualnej](#export). Jeśli nie, wykonaj następujące czynności:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Jeśli masz już sieć wirtualną do lokacji, ale nie statyczne bramy routingu (na podstawie zasad):
1. Należy zmienić typ bramy do routingu dynamicznego. Obejmujący wiele lokacji sieci VPN wymaga dynamicznej bramy routingu (znanej także jako opartej na trasach). Aby zmienić typ bramy, musisz najpierw usunąć istniejącą bramę, a następnie utwórz nową.
2. Konfigurowanie nowej bramy i utworzyć tunel sieci VPN. Aby uzyskać instrukcje, aby uzyskać instrukcje, zobacz [Określ typ jednostki SKU i sieci VPN](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Upewnij się, że należy określić typ routingu jako "Dynamic".

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Jeśli nie masz lokacja-lokacja sieci wirtualnej:
1. Tworzenie sieci wirtualnej lokacja-lokacja, korzystając z tych instrukcji: [utworzyć sieć wirtualną z połączenia sieci VPN typu lokacja-lokacja](vpn-gateway-site-to-site-create.md).  
2. Należy skonfigurować bramę routingu dynamicznego przy użyciu tych instrukcji: [Brama sieci VPN](vpn-gateway-configure-vpn-gateway-mp.md). Należy wybrać **routingu dynamicznego** dla danego typu bramy.

## <a name="export"></a>2. Eksportowanie plików konfiguracji sieci
Wyeksportuj do pliku konfiguracji sieci platformy Azure, uruchamiając następujące polecenie. Możesz zmienić lokalizację pliku, aby wyeksportować ją w innej lokalizacji, w razie potrzeby.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Otwórz plik konfiguracji sieci
Otwórz plik konfiguracji sieci, który został pobrany w ostatnim kroku. Użyj dowolnego edytora xml, który chcesz. Plik powinien wyglądać podobnie do następującego:

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

## <a name="4-add-multiple-site-references"></a>4. Dodawanie odwołania do wielu lokacji
Po dodaniu lub usunięciu informacji o lokacji odniesienia, będzie wprowadzać zmian konfiguracji do ConnectionsToLocalNetwork/elementu LocalNetworkSiteRef. Dodawanie nowych powoduje odwołanie lokacji lokalnej platformy Azure w celu utworzenia nowego tunelu. W poniższym przykładzie konfiguracja sieci jest połączenia pojedynczej lokacji. Zapisz plik, po zakończeniu wprowadzania zmian.

```
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Aby dodać odwołania do dodatkowej lokacji (Tworzenie konfiguracji wielu lokacji), po prostu dodaj dodatkowe wiersze "Elementu LocalNetworkSiteRef", jak pokazano w poniższym przykładzie:

```
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importowanie pliku konfiguracji sieci
Zaimportuj plik konfiguracji sieci. Podczas importowania tego pliku ze zmianami, zostaną dodane nowe tuneli. Tunele użyje bramy dynamiczne utworzony wcześniej. Za pomocą programu PowerShell można zaimportować pliku.

## <a name="6-download-keys"></a>6. Pobierz kluczy
Po dodaniu Twoje nowe tuneli, użyj polecenia cmdlet programu PowerShell "Get-AzureVNetGatewayKey", aby pobrać kluczy wstępnych IPsec i IKE dla każdego tunelu.

Na przykład:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Jeśli wolisz, można również użyć *uzyskać wirtualnych sieci bramy klucz wstępny* interfejsu API REST w celu uzyskania kluczy wstępnych.

## <a name="7-verify-your-connections"></a>7. Sprawdź połączenia
Sprawdź stan tunelu obejmujący wiele lokacji. Po pobraniu kluczy dla każdego tunelu, należy sprawdzić połączenia. Użyj "Get-AzureVnetConnection", aby uzyskać listę tuneli sieci wirtualnej, jak pokazano w poniższym przykładzie. VNet1 jest nazwą sieci wirtualnej.

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

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat bram sieci VPN, zobacz [o bramy sieci VPN](vpn-gateway-about-vpngateways.md).
