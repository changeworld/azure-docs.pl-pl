---
title: Uruchamianie elementu runbook w usłudze Azure Automation
description: Zawiera podsumowanie różnych metod, które mogą być używane do uruchamiania elementu runbook w usłudze Azure Automation i zawiera szczegółowe informacje na temat korzystania z witryny Azure portal i programu Windows PowerShell.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b065b611c923c4a28dc79c390ffb56ed97b316fd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918454"
---
# <a name="start-a-runbook-in-azure-automation"></a>Uruchamianie elementu runbook w usłudze Azure Automation

Poniższa tabela pozwala określić metodę, aby uruchomić element runbook w usłudze Azure Automation, która jest najbardziej odpowiednie dla danego scenariusza. Ten artykuł zawiera szczegółowe informacje na temat uruchamianie elementu runbook za pomocą witryny Azure portal i za pomocą programu Windows PowerShell. Szczegółowe informacje na temat innych metod znajdują się w dokumentacji, która z poniższych linków.

| **Metoda** | **Właściwości** |
| --- | --- |
| [Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>Najprostszą metodą przy użyciu interfejsu użytkownika interaktywnego.<br> <li>Formularz, aby podać wartości parametrów proste.<br> <li>Łatwo śledzić stan zadania.<br> <li>Dostęp uwierzytelniony przy użyciu konta platformy Azure w. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Wywoływanie z wiersza polecenia za pomocą poleceń cmdlet programu Windows PowerShell.<br> <li>Mogą być dołączane zautomatyzowane rozwiązanie z wielu kroków.<br> <li>Żądanie jest uwierzytelniane za pomocą certyfikatu lub użytkownika OAuth głównej / service podmiotu zabezpieczeń.<br> <li>Podaj wartości parametrów proste i złożone.<br> <li>Śledzić stan zadania.<br> <li>Oprogramowanie klienckie wymagane do obsługi poleceń cmdlet programu PowerShell. |
| [Interfejs API usługi Azure Automation](/rest/api/automation/) |<li>Metoda najbardziej elastyczny, ale także większość złożonej.<br> <li>Wywoływanie z kodu niestandardowego, który może zgłaszać żądania HTTP.<br> <li>Żądanie uwierzytelnić się za pomocą certyfikatu lub użytkownika Oauth głównej / service podmiotu zabezpieczeń.<br> <li>Podaj wartości parametrów proste i złożone. *Jeśli wykonywane jest połączenie runbook języka Python przy użyciu interfejsu API, trzeba go serializować ładunek JSON.*<br> <li>Śledzić stan zadania. |
| [Elementy Webhook](automation-webhooks.md) |<li>Uruchom element runbook z poziomu pojedynczego żądania HTTP.<br> <li>Uwierzytelniane przy użyciu tokenu zabezpieczającego w adresie URL.<br> <li>Klient nie może zastąpić wartości parametrów, które określono podczas tworzenia elementu webhook. Element Runbook może definiować pojedynczy parametr, który jest wypełniana przy użyciu szczegółów żądania HTTP.<br> <li>Brak możliwości, aby śledzić stan zadania przy użyciu adresu URL elementu webhook. |
| [Odpowiadanie na Alert usługi Azure](../log-analytics/log-analytics-alerts.md) |<li>Uruchom element runbook w odpowiedzi na alert usługi Azure.<br> <li>Konfigurowanie elementu webhook dla elementu runbook i link do alertu.<br> <li>Uwierzytelniane przy użyciu tokenu zabezpieczającego w adresie URL. |
| [Harmonogram](automation-schedules.md) |<li>Automatycznie uruchomić element runbook zgodnie z harmonogramem co godzinę, codziennie, co tydzień lub co miesiąc.<br> <li>Manipulowanie harmonogramu za pośrednictwem witryny Azure portal, poleceń cmdlet programu PowerShell lub interfejsu API platformy Azure.<br> <li>Podaj wartości parametrów do użycia z harmonogramem. |
| [Z innego elementu Runbook](automation-child-runbooks.md) |<li>Jako działanie w inny element runbook za pomocą elementu runbook.<br> <li>Przydatne w przypadku funkcji używanych przez wiele elementów runbook.<br> <li>Podaj wartości parametrów podrzędnego elementu runbook, a następnie użyć danych wyjściowych w nadrzędny element runbook. |

Na poniższym obrazie przedstawiono szczegółowe instrukcje krok po kroku proces w cyklu życia w elemencie runbook. Zawiera on różne sposoby, element runbook jest uruchamiany w usłudze Azure Automation, które składniki wymagane do hybrydowego procesu roboczego elementu Runbook do wykonywania elementów runbook usługi Azure Automation i interakcje między poszczególnymi składnikami. Aby dowiedzieć się więcej o wykonywaniu elementów runbook usługi Automation w centrum danych, zobacz [hybrydowych procesów roboczych runbook](automation-hybrid-runbook-worker.md)

![Architektura elementu Runbook](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Uruchamianie elementu runbook za pomocą witryny Azure portal

1. W witrynie Azure portal wybierz **automatyzacji** , a następnie kliknij nazwę konta usługi automation.
2. W menu Centrum wybierz **elementów Runbook**.
3. Na **elementów Runbook** strony, wybierz element runbook, a następnie kliknij przycisk **Start**.
4. Jeśli element runbook ma parametry, zostanie wyświetlony monit podaj wartości z pola tekstowego dla każdego parametru. Aby uzyskać więcej informacji na temat parametrów, zobacz [parametry elementu Runbook](#runbook-parameters).
5. Na **zadania** strony, można wyświetlić stan zadania elementu runbook.

## <a name="start-a-runbook-with-powershell"></a>Uruchamianie elementu runbook za pomocą programu PowerShell

Możesz użyć [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) do uruchamiania elementu runbook za pomocą programu Windows PowerShell. Następujący przykładowy kod uruchamia element runbook o nazwie Test-Runbook.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook zwraca obiekt zadania, za pomocą którego można użyć, aby śledzić jego stan po uruchomieniu elementu runbook. Następnie można użyć tego obiektu zadania przy użyciu [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) do określenia stanu zadania i [Get AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) do uzyskania danych wyjściowych. Następujący przykładowy kod uruchamia element runbook o nazwie Test-Runbook, czeka, aż zostanie zakończona, a następnie wyświetla dane wyjściowe.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Jeśli element runbook wymaga parametrów, a następnie należy podać je jako [hashtable](https://technet.microsoft.com/library/hh847780.aspx). Klucz tablicy skrótów musi pasować do nazwy parametru, a wartość jest wartością parametru. Poniższy przykład pokazuje, jak można uruchomić elementu runbook z dwoma parametrami o nazwie FirstName i LastName, liczbą całkowitą o nazwie RepeatCount i parametrem logicznym o nazwie Show. Aby uzyskać więcej informacji na temat parametrów, zobacz [parametry elementu Runbook](#runbook-parameters) poniżej.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Parametry elementu Runbook

Po uruchomieniu elementu runbook z witryny Azure portal lub programu Windows PowerShell instrukcja jest wysyłana za pośrednictwem usługi sieci web usługi Azure Automation. Ta usługa nie obsługuje parametrów o złożonych typach danych. Jeśli musisz podać wartość parametru złożonego, a następnie użytkownik musi wywołać go z wnętrza innego elementu runbook zgodnie z opisem w [podrzędnych elementów runbook w usłudze Azure Automation](automation-child-runbooks.md).

Usługa sieci web usługi Azure Automation zawiera specjalne funkcje dotyczące parametrów używających określonych typów danych, zgodnie z opisem w poniższych sekcjach:

### <a name="named-values"></a>Nazwane wartości

Jeśli parametr jest typem danych [object], wówczas można użyć następującego formatu JSON do wysłania listy nazwanych wartości: *{Name1: "Wartość1", Nazwa2: "Wartość2", nazwa3: 'Wartość3'}*. Te wartości muszą być typu prostego. Element runbook otrzymuje jako parametr [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) z właściwościami, które odpowiadają każdej nazwanej wartości.

Rozważmy następujący tekstowy element runbook, który akceptuje parametr o nazwie użytkownika.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

Następujący tekst może służyć jako parametru user.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

To powoduje zwrócenie następujących danych wyjściowych:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Tablice

Jeśli parametr jest tablicą, taką jak [array] lub [string []], wówczas można użyć następującego formatu JSON do wysłania listy wartości: *[Wartość1, wartość2, Wartość3]*. Te wartości muszą być typu prostego.

Rozważmy następujący tekstowy element runbook, który akceptuje parametr o nazwie *użytkownika*.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

Następujący tekst może służyć jako parametru user.

```input
["Joe","Smith",2,true]
```

To powoduje zwrócenie następujących danych wyjściowych:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Poświadczenia

Jeśli parametr jest typem danych **PSCredential**, wówczas można podać nazwę usługi Azure Automation [zasób poświadczeń](automation-credentials.md). Element runbook pobiera poświadczenia o podanej nazwie.

Rozważmy następujący tekstowy element runbook, który akceptuje parametr o nazwie poświadczeń.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Następujący tekst może służyć do użytkownika parametru przy założeniu, że wystąpił zasób poświadczeń o nazwie *moje poświadczenia*.

```input
My Credential
```

Zakładając, że nazwa użytkownika w poświadczeniu został *jsmith*, powoduje to następujące dane wyjściowe:

```output
jsmith
```

## <a name="next-steps"></a>Kolejne kroki

* Architektura bieżącego artykułu z elementu runbook zawiera ogólne omówienie elementami runbook zarządzającymi zasobami na platformie Azure i lokalnie za pomocą hybrydowego procesu roboczego elementu Runbook. Aby dowiedzieć się więcej o wykonywaniu elementów runbook usługi Automation w centrum danych, zobacz [hybrydowych procesów roboczych Runbook](automation-hybrid-runbook-worker.md).
* Aby dowiedzieć się więcej na temat tworzenia modułowej elementów runbook w określonej lub typowe funkcje używane przez inne elementy runbook, zobacz [podrzędnych elementów Runbook](automation-child-runbooks.md).
