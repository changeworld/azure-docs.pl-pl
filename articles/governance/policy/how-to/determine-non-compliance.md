---
title: Ustalanie przyczyn niezgodności
description: Gdy zasób jest niezgodny, istnieje wiele możliwych przyczyn. Dowiedz się, co było przyczyną niezgodności.
ms.date: 04/26/2019
ms.topic: how-to
ms.openlocfilehash: c931831ddf3cc727b9861e75969eac3bf00c9e45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264637"
---
# <a name="determine-causes-of-non-compliance"></a>Ustalanie przyczyn niezgodności

Gdy zasób platformy Azure zostanie uznany za niezgodny z regułą zasad, warto zrozumieć, z którą częścią reguły nie jest zgodny zasób. Jest również przydatne, aby zrozumieć, co zmiana zmieniła wcześniej zgodny zasób, aby uczynić go niezgodnym. Istnieją dwa sposoby znajdowania tych informacji:

> [!div class="checklist"]
> - [Szczegóły zgodności](#compliance-details)
> - [Historia zmian (wersja zapoznawcza)](#change-history)

## <a name="compliance-details"></a>Szczegóły zgodności

Gdy zasób jest niezgodny, szczegóły zgodności dla tego zasobu są dostępne na stronie **Zgodność zasad.** Okienko szczegółów zgodności zawiera następujące informacje:

- Szczegóły zasobu, takie jak nazwa, typ, lokalizacja i identyfikator zasobu
- Stan zgodności i sygnatura czasowa ostatniej oceny dla bieżącego przypisania zasad
- Lista _przyczyn_ niezgodności zasobów

> [!IMPORTANT]
> Ponieważ szczegóły zgodności zasobu _niezgodnego_ pokazuje bieżącą wartość właściwości tego zasobu, użytkownik musi mieć operację **odczytu** do **typu** zasobu. Na przykład jeśli _zasobem niezgodnym_ jest **Microsoft.Compute/virtualMachines,** użytkownik musi mieć operację **Microsoft.Compute/virtualMachines/read.** Jeśli użytkownik nie ma wymaganej operacji, wyświetlany jest błąd dostępu.

Aby wyświetlić szczegóły zgodności, wykonaj następujące kroki:

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając opcję **Wszystkie usługi** i następnie wyszukując i wybierając opcję **Zasada**.

1. Na stronie **Przegląd** lub **Zgodność** wybierz zasadę w **stanie zgodności,** który jest _niezgodny_.

1. Na karcie **Zgodność zasobów na** stronie Zgodność **zasad** kliknij prawym przyciskiem myszy lub wybierz wielokropek zasobu w **stanie zgodności,** który jest _niezgodny_. Następnie wybierz pozycję **Wyświetl szczegóły zgodności**.

   ![Wyświetl szczegóły zgodności, opcja](../media/determine-non-compliance/view-compliance-details.png)

1. W okienku **Szczegóły zgodności** są wyświetlane informacje z najnowszej oceny zasobu do bieżącego przypisania zasad. W tym przykładzie pole **Microsoft.Sql/servers/version** jest _12.0,_ podczas gdy definicja zasad oczekiwana _14.0_. Jeśli zasób jest niezgodny z wielu powodów, każdy z nich jest wymieniony w tym okienku.

   ![Okienko szczegółów zgodności i przyczyny niezgodności](../media/determine-non-compliance/compliance-details-pane.png)

   Dla **auditIfNotExists** lub **deployIfNotExists** definicji zasad, szczegóły obejmują **details.type** właściwości i wszystkie właściwości opcjonalne. Aby uzyskać listę, zobacz [auditIfNotExists właściwości](../concepts/effects.md#auditifnotexists-properties) i [deployIfNotExists właściwości](../concepts/effects.md#deployifnotexists-properties). **Ostatnio oceniony zasób** jest powiązanym zasobem z sekcji **szczegółów** definicji.

   Przykład częściowego **deployIfNotExists** definicji:

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

   ![Okienko szczegółów zgodności — *ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Aby chronić dane, gdy wartość właściwości jest _kluczem tajnym,_ bieżąca wartość wyświetla gwiazdki.

Te szczegóły wyjaśniają, dlaczego zasób jest obecnie niezgodny, ale nie pokazują, kiedy zmiana została wniesienia do zasobu, który spowodował, że stał się niezgodny. Aby uzyskać te informacje, zobacz [Historia zmian (wersja zapoznawcza)](#change-history) poniżej.

### <a name="compliance-reasons"></a>Przyczyny zgodności

Następująca macierz mapuje każdą możliwą _przyczynę_ do [odpowiedzialnego warunku](../concepts/definition-structure.md#conditions) w definicji zasad:

|Przyczyna | Warunek |
|-|-|
|Bieżąca wartość musi zawierać wartość docelową jako klucz. |containsKey **not** lub notContainsKey |
|Bieżąca wartość musi zawierać wartość docelową. |zawiera **not** lub niezawiera |
|Bieżąca wartość musi być równa wartości docelowej. |równa się **not** lub nieEquals |
|Bieżąca wartość musi być mniejsza niż wartość docelowa. |mniej lub **nie** większaLubEquals |
|Bieżąca wartość musi być większa lub równa wartości docelowej. |greaterOrEquals lub **nie** mniej |
|Bieżąca wartość musi być większa niż wartość docelowa. |większe lub **nie** leasinguLubyce |
|Bieżąca wartość musi być mniejsza lub równa wartości docelowej. |leasingOrEquals lub **nie** większy |
|Bieżąca wartość musi istnieć. |Istnieje |
|Bieżąca wartość musi być w wartości docelowej. |w in or **notIn** |
|Bieżąca wartość musi być podobna do wartości docelowej. |jak lub **nieJak** |
|Bieżąca wartość musi odpowiadać wartości docelowej z uwzględnieniem wielkości liter. |dopasować lub **nie** namatchać |
|Bieżąca wartość musi odpowiadać wartości docelowej bez uwzględniania wielkości liter. |matchInsensitively **not** or notMatchInsensitively matchInsensitively matchInsensitively or notMatchInsensitively matchInsensitively matchInsensitively matchIns |
|Bieżąca wartość nie może zawierać wartości docelowej jako klucza. |notContainsKey lub **nie** containsKey|
|Bieżąca wartość nie może zawierać wartości docelowej. |nie zawiera lub **nie** zawiera |
|Bieżąca wartość nie może być równa wartości docelowej. |notEquals lub **nie** równa się |
|Bieżąca wartość nie może istnieć. |**nie** istnieje  |
|Bieżąca wartość nie może być w wartości docelowej. |notIn lub **nie** w |
|Bieżąca wartość nie może być podobna do wartości docelowej. |notLike lub **nie** lubić |
|Bieżąca wartość nie może odpowiadać wartości docelowej z uwzględnieniem wielkości liter. |notMatch lub **nie** pasuje |
|Bieżąca wartość nie może odpowiadać wartości docelowej bez uwzględniania wielkości liter. |notMatchInsensitively or notInsensitively notMatchInsensitively or notInsensitively notMatchInsensitively or **not** |
|Żadne powiązane zasoby nie odpowiadają szczegółom efektu w definicji zasad. |Zasób typu zdefiniowany w **then.details.type** i powiązany z zasobem zdefiniowanym w **if** części reguły zasad nie istnieje. |

## <a name="compliance-details-for-guest-configuration"></a>Szczegóły zgodności dotyczące konfiguracji gościa

W przypadku _zasad auditIfNotExists_ w kategorii _Konfiguracja gościa_ może istnieć wiele ustawień ocenianych wewnątrz maszyny Wirtualnej i musisz wyświetlić szczegóły na ustawienie. Na przykład, jeśli przeprowadzasz inspekcję listy zasad haseł, a tylko jedna z nich ma stan _Niezgodny,_ musisz wiedzieć, które określone zasady haseł są niezgodne i dlaczego.

Możesz również nie mieć dostępu do bezpośredniego logowania się do maszyny Wirtualnej, ale musisz poinformować, dlaczego maszyna wirtualna jest _niezgodna._

### <a name="azure-portal"></a>Portal Azure

Rozpocznij od wykonania tych samych kroków w powyższej sekcji, aby wyświetlać szczegóły zgodności zasad.

W widoku okienka **Szczegóły zgodności** kliknij łącze **Ostatnio oceniony zasób**.

   ![Wyświetl szczegóły definicji auditIfNotExists](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

Na stronie **Przypisanie gościa** są wyświetlane wszystkie dostępne szczegóły zgodności. Każdy wiersz w widoku reprezentuje ocenę, która została wykonana wewnątrz komputera. W **kolumnie Przyczyna** wyświetlana jest fraza opisująca, dlaczego przypisanie gościa jest _niezgodne._ Na przykład w przypadku inspekcji zasad haseł kolumna **Przyczyna** będzie wyświetlana w kolumnie Tekst zawierający bieżącą wartość dla każdego ustawienia.

![Wyświetlanie szczegółów zgodności](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Można również wyświetlić szczegóły zgodności z programu Azure PowerShell. Najpierw upewnij się, że masz zainstalowany moduł konfiguracji gościa.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Bieżący stan wszystkich przypisań gościa dla maszyny Wirtualnej można wyświetlić za pomocą następującego polecenia:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Aby wyświetlić tylko wyrażenie _przyczyny,_ który opisuje, dlaczego maszyna wirtualna jest _niezgodna_, zwraca tylko Przyczyna podrzędna właściwości.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Można również wysuń historię zgodności dla przypisań gościa w zakresie dla komputera. Dane wyjściowe z tego polecenia zawiera szczegóły każdego raportu dla maszyny Wirtualnej.

> [!NOTE]
> Dane wyjściowe mogą zwracać dużą ilość danych. Zaleca się przechowywanie danych wyjściowych w zmiennej.

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

Aby uprościć ten widok, należy użyć **showchanged** parametru. Dane wyjściowe z tego polecenia obejmują tylko raporty, które nastąpiły po zmianie stanu zgodności.

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

## <a name="change-history-preview"></a><a name="change-history"/>Historia zmian (wersja zapoznawcza)

W ramach nowej **publicznej wersji zapoznawczej**ostatnie 14 dni historii zmian jest dostępnych dla wszystkich zasobów platformy Azure obsługujących [usuwanie trybu pełnego.](../../../azure-resource-manager/templates/complete-mode-deletion.md) Historia zmian zawiera szczegółowe informacje o tym, kiedy wykryto zmianę i _wizualny dyferwek_ dla każdej zmiany. Wykrywanie zmian jest wyzwalane po dodaniu, usunięciu lub zmianie właściwości Menedżera zasobów.

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając opcję **Wszystkie usługi** i następnie wyszukując i wybierając opcję **Zasada**.

1. Na stronie **Przegląd** lub **Zgodność** wybierz zasadę w dowolnym **stanie zgodności**.

1. W obszarze **Zgodność zasobów na** stronie Zgodność **zasad** wybierz zasób.

1. Wybierz kartę **Historia zmian (podgląd)** na stronie **Zgodność zasobów.** Zostanie wyświetlona lista wykrytych zmian, jeśli istnieją.

   ![Karta Historia zmian zasad platformy Azure na stronie Zgodność zasobów](../media/determine-non-compliance/change-history-tab.png)

1. Wybierz jedną z wykrytych zmian. _Wizualny dyferda_ dla zasobu jest przedstawiony na stronie **Historia zmian.**

   ![Różnice wizualne historii zmian zasad platformy Azure na stronie Historia zmian](../media/determine-non-compliance/change-history-visual-diff.png)

_Wizualne diff_ pomocników w identyfikacji zmian w zasobie. Wykryte zmiany mogą nie być związane z bieżącym stanem zgodności zasobu.

Dane historii zmian są dostarczane przez [usługę Azure Resource Graph](../../resource-graph/overview.md). Aby zbadać te informacje poza portalem Azure, zobacz [Get changes resource changes](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Następne kroki

- Przejrzyj przykłady w [przykładach zasad platformy Azure](../samples/index.md).
- Przejrzyj temat [Struktura definicji zasad Azure Policy](../concepts/definition-structure.md).
- Przejrzyj [wyjaśnienie działania zasad](../concepts/effects.md).
- Dowiedz się, jak [programowo tworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [uzyskać dane dotyczące zgodności](get-compliance-data.md).
- Dowiedz się, jak [korygować niezgodne zasoby](remediate-resources.md).
- Sprawdź, czym jest grupa zarządzania, [organizuj swoje zasoby za pomocą grup zarządzania platformy Azure](../../management-groups/overview.md).
