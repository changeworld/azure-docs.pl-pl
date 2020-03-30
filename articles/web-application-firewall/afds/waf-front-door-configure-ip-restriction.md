---
title: Konfigurowanie reguły WAF ograniczenia adresu IP dla drzwi ami frontowymi platformy Azure
description: Dowiedz się, jak skonfigurować regułę Zapory aplikacji sieci Web, aby ograniczyć adresy IP dla istniejącego punktu końcowego drzwi frontowych platformy Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: 077f127648688b25d45b433fa2bc94ee011b3f2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336110"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Konfigurowanie reguły ograniczenia adresu IP przy zaporze aplikacji sieci Web dla drzwi ami frontowymi platformy Azure

W tym artykule pokazano, jak skonfigurować reguły ograniczeń IP w zaporze aplikacji sieci Web (WAF) dla drzwi y frontowej platformy Azure przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure, programu Azure PowerShell lub szablonu usługi Azure Resource Manager.

Reguła kontroli dostępu oparta na adresie IP to niestandardowa reguła WAF, która umożliwia kontrolowanie dostępu do aplikacji sieci web. Odbywa się to poprzez określenie listy adresów IP lub zakresów adresów IP w formacie CIDR (Classless Inter-Domain Routing).

Domyślnie aplikacja internetowa jest dostępna z Internetu. Jeśli chcesz ograniczyć dostęp do klientów z listy znanych adresów IP lub zakresów adresów IP, możesz utworzyć regułę dopasowywania adresów IP, która zawiera listę adresów IP jako pasujące wartości i ustawia operatora na "Nie" (negata jest prawdziwa) i akcję **blokowania**. Po zastosowaniu reguły ograniczenia IP żądania pochodzące z adresów spoza tej listy dozwolonych otrzymują odpowiedź 403 Zabronione.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Konfigurowanie zasad WAF za pomocą witryny Azure portal

### <a name="prerequisites"></a>Wymagania wstępne

