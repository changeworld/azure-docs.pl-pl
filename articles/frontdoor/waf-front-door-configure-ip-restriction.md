---
title: Konfigurowanie reguły ograniczeń adresów IP za pomocą reguły zapory aplikacji sieci Web dla usługi frontonu platformy Azure
description: Dowiedz się, jak skonfigurować regułę zapory aplikacji sieci Web, aby ograniczyć adresy IP dla istniejącego punktu końcowego usługi "Azure front-drzwi".
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: adca1bdd0cf525627cc284b1c0d3509beddef131
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219389"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Konfigurowanie reguły ograniczeń adresów IP za pomocą zapory aplikacji sieci Web dla usługi platformy Azure front-drzwi
W tym artykule opisano sposób konfigurowania reguł ograniczeń adresów IP w zaporze aplikacji sieci Web (WAF) dla usługi frontonu platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, Azure PowerShell lub szablonu Azure Resource Manager.

Reguła kontroli dostępu opartej na adresie IP to Niestandardowa reguła WAF, która umożliwia kontrolowanie dostępu do aplikacji sieci Web. W tym celu należy określić listę adresów IP lub zakresy adresów IP w formacie routingu międzydomenowego bez klas.

Domyślnie aplikacja sieci Web jest dostępna z Internetu. Jeśli chcesz ograniczyć dostęp do klientów z listy znanych adresów IP lub zakresów adresów IP, możesz utworzyć regułę dopasowania adresów IP, która zawiera listę adresów IP jako pasujące wartości i zestawy, do "not" (Negate jest true) i akcję do **zablokowania**. Po zastosowaniu reguły ograniczeń adresów IP żądania, które pochodzą z adresów spoza tej listy dozwolonych, odbierają niedostępną odpowiedź 403.  

Adres IP klienta może różnić się od adresu IP WAF, na przykład gdy klient uzyskuje dostęp do WAF za pośrednictwem serwera proxy. Reguły ograniczeń adresów IP można utworzyć na podstawie adresów IP klienta (RemoteAddr) lub adresów IP gniazd widzianych przez WAF (SocketAddr). 

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Konfigurowanie zasad WAFymi za pomocą interfejsu wiersza polecenia platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad ograniczeń adresów IP Skonfiguruj środowisko interfejsu wiersza polecenia i Utwórz profil usługi frontonu platformy Azure.

