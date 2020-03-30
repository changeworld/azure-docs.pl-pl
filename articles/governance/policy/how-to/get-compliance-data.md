---
title: Uzyskaj dane dotyczące zgodności zasad
description: Oceny i efekty zasad platformy Azure określają zgodność. Dowiedz się, jak uzyskać szczegółowe informacje o zgodności zasobów platformy Azure.
ms.date: 02/01/2019
ms.topic: how-to
ms.openlocfilehash: 891c9c72d8e83dc8f9adb930e8ebd11b70f6aad8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280640"
---
# <a name="get-compliance-data-of-azure-resources"></a>Uzyskaj dane dotyczące zgodności zasobów platformy Azure

Jedną z największych zalet usługi Azure Policy jest wgląd i kontrola, które zapewnia zasoby w subskrypcji lub [grupie zarządzania](../../management-groups/overview.md) subskrypcji. Ten formant może być wykonywany na wiele różnych sposobów, takich jak zapobieganie tworzenie zasobów w niewłaściwej lokalizacji, wymuszanie użycia tagów typowych i spójnych lub inspekcja istniejących zasobów pod kątem odpowiednich konfiguracji i ustawień. We wszystkich przypadkach dane są generowane przez zasady platformy Azure, aby umożliwić zrozumienie stanu zgodności środowiska.

Istnieje kilka sposobów uzyskiwania dostępu do informacji o zgodności generowanych przez przypisania zasad i inicjatyw:

- Korzystanie z [witryny Azure portal](#portal)
- Za pomocą skryptów [wiersza polecenia](#command-line)

Zanim przyjrzyjmy się metodom raportowania zgodności, przyjrzyjmy się, kiedy informacje o zgodności są aktualizowane i częstotliwość i zdarzenia, które wyzwalają cykl oceny.

> [!WARNING]
> Jeśli stan zgodności jest zgłaszany jako **Nie zarejestrowany,** sprawdź, czy dostawca zasobów **Microsoft.PolicyInsights** jest zarejestrowany i czy użytkownik ma odpowiednie uprawnienia kontroli dostępu opartej na rolach (RBAC), zgodnie z opisem w [rbac w usłudze Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Wyzwalacze oceny

Wyniki ukończonego cyklu oceny są `Microsoft.PolicyInsights` dostępne `PolicyStates` w `PolicyEvents` dostawcy zasobów za pośrednictwem i operacji. Aby uzyskać więcej informacji na temat operacji interfejsu API REST usługi Azure Policy Insights, zobacz [Usługa Azure Policy Insights](/rest/api/policy-insights/).

Oceny przypisanych polityk i inicjatyw są wynikiem różnych zdarzeń:

- Zasady lub inicjatywy jest nowo przypisany do zakresu. Trwa około 30 minut dla przypisania, które mają być stosowane do zdefiniowanego zakresu. Po zastosowaniu cykl oceny rozpoczyna się dla zasobów w tym zakresie względem nowo przypisanych zasad lub inicjatywy i w zależności od efektów używanych przez zasady lub inicjatywę zasoby są oznaczane jako zgodne lub niezgodne. Duża polityka lub inicjatywa oceniona na podstawie dużego zakresu zasobów może zająć trochę czasu. W związku z tym nie ma wstępnie zdefiniowane oczekiwania, kiedy zakończy się cykl oceny. Po jego zakończeniu zaktualizowane wyniki zgodności są dostępne w portalu i w skusie SDK.

- Zasady lub inicjatywy już przypisane do zakresu jest aktualizowana. Cykl oceny i czas dla tego scenariusza jest taka sama jak dla nowego przypisania do zakresu.

- Zasób jest wdrażany w zakresie z przydziałem za pośrednictwem Menedżera zasobów, REST, interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell. W tym scenariuszu zdarzenie efektu (dołącz, inspekcji, odmów, wdrożenia) i zgodne informacje o stanie dla poszczególnych zasobów stają się dostępne w portalu i SDK około 15 minut później. To zdarzenie nie powoduje oceny innych zasobów.

- Standardowy cykl oceny zgodności. Raz na 24 godziny przypisania są automatycznie ponownie dokonywane. Duża zasada lub inicjatywa wielu zasobów może zająć trochę czasu, więc nie ma wstępnie zdefiniowanego oczekiwania, kiedy zakończy się cykl oceny. Po jego zakończeniu zaktualizowane wyniki zgodności są dostępne w portalu i w skusie SDK.

- Dostawca zasobów [konfiguracji gościa](../concepts/guest-configuration.md) jest aktualizowany o szczegóły zgodności przez zasób zarządzany.

- Skanowanie na żądanie

### <a name="on-demand-evaluation-scan"></a>Skanowanie oceny na żądanie

Skanowanie ewaluacyjne dla subskrypcji lub grupy zasobów można uruchomić z wywołaniem interfejsu API REST. To skanowanie jest procesem asynchronizacyjnym. W związku z tym punkt końcowy REST, aby rozpocząć skanowanie nie czeka, aż skanowanie zostanie zakończone, aby odpowiedzieć. Zamiast tego udostępnia identyfikator URI do kwerendy stan żądanej oceny.

Każdy identyfikator URI interfejsu API REST zawiera używane zmienne, które musisz zastąpić własnymi wartościami:

- `{YourRG}`- Zastąp nazwą swojej grupy zasobów
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji

Skanowanie obsługuje ocenę zasobów w ramach subskrypcji lub w grupie zasobów. Rozpocznij skanowanie według zakresu za pomocą polecenia REST API **POST** przy użyciu następujących struktur URI:

- Subskrypcja

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Grupa zasobów

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

Wywołanie zwraca **stan 202 Zaakceptowane.** W nagłówku odpowiedzi znajduje się **właściwość Lokalizacja** o następującym formacie:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}`jest generowany statycznie dla żądanego zakresu. Jeśli zakres jest już uruchomiony skanowania na żądanie, nowe skanowanie nie jest uruchomiona. Zamiast tego nowe żądanie jest `{ResourceContainerGUID}` podana identyfikator URI tej **samej lokalizacji** dla stanu. Polecenie REST API **GET** do **identyfikatora** URI lokalizacji zwraca **202 zaakceptowane,** gdy trwa ocena. Po zakończeniu skanowania ewaluacyjnego zwraca stan **200 OK.** Treść zakończonego skanowania jest odpowiedzią JSON o stanie:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Jak działa zgodność z przepisami

W przypisaniu zasób jest **niezgodny,** jeśli nie jest zgodny z zasadami lub regułami inicjatywy.
W poniższej tabeli pokazano, jak różne efekty zasad działają z oceną stanu stanu zgodności wynikowego:

| Stan zasobu | Efekt | Ocena polityki | Stan zgodności |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Niezgodne |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Zgodne |
| Nowa | Audit, AuditIfNotExist\* | True | Niezgodne |
| Nowa | Audit, AuditIfNotExist\* | False | Zgodne |

\* Efekty Append, DeployIfNotExist i AuditIfNotExist wymagają instrukcji IF z wartością TRUE.
Ponadto efekty wymagają, aby warunek istnienia miał wartość FALSE, aby być niezgodnymi. W przypadku wartości TRUE warunek IF wyzwala ocenę warunku istnienia dla powiązanych zasobów.

Załóżmy na przykład, że masz grupę zasobów — ContsoRG, z niektórych kont magazynu (wyróżnione na czerwono), które są narażone na sieci publiczne.

![Konta magazynu narażone na sieci publiczne](../media/getting-compliance-data/resource-group01.png)

W tym przykładzie należy uważać na zagrożenia bezpieczeństwa. Teraz, gdy utworzono przypisanie zasad, jest ono oceniane dla wszystkich kont magazynu w grupie zasobów ContosoRG. Kontroluje trzy niezgodne konta magazynu, zmieniając w związku z tym ich stany na **niezgodne.**

![Skontrolowane niezgodne konta magazynu](../media/getting-compliance-data/resource-group03.png)

Oprócz **zgodności** i **niezgodności,** zasady i zasoby mają trzy inne stany:

- **Konflikt:** istnieją co najmniej dwie zasady z regułami powodującymi konflikt. Na przykład dwie zasady dołączające ten sam tag z różnymi wartościami.
- **Nie rozpoczęto:** Cykl oceny nie został uruchomiony dla zasad lub zasobów.
- **Nie zarejestrowany:** Dostawca zasobów zasad platformy Azure nie został zarejestrowany lub konto zalogowane nie ma uprawnień do odczytu danych zgodności.

Usługa Azure Policy używa pól **typu** i **nazwy** w definicji, aby określić, czy zasób jest zgodny. Gdy zasób jest zgodny, jest uważany za odpowiedni i ma stan **zgodny** lub **niezgodny**. Jeśli **typ** lub **nazwa** jest jedyną właściwością w definicji, wszystkie zasoby są uważane za stosowne i są oceniane.

Procent zgodności jest określany przez podzielenie zasobów **zgodnych** przez _zasoby całkowite_.
_Zasoby ogółem_ są definiowane jako **suma**zasobów zgodnych, **niezgodnych i** **sprzecznych.** Ogólne liczby zgodności są sumą różnych zasobów, które są **zgodne** podzielone przez sumę wszystkich różnych zasobów. Na poniższej ilustracji znajduje się 20 różnych zasobów, które mają zastosowanie i tylko jeden jest **niezgodny**. Ogólna zgodność zasobów wynosi 95% (19 z 20).

![Przykład zgodności zasad ze strony Zgodność](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portal

Portal Azure prezentuje graficzne środowisko wizualizacji i zrozumienia stanu zgodności w środowisku. Na stronie **Zasady** opcja **Przegląd** zawiera szczegółowe informacje dotyczące dostępnych zakresów zgodności zarówno zasad, jak i inicjatyw. Wraz ze stanem zgodności i liczbą na przypisanie zawiera wykres przedstawiający zgodność z przepisami w ciągu ostatnich siedmiu dni. Strona **Zgodność** zawiera wiele tych samych informacji (z wyjątkiem wykresu), ale zawiera dodatkowe opcje filtrowania i sortowania.

![Przykład strony Zgodności z zasadami platformy Azure](../media/getting-compliance-data/compliance-page.png)

Ponieważ zasady lub inicjatywy mogą być przypisane do różnych zakresów, tabela zawiera zakres dla każdego przypisania i typ definicji, który został przypisany. Podano również liczbę niezgodnych zasobów i niezgodnych zasad dla każdego przypisania. Kliknięcie zasad lub inicjatywy w tabeli zapewnia głębsze spojrzenie na zgodność dla danego przypisania.

![Przykład strony Szczegóły zgodności zasad platformy Azure](../media/getting-compliance-data/compliance-details.png)

Lista zasobów na karcie **Zgodność zasobów** pokazuje stan oceny istniejących zasobów dla bieżącego przydziału. Karta domyślnie **ma wartość Niezgodna**, ale może być filtrowana.
Zdarzenia (dołącz, inspekcji, odmowy, wdrożenia) wyzwalane przez żądanie utworzenia zasobu są wyświetlane na karcie **Zdarzenia.**

> [!NOTE]
> W przypadku zasad aparatu AKS wyświetlany zasób jest grupą zasobów.

![Przykład zdarzeń zgodności zasad platformy Azure](../media/getting-compliance-data/compliance-events.png)

W przypadku zasobów [w trybie dostawcy zasobów](../concepts/definition-structure.md#resource-provider-modes) na karcie Zgodność **zasobów** zaznaczenie zasobu lub kliknięcie prawym przyciskiem myszy w wierszu i wybranie opcji Wyświetl **szczegóły zgodności** powoduje otwarcie szczegółów zgodności składnika. Ta strona oferuje również karty, aby zobaczyć zasady, które są przypisane do tego zasobu, zdarzenia, zdarzenia składników i historii zmian.

![Przykład szczegółów zgodności składnika zasad platformy Azure](../media/getting-compliance-data/compliance-components.png)

Wróć do strony zgodności zasobów, kliknij prawym przyciskiem myszy wiersz wydarzenia, na które chcesz zebrać więcej szczegółów, a następnie wybierz pozycję **Pokaż dzienniki aktywności**. Zostanie otwarta strona dziennika aktywności i jest wstępnie filtrowana do wyszukiwania z informacjami dotyczącymi przypisania i zdarzeń. Dziennik aktywności zawiera dodatkowy kontekst i informacje o tych zdarzeniach.

![Przykład dziennika działań zgodności zasad platformy Azure](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Zrozumienie niezgodności

<a name="change-history-preview"></a>

Gdy zasoby są określane jako **niezgodne**, istnieje wiele możliwych przyczyn. Aby ustalić przyczynę **niezgodności** zasobu lub znaleźć odpowiedzialną zmianę, zobacz [Określanie niezgodności](./determine-non-compliance.md).

## <a name="command-line"></a>Wiersz polecenia

Te same informacje dostępne w portalu można pobrać za pomocą interfejsu API REST (w tym z [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell i Azure CLI (wersja zapoznawcza).
Aby uzyskać szczegółowe informacje na temat interfejsu API REST, zobacz odwołanie [usługi Azure Policy Insights.](/rest/api/policy-insights/) Strony referencyjne interfejsu API REST mają zielony przycisk "Wypróbuj" na każdej operacji, który pozwala spróbować go w przeglądarce.

Użyj ARMClient lub podobne narzędzie do obsługi uwierzytelniania na platformie Azure dla przykładów interfejsu API REST.

### <a name="summarize-results"></a>Podsumuj wyniki

Za pomocą interfejsu API REST podsumowanie może być wykonywane przez kontener, definicję lub przypisanie. Oto przykład podsumowania na poziomie subskrypcji przy użyciu usługi Podsumowanie [subskrypcji](/rest/api/policy-insights/policystates/summarizeforsubscription)usługi Azure Policy Insight:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

Dane wyjściowe podsumowuje subskrypcji. W poniższym przykładzie dane wyjściowe podsumowane zgodność znajdują się pod **względem value.results.nonCompliantResources** i **value.results.nonCompliantPolicies**. To żądanie zawiera dalsze szczegóły, w tym każde przypisanie, które składa się na niezgodne numery i informacje o definicji dla każdego przypisania. Każdy obiekt zasad w hierarchii udostępnia **queryResultsUri,** który może służyć do uzyskania dodatkowych szczegółów na tym poziomie.

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

W powyższym przykładzie **value.policyAssignments.policyDefinitions.results.queryResultsUri** udostępnia przykładowy identyfikator Uri dla wszystkich niezgodnych zasobów dla określonej definicji zasad. Patrząc na **wartość $filter,** IsCompliant jest równa (eq) do false, PolicyAssignmentId jest określony dla definicji zasad, a następnie PolicyDefinitionId się. Powodem włączenia PolicyAssignmentId w filtrze jest, ponieważ PolicyDefinitionId może istnieć w kilku przypisaniach zasad lub inicjatyw o różnych zakresach. Określając zarówno PolicyAssignmentId i PolicyDefinitionId, możemy być jawne w wynikach, których szukamy. Wcześniej w przypadku policystates użyliśmy **najnowszych**, który automatycznie ustawia **z** i **do** przedziału czasu z ostatnich 24 godzin.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Poniższa przykładowa odpowiedź została przycięta do pojedynczego zasobu niezgodnego z wymogami dla zwięzłości. Szczegółowa odpowiedź zawiera kilka fragmentów danych dotyczących zasobu, zasad lub inicjatywy oraz przypisania. Należy zauważyć, że można również zobaczyć, jakie parametry przypisania zostały przekazane do definicji zasad.

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

Podczas tworzenia lub aktualizowania zasobu generowany jest wynik oceny zasad. Wyniki są nazywane _zdarzeniami zasad_. Użyj następującego identyfikatora Uri, aby wyświetlić ostatnie zdarzenia zasad skojarzone z subskrypcją.

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

Aby uzyskać więcej informacji na temat wykonywania zapytań o zdarzenia zasad, zobacz artykuł [referencyjny zdarzenia zasad platformy Azure.](/rest/api/policy-insights/policyevents)

### <a name="azure-powershell"></a>Azure PowerShell

Moduł programu Azure PowerShell for Azure Policy jest dostępny w Galerii programu PowerShell jako [Az.PolicyInsights.](https://www.powershellgallery.com/packages/Az.PolicyInsights)
Za pomocą programu PowerShellGet, `Install-Module -Name Az.PolicyInsights` można zainstalować moduł przy użyciu (upewnij się, że masz zainstalowaną najnowszą [usługę Azure PowerShell):](/powershell/azure/install-az-ps)

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Moduł posiada następujące polecenia cmdlet:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Przykład: Uzyskiwanie podsumowania stanu dla najwyższej przypisanej zasady z największą liczbą niezgodnych zasobów.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Przykład: Uzyskiwanie rekordu stanu dla ostatnio ocenianego zasobu (domyślnie jest to sygnatura czasowa w kolejności malejącej).

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

Przykład: Uzyskiwanie szczegółowych informacji na temat wszystkich niezgodnych zasobów sieci wirtualnej.

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

Przykład: Uzyskiwanie zdarzeń związanych z niezgodnymi zasobami sieci wirtualnej, które wystąpiły po określonej dacie.

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

Pole **PrincipalOid** może służyć do uzyskania określonego użytkownika za pomocą `Get-AzADUser`polecenia cmdlet programu Azure PowerShell. Zamień **{principalOid}** na odpowiedź otrzymaną z poprzedniego przykładu.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

Jeśli masz [obszar roboczy](../../../log-analytics/log-analytics-overview.md) `AzureActivity` usługi Log Analytics z rozwiązania usługi Activity Log [Analytics](../../../azure-monitor/platform/activity-log-collect.md) powiązanego z subskrypcją, możesz również `AzureActivity` wyświetlić wyniki niezgodności z cyklu oceny przy użyciu prostych zapytań Kusto i tabeli. Dzięki szczegółom w dziennikach usługi Azure Monitor alerty można skonfigurować tak, aby wyglądały na niezgodności.


![Zgodność zasad platformy Azure przy użyciu dzienników usługi Azure Monitor](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Następne kroki

- Przejrzyj przykłady w [przykładach zasad platformy Azure](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo tworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [korygować niezgodne zasoby](remediate-resources.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).