---
title: Korygowanie niezgodnych zasobów
description: Ten przewodnik przeprowadzi Cię przez korygowanie zasobów, które są niezgodne z zasadami w Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: d6ca7827200815cf9b9b1c7ac697d06f9c6b306d
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147048"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Korygowanie niezgodnych zasobów przy użyciu usługi Azure Policy

Zasoby, które są niezgodne z zasadami **deployIfNotExistsymi** lub **modyfikacjami** , mogą zostać umieszczone w stanie zgodnym przez **skorygowanie**. Korygowanie jest realizowane przez poinstruuj Azure Policy, aby uruchomić efekt **deployIfNotExists** lub Tagi **operacji** przypisanych zasad w istniejących zasobach. W tym artykule przedstawiono kroki niezbędne do zrozumienia i wykonania korygowania Azure Policy.

## <a name="how-remediation-security-works"></a>Jak działają zabezpieczenia korygowania

Gdy Azure Policy uruchamia szablon w definicji zasad **deployIfNotExists** , robi to przy użyciu [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md).
Azure Policy tworzy tożsamość zarządzaną dla każdego przydziału, ale musi mieć szczegółowe informacje o rolach, które mają udzielić zarządzanej tożsamości. Jeśli tożsamość zarządzana brakuje ról, ten błąd jest wyświetlany podczas przypisywania zasad lub inicjatywy. W przypadku korzystania z portalu Azure Policy automatycznie udzielą zarządzanej tożsamości po rozpoczęciu przypisywania.

