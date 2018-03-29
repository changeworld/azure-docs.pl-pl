---
title: Maszyny wirtualne uruchamiania i zatrzymywania podczas rozwiązania poza godzinami szczytu (wersja zapoznawcza)
description: To rozwiązanie do zarządzania maszyna wirtualna uruchamia i zatrzymuje maszynach wirtualnych Azure Resource Manager zgodnie z harmonogramem i aktywnie monitoruje z analizy dzienników.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: da2d95bc100a6160282c93682ad76f7ee881e105
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="startstop-vms-during-off-hours-solution-preview-in-azure-automation"></a>Maszyny wirtualne uruchamiania i zatrzymywania podczas rozwiązania poza godzinami szczytu (wersja zapoznawcza) w usłudze Automatyzacja Azure

Maszyny wirtualne uruchamiania i zatrzymywania godzinami rozwiązania uruchamia i zatrzymuje maszynach wirtualnych Azure harmonogramów zdefiniowane przez użytkownika, zawiera informacje na temat technologii za pośrednictwem usługi Analiza dzienników Azure i wysyła opcjonalne wiadomości e-mail przy użyciu [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). Obsługuje ona zarówno usługi Azure Resource Manager i klasycznych maszyn wirtualnych dla większości scenariuszy.

To rozwiązanie zapewnia opcję zdecentralizowane automatyzacji dla użytkowników, którzy mają zmniejszyć koszty ich przy użyciu zasobów niekorzystającą, niskich kosztach. W tym rozwiązaniu można:

* Planowanie maszyn wirtualnych, aby uruchomić i zatrzymać.
* Planowanie maszyn wirtualnych, aby uruchomić i zatrzymać w kolejności rosnącej przy użyciu tagów Azure (nieobsługiwane w przypadku klasycznych maszyn wirtualnych).
* Auto, aby zatrzymać maszyn wirtualnych na podstawie niski użycia procesora CPU.

## <a name="prerequisites"></a>Wymagania wstępne

