---
title: Zarządzanie obszarami roboczymi w usłudze Azure Log Analytics i portalu pakietu OMS | Microsoft Docs
description: Możesz zarządzać obszarami roboczymi w usłudze Azure Log Analytics i portalu pakietu OMS przy użyciu różnych zadań administracyjnych dotyczących użytkowników, kont, obszarów roboczych i kont platformy Azure.
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
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 71987fcde08c5098d98d21405ce79e61d3094424
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186059"
---
# <a name="manage-workspaces"></a>Zarządzanie obszarami roboczymi

Dostępem do usługi Log Analytics można zarządzać, wykonując różne zadania administracyjne dotyczące obszarów roboczych. Ten artykuł zawiera wskazówki i procedury umożliwiające zarządzanie obszarami roboczymi. Obszar roboczy to kontener, który zawiera informacje o koncie i podstawowe informacje o konfiguracji konta. Ty i inni członkowie organizacji możecie używać wielu obszarów roboczych, aby zarządzać różnymi zestawami danych zebranymi z całej infrastruktury IT lub jej części.

Aby utworzyć obszar roboczy, trzeba:

1. Mieć subskrypcję platformy Azure.
2. Wybrać nazwę obszaru roboczego.
3. Skojarzyć obszar roboczy z jednym z Twojej subskrypcji i grup zasobów.
4. Wybrać lokalizację geograficzną.

## <a name="determine-the-number-of-workspaces-you-need"></a>Określanie wymaganej liczby obszarów roboczych
Obszar roboczy jest zasobem platformy Azure w postaci kontenera, w którym dane są zbierane, agregowane, analizowane i przedstawiane w witrynie Azure Portal.

Możesz mieć wiele obszarów roboczych na subskrypcję platformy Azure i dostęp do więcej niż jednego obszaru roboczego, aby łatwo wykonywać zapytania obejmujące różne obszary. W tej sekcji opisano sytuacje, w których pomocne może być utworzenie więcej niż jednego obszaru roboczego.

Obecnie obszar roboczy oferuje następujące możliwości:

* Lokalizacja geograficzna do przechowywania danych
* Izolacja danych, aby zdefiniować prawa dostępu różnych użytkowników
* Możliwość konfiguracji ustawień, takich jak przechowywanie i danych są takie same

Z punktu widzenia użycia firma Microsoft zaleca się utworzenie w kilku obszarów roboczych jak to możliwe. Ułatwia administrowanie i zapytania łatwiej i szybciej. Jednak na podstawie poprzedniego właściwości, warto utworzyć wiele obszarów roboczych, jeśli:

* Globalna firma wymaga, aby jej dane były przechowywane w określonych regionach w celu zachowania poufności danych i ich zgodności z przepisami.
* Korzystasz z platformy Azure i chcesz uniknąć naliczania opłat za transfer danych wychodzących przez umieszczenie obszaru roboczego w tym samym regionie co zarządzane przez niego zasoby platformy Azure.
* Chcesz przydzielić opłaty do różnych działów lub grup biznesowych na podstawie ich użycia, tworząc obszar roboczy dla każdego działu lub grupie biznesowej w jego własnej subskrypcji platformy Azure.
* Jesteś dostawcą usługi zarządzanej i chcesz, aby dane usługi Log Analytics dla klientów, którymi zarządzasz, były odizolowane od danych innych klientów.
* Zarządzasz wieloma klientami i chcesz, aby każdy klient / działu / firm grupy, własnych danych, ale nie danych od innych użytkowników.

Jeśli zbierasz dane przy użyciu agentów systemu Windows, możesz [skonfigurować każdego agenta w celu raportowania do co najmniej jednego obszaru roboczego](../../azure-monitor/platform/agent-windows.md).

Jeśli używasz programu System Center Operations Manager, jedna grupa zarządzania programu Operations Manager może być połączona tylko z jednym obszarem roboczym. Możesz zainstalować program Microsoft Monitoring Agent na komputerach zarządzanych przez program Operations Manager oraz skonfigurować agenta w celu raportowania do programu Operations Manager i innego obszaru roboczego usługi Log Analytics.

## <a name="workspace-information"></a>Informacje o obszarze roboczym

Szczegółowe informacje o obszarze roboczym można wyświetlić w witrynie Azure Portal. 

1. Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.  

    ![Azure Portal](media/manage-access/azure-portal-01.png)  

3. W okienku subskrypcji usługi Log Analytics wybierz obszar roboczy.

