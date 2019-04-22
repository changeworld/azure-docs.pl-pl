---
title: Harmonogramów w usłudze Azure Automation
description: Harmonogramy automatyzacji służą do zaplanowania elementy runbook w usłudze Azure Automation do automatycznego uruchamiania. W tym artykule opisano, jak tworzyć i zarządzać nimi z harmonogramem na platformie, dzięki czemu może automatycznie uruchomić element runbook o określonej godzinie lub zgodnie z cyklicznym harmonogramem.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 483f9092d29fc40937ed9d54510269af2af30872
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59008650"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Planowanie elementu Runbook w usłudze Azure Automation

Aby zaplanować uruchamianie elementu runbook w usłudze Azure Automation można uruchomić w określonym czasie, należy go powiązać co najmniej jeden harmonogram. Harmonogram można skonfigurować do uruchamiania jednokrotnie lub na pojawiał co godzinę lub codziennie harmonogramu dla elementów runbook w witrynie Azure portal. Można także zaplanować je dla co tydzień, co miesiąc, określone dni tygodnia lub dni miesiąca lub określonego dnia miesiąca. Element Runbook może zostać powiązany z wieloma harmonogramami, a harmonogram może zostać powiązany z wieloma elementami Runbook.

> [!NOTE]
> Harmonogramy aktualnie nie obsługuje konfiguracji usługi Azure Automation DSC.

## <a name="powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell

Polecenia cmdlet w poniższej tabeli służą do tworzenia i zarządzania nimi harmonogramów przy użyciu programu PowerShell w usłudze Azure Automation. Są dostarczane jako część systemu [modułu Azure PowerShell](/powershell/azure/overview).

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Pobiera harmonogram. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Tworzy nowy harmonogram. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Usuwa harmonogram. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Ustawia właściwości istniejącego harmonogramu. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Pobiera elementy runbook. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Kojarzy elementu runbook z harmonogramem. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissociates elementu runbook z harmonogramem. |

## <a name="creating-a-schedule"></a>Tworzenie harmonogramu

W witrynie Azure portal lub za pomocą programu PowerShell, można utworzyć nowy harmonogram dla elementów runbook.

> [!NOTE]
> Usługa Azure Automation używa najnowszych modułów na koncie usługi Automation, po uruchomieniu nowego zaplanowanego zadania.  Aby uniknąć wpływu na Twoje elementy runbook i procesy, które mogą zautomatyzować, należy najpierw sprawdzić wszelkie elementy runbook, które mają połączone harmonogramy przy użyciu konta usługi Automation do testowania w wersji dedykowanej.  To sprawdzenie poprawności zaplanowane elementy runbook będą nadal działać prawidłowo, a jeśli nie, można dodatkowo Rozwiązywanie problemów i zastosować zmiany wymagane do przeprowadzenia migracji z wersji zaktualizowanych elementów runbook do środowiska produkcyjnego.
> Konto usługi Automation nie automatycznie pobrać wszelkich nowych wersji moduły, chyba że masz zaktualizowane je ręcznie, wybierając [aktualizowania modułów platformy Azure](../automation-update-azure-modules.md) opcję **modułów**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Aby utworzyć nowy harmonogram w witrynie Azure portal

