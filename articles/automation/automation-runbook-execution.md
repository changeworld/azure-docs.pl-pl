---
title: Wykonanie elementu Runbook w usłudze Azure Automation
description: W tym artykule opisano szczegóły dotyczące sposobu przetwarzania elementu runbook w usłudze Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3dfe16cc09f0453aef8adf8bf87a00aebd2054bc
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214639"
---
# <a name="runbook-execution-in-azure-automation"></a>Wykonanie elementu Runbook w usłudze Azure Automation

Po uruchomieniu elementu runbook w usłudze Azure Automation tworzone jest zadanie. Zadanie jest pojedynczym wystąpieniem wykonania elementu runbook. Proces roboczy usługi Azure Automation jest przypisany do każdego zadania. Procesy robocze są współużytkowane przez wielu kont platformy Azure, zadaniami z różnych kont automatyzacji są odizolowane od siebie nawzajem. Możesz nie mieć kontroli za pośrednictwem których procesu roboczego obsługującym żądanie dla zadania. Pojedynczy element runbook może mieć wiele zadań, uruchomione w tym samym czasie. Środowisko do wykonywania zadań z tego samego konta usługi Automation mogą być ponownie używane. Przeglądając listę elementów runbook w witrynie Azure portal, wyświetla stan wszystkich zadań, które zostały uruchomione dla każdego elementu runbook. Możesz wyświetlić listę zadań dla każdego elementu runbook, aby można było śledzić stan każdego z nich. Aby uzyskać opis stany różne zadania [stany zadania](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Na poniższym diagramie przedstawiono cykl życia zadania elementu runbook w taki sposób, aby uzyskać [graficznych elementów runbook](automation-runbook-types.md#graphical-runbooks) i [elementami runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Stany zadań - przepływu pracy programu PowerShell](./media/automation-runbook-execution/job-statuses.png)

Na poniższym diagramie przedstawiono cykl życia zadania elementu runbook w taki sposób, aby uzyskać [elementy runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks).

![Stany zadań - skrypt programu PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

Zadania mają dostęp do zasobów platformy Azure przez utworzenie połączenia z subskrypcją platformy Azure. Tylko mają dostęp do zasobów w centrum danych, jeśli te zasoby są dostępne w chmurze publicznej.

## <a name="job-statuses"></a>Stany zadania

W poniższej tabeli opisano różne stany, które można wykonać zadania.

| Stan | Opis |
|:--- |:--- |
| Ukończony |Zadanie zostało ukończone pomyślnie. |
| Niepowodzenie |Aby uzyskać [graficzny i przepływu pracy programu PowerShell elementów runbook](automation-runbook-types.md), element runbook kompilacja nie powiodła się. Aby uzyskać [skrypt programu PowerShell, elementów runbook](automation-runbook-types.md), nie można uruchomić elementu runbook lub zadanie napotkało wyjątek. |
| Nie powiodło się, oczekiwania na zasoby |Zadanie nie powiodło się, ponieważ osiągnęła ona [udział](#fair-share) ograniczyć trzy razy, a następnie z tego samego punktu kontrolnego lub od początku elementu runbook każdej godzina uruchomienia. |
| W kolejce |Zadanie oczekuje na zasoby procesu roboczego automatyzacji na udostępnienie tak, aby go uruchomić. |
| Uruchamianie |Zadanie zostało przypisane do procesu roboczego, a system jest w trakcie jego uruchamiania. |
| Wznawianie |System jest w trakcie wznawiania zadania po został wstrzymany. |
| Działanie |Zadanie zostało uruchomione. |
| Uruchamianie i oczekiwania na zasoby |Zadanie zostało zwolnione, ponieważ osiągnęła ona [udział](#fair-share) limit. Wznawia wkrótce z ostatniego punktu kontrolnego. |
| Zatrzymano |Zadanie zostało zatrzymane przez użytkownika, przed jego ukończeniem. |
| Zatrzymywanie |System jest w trakcie zatrzymywania zadania. |
| Zawieszone |Zadanie zostało zawieszone przez użytkownika, przez system lub za pomocą polecenia w elemencie runbook. Zawieszone zadanie można uruchomić ponownie i wznowić z ostatniego punktu kontrolnego lub od początku elementu runbook, jeśli nie ma punktów kontrolnych. Element runbook działa tylko zawieszony przez system, gdy wystąpi wyjątek. Domyślnie ustawiono ErrorActionPreference **Kontynuuj**, co oznacza, że zadanie jest uruchomiony w przypadku błędu. Jeśli ustawiono tę zmienną preferencji **zatrzymać**, a następnie zadanie zawiesza się w przypadku błędu. Dotyczy [graficzny i przepływu pracy programu PowerShell elementów runbook](automation-runbook-types.md) tylko. |
| Wstrzymywanie |System próbuje zawiesić zadanie na żądanie użytkownika. Element runbook musi dotrzeć do swojego następnego punktu kontrolnego, zanim może zostać zawieszone. Jeśli przekazywany już ostatni punkt kontrolny, a następnie zostanie zakończona, zanim może zostać zawieszone. Dotyczy [graficzny i przepływu pracy programu PowerShell elementów runbook](automation-runbook-types.md) tylko. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Wyświetlanie stanu zadań w witrynie Azure portal

Możesz wyświetlić podsumowania stanu wszystkich zadań elementów runbook lub przejść do szczegółów określonych zadania elementu runbook w witrynie Azure portal lub przez skonfigurowanie integracji z obszarem roboczym usługi Log Analytics do przekazywania strumieni zadań i stanu zadania elementu runbook. Aby uzyskać więcej informacji na temat integracji z usługą Log Analytics, zobacz [przekazywać strumienie zadania i stan zadania z usługi Automation do usługi Log Analytics](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Podsumowanie zadań elementu runbook usługi Automation

Po prawej stronie wybranego konta usługi Automation, zostanie wyświetlone podsumowanie wszystkich zadań elementów runbook dla wybranego konta usługi Automation w ramach **statystyki zadania** kafelka.

![Kafelek statystyki zadania](./media/automation-runbook-execution/automation-account-job-status-summary.png).

Ten Kafelek zawiera liczbę oraz graficzną reprezentację stanu zadania dla wszystkich zadań wykonywanych.

Klikając Kafelek Wyświetla **zadań** bloku, który zawiera listę podsumowania wszystkich zadań wykonywanych, stan, wykonywania zadań i czasy rozpoczęcia i zakończenia.

![Blok zadania konta usługi Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Lista zadań można filtrować, wybierając **filtrowania zadań** i filtrowanie w jeden element runbook, stan zadania, lub z listy rozwijanej zakresu dat/godzin do przeszukania.

![Filtr stanu zadania](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Alternatywnie, można wyświetlić szczegóły podsumowania zadania dla określonego elementu runbook przez wybranie tego elementu runbook z **elementów Runbook** bloku konta usługi Automation, a następnie wybierz **zadań** kafelka. Przedstawia **zadań** bloku, w tym miejscu możesz kliknąć przycisk rekord zadania, aby wyświetlić szczegóły i jego dane wyjściowe.

![Blok zadania konta usługi Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Podsumowanie zadania

Można wyświetlić listę wszystkich zadań, które zostały utworzone dla określonego elementu runbook oraz ich najnowszy stan. Można filtrować tej listy według stanu zadania i zakresu dat dla ostatniej zmiany zadania. Aby wyświetlić szczegółowe informacje i jego dane wyjściowe, kliknij nazwę zadania. Widok szczegółowy zadania zawiera wartości parametrów elementu runbook udostępnione danemu zadaniu.

Następujące kroki służą do wyświetlania zadań elementu runbook.

1. W witrynie Azure portal wybierz **automatyzacji** i następnie wybierz nazwę konta usługi Automation.
2. Z Centrum, wybierz **elementów Runbook** a następnie na **elementów Runbook** bloku wybierz element runbook z listy.
3. W bloku dla wybranego elementu runbook kliknij **zadań** kafelka.
4. Kliknij jedno z zadań na liście, a następnie w bloku szczegóły zadania elementu runbook można wyświetlić szczegóły i jego dane wyjściowe.

## <a name="retrieving-job-status-using-windows-powershell"></a>Pobieranie stanu zadania za pomocą programu Windows PowerShell

Możesz użyć [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) do pobrania zadań utworzonych dla elementu runbook i szczegółów dotyczących określonego zadania. Po uruchomieniu elementu runbook za pomocą środowiska Windows PowerShell przy użyciu [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx), a następnie zwraca zadania wynikowe. Użyj [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)danych wyjściowych, aby uzyskać zadanie danych wyjściowych.

Następujące przykładowe polecenia Pobierz ostatnie zadanie przykładowego elementu runbook i wyświetl jego stan, wartości podanych dla parametrów elementu runbook, a dane wyjściowe z zadania.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Poniższy przykład pobiera dane wyjściowe dla określonego zadania i zwraca każdego rekordu. W tym przypadku Wystąpił wyjątek dla jednego z rekordów, wyjątek jest zapisywany zamiast wartości. Jest to przydatne, ponieważ wyjątki mogą dostarczać dodatkowych informacji, które mogą nie być rejestrowane normalnie w danych wyjściowych.

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

Aby udostępnianie zasobów między wszystkie elementy runbook w chmurze, Azure Automation tymczasowo spowoduje zwolnienie wszystkie zadania, po jej uruchomieniu przez trzy godziny. W tym czasie zadania dla [oparte na programie PowerShell elementów runbook](automation-runbook-types.md#powershell-runbooks) zostaną zatrzymane i nie zostaną ponownie uruchomione. Pokazuje stan zadania **zatrzymane**. Ten typ elementu runbook jest zawsze ponownie od samego początku, ponieważ nie obsługują punkty kontrolne.

[Na podstawie przepływu pracy programu PowerShell runbook](automation-runbook-types.md#powershell-workflow-runbooks) wznowienia z ostatnią [punktu kontrolnego](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints). Po uruchomieniu trzy godziny, zadanie elementu runbook została zawieszona przez usługę i jej stanie pokazuje **uruchomiony oraz że trwa oczekiwanie na zasoby**. Po udostępnieniu piaskownicy, element runbook jest automatycznie uruchamiany ponownie przez usługę automatyzacji i wznawia od ostatniego punktu kontrolnego. Jest to normalne zachowanie przepływu pracy programu PowerShell na wstrzymywanie/ponowne uruchomienie. Jeśli element runbook ponownie przekracza trzy godziny środowiska uruchomieniowego, ten proces jest powtarzany, maksymalnie trzy razy. Po trzecim ponownym uruchomieniu, jeśli element runbook nadal nie została ukończona w ciągu trzech godzin, a następnie zadanie elementu runbook nie powiodło się i pokazuje stan zadania **nie powiodło się oczekiwanie na zasoby**. W takim przypadku otrzymujesz następujący wyjątek z określonym błędem.

*Zadanie nie może kontynuować uruchamianie, ponieważ wielokrotnie została wykluczona z tego samego punktu kontrolnego. Upewnij się, że dany element Runbook nie wykona długotrwałej operacji bez utrwalanie stanu.*

To jest ochrona usługi z poziomu elementów runbook uruchomiona przez czas nieokreślony, nie kończą działania, ponieważ nie są one w stanie się to do następnego punktu kontrolnego bez zwalniany ponownie.

Jeśli element runbook ma punktów kontrolnych lub zadanie nie osiągnęła pierwszy punkt kontrolny przed zwalniany, następnie jest uruchamiany ponownie od samego początku.

Do uruchomienia długich zadań, zaleca się używać [hybrydowego procesu roboczego Runbook](automation-hrw-run-runbooks.md#job-behavior). Hybrydowych procesów roboczych Runbook nie są ograniczone przez udział i nie mają ograniczenie na jak długo element runbook może wykonać.

Jeśli używasz elementu runbook przepływu pracy programu PowerShell w systemie Azure podczas tworzenia elementu runbook, należy upewnić się, czas do uruchomienia żadnych działań między dwoma punktami kontrolnymi nie przekracza 3 godziny. Może być konieczne do dodawania punktów kontrolnych do elementu runbook, aby upewnić się, że nie osiągnięcia tego limitu trzech godzin lub Podziel długich długotrwałych operacji. Na przykład element runbook może wykonywać reindex na dużej bazy danych SQL. Jeśli ta jednej operacji nie została zakończona w wyznaczonym udział, zadanie jest załadowane i uruchomione od początku. W takim przypadku należy podzielić operacji reindex na wiele kroków, takie jak indeksowanie jednej tabeli w czasie i następnie Wstaw punkt kontrolny po zakończeniu każdej operacji, dzięki czemu można wznowić zadania, po ostatniej operacji do wykonania.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat różnych metod, które mogą służyć do uruchamiania elementu runbook w usłudze Azure Automation, zobacz [uruchamianie elementu runbook w usłudze Azure Automation](automation-starting-a-runbook.md)