4. Strona obszaru roboczego zawiera szczegółowe informacje o wprowadzenie, konfiguracji i linki do dodatkowych informacji.  

    ![Szczegóły obszaru roboczego](./media/manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>Zarządzanie kontami i użytkownikami
Każdy obszar roboczy może mieć wiele kont skojarzonych z nim, a każde konto może uzyskiwać dostęp do wielu obszarów roboczych. Dostęp odbywa się za pośrednictwem [dostępu opartej na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md). Te prawa dostępu ma zastosowanie zarówno w witrynie Azure portal, jak i na dostęp do interfejsu API.


Następujące działania również wymagają uprawnień platformy Azure:

| Akcja                                                          | Wymagane uprawnienia platformy Azure | Uwagi |
|-----------------------------------------------------------------|--------------------------|-------|
| Dodawanie i usuwanie rozwiązań do zarządzania                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Te uprawnienia należy nadać na poziomie grupy zasobów lub subskrypcji. |
| Zmienianie warstwy cenowej                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Wyświetlanie danych w kafelkach rozwiązań *Backup* i *Site Recovery* | Administrator/współadministrator | Uzyskuje dostęp do zasobów wdrożonych przy użyciu klasycznego modelu wdrażania |
| Tworzenie obszaru roboczego w witrynie Azure Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Zarządzanie dostępem do usługi Log Analytics przy użyciu uprawnień platformy Azure
Aby udzielić dostępu do obszaru roboczego usługi Log Analytics przy użyciu uprawnień platformy Azure, wykonaj kroki opisane w części [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../../role-based-access-control/role-assignments-portal.md).

Platforma Azure ma dwie wbudowane role użytkownika w usłudze Log Analytics:
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

Zaleca się wykonanie przypisań na poziomie zasobów (obszar roboczy), aby zapewnić dokładną kontrolę dostępu.  Za pomocą [ról niestandardowych](../../role-based-access-control/custom-roles.md) możesz utworzyć role z określonymi, wymaganymi uprawnieniami.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Łączenie istniejącego obszaru roboczego z subskrypcją platformy Azure
Wszystkie obszary robocze utworzone po 26 września 2016 roku muszą być w czasie tworzenia połączone z subskrypcją platformy Azure. Obszary robocze utworzone wcześniej muszą zostać połączone z obszarem roboczym po zalogowaniu. Jeśli utworzysz obszar roboczy z poziomu witryny Azure Portal lub połączysz obszar roboczy z subskrypcją platformy Azure, usługa Azure Active Directory zostanie połączona jako konto organizacyjne.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Aby połączyć obszar roboczy z subskrypcją platformy Azure w witrynie Azure Portal
1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.  

2. W okienku subskrypcji usługi Log Analytics, kliknij polecenie **Dodaj**.  

    ![lista obszarów roboczych](./media/manage-access/workspace-link-existing-01.png)

3. Z **obszaru roboczego usługi Log Analytics** okienku kliknij **łączenie istniejącego obszaru roboczego**.  

4. Kliknij pozycję **Skonfiguruj wymagane ustawienia**.  

5. Zostanie wyświetlona lista obszarów roboczych, które nie są jeszcze połączone z Twoim kontem platformy Azure. Wybierz obszar roboczy.  
   
6. W razie potrzeby możesz zmienić wartości następujących elementów:
   * Subskrypcja
   * Grupa zasobów
   * Lokalizacja
   * Warstwa cenowa  

7. Kliknij przycisk **OK**. Obszar roboczy jest teraz połączony z kontem platformy Azure.

> [!NOTE]
> Jeśli obszar roboczy, który chcesz połączyć, nie jest wyświetlany, oznacza to, że subskrypcja platformy Azure nie ma dostępu do obszaru roboczego utworzonego przy użyciu portalu pakietu OMS.  Aby udzielić dostępu do tego konta z poziomu portalu pakietu OMS, zobacz sekcję [Dodawanie użytkownika do istniejącego obszaru roboczego](#add-a-user-to-an-existing-workspace).
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Uaktualnianie obszaru roboczego do płatnego planu
Istnieją trzy typy planów obszarów roboczych pakietu OMS: **Bezpłatne**, **autonomiczny**, i **OMS**.  Jeśli używany jest plan *Bezpłatny*, do usługi Log Analytics można wysłać dziennie maksymalnie 500 MB danych.  Aby po przekroczeniu tego limitu móc nadal zbierać dane, musisz zmienić obszar roboczy na plan płatny. Typ planu można zmienić w dowolnym momencie.  Aby uzyskać więcej informacji o cenach pakietu OMS, zobacz [szczegółowe informacje o cenach](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing).

### <a name="using-entitlements-from-an-oms-subscription"></a>Używanie uprawnień z subskrypcji pakietu OMS
Aby używać uprawnień wynikających z zakupu pakietu OMS E1, OMS E2 OMS lub dodatku pakietu OMS dla programu System Center, wybierz plan *OMS* dla usługi Log Analytics pakietu OMS.

Po zakupie subskrypcji pakietu OMS uprawnienia zostaną dodane do umowy Enterprise Agreement. Z tych uprawnień mogą korzystać dowolne subskrypcje platformy Azure utworzone w ramach tej umowy. Wszystkie obszary robocze w ramach tych subskrypcji korzystają z uprawnień pakietu OMS.

Aby upewnić się, że uprawnienia subskrypcji pakietu OMS są uwzględniane w danych użycia obszaru roboczego, wykonaj następujące czynności:

1. Połącz obszar roboczy z subskrypcją platformy Azure w ramach umowy Enterprise Agreement, która obejmuje subskrypcję pakietu OMS

2. Wybierz plan *OMS* dla obszaru roboczego

> [!NOTE]
> Jeśli obszar roboczy został utworzony przed 26 września 2016 r., a plan taryfowy usługi Log Analytics to *Premium*, ten obszar roboczy używa uprawnień z dodatku pakietu OMS dla programu System Center. Uprawnień można także użyć, zmieniając warstwę cenową pakietu *OMS*.
>
>

Uprawnienia subskrypcji pakietu OMS nie są widoczne w witrynie Azure portal. Uprawnienia i użycie są natomiast widoczne w witrynie Enterprise Portal.  

Jeśli chcesz zmienić subskrypcję platformy Azure, z którą jest połączony obszar roboczy, możesz użyć polecenia cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) programu Azure PowerShell.

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Korzystanie z zobowiązania platformy Azure w ramach umowy Enterprise Agreement
Jeśli nie masz subskrypcji pakietu OMS, zapłacisz osobno za każdy składnik tego pakietu, a dane użycia będą widoczne na rachunku dotyczącym platformy Azure.

Jeśli istnieje zobowiązanie pieniężne platformy Azure dotyczące rejestracji przedsiębiorstwa, z którą są połączone subskrypcje platformy Azure, użycie usługi Log Analytics spowoduje automatyczne obciążenie pozostałego zobowiązania pieniężnego.

Jeśli chcesz zmienić subskrypcję platformy Azure, z którą jest połączony obszar roboczy, możesz użyć polecenia cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) programu Azure PowerShell.  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>Zmienianie obszaru roboczego na płatną warstwę cenową w witrynie Azure Portal
1. W witrynie Azure portal w okienku subskrypcji usługi Log Analytics wybierz obszar roboczy.

