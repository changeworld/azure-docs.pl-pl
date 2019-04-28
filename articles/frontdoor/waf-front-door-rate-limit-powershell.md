---
title: Skonfiguruj reguły limit szybkości zapory aplikacji sieci web na wejściu — Azure PowerShell
description: Dowiedz się, jak skonfigurować regułę limit szybkości dla istniejącego punktu końcowego wejściu.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2019
ms.author: kumud;tyao
ms.openlocfilehash: e0ad1e85a4cd47de823bc4f224b5a8834b1068b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459321"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Konfigurowanie sieci web aplikacji współczynnik limit reguły zapory przy użyciu programu Azure PowerShell
Sieci web platformy Azure reguła zapory usługi application (WAF) współczynnik limit Azure drzwiami frontowymi kontroluje liczbę żądań od jednego klienta adresu IP podczas okresu jednej minuty.
W tym artykule pokazano, jak skonfigurować regułę limit szybkości zapory aplikacji sieci Web, która steruje liczbą żądań od jednego klienta do aplikacji sieci web, która zawiera */promo* w adresie URL, za pomocą programu Azure PowerShell.

> [!IMPORTANT]
> Funkcji reguły limit szybkości zapory aplikacji sieci Web Azure drzwiami frontowymi jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad limitu szybkości służą do konfigurowania środowiska PowerShell i tworzenie profilu drzwi wejściowe.
### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu swoich poświadczeń platformy Azure i zainstaluj moduł Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Łączenie z platformą Azure za pomocą interakcyjne okno dialogowe logowania
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

## <a name="define-url-match-conditions"></a>Zdefiniuj warunki dopasowań adresów url
Zdefiniuj warunek dopasowania adresów URL (adres URL zawiera /promo) przy użyciu [New AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject).
W poniższym przykładzie dopasowywane */promo* jako wartość *RequestUri* zmiennej:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Utwórz regułę limit szybkości niestandardowe
Ustaw limit szybkości za pomocą [New AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject). W poniższym przykładzie ustawiono limit 1000. Żądania za pomocą dowolnego klienta do strony promocyjnych przekracza 1000 w ciągu jednej minuty są blokowane, dopóki nie rozpoczyna się następna minuta.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Konfigurowanie zasad zabezpieczeń

Znajdowanie nazwy grupy zasobów, która zawiera, przy użyciu profilu drzwiami frontowymi `Get-AzureRmResourceGroup`. Następnie skonfiguruj zasady zabezpieczeń przy użyciu reguły limit szybkości niestandardowe [New AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) w grupie określonego zasobu, który zawiera profil drzwi wejściowe.

Poniższym przykładzie używa nazwy grupy zasobów *myResourceGroupFD1* przy założeniu, że utworzono drzwiami frontowymi profilu przy użyciu instrukcji podanych w [Szybki Start: Utwórz drzwiami frontowymi](quickstart-create-front-door.md) artykułu.

 za pomocą [New AzFrontDoorFireWallPolicy](/powershell/module/Az.FrontDoor/New-AzFrontDoorFireWallPolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorFireWallPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Zasady łącze do hosta frontonu drzwi
Łącze obiektu zasad zabezpieczeń do istniejącego hosta frontonu drzwiami frontowymi i aktualizować drzwiami frontowymi właściwości. Najpierw pobrać za pomocą obiektu drzwiami frontowymi [Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) polecenia.
Następnym etapem jest skonfigurowanie frontonu *WebApplicationFirewallPolicyLink* właściwości *resourceId* z "$ratePolicy" utworzony w poprzednim kroku, używając [AzFrontDoor zestaw](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) polecenie. 

Poniższym przykładzie używa nazwy grupy zasobów *myResourceGroupFD1* przy założeniu, że utworzono drzwiami frontowymi profilu przy użyciu instrukcji podanych w [Szybki Start: Utwórz drzwiami frontowymi](quickstart-create-front-door.md) artykułu. Ponadto w poniższym przykładzie Zastąp $frontDoorName nazwę profilu wejściu. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Musisz ustawić *WebApplicationFirewallPolicyLink* właściwość raz połączyć drzwiami frontowymi frontonu zasady zabezpieczeń. Zasady kolejne aktualizacje są automatycznie stosowane do frontonu.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [drzwi](front-door-overview.md) 


