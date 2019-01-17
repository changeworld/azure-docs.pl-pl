---
title: Samouczek — konfigurowanie filtrowania geograficznego domeny w usłudze Azure Front Door Service | Microsoft Docs
description: W tym samouczku dowiesz się, jak utworzyć proste zasady filtrowania geograficznego i skojarzyć je z istniejącym hostem frontonu usługi Front Door
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2018
ms.author: sharadag
ms.openlocfilehash: 68da9a0255cde6cbad5c675901c80193888bf255
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214882"
---
# <a name="how-to-set-up-a-geo-filtering-policy-for-your-front-door"></a>Jak skonfigurować zasady filtrowania geograficznego dla własnej usługi Front Door
W tym samouczku pokazano, jak utworzyć przykładowe zasady filtrowania geograficznego za pomocą programu Azure PowerShell i skojarzyć je z istniejącym hostem frontonu usługi Front Door. Te przykładowe zasady filtrowania geograficznego blokują żądania ze wszystkich pozostałych krajów z wyjątkiem Stanów Zjednoczonych.

## <a name="1-set-up-your-powershell-environment"></a>1. Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu poświadczeń platformy Azure i zainstalować usługę AzureRM.
```
# Connect to Azure with an interactive dialog for sign-in
Connect-AzureRmAccount
Install-Module -Name AzureRM
```
> [!NOTE]
>  Usługa [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) już wkrótce będzie obsługiwana.

Przed zainstalowaniem modułu usługi Front Door upewnij się, że masz zainstalowaną bieżącą wersję modułu PowerShellGet. Uruchom poniższe polecenie i ponownie otwórz program PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

Zainstaluj moduł AzureRM.FrontDoor. 

```
Install-Module -Name AzureRM.FrontDoor -AllowPrerelease
```

## <a name="2-define-geo-filtering-match-conditions"></a>2. Definiowanie warunków dopasowań filtrowania geograficznego
Najpierw utwórz przykładowy warunek dopasowania wybierający żądania, które nie pochodzą ze Stanów Zjednoczonych. Zapoznaj się z [przewodnikiem](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoormatchconditionobject) programu PowerShell, aby uzyskać informacje o parametrach używanych podczas tworzenia warunku dopasowania. Dwuliterowy kod kraju do mapowania kraju jest dostępny [tutaj](front-door-geo-filtering.md).

```
$nonUSGeoMatchCondition = New-AzureRmFrontDoorMatchConditionObject -MatchVariable RemoteAddr -OperatorProperty GeoMatch -NegateCondition $true -MatchValue "US"
```
 
## <a name="3-add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>3. Dodawanie warunku dopasowania filtrowania geograficznego do reguły za pomocą akcji i priorytetu

Następnie utwórz obiekt elementu CustomRule `nonUSBlockRule` na podstawie warunku dopasowania, akcji i priorytetu.  Element CustomRule może mieć wiele elementów MatchCondition.  W tym przykładzie ustawiono akcję blokowania i najwyższy priorytet 1.

```
$nonUSBlockRule = New-AzureRmFrontDoorCustomRuleObject -Name "geoFilterRule" -RuleType MatchRule -MatchCondition $nonUSGeoMatchCondition -Action Block -Priority 1
```

Zapoznaj się z [przewodnikiem](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorcustomruleobject) programu PowerShell, aby uzyskać informacje o parametrach używanych podczas tworzenia elementu CustomRuleObject.

## <a name="4-add-rules-to-a-policy"></a>4. Dodawanie reguł do zasad
Ten krok powoduje utworzenie obiektu zasad `geoPolicy` zawierającego element `nonUSBlockRule` z poprzedniego kroku w określonej grupie zasobów. Użyj elementu `Get-AzureRmResourceGroup`, aby znaleźć element ResourceGroupName $resourceGroup.

```
$geoPolicy = New-AzureRmFrontDoorFireWallPolicy -Name "geoPolicyAllowUSOnly" -resourceGroupName $resourceGroup -Customrule $nonUSBlockRule  -Mode Prevention -EnabledState Enabled
```

Zapoznaj się z [przewodnikiem](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorfirewallpolicy) programu PowerShell, aby uzyskać informacje o parametrach używanych podczas tworzenia zasad.

## <a name="5-link-policy-to-a-front-door-frontend-host"></a>5. Łączenie zasad z hostem frontonu usługi Front Door
Poprzednie kroki dotyczą łączenia obiektu zasad ochrony z istniejącym hostem frontonu usługi Front Door i aktualizowania właściwości usługi Front Door. Najpierw pobierz obiekt usługi Front Door przy użyciu polecenia [Get-AzureRmFrontDoor](https://docs.microsoft.com/azure/frontdoor/get-azurermfrontdoor), a następnie ustaw dla właściwości frontonu WebApplicationFirewallPolicyLink identyfikator resourceId elementu `geoPolicy`.

```
$geoFrontDoorObjectExample = Get-AzureRmFrontDoor -ResourceGroupName $resourceGroup
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Użyj poniższego [polecenia](https://docs.microsoft.com/azure/frontdoor/set-azurermfrontdoor), aby zaktualizować obiekt usługi Front Door.

```
Set-AzureRmFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Właściwość WebApplicationFirewallPolicyLink wystarczy ustawić raz, aby połączyć zasady ochrony z hostem frontonu usługi Front Door. Kolejne aktualizacje zasad będą automatycznie stosowane do hosta frontonu.

## <a name="next-steps"></a>Następne kroki

- Uzyskaj informacje o [zabezpieczeniach warstwy aplikacji w usłudze Front Door](front-door-application-security.md).
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
