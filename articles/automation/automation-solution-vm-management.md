---
title: Uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu rozwiązania
description: To rozwiązanie do zarządzania maszyną Wirtualną uruchamia i zatrzymuje maszyny wirtualne usługi Azure Resource Manager zgodnie z harmonogramem i aktywnie monitoruje od usługi Log Analytics.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9abdb17c7b1cf0662913a4c9cd59c49efc91e419
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785498"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu rozwiązania w usłudze Azure Automation

Uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu rozwiązania rozpoczyna się i zatrzymuje maszyny wirtualne, usługi platformy Azure zgodnie z harmonogramami zdefiniowanych przez użytkownika, dają wgląd za pomocą usługi Azure Log Analytics i wysyła opcjonalne wiadomości e-mail przy użyciu [grup akcji](../monitoring-and-diagnostics/monitoring-action-groups.md). Obsługuje ona usługi Azure Resource Manager i klasycznych maszyn wirtualnych w przypadku większości scenariuszy.

To rozwiązanie udostępnia opcję zdecentralizowane automatyzacji niskie koszty dla użytkowników, którzy chcesz zoptymalizować swoje koszty maszyn wirtualnych. Dzięki temu rozwiązaniu można wykonywać następujące czynności:

- Zaplanuj uruchamianie i zatrzymywanie maszyn wirtualnych.
- Planowanie maszyn wirtualnych do uruchamiania i zatrzymywania w kolejności rosnącej przy użyciu tagów Azure (nieobsługiwane dla klasycznych maszyn wirtualnych).
- AUTOSTOP maszyny wirtualne w oparciu o niskim użyciu procesora CPU.

Ograniczenia związane z bieżącego rozwiązania są następujące:

- To rozwiązanie zarządza VMs w dowolnym regionie, ale można używać tylko w tej samej subskrypcji co konto usługi Azure Automation.
- To rozwiązanie jest dostępne na platformie Azure i AzureGov do dowolnego regionu, który obsługuje obszar roboczy usługi Log Analytics, konto usługi Azure Automation i alertach. Regiony AzureGov aktualnie nie obsługują funkcje poczty e-mail.

> [!NOTE]
> Jeśli używasz rozwiązania dla klasycznych maszyn wirtualnych, następnie wszystkie maszyny wirtualne będą przetwarzane sekwencyjnie na usługę w chmurze. Przetwarzanie równoległe zadania jest w dalszym ciągu będzie obsługiwana w różnych usługach w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

Elementy runbook dla tego rozwiązania, pracować [konta Uruchom jako platformy](automation-create-runas-account.md). Konto Uruchom jako jest preferowaną metodą uwierzytelniania, ponieważ używa ona uwierzytelniania certyfikatu zamiast hasła, które może być wygaśnięcie lub ulegać częstym zmianom.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Wykonaj poniższe kroki, aby dodać uruchamianie/zatrzymywanie maszyn wirtualnych podczas szczytu rozwiązania do konta usługi Automation, a następnie skonfiguruj zmienne, aby dostosować rozwiązanie.

