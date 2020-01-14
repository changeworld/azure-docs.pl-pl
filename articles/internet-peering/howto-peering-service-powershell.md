---
title: Włączanie usługi komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell
titleSuffix: Azure
description: Włączanie usługi komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774187"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Włączanie usługi komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell

W tym artykule opisano sposób włączania [usługi komunikacji równorzędnej](overview-peering-service.md) w bezpośredniej komunikacji równorzędnej przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania Menedżer zasobów.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [portalu](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Zapoznaj się z [wymaganiami wstępnymi](prerequisites.md) przed rozpoczęciem konfiguracji.
* Wybierz bezpośrednią komunikację równorzędną w ramach subskrypcji, w której chcesz włączyć usługę komunikacji równorzędnej. Jeśli go nie masz, Przekształć starszą bezpośrednią komunikację równorzędną lub Utwórz nową bezpośrednią komunikację równorzędną.
    * Aby skonwertować starszą bezpośrednią komunikację równorzędną, postępuj zgodnie z instrukcjami w temacie [konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-direct-powershell.md).
    * Aby utworzyć nową bezpośrednią komunikację równorzędną, postępuj zgodnie z instrukcjami w temacie [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Włącz usługę komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej

### <a name= get></a>Wyświetlanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name= get></a>Włącz bezpośrednią komunikację równorzędną dla usługi Komunikacja równorzędna

Po otrzymaniu bezpośredniej komunikacji równorzędnej w poprzednim kroku Włącz ją dla usługi komunikacji równorzędnej.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modyfikowanie bezpośredniego połączenia komunikacji równorzędnej

Jeśli musisz zmodyfikować ustawienia połączenia, zapoznaj się z sekcją **Modyfikowanie bezpośredniej komunikacji równorzędnej** w temacie [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell](howto-direct-powershell.md) .

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell](howto-exchange-powershell.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu programu PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Zasoby dodatkowe
Szczegółowy opis wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```

Często zadawane pytania można znaleźć w temacie [Usługa Komunikacja równorzędna — często zadawane pytania](service-faqs.md).