---
title: Runbook Output and Messages w usłudze Azure Automation
description: Opisuje sposób tworzenia i pobrać dane wyjściowe i błędów komunikaty z elementów runbook w usłudze Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cbf91af4e91f41fff30a7edfa869d07a21b881e
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487672"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Dane wyjściowe elementu Runbook i komunikatów w usłudze Azure Automation
Większość elementów runbook automatyzacji Azure obejmuje jakąś formę danych wyjściowych. Te dane wyjściowe mogą być komunikat o błędzie do użytkownika lub obiekt złożony czy zamierzasz korzystać z innego elementu runbook. Program Windows PowerShell udostępnia [wiele strumieni](/powershell/module/microsoft.powershell.core/about/about_redirection) do wysyłania danych wyjściowych w skrypcie lub przepływu pracy. Usługa Azure Automation działa z każdym z tych strumieni inaczej. Należy stosować najlepsze rozwiązania w zakresie używania każdego podczas tworzenia elementu runbook.

Poniższa tabela zawiera krótki opis każdego strumienia i ich zachowania w witrynie Azure portal w przypadku opublikowanych elementów runbook i kiedy [testowania elementu runbook](automation-testing-runbook.md). W kolejnych sekcjach znajdują się dalsze szczegółowe informacje na temat każdego strumienia.

| Strumień | Opis | Opublikowane | Test |
|:--- |:--- |:--- |:--- |
| Dane wyjściowe |Obiekty, które mają być używane przez inne elementy runbook. |Zapisywany w historii zadania. |Wyświetlane w okienku danych wyjściowych testu. |
| Ostrzeżenie |Komunikat ostrzegawczy przeznaczony dla użytkownika. |Zapisywany w historii zadania. |Wyświetlane w okienku danych wyjściowych testu. |
| Błąd |Przeznaczony dla użytkownika komunikat o błędzie. W przeciwieństwie do wyjątku element runbook jest kontynuowane po komunikat o błędzie domyślnie. |Zapisywany w historii zadania. |Wyświetlane w okienku danych wyjściowych testu. |
| Pełne |Komunikaty, które podają informacje ogólne lub debugowania. |Zapisywany w historii zadań tylko wtedy, gdy dla elementu runbook jest włączone rejestrowanie pełne. |Wyświetlane w okienku danych wyjściowych testu tylko wtedy, gdy $VerbosePreference ustawiono Kontynuuj w elemencie runbook. |
| Postęp |Rekordy generowane automatycznie przed i po każdym działaniu elementu runbook. Element runbook nie należy próbować utworzyć swoich własnych rekordów postępu, ponieważ są one przeznaczone dla użytkownika interaktywnego. |Zapisywany w historii zadań tylko wtedy, gdy dla elementu runbook jest włączone rejestrowanie postępu. |Nie są wyświetlane w okienku danych wyjściowych testu. |
| Debugowanie |Komunikaty przeznaczone dla użytkownika interaktywnego. Nie można używać w elementach runbook. |Nie jest zapisywany w historii zadań. |Nie jest zapisywany do okienka danych wyjściowych testu. |

