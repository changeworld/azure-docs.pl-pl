---
title: Harmonogramy w Azure Automation
description: Harmonogramy automatyzacji służą do planowania automatycznego uruchamiania elementów Runbook w programie Azure Automation. Opisuje sposób tworzenia harmonogramu i zarządzania nim w programie, dzięki czemu można automatycznie uruchomić element Runbook w konkretnym czasie lub zgodnie z harmonogramem cyklicznym.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4898ba62abdc42d95b77b9a77387bfe71fb4771
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252664"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Planowanie elementu Runbook w usłudze Azure Automation

Aby zaplanować uruchomienie elementu Runbook w Azure Automation o określonym czasie, należy połączyć go z co najmniej jednym harmonogramem. Harmonogram można skonfigurować do uruchamiania jednorazowo lub w harmonogramie godzinnym lub codziennym dla elementów Runbook w Azure Portal. Można je również zaplanować co tydzień, co miesiąc, konkretne dni tygodnia lub dni miesiąca lub określony dzień miesiąca. Element Runbook może zostać powiązany z wieloma harmonogramami, a harmonogram może zostać powiązany z wieloma elementami Runbook.

> [!NOTE]
> Harmonogramy nie obsługują obecnie Azure Automation konfiguracji DSC.

## <a name="powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell

Polecenia cmdlet w poniższej tabeli służą do tworzenia harmonogramów i zarządzania nimi za pomocą programu PowerShell w Azure Automation. Są one dostarczane jako część [modułu Azure PowerShell](/powershell/azure/overview).

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Pobiera harmonogram. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Tworzy nowy harmonogram. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Usuwa harmonogram. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Ustawia właściwości istniejącego harmonogramu. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Pobiera zaplanowane elementy Runbook. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Kojarzy element Runbook z harmonogramem. |
| [Unregister — AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Deskojarzenie elementu Runbook z harmonogramem. |

## <a name="creating-a-schedule"></a>Tworzenie harmonogramu

Nowy harmonogram dla elementów Runbook można utworzyć w Azure Portal lub przy użyciu programu PowerShell.

> [!NOTE]
> Azure Automation korzysta z najnowszych modułów na koncie usługi Automation, gdy zostanie uruchomione nowe zaplanowane zadanie.  Aby uniknąć wpływu elementów Runbook i procesów, które automatyzują, należy najpierw przetestować wszystkie elementy Runbook, które mają połączone harmonogramy z kontem usługi Automation dedykowanym do testowania.  Spowoduje to zweryfikowanie, czy zaplanowane elementy Runbook nadal działają poprawnie, a jeśli nie, można dodatkowo rozwiązać problemy i zastosować wszelkie zmiany wymagane przed przeprowadzeniem migracji zaktualizowanej wersji elementu Runbook do środowiska produkcyjnego.
> Twoje konto usługi Automation nie otrzymuje automatycznie żadnych nowych wersji modułów, o ile nie zostały zaktualizowane ręcznie poprzez wybranie opcji [Aktualizuj moduły platformy Azure](../automation-update-azure-modules.md) z **modułów**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Aby utworzyć nowy harmonogram w Azure Portal

1. W Azure Portal z konta usługi Automation wybierz pozycję **harmonogramy** w sekcji **udostępnione zasoby** po lewej stronie.
2. Kliknij pozycję **Dodaj harmonogram** w górnej części strony.
3. W okienku **Nowy harmonogram** wpisz **nazwę** i opcjonalnie **Opis** nowego harmonogramu.
4. Wybierz, czy harmonogram ma być uruchamiany jednokrotnie, czy też w harmonogramie, wybierając **jeden raz** lub **cyklicznie**. W przypadku wybrania opcji **raz** Określ **godzinę rozpoczęcia**, a następnie kliknij przycisk **Utwórz**. Jeśli wybierzesz **opcję cyklicznie**, określ **czas rozpoczęcia** i dla **każdego cykliczności co**, wybierz częstotliwość powtarzania elementu Runbook według **godziny**, **dnia**, **tygodnia**lub **miesiąca**.
    1. Jeśli wybierzesz opcję **tydzień**, otrzymasz listę dni tygodnia do wyboru. Wybierz dowolną liczbę dni. Pierwsze uruchomienie harmonogramu zostanie wykonane pierwszego dnia wybranego po czasie rozpoczęcia. Na przykład, aby wybrać harmonogram weekendowy, wybierz **soboty** i **Niedziela**.

       ![Ustawianie cyklicznego harmonogramu weekendu](../media/schedules/week-end-weekly-recurrence.png)

    2. W przypadku wybrania **miesiąca**są dostępne różne opcje. Dla opcji **miesięczne wystąpienia** wybierz opcję **miesiąc** lub dni **tygodnia**. Jeśli wybierzesz **miesiąc dni**, zostanie wyświetlony kalendarz, który pozwala wybrać dowolną liczbę dni. Jeśli wybierzesz datę, taką jak 31, która nie występuje w bieżącym miesiącu, harmonogram nie zostanie uruchomiony. Jeśli chcesz, aby harmonogram był uruchamiany w ostatnim dniu miesiąca, wybierz pozycję **tak** w obszarze **Uruchom w ostatnim dniu**. Jeśli wybierzesz **tydzień dni**, zostanie wyświetlona opcja **Powtarzaj każdy** z nich. Wybierz **pierwszy**, **drugi**, **trzeci**, **czwarty**lub **ostatni**. Na koniec wybierz dzień do powtórzenia.

       ![Harmonogram miesięczny pierwszego, piętnastu i ostatniego dnia miesiąca](../media/schedules/monthly-first-fifteenth-last.png)

5. Po zakończeniu kliknij przycisk **Utwórz**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Aby utworzyć nowy harmonogram za pomocą programu PowerShell

Do tworzenia harmonogramów służy polecenie cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) . Należy określić godzinę rozpoczęcia harmonogramu oraz częstotliwość, w której ma zostać uruchomiony. W poniższych przykładach pokazano, jak utworzyć wiele różnych scenariuszy harmonogramu.

