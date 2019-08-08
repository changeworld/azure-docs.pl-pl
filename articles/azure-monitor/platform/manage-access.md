---
title: Zarządzanie obszarami roboczymi Log Analytics w Azure Monitor | Microsoft Docs
description: Obszarami roboczymi Log Analytics można zarządzać w Azure Monitor przy użyciu różnych zadań administracyjnych dotyczących użytkowników, kont, obszarów roboczych i kont platformy Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: magoedte
ms.openlocfilehash: 05b022be3bd460809de77945710ed0bdcd275648
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839313"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Zarządzanie danymi dziennika i obszarami roboczymi w Azure Monitor

Azure Monitor przechowuje dane [dziennika](data-platform-logs.md) w log Analytics obszarze roboczym, który zasadniczo jest kontenerem zawierającym informacje o danych i konfiguracji. Aby zarządzać dostępem do danych dziennika, należy wykonać różne zadania administracyjne związane z obszarem roboczym.

W tym artykule opisano sposób zarządzania dostępem do dzienników i zarządzania obszarami roboczymi, które je zawierają, w tym:

* Jak udzielić dostępu użytkownikom, którzy potrzebują dostępu do danych dzienników z określonych zasobów przy użyciu kontroli dostępu opartej na rolach (RBAC) na platformie Azure.

* Jak udzielić dostępu do obszaru roboczego przy użyciu uprawnień obszaru roboczego.

* Jak udzielić dostępu użytkownikom, którzy potrzebują dostępu do danych dziennika w określonej tabeli w obszarze roboczym przy użyciu funkcji RBAC platformy Azure.

## <a name="define-access-control-mode-in-azure-portal"></a>Zdefiniuj tryb kontroli dostępu w Azure Portal

Bieżący tryb kontroli dostępu obszaru roboczego można wyświetlić na stronie **Przegląd** obszaru roboczego w menu **log Analytics obszaru roboczego** . 

![Wyświetlanie trybu kontroli dostępu do obszaru roboczego](media/manage-access/view-access-control-mode.png)

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
1. W Azure Portal wybierz pozycję Log Analytics obszary robocze > obszarze roboczym.  

To ustawienie można zmienić na stronie **Właściwości** obszaru roboczego. Zmiana ustawienia zostanie wyłączona, jeśli nie masz uprawnień do konfigurowania obszaru roboczego.

![Zmień tryb dostępu obszaru roboczego](media/manage-access/change-access-control-mode.png)

## <a name="define-access-control-mode-using-powershell"></a>Definiowanie trybu kontroli dostępu przy użyciu programu PowerShell

Użyj następującego polecenia, aby przeanalizować tryb kontroli dostępu dla wszystkich obszarów roboczych w subskrypcji:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

Dane wyjściowe powinny wyglądać podobnie do następujących:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Wartość `False` oznacza, że obszar roboczy jest skonfigurowany z trybem dostępu do kontekstu obszaru roboczego.  Wartość `True` oznacza, że obszar roboczy jest skonfigurowany z trybem dostępu do kontekstu zasobów. 

>[!NOTE]
>Jeśli obszar roboczy jest zwracany bez wartości logicznej i jest pusty, jest również zgodny z wynikami `False` wartości.
>

Użyj poniższego skryptu, aby ustawić tryb kontroli dostępu dla określonego obszaru roboczego na uprawnienie do kontekstu zasobu:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Poniższy skrypt służy do ustawiania trybu kontroli dostępu dla wszystkich obszarów roboczych w ramach subskrypcji do uprawnienia kontekstu zasobu:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

## <a name="define-access-mode-using-resource-manager-template"></a>Definiowanie trybu dostępu przy użyciu szablonu Menedżer zasobów

Aby skonfigurować tryb dostępu w szablonie Azure Resource Manager, Ustaw flagę funkcji **enableLogAccessUsingOnlyResourcePermissions** w obszarze roboczym na jedną z następujących wartości.

* **wartość false**: Ustaw obszar roboczy na uprawnienia kontekstu obszaru roboczego. Jest to ustawienie domyślne, jeśli flaga nie jest ustawiona.
* **wartość true**: Ustaw obszar roboczy na uprawnienia kontekstu zasobów.

## <a name="manage-accounts-and-users"></a>Zarządzanie kontami i użytkownikami