* Elementy Runbook działają przy użyciu [konta Uruchom jako platformy Azure](automation-offering-get-started.md#authentication-methods). Konto Uruchom jako jest preferowaną metodą uwierzytelniania, ponieważ używa certyfikatu uwierzytelniania zamiast hasła, które może wygaśnie lub często zmieniana.
* To rozwiązanie umożliwia zarządzanie tylko maszyny wirtualne, które znajdują się w tej samej subskrypcji co konto usługi Automatyzacja Azure.
* To rozwiązanie jest wdrożyć tylko w następujących regionach platformy Azure: Australia Południowo-Wschodnia, Kanada centralnej, Indie środkowe, wschodnie stany USA, Japonia Wschodnia, Azja południowo-wschodnia, Wielka Brytania Południowa i Europa Zachodnia.

  > [!NOTE]
  > Elementy runbook, zarządzanie harmonogramem maszyny Wirtualnej można kierować maszyn wirtualnych w dowolnym regionie.

* Do wysyłania powiadomień e-mail po zakończeniu uruchamianie i zatrzymywanie elementów runbook maszyny Wirtualnej, podczas dołączania z portalu Azure Marketplace, wybierz **tak** SendGrid wdrażania.

  > [!IMPORTANT]
  > SendGrid jest usługi innej firmy. Aby uzyskać pomoc, skontaktuj się z [SendGrid](https://sendgrid.com/contact/).

  Ograniczenia sendgrid są:

  * Maksymalnie jedno konto SendGrid na użytkownika na subskrypcję.
  * Maksymalnie dwóch kont SendGrid na subskrypcję.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

Wykonaj poniższe kroki, aby dodać uruchamiania/zatrzymywania maszyn wirtualnych podczas rozwiązania poza godzinami szczytu na koncie automatyzacji, a następnie skonfiguruj zmienne, aby dostosować rozwiązania.

1. W witrynie Azure Portal kliknij pozycję **Utwórz zasób**.
1. Na stronie witryny Marketplace, wpisz słowo kluczowe, taką jak **Start** lub **uruchamiania i zatrzymywania**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Alternatywnie można wpisz słowa kluczowe co najmniej jeden z pełną nazwę rozwiązania i naciśnij klawisz Enter. Wybierz **uruchamiania/zatrzymywania maszyn wirtualnych w godzinach [Podgląd]** w wynikach wyszukiwania.
1. W **uruchamiania/zatrzymywania maszyn wirtualnych w godzinach [Podgląd]** dla wybranego rozwiązania strony, sprawdź informacje, a następnie kliknij przycisk **Utwórz**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

1. **Dodaj rozwiązanie** zostanie wyświetlona strona. Monit o skonfigurowanie rozwiązania, przed zaimportowaniem go w ramach subskrypcji automatyzacji.
   ![Strona Dodawanie zarządzającego maszyny Wirtualnej](media/automation-solution-vm-management/azure-portal-add-solution-01.png)
1. Na **Dodaj rozwiązanie** wybierz pozycję **obszaru roboczego**. Wybierz obszar roboczy OMS jest połączony z tej samej subskrypcji Azure, która jest konta automatyzacji. Jeśli nie masz obszaru roboczego wybierz **Utwórz nowy obszar roboczy**. Na **obszarem roboczym pakietu OMS** strony, wykonaj następujące czynności:
   * Określ nazwę dla nowego **Obszaru roboczego OMS**.
   * Wybierz **subskrypcji** się połączyć, wybierając z listy rozwijanej, jeśli wybrana domyślnie nie jest odpowiedni.
   * Aby uzyskać **grupy zasobów**, możesz utworzyć nową grupę zasobów lub wybierz istniejący.
   * Wybierz **lokalizację**. Obecnie jedynymi lokalizacjami, dostępne są **południowo-wschodnia Australia**, **Kanada centralnej**, **Indie środkowe**, **wschodnie stany USA**, **Japonia Wschodnia**, **Azja południowo-wschodnia**, **Wielka Brytania Południowa**, i **Europa**.
   * Wybierz **warstwę cenową**. Rozwiązanie oferuje dwie warstwy: **wolne** i **na węzeł (OMS)**. Warstwa bezpłatna ma ograniczenie ilości zbieranych danych codziennie, okres przechowywania i minut czasu wykonywania zadania elementu runbook. Warstwy każdego węzła nie ma ograniczenie ilości danych zbieranych dziennie.

        > [!NOTE]
        > Mimo że warstwy na GB (autonomiczna) płatnej jest wyświetlany jako opcja, nie ma zastosowania. Jeśli zaznacz go i kontynuować tworzenie tego rozwiązania, w ramach subskrypcji, zwróci błąd. Ten problem zostanie rozwiązany po oficjalnym wydaniu tego rozwiązania. To rozwiązanie tylko używa automatyzacji zadań minut i wprowadzanie dziennika. Dodatkowe węzły OMS nie dodaje do środowiska.

1. Po podaniu wymaganych informacji w **obszarem roboczym pakietu OMS** kliknij przycisk **Utwórz**. Można śledzić postęp w obszarze **powiadomienia** z menu, która zwraca do **Dodaj rozwiązanie** strony po zakończeniu.
1. Na **Dodaj rozwiązanie** wybierz pozycję **konto automatyzacji**. Jeśli tworzysz nowy obszar roboczy OMS, należy także utworzyć nowe konto automatyzacji ma zostać skojarzony z nim. Wybierz **utworzyć konto usługi automatyzacja**i na **konto automatyzacji dodać** pozycję zapewnia następujące korzyści:
   * W polu **Nazwa** wprowadź nazwę konta usługi Automation.

    Inne opcje są automatycznie wypełniane oparte na obszar roboczy OMS wybrane. Nie można modyfikować tych opcji. Konto Uruchom jako platformy Azure jest domyślną metodą uwierzytelniania dla elementów Runbook zawartych w tym rozwiązaniu. Po kliknięciu **OK**, opcje konfiguracji są weryfikowane i utworzeniu konta automatyzacji. Postęp możesz śledzić w sekcji **Powiadomienia** z poziomu menu.

1. Na koniec na **Dodaj rozwiązanie** wybierz pozycję **konfiguracji**. **Parametry** zostanie wyświetlona strona.

   ![Strona parametrów dla rozwiązania](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   W tym miejscu zostanie wyświetlony monit o:
   * Określ **docelowa grupa zasobów nazwy**. Są to nazwy grup zasobów, które zawierają maszyny wirtualne mają być zarządzane przy użyciu tego rozwiązania. Można wprowadzić więcej niż jedną nazwę i oddzielić od siebie za pomocą przecinka (wartości nie są z uwzględnieniem wielkości liter). Użycie symbolu wieloznacznego jest obsługiwane. Możesz skorzystać z tej opcji, jeśli chcesz uwzględnić maszyny wirtualne we wszystkich grupach zasobów w subskrypcji. Ta wartość jest przechowywana w **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames** zmiennych.
   * Określ **listę wykluczyć maszyny Wirtualnej (ciąg)**. Jest to nazwa co najmniej jednej maszyny wirtualnej, docelowa grupa zasobów. Można wprowadzić więcej niż jedną nazwę i oddzielić od siebie za pomocą przecinka (wartości nie są z uwzględnieniem wielkości liter). Przy użyciu symboli wieloznacznych jest obsługiwana. Ta wartość jest przechowywana w **External_ExcludeVMNames** zmiennej.
   * Wybierz **harmonogram**. Jest to cykliczne datę i godzinę, uruchamianie i zatrzymywanie maszyn wirtualnych w docelowej grupy zasobów. Domyślnie harmonogramu jest skonfigurowany zgodnie ze strefą czasową UTC. Wybrać inny region nie jest dostępna. Aby skonfigurować harmonogram określonej strefy czasowej po skonfigurowaniu rozwiązania, zobacz [modyfikowanie harmonogramu uruchamiania i wyłączania](#modify-the-startup-and-shutdown-schedule).
   * Aby otrzymywać **wiadomości E-mail z powiadomieniami** z SendGrid, zaakceptuj wartość domyślną **tak** i podaj prawidłowy adres e-mail. W przypadku wybrania **nr** , ale później zdecydować, czy chcesz otrzymywać powiadomienia pocztą e-mail, możesz zaktualizować **External_EmailToAddress** zmiennej z prawidłowych adresów e-mail oddzielonych przecinkami, a następnie Modyfikowanie zmiennej **External_IsSendEmail** z wartością **tak**.

1. Po skonfigurowaniu początkowego ustawienia wymagane dla rozwiązania, kliknij przycisk **OK** zamknąć **parametry** i wybrać opcję **Utwórz**. Po zweryfikowaniu wszystkich ustawień rozwiązanie jest wdrożone do subskrypcji. Ten proces może potrwać kilka sekund, aby zakończyć, a można śledzić postęp w obszarze **powiadomienia** z menu.

## <a name="scenarios"></a>Scenariusze

Rozwiązanie zawiera trzy różne scenariusze. Te scenariusze są następujące:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scenariusz 1: Uruchamiania i zatrzymywania VMs zgodnie z harmonogramem

To jest konfiguracja domyślna najpierw wdrażania rozwiązania. Na przykład można skonfigurować jego zatrzymanie wszystkich maszyn wirtualnych w ramach subskrypcji, po opuszczeniu pracy wieczorem i uruchom je w nocy, podczas pracy w biurze. Po skonfigurowaniu harmonogramy **zaplanowane-StartVM** i **StopVM zaplanowane** podczas wdrażania, uruchomić i zatrzymać docelowych maszyn wirtualnych. Konfigurowanie tego rozwiązania pod kątem wszystko maszyn wirtualnych jest obsługiwane, zobacz [Modyfikuj harmonogramy uruchamiania i wyłączania](#modify-the-startup-and-shutdown-schedules) informacje na temat konfigurowania harmonogramu niestandardowego.

>[!NOTE]
>Strefa czasowa jest bieżącej strefy czasowej po skonfigurowaniu harmonogramu parametru czasu. Jednak jest on przechowywany w formacie UTC automatyzacji Azure. Nie trzeba wykonywać żadnych konwersji strefy czasowej odbywa się to podczas wdrażania.

Możesz kontrolować, które maszyny wirtualne są w zakresie przez skonfigurowanie następujących zmiennych: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, i **External_ ExcludeVMNames**.

Można włączyć przeznaczonych dla akcji względem subskrypcji i grupy zasobów, lub przeznaczonych dla określonej listy maszyn wirtualnych, ale nie oba.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Uruchamianie i zatrzymywanie akcje dotyczące subskrypcji i zasobu grupy docelowe

1. Skonfiguruj **External_Stop_ResourceGroupNames** i **External_ExcludeVMNames** zmienne do określania docelowych maszyn wirtualnych.
2. Włącz i zaktualizuj **zaplanowane-StartVM** i **StopVM zaplanowane** harmonogramów.
3. Uruchom **ScheduledStartStop_Parent** runbook przy użyciu parametru ACTION ustawioną **start** i ustawić parametr WHATIF **True** Aby przejrzeć wprowadzone zmiany.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Docelowa rozpoczęcie i zakończenie działania przez listę maszyn wirtualnych

1. Uruchom **ScheduledStartStop_Parent** runbook przy użyciu parametru ACTION ustawioną **start**, dodać rozdzielaną przecinkami listę maszyn wirtualnych w *VMList* parametr, a następnie ustawić Parametr WHATIF **True**. Podgląd zmian.
2. Skonfiguruj **External_ExcludeVMNames** parametru rozdzielaną przecinkami listę maszyn wirtualnych (VM1, maszyny VM2, VM3).
3. W tym scenariuszu nie honoruje **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupnames** zmiennych. W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać więcej informacji, zobacz [Planowanie elementu runbook automatyzacji Azure](../automation/automation-schedules.md).

>[!NOTE]
> Wartość **nazw grupa zasobów docelowych** jest przechowywana jako wartość dla obu **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames**. Dla dalszego szczegółowości można zmodyfikować każdą z tych zmiennych pod kątem różnych grup zasobów. Do rozpoczęcia działania, użyj **External_Start_ResourceGroupNames**i zatrzymania działania, użyj **External_Stop_ResourceGroupNames**. Maszyny wirtualne są automatycznie dodawane do uruchomienia i zatrzymania harmonogramów.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scenariusz 2: Uruchamiania/zatrzymywania maszyn wirtualnych w sekwencji za pomocą tagów

W środowisku zawierającym co najmniej dwóch składników na wiele maszyn wirtualnych, obsługa obciążeń rozproszonych ważne jest Obsługa sekwencji, w którym uruchamiania i zatrzymywania elementów w kolejności. Można to zrobić, wykonując następujące czynności:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Uruchamianie i zatrzymywanie akcje dotyczące subskrypcji i zasobu grupy docelowe

1. Dodaj **SequenceStart** i **SequenceStop** tag z dodatnią liczbę całkowitą na maszynach wirtualnych, które są stosowane w **External_Start_ResourceGroupNames** i  **External_Stop_ResourceGroupNames** zmiennych. Uruchamianie i zatrzymywanie działania są wykonywane w kolejności rosnącej. Aby dowiedzieć się, jak znacznik maszyny Wirtualnej, zobacz [tagu maszyny wirtualnej systemu Windows na platformie Azure](../virtual-machines/windows/tag.md) i [tagu maszyny wirtualnej systemu Linux na platformie Azure](../virtual-machines/linux/tag.md).
2. Modyfikuj harmonogramy **Sequenced-StartVM** i **Sequenced StopVM** datę i godzinę, które spełniają wymagania i włączony harmonogram.
3. Uruchom **SequencedStartStop_Parent** runbook przy użyciu parametru ACTION ustawioną **start** i ustawić parametr WHATIF **True** Aby przejrzeć wprowadzone zmiany.
4. Przeglądanie akcji i wprowadź niezbędne zmiany przed zaimplementowaniem względem maszyn wirtualnych w środowisku produkcyjnym. Podczas gotowe, ręcznie wykonywania elementu runbook za pomocą parametru ustawioną **False**, lub programowi Harmonogram automatyzacji **Sequenced-StartVM** i **Sequenced StopVM** Uruchom automatycznie po określonego harmonogramu.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Docelowa rozpoczęcie i zakończenie działania przez listę maszyn wirtualnych

1. Dodaj **SequenceStart** i **SequenceStop** tag z dodatnią liczbę całkowitą na maszynach wirtualnych planowane jest dodanie **VMList** zmiennej. 
2. Uruchom **SequencedStartStop_Parent** runbook przy użyciu parametru ACTION ustawioną **start**, dodać rozdzielaną przecinkami listę maszyn wirtualnych w *VMList* parametr, a następnie ustawić Parametr WHATIF **True**. Podgląd zmian.
3. Skonfiguruj **External_ExcludeVMNames** parametru rozdzielaną przecinkami listę maszyn wirtualnych (VM1, maszyny VM2, VM3).
4. W tym scenariuszu nie honoruje **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupnames** zmiennych. W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać więcej informacji, zobacz [Planowanie elementu runbook automatyzacji Azure](../automation/automation-schedules.md).
5. Przeglądanie akcji i wprowadź niezbędne zmiany przed zaimplementowaniem względem maszyn wirtualnych w środowisku produkcyjnym. Podczas gotowe, ręcznie wykonywania elementu runbook za pomocą parametru ustawioną **False**, lub programowi Harmonogram automatyzacji **Sequenced-StartVM** i **Sequenced StopVM** Uruchom automatycznie po określonego harmonogramu.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Scenariusz 3: Uruchamiania i zatrzymywania automatycznie na podstawie użycia Procesora

To rozwiązanie może ułatwić zarządzanie kosztów maszyn wirtualnych działających w Twojej subskrypcji, oceniając maszynach wirtualnych platformy Azure, które nie są używane w okresach, poza szczytem, takich jak po godzinach i automatycznego zamykania ich Jeśli wykorzystanie procesora jest mniej niż x %.

Domyślnie rozwiązania jest wstępnie skonfigurowana do oceny Metryka procent procesora CPU czy średnie wykorzystanie jest 5 procent lub mniej. To jest kontrolowany przez następujące zmienne i może być modyfikowany, jeśli wartości domyślne nie spełniają wymagań:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

Można włączyć przeznaczonych dla akcji względem subskrypcji i grupy zasobów, lub przeznaczonych dla określonej listy maszyn wirtualnych, ale nie oba.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Akcja zatrzymania w odniesieniu do subskrypcji i zasobu grupy docelowe

1. Skonfiguruj **External_Stop_ResourceGroupNames** i **External_ExcludeVMNames** zmienne do określania docelowych maszyn wirtualnych.
2. Włącz i zaktualizuj **Schedule_AutoStop_CreateAlert_Parent** harmonogramu.
3. Uruchom **AutoStop_CreateAlert_Parent** runbook przy użyciu parametru ACTION ustawioną **start** i ustawić parametr WHATIF **True** Aby przejrzeć wprowadzone zmiany.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Docelowa rozpoczęcie i zakończenie działania przez listę maszyn wirtualnych

1. Uruchom **AutoStop_CreateAlert_Parent** runbook przy użyciu parametru ACTION ustawioną **start**, dodać rozdzielaną przecinkami listę maszyn wirtualnych w *VMList* parametr, a następnie ustawić Parametr WHATIF **True**. Podgląd zmian.
2. Skonfiguruj **External_ExcludeVMNames** parametru rozdzielaną przecinkami listę maszyn wirtualnych (VM1, maszyny VM2, VM3).
3. W tym scenariuszu nie honoruje **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupnames** zmiennych. W tym scenariuszu należy utworzyć własny harmonogram automatyzacji. Aby uzyskać więcej informacji, zobacz [Planowanie elementu runbook automatyzacji Azure](../automation/automation-schedules.md).

Teraz, gdy masz harmonogram zatrzymywanie maszyn wirtualnych na podstawie wykorzystania procesora CPU, musisz włączyć jedną z następujących harmonogramów, aby je uruchomić.

* Docelowy uruchomienie akcji przez subskrypcji i grupy zasobów. Zobacz kroki w [scenariusz 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) do testowania i włączenie **StartVM zaplanowane** harmonogramów.
* Docelowy uruchomienie akcji przez subskrypcji, grupy zasobów i tagów. Zobacz kroki w [scenariuszu 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) do testowania i włączenie **Sequenced StartVM** harmonogramów.

## <a name="solution-components"></a>Składniki rozwiązania

To rozwiązanie obejmuje wstępnie skonfigurowane elementów runbook, harmonogramów i integracja z usługą analizy dzienników, więc można dostosować sposób uruchamiania i wyłączania maszyn wirtualnych, w zależności od potrzeb biznesowych.

### <a name="runbooks"></a>Elementy Runbook

W poniższej tabeli wymieniono elementy runbook wdrożony Twoje konto usługi Automatyzacja przez to rozwiązanie. Nie należy wprowadzać zmian kod elementu runbook. Zamiast tego należy zapisać własnego elementu runbook dla nowych funkcji.

> [!IMPORTANT]
> Nie należy bezpośrednio uruchamiać żadnych elementów runbook z "podrzędny" dołączonym do jego nazwy.

Obejmują wszystkie nadrzędne elementy runbook *WhatIf* parametru. Wartość **True**, *WhatIf* obsługuje określające zachowanie dokładne wykonuje element runbook podczas uruchamiania bez *WhatIf* parametru i sprawdza poprawność poprawny są maszyny wirtualne docelowe. Element runbook tylko wykonuje działania zdefiniowane podczas *WhatIf* ustawiono parametr **False**.

|**Runbook** | **Parametry** | **Opis**|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Wywoływana z nadrzędnego elementu runbook. Ten element runbook tworzy alerty na podstawie ciągu zasobu dla scenariusza AutoStop.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Wartość PRAWDA lub FAŁSZ  | Tworzy lub aktualizuje Azure reguł alertów na maszynach wirtualnych w grupach docelowych subskrypcja lub zasób. <br> VMList: Rozdzielana przecinkami lista maszyn wirtualnych. Na przykład *vm1 maszyny vm2, vm3*.<br> *WhatIf* weryfikuje bez wykonywania logiki elementu runbook.|
|AutoStop_Disable | brak | Wyłącza alerty AutoStop i domyślnego harmonogramu.|
|AutoStop_StopVM_Child | WebHookData | Wywoływana z nadrzędnego elementu runbook. Reguły alertów wywołanie tego elementu runbook można zatrzymać maszyny Wirtualnej.|
|Bootstrap_Main | brak | Jednorazowo można skonfigurować bootstrap konfiguracje, takie jak webhookURI, które nie są zwykle dostępne z usługi Azure Resource Manager. Ten element runbook zostanie usunięta automatycznie po pomyślnym wdrożeniu.|
|ScheduledStartStop_Child | VMName <br> Akcja: Uruchamianie lub zatrzymywanie <br> ResourceGroupName | Wywoływana z nadrzędnego elementu runbook. Wykonuje do uruchomienia lub zatrzymania akcji zaplanowanych stop.|
|ScheduledStartStop_Parent | Akcja: Uruchamianie lub zatrzymywanie <br>VMList <br> WhatIf: Wartość PRAWDA lub FAŁSZ | Ma to wpływ na wszystkie maszyny wirtualne w subskrypcji. Edytuj **External_Start_ResourceGroupNames** i **External_Stop_ResourceGroupNames** można wykonać tylko na tych grup zasobów docelowych. Można też wykluczyć określone maszyn wirtualnych, aktualizując **External_ExcludeVMNames** zmiennej.<br> VMList: Rozdzielana przecinkami lista maszyn wirtualnych. Na przykład *vm1 maszyny vm2, vm3*.<br> *WhatIf* weryfikuje bez wykonywania logiki elementu runbook.|
|SequencedStartStop_Parent | Akcja: Uruchamianie lub zatrzymywanie <br> WhatIf: Wartość PRAWDA lub FAŁSZ<br>VMList| Utwórz znaczniki o nazwie **SequenceStart** i **SequenceStop** na każdej maszynie Wirtualnej, dla którego chcesz działania uruchamiania/zatrzymywania sekwencji. Wartość tagu powinna być dodatnią liczbą całkowitą (1, 2, 3) umożliwiająca kolejność, w którym chcesz uruchomić lub zatrzymać. <br> VMList: Rozdzielana przecinkami lista maszyn wirtualnych. Na przykład *vm1 maszyny vm2, vm3*. <br> *WhatIf* weryfikuje bez wykonywania logiki elementu runbook. <br> **Uwaga**: maszyny wirtualne muszą się znajdować w zdefiniowany jako External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames i External_ExcludeVMNames w automatyzacji Azure zmiennych grupy zasobów. Muszą mieć odpowiednie znaczniki do wykonywania działań zaczęły obowiązywać.|

### <a name="variables"></a>Zmienne

W poniższej tabeli przedstawiono zmienne utworzone na Twoim koncie automatyzacji. Należy modyfikować tylko zmienne prefiksem **zewnętrznych**. Modyfikowanie zmiennych prefiksem **wewnętrzne** powoduje, że niepożądane skutki.

|**Zmienna** | **Opis**|
---------|------------|
|External_AutoStop_Condition | Operator warunkowy wymagane do skonfigurowania warunek przed wyzwolenie alertu. Dopuszczalne wartości to **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, i **LessThanOrEqual**.|
|External_AutoStop_Description | Alert zatrzymania maszyny Wirtualnej, jeśli procent użycia procesora CPU przekracza wartość progową.|
|External_AutoStop_MetricName | Nazwa metryki wydajności, dla którego ma zostać skonfigurowana Azure alertu.|
|External_AutoStop_Threshold | Próg alertu Azure reguły określone w zmiennej *External_AutoStop_MetricName*. Wartości procentowe mogą należeć do zakresu od 1 do 100.|
|External_AutoStop_TimeAggregationOperator | Operator agregacji czasu, która jest stosowana do rozmiaru wybranego okna Próba sprawdzenia warunku. Dopuszczalne wartości to **średni**, **Minimum**, **maksymalna**, **całkowita**, i **ostatniego**.|
|External_AutoStop_TimeWindow | Rozmiar okna, w którym Azure analizuje wybranego metryki służącą do wyzwalania alertu. Ten parametr akceptuje dane wejściowe w formacie timespan. Możliwe wartości to od 5 minut do 6 godzin.|
|External_EmailFromAddress | Określa nadawcy wiadomości e-mail.|
|External_EmailSubject | Określa tekst dla wiersza tematu wiadomości e-mail.|
|External_EmailToAddress | Określa adresatów wiadomości e-mail. Rozdziel nazwy za pomocą przecinka.|
|External_ExcludeVMNames | Wprowadź nazw maszyn wirtualnych, które mają zostać wykluczone, oddzielając nazwy za pomocą przecinka nie może zawierać spacji.|
|External_IsSendEmail | Określa opcje do wysyłania powiadomień e-mail po zakończeniu. Określ **tak** lub **nr** nie wysłać wiadomości e-mail. Ta opcja powinna być **nr** Jeśli powiadomienia e-mail nie została włączona podczas pierwszego wdrożenia.|
|External_Start_ResourceGroupNames | Określa co najmniej jedną grupę zasobów, oddzielanie wartości przecinkami, przeznaczony dla działania uruchamiania.|
|External_Stop_ResourceGroupNames | Określa co najmniej jedną grupę zasobów, oddzielanie wartości za pomocą przecinka, przeznaczony dla akcji zatrzymania.|
|Internal_AutomationAccountName | Określa nazwę konta automatyzacji.|
|Internal_AutoSnooze_WebhookUri | Określa, że identyfikator URI elementu Webhook wywołana dla tego scenariusza AutoStop.|
|Internal_AzureSubscriptionId | Określa identyfikator subskrypcji platformy Azure.|
|Internal_ResourceGroupName | Określa nazwę grupy zasobów konta automatyzacji.|
|Internal_SendGridAccountName | Określa nazwę konta SendGrid.|
|Internal_SendGridPassword | Określa hasło konta SendGrid.|

We wszystkich scenariuszach **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, i **External_ExcludeVMNames** zmienne są wymagane przeznaczony do maszyn wirtualnych, z wyjątkiem podając rozdzielaną przecinkami listę maszyn wirtualnych dla **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**, i  **ScheduledStartStop_Parent** elementów runbook. Oznacza to maszyn wirtualnych musi znajdować się w grup zasobów obiektu docelowego, aby rozpocząć i zatrzymać operacje. Podobne do zasad platformy Azure, w tym można grupy zasobów lub subskrypcji i akcji działa logiki dziedziczone przez nowo utworzony maszyn wirtualnych. Takie podejście pozwala uniknąć konieczności obsługi oddzielnych harmonogramu dla każdej maszyny Wirtualnej i Zarządzaj uruchamia i zatrzymuje w skali.

### <a name="schedules"></a>Harmonogramy

Poniższa tabela zawiera listę domyślnych planów utworzone na Twoim koncie automatyzacji.  Można je zmodyfikować lub utworzyć własne niestandardowe harmonogramy. Domyślnie każdy z tych wyłączone z wyjątkiem **Scheduled_StartVM** i **Scheduled_StopVM**.

Nie należy włączać wszystkie harmonogramy, ponieważ może to powodować nakładające się akcje harmonogramu. Najlepiej określić optymalizacji, które chcesz wykonać i zmodyfikuj odpowiednio. Zobacz przykładowe scenariusze, w sekcji Przegląd, aby uzyskać dokładniejsze objaśnienie.

|**Nazwa harmonogramu** | **Częstotliwość** | **Opis**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Co 8 godzin | Uruchamia element runbook AutoStop_CreateAlert_Parent co 8 godzin, co z kolei uniemożliwia wartości na podstawie maszyny Wirtualnej w External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames i External_ExcludeVMNames w zmiennych automatyzacji Azure. Alternatywnie można określić rozdzielaną przecinkami listę maszyn wirtualnych za pomocą parametru VMList.|
|Scheduled_StopVM | Zdefiniowane przez codziennie użytkownika | Uruchamia element runbook Scheduled_Parent z parametrem *zatrzymać* każdego dnia o określonej godzinie. Automatycznie zatrzymuje wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez zmienne zasobów. Należy włączyć pokrewne harmonogram **StartVM zaplanowane**.|
|Scheduled_StartVM | Zdefiniowane przez codziennie użytkownika | Uruchamia element runbook Scheduled_Parent z parametrem *Start* każdego dnia o określonej godzinie.  Wszystkie maszyny wirtualne, które spełniają reguły zdefiniowane przez odpowiednie zmienne jest uruchamiana automatycznie. Należy włączyć pokrewne harmonogram **StopVM zaplanowane**.|
|StopVM sekwencjonowania | 1:00:00 (UTC), każdy piątek | Uruchamia element runbook Sequenced_Parent z parametrem *zatrzymać* każdy piątek o określonej godzinie. Sekwencyjnie (rosnąco) zatrzymuje wszystkich maszyn wirtualnych przy użyciu tagu z **SequenceStop** wynika z odpowiednich zmiennych. Zapoznaj się z sekcją elementów Runbook, aby uzyskać więcej informacji o wartości tagów i zmienne zasobów. Należy włączyć pokrewne harmonogram **Sequenced StartVM**.|
|StartVM sekwencjonowania | 1:00 PM (UTC), każdy poniedziałek | Uruchamia element runbook Sequenced_Parent z parametrem *Start* w każdy poniedziałek o określonej godzinie. Sekwencyjnie (malejąco) uruchamiania wszystkich maszyn wirtualnych przy użyciu tagu z **SequenceStart** wynika z odpowiednich zmiennych. Zapoznaj się z sekcją elementów Runbook, aby uzyskać więcej informacji o wartości tagów i zmienne zasobów. Należy włączyć pokrewne harmonogram **Sequenced StopVM**.|

## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics

Automatyzacja tworzy dwa typy rekordów w repozytorium OMS: zadania dzienniki i strumieni zadania.

### <a name="job-logs"></a>Dzienniki zadań

Właściwość | Opis|
----------|----------|
Obiekt wywołujący |  Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań.|
Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobLogs.|
CorrelationId | Identyfikator GUID jest Identyfikatorem korelacji zadania elementu runbook.|
JobId | Identyfikator GUID jest Identyfikatorem zadania elementu runbook.|
operationName | Określa typ operacji wykonywanej na platformie Azure. Do automatyzacji wartość jest zadanie.|
resourceId | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
ResourceGroup | Określa nazwę grupy zasobów zadania elementu Runbook.|
ResourceProvider | Określa nazwę usługi platformy Azure, która zapewnia zasoby do wdrożenia i zarządzania. W przypadku usługi Automation wartością jest Azure Automation.|
ResourceType | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
resultType | Stan zadania elementu Runbook. Możliwe wartości:<br>— Uruchomione<br>— Zatrzymane<br>— Wstrzymane<br>— Nie powiodło się<br>— Powiodło się|
resultDescription | Opisuje stan wyniku zadania elementu Runbook. Możliwe wartości:<br>— Zadanie jest uruchomione<br>— Zadanie nie powiodło się<br>— Zadanie zostało ukończone|
RunbookName | Określa nazwę elementu Runbook.|
SourceSystem | Określa system źródłowy dla przesłanych danych. W przypadku automatyzacji wartość jest OpsManager|
StreamType | Określa typ zdarzenia. Możliwe wartości:<br>— Pełne<br>— Dane wyjściowe<br>— Błąd<br>— Ostrzeżenie|
SubscriptionId | Określa identyfikator subskrypcji zadania.
Time | Data i godzina dla wykonania zadania elementu Runbook.|

### <a name="job-streams"></a>Strumienie zadania

Właściwość | Opis|
----------|----------|
Obiekt wywołujący |  Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań.|
Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobStreams.|
JobId | Identyfikator GUID jest Identyfikatorem zadania elementu runbook.|
operationName | Określa typ operacji wykonywanej na platformie Azure. Do automatyzacji wartość jest zadanie.|
ResourceGroup | Określa nazwę grupy zasobów zadania elementu Runbook.|
resourceId | Określa identyfikator zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
ResourceProvider | Określa nazwę usługi platformy Azure, która zapewnia zasoby do wdrożenia i zarządzania. W przypadku usługi Automation wartością jest Azure Automation.|
ResourceType | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
resultType | Wynik zadania elementu Runbook w czasie wygenerowania zdarzenia. Możliwa wartość to:<br>— W toku|
resultDescription | Obejmuje strumień wyjściowy z elementu Runbook.|
RunbookName | Nazwa elementu Runbook.|
SourceSystem | Określa system źródłowy dla przesłanych danych. Do automatyzacji wartość jest OpsManager.|
StreamType | Typ strumienia zadania. Możliwe wartości:<br>-Postępu<br>— Dane wyjściowe<br>— Ostrzeżenie<br>— Błąd<br>— Debugowanie<br>— Pełne|
Time | Data i godzina dla wykonania zadania elementu Runbook.|

Po wykonaniu wyszukiwania dziennika zwracające rekordów kategorii **JobLogs** lub **JobStreams**, można wybrać **JobLogs** lub **JobStreams**widoku, który wyświetla zestaw kafelków podsumowania zwrócona przez wyszukiwanie aktualizacji.

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla rekordów dzienników zbieranych przez to rozwiązanie.

Zapytanie | Opis|
----------|----------|
Znajdź zadania dla elementu runbook ScheduledStartStop_Parent, które zakończyły się pomyślnie | Wyszukiwanie kategorii == "JobLogs" &#124; gdzie (RunbookName_s == "ScheduledStartStop_Parent") &#124; gdzie (ResultType == "Completed") &#124; Podsumuj AggregatedValue = count() przez ResultType, bin (TimeGenerated, 1 godz.) &#124; Sortuj według TimeGenerated desc|
Znajdź zadania dla elementu runbook SequencedStartStop_Parent, które zakończyły się pomyślnie | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="viewing-the-solution"></a>Wyświetlanie rozwiązania

Aby uzyskać dostęp do rozwiązania, przejdź do swojego konta automatyzacji, wybierz **obszaru roboczego** w obszarze **powiązane zasoby**. Na stronie analizy dzienników wybierz **rozwiązań** w obszarze **ogólne**. Na **rozwiązań** wybierz rozwiązanie **Start-Stop-VM [obszaru roboczego]** z listy.

Wybranie rozwiązania powoduje wyświetlenie **Start-Stop-VM [obszaru roboczego]** strona rozwiązania. W tym miejscu można przejrzeć ważne informacje takie jak **StartStopVM** kafelka. Jak obszaru roboczego analizy dzienników tego kafelka Wyświetla graficzną reprezentację zadań elementu runbook dla rozwiązania, które zostały uruchomione i zakończyły się pomyślnie i liczba.

![Strona rozwiązania Automatyzacja zarządzania aktualizacjami](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

W tym miejscu można wykonywać dalszej analizy rekordów zadań, klikając Kafelek pierścień. Pulpit nawigacyjny rozwiązania zawiera historię zadania i wstępnie zdefiniowane zapytania wyszukiwania dziennika. Przełącz się do portalu zaawansowane analizy dziennika do wyszukiwania oparte na zapytania wyszukiwania.

## <a name="configure-email-notifications"></a>Skonfiguruj powiadomienia e-mail

Aby skonfigurować powiadomienia e-mail, po wdrożeniu rozwiązania, zmodyfikuj trzy następujące zmienne:

* External_EmailFromAddress: Określ adres e-mail nadawcy.
* External_EmailToAddress: Określ listę adresów e-mail rozdzielana przecinkami (user@hotmail.com, user@outlook.com) do odbierania wiadomości e-mail z powiadomieniem.
* External_IsSendEmail: Ustaw **tak** do odbierania wiadomości e-mail. Aby wyłączyć powiadomienia e-mail, ustaw wartość na **nr**.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modyfikuj harmonogramy uruchamiania i wyłączania

Zarządzanie uruchamiania i wyłączania harmonogramów w tym rozwiązaniu opisano te same kroki, zgodnie z opisem w [Planowanie elementu runbook automatyzacji Azure](automation-schedules.md).

Konfigurowanie rozwiązania pod kątem wszystko maszyn wirtualnych przez pewien czas jest obsługiwane. W tym celu należy:

1. Upewnij się, dodaniu grup zasobów dla maszyn wirtualnych do zamykania w **External_Start_ResourceGroupNames** zmiennej.
2. Utwórz swój własny harmonogram czas, który chcesz zamknąć maszyn wirtualnych.
3. Przejdź do **ScheduledStartStop_Parent** runbook i kliknij przycisk **harmonogram**. Dzięki temu można zaznaczyć harmonogram, który został utworzony w poprzednim kroku.
4. Wybierz **parametry i ustawienia uruchamiania** i ustaw dla parametru akcji do "Stop".
5. Kliknij przycisk **OK**, aby zapisać zmiany.

## <a name="update-the-solution"></a>Aktualizacja rozwiązania

Jeśli wdrożono poprzedniej wersji tego rozwiązania, należy najpierw usunąć go z Twojego konta przed wdrożeniem zaktualizowaną wersję. Wykonaj kroki, aby [usunąć rozwiązanie](#remove-the-solution) , a następnie postępuj zgodnie z powyższymi instrukcjami do [wdrażania rozwiązania](#deploy-the-solution).

## <a name="remove-the-solution"></a>Usuń rozwiązanie

Jeśli zdecydujesz, że nie trzeba używać rozwiązania, należy ją usunąć z konta automatyzacji. Usuwanie rozwiązania spowoduje usunięcie tylko elementy runbook. Nie powoduje usunięcia harmonogramy lub zmiennych, które zostały utworzone, gdy rozwiązanie zostało dodane. Zasoby, które należy usunąć ręcznie, jeśli nie używasz je z innymi elementami runbook.

Aby usunąć rozwiązanie, wykonaj następujące czynności:

1. Twoje konto usługi Automatyzacja, zaznacz **obszaru roboczego** z lewej strony.
1. Na **rozwiązań** wybierz rozwiązanie **Start-Stop-VM [obszaru roboczego]**. Na **VMManagementSolution [obszaru roboczego]** strony z menu wybierz opcję **usunąć**.<br><br> ![Usuwanie maszyny Wirtualnej Mgmt rozwiązania](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. W **Usuń rozwiązanie** okna, upewnij się, że chcesz usunąć rozwiązania.
1. Informacje została zweryfikowana i rozwiązanie zostanie usunięty, można śledzić postęp w obszarze **powiadomienia** z menu. Nastąpi powrót do **rozwiązań** strony po rozpoczęciu procesu, aby usunąć rozwiązania.

Konto usługi Automatyzacja i obszaru roboczego analizy dzienników nie są usuwane w ramach tego procesu. Jeśli nie chcesz zachować obszaru roboczego analizy dzienników, musisz ręcznie je usunąć. Można to zrobić w portalu Azure:

1. Wybierz z portalu Azure ekranu głównego, **analizy dzienników**.
1. Na **analizy dzienników** wybierz obszar roboczy.
1. Wybierz **usunąć** z menu na stronie Ustawienia obszaru roboczego.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat sposobu konstruowania różne zapytania i przejrzyj dzienniki zadania automatyzacji z analizy dzienników, zobacz [Zaloguj wyszukiwania analizy dzienników](../log-analytics/log-analytics-log-searches.md).
* Aby dowiedzieć się więcej o wykonywaniu elementów runbook, sposobie monitorowania zadań elementów runbook i innych szczegółach technicznych, zobacz [Track a runbook job](automation-runbook-execution.md) (Śledzenie zadania elementu runbook).
* Aby dowiedzieć się więcej na temat analizy dzienników i źródeł danych kolekcji, zobacz [Azure zbierania danych magazynu w omówieniu analizy dzienników](../log-analytics/log-analytics-azure-storage.md).
