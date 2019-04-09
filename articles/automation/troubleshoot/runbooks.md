---
title: Rozwiązywanie problemów przy użyciu elementów Runbook usługi Azure Automation
description: Dowiedz się, jak rozwiązywać problemy związane z elementami runbook usługi Azure Automation
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: f93f6c8891ba9f7407310a8f09387e97f5c1f578
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267348"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Rozwiązywanie problemów z elementami runbook

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Błędy uwierzytelniania podczas pracy z elementami runbook usługi Azure Automation

### <a name="sign-in-failed"></a>Scenariusz: Zaloguj się do konta platformy Azure nie powiodło się

#### <a name="issue"></a>Problem

Zostanie wyświetlony następujący błąd podczas pracy z `Add-AzureAccount` lub `Connect-AzureRmAccount` polecenia cmdlet.
:

```error
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, jeśli nazwa zasobu poświadczeń jest nieprawidłowy. Ten błąd może również wystąpić, jeśli nazwa użytkownika i hasło, których użyto do skonfigurowania zasób poświadczenia usługi Automation są nieprawidłowe.

#### <a name="resolution"></a>Rozwiązanie

Aby określić, na czym polega problem, wykonaj następujące czynności:  

1. Upewnij się, że nie masz żadnych znaków specjalnych. Obejmuje znaki **\@** znaków w nazwie zasobu poświadczeń usługi Automation za pomocą połączenia z platformą Azure.  
2. Upewnij się, że można użyć nazwy użytkownika i hasło, które są przechowywane w poświadczeniach usługi Azure Automation w edytorze lokalnym środowisku PowerShell ISE. Możesz zrobić Sprawdź nazwę użytkownika i hasło są poprawne, uruchamiając następujące polecenia cmdlet w środowisku PowerShell ISE:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Jeśli uwierzytelniania lokalnie nie powiedzie się, oznacza to, że możesz nie zostały prawidłowo skonfigurowane poświadczeń usługi Azure Active Directory. Zapoznaj się [uwierzytelnianie na platformie Azure przy użyciu usługi Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) wpis w blogu można uzyskać konta usługi Azure Active Directory, poprawnie skonfigurowany.  

4. Jeśli wygląda na to błąd przejściowy, spróbuj dodać logikę ponawiania próby do procedury usługi uwierzytelniania umożliwiają uwierzytelnianie działał on bardziej niezawodnie.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>Scenariusz: Nie można odnaleźć subskrypcji platformy Azure

#### <a name="issue"></a>Problem

Zostanie wyświetlony następujący błąd podczas pracy z `Select-AzureSubscription` lub `Select-AzureRmSubscription` poleceń cmdlet:

```error
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Błąd

Ten błąd może wystąpić, jeśli:

* Nazwa subskrypcji jest nieprawidłowy

* Użytkownik usługi Azure Active Directory, który próbuje uzyskać szczegółów subskrypcji nie jest skonfigurowana jako administratora subskrypcji.

#### <a name="resolution"></a>Rozwiązanie

Wykonaj poniższe kroki, aby ustalić, czy zostały uwierzytelnione na platformie Azure i mają dostęp do subskrypcji, którą próbujesz wybierz:  

1. Aby upewnić się, że działa ona autonomicznych, testowanie skryptu poza usługi Azure Automation.
2. Upewnij się, że uruchomieniu `Add-AzureAccount` zanim uruchomisz polecenie cmdlet `Select-AzureSubscription` polecenia cmdlet. 
3. Dodaj `Disable-AzureRmContextAutosave –Scope Process` na początku elementu runbook. To polecenie cmdlet zapewnia, że wszystkie poświadczenia mają zastosowanie tylko do wykonywania bieżącego elementu runbook.
4. Jeśli nadal widzisz ten komunikat o błędzie, należy zmodyfikować kod, dodając **AzureRmContext** następujący parametr `Add-AzureAccount` polecenia cmdlet, a następnie wykonaj kod.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>Scenariusz: Uwierzytelnianie na platformie Azure nie powiodło się, ponieważ włączono uwierzytelnianie wieloskładnikowe

#### <a name="issue"></a>Problem

Pojawi się następujący błąd podczas uwierzytelniania na platformie Azure przy użyciu platformy Azure nazwy użytkownika i hasła:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Przyczyna

