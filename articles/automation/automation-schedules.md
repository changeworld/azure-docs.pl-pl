---
title: Harmonogramów w usłudze Automatyzacja Azure
description: Harmonogramy automatyzacji służą do planowania elementów runbook automatyzacji Azure do automatycznego uruchamiania. Opisuje sposób tworzenia i zarządzania nimi harmonogramu w, dzięki czemu może automatycznie uruchomić element runbook o określonej godzinie lub zgodnie z harmonogramem cyklicznym.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6c7bd4d4249d304ee7c1df4ae4b8fc0af476b99c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192075"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Planowanie elementu Runbook w usłudze Azure Automation

Aby zaplanować elementu runbook automatyzacji Azure można uruchomić w określonym czasie, łącze do co najmniej jeden harmonogram. Harmonogram można skonfigurować do uruchamiania raz lub pojawiał co godzinę lub codziennie harmonogramu dla elementów runbook w portalu Azure. Można również zaplanować je na co tydzień, co miesiąc, określone dni tygodnia lub dni miesiąca lub określony dzień miesiąca. Element runbook może odnosić się do wielu harmonogramów i harmonogram może mieć wielu elementów runbook z nim połączone.

> [!NOTE]
> Harmonogramy aktualnie nie obsługuje konfiguracji DSC automatyzacji Azure.

## <a name="windows-powershell-cmdlets"></a>Polecenia cmdlet programu Windows PowerShell

Polecenia cmdlet w poniższej tabeli służą do tworzenia i zarządzania nimi harmonogramów z programu Windows PowerShell w automatyzacji Azure. One dostarczane jako część [modułu Azure PowerShell](/powershell/azure/overview).

| Polecenia cmdlet | Opis |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Pobiera harmonogram. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Tworzy nowy harmonogram. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Usuwa harmonogram. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Ustawia właściwości istniejącego harmonogramu. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |Pobiera zaplanowanego elementach runbook. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Kojarzy elementu runbook z harmonogramem. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissociates elementu runbook z harmonogramem. |

## <a name="creating-a-schedule"></a>Tworzenie harmonogramu

Można utworzyć nowy harmonogram dla elementów runbook w portalu Azure lub programu Windows PowerShell.

> [!NOTE]
> Po uruchomieniu nowe zadanie zaplanowane usługi Automatyzacja Azure korzysta z modułów najnowszego na Twoim koncie automatyzacji.  Aby uniknąć wpływu na elementy runbook i ich automatyzacji procesów, należy najpierw przetestować wszystkie elementy runbook, które zostały połączone harmonogramy przy użyciu konta automatyzacji przeznaczonego do testowania.  To sprawdzenie poprawności zaplanowane elementy runbook w dalszym ciągu działać poprawnie, a jeśli nie, dalsze rozwiązywanie problemów z i zastosować zmiany wymagane do przeprowadzenia migracji do środowiska produkcyjnego runbook zaktualizowanej wersji.
> Konta automatyzacji nie automatycznie pobrać wszystkich nowych wersji modułów, chyba że zaktualizowano je ręcznie, wybierając [modułów Azure aktualizacji](automation-update-azure-modules.md) opcję **modułów**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Aby utworzyć nowy harmonogram w portalu Azure

1. W portalu Azure, z Twojego konta automatyzacji wybierz **harmonogramy** w sekcji **udostępnione zasoby** po lewej stronie.
1. Kliknij przycisk **Dodaj harmonogram** w górnej części strony.
1. Na **nowy harmonogram** okienku wpisz **nazwa** i opcjonalnie **opis** nowego harmonogramu.
1. Wybierz harmonogram jest uruchamiane jeden raz, czy reoccurring zgodnie z harmonogramem, wybierając **raz** lub **cyklu**. W przypadku wybrania **raz** określ **godzina rozpoczęcia**, a następnie kliknij przycisk **Utwórz**. W przypadku wybrania **cyklu**, określ **godzina rozpoczęcia** i częstotliwość Częstotliwość elementu runbook przez powtarzanie - **godzina**, **dzień**, **tygodnia**, lub **miesiąca**. W przypadku wybrania **tygodnia** lub **miesiąca** z listy rozwijanej **opcji cyklu** zostanie wyświetlona w okienku i na wybór, **opcji cyklu** okienku są prezentowane i można wybrać dzień tygodnia, w przypadku wybrania **tygodnia**. W przypadku wybrania **miesiąca**, aby wybrać **dni tygodnia** lub określone dni miesiąca w kalendarzu i na koniec ma Uruchom ostatniego dnia miesiąca lub nie, a następnie kliknij przycisk **OK**.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Aby utworzyć nowy harmonogram za pomocą środowiska Windows PowerShell

