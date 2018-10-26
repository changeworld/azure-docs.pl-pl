---
title: Rozwiązywanie problemów przy użyciu elementów Runbook usługi Azure Automation
description: Dowiedz się, jak rozwiązywać problemy związane z elementami runbook usługi Azure Automation
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/17/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 532d3d73c939a44678091734f2bbff22267ab6b7
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094868"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Rozwiązywanie problemów z elementami runbook

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Błędy uwierzytelniania podczas pracy z elementami runbook usługi Azure Automation

### <a name="sign-in-failed"></a>Scenariusz: Zaloguj się do konta platformy Azure nie powiodło się

#### <a name="issue"></a>Problem

Zostanie wyświetlony następujący błąd podczas pracy z `Add-AzureAccount` lub `Connect-AzureRmAccount` polecenia cmdlet.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, jeśli nazwa zasobu poświadczeń jest nieprawidłowa lub nazwę użytkownika i hasło, których użyto do skonfigurowania zasób poświadczenia usługi Automation są nieprawidłowe.

#### <a name="resolution"></a>Rozwiązanie

Aby określić, na czym polega problem, wykonaj następujące czynności:  

1. Upewnij się, że nie masz żadnych znaków specjalnych, w tym **@** znaków w nazwie zasobu poświadczeń usługi Automation za pomocą połączenia z platformą Azure.  
2. Sprawdź, czy można użyć nazwy użytkownika i hasła, które są przechowywane w poświadczeniach usługi Azure Automation w edytorze lokalnym środowisku PowerShell ISE. Możesz zrobić Sprawdź nazwę użytkownika i hasło są poprawne, uruchamiając następujące polecenia cmdlet w środowisku PowerShell ISE:  

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

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Błąd

Ten błąd występuje, jeśli nazwa subskrypcji jest nieprawidłowy lub użytkownika usługi Azure Active Directory, który próbuje uzyskać szczegółów subskrypcji nie jest skonfigurowana jako administratora subskrypcji.

#### <a name="resolution"></a>Rozwiązanie

Aby ustalić, czy poprawnie uwierzytelniono na platformie Azure i mieć dostęp do subskrypcji, w którym próbujesz wybierz, wykonaj następujące czynności:  

1. Testowanie skryptu poza usługi Azure Automation się upewnić, że działa ona autonomicznych.
2. Upewnij się, że uruchomieniu **Add-AzureAccount** zanim uruchomisz polecenie cmdlet **Select-AzureSubscription** polecenia cmdlet.  
3. Jeśli nadal widzisz ten komunikat o błędzie, należy zmodyfikować kod, dodając **- AzureRmContext** następujący parametr **Add-AzureAccount** polecenia cmdlet, a następnie wykonaj kod.

   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>Scenariusz: Uwierzytelnianie nie powiodło się, ponieważ włączono uwierzytelnianie wieloskładnikowe Azure

#### <a name="issue"></a>Problem

Pojawi się następujący błąd podczas uwierzytelniania na platformie Azure przy użyciu platformy Azure nazwy użytkownika i hasła:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Przyczyna

Jeśli masz usługę uwierzytelnianie wieloskładnikowe na koncie platformy Azure, nie możesz użyć użytkownika usługi Azure Active Directory do uwierzytelniania na platformie Azure. Zamiast tego należy używać certyfikatu lub jednostki usługi do uwierzytelniania na platformie Azure.

#### <a name="resolution"></a>Rozwiązanie