Jeśli masz usługę uwierzytelnianie wieloskładnikowe na koncie platformy Azure, nie możesz użyć użytkownika usługi Azure Active Directory do uwierzytelniania na platformie Azure. Zamiast tego należy używać certyfikatu lub jednostki usługi do uwierzytelniania na platformie Azure.

#### <a name="resolution"></a>Rozwiązanie

Aby używać certyfikatu za pomocą poleceń cmdlet modelu klasycznym wdrożeniu platformy Azure, zapoznaj się [tworzenie i dodawanie certyfikatu do zarządzania usługami platformy Azure.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Aby użyć jednostki usługi przy użyciu poleceń cmdlet usługi Azure Resource Manager, zapoznaj się [Tworzenie nazwy głównej, przy użyciu witryny Azure portal usługi](../../active-directory/develop/howto-create-service-principal-portal.md) i [uwierzytelniania jednostki usługi przy użyciu usługi Azure Resource Manager.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="common-errors-when-working-with-runbooks"></a>Typowe błędy podczas pracy z elementami runbook

### <a name="child-runbook-object"></a>Podrzędny element runbook zwraca błąd, jeśli strumień wyjściowy zawiera obiekty, a nie proste typy danych

#### <a name="issue"></a>Problem

Zostanie wyświetlony następujący błąd podczas wywoływania podrzędnego elementu runbook za pomocą `-Wait` przełącznika strumień wyjściowy zawiera i obiektu:

```error
Object reference not set to an instance of an object
```

#### <a name="cause"></a>Przyczyna

Jest to znany problem gdzie [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) nie obsługuje strumień wyjściowy poprawnie, jeśli zawiera on obiektów.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać problem zalecane jest, zamiast tego zaimplementować logikę sondowania i użyj [Get AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) polecenie cmdlet do pobierania danych wyjściowych. Przykład logika jest zdefiniowany w poniższym przykładzie.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

### <a name="get-serializationsettings"></a>Scenariusz: Zostanie wyświetlony błąd w strumienie zadania o get_SerializationSettings — metoda

#### <a name="issue"></a>Problem

Zobacz błędu w strumienie zadania elementu runbook z następującym komunikatem:

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type 
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly 
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany przy użyciu poleceń cmdlet usługi AzureRM i Az w elemencie runbook. Występuje, gdy importujesz `Az` przed zaimportowaniem `AzureRM`.

#### <a name="resolution"></a>Rozwiązanie

Polecenia cmdlet az i AzureRM nie można zaimportować i używane w ten sam element runbook, aby dowiedzieć się więcej na temat obsługi Az w usłudze Azure Automation, zobacz [Az Obsługa modułu w usłudze Azure Automation](../az-modules.md).

### <a name="task-was-cancelled"></a>Scenariusz: Element runbook nie powiodło się z powodu błędu: Zadanie zostało anulowane

#### <a name="issue"></a>Problem

Element runbook nie powiedzie się z powodu błędu podobnego do poniższego przykładu:

```error
Exception: A task was canceled.
```

#### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przy użyciu nieaktualnych moduły platformy Azure.

#### <a name="resolution"></a>Rozwiązanie

Ten błąd można rozwiązać, aktualizując swoje moduły platformy Azure do najnowszej wersji.

Na koncie usługi Automation kliknij **modułów**i kliknij przycisk **modułów Azure aktualizacji**. Aktualizacja trwa około 15 minut, po wykonaniu tych czynności ponownie uruchomić element runbook, który został kończy się niepowodzeniem. Aby dowiedzieć się więcej o aktualizowaniu moduły, zobacz [modułów Azure aktualizacji w usłudze Azure Automation](../automation-update-azure-modules.md).

### <a name="runbook-auth-failure"></a>Scenariusz: Elementy Runbook się niepowodzeniem podczas rozwiązywania problemów związanych z wieloma subskrypcjami

#### <a name="issue"></a>Problem

Podczas wykonywania elementów runbook w usłudze `Start-AzureRmAutomationRunbook`, element runbook nie może zarządzać zasobami platformy Azure.

#### <a name="cause"></a>Przyczyna

Element runbook nie używa poprawny kontekst, podczas uruchamiania.

#### <a name="resolution"></a>Rozwiązanie

Podczas pracy z wieloma subskrypcjami, kontekstu subskrypcji mogą zostać utracone podczas wywoływania elementów runbook. Aby upewnić się, że kontekstu subskrypcji jest przekazywany do elementów runbook, należy dodać `AzureRmContext` parametru do polecenia cmdlet i przekazać kontekst do niego. Ponadto zaleca się używanie `Disable-AzureRmContextAutosave` polecenia cmdlet z **procesu** zakres, aby upewnić się, że poświadczenia, których używasz są używane tylko dla bieżącego elementu runbook.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

### <a name="not-recognized-as-cmdlet"></a>Scenariusz: Element runbook zakończy się niepowodzeniem ze względu na brak polecenia cmdlet

#### <a name="issue"></a>Problem

Element runbook nie powiedzie się z powodu błędu podobnego do poniższego przykładu:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Przyczyna

Ten błąd może się zdarzyć, oparty na jednym z następujących powodów:

1. Moduł zawierający polecenie cmdlet nie jest zaimportowany do konta usługi automation
2. Moduł zawierający polecenie cmdlet jest zaimportowane, ale jest nieaktualna

#### <a name="resolution"></a>Rozwiązanie

Ten błąd można rozwiązać, wykonując jedną z następujących czynności:

Jeśli moduł jest moduł platformy Azure, zobacz [jak aktualizowanie modułów programu Azure PowerShell w usłudze Azure Automation](../automation-update-azure-modules.md) dowiesz się, jak zaktualizować moduły na koncie usługi automation.

Jeśli jest oddzielny moduł, upewnij się, że moduł w zaimportowane na koncie usługi Automation.

### <a name="job-attempted-3-times"></a>Scenariusz: Podjęto próbę rozpoczęcia zadania elementu runbook trzy razy, ale nie można uruchomić w każdym

#### <a name="issue"></a>Problem

Element runbook nie powiodło się z powodu błędu:

```error
The job was tried three times but it failed
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje ze względu na jeden z następujących problemów:

1. Limit pamięci. Udokumentowane ograniczenia, o ile pamięci jest przydzielony do piaskownicy znajduje się na [limity usługi Automation](../../azure-subscription-service-limits.md#automation-limits). Zadanie może nie go, jeśli jest używany więcej niż 400 MB pamięci.

2. Gniazd sieciowych. Piaskownice platformy Azure są ograniczone do 1000 równoczesnych sieci gniazda, zgodnie z opisem w [limity usługi Automation](../../azure-subscription-service-limits.md#automation-limits).

3. Niezgodne modułu. Ten błąd może wystąpić, jeśli moduł zależności nie są poprawne, a nie są one, element runbook zwykle zwraca "nie znaleziono polecenia" lub "Nie można powiązać parametr" wiadomości.

4. Element runbook próba wywołania pliku wykonywalnego lub subprocess w elemencie runbook, które jest uruchamiane w piaskownicy usługi Azure. Ten scenariusz nie jest obsługiwany w piaskownicach platformy Azure.

#### <a name="resolution"></a>Rozwiązanie

Dowolne z poniższych rozwiązań rozwiązać ten problem:

* Sugerowane metody do pracy w ramach limitu pamięci są podziału obciążenia między wiele elementów runbook, nie chcesz przetworzyć tak dużej ilości danych w pamięci, aby nie zapisywać niepotrzebne dane wyjściowe z elementami runbook lub należy wziąć pod uwagę liczbę punktów kontrolnych zapis do Twojego przepływu pracy programu PowerShell elementy runbook. Clear — metoda, można użyć takich jak `$myVar.clear()` aby umożliwić wyczyszczenie zmiennej i użycia `[GC]::Collect()` natychmiastowe uruchomienie wyrzucania elementów bezużytecznych. Te akcje mniejsze zużycie pamięci elementu runbook w czasie wykonywania.

* Aktualizuj moduły platformy Azure przez wykonanie kroków [jak aktualizowanie modułów programu Azure PowerShell w usłudze Azure Automation](../automation-update-azure-modules.md).  

* Innym rozwiązaniem jest uruchomienie elementu runbook na [hybrydowego procesu roboczego Runbook](../automation-hrw-run-runbooks.md). Hybrydowe procesy robocze nie są ograniczone przez limity pamięci i sieci, które są piaskownic platformy Azure.

* Jeśli musisz wywołać procesu (na przykład .exe lub subprocess.call) w elemencie runbook, należy uruchomić element runbook na [hybrydowego procesu roboczego Runbook](../automation-hrw-run-runbooks.md).

### <a name="fails-deserialized-object"></a>Scenariusz: Element Runbook nie powiodło się z powodu zdeserializowany obiekt

#### <a name="issue"></a>Problem

Element runbook nie powiodło się z powodu błędu:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Przyczyna

Jeśli element runbook przepływu pracy programu PowerShell, złożone obiekty są przechowywane w formacie po deserializacji do utrwala swój stan elementu runbook, jeśli przepływ pracy jest zawieszone.

#### <a name="resolution"></a>Rozwiązanie

Jedną z następujących trzech rozwiązań tego problemu:

1. Jeśli użytkownik jest przekazanie w potoku złożonych obiektów z jednego polecenia cmdlet do innego, opakować tych poleceń cmdlet w bloku skryptu InlineScript.
2. Przekazać nazwę lub wartość, które należy z obiektu złożonego zamiast przekazywać cały obiekt.
3. Użyj elementu runbook programu PowerShell zamiast elementu runbook przepływu pracy programu PowerShell.

### <a name="runbook-fails"></a>Scenariusz: Mój element Runbook nie powiedzie się, ale działa, gdy uruchomiono lokalnie

#### <a name="issue"></a>Problem

Skrypt zakończy się niepowodzeniem kiedy uruchomione jako element runbook, ale to działa, gdy uruchomiono lokalnie.

#### <a name="cause"></a>Przyczyna

Skrypt może zakończyć się niepowodzeniem podczas uruchamiania jako elementu runbook dla jednego z następujących powodów:

1. Problemy z uwierzytelnianiem
2. Wymagane moduły nie są importowane lub nieaktualny.
3. Skrypt może monitowania do interakcji z użytkownikiem.
4. Niektóre moduły należy założeń dotyczących bibliotek, które znajdują się na komputerach z Windows. Te biblioteki nie może być obecny w piaskownicy.
5. Niektóre moduły zależy od wersji programu .NET, który jest inny niż ten dostępny w piaskownicy.

#### <a name="resolution"></a>Rozwiązanie

Dowolne z poniższych rozwiązań może rozwiązać ten problem:

1. Sprawdź poprawnie [uwierzytelnianie na platformie Azure](../manage-runas-account.md).
2. Upewnij się, Twoje [moduły platformy Azure są importowane i bądź na bieżąco](../automation-update-azure-modules.md).
3. Upewnij się, że żaden z poleceń cmdlet są należy podać informacje. To zachowanie nie jest obsługiwane w elementach runbook.
4. Sprawdź, czy wszystkie elementy, które jest częścią modułu zależny od elementu, który nie znajduje się w module.
5. Piaskownice Azure korzystają z .NET Framework 4.7.2, jeśli moduł używa nowszej wersji, który nie będzie działać. W takim przypadku należy używać [hybrydowego procesu roboczego Runbook](../automation-hybrid-runbook-worker.md)

Jeśli żadne z tych rozwiązań rozwiązuje swoje problemReview [zadania dzienniki](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) szczegóły dotyczące w, dlaczego dany element runbook nie powiodło się.

### <a name="quota-exceeded"></a>Scenariusz: Zadanie elementu Runbook nie powiodło się, ponieważ przekroczono limit przydziału

#### <a name="issue"></a>Problem

Zadanie elementu runbook zakończy się niepowodzeniem z powodu błędu:

```error
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas wykonywania zadania przekracza 500 minut bezpłatny limit przydziału dla konta usługi. Ten limit przydziału ma zastosowanie do wszystkich typów zadań do wykonania zadania. Niektóre z tych zadań testowanie za pomocą zadania uruchamiania zadania z poziomu portalu, wykonywanie zadania za pomocą elementów webhook, lub planowania zadania do wykonania za pomocą witryny Azure portal lub w centrum danych. Aby dowiedzieć się więcej o cenach usługi Automation, zobacz [cennik usługi Automation](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Rozwiązanie

Jeśli chcesz użyć więcej niż 500 minut w ramach przetwarzania na miesiąc, musisz zmienić subskrypcję bezpłatną warstwę do warstwy podstawowa. Możesz uaktualnić do warstwy podstawowa, wykonując następujące czynności:  

1. Zaloguj się do Twojej subskrypcji platformy Azure.  
2. Wybierz konto usługi Automation, które chcesz zmienić.  
3. Kliknij przycisk **ustawienia** > **ceny**.
4. Kliknij przycisk **Włącz** na dole strony, aby uaktualnić konto do **podstawowe** warstwy.

### <a name="cmdlet-not-recognized"></a>Scenariusz: Polecenie cmdlet nie został rozpoznany podczas wykonywania elementu runbook

#### <a name="issue"></a>Problem

Zadanie elementu runbook zakończy się niepowodzeniem z powodu błędu:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany aparatu programu PowerShell nie można znaleźć polecenia cmdlet, którego używasz w elemencie runbook. Ten błąd może być, ponieważ moduł zawierający polecenie cmdlet nie ma konta, występuje konflikt nazw z nazwą elementu runbook lub polecenie cmdlet występuje także w innym module i automatyzacji nie może rozpoznać nazwy.

#### <a name="resolution"></a>Rozwiązanie

Dowolne z poniższych rozwiązań rozwiązać ten problem:  

* Sprawdź, czy nazwa polecenia cmdlet został wprowadzony poprawnie.  
* Upewnij się, że polecenia cmdlet istnieje na koncie usługi Automation i że nie istnieją żadne konflikty. Aby sprawdzić, czy polecenie cmdlet jest obecny, otwórz element runbook w trybie edycji i wyszukaj ma zostać znaleziona w bibliotece lub uruchom polecenie cmdlet `Get-Command <CommandName>`. Po zweryfikowaniu, polecenie cmdlet jest dostępny dla konta, które i że nie są nazwa powoduje konflikt z innych poleceń cmdlet lub elementy runbook, należy dodać go do obszaru roboczego i upewnij się, że używasz prawidłowego parametru w elemencie runbook.  
* Jeśli występuje konflikt nazw i polecenia cmdlet jest dostępna w dwóch różnych modułach, możesz rozwiązać ten problem, korzystając z w pełni kwalifikowana nazwa polecenia cmdlet. Na przykład, można użyć **ModuleName\CmdletName**.  
* Jeśli użytkownik jest wykonywanie grupy hybrydowych procesów roboczych elementu runbook w środowisku lokalnym, a następnie upewnij się, że moduł i polecenia cmdlet zostanie zainstalowana na komputerze, który jest hostem hybrydowy proces roboczy.

### <a name="long-running-runbook"></a>Scenariusz: Długotrwałe elementu runbook nie powiedzie się

#### <a name="issue"></a>Problem

Element runbook zawiera w **zatrzymane** stan po uruchomieniu przez 3 godziny. Również może zostać wyświetlony błąd:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

To zachowanie jest celowe w piaskownicach platformy Azure z powodu "Udział" Monitorowanie procesów w usłudze Azure Automation. Jeśli wykonuje dłuższy niż trzy godziny, udział automatycznie zatrzymuje elementu runbook. Stan elementu runbook, który przechodzi upłynął limit czasu udziału uczciwego różni się typ elementu runbook. Elementy runbook programu PowerShell i Python są ustawione na **zatrzymane** stanu. Elementy runbook przepływu pracy programu PowerShell są ustawione na **niepowodzenie**.

#### <a name="cause"></a>Przyczyna

Element runbook został uruchomiony za pośrednictwem limit 3 godziny, dozwolone przez udział w piaskownicy usługi Azure.

#### <a name="resolution"></a>Rozwiązanie

Jeden zalecanych rozwiązań jest uruchomienie elementu runbook na [hybrydowego procesu roboczego Runbook](../automation-hrw-run-runbooks.md).

Hybrydowe procesy robocze nie są ograniczone przez [udział](../automation-runbook-execution.md#fair-share) limit elementu runbook 3 godziny, będące w piaskownicach platformy Azure. Elementy Runbook został uruchomiony na hybrydowych procesów roboczych Runbook należy opracować do obsługi zachowania ponownego uruchomienia, jeśli występują problemy z infrastrukturą lokalną nieoczekiwany.

Innym rozwiązaniem jest optymalizacja elementu runbook, tworząc [podrzędnych elementów runbook](../automation-child-runbooks.md). Jeśli element runbook w pętli taką samą funkcję na kilka zasobów, takich jak operacji bazy danych w wielu bazach danych, można przenieść tej funkcji do podrzędnego elementu runbook. Każda z tych podrzędnych elementów runbook jest wykonywane równolegle w osobnych procesach. To zachowanie zmniejsza całkowitą ilość czasu dla nadrzędnego elementu runbook zakończyć.

Dostępne są następujące polecenia cmdlet programu PowerShell, które umożliwiają scenariusza podrzędnego elementu runbook:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) — to polecenie cmdlet pozwala na uruchamianie elementu runbook i parametry są przekazywane do elementu runbook

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) — w przypadku operacji, które należy wykonać po zakończeniu działania podrzędnego elementu runbook, to polecenie cmdlet pozwala sprawdzić stan zadania dla każdego elementu podrzędnego.

### <a name="expired webhook"></a>Scenariusz: Stan: 400 Niewłaściwe żądanie podczas wywoływania elementu webhook

#### <a name="issue"></a>Problem

Podczas próby wywołania elementu webhook dla elementu runbook usługi Azure Automation, pojawi się następujący błąd:

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>Przyczyna

Element webhook, który próbujesz wywołać, jest wyłączony lub wygasł.

#### <a name="resolution"></a>Rozwiązanie

Jeśli element webhook jest wyłączona, można ponownie włączyć elementu webhook w witrynie Azure portal. Po wygaśnięciu element webhook elementu webhook musi zostać usunięte i utworzone ponownie. Można jedynie [odnowić element webhook](../automation-webhooks.md#renew-webhook) Jeśli jeszcze nie upłynął.

### <a name="429"></a>Scenariusz: 429: Współczynnik żądań jest obecnie zbyt duży. Spróbuj ponownie

#### <a name="issue"></a>Problem

Pojawi się następujący komunikat o błędzie podczas uruchamiania `Get-AzureRmAutomationJobOutput` polecenia cmdlet:

```error
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić podczas pobierania danych wyjściowych zadania z poziomu elementu runbook, który ma wiele [strumienie pełne](../automation-runbook-output-and-messages.md#verbose-stream).

#### <a name="resolution"></a>Rozwiązanie

Istnieją dwa sposoby, aby rozwiązać ten problem:

* Edytuj element runbook i zmniejszyć liczbę strumieni zadań, które on emituje.
* Zmniejsz liczbę strumieni, które mają zostać pobrane podczas uruchamiania polecenia cmdlet. Aby wykonać to zachowanie, możesz określić `-Stream Output` parametr `Get-AzureRmAutomationJobOutput` polecenie cmdlet do pobierania tylko strumieni danych wyjściowych. 

### <a name="cannot-invoke-method"></a>Scenariusz: Zadanie programu PowerShell zakończy się niepowodzeniem z powodu błędu: Nie można wywołać metody

#### <a name="issue"></a>Problem

Pojawi się następujący komunikat o błędzie podczas uruchamiania zadania programu PowerShell w elemencie runbook działające na platformie Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

#### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić po uruchomieniu zadania w elemencie runbook został uruchomiony w systemie Azure PowerShell. To zachowanie może być fakt, że elementy runbook uruchomione na platformie Azure piaskownicy mogą nie działać [tryb pełny języka](/powershell/module/microsoft.powershell.core/about/about_language_modes)).

#### <a name="resolution"></a>Rozwiązanie

Istnieją dwa sposoby, aby rozwiązać ten problem:

* Zamiast używania `Start-Job`, użyj `Start-AzureRmAutomationRunbook` do uruchamiania elementu runbook
* Jeśli element runbook ma ten komunikat o błędzie, uruchom go na hybrydowy proces roboczy elementu Runbook

Aby dowiedzieć się więcej na temat tego zachowania i innych zachowań elementów Runbook usługi Azure Automation, zobacz [zachowanie elementu Runbook](../automation-runbook-execution.md#runbook-behavior).

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie był widoczny problemu lub są w stanie rozwiązać problemu, odwiedź jedną z następujących kanałów obsługi więcej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.