![Tożsamość zarządzana — Brak roli](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Jeśli zasób zmodyfikowany przez **deployIfNotExists** lub **Modyfikuj** znajduje się poza zakresem przypisania zasad lub szablon uzyskuje dostęp do właściwości zasobów poza zakresem przypisania zasad, tożsamość zarządzana przypisania musi być [ ręcznie udzielenie dostępu](#manually-configure-the-managed-identity) lub wdrożenie korygowania zakończy się niepowodzeniem.

## <a name="configure-policy-definition"></a>Skonfiguruj definicję zasad

Pierwszym krokiem jest zdefiniowanie ról, które **deployIfNotExists** i **modyfikują** potrzeby w definicji zasad, aby pomyślnie wdrożyć zawartość dołączonego szablonu. W obszarze **szczegóły** właściwości, Dodaj **roleDefinitionIds** właściwości. Ta właściwość jest tablicą ciągów, które odpowiadają ról w danym środowisku. Aby zapoznać się z pełnymi przykładami, zobacz [przykład deployIfNotExists](../concepts/effects.md#deployifnotexists-example) lub [Modify przykłady](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Właściwość **roleDefinitionIds** używa pełnego identyfikatora zasobu i nie przyjmuje krótkie **rolename** roli. Aby uzyskać identyfikator dla roli "Współautor" w danym środowisku, należy użyć następującego kodu:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Ręcznie skonfiguruj tożsamość zarządzaną

Podczas tworzenia przypisania przy użyciu portalu, Azure Policy zarówno generuje tożsamość zarządzaną, jak i przyznaje jej role zdefiniowane w **roleDefinitionIds**. W następujących warunkach kroki, aby utworzyć tożsamość zarządzaną i przypisz mu uprawnienia musi być wykonywane ręcznie:

- Podczas korzystania z zestawu SDK (np. programu Azure PowerShell)
- Po zmodyfikowaniu zasobu poza zakres przypisania przez szablon
- Zasobów spoza zakresu przypisania jest odczytywana przez szablon

> [!NOTE]
> Azure PowerShell i .NET są tylko zestawy SDK, które obecnie obsługuje tę możliwość.

### <a name="create-managed-identity-with-powershell"></a>Utwórz tożsamość zarządzana przy użyciu programu PowerShell

Aby utworzyć tożsamość zarządzaną podczas przypisywania zasad, **lokalizacji** muszą być zdefiniowane i **AssignIdentity** używane. Poniższy przykład pobiera definicji zasad wbudowanych **wdrażanie bazy danych SQL technologii transparent data encryption**, ustawia docelową grupę zasobów, a następnie tworzy przypisanie.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

`$assignment` Zmienna zawiera teraz identyfikator podmiotu zabezpieczeń tożsamości zarządzanych oraz standardowe wartości zwracane, gdy Tworzenie przypisania zasad. Jest możliwy za pośrednictwem `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Udziel określonych ról przy użyciu programu PowerShell

Nowej tożsamości zarządzanej musi wykonać replikację za pomocą usługi Azure Active Directory, zanim mogą być udzielone wymagane role. Po zakończeniu replikacji Poniższy przykład iteruje definicję zasad w programie `$policyDef` dla **roleDefinitionIds** i używa funkcji [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) do udzielenia nowej tożsamości zarządzanej role.

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

### <a name="grant-defined-roles-through-portal"></a>Udziel określonych ról za pomocą portalu

Istnieją dwa sposoby udzielania przydział tożsamość zarządzaną zdefiniowanych ról przy użyciu portalu, **kontrola dostępu (IAM)** lub edytowanie przypisania zasad lub inicjatywy, a następnie klikając polecenie **Zapisz**.

Aby dodać rolę przydział tożsamość zarządzaną, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając pozycję **Wszystkie usługi**, a następnie wyszukując i wybierając opcję **Zasady**.

1. Wybierz pozycję **Przypisania** w lewej części strony usługi Azure Policy.

1. Znajdź przydziału, którego tożsamość zarządzaną i kliknij nazwę.

1. Znajdź **identyfikator przypisania** właściwości na stronie edycji. Identyfikator przypisania będzie mniej więcej tak:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Nazwa tożsamości zarządzanej to ostatnia część przypisania identyfikator zasobu, który jest `2802056bfc094dfb95d4d7a5` w tym przykładzie. Ta część identyfikatora przydziału zasobu. Kopiuj

1. Przejdź do zasobu lub kontenera nadrzędnego zasobów (Grupa zasobów, subskrypcji, grupy zarządzania), który wymaga definicji roli, dodane ręcznie.

1. Kliknij przycisk **kontrola dostępu (IAM)** łącze w na stronie zasobów, a następnie kliknij przycisk **+ Dodaj przypisanie roli** w górnej części strony kontrola dostępu.

1. Wybierz odpowiednią rolę, który odpowiada **roleDefinitionIds** z definicji zasad.
   Pozostaw **Przypisz dostęp do** wartość domyślną "Azure AD użytkownika, grupy lub aplikacji". W **wybierz** pole, wklej lub wpisz część Identyfikatora zasobu przypisania znajdujące się wcześniej. Po zakończeniu wyszukiwania, kliknij obiekt o takiej samej nazwie, wybierz identyfikator, a następnie kliknij przycisk **Zapisz**.

## <a name="create-a-remediation-task"></a>Utwórz zadanie korygowania

### <a name="create-a-remediation-task-through-portal"></a>Tworzenie zadania korygującego za pomocą portalu

Podczas obliczania przypisanie zasad z **deployIfNotExists** lub **modyfikowaniem** efektów określa, czy istnieją niezgodne zasoby. Gdy zostaną odnalezione niezgodnych zasobów, szczegółowe informacje znajdują się na **korygowania** strony. Wraz z listy zasad, które mają niezgodne zasoby to opcja umożliwiająca wyzwalanie **zadań korygowania**. Ta opcja polega na utworzeniu wdrożenia z szablonu **deployIfNotExists** lub operacji **modyfikowania** .

Aby utworzyć **zadań korygowania**, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając pozycję **Wszystkie usługi**, a następnie wyszukując i wybierając opcję **Zasady**.

   ![Wyszukaj zasady w obszarze wszystkie usługi](../media/remediate-resources/search-policy.png)

1. Wybierz **korygowania** po lewej stronie strony usługi Azure Policy.

   ![Wybieranie opcji korygowania na stronie zasad](../media/remediate-resources/select-remediation.png)

1. Wszystkie przypisania zasad **deployIfNotExists** i **Modify** z niezgodnymi zasobami są zawarte w **zasadach do korygowania** kart i tabeli danych. Kliknij zasady z zasobami, które są niezgodne. **Nowe zadanie korygowania** zostanie otwarta strona.

   > [!NOTE]
   > Alternatywny sposób, aby otworzyć **zadań korygowania** strony jest znalezienie i kliknij zasady, które z **zgodności** stronie, a następnie kliknij przycisk **Utwórz zadanie korygowania** przycisku.

1. Na **nowe zadanie korygowania** strony, Filtruj zasoby, aby skorygować za pomocą **zakres** wielokropek, aby wybrać zasoby podrzędne, z której jest przypisany zasad (w tym do pojedynczego zasobu obiekty). Ponadto, za pomocą **lokalizacje** rozwinięcie listy w celu dalszego filtrowania zasobów. Tylko zasoby wymienione w tabeli zostaną skorygowane.

   ![Koryguj — Wybierz zasoby do skorygowania](../media/remediate-resources/select-resources.png)

1. Rozpocznij zadanie korygowania po zasobów zostały przefiltrowane przez kliknięcie przycisku **Koryguj**. Zostanie otwarta strona zgodność zasad **zadania korygowania** kartę, aby wyświetlić stan postępu zadań.

   ![Korygowanie postępu zadań korygowania](../media/remediate-resources/task-progress.png)

1. Kliknij pozycję **zadań korygowania** ze strony zasad zgodności, aby uzyskać szczegółowe informacje o postępie. Filtrowanie używane dla tego zadania jest wyświetlana wraz z listy zasobów są rozwiązane.

1. Z **zadań korygowania** stronie, kliknij prawym przyciskiem myszy na zasób, aby wyświetlić albo wdrożenia zadania korygowania lub zasobu. Na końcu wiersza kliknij **zdarzenia związane z** Aby wyświetlić szczegóły, takie jak komunikat o błędzie.

   ![Korygowanie — menu kontekstowe zadania zasobów](../media/remediate-resources/resource-task-context-menu.png)

Zasoby wdrożone za pośrednictwem **zadań korygowania** są dodawane do **wdrożonych zasobów** karty na stronie zasad zgodności.

### <a name="create-a-remediation-task-through-azure-cli"></a>Tworzenie zadania korygującego za pomocą interfejsu wiersza polecenia platformy Azure

Aby utworzyć **zadanie korygowania** przy użyciu interfejsu wiersza polecenia platformy Azure `az policy remediation` , Użyj poleceń. Zastąp `{subscriptionId}` ciąg identyfikatorem subskrypcji `{myAssignmentId}` i identyfikatorem przydziału **deployIfNotExists** lub **zmodyfikuj** zasady.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Aby poznać inne polecenia i przykłady korygowania, zobacz [AZ Policy korygowanie](/cli/azure/policy/remediation) Commands.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Utwórz zadanie korygujące za pośrednictwem Azure PowerShell

Aby utworzyć **zadanie korygowania** przy użyciu Azure PowerShell, użyj `Start-AzPolicyRemediation` poleceń. Zastąp `{subscriptionId}` ciąg identyfikatorem subskrypcji `{myAssignmentId}` i identyfikatorem przydziału **deployIfNotExists** lub **zmodyfikuj** zasady.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Inne polecenia cmdlet służące do korygowania i przykłady można znaleźć w module [AZ. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) .

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo utworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](getting-compliance-data.md).
- Zapoznaj się z informacjami o tym, czym jest Grupa zarządzania, aby [zorganizować swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).