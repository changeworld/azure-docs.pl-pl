---
title: Skonfiguruj zasady umożliwiającymi zainstalowanie zapory aplikacji sieci web za pomocą reguł niestandardowych i domyślnego zestawu Ruse na wejściu — Azure PowerShell
description: Informacje o sposobie konfigurowania zapory aplikacji sieci Web zasady składają się z zarządzanych i niestandardowych reguł dla istniejącego punktu końcowego drzwiami frontowymi.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: aac871e71ca0dd30a32e74dd92e417fc95eaa5e1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241334"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Skonfiguruj zasady zapory aplikacji sieci web przy użyciu programu Azure PowerShell
Zasady zapory aplikacji sieci web platformy Azure definiuje inspekcji wymagana, gdy żądanie dociera przy wejściu.
W tym artykule pokazano, jak skonfigurować zasady zapory aplikacji sieci Web, który składa się kilka niestandardowych reguł i za pomocą usługi Azure managed domyślnego Ruse zestawu włączone.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad limitu szybkości służą do konfigurowania środowiska PowerShell i tworzenie profilu drzwi wejściowe.
### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu swoich poświadczeń platformy Azure i zainstaluj moduł Az PowerShell.

#### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
```
Connect-AzAccount

```
Przed zainstalowaniem modułu usługi Front Door upewnij się, że masz zainstalowaną bieżącą wersję modułu PowerShellGet. Uruchom poniższe polecenie i ponownie otwórz program PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Zainstaluj moduł Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Utwórz profil drzwi
Utwórz profil drzwiami frontowymi, wykonując instrukcje opisane w [Szybki Start: Utwórz profil drzwi](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Niestandardowe reguły na podstawie parametrów http

Poniższy przykład pokazuje, jak skonfigurować niestandardową regułę z dwóch warunków dopasowania za pomocą [New AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Żądania pochodzą z określonej lokacji, zgodnie z definicją odwołania, a ciąg zapytania nie zawiera "password". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Niestandardowe reguły na podstawie metody żądania http
Tworzenie reguły blokowania, używając metody "PUT" [New AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) w następujący sposób:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Utwórz regułę niestandardową, w oparciu o ograniczenie rozmiaru

Poniższy przykład tworzy regułę, blokuje żądań z adresu Url, który jest dłuższa niż 100 znaków, przy użyciu programu Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Dodaj zarządzane domyślny zestaw reguł

Poniższy przykład tworzy zarządzanych domyślny zestaw reguł przy użyciu programu Azure PowerShell:
```powershell-interactive
$managedRules = New-AzFrontDoorManagedRuleObject -Type DefaultRuleSet -Version "preview-0.1"
```
## <a name="configure-a-security-policy"></a>Konfigurowanie zasad zabezpieczeń

Znajdowanie nazwy grupy zasobów, która zawiera, przy użyciu profilu drzwiami frontowymi `Get-AzResourceGroup`. Następnie skonfiguruj zasady zabezpieczeń z regułami utworzony w poprzednich krokach za pomocą [New AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) w grupie określonego zasobu, który zawiera profil drzwi wejściowe.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Zasady łącze do hosta frontonu drzwi
Łącze obiektu zasad zabezpieczeń do istniejącego hosta frontonu drzwiami frontowymi i aktualizować drzwiami frontowymi właściwości. Najpierw należy pobrać, za pomocą obiektu drzwiami frontowymi [Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Następnym etapem jest skonfigurowanie frontonu *WebApplicationFirewallPolicyLink* właściwości *resourceId* "$ $myWAFPolicy" utworzony w poprzednim kroku, używając [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

Poniższym przykładzie używa nazwy grupy zasobów *myResourceGroupFD1* przy założeniu, że utworzono drzwiami frontowymi profilu przy użyciu instrukcji podanych w [Szybki Start: Utwórz drzwiami frontowymi](quickstart-create-front-door.md) artykułu. Ponadto w poniższym przykładzie Zastąp $frontDoorName nazwę profilu wejściu. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Musisz ustawić *WebApplicationFirewallPolicyLink* właściwość raz połączyć drzwiami frontowymi frontonu zasady zabezpieczeń. Zasady kolejne aktualizacje są automatycznie stosowane do frontonu.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [drzwi](front-door-overview.md) 
- Dowiedz się więcej o [zapory aplikacji sieci Web dla drzwi](waf-overview.md)
