---
title: Pobieranie skryptów konfiguracji urządzeń sieci VPN dla połączeń sieci VPN S2S
description: W tym artykule oszukujesz skrypty konfiguracji urządzeń sieci VPN dla połączeń sieci VPN s2S z bramami sieci VPN platformy Azure przy użyciu usługi Azure Resource Manager.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: f905e27f48a0bf9181625bbba07549a13d9420cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162140"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Pobieranie skryptów konfiguracji urządzeń sieci VPN dla połączeń sieci VPN S2S

W tym artykule oszukujesz skrypty konfiguracji urządzeń sieci VPN dla połączeń sieci VPN s2S z bramami sieci VPN platformy Azure przy użyciu usługi Azure Resource Manager. Na poniższym diagramie przedstawiono przepływ pracy wysokiego poziomu.

![skrypt pobierania](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Dostępne są następujące urządzenia:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>Skrypty konfiguracji urządzeń sieci VPN — informacje

Wielolokacyjny połączenie sieci VPN składa się z bramy sieci VPN platformy Azure, lokalnego urządzenia sieci VPN i tunelu sieci VPN IPsec S2S łączącego te dwa. Typowy przepływ pracy obejmuje następujące kroki:

1. Tworzenie i konfigurowanie bramy sieci VPN platformy Azure (bramy sieci wirtualnej)
2. Tworzenie i konfigurowanie bramy sieci lokalnej platformy Azure reprezentującej lokalną sieć i urządzenie sieci VPN
3. Tworzenie i konfigurowanie połączenia sieci VPN platformy Azure między bramą sieci VPN platformy Azure a bramą sieci lokalnej
4. Konfigurowanie lokalnego urządzenia sieci VPN reprezentowane przez bramę sieci lokalnej w celu ustanowienia rzeczywistego tunelu sieci VPN S2S za pomocą bramy sieci VPN platformy Azure

Kroki od 1 do 3 można wykonać za pomocą [portalu](vpn-gateway-howto-site-to-site-resource-manager-portal.md)Azure, [programu PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)lub [interfejsu wiersza polecenia.](vpn-gateway-howto-site-to-site-resource-manager-cli.md) Ostatni krok obejmuje konfigurowanie lokalnych urządzeń sieci VPN poza platformą Azure. Ta funkcja umożliwia pobranie skryptu konfiguracji urządzenia sieci VPN z odpowiednimi wartościami bramy sieci VPN platformy Azure, prefiksów adresów sieciowych i sieci lokalnej oraz właściwości połączenia sieci VPN itp. Skrypt można użyć jako punktu wyjścia lub zastosować skrypt bezpośrednio do lokalnych urządzeń sieci VPN za pośrednictwem konsoli konfiguracji.

> [!IMPORTANT]
> * Składnia dla każdego skryptu konfiguracji urządzenia sieci VPN jest inna i jest w dużym stopniu zależna od modeli i wersji oprogramowania układowego. Zwróć szczególną uwagę na informacje o modelu urządzenia i wersji w stosunku do dostępnych szablonów.
> * Niektóre wartości parametrów muszą być unikatowe na urządzeniu i nie można ich określić bez uzyskania dostępu do urządzenia. Skrypty konfiguracji generowane przez platformę Azure wstępnie wypełniają te wartości, ale musisz upewnić się, że podane wartości są prawidłowe na urządzeniu. Przykłady:
>    * Numery interfejsów
>    * Numery list kontroli dostępu
>    * Nazwy zasad lub numery itp.
> * Poszukaj słowa kluczowego "**REPLACE**", osadzonego w skrypcie, aby znaleźć parametry, które należy zweryfikować przed zastosowaniem skryptu.
> * Niektóre szablony zawierają sekcję **"CLEANUP",** którą można zastosować w celu usunięcia konfiguracji. Sekcje oczyszczania są domyślnie komentowane.

## <a name="download-the-configuration-script-from-azure-portal"></a>Pobierz skrypt konfiguracji z witryny Azure portal

Utwórz bramę sieci VPN platformy Azure, bramę sieci lokalnej i zasób połączenia łączący te dwa. Poniższa strona prowadzi użytkownika przez te kroki:

* [Tworzenie połączenia typu lokacja-lokacja w witrynie Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Po utworzeniu zasobu połączenia postępuj zgodnie z poniższymi instrukcjami, aby pobrać skrypty konfiguracji urządzenia sieci VPN:

1. W przeglądarce przejdź do [witryny Azure portal](https://portal.azure.com) i w razie potrzeby zaloguj się za pomocą konta platformy Azure
2. Przejdź do utworzonego zasobu połączenia. Listę wszystkich zasobów połączenia można znaleźć, klikając "Wszystkie usługi", następnie "NETWORKING" i "Połączenia".

    ![lista połączeń](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Kliknij połączenie, które chcesz skonfigurować.

    ![omówienie połączenia](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Kliknij link "Konfiguracja pobierania", wyróżniony na czerwono na stronie Przegląd połączenia; spowoduje otwarcie strony "Konfiguracja pobierania".

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Wybierz wersję rodziny modeli i oprogramowania układowego dla swojego urządzenia VPN, a następnie kliknij przycisk "Pobierz konfigurację".

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Zostanie wyświetlony monit o zapisanie pobranego skryptu (pliku tekstowego) w przeglądarce.
7. Po pobraniu skryptu konfiguracyjnego otwórz go za pomocą edytora tekstu i wyszukaj słowo kluczowe "ZASTĄP", aby zidentyfikować i sprawdzić parametry, które mogą wymagać wymiany.

    ![skrypt edycji](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Pobierz skrypt konfiguracji przy użyciu programu Azure PowerShell



Skrypt konfiguracji można również pobrać przy użyciu programu Azure PowerShell, jak pokazano w poniższym przykładzie:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Stosowanie skryptu konfiguracji do urządzenia sieci VPN

Po pobraniu i sprawdzeniu poprawności skryptu konfiguracyjnego następnym krokiem jest zastosowanie skryptu do urządzenia sieci VPN. Rzeczywista procedura różni się w zależności od urządzenia VPN sprawia i modeli. Zapoznaj się z instrukcjami obsługi lub stronami instrukcji dla urządzeń VPN.

## <a name="next-steps"></a>Następne kroki

Kontynuuj konfigurowanie [połączenia lokacja lokacja.](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
