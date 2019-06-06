---
title: Skrypt programu Azure PowerShell przykładowy — Tworzenie niestandardowych reguł zapory aplikacji sieci Web
description: Skrypt programu Azure PowerShell przykładowy — Tworzenie niestandardowych reguł zapory aplikacji sieci Web
author: vhorne
ms.service: application-gateway
ms.topic: sample
ms.date: 6/7/2019
ms.author: victorh
ms.openlocfilehash: ffdde80598322222e2a8f000eee8be269becdd11
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743423"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>Tworzenie niestandardowych reguł zapory aplikacji sieci Web przy użyciu programu Azure PowerShell

Ten skrypt umożliwia utworzenie aplikacji bramy zapory aplikacji sieci Web używającej reguły niestandardowe. Bloki niestandardową regułę ruchu, jeśli nagłówek żądania zawiera agenta użytkownika *evilbot*.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-powershell-module"></a>Moduł programu Azure PowerShell

Jeśli zdecydujesz się zainstalować i korzystać z programu Azure PowerShell lokalnie, ten skrypt wymaga modułu Wersja 2.1.0 programu Azure PowerShell lub nowszej.

1. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).
2. Aby utworzyć połączenie z platformą Azure, uruchom polecenie `Connect-AzAccount`.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, bramę aplikacji i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Tworzy konfigurację podsieci. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną przy użyciu konfiguracji podsieci. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP dla bramy aplikacji. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Tworzy konfigurację, która kojarzy podsieć z bramą aplikacji. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Tworzy konfigurację, która przypisuje publiczny adres IP do bramy aplikacji. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Przypisuje port używany do uzyskiwania dostępu do bramy aplikacji. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Tworzy pulę zaplecza dla bramy aplikacji. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Konfiguruje ustawienia dla puli zaplecza. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Tworzy odbiornik. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Tworzy regułę routingu. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Określa warstwę i pojemność bramy aplikacji. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Tworzy bramę aplikacji. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Tworzy konfigurację skalowania automatycznego w usłudze Application Gateway.|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Tworzy zmienną dopasowania dla warunku zapory.|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Tworzy warunek dopasowania dla reguły niestandardowej.|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Tworzy nową regułę niestandardową dla zasad zapory bramy aplikacji.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Tworzy zasady zapory bramy aplikacji.|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Tworzy konfigurację zapory aplikacji sieci Web w usłudze application gateway.|

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat niestandardowych reguł zapory aplikacji sieci Web, zobacz [niestandardowych reguł zapory aplikacji sieci Web](../custom-waf-rules-overview.md)
- Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).
- Dodatkowe przykładowe skrypty programu PowerShell dotyczące bramy aplikacji można znaleźć w [dokumentacji usługi Azure Application Gateway](../powershell-samples.md).
