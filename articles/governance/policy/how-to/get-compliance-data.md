---
title: Pobierz dane zgodności zasad
description: Azure Policy oceny i efekty określają zgodność. Dowiedz się, jak uzyskać szczegóły zgodności.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: ff50619d7b3d5bc803e8ee8d9e4cbf4389a4191f
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978082"
---
# <a name="get-compliance-data-of-azure-resources"></a>Pobieranie danych zgodności zasobów platformy Azure

Jednym z największych korzyści z Azure Policy jest wgląd i kontrola nad zasobami w ramach subskrypcji lub [grupy zarządzania](../../management-groups/overview.md) subskrypcjami. Ta kontrolka może być przeprowadzana na wiele różnych sposobów, takich jak uniemożliwianie tworzenia zasobów w niewłaściwej lokalizacji, wymuszanie wspólnego i spójnego użycia tagów lub Inspekcja istniejących zasobów dla odpowiednich konfiguracji i ustawień. We wszystkich przypadkach dane są generowane przez Azure Policy, aby umożliwić zrozumienie stanu zgodności środowiska.

Istnieje kilka sposobów uzyskiwania dostępu do informacji o zgodności wygenerowanych przez przydziały zasad i inicjatyw:

- Korzystanie z [Azure Portal](#portal)
- Za pomocą skryptów [wiersza polecenia](#command-line)

Przed przystąpieniem do metod zgłaszania zgodności Sprawdź, czy informacje o zgodności są aktualizowane oraz częstotliwość i zdarzenia wyzwalające cykl oceniania.

> [!WARNING]
> Jeśli stan zgodności jest raportowany jako **niezarejestrowany**, sprawdź, czy dostawca zasobów **Microsoft. PolicyInsights** jest zarejestrowany i czy użytkownik ma odpowiednie uprawnienia kontroli dostępu opartej na rolach (RBAC), zgodnie z opisem w temacie [RBAC w Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Wyzwalacze oceny

Wyniki ukończonego cyklu oceny są dostępne w ramach dostawcy zasobów `Microsoft.PolicyInsights` za pomocą operacji `PolicyStates` i `PolicyEvents`. Aby uzyskać więcej informacji na temat operacji interfejsu API REST usługi Azure Policy Insights, zobacz [Azure Policy Insights](/rest/api/policy-insights/).

Oceny przypisanych zasad i inicjatyw odbywają się w wyniku różnych zdarzeń:

- Zasady lub inicjatywa są nowo przypisane do zakresu. Przypisanie do określonego zakresu zajmie około 30 minut. Po zastosowaniu cykl oceny rozpoczyna się dla zasobów należących do tego zakresu od nowo przypisanych zasad lub inicjatyw, a w zależności od skutków używanych przez zasady lub inicjatywę zasoby są oznaczane jako zgodne lub niezgodne. Duże zasady lub inicjatywy oceniane w odniesieniu do dużego zakresu zasobów mogą zająć dużo czasu. W związku z tym nie istnieje wstępnie zdefiniowane oczekiwanie, kiedy cykl szacowania zostanie ukończony. Po jego zakończeniu zaktualizowane wyniki zgodności są dostępne w portalu i zestawach SDK.

- Zasady lub inicjatywa już przypisane do zakresu zostały zaktualizowane. Cykl oceny i czas dla tego scenariusza są takie same jak w przypadku nowego przypisania do zakresu.

- Zasób jest wdrażany w zakresie z przypisaniem za pośrednictwem Menedżer zasobów, REST, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. W tym scenariuszu zdarzenie wpływu (dołączanie, inspekcja, odmowa, wdrożenie) i informacje o stanie zgodnym dla poszczególnych zasobów staną się dostępne w portalu i zestawach SDK około 15 minut później. To zdarzenie nie powoduje oceny innych zasobów.

- Cykl oceny zgodności standardowej. Co 24 godziny, przydziały są automatycznie oceniane. Duże zasady lub inicjatywy wielu zasobów mogą zająć dużo czasu, dlatego nie istnieje wstępnie zdefiniowane oczekiwanie, kiedy cykl oceny zakończy się. Po jego zakończeniu zaktualizowane wyniki zgodności są dostępne w portalu i zestawach SDK.

- Dostawca zasobów [konfiguracji gościa](../concepts/guest-configuration.md) został zaktualizowany o szczegóły zgodności przez zasób zarządzany.

- Skanowanie na żądanie

### <a name="on-demand-evaluation-scan"></a>Skanowanie w celu oceny na żądanie

Skanowanie w celu uzyskania subskrypcji lub grupy zasobów można rozpocząć przy użyciu wywołania interfejsu API REST. To skanowanie jest procesem asynchronicznym. W związku z tym punkt końcowy REST do uruchomienia skanowania nie czeka na zakończenie skanowania. Zamiast tego zapewnia identyfikator URI, aby wykonać zapytanie o stan żądana oceny.

W każdym identyfikatorze URI interfejsu API REST istnieją zmienne, które są używane do zamiany na własne wartości:

- `{YourRG}` — Zamień na nazwę grupy zasobów
- `{subscriptionId}` — Zastąp IDENTYFIKATORem subskrypcji

Skanowanie obsługuje Obliczanie zasobów w ramach subskrypcji lub grupy zasobów. Rozpocznij skanowanie według zakresu za pomocą polecenia interfejsu API REST **post** przy użyciu następujących struktur identyfikatorów URI:

- Ramach

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Grupa zasobów

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

Wywołanie zwraca stan **Zaakceptowany 202** . Uwzględniony w nagłówku odpowiedzi to właściwość **Location** o następującym formacie:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` jest generowana statycznie dla żądanego zakresu. Jeśli w zakresie jest już uruchomione skanowanie na żądanie, nowe skanowanie nie zostanie uruchomione. Zamiast tego nowe żądanie ma ten sam identyfikator URI **lokalizacji** `{ResourceContainerGUID}` dla stanu. Polecenie API REST **Get** do identyfikatora URI **lokalizacji** zwraca **202 zaakceptowane** podczas obliczania. Po zakończeniu skanowania ewaluacyjnego zostanie zwrócony stan **200 OK** . Treść ukończonego skanowania jest odpowiedzią JSON o stanie:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Jak działa zgodność

W przypisaniu zasób nie jest **zgodny** , jeśli nie przestrzega reguł zasad lub inicjatyw.
W poniższej tabeli przedstawiono, w jaki sposób różne skutki zasad działają w przypadku oceny warunku dotyczącego stanu zgodności:

| Stan zasobu | Efekt | Ocena zasad | Stan zgodności |
| --- | --- | --- | --- |
| Istniejący | Odmów, Audit, Append @ no__t-0, DeployIfNotExist @ no__t-1, AuditIfNotExist @ no__t-2 | Oznacza | Niezgodne |
| Istniejący | Odmów, Audit, Append @ no__t-0, DeployIfNotExist @ no__t-1, AuditIfNotExist @ no__t-2 | False | zgodnych |
| New | Inspekcja, AuditIfNotExist @ no__t-0 | Oznacza | Niezgodne |
| New | Inspekcja, AuditIfNotExist @ no__t-0 | False | zgodnych |

\* efekty append, DeployIfNotExist i AuditIfNotExist wymagają, aby instrukcja IF była prawdziwa.
Efekty muszą także wymagać, aby warunek istnienia miał wartość FALSE, aby nie był zgodny. W przypadku wartości TRUE warunek IF wyzwala ocenę stanu istnienia powiązanych zasobów.

Załóżmy na przykład, że masz grupę zasobów — ContsoRG z pewnymi kontami magazynu (wyróżnioną kolorem czerwonym), które są dostępne w sieciach publicznych.

![Konta magazynu udostępniane w sieciach publicznych](../media/getting-compliance-data/resource-group01.png)

W tym przykładzie należy zastanowić się nad zagrożeniem bezpieczeństwa. Po utworzeniu przypisania zasad jest ono oceniane dla wszystkich kont magazynu w grupie zasobów ContosoRG. Przeprowadza inspekcję trzech niezgodnych kont magazynu, wskutek zmiany ich Stanów na **niezgodne.**

![Inspekcja niezgodnych kont magazynu](../media/getting-compliance-data/resource-group03.png)

Oprócz **zgodnych** i **niezgodnych**zasad i zasobów mają trzy inne stany:

- **Konflikt**: istnieją co najmniej dwie zasady z regułami powodującymi konflikt. Na przykład dwie zasady dołączają ten sam tag z różnymi wartościami.
- **Nierozpoczęte**: cykl oceniania nie został uruchomiony dla zasad lub zasobów.
- **Nie zarejestrowano**: dostawca zasobów Azure Policy nie został zarejestrowany lub zalogowane konto nie ma uprawnień do odczytu danych zgodności.

Azure Policy używa pól **Typ** i **Nazwa** w definicji, aby określić, czy zasób jest zgodny. Gdy zasób jest zgodny, jest uznawany za stosowany i ma stan **zgodne** lub **niezgodne**. Jeśli **Typ** lub **Nazwa** jest jedyną właściwością w definicji, wszystkie zasoby są uważane za stosowane i są oceniane.

Wartość procentowa zgodności jest określana przez podzielenie **zgodnych** zasobów przez _Łączne zasoby_.
_Łączna liczba zasobów_ jest definiowana jako suma **zgodnych**, **niezgodnych**i **sprzecznych** zasobów. Ogólne numery zgodności są sumą różnych zasobów, które są **zgodne** , podzieloną przez sumę wszystkich różnych zasobów. Na poniższej ilustracji przedstawiono 20 odrębnych zasobów, które mają zastosowanie i tylko jeden z nich jest **niezgodny**. Ogólna zgodność zasobów wynosi 95% (19 z 20).

![Przykład zgodności z zasadami ze strony zgodności](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portal

Azure Portal przedstawia graficzne środowisko wizualizacji i zrozumienie stanu zgodności w danym środowisku. Na stronie **zasady** opcja **Przegląd** zawiera szczegółowe informacje dotyczące dostępnych zakresów na potrzeby zgodności obu zasad i inicjatyw. Wraz ze stanem zgodności i liczbą na przypisanie zawiera wykres pokazujący zgodność w ciągu ostatnich siedmiu dni. Strona **zgodność** zawiera wiele tych samych informacji (poza wykresem), ale zapewnia dodatkowe opcje filtrowania i sortowania.

![Przykład strony zgodność Azure Policy](../media/getting-compliance-data/compliance-page.png)

Ze względu na to, że zasady lub inicjatywy mogą być przypisane do różnych zakresów, tabela zawiera zakres dla każdego przypisania i typ przypisanej definicji. Podano również liczbę niezgodnych zasobów i niezgodnych zasad dla każdego przypisania. Kliknięcie zasad lub inicjatywy w tabeli zapewnia dokładniejsze sprawdzenie zgodności dla danego przypisania.

![Przykład strony szczegółów zgodności Azure Policy](../media/getting-compliance-data/compliance-details.png)

Lista zasobów na karcie **zgodność zasobów** zawiera stan oceny istniejących zasobów dla bieżącego przypisania. Karta domyślnie nie jest **zgodna**, ale można ją filtrować.
Zdarzenia (append, Audit, Deny, Deploy) wyzwalane przez żądanie utworzenia zasobu są wyświetlane na karcie **zdarzenia** .

![Przykład Azure Policy zdarzeń zgodności](../media/getting-compliance-data/compliance-events.png)

Kliknij prawym przyciskiem myszy wiersz zdarzenia, dla którego chcesz zebrać więcej szczegółów, a następnie wybierz pozycję **Pokaż dzienniki aktywności**. Zostanie otwarta strona dziennika aktywności, która jest wstępnie filtrowana do wyszukiwania pokazującego szczegóły dotyczące przypisania i zdarzeń. Dziennik aktywności zawiera dodatkowy kontekst i informacje dotyczące tych zdarzeń.

![Przykład dziennika aktywności zgodności Azure Policy](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Zrozumienie braku zgodności

<a name="change-history-preview"></a>

Gdy zasoby są określone jako **niezgodne**, istnieje wiele możliwych przyczyn. Aby określić przyczynę braku zgodności zasobu lub aby znaleźć zmianę odpowiedzialną, zobacz [Określanie, które nie](./determine-non-compliance.md)są **zgodne** .

## <a name="command-line"></a>Wiersz polecenia

Te same informacje dostępne w portalu można pobrać przy użyciu interfejsu API REST (w tym z [ARMClient](https://github.com/projectkudu/ARMClient)) lub Azure PowerShell. Aby uzyskać szczegółowe informacje na temat interfejsu API REST, zobacz informacje dotyczące [Azure Policy Insights](/rest/api/policy-insights/) . Strony referencyjne interfejsu API REST mają zielony przycisk "Wypróbuj go" dla każdej operacji, która pozwala na wypróbowanie jej w przeglądarce.

Aby skorzystać z poniższych przykładów w Azure PowerShell, Skonstruuj token uwierzytelniania przy użyciu tego przykładowego kodu. Następnie zastąp $restUri ciągiem w przykładach, aby pobrać obiekt JSON, który można następnie przeanalizować.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
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

W przypadku interfejsu API REST podsumowanie może być wykonywane przez kontener, definicję lub przypisanie. Oto przykład podsumowania na poziomie subskrypcji przy użyciu [podsumowania Azure Policy wgląd w subskrypcję](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

Dane wyjściowe podsumowują subskrypcję. W poniższym przykładzie danych wyjściowych Podsumowanie zgodności ma **wartość. results. nonCompliantResources** i **Value. results. nonCompliantPolicies**. To żądanie zawiera dalsze szczegóły, w tym każde przypisanie, które wprowadziło niezgodne numery i informacje o definicji dla każdego przypisania. Każdy obiekt zasad w hierarchii zawiera element **queryResultsUri** , którego można użyć do uzyskania dodatkowych szczegółów na tym poziomie.

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

### <a name="query-for-resources"></a>Zapytanie o zasoby

W powyższym przykładzie **wartość. policyAssignments. policyDefinitions. results. queryResultsUri** zawiera przykładowy identyfikator URI dla wszystkich niezgodnych zasobów dla określonej definicji zasad. Patrząc na wartość **$Filter** , isequality jest równe (EQ) na false, PolicyAssignmentId jest określony dla definicji zasad, a następnie sam PolicyDefinitionId. Powodem dołączenia PolicyAssignmentId w filtrze jest fakt, że PolicyDefinitionId może istnieć w kilku przypisaniach zasad lub inicjatyw z różnymi zakresami. Określając zarówno PolicyAssignmentId, jak i PolicyDefinitionId, możemy być jawne w wynikach, których szukamy. Wcześniej w przypadku usługi PolicyStates, która była używana w **najnowszej wersji**, która automatycznie ustawia okno **od** i **do** godziny z ostatnich 24 godzin.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Przykładowa odpowiedź poniżej została przycięta do jednego niezgodnego zasobu dla zwięzłości. Szczegółowa odpowiedź zawiera kilka danych dotyczących zasobu, zasad lub inicjatywy oraz przydziału. Należy zauważyć, że parametry przypisania zostały przekazano do definicji zasad.

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

### <a name="view-events"></a>Wyświetl zdarzenia

Po utworzeniu lub zaktualizowaniu zasobu zostanie wygenerowane wyniki oceny zasad. Wyniki są nazywane _zdarzeniami zasad_. Użyj poniższego identyfikatora URI, aby wyświetlić ostatnie zdarzenia zasad skojarzone z subskrypcją.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Wyniki przypominają następujący przykład:

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

Aby uzyskać więcej informacji na temat wykonywania zapytań dotyczących zdarzeń zasad, zobacz artykuł dotyczący [zdarzeń Azure Policy](/rest/api/policy-insights/policyevents) .

### <a name="azure-powershell"></a>Azure PowerShell

Moduł Azure PowerShell dla Azure Policy jest dostępny w Galeria programu PowerShell jako [AZ. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights).
Za pomocą PowerShellGet można zainstalować moduł przy użyciu `Install-Module -Name Az.PolicyInsights` (Upewnij się, że zainstalowano najnowszą [Azure PowerShell](/powershell/azure/install-az-ps) ):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Moduł zawiera następujące polecenia cmdlet:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Przykład: pobieranie podsumowania stanu dla najwyżej przypisanych zasad o największej liczbie niezgodnych zasobów.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Przykład: Pobieranie rekordu stanu dla ostatnio obliczonego zasobu (wartość domyślna to sygnatura czasowa w kolejności malejącej).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

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

Przykład: pobieranie szczegółów dla wszystkich niezgodnych zasobów sieci wirtualnej.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

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

Przykład: pobieranie zdarzeń związanych z niezgodnymi zasobami sieci wirtualnej, które wystąpiły po określonej dacie.

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

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

Pole **PrincipalOid** może służyć do uzyskiwania określonego użytkownika za pomocą polecenia cmdlet Azure PowerShell `Get-AzADUser`. Zastąp **{principalOid}** odpowiedzią uzyskaną z poprzedniego przykładu.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Dzienniki Azure Monitor

Jeśli masz [obszar roboczy log Analytics](../../../log-analytics/log-analytics-overview.md) z `AzureActivity` z [rozwiązania Activity Log Analytics](../../../azure-monitor/platform/activity-log-collect.md) powiązanego z subskrypcją, możesz również wyświetlić wyniki niezgodności z cyklu oceny przy użyciu prostych zapytań Kusto i tabeli `AzureActivity`. Dzięki szczegółowym dziennikom Azure Monitor alerty można skonfigurować tak, aby oglądać niezgodność.


![Azure Policy zgodności przy użyciu dzienników Azure Monitor](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Zapoznaj się ze [strukturą definicji Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [Informacje o skutkach zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).