1. W witrynie Azure portal na koncie usługi automation wybierz **harmonogramy** sekcji **zasoby udostępnione** po lewej stronie.
2. Kliknij przycisk **Dodaj harmonogram** w górnej części strony.
3. Na **nowy harmonogram** okienku wpisz **nazwa** i opcjonalnie **opis** nowego harmonogramu.
4. Wybierz harmonogram jest uruchamiane jeden raz, czy zgodnie z harmonogramem cyklicznych, wybierając **raz** lub **cyklicznie**. Jeśli wybierzesz **raz** określ **czas rozpoczęcia**, a następnie kliknij przycisk **Utwórz**. Jeśli wybierzesz **cyklicznie**, określ **czas rozpoczęcia** i **Powtórz co**, wybierz częstotliwość, jak często chcesz elementu runbook Powtórz - przez **godzinę**, **dzień**, **tydzień**, lub **miesiąca**.
    1. Jeśli wybierzesz **tygodnia**, otrzymasz listę dni tygodnia do wyboru. Wybierz liczbę dni, jak chcesz. Pierwsze uruchomienie harmonogramu nastąpi w pierwszym dniu wybrane po czasie rozpoczęcia. Na przykład, aby wybrać harmonogram weekendy, należy wybrać **sobota** i **niedziela**.

       ![Ustawienie weekend cykliczne](../media/schedules/week-end-weekly-recurrence.png)

    2. Jeśli wybierzesz **miesiąca**, otrzymuje różne opcje. Aby uzyskać **comiesięczne wystąpienia** , wybierz opcję **dni miesiąca** lub **dni tygodnia**. Jeśli wybierzesz **dni miesiąca**, kalendarz jest wyświetlany, który pozwala wybrać dowolną liczbę dni, jak chcesz. Wybranie daty, takiej jak 31, która nie występuje w bieżącym miesiącu, harmonogram nie będzie uruchamiany. Uruchomienie ostatniego dnia harmonogramu, wybierz opcję **tak** w obszarze **Uruchom ostatniego dnia miesiąca**. Jeśli wybierzesz **dni tygodnia**, **Powtórz co** opcję jest przedstawiany. Wybierz **pierwszy**, **drugi**, **trzeci**, **czwarty**, lub **ostatniego**. Na koniec wybierz dzień do powtarzania.

       ![Harmonogram miesięczny pierwszy, 15 i ostatniego dnia miesiąca](../media/schedules/monthly-first-fifteenth-last.png)

5. Po zakończeniu kliknij przycisk **Utwórz**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Aby utworzyć nowy harmonogram za pomocą programu PowerShell

Możesz użyć [polecenia New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) polecenie cmdlet do tworzenia harmonogramów. Należy określić godzinę rozpoczęcia, harmonogram i częstotliwość, z których ma być uruchamiany. Następujące przykłady przedstawiają sposób tworzenia wielu scenariuszy inny harmonogram.

#### <a name="create-a-one-time-schedule"></a>Utwórz harmonogram

Następujące przykładowe polecenia Utwórz harmonogram.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Utwórz harmonogram cykliczny

Następujące przykładowe polecenia pokazują, jak utworzyć harmonogram cykliczny, które jest uruchamiane codziennie o 13:00 w roku.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Tworzenie cyklicznego harmonogramu tygodniowego

Następujące przykładowe polecenia pokazują, jak utworzyć harmonogram tygodniowy harmonogram, który jest uruchamiany tylko w dni robocze.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Tworzenie harmonogramu tygodniowego cyklicznego weekendy

Następujące przykładowe polecenia pokazują, jak utworzyć działającą w weekendy tylko harmonogramu tygodniowego.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Utwórz harmonogram cykliczny dla funkcji first, 15 i ostatniego dnia miesiąca

Następujące przykładowe polecenia pokazują, jak utworzyć harmonogram cykliczny, które jest uruchamiane na 1, 15 i ostatniego dnia miesiąca.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Powiązanie harmonogramu z elementem runbook

Element Runbook może zostać powiązany z wieloma harmonogramami, a harmonogram może zostać powiązany z wieloma elementami Runbook. Jeśli element runbook ma parametry, można podać wartości dla nich. Należy podać wartości parametrów obowiązkowych i opcjonalnych parametrów może podać wartości. Te wartości są używane w każdym razem, gdy element runbook jest uruchamiany przez tego harmonogramu. Należy dołączyć ten sam element runbook do innego i określić różne wartości parametrów.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>W celu powiązania harmonogramu z elementem runbook za pomocą witryny Azure portal

