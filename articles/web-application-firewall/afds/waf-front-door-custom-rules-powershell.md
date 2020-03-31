---
title: Konfigurowanie reguł niestandardowych WAF & domyślnego zestawu reguł dla drzwi frontowych platformy Azure
description: Dowiedz się, jak skonfigurować zasady WAF składają się zarówno z reguł niestandardowych, jak i zarządzanych dla istniejącego punktu końcowego drzwi frontowych.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186636"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Konfigurowanie zasad zapory aplikacji sieci Web przy użyciu programu Azure PowerShell

Zasady zapory aplikacji sieci Web platformy Azure (WAF) definiują inspekcje wymagane, gdy żądanie dociera do drzwi frontowych.
W tym artykule pokazano, jak skonfigurować zasady WAF, która składa się z niektórych reguł niestandardowych i z włączoną domyślną regułą zarządzaną platformą Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konfigurowania zasad limitu szybkości należy skonfigurować środowisko programu PowerShell i utworzyć profil drzwi frontowych.

### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell

Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu poświadczeń platformy Azure i zainstalować moduł Az PowerShell.

#### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

```
Connect-AzAccount

```
Przed zainstalowaniem modułu usługi Front Door upewnij się, że masz zainstalowaną bieżącą wersję modułu PowerShellGet. Uruchom poniższe polecenie i ponownie otwórz program PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalowanie modułu Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Tworzenie profilu drzwi przednich

Tworzenie profilu drzwi przednich, postępując zgodnie z instrukcjami opisanymi w [przewodniku Szybki start: Tworzenie profilu drzwi przednich](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Reguła niestandardowa oparta na parametrach http

W poniższym przykładzie pokazano, jak skonfigurować regułę niestandardową z dwoma warunkami dopasowania przy użyciu [new-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Żądania pochodzą z określonej witryny zdefiniowanej przez stronę odsyłającą, a ciąg zapytania nie zawiera "hasła". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Reguła niestandardowa oparta na metodzie żądania http

Utwórz metodę blokowania reguły "PUT" przy użyciu [metody New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) w następujący sposób:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Tworzenie reguły niestandardowej na podstawie ograniczenia rozmiaru

Poniższy przykład tworzy żądania blokowania reguły z adresem URL, który jest dłuższy niż 100 znaków przy użyciu programu Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Dodaj zarządzany domyślny zestaw reguł

Poniższy przykład tworzy zarządzany domyślny zestaw reguł przy użyciu programu Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Konfigurowanie zasad zabezpieczeń

Znajdź nazwę grupy zasobów zawierającej profil Drzwiami frontowymi za pomocą programu `Get-AzResourceGroup`. Następnie skonfiguruj zasady zabezpieczeń z utworzonymi regułami w poprzednich krokach przy użyciu [funkcji New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) w określonej grupie zasobów zawierającej profil drzwiami frontowymi.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Połącz zasady z hostem frontu drzwi frontowych

Połącz obiekt zasad zabezpieczeń z istniejącym hostem frontu drzwi frontowych i zaktualizuj właściwości drzwi frontowych. Najpierw pobierz obiekt Drzwi frontowe za pomocą [funkcji Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Następnie ustaw właściwość *Front-End WebApplicationFirewallPolicyLink* na *identyfikator resourceId* "$myWAFPolicy$" utworzoną w poprzednim kroku przy użyciu [funkcji Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

W poniższym przykładzie użyto nazwy grupy zasobów *myResourceGroupFD1* przy założeniu, że profil drzwi frontowych został utworzony przy użyciu instrukcji podanych w artykule [Szybki start: Tworzenie drzwi przednich.](../../frontdoor/quickstart-create-front-door.md) Ponadto w poniższym przykładzie zastąp $frontDoorName nazwą profilu drzwiami frontowymi. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Wystarczy tylko raz ustawić właściwość *WebApplicationFirewallPolicyLink,* aby połączyć zasady zabezpieczeń z frontonem drzwi frontowych. Kolejne aktualizacje zasad są automatycznie stosowane do front-endu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [drzwiach frontowych](../../frontdoor/front-door-overview.md) 
- Dowiedz się więcej o [WAF z drzwiami przednimi](afds-overview.md)
