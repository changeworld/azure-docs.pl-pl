---
title: Skonfiguruj regułę ograniczeń adresów IP z regułą zapory aplikacji sieci web Azure drzwiami frontowymi
description: Dowiedz się, jak skonfigurować regułę zapory aplikacji sieci Web IP adres ograniczeń dla istniejącego punktu końcowego wejściu.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: kumud;tyao
ms.openlocfilehash: 514c034c23eed3a87111331724f3a33104651a43
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514907"
---
# <a name="configure-an-ip-restriction-rule-with-web-application-firewall-for-azure-front-door-preview"></a>Skonfiguruj regułę ograniczeń adresów IP przy użyciu zapory aplikacji sieci web dla platformy Azure drzwiami frontowymi (wersja zapoznawcza)
 W tym artykule pokazano, jak skonfigurować zasady ograniczeń adresów IP w zapory aplikacji sieci web platformy Azure (WAF) dla drzwiami frontowymi przy użyciu szablonu z wiersza polecenia platformy Azure, programu Azure PowerShell lub usługi Azure Resource Manager.

Reguły kontroli dostępu na podstawie adresów IP jest niestandardową regułę zapory aplikacji sieci Web, która pozwala na kontrolowanie dostępu do aplikacji sieci web, określając listę adresów IP lub zakres adresów IP w postaci Bezklasowego routingu międzydomenowego (CIDR).

Domyślnie aplikacja sieci web jest dostępny z Internetu. Jeśli chcesz ograniczyć dostęp do aplikacji sieci web tylko dla klientów z listy znanych adresów IP lub zakres adresów IP, musisz utworzyć dwóch reguł dopasowania adresów IP. Pierwsza pasująca reguła IP zawiera listę adresów IP jako pasujących wartości i Ustaw akcję "Zezwalaj". Drugi z niższym priorytetem jest Blokuj wszystkie adresy IP za pomocą operatora "All" i Ustaw akcję "Zablokowanie". Po zastosowaniu reguły ograniczeń adresów IP wszystkich żądań wysyłanych z adresów spoza tej listy dozwolonych odbiera odpowiedź 403 (zabronione).  

> [!IMPORTANT]
> Funkcja ograniczenia adresów IP zapory aplikacji sieci Web Azure drzwiami frontowymi jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="configure-waf-policy-with-azure-cli"></a>Konfigurowanie zasad zapory aplikacji sieci Web przy użyciu wiersza polecenia platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad ograniczeń adresów IP, konfigurowanie środowiska interfejsu wiersza polecenia i utworzyć profil wejściu.

#### <a name="set-up-azure-cli-environment"></a>Konfigurowanie środowiska wiersza polecenia platformy Azure
1. Zainstaluj [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), lub za pomocą usługi Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wiersza polecenia platformy Azure wstępnie zainstalowane i skonfigurowane do użycia z Twoim kontem. Wybierz **wypróbuj** przycisku w interfejsie wiersza polecenia platformy polecenia poniżej. Wybieranie **wypróbuj** wywołuje Cloud Shell, która może zalogować się do konta platformy Azure za pomocą. Po uruchomieniu sesji powłoki cloud, wprowadź `az extension add --name front-door` można dodać rozszerzenia drzwiami frontowymi.
 2. Jeśli używasz interfejsu wiersza polecenia lokalnie w powłoce Bash, zaloguj się do platformy Azure z `az login`.

