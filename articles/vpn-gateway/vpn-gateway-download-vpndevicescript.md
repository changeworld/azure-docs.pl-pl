---
title: 'Pobieranie skryptów konfiguracji urządzenia sieci VPN dla połączeń S2S sieci VPN: Azure Resource Manager | Microsoft Docs'
description: Ten artykuł przeprowadzi pobieranie skryptów konfiguracji urządzenia sieci VPN dla połączeń sieci VPN S2S z bramami sieci VPN Azure przy użyciu usługi Azure Resource Manager.
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: f7ee53c10c6597dbf98f8f85fc31fe789137471e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66157572"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Pobieranie skryptów konfiguracji urządzenia sieci VPN dla połączeń sieci VPN S2S

Ten artykuł przeprowadzi pobieranie skryptów konfiguracji urządzenia sieci VPN dla połączeń sieci VPN S2S z bramami sieci VPN Azure przy użyciu usługi Azure Resource Manager. Na poniższym diagramie przedstawiono ogólny przepływ pracy.

![download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Następujące urządzenia mają dostępnych skryptów:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about"></a>Skrypty konfiguracji urządzenia sieci VPN — informacje

Połączenie sieci VPN między lokalizacjami składa się z bramą Azure VPN gateway, lokalnego urządzenia sieci VPN i tunelu IPsec S2S sieci VPN, połączenie dwóch. Przepływ pracy typowy obejmuje następujące kroki:

1. Utwórz i skonfiguruj bramę Azure VPN gateway (Brama sieci wirtualnej)
2. Utwórz i skonfiguruj bramę sieci lokalnej platformy Azure, która reprezentuje Twojej sieci lokalnej i urządzeniem sieci VPN
3. Tworzenie i konfigurowanie połączenia sieci VPN platformy Azure między bramą sieci VPN platformy Azure i bramy sieci lokalnej
4. Konfigurowanie lokalnego urządzenia sieci VPN, reprezentowane przez bramę sieci lokalnej, aby ustanowić rzeczywiste tunel S2S VPN z bramą sieci VPN platformy Azure

Możesz wykonać kroki od 1 do 3 za pomocą platformy Azure [portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md), lub [interfejsu wiersza polecenia](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Ostatni krok obejmuje Konfigurowanie lokalnych urządzeń sieci VPN spoza platformy Azure. Ta funkcja umożliwia pobieranie skryptu konfiguracji urządzenia sieci VPN, z odpowiednimi wartościami swojej bramy sieci VPN Azure sieci wirtualnej i prefiksy adresów sieci lokalnej i właściwości połączenia sieci VPN, itp. już wypełnione. Możesz użyć skryptu jako punktu wyjścia lub zastosować skrypt bezpośrednio do lokalnych urządzeń sieci VPN za pośrednictwem konsoli programu configuration.

> [!IMPORTANT]
> * Składnia dla każdego skryptu konfiguracji urządzenia sieci VPN jest różne i stopniu zależą od modeli i wersji oprogramowania układowego. Należy zwrócić szczególną uwagę na Twoje urządzenie model i wersja informacje pod kątem dostępnych szablonów.
> * Niektóre wartości parametru musi być unikatowa na urządzeniu i nie można ustalić bez uzyskiwania dostępu do urządzenia. Skryptów konfiguracyjnych generowanych przez usługi Azure wstępnie wypełnić te wartości, ale należy upewnić się, że podane wartości są prawidłowe na urządzeniu. Przykłady:
>    * Numery
>    * Numery listy kontroli dostępu
>    * Nazwy zasad lub liczb, itp.
> * Wyszukaj słowa kluczowego "**Zastąp**" osadzony w skrypcie, aby odnaleźć parametrów, należy sprawdzić przed zastosowaniem skryptu.
> * Niektóre szablony obejmują "**oczyszczania**" sekcji można zastosować, aby usunąć konfiguracje. Domyślnie opatrzony sekcje oczyszczania.

## <a name="download-the-configuration-script-from-azure-portal"></a>Pobierz skrypt konfiguracji z witryny Azure portal

Utwórz bramę Azure VPN gateway, bramy sieci lokalnej i zasobu połączenia, połączenie dwóch. Następująca strona przeprowadzi Cię przez kroki:

* [Utwórz połączenie lokacja-lokacja w witrynie Azure portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Po utworzeniu zasobu połączenia postępuj zgodnie z poniższymi instrukcjami, aby pobrać skrypty konfiguracji urządzenia sieci VPN:

1. W przeglądarce przejdź do [witryny Azure portal](https://portal.azure.com) i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure
2. Przejdź do zasobu połączenia, który został utworzony. Lista wszystkich zasobów połączenia można znaleźć, klikając pozycję "Wszystkie usługi", a następnie "Sieć" i "Połączenia".

    ![Lista połączeń](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Kliknij połączenie, które chcesz skonfigurować.

    ![Przegląd połączenia](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Kliknij link "Pobierz konfiguracji", jak podkreślono na czerwono na stronie Przegląd połączenia; Spowoduje to otwarcie strony "Pobierania konfiguracji".

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Wybierz wersję modelu rodziny i oprogramowania układowego urządzenia sieci VPN, a następnie kliknij przycisk "Pobierz configuration".

    ![download66 skryptu 2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Monit o zapisanie pobranego skryptu (plik tekstowy) w przeglądarce.
7. Pobrany skrypt konfiguracji, otwórz go za pomocą edytora tekstów i wyszukaj słowo kluczowe "REPLACE" do zidentyfikowania i zbadania parametry, które może być konieczne, można zastąpić.

    ![Edytuj skrypt](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Pobierz skrypt konfiguracji przy użyciu programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz również pobrać skrypt konfiguracji, za pomocą programu Azure PowerShell, jak pokazano w poniższym przykładzie:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Dotyczą skryptu konfiguracji urządzenia sieci VPN

Po pobraniu i zweryfikować skrypt konfiguracji, następnym krokiem jest dotyczą skrypt urządzenia sieci VPN. Rzeczywiste procedura zależy od Twoich sprawia, że urządzenie sieci VPN i modeli. Zapoznaj się z podręczników operacji lub strony z instrukcjami dla urządzenia sieci VPN.

## <a name="next-steps"></a>Kolejne kroki

Kontynuować konfigurowanie usługi [połączenia lokacja lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
