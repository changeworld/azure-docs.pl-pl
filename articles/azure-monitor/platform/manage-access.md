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
ms.date: 07/16/2019
ms.author: magoedte
ms.openlocfilehash: fbfbd8e26ab3e92f06194322be7ec2be2fb180fd
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254462"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Zarządzanie danymi dziennika i obszarami roboczymi w Azure Monitor
Azure Monitor przechowuje dane dziennika w Log Analytics obszarze roboczym, który zasadniczo jest kontenerem zawierającym informacje o danych i konfiguracji. Aby zarządzać dostępem do danych dziennika, należy wykonać różne zadania administracyjne związane z obszarami roboczymi. Ty i inni członkowie organizacji możecie używać wielu obszarów roboczych, aby zarządzać różnymi zestawami danych zebranymi z całej infrastruktury IT lub jej części.

W tym artykule opisano sposób zarządzania dostępem do dzienników i zarządzania obszarami roboczymi, które je zawierają. 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
Aby utworzyć obszar roboczy Log Analytics, należy:

1. Mieć subskrypcję platformy Azure.
2. Wybrać nazwę obszaru roboczego.
3. Skojarzyć obszar roboczy z jednym z Twojej subskrypcji i grup zasobów.
4. Wybrać lokalizację geograficzną.

Aby uzyskać szczegółowe informacje na temat tworzenia obszaru roboczego, zapoznaj się z następującymi artykułami:

