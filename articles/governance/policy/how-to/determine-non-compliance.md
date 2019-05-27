---
title: Ustalanie przyczyn niezgodności
description: Jeśli zasób jest niezgodne, istnieje wiele możliwych przyczyn. Dowiedz się dowiedzieć się, co było przyczyną braku zgodności.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 6e3e01ca9bd459aa6c6aca8dfaacb98b1267fada
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979346"
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

|Reason | Warunek |
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

## <a name="compliance-details-for-guest-configuration"></a>Szczegóły zgodności konfiguracji gościa

Aby uzyskać _inspekcji_ zasad w _konfiguracji gościa_ kategorii, może istnieć wiele ustawień, ocenione w ramach maszyny Wirtualnej i konieczne będzie wyświetlać szczegóły poszczególnych ustawień. Na przykład, jeśli masz inspekcji, aby uzyskać listę zainstalowanych aplikacji i stanu przypisania jest _niezgodne_, musisz wiedzieć, określonych aplikacji, które są nieobecne.

Możesz również utracić dostęp do bezpośrednio zalogować się do maszyny Wirtualnej, ale należy sporządzić raport na temat przyczyny maszyna wirtualna jest _niezgodne_. Na przykład może być inspekcji, że maszyny wirtualne są przyłączone do domeny i obejmują bieżącego członkostwa w domenie, w obszarze szczegółów raportowania.

### <a name="azure-portal"></a>Azure Portal

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając pozycję **Wszystkie usługi**, a następnie wyszukując i wybierając opcję **Zasady**.

1. Na **Przegląd** lub **zgodności** wybierz przypisanie zasad dla dowolnego inicjatywy, który zawiera definicję zasad konfiguracji gościa to _niezgodne_.

1. Wybierz _inspekcji_ zasad w inicjatywy to _niezgodne_.

   ![Wyświetl szczegóły definicji inspekcji](../media/determine-non-compliance/guestconfig-audit-compliance.png)

1. Na **zgodność zasobów** karcie podano następujące informacje:

   - **Nazwa** — nazwa przypisania konfiguracji gościa.
   - **Zasób nadrzędny** -maszynę wirtualną w _niezgodne_ stanu dla wybranego przypisania konfiguracji gościa.
   - **Typ zasobu** — _guestConfigurationAssignments_ pełnej nazwy.
   - **Ostatnio oceniono** — czas ostatniego powiadomienia, usługi gościa konfiguracji usługi Azure Policy o stanie docelowej maszyny wirtualnej.

   ![Wyświetl szczegóły zgodności.](../media/determine-non-compliance/guestconfig-assignment-view.png)

1. Wybierz nazwę przypisanie konfiguracji gościa w **nazwa** kolumnie, aby otworzyć **zgodność zasobów** strony.

1. Wybierz **Wyświetl zasób** znajdujący się u góry strony Aby otworzyć **przypisania gościa** strony.

**Przypisania gościa** strony wyświetli wszystkie szczegóły dostępne zgodności. Każdy wiersz w widoku reprezentuje wersję próbną, która została wykonana na maszynie wirtualnej. W **Przyczyna** kolumny, frazę opisujące, dlaczego jest przypisanie gościa _niezgodne_ jest wyświetlany. Jeśli masz inspekcji, że maszyny wirtualne powinny być przyłączony do domeny, na przykład **Przyczyna** kolumny zostanie wyświetlony tekst w tym bieżące członkostwo w domenie.

![Wyświetl szczegóły zgodności.](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Można również wyświetlić szczegółowe informacje o zgodności z programu Azure PowerShell. Najpierw upewnij się, że masz zainstalowany moduł konfiguracji gościa.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Można wyświetlić bieżący stan wszystkich przypisań gościa dla maszyny Wirtualnej przy użyciu następującego polecenia:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Aby wyświetlić tylko _Przyczyna_ frazę, która opisuje, dlaczego maszyna wirtualna jest _niezgodne_, zwraca tylko właściwości elementu podrzędnego przyczyna.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Historia zgodności można również danych wyjściowych przydziałów gościa w zakresie dla maszyny wirtualnej. Dane wyjściowe tego polecenia zawiera szczegóły każdego raportu dla maszyny Wirtualnej.

> [!NOTE]
> Dane wyjściowe mogą zwracać dużej ilości danych. Zalecane jest przechowywanie danych wyjściowych w zmiennej.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Aby uprościć ten widok, należy użyć **ShowChanged** parametru. Dane wyjściowe tego polecenia obejmuje tylko raporty, które następnie zmianę stanu zgodności.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>Historia zmian (wersja zapoznawcza)

Jako część nowego **publicznej wersji zapoznawczej**, ostatnich 14 dni historii zmian są dostępne dla wszystkich zasobów platformy Azure, które obsługują [ukończyć usuwanie tryb](../../../azure-resource-manager/complete-mode-deletion.md). Historię zmian zawiera szczegółowe informacje o, gdy wykryte zmiany i a _visual diff_ dla każdej zmiany. Wykrywanie zmian jest wyzwalany, gdy dodane, usunięte lub zmienione właściwości Menedżera zasobów.

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając pozycję **Wszystkie usługi**, a następnie wyszukując i wybierając opcję **Zasady**.

1. Na **Przegląd** lub **zgodności** wybierz zasadę w dowolnym **stan zgodności**.

1. W obszarze **zgodność zasobów** karcie **zgodność z zasadami** wybierz zasób.

1. Wybierz **historię zmian (wersja zapoznawcza)** karcie **zgodność zasobów** strony. Lista Wykryto zmiany, jeśli nie są wyświetlane wszelkie istnieje.

   ![Azure karcie Historia zmian zasad zgodności zasobu strony](../media/determine-non-compliance/change-history-tab.png)

1. Wybierz jeden z wykryte zmiany. _Visual diff_ dla zasobu są przedstawione na **historię zmian** strony.

   ![Azure zasad zmian historii Visual różnicowego na stronie Historia zmian](../media/determine-non-compliance/change-history-visual-diff.png)

_Visual diff_ aides do identyfikowania zmiany w zasobie. Wykryto zmiany mogą być niepowiązane z bieżącego stanu zgodności zasobu.

Dane historii zmian są udostępniane przez [wykres zasobów Azure](../../resource-graph/overview.md). Aby wysłać zapytanie tych informacji poza witryny Azure portal, zobacz [pobieranie zmian zasobów](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj przykłady na [przykładów usługi Azure Policy](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Zrozumienie sposobu [programowe tworzenie zasad](programmatically-create.md).
- Dowiedz się, jak [Pobierz dane zgodności](getting-compliance-data.md).
- Dowiedz się, jak [korygowanie niezgodnych zasobów](remediate-resources.md).
- Przejrzyj grupy zarządzania jest [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../management-groups/overview.md).