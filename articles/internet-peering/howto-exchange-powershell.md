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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774408"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell

W tym artykule opisano sposób tworzenia komunikacji równorzędnej programu Microsoft Exchange przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania Menedżera zasobów. W tym artykule pokazano również, jak sprawdzić stan zasobu, zaktualizować go lub usunąć i anulować jego aprowizowanie.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą [portalu](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przed rozpoczęciem konfiguracji zapoznaj [się z wymaganiami wstępnymi](prerequisites.md) i [instruktażem komunikacji równorzędnej programu Exchange.](walkthrough-exchange-all.md)
* Jeśli masz już komunikację równorzędną programu Exchange z firmą Microsoft, która nie jest konwertowana na zasoby platformy Azure, zapoznaj się [z temat Convert a legacy Exchange peering to Azure resource using PowerShell](howto-legacy-exchange-powershell.md)

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Tworzenie i inicjowanie obsługi administracyjnej komunikacji równorzędnej programu Exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się na swoje konto platformy Azure i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Pobierz listę obsługiwanych lokalizacji komunikacji równorzędnej dla komunikacji równorzędnej programu Exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Tworzenie komunikacji równorzędnej programu Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Uzyskaj komunikację równorzędnie programu Exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modyfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Anulowanie obsługi administracyjnej komunikacji równorzędnej programu Exchange

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej za pomocą programu PowerShell](howto-direct-powershell.md)
* [Konwertowanie starszego bezpośredniego równorzędnego zasobu na platformę Azure przy użyciu programu PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Zasoby dodatkowe
Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```

Aby uzyskać więcej informacji, odwiedź [najczęściej zadawane pytania dotyczące komunikacji internet](faqs.md)