- [Utwórz obszar roboczy Log Analytics w Azure Portal](../learn/quick-create-workspace.md)
- [Tworzenie obszaru roboczego Log Analytics przy użyciu interfejsu wiersza polecenia platformy Azure 2,0](../learn/quick-create-workspace-cli.md)
- [Utwórz obszar roboczy Log Analytics z Azure PowerShell](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>Określanie wymaganej liczby obszarów roboczych
Obszar roboczy Log Analytics jest zasobem platformy Azure i jest kontenerem, w którym dane są zbierane, agregowane, analizowane i prezentowane w Azure Monitor. Można mieć wiele obszarów roboczych na subskrypcję platformy Azure i mieć dostęp do więcej niż jednego obszaru roboczego, dzięki czemu można łatwo wykonywać zapytania na ich temat. W tej sekcji opisano sytuacje, w których pomocne może być utworzenie więcej niż jednego obszaru roboczego.

Obszar roboczy Log Analytics zawiera następujące informacje:

* Lokalizacja geograficzna magazynu danych.
* Izolacja danych w celu zdefiniowania różnych praw dostępu użytkownika w trybie skoncentrowanym na obszarze roboczym. Nie dotyczy podczas pracy w trybie zorientowanym na zasoby.
* Zakres konfiguracji ustawień, takich jak [warstwa cenowa](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [przechowywanie](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) i [pułapy danych](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).
* Opłaty związane z pozyskiwaniem i przechowywaniem danych są dokonywane w ramach zasobu obszaru roboczego.

Z punktu widzenia użycia firma Microsoft zaleca się utworzenie w kilku obszarów roboczych jak to możliwe. Ułatwia administrowanie i zapytania łatwiej i szybciej. Jednak na podstawie poprzedniego właściwości, warto utworzyć wiele obszarów roboczych, jeśli:

* Jesteś firmą globalną i potrzebujesz danych dzienników przechowywanych w określonych regionach w celu zachowania poufności danych lub jej zgodności.
* Korzystasz z platformy Azure i chcesz uniknąć naliczania opłat za transfer danych wychodzących przez umieszczenie obszaru roboczego w tym samym regionie co zarządzane przez niego zasoby platformy Azure.
* Jesteś dostawcą usługi zarządzanej i chcesz, aby dane usługi Log Analytics dla klientów, którymi zarządzasz, były odizolowane od danych innych klientów.
* Zarządzasz wieloma klientami i chcesz, aby każdy klient/dział/Grupa biznesowa mogli zobaczyć własne dane, ale nie dane pochodzące od innych, i nie ma potrzeby biznesowego dla skonsolidowanego, międzypracowników/działu/widoku grupy biznesowej ".

Jeśli zbierasz dane przy użyciu agentów systemu Windows, możesz [skonfigurować każdego agenta w celu raportowania do co najmniej jednego obszaru roboczego](../../azure-monitor/platform/agent-windows.md).

Jeśli używasz programu System Center Operations Manager, jedna grupa zarządzania programu Operations Manager może być połączona tylko z jednym obszarem roboczym. Możesz zainstalować program Microsoft Monitoring Agent na komputerach zarządzanych przez program Operations Manager oraz skonfigurować agenta w celu raportowania do programu Operations Manager i innego obszaru roboczego usługi Log Analytics.

Po zdefiniowaniu architektury obszaru roboczego należy wymusić te zasady dla zasobów platformy Azure za pomocą [Azure Policy](../../governance/policy/overview.md). Może to być wbudowana definicja, która będzie automatycznie stosowana do wszystkich zasobów platformy Azure. Można na przykład ustawić zasady, aby upewnić się, że wszystkie zasoby platformy Azure w określonym regionie przesłały wszystkie dzienniki diagnostyczne do określonego obszaru roboczego.

## <a name="view-workspace-details"></a>Wyświetl szczegóły obszaru roboczego
Analizując dane w obszarze roboczym Log Analytics z menu **Azure monitor** w Azure Portal, tworzysz obszary robocze i zarządzaj nimi w menu **log Analytics obszary robocze** .
 

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i kliknij pozycję **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **log Analytics** obszary robocze.  

    ![Azure Portal](media/manage-access/azure-portal-01.png)  

3. Z listy wybierz swój obszar roboczy.

4. Na stronie obszaru roboczego są wyświetlane szczegóły dotyczące obszaru roboczego, wprowadzenie, konfiguracja i linki w celu uzyskania dodatkowych informacji.  

    ![Szczegóły obszaru roboczego](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Uprawnienia i zakres obszaru roboczego
Dane, do których użytkownik ma dostęp, są określane przez wiele czynników, które są wymienione w poniższej tabeli. Każdy z nich został opisany w poniższych sekcjach.

| współczynnik | Opis |
|:---|:---|
| [Tryb dostępu](#access-modes) | Metoda wykorzystywana przez użytkownika w celu uzyskania dostępu do obszaru roboczego.  Definiuje zakres dostępnych danych i tryb kontroli dostępu, który został zastosowany. |
| [Tryb kontroli dostępu](#access-control-mode) | Ustawienie w obszarze roboczym, które określa, czy uprawnienia są stosowane na poziomie obszaru roboczego czy zasobu. |
| [Uprawnienia](#manage-accounts-and-users) | Uprawnienia zastosowane do poszczególnych lub grup użytkowników dla obszaru roboczego lub zasobu. Definiuje dane, do których użytkownik będzie miał dostęp. |
| [Kontrola RBAC na poziomie tabeli](#table-level-rbac) | Opcjonalne, szczegółowe uprawnienia, które mają zastosowanie do wszystkich użytkowników niezależnie od ich trybu dostępu lub trybu kontroli dostępu. Określa typy danych, do których użytkownik może uzyskać dostęp. |



## <a name="access-modes"></a>Tryby dostępu
_Tryb dostępu_ odnosi się do sposobu, w jaki użytkownik uzyskuje dostęp do obszaru roboczego log Analytics i definiuje zakres danych, do których mają dostęp. 

**Skoncentrowane na obszarze roboczym**: W tym trybie użytkownik może wyświetlić wszystkie dzienniki w obszarze roboczym, do którego mają uprawnienia. Zapytania w tym trybie są ograniczone do wszystkich danych we wszystkich tabelach w obszarze roboczym. Jest to tryb dostępu używany do uzyskiwania dostępu do dzienników z obszarem roboczym jako zakres, na przykład po wybraniu opcji **dzienniki** z menu **Azure monitor** w Azure Portal.

**Skoncentrowane**na zasobach: Gdy uzyskujesz dostęp do obszaru roboczego dla określonego zasobu, na przykład po wybraniu opcji **dzienniki** z menu zasobów w Azure Portal, można wyświetlić dzienniki tylko dla tego zasobu we wszystkich tabelach, do których masz dostęp. Zapytania w tym trybie są ograniczone do danych skojarzonych z tym zasobem. Ten tryb umożliwia także szczegółowe kontroli dostępu opartej na rolach (RBAC). 

> [!NOTE]
> Dzienniki są dostępne dla zapytań skoncentrowanych na zasobach, tylko jeśli zostały prawidłowo skojarzone z odpowiednim zasobem. Obecnie następujące zasoby mają ograniczenia: 
> - Komputery poza platformą Azure
> - Service Fabric
> - Application Insights
> - Containers
>
> Możesz sprawdzić, czy dzienniki są prawidłowo skojarzone ze swoimi zasobami, uruchamiając zapytanie i sprawdzając odpowiednie rekordy. Jeśli prawidłowy identyfikator zasobu znajduje się we właściwości [_ResourceId](log-standard-properties.md#_resourceid) , dane są dostępne dla zapytań skoncentrowanych na zasobach.

### <a name="comparing-access-modes"></a>Porównywanie trybów dostępu

Poniższa tabela zawiera podsumowanie trybów dostępu:

| | Skoncentrowane na obszarze roboczym | Skoncentrowane na zasobach |
|:---|:---|:---|
| Dla kogo jest przeznaczony każdy model? | Administracja centralna. Administratorzy, którzy muszą skonfigurować zbieranie danych i użytkowników, którzy potrzebują dostępu do szerokiej gamy zasobów. Są one również wymagane dla użytkowników, którzy mają dostęp do dzienników dla zasobów poza platformą Azure. | Zespoły aplikacji. Administratorzy monitorowanych zasobów platformy Azure. |
| Co jest wymagane przez użytkownika do wyświetlania dzienników? | Uprawnienia do obszaru roboczego. Zobacz **uprawnienia obszaru roboczego** w artykule [Zarządzanie kontami i użytkownikami](#manage-accounts-and-users). | Dostęp do odczytu do zasobu. Zobacz **uprawnienia zasobów** w obszarze [Zarządzanie kontami i użytkownikami](#manage-accounts-and-users). Uprawnienia mogą być dziedziczone (takie jak z grupy zasobów zawierających) lub bezpośrednio przypisane do zasobu. Uprawnienie do dzienników dla zasobu zostanie automatycznie przypisane. |
| Jaki jest zakres uprawnień? | Obszary. Użytkownicy z dostępem do obszaru roboczego mogą wykonywać zapytania dotyczące wszystkich dzienników w tym obszarze roboczym z tabel, do których mają uprawnienia. Zobacz [tabelę kontroli dostępu](#table-level-rbac) | Zasób platformy Azure. Użytkownik może wysyłać zapytania o zasoby, do których mają dostęp z dowolnego obszaru roboczego, ale nie może wysyłać zapytań do dzienników innych zasobów. |
| Jak użytkownicy mogą uzyskiwać dostęp do dzienników? | Uruchom **dzienniki** z menu **Azure monitor** lub **log Analytics obszarów roboczych**. | Uruchom **dzienniki** z menu dla zasobu platformy Azure. |


## <a name="access-control-mode"></a>Tryb kontroli dostępu
_Tryb kontroli dostępu_ jest ustawieniem dla każdego obszaru roboczego, który definiuje sposób określania uprawnień dla danego obszaru roboczego.

**Wymagaj uprawnień obszaru roboczego**:  Ten tryb kontroli nie zezwala na szczegółową RBAC. Aby użytkownik mógł uzyskać dostęp do obszaru roboczego, muszą mieć przyznane uprawnienia do obszaru roboczego lub do określonych tabel. 

Jeśli użytkownik uzyskuje dostęp do obszaru roboczego w trybie skoncentrowanym na obszarze roboczym, będzie miał dostęp do wszystkich danych dowolnych tabel, do których udzielono dostępu. Jeśli użytkownik uzyskuje dostęp do obszaru roboczego w trybie zorientowanym na zasoby, będzie miał dostęp tylko do danych dla tego zasobu w każdej tabeli, do której udzielono dostępu.

Jest to ustawienie domyślne dla wszystkich obszarów roboczych utworzonych przed marcem 2019.

**Użyj uprawnień zasobu lub obszaru roboczego**: Ten tryb kontroli zezwala na szczegółową RBAC. Użytkownikom uzyskuje się dostęp tylko do danych skojarzonych z zasobami, które mogą przeglądać za pomocą uprawnień platformy Azure, zasobów, `read` do których mają uprawnienia. 

Gdy użytkownik uzyskuje dostęp do obszaru roboczego w trybie skoncentrowanym na obszarze roboczym, zostaną zastosowane uprawnienia do obszaru roboczego. Gdy użytkownik uzyskuje dostęp do obszaru roboczego w trybie zorientowanym na zasoby, zostaną zweryfikowane tylko uprawnienia do zasobów, a uprawnienia obszaru roboczego zostaną zignorowane. Włącz funkcję RBAC dla użytkownika, usuwając je z uprawnień obszaru roboczego i zezwalając na ich rozpoznanie.

Jest to ustawienie domyślne dla wszystkich obszarów roboczych utworzonych po marcu 2019.

> [!NOTE]
> Jeśli użytkownik ma tylko uprawnienia do zasobów w obszarze roboczym, będzie mógł uzyskać dostęp do obszaru roboczego tylko przy użyciu [trybu skoncentrowanego](#access-modes)na zasobach.


### <a name="define-access-control-mode-in-azure-portal"></a>Zdefiniuj tryb kontroli dostępu w Azure Portal
Bieżący tryb kontroli dostępu obszaru roboczego można wyświetlić na stronie **Przegląd** obszaru roboczego w menu **log Analytics obszaru roboczego** .

![Wyświetlanie trybu kontroli dostępu do obszaru roboczego](media/manage-access/view-access-control-mode.png)

To ustawienie można zmienić na stronie **Właściwości** obszaru roboczego. Zmiana ustawienia zostanie wyłączona, jeśli nie masz uprawnień do konfigurowania obszaru roboczego.

![Zmień tryb dostępu obszaru roboczego](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Definiowanie trybu kontroli dostępu w programie PowerShell

Użyj następującego polecenia, aby przeanalizować tryb kontroli dostępu dla wszystkich obszarów roboczych w subskrypcji:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

Użyj następującego skryptu, aby ustawić tryb kontroli dostępu dla określonego obszaru roboczego:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Użyj poniższego skryptu, aby ustawić tryb kontroli dostępu dla wszystkich obszarów roboczych w subskrypcji

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Zdefiniuj tryb dostępu w szablonie Menedżer zasobów
Aby skonfigurować tryb dostępu w szablonie Azure Resource Manager, Ustaw flagę funkcji **enableLogAccessUsingOnlyResourcePermissions** w obszarze roboczym na jedną z następujących wartości.

- **wartość false**: Ustaw obszar roboczy na uprawnienia skoncentrowane na obszarze roboczym. Jest to ustawienie domyślne, jeśli flaga nie jest ustawiona.
- **wartość true**: Ustaw obszar roboczy na uprawnienia skoncentrowane na zasobach.


## <a name="manage-accounts-and-users"></a>Zarządzanie kontami i użytkownikami
Uprawnienia do obszaru roboczego, które są stosowane do określonego użytkownika, są definiowane przez ich tryb dostępu i [Tryb kontroli dostępu](#access-control-mode) obszaru roboczego. **Uprawnienia obszaru roboczego** są stosowane, gdy użytkownik uzyskuje dostęp do dowolnego obszaru roboczego przy użyciu **obszaru** roboczego skoncentrowanego na obszarze [roboczym](#access-modes). **Uprawnienia do zasobów** są stosowane w przypadku, gdy użytkownik uzyskuje dostęp do obszaru roboczego za pomocą [trybu kontroli dostępu](#access-control-mode) **zasobów lub obszaru roboczego** przy użyciu [trybu skoncentrowanego](#access-modes)na zasobach.

### <a name="workspace-permissions"></a>Uprawnienia obszaru roboczego
Każdy obszar roboczy może mieć wiele kont skojarzonych z nim, a każde konto może uzyskiwać dostęp do wielu obszarów roboczych. Dostęp odbywa się za pośrednictwem [dostępu opartej na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md). 


Następujące działania również wymagają uprawnień platformy Azure:

||Akcja |Wymagane uprawnienia platformy Azure |Uwagi |
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



#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Zarządzanie dostępem do obszaru roboczego Log Analytics przy użyciu uprawnień platformy Azure 
Aby udzielić dostępu do obszaru roboczego usługi Log Analytics przy użyciu uprawnień platformy Azure, wykonaj kroki opisane w części [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../../role-based-access-control/role-assignments-portal.md).

Platforma Azure ma dwie wbudowane role użytkowników dla Log Analytics obszarów roboczych:
- Czytelnik usługi Log Analytics
- Współautor usługi Log Analytics

Członkowie roli *Czytelnik usługi Log Analytics* mogą wykonywać następujące czynności:
- Wyświetlanie i wyszukiwanie wszystkich monitorowanych danych 
- Wyświetlanie ustawień monitorowania, w tym konfiguracji diagnostyki platformy Azure, dla wszystkich zasobów platformy Azure

Rola Czytelnik usługi Log Analytics obejmuje następujące działania dla platformy Azure:

| Typ    | Uprawnienie | Opis |
| ------- | ---------- | ----------- |
| Akcja | `*/read`   | Możliwość wyświetlania wszystkich zasobów platformy Azure i konfiguracji zasobów. Obejmuje wyświetlanie następujących elementów: <br> Stan rozszerzenia maszyny wirtualnej <br> Konfiguracja diagnostyki platformy Azure dla zasobów <br> Wszystkie właściwości i ustawienia wszystkich zasobów. <br> W przypadku obszarów roboczych umożliwia pełne nieograniczone uprawnienia do odczytywania ustawień obszaru roboczego i wykonywania zapytań dotyczących danych. Zobacz bardziej szczegółowe opcje powyżej. |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Przestarzałe, nie trzeba ich przypisywać do użytkowników. s |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | Przestarzałe, nie trzeba ich przypisywać do użytkowników. |
| Action | `Microsoft.Support/*` | Możliwość otwierania zgłoszeń do pomocy technicznej |
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
| `*/read`     | Możliwość wyświetlania wszystkich zasobów platformy Azure i konfiguracji zasobów. Obejmuje wyświetlanie następujących elementów: <br> Stan rozszerzenia maszyny wirtualnej <br> Konfiguracja diagnostyki platformy Azure dla zasobów <br> Wszystkie właściwości i ustawienia wszystkich zasobów. <br> W przypadku obszarów roboczych umożliwia pełne nieograniczone uprawnienia do odczytywania ustawień obszaru roboczego i wykonywania zapytań dotyczących danych. Zobacz bardziej szczegółowe opcje powyżej. |
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

W celu zapewnienia dokładnej kontroli dostępu należy wykonać przydziały na poziomie zasobów (w obszarze roboczym).  Za pomocą [ról niestandardowych](../../role-based-access-control/custom-roles.md) możesz utworzyć role z określonymi, wymaganymi uprawnieniami.

### <a name="resource-permissions"></a>Uprawnienia zasobów 
Gdy użytkownicy wykonują zapytania dotyczące dzienników z obszaru roboczego przy użyciu dostępu skoncentrowanego na zasobach, będą mieć następujące uprawnienia do zasobu:

| Uprawnienie | Opis |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Przykłady:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Możliwość wyświetlania wszystkich danych dziennika dla zasobu.  |
| `Microsoft.Insights/diagnosticSettings/write ` | Możliwość skonfigurowania ustawień diagnostycznych w celu zezwolenia na Konfigurowanie dzienników dla tego zasobu. |

To uprawnienie _\*_ jest zwykle udzielane przez rolę [](../../role-based-access-control/built-in-roles.md#contributor) [](../../role-based-access-control/built-in-roles.md#reader)  _\*obejmującą/Read lub_ uprawnienia, takie jak wbudowane czytniki i role współautor. Należy zauważyć, że role niestandardowe zawierające określone akcje lub dedykowane role wbudowane mogą nie uwzględniać tego uprawnienia.

Zobacz [Definiowanie kontroli dostępu dla tabel](#table-level-rbac) poniżej, jeśli chcesz utworzyć inną kontrolę dostępu dla różnych tabel.


## <a name="table-level-rbac"></a>Kontrola RBAC na poziomie tabeli
Kontrolka **RBAC na poziomie tabeli** umożliwia zapewnienie bardziej szczegółowej kontroli nad danymi w obszarze roboczym log Analytics oprócz innych uprawnień. Ta kontrolka umożliwia definiowanie określonych typów danych, które są dostępne tylko dla określonego zestawu użytkowników.

W celu zaimplementowania kontroli dostępu do tabel przy użyciu [ról niestandardowych platformy Azure](../../role-based-access-control/custom-roles.md) można udzielić lub odmówić dostępu do określonych [tabel](../log-query/logs-structure.md) w obszarze roboczym. Te role są stosowane do obszarów roboczych z [trybami kontroli dostępu](#access-control-mode) skoncentrowanymi na obszarze roboczym lub zasobami niezależnie od [trybu dostępu](#access-modes)użytkownika.

Utwórz niestandardową [rolę](../../role-based-access-control/custom-roles.md) z poniższymi akcjami, aby zdefiniować dostęp do kontroli dostępu do tabeli.

- Aby udzielić dostępu do tabeli, Uwzględnij ją w sekcji **Akcje** w definicji roli.
- Aby odmówić dostępu do tabeli, Uwzględnij ją **w sekcji** nogrids w definicji roli.
- Aby określić wszystkie tabele, użyj znaku *.

Na przykład aby utworzyć rolę z dostępem do tabel pulsu __ i _Azure_ , Utwórz rolę niestandardową przy użyciu następujących akcji:

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

### <a name="custom-logs"></a>Niestandardowe dzienniki
 Dzienniki niestandardowe są tworzone przez źródła danych, takie jak dzienniki niestandardowe i interfejs API modułu zbierającego dane HTTP. Najprostszym sposobem identyfikacji typu dziennika jest sprawdzenie tabel wymienionych w obszarze [dzienniki niestandardowe w schemacie dziennika](../log-query/get-started-portal.md#understand-the-schema).

 Obecnie nie można udzielić lub odmówić dostępu do poszczególnych dzienników niestandardowych, ale można udzielić lub odmówić dostępu do wszystkich dzienników niestandardowych. Aby utworzyć rolę mającą dostęp do wszystkich dzienników niestandardowych, Utwórz rolę niestandardową przy użyciu następujących akcji:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Zagadnienia do rozważenia

- Jeśli użytkownik otrzymuje globalne uprawnienie do odczytu ze standardowym czytnikiem lub rolą współautor, które zawiera  _\*akcję/Read_ , zastąpi kontrolę dostępu do tabeli i przekaże im dostęp do wszystkich danych dziennika.
- Jeśli użytkownik uzyska dostęp do tabeli, ale nie ma żadnych innych uprawnień, może uzyskać dostęp do danych dziennika z interfejsu API, ale nie z Azure Portal. Aby zapewnić dostęp do Azure Portal, Użyj czytnika Log Analytics jako roli podstawowej.
- Administratorzy subskrypcji będą mieli dostęp do wszystkich typów danych, niezależnie od innych ustawień uprawnień.
- Właściciele obszarów roboczych są traktowani jak każdy inny użytkownik do kontroli dostępu do tabeli.
- Aby zmniejszyć liczbę przypisań, należy przypisać role do grup zabezpieczeń zamiast poszczególnych użytkowników. Ułatwi to również korzystanie z istniejących narzędzi do zarządzania grupami w celu konfigurowania i weryfikowania dostępu.




## <a name="next-steps"></a>Kolejne kroki
* Zobacz [omówienie agenta usługi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) do zbierania danych z komputerów w centrum danych lub w innym środowisku chmury.
* Aby skonfigurować zbieranie danych z maszyn wirtualnych platformy Azure, zobacz [Zbieranie danych dotyczących infrastruktury Azure Virtual Machines](../../azure-monitor/learn/quick-collect-azurevm.md).  

