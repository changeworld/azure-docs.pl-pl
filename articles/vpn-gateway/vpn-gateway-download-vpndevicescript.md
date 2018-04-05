---
title: 'Pobierz skrypty do konfiguracji urządzenia sieci VPN dla połączeń sieci VPN S2S: usługi Azure Resource Manager | Dokumentacja firmy Microsoft'
description: Ten artykuł przeprowadzi Cię przez pobierane skrypty do konfiguracji urządzenia sieci VPN dla połączeń sieci VPN S2S bramy sieci VPN platformy Azure przy użyciu usługi Azure Resource Manager.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: yushwang
ms.openlocfilehash: 254f5012bfbf827aebc20d90405636dcb204193c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Pobierz skrypty do konfiguracji urządzenia sieci VPN dla połączeń sieci VPN S2S

Ten artykuł przeprowadzi Cię przez pobierane skrypty do konfiguracji urządzenia sieci VPN dla połączeń sieci VPN S2S bramy sieci VPN platformy Azure przy użyciu usługi Azure Resource Manager. Na poniższym diagramie przedstawiono ogólny przepływ pracy.

![Pobieranie skryptu](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Następujące urządzenia mają dostępnych skryptów:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about"></a>Temat skryptów konfiguracji urządzenia sieci VPN

Połączenia sieci VPN między lokalizacjami składa się z bramy sieci VPN platformy Azure, lokalnego urządzenia sieci VPN i tunel VPN S2S protokołu IPsec, połączone. Przepływ pracy typowy obejmuje następujące kroki:

1. Tworzenie i konfigurowanie sieci VPN platformy Azure bramy sieci wirtualnej
2. Tworzenie i konfigurowanie bramy sieci lokalnej platformy Azure, która reprezentuje lokalną sieć i urządzenia sieci VPN
3. Tworzenie i konfigurowanie połączenia sieci VPN platformy Azure między bramą sieci VPN platformy Azure i Brama sieci lokalnej
4. Konfigurowanie lokalnego urządzenia sieci VPN reprezentowany przez bramę sieci lokalnej, aby ustanowić rzeczywiste tunel S2S VPN z bramą sieci VPN platformy Azure

Można wykonać kroki od 1 do 3 za pomocą platformy Azure [portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md), lub [interfejsu wiersza polecenia](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Ostatni krok obejmuje Konfigurowanie lokalnego urządzenia sieci VPN poza platformą Azure. Ta funkcja umożliwia pobieranie skryptu konfiguracji urządzenia sieci VPN z odpowiednimi wartościami bramy sieci VPN platformy Azure, sieci wirtualnej i prefiksy adresów sieci lokalnej i właściwości połączenia sieci VPN, itp. już wypełnione. Możesz użyć skryptu jako punkt początkowy lub zastosować skrypt bezpośrednio do lokalnego urządzenia sieci VPN za pomocą konsoli programu configuration.

> [!IMPORTANT]
> * Składnia dla każdego skryptu konfiguracji urządzenia sieci VPN jest inny i stopniu zależą od modeli i wersji oprogramowania układowego. Należy zwrócić szczególną uwagę na Twoje urządzenie modelu i informacje o wersji przed dostępnych szablonów.
> * Niektóre wartości parametru musi być unikatowa na urządzeniu i nie można ustalić bez uzyskiwania dostępu do urządzenia. Skrypty do konfiguracji generowany Azure wstępnie wypełnić te wartości, ale należy upewnić się, że podane wartości są prawidłowe na urządzeniu. Przykłady:
>    * Numery — interfejs
>    * Numery listy kontroli dostępu
>    * Nazwy zasad lub cyfr, itp.
> * Wyszukaj słowo kluczowe "**Zastąp**" osadzony w skrypcie można znaleźć parametrów, należy sprawdzić przed zastosowaniem skryptu.
> * Niektóre szablony obejmują "**oczyszczania**" sekcji można zastosować do usunięcia konfiguracji. Domyślnie opatrzony sekcje oczyszczania.

## <a name="download-the-configuration-script-from-azure-portal"></a>Pobieranie skryptu konfiguracji z portalu Azure

Tworzenie bramy sieci VPN platformy Azure, Brama sieci lokalnej i zasobu połączenia, połączenie dwóch. Następująca strona prowadzi użytkownika przez kroki:

* [Utwórz połączenie lokacja-lokacja w portalu Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Po utworzeniu zasobu połączenia, postępuj zgodnie z instrukcjami poniżej, aby pobrać skrypty do konfiguracji urządzenia sieci VPN:

1. W przeglądarce przejdź do [portalu Azure](http://portal.azure.com) i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure
2. Przejdź do zasobu połączenia, który został utworzony. Listę wszystkich zasobów połączenia można znaleźć klikając "Wszystkie usługi", a następnie "Sieć" i "Połączeń."

    ![Lista połączeń](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Kliknij połączenie, które chcesz skonfigurować.

    ![połączenie — omówienie](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Kliknij łącze "Pobierz konfiguracji" jako wyróżnione na czerwono na stronie przeglądu połączenia; Spowoduje to otwarcie strony "Pobieranie konfiguracji".

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Wybierz wersję modelu rodziny i oprogramowanie układowe urządzenia sieci VPN, a następnie kliknij przycisk "Pobierz configuration".

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Zostanie wyświetlony monit zapisać pobranego skryptu (plik tekstowy) w przeglądarce.
7. Pobrany skryptu konfiguracji, otwórz go Edytor tekstu i wyszukaj słowo kluczowe "REPLACE" zidentyfikować i sprawdź, czy parametry, które mogą wymagać wymiany.

    ![Edycja skryptu](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Pobieranie skryptu konfiguracji przy użyciu programu Azure PowerShell

Możesz również pobrać skrypt konfiguracji przy użyciu programu Azure PowerShell, jak pokazano w poniższym przykładzie:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Dotyczą skryptu konfiguracji urządzenia sieci VPN

Po pobraniu i sprawdzić poprawności skryptu konfiguracji, następnym krokiem jest dotyczą skryptu urządzenia sieci VPN. Rzeczywiste procedura zależy od modele i sprawia, że urządzenia sieci VPN. Poszukaj stron instrukcji lub podręczniki operacji urządzenia sieci VPN.

## <a name="next-steps"></a>Kolejne kroki

Kontynuować konfigurowanie Twojego [połączenie lokacja-lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md).