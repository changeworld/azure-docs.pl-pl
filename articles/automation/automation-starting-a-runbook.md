---
title: Uruchamianie elementu runbook automatyzacji Azure
description: Podsumowanie różnych metod, które mogą służyć do uruchamiania elementu runbook automatyzacji Azure i zawiera szczegółowe informacje przy użyciu portalu Azure i programu Windows PowerShell.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 45ac19ad5011ae67e95281d1c9928c1db4bc7043
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="starting-a-runbook-in-azure-automation"></a>Uruchamianie elementu runbook automatyzacji Azure
Poniższa tabela ułatwia określenie metody uruchomienia elementu runbook w automatyzacji Azure, która jest najbardziej odpowiednie dla Twojego scenariusza. Ten artykuł zawiera szczegółowe informacje dotyczące uruchamiania elementu runbook z portalu Azure i programu Windows PowerShell. Szczegółowe informacje o innych metodach znajdują się w dokumentacji, która można korzystać z poniższych łączy.

| **— METODA** | **WŁAŚCIWOŚCI** |
| --- | --- |
| [Azure Portal](#starting-a-runbook-with-the-azure-portal) |<li>Najprostsza metoda o interakcyjny interfejs użytkownika.<br> <li>Formularz, aby podać wartości parametrów proste.<br> <li>Łatwo śledzić stan zadania.<br> <li>Dostęp uwierzytelniony dzięki funkcji logowania do platformy Azure. |
| [Środowisko Windows PowerShell](https://msdn.microsoft.com/library/dn690259.aspx) |<li>Wywoływanie z wiersza polecenia za pomocą poleceń cmdlet programu Windows PowerShell.<br> <li>Można dołączyć do automatycznego rozwiązania z wielu kroków.<br> <li>Uwierzytelniania żądań certyfikatów lub użytkownika OAuth główna / service podmiotu zabezpieczeń.<br> <li>Podaj wartości parametrów proste i złożone.<br> <li>Śledź stan zadania.<br> <li>Klient wymagane do obsługi poleceń cmdlet programu PowerShell. |
| [Interfejs API usługi Automatyzacja Azure](https://msdn.microsoft.com/library/azure/mt662285.aspx) |<li>Metoda najbardziej elastycznego, ale również większość złożone.<br> <li>Wywoływanie z kodu niestandardowego, który może zgłaszać żądania HTTP.<br> <li>Żądanie uwierzytelniony przy użyciu certyfikatu lub użytkownika Oauth główna / service podmiotu zabezpieczeń.<br> <li>Podaj wartości parametrów proste i złożone. *Jeśli są wywołanie elementu runbook Python za pomocą interfejsu API, musi być serializowany ładunek JSON.*<br> <li>Śledź stan zadania. |
| [elementów webhook](automation-webhooks.md) |<li>Uruchom element runbook z pojedynczego żądania HTTP.<br> <li>Uwierzytelniani tokenu zabezpieczającego w adresie URL.<br> <li>Klient nie może zastąpić wartości parametrów podczas tworzenia elementu webhook. Element Runbook może definiować jeden parametr, który jest wypełniana szczegółów żądania HTTP.<br> <li>Brak możliwości śledzenia stanu zadania za pomocą adresu URL elementu webhook. |
| [Odpowiadanie na Azure alertu](../log-analytics/log-analytics-alerts.md) |<li>Uruchom element runbook w odpowiedzi na alert platformy Azure.<br> <li>Konfigurowanie elementu webhook dla elementu runbook i łącze do alertów.<br> <li>Uwierzytelniani tokenu zabezpieczającego w adresie URL. |
| [Harmonogram](automation-schedules.md) |<li>Automatyczne uruchamianie elementu runbook z harmonogramem co godzinę, codziennie, co tydzień lub co miesiąc.<br> <li>Manipulowanie harmonogramu za pośrednictwem portalu Azure, poleceń cmdlet programu PowerShell lub interfejsu API Azure.<br> <li>Podaj wartości parametrów do użycia z harmonogramem. |
| [Z innego elementu Runbook](automation-child-runbooks.md) |<li>Użyj elementu runbook jako działanie w innego elementu runbook.<br> <li>Przydatne w przypadku funkcji używany przez wiele elementów runbook.<br> <li>Podaj wartości parametrów podrzędnego elementu runbook i korzystanie z danych wyjściowych w nadrzędny element runbook. |

Na poniższym obrazie przedstawiono krok po kroku proces szczegółowy w cyklu życia elementu runbook. Zawiera różne sposoby, element runbook jest uruchamiany w automatyzacji Azure składniki wymagane dla hybrydowego procesu roboczego elementu Runbook do wykonania elementu runbook usługi Automatyzacja Azure i interakcje między poszczególnymi składnikami. Aby uzyskać informacje dotyczące wykonywania elementu runbook usługi Automatyzacja w centrum danych, należy zapoznać się [hybrydowych procesów roboczych elementu runbook](automation-hybrid-runbook-worker.md)

![Architektura elementu Runbook](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="starting-a-runbook-with-the-azure-portal"></a>Uruchamianie elementu runbook w portalu Azure
1. W portalu Azure wybierz **automatyzacji** , a następnie kliknij nazwę konta automatyzacji.
2. W menu Centrum wybierz **elementów Runbook**.
3. Na **Runbook** wybrać element runbook, a następnie kliknij pozycję **Start**.
4. Jeśli element runbook ma parametry, monit o podanie wartości pola tekstowego do każdego parametru. Zobacz [parametrów elementu Runbook](#Runbook-parameters) poniżej zawiera bardziej szczegółowe informacje o parametrach.
5. Na **zadania** strony, można wyświetlić stan zadania elementu runbook.

## <a name="starting-a-runbook-with-windows-powershell"></a>Uruchamianie elementu runbook przy użyciu programu Windows PowerShell
Można użyć [Start AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) uruchomienia elementu runbook przy użyciu programu Windows PowerShell. Następujący przykładowy kod uruchamia element runbook o nazwie Test-Runbook.

```
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start AzureRmAutomationRunbook zwraca obiekt zadania, które można śledzić jego stan po uruchomieniu elementu runbook. Następnie można użyć z tym obiektem zadania [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) do określenia stanu zadania i [Get AzureRmAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) można pobrać dane wyjściowe. Następujący przykładowy kod uruchamia element runbook o nazwie Test-Runbook, czeka, dopóki nie zostało ukończone, a następnie wyświetla dane wyjściowe.

```
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

Jeśli element runbook wymaga parametrów, a następnie należy podać je jako [hashtable](http://technet.microsoft.com/library/hh847780.aspx) gdzie klucz tablicy skrótów odpowiada nazwie parametru, a wartość jest wartością parametru. Poniższy przykład przedstawia sposób uruchamiania elementu runbook z dwoma parametrami będącymi ciągami o nazwie FirstName i LastName, liczbą całkowitą o nazwie RepeatCount i parametrem logicznym o nazwie Show. Aby uzyskać więcej informacji o parametrach, zobacz [parametrów elementu Runbook](#Runbook-parameters) poniżej.

```
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Parametry elementu Runbook
Po uruchomieniu elementu runbook z portalu Azure lub programu Windows PowerShell instrukcja jest wysyłana za pośrednictwem usługi sieci web automatyzacji Azure. Ta usługa nie obsługuje parametrów o złożonych typach danych. Jeśli musisz podać wartość parametru złożonego, użytkownik musi wywołać go z wnętrza innego elementu runbook zgodnie z opisem w [podrzędnych elementów runbook automatyzacji Azure](automation-child-runbooks.md).

Usługi sieci web Azure automatyzacji zawiera specjalne funkcje dotyczące parametrów używających określonych typów danych, zgodnie z opisem w poniższych sekcjach:

### <a name="named-values"></a>Nazwane wartości
Jeśli parametr ma typ danych [object], wówczas można użyć następującego formatu JSON do wysłania listy nazwanych wartości: *{Nazwa1: 'Wartość1', Nazwa2: 'Wartość2', nazwa3: "Wartość3"}*. Te wartości muszą być typu prostego. Element runbook otrzymuje jako parametr [PSCustomObject](https://msdn.microsoft.com/library/system.management.automation.pscustomobject%28v=vs.85%29.aspx) z właściwościami, które odpowiadają każdej nazwanej wartości.

Należy wziąć pod uwagę następujące runbook testu, który akceptuje parametr o nazwie użytkownika.

```
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

Następujący tekst może zostać użyty dla parametru user.

```
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Powoduje to następujące dane wyjściowe:

```
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Tablice
Jeśli parametr jest tablicą, taką jak [array] lub [string []], wówczas można użyć następującego formatu JSON do wysłania listy wartości: *[Wartość1, wartość2, Wartość3]*. Te wartości muszą być typu prostego.

Należy wziąć pod uwagę następujące runbook testu, który akceptuje parametr o nazwie *użytkownika*.

```
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

Następujący tekst może zostać użyty dla parametru user.

```
["Joe","Smith",2,true]
```

Powoduje to następujące dane wyjściowe:

```
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Poświadczenia
Jeśli parametr ma typ danych **PSCredential**, wówczas można podać nazwę usługi Automatyzacja Azure [zasób poświadczeń](automation-credentials.md). Element runbook pobiera poświadczenie o określonej nazwie.

Należy wziąć pod uwagę następujące runbook testu, który akceptuje parametr o nazwie poświadczeń.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Następujący tekst może zostać użyty dla użytkownika parametr przy założeniu, że wystąpił zasób poświadczeń o nazwie *moich poświadczeń*.

```
My Credential
```

Zakładając, że nazwa użytkownika w poświadczeniu został *jsmith*, powoduje to następujące dane wyjściowe:

```
jsmith
```

## <a name="next-steps"></a>Kolejne kroki
* Architektura elementu runbook w bieżącym artykuł zawiera omówienie elementów runbook Zarządzanie zasobów platformy Azure i lokalnymi z hybrydowy proces roboczy elementu Runbook. Aby uzyskać informacje dotyczące wykonywania elementu runbook usługi Automatyzacja w centrum danych, należy zapoznać się [hybrydowych procesów roboczych Runbook](automation-hybrid-runbook-worker.md).
* Aby dowiedzieć się więcej na temat tworzenia modułowych elementów runbook do użycia przez inne elementy runbook dla określonych lub typowych funkcji, zobacz [podrzędnych elementów Runbook](automation-child-runbooks.md).

