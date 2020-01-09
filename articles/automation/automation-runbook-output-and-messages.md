---
title: Dane wyjściowe i komunikaty elementu Runbook w Azure Automation
description: Opisuje sposób tworzenia i pobierania komunikatów wyjściowych i błędów z elementów Runbook w programie Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 34246d66a48baec160a83411511ed78948c5dd8d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421040"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Dane wyjściowe i komunikaty elementu Runbook w Azure Automation

Większość elementów Runbook Azure Automation ma pewną formę danych wyjściowych. Ten wynik może być komunikatem o błędzie dla użytkownika lub obiektu złożonego, który ma być używany z innym elementem Runbook. Program Windows PowerShell udostępnia [wiele strumieni](/powershell/module/microsoft.powershell.core/about/about_redirection) do wysyłania danych wyjściowych ze skryptu lub przepływu pracy. Azure Automation działa z każdym z tych strumieni inaczej. W przypadku tworzenia elementu Runbook należy stosować najlepsze rozwiązania.

W poniższej tabeli przedstawiono krótki opis każdego ze strumieni i ich zachowanie w Azure Portal dla opublikowanych elementów Runbook i podczas [testowania elementu Runbook](automation-testing-runbook.md). Więcej szczegółowych informacji o każdym strumieniu znajduje się w kolejnych sekcjach.

| Stream | Opis | Opublikowano | Test |
|:--- |:--- |:--- |:--- |
| Dane wyjściowe |Obiekty, które mają być używane przez inne elementy runbook. |Zapisywany w historii zadania. |Wyświetlane w okienku danych wyjściowych testu. |
| Ostrzeżenie |Komunikat ostrzegawczy przeznaczony dla użytkownika. |Zapisywany w historii zadania. |Wyświetlane w okienku danych wyjściowych testu. |
| Błąd |Przeznaczony dla użytkownika komunikat o błędzie. W przeciwieństwie do wyjątku element runbook jest kontynuowane po komunikat o błędzie domyślnie. |Zapisywany w historii zadania. |Wyświetlane w okienku danych wyjściowych testu. |
| Pełny |Komunikaty, które zawierają ogólne lub debugowane informacje. |Zapisywany w historii zadań tylko wtedy, gdy dla elementu runbook jest włączone rejestrowanie pełne. |Wyświetlany w okienku danych wyjściowych testu tylko wtedy, gdy parametr $VerbosePreference ma ustawioną wartość Continue w elemencie Runbook. |
| Postęp |Rekordy generowane automatycznie przed i po każdym działaniu elementu runbook. Element Runbook nie powinien próbować tworzyć własnych rekordów postępu, ponieważ są one przeznaczone dla użytkownika interaktywnego. |Zapisywany w historii zadań tylko wtedy, gdy dla elementu runbook jest włączone rejestrowanie postępu. |Nie są wyświetlane w okienku danych wyjściowych testu. |
| Debugowanie |Komunikaty przeznaczone dla użytkownika interaktywnego. Nie należy używać w elementach Runbook. |Nie jest zapisywany w historii zadań. |Nie jest zapisywany do okienka danych wyjściowych testu. |