#### <a name="create-front-door-profile"></a>Utwórz profil drzwi
Utwórz profil drzwiami frontowymi, wykonując instrukcje opisane w [Szybki Start: Utwórz profil drzwi](quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Utwórz zasady zapory aplikacji sieci Web

Utwórz zasady zapory aplikacji sieci Web [Utwórz zasady zapory aplikacji sieci Web az sieci](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) polecenia. W poniższym przykładzie Zastąp nazwę zasady *IPAllowPolicyExampleCLI* z unikatowa nazwa zasad.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-custom-ip-access-control-rule"></a>Dodaj niestandardowe reguły kontroli dostępu IP

Dodaj niestandardowe reguły kontroli dostępu IP do zasad zapory aplikacji sieci Web, utworzony w poprzednim kroku przy użyciu [tworzenie az sieci zasad zapory aplikacji sieci Web — reguły niestandardowej](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) polecenia. 

W poniższym przykładzie:
-  Zastąp *IPAllowPolicyExampleCLI* o swoje unikatowe zasady utworzone wcześniej.
-  Zastąp *zakresu 1, ip adresu w-*, *ip adres zakresu-2* przy użyciu własnego zakresu.

Najpierw należy utworzyć pakiet Integracyjny Zezwalaj na regułę dla określonych adresów.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Następnie należy utworzyć bloku wszystkich adresów IP reguły z niższym priorytetem niż poprzednie IP regułę Zezwalaj. Zastąp *IPAllowPolicyExampleCLI* o swoje unikatowe zasady utworzone wcześniej.

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
 ```

### <a name="find-waf-policy-id"></a>Znajdź identyfikator zasad zapory aplikacji sieci Web
Znajdowanie Identyfikatora zasadę zapory aplikacji sieci Web za pomocą [az sieci zapory aplikacji sieci Web policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) polecenia. Zastąp *IPAllowPolicyExampleCLI* o swoje unikatowe zasady utworzone wcześniej.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Zasady łącze zapory aplikacji sieci Web do hosta frontonu drzwi

Ustaw drzwiami frontowymi *WebApplicationFirewallPolicyLink* identyfikator identyfikator zasad za pomocą [az sieci drzwiami frontowymi aktualizacji](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) polecenia. Zastąp *IPAllowPolicyExampleCLI* o swoje unikatowe zasady utworzone wcześniej.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
W tym przykładzie zasady zapory aplikacji sieci Web są stosowane do FrontendEndpoints [0]. Zasady zapory aplikacji sieci Web może połączyć do dowolnej usługi frontonach.
> [!Note]
> Musisz ustawić **WebApplicationFirewallPolicyLink** właściwość raz połączyć drzwiami frontowymi frontonu zasadę zapory aplikacji sieci Web. Zasady kolejne aktualizacje są automatycznie stosowane do frontonu.

## <a name="configure-waf-policy-with-azure-powershell"></a>Konfigurowanie zasad zapory aplikacji sieci Web przy użyciu programu Azure PowerShell

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad ograniczeń adresów IP, konfigurowanie środowiska PowerShell i utworzyć profil wejściu.

#### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet, które pozwalają zarządzać zasobami platformy Azure przy użyciu modelu usługi [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się przy użyciu swoich poświadczeń platformy Azure i zainstaluj moduł Az PowerShell.

##### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Łączenie z platformą Azure za pomocą dialogu interaktywnego logowania
```
Connect-AzAccount

```
Przed zainstalowaniem modułu drzwiami frontowymi, upewnij się, że masz bieżącą wersję zainstalowanego modułu PowerShellGet. Uruchom poniższe polecenie, a następnie ponownie otwórz program PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

##### <a name="install-azfrontdoor-module"></a>Zainstaluj moduł Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Utwórz profil drzwi
Utwórz profil drzwiami frontowymi, wykonując instrukcje opisane w [Szybki Start: Utwórz profil drzwi](quickstart-create-front-door.md)

### <a name="define-ip-match-condition"></a>Zdefiniuj warunek dopasowania adresów IP
Użyj [New AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject) polecenie, aby zdefiniować warunek dopasowania adresów IP. W poniższym przykładzie Zastąp *zakresu 1, ip adresu w-*, *ip adres zakresu-2* przy użyciu własnego zakresu.

```powershell
  $IPMatchCondition = New-AzFrontDoorMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty IPMatch `
    -MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Utwórz wszystkie reguły warunku dopasowania adresów IP
```powershell
  $IPMatchALlCondition = New-AzFrontDoorMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty Any
    
```

### <a name="create-a-custom-ip-allow-rule"></a>Utwórz niestandardową regułę Zezwalaj adresu IP
   Użyj [New AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject) polecenie, aby zdefiniować akcję i ustaw priorytet. W poniższym przykładzie będą dozwolone żądania z adresów IP klientów, które pasują do listy. 

```powershell
  $IPAllowRule = New-AzFrontDoorCustomRuleObject `
    -Name "IPAllowRule" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchCondition `
    -Action Allow -Priority 1
```
Tworzenie bloku wszystkich adresów IP reguły z niższym priorytetem niż poprzednie IP regułę Zezwalaj.

```powershell
  $IPBlockAll = New-AzFrontDoorCustomRuleObject `
    -Name "IPDenyAll" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchALlCondition `
    -Action Block `
    -Priority 2
   ```

### <a name="configure-waf-policy"></a>Konfigurowanie zasad zapory aplikacji sieci Web
Znajdowanie nazwy grupy zasobów, która zawiera, przy użyciu profilu drzwiami frontowymi `Get-AzResourceGroup`. Następnie skonfiguruj zasady zapory aplikacji sieci Web przy użyciu reguły bloku adresów IP [New AzFrontDoorFireWallPolicy](/powershell/module/Az.FrontDoor/New-AzFrontDoorFireWallPolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorFireWallPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Zasady łącze zapory aplikacji sieci Web do hosta frontonu drzwi

Łącze obiektu zasad zapory aplikacji sieci Web do istniejącego hosta frontonu drzwiami frontowymi i aktualizować drzwiami frontowymi właściwości. Najpierw pobrać za pomocą obiektu drzwiami frontowymi [Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Następnym etapem jest skonfigurowanie frontonu *WebApplicationFirewallPolicyLink* właściwość resourceId z *$IPAllowPolicyExamplePS* utworzony w poprzednim kroku przy użyciu [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) polecenia.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> W tym przykładzie zasady zapory aplikacji sieci Web są stosowane do FrontendEndpoints [0]. Zasady zapory aplikacji sieci Web może połączyć do dowolnej usługi frontonach. Musisz ustawić *WebApplicationFirewallPolicyLink* właściwość raz połączyć drzwiami frontowymi frontonu zasadę zapory aplikacji sieci Web. Zasady kolejne aktualizacje są automatycznie stosowane do frontonu.


## <a name="configure-waf-policy-with-resource-manager-template"></a>Konfigurowanie zasad zapory aplikacji sieci Web przy użyciu szablonu usługi Resource Manager
Wyświetl szablon, który umożliwia utworzenie drzwiami frontowymi i zasad zapory aplikacji sieci Web za pomocą niestandardowych reguł ograniczenia adresów IP [tutaj](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Utwórz profil drzwiami frontowymi](quickstart-create-front-door.md).
