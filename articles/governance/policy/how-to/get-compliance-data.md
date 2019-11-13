---
title: Pobierz dane zgodności zasad
description: Azure oceny zasad i efekty określenia zgodności. Dowiedz się, jak można pobrać szczegółów zgodności.
ms.date: 02/01/2019
ms.topic: conceptual
ms.openlocfilehash: b460a3e70b5462be3fdc7f34dd7261d491a495b3
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959583"
---
# <a name="get-compliance-data-of-azure-resources"></a>Pobieranie danych zgodności zasobów platformy Azure

Jedną z największych zalet usługi Azure Policy jest wglądu i kontroli jest lepsza od zasobów w ramach subskrypcji lub [grupy zarządzania](../../management-groups/overview.md) subskrypcji. Ten formant może być wykonywana na wiele różnych sposobów, na przykład zapobieganie zasobów tworzonych w niewłaściwej lokalizacji, wymuszając użycie wspólny i spójny tag lub inspekcji istniejących zasobów, aby uzyskać odpowiednie konfiguracje i ustawienia. We wszystkich przypadkach dane są generowane przez Azure Policy, aby umożliwić zrozumienie stanu zgodności środowiska.

Istnieje kilka sposobów na dostęp do informacji o zgodności, generowane przez zasady i przypisań inicjatywy:

- Za pomocą [witryny Azure portal](#portal)
- Za pomocą [wiersza polecenia](#command-line) skryptów

Przed obejrzeniem metody sporządzić raport na temat zgodności, Przyjrzyjmy się po zaktualizowaniu informacje o zgodności oraz częstotliwości i zdarzenia, które mogą powodować cykl oceny.

> [!WARNING]
> Jeśli stan zgodności jest raportowany jako **niezarejestrowany**, sprawdź, czy dostawca zasobów **Microsoft. PolicyInsights** jest zarejestrowany i czy użytkownik ma odpowiednie uprawnienia kontroli dostępu opartej na rolach (RBAC), zgodnie z opisem w temacie [RBAC w Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Wyzwalacze oceny

Wyniki cyklu ukończone oceny są dostępne w `Microsoft.PolicyInsights` dostawcy zasobów za pomocą `PolicyStates` i `PolicyEvents` operacji. Aby uzyskać więcej informacji na temat operacji interfejsu API REST usługi Azure Policy Insights, zobacz [Azure Policy Insights](/rest/api/policy-insights/).

Wersje ewaluacyjne przypisane zasady oraz inicjatyw się zdarzyć w wyniku różnych zdarzeń:

- Zasad lub inicjatywy zostało przydzielone do zakresu. Zajmuje około 30 minut do przypisania, które mają być stosowane do zdefiniowanego zakresu. Po zastosowaniu, cykl oceny rozpoczyna się dla zasobów w ramach tego zakresu, w przypadku nowo przypisanych zasad lub inicjatywy, a w zależności od wpływu używany przez zasady lub inicjatywy, zasoby zostaną oznaczone jako zgodne lub niezgodne. Duże zasad lub inicjatywy, oceniane pod kątem dużych zakres zasobów może potrwać. W efekcie jest nie predefiniowanych oczekiwania na gdy zakończy cykl oceny. Po zakończeniu wyniki sprawdzania zgodności zaktualizowane są dostępne w portalu i zestawów SDK.

- Aktualizacji zasad lub inicjatywy już przypisane do zakresu. Cykl oceny i czas, w tym scenariuszu jest taka sama, jak w przypadku nowe przypisanie do zakresu.

- Zasób jest wdrażany w zakresie z przydziałem przy użyciu usługi Resource Manager, REST, wiersza polecenia platformy Azure lub programu Azure PowerShell. W tym scenariuszu zdarzeń efekt (Dołącz, inspekcji, Odmów, wdrażania) i informacje o stanie zgodności dla poszczególnych zasobów stają się dostępne w portalu i zestawy SDK około 15 minut. To zdarzenie nie powoduje oceny innych zasobów.

- Cykl oceny zgodności standardowych. Co 24 godziny, przydziały są automatycznie ponownie oceniane. Duże zasad lub inicjatywy wiele zasobów może potrwać, więc ma nie wstępnie zdefiniowanych oczekiwania podczas cyklu oceny zakończy. Po zakończeniu wyniki sprawdzania zgodności zaktualizowane są dostępne w portalu i zestawów SDK.

- Dostawca zasobów [konfiguracji gościa](../concepts/guest-configuration.md) został zaktualizowany o szczegóły zgodności przez zasób zarządzany.

- Skanowanie na żądanie

### <a name="on-demand-evaluation-scan"></a>Skanowanie na żądanie

Skanowanie oceny dla subskrypcji lub grupy zasobów może być uruchamiany przy użyciu wywołania interfejsu API REST. To skanowanie jest proces asynchroniczny. W efekcie punktu końcowego REST, aby uruchomić skanowanie nie poczekaj, aż skanowanie zostanie zakończone na odpowiedź. Zamiast tego zapewnia identyfikatora URI, aby wykonać zapytanie o stan żądanej wersji ewaluacyjnej.

Każdy identyfikator URI interfejsu API REST zawiera używane zmienne, które musisz zastąpić własnymi wartościami:

- `{YourRG}` -Zamień na nazwę grupy zasobów
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji

Skanowanie obsługuje ocena zasobów w ramach subskrypcji lub w grupie zasobów. Uruchom skanowanie w zakresie za pomocą interfejsu API REST **WPIS** polecenie, używając następujących struktur identyfikatora URI:

- Subskrypcja

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Grupa zasobów

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

To wywołanie zwraca **202 zaakceptowano** stanu. Uwzględnione w odpowiedzi nagłówek jest **lokalizacji** właściwość o następującym formacie:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` statycznie jest generowany dla zakres żądany. Jeśli zakres jest już uruchomiony skanowania na żądanie, nowe skanowanie nie jest uruchomiona. Zamiast tego nowe żądanie znajduje się taka sama `{ResourceContainerGUID}` **lokalizacji** identyfikator URI dla stanu. Interfejs API REST **UZYSKAĆ** polecenie **lokalizacji** zwraca identyfikator URI **202 zaakceptowano** podczas, gdy trwa obliczanie. Po zakończeniu skanowania oceny zwraca **200 OK** stanu. Treść skanowanie ukończone jest odpowiedź w formacie JSON ze stanem:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Jak działa zgodności

W przypisaniu zasobu jest **niezgodne** Jeśli on nie być zgodny z reguły zasad lub inicjatywy.
W poniższej tabeli przedstawiono, jak inne zasady, efekty pracy z oceny warunku na potrzeby wynikowego stanu zgodności:

| Stan zasobu | Efekt | Ocena zasad | Stan zgodności |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Prawda | Niezgodne |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Zgodne |
| Nowa | Audit, AuditIfNotExist\* | Prawda | Niezgodne |
| Nowa | Audit, AuditIfNotExist\* | False | Zgodne |

\* Efekty Append, DeployIfNotExist i AuditIfNotExist wymagają instrukcji IF z wartością TRUE.
Ponadto efekty wymagają, aby warunek istnienia miał wartość FALSE, aby być niezgodnymi. W przypadku wartości TRUE warunek IF wyzwala ocenę warunku istnienia dla powiązanych zasobów.

Załóżmy na przykład, istnieje grupa zasobów — ContsoRG, z niektórych kont magazynu (wyróżniony na czerwono), które są dostępne do sieci publicznych.

![Konta magazynu uwidaczniany w sieciach publicznych](../media/getting-compliance-data/resource-group01.png)

W tym przykładzie należy zachować ostrożność przy zagrożenia dla bezpieczeństwa. Teraz, po utworzeniu przypisania zasad, sprawdzana jest zgodność to dla wszystkich kont magazynu w grupie zasobów ContosoRG. Inspekcje on trzy konta magazynu nie jest zgodne, w związku z tym zmianę ich stanów do **niezgodne.**

![Inspekcję kont magazynu braku zgodności](../media/getting-compliance-data/resource-group03.png)

Oprócz **zgodne** i **niezgodne**, zasady i zasobów mają trzy inne stany:

- **Konflikt**: istnieje co najmniej dwie zasady, za pomocą reguł powodujących konflikt. Na przykład dwie zasady dołączania tego samego tagu z różnymi wartościami.
- **Nierozpoczęte**: cykl oceny nie zostało rozpoczęte zasad lub zasobu.
- **Niezarejestrowany**: dostawcy zasobów zasad platformy Azure nie został zarejestrowany lub konto rejestrowane w nie ma uprawnienia do odczytu danych zgodności.

Azure Policy używa pól **Typ** i **Nazwa** w definicji, aby określić, czy zasób jest zgodny. Jeśli zasób jest zgodny, jest traktowana jako odpowiednie i ma stan **zgodne** lub **niezgodne**. Jeśli **typu** lub **nazwa** jest właściwością tylko do definicji, a wszystkie zasoby są traktowane jako stosowane są oceniane.

Wartość procentowa zgodności jest określana przez podzielenie **zgodne** zasobów przez _łączna liczba zasobów_.
_Łączna liczba zasobów_ jest zdefiniowany jako suma **zgodne**, **niezgodne**, i **powodujące konflikt** zasobów. Ogólnej zgodności numery stanowią sumę odrębne zasoby, które są **zgodne** podzielona przez sumę wszystkich odrębne zasoby. Na poniższej ilustracji są 20 odrębne zasoby, które mają zastosowanie i jest tylko jeden **niezgodne**. Ogólna zgodność zasobów jest 95% (19 z 20).

![Przykład zgodności z zasadami ze strony zgodności](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portal

Azure portal prezentuje graficznego środowiska wizualizacji i zrozumienie stanu zgodności w danym środowisku. Na **zasad** stronie **Przegląd** opcja zawiera szczegółowe informacje o dostępnych zakresów na zgodność inicjatywy i zasady. Oprócz stanu zgodności i liczba na przypisanie zawiera wykres przedstawiający zgodność z ostatnich siedmiu dni. **Zgodności** strona zawiera wiele tych samych informacji (z wyjątkiem wykres), ale zapewnia dodatkowe opcji filtrowania i sortowania.

![Przykład strony zgodność Azure Policy](../media/getting-compliance-data/compliance-page.png)

Ponieważ do różnych zakresów można przypisywać zasad lub inicjatywy, uwzględniono w zakresie dla każdego przypisania i typu definicji, który został przypisany. Liczba niezgodnych zasobów i dostęp niezgodnych zasad dla każdego przypisania również są dostarczone. Kliknięcie zasad lub inicjatywy w tabeli zawiera lepiej poznać zgodności dla tego przypisania.

![Przykład strony szczegółów zgodności Azure Policy](../media/getting-compliance-data/compliance-details.png)

Lista zasobów na **zgodność zasobów** karta przedstawia stan oceny istniejących zasobów dla bieżącego przypisania. Wartością domyślną jest karcie **niezgodne**, ale można je filtrować.
Zdarzenia (Dołącz, inspekcji, Odmów, Wdrażaj) wyzwalane przez żądanie do utworzenia zasobu zostaną wyświetlone w obszarze **zdarzenia** kartę.

> [!NOTE]
> W przypadku zasad aparatu AKS wyświetlony zasób jest grupą zasobów.

![Przykład Azure Policy zdarzeń zgodności](../media/getting-compliance-data/compliance-events.png)

W przypadku zasobów [trybu dostawcy zasobów](../concepts/definition-structure.md#resource-provider-modes) na karcie **zgodność zasobów** wybierz zasób lub kliknij prawym przyciskiem myszy wiersz i wybierz polecenie **Wyświetl szczegóły zgodności** , aby otworzyć Szczegóły zgodności składnika. Ta strona zawiera również karty umożliwiające wyświetlanie zasad przypisanych do tego zasobu, zdarzeń, zdarzeń składników i historii zmian.

![Przykład szczegółów zgodności składników Azure Policy](../media/getting-compliance-data/compliance-components.png)

Wróć do strony zgodność zasobów, kliknij prawym przyciskiem myszy wiersz zdarzenia, dla którego chcesz zebrać więcej szczegółów, a następnie wybierz pozycję **Pokaż dzienniki aktywności**. Na stronie dziennik aktywności otwiera się i jest wstępnie filtrowane do wyszukiwanie przedstawiający szczegółowe informacje dotyczące przypisania i zdarzenia. Dziennik aktywności zawiera dodatkowy kontekst oraz informacje o tych zdarzeń.

![Przykład dziennika aktywności zgodności Azure Policy](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Zrozumienie braku zgodności

<a name="change-history-preview"></a>

Gdy zasoby są określone jako **niezgodne**, istnieje wiele możliwych przyczyn. Aby określić przyczynę braku zgodności zasobu lub aby znaleźć zmianę odpowiedzialną, zobacz [Określanie, które nie](./determine-non-compliance.md)są **zgodne** .

## <a name="command-line"></a>Wiersz polecenia

Te same informacje dostępne w portalu można pobrać przy użyciu interfejsu API REST (w tym z [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell i interfejsu wiersza polecenia platformy Azure (wersja zapoznawcza).
Aby uzyskać szczegółowe informacje na temat interfejsu API REST, zobacz informacje dotyczące [Azure Policy Insights](/rest/api/policy-insights/) . Strony podręcznika interfejsu API REST ma zielony "Try It" przycisku na każdej operacji, która pozwala wypróbować go bezpośrednio w przeglądarce.

Użyj ARMClient lub podobnego narzędzia do obsługi uwierzytelniania na platformie Azure na potrzeby przykładów interfejsu API REST.

### <a name="summarize-results"></a>Podsumowanie wyników

Za pomocą interfejsu API REST podsumowania jest możliwe w kontenerze, definicji lub przypisania. Oto przykład podsumowania na poziomie subskrypcji przy użyciu [podsumowania Azure Policy wgląd w subskrypcję](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

Dane wyjściowe zawiera podsumowanie subskrypcji. W przykładzie danych wyjściowych poniżej podsumowania zgodności podlegają **value.results.nonCompliantResources** i **value.results.nonCompliantPolicies**. To żądanie zawiera dalsze szczegóły, w tym każdego przypisania, które składają się braku zgodności numery i informacje definicji dla każdego przypisania. Każdy obiekt zasad w hierarchii zawiera **queryResultsUri** można uzyskać dodatkowe szczegóły na tym samym poziomie.

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

W powyższym przykładzie **value.policyAssignments.policyDefinitions.results.queryResultsUri** zawiera przykładowy identyfikator Uri dla wszystkich niezgodnych zasobów w definicji określonych zasad. Patrząc **$filter** wartość IsCompliant jest równe (eq) na wartość false, PolicyAssignmentId jest określona w definicji zasad, a następnie PolicyDefinitionId, sam. Przyczyna, w tym PolicyAssignmentId w filtrze jest, ponieważ PolicyDefinitionId może znajdować się w kilku zasad lub przypisania inicjatywy z różnymi zakresami. Określając PolicyAssignmentId i PolicyDefinitionId, firma Microsoft może być jawne w wynikach, który szukamy. Wcześniej dla PolicyStates użyliśmy **najnowsze**, który automatycznie ustawia **z** i **do** przedziału czasu w ciągu ostatnich 24 godzin.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Przykładową odpowiedź poniżej została wycofana na jednym niezgodnym zasobem w celu skrócenia programu. Szczegółowa odpowiedź ma kilka rodzajów danych dotyczących zasobu, zasad lub inicjatywy i przypisania. Należy zauważyć, że można również wyświetlić parametry przypisania zostały przekazane do definicji zasad.

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

Wynik oceny zasad jest generowany, gdy zasób jest tworzony lub aktualizowany. Wyniki są nazywane _zdarzenia dotyczące zasad_. Użyj następujący identyfikator Uri, aby wyświetlić ostatnie zdarzenia zasady skojarzone z tą subskrypcją.

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

Aby uzyskać więcej informacji na temat wykonywania zapytań dotyczących zdarzeń zasad, zobacz artykuł dotyczący [zdarzeń Azure Policy](/rest/api/policy-insights/policyevents) .

### <a name="azure-powershell"></a>Azure PowerShell

Moduł Azure PowerShell dla Azure Policy jest dostępny w Galeria programu PowerShell jako [AZ. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights).
Korzystanie z modułu PowerShellGet, można zainstalować przy użyciu modułu `Install-Module -Name Az.PolicyInsights` (Upewnij się, że zainstalowano najnowszy [programu Azure PowerShell](/powershell/azure/install-az-ps) zainstalowane):

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

Przykład: Aby uzyskać stan podsumowania najwyższego poziomu przypisanych zasad o najwyższym numerze niezgodnych zasobów.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Przykład: Pobieranie rekordów stanu dla najbardziej niedawno ocenę zasobów (wartość domyślna to według sygnatur czasowych w kolejności malejącej).

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

Przykład: Trwa pobieranie szczegółów dla wszystkich zasobów niezgodnych sieci wirtualnej.

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

Przykład: Pobieranie zdarzenia związane z zasobami niezgodnych sieci wirtualnej, które wystąpiły po określonej dacie.

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

**PrincipalOid** pola można pobrać określonego użytkownika za pomocą polecenia cmdlet programu Azure PowerShell `Get-AzADUser`. Zastąp **{principalOid}** przy użyciu odpowiedzi, Pobierz z poprzedniego przykładu.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

Jeśli masz [obszar roboczy log Analytics](../../../log-analytics/log-analytics-overview.md) z `AzureActivity` z [rozwiązania Activity Log Analytics](../../../azure-monitor/platform/activity-log-collect.md) powiązanego z subskrypcją, możesz również wyświetlić wyniki niezgodności z cyklu oceny przy użyciu prostych zapytań Kusto i tabeli `AzureActivity`. Dzięki szczegółowym dziennikom Azure Monitor alerty można skonfigurować tak, aby oglądać niezgodność.


![Azure Policy zgodności przy użyciu dzienników Azure Monitor](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](remediate-resources.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).