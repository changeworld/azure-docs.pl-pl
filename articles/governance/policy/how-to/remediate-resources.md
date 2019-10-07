---
title: Koryguj niezgodne zasoby
description: Ten przewodnik przeprowadzi Cię przez korygowanie zasobów, które są niezgodne z zasadami w Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 219a3c56f9e4e4c9e132fa759b017fac63ade766
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71977992"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Koryguj niezgodne zasoby za pomocą Azure Policy

Zasoby, które są niezgodne z zasadami **deployIfNotExistsymi** lub **modyfikacjami** , mogą zostać umieszczone w stanie zgodnym przez **skorygowanie**. Korygowanie jest realizowane przez poinstruuj Azure Policy, aby uruchomić efekt **deployIfNotExists** lub Tagi **operacji** przypisanych zasad w istniejących zasobach. W tym artykule przedstawiono kroki niezbędne do zrozumienia i wykonania korygowania Azure Policy.

## <a name="how-remediation-security-works"></a>Jak działa zabezpieczenia korygujące

Gdy Azure Policy uruchamia szablon w definicji zasad **deployIfNotExists** , robi to przy użyciu [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md).
Azure Policy tworzy tożsamość zarządzaną dla każdego przydziału, ale musi mieć szczegółowe informacje o rolach, które mają udzielić zarządzanej tożsamości. Jeśli zarządzana tożsamość nie zawiera ról, ten błąd jest wyświetlany podczas przypisywania zasad lub inicjatywy. W przypadku korzystania z portalu Azure Policy automatycznie udzielą zarządzanej tożsamości po rozpoczęciu przypisywania.