#### <a name="set-up-the-azure-cli-environment"></a>Konfigurowanie środowiska interfejsu wiersza polecenia platformy Azure
1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)lub użyj Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj** w następujących poleceń interfejsu wiersza polecenia, a następnie zaloguj się do konta platformy Azure w sesji Cloud Shell, która zostanie otwarta. Po rozpoczęciu sesji wprowadź `az extension add --name front-door` , aby dodać rozszerzenie usługi Azure front-drzwi.
 2. Jeśli używasz interfejsu wiersza polecenia lokalnie w bash, zaloguj się do platformy Azure przy użyciu `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Tworzenie profilu usługi frontonu platformy Azure
Utwórz profil usługi frontonu platformy Azure, postępując zgodnie z instrukcjami opisanymi w [przewodniku szybki start: Utwórz drzwi tylne dla globalnej aplikacji](quickstart-create-front-door.md)sieci Web o wysokiej dostępności.

### <a name="create-a-waf-policy"></a>Utwórz zasady zapory aplikacji internetowej

Utwórz zasady WAF przy użyciu polecenia [AZ Network Front-drzwiczke WAF-Policy Create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) . W poniższym przykładzie Zastąp nazwę zasad *IPAllowPolicyExampleCLI* unikatową nazwą zasad.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Dodaj niestandardową regułę kontroli dostępu IP

Użyj polecenia [AZ Network Front-WAF-Policy Custom-Rule Create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) , aby dodać niestandardową regułę kontroli dostępu IP dla właśnie utworzonych zasad WAF.

W następujących przykładach:
-  Zastąp *IPAllowPolicyExampleCLI* własnymi utworzonymi wcześniej zasadami.
-  Zastąp wartości *IP-Address-Range-1*, *IP-Address-Range-2* własnym zakresem.

Najpierw utwórz regułę zezwalania IP dla zasad utworzonych w poprzednim kroku. Uwaga: **odroczenie** jest wymagane, ponieważ reguła musi zawierać co najmniej jeden warunek dopasowania. 

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Następnie Dodaj warunek dopasowania adresu IP klienta do reguły:

```azurecli
az network front-door waf-policy rule match-condition add\
--match-variable RemoteAddr \
--operator IPMatch
--values "ip-address-range-1" "ip-address-range-2"
--negate true\
--name IPAllowListRule\
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
Warunek dopasowania dla gniazda IP (SocketAddr):
  ```azurecli
az network front-door waf-policy rule match-condition add\
--match-variable SocketAddr \
--operator IPMatch
--values "ip-address-range-1" "ip-address-range-2"
--negate true\
--name IPAllowListRule\
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI                                                  

### Find the ID of a WAF policy 
Find a WAF policy's ID by using the [az network front-door waf-policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) command. Replace *IPAllowPolicyExampleCLI* in the following example with your unique policy that you created earlier.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Łączenie zasad WAF z hostem frontonu usługi Azure front-end

Ustaw identyfikator *WebApplicationFirewallPolicyLink* usługi Azure Front-DRZWICZK na identyfikator zasad za pomocą polecenia [AZ Network Front-drzwiczk Update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) . Zastąp *IPAllowPolicyExampleCLI* własnymi utworzonymi wcześniej zasadami.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
W tym przykładzie zasady WAF są stosowane do **FrontendEndpoints [0]** . Zasady WAF można połączyć z dowolnym frontonem.
> [!Note]
> Właściwość **WebApplicationFirewallPolicyLink** należy ustawić tylko raz, aby połączyć zasady WAF z frontonem usługi Azure Front-Only. Kolejne aktualizacje zasad są automatycznie stosowane do frontonu.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurowanie zasad WAFymi za pomocą Azure PowerShell

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad ograniczeń adresów IP Skonfiguruj środowisko programu PowerShell i Utwórz profil usługi frontonu platformy Azure.

#### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Azure PowerShell zawiera zestaw poleceń cmdlet, które używają modelu [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) do zarządzania zasobami platformy Azure.

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się do programu PowerShell przy użyciu poświadczeń platformy Azure, a następnie zainstaluj moduł AZ module.

1. Połącz się z platformą Azure przy użyciu następującego polecenia, a następnie zaloguj się przy użyciu interaktywnego okna dialogowego.
    ```
    Connect-AzAccount
    ```
 2. Przed zainstalowaniem modułu usługi Azure Front-drzwiczk upewnij się, że masz zainstalowaną bieżącą wersję modułu PowerShellGet. Uruchom następujące polecenie, a następnie otwórz ponownie program PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Zainstaluj moduł AZ. Usługa frontdoor za pomocą następującego polecenia. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Tworzenie profilu usługi frontonu platformy Azure
Utwórz profil usługi frontonu platformy Azure, postępując zgodnie z instrukcjami opisanymi w [przewodniku szybki start: Utwórz drzwi tylne dla globalnej aplikacji](quickstart-create-front-door.md)sieci Web o wysokiej dostępności.

### <a name="define-an-ip-match-condition"></a>Zdefiniuj warunek dopasowania adresu IP
Użyj polecenia [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) , aby zdefiniować warunek dopasowania adresów IP.
W poniższym przykładzie Zastąp wartość *IP-Address-Range-1*, *IP-Address-Range-2* własnym zakresem.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```

Warunek dopasowania dla gniazda IP (SocketAddr):   
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  SocketAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```


### <a name="create-a-custom-ip-allow-rule"></a>Tworzenie niestandardowej reguły zezwalania na adresy IP

Użyj polecenia [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) , aby zdefiniować akcję i ustawić priorytet. W poniższym przykładzie żądania spoza adresów IP klientów pasujących do listy zostaną zablokowane.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Konfigurowanie zasad WAFymi
Znajdź nazwę grupy zasobów, która zawiera profil usługi frontonu platformy Azure przy użyciu programu `Get-AzResourceGroup`. Następnie skonfiguruj zasady WAF z regułą adresów IP przy użyciu polecenia [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Łączenie zasad WAF z hostem frontonu usługi Azure front-end

Połącz obiekt zasad WAF z istniejącym hostem frontonu i zaktualizuj właściwości usługi frontonu platformy Azure. Najpierw Pobierz obiekt usługi frontonu platformy Azure za pomocą polecenia [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Następnie ustaw właściwość **WebApplicationFirewallPolicyLink** na identyfikator zasobu *$IPAllowPolicyExamplePS*, który został utworzony w poprzednim kroku przy użyciu polecenia [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) .

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> W tym przykładzie zasady WAF są stosowane do **FrontendEndpoints [0]** . Zasady WAF można połączyć z dowolnym frontonem. Właściwość **WebApplicationFirewallPolicyLink** należy ustawić tylko raz, aby połączyć zasady WAF z frontonem usługi Azure Front-Only. Kolejne aktualizacje zasad są automatycznie stosowane do frontonu.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Konfigurowanie zasad WAFymi przy użyciu szablonu Menedżer zasobów
Aby wyświetlić szablon służący do tworzenia zasad usługi Azure Front-drzwiczk i zasad WAF z użyciem niestandardowych reguł ograniczeń adresów IP, przejdź do witryny [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć profil usługi frontonu platformy Azure](quickstart-create-front-door.md).
