---
title: Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell
titleSuffix: Azure
description: Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774239"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell

W tym artykule opisano sposób tworzenia bezpośredniej komunikacji równorzędnej firmy Microsoft przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania Menedżer zasobów. W tym artykule przedstawiono również sposób sprawdzania stanu zasobu, aktualizowania go lub usuwania i anulowania aprowizacji.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [portalu](howto-direct-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) i [bezpośredni Przewodnik komunikacji równorzędnej](walkthrough-direct-all.md) przed rozpoczęciem konfiguracji.
* Jeśli masz już bezpośrednią komunikację równorzędną z firmą Microsoft, która nie jest konwertowana na zasoby platformy Azure, zapoznaj się z artykułem [konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-direct-powershell.md) .

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Tworzenie i obsługa bezpośredniej komunikacji równorzędnej

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=direct-location></a>Pobierz listę obsługiwanych lokalizacji komunikacji równorzędnej dla bezpośredniej komunikacji równorzędnej
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name=create></a>Tworzenie bezpośredniej komunikacji równorzędnej
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name=get></a>Weryfikowanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify"></a>Modyfikowanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="delete"></a>Anulowanie aprowizacji bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell](howto-exchange-powershell.md).
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu programu PowerShell](howto-legacy-exchange-powershell.md).

## <a name="additional-resources"></a>Zasoby dodatkowe
Szczegółowy opis wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md)
