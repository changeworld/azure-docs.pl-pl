---
title: Element Runbook danych wyjściowych i komunikatów w usłudze Automatyzacja Azure
description: Opisuje sposób tworzenia i pobrać dane wyjściowe i błąd komunikaty z elementów runbook automatyzacji Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 04fae653c72c127b22f994e89b050477dac6495d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194254"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Element Runbook danych wyjściowych i komunikatów w usłudze Automatyzacja Azure
Większość elementów runbook automatyzacji Azure ma dane wyjściowe, np. komunikat o błędzie dla użytkownika lub obiekt złożony przeznaczony do użycia przez inny przepływ pracy. Środowisko Windows PowerShell udostępnia [wiele strumieni](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) do wysyłania danych wyjściowych w skrypcie lub przepływ pracy. Automatyzacja Azure działa z każdym z tych strumieni inaczej, a należy stosować najlepsze rozwiązania dotyczące sposobu używania każdej podczas tworzenia elementu runbook.

Poniższa tabela zawiera krótki opis każdego strumienia i jego zachowanie w portalu Azure, zarówno podczas uruchamiania opublikowanego elementu runbook, jak i podczas [testowania elementu runbook](automation-testing-runbook.md). W kolejnych sekcjach znajdują się dodatkowe informacje na temat każdego strumienia.

| Stream | Opis | Opublikowano | Testowanie |
|:--- |:--- |:--- |:--- |
| Dane wyjściowe |Obiekty, które mają być używane przez inne elementy runbook. |Zapisywany w historii zadania. |Wyświetlane w okienku danych wyjściowych testu. |
| Ostrzeżenie |Komunikat ostrzegawczy przeznaczony dla użytkownika. |Zapisywany w historii zadania. |Wyświetlane w okienku danych wyjściowych testu. |
| Błąd |Komunikat o błędzie przeznaczony dla użytkownika. W odróżnieniu od wyjątek element runbook kontynuuje po komunikat o błędzie domyślnie. |Zapisywany w historii zadania. |Wyświetlane w okienku danych wyjściowych testu. |
| Pełne |Komunikaty z informacjami ogólne lub debugowania. |Zapisywany w historii zadań tylko wtedy, gdy jest włączone pełne rejestrowanie dla elementu runbook. |Wyświetlane w okienku danych wyjściowych testu tylko wtedy, gdy $VerbosePreference ma ustawioną wartość Kontynuuj w elemencie runbook. |
| Postęp |Rekordy są generowane automatycznie przed i po każdym działaniu elementu runbook. Element runbook nie powinny podejmować próby tworzenia swoich własnych rekordów postępu, ponieważ są one przeznaczone dla użytkownika interaktywnego. |Zapisywany w historii zadań tylko wtedy, gdy dla elementu runbook jest włączone rejestrowanie postępu. |Nie są wyświetlane w okienku danych wyjściowych testu. |
| Debugowanie |Komunikaty przeznaczone dla użytkownika interaktywnego. Nie można używać w elementach runbook. |Nie jest zapisywany w historii zadań. |Nie zapisać w okienku danych wyjściowych testu. |

