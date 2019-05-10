---
title: Samouczek — Konfigurowanie filtrowania geograficznego sieci web aplikacji zasady zapory dla usługi Azure drzwiami frontowymi
description: W tym samouczku dowiesz się, jak utworzyć proste zasady filtrowania geograficznego i skojarzyć je z istniejącym hostem frontonu usługi Front Door
services: frontdoor
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: af1846f66996ded553a95188df958e9592ec68a2
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523788"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Jak skonfigurować zasady filtrowania geograficznego zapory aplikacji sieci Web dla usługi drzwi
W tym samouczku pokazano, jak utworzyć przykładowe zasady filtrowania geograficznego za pomocą programu Azure PowerShell i skojarzyć je z istniejącym hostem frontonu usługi Front Door. Te zasady filtrowania geograficznego próbka będzie blokować żądania od wszystkich innych krajach/regionach z wyjątkiem Stanów Zjednoczonych.

Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad filtr geograficzny służą do konfigurowania środowiska PowerShell i tworzenie profilu drzwi wejściowe.
### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu swoich poświadczeń platformy Azure, a następnie zainstalować moduł Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Łączenie z platformą Azure za pomocą interakcyjne okno dialogowe logowania
```
Connect-AzAccount
Install-Module -Name Az
```
Upewnij się, że masz bieżącą wersję zainstalowanego modułu PowerShellGet. Uruchom poniższe polecenie i ponownie otwórz program PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Zainstaluj moduł Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Utwórz profil drzwi
Utwórz profil drzwiami frontowymi, wykonując instrukcje opisane w [Szybki Start: Utwórz profil drzwiami frontowymi](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Zdefiniuj filtrowania geograficznego warunek dopasowania

Utwórz warunek dopasowania przykładowe wybierające żądań, które nie pochodzą z "PL" przy użyciu [AzFrontDoorWafMatchConditionObject nowy](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) na parametry podczas tworzenia warunków dopasowania. Znajdują się dwie litery numerów kierunkowych krajów do mapowania kraju [tutaj](front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Dodawanie warunku dopasowania filtrowania geograficznego do reguły za pomocą akcji i priorytetu

Utwórz obiekt elementu CustomRule `nonUSBlockRule` na podstawie warunków dopasowania, akcję i priorytetu przy użyciu [New AzFrontDoorCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Element CustomRule może mieć wiele elementów MatchCondition.  W tym przykładzie ustawiono akcję blokowania i najwyższy priorytet 1.

```
$nonUSBlockRule = New-AzFrontDoorCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Dodawanie reguł do zasad
Znajdowanie nazwy grupy zasobów, która zawiera, przy użyciu profilu drzwiami frontowymi `Get-AzResourceGroup`. Następnie należy utworzyć `geoPolicy` zasad obiekt zawierający `nonUSBlockRule` przy użyciu [New AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) w grupie określonego zasobu, który zawiera profil drzwi wejściowe. Należy podać unikatową nazwę zasad geograficznej. 

Poniższym przykładzie używa nazwy grupy zasobów *myResourceGroupFD1* przy założeniu, że utworzono drzwiami frontowymi profilu przy użyciu instrukcji podanych w [Szybki Start: Utwórz drzwiami frontowymi](quickstart-create-front-door.md) artykułu. W poniższym przykładzie Zastąp nazwę zasady *geoPolicyAllowUSOnly* z unikatowa nazwa zasad.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Zasady łącze zapory aplikacji sieci Web hosta frontonu drzwi
Łącze obiektu zasad zapory aplikacji sieci Web do istniejącego hosta frontonu drzwiami frontowymi i aktualizować drzwiami frontowymi właściwości. 

W tym celu należy najpierw pobrać drzwiami frontowymi obiektu za pomocą [Get AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Następnie ustaw właściwość WebApplicationFirewallPolicyLink frontonu resourceId z `geoPolicy`przy użyciu [AzFrontDoor zestaw](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Należy ustawić właściwość WebApplicationFirewallPolicyLink raz połączyć zasadę zapory aplikacji sieci Web z hosta serwera sieci Web drzwiami frontowymi. Zasady kolejne aktualizacje są automatycznie stosowane do hosta serwera sieci Web.

## <a name="next-steps"></a>Kolejne kroki

- Uzyskaj informacje o [zabezpieczeniach warstwy aplikacji w usłudze Front Door](front-door-application-security.md).
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
