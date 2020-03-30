---
title: Konfigurowanie reguły limitu szybkości WAF dla drzwi wejściowych — usługa Azure PowerShell
description: Dowiedz się, jak skonfigurować regułę limitu szybkości dla istniejącego punktu końcowego drzwi y przedniej.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: b034159c3d12927f6425b3dc3c5b5609af9b0b76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649368"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Konfigurowanie reguły limitu szybkości zapory aplikacji sieci Web przy użyciu programu Azure PowerShell
Reguła limitu szybkości zapory aplikacji sieci Azure (WAF) dla drzwi frontowych platformy Azure kontroluje liczbę żądań dozwolonych od klientów w ciągu jednej minuty.
W tym artykule pokazano, jak skonfigurować regułę limitu szybkości WAF, która kontroluje liczbę żądań dozwolonych od klientów do aplikacji sieci web, która zawiera */promo* w adresie URL przy użyciu programu Azure PowerShell.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad limitu szybkości należy skonfigurować środowisko programu PowerShell i utworzyć profil drzwi frontowych.
### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu poświadczeń platformy Azure i zainstalować moduł Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Łączenie się z platformą Azure za pomocą interaktywnego okna dialogowego do logowania
```
Connect-AzAccount

```
Przed zainstalowaniem modułu Drzwi frontowych upewnij się, że jest zainstalowana bieżąca wersja programu PowerShellGet. Uruchom następujące polecenie i ponownie otwórz program PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalowanie modułu Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Tworzenie profilu drzwi przednich
Tworzenie profilu drzwi przednich, postępując zgodnie z instrukcjami opisanymi w [przewodniku Szybki start: Tworzenie profilu drzwi przednich](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Definiowanie warunków dopasowania adresu URL
Zdefiniuj warunek dopasowania adresu URL (URL zawiera /promo) za pomocą [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
Poniższy przykład pasuje */promo* jako wartość zmiennej *RequestUri:*

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Tworzenie niestandardowej reguły limitu szybkości
Ustaw limit szybkości za pomocą [new-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). W poniższym przykładzie limit jest ustawiony na 1000. Żądania od dowolnego klienta do strony promocyjnej przekraczającej 1000 w ciągu jednej minuty są blokowane do następnej minuty.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Konfigurowanie zasad zabezpieczeń

Znajdź nazwę grupy zasobów zawierającej profil Drzwiami frontowymi za pomocą programu `Get-AzureRmResourceGroup`. Następnie skonfiguruj zasady zabezpieczeń z niestandardową regułą limitu szybkości przy użyciu [new-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) w określonej grupie zasobów zawierającej profil Drzwi frontowe.

W poniższym przykładzie użyto nazwy grupy zasobów *myResourceGroupFD1* przy założeniu, że profil drzwi frontowych został utworzony przy użyciu instrukcji podanych w artykule [Szybki start: Tworzenie drzwi przednich.](../../frontdoor/quickstart-create-front-door.md)

 za pomocą [programu New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Połącz zasady z hostem frontu drzwi frontowych
Połącz obiekt zasad zabezpieczeń z istniejącym hostem frontu drzwi frontowych i zaktualizuj właściwości drzwi frontowych. Najpierw pobierz obiekt Drzwi frontowe za pomocą polecenia [Get-AzFrontDoor.](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)
Następnie ustaw właściwość *Front-End WebApplicationFirewallPolicyLink* na *identyfikator resourceId* "$ratePolicy" utworzoną w poprzednim kroku przy użyciu polecenia [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 

W poniższym przykładzie użyto nazwy grupy zasobów *myResourceGroupFD1* przy założeniu, że profil drzwi frontowych został utworzony przy użyciu instrukcji podanych w artykule [Szybki start: Tworzenie drzwi przednich.](../../frontdoor/quickstart-create-front-door.md) Ponadto w poniższym przykładzie zastąp $frontDoorName nazwą profilu drzwiami frontowymi. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Wystarczy tylko raz ustawić właściwość *WebApplicationFirewallPolicyLink,* aby połączyć zasady zabezpieczeń z frontonem drzwi frontowych. Kolejne aktualizacje zasad są automatycznie stosowane do front-endu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [drzwiach frontowych](../../frontdoor/front-door-overview.md). 


