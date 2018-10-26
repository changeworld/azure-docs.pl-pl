---
title: Wykonanie elementu Runbook w usłudze Azure Automation
description: W tym artykule opisano szczegóły dotyczące sposobu przetwarzania elementu runbook w usłudze Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 85ff0013b469c006d1922c3994ec58a0595d1dd2
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091989"
---
# <a name="runbook-execution-in-azure-automation"></a>Wykonanie elementu Runbook w usłudze Azure Automation

Po uruchomieniu elementu runbook w usłudze Azure Automation tworzone jest zadanie. Zadanie jest pojedynczym wystąpieniem wykonania elementu runbook. Proces roboczy usługi Azure Automation jest przypisany do każdego zadania. Procesy robocze są współużytkowane przez wiele kont platformy Azure, zadaniami z różnych kont automatyzacji są odizolowane od siebie nawzajem. Możesz nie mieć kontrolować za pośrednictwem których procesu roboczego obsługującym żądanie dla zadania. Pojedynczy element runbook może mieć wiele zadań, uruchomione w tym samym czasie. Środowisko do wykonywania zadań z tego samego konta usługi Automation mogą być ponownie używane. Przeglądając listę elementów runbook w witrynie Azure portal, wyświetla stan wszystkich zadań, które zostały uruchomione dla każdego elementu runbook. Można wyświetlić listę zadań dla każdego elementu runbook śledzić stan każdego z nich. Dzienniki zadania są przechowywane przez maksymalnie 30 dni. Aby uzyskać opis stany różne zadania [stany zadania](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Na poniższym diagramie przedstawiono cykl życia zadania elementu runbook w taki sposób, aby uzyskać [graficznych elementów runbook](automation-runbook-types.md#graphical-runbooks) i [elementami runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Stany zadań - przepływu pracy programu PowerShell](./media/automation-runbook-execution/job-statuses.png)

Na poniższym diagramie przedstawiono cykl życia zadania elementu runbook w taki sposób, aby uzyskać [elementy runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks).

![Stany zadań - skrypt programu PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

Zadania mają dostęp do zasobów platformy Azure przez utworzenie połączenia z subskrypcją platformy Azure. Tylko mają dostęp do zasobów w centrum danych, jeśli te zasoby są dostępne w chmurze publicznej.

## <a name="job-statuses"></a>Stany zadania

W poniższej tabeli opisano różne stany, które można wykonać zadania. Program PowerShell udostępnia dwa typy błędów, jak i niepowodujące błędów. Kończenie błędy ustawiono stan elementu runbook **niepowodzenie** Jeśli występują one. Błędy niepowodujący umożliwiają skryptu, który ma być kontynuowana nawet, po ich wystąpieniu. Korzysta z przykładem błąd niepowodujący `Get-ChildItem` polecenia cmdlet ze ścieżką, która nie istnieje. Program PowerShell widzi, że ścieżka nie istnieje, zgłasza błąd i kontynuuje do następnego folderu. Ten błąd nie ustawiono stan elementu runbook **niepowodzenie** i może być oznaczona jako **Ukończono**. Aby wymusić elementu runbook, aby zatrzymać na błąd niepowodujący, można użyć `-ErrorAction Stop` na polecenia cmdlet.

| Stan | Opis |
|:--- |:--- |
| Zakończone |Zadanie zostało ukończone pomyślnie. |
| Niepowodzenie |Aby uzyskać [graficzny i przepływu pracy programu PowerShell elementów runbook](automation-runbook-types.md), element runbook kompilacja nie powiodła się. Aby uzyskać [skrypt programu PowerShell, elementów runbook](automation-runbook-types.md), nie można uruchomić elementu runbook lub dla zadania zaalokowano wyjątek. |
| Nie powiodło się, oczekiwania na zasoby |Zadanie nie powiodło się, ponieważ osiągnęła ona [udział](#fair-share) ograniczyć trzy razy, a następnie z tego samego punktu kontrolnego lub od początku elementu runbook każdej godzina uruchomienia. |
| W kolejce |Zadanie oczekuje na zasoby procesu roboczego automatyzacji na udostępnienie tak, aby go uruchomić. |
| Uruchamianie |Zadanie zostało przypisane do procesu roboczego, a system zaczyna go. |
| Wznawianie |System wznawia zadanie po został wstrzymany. |
| Działanie |Zadanie zostało uruchomione. |
| Uruchamianie i oczekiwania na zasoby |Zadanie zostało zwolnione, ponieważ osiągnęła ona [udział](#fair-share) limit. Wznawia wkrótce z ostatniego punktu kontrolnego. |
| Zatrzymano |Zadanie zostało zatrzymane przez użytkownika, przed jego ukończeniem. |
| Zatrzymywanie |System jest zatrzymywania zadania. |
| Zawieszone |Zadanie zostało zawieszone przez użytkownika, przez system lub za pomocą polecenia w elemencie runbook. Jeśli element runbook nie ma punktu kontrolnego, rozpoczyna się od początku elementu runbook. Ma punkt kontrolny, może ponownie uruchomić i wznowić z ostatniego punktu kontrolnego. Element runbook działa tylko zawieszony przez system, gdy wystąpi wyjątek. Domyślnie ustawiono ErrorActionPreference **Kontynuuj**, co oznacza, że zadanie jest uruchomiony w przypadku błędu. Jeśli ustawiono tę zmienną preferencji **zatrzymać**, a następnie zadanie zawiesza się w przypadku błędu. Dotyczy [graficzny i przepływu pracy programu PowerShell elementów runbook](automation-runbook-types.md) tylko. |
| Wstrzymywanie |System próbuje zawiesić zadanie na żądanie użytkownika. Element runbook musi dotrzeć do swojego następnego punktu kontrolnego, zanim może zostać zawieszone. Jeśli przekazywany już ostatni punkt kontrolny, a następnie zostanie zakończona, zanim może zostać zawieszone. Dotyczy [graficzny i przepływu pracy programu PowerShell elementów runbook](automation-runbook-types.md) tylko. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Wyświetlanie stanu zadań w witrynie Azure portal

Możesz wyświetlić podsumowania stanu wszystkich zadań elementów runbook lub przejść do szczegółów określonych zadania elementu runbook w witrynie Azure portal. Możesz również Konfigurowanie integracji z obszarem roboczym usługi Log Analytics do przekazywania strumieni zadań i stanu zadania elementu runbook. Aby uzyskać więcej informacji na temat integracji z usługą Log Analytics, zobacz [przekazywać strumienie zadania i stan zadania z usługi Automation do usługi Log Analytics](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Podsumowanie zadań elementu runbook usługi Automation

Po prawej stronie wybranego konta usługi Automation, zostanie wyświetlone podsumowanie wszystkich zadań elementów runbook w ramach **statystyki zadania** kafelka.

![Kafelek statystyki zadania](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Ten Kafelek zawiera liczbę oraz graficzną reprezentację stanu zadania dla wszystkich zadań wykonywanych.

Klikając Kafelek Wyświetla **zadań** strony, która obejmuje jest skrócona lista wszystkich zadań wykonywanych. Ta strona pokazuje stan, godziny rozpoczęcia i czas ukończenia.

![Strona zadania konta usługi Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Lista zadań można filtrować, wybierając **filtrowania zadań** i filtrowanie w jeden element runbook, stan zadania, lub z listy rozwijanej, a zakres czasu do przeszukania.

![Filtr stanu zadania](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Alternatywnie, można wyświetlić szczegóły podsumowania zadania dla określonego elementu runbook przez wybranie tego elementu runbook z **elementów Runbook** strony na Twoim koncie usługi Automation, a następnie wybierz pozycję **zadań** kafelka. Przedstawia informacje o tej akcji **zadań** strony, w tym miejscu możesz kliknąć przycisk rekord zadania, aby wyświetlić szczegóły i jego dane wyjściowe.

![Strona zadania konta usługi Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Podsumowanie zadania

Można wyświetlić listę wszystkich zadań, które zostały utworzone dla określonego elementu runbook oraz ich najnowszy stan. Można filtrować tej listy według stanu zadania i zakresu dat dla ostatniej zmiany zadania. Aby wyświetlić szczegółowe informacje i jego dane wyjściowe, kliknij nazwę zadania. Widok szczegółowy zadania zawiera wartości parametrów elementu runbook udostępnione danemu zadaniu.

Następujące kroki służą do wyświetlania zadań elementu runbook.

1. W witrynie Azure portal wybierz **automatyzacji** i następnie wybierz nazwę konta usługi Automation.
2. Z Centrum, wybierz **elementów Runbook** a następnie na **elementów Runbook** strony wybierz element runbook z listy.
3. Na stronie dla wybranego elementu runbook kliknij **zadań** kafelka.
4. Kliknij jedno z zadań na liście, a następnie na stronie Szczegóły zadania elementu runbook można wyświetlić szczegóły i jego dane wyjściowe.

## <a name="retrieving-job-status-using-windows-powershell"></a>Pobieranie stanu zadania za pomocą programu Windows PowerShell

Możesz użyć [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) do pobrania zadań utworzonych dla elementu runbook i szczegółów dotyczących określonego zadania. Po uruchomieniu elementu runbook za pomocą środowiska Windows PowerShell przy użyciu [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), a następnie zwraca zadania wynikowe. Użyj [Get AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) można pobrać zadania danych wyjściowych.

Następujące przykładowe polecenia Pobierz ostatnie zadanie przykładowego elementu runbook i wyświetl jego stan, wartości podanych dla parametrów elementu runbook, a dane wyjściowe z zadania.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Poniższy przykład pobiera dane wyjściowe dla określonego zadania i zwraca każdego rekordu. W tym przypadku Wystąpił wyjątek dla jednego z rekordów, wyjątek jest zapisywany zamiast wartości. To zachowanie jest przydatne, ponieważ wyjątki zapewniają dodatkowe informacje, które mogą nie być rejestrowane normalnie w danych wyjściowych.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>Uzyskiwanie szczegółowych informacji z dziennika aktywności

Inne szczegóły, takie jak osoba lub uwagę, że uruchomieniu elementu runbook można pobrać z dziennika aktywności dla konta usługi automation. W poniższym przykładzie programu PowerShell zawiera ostatni użytkownik do uruchomienia danego elementu runbook:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$rg = "ResourceGroup01"
$AutomationAccount = "MyAutomationAccount"
$RunbookName = "Test-Runbook"
$JobResourceID = "/subscriptions/$subid/resourcegroups/$rg/providers/Microsoft.Automation/automationAccounts/$AutomationAccount/jobs"

Get-AzureRmLog -ResourceId $JobResourceID -MaxRecord 1 | Select Caller
```

## <a name="fair-share"></a>Udział

Aby udostępnianie zasobów między wszystkie elementy runbook w chmurze, Azure Automation będzie tymczasowo zwolnienie lub Zatrzymaj dowolnego zadania, które są uruchomione przez ponad trzy godziny. Zadania dla [oparte na programie PowerShell elementów runbook](automation-runbook-types.md#powershell-runbooks) i [elementy runbook języka Python](automation-runbook-types.md#python-runbooks) zostaną zatrzymane i ponownie nie jest uruchomiona, i stan zadania to zatrzymana.

W przypadku długo działających zadań zaleca się użycie [hybrydowego procesu roboczego elementu runbook](automation-hrw-run-runbooks.md#job-behavior). Hybrydowych procesów roboczych Runbook nie są ograniczone przez udział, a nie mają ograniczenie na jak długo element runbook może wykonać. Inne zadania [limity](../azure-subscription-service-limits.md#automation-limits) zastosowanie zarówno do piaskownic usługi Azure, jak i hybrydowych procesów roboczych Runbook. Gdy hybrydowych procesów roboczych Runbook nie są ograniczone przez limit udział 3 godziny, elementy runbook uruchomione na nich nadal powinien zostać opracowany do obsługi zachowania ponownego uruchamiania z infrastruktury lokalnej nieoczekiwane problemy.

Innym rozwiązaniem jest optymalizacja elementu runbook przy użyciu podrzędnych elementów runbook. Element runbook działa w pętli za pomocą tej samej funkcji liczby zasobów, takich jak operacji bazy danych w wielu bazach danych, należy przenieść tę funkcję, aby [podrzędnego elementu runbook](automation-child-runbooks.md) i wywoływać ją za pomocą [ Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) polecenia cmdlet. Każdy z tych podrzędnych elementów runbook będzie wykonywany równolegle w osobnych procesach, co spowoduje skrócenie całkowitego czasu potrzebnego nadrzędnemu elementowi runbook do zakończenia zadania. Możesz użyć [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) polecenia cmdlet w elemencie runbook, aby sprawdzić stan zadania dla każdego elementu podrzędnego, jeśli istnieją operacje, które należy wykonać po zakończeniu działania podrzędnego elementu runbook.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat różnych metod, które mogą służyć do uruchamiania elementu runbook w usłudze Azure Automation, zobacz [uruchamianie elementu runbook w usłudze Azure Automation](automation-starting-a-runbook.md)
