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
ms.date: 04/08/2019
ms.author: kumud;tyao
ms.openlocfilehash: 7d024dd958e6b29b52f095a9a55a67154bf6cde6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459797"
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

Poniższy przykład pokazuje, jak skonfigurować niestandardową regułę z dwóch warunków dopasowania za pomocą [New AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject). Żądania pochodzą z określonej lokacji, zgodnie z definicją odwołania, a ciąg zapytania nie zawiera "password". 

```powershell-interactive
$referer = New-AzFrontDoorMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Niestandardowe reguły na podstawie metody żądania http
Tworzenie reguły blokowania, używając metody "PUT" [New AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject) w następujący sposób:

```powershell-interactive
$put = New-AzFrontDoorMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Utwórz regułę niestandardową, w oparciu o ograniczenie rozmiaru

Poniższy przykład tworzy regułę, blokuje żądań z adresu Url, który jest dłuższa niż 100 znaków, przy użyciu programu Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Dodaj zarządzane domyślny zestaw reguł

Poniższy przykład tworzy zarządzanych domyślny zestaw reguł przy użyciu programu Azure PowerShell:
```powershell-interactive
$managedRules = New-AzFrontDoorManagedRuleObject -Type DefaultRuleSet -Version "preview-0.1"
```
## <a name="configure-a-security-policy"></a>Konfigurowanie zasad zabezpieczeń

Znajdowanie nazwy grupy zasobów, która zawiera, przy użyciu profilu drzwiami frontowymi `Get-AzResourceGroup`. Następnie skonfiguruj zasady zabezpieczeń z regułami utworzony w poprzednich krokach za pomocą [New AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) w grupie określonego zasobu, który zawiera profil drzwi wejściowe.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorFireWallPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
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