Możesz użyć [AzureRmAutomationSchedule nowy](/powershell/module/azurerm.automation/new-azurermautomationschedule) polecenia cmdlet, aby utworzyć harmonogramów. Należy określić godzinę rozpoczęcia harmonogramu i częstotliwości, który ma być uruchamiany.

W następujących przykładowych poleceniach pokazano, jak utworzyć harmonogram 15 i 30 co miesiąc, za pomocą polecenia cmdlet usługi Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
$scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
-DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
```

## <a name="linking-a-schedule-to-a-runbook"></a>Powiązanie harmonogramu z elementem runbook

Element runbook może odnosić się do wielu harmonogramów i harmonogram może mieć wielu elementów runbook z nim połączone. Jeśli element runbook ma parametry, można podać wartości dla nich. Należy podać wartości parametrów obowiązkowych i może podać wartości parametrów opcjonalnych. Te wartości są używane przy każdym uruchomieniu elementu runbook przez ten harmonogram. Możesz dołączyć ten sam element runbook do innego harmonogramu i wartości różnych parametrów.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Aby połączyć harmonogramu do elementu runbook w portalu Azure

1. W portalu Azure, z Twojego konta automatyzacji wybierz **Runbook** w sekcji **automatyzacji procesu** po lewej stronie.
1. Kliknij nazwę elementu runbook do zaplanowania.
1. Jeśli element runbook nie jest obecnie połączony z harmonogramem, są oferowane opcję, aby utworzyć nowy harmonogram lub link do istniejącego harmonogramu.
1. Jeśli element runbook ma parametry, można wybrać opcję **zmodyfikuj parametry uruchomieniowe (domyślne: Azure)** i **parametry** okienku są prezentowane, gdzie można wprowadzić informacje w związku z tym.

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Aby połączyć harmonogramu do elementu runbook przy użyciu programu Windows PowerShell

Można użyć [AzureRmAutomationScheduledRunbook rejestru](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) polecenia cmdlet w celu powiązania harmonogramu. Można określić wartości parametrów elementu runbook przy użyciu parametru parametrów. Aby uzyskać więcej informacji na temat określania wartości parametrów, zobacz [uruchamianie elementu Runbook automatyzacji Azure](automation-starting-a-runbook.md).
Następujące przykładowe polecenia pokazują, jak połączyć harmonogramu do elementu runbook za pomocą polecenia cmdlet usługi Azure Resource Manager z parametrami.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Planowanie częściej elementów runbook

Najczęstsze Interwał harmonogramu automatyzacji Azure mogą być konfigurowane dla to jedna godzina. Jeśli potrzebujesz harmonogramów można wykonać częściej niż ta, dostępne są dwie opcje:

* Utwórz [webhook](automation-webhooks.md) dla elementu runbook i użyj [Harmonogram systemu Azure](../scheduler/scheduler-get-started-portal.md) do wywołania elementu webhook. Harmonogram systemu Azure zapewnia bardziej szczegółowo szczegółowości podczas definiowania zgodnie z harmonogramem.

* Utwórz cztery harmonogramy wszystkich rozpoczynające się w ciągu 15 minut od siebie uruchomiona co godzinę. W tym scenariuszu zezwala na uruchomienie co 15 minut z różne harmonogramy elementu runbook.

## <a name="disabling-a-schedule"></a>Wyłączanie harmonogramu

Po wyłączeniu harmonogramu dowolnym elemencie runbook nie jest już powiązany z działa zgodnie z tym harmonogramem. Można ręcznie wyłączyć harmonogram lub ustaw czas wygaśnięcia harmonogramów z częstotliwością po ich utworzeniu. Po osiągnięciu czas wygaśnięcia harmonogramie jest wyłączona.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Aby wyłączyć harmonogram z portalu Azure

1. W portalu Azure, z Twojego konta automatyzacji wybierz **harmonogramy** w sekcji **udostępnione zasoby** po lewej stronie.
1. Kliknij nazwę harmonogramu, aby otworzyć okienko szczegółów.
1. Zmień **włączone** do **nr**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Aby wyłączyć harmonogram przy użyciu programu Windows PowerShell

Można użyć [AzureRmAutomationSchedule zestaw](/powershell/module/azurerm.automation/set-azurermautomationschedule) polecenia cmdlet, aby zmienić właściwości istniejącego harmonogramu. Aby wyłączyć harmonogram, określ **false** dla **IsEnabled** parametru.

W następujących przykładowych poleceniach pokazano, jak wyłączyć harmonogram dla elementu runbook za pomocą polecenia cmdlet usługi Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Kolejne kroki

* Aby rozpocząć pracę z elementami runbook w automatyzacji Azure, zobacz [uruchamianie elementu Runbook automatyzacji Azure](automation-starting-a-runbook.md)