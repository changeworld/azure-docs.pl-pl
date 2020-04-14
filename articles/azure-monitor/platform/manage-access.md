---
title: Zarządzanie obszarami roboczymi usługi Log Analytics w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Można zarządzać dostępem do danych przechowywanych w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor przy użyciu uprawnień zasobu, obszaru roboczego lub poziomu tabeli. W tym artykule opisano sposób ukończenia.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: b8d7f995997b828c2323b3e6934b97354c2f8c8b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255247"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Zarządzanie dostępem do danych dziennika i obszarów roboczych w usłudze Azure Monitor

Usługa Azure Monitor przechowuje dane [dziennika](data-platform-logs.md) w obszarze roboczym usługi Log Analytics. Obszar roboczy jest kontenerem, który zawiera informacje o danych i konfiguracji. Aby zarządzać dostępem do danych dziennika, należy wykonać różne zadania administracyjne związane z obszarem roboczym.

W tym artykule wyjaśniono, jak zarządzać dostępem do dzienników i administrować obszarami roboczymi, które je zawierają, w tym jak udzielić dostępu do: 

* Obszar roboczy przy użyciu uprawnień obszaru roboczego.
* Użytkownicy, którzy potrzebują dostępu do rejestrowania danych z określonych zasobów przy użyciu kontroli dostępu opartej na rolach platformy Azure (RBAC).
* Użytkownicy, którzy potrzebują dostępu do rejestrowania danych w określonej tabeli w obszarze roboczym przy użyciu usługi Azure RBAC.

## <a name="configure-access-control-mode"></a>Konfigurowanie trybu kontroli dostępu

Można wyświetlić [tryb kontroli dostępu](design-logs-deployment.md) skonfigurowany w obszarze roboczym z witryny Azure portal lub za pomocą programu Azure PowerShell.  To ustawienie można zmienić za pomocą jednej z następujących obsługiwanych metod:

* Azure Portal

* Azure PowerShell

* Szablon usługi Azure Resource Manager

### <a name="from-the-azure-portal"></a>Z witryny Azure Portal

Bieżący tryb kontroli dostępu do obszaru roboczego można wyświetlić na stronie **Przegląd** obszaru roboczego w menu **obszaru roboczego usługi Log Analytics.**

![Wyświetlanie trybu kontroli dostępu do obszaru roboczego](media/manage-access/view-access-control-mode.png)

1. Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).
1. W witrynie Azure portal wybierz obszary robocze usługi Log Analytics > obszaru roboczego.

To ustawienie można zmienić na stronie **Właściwości** obszaru roboczego. Zmiana ustawienia zostanie wyłączona, jeśli nie masz uprawnień do konfigurowania obszaru roboczego.