#### <a name="create-a-one-time-schedule"></a>Utwórz harmonogram jednorazowy

Następujące przykładowe polecenia tworzą harmonogram jednorazowy.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Tworzenie harmonogramu cyklicznego

Następujące przykładowe polecenia pokazują, jak utworzyć cykliczny harmonogram, który jest uruchamiany codziennie o godzinie 1:13:00 przez rok.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Tworzenie tygodniowego harmonogramu cyklicznego

Następujące przykładowe polecenia pokazują, jak utworzyć harmonogram tygodniowy, który jest uruchamiany tylko w dni robocze.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Tworzenie tygodniowego harmonogramu cyklicznego dla weekendów

Następujące przykładowe polecenia pokazują, jak utworzyć harmonogram tygodniowy, który jest uruchamiany tylko w weekendy.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Utwórz harmonogram cykliczny dla pierwszych, 15 i ostatnich dni miesiąca

Następujące przykładowe polecenia pokazują, jak utworzyć cykliczny harmonogram, który jest uruchamiany na 1., 15 i ostatni dzień miesiąca.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Łączenie harmonogramu z elementem Runbook

Element Runbook może zostać powiązany z wieloma harmonogramami, a harmonogram może zostać powiązany z wieloma elementami Runbook. Jeśli element Runbook ma parametry, można podać dla nich wartości. Należy podać wartości parametrów obowiązkowych i podać wartości parametrów opcjonalnych. Te wartości są używane za każdym razem, gdy element Runbook jest uruchamiany zgodnie z harmonogramem. Możesz dołączyć ten sam element Runbook do innego harmonogramu i określić różne wartości parametrów.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Aby połączyć harmonogram z elementem Runbook za pomocą Azure Portal

1. W Azure Portal z konta usługi Automation wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesu** po lewej stronie.
2. Kliknij nazwę elementu Runbook do zaplanowania.
3. Jeśli element Runbook nie jest obecnie połączony z harmonogramem, masz możliwość utworzenia nowego harmonogramu lub połączenia z istniejącym harmonogramem.
4. Jeśli element Runbook ma parametry, można wybrać opcję **Modyfikuj Parametry uruchomieniowe (domyślnie: Azure)** , a okienko **Parametry** jest wyświetlane, gdzie można wprowadzić informacje.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Łączenie harmonogramu z elementem Runbook za pomocą programu PowerShell

Do powiązania harmonogramu można użyć polecenia cmdlet [register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) . Można określić wartości parametrów elementu Runbook za pomocą parametru Parametry. Aby uzyskać więcej informacji na temat określania wartości parametrów, zobacz [Uruchamianie elementu Runbook w Azure Automation](../automation-starting-a-runbook.md).
Następujące przykładowe polecenia pokazują, jak powiązać harmonogram z elementem Runbook za pomocą polecenia cmdlet Azure Resource Manager z parametrami.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Częściowe planowanie elementów Runbook

Częstotliwość, z jaką Azure Automation można skonfigurować dla harmonogramu, wynosi godzinę. Aby harmonogramy były wykonywane częściej niż to możliwe, dostępne są dwie opcje:

* Utwórz [element webhook](../automation-webhooks.md) dla elementu Runbook i Użyj [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) do wywołania elementu webhook. Azure Logic Apps zapewnia bardziej szczegółowy poziom szczegółowości podczas definiowania harmonogramu.

* Utwórz cztery harmonogramy, rozpoczynając od 15 minut od siebie co godzinę. Ten scenariusz umożliwia uruchamianie elementu Runbook co 15 minut przy użyciu różnych harmonogramów.

## <a name="disabling-a-schedule"></a>Wyłączanie harmonogramu

Po wyłączeniu harmonogramu wszystkie elementy Runbook połączone z nim nie będą już działać w tym harmonogramie. Harmonogram można wyłączyć ręcznie lub ustawić czas wygaśnięcia dla harmonogramów z częstotliwością podczas ich tworzenia. Po osiągnięciu czasu wygaśnięcia harmonogram jest wyłączony.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Aby wyłączyć harmonogram z Azure Portal

1. W Azure Portal z konta usługi Automation wybierz pozycję **harmonogramy** w sekcji **udostępnione zasoby** po lewej stronie.
2. Kliknij nazwę harmonogramu, aby otworzyć okienko Szczegóły.
3. Zmień **włączone** na **nie**.

> [!NOTE]
> Jeśli chcesz wyłączyć harmonogram, który ma godzinę rozpoczęcia w przeszłości, należy zmienić datę początkową na godzinę w przyszłości przed jej zapisaniem.

### <a name="to-disable-a-schedule-with-powershell"></a>Aby wyłączyć harmonogram przy użyciu programu PowerShell

Aby zmienić właściwości istniejącego harmonogramu, można użyć polecenia cmdlet [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) . Aby wyłączyć harmonogram, należy określić **wartość false** dla parametru **IsEnabled** .

Następujące przykładowe polecenia pokazują, jak wyłączyć harmonogram dla elementu Runbook za pomocą polecenia cmdlet Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z elementami Runbook w Azure Automation, zobacz [Uruchamianie elementu Runbook w programie Azure Automation](../automation-starting-a-runbook.md)

