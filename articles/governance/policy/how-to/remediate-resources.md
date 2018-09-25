---
title: Korygowanie niezgodnych zasobów przy użyciu usługi Azure Policy
description: Niniejszy instruktaż przeprowadzi Cię przez czynności naprawcze zasobów, które nie są zgodne z zasadami, usługa Azure Policy.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 747650bc47644cdca07f705f42d063c995ebe9bf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980257"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Korygowanie niezgodnych zasobów przy użyciu usługi Azure Policy

Zasoby, które nie są zgodne na **deployIfNotExists** zasad można umieścić w stanu zgodności za pośrednictwem **korygowania**. Korygowanie odbywa się przez poinstruowanie zasady uruchomią **deployIfNotExists** wpływu przypisanych zasad na istniejących zasobów. Niniejszy instruktaż poprowadzi kroki niezbędne do to osiągnąć.

## <a name="how-remediation-security-works"></a>Jak działają zabezpieczenia korygowania

Po uruchomieniu szablon zasad **deployIfNotExists** definicji zasad, jak za pomocą [tożsamości zarządzanej](../../../active-directory/managed-identities-azure-resources/overview.md).
Zasady tworzenia zarządzanych tożsamości dla każdego przypisania dla Ciebie, ale należy podać szczegółowe informacje o jakie role, aby przyznać tożsamości zarządzanej. Jeśli tożsamość zarządzana brakuje ról, jest on wyświetlany podczas przypisywania zasad lub inicjatywy zawierający zasady. Korzystając z portalu, automatycznie przyzna zasada tożsamość zarządzaną wymienionych ról po zainicjowaniu przypisania.

![Tożsamość zarządzana — Brak roli](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Jeśli zasób jest modyfikowany przez **deployIfNotExists** poza zakres przypisania zasad przydział tożsamość zarządzaną musi być programowo uzyskuje dostęp lub wdrożenie korygowania zakończy się niepowodzeniem.

## <a name="configure-policy-definition"></a>Skonfiguruj definicję zasad

Pierwszym krokiem jest zdefiniowanie ról, **deployIfNotExists** musi w definicji zasad, aby pomyślnie wdrożyć zawartość dołączone szablonu. W obszarze **szczegóły** właściwości, Dodaj **roleDefinitionIds** właściwości. Jest to tablica ciągów, które odpowiadają ról w danym środowisku.
Aby uzyskać pełny przykład, zobacz [przykład deployIfNotExists](../concepts/effects.md#deployifnotexists-example).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**roleDefinitionIds** używa identyfikatora wszystkich zasobów i nie w krótkim **roleName** roli. Aby uzyskać identyfikator dla roli "Współautor" w danym środowisku, należy użyć następującego kodu:

```azurecli-interactive
az role definition list --name 'Contributor'
```

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Contributor'
```

## <a name="create-a-remediation-task"></a>Utwórz zadanie korygowania

Podczas obliczania wartości, przypisanie zasad o **deployIfNotExists** efekt Określa, czy istnieją niezgodne zasoby. Gdy zostaną odnalezione niezgodnych zasobów, szczegółowe informacje znajdują się na **korygowania** strony. Wraz z listy zasad, które mają niezgodne zasoby to opcja umożliwiająca wyzwalanie **zadań korygowania**. Jest to, co tworzy wdrożenie z **deployIfNotExists** szablonu.

Aby utworzyć **zadań korygowania**, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając pozycję **Wszystkie usługi**, a następnie wyszukując i wybierając opcję **Zasady**.

   ![Wyszukiwanie zasad](../media/remediate-resources/search-policy.png)

1. Wybierz **korygowania** po lewej stronie strony usługi Azure Policy.

   ![Wybierz opcję korygowania](../media/remediate-resources/select-remediation.png)

1. Wszystkie **deployIfNotExists** przypisania zasad za pomocą niezgodnych zasobów znajdują się w **zasad w celu korygowania** kartę i tabelę danych. Kliknij zasady z zasobami, które są niezgodne. **Nowe zadanie korygowania** zostanie otwarta strona.

   > [!NOTE]
   > Alternatywny sposób, aby otworzyć **zadań korygowania** strony jest znalezienie i kliknij zasady, które z **zgodności** stronie, a następnie kliknij przycisk **Utwórz zadanie korygowania** przycisku.

1. Na **nowe zadanie korygowania** strony, Filtruj zasoby, aby skorygować za pomocą **zakres** wielokropek, aby wybrać zasoby podrzędne, z których przypisano zasad (w tym do pojedynczego zasobu obiekty). Ponadto, za pomocą **lokalizacje** rozwinięcie listy w celu dalszego filtrowania zasobów. Tylko zasoby wymienione w tabeli zostaną skorygowane.

   ![Korygowanie — Wybierz zasoby](../media/remediate-resources/select-resources.png)

1. Zainicjować zadanie korygowania, gdy zasoby zostały przefiltrowane przez kliknięcie przycisku **Koryguj**. Zostanie otwarta strona zgodność zasad **zadania korygowania** kartę, aby wyświetlić stan postępu zadań.

   ![Korygowanie — postęp zadania](../media/remediate-resources/task-progress.png)

1. Kliknij pozycję **zadań korygowania** ze strony zasad zgodności, aby uzyskać szczegółowe informacje o postępie. Filtrowanie używane dla tego zadania są wyświetlane wraz z listy zasobów są rozwiązane.

1. Z **zadań remedation** stronie, kliknij prawym przyciskiem myszy na zasób, aby wyświetlić albo wdrożenia zadania korygowania lub zasobu. Na końcu wiersza kliknij **zdarzenia związane z** Aby wyświetlić szczegóły, takie jak komunikat o błędzie.

   ![Korygowanie — menu kontekstowe zadania zasobów](../media/remediate-resources/resource-task-context-menu.png)

Zasoby wdrożone za pośrednictwem **zadań korygowania** zostaną dodane do **wdrożonych zasobów** karty na stronie zasad zgodności z niewielkim opóźnieniem.

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj przykłady na [przykładów usługi Azure Policy](../samples/index.md)
- Przegląd [struktura definicji zasad](../concepts/definition-structure.md)
- Przegląd [zrozumienia efektów zasad](../concepts/effects.md)
- Zrozumienie sposobu [programowe tworzenie zasad](programmatically-create.md)
- Dowiedz się, jak [uzyskać dane na temat zgodności](getting-compliance-data.md)
- Sprawdzanie, co to jest grupa zarządzania, na stronie [Organize your resources with Azure management groups (Organizowanie zasobów za pomocą grup zarządzania platformy Azure)](../../management-groups/overview.md)