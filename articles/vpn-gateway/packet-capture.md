---
title: 'Brama sieci VPN platformy Azure: konfigurowanie przechwytywania pakietów'
description: Dowiedz się więcej o funkcjach przechwytywania pakietów, których można używać w bramkach sieci VPN.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353521"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Konfigurowanie przechwytywania pakietów dla bram sieci VPN

Problemy z łącznością i wydajnością są często złożone i zajmują dużo czasu i wysiłku, aby zawęzić przyczynę problemu. Możliwość przechwytywania pakietów znacznie pomaga skrócić czas w zawężeniu zakresu problemu do niektórych części sieci, takich jak czy problem jest po stronie klienta sieci, po stronie platformy Azure sieci lub gdzieś pomiędzy. Po zawężeniu problemu jest znacznie bardziej wydajne do debugowania i podjęcia działań naprawczych.

Istnieje kilka często dostępnych narzędzi do przechwytywania pakietów. Jednak uzyskanie odpowiednich przechwytuje pakietów przy użyciu tych narzędzi jest często kłopotliwe, zwłaszcza podczas pracy z dużych scenariuszy ruchu woluminu. Funkcje filtrowania udostępniane przez przechwytywanie pakietów bramy sieci VPN stają się głównym wyróżnikiem. Oprócz powszechnie dostępnych narzędzi do przechwytywania pakietów za pomocą bramy sieci VPN można użyć przechwytywania pakietów bramy sieci VPN.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Funkcje filtrowania przechwytywania pakietów bramy sieci VPN

Przechwytywanie pakietów bramy sieci VPN można uruchamiać na bramie lub na określonym połączeniu w zależności od potrzeb klienta. Można również uruchamiać przechwytywanie pakietów w wielu tunelach w tym samym czasie. Można przechwytywać ruch jedno- lub dwukierunkowy, ruch IKE i ESP oraz pakiety wewnętrzne wraz z filtrem na bramie sieci VPN.

Użycie filtru 5 krotek (podsieci źródłowej, podsieci docelowej, portu źródłowego, portu docelowego, protokołu) i flag TCP (SYN, ACK, FIN, URG, PSH, RST) jest przydatne podczas izolowania problemów z dużym ruchem objętościowym.

Podczas uruchamiania przechwytywania pakietów można użyć tylko jednej opcji na właściwość.

## <a name="setup-packet-capture-using-powershell"></a>Przechwytywanie pakietów instalatora przy użyciu programu PowerShell

Zobacz poniższe przykłady poleceń programu PowerShell, aby uruchamiać i zatrzymywać przechwytywanie pakietów. Aby uzyskać więcej informacji na temat opcji parametrów (takich jak sposób tworzenia filtru), zobacz ten [dokument](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)programu PowerShell .

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Uruchamianie przechwytywania pakietów dla bramy sieci VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Opcjonalny parametr **-FilterData** może służyć do zastosowania filtru.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Zatrzymywanie przechwytywania pakietów dla bramy sieci VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Uruchamianie przechwytywania pakietów dla połączenia bramy sieci VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Opcjonalny parametr **-FilterData** może służyć do zastosowania filtru.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Zatrzymywanie przechwytywania pakietów w połączeniu bramy sieci VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Najważniejsze zagadnienia

- Uruchamianie przechwytywania pakietów może mieć wpływ na wydajność. Pamiętaj, aby zatrzymać przechwytywanie pakietów, gdy nie jest potrzebne.
- Sugerowany minimalny czas przechwytywania pakietów wynosi 600 sekund. Krótszy czas przechwytywania pakietów może nie dostarczyć pełnych danych z powodu synchronizacji problemów między wieloma składnikami na ścieżce.
- Pliki danych przechwytywania pakietów są generowane w formacie PCAP. Użyj Wireshark lub innych powszechnie dostępnych aplikacji, aby otworzyć pliki PCAP.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat bramy sieci VPN, zobacz [Informacje o bramie sieci VPN](vpn-gateway-about-vpngateways.md)