1. W witrynie Azure portal na koncie usługi automation wybierz **elementów Runbook** sekcji **automatyzacji procesów** po lewej stronie.
2. Kliknij nazwę elementu runbook do zaplanowania.
3. Element runbook nie jest obecnie powiązany z harmonogramem, jest oferowana możliwość utworzenia nowego harmonogramu lub Połącz z istniejącym harmonogramem.
4. Jeśli element runbook ma parametry, można wybrać opcję **zmodyfikuj parametry uruchomieniowe (domyślne: Azure)** i **parametry** okienko zostanie wyświetlony, gdzie można wprowadzić informacje.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Powiązanie harmonogramu z elementem runbook za pomocą programu PowerShell

Możesz użyć [AzureRmAutomationScheduledRunbook rejestru](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) polecenia cmdlet w celu powiązania harmonogramu. Można określić wartości parametrów elementu runbook za pomocą parametru parametrów. Aby uzyskać więcej informacji na temat określania wartości parametrów, zobacz [uruchamianie elementu Runbook w usłudze Azure Automation](../automation-starting-a-runbook.md).
Następujące przykładowe polecenia pokazują, jak w celu powiązania harmonogramu z elementem runbook za pomocą polecenia cmdlet usługi Azure Resource Manager za pomocą parametrów.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Planowanie elementów runbook częściej

Najczęstsze interwał, który można skonfigurować harmonogramu w usłudze Azure Automation dla to jedna godzina. Jeśli potrzebujesz harmonogramy można wykonać częściej niż ta, dostępne są dwie opcje:

* Tworzenie [elementu webhook](../automation-webhooks.md) dla elementu runbook i użyj [usługi Azure Scheduler](../../scheduler/scheduler-get-started-portal.md) do wywoływania elementu webhook. Usługa Azure Scheduler zapewnia bardziej szczegółowo poziom szczegółowości, podczas definiowania zgodnie z harmonogramem.

* Utwórz cztery harmonogramy uruchamiania wszystkich w ciągu 15 minut od siebie uruchamiania raz na godzinę. Ten scenariusz umożliwia przez element runbook uruchamiany co 15 minut z użyciem różnych harmonogramów.

## <a name="disabling-a-schedule"></a>Wyłączanie harmonogramu

Po wyłączeniu harmonogramu każdego elementu runbook, które nie są już połączone z tym rekordem działa zgodnie z tym harmonogramem. Można ręcznie wyłączyć harmonogram lub ustaw czas wygaśnięcia, w przypadku harmonogramów z częstotliwością, podczas ich tworzenia. Po upływie czasu wygaśnięcia, harmonogram jest wyłączony.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Aby wyłączyć harmonogram w witrynie Azure portal

1. W witrynie Azure portal na koncie usługi Automation wybierz **harmonogramy** sekcji **zasoby udostępnione** po lewej stronie.
2. Kliknij nazwę harmonogramu, aby otworzyć okienko szczegółów.
3. Zmiana **włączone** do **nie**.

> [!NOTE]
> Jeśli chcesz wyłączyć harmonogram, który ma godzinę rozpoczęcia w przeszłości, możesz zmienić datę rozpoczęcia, czas w przyszłości przed zapisaniem zmian.

### <a name="to-disable-a-schedule-with-powershell"></a>Aby wyłączyć harmonogram za pomocą programu PowerShell

Możesz użyć [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) polecenia cmdlet, aby zmienić właściwości istniejącego harmonogramu. Aby wyłączyć harmonogram, należy określić **false** dla **IsEnabled** parametru.

Następujące przykładowe polecenia pokazują, jak wyłączyć harmonogram dla elementu runbook za pomocą polecenia cmdlet usługi Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Kolejne kroki

* Aby rozpocząć pracę z elementami runbook w usłudze Azure Automation, zobacz [uruchamianie elementu Runbook w usłudze Azure Automation](../automation-starting-a-runbook.md)