## <a name="output-stream"></a>Strumień wyjściowy
Strumień wyjściowy jest przeznaczony dla danych wyjściowych obiektów, które są tworzone przez skrypt lub przepływu pracy, gdy działa poprawnie. W usłudze Azure Automation ten strumień jest używany głównie dla obiektów, które mają być używane przez [nadrzędne elementy runbook, które wywołań bieżącego elementu runbook](automation-child-runbooks.md). Gdy użytkownik [element runbook zostanie wywołany](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) nadrzędnego elementu runbook, zwraca dane ze strumienia wyjściowego do nadrzędnego. Dane ze strumienia wyjściowego należy używać tylko do przekazywania ogólnych informacji do użytkownika, jeśli wiesz, że element runbook nigdy nie jest wywoływana przez inny element runbook. Najlepszym rozwiązaniem jest jednak należy zwykle użyć [pełne Stream](#verbose-stream) do przekazywania ogólnych informacji do użytkownika.

Możesz zapisać dane do strumienia wyjściowego przy użyciu [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) lub umieszczając obiekt w osobnym wierszu w elemencie runbook.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Dane wyjściowe funkcji
Podczas zapisywania do strumienia wyjściowego w funkcji, która znajduje się w elemencie runbook, dane wyjściowe są przekazywane do elementu runbook. Jeśli element runbook przypisuje te dane wyjściowe do zmiennej, następnie go nie jest zapisywany do strumienia wyjściowego. Zapisywanie do wszelkich innych strumieni w funkcji zapisuje do odpowiedniego strumienia elementu runbook.

Należy wziąć pod uwagę następujący przykładowy element runbook:

```powershell
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

Odpowiedni strumień wyjściowy dla zadania elementu runbook będzie następująca:

```output
Output inside of function
Output outside of function
```

Odpowiedni strumień pełny dla zadania elementu runbook będzie następująca:

```output
Verbose outside of function
Verbose inside of function
```

Po opublikowaniu elementu runbook i przed jej rozpoczęciem, należy również włączyć pełne rejestrowanie, ustawienia elementu runbook, aby uzyskać pełne strumień wyjściowy.

### <a name="declaring-output-data-type"></a>Deklarujący typ danych wyjściowych
Przepływ pracy można określić typu danych jego danych wyjściowych za pomocą [atrybutu OutputType](https://technet.microsoft.com/library/hh847785.aspx). Ten atrybut jest ignorowany podczas wykonywania, ale stanowi wskazówkę twórcy elementu runbook w czasie projektowania oczekiwane dane wyjściowe elementu runbook. Jak zestaw narzędzi dla elementów runbook w dalszym ciągu rozwijany, w ważność wzrasta znaczenie deklarowanie typów danych wyjściowych w czasie projektowania. W rezultacie jest najlepszym rozwiązaniem jest to dołączenie tej deklaracji w wszelkie elementy runbook, którą tworzysz.

Oto lista przykład typów danych wyjściowych:

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

Następujący przykładowy element runbook generuje obiekt ciągu i zawiera deklarację typu jego danych wyjściowych. Jeśli element runbook generuje tablicę pewnego typu, należy nadal określić typ a nie tablicę typu.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Aby zadeklarować typ danych wyjściowych w elementach runbook graficzny lub graficzny przepływ pracy programu PowerShell, można wybrać **danych wejściowych i wyjściowych** opcji menu i wpisz nazwę typu danych wyjściowych. Zalecane jest, że używasz Pełna nazwa klasy .NET, aby ułatwić jego identyfikację podczas odwoływania się do nadrzędnego elementu runbook. Udostępnia właściwości tej klasy do magistrali danych w elemencie runbook i zapewnia dużą elastyczność podczas korzystania z nich logikę warunkową, rejestrowanie i odwoływanie się do wartości innych działań w elemencie runbook.<br> ![Opcji elementu Runbook w danych wejściowych i wyjściowych](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

W poniższym przykładzie masz dwie graficznych elementów runbook, aby zademonstrować tę funkcję. Jeśli zastosujesz model projektowania modułowej elementu runbook, masz jednego elementu runbook, który służy jako *szablonu elementu Runbook uwierzytelniania* zrządzania uwierzytelnianiem za pomocą platformy Azure przy użyciu konta Uruchom jako. Nasze drugiego elementu runbook, który normalnie wykona podstawowe zasady logiczne do zautomatyzowania danego scenariusza, w tym przypadku zamierza wykonać *szablonu elementu Runbook uwierzytelniania* i wyświetlić wyniki, aby Twoje **testu** Okienko danych wyjściowych. W normalnych warunkach trzeba ten element runbook czymś wobec zasobu, wykorzystując dane wyjściowe z podrzędnego elementu runbook.

Poniżej przedstawiono podstawowe logiki **AuthenticateTo Azure** elementu runbook.<br> ![Przykładowy szablon elementu Runbook do uwierzytelniania](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

Zawiera typ danych wyjściowych *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, która zwraca uwierzytelniania właściwości profilu.<br> ![Przykład typu dane wyjściowe elementu Runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Chociaż ten element runbook jest bardzo proste, ma jeden element konfiguracji do wyróżnienia tutaj. Ostatnie działanie wykonuje **Write-Output** polecenia cmdlet i zapisuje dane profilu ze zmienną $_, za pomocą wyrażenia programu PowerShell dla **Inputobject** parametr, który jest wymagany dla tego polecenia cmdlet.  

Dla drugiego elementu runbook w tym przykładzie, o nazwie *ChildOutputType testu*, wystarczy dwóch działań.<br> ![Przykład podrzędnego dane wyjściowe elementu Runbook typu](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

Pierwszego wywołania działania **AuthenticateTo Azure** działa element runbook i drugiego działania **Write-Verbose** polecenia cmdlet z **źródła danych** z  **Wyjście działania** i wartości dla **ścieżka pola** jest **Context.Subscription.SubscriptionName**, który jest określanie kontekstu danych wyjściowych  **AuthenticateTo Azure** elementu runbook.<br> ![Polecenie cmdlet Write-Verbose parametr źródła danych](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

Dane wyjściowe to nazwę subskrypcji.<br> ![Wyniki elementu Runbook ChildOutputType testu](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

Pamiętać o zachowaniu kontrolki typ danych wyjściowych. Podczas wpisywania wartości w polu Typ danych wyjściowych w bloku właściwości danych wejściowych i wyjściowych masz kliknij poza kontrolą po wpisaniu, aby swój wpis zostały rozpoznane przez formant.  

## <a name="message-streams"></a>Strumienie komunikatów
W przeciwieństwie do strumienia wyjściowego strumienie komunikatów są przeznaczone do przekazywania informacji do użytkownika. Istnieje wiele strumieni komunikatów dla różnych rodzajów informacji, a każda odbywa się inaczej przez usługę Azure Automation.

### <a name="warning-and-error-streams"></a>Strumienie ostrzeżeń i błędów
Strumienie ostrzeżeń i błędów są przeznaczone do rejestrowania problemów występujących w elemencie runbook. Są one zapisywane w historii zadań elementu runbook jest wykonywany, gdy znajdują się w okienku danych wyjściowych testu w witrynie Azure portal podczas testowania elementu runbook. Domyślnie element runbook kontynuuje wykonywanie po ostrzeżeniu lub błędzie. Można określić, że element runbook ma zostać zawieszony w ostrzeżenia lub błędu, ustawiając [zmiennej preferencji](#preference-variables) w elemencie runbook przed utworzeniem komunikatu. Na przykład, aby spowodować zawieszenie elementu runbook w błąd jako wyjątek w ten sposób, należy ustawić **$ErrorActionPreference** do zatrzymania.

Utwórz ostrzeżenia lub błędu komunikat przy użyciu [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) lub [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) polecenia cmdlet. Działania mogą także zapisywać do tych strumieni.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Strumień pełny
Strumień komunikatów pełnych jest ogólne informacje o działaniu elementu runbook. Ponieważ [Stream debugowania](#debug-stream) nie jest dostępna w elemencie runbook, pełne komunikaty wyjściowe powinny być używane do informacji o debugowaniu. Domyślnie komunikaty pełne z opublikowanych elementów runbook nie są przechowywane w historii zadań. Aby zapisywać komunikaty pełne, skonfiguruj opublikowane elementy runbook w celu rejestrowania rekordów pełnych na karcie Konfiguracja elementu runbook w witrynie Azure portal. W większości przypadków należy zachować domyślne ustawienie bez rejestrowania rekordów pełnych dla elementu runbook ze względu na wydajność. Włącz tę opcję tylko rozwiązania lub debugowania elementu runbook.

Gdy [testowania elementu runbook](automation-testing-runbook.md), komunikaty pełne nie są wyświetlane nawet wtedy, jeśli element runbook jest skonfigurowany do rejestrowania rekordów pełnych. Aby wyświetlić komunikaty pełne podczas [testowania elementu runbook](automation-testing-runbook.md), należy ustawić dla zmiennej $VerbosePreference Kontynuuj. Za pomocą ta zmienna jest ustawiona komunikaty pełne są wyświetlane w okienku danych wyjściowych testu w witrynie Azure Portal.

Tworzenie przy użyciu komunikat trybu informacji pełnej [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) polecenia cmdlet.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Strumień debugowania
Strumień debugowania jest przeznaczony do użytku użytkowników interakcyjnych i nie należy używać w elementach runbook.

## <a name="progress-records"></a>Rekordy postępu
Jeśli skonfigurujesz elementu runbook do rejestrowania postępu rejestruje (na karcie Konfiguracja elementu runbook w portalu Azure), a następnie rekord zostanie zapisany w historii zadań przed i po każdym uruchomieniem działania. W większości przypadków należy zachować domyślne ustawienie bez rejestrowania rekordów postępu dla elementu runbook w celu zmaksymalizowania wydajności. Włącz tę opcję tylko rozwiązania lub debugowania elementu runbook. Podczas testowania elementu runbook, komunikaty o postępie nie są wyświetlane, nawet jeśli element runbook jest skonfigurowany do rejestrowania rekordów postępu.

[Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) polecenia cmdlet jest nieprawidłowy w elemencie runbook, ponieważ to polecenie cmdlet jest przeznaczone dla użytkownika interaktywnego.

## <a name="preference-variables"></a>Zmienne preferencji
Używa programu Windows PowerShell [zmienne preferencji](https://technet.microsoft.com/library/hh847796.aspx) ustalenie, jak reagować na dane wysyłane do różnych strumieni wyjściowych. Te zmienne możesz ustawić w elemencie runbook do kontrolowania sposobu odpowiedzi na dane przesyłane do różnych strumieni.

Poniższa tabela zawiera listę zmiennych preferencji, które mogą być używane w elementach runbook oraz ich prawidłowe i domyślne wartości. Poniższa tabela zawiera tylko wartości, które są prawidłowe w elemencie runbook. Dodatkowe wartości są prawidłowe dla zmiennych preferencji, gdy jest używana w programie Windows PowerShell poza usługi Azure Automation.

| Zmienna | Wartość domyślna | Prawidłowe wartości |
|:--- |:--- |:--- |
| WarningPreference |Kontynuuj |Stop<br>Kontynuuj<br>SilentlyContinue |
| ErrorActionPreference |Kontynuuj |Stop<br>Kontynuuj<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Stop<br>Kontynuuj<br>SilentlyContinue |

W poniższej tabeli wymieniono zachowanie dla wartości zmiennych preferencji, które są prawidłowe w elementach runbook.

| Wartość | Zachowanie |
|:--- |:--- |
| Kontynuuj |Rejestruje komunikat i kontynuuje wykonywanie elementu runbook. |
| SilentlyContinue |Kontynuuje wykonywanie elementu runbook bez rejestrowania komunikatu. Wartość ta powoduje zignorowanie komunikatu. |
| Stop |Rejestruje komunikat i zawiesza element runbook. |

## <a name="runbook-output"></a>Trwa pobieranie elementu runbook dane wyjściowe i komunikaty
### <a name="azure-portal"></a>Azure Portal
Szczegóły zadania elementu runbook można wyświetlić w witrynie Azure portal, na karcie zadania elementu runbook. Podsumowanie zadania przedstawia parametry wejściowe i [Stream dane wyjściowe](#output-stream) oraz ogólne informacje o zadaniu wraz z wygenerowanymi wyjątkami, jakby one miejsce. Historia zawiera komunikaty z [Stream dane wyjściowe](#output-stream) i [strumienie ostrzeżeń i błędów](#warning-and-error-streams) , jak również [pełne Stream](#verbose-stream) i [rekordów postępu](#progress-records) Jeśli element runbook jest skonfigurowany do dziennika, pełne i rekordy postępu.

### <a name="windows-powershell"></a>Windows PowerShell
W programie Windows PowerShell można pobrać dane wyjściowe i komunikaty elementu runbook, w którym używana jest [Get AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) polecenia cmdlet. To polecenie cmdlet wymaga Identyfikatora zadania i ma parametr o nazwie Stream, w którym należy określić strumień do zwrócenia. Można określić **wszelkie** aby spowodować zwrócenie wszystkich strumieni zadania.

Poniższy przykład rozpoczyna się przykładowego elementu runbook, a następnie czeka na jego zakończenie. Po zakończeniu jego strumień wyjściowy jest zbierany z zadania.

```powershell
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

### <a name="graphical-authoring"></a>Tworzenie graficzne
Graficzne elementy runbook bardzo rejestrowania jest dostępna w formie śledzenie na poziomie działania. Istnieją dwa poziomy śledzenia: Podstawowe i szczegółowe. W śledzenia, można zobaczyć godzina rozpoczęcia i zakończenia każdego działania w elemencie runbook, a także informacji związanych z dowolnego powtórzeń działania. Przykładami są: liczba prób i godzina rozpoczęcia działania. W szczegółowe śledzenie otrzymasz podstawowe śledzenie plus dane wejściowe i dane wyjściowe dla każdego działania. Obecnie rekordów śledzenia są zapisywane z użyciem strumień pełny, więc należy włączyć pełne rejestrowanie, po włączeniu śledzenia. Dla graficznych elementów runbook z włączonym śledzeniem nie ma potrzeby kątem rejestrowania rekordów postępu. Podstawowe śledzenie służy do tego samego celu i jest bardziej szczegółowy.

![Zadanie tworzenia graficznego strumieni widoku](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Z poprzednim zrzucie ekranu widać, gdy włączone jest pełne, rejestrowania i śledzenia dla graficznych elementów runbook, znacznie więcej informacji o dostępnych w widoku strumieni zadań w środowisku produkcyjnym. Te dodatkowe informacje mogą być niezbędne do rozwiązywania problemów produkcyjnych przy użyciu elementu runbook, a w związku z tym należy tylko włączyć je w tym celu, a nie ogólną praktyką. Rekordy śledzenia może być szczególnie wiele. Z włączonym śledzeniem graficzny element runbook, możesz uzyskać dwóch do czterech rekordy na działanie w zależności od tego, czy skonfigurowano śledzenie podstawowy lub szczegółowy. Jeśli nie potrzebujesz tych informacji do śledzenia postępu dla elementów runbook w celu rozwiązywania problemów, możesz chcieć zachować śledzenie wyłączone.

**Aby włączyć śledzenie poziom aktywności, wykonaj następujące czynności:**

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. W obszarze **automatyzacji procesów**, wybierz opcję **elementów Runbook** aby otworzyć listę elementów runbook.
3. Na stronie elementy Runbook kliknij, aby wybrać graficznego elementu runbook z listy elementów runbook.
4. W obszarze **ustawienia**, kliknij przycisk **rejestrowanie i śledzenie**.
5. Rejestrowanie i śledzenie stronie w obszarze rejestrowania rekordów pełnych, wybierz polecenie **na** Włącz pełne rejestrowanie i w obszarze działania na poziomie śledzenia, zmień poziom śledzenia **podstawowe** lub **szczegółowe** zależnie od poziomu śledzenia potrzebujesz.<br>
   
   ![Graficzny tworzenie, rejestrowanie i śledzenie strony](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Dzienniki systemu Microsoft Azure Monitor
Automatyzacja może wysyłać elementu runbook strumieni zadań i stanu zadania do obszaru roboczego usługi Log Analytics. Przy użyciu dzienników usługi Azure Monitor możesz,

* Uzyskaj wgląd w automatyzacji zadań 
* Wyzwalacza poczty e-mail lub alertu opartego na stan zadania elementu runbook (na przykład nie powiodło się lub zawieszone) 
* Pisanie zapytań zaawansowanych między strumienie zadania 
* Korelowanie zadań w ramach kont usługi Automation 
* Wizualizowanie historii zadań wraz z upływem czasu    

Aby uzyskać więcej informacji na temat konfigurowania integracji przy użyciu dzienników usługi Azure Monitor, można gromadzić, korelować i działają na dane zadania, zobacz [przekazywania strumienie zadania i stan zadania z usługi Automation do usługi Azure Monitor dzienników](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej o wykonywaniu elementów Runbook, sposobie monitorowania zadań elementów Runbook i innych szczegółach technicznych, zobacz [Track a runbook job](automation-runbook-execution.md) (Śledzenie zadania elementu Runbook)
* Aby zrozumieć sposób projektowania i używania podrzędnych elementów runbook, zobacz [podrzędnych elementów runbook w usłudze Azure Automation](automation-child-runbooks.md)


