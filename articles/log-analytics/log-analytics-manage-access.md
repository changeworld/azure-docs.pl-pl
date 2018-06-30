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
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 3b4e0f978cc7d23d0157b78fd2dff27096d2768b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133017"
---
# <a name="manage-workspaces"></a>Zarządzanie obszarami roboczymi

Dostępem do usługi Log Analytics można zarządzać, wykonując różne zadania administracyjne dotyczące obszarów roboczych. Ten artykuł zawiera opis najlepszych rozwiązań i procedur z zakresu zarządzania obszarami roboczymi. Obszar roboczy to kontener, który zawiera informacje o koncie i podstawowe informacje o konfiguracji konta. Ty i inni członkowie organizacji możecie używać wielu obszarów roboczych, aby zarządzać różnymi zestawami danych zebranymi z całej infrastruktury IT lub jej części.

Aby utworzyć obszar roboczy, trzeba:

1. Mieć subskrypcję platformy Azure.
2. Wybrać nazwę obszaru roboczego.
3. Kojarzenie obszaru roboczego z jednym z Twojej subskrypcji i grup zasobów.
4. Wybrać lokalizację geograficzną.

## <a name="determine-the-number-of-workspaces-you-need"></a>Określanie wymaganej liczby obszarów roboczych
Obszar roboczy jest zasobem platformy Azure w postaci kontenera, w którym dane są zbierane, agregowane, analizowane i przedstawiane w witrynie Azure Portal.

Możesz mieć wiele obszarów roboczych na subskrypcję platformy Azure i dostęp do więcej niż jednego obszaru roboczego, aby łatwo wykonywać zapytania obejmujące różne obszary. W tej sekcji opisano sytuacje, w których pomocne może być utworzenie więcej niż jednego obszaru roboczego.

Obecnie obszar roboczy oferuje następujące możliwości:

* Lokalizacja geograficzna do przechowywania danych
* Izolacja danych, aby zdefiniować prawa dostępu różnych użytkowników
* Zakres do konfiguracji ustawień, takich jak przechowywania i dane są takie same

Z punktu widzenia zużycia zaleca się tworzenia jako kilka obszarów roboczych, jak to możliwe. Ułatwia zarządzanie i wykonywania zapytań łatwiejsze i szybszy. Jednak, w oparciu o poprzednim właściwości, można utworzyć wiele obszarów roboczych, jeśli:

* Globalna firma wymaga, aby jej dane były przechowywane w określonych regionach w celu zachowania poufności danych i ich zgodności z przepisami.
* Korzystasz z platformy Azure i chcesz uniknąć naliczania opłat za transfer danych wychodzących przez umieszczenie obszaru roboczego w tym samym regionie co zarządzane przez niego zasoby platformy Azure.
* Chcesz przydzielić opłaty do różnych działów lub grup biznesowych na podstawie ich użycia. Po utworzeniu obszaru roboczego dla każdego działu lub grupie biznesowej w jego własnej subskrypcji platformy Azure.
* Jesteś dostawcą usługi zarządzanej i chcesz, aby dane usługi Log Analytics dla klientów, którymi zarządzasz, były odizolowane od danych innych klientów.
* Zarządzasz wieloma klientami i chcesz, aby dla poszczególnych klientów, działów i grup biznesowych były widoczne ich własne dane, ale nie dane innych klientów, działów czy grup biznesowych.

Jeśli zbierasz dane przy użyciu agentów systemu Windows, możesz [skonfigurować każdego agenta w celu raportowania do co najmniej jednego obszaru roboczego](log-analytics-windows-agents.md).

Jeśli używasz programu System Center Operations Manager, jedna grupa zarządzania programu Operations Manager może być połączona tylko z jednym obszarem roboczym. Możesz zainstalować program Microsoft Monitoring Agent na komputerach zarządzanych przez program Operations Manager oraz skonfigurować agenta w celu raportowania do programu Operations Manager i innego obszaru roboczego usługi Log Analytics.

### <a name="workspace-information"></a>Informacje o obszarze roboczym

Szczegółowe informacje o obszarze roboczym można wyświetlić w witrynie Azure Portal. 

#### <a name="view-workspace-information-in-the-azure-portal"></a>Wyświetlanie informacji o obszarze roboczym w witrynie Azure Portal

