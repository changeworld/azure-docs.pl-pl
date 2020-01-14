---
title: Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell
titleSuffix: Azure
description: Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ba41f4ad8014ba3e85174b7c32e11394f0068643
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775019"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-powershell"></a>Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell

W tym artykule opisano sposób konwertowania istniejącej starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu poleceń cmdlet programu PowerShell.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [portalu](howto-legacy-direct-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) i [bezpośredni Przewodnik komunikacji równorzędnej](walkthrough-direct-all.md) przed rozpoczęciem konfiguracji.

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>Pobieranie starszej bezpośredniej komunikacji równorzędnej na potrzeby konwersji
Poniżej znajduje się przykład uzyskiwania starszej bezpośredniej komunikacji równorzędnej w lokalizacji komunikacji równorzędnej w Seattle

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Poniżej znajduje się Przykładowa odpowiedź:
```powershell
Name                       :
Sku                        : Basic_Direct_Free
Kind                       : Direct
PeeringLocation            : Seattle
UseForPeeringService       : False
PeerAsn.Id                 :
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.71.156.72/30
PeerSessionIPv4Address     : 4.71.156.73
MicrosoftIPv4Address       : 4.71.156.74
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:2100::1e10/126
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.68.70.140/30
PeerSessionIPv4Address     : 4.68.70.141
MicrosoftIPv4Address       : 4.68.70.142
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:4:3::cc/126
PeerSessionIPv6Address     : 2001:1900:4:3::cd
MicrosoftIPv6Address       : 2001:1900:4:3::ce
SessionStateV6             : Established
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
ProvisioningState          : Succeeded
```

### <a name="convert-legacy-direct-peering"></a>Konwertuj starszą bezpośrednią komunikację równorzędną

&nbsp;
> [!IMPORTANT]
> Należy pamiętać, że podczas konwertowania starszej komunikacji równorzędnej na zasób platformy Azure modyfikacje nie są obsługiwane. &nbsp;

Użyj poniższego polecenia, aby skonwertować starszą bezpośrednią komunikację równorzędną do zasobu platformy Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Poniżej znajduje się Przykładowa odpowiedź:

```powershell
Name                 : SeattleDirectPeering
Sku.Name             : Basic_Direct_Free
Kind                 : Direct
Connections          : {11, 11}
PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{asnNumber}
UseForPeeringService : False
PeeringLocation      : Seattle
ProvisioningState    : Succeeded
Location             : centralus
Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
Type                 : Microsoft.Peering/peerings
Tags                 : {}
```

## <a name="additional-resources"></a>Zasoby dodatkowe
Szczegółowy opis wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell](howto-direct-powershell.md).
