---
title: Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu programu PowerShell
titleSuffix: Azure
description: Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775396"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu programu PowerShell

W tym artykule opisano sposób konwertowania istniejącej starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu poleceń cmdlet programu PowerShell.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą [portalu](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przed rozpoczęciem konfiguracji zapoznaj [się z wymaganiami wstępnymi](prerequisites.md) i [instruktażem komunikacji równorzędnej programu Exchange.](walkthrough-exchange-all.md)

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Konwertowanie starszej komunikacji równorzędnej z punktem wymiany na zasób platformy Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się na swoje konto platformy Azure i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Uzyskaj starszą komunikację równorzędnych programu Exchange dla konwersji
Poniżej znajduje się przykład, aby uzyskać starsze exchange komunikacji równorzędnej w Seattle lokalizacji komunikacji równorzędnej:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

Odpowiedź wygląda podobnie do następującego przykładu:
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>Konwertowanie starszej komunikacji równorzędnej
Poniższe polecenie może służyć do konwersji starszych komunikacji równorzędnej programu Exchange do zasobu platformy Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Należy zauważyć, że podczas konwertowania starszej komunikacji równorzędnej na zasób platformy Azure modyfikacje nie są obsługiwane&nbsp;

Poniżej znajduje się przykład odpowiedzi po pomyślnym zakończeniu inicjowania obsługi administracyjnej end-to-end:

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>Zasoby dodatkowe
Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```
Aby uzyskać więcej informacji, odwiedź [najczęściej zadawane pytania dotyczące komunikacji internet](faqs.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell](howto-exchange-powershell.md)