1. Jeśli nie zostało to jeszcze zrobione, zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu subskrypcji platformy Azure.
2. W menu **Centrum** kliknij pozycję **Więcej usług** i na liście zasobów wpisz ciąg **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Kliknij pozycję **Log Analytics**.  
    ![Centrum platformy Azure](./media/log-analytics-manage-access/hub.png)  
3. Wybierz obszar roboczy w bloku subskrypcji usługi Log Analytics.
4. Blok obszaru roboczego zawiera szczegółowe dane dotyczące obszaru roboczego i linki do dodatkowych informacji.  
    ![szczegóły obszaru roboczego](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Zarządzanie kontami i użytkownikami
Każdego obszaru roboczego może mieć wiele skojarzonych z nim kont, a każde konto mogą uzyskiwać dostęp do wielu obszarów roboczych. Dostęp jest zarządzana za pośrednictwem [dostępu opartej na rolach na platformie Azure](../active-directory/role-based-access-control-configure.md). Te prawa dostępu ma zastosowanie zarówno w portalu Azure, jak i na dostęp do interfejsu API.


Następujące działania również wymagają uprawnień platformy Azure:

| Akcja                                                          | Wymagane uprawnienia platformy Azure | Uwagi |
|-----------------------------------------------------------------|--------------------------|-------|
| Dodawanie i usuwanie rozwiązań do zarządzania                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Te uprawnienia należy nadać na poziomie grupy zasobów lub subskrypcji. |
| Zmienianie warstwy cenowej                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Wyświetlanie danych w kafelkach rozwiązań *Backup* i *Site Recovery* | Administrator/współadministrator | Uzyskuje dostęp do zasobów wdrożonych przy użyciu klasycznego modelu wdrażania |
| Tworzenie obszaru roboczego w witrynie Azure Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Zarządzanie dostępem do usługi Log Analytics przy użyciu uprawnień platformy Azure
Aby udzielić dostępu do obszaru roboczego usługi Log Analytics przy użyciu uprawnień platformy Azure, wykonaj kroki opisane w części [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../active-directory/role-based-access-control-configure.md).

Platforma Azure ma dwie wbudowane role użytkownika w usłudze Log Analytics:
- Czytelnik usługi Log Analytics
- Współautor usługi Log Analytics

Członkowie roli *Czytelnik usługi Log Analytics* mogą wykonywać następujące czynności:
- Wyświetlanie i wyszukiwanie wszystkich monitorowanych danych 
- Wyświetlanie ustawień monitorowania, w tym konfiguracji diagnostyki platformy Azure, dla wszystkich zasobów platformy Azure

Rola Log Analytics Reader obejmuje Azure następujące akcje:

| Typ    | Uprawnienie | Opis |
| ------- | ---------- | ----------- |
| Akcja | `*/read`   | Zdolność do wyświetlania wszystkich zasobów systemu Azure i konfigurację zasobu. Obejmuje wyświetlanie następujących elementów: <br> Stan rozszerzenia maszyny wirtualnej <br> Konfiguracja diagnostyki platformy Azure dla zasobów <br> Wszystkie właściwości i ustawienia wszystkich zasobów |
| Akcja | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Możliwość wykonywania zapytań przeszukiwania dzienników w wersji 2 |
| Akcja | `Microsoft.OperationalInsights/workspaces/search/action` | Możliwość wykonywania zapytań przeszukiwania dzienników w wersji 1 |
| Akcja | `Microsoft.Support/*` | Możliwość otwierania zgłoszeń do pomocy technicznej |
|Inne | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Zapobiega odczytu obszaru roboczego klucz wymagany do używania zbierania danych interfejsu API i zainstaluj agentów. Zapobiega to użytkownik Dodawanie nowych zasobów do obszaru roboczego |


Członkowie roli *Współautor usługi Log Analytics* mogą wykonywać następujące czynności:
- Odczytać wszystkich danych monitorowania, jak czytnik analizy dziennika  
- Tworzenie i konfigurowanie kont usługi Automation  
- Dodawanie i usuwanie rozwiązań do zarządzania    
    > [!NOTE] 
    > Aby pomyślnie wykonać akcje ostatnie dwa, to uprawnienie powinno być przyznane na poziomie grupy lub subskrypcji zasobu.  

- Odczytywanie kluczy kont magazynu   
- Konfigurowanie kolekcji dzienników z usługi Azure Storage  
- Edytowanie ustawień monitorowania dla zasobów platformy Azure, w tym:
  - Dodawanie rozszerzenia do maszyn wirtualnych
  - Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure

> [!NOTE] 
> Możliwości dodania rozszerzenia do maszyny wirtualnej możesz użyć, aby zyskać pełną kontrolę nad maszyną wirtualną.

Rola współautora analizy dziennika zawiera Azure następujące akcje:

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

Zaleca się wykonanie przypisań na poziomie zasobów (obszar roboczy), aby zapewnić dokładną kontrolę dostępu.  Za pomocą [ról niestandardowych](../active-directory/role-based-access-control-custom-roles.md) możesz utworzyć role z określonymi, wymaganymi uprawnieniami.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Łączenie istniejącego obszaru roboczego z subskrypcją platformy Azure
Wszystkie obszary robocze utworzone po 26 września 2016 roku muszą być w czasie tworzenia połączone z subskrypcją platformy Azure. Obszary robocze utworzone wcześniej muszą zostać połączone z obszarem roboczym po zalogowaniu. Jeśli utworzysz obszar roboczy z poziomu witryny Azure Portal lub połączysz obszar roboczy z subskrypcją platformy Azure, usługa Azure Active Directory zostanie połączona jako konto organizacyjne.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Aby połączyć obszar roboczy z subskrypcją platformy Azure w witrynie Azure Portal
1. Zaloguj się do [Azure Portal](http://portal.azure.com).
2. Wyszukaj pozycję **Log Analytics** i wybierz ją.
3. Zostanie wyświetlona lista istniejących obszarów roboczych. Kliknij pozycję **Add** (Dodaj).  
   ![lista obszarów roboczych](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. W obszarze **Obszar roboczy pakietu OMS** kliknij pozycję **Lub połącz istniejący**.  
   ![łączenie istniejącego obszaru roboczego](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Kliknij pozycję **Skonfiguruj wymagane ustawienia**.  
   ![konfigurowanie wymaganych ustawień](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Zostanie wyświetlona lista obszarów roboczych, które nie są jeszcze połączone z Twoim kontem platformy Azure. Wybierz obszar roboczy.  
   ![wybieranie obszarów roboczych](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. W razie potrzeby możesz zmienić wartości następujących elementów:
   * Subskrypcja
   * Grupa zasobów
   * Lokalizacja
   * Warstwa cenowa  
     ![zmienianie wartości](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Kliknij przycisk **OK**. Obszar roboczy jest teraz połączony z kontem platformy Azure.

> [!NOTE]
> Jeśli obszar roboczy, który chcesz połączyć, nie jest wyświetlany, oznacza to, że subskrypcja platformy Azure nie ma dostępu do obszaru roboczego utworzonego przy użyciu portalu pakietu OMS.  Aby udzielić dostępu do tego konta z poziomu portalu pakietu OMS, zobacz sekcję [Dodawanie użytkownika do istniejącego obszaru roboczego](#add-a-user-to-an-existing-workspace).
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Uaktualnianie obszaru roboczego do płatnego planu
Dostępne są trzy typy planów obszarów roboczych dla pakietu OMS: **Bezpłatny**, **Autonomiczny** i **OMS**.  Jeśli używany jest plan *Bezpłatny*, do usługi Log Analytics można wysłać dziennie maksymalnie 500 MB danych.  Aby po przekroczeniu tego limitu móc nadal zbierać dane, musisz zmienić obszar roboczy na plan płatny. Typ planu można zmienić w dowolnym momencie.  Aby uzyskać więcej informacji o cenach pakietu OMS, zobacz [szczegółowe informacje o cenach](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing).

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

Uprawnienia subskrypcji pakietu OMS nie są widoczne w witrynie Azure Portal lub w portalu pakietu OMS. Uprawnienia i użycie są natomiast widoczne w witrynie Enterprise Portal.  

Jeśli chcesz zmienić subskrypcję platformy Azure, z którą jest połączony obszar roboczy, możesz użyć polecenia cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) programu Azure PowerShell.

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Korzystanie z zobowiązania platformy Azure w ramach umowy Enterprise Agreement
Jeśli nie masz subskrypcji pakietu OMS, zapłacisz osobno za każdy składnik tego pakietu, a dane użycia będą widoczne na rachunku dotyczącym platformy Azure.

Jeśli istnieje zobowiązanie pieniężne platformy Azure dotyczące rejestracji przedsiębiorstwa, z którą są połączone subskrypcje platformy Azure, użycie usługi Log Analytics spowoduje automatyczne obciążenie pozostałego zobowiązania pieniężnego.

Jeśli chcesz zmienić subskrypcję platformy Azure, z którą jest połączony obszar roboczy, możesz użyć polecenia cmdlet [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) programu Azure PowerShell.  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>Zmienianie obszaru roboczego na płatną warstwę cenową w witrynie Azure Portal
1. Zaloguj się do [Azure Portal](http://portal.azure.com).
2. Wyszukaj pozycję **Log Analytics** i wybierz ją.
3. Zostanie wyświetlona lista istniejących obszarów roboczych. Wybierz obszar roboczy.  
4. W bloku obszaru roboczego w obszarze **Ogólne** kliknij pozycję **Warstwa cenowa**.  
5. W obszarze **Warstwa cenowa** kliknij warstwę cenową, a następnie kliknij przycisk **Wybierz**.  
    ![wybieranie planu](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6. Gdy odświeżysz widok w witrynie Azure Portal, zostanie wyświetlony zaktualizowany obszar **Warstwa cenowa** dla wybranej warstwy.  
    ![zaktualizowany plan](./media/log-analytics-manage-access/manage-access-change-plan04.png)

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
   ![subskrypcja i plany taryfowe](./media/log-analytics-manage-access/subscription-tab.png)

Nowy plan taryfowy jest wyświetlany na wstążce portalu pakietu OMS w górnej części strony sieci Web.

![Wstążka pakietu OMS](./media/log-analytics-manage-access/data-plan-changed.png)


## <a name="change-how-long-log-analytics-stores-data"></a>Zmiana czasu przechowywania danych przez usługę Log Analytics

W warstwie cenowej Bezpłatna usługa Log Analytics udostępnia dane z ostatnich siedmiu dni.
W warstwie cenowej Standardowa usługa Log Analytics udostępnia dane z ostatnich 30 dni.
W warstwie cenowej Premium usługa Log Analytics udostępnia dane z ostatnich 365 dni.
W warstwach cenowych Autonomiczna i OMS usługa Log Analytics domyślnie udostępnia dane z ostatnich 31 dni.

Korzystając z warstw cenowych Autonomiczna i OMS, możesz przechowywać dane z maksymalnie 2 lat (730 dni). Za dane przechowywane dłużej niż domyślne 31 dni jest naliczana opłata za przechowywanie danych. Aby uzyskać więcej informacji na temat cen, zobacz [opłaty za użycie nadwyżkowe](https://azure.microsoft.com/pricing/details/log-analytics/).

Aby zmienić czas przechowywania danych, zobacz [Manage cost by controlling data volume and retention in Log Analytics (Zarządzanie kosztami przez kontrolowanie ilości danych i czasu ich przechowywania w usłudze Log Analytics)](log-analytics-manage-cost-storage.md).


## <a name="delete-a-log-analytics-workspace"></a>Usuwanie obszaru roboczego usługi Log Analytics
Gdy usuniesz obszar roboczy usługi Log Analytics, wszystkie powiązane z nim dane zostaną usunięte z usługi Log Analytics w ciągu 30 dni.

Jeśli jesteś administratorem i z tym obszarem roboczym jest skojarzonych wielu użytkowników, skojarzenie między tymi użytkownikami i obszarem roboczym zostanie przerwane. Jeśli użytkownicy są skojarzeni z innymi obszarami roboczymi, mogą nadal z nich korzystać w usłudze Log Analytics. Jeśli jednak nie są oni skojarzeni z innymi obszarami roboczymi, muszą utworzyć obszar roboczy, aby korzystać z usługi. Aby usunąć obszar roboczy, zobacz [Usuwanie obszaru roboczego usługi Azure Log Analytics](log-analytics-manage-del-workspace.md).

## <a name="next-steps"></a>Kolejne kroki
* Aby zbierać dane z komputerów w centrum danych lub z innego środowiska chmury, zobacz [Collect data from computers in your environment with Log Analytics (Zbieranie danych z komputerów w używanym środowisku za pomocą usługi Log Analytics)](log-analytics-concept-hybrid.md).
* Aby skonfigurować zbieranie danych z maszyn wirtualnych platformy Azure, zobacz [Zbieranie danych dotyczących infrastruktury Azure Virtual Machines](log-analytics-quick-collect-azurevm.md).  
* [Dodaj rozwiązania Log Analytics z galerii rozwiązań](log-analytics-add-solutions.md), aby dodać funkcje i zebrać dane.

