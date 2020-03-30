---
title: Tworzenie lub modyfikowanie komunikacji równorzędnej za pomocą programu PowerShell
titleSuffix: Azure
description: Tworzenie lub modyfikowanie komunikacji równorzędnej za pomocą programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774239"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Tworzenie lub modyfikowanie komunikacji równorzędnej za pomocą programu PowerShell

W tym artykule opisano sposób tworzenia komunikacji równorzędnej microsoft direct przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania Menedżera zasobów. W tym artykule pokazano również, jak sprawdzić stan zasobu, zaktualizować go lub usunąć i anulować jego aprowizowanie.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą [portalu](howto-direct-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przed rozpoczęciem konfiguracji zapoznaj [się z wymaganiami wstępnymi](prerequisites.md) i [bezpośrednią komunikacją równorzędnej.](walkthrough-direct-all.md)
* W przypadku, gdy masz już bezpośrednią komunikację równorzędną z firmą Microsoft, która nie jest konwertowana na zasoby platformy Azure, zapoznaj się [z poleceniem Konwertuj starszy bezpośrednią komunikację równorzędną na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-direct-powershell.md)

### <a name="working-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Tworzenie i inicjowanie obsługi administracyjnej komunikacji równorzędnej

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się na swoje konto platformy Azure i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Pobierz listę obsługiwanych lokalizacji komunikacji równorzędnej dla komunikacji bezpośredniej komunikacji równorzędnej
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Tworzenie komunikacji równorzędnej bezpośredniej
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Weryfikowanie komunikacji bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modyfikowanie komunikacji równorzędnej bezpośredniej
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Anulowanie obsługi administracyjnej funkcji bezpośrednie równorzędne
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell](howto-exchange-powershell.md).
* [Konwertuj starszą usługę Exchange w komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-exchange-powershell.md).

## <a name="additional-resources"></a>Zasoby dodatkowe
Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```

Aby uzyskać więcej informacji, odwiedź [najczęściej zadawane pytania dotyczące komunikacji internet](faqs.md)
