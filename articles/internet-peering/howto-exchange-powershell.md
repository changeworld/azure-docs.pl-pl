---
title: Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell
titleSuffix: Azure
description: Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774408"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell

W tym artykule opisano sposób tworzenia komunikacji równorzędnej programu Microsoft Exchange przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania Menedżer zasobów. W tym artykule przedstawiono również sposób sprawdzania stanu zasobu, aktualizowania go lub usuwania i anulowania aprowizacji.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [portalu](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) i wskazówki dotyczące [komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md) przed rozpoczęciem konfiguracji.
* Jeśli masz już połączenie komunikacji równorzędnej programu Exchange z firmą Microsoft, które nie zostały przekonwertowane na zasoby platformy Azure, zapoznaj się z artykułem [konwertowanie starszej komunikacji równorzędnej programu Exchange do zasobu platformy Azure przy użyciu programu PowerShell](howto-legacy-exchange-powershell.md)

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Tworzenie i Inicjowanie obsługi komunikacji równorzędnej programu Exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=exchange-location></a>Pobierz listę obsługiwanych lokalizacji komunikacji równorzędnej dla komunikacji równorzędnej programu Exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name=create></a>Tworzenie komunikacji równorzędnej programu Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name=get></a>Pobieranie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify"></a>Modyfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name=delete></a>Anulowanie aprowizacji komunikacji równorzędnej programu Exchange

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell](howto-direct-powershell.md)
* [Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Zasoby dodatkowe
Szczegółowy opis wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md)
