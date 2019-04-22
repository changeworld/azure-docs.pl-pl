---
title: Ustalanie przyczyn niezgodności
description: Jeśli zasób jest niezgodne, istnieje wiele możliwych przyczyn. Dowiedz się dowiedzieć się, co było przyczyną braku zgodności.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 8a593e92d7f24885c35043b874528e881d2e021e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59276103"
---
# <a name="determine-causes-of-non-compliance"></a>Ustalanie przyczyn niezgodności

Zasób platformy Azure zostanie uznane za niezgodne reguły zasad, to zrozumieć, która część reguły, zasób nie jest zgodne z. Jest to również przydatne zrozumieć, jakie zmiany zmienić uprzednio zgodna zasobów, aby stał się brak zgodności. Istnieją dwa sposoby znajdowania tych informacji:

> [!div class="checklist"]
> - [Szczegóły zgodności](#compliance-details)
> - [Historia zmian (wersja zapoznawcza)](#change-history-preview)

## <a name="compliance-details"></a>Szczegóły zgodności

Zasób jest niezgodne, szczegóły zgodności dla tego zasobu są dostępne z **zgodność z zasadami** strony. W okienku szczegółów zgodności zawiera następujące informacje:

- Szczegóły zasobu, takie jak nazwa, typ, lokalizację i identyfikator zasobu
- Stan zgodności i sygnaturę czasową ostatniej oceny bieżące przypisania zasad
- Lista _przyczyn_ dla zasobów niezgodności

> [!IMPORTANT]
> Jako szczegóły zgodności _niezgodne_ zasobów zawiera bieżącą wartość właściwości tego zasobu, użytkownik musi mieć **odczytu** operacji **typu** programu zasób. Na przykład jeśli _niezgodne_ zasób jest **Microsoft.Compute/virtualMachines** , a następnie użytkownik musi mieć **Microsoft.Compute/virtualMachines/read** Operacja. Jeśli użytkownik nie ma wymaganych operacji, jest wyświetlany błąd dostępu.

Aby wyświetlić szczegóły zgodności, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając pozycję **Wszystkie usługi**, a następnie wyszukując i wybierając opcję **Zasady**.

1. Na **Przegląd** lub **zgodności** wybierz zasadę w **stan zgodności** czyli _niezgodne_.

1. W obszarze **zgodność zasobów** karcie **zgodność z zasadami** strony, kliknij prawym przyciskiem myszy lub wybierz wielokropek zasobu w **stan zgodności** to znaczy  _Niezgodne_. Następnie wybierz pozycję **wyświetlić szczegóły zgodności**.

   ![Opcja Szczegóły zgodności widoku](../media/determine-non-compliance/view-compliance-details.png)

1. **Szczegóły zgodności** okienko wyświetla informacje z najnowszej wersji ewaluacyjnej zasobu do bieżącego przypisania zasad. W tym przykładzie pole **Microsoft.Sql/servers/version** okaże się _12.0_ podczas gdy oczekiwano definicji zasad _14.0_. Jeśli zasób jest niezgodny z wielu powodów, każda znajduje się w tym okienku.

   ![W okienku szczegółów zgodności i przyczyn braku zgodności](../media/determine-non-compliance/compliance-details-pane.png)

   Dla **auditIfNotExists** lub **deployIfNotExists** zawierają szczegóły definicji zasad **details.type** właściwości oraz wszelkie opcjonalne właściwości. Aby uzyskać listę, zobacz [właściwości auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) i [właściwości deployIfNotExists](../concepts/effects.md#deployifnotexists-properties). **Ostatnio oceniono zasobów** jest zasobem powiązane z **szczegóły** sekcja definicji.

   Przykład częściowe **deployIfNotExists** definicji:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![W okienku szczegółów zgodności - * ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Aby chronić dane, gdy wartość właściwości jest _klucz tajny_ bieżącą wartość wyświetla gwiazdki.

Te informacje wyjaśniają, dlaczego zasób jest obecnie niezgodne, ale nie pokazuj, kiedy zmiana została wprowadzona do zasobu, który spowodował jego stanie się niezgodne. Aby uzyskać te informacje, zobacz [(wersja zapoznawcza) historię zmian](#change-history-preview) poniżej.

### <a name="compliance-reasons"></a>Zachowania zgodności

Następujące macierzy mapuje każdą możliwą _Przyczyna_ w odpowiedzialne [warunek](../concepts/definition-structure.md#conditions) w definicji zasad:

|Przyczyna | Warunek |
|-|-|
|Bieżąca wartość musi zawierać wartość docelową jako klucz. |containsKey lub **nie** notContainsKey |
|Bieżąca wartość musi zawierać wartość docelową. |zawiera lub **nie** notContains |
|Bieżąca wartość musi być równa wartości docelowej. |równa się lub **nie** notEquals |
|Bieżąca wartość musi istnieć. |Istnieje |
|Bieżąca wartość musi zawierać się w wartości docelowej. |w lub **nie** notIn |
|Bieżąca wartość musi być podobna do wartości docelowej. |Podobnie jak lub **nie** notLike |
|Bieżąca wartość musi być zgodna z wartością docelową (z uwzględnieniem wielkości liter). |zgodne lub **nie** notMatch |
|Bieżąca wartość musi być zgodna z wartością docelową (bez uwzględniania wielkości liter). |matchInsensitively lub **nie** notMatchInsensitively |
|Bieżąca wartość nie może zawierać wartości docelowej jako klucza. |notContainsKey lub **nie** containsKey|
|Bieżąca wartość nie może zawierać wartości docelowej. |notContains lub **nie** zawiera |
|Bieżąca wartość nie może być równa wartości docelowej. |notEquals lub **nie** równa się |
|Bieżąca wartość nie może istnieć. |**nie** istnieje  |
|Bieżąca wartość nie może zawierać się w wartości docelowej. |notIn lub **nie** w |
|Bieżąca wartość nie może być podobna do wartości docelowej. |notLike lub **nie** np. |
|Bieżąca wartość nie może być zgodna z wartością docelową (z uwzględnieniem wielkości liter). |notMatch lub **nie** dopasowania |
|Bieżąca wartość nie może być zgodna z wartością docelową (bez uwzględniania wielkości liter). |notMatchInsensitively lub **nie** matchInsensitively |
|Żadne powiązane zasoby nie są zgodne ze szczegółami efektu w definicji zasad. |Zasób typu zdefiniowanego w **then.details.type** i powiązane z zasobem zdefiniowane w **Jeśli** część reguły zasad nie istnieje. |

## <a name="change-history-preview"></a>Historia zmian (wersja zapoznawcza)

Jako część nowego **publicznej wersji zapoznawczej**, w ciągu ostatnich 14 dni zmian historii jest dostępna dla wszystkich zasobów platformy Azure, które obsługują [ukończyć usuwanie tryb](../../../azure-resource-manager/complete-mode-deletion.md). Historię zmian zawiera szczegółowe informacje o, gdy wykryte zmiany i a _visual diff_ dla każdej zmiany. Wykrywanie zmian jest wyzwalany, gdy dodane, usunięte lub zmienione właściwości Menedżera zasobów.

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając pozycję **Wszystkie usługi**, a następnie wyszukując i wybierając opcję **Zasady**.

1. Na **Przegląd** lub **zgodności** wybierz zasadę w dowolnym **stan zgodności**.

1. W obszarze **zgodność zasobów** karcie **zgodność z zasadami** wybierz zasób.

1. Wybierz **historię zmian (wersja zapoznawcza)** karcie **zgodność zasobów** strony. Lista Wykryto zmiany, jeśli nie są wyświetlane wszelkie istnieje.

   ![Karta Historia zmian zasad na stronie zgodność zasobów](../media/determine-non-compliance/change-history-tab.png)

1. Wybierz jeden z wykryte zmiany. _Visual diff_ dla zasobu są przedstawione na **historię zmian** strony.

   ![Diff Visual historii zmian zasad na stronie Historia zmian](../media/determine-non-compliance/change-history-visual-diff.png)

_Visual diff_ aides do identyfikowania zmiany w zasobie. Wykryto zmiany mogą być niepowiązane z bieżącego stanu zgodności zasobu.

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj przykłady na [przykładów usługi Azure Policy](../samples/index.md)
- Przegląd [struktura definicji zasad](../concepts/definition-structure.md)
- Przegląd [zrozumienia efektów zasad](../concepts/effects.md)
- Zrozumienie sposobu [programowe tworzenie zasad](programmatically-create.md)
- Dowiedz się, jak [uzyskać dane na temat zgodności](getting-compliance-data.md)
- Dowiedz się, jak [korygowanie niezgodnych zasobów](remediate-resources.md)
- Sprawdzanie, co to jest grupa zarządzania, na stronie [Organize your resources with Azure management groups (Organizowanie zasobów za pomocą grup zarządzania platformy Azure)](../../management-groups/overview.md)