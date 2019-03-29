---
title: Zarządzanie obszarami roboczymi usługi Log Analytics, w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Możesz zarządzać obszarów roboczych usługi Log Analytics w usłudze Azure Monitor przy użyciu różnych zadań administracyjnych na użytkowników, kont, obszarów roboczych i kont platformy Azure.
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
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: fd47b5de2226d88b6295cb28713db2a5d251e768
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577250"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Zarządzanie danymi dziennika i obszarami roboczymi w usłudze Azure Monitor
Magazyny usługi Azure Monitor możesz rejestrować dane w obszarze roboczym usługi Log Analytics, która jest kontener, który zawiera dane i informacje konfiguracyjne. W celu zarządzania dostępem do danych logowania, należy wykonać różne zadania administracyjne dotyczące obszarów roboczych. Ty i inni członkowie organizacji możecie używać wielu obszarów roboczych, aby zarządzać różnymi zestawami danych zebranymi z całej infrastruktury IT lub jej części.

W tym artykule wyjaśniono, jak zarządzać dostępem do dzienników i Administruj obszarami roboczymi, zawierające je. 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
Aby utworzyć obszar roboczy usługi Log Analytics, należy:

1. Mieć subskrypcję platformy Azure.
2. Wybrać nazwę obszaru roboczego.
3. Skojarzyć obszar roboczy z jednym z Twojej subskrypcji i grup zasobów.
4. Wybrać lokalizację geograficzną.

Zapoznaj się z następującymi artykułami, aby uzyskać szczegółowe informacje na temat tworzenia obszaru roboczego:

- [Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal](../learn/quick-create-workspace.md)
- [Utwórz obszar roboczy usługi Log Analytics przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0](../learn/quick-create-workspace-cli.md)
- [Utwórz obszar roboczy usługi Log Analytics przy użyciu programu Azure PowerShell](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>Określanie wymaganej liczby obszarów roboczych
Obszar roboczy usługi Log Analytics jest zasobem platformy Azure i kontenerów, w którym dane są zbierane, agregowane, analizowane i znajdujące się w usłudze Azure Monitor. Może mieć wiele obszarów roboczych na subskrypcję platformy Azure i masz dostęp do więcej niż jednego obszaru roboczego, umożliwia łatwe wyszukiwanie między nimi. W tej sekcji opisano sytuacje, w których pomocne może być utworzenie więcej niż jednego obszaru roboczego.

Obszar roboczy usługi Log Analytics oferuje:

* Lokalizacja geograficzna do przechowywania danych.
* Izolacja danych, aby zdefiniować prawa dostępu różnych użytkowników w trybie skoncentrowane na obszar roboczy. Nie są istotne podczas pracy w trybie skoncentrowane na zasób.
* Możliwość konfiguracji ustawień, takich jak [warstwy cenowej](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [przechowywania](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) i [danych są takie same](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).
* Opłaty związane z pozyskiwanie i przechowywanie danych są wykonywane na zasób obszaru roboczego.

Z punktu widzenia użycia firma Microsoft zaleca się utworzenie w kilku obszarów roboczych jak to możliwe. Ułatwia administrowanie i zapytania łatwiej i szybciej. Jednak na podstawie poprzedniego właściwości, warto utworzyć wiele obszarów roboczych, jeśli:

* Są globalne firmy i należy rejestrować — dane przechowywane w określonych regionach ze względów danych suwerenności lub zgodności.
* Korzystasz z platformy Azure i chcesz uniknąć naliczania opłat za transfer danych wychodzących przez umieszczenie obszaru roboczego w tym samym regionie co zarządzane przez niego zasoby platformy Azure.
* Jesteś dostawcą usługi zarządzanej i chcesz, aby dane usługi Log Analytics dla klientów, którymi zarządzasz, były odizolowane od danych innych klientów.
* Zarządzasz wieloma klientami i chcesz, aby każdy klient / działu / firm grupy, własnych danych, ale nie danych od innych użytkowników, i nie ma żadnych potrzebę dysponowania skonsolidowany klientów między / działu / firm group widoku. ".

Jeśli zbierasz dane przy użyciu agentów systemu Windows, możesz [skonfigurować każdego agenta w celu raportowania do co najmniej jednego obszaru roboczego](../../azure-monitor/platform/agent-windows.md).

Jeśli używasz programu System Center Operations Manager, jedna grupa zarządzania programu Operations Manager może być połączona tylko z jednym obszarem roboczym. Możesz zainstalować program Microsoft Monitoring Agent na komputerach zarządzanych przez program Operations Manager oraz skonfigurować agenta w celu raportowania do programu Operations Manager i innego obszaru roboczego usługi Log Analytics.

Po zdefiniowaniu architektury obszaru roboczego powinien wymuszać tych zasad na zasoby platformy Azure za pomocą [usługi Azure Policy](../../governance/policy/overview.md). Może to wbudowana definicja, będą automatycznie stosowane do wszystkich zasobów platformy Azure. Na przykład można ustawić zasady, aby upewnić się, że wszystkie zasoby platformy Azure w danym regionie wysyłane ich dzienników diagnostycznych do określonego obszaru roboczego.

## <a name="view-workspace-details"></a>Wyświetl szczegóły obszaru roboczego
Podczas analizowania danych w obszarze roboczym usługi Log Analytics z **usługi Azure Monitor** menu w witrynie Azure portal, możesz utworzyć i zarządzać obszarami roboczymi w **obszarów roboczych usługi Log Analytics** menu.
 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i kliknij przycisk **wszystkich usług**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **usługi Log Analytics** obszarów roboczych.  

    ![Azure Portal](media/manage-access/azure-portal-01.png)  

3. Wybierz swój obszar roboczy z listy.

4. Strona obszaru roboczego zawiera szczegółowe informacje o obszarze roboczym, wprowadzenie, konfiguracji i linki do dodatkowych informacji.  

    ![Szczegóły obszaru roboczego](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Uprawnień obszaru roboczego i zakresu
Dane, których użytkownik ma dostęp, są określane przez wiele czynników, które są wymienione w poniższej tabeli. W poniższych sekcjach opisano każdy.

| współczynnik | Opis |
|:---|:---|
| [Tryb dostępu](#access-modes) | Metody, których użytkownik używa do uzyskuje dostęp do obszaru roboczego.  Definiuje zakres dostępnych danych i tryb kontroli dostępu, który jest stosowany. |
| [Tryb kontroli dostępu](#access-control-mode) | Ustawienie w obszarze roboczym, który definiuje, czy uprawnienia są stosowane na poziomie obszaru roboczego lub zasobu. |
| [Uprawnienia](#manage-accounts-and-users) | Uprawnienia zastosowane do osoby lub grupy użytkowników dla obszaru roboczego lub zasobu. Definiuje, jakie dane użytkownik będzie miał dostęp do. |
| [Na poziomie tabeli RBAC](#table-level-rbac) | Opcjonalne szczegółowe uprawnienia, które ma zastosowanie do wszystkich użytkowników niezależnie od ich trybu dostępu lub kontroli dostępu. Definiuje typy danych, które użytkownik może uzyskać dostęp. |



## <a name="access-modes"></a>Tryby dostępu
_Tryb dostępu_ odnosi się do sposobu użytkownik uzyskuje dostęp do obszaru roboczego usługi Log Analytics i definiuje zakres mogą uzyskiwać dostęp do danych. 

**Skoncentrowane na obszar roboczy**: W tym trybie użytkownik może wyświetlić wszystkie dzienniki w obszarze roboczym, które mają uprawnienia do. Zapytania w tym trybie są ograniczone do wszystkich danych we wszystkich tabelach w obszarze roboczym. Jest to tryb dostępu używany podczas dzienniki są dostępne przy użyciu obszaru roboczego jako zasięg, na przykład po wybraniu **dzienniki** z **usługi Azure Monitor** menu w witrynie Azure portal.

**Skoncentrowane na zasób**: Jeśli uzyskujesz dostęp do obszaru roboczego dla określonego zasobu, na przykład po wybraniu **dzienniki** z menu zasobów w witrynie Azure portal, można przeglądać dzienniki dla tylko tego zasobu, we wszystkich tabelach, które mają dostęp do. Zapytania w tym trybie ograniczone tylko do danych skojarzonych z tym zasobem. Ten tryb umożliwia także kontroli szczegółową dostępu opartej na rolach (RBAC). 

> [!NOTE]
> Dzienniki są dostępne dla zapytań skoncentrowane na zasób, tylko wtedy, gdy zostały prawidłowo skojarzone z wybranego zasobu. Obecnie następujących zasobów podlegają ograniczeniom: 
> - Komputery spoza platformy Azure
> - Service Fabric
> - Application Insights
> - Containers
> - Niestandardowe dzienniki utworzone przez interfejs API modułu zbierającego dane HTTP
>
> Możesz przetestować, jeśli dzienniki są poprawnie skojarzona z zasobami, uruchamiając zapytanie i sprawdzanie rekordy interesuje Cię. Jeżeli identyfikator odpowiedniego zasobu jest [_ResourceId](log-standard-properties.md#_resourceid) właściwości, a następnie data jest dostępna dla zapytań skoncentrowane na zasób.

### <a name="comparing-access-modes"></a>Porównanie trybów dostępu

W poniższej tabeli podsumowano tryby dostępu:

| | Skoncentrowane na obszar roboczy | Skoncentrowane na zasób |
|:---|:---|:---|
| Kogo jest przeznaczony każdego modelu? | Administracja centralna. Administratorzy, którzy należy skonfigurować zbieranie danych i użytkowników, którzy muszą mieć dostęp do szerokiej gamy zasobów. Obecnie wymagane również dla użytkowników, którzy mają dostęp do dzienników dla zasobów spoza platformy Azure. | Zespoły aplikacji. Administratorzy zasobów platformy Azure, które są monitorowane. |
| Aby wyświetlić dzienniki co wymaga użytkownika? | Uprawnienia do obszaru roboczego. Zobacz **uprawnień obszaru roboczego** w [Zarządzanie kontami i użytkownikami](#manage-accounts-and-users). | Dostęp do odczytu do tego zasobu. Zobacz **uprawnień dotyczących zasobów** w [Zarządzanie kontami i użytkownikami](#manage-accounts-and-users). Uprawnienia mogą być dziedziczone (np. od 1.02.2016 do grupy zasobów zawierającej) lub bezpośrednio przypisanego do zasobu. Uprawnienie do dzienników dla zasobu zostanie automatycznie przypisana. |
| Co to jest zakres uprawnień? | Obszar roboczy. Użytkownicy z dostępem do obszaru roboczego można badać wszystkie dzienniki w tym obszarze roboczym z tabel, które mają uprawnienia do. Zobacz [tabeli kontroli dostępu](#table-level-rbac) | Zasób platformy Azure. Użytkownik może zbadać dzienników dla zasobów mają dostęp do w dowolnym obszarze roboczym, ale nie można wykonać zapytania dzienników dla innych zasobów. |
| Jak można Dzienniki dostępu użytkownika? | Rozpocznij **dzienniki** z **usługi Azure Monitor** menu lub **obszarów roboczych usługi Log Analytics**. | Rozpocznij **dzienniki** menu dla zasobów platformy Azure. |


## <a name="access-control-mode"></a>Tryb kontroli dostępu
_Tryb kontroli dostępu_ jest ustawienie na poszczególnych obszarach roboczych, który definiuje, jak uprawnienia są określane dla danego obszaru roboczego.

**Wymaga uprawnień obszaru roboczego**:  W tym trybie formantu nie zezwala na szczegółową RBAC. Użytkownikowi dostępu do obszaru roboczego muszą otrzymać uprawnienia do obszaru roboczego lub określonych tabel. 

Jeśli użytkownik uzyskuje dostęp do obszaru roboczego w trybie skoncentrowane na obszar roboczy, będzie miał dostęp do wszystkich danych wszystkie tabele, które już zostać przyznany dostęp do. Jeśli użytkownik uzyskuje dostęp do obszaru roboczego w trybie skoncentrowane na zasób, będzie miał dostęp do danych tylko dla tego zasobu w żadnych tabel, które już zostać przyznany dostęp do.

Jest to domyślne ustawienie dla wszystkich obszarów roboczych utworzonych przed marca 2019 r.

**Użyj uprawnień zasobów lub obszaru roboczego**: Ten tryb kontroli umożliwia szczegółową RBAC. Użytkownicy uzyskują dostęp tylko do danych skojarzonych z zasobami, które mogą oni wyświetlać przy użyciu uprawnień platformy Azure, zasoby, do których mają `read` uprawnień. 

Gdy użytkownik uzyskuje dostęp do obszaru roboczego w trybie skoncentrowane na obszar roboczy, uprawnień obszaru roboczego będą stosowane. Gdy użytkownik uzyskuje dostęp do obszaru roboczego w trybie skoncentrowane na zasób, tylko uprawnień dotyczących zasobów zostaną zweryfikowane i uprawnień obszaru roboczego będą ignorowane. Włącz RBAC dla użytkownika poprzez usunięcie ich z uprawnień obszaru roboczego, dzięki czemu ich uprawnień dotyczących zasobów umożliwiających rozpoznana.

Jest to domyślne ustawienie dla wszystkich obszarów roboczych utworzonych po marca 2019 r.

> [!NOTE]
> Jeśli użytkownik ma tylko uprawnienia do zasobów, do obszaru roboczego, tylko będą w stanie uzyskać dostęp za pomocą obszaru roboczego do [skoncentrowane na zasób tryb](#access-modes).


### <a name="define-access-control-mode-in-azure-portal"></a>Zdefiniuj tryb kontroli dostępu w witrynie Azure portal
Bieżący tryb kontroli dostępu do obszaru roboczego można wyświetlić na **Przegląd** strony dla obszaru roboczego w **obszaru roboczego usługi Log Analytics** menu.

![Tryb kontroli dostępu do obszaru roboczego widoku](media/manage-access/view-access-control-mode.png)

To ustawienie można zmienić na **właściwości** strony dla obszaru roboczego. Zmienianie ustawień zostanie wyłączona, jeśli nie masz uprawnienia do konfigurowania obszaru roboczego.

![Zmień tryb dostępu do obszaru roboczego](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Zdefiniuj tryb kontroli dostępu w programie PowerShell

Aby sprawdzić tryb kontroli dostępu dla wszystkich obszarów roboczych w ramach subskrypcji, użyj następującego polecenia:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

Aby ustawić tryb kontroli dostępu dla określonego obszaru roboczego, użyj następującego skryptu:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Użyj następującego skryptu, aby ustawić tryb kontroli dostępu dla wszystkich obszarów roboczych w ramach subskrypcji

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Zdefiniuj tryb dostępu w szablonie usługi Resource Manager
Aby skonfigurować tryb dostępu w szablonie usługi Azure Resource Manager, należy ustawić **enableLogAccessUsingOnlyResourcePermissions** funkcji flagi w obszarze roboczym do jednej z następujących wartości.

- **FALSE**: Ustaw obszar roboczy uprawnień skoncentrowane na obszar roboczy. To ustawienie domyślne, jeśli flaga nie jest ustawiona.
- **Wartość true,**: Ustaw obszar roboczy skoncentrowane na zasób uprawnień.


## <a name="manage-accounts-and-users"></a>Zarządzanie kontami i użytkownikami
Uprawnienia do obszaru roboczego, które są stosowane do określonego użytkownika są definiowane przez ich tryb dostępu i [tryb kontroli dostępu](#access-control-mode) obszaru roboczego. **Uprawnień obszaru roboczego** są stosowane, gdy użytkownik uzyskuje dostęp do dowolnego obszaru roboczego przy użyciu **skoncentrowane na obszar roboczy** w [skoncentrowane na obszar roboczy tryb](#access-modes). **Uprawnień dotyczących zasobów** są stosowane, gdy użytkownik uzyskuje dostęp do obszaru roboczego z **Użyj uprawnień zasobów lub obszaru roboczego** [tryb kontroli dostępu](#access-control-mode) przy użyciu [tryb skoncentrowane na zasób ](#access-modes).

### <a name="workspace-permissions"></a>Uprawnień obszaru roboczego
Każdy obszar roboczy może mieć wiele kont skojarzonych z nim, a każde konto może uzyskiwać dostęp do wielu obszarów roboczych. Dostęp odbywa się za pośrednictwem [dostępu opartej na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md). 


Następujące działania również wymagają uprawnień platformy Azure:

| Akcja                                                          | Wymagane uprawnienia platformy Azure | Uwagi |
|-----------------------------------------------------------------|--------------------------|-------|
| Dodawanie i usuwanie rozwiązań do monitorowania                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Te uprawnienia należy nadać na poziomie grupy zasobów lub subskrypcji. |
| Zmienianie warstwy cenowej                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Wyświetlanie danych w kafelkach rozwiązań *Backup* i *Site Recovery* | Administrator/współadministrator | Uzyskuje dostęp do zasobów wdrożonych przy użyciu klasycznego modelu wdrażania |
| Tworzenie obszaru roboczego w witrynie Azure Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Zarządzanie dostępem do korzystania z platformy Azure uprawnień obszaru roboczego usługi Log Analytics 
Aby udzielić dostępu do obszaru roboczego usługi Log Analytics przy użyciu uprawnień platformy Azure, wykonaj kroki opisane w części [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../../role-based-access-control/role-assignments-portal.md).

Platforma Azure ma dwie wbudowane role użytkownika w przypadku obszarów roboczych usługi Log Analytics:
- Czytelnik usługi Log Analytics
- Współautor usługi Log Analytics

Członkowie roli *Czytelnik usługi Log Analytics* mogą wykonywać następujące czynności:
- Wyświetlanie i wyszukiwanie wszystkich monitorowanych danych 
- Wyświetlanie ustawień monitorowania, w tym konfiguracji diagnostyki platformy Azure, dla wszystkich zasobów platformy Azure

Rola Czytelnik usługi Log Analytics obejmuje następujące działania dla platformy Azure:

| Typ    | Uprawnienie | Opis |
| ------- | ---------- | ----------- |
| Akcja | `*/read`   | Możliwość wyświetlania wszystkich zasobów platformy Azure i konfiguracji zasobów. Obejmuje wyświetlanie następujących elementów: <br> Stan rozszerzenia maszyny wirtualnej <br> Konfiguracja diagnostyki platformy Azure dla zasobów <br> Wszystkie właściwości i ustawienia wszystkich zasobów |
| Akcja | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Możliwość wykonywania zapytań przeszukiwania dzienników w wersji 2 |
| Akcja | `Microsoft.OperationalInsights/workspaces/search/action` | Możliwość wykonywania zapytań przeszukiwania dzienników w wersji 1 |
| Akcja | `Microsoft.Support/*` | Możliwość otwierania zgłoszeń do pomocy technicznej |
|Inne | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Zapobiega odczytywaniu obszaru roboczego klucz jest wymagany do użycia interfejsu API zbierania danych oraz instalowania agentów. To uniemożliwia dodanie nowych zasobów do obszaru roboczego |


Członkowie roli *Współautor usługi Log Analytics* mogą wykonywać następujące czynności:
- Odczytywać wszystkie dane monitorowania, jak czytelnik usługi Log Analytics  
- Tworzenie i konfigurowanie kont usługi Automation  
- Dodawanie i usuwanie rozwiązań do zarządzania    
    > [!NOTE] 
    > W celu pomyślnego przeprowadzenia ostatniego dwie akcje, to uprawnienie musi mieć uprawnienia na poziomie grupy lub subskrypcji zasobów.  

- Odczytywanie kluczy kont magazynu   
- Konfigurowanie kolekcji dzienników z usługi Azure Storage  
- Edytowanie ustawień monitorowania dla zasobów platformy Azure, w tym:
  - Dodawanie rozszerzenia do maszyn wirtualnych
  - Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure

> [!NOTE] 
> Możliwości dodania rozszerzenia do maszyny wirtualnej możesz użyć, aby zyskać pełną kontrolę nad maszyną wirtualną.

Rola Współautor usługi Log Analytics obejmuje następujące działania dla platformy Azure:

| Uprawnienie | Opis |
| ---------- | ----------- |
| `*/read`     | Możliwość wyświetlania wszystkich zasobów i konfiguracji zasobów. Obejmuje wyświetlanie następujących elementów: <br> Stan rozszerzenia maszyny wirtualnej <br> Konfiguracja diagnostyki platformy Azure dla zasobów <br> Wszystkie właściwości i ustawienia wszystkich zasobów |
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
- Subskrypcja — dostęp do wszystkich obszarów roboczych w subskrypcji
- Grupa zasobów — dostęp do wszystkich obszarów roboczych w grupie zasobów
- Zasób — dostęp tylko do określonego obszaru roboczego

Należy to wykonać przydziały na poziomie zasobów (obszaru roboczego) w celu zapewnienia kontroli dostępu dokładne.  Za pomocą [ról niestandardowych](../../role-based-access-control/custom-roles.md) możesz utworzyć role z określonymi, wymaganymi uprawnieniami.

### <a name="resource-permissions"></a>Uprawnienia dotyczące zasobów 
Po zalogowaniu użytkownicy zapytania z obszaru roboczego przy użyciu dostępu skoncentrowane na zasób, będą mieć następujące uprawnienia do zasobu:

| Uprawnienie | Opis |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Przykłady:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Możliwość wyświetlania wszystkich danych dziennika dla zasobu.  |


To zazwyczaj uprawnienia z roli, która obejmuje  _\*/Odczyt lub_ _\*_ uprawnienia, takie jak wbudowane [czytnika](../../role-based-access-control/built-in-roles.md#reader) i [ Współautor](../../role-based-access-control/built-in-roles.md#contributor) ról. Należy pamiętać, że role niestandardowe, które obejmują konkretne akcje lub dedykowane wbudowane role mogą nie obejmować to uprawnienie.

Zobacz [Definiowanie kontroli dostępu na tabelę](#table-level-rbac) poniżej if, do której chcesz utworzyć kontroli dostępu różne dla różnych tabel.


## <a name="table-level-rbac"></a>Na poziomie tabeli RBAC
**Tabela poziom RBAC** umożliwia zapewniają bardziej szczegółową kontrolę do danych w obszarze roboczym usługi Log Analytics, oprócz innych uprawnień. Dzięki temu można zdefiniować konkretnych typów danych, które są dostępne tylko dla określonych użytkowników.

Implementowanie kontroli dostępu tabeli za pomocą [Azure role niestandardowe](../../role-based-access-control/custom-roles.md) udzielić lub odmówić dostępu do określonych [tabel](../log-query/log-query-overview.md#how-azure-monitor-log-data-is-organized) w obszarze roboczym. Te role są stosowane do obszarów roboczych skoncentrowane na obszar roboczy lub skoncentrowane na zasób [tryby kontroli dostępu](#access-control-mode) niezależnie od użytkownika [tryb dostępu](#access-modes).

Tworzenie [roli niestandardowej](../../role-based-access-control/custom-roles.md) z następujących akcji, aby definiują dostęp użytkownika do kontroli dostępu w tabeli.

- Aby udzielić dostępu do tabeli, należy dołączyć go w **akcje** części definicji roli.
- Aby odmówić dostępu do tabeli, należy dołączyć go w **NotActions** części definicji roli.
- Użyj * Aby określić wszystkie tabele.

Na przykład, aby utworzyć rolę z uprawnieniami do _pulsu_ i _AzureActivity_ tabel, utworzyć rolę niestandardową przy użyciu następujących czynności:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Aby utworzyć rolę z uprawnieniami do tylko _SecurityBaseline_ i żadne inne tabele, utworzyć rolę niestandardową przy użyciu następujących czynności:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Niestandardowe dzienniki
 Niestandardowe dzienniki są tworzone przez źródeł danych, takich jak niestandardowe dzienniki i interfejsu API modułu zbierającego dane HTTP. Najprostszym sposobem określenia typu dziennika jest, sprawdzając tabele na liście [niestandardowe dzienniki w schemacie dziennika](../log-query/get-started-portal.md#understand-the-schema).

 Obecnie nie można udzielić lub odmówić dostępu do poszczególnych dzienników niestandardowych, ale można udzielić lub odmówić dostępu do wszystkich dzienników niestandardowych. Aby utworzyć rolę z uprawnieniami do wszystkich dzienników niestandardowych, należy utworzyć rolę niestandardową przy użyciu następujących czynności:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Zagadnienia do rozważenia

- Jeśli użytkownikowi udzielono globalnego Odczyt uprawnień przy użyciu standardowych ról czytelnika lub współautora, obejmujących  _\*/odczyt_ akcji, zastąpi on kontroli dostępu na tabelę i przyznać im dostęp do wszystkich danych dziennika.
- Jeśli użytkownikowi udzielono dostępu do poszczególnych tabel, ale żadne inne uprawnienia, będzie mogła uzyskiwać dostęp do danych dziennika, z interfejsu API, ale nie w witrynie Azure portal. Aby zapewnić dostęp do witryny Azure portal, należy użyć jako jego podstawowy rola Czytelnik usługi Log Analytics.
- Administratorzy subskrypcji mają dostęp do wszystkich typów danych, niezależnie od innych ustawień uprawnień.
- Właściciele obszaru roboczego są traktowane jak każdy inny użytkownik, do kontroli dostępu na tabelę.
- Należy przypisać role do grup zabezpieczeń, zamiast poszczególnych użytkowników w celu zmniejszenia liczby przydziałów. Ułatwi to również możesz użyć istniejących narzędzi do zarządzania grupy do skonfigurowania i zweryfikować dostęp.




## <a name="next-steps"></a>Kolejne kroki
* Zobacz [omówienie agenta usługi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) do zbierania danych z komputerów w centrum danych lub w innym środowisku chmury.
* Aby skonfigurować zbieranie danych z maszyn wirtualnych platformy Azure, zobacz [Zbieranie danych dotyczących infrastruktury Azure Virtual Machines](../../azure-monitor/learn/quick-collect-azurevm.md).  