![Zmienianie trybu dostępu do obszaru roboczego](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>Korzystanie z programu PowerShell

Użyj następującego polecenia, aby sprawdzić tryb kontroli dostępu dla wszystkich obszarów roboczych w subskrypcji:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

Dane wyjściowe powinny przypominać następujące dane:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Wartość `False` oznacza, że obszar roboczy jest skonfigurowany w trybie dostępu kontekstowego obszaru roboczego.  Wartość `True` oznacza, że obszar roboczy jest skonfigurowany w trybie dostępu kontekstu zasobów.

> [!NOTE]
> Jeśli obszar roboczy jest zwracany bez wartości logicznej i jest `False` pusty, jest to również zgodne z wynikami wartości.
>

Użyj następującego skryptu, aby ustawić tryb kontroli dostępu dla określonego obszaru roboczego na uprawnienie kontekst zasobu:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Użyj następującego skryptu, aby ustawić tryb kontroli dostępu dla wszystkich obszarów roboczych w subskrypcji do uprawnienia kontekstu zasobu:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>Używanie szablonu usługi Resource Manager

Aby skonfigurować tryb dostępu w szablonie usługi Azure Resource Manager, ustaw flagę funkcji **enableLogAccessUsingOnlyResourcePermissions** w obszarze roboczym na jedną z następujących wartości.

* **false**: Ustaw uprawnienia obszaru roboczego do kontekstu. Jest to ustawienie domyślne, jeśli flaga nie jest ustawiona.
* **true**: Ustaw uprawnienia kontekstu zasobu do kontekstu.

## <a name="manage-access-using-workspace-permissions"></a>Zarządzanie dostępem przy użyciu uprawnień obszaru roboczego

Każdy obszar roboczy może mieć skojarzone z nim wiele kont, a każde konto może mieć dostęp do wielu obszarów roboczych. Program Access jest zarządzany przy użyciu [dostępu opartego na rolach platformy Azure.](../../role-based-access-control/role-assignments-portal.md)

Następujące działania również wymagają uprawnień platformy Azure:

|Akcja |Wymagane uprawnienia platformy Azure |Uwagi |
|-------|-------------------------|------|
| Dodawanie i usuwanie rozwiązań do monitorowania | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Te uprawnienia należy nadać na poziomie grupy zasobów lub subskrypcji. |
| Zmienianie warstwy cenowej | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Wyświetlanie danych w kafelkach rozwiązań *Backup* i *Site Recovery* | Administrator/współadministrator | Uzyskuje dostęp do zasobów wdrożonych przy użyciu klasycznego modelu wdrażania |
| Tworzenie obszaru roboczego w witrynie Azure Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Wyświetlanie podstawowych właściwości obszaru roboczego i wprowadzanie bloku obszaru roboczego w portalu | `Microsoft.OperationalInsights/workspaces/read` ||
| Dzienniki kwerend przy użyciu dowolnego interfejsu | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Dostęp do wszystkich typów dzienników przy użyciu kwerend | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Uzyskiwanie dostępu do określonej tabeli dziennika | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Odczytywanie kluczy obszaru roboczego w celu umożliwienia wysyłania dzienników do tego obszaru roboczego | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Zarządzanie dostępem przy użyciu uprawnień platformy Azure

Aby udzielić dostępu do obszaru roboczego usługi Log Analytics przy użyciu uprawnień platformy Azure, wykonaj kroki opisane w części [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../../role-based-access-control/role-assignments-portal.md). Na przykład role niestandardowe można znaleźć [w przykładowych rolach niestandardowych](#custom-role-examples)

Platforma Azure ma dwie wbudowane role użytkowników dla obszarów roboczych usługi Log Analytics:

* Czytelnik usługi Log Analytics
* Współautor usługi Log Analytics

Członkowie roli *Czytelnik usługi Log Analytics* mogą wykonywać następujące czynności:

* Wyświetlanie i wyszukiwanie wszystkich monitorowanych danych
* Wyświetlanie ustawień monitorowania, w tym konfiguracji diagnostyki platformy Azure, dla wszystkich zasobów platformy Azure

Rola czytnika analizy dzienników obejmuje następujące akcje platformy Azure:

| Typ    | Uprawnienie | Opis |
| ------- | ---------- | ----------- |
| Akcja | `*/read`   | Możliwość wyświetlania wszystkich zasobów platformy Azure i konfiguracji zasobów. Obejmuje wyświetlanie następujących elementów: <br> Stan rozszerzenia maszyny wirtualnej <br> Konfiguracja diagnostyki platformy Azure dla zasobów <br> Wszystkie właściwości i ustawienia wszystkich zasobów. <br> W przypadku obszarów roboczych umożliwia pełne nieograniczone uprawnienia do odczytu ustawień obszaru roboczego i wykonywania kwerendy na dane. Zobacz więcej szczegółowych opcji powyżej. |
| Akcja | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Przestarzałe, nie ma potrzeby przypisywania ich do użytkowników. |
| Akcja | `Microsoft.OperationalInsights/workspaces/search/action` | Przestarzałe, nie ma potrzeby przypisywania ich do użytkowników. |
| Akcja | `Microsoft.Support/*` | Możliwość otwierania zgłoszeń do pomocy technicznej |
|Inne | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Zapobiega odczytowi klucza obszaru roboczego wymaganego do korzystania z interfejsu API zbierania danych i instalowania agentów. Uniemożliwia to użytkownikowi dodawanie nowych zasobów do obszaru roboczego |

Członkowie roli *Współautor usługi Log Analytics* mogą wykonywać następujące czynności:

* Zawiera wszystkie uprawnienia *roli czytnika usługi Log Analytics Reader,* umożliwiając użytkownikowi odczytanie wszystkich danych monitorowania
* Tworzenie i konfigurowanie kont automatyzacji
* Dodawanie i usuwanie rozwiązań do zarządzania

    > [!NOTE]
    > Aby pomyślnie wykonać dwie ostatnie akcje, to uprawnienie musi zostać przyznane na poziomie grupy zasobów lub subskrypcji.

* Odczytanie kluczy konta magazynu
* Konfigurowanie kolekcji dzienników z usługi Azure Storage
* Edytowanie ustawień monitorowania dla zasobów platformy Azure, w tym:
  * Dodawanie rozszerzenia do maszyn wirtualnych
  * Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure

> [!NOTE]
> Możliwości dodania rozszerzenia do maszyny wirtualnej możesz użyć, aby zyskać pełną kontrolę nad maszyną wirtualną.

Rola współautora usługi Log Analytics obejmuje następujące akcje platformy Azure:

| Uprawnienie | Opis |
| ---------- | ----------- |
| `*/read`     | Możliwość wyświetlania wszystkich zasobów i konfiguracji zasobów. Obejmuje wyświetlanie następujących elementów: <br> Stan rozszerzenia maszyny wirtualnej <br> Konfiguracja diagnostyki platformy Azure dla zasobów <br> Wszystkie właściwości i ustawienia wszystkich zasobów. <br> W przypadku obszarów roboczych umożliwia pełne nieograniczone uprawnienia do odczytu ustawienia obszaru roboczego i wykonywania kwerendy na danych. Zobacz więcej szczegółowych opcji powyżej. |
| `Microsoft.Automation/automationAccounts/*` | Możliwość tworzenia i konfigurowania kont usługi Azure Automation, w tym dodawania i edytowania elementów runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Dodawanie, aktualizowanie i usuwanie rozszerzeń maszyn wirtualnych, w tym rozszerzenia Microsoft Monitoring Agent oraz rozszerzenia OMS Agent for Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Wyświetlanie klucza konta magazynu. Wymagane do skonfigurowania usługi Log Analytics w celu odczytu dzienników z kont magazynów platformy Azure |
| `Microsoft.Insights/alertRules/*` | Dodawanie, aktualizowanie i usuwanie reguł alertu |
| `Microsoft.Insights/diagnosticSettings/*` | Dodawanie, aktualizowanie i usuwanie ustawień diagnostycznych dla zasobów platformy Azure |
| `Microsoft.OperationalInsights/*` | Dodawanie, aktualizowanie i usuwanie konfiguracji obszarów roboczych usługi Log Analytics. Aby edytować zaawansowane ustawienia `Microsoft.OperationalInsights/workspaces/write`obszaru roboczego, użytkownik potrzebuje . |
| `Microsoft.OperationsManagement/*` | Dodawanie i usuwanie rozwiązań do zarządzania |
| `Microsoft.Resources/deployments/*` | Tworzenie i usuwanie wdrożeń. Wymagane w celu dodawania i usuwania rozwiązań, obszarów roboczych oraz kont usługi Automation |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Tworzenie i usuwanie wdrożeń. Wymagane w celu dodawania i usuwania rozwiązań, obszarów roboczych oraz kont usługi Automation |

Aby dodawać użytkowników do roli użytkownika i usuwać ich z niej, wymagane jest uprawnienie `Microsoft.Authorization/*/Delete` i `Microsoft.Authorization/*/Write`.

Za pomocą następujących ról możesz udzielić użytkownikom dostępu w różnych zakresach:

* Subskrypcja — dostęp do wszystkich obszarów roboczych w subskrypcji
* Grupa zasobów — dostęp do wszystkich obszarów roboczych w grupie zasobów
* Zasób — dostęp tylko do określonego obszaru roboczego

Zaleca się wykonywanie przydziałów na poziomie zasobu (obszaru roboczego), aby zapewnić dokładną kontrolę dostępu. Za pomocą [ról niestandardowych](../../role-based-access-control/custom-roles.md) możesz utworzyć role z określonymi, wymaganymi uprawnieniami.

### <a name="resource-permissions"></a>Uprawnienia zasobów

Gdy użytkownicy kwerendy dzienniki z obszaru roboczego przy użyciu dostępu kontekstu zasobów, będą mieli następujące uprawnienia do zasobu:

| Uprawnienie | Opis |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Przykłady:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Możliwość wyświetlania wszystkich danych dziennika dla zasobu.  |
| `Microsoft.Insights/diagnosticSettings/write` | Możliwość konfigurowania ustawienia diagnostyki w celu umożliwienia konfigurowania dzienników dla tego zasobu. |

`/read`uprawnienie jest zwykle przyznawane z roli, która _\*_ zawiera _ \*/read lub_ uprawnienia, takie jak wbudowane role [czytnika](../../role-based-access-control/built-in-roles.md#reader) i [współautora.](../../role-based-access-control/built-in-roles.md#contributor) Role niestandardowe, które zawierają określone akcje lub dedykowane wbudowane role mogą nie zawierać tego uprawnienia.

Zobacz [Definiowanie kontroli dostępu dla tabeli](#table-level-rbac) poniżej, jeśli chcesz utworzyć inną kontrolę dostępu dla różnych tabel.

## <a name="custom-role-examples"></a>Przykłady ról niestandardowych

1. Aby udzielić użytkownikowi dostępu do danych dziennika z ich zasobów, wykonaj następujące czynności:

    * Konfigurowanie trybu kontroli dostępu do obszaru roboczego w celu **używania uprawnień do obszaru roboczego lub zasobu**

    * Udziel `*/read` `Microsoft.Insights/logs/*/read` użytkownikom lub uprawnień do ich zasobów. Jeśli są już przypisane roli [czytnika analizy dziennika](../../role-based-access-control/built-in-roles.md#reader) w obszarze roboczym, jest to wystarczające.

2. Aby udzielić użytkownikowi dostępu do danych dziennika z ich zasobów i skonfigurować swoje zasoby do wysyłania dzienników do obszaru roboczego, wykonaj następujące czynności:

    * Konfigurowanie trybu kontroli dostępu do obszaru roboczego w celu **używania uprawnień do obszaru roboczego lub zasobu**

    * Udziel użytkownikom następujących uprawnień do `Microsoft.OperationalInsights/workspaces/read` `Microsoft.OperationalInsights/workspaces/sharedKeys/action`obszaru roboczego: i . Dzięki tym uprawnieniom użytkownicy nie mogą wykonywać żadnych zapytań na poziomie obszaru roboczego. Mogą one tylko wyliczyć obszar roboczy i używać go jako miejsce docelowe dla ustawień diagnostycznych lub konfiguracji agenta.

    * Udziel użytkownikom następujących uprawnień `Microsoft.Insights/logs/*/read` do `Microsoft.Insights/diagnosticSettings/write`swoich zasobów: i . Jeśli są już przypisane [log Analytics roli współautora,](../../role-based-access-control/built-in-roles.md#contributor) przypisane `*/read` roli czytelnika lub przyznane uprawnienia do tego zasobu, to jest wystarczające.

3. Aby udzielić użytkownikowi dostępu do danych dziennika z ich zasobów bez możliwości odczytywania zdarzeń zabezpieczeń i wysyłania danych, wykonaj następujące czynności:

    * Konfigurowanie trybu kontroli dostępu do obszaru roboczego w celu **używania uprawnień do obszaru roboczego lub zasobu**

    * Udziel użytkownikom następujących uprawnień `Microsoft.Insights/logs/*/read`do swoich zasobów: .

    * Dodaj następującą nonaction, aby uniemożliwić użytkownikom `Microsoft.Insights/logs/SecurityEvent/read`odczytanie typu SecurityEvent: . NonAction ma taką samą rolę niestandardową jak akcja,`Microsoft.Insights/logs/*/read`która zapewnia uprawnienie do odczytu ( ). Jeśli użytkownik nieodłączne akcji odczytu z innej roli, która jest przypisana do tego zasobu lub do subskrypcji lub grupy zasobów, będą mogli odczytać wszystkie typy dzienników. Jest to również prawdą, jeśli dziedziczą `*/read`, które istnieją na przykład z reader lub współautora roli.

4. Aby udzielić użytkownikowi dostępu do danych dziennika z ich zasobów i odczytać wszystkie logowania usługi Azure AD i odczytać dane dziennika rozwiązania usługi Update Management z obszaru roboczego, wykonaj następujące czynności:

    * Konfigurowanie trybu kontroli dostępu do obszaru roboczego w celu **używania uprawnień do obszaru roboczego lub zasobu**

    * Udziel użytkownikom następujących uprawnień do obszaru roboczego: 

        * `Microsoft.OperationalInsights/workspaces/read`– wymagane, aby użycie można wyliczyć obszar roboczy i otworzyć blok obszaru roboczego w witrynie Azure portal
        * `Microsoft.OperationalInsights/workspaces/query/read`– wymagane dla każdego użytkownika, który może wykonywać zapytania
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read`– aby móc odczytywać dzienniki logowania usługi Azure AD
        * `Microsoft.OperationalInsights/workspaces/query/Update/read`– aby móc czytać dzienniki rozwiązań do zarządzania aktualizacjami
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read`– aby móc czytać dzienniki rozwiązań do zarządzania aktualizacjami
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read`– aby móc czytać dzienniki zarządzania aktualizacjami
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read`– wymagane, aby móc korzystać z rozwiązania zarządzania aktualizacjami
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read`– wymagane, aby móc korzystać z rozwiązania zarządzania aktualizacjami

    * Udziel użytkownikom następujących uprawnień `*/read`do swoich zasobów: , `Microsoft.Insights/logs/*/read`przypisanych do roli Czytelnika lub . 

## <a name="table-level-rbac"></a>Poziom tabeli RBAC

**Poziom tabeli RBAC** umożliwia definiowanie bardziej szczegółowe kontroli danych w obszarze roboczym usługi Log Analytics oprócz innych uprawnień. Ten formant umożliwia definiowanie określonych typów danych, które są dostępne tylko dla określonego zestawu użytkowników.

Zaimplementuj kontrolę dostępu do tabeli za pomocą [ról niestandardowych platformy Azure,](../../role-based-access-control/custom-roles.md) aby udzielić dostępu do określonych [tabel](../log-query/logs-structure.md) w obszarze roboczym. Te role są stosowane do obszarów roboczych z [trybami kontroli dostępu](design-logs-deployment.md#access-control-mode) w kontekście obszaru roboczego lub kontekstu zasobu, niezależnie od trybu [dostępu](design-logs-deployment.md#access-mode)użytkownika.

Utwórz [rolę niestandardową](../../role-based-access-control/custom-roles.md) z następującymi akcjami, aby zdefiniować dostęp do kontroli dostępu do tabeli.

* Aby udzielić dostępu do tabeli, dołącz ją do sekcji **Akcje** definicji roli. Aby odjąć dostęp od **dozwolonych akcji,** należy uwzględnić go w sekcji **NotActions.**
* Aby określić wszystkie tabele, użyj opcji Microsoft.OperationalInsights/workspaces/query/* .

Na przykład, aby utworzyć rolę z dostępem do _pulsu_ i _AzureActivity_ tabel, należy utworzyć rolę niestandardową przy użyciu następujących akcji:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Aby utworzyć rolę z dostępem tylko do tabeli _SecurityBaseline,_ utwórz rolę niestandardową przy użyciu następujących akcji:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```

### <a name="custom-logs"></a>Niestandardowe dzienniki

 Dzienniki niestandardowe są tworzone ze źródeł danych, takich jak dzienniki niestandardowe i interfejs API modułu zbierającego dane HTTP. Najprostszym sposobem zidentyfikowania typu dziennika jest sprawdzenie tabel wymienionych w obszarze [Dzienniki niestandardowe w schemacie dziennika](../log-query/get-started-portal.md#understand-the-schema).

 Nie można udzielić dostępu do poszczególnych dzienników niestandardowych, ale można udzielić dostępu do wszystkich dzienników niestandardowych. Aby utworzyć rolę z dostępem do wszystkich dzienników niestandardowych, utwórz rolę niestandardową przy użyciu następujących akcji:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
Alternatywne podejście do zarządzania dostępem do dzienników niestandardowych jest przypisanie ich do zasobu platformy Azure i zarządzanie dostępem przy użyciu paradygmatu kontekstu zasobów. Aby użyć tej metody, należy uwzględnić identyfikator zasobu, określając go w nagłówku [x-ms-AzureResourceId,](data-collector-api.md#request-headers) gdy dane są pochłoane do usługi Log Analytics za pośrednictwem [interfejsu API modułu zbierającego dane HTTP](data-collector-api.md). Identyfikator zasobu musi być prawidłowy i mieć do niego zastosowane reguły dostępu. Po spożyciu dzienników są one dostępne dla osób z dostępem do odczytu do zasobu, jak wyjaśniono w tym miejscu.

Czasami dzienniki niestandardowe pochodzą ze źródeł, które nie są bezpośrednio skojarzone z określonym zasobem. W takim przypadku należy utworzyć grupę zasobów tylko do zarządzania dostępem do tych dzienników. Grupa zasobów nie ponosi żadnych kosztów, ale daje prawidłowy identyfikator zasobu, aby kontrolować dostęp do dzienników niestandardowych. Na przykład, jeśli określona zapora wysyła dzienniki niestandardowe, utwórz grupę zasobów o nazwie "MyFireWallLogs" i upewnij się, że żądania interfejsu API zawierają identyfikator zasobu "MyFireWallLogs". Rekordy dziennika zapory są następnie dostępne tylko dla użytkowników, którym przyznano dostęp do MyFireWallLogs lub osób z pełnym dostępem do obszaru roboczego.          

### <a name="considerations"></a>Zagadnienia do rozważenia

* Jeśli użytkownikowi zostanie przyznane globalne uprawnienie do odczytu ze standardowymi rolami programu Reader lub Współautor, które zawierają akcję _ \*/read,_ zastąpi kontrolę dostępu dla tabeli i zapewni im dostęp do wszystkich danych dziennika.
* Jeśli użytkownikowi udzielono dostępu dla tabeli, ale nie ma innych uprawnień, będą mogli uzyskać dostęp do danych dziennika z interfejsu API, ale nie z witryny Azure portal. Aby zapewnić dostęp z witryny Azure portal, użyj log analytics reader jako jego roli podstawowej.
* Administratorzy subskrypcji będą mieli dostęp do wszystkich typów danych, niezależnie od innych ustawień uprawnień.
* Właściciele obszarów roboczych są traktowani jak każdy inny użytkownik do kontroli dostępu dla każdej tabeli.
* Zalecamy przypisywanie ról grupom zabezpieczeń zamiast poszczególnym użytkownikom w celu zmniejszenia liczby przypisań. Pomoże to również w skonfigurowaniu i zweryfikowaniu dostępu przy użyciu istniejących narzędzi do zarządzania grupami.

## <a name="next-steps"></a>Następne kroki

* Zobacz [Omówienie agenta usługi Log Analytics,](../../azure-monitor/platform/log-analytics-agent.md) aby zbierać dane z komputerów w centrum danych lub w innym środowisku chmury.

* Zobacz [Zbieranie danych o maszynach wirtualnych platformy Azure](../../azure-monitor/learn/quick-collect-azurevm.md) w celu skonfigurowania zbierania danych z maszyn wirtualnych platformy Azure.
