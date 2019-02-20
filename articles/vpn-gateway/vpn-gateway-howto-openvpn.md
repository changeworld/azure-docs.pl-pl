---
title: 'Jak skonfigurować OpenVPN w usłudze Azure VPN Gateway: PowerShell| Microsoft Docs'
description: Kroki, aby skonfigurować OpenVPN dla bramy sieci VPN platformy Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 50a8c30831ba806d0ea02d4f67b4e672e71e6325
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415089"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Konfigurowanie OpenVPN Azure point-to-site VPN Gateway (wersja zapoznawcza)

Ten artykuł pomoże Ci skonfigurować OpenVPN w usłudze Azure VPN Gateway. Tego artykułu przyjęto założenie, iż już środowisku point-to-site pracy. Jeśli tego nie zrobisz, użyj instrukcji w kroku 1, aby utworzyć sieci VPN punkt lokacja.

> [!IMPORTANT]
> Publiczna wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie należy korzystać z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register"></a>Rejestrowanie tej funkcji

Kliknij przycisk **TryIt** w ramach tej procedury, aby zarejestrować tę funkcję za pomocą usługi Azure Cloud Shell.

>[!NOTE]
>Jeżeli nie zarejestrujesz tej funkcji, nie będzie można z niego korzystać.
>

Po kliknięciu pozycji **Wypróbuj** w celu otwarcia usługi Azure Cloud Shell skopiuj i wklej następujące polecenia:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Gdy funkcja będzie widoczna jako zarejestrowana, ponownie zarejestruj subskrypcję w przestrzeni nazw Microsoft.Network.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Tworzenie sieci VPN punkt lokacja

Jeśli nie masz już działający środowiska point-to-site, postępuj zgodnie z instrukcjami Aby utworzyć jeden. Zobacz [tworzenie sieci VPN punkt lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md) do tworzenia i konfigurowania bramy sieci VPN typu punkt lokacja przy użyciu uwierzytelniania certyfikatu platformy Azure natywnych. 

> [!IMPORTANT]
> Podstawowa jednostka SKU nie jest obsługiwana dla OpenVPN.

## <a name="cmdlets"></a>2. Instalowanie poleceń cmdlet programu PowerShell

Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Resource Manager. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). Jest to ważne, ponieważ wcześniejsze wersje poleceń cmdlet nie zawierają bieżących wartości potrzebnych w tym ćwiczeniu.

## <a name="enable"></a>3. Włącz OpenVPN bramy

Włącz OpenVPN na bramie. Upewnij się, że brama została już skonfigurowana do punkt lokacja (protokół IKEv2 lub SSTP) przed uruchomieniem poniższych poleceń:

```powershell
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Kolejne kroki

Aby skonfigurować klientów OpenVPN, zobacz [klientom Konfigurowanie OpenVPN](vpn-gateway-howto-openvpn-clients.md).
