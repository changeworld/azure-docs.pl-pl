---
title: Skonfiguruj regułę ograniczeń adresów IP z regułą zapory aplikacji sieci web dla usługi Azure Service drzwiami frontowymi
description: Dowiedz się, jak skonfigurować reguły zapory aplikacji sieci web w celu ograniczenia adresów IP dla istniejącego punktu końcowego usługi drzwiami frontowymi platformy Azure.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 88c5c284f26203ff3d6c39810a7b2810c1ebbc5a
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743160"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Skonfigurować regułę ograniczeń adresów IP przy użyciu zapory aplikacji sieci web dla usługi Azure Service drzwi
W tym artykule pokazano, jak skonfigurować zasady ograniczeń adresów IP w zapory aplikacji sieci web (WAF) dla usługi Azure Service drzwiami frontowymi przy użyciu wiersza polecenia platformy Azure, programu Azure PowerShell lub szablonu usługi Azure Resource Manager.

Reguły kontroli dostępu na podstawie adresu IP jest niestandardową regułę zapory aplikacji sieci Web, która umożliwia kontrolowanie dostępu do aplikacji sieci web. Dzieje się tak, określając listę adresów IP lub zakres adresów IP w formacie Bezklasowego routingu międzydomenowego (CIDR).

Domyślnie aplikacja sieci web jest dostępny z Internetu. Jeśli chcesz ograniczyć dostęp dla klientów z listy znane adresy IP lub zakres adresów IP, należy utworzyć dwie reguły dopasowania adresów IP. Pierwsza pasująca reguła IP zawiera listę adresów IP jako pasujących wartości i ustawia akcję **Zezwalaj**. Drugi o niższym priorytecie, blokuje wszystkie adresy IP przy użyciu **wszystkich** operatora i ustawienie akcji **bloku**. Po zastosowaniu reguły ograniczeń adresów IP, żądań, które pochodzą z adresów spoza tej listy dozwolonych otrzymują odpowiedź 403 Zabroniony.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Skonfiguruj zasady zapory aplikacji sieci Web przy użyciu wiersza polecenia platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad ograniczeń adresów IP, konfigurowanie środowiska interfejsu wiersza polecenia i tworzenie profilu usługi drzwiami frontowymi platformy Azure.