Uprawnienia zastosowane do obszaru roboczego dla określonego użytkownika są definiowane przez ich [tryb dostępu](design-logs-deployment.md#access-mode) i [Tryb kontroli dostępu](design-logs-deployment.md#access-control-mode) obszaru roboczego. Za pomocą **kontekstu obszaru roboczego**można wyświetlić wszystkie dzienniki w obszarze roboczym, do którego masz uprawnienia, ponieważ zapytania w tym trybie są ograniczone do wszystkich danych we wszystkich tabelach w obszarze roboczym. Przy użyciu **kontekstu zasobów**można wyświetlać dane dzienników w obszarze roboczym dla określonego zasobu, grupy zasobów lub subskrypcji podczas wyszukiwania bezpośrednio z zasobu w Azure Portal, do którego masz dostęp. Zapytania w tym trybie są ograniczone do danych skojarzonych z tym zasobem.

### <a name="workspace-permissions"></a>Uprawnienia obszaru roboczego

Każdy obszar roboczy może mieć wiele kont skojarzonych z nim, a każde konto może uzyskiwać dostęp do wielu obszarów roboczych. Dostęp jest zarządzany przy użyciu [dostępu opartego na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md).

Następujące działania również wymagają uprawnień platformy Azure:

|Akcja |Wymagane uprawnienia platformy Azure |Uwagi |
|-------|-------------------------|------|
| Dodawanie i usuwanie rozwiązań do monitorowania | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Te uprawnienia należy nadać na poziomie grupy zasobów lub subskrypcji. |
| Zmienianie warstwy cenowej | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Wyświetlanie danych w kafelkach rozwiązań *Backup* i *Site Recovery* | Administrator/współadministrator | Uzyskuje dostęp do zasobów wdrożonych przy użyciu klasycznego modelu wdrażania |
| Tworzenie obszaru roboczego w witrynie Azure Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Wyświetlanie podstawowych właściwości obszaru roboczego i wprowadzanie bloku obszaru roboczego w portalu | `Microsoft.OperationalInsights/workspaces/read` ||
| Wykonywanie zapytań dzienników przy użyciu dowolnego interfejsu | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Dostęp do wszystkich typów dzienników przy użyciu zapytań | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Dostęp do określonej tabeli dziennika | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Odczytaj klucze obszaru roboczego, aby umożliwić wysyłanie dzienników do tego obszaru roboczego | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Zarządzanie dostępem przy użyciu uprawnień platformy Azure

Aby udzielić dostępu do obszaru roboczego usługi Log Analytics przy użyciu uprawnień platformy Azure, wykonaj kroki opisane w części [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../../role-based-access-control/role-assignments-portal.md).

Platforma Azure ma dwie wbudowane role użytkowników dla Log Analytics obszarów roboczych:

* Czytelnik usługi Log Analytics
* Współautor usługi Log Analytics

Członkowie roli *Czytelnik usługi Log Analytics* mogą wykonywać następujące czynności:

* Wyświetlanie i wyszukiwanie wszystkich monitorowanych danych
* Wyświetlanie ustawień monitorowania, w tym konfiguracji diagnostyki platformy Azure, dla wszystkich zasobów platformy Azure

Rola Czytelnik usługi Log Analytics obejmuje następujące działania dla platformy Azure:

| Typ    | Uprawnienie | Opis |
| ------- | ---------- | ----------- |
| Akcja | `*/read`   | Możliwość wyświetlania wszystkich zasobów platformy Azure i konfiguracji zasobów. Obejmuje wyświetlanie następujących elementów: <br> Stan rozszerzenia maszyny wirtualnej <br> Konfiguracja diagnostyki platformy Azure dla zasobów <br> Wszystkie właściwości i ustawienia wszystkich zasobów. <br> W przypadku obszarów roboczych umożliwia pełne nieograniczone uprawnienia do odczytywania ustawień obszaru roboczego i wykonywania zapytań dotyczących danych. Zobacz bardziej szczegółowe opcje powyżej. |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Przestarzałe, nie trzeba ich przypisywać do użytkowników. |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | Przestarzałe, nie trzeba ich przypisywać do użytkowników. |
| Action | `Microsoft.Support/*` | Możliwość otwierania zgłoszeń do pomocy technicznej |
|Inne | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Zapobiega odczytywaniu obszaru roboczego klucz jest wymagany do użycia interfejsu API zbierania danych oraz instalowania agentów. To uniemożliwia dodanie nowych zasobów do obszaru roboczego |

Członkowie roli *Współautor usługi Log Analytics* mogą wykonywać następujące czynności:

* Odczytywać wszystkie dane monitorowania, jak czytelnik usługi Log Analytics
* Tworzenie i konfigurowanie kont usługi Automation
* Dodawanie i usuwanie rozwiązań do zarządzania

    > [!NOTE]
    > W celu pomyślnego przeprowadzenia ostatniego dwie akcje, to uprawnienie musi mieć uprawnienia na poziomie grupy lub subskrypcji zasobów.  

* Odczytywanie kluczy kont magazynu
* Konfigurowanie kolekcji dzienników z usługi Azure Storage  
* Edytowanie ustawień monitorowania dla zasobów platformy Azure, w tym:
  * Dodawanie rozszerzenia do maszyn wirtualnych
  * Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure

> [!NOTE]
> Możliwości dodania rozszerzenia do maszyny wirtualnej możesz użyć, aby zyskać pełną kontrolę nad maszyną wirtualną.

Rola Współautor usługi Log Analytics obejmuje następujące działania dla platformy Azure:

| Uprawnienie | Opis |
| ---------- | ----------- |
| `*/read`     | Możliwość wyświetlania wszystkich zasobów i konfiguracji zasobów. Obejmuje wyświetlanie następujących elementów: <br> Stan rozszerzenia maszyny wirtualnej <br> Konfiguracja diagnostyki platformy Azure dla zasobów <br> Wszystkie właściwości i ustawienia wszystkich zasobów. <br> W przypadku obszarów roboczych umożliwia pełne nieograniczone uprawnienia do odczytu ustawienia obszaru roboczego i wykonywania zapytań dotyczących danych. Zobacz bardziej szczegółowe opcje powyżej. |
| `Microsoft.Automation/automationAccounts/*` | Możliwość tworzenia i konfigurowania kont usługi Azure Automation, w tym dodawania i edytowania elementów runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Dodawanie, aktualizowanie i usuwanie rozszerzeń maszyn wirtualnych, w tym rozszerzenia Microsoft Monitoring Agent oraz rozszerzenia OMS Agent for Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Wyświetlanie klucza konta magazynu. Wymagane do skonfigurowania usługi Log Analytics w celu odczytu dzienników z kont magazynów platformy Azure |
| `Microsoft.Insights/alertRules/*` | Dodawanie, aktualizowanie i usuwanie reguł alertu |
| `Microsoft.Insights/diagnosticSettings/*` | Dodawanie, aktualizowanie i usuwanie ustawień diagnostycznych dla zasobów platformy Azure |
| `Microsoft.OperationalInsights/*` | Dodawanie, aktualizowanie i usuwanie konfiguracji dla obszarów roboczych usługi Log Analytics |
| `Microsoft.OperationsManagement/*` | Dodawanie i usuwanie rozwiązań do zarządzania |
| `Microsoft.Resources/deployments/*` | Tworzenie i usuwanie wdrożeń. Wymagane w celu dodawania i usuwania rozwiązań, obszarów roboczych oraz kont usługi Automation |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Tworzenie i usuwanie wdrożeń. Wymagane w celu dodawania i usuwania rozwiązań, obszarów roboczych oraz kont usługi Automation |

Aby dodawać użytkowników do roli użytkownika i usuwać ich z niej, wymagane jest uprawnienie `Microsoft.Authorization/*/Delete` i `Microsoft.Authorization/*/Write`.

Za pomocą następujących ról możesz udzielić użytkownikom dostępu w różnych zakresach:

* Subskrypcja — dostęp do wszystkich obszarów roboczych w subskrypcji
* Grupa zasobów — dostęp do wszystkich obszarów roboczych w grupie zasobów
* Zasób — dostęp tylko do określonego obszaru roboczego

W celu zapewnienia dokładnej kontroli dostępu należy wykonać przydziały na poziomie zasobów (w obszarze roboczym).  Za pomocą [ról niestandardowych](../../role-based-access-control/custom-roles.md) możesz utworzyć role z określonymi, wymaganymi uprawnieniami.

### <a name="resource-permissions"></a>Uprawnienia zasobów

Gdy użytkownicy wykonują zapytania dotyczące dzienników z obszaru roboczego przy użyciu dostępu do kontekstu zasobów, będą mieć następujące uprawnienia do zasobu:

| Uprawnienie | Opis |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Przykłady:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Możliwość wyświetlania wszystkich danych dziennika dla zasobu.  |
| `Microsoft.Insights/diagnosticSettings/write ` | Możliwość skonfigurowania ustawień diagnostycznych w celu zezwolenia na Konfigurowanie dzienników dla tego zasobu. |

`/read`uprawnienie _\*_ jest zazwyczaj udzielane z roli [](../../role-based-access-control/built-in-roles.md#contributor) [](../../role-based-access-control/built-in-roles.md#reader)  _\*obejmującej/Read lub_ uprawnienia, takie jak wbudowane czytniki i role współautor. Należy zauważyć, że role niestandardowe zawierające określone akcje lub dedykowane role wbudowane mogą nie uwzględniać tego uprawnienia.

Zobacz [Definiowanie kontroli dostępu dla tabel](#table-level-rbac) poniżej, jeśli chcesz utworzyć inną kontrolę dostępu dla różnych tabel.

## <a name="table-level-rbac"></a>Kontrola RBAC na poziomie tabeli

Kontrolka **RBAC na poziomie tabeli** umożliwia zdefiniowanie bardziej szczegółowej kontroli danych w obszarze roboczym log Analytics oprócz innych uprawnień. Ta kontrolka umożliwia definiowanie określonych typów danych, które są dostępne tylko dla określonego zestawu użytkowników.

W celu zaimplementowania kontroli dostępu do tabel przy użyciu [ról niestandardowych platformy Azure](../../role-based-access-control/custom-roles.md) można udzielić lub odmówić dostępu do określonych [tabel](../log-query/logs-structure.md) w obszarze roboczym. Te role są stosowane do obszarów roboczych z [trybami kontroli dostępu](design-logs-deployment.md#access-control-mode) do kontekstu obszaru roboczego lub zasobu, niezależnie od [trybu dostępu](design-logs-deployment.md#access-mode)użytkownika.

Utwórz niestandardową [rolę](../../role-based-access-control/custom-roles.md) z poniższymi akcjami, aby zdefiniować dostęp do kontroli dostępu do tabeli.

* Aby udzielić dostępu do tabeli, Uwzględnij ją w sekcji **Akcje** w definicji roli.
* Aby odmówić dostępu do tabeli, Uwzględnij ją w sekcji nogrids w definicji roli.
* Aby określić wszystkie tabele, użyj znaku *.

Na przykład aby utworzyć rolę z dostępem do tabel pulsu i _Azure_ , Utwórz rolę niestandardową przy użyciu następujących akcji:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Aby utworzyć rolę mającą dostęp tylko do _SecurityBaseline_ i braku innych tabel, Utwórz rolę niestandardową przy użyciu następujących akcji:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Dzienniki niestandardowe

 Dzienniki niestandardowe są tworzone na podstawie źródeł danych, takich jak dzienniki niestandardowe i interfejs API modułu zbierającego dane HTTP. Najprostszym sposobem identyfikacji typu dziennika jest sprawdzenie tabel wymienionych w obszarze [dzienniki niestandardowe w schemacie dziennika](../log-query/get-started-portal.md#understand-the-schema).

 Obecnie nie można udzielić lub odmówić dostępu do poszczególnych dzienników niestandardowych, ale można udzielić lub odmówić dostępu do wszystkich dzienników niestandardowych. Aby utworzyć rolę mającą dostęp do wszystkich dzienników niestandardowych, Utwórz rolę niestandardową przy użyciu następujących akcji:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Zagadnienia do rozważenia

* Jeśli użytkownik otrzymuje globalne uprawnienie do odczytu ze standardowym czytnikiem lub rolą współautor, które zawiera  _\*akcję/Read_ , zastąpi kontrolę dostępu do tabeli i przekaże im dostęp do wszystkich danych dziennika.
* Jeśli użytkownik uzyska dostęp do tabeli, ale nie ma żadnych innych uprawnień, może uzyskać dostęp do danych dziennika z interfejsu API, ale nie z Azure Portal. Aby zapewnić dostęp z Azure Portal, Użyj czytnika Log Analytics jako roli podstawowej.
* Administratorzy subskrypcji będą mieć dostęp do wszystkich typów danych, niezależnie od innych ustawień uprawnień.
* Właściciele obszarów roboczych są traktowani jak każdy inny użytkownik do kontroli dostępu do tabeli.
* Aby zmniejszyć liczbę przypisań, należy przypisać role do grup zabezpieczeń zamiast poszczególnych użytkowników. Ułatwi to również korzystanie z istniejących narzędzi do zarządzania grupami w celu konfigurowania i weryfikowania dostępu.

## <a name="next-steps"></a>Następne kroki

* Zobacz [omówienie agenta usługi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) do zbierania danych z komputerów w centrum danych lub w innym środowisku chmury.

* Aby skonfigurować zbieranie danych z maszyn wirtualnych platformy Azure, zobacz [zbieranie danych o maszynach wirtualnych platformy Azure](../../azure-monitor/learn/quick-collect-azurevm.md) .