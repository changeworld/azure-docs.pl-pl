---
title: Kojarzenie zasad zapory aplikacji sieci Web z istniejącą bramą aplikacji platformy Azure
description: Dowiedz się, jak skojarzyć zasady zapory aplikacji sieci Web z istniejącą bramą aplikacji platformy Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083903"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Kojarzenie zasad WAF z istniejącą bramą aplikacji

Za pomocą programu Azure PowerShell można [utworzyć zasady WAF](create-waf-policy-ag.md), ale może już mieć bramę aplikacji i po prostu chcesz skojarzyć zasady WAF z nim. W tym artykule, to zrobić tylko to; tworzysz zasady WAF i kojarzysz ją z już istniejącą bramą aplikacji. 

1. Pobierz bramę aplikacji i zasady zapory. Jeśli nie masz istniejących zasad zapory, zobacz krok 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. (Opcjonalnie) Tworzenie zasad zapory.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Jeśli tworzysz tę Politykę WAF, aby przejść z konfigura waf do zasad WAF, polityka musi być dokładną kopią starego configu. Oznacza to, że każde wykluczenie, reguła niestandardowa, wyłączona grupa reguł itp.
3. (Opcjonalnie) Zasady WAF można skonfigurować zgodnie z potrzebami. Obejmuje to reguły niestandardowe, wyłączenie reguł/grup reguł, wykluczeń, ustawianie limitów przekazywania plików itp. Jeśli pominiesz ten krok, zostaną wybrane wszystkie wartości domyślne. 
   
4. Zapisz zasady i dołącz ją do bramy aplikacji. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o regułach niestandardowych.](configure-waf-custom-rules.md)
