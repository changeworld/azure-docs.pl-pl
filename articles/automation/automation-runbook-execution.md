---
title: Wykonanie elementu Runbook w Azure Automation
description: Opisuje szczegóły dotyczące sposobu przetwarzania elementu Runbook w Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: c97e10d2785b7dc1a438c95dca9be94fcef82f94
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714845"
---
# <a name="runbook-execution-in-azure-automation"></a>Wykonanie elementu Runbook w Azure Automation

Po uruchomieniu elementu Runbook w Azure Automation tworzone jest zadanie. Zadanie jest pojedynczym wystąpieniem wykonania elementu Runbook. Azure Automation proces roboczy jest przypisywany do uruchamiania każdego zadania. Chociaż procesy robocze są współużytkowane przez wiele kont platformy Azure, zadania z różnych kont usługi Automation są od siebie odizolowane. Nie masz kontroli nad tym, które usługi procesu roboczego żądają danego zadania. Pojedynczy element Runbook może mieć wiele zadań uruchomionych jednocześnie. Środowisko wykonawcze dla zadań z tego samego konta usługi Automation może być ponownie używane. Im więcej zadań jest wykonywanych w tym samym czasie, tym częściej można je wysłać do tej samej piaskownicy. Zadania uruchomione w tym samym procesie piaskownicy mogą mieć wpływ na siebie nawzajem. na jednym przykładzie działa polecenie cmdlet `Disconnect-AzureRMAccount`. Uruchomienie tego polecenia cmdlet spowoduje rozłączenie każdego zadania elementu Runbook w udostępnionym procesie piaskownicy. Po wyświetleniu listy elementów Runbook w Azure Portal zostanie wyświetlona lista stanu wszystkich zadań uruchomionych dla każdego elementu Runbook. Możesz wyświetlić listę zadań dla każdego elementu Runbook, aby śledzić stan każdego z nich. Dzienniki zadań są przechowywane przez maksymalnie 30 dni. Opis różnych stanów [zadań zadań.](#job-statuses)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Na poniższym diagramie przedstawiono cykl życia zadania elementu Runbook dla [elementów Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks), [graficznych elementów Runbook](automation-runbook-types.md#graphical-runbooks) i [elementów Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Stany zadań — przepływ pracy programu PowerShell](./media/automation-runbook-execution/job-statuses.png)

Twoje zadania mają dostęp do zasobów platformy Azure przez nawiązanie połączenia z subskrypcją platformy Azure. Mają dostęp do zasobów w centrum danych tylko wtedy, gdy te zasoby są dostępne z chmury publicznej.

## <a name="where-to-run-your-runbooks"></a>Gdzie można uruchamiać elementy Runbook

Elementy Runbook w Azure Automation mogą być uruchamiane w piaskownicy na platformie Azure lub [hybrydowym procesie roboczym elementu Runbook](automation-hybrid-runbook-worker.md). Piaskownica to środowisko udostępnione na platformie Azure, które może być używane przez wiele zadań. Zadania korzystające z tej samej piaskownicy są powiązane z ograniczeniami zasobów piaskownicy. Hybrydowe procesy robocze elementów Runbook umożliwiają uruchamianie elementów Runbook bezpośrednio na komputerze hostującym rolę i w odniesieniu do zasobów w środowisku, aby zarządzać tymi zasobami lokalnymi. Elementy Runbook są przechowywane i zarządzane w Azure Automation a następnie dostarczane do co najmniej jednego przypisanego komputera. Większość elementów Runbook można łatwo uruchomić w piaskownicach platformy Azure. Istnieją konkretne scenariusze, w których wybór hybrydowego elementu Runbook za pośrednictwem piaskownicy platformy Azure do wykonania elementu Runbook może być zalecany. Listę niektórych przykładowych scenariuszy można znaleźć w poniższej tabeli:

|Zadanie|Najlepszy wybór|Uwagi|
|---|---|---|
|Integracja z zasobami platformy Azure|Azure Sandbox|Uwierzytelnianie jest obsługiwane na platformie Azure. Jeśli używasz hybrydowego procesu roboczego elementu Runbook na maszynie wirtualnej platformy Azure, możesz używać [tożsamości zarządzanych dla zasobów platformy Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Optymalna wydajność zarządzania zasobami platformy Azure|Azure Sandbox|Skrypt jest uruchamiany w tym samym środowisku, co z kolei ma mniejsze opóźnienia|
|Minimalizuj koszty operacyjne|Azure Sandbox|Nie ma żadnych nakładów obliczeniowych, brak potrzeby dla maszyny wirtualnej|
|Długi uruchomiony skrypt|Hybrydowy proces roboczy elementu Runbook|Piaskownice platformy Azure mają [ograniczenia dotyczące zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Korzystanie z usług lokalnych|Hybrydowy proces roboczy elementu Runbook|Może mieć dostęp bezpośrednio do komputera hosta|
|Wymagaj oprogramowania i plików wykonywalnych innej firmy|Hybrydowy proces roboczy elementu Runbook|Zarządzasz systemem operacyjnym i można instalować oprogramowanie|
|Monitorowanie pliku lub folderu za pomocą elementu Runbook|Hybrydowy proces roboczy elementu Runbook|Korzystanie z [zadania obserwatora](automation-watchers-tutorial.md) w hybrydowym procesie roboczym elementu Runbook|
|Skrypt intensywnie korzystający z zasobów|Hybrydowy proces roboczy elementu Runbook| Piaskownice platformy Azure mają [ograniczenia dotyczące zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Korzystanie z modułów z określonymi wymaganiami| Hybrydowy proces roboczy elementu Runbook|Przykłady to:</br> **WinSCP** — zależność w WinSCP. exe </br> **IISAdministration** — wymaga włączenia usług IIS|
|Zainstaluj moduł wymagający Instalatora|Hybrydowy proces roboczy elementu Runbook|Moduły dla piaskownicy muszą być możliwe do przeniesień|
|Korzystanie z elementów Runbook lub modułów, które wymagają .NET Framework różnych od 4.7.2|Hybrydowy proces roboczy elementu Runbook|Piaskownice automatyzacji mają .NET Framework 4.7.2 i nie ma możliwości jego uaktualnienia|
|Skrypty wymagające podniesienia uprawnień|Hybrydowy proces roboczy elementu Runbook|Piaskownice nie umożliwiają podniesienia uprawnień. Aby rozwiązać ten problem, użyj hybrydowego procesu roboczego elementu Runbook i możesz wyłączyć funkcję Kontrola konta użytkownika i użyć `Invoke-Command` podczas uruchamiania polecenia, które wymaga podniesienia uprawnień|
|Skrypty wymagające dostępu do usługi WMI|Hybrydowy proces roboczy elementu Runbook|Zadania uruchomione w piaskownicach w chmurze nie [mają dostępu do usługi WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Zachowanie elementu Runbook

Elementy Runbook są wykonywane na podstawie logiki, która jest w nich zdefiniowana. Jeśli element Runbook zostanie przerwany, element Runbook zostanie ponownie uruchomiony na początku. To zachowanie wymaga zapisywania elementów Runbook w sposób, w którym obsługują one ponowne uruchomienie w przypadku problemów przejściowych.

Zadania programu PowerShell uruchomione z elementu Runbook uruchomionego w piaskownicy platformy Azure mogą nie działać w trybie pełnego języka. Aby dowiedzieć się więcej na temat trybów języka programu PowerShell, zobacz [tryby języka programu PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Aby uzyskać dodatkowe informacje na temat korzystania z zadań w Azure Automation, zobacz [pobieranie stanu zadania przy użyciu programu PowerShell](#retrieving-job-status-using-powershell) .

### <a name="creating-resources"></a>Tworzenie zasobów

Jeśli skrypt tworzy zasoby, należy sprawdzić, czy zasób już istnieje, przed podjęciem próby jego ponownego utworzenia. W poniższym przykładzie pokazano podstawowy przykład:

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependent-scripts"></a>Skrypty zależne od czasu

Należy uważnie rozważyć podczas tworzenia elementów Runbook. Jak wspomniano wcześniej, elementy Runbook muszą zostać utworzone w taki sposób, że są niezawodne i mogą obsługiwać błędy przejściowe, które mogą spowodować ponowne uruchomienie lub niepowodzenie elementu Runbook. Jeśli element Runbook ulegnie awarii, zostanie ponowiona próba. Jeśli element Runbook jest zwykle uruchamiany w ramach ograniczenia czasu, logika do sprawdzenia czasu wykonania powinna zostać zaimplementowana w elemencie Runbook, aby zapewnić, że operacje takie jak uruchamianie, zamykanie lub skalowanie w poziomie są uruchamiane tylko w określonych godzinach.

> [!NOTE]
> Czas lokalny w procesie piaskownicy platformy Azure jest ustawiany na czas UTC. Należy wziąć pod uwagę obliczenia dla daty i godziny w elementach Runbook.

### <a name="tracking-progress"></a>Śledzenie postępu

Dobrym sposobem jest tworzenie elementów Runbook w charakterze modularnym. Oznacza to tworzenie struktury logiki w elemencie Runbook w taki sposób, że może być ponownie używana i uruchomiona ponownie. Śledzenie postępów w elemencie Runbook to dobry sposób, aby zapewnić prawidłowe działanie logiki w elemencie Runbook w przypadku wystąpienia problemów. Niektóre możliwe sposoby śledzenia postępu elementu Runbook to użycie zewnętrznego źródła, takiego jak konta magazynu, baza danych lub udostępnione pliki. Śledząc stan na zewnątrz, można utworzyć logikę w elemencie Runbook, aby najpierw sprawdzić stan ostatniej akcji wykonanej przez element Runbook. Następnie na podstawie wyników możesz pominąć lub kontynuować określone zadania w elemencie Runbook.

### <a name="prevent-concurrent-jobs"></a>Uniemożliwiaj współbieżne zadania

Niektóre elementy Runbook mogą działać dziwnie, jeśli działają w wielu zadaniach w tym samym czasie. W tym przypadku ważne jest zaimplementowanie logiki w celu sprawdzenia, czy element Runbook ma już uruchomione zadanie. W poniższym przykładzie przedstawiono podstawowy przykład sposobu działania tego zachowania:

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Praca z wieloma subskrypcjami

Podczas tworzenia elementów Runbook, które zajmują się wieloma subskrypcjami, element Runbook musi użyć polecenia cmdlet [disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) , aby upewnić się, że kontekst uwierzytelniania nie zostanie pobrany z innego elementu Runbook, który może być uruchomiony w tej samej piaskownicy. Następnie należy użyć parametru `-AzureRmContext` w poleceniach cmdlet `AzureRM` i przekazać go do odpowiedniego kontekstu.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Obsługa wyjątków

Podczas tworzenia skryptów ważne jest, aby obsługiwać wyjątki i potencjalne Sporadyczne awarie. Poniżej przedstawiono różne sposoby obsługi wyjątków lub sporadycznych problemów z elementami Runbook:

#### <a name="erroractionpreference"></a>$ErrorActionPreference

Zmienna preferencji [$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) określa, w jaki sposób program PowerShell reaguje na błąd niepowodujący zakończenia. Nie ma to wpływ na błędy powodujące przerwanie działania `$ErrorActionPreference`. Przy użyciu `$ErrorActionPreference`, zazwyczaj błąd niepowodujący zakończenia, taki jak `PathNotFound` z polecenia cmdlet `Get-ChildItem`, spowoduje zatrzymanie elementu Runbook. W poniższym przykładzie pokazano przy użyciu `$ErrorActionPreference`. Ostatni wiersz `Write-Output` nie zostanie wykonany, ponieważ skrypt zostanie zatrzymany.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Wypróbuj teraz

W skryptach programu PowerShell jest używana usługa [catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) , która pomaga obsłużyć błędy powodujące przerwanie. Za pomocą try catch można przechwytywać określone wyjątki lub wyjątki ogólne. Instrukcja catch powinna służyć do śledzenia błędów lub użycia w celu obsługi błędu. Poniższy przykład próbuje pobrać plik, który nie istnieje. Przechwytuje wyjątek `System.Net.WebException`, jeśli wystąpił inny wyjątek, Ostatnia wartość jest zwracana.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Generować

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) można użyć do wygenerowania błędu kończącego. Może to być przydatne podczas definiowania własnej logiki w elemencie Runbook. Jeśli spełnione są pewne kryteria, które powinny zatrzymać skrypt, można użyć `throw`, aby zatrzymać skrypt. Poniższy przykład pokazuje, że komputer jest parametrem funkcji wymaganym przy użyciu `throw`.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Używanie plików wykonywalnych lub procesów wywołujących

Elementy Runbook działające w piaskownicach platformy Azure nie obsługują wywoływania procesów (takich jak. exe lub subprocess. Call). Wynika to z faktu, że Piaskownice platformy Azure to procesy udostępnione w kontenerach, które mogą nie mieć dostępu do wszystkich bazowych interfejsów API. W przypadku scenariuszy, w których wymagane jest oprogramowanie innej firmy lub wywoływanie procesów podrzędnych, zaleca się wykonanie elementu Runbook w [hybrydowym procesie roboczym elementu Runbook](automation-hybrid-runbook-worker.md).

### <a name="device-and-application-characteristics"></a>Charakterystyka urządzenia i aplikacji

Zadania elementu Runbook uruchamiane w piaskownicach platformy Azure nie mają dostępu do żadnych właściwości urządzeń i aplikacji. Najbardziej typowym interfejsem API używanym do wykonywania zapytań dotyczących metryk wydajności w systemie Windows jest usługa WMI. Niektóre z tych typowych metryk to użycie pamięci i procesora CPU. Jednak nie ma znaczenia, który interfejs API jest używany. Zadania uruchomione w chmurze nie mają dostępu do wdrożenia firmy Microsoft opartego na sieci Web (WBEM), który jest oparty na model wspólnych informacji (CIM), które są standardami branżowymi do definiowania cech urządzeń i aplikacji.

## <a name="job-statuses"></a>Stany zadań

W poniższej tabeli opisano różne stany, które może przyjmować zadanie. Program PowerShell ma dwa typy błędów, błędy kończące się i niepowodujące zakończenia. Podczas kończenia błędów ustawienie stanu elementu Runbook **nie powiodło się** , jeśli wystąpi. Błędy niepowodujące zakończenia umożliwiają wykonywanie skryptu nawet po wystąpieniu. Przykładem błędu niekończącego jest użycie polecenia cmdlet `Get-ChildItem` ze ścieżką, która nie istnieje. Program PowerShell widzi, że ścieżka nie istnieje, zgłasza błąd i kontynuuje do następnego folderu. Ten błąd nie ustawi stanu elementu Runbook na **Niepowodzenie** i może zostać oznaczony jako **zakończony**. Aby wymusić zatrzymanie elementu Runbook na nieprzerwanym błędzie, można użyć `-ErrorAction Stop` w poleceniu cmdlet.

| Stan | Opis |
|:--- |:--- |
| Zakończone |Zadanie zostało ukończone pomyślnie. |
| Niepowodzenie |W przypadku [elementów Runbook graficznych i programu PowerShell](automation-runbook-types.md)nie można skompilować elementu Runbook. W przypadku [elementów Runbook skryptu programu PowerShell](automation-runbook-types.md)nie można uruchomić elementu Runbook lub zadanie miało wyjątek. |
| Niepowodzenie, oczekiwanie na zasoby |Zadanie nie powiodło się, ponieważ osiągnął limit [godziwego udziału](#fair-share) trzy razy i został uruchomiony z tego samego punktu kontrolnego lub od początku elementu Runbook za każdym razem. |
| W kolejce |Zadanie oczekuje na dostępność zasobów dla procesu roboczego automatyzacji, tak aby można było je uruchomić. |
| Uruchamianie |Zadanie zostało przypisane do procesu roboczego, a system go uruchamia. |
| Wznawianie |System wznawia zadanie po jego wstrzymaniu. |
| Działanie |Zadanie jest uruchomione. |
| Uruchamianie, oczekiwanie na zasoby |Zadanie zostało zwolnione, ponieważ osiągnęło odpowiedni limit [udostępniania](#fair-share) . Wkrótce wznawia od ostatniego punktu kontrolnego. |
| Zatrzymano |Zadanie zostało zatrzymane przez użytkownika przed jego ukończeniem. |
| Zatrzymywanie |System zatrzymuje zadanie. |
| Suspended |Zadanie zostało zawieszone przez użytkownika, przez system lub za pomocą polecenia w elemencie Runbook. Jeśli element Runbook nie ma punktu kontrolnego, rozpocznie się od początku elementu Runbook. Jeśli ma punkt kontrolny, można go uruchomić ponownie i wznowić od ostatniego punktu kontrolnego. Element Runbook jest zawieszony tylko przez system w przypadku wystąpienia wyjątku. Domyślnie ErrorActionPreference jest ustawiony na **Kontynuuj**, co oznacza, że zadanie zachowuje się w przypadku błędu. Jeśli ta zmienna preferencji ma wartość **Zatrzymaj**, zadanie zawiesza się po błędzie. Dotyczy tylko [elementów Runbook środowiska graficznego i programu PowerShell](automation-runbook-types.md) . |
| Zawieszanie |System próbuje zawiesić zadanie na żądanie użytkownika. Element Runbook musi dotrzeć do swojego następnego punktu kontrolnego, zanim będzie można go wstrzymać. Jeśli przeszedł już ostatni punkt kontrolny, kończy się przed jego wstrzymaniem. Dotyczy tylko [elementów Runbook środowiska graficznego i programu PowerShell](automation-runbook-types.md) . |

## <a name="viewing-job-status-from-the-azure-portal"></a>Wyświetlanie stanu zadania z Azure Portal

Można wyświetlić podsumowanie stanu wszystkich zadań elementu Runbook lub przejść do szczegółów dotyczących określonego zadania elementu Runbook w Azure Portal. Możesz również skonfigurować integrację z obszarem roboczym Log Analytics, aby przesyłać strumieniowo stan zadań elementu Runbook i zadania. Więcej informacji na temat integrowania z dziennikami Azure Monitor można znaleźć [w temacie przekazywanie stanu zadań i strumieni zadań z automatyzacji do Azure monitor dzienników](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Podsumowanie zadań elementu Runbook usługi Automation

Po prawej stronie wybranego konta usługi Automation można zobaczyć podsumowanie wszystkich zadań elementu Runbook w obszarze kafelka **Statystyka zadania** .

![Kafelek statystyk zadania](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Ten kafelek przedstawia liczbę i graficzną reprezentację stanu zadania dla wszystkich wykonywanych zadań.

Kliknięcie kafelka przedstawia stronę **zadania** , która zawiera skróconą listę wszystkich wykonanych zadań. Ta strona zawiera stan, godziny rozpoczęcia i czasy zakończenia.

![Strona zadań konta usługi Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Listę zadań można filtrować, wybierając kolejno opcje **Filtruj zadania** i Filtruj według określonego elementu Runbook, stanu zadania lub z listy rozwijanej oraz zakres czasu, który ma zostać przeszukany.

![Stan zadania filtrowania](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Możesz też wyświetlić szczegóły podsumowania zadania dla określonego elementu Runbook, wybierając ten element Runbook ze strony **elementy Runbook** na koncie usługi Automation, a następnie wybierając kafelek **zadania** . Ta akcja przedstawia stronę **zadania** , a następnie można kliknąć rekord zadania, aby wyświetlić jego szczegóły i dane wyjściowe.

![Strona zadań konta usługi Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Podsumowanie zadania

Można wyświetlić listę wszystkich zadań, które zostały utworzone dla określonego elementu Runbook, oraz ich najnowszy stan. Tę listę można filtrować według stanu zadania i zakresu dat dla ostatniej zmiany zadania. Aby wyświetlić szczegółowe informacje i dane wyjściowe, kliknij nazwę zadania. Widok szczegółowy zadania zawiera wartości parametrów elementu Runbook udostępnione danemu zadaniu.

Następujące kroki służą do wyświetlania zadań elementu Runbook.

1. W Azure Portal wybierz pozycję **Automatyzacja** , a następnie wybierz nazwę konta usługi Automation.
2. Z poziomu centrum wybierz pozycję **elementy Runbook** , a następnie na stronie **elementy Runbook** wybierz element Runbook z listy.
3. Na stronie wybranego elementu Runbook kliknij kafelek **zadania** .
4. Kliknij jedno z zadań na liście i na stronie Szczegóły zadania elementu Runbook możesz wyświetlić jego szczegóły i dane wyjściowe.

## <a name="retrieving-job-status-using-powershell"></a>Pobieranie stanu zadania przy użyciu programu PowerShell

Do pobrania zadań utworzonych dla elementu Runbook i szczegółów dotyczących określonego zadania można użyć programu [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) . Po uruchomieniu elementu Runbook za pomocą programu PowerShell przy użyciu polecenia [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook)usługa zwróci wynikowe zadanie. Użyj polecenia [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) , aby pobrać dane wyjściowe zadania.

Następujące przykładowe polecenia pobierają ostatnie zadanie dla przykładowego elementu Runbook i wyświetlają jego stan, wartości podane dla parametrów elementu Runbook oraz dane wyjściowe zadania.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Poniższy przykład pobiera dane wyjściowe dla określonego zadania i zwraca każdy rekord. W przypadku wystąpienia wyjątku dla jednego z rekordów wyjątek jest zapisywana zamiast wartości. Takie zachowanie jest przydatne, ponieważ wyjątki mogą zawierać dodatkowe informacje, które mogą nie być rejestrowane normalnie podczas danych wyjściowych.

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

## <a name="get-details-from-activity-log"></a>Pobierz szczegóły z dziennika aktywności

Inne szczegóły, takie jak osoba lub konto, które uruchomiło element Runbook, można pobrać z dziennika aktywności dla konta usługi Automation. Poniższy przykład programu PowerShell zapewnia ostatniemu użytkownikowi uruchomienie elementu Runbook:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>Atrakcyjny udział

Aby udostępnić zasoby między wszystkimi elementami Runbook w chmurze, Azure Automation tymczasowo zwalnia lub przerywa każde zadanie, które zostało uruchomione przez ponad trzy godziny. Zadania dla [elementów Runbook opartych na programie PowerShell](automation-runbook-types.md#powershell-runbooks) i [elementów Runbook języka Python](automation-runbook-types.md#python-runbooks) są zatrzymane i nie uruchamiane ponownie, a stan zadania to zatrzymane.

W przypadku długotrwałych zadań zaleca się użycie [hybrydowego procesu roboczego elementu Runbook](automation-hrw-run-runbooks.md#job-behavior). Hybrydowe procesy robocze elementów Runbook nie są ograniczone przez sprawiedliwy udział i nie mają ograniczenia czasu wykonywania elementu Runbook. Pozostałe [limity](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) zadań dotyczą zarówno piaskownic systemu Azure, jak i hybrydowych procesów roboczych elementów Runbook. Chociaż hybrydowe procesy robocze elementów Runbook nie są ograniczone przez 3-godzinny limit udziałów, elementy Runbook są uruchamiane w celu obsługi zachowań ponownego uruchamiania z nieoczekiwanych problemów z infrastrukturą lokalną.

Kolejną opcją jest optymalizacja elementu Runbook przy użyciu podrzędnych elementów Runbook. Jeśli element Runbook pętle za pomocą tej samej funkcji w kilku zasobach, takich jak operacja bazy danych w kilku bazach danych, można przenieść tę funkcję do [podrzędnego elementu Runbook](automation-child-runbooks.md) i wywołać ją za pomocą polecenia cmdlet [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) . Każdy z tych podrzędnych elementów Runbook jest wykonywany równolegle w oddzielnych procesach. To zachowanie zmniejsza łączny czas pracy nadrzędnego elementu Runbook. W elemencie Runbook można użyć polecenia cmdlet [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) , aby sprawdzić stan zadania dla każdego elementu podrzędnego, jeśli istnieją operacje wykonywane po zakończeniu podrzędnego elementu Runbook.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat różnych metod, których można użyć do uruchomienia elementu Runbook w Azure Automation, zobacz [Uruchamianie elementu Runbook w programie Azure Automation](automation-starting-a-runbook.md)
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów dokumentacji i uczenia dotyczącej języka, zapoznaj się z dokumentacją programu [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