Aby używać certyfikatu za pomocą poleceń cmdlet modelu klasycznym wdrożeniu platformy Azure, zapoznaj się [tworzenie i dodawanie certyfikatu do zarządzania usługami platformy Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Aby użyć jednostki usługi przy użyciu poleceń cmdlet usługi Azure Resource Manager, zapoznaj się [Tworzenie nazwy głównej, przy użyciu witryny Azure portal usługi](../../active-directory/develop/howto-create-service-principal-portal.md) i [uwierzytelniania jednostki usługi przy użyciu usługi Azure Resource Manager.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="common-errors-when-working-with-runbooks"></a>Typowe błędy podczas pracy z elementami runbook

### <a name="task-was-cancelled"></a>Scenariusz: Element runbook nie powiodło się z powodu błędu: zadanie zostało anulowane

#### <a name="issue"></a>Problem

Element runbook nie powiedzie się z powodu błędu podobnego do poniższego przykładu:

```
Exception: A task was canceled.
```

#### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przy użyciu nieaktualnych moduły platformy Azure.

#### <a name="resolution"></a>Rozwiązanie

Ten błąd można rozwiązać, aktualizując swoje moduły platformy Azure do najnowszej wersji.

Na koncie usługi Automation kliknij **modułów**i kliknij przycisk **modułów Azure aktualizacji**. Aktualizacja trwa około 15 minut, po pełną ponownie uruchomić element runbook, który został kończy się niepowodzeniem. Aby dowiedzieć się więcej o aktualizowaniu moduły, zobacz [modułów Azure aktualizacji w usłudze Azure Automation](../automation-update-azure-modules.md).

### <a name="child-runbook-auth-failure"></a>Scenariusz: Podrzędny element runbook nie powiodło się podczas pracy z wieloma subskrypcjami

#### <a name="issue"></a>Problem

Podczas wykonywania podrzędne elementy runbook za pomocą `Start-AzureRmRunbook`, podrzędny element runbook nie może zarządzać zasobami platformy Azure.

#### <a name="cause"></a>Przyczyna

Podrzędny element runbook nie używa poprawny kontekst podczas uruchamiania.

#### <a name="resolution"></a>Rozwiązanie

Podczas pracy z wieloma subskrypcjami kontekstu subskrypcji mogą zostać utracone podczas wywoływania podrzędnych elementów runbook. Aby upewnić się, że kontekstu subskrypcji jest przekazywany do podrzędnych elementów runbook, należy dodać `AzureRmContext` parametru do polecenia cmdlet i przekazać kontekst do niego.

```azurepowershell-interactive
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

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przez następujących przyczyn:

1. Moduł zawierający polecenie cmdlet nie jest zaimportowany do konta usługi automation
2. Moduł zawierający polecenie cmdlet jest zaimportowane, ale jest nieaktualna

#### <a name="resolution"></a>Rozwiązanie

Ten błąd można rozwiązać, wykonując jedną z następujących czynności:

Jeśli moduł jest moduł platformy Azure, zobacz [jak aktualizowanie modułów programu Azure PowerShell w usłudze Azure Automation](../automation-update-azure-modules.md) dowiesz się, jak zaktualizować moduły na koncie usługi automation.

Jeśli jest oddzielny moduł, upewnij się, że moduł w zaimportowane na koncie usługi Automation.

### <a name="job-attempted-3-times"></a>Scenariusz: Podjęto próbę rozpoczęcia zadania elementu runbook trzy razy, ale nie można uruchomić w każdym

#### <a name="issue"></a>Problem

Element runbook nie powiodło się z powodu błędu:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przez następujących przyczyn:

1. Limit pamięci. Istnieją limity udokumentowanego ilość pamięci jest przydzielony do piaskownicy [limity usługi Automation](../../azure-subscription-service-limits.md#automation-limits) dlatego zadanie może zakończyć się niepowodzeniem go, jeśli jest używany więcej niż 400 MB pamięci.

1. Gniazd sieciowych. Piaskownice platformy Azure są ograniczone do 1000 równoczesnych sieci gniazda, zgodnie z opisem w [limity usługi Automation](../../azure-subscription-service-limits.md#automation-limits).

1. Niezgodne modułu. Ten błąd może wystąpić, jeśli moduł zależności nie są poprawne, a nie są one, element runbook zwykle zwraca "nie znaleziono polecenia" lub "Nie można powiązać parametr" wiadomości.

#### <a name="resolution"></a>Rozwiązanie

Dowolne z poniższych rozwiązań rozwiązać ten problem:

* Sugerowane metody do pracy w ramach limitu pamięci są podziału obciążenia między wiele elementów runbook, nie chcesz przetworzyć tak dużej ilości danych w pamięci, aby nie zapisywać niepotrzebne dane wyjściowe z elementami runbook lub należy wziąć pod uwagę liczbę punktów kontrolnych zapis do Twojego przepływu pracy programu PowerShell elementy runbook. Clear — metoda, można użyć takich jak `$myVar.clear()` aby umożliwić wyczyszczenie zmiennej i użycia `[GC]::Collect()` przeprowadzić wyrzucanie elementów bezużytecznych natychmiast, zmniejsza to ilość pamięci zajmowaną przez element runbook podczas wykonywania.

* Aktualizuj moduły platformy Azure przez wykonanie kroków [jak aktualizowanie modułów programu Azure PowerShell w usłudze Azure Automation](../automation-update-azure-modules.md).  

* Innym rozwiązaniem jest uruchomienie elementu runbook na [hybrydowego procesu roboczego Runbook](../automation-hrw-run-runbooks.md). Hybrydowe procesy robocze nie są ograniczone przez limity pamięci i sieci, które są piaskownic platformy Azure.

### <a name="fails-deserialized-object"></a>Scenariusz: Element Runbook nie powiodło się z powodu zdeserializowany obiekt

#### <a name="issue"></a>Problem

Element runbook nie powiodło się z powodu błędu:

```
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

### <a name="quota-exceeded"></a>Scenariusz: Zadanie elementu Runbook nie powiodło się, ponieważ przekroczono limit przydziału

#### <a name="issue"></a>Problem

Zadanie elementu runbook zakończy się niepowodzeniem z powodu błędu:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas wykonywania zadania przekracza 500 minut bezpłatny limit przydziału dla konta usługi. Ten limit przydziału ma zastosowanie do wszystkich typów wykonywania zadań, takich jak testowanie zadania uruchamiania zadania z poziomu portalu, wykonując zadania przy użyciu elementów webhook i planowania zadania do wykonania za pomocą witryny Azure portal lub w centrum danych. Aby dowiedzieć się więcej o cenach usługi Automation, zobacz [cennik usługi Automation](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Rozwiązanie

Jeśli chcesz użyć więcej niż 500 minut w ramach przetwarzania na miesiąc, musisz zmienić subskrypcję bezpłatną warstwę do warstwy podstawowa. Możesz uaktualnić do warstwy podstawowa, wykonując następujące czynności:  

1. Zaloguj się do Twojej subskrypcji platformy Azure.  
2. Wybierz konto usługi Automation, które chcesz zmienić.  
3. Kliknij pozycję **ustawienia** > **ceny**.
4. Kliknij przycisk **Włącz** na dole strony, aby uaktualnić konto do **podstawowe** warstwy.

### <a name="cmdlet-not-recognized"></a>Scenariusz: Polecenie Cmdlet nie został rozpoznany podczas wykonywania elementu runbook

#### <a name="issue"></a>Problem

Zadanie elementu runbook zakończy się niepowodzeniem z powodu błędu:

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany aparatu programu PowerShell nie można znaleźć polecenia cmdlet, którego używasz w elemencie runbook. Może to być, ponieważ moduł zawierający polecenie cmdlet nie ma konta, występuje konflikt nazw z nazwą elementu runbook lub polecenie cmdlet występuje także w innym module i automatyzacji nie może rozpoznać nazwy.

#### <a name="resolution"></a>Rozwiązanie

Dowolne z poniższych rozwiązań rozwiązać ten problem:  

* Sprawdź, czy nazwa polecenia cmdlet został wprowadzony poprawnie.  
* Upewnij się, że polecenia cmdlet istnieje na koncie usługi Automation i że nie istnieją żadne konflikty. Aby sprawdzić, czy polecenie cmdlet jest obecny, otwórz element runbook w trybie edycji i wyszukaj ma zostać znaleziona w bibliotece lub uruchom polecenie cmdlet `Get-Command <CommandName>`. Po zweryfikowaniu, polecenie cmdlet jest dostępny dla konta, które i że nie są nazwa powoduje konflikt z innych poleceń cmdlet lub elementy runbook, należy dodać go do obszaru roboczego i upewnij się, że używasz prawidłowego parametru w elemencie runbook.  
* Jeśli występuje konflikt nazw i polecenia cmdlet jest dostępna w dwóch różnych modułach, możesz rozwiązać ten problem za pomocą w pełni kwalifikowana nazwa polecenia cmdlet. Na przykład, można użyć **ModuleName\CmdletName**.  
* Jeśli użytkownik jest wykonywanie grupy hybrydowych procesów roboczych elementu runbook w środowisku lokalnym, a następnie upewnij się, że moduł i polecenia cmdlet zostanie zainstalowana na komputerze, który jest hostem hybrydowy proces roboczy.

### <a name="long-running-runbook"></a>Scenariusz: Długotrwałe elementu runbook nie powiedzie się

#### <a name="issue"></a>Problem

Element runbook zawiera w **zatrzymane** stan po uruchomieniu przez 3 godziny. Również może zostać wyświetlony błąd:

```
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

To zachowanie jest celowe w piaskownicach platformy Azure z powodu "Udział" Monitorowanie procesów w usłudze Azure Automation, która automatycznie zatrzymuje dłuższy niż trzy godziny wykonywania elementu runbook. Stan elementu runbook, który przechodzi upłynął limit czasu udziału uczciwego różni się typ elementu runbook. Elementy runbook programu PowerShell i Python są ustawione na **zatrzymane** stanu. Elementy runbook przepływu pracy programu PowerShell są ustawione na **niepowodzenie**.

#### <a name="cause"></a>Przyczyna

Element runbook został uruchomiony za pośrednictwem limit 3 godziny, dozwolone przez udział w piaskownicy usługi Azure.

#### <a name="resolution"></a>Rozwiązanie

Jeden zalecanych rozwiązań jest uruchomienie elementu runbook na [hybrydowego procesu roboczego Runbook](../automation-hrw-run-runbooks.md).

Hybrydowe procesy robocze nie są ograniczone przez [udział](../automation-runbook-execution.md#fair-share) limit elementu runbook 3 godziny, będące w piaskownicach platformy Azure. Podczas gdy hybrydowych procesów roboczych Runbook nie są ograniczone przez limit udział 3 godziny, elementy runbook został uruchomiony na nadal należy opracować hybrydowych procesów roboczych Runbook do obsługi zachowania ponownego uruchamiania w przypadku problemów nieoczekiwany infrastruktury lokalnej.

Innym rozwiązaniem jest optymalizacja elementu runbook, tworząc [podrzędnych elementów runbook](../automation-child-runbooks.md). Jeśli element runbook działa w pętli za pomocą tej samej funkcji liczby zasobów, takich jak operacji bazy danych w wielu bazach danych, można przenieść tej funkcji do podrzędnego elementu runbook. Każdy z tych podrzędnych elementów runbook będzie wykonywany równolegle w osobnych procesach, co spowoduje skrócenie całkowitego czasu potrzebnego nadrzędnemu elementowi runbook do zakończenia zadania.

Dostępne są następujące polecenia cmdlet programu PowerShell, które umożliwiają scenariusza podrzędnego elementu runbook:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) — to polecenie cmdlet pozwala na uruchamianie elementu runbook i parametry są przekazywane do elementu runbook

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) — to polecenie cmdlet pozwala sprawdzić stan zadania dla każdego elementu podrzędnego, w przypadku operacji, które należy wykonać po zakończeniu działania podrzędnego elementu runbook.

## <a name="common-errors-when-importing-modules"></a>Typowymi błędami podczas importowania modułów

### <a name="module-fails-to-import"></a>Scenariusz: Moduł nie powiedzie się zaimportować lub poleceń cmdlet, nie można wykonać po zaimportowaniu

#### <a name="issue"></a>Problem

Nie powiedzie się zaimportować moduł, lub pomyślnie importowany, ale są wyodrębniane nie poleceń cmdlet.

#### <a name="cause"></a>Przyczyna

Niektóre typowe przyczyny, które moduł nie może pomyślnie zaimportować do usługi Azure Automation są:

* Struktura nie jest zgodna strukturę, która automatyzacji musi się on w.
* Moduł jest zależny od innego modułu, który nie został wdrożony na Twoim koncie usługi Automation.
* Moduł brakuje jego zależności w folderze.
* `New-AzureRmAutomationModule` Polecenia cmdlet jest używane do przekazywania modułu ani have't podanej ścieżki przechowywania pełnej lub nie został załadowany moduł przy użyciu publicznie dostępnego adresu URL.

#### <a name="resolution"></a>Rozwiązanie

Dowolne z poniższych rozwiązań rozwiązać ten problem:

* Upewnij się, że moduł następuje w następującym formacie: ModuleName.Zip **->** ModuleName lub numer wersji **->** (ModuleName.psm1, ModuleName.psd1)
* Otwórz plik psd1 i sprawdzić, czy moduł wszelkie zależności. Jeśli tak jest, należy przekazać te moduły, do konta usługi Automation.
* Upewnij się, że wszystkie odwołania dll debuggle znajdują się w folderze modułu.

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie był widoczny problemu lub są w stanie rozwiązać problemu, odwiedź jedną z następujących kanałów obsługi więcej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.