2. W okienku obszaru roboczego w obszarze **ogólne**, wybierz opcję **warstwa cenowa**.  

3. W obszarze **warstwa cenowa**, wybierz warstwę cenową, a następnie kliknij przycisk **wybierz**.  
    ![Wybrany plan taryfowy](./media/manage-access/workspace-pricing-tier-info.png)

> [!NOTE]
> Jeśli obszar roboczy jest połączony z kontem usługi Automation, przed wybraniem warstwy cenowej *Autonomiczna (za GB)* musisz usunąć wszystkie rozwiązania **Automation and Control** i odłączyć konto usługi Automation. W bloku obszaru roboczego w obszarze **Ogólne** kliknij pozycję **Rozwiązania**, aby wyświetlić i usunąć rozwiązania. Aby odłączyć konto usługi Automation, kliknij nazwę konta usługi Automation w bloku **Warstwa cenowa**.
>
>

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-oms-portal"></a>Zmienianie obszaru roboczego na płatną warstwę cenową w portalu pakietu OMS

Aby zmienić warstwę cenową za pomocą portalu pakietu OMS, musisz mieć subskrypcję platformy Azure.

1. W portalu pakietu OMS kliknij kafelek **Ustawienia**.

2. Kliknij kartę **Konta**, a następnie kliknij kartę **Subskrypcja i plan taryfowy platformy Azure**.

3. Kliknij warstwę cenową, której chcesz użyć.

4. Kliknij pozycję **Zapisz**.  

    ![subskrypcja i plany taryfowe](./media/manage-access/subscription-tab.png)

Nowy plan taryfowy jest wyświetlany na wstążce portalu pakietu OMS w górnej części strony sieci Web.

![Wstążka pakietu OMS](./media/manage-access/data-plan-changed.png)

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [omówienie agenta usługi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) do zbierania danych z komputerów w centrum danych lub w innym środowisku chmury.
* Aby skonfigurować zbieranie danych z maszyn wirtualnych platformy Azure, zobacz [Zbieranie danych dotyczących infrastruktury Azure Virtual Machines](../../azure-monitor/learn/quick-collect-azurevm.md).  
* [Dodaj rozwiązania Log Analytics z galerii rozwiązań](../../azure-monitor/insights/solutions.md), aby dodać funkcje i zebrać dane.

