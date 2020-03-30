---
title: 'Resetowanie nieudanego obwodu — usługa ExpressRoute: PowerShell: Azure | Dokumenty firmy Microsoft'
description: Ten artykuł ułatwia zresetowanie obwodu usługi ExpressRoute, który jest w stanie awarii.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: deeb1c65cae7e3a5b42230dbda1ad8efa717ba0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73748105"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Resetowanie błędnego obwodu usługi ExpressRoute

Jeśli operacja na obwodzie usługi ExpressRoute nie zakończy się pomyślnie, obwód może przejść do stanu "nie powiodło się". Ten artykuł ułatwia zresetowanie nieudanego obwodu usługi Azure ExpressRoute.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

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
5. Uruchom następujące polecenia, aby zresetować obwód, który jest w stanie awarii:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Obwód powinien być teraz zdrowy. Otwórz bilet pomocy technicznej z [pomocą techniczną firmy Microsoft,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) jeśli obwód jest nadal w stanie awarii.

## <a name="next-steps"></a>Następne kroki

Otwórz bilet pomocy technicznej z [pomocą techniczną firmy Microsoft,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) jeśli nadal występują problemy.
