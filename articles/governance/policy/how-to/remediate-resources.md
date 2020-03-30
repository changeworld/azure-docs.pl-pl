---
title: Korygowanie niezgodnych zasobów
description: W tym przewodniku znajdziesz informacje o korygowanie zasobów niezgodnych z zasadami w usłudze Azure Policy.
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 71af5c81e0dce4d5c0a0461534f634db36bd66a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471391"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Korygowanie niezgodnych zasobów za pomocą zasad platformy Azure

Zasoby, które są niezgodne z **deployIfNotExists** lub **zmodyfikować** zasady mogą być umieszczone w stanie zgodnym za pomocą **korygowania**. Korygowanie odbywa się przez poinstruowanie usługi Azure Policy, aby uruchomić efekt **deployIfNotExists** lub **operacje** tag przypisanych zasad dla istniejących zasobów, niezależnie od tego, czy to przypisanie jest do grupy zarządzania, subskrypcji, grupy zasobów lub pojedynczego zasobu. W tym artykule przedstawiono kroki potrzebne do zrozumienia i wykonania korygowania za pomocą usługi Azure Policy.

## <a name="how-remediation-security-works"></a>Jak działają zabezpieczenia naprawcze

Gdy usługa Azure Policy uruchamia szablon w definicji zasad **deployIfNotExists,** robi to przy użyciu [tożsamości zarządzanej.](../../../active-directory/managed-identities-azure-resources/overview.md)
Usługa Azure Policy tworzy tożsamość zarządzaną dla każdego przypisania, ale musi zawierać szczegółowe informacje o rolach, które mają udzielić tożsamości zarządzanej. Jeśli w tożsamości zarządzanej brakuje ról, ten błąd jest wyświetlany podczas przypisywania zasad lub inicjatywy. Podczas korzystania z portalu, usługa Azure Policy automatycznie przyznaje tożsamości zarządzanej wymienione role po uruchomieniu przypisania. _Lokalizacja_ tożsamości zarządzanej nie ma wpływu na jej działanie za pomocą usługi Azure Policy.

