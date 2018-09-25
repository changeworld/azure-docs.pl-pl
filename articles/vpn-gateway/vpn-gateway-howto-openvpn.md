---
title: 'Jak skonfigurować OpenVPN bramy sieci VPN platformy Azure: PowerShell | Dokumentacja firmy Microsoft'
description: Kroki, aby skonfigurować OpenVPN dla bramy sieci VPN platformy Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: b915a56b14332bfa885eaccf9a151d08c58dc5b1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973698"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Konfigurowanie OpenVPN Azure point-to-site VPN Gateway (wersja zapoznawcza)

Ten artykuł pomoże Ci skonfigurować OpenVPN w usłudze Azure VPN Gateway. Tego artykułu przyjęto założenie, iż już środowisku point-to-site pracy. Jeśli tego nie zrobisz, użyj instrukcji w kroku 1, aby utworzyć sieci VPN punkt lokacja.

> [!IMPORTANT]
> Publiczna wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie należy korzystać z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="vnet"></a>1. Tworzenie sieci VPN punkt lokacja

Jeśli nie masz już działający środowiska point-to-site, postępuj zgodnie z instrukcjami Aby utworzyć jeden. Zobacz [tworzenie sieci VPN punkt lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md) do tworzenia i konfigurowania bramy sieci VPN typu punkt lokacja przy użyciu uwierzytelniania certyfikatu platformy Azure natywnych.

## <a name="cmdlets"></a>2. Instalowanie poleceń cmdlet programu PowerShell

Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Resource Manager. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). Jest to ważne, ponieważ wcześniejsze wersje poleceń cmdlet nie zawierają bieżących wartości potrzebnych w tym ćwiczeniu.

## <a name="enable"></a>3. Włącz OpenVPN bramy

Włącz OpenVPN na bramie. Upewnij się, że brama została już skonfigurowana do punkt lokacja (protokół IKEv2 lub SSTP) przed uruchomieniem poniższych poleceń:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Kolejne kroki

Aby skonfigurować klientów OpenVPN, zobacz [klientom Konfigurowanie OpenVPN](vpn-gateway-howto-openvpn-clients.md).