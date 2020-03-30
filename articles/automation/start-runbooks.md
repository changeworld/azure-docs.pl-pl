---
title: Uruchamianie elementu runbook w usłudze Azure Automation
description: Podsumowuje różne metody, które mogą służyć do uruchamiania uruchomieniu księgi rozkładu w usłudze Azure Automation i zawiera szczegółowe informacje na temat korzystania z witryny Azure portal i programu Windows PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 71dd83db02537ed12dc2e711127e32d90603af6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252651"
---
# <a name="start-a-runbook-in-azure-automation"></a>Uruchamianie elementu runbook w usłudze Azure Automation

Poniższa tabela ułatwia określenie metody uruchamiania elementów runbook w usłudze Azure Automation, która jest najbardziej odpowiednia dla danego scenariusza. Ten artykuł zawiera szczegółowe informacje na temat uruchamiania systemu runbook z witryny Azure portal i programu Windows PowerShell. Szczegółowe informacje na temat innych metod znajdują się w innej dokumentacji, do której można uzyskać dostęp z poniższych łączy.

| **Metoda** | **Właściwości** |
| --- | --- |
| [Portal Azure](#start-a-runbook-with-the-azure-portal) |<li>Najprostsza metoda z interaktywnym interfejsem użytkownika.<br> <li>Formularz, aby zapewnić proste wartości parametrów.<br> <li>Łatwo śledź stan zadania.<br> <li>Dostęp uwierzytelniony za pomocą logowania na platformie Azure. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Wywołanie z wiersza polecenia za pomocą poleceń cmdlet programu Windows PowerShell.<br> <li>Może być dołączony do automatycznego rozwiązania z wieloma krokami.<br> <li>Żądanie jest uwierzytelnione za pomocą certyfikatu lub jednostki zabezpieczeń użytkownika OAuth / jednostki usługi.<br> <li>Podaj proste i złożone wartości parametrów.<br> <li>Śledź stan zadania.<br> <li>Klient wymagany do obsługi poleceń cmdlet programu PowerShell. |
| [Interfejs API automatyzacji platformy Azure](/rest/api/automation/) |<li>Najbardziej elastyczna metoda, ale także najbardziej złożona.<br> <li>Zadzwoń z dowolnego kodu niestandardowego, który może tworzyć żądania HTTP.<br> <li>Żądanie uwierzytelnione z certyfikatem lub zleceniodawcą/jednostką usługi Oauth.<br> <li>Podaj proste i złożone wartości parametrów. *Jeśli wywołujesz system runbook języka Python przy użyciu interfejsu API, ładunek JSON musi być serializowany.*<br> <li>Śledź stan zadania. |
| [Elementów webhook](automation-webhooks.md) |<li>Uruchom księgę uruchomię z pojedynczego żądania HTTP.<br> <li>Uwierzytelniony za pomocą tokenu zabezpieczającego w adresie URL.<br> <li>Klient nie może zastąpić wartości parametrów określonych podczas tworzenia elementu webhook. Runbook można zdefiniować pojedynczy parametr, który jest wypełniany szczegółami żądania HTTP.<br> <li>Brak możliwości śledzenia stanu zadania za pośrednictwem adresu URL elementu webhook. |
| [Odpowiadanie na alert platformy Azure](../log-analytics/log-analytics-alerts.md) |<li>Uruchom zestaw runbook w odpowiedzi na alert platformy Azure.<br> <li>Skonfiguruj element webhook dla elementu runbook i łącze do alertu.<br> <li>Uwierzytelniony za pomocą tokenu zabezpieczającego w adresie URL. |
| [Harmonogram](automation-schedules.md) |<li>Automatycznie uruchom program runbook zgodnie z harmonogramem godzinowym, dziennym, tygodniowym lub miesięcznym.<br> <li>Manipuluj harmonogramem za pośrednictwem witryny Azure portal, poleceń cmdlet programu PowerShell lub interfejsu API platformy Azure.<br> <li>Podaj wartości parametrów, które mają być używane z harmonogramem. |
| [Z innego ekscesu](automation-child-runbooks.md) |<li>Użyj elementa runbook jako działania w innym elementem runbook.<br> <li>Przydatne dla funkcji używanych przez wiele żyła.<br> <li>Podaj wartości parametrów podrzędnej żyjącej i użyj danych wyjściowych w nadrzędnym uruchomieniu. Dasz. |

Na poniższej ilustracji przedstawiono szczegółowy proces krok po kroku w cyklu życia elementów runbook. Zawiera różne sposoby uruchamiania systemu runbook w usłudze Azure Automation, które składniki wymagane dla procesu roboczego hybrydowego systemu runbook do wykonywania elementów runbook usługi Azure Automation i interakcji między różnymi składnikami. Aby dowiedzieć się więcej o wykonywaniu śliwa śliwiania aplikacji Automation w centrum danych, zobacz [hybrydowe elementy owebookowe](automation-hybrid-runbook-worker.md)

![Architektura ekscesu](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Uruchamianie uruchomieniu książki za pomocą witryny Azure portal

1. W witrynie Azure portal wybierz pozycję **Automatyzacja,** a następnie kliknij nazwę konta automatyzacji.
2. W menu Centrum wybierz polecenie **Elementy runbook**.
3. Na stronie **Runbooks** wybierz projekt runbook, a następnie kliknij przycisk **Start**.
4. Jeśli projekt runbook ma parametry, zostanie wyświetlony monit o podanie wartości z polem tekstowym dla każdego parametru. Aby uzyskać więcej informacji na temat parametrów, zobacz [Parametry uruchomieniu](#runbook-parameters).
5. Na stronie **Zadanie** można wyświetlić stan zadania runbooka.

## <a name="start-a-runbook-with-powershell"></a>Uruchamianie eksmisji za pomocą programu PowerShell

Za pomocą [książki Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) można uruchomić system runbook z programem Windows PowerShell. Następujący przykładowy kod uruchamia element Runbook o nazwie Test-Runbook.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook zwraca obiekt zadania, którego można użyć do śledzenia jego stanu po uruchomieniu księgi runbook. Następnie można użyć tego obiektu zadania z [Get-AzureRmAutomationOb](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) do określenia stanu zadania i [Get-AzureRmAutomationJobOutput,](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) aby uzyskać jego dane wyjściowe. Następujący przykładowy kod uruchamia element Runbook o nazwie Test-Runbook, czeka, dopóki praca nie zostanie zakończona, a następnie wyświetla dane wyjściowe.

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

Jeśli projekt runbook wymaga parametrów, należy podać je jako [hashtable](https://technet.microsoft.com/library/hh847780.aspx). Klucz tabeli mieszania musi być zgodny z nazwą parametru, a wartość jest wartością parametru. Poniższy przykład przedstawia sposób uruchamiania elementu Runbook z dwoma parametrami będącymi ciągami o nazwie FirstName i LastName, liczbą całkowitą o nazwie RepeatCount i parametrem logicznym o nazwie Show. Aby uzyskać więcej informacji na temat parametrów, zobacz [Parametry uruchomieniu](#runbook-parameters) poniżej.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Parametry uruchomieniu

Po uruchomieniu systemu runbook z witryny Azure portal lub programu Windows PowerShell, instrukcja jest wysyłana za pośrednictwem usługi sieci web usługi Azure Automation. Ta usługa nie obsługuje parametrów ze złożonymi typami danych. Jeśli chcesz podać wartość złożonego parametru, należy wywołać go wwłakom z innego uruchomieniu, zgodnie z opisem w [podręcznikach podrzędnych w usłudze Azure Automation.](automation-child-runbooks.md)

Usługa sieci web usługi Azure Automation zapewnia specjalne funkcje dla parametrów przy użyciu niektórych typów danych, jak opisano w następujących sekcjach:

### <a name="named-values"></a>Nazwane wartości

Jeśli parametrem jest typ danych [object], można użyć następującego formatu JSON, aby wysłać mu listę nazwanych wartości: *{Name1:'Value1', Name2:'Value2', Name3:'Value3'}*. Te wartości muszą być typu prostego. Elementu runbook odbiera parametr jako [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) z właściwości, które odpowiadają każdej nazwanej wartości.

Rozważmy następujący tekstowy element Runbook, który akceptuje parametr o nazwie user.

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

Poniższego tekstu można użyć jako parametru user.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Powoduje to następujące dane wyjściowe:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Tablice

Jeśli parametr jest tablicą taką jak [tablica] lub [string[]], można użyć następującego formatu JSON, aby wysłać mu listę wartości: *[Value1, Value2, Value3]*. Te wartości muszą być typu prostego.

Rozważmy następujący tekstowy element Runbook, który akceptuje parametr o nazwie *user*.

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

Poniższego tekstu można użyć jako parametru user.

```input
["Joe","Smith",2,true]
```

Powoduje to następujące dane wyjściowe:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Poświadczenia

Jeśli parametrem jest typ danych **PSCredential,** można podać nazwę [zasobu poświadczeń](automation-credentials.md)usługi Azure Automation . Grupa runbook pobiera poświadczenia o określonej nazwie.

Rozważmy następujący tekstowy element Runbook, który akceptuje parametr o nazwie credential.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Poniższy tekst może służyć dla parametru użytkownika, zakładając, że istnieje zasób poświadczeń o nazwie *Moje poświadczenia*.

```input
My Credential
```

Przy założeniu, że nazwa użytkownika w poświadczeniu był *jsmith*, powoduje to następujące dane wyjściowe:

```output
jsmith
```

## <a name="next-steps"></a>Następne kroki

* Architektura systemu runbook w bieżącym artykule zawiera omówienie wysokiego poziomu zarządzania zasobami na platformie Azure i lokalnie za pomocą hybrydowego procesu roboczego systemu runbook. Aby dowiedzieć się więcej o wykonywaniu śliwa wiązek ceł automatyzacji w centrum danych, zobacz [Hybrydowe procesy owe workers.](automation-hybrid-runbook-worker.md)
* Aby dowiedzieć się więcej na temat tworzenia modułowych elementów runbook, które mają być używane przez inne programy runbook dla określonych lub typowych funkcji, zapoznaj się z [podręcznikami podrzędnymi](automation-child-runbooks.md).
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym odwoływania się do języka i modułów szkoleniowych, zobacz [Dokumenty programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