## <a name="output-stream"></a>Strumień wyjściowy
Strumień wyjściowy jest przeznaczony do wyprowadzania obiektów, które są tworzone przez skrypt lub przepływ pracy, gdy działa poprawnie. W Azure Automation ten strumień jest używany głównie dla obiektów, które mają być używane przez [nadrzędne elementy Runbook, które wywołują bieżący element Runbook](automation-child-runbooks.md). Gdy użytkownik [element runbook zostanie wywołany](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) nadrzędnego elementu runbook, zwraca dane ze strumienia wyjściowego do nadrzędnego. Strumienia danych wyjściowych należy używać tylko do przekazywania informacji ogólnych z powrotem do użytkownika, jeśli wiadomo, że element Runbook nigdy nie jest wywoływany przez inny element Runbook. Najlepszym rozwiązaniem jest jednak należy zwykle użyć [pełne Stream](#verbose-stream) do przekazywania ogólnych informacji do użytkownika.

Możesz zapisać dane do strumienia wyjściowego przy użyciu [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) lub umieszczając obiekt w osobnym wierszu w elemencie runbook.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Dane wyjściowe funkcji

Gdy zapisujesz do strumienia danych wyjściowych w funkcji, która jest dołączona do elementu Runbook, dane wyjściowe są przekazywane z powrotem do elementu Runbook. Jeśli element runbook przypisuje te dane wyjściowe do zmiennej, następnie go nie jest zapisywany do strumienia wyjściowego. Zapisywanie do innych strumieni z wewnątrz funkcji zapisuje do odpowiedniego strumienia dla elementu Runbook.

Rozważmy następujący przykładowy element Runbook:

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

Po opublikowaniu elementu Runbook i przed jego rozpoczęciem należy również włączyć pełne rejestrowanie w ustawieniach elementu Runbook, aby uzyskać pełne dane wyjściowe strumienia.

### <a name="declaring-output-data-type"></a>Deklarowanie typu danych wyjściowych

Przepływ pracy można określić typu danych jego danych wyjściowych za pomocą [atrybutu OutputType](https://technet.microsoft.com/library/hh847785.aspx). Ten atrybut jest ignorowany podczas wykonywania, ale stanowi wskazówkę twórcy elementu runbook w czasie projektowania oczekiwane dane wyjściowe elementu runbook. Ponieważ zestaw narzędzi dla elementów Runbook jest w dalszym ciągu rozwijany, znaczenie deklarowania typów danych wyjściowych w czasie projektowania zwiększy ważność. W związku z tym najlepszym rozwiązaniem jest dołączenie tej deklaracji do utworzonych elementów Runbook.

Oto lista przykładowych typów danych wyjściowych:

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

Aby zadeklarować typ danych wyjściowych w graficznym lub graficznym elemencie Runbook przepływu pracy programu PowerShell, możesz wybrać opcję menu **dane wejściowe i wyjściowe** i wpisać w polu Nazwa typu danych wyjściowych. Zaleca się używanie pełnej nazwy klasy .NET, aby ułatwić jej identyfikację podczas odwoływania się do niej z nadrzędnego elementu Runbook. Dzięki temu wszystkie właściwości tej klasy zostaną dodane do magistrali danych w elemencie Runbook i zapewniają znacznie elastyczność podczas korzystania z nich do logiki warunkowej, rejestrowania i odwoływania się jako wartości dla innych działań w elemencie Runbook.<br> ![opcji danych wejściowych i wyjściowych elementu Runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

W poniższym przykładzie masz dwa graficzne elementy Runbook, aby przedstawić tę funkcję. Jeśli zastosujesz model projektu modularnego elementu Runbook, masz jeden element Runbook, który służy jako *szablon elementu Runbook uwierzytelniania* Zarządzanie uwierzytelnianiem na platformie Azure przy użyciu konta Uruchom jako. Nasz drugi element Runbook, który zwykle wykonuje podstawową logikę w celu zautomatyzowania danego scenariusza, w tym przypadku będzie wykonywać *szablon elementu Runbook uwierzytelniania* i wyświetlać wyniki w okienku danych wyjściowych **testu** . W normalnych warunkach ten element Runbook będzie miał zawartość dla zasobu wykorzystującego dane wyjściowe z podrzędnego elementu Runbook.

Oto podstawowa logika elementu Runbook **AuthenticateTo-Azure** .<br> ](media/automation-runbook-output-and-messages/runbook-authentication-template.png)przykład ![uwierzytelnić szablonu elementu Runbook.

Zawiera typ danych wyjściowych *Microsoft. Azure. Commands. profile. models. PSAzureContext*, który zwraca właściwości profilu uwierzytelniania.<br> Przykład ![typu wyjściowego elementu Runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Mimo że ten element Runbook jest przesunięty do przodu, istnieje jeden element konfiguracji do wywołania w tym miejscu. Ostatnie działanie wykonuje polecenie cmdlet **Write-Output** i zapisuje dane profilu do zmiennej $ _ przy użyciu wyrażenia programu PowerShell dla parametru **inputobject** , który jest wymagany dla tego polecenia cmdlet.

Dla drugiego elementu Runbook w tym przykładzie o nazwie *test-ChildOutputType*wystarczy dwa działania.<br> ![przykład elementu Runbook podrzędnego typu wyjściowego](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Pierwsze działanie wywołuje element Runbook **AuthenticateTo-Azure** , a drugie działa polecenie cmdlet **Write-verbose** ze **źródłem danych** **wyjścia działania** , a wartością **ścieżki pola** jest **Context. Subscription. subscriptionname**, która określa dane wyjściowe kontekstu z elementu Runbook **AuthenticateTo-Azure** .<br> Źródło danych parametru polecenia cmdlet Write-verbose ![](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Wynikowe dane wyjściowe to nazwa subskrypcji.<br> ![Wyniki elementu Runbook test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

> [!NOTE]
> Po wprowadzeniu wartości w polu **Typ danych wyjściowych** w okienku **Właściwości wejściowe i wyjściowe** należy kliknąć poza formantem, aby umożliwić rozpoznanie wpisu przez formant.

## <a name="message-streams"></a>Strumienie komunikatów

W przeciwieństwie do strumienia wyjściowego strumienie komunikatów są przeznaczone do przekazywania informacji do użytkownika. Istnieje wiele strumieni komunikatów dla różnych rodzajów informacji, a każda z nich jest obsługiwana inaczej przez Azure Automation.

### <a name="warning-and-error-streams"></a>Strumienie ostrzeżeń i błędów

Strumienie ostrzeżeń i błędów są przeznaczone do rejestrowania problemów występujących w elemencie runbook. Są one zapisywane w historii zadań, gdy element Runbook jest wykonywany i znajduje się w okienku danych wyjściowych testu w Azure Portal podczas testowania elementu Runbook. Domyślnie element runbook kontynuuje wykonywanie po ostrzeżeniu lub błędzie. Można określić, że element runbook ma zostać zawieszony w ostrzeżenia lub błędu, ustawiając [zmiennej preferencji](#preference-variables) w elemencie runbook przed utworzeniem komunikatu. Na przykład, aby element Runbook mógł wstrzymać działanie w przypadku błędu, ponieważ spowodowałoby to wyjątek, ustaw **$ErrorActionPreference** na zatrzymany.

Utwórz ostrzeżenia lub błędu komunikat przy użyciu [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) lub [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) polecenia cmdlet. Działania mogą także zapisywać do tych strumieni.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Strumień pełny

Strumień komunikatów pełnych jest ogólne informacje o działaniu elementu runbook. Ponieważ [strumień debugowania](#debug-stream) nie jest dostępny w elemencie Runbook, dla informacji debugowania należy używać pełnych komunikatów. Domyślnie pełne komunikaty z opublikowanych elementów Runbook nie są przechowywane w historii zadań. Aby przechowywać pełne komunikaty, skonfiguruj opublikowane elementy Runbook do rejestrowania pełnych rekordów na karcie Konfiguracja elementu Runbook w Azure Portal. W większości przypadków należy zachować domyślne ustawienie bez rejestrowania rekordów pełnych dla elementu runbook ze względu na wydajność. Włącz tę opcję tylko rozwiązania lub debugowania elementu runbook.

Podczas [testowania elementu Runbook](automation-testing-runbook.md)komunikaty pełne nie są wyświetlane, nawet jeśli element Runbook jest skonfigurowany do rejestrowania pełnych rekordów. Aby wyświetlić pełne komunikaty podczas [testowania elementu Runbook](automation-testing-runbook.md), należy ustawić zmienną $VerbosePreference, aby kontynuować. W przypadku tego zestawu zmiennych w okienku danych wyjściowych testu w Azure Portal są wyświetlane pełne komunikaty.

Tworzenie przy użyciu komunikat trybu informacji pełnej [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) polecenia cmdlet.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Strumień debugowania

Strumień debugowania jest przeznaczony do użytku użytkowników interakcyjnych i nie należy używać w elementach runbook.

## <a name="progress-records"></a>Rekordy postępu

Jeśli element Runbook zostanie skonfigurowany do rejestrowania rekordów postępu (na karcie Konfiguracja elementu Runbook w Azure Portal), rekord zostanie zapisany w historii zadań przed uruchomieniem każdego działania. W większości przypadków należy zachować domyślne ustawienie bez rejestrowania rekordów postępu dla elementu runbook w celu zmaksymalizowania wydajności. Włącz tę opcję tylko rozwiązania lub debugowania elementu runbook. Podczas testowania elementu runbook, komunikaty o postępie nie są wyświetlane, nawet jeśli element runbook jest skonfigurowany do rejestrowania rekordów postępu.

Polecenie cmdlet [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) nie jest prawidłowe w elemencie Runbook, ponieważ to polecenie cmdlet jest przeznaczone do użycia z użytkownikiem interakcyjnym.

## <a name="preference-variables"></a>Zmienne preferencji

Używa programu Windows PowerShell [zmienne preferencji](https://technet.microsoft.com/library/hh847796.aspx) ustalenie, jak reagować na dane wysyłane do różnych strumieni wyjściowych. Można ustawić te zmienne w elemencie Runbook, aby kontrolować, jak reagują na dane wysyłane do różnych strumieni.

Poniższa tabela zawiera listę zmiennych preferencji, które mogą być używane w elementach runbook oraz ich prawidłowe i domyślne wartości. Ta tabela zawiera tylko wartości, które są prawidłowe w elemencie Runbook. Dodatkowe wartości są prawidłowe dla zmiennych preferencji używanych w programie Windows PowerShell poza Azure Automation.

| Zmienna | Default Value | Prawidłowe wartości |
|:--- |:--- |:--- |
| WarningPreference |Kontynuuj |Stop<br>Kontynuuj<br>SilentlyContinue |
| ErrorActionPreference |Kontynuuj |Stop<br>Kontynuuj<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Stop<br>Kontynuuj<br>SilentlyContinue |

W poniższej tabeli wymieniono zachowanie dla wartości zmiennych preferencji, które są prawidłowe w elementach runbook.

| Wartość | Zachowanie |
|:--- |:--- |
| Kontynuuj |Rejestruje komunikat i kontynuuje wykonywanie elementu runbook. |
| SilentlyContinue |Kontynuuje wykonywanie elementu runbook bez rejestrowania komunikatu. Ta wartość ma wpływ na ignorowanie komunikatu. |
| Stop |Rejestruje komunikat i zawiesza element runbook. |

## <a name="runbook-output"></a>Pobieranie danych wyjściowych i komunikatów elementu Runbook

### <a name="azure-portal"></a>Portal Azure

Szczegóły zadania elementu Runbook można wyświetlić w Azure Portal z poziomu karty zadania elementu Runbook. W podsumowaniu zadania są wyświetlane parametry wejściowe i [strumień danych wyjściowych](#output-stream) , a także ogólne informacje o zadaniu i ewentualne wyjątki, jeśli wystąpią. Historia obejmuje komunikaty ze [strumienia wyjściowego](#output-stream) oraz [strumienie ostrzeżeń i błędów](#warning-and-error-streams) , a także pełne rekordy [strumieni](#verbose-stream) i [postępu](#progress-records) , jeśli element Runbook jest skonfigurowany pod kątem rejestrowania rekordów pełnych i informacji o postępie.

### <a name="windows-powershell"></a>Windows PowerShell

W programie Windows PowerShell można pobrać dane wyjściowe i komunikaty z elementu Runbook za pomocą polecenia cmdlet [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) . To polecenie cmdlet wymaga identyfikatora zadania i ma parametr o nazwie Stream, za pomocą którego należy określić strumień do zwrócenia. Można określić **wszelkie** aby spowodować zwrócenie wszystkich strumieni zadania.

Poniższy przykład rozpoczyna się przykładowego elementu runbook, a następnie czeka na jego zakończenie. Po zakończeniu jego strumień wyjściowy jest zbierany z zadania.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="graphical-authoring"></a>Tworzenie graficzne

W przypadku graficznych elementów Runbook dodatkowe rejestrowanie jest dostępne w formie śledzenia na poziomie działania. Istnieją dwa poziomy śledzenia: podstawowe i szczegółowe. W przypadku śledzenia podstawowego można zobaczyć godzinę rozpoczęcia i zakończenia każdego działania w elemencie Runbook oraz informacje związane z ponownymi próbami działania. Oto kilka przykładów, liczba prób i czas rozpoczęcia działania. W ramach szczegółowego śledzenia uzyskasz podstawowe śledzenie oraz dane wejściowe i wyjściowe dla każdego działania. Obecnie rekordy śledzenia są zapisywane przy użyciu pełnego strumienia, więc musisz włączyć pełne rejestrowanie po włączeniu śledzenia. W przypadku graficznych elementów Runbook z włączonym śledzeniem nie ma potrzeby rejestrowania rekordów postępu. Śledzenie podstawowe służy tym samym przeznaczeniu i ma więcej informacji.

![Widok strumieni zadania tworzenia graficznego](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Z poprzedniego zrzutu ekranu można zobaczyć, że po włączeniu pełnego rejestrowania i śledzenia graficznych elementów Runbook w widoku strumienie zadań produkcyjnych jest dostępna znacznie więcej informacji. Te dodatkowe informacje mogą być niezbędne do rozwiązywania problemów produkcyjnych z elementem Runbook, dlatego należy je włączyć tylko do tego celu, a nie jako ogólne rozwiązanie. Rekordy śledzenia mogą być szczególnie liczne. Przy użyciu graficznego śledzenia elementów Runbook można uzyskać dwa do czterech rekordów na działanie w zależności od tego, czy skonfigurowano podstawowe lub szczegółowe śledzenie. Jeśli nie potrzebujesz tych informacji do śledzenia postępu elementu Runbook w celu rozwiązywania problemów, możesz chcieć wyłączyć śledzenie.

**Aby włączyć śledzenie na poziomie działania, wykonaj następujące czynności:**

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. W obszarze **Automatyzacja procesów**wybierz pozycję **elementy Runbook** , aby otworzyć listę elementów Runbook.
3. Na stronie elementy Runbook kliknij, aby wybrać graficzny element Runbook z listy elementów Runbook.
4. W obszarze **Ustawienia**kliknij pozycję **Rejestrowanie i śledzenie**.
5. Na stronie Rejestrowanie i śledzenie w obszarze Rejestruj pełne **Rekordy kliknij pozycję Włącz,** aby włączyć pełne rejestrowanie i w obszarze Śledzenie na poziomie działania, Zmień poziom śledzenia na **podstawowy** lub **szczegółowy** w zależności od wymaganego poziomu śledzenia.<br>

   ![Strona śledzenie i rejestrowanie tworzenia graficznego](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Dzienniki monitora Microsoft Azure

Automatyzacja może wysyłać strumienie zadań elementu Runbook i zadań do obszaru roboczego Log Analytics. Korzystając z dzienników Azure Monitor, możesz

* Uzyskiwanie szczegółowych informacji na temat zadań automatyzacji
* Wyzwalanie wiadomości e-mail lub alertu na podstawie stanu zadania elementu Runbook (na przykład Niepowodzenie lub wstrzymane)
* Zapisuj zaawansowane zapytania w strumieniach zadań
* Skorelowanie zadań na kontach usługi Automation
* Wizualizuj historię zadań w czasie

Aby uzyskać więcej informacji na temat konfigurowania integracji z dziennikami Azure Monitor w celu zbierania, skorelowania i wykonywania zadań związanych z danymi, zobacz [przekazywanie stanu zadań i strumieni zadań z automatyzacji do dzienników Azure monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o wykonywaniu elementów Runbook, sposobie monitorowania zadań elementów Runbook i innych szczegółach technicznych, zobacz [Track a runbook job](automation-runbook-execution.md) (Śledzenie zadania elementu Runbook)
* Aby dowiedzieć się, jak projektować i używać podrzędnych elementów Runbook, zobacz [podrzędne elementy Runbook w Azure Automation](automation-child-runbooks.md)
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów dokumentacji i uczenia dotyczącej języka, zapoznaj się z dokumentacją programu [PowerShell](/powershell/scripting/overview).