![Tożsamość zarządzana — brak roli](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Jeśli zasób zmodyfikowany przez **deployIfNotExists** lub **modyfikacji** jest poza zakresem przypisania zasad lub szablon uzyskuje dostęp do właściwości zasobów poza zakresem przypisania zasad, przypisania tożsamości zarządzanej musi być [ręcznie udzielone dostępu](#manually-configure-the-managed-identity) lub wdrożenia korygowania zakończy się niepowodzeniem.

## <a name="configure-policy-definition"></a>Konfigurowanie definicji zasad

Pierwszym krokiem jest zdefiniowanie ról, które **deployIfNotExists** i **modyfikowanie** potrzeb w definicji zasad, aby pomyślnie wdrożyć zawartość dołączonego szablonu. Pod **właściwością details** dodaj właściwość **roleDefinitionIds.** Ta właściwość jest tablicą ciągów, które pasują do ról w twoim środowisku. Na przykład w pełni zobacz [deployIfNotExists przykład](../concepts/effects.md#deployifnotexists-example) lub [przykłady modyfikowania](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**Właściwość roleDefinitionIds** używa pełnego identyfikatora zasobu i nie przyjmuje krótkiego **nazwy roli.** Aby uzyskać identyfikator roli "Współautor" w twoim środowisku, użyj następującego kodu:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Ręczne konfigurowanie tożsamości zarządzanej

Podczas tworzenia przydziału przy użyciu portalu usługa Azure Policy generuje tożsamość zarządzaną i przyznaje jej role zdefiniowane w **funkcjiDefinitionIds**. W następujących warunkach kroki tworzenia tożsamości zarządzanej i przypisywania jej uprawnień muszą być wykonywane ręcznie:

- Podczas korzystania z SDK (na przykład Azure PowerShell)
- Gdy zasób poza zakresem przydziału jest modyfikowany przez szablon
- Gdy zasób poza zakresem przydziału jest odczytywany przez szablon

> [!NOTE]
> Platformy Azure PowerShell i .NET są tylko zestawów SDK, które obecnie obsługują tę funkcję.

### <a name="create-managed-identity-with-powershell"></a>Tworzenie tożsamości zarządzanej za pomocą programu PowerShell

Aby utworzyć tożsamość zarządzaną podczas przypisywania zasad, należy zdefiniować **lokalizację** i **użyć identyfikatora AssignIdentity.** W poniższym przykładzie pobiera definicję wbudowanej zasady **Wdrażanie szyfrowania danych sql db przezroczyste,** ustawia docelową grupę zasobów, a następnie tworzy przydział.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

Zmienna `$assignment` zawiera teraz główny identyfikator tożsamości zarządzanej wraz z wartościami standardowymi zwracanych podczas tworzenia przypisania zasad. Można uzyskać dostęp `$assignment.Identity.PrincipalId`za pośrednictwem .

### <a name="grant-defined-roles-with-powershell"></a>Przyznawanie zdefiniowanych ról za pomocą programu PowerShell

Nowa tożsamość zarządzana musi zakończyć replikację za pośrednictwem usługi Azure Active Directory, zanim będzie można przyznać potrzebne role. Po zakończeniu replikacji poniższy przykład iteruje `$policyDef` definicję zasad dla **roliDefinitionIds** i używa [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) do przyznania nowej tożsamości zarządzanej ról.

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

### <a name="grant-defined-roles-through-portal"></a>Przyznawanie zdefiniowanych ról za pośrednictwem portalu

Istnieją dwa sposoby przyznania tożsamości zarządzanej przypisania zdefiniowanych ról za pomocą portalu, za pomocą **kontroli dostępu (IAM)** lub edytując przypisanie zasad lub inicjatywy i klikając **przycisk Zapisz**.

Aby dodać rolę do zarządzanej tożsamości przypisania, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając opcję **Wszystkie usługi** i następnie wyszukując i wybierając opcję **Zasada**.

1. Wybierz pozycję **Przypisania** w lewej części strony usługi Azure Policy.

1. Znajdź przypisanie, które ma zarządzaną tożsamość i kliknij nazwę.

1. Znajdź właściwość **Identyfikator przypisania** na stronie edycji. Identyfikator przydziału będzie wyglądać następująco:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Nazwa tożsamości zarządzanej jest ostatnią częścią identyfikatora zasobu `2802056bfc094dfb95d4d7a5` przydziału, który znajduje się w tym przykładzie. Skopiuj tę część identyfikatora zasobu przydziału.

1. Przejdź do kontenera nadrzędnego zasobu lub zasobów (grupa zasobów, subskrypcja, grupa zarządzania), który wymaga ręcznego dodania definicji roli.

1. Kliknij łącze **Kontrola dostępu (IAM)** na stronie zasobów i kliknij **+ Dodaj przypisanie roli** u góry strony kontroli dostępu.

1. Wybierz odpowiednią rolę, która pasuje do **roliDefinitionIds** z definicji zasad.
   Pozostaw **dostęp przypisz, aby** ustawić domyślnie "Użytkownik, grupa lub aplikacja usługi Azure AD". W polu **Zaznacz** wklej lub wpisz wcześniej część identyfikatora zasobu przydziału. Po zakończeniu wyszukiwania kliknij obiekt o tej samej nazwie, aby wybrać identyfikator, a następnie kliknij przycisk **Zapisz**.

## <a name="create-a-remediation-task"></a>Tworzenie zadania korygowania

### <a name="create-a-remediation-task-through-portal"></a>Tworzenie zadania korygowania za pośrednictwem portalu

Podczas oceny przypisanie zasad z **deployIfNotExists** lub **zmodyfikować** efekty określa, czy istnieją niezgodne zasoby. Po znalezieniu niezgodnych zasobów szczegóły znajdują się na stronie **Korygowanie.** Wraz z listą zasad, które mają niezgodne zasoby jest opcja wyzwalania **zadania korygowania**. Ta opcja jest, co tworzy wdrożenie z **deployIfNotExists** szablonu lub **operacji modyfikowania.**

Aby utworzyć **zadanie korygowania,** wykonaj następujące czynności:

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając opcję **Wszystkie usługi** i następnie wyszukując i wybierając opcję **Zasada**.

   ![Wyszukiwanie zasad we wszystkich usługach](../media/remediate-resources/search-policy.png)

1. Wybierz **opcję Korygowanie** po lewej stronie strony Zasady platformy Azure.

   ![Wybieranie opcji Korygowanie na stronie Zasady](../media/remediate-resources/select-remediation.png)

1. Wszystkie **deployIfNotExists** i **zmodyfikować przypisania** zasad z niezgodnych zasobów są zawarte w **zasady do korygowania** karty i tabeli danych. Kliknij zasady z zasobami, które są niezgodne. Zostanie otwarta strona **Nowe zadanie korygowania.**

   > [!NOTE]
   > Alternatywnym sposobem otwarcia strony **zadania korygowania** jest znalezienie i kliknięcie zasad na stronie **Zgodność,** a następnie kliknięcie przycisku **Utwórz zadanie korygowania.**

1. Na stronie **Nowe zadanie korygowania** filtruj zasoby do korygowania za pomocą wielokropek **zakresu,** aby wybrać zasoby podrzędne, z których zasada jest przypisana (w tym w dół do poszczególnych obiektów zasobów). Ponadto użyj **lokalizacji** listy rozwijanej do dalszego filtrowania zasobów. Tylko zasoby wymienione w tabeli zostaną skorygowane.

   ![Korygowanie — wybierz zasoby, które mają być korygować](../media/remediate-resources/select-resources.png)

1. Rozpocznij zadanie korygowania po przefiltrowaniu zasobów, klikając pozycję **Koryguj**. Strona zgodności zasad zostanie otwarta na karcie **Zadania korygowania,** aby pokazać stan postępu zadań. Wdrożenia utworzone przez zadanie korygowania rozpoczynają się od razu.

   ![Korygowanie — postęp zadań naprawczych](../media/remediate-resources/task-progress.png)

1. Kliknij **zadanie korygowania** na stronie zgodności zasad, aby uzyskać szczegółowe informacje o postępie. Filtrowanie używane dla zadania jest wyświetlane wraz z listą zasobów korygowanych.

1. Na stronie **zadania korygowania** kliknij prawym przyciskiem myszy zasób, aby wyświetlić wdrożenie zadania korygowania lub zasób. Na końcu wiersza kliknij zdarzenia **pokrewne,** aby wyświetlić szczegóły, takie jak komunikat o błędzie.

   ![Korygowanie — menu kontekstowe zadania zasobu](../media/remediate-resources/resource-task-context-menu.png)

Zasoby wdrożone za pomocą **zadania korygowania** są dodawane do karty **Wdrożone zasoby** na stronie zgodność zasad.

### <a name="create-a-remediation-task-through-azure-cli"></a>Tworzenie zadania korygowania za pomocą interfejsu wiersza polecenia platformy Azure

Aby utworzyć **zadanie korygowania** za pomocą `az policy remediation` interfejsu wiersza polecenia platformy Azure, należy użyć poleceń. Zamień `{subscriptionId}` identyfikator subskrypcji `{myAssignmentId}` i **wdrożyćIfNotExists** lub **zmodyfikować** identyfikator przypisania zasad.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Aby uzyskać inne polecenia korygowania i przykłady, zobacz polecenia [korygowania zasad az.](/cli/azure/policy/remediation)

### <a name="create-a-remediation-task-through-azure-powershell"></a>Tworzenie zadania korygowania za pomocą programu Azure PowerShell

Aby utworzyć **zadanie korygowania** za pomocą programu `Start-AzPolicyRemediation` Azure PowerShell, należy użyć poleceń. Zamień `{subscriptionId}` identyfikator subskrypcji `{myAssignmentId}` i **wdrożyćIfNotExists** lub **zmodyfikować** identyfikator przypisania zasad.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Aby uzyskać inne polecenia cmdlet i przykłady korygowania, zobacz [moduł Az.PolicyInsights.](/powershell/module/az.policyinsights/#policy_insights)

## <a name="next-steps"></a>Następne kroki

- Przejrzyj przykłady w [przykładach zasad platformy Azure](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo tworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [uzyskać dane dotyczące zgodności](get-compliance-data.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