## <a name="output-stream"></a>Strumień wyjściowy
Strumień wyjściowy jest przeznaczony dla danych wyjściowych obiektów utworzonych przy użyciu skryptu lub przepływu pracy po uruchomieniu poprawnie. W automatyzacji Azure ten strumień jest używany głównie dla obiektów, które mają być używane przez [nadrzędne elementy runbook, które wywołań bieżącego elementu runbook](automation-child-runbooks.md). Gdy zostanie [wywołać wbudowanego elementu runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) z nadrzędnego elementu runbook, to zwróci dane strumienia wyjściowego do nadrzędnego. Strumienia wyjściowego należy używać tylko do przekazywania ogólnych informacji do użytkownika, jeśli wiadomo, że element runbook nigdy nie jest wywoływany przez inny element runbook. Najlepszym rozwiązaniem, jednak należy zwykle użyć [strumień pełny](#Verbose) do przekazywania ogólnych informacji do użytkownika.

Można zapisać danych do strumienia wyjściowego przy użyciu [Write-Output](http://technet.microsoft.com/library/hh849921.aspx) lub umieszczając obiekt w osobnym wierszu w elemencie runbook.

```PowerShell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Dane wyjściowe funkcji
Podczas zapisywania do strumienia wyjściowego w funkcji, który znajduje się w elemencie runbook, dane wyjściowe są przekazywane z powrotem do elementu runbook. Jeśli element runbook przypisuje te dane wyjściowe do zmiennej, następnie go nie jest zapisywany do strumienia wyjściowego. Zapisywanie do wszelkich innych strumieni w funkcji zapisuje do odpowiedniego strumienia elementu runbook.

Rozważmy następujący przykładowy element runbook:

```PowerShell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Strumień wyjściowy dla zadania elementu runbook należy:

    Output inside of function
    Output outside of function

Strumień pełny dla zadania elementu runbook należy:

    Verbose outside of function
    Verbose inside of function

Po opublikowaniu elementu runbook i przed rozpoczęciem pracy, należy również włączyć pełne rejestrowanie w ustawienia elementu runbook, aby uzyskać pełne strumieni wyjściowych.

### <a name="declaring-output-data-type"></a>Deklaracyjny typ danych wyjściowych
Przepływ pracy można określić typu danych przy użyciu jego dane wyjściowe [atrybutu OutputType](http://technet.microsoft.com/library/hh847785.aspx). Ten atrybut nie ma znaczenia w czasie wykonywania, ale stanowi wskazówkę dla twórcy elementu runbook w czasie projektowania oczekiwanych danych wyjściowych elementu runbook. Jak zestaw narzędzi dla elementów runbook w dalszym ciągu rozwijany, deklarowanie typów danych wyjściowych w czasie projektowania zwiększa się znaczenie. W związku z tym jest najlepsze rozwiązanie to dołączenie tej deklaracji w żadnych tworzonym elemencie runbook.

W tym miejscu znajduje się lista przykładowe typy danych wyjściowych:

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

Następujący przykładowy element runbook generuje obiekt ciągu i zawiera deklarację typu jego danych wyjściowych. Jeśli element runbook generuje tablicę pewnego typu, należy także określić typu, a nie tablicę typu.

```PowerShell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Aby zadeklarować typ danych wyjściowych w elementach runbook graficzny lub graficzny przepływ pracy programu PowerShell, można wybrać **wejściowa i wyjściowa** opcji menu i wpisz nazwę typu danych wyjściowych. Zaleca się, że używasz Pełna nazwa klasy .NET, aby ułatwić jego identyfikację podczas odwoływania się do nadrzędnego elementu runbook. Opisuje właściwości tej klasy w magistrali danych w elemencie runbook i zapewnia dużą elastyczność w przypadku, gdy ich użycie jako logikę warunkową, rejestrowania i odwołuje się do wartości dla innych działań w elemencie runbook.<br> ![Opcja Runbook dane wejściowe i wyjściowe](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

W poniższym przykładzie masz dwie graficznych elementów runbook, aby pokazać tę funkcję. Model projektowania moduły elementu runbook w przypadku zastosowania, ma jeden element runbook, który służy jako *uwierzytelniania szablonem* Zarządzanie uwierzytelniania przy użyciu platformy Azure przy użyciu konta Uruchom jako. Nasze drugiego elementu runbook, które zwykle przeprowadziłby logiki core można zautomatyzować w danej sytuacji, w tym przypadku ma wykonać *uwierzytelniania szablonem* i wyświetlić wyniki w Twojej **testu** okienku danych wyjściowych. W normalnych okolicznościach trzeba ten element runbook czymś względem zasobów, wykorzystując dane wyjściowe z podrzędnego elementu runbook.    

Poniżej przedstawiono podstawowe logiki **AuthenticateTo Azure** elementu runbook.<br> ![Przykład szablonem uwierzytelniania](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

Zawiera typ danych wyjściowych *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, która zwraca uwierzytelniania właściwości profilu.<br> ![Przykład typ danych wyjściowych elementu Runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Gdy ten element runbook jest wprost do przodu, istnieje jeden element konfiguracji w celu wyróżnienia w tym miejscu. Ostatnie działanie jest wykonywany **Write-Output** polecenia cmdlet i zapisuje dane profilu przy użyciu wyrażenia środowiska PowerShell dla zmiennej _ $ **Inputobject** parametr, który jest wymagany dla tego polecenia cmdlet.  

Dla drugiego elementu runbook w tym przykładzie, o nazwie *ChildOutputType testu*, wystarczy mieć dwóch działań.<br> ![Przykład podrzędnych Output typ elementu Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

Pierwsze wywołania działania **AuthenticateTo Azure** elementu runbook, a drugi działania działa **Write-Verbose** polecenia cmdlet z **źródła danych** z **dane wyjściowe działania** i wartości dla **ścieżkę pola** jest **Context.Subscription.SubscriptionName**, który jest określenie kontekstu dane wyjściowe z **AuthenticateTo Azure** elementu runbook.<br> ![Write-Verbose polecenia cmdlet parametr źródła danych](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

Dane wyjściowe to nazwa subskrypcji.<br> ![Wyniki Runbook ChildOutputType testu](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

Co należy pamiętać o zachowaniu kontroli typ danych wyjściowych. Gdy wpisz wartość w polu Typ danych wyjściowych w danych wejściowych i wyjściowych bloku właściwości, trzeba kliknąć poza kontrolą po wpisaniu, aby wpis zostały rozpoznane przez formant.  

## <a name="message-streams"></a>Strumienie komunikatów
W przeciwieństwie do strumienia wyjściowego strumieni komunikatów są przeznaczone do przekazywania informacji do użytkownika. Istnieje wiele strumieni komunikatów dla różnych typów informacji, a każdy różni się w automatyzacji Azure.

### <a name="warning-and-error-streams"></a>Strumienie ostrzeżeń i błędów
Strumienie ostrzeżeń i błędów są przeznaczone do rejestrowania problemów występujących w elemencie runbook. Są one zapisywane w historii zadań, gdy element runbook jest wykonywane i znajdują się w okienku danych wyjściowych testu w portalu Azure podczas testowania elementu runbook. Domyślnie element runbook kontynuuje wykonywanie po ostrzeżeniu lub błędzie. Można określić, że element runbook ma zostać zawieszony w ostrzeżenia lub błędu, ustawiając [zmiennej preferencji](#PreferenceVariables) w elemencie runbook przed utworzeniem komunikatu. Na przykład, aby spowodować zawieszenie w przypadku wystąpienia błędu, jak wyjątek elementu runbook, należy ustawić **$ErrorActionPreference** do zatrzymania.

Utwórz ostrzeżenia lub błędu komunikat przy użyciu [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) lub [Write-Error](http://technet.microsoft.com/library/hh849962.aspx) polecenia cmdlet. Działania mogą także zapisywać do tych strumieni.

```PowerShell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Strumień pełny
Strumień komunikatów pełnych jest ogólne informacje o działaniu elementu runbook. Ponieważ [strumienia debugowania](#Debug) jest niedostępny w elemencie runbook, komunikaty pełne należy używać dla informacji debugowania. Domyślnie komunikaty pełne z opublikowanych elementów runbook nie są przechowywane w historii zadań. Aby zapisywać komunikaty pełne, skonfiguruj opublikowane elementy runbook do rejestrowania rekordów pełnych na karcie Konfigurowanie elementu runbook w portalu Azure. W większości przypadków należy pozostawić domyślne ustawienie nie rejestrowania rekordów pełnych dla elementu runbook ze względu na wydajność. Włącz tę opcję tylko do rozwiązania lub debugowania elementu runbook.

Gdy [testowania elementu runbook](automation-testing-runbook.md), komunikaty pełne nie są wyświetlane, nawet jeśli element runbook jest skonfigurowany pod kątem rejestrowania rekordów pełnych. Aby wyświetlić komunikaty pełne podczas [testowania elementu runbook](automation-testing-runbook.md), musisz ustawić dla zmiennej $VerbosePreference Kontynuuj. Z tego zestawu zmiennej komunikaty pełne są wyświetlane w okienku danych wyjściowych testu portalu Azure.

Tworzenie przy użyciu komunikat trybu informacji pełnej [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx) polecenia cmdlet.

```PowerShell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Strumień debugowania
Strumień debugowania jest przeznaczony dla użytkownika interaktywnego i nie należy używać w elementach runbook.

## <a name="progress-records"></a>Rekordy postępu
Jeśli skonfigurujesz rejestruje elementu runbook do rejestrowania postępu (na karcie Konfigurowanie elementu runbook w portalu Azure), a następnie rekord zostanie zapisany w historii zadań przed i po każdym uruchomieniem działania. W większości przypadków należy pozostawić domyślne ustawienie nie rejestrowania rekordów postępu dla elementu runbook, aby zmaksymalizować wydajność. Włącz tę opcję tylko do rozwiązania lub debugowania elementu runbook. Podczas testowania elementu runbook, komunikaty o postępie nie są wyświetlane, nawet jeśli element runbook jest skonfigurowany pod kątem rejestrowania rekordów postępu.

[Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) polecenia cmdlet jest nieprawidłowy w elemencie runbook, ponieważ ta wartość jest przeznaczona do użycia z użytkownika interaktywnego.

## <a name="preference-variables"></a>Zmienne preferencji
Program Windows PowerShell korzysta [zmienne preferencji](http://technet.microsoft.com/library/hh847796.aspx) ustalenie, jak odpowiedzieć na dane przesyłane do różnych strumieni wyjściowych. Te zmienne możesz ustawić w elemencie runbook, aby kontrolować sposób odpowiadania na dane przesyłane do różnych strumieni.

W poniższej tabeli przedstawiono zmienne preferencji, których można użyć w elementach runbook oraz ich prawidłowe i domyślne wartości. Ta tabela zawiera tylko wartości, które są nieprawidłowe w elemencie runbook. Dodatkowe wartości są prawidłowe dla zmiennych preferencji, gdy jest używany w programie Windows PowerShell poza automatyzacji Azure.

| Zmienna | Wartość domyślna | Prawidłowe wartości |
|:--- |:--- |:--- |
| WarningPreference |Kontynuuj |Stop<br>Kontynuuj<br>SilentlyContinue |
| ErrorActionPreference |Kontynuuj |Stop<br>Kontynuuj<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Stop<br>Kontynuuj<br>SilentlyContinue |

W poniższej tabeli wymieniono zachowania dla wartości zmiennych preferencji, które są prawidłowe w elementach runbook.

| Wartość | Zachowanie |
|:--- |:--- |
| Kontynuuj |Rejestruje komunikat i kontynuuje wykonywanie elementu runbook. |
| SilentlyContinue |Kontynuuje wykonywanie elementu runbook bez rejestrowania komunikatu. To powoduje zignorowanie komunikatu. |
| Stop |Rejestruje komunikat i zawiesza element runbook. |

## <a name="retrieving-runbook-output-and-messages"></a>Pobieranie elementu runbook dane wyjściowe i komunikaty
### <a name="azure-portal"></a>Azure Portal
Szczegóły zadania elementu runbook można wyświetlić w portalu Azure na karcie zadania, elementu runbook. Parametry wejściowe Wyświetla podsumowanie zadania a [strumienia wyjściowego](#Output) także ogólne informacje o zadaniu i wszystkie wyjątki, jeśli takie były. Historia zawiera komunikaty z [strumienia wyjściowego](#Output) i [strumienie ostrzeżeń i błędów](#WarningError) oprócz [strumień pełny](#Verbose) i [rekordów postępu](#Progress) Jeśli element runbook jest skonfigurowany do dziennika pełne i rekordy postępu.

### <a name="windows-powershell"></a>Windows PowerShell
W programie Windows PowerShell można pobrać dane wyjściowe i komunikaty z elementu runbook za pomocą [Get-AzureAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) polecenia cmdlet. To polecenie cmdlet wymaga Identyfikatora zadania i ma parametr o nazwie strumienia, w którym można określić strumień do zwrócenia. Można określić **żadnych** aby spowodować zwrócenie wszystkich strumieni zadania.

Poniższy przykład uruchamiany Przykładowy element runbook, a następnie oczekiwanie na jej zakończenie. Po zakończeniu jego strumień wyjściowy jest zbierany z zadania.

```PowerShell
$job = Start-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzureRmAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzureRmAutomationJobOutput to Get-AzureRmAutomationJobOutputRecord
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzureRmAutomationJobOutputRecord
``` 

### <a name="graphical-authoring"></a>Tworzenie graficznych
Graficznych elementów runbook bardzo rejestrowania jest dostępna w formie śledzenie poziom aktywności. Istnieją dwa poziomy śledzenia: Basic i szczegółowe. W śledzenia, można sprawdzić rozpoczęcia i godzina zakończenia działania każdego elementu runbook oraz informacje dotyczące ponownych prób wszystkie działania, takie jak liczba prób i godzina rozpoczęcia działania. W szczegółowego śledzenia, należy uzyskać śledzenia plus danych wejściowych i danych wyjściowych dla każdego działania. Obecnie rekordów śledzenia są zapisywane przy użyciu strumień pełny, więc musisz włączyć pełne rejestrowanie, podczas włączania śledzenia. Dla graficznych elementów runbook z włączonym śledzeniem jest niepotrzebna pod kątem rejestrowania rekordów postępu, ponieważ śledzenie podstawowe pełni tę samą funkcję i więcej informacji.

![Zadanie tworzenia graficznego strumieni widoku](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Z poprzednim zrzucie ekranu widać, gdy jest włączone pełne rejestrowanie i śledzenia dla graficznych elementów runbook, znacznie więcej informacji o dostępnych w środowisku produkcyjnym widoku strumieni zadania. Te dodatkowe informacje mogą być niezbędne podczas rozwiązywania problemów w środowisku produkcyjnym z elementu runbook, a w związku z tym należy włączać jedynie go do tego celu, a nie jako rozwiązaniem ogólne. Może być szczególnie wiele rekordów śledzenia. Z włączonym śledzeniem graficzny element runbook, możesz uzyskać dwóch do czterech rekordów na działania w zależności od tego, czy skonfigurowano śledzenie podstawowy lub szczegółowy. Jeśli nie potrzebujesz tych informacji, aby śledzić postęp działania elementu runbook w celu rozwiązania problemu, możesz chcieć zachować śledzenia wyłączone.

**Aby włączyć śledzenie poziom aktywności, wykonaj następujące czynności:**

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. W obszarze **automatyzacji procesu**, wybierz pozycję **elementów Runbook** aby otworzyć listę elementów runbook.
3. Na stronie elementy Runbook kliknij, aby wybrać graficznego elementu runbook z listy elementów runbook.
4. W obszarze **ustawienia**, kliknij przycisk **rejestrowania i śledzenia**.
5. Rejestrowanie i śledzenie strony w obszarze rejestrowania rekordów pełnych, kliknij przycisk **na** Aby włączyć pełne rejestrowanie i w obszarze Śledzenie poziom aktywności, zmień poziom śledzenia do **podstawowe** lub **szczegółowy** na podstawie poziomu śledzenia wymagane.<br>
   
   ![Graficzny tworzenia rejestrowania i śledzenia bloku](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-log-analytics"></a>Analiza dzienników Microsoft Azure
Automatyzacja może wysyłać runbook strumieni zadania stanu i zadania do swojego obszaru roboczego analizy dzienników. Za pomocą analizy dzienników można,

* Uzyskiwanie wglądu w zadaniach automatyzacji 
* Wyzwalacz poczty e-mail lub alertu oparte na stan zadania elementu runbook (na przykład nie powiodło się lub wstrzymane) 
* Zapis zaawansowanych zapytań na strumienie zadania 
* Korelowanie zadań na konta automatyzacji 
* Wizualizuj historię zadania wraz z upływem czasu    

Aby uzyskać więcej informacji na temat konfigurowania integracji z analizy dzienników do zbierania, korelowania i działają na dane zadania, zobacz [przekazywania strumienie zadania i stan zadania z automatyzacji do analizy dzienników](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej o wykonywaniu elementów Runbook, sposobie monitorowania zadań elementów Runbook i innych szczegółach technicznych, zobacz [Track a runbook job](automation-runbook-execution.md) (Śledzenie zadania elementu Runbook)
* Aby zrozumieć sposób projektowania i używania podrzędnych elementów runbook, zobacz [podrzędnych elementów runbook automatyzacji Azure](automation-child-runbooks.md)