Utwórz profil drzwi frontowych platformy Azure, postępując zgodnie z instrukcjami opisanymi w [przewodniku Szybki start: Utwórz drzwi dla wysokiej dostępności dla globalnej aplikacji sieci Web.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Tworzenie zasad WAF

1. W portalu Azure wybierz pozycję **Utwórz zasób**, wpisz **zaporę aplikacji sieci Web** w polu wyszukiwania, a następnie wybierz pozycję **Zapora aplikacji sieci Web (WAF).**
2. Wybierz **pozycję Utwórz**.
3. Na stronie **Tworzenie zasad WAF** użyj następujących wartości, aby ukończyć kartę **Podstawy:**
   
   |Ustawienie  |Wartość  |
   |---------|---------|
   |Polityka dotycząca     |Globalne WAF (drzwi wejściowe)|
   |Subskrypcja     |Wybierz swoją subskrypcję|
   |Grupa zasobów     |Wybierz grupę zasobów, w której znajdują się drzwi frontowe.|
   |Nazwa zasady     |Wpisz nazwę zasad|
   |Stan zasad     |Enabled (Włączony)|

   Wybierz **dalej: Ustawienia zasad**

1. Na karcie **Ustawienia zasad** wybierz pozycję **Zapobieganie**. Dla **bloku odpowiedzi ciała**, wpisz *Zostałeś zablokowany!* aby zobaczyć, że reguła niestandardowa obowiązuje.
2. Wybierz **dalej: Reguły zarządzane**.
3. Wybierz **dalej: Reguły niestandardowe**.
4. Wybierz **pozycję Dodaj regułę niestandardową**.
5. Na stronie **Dodawanie reguły niestandardowej** użyj następujących wartości testowych, aby utworzyć regułę niestandardową:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Nazwa reguły niestandardowej     |FdWafCustRule (FdWafCustRule)|
   |Stan     |Enabled (Włączony)|
   |Typ reguły     |Dopasowanie|
   |Priorytet    |100|
   |Typ dopasowania     |Adres IP|
   |Dopasuj zmienną|RemoteAddr|
   |Operacja|Nie zawiera|
   |Adres IP lub zakres|10.10.10.0/24|
   |Następnie|Odmów ruchu|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Reguła niestandardowa":::

   Wybierz pozycję **Dodaj**.
6. Wybierz **dalej: Skojarzenie**.
7. Wybierz **pozycję Dodaj hosta frontendu**.
8. W przypadku **hosta frontendu**wybierz hosta frontendu i wybierz pozycję **Dodaj**.
9. Wybierz pozycję **Przegląd + utwórz**.
10. Po przejściu weryfikacji zasad wybierz pozycję **Utwórz**.

### <a name="test-your-waf-policy"></a>Przetestuj swoją politykę WAF

1. Po zakończeniu wdrażania zasad WAF przejdź do nazwy hosta frontendu drzwi frontendowych.
2. Powinien zostać wyświetlony niestandardowy komunikat o bloku.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Test reguł WAF":::

   > [!NOTE]
   > Prywatny adres IP został celowo użyty w regule niestandardowej, aby zagwarantować, że reguła wyzwoli. W rzeczywistym wdrożeniu utwórz *reguły zezwalania* i *odmawianie* przy użyciu adresów IP w określonej sytuacji.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Konfigurowanie zasad WAF za pomocą interfejsu wiersza polecenia platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad ograniczeń ip należy skonfigurować środowisko interfejsu wiersza polecenia i utworzyć profil usługi Azure Front Door.

#### <a name="set-up-the-azure-cli-environment"></a>Konfigurowanie środowiska interfejsu wiersza polecenia platformy Azure
1. Zainstaluj [narzędzie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)lub użyj usługi Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj** w kolejnych poleceniach interfejsu wiersza polecenia, a następnie zaloguj się do konta platformy Azure w otwartej sesji usługi Cloud Shell. Po rozpoczęciu sesji `az extension add --name front-door` wprowadź, aby dodać rozszerzenie usługi Azure Front Door.
 2. Jeśli używasz interfejsu wiersza polecenia lokalnie w Bash, zaloguj się na platformie Azure przy użyciu . `az login`

#### <a name="create-an-azure-front-door-profile"></a>Tworzenie profilu drzwi y frontowej platformy Azure
Utwórz profil drzwi frontowych platformy Azure, postępując zgodnie z instrukcjami opisanymi w [przewodniku Szybki start: Utwórz drzwi dla wysokiej dostępności dla globalnej aplikacji sieci Web.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Tworzenie zasad WAF

Utwórz zasady WAF za pomocą polecenia tworzenia zasad [waf az.](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) W poniższym przykładzie zastąp nazwę zasad *IPAllowPolicyExampleCLI* unikatową nazwą zasad.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Dodawanie niestandardowej reguły kontroli dostępu do adresu IP

Użyj polecenia [tworzenia reguły niestandardowej az network waf-policy custom-rule,](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) aby dodać niestandardową regułę kontroli dostępu do adresu IP dla właśnie utworzonej zasady WAF.

W poniższych przykładach:
-  Zamień *IPAllowPolicyExampleCLI* na unikatową zasadę utworzoną wcześniej.
-  Zastąp *ip-address-range-1*, *ip-address-range-2* z własnym zakresem.

Najpierw utwórz regułę zezwalania na adres IP dla zasad utworzonych z poprzedniego kroku. 
> [!NOTE]
> **--odroczenie** jest wymagane, ponieważ reguła musi mieć warunek dopasowania, który ma zostać dodany w następnym kroku.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Następnie dodaj warunek dopasowania do reguły:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Znajdowanie identyfikatora zasad WAF 
Znajdź identyfikator zasad WAF za pomocą polecenia [az network front-door waf-policy show.](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) Zamień *IPAllowPolicyExampleCLI* w poniższym przykładzie na unikatową zasadę, która została utworzona wcześniej.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Łączenie zasad WAF z hostem frontu usługi Azure Front-Door

Ustaw identyfikator usługi Azure Front Door *WebApplicationFirewallPolicyLink* iD na identyfikator zasad przy użyciu polecenia [aktualizacji drzwi frontowych az.](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) Zastąp *IPAllowPolicyExampleCLI* unikatową zasadą utworzoną wcześniej.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
W tym przykładzie zasady WAF są stosowane do **punktów Frontendend[0]**. Zasady WAF można połączyć z dowolnym frontem.
> [!Note]
> Musisz ustawić **WebApplicationFirewallPolicyLink** właściwość tylko raz, aby połączyć zasady WAF do frontonu drzwi frontowych platformy Azure. Kolejne aktualizacje zasad są automatycznie stosowane do frontu.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurowanie zasad WAF za pomocą programu Azure PowerShell

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem konfigurowania zasad ograniczeń ip należy skonfigurować środowisko programu PowerShell i utworzyć profil usługi Azure Front Door.

#### <a name="set-up-your-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell
Program Azure PowerShell udostępnia zestaw poleceń cmdlet korzystających z modelu [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) do zarządzania zasobami platformy Azure.

Możesz zainstalować program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) w maszynie lokalnej i używać go w dowolnej sesji programu PowerShell. Postępuj zgodnie z instrukcjami na stronie, aby zalogować się do programu PowerShell przy użyciu poświadczeń platformy Azure, a następnie zainstalować moduł Az.

1. Połącz się z platformą Azure za pomocą następującego polecenia, a następnie użyj interaktywnego okna dialogowego, aby się zalogować.
    ```
    Connect-AzAccount
    ```
 2. Przed zainstalowaniem modułu usługi Azure Front Door upewnij się, że masz zainstalowaną bieżącą wersję modułu Programu PowerShellGet. Uruchom następujące polecenie, a następnie ponownie otwórz program PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Zainstaluj moduł Az.FrontDoor za pomocą następującego polecenia. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Tworzenie profilu drzwi y frontowej platformy Azure
Utwórz profil drzwi frontowych platformy Azure, postępując zgodnie z instrukcjami opisanymi w [przewodniku Szybki start: Utwórz drzwi dla wysokiej dostępności dla globalnej aplikacji sieci Web.](../../frontdoor/quickstart-create-front-door.md)

### <a name="define-an-ip-match-condition"></a>Definiowanie warunku dopasowania IP
Polecenie [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) służy do definiowania warunku dopasowania IP.
W poniższym przykładzie zastąp *ip-address-range-1*, *ip-address-range-2* własnym zakresem.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Tworzenie niestandardowej reguły zezwalania na adres IP

Użyj [polecenia New-AzFrontDoorWafCustomRuleObject,](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) aby zdefiniować akcję i ustawić priorytet. W poniższym przykładzie żądania nie od adresów IP klienta, które pasują do listy zostaną zablokowane.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Konfigurowanie zasad WAF
Znajdź nazwę grupy zasobów zawierającej profil usługi Azure `Get-AzResourceGroup`Front Door za pomocą programu . Następnie skonfiguruj zasady WAF z regułą IP za pomocą [new-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Łączenie zasad WAF z hostem frontu usługi Azure Front-Door

Połącz obiekt zasad WAF z istniejącym hostem front-end i zaktualizuj właściwości usługi Azure Front Door. Najpierw pobierz obiekt Azure Front Door przy użyciu [get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Następnie ustaw właściwość **WebApplicationFirewallPolicyLink** na identyfikator zasobu *$IPAllowPolicyExamplePS*, utworzonej w poprzednim kroku, za pomocą polecenia [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> W tym przykładzie zasady WAF są stosowane do **punktów Frontendend[0]**. Zasady WAF można połączyć z dowolnym frontem. Musisz ustawić **WebApplicationFirewallPolicyLink** właściwość tylko raz, aby połączyć zasady WAF do frontonu drzwi frontowych platformy Azure. Kolejne aktualizacje zasad są automatycznie stosowane do frontu.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Konfigurowanie zasad WAF za pomocą szablonu Menedżera zasobów
Aby wyświetlić szablon, który tworzy zasady usługi Azure Front Door i zasady WAF z niestandardowymi regułami ograniczeń IP, przejdź do [gitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć profil drzwi y frontowej platformy Azure.](../../frontdoor/quickstart-create-front-door.md)
