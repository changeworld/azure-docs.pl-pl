---
title: Harmonogramy w usłudze Azure Automation
description: Harmonogramy automatyzacji są używane do planowania uruchomieniu w usłudze Azure Automation, aby uruchomić automatycznie. W tym artykule opisano sposób tworzenia harmonogramu i zarządzania nim, tak aby można było automatycznie uruchomić księgę runbook w określonym czasie lub zgodnie z harmonogramem cyklicznym.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4898ba62abdc42d95b77b9a77387bfe71fb4771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252664"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Planowanie elementu Runbook w usłudze Azure Automation

Aby zaplanować uruchomienie systemu Runbook w usłudze Azure Automation o określonej godzinie, należy połączyć go z co najmniej jednym harmonogramem. Harmonogram można skonfigurować tak, aby uruchamiał się raz lub w powtarzającym się harmonogramie godzinowym lub dziennym dla śmięty uruchomieniu w witrynie Azure portal. Można je również zaplanować na tygodniowe, miesięczne, określone dni tygodnia lub dni miesiąca lub określonego dnia miesiąca. Element Runbook może zostać powiązany z wieloma harmonogramami, a harmonogram może zostać powiązany z wieloma elementami Runbook.

> [!NOTE]
> Harmonogramy nie obsługują obecnie konfiguracji usługi Azure Automation DSC.

## <a name="powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell

Polecenia cmdlet w poniższej tabeli są używane do tworzenia harmonogramów i zarządzania nimi za pomocą programu PowerShell w usłudze Azure Automation. Są one dostarczane jako część [modułu Azure PowerShell](/powershell/azure/overview).

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Pobierz AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Pobiera harmonogram. |
| [Nowa autoryzacjaRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Tworzy nowy harmonogram. |
| [Usuń-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Usuwa harmonogram. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Ustawia właściwości istniejącego harmonogramu. |
| [Książka Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Pobiera zaplanowane runbooki. |
| [Zarejestruj się AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Kojarzy runbook z harmonogramem. |
| [Wyrejestrowanie-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Odłącza runbook od harmonogramu. |

## <a name="creating-a-schedule"></a>Tworzenie harmonogramu

Można utworzyć nowy harmonogram dla uruchomieniu w witrynie Azure portal lub za pomocą programu PowerShell.

> [!NOTE]
> Usługa Azure Automation używa najnowszych modułów na koncie automatyzacji po uruchomieniu nowego zaplanowanego zadania.  Aby uniknąć wpływu na elementy runbook i procesy, które automatyzują, należy najpierw przetestować wszystkie elementy runbook, które mają połączone harmonogramy z kontem automatyzacji przeznaczonym do testowania.  To sprawdza, czy zaplanowane runbooki nadal działają poprawnie, a jeśli nie, można dalej rozwiązywać problemy i stosować wszelkie zmiany wymagane przed migracją zaktualizowanej wersji likścień likwierkacza do produkcji.
> Konto automatyzacji nie otrzymuje automatycznie żadnych nowych wersji modułów, chyba że zaktualizowano je ręcznie, wybierając opcję [Aktualizuj moduły platformy Azure](../automation-update-azure-modules.md) z **modułów**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Aby utworzyć nowy harmonogram w witrynie Azure portal

1. W witrynie Azure portal z konta automatyzacji wybierz pozycję **Harmonogramy** w sekcji **Zasoby udostępnione** po lewej stronie.
2. Kliknij **pozycję Dodaj harmonogram** u góry strony.
3. W okienku **Nowy harmonogram** wpisz **nazwę** i opcjonalnie **opis** nowego harmonogramu.
4. O tym, czy harmonogram jest uruchamiany jeden raz, czy w harmonogramie powtarzającym się, wybierz opcję **Raz** czy **Cyklicznie**. Jeśli **wybierzesz raz** określić **godzinę rozpoczęcia,** a następnie kliknij przycisk **Utwórz**. W przypadku wybrania opcji **Cykliczne**, określ **czas rozpoczęcia** i dla **powtarzania co**, wybierz częstotliwość powtarzania przez system runbook — według **godziny,** **dnia,** **tygodnia**lub **miesiąca.**
    1. Jeśli wybierzesz **tydzień,** otrzymasz listę dni tygodnia do wyboru. Wybierz dowolną liczbę dni. Pierwszy bieg harmonogramu odbędzie się pierwszego dnia wybranego po godzinie rozpoczęcia. Na przykład, aby wybrać harmonogram weekendowy, wybierz **sobotę** i **niedzielę**.

       ![Ustawianie harmonogramu cyklicznego weekendu](../media/schedules/week-end-weekly-recurrence.png)

    2. Jeśli wybierzesz **miesiąc,** masz różne opcje. W przypadku opcji **Wystąpienia miesięczne** wybierz opcję **Dni miesiąca** lub **Dni tygodnia**. Jeśli wybierzesz **Miesiąc dni,** zostanie wyświetlony kalendarz, który pozwoli wybrać dowolną liczbę dni. Jeśli wybierzesz datę, taką jak 31, która nie występuje w bieżącym miesiącu, harmonogram nie zostanie uruchomiony. Jeśli chcesz, aby harmonogram był uruchamiany ostatniego dnia, wybierz pozycję **Tak** w obszarze **Uruchom ostatni dzień miesiąca**. Jeśli wybierzesz **dni tygodnia,** zostanie wyświetlona opcja **Powtórz każdy.** Wybierz **pierwszy,** **drugi,** **trzeci,** **czwarty**lub **ostatni**. Na koniec wybierz dzień, aby powtórzyć.

       ![Miesięczny harmonogram pierwszego, piętnastego i ostatniego dnia miesiąca](../media/schedules/monthly-first-fifteenth-last.png)

5. Po zakończeniu kliknij przycisk **Utwórz**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Aby utworzyć nowy harmonogram za pomocą programu PowerShell

Polecenie cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) służy do tworzenia harmonogramów. Należy określić godzinę rozpoczęcia dla harmonogramu i częstotliwość, którą powinien uruchomić. Poniższe przykłady pokazują, jak utworzyć wiele różnych scenariuszy harmonogramu.

#### <a name="create-a-one-time-schedule"></a>Tworzenie harmonogramu jednorazowego

Poniższe przykładowe polecenia tworzą harmonogram jednorazowy.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Tworzenie harmonogramu cyklicznego

Poniższe przykładowe polecenia pokazują, jak utworzyć harmonogram cykliczny, który jest uruchamiany codziennie o godzinie 13:00 przez rok.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Tworzenie tygodniowego harmonogramu cyklicznego

Poniższe przykładowe polecenia pokazują, jak utworzyć harmonogram tygodniowy, który działa tylko w dni powszednie.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Tworzenie tygodniowego harmonogramu cyklicznego dla weekendów

Poniższe przykładowe polecenia pokazują, jak utworzyć harmonogram tygodniowy, który działa tylko w weekendy.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Tworzenie harmonogramu cyklicznego dla pierwszych, 15 i ostatnich dni miesiąca

Poniższe przykładowe polecenia pokazują, jak utworzyć harmonogram cykliczny, który działa na 1, 15 i ostatni dzień miesiąca.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Łączenie harmonogramu z runbookiem

Element Runbook może zostać powiązany z wieloma harmonogramami, a harmonogram może zostać powiązany z wieloma elementami Runbook. Jeśli system runbook ma parametry, można podać dla nich wartości. Należy podać wartości dla wszystkich parametrów obowiązkowych i może zawierać wartości dla wszystkich parametrów opcjonalnych. Te wartości są używane za każdym razem, gdy projekt runbook jest uruchamiany przez ten harmonogram. Ten sam projekt runbook można dołączyć do innego harmonogramu i określić różne wartości parametrów.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Aby połączyć harmonogram z zestawem runbook za pomocą witryny Azure portal

1. W witrynie Azure portal z konta automatyzacji wybierz pozycję **Runbooks** w sekcji **Automatyzacja procesów** po lewej stronie.
2. Kliknij nazwę elementu Runbook do zaplanowania.
3. Jeśli element runbook nie jest obecnie połączony z harmonogramem, zostanie zaoferowana opcja utworzenia nowego harmonogramu lub łącza do istniejącego harmonogramu.
4. Jeśli element runbook ma parametry, można wybrać opcję **Modyfikuj ustawienia uruchamiania (domyślne: Azure)** i zostanie wyświetlone okienko **Parametry,** w którym można wprowadzić informacje.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Aby połączyć harmonogram z programem Runbook za pomocą programu PowerShell

Aby połączyć harmonogram, można użyć polecenia cmdlet [Register-AzureRmAutomationScheduledRunbook.](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) Można określić wartości parametrów elementu Runbook za pomocą parametru Parametry . Aby uzyskać więcej informacji na temat określania wartości parametrów, zobacz [Uruchamianie uruchomieniu księgi uruchomieniu w usłudze Azure Automation](../automation-starting-a-runbook.md).
Poniższe przykładowe polecenia pokazują, jak połączyć harmonogram z elementem runbook przy użyciu polecenia cmdlet usługi Azure Resource Manager z parametrami.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Częstsze planowanie obrażeń ekemisji

Najczęściej interwał harmonogram w usłudze Azure Automation można skonfigurować dla jest jedną godzinę. Jeśli harmonogramy wymagają wykonywania częściej niż to, istnieją dwie opcje:

* Utwórz element [webhook](../automation-webhooks.md) dla elementu runbook i użyj [usługi Azure Logic Apps,](../../logic-apps/logic-apps-overview.md) aby wywołać element webhook. Usługa Azure Logic Apps zapewnia bardziej szczegółowe ziarnistość podczas definiowania harmonogramu.

* Utwórz cztery harmonogramy, które rozpoczynają się w ciągu 15 minut od siebie uruchomionego raz na godzinę. W tym scenariuszu umożliwia uruchamianie egobooka co 15 minut z różnymi harmonogramami.

## <a name="disabling-a-schedule"></a>Wyłączanie harmonogramu

Po wyłączeniu harmonogramu każdy schemat runbook połączony z nim nie działa już zgodnie z tym harmonogramem. Harmonogram można ręcznie wyłączyć lub ustawić czas wygaśnięcia dla harmonogramów z częstotliwością podczas ich tworzenia. Po osiągnięciu czasu wygaśnięcia harmonogram jest wyłączony.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Aby wyłączyć harmonogram z witryny Azure portal

1. W witrynie Azure portal z konta automatyzacji wybierz pozycję **Harmonogramy** w sekcji **Zasoby udostępnione** po lewej stronie.
2. Kliknij nazwę harmonogramu, aby otworzyć okienko szczegółów.
3. Zmień **włączono** na **Nie**.

> [!NOTE]
> Jeśli chcesz wyłączyć harmonogram, który ma czas rozpoczęcia w przeszłości, należy zmienić datę rozpoczęcia na godzinę w przyszłości przed zapisaniem go.

### <a name="to-disable-a-schedule-with-powershell"></a>Aby wyłączyć harmonogram z programem PowerShell

Można użyć polecenia cmdlet [Set-AzureRmAutomationSchedule,](/powershell/module/azurerm.automation/set-azurermautomationschedule) aby zmienić właściwości istniejącego harmonogramu. Aby wyłączyć harmonogram, należy określić **false** dla **IsEnabled** parametru.

Poniższe przykładowe polecenia pokazują, jak wyłączyć harmonogram dla zestawu runbook przy użyciu polecenia cmdlet usługi Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z uruchomieniu w usłudze Azure Automation, zobacz [Uruchamianie uruchomieniu w usłudze Azure Automation](../automation-starting-a-runbook.md)

