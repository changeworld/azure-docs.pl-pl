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
ms.date: 02/07/2019
ms.author: magoedte
ms.openlocfilehash: 4a777c2bd57d40b4bb6c8d36c996b655cb019e5f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005374"
---
# <a name="manage-log-analytics-workspaces-in-azure-monitor"></a>Zarządzanie obszarami roboczymi usługi Log Analytics, w usłudze Azure Monitor
Magazyny usługi Azure Monitor możesz rejestrować dane w obszarze roboczym usługi Log Analytics, która jest kontener, który zawiera dane i informacje konfiguracyjne. W celu zarządzania dostępem do danych logowania, należy wykonać różne zadania administracyjne dotyczące obszarów roboczych. Ty i inni członkowie organizacji możecie używać wielu obszarów roboczych, aby zarządzać różnymi zestawami danych zebranymi z całej infrastruktury IT lub jej części.

Aby utworzyć obszar roboczy, trzeba:

1. Mieć subskrypcję platformy Azure.
2. Wybrać nazwę obszaru roboczego.
3. Skojarzyć obszar roboczy z jednym z Twojej subskrypcji i grup zasobów.
4. Wybrać lokalizację geograficzną.

## <a name="determine-the-number-of-workspaces-you-need"></a>Określanie wymaganej liczby obszarów roboczych
Obszar roboczy usługi Log Analytics jest zasobem platformy Azure i kontenerów, w którym dane są zbierane, agregowane, analizowane i znajdujące się w usłudze Azure Monitor.

Może mieć wiele obszarów roboczych na subskrypcję platformy Azure i masz dostęp do więcej niż jednego obszaru roboczego, umożliwia łatwe wyszukiwanie między nimi. W tej sekcji opisano sytuacje, w których pomocne może być utworzenie więcej niż jednego obszaru roboczego.

Obszar roboczy usługi Log Analytics oferuje:

* Lokalizacja geograficzna do przechowywania danych
* Izolacja danych, aby zdefiniować prawa dostępu różnych użytkowników
* Możliwość konfiguracji ustawień, takich jak [warstwy cenowej](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [przechowywania](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) i [danych są takie same](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap) 

Z punktu widzenia użycia firma Microsoft zaleca się utworzenie w kilku obszarów roboczych jak to możliwe. Ułatwia administrowanie i zapytania łatwiej i szybciej. Jednak na podstawie poprzedniego właściwości, warto utworzyć wiele obszarów roboczych, jeśli:

* Są globalne firmy i należy rejestrować — dane przechowywane w określonych regionach ze względów danych suwerenności lub zgodności.
* Korzystasz z platformy Azure i chcesz uniknąć naliczania opłat za transfer danych wychodzących przez umieszczenie obszaru roboczego w tym samym regionie co zarządzane przez niego zasoby platformy Azure.
* Chcesz przydzielić opłaty do różnych działów lub grup biznesowych na podstawie ich użycia, tworząc obszar roboczy dla każdego działu lub grupie biznesowej w jego własnej subskrypcji platformy Azure.
* Jesteś dostawcą usługi zarządzanej i chcesz, aby dane usługi Log Analytics dla klientów, którymi zarządzasz, były odizolowane od danych innych klientów.
* Zarządzasz wieloma klientami i chcesz, aby każdy klient / działu / firm grupy, własnych danych, ale nie danych od innych użytkowników.

Jeśli zbierasz dane przy użyciu agentów systemu Windows, możesz [skonfigurować każdego agenta w celu raportowania do co najmniej jednego obszaru roboczego](../../azure-monitor/platform/agent-windows.md).

Jeśli używasz programu System Center Operations Manager, jedna grupa zarządzania programu Operations Manager może być połączona tylko z jednym obszarem roboczym. Możesz zainstalować program Microsoft Monitoring Agent na komputerach zarządzanych przez program Operations Manager oraz skonfigurować agenta w celu raportowania do programu Operations Manager i innego obszaru roboczego usługi Log Analytics.

## <a name="workspace-information"></a>Informacje o obszarze roboczym
Podczas analizowania danych w obszarze roboczym usługi Log Analytics w **usługi Azure Monitor** menu w witrynie Azure portal, możesz utworzyć i zarządzać obszarami roboczymi w **obszarów roboczych usługi Log Analytics** menu.
 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i kliknij przycisk **wszystkich usług**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **usługi Log Analytics** obszarów roboczych.  

    ![Azure Portal](media/manage-access/azure-portal-01.png)  

3. Wybierz swój obszar roboczy z listy.

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


### <a name="managing-access-to-log-analytics-workspace-using-azure-permissions"></a>Zarządzanie dostępem do obszaru roboczego usługi Log Analytics przy użyciu uprawnień platformy Azure
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

Zaleca się wykonanie przypisań na poziomie zasobów (obszar roboczy), aby zapewnić dokładną kontrolę dostępu.  Za pomocą [ról niestandardowych](../../role-based-access-control/custom-roles.md) możesz utworzyć role z określonymi, wymaganymi uprawnieniami.

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [omówienie agenta usługi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) do zbierania danych z komputerów w centrum danych lub w innym środowisku chmury.
* Aby skonfigurować zbieranie danych z maszyn wirtualnych platformy Azure, zobacz [Zbieranie danych dotyczących infrastruktury Azure Virtual Machines](../../azure-monitor/learn/quick-collect-azurevm.md).  