1. Wybierz z konta usługi Automation **uruchamianie/zatrzymywanie maszyn wirtualnych** w obszarze **powiązane zasoby**. W tym miejscu możesz kliknąć pozycję **Dowiedz się więcej o, a następnie włącz rozwiązanie**. Jeśli masz już wdrożone rozwiązanie uruchamianie/zatrzymywanie maszyn wirtualnych, można wybrać ją, klikając **Zarządzanie rozwiązaniem** i umieszczona na liście.

   ![Korzystanie z konta usługi automation](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Można również utworzyć go z dowolnego miejsca w witrynie Azure portal, klikając **Utwórz zasób**. Na stronie portalu Marketplace, wpisz słowo kluczowe, taką jak **Start** lub **uruchomień/zatrzymań**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Alternatywnie możesz wpisać jeden lub więcej słów kluczowych z pełną nazwę rozwiązania i naciśnij klawisz Enter. Wybierz **uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu** w wynikach wyszukiwania.
2. W **uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu** dla wybranego rozwiązania strony, przejrzyj dane podsumowania, a następnie kliknij przycisk **Utwórz**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

3. **Dodaj rozwiązanie** zostanie wyświetlona strona. Monit o skonfigurowanie rozwiązania przed zaimportowaniem do subskrypcji usługi Automation.

   ![Strona Dodaj rozwiązanie zarządzania maszyny Wirtualnej](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Na **Dodaj rozwiązanie** wybierz opcję **obszaru roboczego**. Wybierz obszar roboczy usługi Log Analytics, która jest połączona z tą samą subskrypcją platformy Azure, w której znajduje się konto usługi Automation. Jeśli nie masz obszaru roboczego wybierz **Utwórz nowy obszar roboczy**. Na **obszar roboczy usługi Log Analytics** strony, wykonaj następujące czynności:
   - Określ nazwę dla nowego **obszar roboczy usługi Log Analytics**.
   - Wybierz **subskrypcji** się połączyć, wybierając z listy rozwijanej, jeśli nie jest domyślnie wybrana.
   - Aby uzyskać **grupy zasobów**, można utworzyć nową grupę zasobów lub wybrać istniejącą grupę.
   - Wybierz **lokalizację**. Obecnie jedynymi dostępnymi lokalizacjami są **Australia południowo-wschodnia**, **Kanada Środkowa**, **Indie środkowe**, **wschodnie stany USA**, **Japonia, część wschodnia**, **Azja południowo-wschodnia**, **Południowe Zjednoczone Królestwo**, i **Europa Zachodnia**.
   - Wybierz **warstwę cenową**. Wybierz **na GB (autonomiczne)** opcji. Usługa log Analytics został zaktualizowany [ceny](https://azure.microsoft.com/pricing/details/log-analytics/) i warstwie na GB jest jedyną opcją.

5. Po podaniu wymaganych informacji w **obszaru roboczego usługi Log Analytics** kliknij **Utwórz**. Możesz śledzić postęp w sekcji **powiadomienia** z menu, która zwraca do **Dodaj rozwiązanie** strony po zakończeniu.
6. Na **Dodaj rozwiązanie** wybierz opcję **konta usługi Automation**. Jeśli tworzysz nowy obszar roboczy usługi Log Analytics, możesz utworzyć nowe konto usługi Automation, który ma zostać skojarzony z nim lub wybierz istniejące konto usługi Automation, która nie jest już połączona z obszarem roboczym usługi Log Analytics. Wybierz istniejące konto usługi Automation, lub kliknij przycisk **Tworzenie konta usługi Automation**, a następnie na **Dodawanie konta usługi Automation** Podaj następujące informacje:
   - W polu **Nazwa** wprowadź nazwę konta usługi Automation.

    Wszystkie inne opcje są wypełniane automatycznie na podstawie na wybrany obszar roboczy usługi Log Analytics. Nie można zmodyfikować tych opcji. Konto Uruchom jako platformy Azure jest domyślną metodą uwierzytelniania dla elementów Runbook zawartych w tym rozwiązaniu. Po kliknięciu **OK**, opcje konfiguracji zostaną sprawdzone i utworzeniu konta usługi Automation. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

7. Na koniec na **Dodaj rozwiązanie** wybierz opcję **konfiguracji**. **Parametry** zostanie wyświetlona strona.

   ![Strona parametrów dla rozwiązania](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   W tym miejscu zostanie wyświetlony monit:
   - Określ **docelowa grupa zasobów nazwy**. Te wartości są nazwy grup zasobów zawierających maszyny wirtualne mają być zarządzane przez to rozwiązanie. Można wprowadzić więcej niż jedną nazwę i oddzielić je za pomocą przecinka (wartości nie jest rozróżniana). Użycie symbolu wieloznacznego jest obsługiwane. Możesz skorzystać z tej opcji, jeśli chcesz uwzględnić maszyny wirtualne we wszystkich grupach zasobów w subskrypcji. Ta wartość jest przechowywana w **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames** zmiennych.
   - Określ **listę wykluczeń maszyny Wirtualnej (ciąg)**. Ta wartość jest nazwa co najmniej jednej maszyny wirtualnej z docelową grupę zasobów. Można wprowadzić więcej niż jedną nazwę i oddzielić je za pomocą przecinka (wartości nie jest rozróżniana). Użycie symbolu wieloznacznego jest obsługiwane. Ta wartość jest przechowywana w **External_ExcludeVMNames** zmiennej.
   - Wybierz **harmonogram**. Ta wartość jest cyklicznej daty i godziny uruchamiania oraz zatrzymywania maszyn wirtualnych w docelowych grupach zasobów. Domyślnie harmonogram jest skonfigurowany w ciągu 30 minut od teraz. Wybierając inny region jest niedostępny. Aby skonfigurować harmonogram do określonej strefy czasowej po skonfigurowaniu rozwiązania, zobacz [modyfikowanie harmonogramu uruchamiania i zamykania](#modify-the-startup-and-shutdown-schedule).
   - Aby otrzymać **wiadomości E-mail z powiadomieniami** z grupy akcji, zaakceptuj wartość domyślną **tak** i podaj prawidłowy adres e-mail. Jeśli wybierzesz **nie** , ale później zdecydujesz w późniejszym terminie, że chcesz otrzymywać powiadomień pocztą e-mail, możesz zaktualizować [grupy akcji](../monitoring-and-diagnostics/monitoring-action-groups.md) utworzonego za pomocą prawidłowych adresów e-mail rozdzielonych przecinkami. Należy również włączyć następujące reguły alertu:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Wartością domyślną dla **nazwy grupy zasobów obiektów docelowych** jest **&ast;**. Jest przeznaczony dla wszystkich maszyn wirtualnych w ramach subskrypcji. Jeśli nie chcesz, aby rozwiązanie pod kątem wszystkich maszyn wirtualnych w ramach subskrypcji, ta wartość musi zostać zaktualizowany do listy nazwy grupy zasobów przed włączeniem harmonogramy.

8. Po skonfigurowaniu ustawień początkowych wymaganych dla rozwiązania, kliknij przycisk **OK** zamknąć **parametry** strony i wybierz **Utwórz**. Po wszystkie ustawienia zostaną zweryfikowane, rozwiązanie jest wdrożone do subskrypcji. Ten proces może potrwać kilka sekund, aby zakończyć i śledzić postęp w obszarze **powiadomienia** z menu.

## <a name="scenarios"></a>Scenariusze

Rozwiązanie zawiera trzy różne scenariusze. Te scenariusze są następujące:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scenariusz 1: Uruchamianie/zatrzymywanie maszyn wirtualnych zgodnie z harmonogramem

Ten scenariusz jest domyślna konfiguracja przy pierwszym wdrożeniu rozwiązania. Na przykład można skonfigurować go w celu zatrzymania wszystkich maszyn wirtualnych w ramach subskrypcji, po opuszczeniu pracy wieczorem i uruchom je rano, gdy się ponownie w biurze. Po skonfigurowaniu harmonogramy **StartVM zaplanowane** i **StopVM zaplanowane** podczas wdrażania, mogą uruchamiać i zatrzymywać docelowych maszyn wirtualnych. Konfigurowanie tego rozwiązania, można po prostu zatrzymywanie maszyn wirtualnych jest obsługiwana, zobacz [modyfikować harmonogramy uruchamiania i zamykania](#modify-the-startup-and-shutdown-schedules) dowiesz się, jak skonfigurować harmonogram niestandardowy.

> [!NOTE]
> Strefa czasowa jest Twojej bieżącej strefy czasowej po skonfigurowaniu harmonogramu parametru czasu. Jednakże są przechowywane w formacie UTC w usłudze Azure Automation. Nie trzeba wykonać żadnych konwersji strefy czasowej, jak odbywa się to podczas wdrażania.

Możesz kontrolować, jakie maszyny wirtualne znajdują się w zakresie, konfigurując następujące zmienne: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, i **External_ ExcludeVMNames**.

Aby umożliwić przeznaczonych dla akcji względem subskrypcji i grupy zasobów lub przeznaczonych dla określonej listy maszyn wirtualnych, ale nie oba.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Uruchamianie i zatrzymywanie działania względem subskrypcji i grupie zasobów docelowych

1. Konfigurowanie **External_Stop_ResourceGroupNames** i **External_ExcludeVMNames** zmiennych do określania docelowych maszyn wirtualnych.
1. Włącz i zaktualizuj **StartVM zaplanowane** i **StopVM zaplanowane** harmonogramów.
1. Uruchom **ScheduledStartStop_Parent** element runbook z parametrem akcji ustawionym na **start** i ustaw parametr WHATIF **True** nad wersją zapoznawczą zmiany.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Docelowy akcji uruchamianie i zatrzymywanie przez listy maszyn wirtualnych

1. Uruchom **ScheduledStartStop_Parent** runbook za pomocą parametru akcji równa **start**, Dodaj rozdzielaną przecinkami listę maszyn wirtualnych w *VMList* parametru, a następnie ustaw Parametr WHATIF **True**. Przejrzyj wprowadzone zmiany.
1. Konfigurowanie **External_ExcludeVMNames** parametrem rozdzielana przecinkami lista maszyn wirtualnych (maszyny VM1, VM2, VM3).
1. W tym scenariuszu nie uznaje **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupnames** zmiennych. W tym scenariuszu musisz utworzyć swój własny harmonogram automatyzacji. Aby uzyskać więcej informacji, zobacz [Planowanie elementu runbook w usłudze Azure Automation](../automation/automation-schedules.md).

> [!NOTE]
> Wartość **nazwy grupy zasobów obiektów docelowych** jest przechowywany jako wartość dla obu **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames**. Dla dalszych szczegółowości można zmodyfikować każde z tych zmiennych pod kątem różnych grupach zasobów. W przypadku akcji uruchamiania, użyj **External_Start_ResourceGroupNames**w przypadku Akcja zatrzymania, użyj **External_Stop_ResourceGroupNames**. Maszyny wirtualne są automatycznie dodawane do uruchomienia i zatrzymania harmonogramów.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scenariusz 2: Uruchamianie/zatrzymywanie maszyn wirtualnych w kolejności przy użyciu tagów

W środowisku zawierającym co najmniej dwóch składników na wielu maszynach wirtualnych, obsługi rozproszonego obciążenia pracą ważne jest Obsługa sekwencji, w którym składniki są uruchomione i zatrzymane w kolejności. W tym scenariuszu można osiągnąć, wykonując następujące czynności:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Uruchamianie i zatrzymywanie działania względem subskrypcji i grupie zasobów docelowych

1. Dodaj **sequencestart** i **sequencestop** tag z dodatnią liczbą całkowitą do maszyn wirtualnych, które są wykrywane i usuwane **External_Start_ResourceGroupNames** i  **External_Stop_ResourceGroupNames** zmiennych. Uruchamianie i zatrzymywanie działania są wykonywane w kolejności rosnącej. Aby dowiedzieć się, jak oznaczyć Maszynę wirtualną, zobacz [oznaczyć maszynę wirtualną Windows na platformie Azure](../virtual-machines/windows/tag.md) i [oznaczyć maszynę wirtualną systemu Linux na platformie Azure](../virtual-machines/linux/tag.md).
1. Modyfikuj harmonogramy **Sequenced StartVM** i **Sequenced StopVM** do daty i godziny, który zgodnie z wymaganiami i włączyć harmonogram.
1. Uruchom **SequencedStartStop_Parent** element runbook z parametrem akcji ustawionym na **start** i ustaw parametr WHATIF **True** nad wersją zapoznawczą zmiany.
1. Wyświetlić podgląd akcji, a następnie wprowadź niezbędne zmiany przed zaimplementowaniem względem maszyn wirtualnych w środowisku produkcyjnym. Gdy gotowy, ręcznie umożliwia uruchomienie elementu runbook z parametrem ustawionym na **False**, lub pozwól, aby harmonogram Automation **Sequenced StartVM** i **Sequenced StopVM** uruchamiania automatycznie zgodnie z ustalonym harmonogramem.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Docelowy akcji uruchamianie i zatrzymywanie przez listy maszyn wirtualnych

1. Dodaj **sequencestart** i **sequencestop** tag z dodatnią liczbą całkowitą na maszyny wirtualne mają zostać dodane do **VMList** zmiennej. 
1. Uruchom **SequencedStartStop_Parent** runbook za pomocą parametru akcji równa **start**, Dodaj rozdzielaną przecinkami listę maszyn wirtualnych w *VMList* parametru, a następnie ustaw Parametr WHATIF **True**. Przejrzyj wprowadzone zmiany.
1. Konfigurowanie **External_ExcludeVMNames** parametrem rozdzielana przecinkami lista maszyn wirtualnych (maszyny VM1, VM2, VM3).
1. W tym scenariuszu nie uznaje **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupnames** zmiennych. W tym scenariuszu musisz utworzyć swój własny harmonogram automatyzacji. Aby uzyskać więcej informacji, zobacz [Planowanie elementu runbook w usłudze Azure Automation](../automation/automation-schedules.md).
1. Wyświetlić podgląd akcji, a następnie wprowadź niezbędne zmiany przed zaimplementowaniem względem maszyn wirtualnych w środowisku produkcyjnym. Po gotowy, ręcznie wykonać monitorowania i diagnostyki/monitorowania — Akcja groupsrunbook z parametrem ustawionym na **False**, lub pozwól, aby harmonogram Automation **Sequenced StartVM** i **Sequenced StopVM** Uruchom automatycznie zgodnie z ustalonym harmonogramem.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Scenariusz 3: Uruchamianie/zatrzymywanie automatycznie na podstawie użycia procesora CPU

To rozwiązanie pomaga zarządzać koszt uruchamiania maszyn wirtualnych w ramach subskrypcji, oceniając maszyn wirtualnych platformy Azure, które nie są używane w okresach poza szczytem, takie jak po godzinach, i automatycznie ich zamknięcie Jeśli użycie procesora wynosi mniej niż x %.

Domyślnie rozwiązanie jest wstępnie skonfigurowana pod kątem oceny metrykę Procesora procent, aby sprawdzić, czy średnie wykorzystanie jest 5 procent lub mniej. W tym scenariuszu jest kontrolowana przez następujące zmienne i mogą być modyfikowane, jeśli wartości domyślne nie spełniają wymagań:

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

Aby umożliwić przeznaczonych dla akcji względem subskrypcji i grupy zasobów lub przeznaczonych dla określonej listy maszyn wirtualnych, ale nie oba.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Akcja zatrzymania względem subskrypcji i grupie zasobów docelowych

1. Konfigurowanie **External_Stop_ResourceGroupNames** i **External_ExcludeVMNames** zmiennych do określania docelowych maszyn wirtualnych.
1. Włącz i zaktualizuj **Schedule_AutoStop_CreateAlert_Parent** harmonogramu.
1. Uruchom **AutoStop_CreateAlert_Parent** element runbook z parametrem akcji ustawionym na **start** i ustaw parametr WHATIF **True** nad wersją zapoznawczą zmiany.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Docelowy akcji uruchamianie i zatrzymywanie przez listy maszyn wirtualnych

1. Uruchom **AutoStop_CreateAlert_Parent** runbook za pomocą parametru akcji równa **start**, Dodaj rozdzielaną przecinkami listę maszyn wirtualnych w *VMList* parametru, a następnie ustaw Parametr WHATIF **True**. Przejrzyj wprowadzone zmiany.
1. Konfigurowanie **External_ExcludeVMNames** parametrem rozdzielana przecinkami lista maszyn wirtualnych (maszyny VM1, VM2, VM3).
1. W tym scenariuszu nie uznaje **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupnames** zmiennych. W tym scenariuszu musisz utworzyć swój własny harmonogram automatyzacji. Aby uzyskać więcej informacji, zobacz [Planowanie elementu runbook w usłudze Azure Automation](../automation/automation-schedules.md).

Teraz, gdy harmonogram zatrzymywanie maszyn wirtualnych na podstawie użycia procesora CPU, musisz włączyć jedną z następujących harmonogramów, aby je uruchomić.

- Docelowy akcji uruchamiania przez subskrypcję i grupę zasobów. Zobacz kroki w [scenariusz 1](#scenario-1-startstop-vms-on-a-schedule) do testowania i włączanie **StartVM zaplanowane** harmonogramów.
- Docelowy uruchomienie działania według subskrypcji, grupy zasobów i tagu. Zobacz kroki w [scenariuszu 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) do testowania i włączanie **Sequenced StartVM** harmonogramów.

## <a name="solution-components"></a>Składniki rozwiązania

To rozwiązanie oferuje wstępnie skonfigurowane elementów runbook, harmonogramy i integracji z usługą Log Analytics, dzięki czemu można dostosować, uruchamiania i zamykania maszyn wirtualnych do potrzeb biznesowych.

### <a name="runbooks"></a>Elementy Runbook

Poniższa tabela zawiera listę elementów runbook, wdrożone do konta usługi Automation przy użyciu tego rozwiązania. Nie należy wprowadzać zmian w kodzie elementu runbook. Zamiast tego należy napisać własnego elementu runbook dla nowych funkcji.

> [!IMPORTANT]
> Nie należy bezpośrednio uruchamiać każdego elementu runbook za pomocą "podrzędny" dołączonym do jego nazwy.

Obejmują wszystkie nadrzędne elementy runbook _WhatIf_ parametru. Po ustawieniu **True**, _WhatIf_ obsługuje ze szczegółami dotyczącymi dokładne zachowanie wykonuje element runbook po uruchomieniu bez _WhatIf_ parametru i sprawdza poprawność poprawny są maszyny wirtualne celem. Element runbook wykonuje tylko działania zdefiniowane podczas _WhatIf_ parametr ma wartość **False**.

|Element Runbook | Parametry | Opis|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Wywoływana z nadrzędnego elementu runbook. Ten element runbook tworzy alerty na podstawie poszczególnych zasobów w ramach scenariusza opisywanego AutoStop.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Wartość True lub False  | Tworzy lub aktualizuje Azure reguł alertów na maszynach wirtualnych w grupach docelowych, subskrypcji lub zasobu. <br> VMList: Rozdzielana przecinkami lista maszyn wirtualnych. Na przykład _maszyny vm1, vm2, vm3_.<br> *WhatIf* weryfikuje logiką wykonywania elementu runbook bez wykonywania.|
|AutoStop_Disable | brak | Wyłącza alerty AutoStop i domyślnego harmonogramu.|
|AutoStop_StopVM_Child | WebHookData | Wywoływana z nadrzędnego elementu runbook. Reguły alertów wywołać ten element runbook, aby zatrzymać maszynę Wirtualną.|
|Bootstrap_Main | brak | Umożliwia konfigurowanie ładowania początkowego konfiguracji, takich jak webhookURI, które nie są zwykle dostępne z usługi Azure Resource Manager jeden raz. Ten element runbook zostanie automatycznie usunięta po pomyślnym wdrożeniu.|
|ScheduledStartStop_Child | Nazwa maszyny wirtualnej <br> Akcja: Uruchamianie lub zatrzymywanie <br> ResourceGroupName | Wywoływana z nadrzędnego elementu runbook. Wykonuje uruchamianie lub zatrzymywanie akcję na zatrzymanie zaplanowane.|
|ScheduledStartStop_Parent | Akcja: Uruchamianie lub zatrzymywanie <br>VMList <br> WhatIf: Wartość True lub False | To ustawienie ma wpływ na wszystkie maszyny wirtualne w subskrypcji. Edytuj **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames** można wykonywać tylko na ten temat grup zasobów docelowych. Można również wykluczyć określonych maszyn wirtualnych, aktualizując **External_ExcludeVMNames** zmiennej.<br> VMList: Rozdzielana przecinkami lista maszyn wirtualnych. Na przykład _maszyny vm1, vm2, vm3_.<br> _WhatIf_ weryfikuje logiką wykonywania elementu runbook bez wykonywania.|
|SequencedStartStop_Parent | Akcja: Uruchamianie lub zatrzymywanie <br> WhatIf: Wartość True lub False<br>VMList| Utworzenie tagów o nazwie **sequencestart** i **sequencestop** na każdej maszynie Wirtualnej, dla którego chcesz działania uruchamiania/zatrzymywania sekwencji. Te nazwy tagu jest rozróżniana wielkość liter. Wartość tagu powinna być dodatnią liczbą całkowitą (1, 2, 3), kolejność, w którym chcesz rozpocząć lub zatrzymać. <br> VMList: Rozdzielana przecinkami lista maszyn wirtualnych. Na przykład _maszyny vm1, vm2, vm3_. <br> _WhatIf_ weryfikuje logiką wykonywania elementu runbook bez wykonywania. <br> **Uwaga**: maszyny wirtualne muszą znajdować się w grupach zasobów, zdefiniowane jako External_Start_ResourceGroupNames External_Stop_ResourceGroupNames i External_ExcludeVMNames w usłudze Azure Automation zmiennych. Muszą mieć odpowiednie znaczniki do wykonywania czynności zastosować zmiany.|

### <a name="variables"></a>Zmienne

W poniższej tabeli wymieniono zmiennych utworzonych na koncie usługi Automation. Modyfikowanie tylko zmienne prefiksem **zewnętrznych**. Modyfikowanie zmiennych prefiksem **wewnętrzne** powoduje niepożądanych skutków.

|Zmienna | Opis|
|---------|------------|
|External_AutoStop_Condition | Operator warunkowy wymagane do skonfigurowania warunek przed wyzwolenie alertu. Dopuszczalne wartości to **większe**, **równe**, **mniejsze**, i **równe**.|
|External_AutoStop_Description | Alert, aby zatrzymać maszynę Wirtualną, jeśli procent użycia procesora CPU przekracza wartość progową.|
|External_AutoStop_MetricName | Nazwa metryki wydajności, dla którego ma zostać skonfigurowany reguły alertów platformy Azure.|
|External_AutoStop_Threshold | Próg alertu Azure reguły określone w zmiennej _External_AutoStop_MetricName_. Wartości procentowe mogą należeć do zakresu od 1 do 100.|
|External_AutoStop_TimeAggregationOperator | Operator agregacji czasu, która jest stosowana do rozmiaru okna wybranego warunku. Dopuszczalne wartości to **średni**, **co najmniej**, **maksymalna**, **całkowita**, i **ostatniego**.|
|External_AutoStop_TimeWindow | Rozmiar okna, w którym Azure analizuje wybrane metryki służącą do wyzwalania alertu. Ten parametr akceptuje dane wejściowe w formacie przedziału czasu. Możliwe wartości to od 5 minut do 6 godzin.|
|External_ExcludeVMNames | Wprowadź nazwy maszyn wirtualnych, które mają być wykluczone, oddzielając nazwy za pomocą przecinka bez spacji.|
|External_Start_ResourceGroupNames | Określa co najmniej jedną grupę zasobów, oddzielając wartości przecinkami, przeznaczone dla działania uruchamiania.|
|External_Stop_ResourceGroupNames | Określa co najmniej jedną grupę zasobów, oddzielając wartości za pomocą przecinków, przeznaczony dla akcji stop.|
|Internal_AutomationAccountName | Określa nazwę konta usługi Automation.|
|Internal_AutoSnooze_WebhookUri | Określa, że identyfikator URI elementu Webhook o nazwie scenariusz AutoStop.|
|Internal_AzureSubscriptionId | Określa identyfikator subskrypcji platformy Azure.|
|Internal_ResourceGroupName | Określa nazwę grupy zasobów konta usługi Automation.|

We wszystkich scenariuszach **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, i **External_ExcludeVMNames** zmienne są wymagane na przeznaczonych dla maszyn wirtualnych, z wyjątkiem rozdzielaną przecinkami listę maszyn wirtualnych przez zapewnienie **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**, i  **ScheduledStartStop_Parent** elementów runbook. Oznacza to maszyn wirtualnych musi znajdować się w docelowej grupy zasobów, start i zatrzymać wykonanie akcji. Podobnie jak usługa Azure policy w można docelowej subskrypcji lub grupy zasobów i akcji działa logiki dziedziczone przez nowo utworzonymi maszynami wirtualnymi. Ta metoda pozwala uniknąć konieczności kupowania oddzielne harmonogramu dla każdej maszyny Wirtualnej i Zarządzaj uruchamia i zatrzymuje się na skali.

### <a name="schedules"></a>Harmonogramy

W poniższej tabeli wymieniono każdy z domyślnymi harmonogramami utworzone na koncie usługi Automation. Można je zmodyfikować lub utworzyć własne niestandardowe harmonogramy. Domyślnie wszystkie harmonogramy są wyłączone z wyjątkiem **Scheduled_StartVM** i **Scheduled_StopVM**.

Nie należy włączać wszystkie harmonogramy, ponieważ może to powodować nakładających się akcje harmonogramu. Najlepiej określić optymalizacje, które chcesz wykonać i odpowiednio zmodyfikować. Zobacz przykładowe scenariusze, w sekcji Przegląd, aby uzyskać dokładniejsze objaśnienie.

|Nazwa harmonogramu | Częstotliwość | Opis|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Co 8 godzin | Uruchamia element runbook AutoStop_CreateAlert_Parent co 8 godzin, które z kolei powoduje zatrzymanie maszyny Wirtualnej na podstawie wartości w External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames i External_ExcludeVMNames w usłudze Azure Automation zmiennych. Alternatywnie można określić rozdzielana przecinkami lista maszyn wirtualnych przy użyciu parametru VMList.|
|Scheduled_StopVM | Zdefiniowane przez codziennie użytkownika | Uruchamia element runbook Scheduled_Parent z parametrem _zatrzymać_ codziennie o określonej godzinie. Automatycznie zatrzymuje wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez zmienne zasobów. Włącz harmonogram powiązanej **StartVM zaplanowane**.|
|Scheduled_StartVM | Zdefiniowane przez codziennie użytkownika | Uruchamia element runbook Scheduled_Parent z parametrem _Start_ codziennie o określonej godzinie. Automatycznie uruchamia wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez odpowiednich zmiennych. Włącz harmonogram powiązanej **StopVM zaplanowane**.|
|Sekwencjonowania StopVM | 1:00:00 (czas UTC), każdy piątek | Uruchamia element runbook Sequenced_Parent z parametrem _zatrzymać_ każdy piątek o określonej godzinie. Sekwencyjnie (rosnąco) zatrzymania wszystkich maszyn wirtualnych przy użyciu tagu elementu **SequenceStop** definicją odpowiednich zmiennych. Aby uzyskać więcej informacji na temat zmiennych zasobów i wartości tagów zobacz sekcję elementów Runbook. Włącz harmonogram powiązanej **Sequenced StartVM**.|
|StartVM sekwencjonowania | 1:00 PM (UTC), każdy poniedziałek | Uruchamia element runbook Sequenced_Parent z parametrem _Start_ w każdy poniedziałek o określonej godzinie. Sekwencyjnie (malejąco) zaczyna się od wszystkich maszyn wirtualnych tag **SequenceStart** definicją odpowiednich zmiennych. Aby uzyskać więcej informacji na temat zmiennych zasobów i wartości tagów zobacz sekcję elementów Runbook. Włącz harmonogram powiązanej **Sequenced StopVM**.|

## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics

Usługa Automation tworzy dwa typy rekordów w obszarze roboczym usługi Log Analytics: dzienniki zadania i zadania strumieni.

### <a name="job-logs"></a>Dzienniki zadań

|Właściwość | Opis|
|----------|----------|
|Obiekt wywołujący |  Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań.|
|Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobLogs.|
|CorrelationId | Identyfikator GUID, który jest Identyfikatorem korelacji zadania elementu runbook.|
|JobId | Identyfikator GUID, który jest Identyfikatorem zadania elementu runbook.|
|operationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest zadanie.|
|resourceId | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|ResourceGroup | Określa nazwę grupy zasobów zadania elementu Runbook.|
|ResourceProvider | Określa nazwę usługi platformy Azure, która zapewnia zasoby do wdrożenia i zarządzania. W przypadku usługi Automation wartością jest Azure Automation.|
|ResourceType | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|resultType | Stan zadania elementu Runbook. Możliwe wartości:<br>— Uruchomione<br>— Zatrzymane<br>— Wstrzymane<br>— Nie powiodło się<br>— Powiodło się|
|resultDescription | Opisuje stan wyniku zadania elementu Runbook. Możliwe wartości:<br>— Zadanie jest uruchomione<br>— Zadanie nie powiodło się<br>— Zadanie zostało ukończone|
|RunbookName | Określa nazwę elementu Runbook.|
|SourceSystem | Określa system źródłowy dla przesłanych danych. W przypadku usługi Automation wartością jest OpsManager|
|StreamType | Określa typ zdarzenia. Możliwe wartości:<br>— Pełne<br>— Dane wyjściowe<br>— Błąd<br>— Ostrzeżenie|
|SubscriptionId | Określa identyfikator subskrypcji zadania.
|Time | Data i godzina dla wykonania zadania elementu Runbook.|

### <a name="job-streams"></a>Strumienie zadania

|Właściwość | Opis|
|----------|----------|
|Obiekt wywołujący |  Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań.|
|Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobStreams.|
|JobId | Identyfikator GUID, który jest Identyfikatorem zadania elementu runbook.|
|operationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest zadanie.|
|ResourceGroup | Określa nazwę grupy zasobów zadania elementu Runbook.|
|resourceId | Określa identyfikator zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|ResourceProvider | Określa nazwę usługi platformy Azure, która zapewnia zasoby do wdrożenia i zarządzania. W przypadku usługi Automation wartością jest Azure Automation.|
|ResourceType | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|resultType | Wynik zadania elementu Runbook w czasie wygenerowania zdarzenia. Możliwa wartość to:<br>— W toku|
|resultDescription | Obejmuje strumień wyjściowy z elementu Runbook.|
|RunbookName | Nazwa elementu Runbook.|
|SourceSystem | Określa system źródłowy dla przesłanych danych. W przypadku usługi Automation wartością jest OpsManager.|
|StreamType | Typ strumienia zadania. Możliwe wartości:<br>-Postęp<br>— Dane wyjściowe<br>— Ostrzeżenie<br>— Błąd<br>— Debugowanie<br>— Pełne|
|Time | Data i godzina dla wykonania zadania elementu Runbook.|

Jeśli wykonujesz dowolne wyszukiwanie dzienników, które zwraca rekordy kategorii **JobLogs** lub **JobStreams**, możesz wybrać **JobLogs** lub **JobStreams**widoku, który wyświetla zestaw kafelków z podsumowaniem aktualizacji zwracanych przez wyszukiwanie.

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla rekordów dzienników zbieranych przez to rozwiązanie.

|Zapytanie | Opis|
|----------|----------|
|Znajdź zadania dla elementu runbook ScheduledStartStop_Parent, która została zakończona pomyślnie | "" Wyszukaj kategorię == "JobLogs" | gdzie (RunbookName_s == "ScheduledStartStop_Parent") | gdzie (typ ResultType == "Ukończone")  | Podsumowanie |Elementy AggregatedValue = count() by ResultType, bin (TimeGenerated, 1 godz.) | Sortuj według malejącej TimeGenerated'' '|
|Znajdź zadania dla elementu runbook SequencedStartStop_Parent, która została zakończona pomyślnie | "" Wyszukaj kategorię == "JobLogs" | gdzie (RunbookName_s == "SequencedStartStop_Parent") | gdzie (typ ResultType == "Ukończone") | Podsumowanie |Elementy AggregatedValue = count() by ResultType, bin (TimeGenerated, 1 godz.) | Sortuj według malejącej TimeGenerated'' '|

## <a name="viewing-the-solution"></a>Wyświetlanie rozwiązania

Aby uzyskać dostęp do rozwiązania, przejdź do konta usługi Automation wybierz **obszaru roboczego** w obszarze **powiązane zasoby**. Na stronie usługi Log Analytics wybierz **rozwiązania** w obszarze **ogólne**. Na **rozwiązania** wybierz rozwiązanie **Start-Stop-VM [obszar roboczy]** z listy.

Wybranie rozwiązania Wyświetla **Start-Stop-VM [obszar roboczy]** stronie rozwiązania. W tym miejscu możesz sprawdzić ważne szczegóły, takie jak **StartStopVM** kafelka. W obszarze roboczym usługi Log Analytics ten Kafelek zawiera liczbę oraz graficzną reprezentację zadań elementów runbook dla rozwiązania, które zostały uruchomione i została zakończona pomyślnie.

![Strona rozwiązania Update Management Automation](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

W tym miejscu możesz można wykonać dalszą analizę rekordów zadania, klikając kafelka pierścieniowej. Pulpit nawigacyjny rozwiązania przedstawia historię zadania i wstępnie zdefiniowane zapytania wyszukiwania w dzienniku. Przełącz się do portalu Advanced Analytics dziennik wyszukiwania na podstawie zapytań wyszukiwania.

## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

Aby zmienić powiadomienia e-mail, po wdrożeniu rozwiązania, zmodyfikuj grupę akcji, który został utworzony podczas wdrażania.  

W witrynie Azure portal przejdź do monitora -> grupy akcji. Wybierz grupę akcji o nazwie **StartStop_VM_Notication**.

![Strona rozwiązania Update Management Automation](media/automation-solution-vm-management/azure-monitor.png)

Na **StartStop_VM_Notification** kliknij **Edytuj szczegóły** w obszarze **szczegóły**. Spowoduje to otwarcie **poczty E-mail/SMS/wypychania/rejestr** strony. Zaktualizuj adres e-mail, a następnie kliknij przycisk **OK** Aby zapisać zmiany.

![Strona rozwiązania Update Management Automation](media/automation-solution-vm-management/change-email.png)

Alternatywnie można dodać dodatkowe akcje grupę akcji, aby dowiedzieć się więcej o grupach akcji, zobacz [grupy akcji](../monitoring-and-diagnostics/monitoring-action-groups.md)

Poniżej przedstawiono przykładową wiadomością e-mail, która jest wysyłana, gdy rozwiązanie zamyka maszyny wirtualne.

![Strona rozwiązania Update Management Automation](media/automation-solution-vm-management/email.png)

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modyfikuj harmonogramy uruchamiania i zamykania

Harmonogramy uruchamiania i zamykania w tym rozwiązaniu Zarządzanie przedstawiono te same kroki, co zostało opisane w [Planowanie elementu runbook w usłudze Azure Automation](automation-schedules.md).

Konfigurowanie rozwiązania do właśnie zatrzymywania maszyn wirtualnych w określonym czasie jest obsługiwane. W tym celu należy:

1. Upewnij się, dodano grupy zasobów dla maszyn wirtualnych zamknąć w **External_Start_ResourceGroupNames** zmiennej.
2. Utwórz swój własny harmonogram, czas, który chcesz zamknąć maszyny wirtualne.
3. Przejdź do **ScheduledStartStop_Parent** elementu runbook i kliknij przycisk **harmonogram**. Dzięki temu można zaznaczyć harmonogram, który został utworzony w poprzednim kroku.
4. Wybierz **parametry i ustawienia uruchamiania** i ustaw parametr akcji "Stop".
5. Kliknij przycisk **OK**, aby zapisać zmiany.

## <a name="update-the-solution"></a>Aktualizowanie rozwiązania

Jeśli wdrożono poprzednią wersję tego rozwiązania, należy najpierw usunąć go z Twojego konta przed wdrożeniem zaktualizowanej wersji. Postępuj zgodnie z instrukcjami, aby [usunąć rozwiązanie](#remove-the-solution) a następnie wykonaj powyższe kroki, aby [wdrażania rozwiązania](#deploy-the-solution).

## <a name="remove-the-solution"></a>Usuń rozwiązanie

Jeśli zdecydujesz, że nie jest już konieczne korzystanie z odpowiedniego rozwiązania, możesz usunąć go z konta usługi Automation. Usunięcie rozwiązania powoduje usunięcie tylko elementy runbook. Nie powoduje usunięcia harmonogramy lub zmienne, które zostały utworzone, gdy rozwiązanie zostało dodane. Zasoby, które należy usunąć ręcznie, jeśli nie używasz je z innymi elementami runbook.

Aby usunąć rozwiązanie, wykonaj następujące czynności:

1. Z poziomu konta usługi Automation wybierz **obszaru roboczego** z lewej strony.
1. Na **rozwiązania** wybierz rozwiązanie **Start-Stop-VM [obszar roboczy]**. Na **VMManagementSolution [obszar roboczy]** stronę z menu, wybierz opcję **Usuń**.<br><br> ![Usuń rozwiązanie Mgmt maszyny Wirtualnej](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. W **Usuń rozwiązanie** okna, upewnij się, że chcesz usunąć rozwiązanie.
1. Podczas weryfikowania informacji i rozwiązanie zostanie usunięty, możesz śledzić postęp w sekcji **powiadomienia** z menu. Nastąpi powrót do **rozwiązania** strony po rozpoczęciu procesu, aby usunąć rozwiązania.

Konto usługi Automation i obszaru roboczego usługi Log Analytics nie są usuwane w ramach tego procesu. Jeśli nie chcesz zachować obszaru roboczego usługi Log Analytics, musisz ręcznie je usunąć. Można to zrobić w witrynie Azure portal:

1. Z platformy Azure ekran główny portalu, wybierz **usługi Log Analytics**.
1. Na **usługi Log Analytics** wybierz obszar roboczy.
1. Wybierz **Usuń** menu na stronie Ustawienia w obszarze roboczym.

Jeśli nie chcesz zachować składniki konta usługi Azure Automation, możesz ręcznie usunąć każdy. Aby uzyskać listę elementów runbook, zmienne i harmonogramów utworzonych przez to rozwiązanie, zobacz [składników rozwiązania](#solution-components).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat do tworzenia różnych zapytań wyszukiwania i przeglądania dzienników zadań usługi Automation z usługą Log Analytics, zobacz [przeszukiwanie dzienników w usłudze Log Analytics](../log-analytics/log-analytics-log-searches.md).
- Aby dowiedzieć się więcej o wykonywaniu elementów runbook, sposobie monitorowania zadań elementów runbook i innych szczegółach technicznych, zobacz [Track a runbook job](automation-runbook-execution.md) (Śledzenie zadania elementu runbook).
- Aby dowiedzieć się więcej na temat usługi Log Analytics i źródłach zbierania danych, zobacz [Azure zbieranie danych magazynu w usłudze Log Analytics — omówienie](../log-analytics/log-analytics-azure-storage.md).