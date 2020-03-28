---
title: Samouczek — konfigurowanie zasad WAF filtrowania geograficznego — drzwi frontowe platformy Azure
description: W tym samouczku dowiesz się, jak utworzyć zasady filtrowania geograficznego i skojarzyć zasady z istniejącym hostem frontendu drzwi frontowych
services: frontdoor
documentationcenter: ''
author: teresayao
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: tyao
ms.openlocfilehash: e3119745e35140d0344d25f34f54b63939d2542d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79471459"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Jak skonfigurować zasady filtrowania geograficznego zapory aplikacji internetowej dla własnej usługi Front Door
W tym samouczku pokazano, jak utworzyć przykładowe zasady filtrowania geograficznego za pomocą programu Azure PowerShell i skojarzyć je z istniejącym hostem frontonu usługi Front Door. Ta przykładowa zasada filtrowania geograficznego będzie blokować żądania ze wszystkich innych krajów/regionów z wyjątkiem Stanów Zjednoczonych.

Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad filtrowania geograficznego należy skonfigurować środowisko programu PowerShell i utworzyć profil drzwiach frontowych.
### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu poświadczeń platformy Azure i zainstalować moduł Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Łączenie się z platformą Azure za pomocą interaktywnego okna dialogowego logowania
```
Install-Module -Name Az
Connect-AzAccount
```
Upewnij się, że masz zainstalowaną bieżącą wersję programu PowerShellGet. Uruchom poniższe polecenie i ponownie otwórz program PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Instalowanie modułu Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Tworzenie profilu drzwi przednich
Utwórz profil drzwi przednich, postępując zgodnie z instrukcjami opisanymi w [przewodniku Szybki start: Utwórz profil drzwi przednich](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definiowanie warunku dopasowania filtrowania geograficznego

Utwórz warunek dopasowania przykładowego, który wybiera żądania nie pochodzące z "US" przy użyciu [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) na parametry podczas tworzenia warunku dopasowania. W [tym miejscu](front-door-geo-filtering.md)znajdują się dwuliterowe kody krajów do mapowania kraju.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Dodawanie warunku dopasowania filtrowania geograficznego do reguły za pomocą akcji i priorytetu

Utwórz obiekt `nonUSBlockRule` CustomRule na podstawie warunku dopasowania, akcji i priorytetu przy użyciu [funkcji New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Element CustomRule może mieć wiele elementów MatchCondition.  W tym przykładzie ustawiono akcję blokowania i najwyższy priorytet 1.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Dodawanie reguł do zasad
Znajdź nazwę grupy zasobów zawierającej profil Drzwiami frontowymi za pomocą programu `Get-AzResourceGroup`. Następnie utwórz `geoPolicy` obiekt zasad `nonUSBlockRule` zawierający przy użyciu [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) w określonej grupie zasobów, która zawiera profil drzwi frontowych. Należy podać unikatową nazwę dla zasad filtrowania geograficznego. 

W poniższym przykładzie użyto nazwy grupy zasobów *myResourceGroupFD1* przy założeniu, że profil drzwi frontowych został utworzony przy użyciu instrukcji podanych w artykule [Szybki start: Tworzenie drzwi frontowych.](quickstart-create-front-door.md) W poniższym przykładzie zastąp nazwę zasad *geoPolicyAllowUSOnly* unikatową nazwą zasad.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Łączenie zasad WAF z hostem frontendu drzwi frontowych
Połącz obiekt zasad WAF z istniejącym hostem frontendu drzwi frontowych i zaktualizuj właściwości drzwiami frontowymi. 

Aby to zrobić, najpierw pobierz obiekt Drzwi frontowych za pomocą [get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Następnie ustaw właściwość WebApplicationFirewallPolicyLink na resourceId `geoPolicy`używającej [funkcji Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Wystarczy tylko raz ustawić właściwość WebApplicationFirewallPolicyLink, aby połączyć zasady WAF z hostem frontendu drzwi frontowych. Kolejne aktualizacje zasad są automatycznie stosowane do hosta frontendu.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [zaporze aplikacji sieci Web platformy Azure](waf-overview.md).
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
