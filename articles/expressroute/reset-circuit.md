---
title: 'Resetowanie obwodu nie powiodło się — ExpressRoute: Program PowerShell: Azure | Microsoft Docs'
description: Ten artykuł pomoże Ci zresetować obwodu usługi ExpressRoute, która jest w stanie niepowodzenia.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: f6fd8e5f0e5730e55fa5bbe13f74be796dd6a4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66115839"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Resetowanie obwodu usługi ExpressRoute nie powiodło się

Podczas operacji na obwód usługi ExpressRoute nie zostanie ukończone pomyślnie, obwód może przejść do stanu "failed". Ten artykuł pomoże Ci Resetowanie obwodu ExpressRoute systemu Azure nie powiodło się.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reset-a-circuit"></a>Resetowanie obwodu

1. Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-az-ps).

2. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Jeśli masz wiele subskrypcji platformy Azure, wyświetl subskrypcje dla konta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Wskaż subskrypcję, której chcesz użyć.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Uruchom następujące polecenia, aby Resetowanie obwodu, która jest w stanie niepowodzenia:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Obwód powinien być teraz w dobrej kondycji. Otwórz bilet pomocy technicznej za pomocą [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Jeśli obwód jest nadal w stanie niepowodzenia.

## <a name="next-steps"></a>Kolejne kroki

Otwórz bilet pomocy technicznej za pomocą [pomocy technicznej firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Jeśli nadal występują problemy.
