---
title: Włączanie usługi komunikacji równorzędnej w komunikacji bezpośredniej przy użyciu programu PowerShell
titleSuffix: Azure
description: Włączanie usługi komunikacji równorzędnej w komunikacji bezpośredniej przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774187"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Włączanie usługi komunikacji równorzędnej w komunikacji bezpośredniej przy użyciu programu PowerShell

W tym artykule opisano sposób [włączania usługi komunikacji równorzędnej](overview-peering-service.md) w bezpośredniej komunikacji równorzędnej przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania Menedżera zasobów.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą [portalu](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) przed rozpoczęciem konfiguracji.
* Wybierz bezpośrednią komunikację równorzędną w subskrypcji, na której chcesz włączyć usługę komunikacji równorzędnej. Jeśli go nie masz, przekonwertuj starszą bezpośrednią komunikację równorzędną lub utwórz nową bezpośrednią komunikację równorzędną.
    * Aby przekonwertować starszą komunikację równorzędną bezpośrednią, postępuj zgodnie z instrukcjami w [obszarze Konwertuj starszy bezpośrednią komunikację równorzędną na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-direct-powershell.md).
    * Aby utworzyć nową komunikację równorzędną bezpośrednią, postępuj zgodnie z instrukcjami w [programie Tworzenie lub modyfikowanie komunikacji bezpośredniej równorzędnej przy użyciu programu PowerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Włączanie usługi Peering Service w bezpośredniej komunikacji równorzędnej

### <a name="view-direct-peering"></a><a name= get></a>Wyświetl bezpośrednią komunikację równorzędnych
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Włączanie usługi komunikacji równorzędnej bezpośredniej dla komunikacji równorzędnej

Po uzyskaniu bezpośredniego komunikacji równorzędnej w poprzednim kroku włącz go dla usługi komunikacji równorzędnej.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modyfikowanie bezpośredniego połączenia równorzędnego

Jeśli chcesz zmodyfikować ustawienia połączenia, zapoznaj się **z sekcją Modyfikowanie bezpośredniej komunikacji równorzędnej** w [obszarze Tworzenie lub modyfikowanie komunikacji bezpośredniej przy użyciu programu PowerShell](howto-direct-powershell.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell](howto-exchange-powershell.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Zasoby dodatkowe
Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```

Aby zapoznać się z często zadawanymi pytaniami, zobacz [Często zadawane pytania dotyczące usługi komunikacji równorzędnej.](service-faqs.md)