#### <a name="set-up-the-azure-cli-environment"></a>Konfigurowanie środowiska wiersza polecenia platformy Azure
1. Zainstaluj [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), lub za pomocą usługi Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz **wypróbuj** przycisk za pomocą poleceń interfejsu wiersza polecenia, które należy wykonać, a następnie zaloguj się do konta platformy Azure w otwartej sesji usługi Cloud Shell. Po uruchomieniu sesję, wprowadź `az extension add --name front-door` można dodać rozszerzenia usługi drzwiami frontowymi platformy Azure.
 2. Jeśli używasz interfejsu wiersza polecenia lokalnie powłoki Bash, zaloguj się na platformie Azure przy użyciu `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Tworzenie profilu usługi drzwiami frontowymi platformy Azure
Tworzenie profilu usługi drzwiami frontowymi platformy Azure, wykonując instrukcje opisane w [Szybki Start: Utwórz drzwiami frontowymi dla aplikacji o wysokiej dostępności globalnej sieci web](quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Utwórz zasady zapory aplikacji sieci Web

Utwórz zasadę zapory aplikacji sieci Web za pomocą [Utwórz zasady zapory aplikacji sieci Web az sieci](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) polecenia. W przykładzie, czy poniżej, zastąp nazwę zasady *IPAllowPolicyExampleCLI* z unikatowa nazwa zasad.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Dodaj niestandardowe reguły kontroli dostępu IP

Użyj [tworzenie az sieci zasad zapory aplikacji sieci Web — reguły niestandardowej](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) polecenie, aby dodać niestandardowe IP reguły kontroli dostępu dla zasad zapory aplikacji sieci Web został utworzony.

W poniższych przykładach:
-  Zastąp *IPAllowPolicyExampleCLI* o swoje unikatowe zasady utworzone wcześniej.
-  Zastąp *zakresu 1, ip adresu w-* , *ip adres zakresu-2* przy użyciu własnego zakresu.

Najpierw należy utworzyć pakiet Integracyjny Zezwalaj na regułę dla określonych adresów.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch "<ip-address-range-1>","<ip-address-range-2>" \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Następnie należy utworzyć **zablokować wszystkie** reguły z niższym priorytetem niż poprzedni **Zezwalaj** reguły. Ponownie *IPAllowPolicyExampleCLI* w poniższym przykładzie przy użyciu utworzonego wcześniej unikatowe zasady.

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
    
### <a name="find-the-id-of-a-waf-policy"></a>Znajdowanie Identyfikatora zasad zapory aplikacji sieci Web 
Znajdź identyfikator zasady zapory aplikacji sieci Web za pomocą [az sieci zapory aplikacji sieci Web policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) polecenia. Zastąp *IPAllowPolicyExampleCLI* w poniższym przykładzie przy użyciu utworzonego wcześniej unikatowe zasady.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Połącz zasady zapory aplikacji sieci Web hosta frontonu usługi drzwiami frontowymi platformy Azure

Skonfiguruj usługę Azure drzwiami frontowymi *WebApplicationFirewallPolicyLink* identyfikator identyfikator zasad za pomocą [az sieci drzwiami frontowymi aktualizacji](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) polecenia. Zastąp *IPAllowPolicyExampleCLI* z zasadami unikatowy utworzonego wcześniej.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
W tym przykładzie zastosowano zasady zapory aplikacji sieci Web, aby **FrontendEndpoints [0]** . Zasady zapory aplikacji sieci Web można połączyć do dowolnej usługi frontonów.
> [!Note]
> Należy ustawić **WebApplicationFirewallPolicyLink** właściwość tylko raz, połączyć zasadę zapory aplikacji sieci Web frontonu usługi drzwiami frontowymi platformy Azure. Zasady kolejne aktualizacje są automatycznie stosowane do frontonu.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Skonfiguruj zasady zapory aplikacji sieci Web przy użyciu programu Azure PowerShell

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad ograniczeń adresów IP, konfigurowanie środowiska PowerShell i tworzenie profilu usługi drzwiami frontowymi platformy Azure.

#### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Azure PowerShell udostępnia zestaw poleceń cmdlet, używanego przez [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelu zarządzania zasobami platformy Azure.

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się do programu PowerShell przy użyciu swoich poświadczeń platformy Azure, a następnie zainstalować moduł Az.

1. Łączenie z platformą Azure za pomocą następującego polecenia, a następnie użyj okno interaktywne, aby zalogować się.
    ```
    Connect-AzAccount
    ```
 2. Przed zainstalowaniem moduł usługi Azure Service drzwiami frontowymi, upewnij się, że masz bieżącą wersję zainstalowanym modułem PowerShellGet. Uruchom następujące polecenie, a następnie ponownie otwórz program PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Zainstaluj moduł Az.FrontDoor przy użyciu następującego polecenia. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Tworzenie profilu usługi drzwiami frontowymi platformy Azure
Tworzenie profilu usługi drzwiami frontowymi platformy Azure, wykonując instrukcje opisane w [Szybki Start: Utwórz drzwiami frontowymi dla aplikacji o wysokiej dostępności globalnej sieci web](quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Zdefiniuj warunek dopasowania adresów IP
Użyj [New AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) polecenie, aby zdefiniować warunek dopasowania adresów IP.
W poniższym przykładzie Zastąp *zakresu 1, ip adresu w-* , *ip adres zakresu-2* przy użyciu własnego zakresu.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Tworzenie pakietu Integracyjnego *dopasować stan wszystkich* reguły za pomocą następującego polecenia:
```powershell
$IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty Any        
  ```
    
### <a name="create-a-custom-ip-allow-rule"></a>Utwórz niestandardową regułę Zezwalaj adresu IP

Użyj [New AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) polecenie, aby zdefiniować akcję i ustaw priorytet. W poniższym przykładzie będą dozwolone żądania z adresów IP klientów, które pasują do listy.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Allow -Priority 1
```
Tworzenie **zablokować wszystkie** reguły z niższym priorytetem niż poprzednie IP **Zezwalaj** reguły.
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>Konfigurowanie zasad zapory aplikacji sieci Web
Znajdź nazwę grupy zasobów, który zawiera profil usługi drzwiami frontowymi platformy Azure przy użyciu `Get-AzResourceGroup`. Następnie skonfiguruj zasady zapory aplikacji sieci Web z adresem IP **zablokować wszystkie** reguły za pomocą [New AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Połącz zasady zapory aplikacji sieci Web hosta frontonu usługi drzwiami frontowymi platformy Azure

Łącze obiektu zasad zapory aplikacji sieci Web do istniejącego frontonu hosta i zaktualizuj usługę drzwiami frontowymi Azure właściwości. Najpierw Pobierz obiekt usługi drzwiami frontowymi platformy Azure przy użyciu [Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Następnym etapem jest skonfigurowanie **WebApplicationFirewallPolicyLink** właściwość Identyfikatora zasobu *$IPAllowPolicyExamplePS*utworzony w poprzednim kroku, korzystając z [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)polecenia.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> W tym przykładzie zastosowano zasady zapory aplikacji sieci Web, aby **FrontendEndpoints [0]** . Do dowolnej usługi frontonów, możesz połączyć zasadę zapory aplikacji sieci Web. Należy ustawić **WebApplicationFirewallPolicyLink** właściwość tylko raz, połączyć zasadę zapory aplikacji sieci Web frontonu usługi drzwiami frontowymi platformy Azure. Zasady kolejne aktualizacje są automatycznie stosowane do frontonu.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Skonfiguruj zasady zapory aplikacji sieci Web przy użyciu szablonu usługi Resource Manager
Aby wyświetlić szablon, który umożliwia utworzenie zasad usługi Azure Service drzwiami frontowymi i zasad zapory aplikacji sieci Web za pomocą niestandardowych reguł ograniczenia adresów IP, przejdź do [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Tworzenie profilu usługi Azure Service drzwiami frontowymi](quickstart-create-front-door.md).
