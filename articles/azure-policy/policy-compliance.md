---
title: Pobieranie danych zgodności w zasadach usługi Azure
description: Azure oceny zasad i efekty określają zgodność. Dowiedz się, jak można pobrać szczegółów zgodności.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: d36ecb18811901fb781e151c06badc0697c2d769
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655098"
---
# <a name="getting-compliance-data"></a>Pobieranie danych zgodności

Jedną z największych zalet zasad usługi Azure jest szczegółowe dane i formantów jest lepsza od zasobów w ramach subskrypcji lub [grupy zarządzania](../azure-resource-manager/management-groups-overview.md) subskrypcji. Ten formant może być wykonywane na wiele sposobów, np. uniemożliwia zasobów tworzony w niewłaściwej lokalizacji Wymuszanie użycia tagu wspólny i spójny lub inspekcji istniejących zasobów dla odpowiednich konfiguracje i ustawienia. We wszystkich przypadkach dane są generowane przez zasady, aby umożliwić Ci zrozumieć, stan zgodności środowiska.

Istnieje kilka sposobów dostęp do informacji o zgodności generowane przez zasady i inicjatywy przypisania:

- Przy użyciu [portalu Azure](#portal)
- Za pomocą [wiersza polecenia](#command_line) skryptów

Przed patrzeć metody raport dotyczący zgodności, Przyjrzyjmy się gdy zaktualizowano informacje o zgodności oraz częstotliwości i zdarzenia, które mogą powodować cykl oceny.

## <a name="evaluation-triggers"></a>Wyzwalacze oceny

Wyniki cykl oceny ukończone są uwzględniane w `Microsoft.PolicyInsights` dostawcy zasobów za pomocą `PolicyStates` i `PolicyEvents` operacji. Aby uzyskać więcej informacji o opcjach i funkcjach interfejsu API REST, zasad szczegółowych informacji, zobacz [Insights zasad](/rest/api/policy-insights/).

Obliczanie przypisane zasady i inicjatyw się tak zdarzyć w wyniku różnych zdarzeń:

- Zasady lub inicjatywy nowo jest przypisany do zakresu. W takim przypadku trwa około 30 minut do przypisania do zastosowania do określonego zakresu. Po zastosowaniu go cykl oceny rozpoczyna się dla zasobów w ramach tego zakresu względem nowo przypisanych zasad lub inicjatywy i w zależności od wpływu używanego przez zasady lub inicjatywy zasoby są oznaczone jako zgodne lub niezgodne. Inicjatywy porównywany duży zakres zasobów lub duże zasad może trwać, więc ukończy nie predefiniowanych oczekiwania podczas cyklu oceny. Po zakończeniu pracy, wyniki zgodności zaktualizowane są dostępne w portalu i zestawy SDK.
- Aktualizacji zasad lub inicjatywy już przypisany do zakresu. Cykl oceny i czas, w tym scenariuszu są takie same jak nowe przypisanie do zakresu.
- Zasób jest wdrażana na zakres przypisania za pomocą Menedżera zasobów, REST, Azure CLI lub Azure PowerShell. W tym scenariuszu zdarzeń efekt (append, inspekcji, Odmów, wdrażania) i informacje o stanie zgodności stają się dostępne w portalu i zestawy SDK około 15 minut.
- Cykl oceny zgodności standardowa. Co 24 godziny, przypisania są automatycznie ponownie oceniane. Inicjatywy porównywany duży zakres zasobów lub duże zasad może trwać, więc ukończy nie predefiniowanych oczekiwania podczas cyklu oceny. Po zakończeniu pracy, wyniki zgodności zaktualizowane są dostępne w portalu i zestawy SDK.

## <a name="how-compliance-works"></a>Jak działa zgodności

W przypisaniu zasobu nie jest zgodny, jeśli nie będzie zgodna z zasadami lub inicjatywy reguły. W poniższej tabeli przedstawiono, jak inne zasady działają efekty oceny warunku dla Wynikowy stan zgodności:

| Stan zasobu | Efekt | Ocena zasad | Stan zgodności |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Niezgodne |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Zgodne |
| Nowa | Audit, AuditIfNotExist\* | True | Niezgodne |
| Nowa | Audit, AuditIfNotExist\* | False | Zgodne |

\* Efekty Append, DeployIfNotExist i AuditIfNotExist wymagają instrukcji IF z wartością TRUE.
Ponadto efekty wymagają, aby warunek istnienia miał wartość FALSE, aby być niezgodnymi. W przypadku wartości TRUE warunek IF wyzwala ocenę warunku istnienia dla powiązanych zasobów.

Aby lepiej zrozumieć, jak zasoby są oznaczone jako niezgodna, Użyjmy przykład przypisanie zasady utworzone powyżej.

Załóżmy na przykład, ma grupa zasobów — ContsoRG, z niektórych kont magazynu (wyróżnionych kolorem czerwonym), które są dostępne do sieci publicznych.

![Konta magazynu uwidaczniany w sieciach publicznych](media/policy-insights/resource-group01.png)

W tym przykładzie należy zachować ostrożność przy zagrożenia bezpieczeństwa. Teraz, po utworzeniu przypisania zasad jest obliczane dla wszystkich kont magazynu w grupie zasobów ContosoRG. Inspekcja on trzy konta magazynu niezgodnych, w związku z tym zmienianie ich stany do **niezgodnych.**

![Przeprowadzono inspekcję kont magazynu niezgodnych](media/policy-insights/resource-group03.png)

## <a name="portal"></a>Portal

Azure portal ilustrację graficznego środowisko wizualizacji i zrozumienie stanu zgodności w danym środowisku. Na **zasad** strony, **omówienie** opcja zawiera szczegółowe informacje o dostępnych zakresów zgodności zasad i inicjatyw. Oprócz stanu zgodności oraz liczby na przypisanie zawiera wykres przedstawiający zgodności w ciągu ostatnich siedmiu dni. **Zgodności** strona zawiera wiele z tych informacji (z wyjątkiem wykres), ale zapewnia dodatkowych opcji filtrowania i sortowania.

![Strona zasad zgodności](media/policy-compliance/compliance-page.png)

Zgodnie z inicjatywy lub zasad mogą być przypisane do różnych zakresów, należy pamiętać, w tabeli zakres dla każdego przydziału i rodzaj definicji, która została przypisana do tego zakresu. Podawane są również liczba niezgodnych zasad i niezgodnych zasobów dla każdego przydziału. Kliknięcie inicjatywy w tabeli lub zasad zapewnia lepszy przyjrzeć się zgodności dla danego przydziału.

![Szczegóły zgodności zasad](media/policy-compliance/compliance-details.png)

Podczas listy zasobów na **niezgodne zasoby** kartę odzwierciedla stan oceny istniejących zasobów dla bieżącego przydziału zdarzenia (append, inspekcji, Odmów, wdrażanie) wyzwalane przez żądanie, aby utworzyć zasób są wyświetlany w obszarze **zdarzenia** kartę.

![Zdarzenia dotyczące zasad zgodności](media/policy-compliance/compliance-events.png)

Kliknij prawym przyciskiem myszy w wierszu zdarzenia, które chcesz zbierać więcej szczegółowych informacji o, a następnie wybierz **Pokaż dzienniki aktywności**. Stronie dziennik aktywności otwiera się i jest wstępnie filtrowane do wyszukiwania, wyświetlanie szczegółów zdarzenia i przydziału. Dziennik zawiera dodatkowy kontekst oraz informacje o tych zdarzeń.

![Dziennik aktywności zasad zgodności](media/policy-compliance/compliance-activitylog.png)

## <a name="command-line"></a>Wiersz polecenia

Te same informacje, które są dostępne w portalu można pobrać bezpośrednio za pomocą interfejsu API REST (łącznie z [ARMClient](https://github.com/projectkudu/ARMClient)) lub Azure PowerShell przy użyciu interfejsu API REST. Aby uzyskać szczegółowe informacje na temat interfejsu API REST, zobacz [Insights zasad](/rest/api/policy-insights/) odwołania. Strony podręcznika interfejsu API REST na znajduje się zielony "Spróbuj on" przycisk każdej operacji umożliwiający spróbuj go bezpośrednio w przeglądarce.

Aby użyć w poniższych przykładach programu Azure PowerShell, utworzenia tokenu uwierzytelniania z tego przykładu kodu. Następnie zastąp $restUri żądanego ciągu w przykładach, można pobrać obiekt JSON, który można następnie analizować.

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>Podsumowanie wyników

Przy użyciu interfejsu API REST, podsumowania mogą być wykonane przez grupę zarządzania, subskrypcji, grupy zasobów, zasobów, inicjatywy, zasad, przypisanie poziomu subskrypcji lub przypisanie poziomu grupy zasobów. Oto przykład podsumowania na poziomie subskrypcji przy użyciu zasad wglądu [Podsumuj subskrypcji](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

Dane wyjściowe zawiera podsumowanie subskrypcji. W przykładzie danych wyjściowych poniżej podsumowania zgodności podlegają **value.results.nonCompliantResources** i **value.results.nonCompliantPolicies**. To żądanie zawiera dalsze szczegóły, w tym każdego przypisania, które składają się niezgodny z liczb i informacje definicji dla każdego przydziału. Każdy obiekt zasad w hierarchii zawiera **queryResultsUri** można uzyskać dodatkowe szczegóły na tym poziomie.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Zapytanie dotyczące zasobów

Korzystanie z powyższego przykładu **value.policyAssignments.policyDefinitions.results.queryResultsUri** przewidzianych nam z przykładowym identyfikatora Uri pobierania wszystkich niezgodnych zasobów dla definicji szczegółowe zasady. Spojrzenie na **$filter** wartość IsCompliant jest równe (eq) na wartość false, PolicyAssignmentId określono definicji zasad, a następnie PolicyDefinitionId, sama.
Przyczyna tym PolicyAssignmentId w filtrze jest ponieważ PolicyDefinitionId może znajdować się w kilku zasad lub inicjatywy przydziałów, przy użyciu różnych zakresów. Określenie zarówno PolicyAssignmentId i PolicyDefinitionId, firma Microsoft może być jawne w wynikach, który czekamy na. Wcześniej, użyliśmy **najnowsze** dla PolicyStates (jedyną dozwoloną wartość **policyStatesSummaryResource** na operatorze Podsumuj subskrypcji), który automatycznie ustawia  **z** i **do** okno czasu ostatnich 24 godzin.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Przykład odpowiedzi poniżej została wycofana pokazywane tylko pojedynczy zasób niezgodnych skrócenia (należy pamiętać, że @odata.count jest w rzeczywistości 15 i odpowiada liczba niezgodnych zasobów w powyższym przykładzie). Szczegółowe odpowiedzi zawiera kilka fragmentów danych dotyczących zasobów, zasady (lub inicjatywy) oraz przypisanie. Zwróć uwagę, że możesz też sprawdzić, jakie przypisania parametry zostały przekazane do definicji zasad.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Wyświetlanie zdarzeń

Wynik oceny zasad jest generowany, gdy zasób jest tworzony lub aktualizowany. Wyniki są nazywane _zdarzenia dotyczące zasad_. Użyj następujący identyfikator Uri, aby wyświetlić ostatnie zdarzenia zasady skojarzone z subskrypcją.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Wyniki powinny wyglądać podobnie do następujących:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Aby uzyskać więcej informacji o zapytaniach dotyczących zdarzenia dotyczące zasad, zobacz [zdarzenia dotyczące zasad](/rest/api/policy-insights/policyevents) artykule.

### <a name="azure-powershell-preview"></a>Program Azure PowerShell (wersja zapoznawcza)

Moduł Azure PowerShell dla zasad nie jest jeszcze końcowego, ale jest obecnie dostępna w galerii programu PowerShell jako [wersja preview](https://www.powershellgallery.com/packages/AzureRM.PolicyInsights).
Jeśli PowerShellGet jest co najmniej wersji 1.6.0 (wymagane do obsługi elementów wersja wstępna), można pobrać przy użyciu wersji zapoznawczej `Install-Module` (Upewnij się, że masz najnowszą [programu Azure PowerShell](/powershell/azure/install-azurerm-ps) zainstalowany):

```powershell
# Download preview from PowerShell Gallery via PowerShellGet
Install-Module -Name AzureRM.PolicyInsights -AllowPrerelease

# Import the downloaded module
Import-Module AzureRM.PolicyInsights

# Login with Connect-AzureRmAccount if not using Cloud Shell
Connect-AzureRmAccount
```

Moduł Podgląd ma trzy polecenia cmdlet:

- `Get-AzureRmPolicyStateSummary`
- `Get-AzureRmPolicyState`
- `Get-AzureRmPolicyEvent`

Przykład: Pobieranie stanu podsumowania przypisanej zasady najwyższego poziomu o najwyższym numerze niezgodnych zasobów.

```powershell
PS > Get-AzureRmPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Przykład: Pobieranie rekordów stanu dla najbardziej ostatnio obliczone zasobów (wartość domyślna to przez sygnaturą czasową w kolejności malejącej).

```powershell
PS > Get-AzureRmPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Przykład: Pobieranie szczegółów dla wszystkich zasobów niezgodnych sieci wirtualnej.

```powershell
PS > Get-AzureRmPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Przykład: Pobieranie zdarzenia związane z zasobami niezgodnych sieci wirtualnej, które wystąpiły po określonej dacie.

```powershell
PS > Get-AzureRmPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

**PrincipalOid** pola może służyć do pobrania określonego użytkownika za pomocą polecenia cmdlet programu Azure PowerShell `Get-AzureRmADUser`. Zastąp **{principalOid}** z odpowiedzią, Pobierz z poprzedniego przykładu.

```powershell
PS > (Get-AzureRmADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="log-analytics"></a>Log Analytics

Jeśli masz [analizy dzienników](../log-analytics/log-analytics-overview.md) obszaru roboczego z `AzureActivity` rozwiązania powiązany z subskrypcją, a następnie można również wyświetlić wyniki zgodności z systemem innym niż cykl oceny przy użyciu prostego zapytania Kusto i `AzureActivity` tabeli. Szczegóły braku zgodności w analizy dzienników oznacza to również czy alerty może zostać skonfigurowany przez niezgodność na konkretnego zasobu, grupy zasobów lub nawet progu niezgodnych elementów, takich jak więcej niż 10 w ostatnich 24 godzin.

![Zasady zgodności przy użyciu analizy dzienników](media/policy-compliance/compliance-loganalytics.png)

## <a name="next-steps"></a>Kolejne kroki

- Przegląd [struktura definicji zasad](policy-definition.md).
- Przegląd [opis skutków zasad](policy-effects.md).
- Grupa zarządzania jest z przeglądu [organizowania zasobów z grupami zarządzania Azure](../azure-resource-manager/management-groups-overview.md)