![Tożsamość zarządzana — brak roli](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Jeśli zasób zmodyfikowany przez **deployIfNotExists** lub **Modyfikuj** znajduje się poza zakresem przypisania zasad lub szablon uzyskuje dostęp do właściwości zasobów poza zakresem przypisania zasad, tożsamość zarządzana przypisania musi być [ ręcznie udzielenie dostępu](#manually-configure-the-managed-identity) lub wdrożenie korygowania zakończy się niepowodzeniem.

## <a name="configure-policy-definition"></a>Konfigurowanie definicji zasad

Pierwszym krokiem jest zdefiniowanie ról, które **deployIfNotExists** i **modyfikują** potrzeby w definicji zasad, aby pomyślnie wdrożyć zawartość dołączonego szablonu. W obszarze właściwości **szczegóły** Dodaj właściwość **roleDefinitionIds** . Ta właściwość jest tablicą ciągów, które pasują do ról w danym środowisku. Aby zapoznać się z pełnymi przykładami, zobacz [przykład deployIfNotExists](../concepts/effects.md#deployifnotexists-example) lub [Modify przykłady](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Właściwość **roleDefinitionIds** używa pełnego identyfikatora zasobu i nie przyjmuje krótkie **rolename** roli. Aby uzyskać identyfikator roli "Współautor" w Twoim środowisku, użyj następującego kodu:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Ręcznie skonfiguruj zarządzaną tożsamość

Podczas tworzenia przypisania przy użyciu portalu, Azure Policy zarówno generuje tożsamość zarządzaną, jak i przyznaje jej role zdefiniowane w **roleDefinitionIds**. W poniższych warunkach należy ręcznie wykonać kroki umożliwiające utworzenie tożsamości zarządzanej i przypisanie jej uprawnień:

- Podczas korzystania z zestawu SDK (takiego jak Azure PowerShell)
- Gdy zasób poza zakresem przypisania jest modyfikowany przez szablon
- Gdy zasób poza zakresem przypisania jest odczytywany przez szablon

> [!NOTE]
> Azure PowerShell i .NET są jedynymi zestawami SDK, które obecnie obsługują tę funkcję.

### <a name="create-managed-identity-with-powershell"></a>Tworzenie tożsamości zarządzanej przy użyciu programu PowerShell

Aby utworzyć tożsamość zarządzaną podczas przypisywania zasad, należy zdefiniować **lokalizację** i **AssignIdentity** . Poniższy przykład pobiera definicję wbudowanego zasad **Wdróż nieprzezroczyste szyfrowanie danych SQL DB**, ustawia docelową grupę zasobów, a następnie tworzy przypisanie.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

Zmienna `$assignment` zawiera teraz Identyfikator podmiotu zabezpieczeń zarządzanej oraz standardowe wartości zwracane podczas tworzenia przypisania zasad. Dostęp do niego można uzyskać za poorednictwem `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Przyznawanie zdefiniowanych ról przy użyciu programu PowerShell

Nowa tożsamość zarządzana musi ukończyć replikację za pomocą Azure Active Directory, zanim będzie można przyznać wymagane role. Po zakończeniu replikacji Poniższy przykład iteruje definicję zasad w `$policyDef` dla **roleDefinitionIds** i używa funkcji [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) , aby przyznać nowej tożsamości zarządzanej role.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Przyznawanie zdefiniowanych ról za poorednictwem portalu

Istnieją dwa sposoby udzielenia tożsamości zarządzanej przez przypisanie zdefiniowane role przy użyciu portalu przy użyciu **kontroli dostępu (IAM)** lub przez edytowanie przypisania zasad lub inicjatywy i klikanie przycisku **Zapisz**.

Aby dodać rolę do zarządzanej tożsamości przypisania, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w Azure Portal, klikając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając pozycję **zasady**.

1. Wybierz pozycję **przypisania** w lewej części strony Azure Policy.

1. Znajdź przypisanie, które ma zarządzaną tożsamość i kliknij nazwę.

1. Znajdź właściwość **ID przypisania** na stronie Edycja. Identyfikator przypisania będzie wyglądać następująco:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Nazwa tożsamości zarządzanej jest ostatnią częścią identyfikatora zasobu przypisania, która jest `2802056bfc094dfb95d4d7a5` w tym przykładzie. Skopiuj tę część identyfikatora zasobu przypisania.

1. Przejdź do zasobu lub kontenera nadrzędnego zasobów (grupy zasobów, subskrypcji, grupy zarządzania), które wymagają ręcznego dodania definicji roli.

1. Kliknij link **Kontrola dostępu (IAM)** na stronie zasoby, a następnie kliknij pozycję **+ Dodaj przypisanie roli** w górnej części strony kontroli dostępu.

1. Wybierz odpowiednią rolę zgodną z **roleDefinitionIdsą** z definicji zasad.
   Pozostaw pozycję **Przypisz dostęp, aby** ustawić domyślną wartość "użytkownik, Grupa lub aplikacja usługi Azure AD". W polu **wyboru** Wklej lub wpisz część identyfikatora zasobu przypisania zlokalizowanego wcześniej. Po zakończeniu wyszukiwania kliknij obiekt o tej samej nazwie, aby wybrać identyfikator, a następnie kliknij przycisk **Zapisz**.

## <a name="create-a-remediation-task"></a>Utwórz zadanie korygowania

### <a name="create-a-remediation-task-through-portal"></a>Tworzenie zadania korygującego za pomocą portalu

Podczas obliczania przypisanie zasad z **deployIfNotExists** lub **modyfikowaniem** efektów określa, czy istnieją niezgodne zasoby. Po znalezieniu niezgodnych zasobów szczegóły są dostępne na stronie **korygowanie** . Wraz z listą zasad, które mają niezgodne zasoby, jest opcja wyzwalająca **zadanie korygowania**. Ta opcja polega na utworzeniu wdrożenia z szablonu **deployIfNotExists** lub operacji **modyfikowania** .

Aby utworzyć **zadanie korygowania**, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w Azure Portal, klikając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając pozycję **zasady**.

   ![Wyszukaj zasady w obszarze wszystkie usługi](../media/remediate-resources/search-policy.png)

1. Wybierz pozycję **korygowanie** po lewej stronie Azure Policy.

   ![Wybieranie opcji korygowania na stronie zasad](../media/remediate-resources/select-remediation.png)

1. Wszystkie przypisania zasad **deployIfNotExists** i **Modify** z niezgodnymi zasobami są zawarte w **zasadach do korygowania** kart i tabeli danych. Kliknij zasadę z zasobami, które nie są zgodne. Zostanie otwarta strona **nowe zadanie korygowania** .

   > [!NOTE]
   > Alternatywnym sposobem otwarcia strony **zadania korygowania** jest znalezienie i kliknięcie zasad ze strony **zgodność** , a następnie kliknięcie przycisku **Utwórz zadanie korygujące** .

1. Na stronie **nowe zadanie korygowania** odfiltruj zasoby do skorygowania, używając wielokropka **zakresu** , aby wybrać zasoby podrzędne, z których są przypisane zasady (w tym w dół do poszczególnych obiektów zasobów). Ponadto należy użyć listy rozwijanej **lokalizacje** do dalszej odfiltrowania zasobów. Korygowane są tylko zasoby wymienione w tabeli.

   ![Koryguj — Wybierz zasoby do skorygowania](../media/remediate-resources/select-resources.png)

1. Rozpocznij zadanie korygowania po odfiltrowaniu zasobów przez kliknięcie przycisku **Koryguj**. Strona zgodność zasad zostanie otwarta na karcie **zadania korygowania** , aby wyświetlić stan zadań postępu.

   ![Korygowanie postępu zadań korygowania](../media/remediate-resources/task-progress.png)

1. Kliknij **zadanie korygowania** na stronie zgodność zasad, aby uzyskać szczegółowe informacje o postępie. Filtrowanie używane na potrzeby zadania jest wyświetlane wraz z listą zasobów, które są korygowane.

1. Na stronie **zadanie korygowania** kliknij prawym przyciskiem myszy zasób, aby wyświetlić jego wdrożenie lub zasób. Na końcu wiersza kliknij pozycję **powiązane zdarzenia** , aby wyświetlić szczegóły, takie jak komunikat o błędzie.

   ![Menu kontekstowe zadań korygowania zasobów](../media/remediate-resources/resource-task-context-menu.png)

Zasoby wdrożone za pośrednictwem **zadania korygowania** są dodawane do karty **wdrożone zasoby** na stronie Zgodność z zasadami.

### <a name="create-a-remediation-task-through-azure-cli"></a>Tworzenie zadania korygującego za pomocą interfejsu wiersza polecenia platformy Azure

Aby utworzyć **zadanie korygowania** przy użyciu interfejsu wiersza polecenia platformy Azure, użyj poleceń `az policy remediation`. Zastąp wartość `{subscriptionId}` IDENTYFIKATORem subskrypcji i `{myAssignmentId}` identyfikatorem przydziału zasad **deployIfNotExists** lub **Modify** .

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Aby poznać inne polecenia i przykłady korygowania, zobacz [AZ Policy korygowanie](/cli/azure/policy/remediation) Commands.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Utwórz zadanie korygujące za pośrednictwem Azure PowerShell

Aby utworzyć **zadanie korygowania** przy użyciu Azure PowerShell, użyj poleceń `Start-AzPolicyRemediation`. Zastąp wartość `{subscriptionId}` IDENTYFIKATORem subskrypcji i `{myAssignmentId}` identyfikatorem przydziału zasad **deployIfNotExists** lub **Modify** .

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Inne polecenia cmdlet służące do korygowania i przykłady można znaleźć w module [AZ. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) .

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Zapoznaj się ze [strukturą definicji Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [Informacje o skutkach zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](getting-compliance-data.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).