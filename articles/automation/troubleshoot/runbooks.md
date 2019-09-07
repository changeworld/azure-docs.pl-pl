---
title: Rozwiązywanie problemów z Azure Automation elementami Runbook
description: Dowiedz się, jak rozwiązywać problemy z Azure Automation elementami Runbook
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 326a2dbf644cd5792a45dbf40d9574982053aae1
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743910"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Rozwiązywanie problemów z elementami Runbook

Jeśli występują błędy wykonywania elementów Runbook w Azure Automation, można wykonać następujące czynności, aby pomóc zdiagnozować problem.

1. **Upewnij się, że skrypt elementu Runbook został pomyślnie wykonany na komputerze lokalnym:**  Zapoznaj się z dokumentacją dla modułów dokumentacji [programu PowerShell](/powershell/scripting/overview) lub języka [Python](https://docs.python.org/3/) .

   Wykonywanie skryptu lokalnie może wykrywać i rozwiązywać typowe błędy, takie jak:

   - **Brakujące moduły**
   - **Błędy składniowe**
   - **Błędy logiki**

2. **Zbadaj element Runbook** [strumienie błędów](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output) dla konkretnych komunikatów i porównaj je z poniższymi błędami.

3. **Upewnij się, że węzły i obszar roboczy usługi Automation mają wymagane moduły:** Jeśli element Runbook importuje dowolne moduły, upewnij się, że są one dostępne na koncie usługi Automation, korzystając z procedury wymienionej w temacie [Import modules](../shared-resources/modules.md#import-modules). Zaktualizuj moduły do najnowszej wersji, postępując zgodnie z instrukcjami w sekcji [aktualizowanie modułów platformy Azure w Azure Automation](..//automation-update-azure-modules.md). Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, zobacz [Rozwiązywanie problemów modułów](shared-resources.md#modules).

Jeśli element Runbook został zawieszony lub nieoczekiwanie zakończył się niepowodzeniem:

* [Sprawdź stan zadania](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) określa Stany elementów Runbook i niektóre możliwe przyczyny.
* [Dodaj dodatkowe dane wyjściowe](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) do elementu Runbook, aby określić, co się dzieje przed wstrzymaniem elementu Runbook.
* [Obsługa wszelkich wyjątków](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) zgłaszanych przez zadanie.

## <a name="login-azurerm"></a>Scenariusz Uruchom login-AzureRMAccount, aby zalogować się

### <a name="issue"></a>Problem

Podczas wykonywania elementu Runbook pojawia się następujący błąd:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić, gdy nie używasz konta Uruchom jako lub konto to wygasło. Zobacz [Zarządzanie kontami funkcji RunAs Azure Automation](https://docs.microsoft.com/azure/automation/manage-runas-account).

Ten błąd ma dwie przyczyny główne:

* Różne wersje modułów AzureRM.
* Próbujesz uzyskać dostęp do zasobów w oddzielnej subskrypcji.

### <a name="resolution"></a>Rozwiązanie

Jeśli ten błąd wystąpi po zaktualizowaniu jednego modułu AzureRM, należy zaktualizować wszystkie moduły AzureRM do tej samej wersji.

Jeśli próbujesz uzyskać dostęp do zasobów w innej subskrypcji, możesz wykonać poniższe kroki, aby skonfigurować uprawnienia.

1. Przejdź do konta Uruchom jako konta usługi Automation i skopiuj identyfikator aplikacji i odcisk palca.
  ![Kopiowanie identyfikatora aplikacji i odcisku palca](../media/troubleshoot-runbooks/collect-app-id.png)
1. Przejdź do Access Control subskrypcji, w której konto usługi Automation nie jest hostowane, a następnie Dodaj nowe przypisanie roli.
  ![Kontrola dostępu](../media/troubleshoot-runbooks/access-control.png)
1. Dodaj identyfikator aplikacji, który został zebrany w poprzednim kroku. Wybierz pozycję uprawnienia współautora.
   ![Dodaj przypisanie roli](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Skopiuj nazwę subskrypcji dla kolejnego kroku.
1. Teraz można użyć poniższego kodu elementu Runbook do przetestowania uprawnień z konta usługi Automation do innej subskrypcji.

    Zastąp ciąg\<"\>CertificateThumbprint" wartością skopiowaną w kroku #1\>i wartość "\<subscriptionname" skopiowaną w kroku #4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="unable-to-find-subscription"></a>Scenariusz Nie można znaleźć subskrypcji platformy Azure

### <a name="issue"></a>Problem

Podczas pracy z `Select-AzureSubscription` poleceniami cmdlet lub `Select-AzureRmSubscription` jest wyświetlany następujący błąd:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Błąd

Ten błąd może wystąpić, jeśli:

* Nazwa subskrypcji jest nieprawidłowa

* Użytkownik Azure Active Directory, który próbuje uzyskać szczegóły subskrypcji, nie jest skonfigurowany jako administrator subskrypcji.

### <a name="resolution"></a>Rozwiązanie

Wykonaj następujące kroki, aby określić, czy masz uwierzytelnienie na platformie Azure i czy masz dostęp do subskrypcji, którą próbujesz wybrać:

1. Aby upewnić się, że działa autonomicznie, przetestuj skrypt poza Azure Automation.
2. `Add-AzureAccount` Przed`Select-AzureSubscription` uruchomieniem polecenia cmdlet upewnij się, że zostało uruchomione polecenie cmdlet.
3. Dodaj `Disable-AzureRmContextAutosave –Scope Process` do początku elementu Runbook. To polecenie cmdlet zapewnia, że wszystkie poświadczenia mają zastosowanie tylko do wykonywania bieżącego elementu Runbook.
4. Jeśli ten komunikat o błędzie jest nadal wyświetlany, zmodyfikuj swój kod, dodając parametr **AzureRmContext** po `Add-AzureAccount` poleceniu cmdlet, a następnie wykonaj kod.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="auth-failed-mfa"></a>Scenariusz Uwierzytelnianie na platformie Azure nie powiodło się, ponieważ jest włączone uwierzytelnianie wieloskładnikowe

### <a name="issue"></a>Problem

Podczas uwierzytelniania na platformie Azure przy użyciu nazwy użytkownika i hasła platformy Azure zostanie wyświetlony następujący błąd:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Przyczyna

Jeśli masz uwierzytelnianie wieloskładnikowe na koncie platformy Azure, nie możesz używać Azure Active Directory użytkownika do uwierzytelniania na platformie Azure. Zamiast tego należy użyć certyfikatu lub nazwy głównej usługi do uwierzytelniania na platformie Azure.

### <a name="resolution"></a>Rozwiązanie

Aby użyć certyfikatu z poleceniami cmdlet klasycznego modelu wdrażania platformy Azure, zapoznaj się z tematem [Tworzenie i Dodawanie certyfikatu w celu zarządzania usługami platformy Azure.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Aby użyć nazwy głównej usługi z poleceniami cmdlet Azure Resource Manager, zobacz [Tworzenie jednostki usługi przy użyciu Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) i [uwierzytelnianie jednostki usługi za pomocą Azure Resource Manager.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="get-serializationsettings"></a>Scenariusz W strumieniach zadań pojawia się błąd dotyczący metody get_SerializationSettings

### <a name="issue"></a>Problem

Wystąpił błąd w strumieniach zadań dla elementu Runbook z następującym komunikatem:

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

### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany przez użycie poleceń cmdlet AzureRM i AZ w elemencie Runbook. Występuje podczas importowania `Az` przed zaimportowaniem `AzureRM`.

### <a name="resolution"></a>Rozwiązanie

Poleceń cmdlet AZ i AzureRM nie można zaimportować i użyć w tym samym elemencie Runbook, aby dowiedzieć się więcej na temat polecenia AZ support w Azure Automation, zobacz [AZ module Support in Azure Automation](../az-modules.md).

## <a name="task-was-cancelled"></a>Scenariusz Wystąpił błąd elementu Runbook: Zadanie zostało anulowane

### <a name="issue"></a>Problem

Element Runbook kończy się niepowodzeniem z powodu błędu podobnego do poniższego przykładu:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany użyciem nieaktualnych modułów platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Ten błąd można rozwiązać przez zaktualizowanie modułów platformy Azure do najnowszej wersji.

Na koncie usługi Automation kliknij pozycję **moduły**, a następnie kliknij pozycję **Aktualizuj moduły platformy Azure**. Aktualizacja trwa około 15 minut, po zakończeniu ponownie uruchom element Runbook, który zakończył się niepowodzeniem. Aby dowiedzieć się więcej na temat aktualizowania modułów, zobacz temat [aktualizowanie modułów platformy Azure w Azure Automation](../automation-update-azure-modules.md).

## <a name="runbook-auth-failure"></a>Scenariusz Elementy Runbook są niepowodzenie podczas pracy z wieloma subskrypcjami

### <a name="issue"></a>Problem

Podczas wykonywania elementów Runbook element Runbook nie może zarządzać zasobami platformy Azure.

### <a name="cause"></a>Przyczyna

Element Runbook nie używa poprawnego kontekstu podczas uruchamiania.

### <a name="resolution"></a>Rozwiązanie

Podczas pracy z wieloma subskrypcjami kontekst subskrypcji może zostać utracony podczas wywoływania elementów Runbook. Aby upewnić się, że kontekst subskrypcji jest przekazywany do elementów Runbook, Dodaj `AzureRmContext` parametr do polecenia cmdlet i przekaż kontekst do niego. Zaleca się również korzystanie `Disable-AzureRmContextAutosave` z polecenia cmdlet z zakresem **procesu** , aby upewnić się, że używane poświadczenia są używane tylko dla bieżącego elementu Runbook.

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

Aby uzyskać więcej informacji, zobacz [Praca z wieloma subskrypcjami](../automation-runbook-execution.md#working-with-multiple-subscriptions).

## <a name="not-recognized-as-cmdlet"></a>Scenariusz Nie rozpoznano terminu jako nazwy polecenia cmdlet, funkcji, skryptu

### <a name="issue"></a>Problem

Element Runbook kończy się niepowodzeniem z powodu błędu podobnego do poniższego przykładu:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić z jednego z następujących powodów:

* Moduł zawierający polecenie cmdlet nie został zaimportowany do konta usługi Automation
* Moduł zawierający polecenie cmdlet został zaimportowany, ale jest nieaktualny

### <a name="resolution"></a>Rozwiązanie

Ten błąd można rozwiązać, wykonując jedno z następujących zadań:

Jeśli moduł jest modułem platformy Azure, zobacz [jak zaktualizować moduły Azure PowerShell w Azure Automation](../automation-update-azure-modules.md) , aby dowiedzieć się, jak zaktualizować moduły na koncie usługi Automation.

Jeśli jest to oddzielny moduł, upewnij się, że moduł został zaimportowany na konto usługi Automation.

## <a name="job-attempted-3-times"></a>Scenariusz Podjęto próbę uruchomienia zadania elementu Runbook trzy razy, ale jego uruchomienie nie powiodło się

### <a name="issue"></a>Problem

Wystąpił błąd elementu Runbook:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje z powodu jednego z następujących problemów:

* Limit pamięci. Udokumentowane limity ilości pamięci przydzielanej do piaskownicy znajdują się w [granicach usługi Automation](../../azure-subscription-service-limits.md#automation-limits). Zadanie może zakończyć się niepowodzeniem, jeśli używa więcej niż 400 MB pamięci.

* Gniazda sieciowe. Piaskownice platformy Azure są ograniczone do 1000 współbieżnych gniazd sieciowych zgodnie z opisem w [limitach usługi Automation](../../azure-subscription-service-limits.md#automation-limits).

* Moduł jest niezgodny. Ten błąd może wystąpić, jeśli zależności modułu nie są poprawne i jeśli nie, element Runbook zwykle zwróci komunikat "nie można odnaleźć polecenia" lub "nie można powiązać parametru".

* Element Runbook podjął próbę wywołania pliku wykonywalnego lub podprocesu w elemencie Runbook, który działa w piaskownicy platformy Azure. Ten scenariusz nie jest obsługiwany w piaskownicach platformy Azure.

* Element Runbook podjął próbę zapisania zbyt dużej ilości danych wyjątku w strumieniu wyjściowym.

### <a name="resolution"></a>Rozwiązanie

Niektóre z poniższych rozwiązań rozwiązują ten problem:

* Sugerowane metody pracy w ramach limitu pamięci to podział obciążenia między wieloma elementami Runbook, nieprzetwarzające ilości danych w pamięci, nie w celu zapisania niepotrzebnych danych wyjściowych z elementów Runbook lub rozważenie liczby punktów kontrolnych, które można napisać w przepływie pracy programu PowerShell elementów Runbook. Można użyć metody Clear, `$myVar.clear()` na przykład w celu wyczyszczenia zmiennej i użycia `[GC]::Collect()` do natychmiastowego uruchomienia wyrzucania elementów bezużytecznych. Te akcje zmniejszają rozmiar pamięci elementu Runbook w czasie wykonywania.

* Zaktualizuj moduły platformy Azure, wykonując czynności opisane w temacie [jak zaktualizować moduły Azure PowerShell w programie Azure Automation](../automation-update-azure-modules.md).

* Innym rozwiązaniem jest uruchomienie elementu Runbook w [hybrydowym procesie roboczym elementu Runbook](../automation-hrw-run-runbooks.md). Hybrydowe procesy robocze nie są ograniczone przez limity pamięci i sieci, które znajdują się w piaskownicach platformy Azure.

* Jeśli musisz wywołać proces (na przykład exe lub subprocess. Call) w elemencie Runbook, musisz uruchomić element Runbook w [hybrydowym procesie roboczym elementu Runbook](../automation-hrw-run-runbooks.md).

* W strumieniu wyjściowym zadania istnieje limit 1 MB. Upewnij się, że zostały załączone wywołania do pliku wykonywalnego lub podprocesu w bloku try/catch. Jeśli zgłasza wyjątek, napisz komunikat z tego wyjątku do zmiennej automatyzacji. Uniemożliwi to zapisanie go w strumieniu wyjściowym zadania.

## <a name="sign-in-failed"></a>Scenariusz Logowanie do konta platformy Azure nie powiodło się

### <a name="issue"></a>Problem

Podczas pracy z `Add-AzureAccount` poleceniami cmdlet lub `Connect-AzureRmAccount` zostanie wyświetlony jeden z następujących błędów:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy nazwa zasobu poświadczenia jest nieprawidłowa. Ten błąd może również wystąpić, jeśli nazwa użytkownika i hasło użyte do skonfigurowania zasobu poświadczenia usługi Automation są nieprawidłowe.

### <a name="resolution"></a>Rozwiązanie

Aby określić, co się stało, wykonaj następujące czynności:

1. Upewnij się, że nie masz żadnych znaków specjalnych. Te znaki zawierają **\@** znak w nazwie zasobu poświadczenia usługi Automation używany do nawiązywania połączenia z platformą Azure.
2. Sprawdź, czy można użyć nazwy użytkownika i hasła, które są przechowywane w Azure Automation poświadczenia w lokalnym edytorze ISE programu PowerShell. Aby sprawdzić, czy nazwa użytkownika i hasło są poprawne, należy uruchomić następujące polecenia cmdlet w programie PowerShell ISE:

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Jeśli uwierzytelnienie nie powiedzie się lokalnie, oznacza to, że poświadczenia Azure Active Directory nie zostały prawidłowo skonfigurowane. Zapoznaj się z artykułem [uwierzytelnianie na platformie Azure przy użyciu](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) wpisu w blogu Azure Active Directory, aby pomyślnie skonfigurować konto Azure Active Directory.

4. Jeśli jest to błąd przejściowy, spróbuj dodać logikę ponowień do procedury uwierzytelniania, aby zapewnić bardziej niezawodne uwierzytelnianie.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="child-runbook-object"></a>Odwołanie do obiektu nie jest ustawione na wystąpienie obiektu

### <a name="issue"></a>Problem

Wystąpi następujący błąd podczas wywoływania podrzędnego elementu Runbook z `-Wait` przełącznikiem, a strumień wyjściowy zawiera obiekt:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Przyczyna

Występuje znany problem, w którym element [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) nie obsługuje prawidłowo strumienia wyjściowego, jeśli zawiera on obiekty.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, zaleca się zaimplementowanie logiki sondowania i użycie polecenia cmdlet [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) w celu pobrania danych wyjściowych. Przykład tej logiki jest zdefiniowany w poniższym przykładzie.

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

## <a name="fails-deserialized-object"></a>Scenariusz Element Runbook kończy się niepowodzeniem z powodu deserializacji obiektu

### <a name="issue"></a>Problem

Wystąpił błąd elementu Runbook:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Przyczyna

Jeśli element Runbook jest przepływem pracy programu PowerShell, przechowuje złożone obiekty w deserializowanym formacie, aby zachować stan elementu Runbook, jeśli przepływ pracy zostanie zawieszony.

### <a name="resolution"></a>Rozwiązanie

Niektóre z następujących trzech rozwiązań rozwiązują ten problem:

* Jeśli tworzysz złożone obiekty z jednego polecenia cmdlet do innego, zawiń te polecenia cmdlet w InlineScript.
* Przekaż nazwę lub wartość, która jest potrzebna z obiektu złożonego, zamiast przekazywać cały obiekt.
* Użyj elementu Runbook programu PowerShell zamiast elementu Runbook przepływu pracy programu PowerShell.

## <a name="quota-exceeded"></a>Scenariusz Zadanie elementu Runbook nie powiodło się, ponieważ przekroczono przydział przydziału

### <a name="issue"></a>Problem

Zadanie elementu Runbook kończy się niepowodzeniem z powodu błędu:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy wykonanie zadania przekracza 500-minutowy bezpłatny limit przydziału dla Twojego konta. Ten limit przydziału dotyczy wszystkich typów zadań wykonywania zadań. Niektóre z tych zadań mogą służyć do testowania zadania, uruchamiania zadania z portalu, wykonywania zadania przy użyciu elementów webhook lub planowania zadania do wykonania przy użyciu Azure Portal lub w centrum danych. Aby dowiedzieć się więcej o cenach usługi Automation, zobacz [Cennik usługi Automation](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Rozwiązanie

Jeśli chcesz użyć więcej niż 500 minut przetwarzania miesięcznie, musisz zmienić subskrypcję z warstwy Bezpłatna na warstwę podstawowa. Możesz przeprowadzić uaktualnienie do warstwy Podstawowa, wykonując następujące czynności:

1. Zaloguj się do Twojej subskrypcji platformy Azure.
2. Wybierz konto usługi Automation, które chcesz zmienić.
3. Kliknij pozycję **Ustawienia** > **Cennik**.
4. Kliknij pozycję **Włącz** na stronie u dołu, aby uaktualnić konto do warstwy **podstawowa** .

## <a name="cmdlet-not-recognized"></a>Scenariusz Nie rozpoznano polecenia cmdlet podczas wykonywania elementu Runbook

### <a name="issue"></a>Problem

Zadanie elementu Runbook kończy się niepowodzeniem z powodu błędu:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany tym, że aparat programu PowerShell nie może znaleźć polecenia cmdlet używanego w elemencie Runbook. Ten błąd może być spowodowany brakiem modułu zawierającego polecenie cmdlet w ramach konta, wystąpił konflikt nazw z nazwą elementu Runbook lub polecenie cmdlet istnieje również w innym module, a Automatyzacja nie może rozpoznać nazwy.

### <a name="resolution"></a>Rozwiązanie

Niektóre z poniższych rozwiązań rozwiązują ten problem:

* Sprawdź, czy nazwa polecenia cmdlet została wpisana poprawnie.
* Upewnij się, że polecenie cmdlet istnieje na koncie usługi Automation i że nie występują żadne konflikty. Aby sprawdzić, czy polecenie cmdlet jest obecne, Otwórz element Runbook w trybie edycji i Wyszukaj polecenie cmdlet, które chcesz znaleźć w bibliotece lub uruchom `Get-Command <CommandName>`. Po sprawdzeniu, czy polecenie cmdlet jest dostępne dla konta i że nie występują konflikty nazw z innymi poleceniami cmdlet lub elementami Runbook, Dodaj je do kanwy i upewnij się, że używasz prawidłowego zestawu parametrów w elemencie Runbook.
* Jeśli wystąpi konflikt nazw, a polecenie cmdlet jest dostępne w dwóch różnych modułach, można rozwiązać ten problem, używając w pełni kwalifikowanej nazwy polecenia cmdlet. Na przykład można użyć **ModuleName\CmdletName**.
* Jeśli element Runbook jest wykonywany lokalnie w grupie hybrydowych procesów roboczych, upewnij się, że moduł i polecenie cmdlet są zainstalowane na komputerze, który hostuje hybrydowy proces roboczy.

## <a name="long-running-runbook"></a>Scenariusz Wykonywanie długotrwałego elementu Runbook kończy się niepowodzeniem

### <a name="issue"></a>Problem

Element Runbook jest wyświetlany w stanie **zatrzymanym** po uruchomieniu przez 3 godziny. Może również zostać wyświetlony komunikat o błędzie:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

To zachowanie jest zaprojektowana w piaskownicy platformy Azure z powodu "uczciwego udziału" monitorowania procesów w ramach Azure Automation. Jeśli jest wykonywana dłużej niż trzy godziny, prawidłowy udział automatycznie zatrzyma element Runbook. Stan elementu Runbook, który jest wcześniejszy niż limit czasu dla udziału, różni się od typu elementu Runbook. Elementy Runbook programu PowerShell i języka Python mają ustawiony stan **zatrzymany** . Elementy Runbook przepływu pracy programu PowerShell są ustawione na wartość **Niepowodzenie**.

### <a name="cause"></a>Przyczyna

Element Runbook działał ponad 3-godzinny limit dozwolony przez sprawiedliwy udział w piaskownicy platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Jednym z zalecanych rozwiązań jest uruchomienie elementu Runbook w [hybrydowym procesie roboczym elementu Runbook](../automation-hrw-run-runbooks.md).

Hybrydowe procesy robocze nie są ograniczone przez zakres elementów Runbook o [atrakcyjnym udziale](../automation-runbook-execution.md#fair-share) 3 godziny, który są piaskownicy platformy Azure. Elementy Runbook uruchomione w hybrydowych procesach roboczych elementu Runbook powinny zostać opracowane w celu obsługi zachowań ponownego uruchamiania w przypadku wystąpienia nieoczekiwanych problemów z infrastrukturą lokalną.

Kolejną opcją jest optymalizacja elementu Runbook przez utworzenie [podrzędnych elementów Runbook](../automation-child-runbooks.md). Jeśli element Runbook pętle za pomocą tej samej funkcji w kilku zasobach, takich jak operacja bazy danych na kilku bazach danych, można przenieść tę funkcję do podrzędnego elementu Runbook. Każdy z tych podrzędnych elementów Runbook jest wykonywany równolegle w oddzielnych procesach. To zachowanie zmniejsza łączny czas pracy nadrzędnego elementu Runbook.

Polecenia cmdlet programu PowerShell, które umożliwiają korzystanie z podrzędnego scenariusza elementu Runbook, to:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) — to polecenie cmdlet umożliwia uruchomienie elementu Runbook i przekazywanie parametrów do elementu Runbook

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) — Jeśli istnieją operacje, które należy wykonać po zakończeniu podrzędnego elementu Runbook, to polecenie cmdlet umożliwia sprawdzenie stanu zadania dla każdego elementu podrzędnego.

## <a name="expired webhook"></a>Scenariusz Stany 400 Nieprawidłowe żądanie podczas wywoływania elementu webhook

### <a name="issue"></a>Problem

Podczas próby wywołania elementu webhook dla Azure Automation elementu Runbook zostanie wyświetlony następujący błąd:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Przyczyna

Element webhook, który próbujesz wywołać, jest wyłączony lub wygasł.

### <a name="resolution"></a>Rozwiązanie

Jeśli element webhook jest wyłączony, można ponownie włączyć element webhook za pomocą Azure Portal. po wygaśnięciu elementu webhook należy go usunąć i utworzyć ponownie. Możesz [odnowić element webhook](../automation-webhooks.md#renew-webhook) , jeśli jeszcze go nie wygasł.

## <a name="429"></a>Scenariusz 429: Współczynnik żądań jest obecnie zbyt duży. Spróbuj ponownie

### <a name="issue"></a>Problem

Podczas uruchamiania `Get-AzureRmAutomationJobOutput` polecenia cmdlet zostanie wyświetlony następujący komunikat o błędzie:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić podczas pobierania danych wyjściowych zadania z elementu Runbook, który ma wiele [strumieni pełnych](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Rozwiązanie

Istnieją dwa sposoby rozwiązania tego błędu:

* Edytuj element Runbook i zmniejsz liczbę strumieni zadań, które emituje.
* Zmniejsz liczbę strumieni do pobrania podczas uruchamiania polecenia cmdlet. Aby skorzystać z tego zachowania, można określić `-Stream Output` parametr `Get-AzureRmAutomationJobOutput` polecenia cmdlet, aby pobrać tylko strumienie wyjściowe. 

## <a name="cannot-invoke-method"></a>Scenariusz Zadanie programu PowerShell nie powiodło się z powodu błędu: Nie można wywołać metody

### <a name="issue"></a>Problem

Podczas uruchamiania zadania programu PowerShell w elemencie Runbook działającym na platformie Azure zostanie wyświetlony następujący komunikat o błędzie:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Przyczyna

Ten błąd może wystąpić po rozpoczęciu zadania programu PowerShell w elemencie Runbook uruchomionym na platformie Azure. Takie zachowanie może wystąpić, ponieważ elementy Runbook uruchomione w piaskownicy platformy Azure mogą nie działać w [trybie pełnego języka](/powershell/module/microsoft.powershell.core/about/about_language_modes)).

### <a name="resolution"></a>Rozwiązanie

Istnieją dwa sposoby rozwiązania tego błędu:

* Zamiast korzystać z programu `Start-AzureRmAutomationRunbook` ,użyjpolecenia,abyuruchomićelementRunbook`Start-Job`
* Jeśli element Runbook ma ten komunikat o błędzie, uruchom go na hybrydowym procesie roboczym elementu Runbook

Aby dowiedzieć się więcej na temat tego zachowania i innych zachowań Azure Automation elementów Runbook, zobacz [zachowanie elementu Runbook](../automation-runbook-execution.md#runbook-behavior).

## <a name="other"></a>Mój problem nie jest wymieniony powyżej

W poniższych sekcjach wymieniono inne typowe błędy oprócz dokumentacji pomocniczej, które ułatwiają rozwiązanie problemu.

## <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Hybrydowy proces roboczy runbook nie uruchamia zadań lub nie odpowiada

W przypadku uruchamiania zadań przy użyciu hybrydowego procesu roboczego, a nie w Azure Automation, może być konieczne [rozwiązanie tego samego procesu](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

## <a name="runbook-fails-with-no-permission-or-some-variation"></a>Działanie elementu runbook kończy się błędem „Brak uprawnienia” lub jego odmianą

Konta Uruchom jako nie mogą mieć tych samych uprawnień do zasobów platformy Azure co bieżące konto. Upewnij się, że Twoje konto Uruchom jako [ma uprawnienia dostępu do wszystkich zasobów](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) używanych w skrypcie.

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Elementy runbook działały, ale nagle przestały

* Jeśli elementy Runbook były wcześniej wykonywane, ale zatrzymane, [upewnij się, że konto Uruchom jako nie wygasło](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Jeśli używasz elementów webhook do uruchamiania elementów runbook, [upewnij się, że odpowiedni element webhook nie wygasł](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

## <a name="issues-passing-parameters-into-webhooks"></a>Problemy z przekazywaniem parametrów do elementów webhook

Aby uzyskać pomoc w przekazywaniu parametrów do elementów webhook, zobacz [Uruchamianie elementu Runbook z elementu webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

## <a name="issues-using-az-modules"></a>Problemy przy użyciu polecenia AZ modules

Używanie modułów Az i modułów AzureRM w ramach tego samego konta usługi Automation nie jest obsługiwane. Aby uzyskać więcej informacji, zobacz [AZ modules in Runbook](https://docs.microsoft.com/azure/automation/az-modules) .

## <a name="inconsistent-behavior-in-runbooks"></a>Niespójne zachowanie w elementach runbook

Postępuj zgodnie ze wskazówkami w temacie [Wykonywanie elementu runbook](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior), aby zapobiec problemom dotyczącym zadań współbieżnych, tworzeniu zasobów wiele razy lub innej logiki zależnej od czasu w elementach runbook.

## <a name="runbook-fails-with-the-errors-no-permission-forbidden-403-or-some-variation"></a>Wystąpił błąd elementu Runbook z błędami: Brak uprawnień, zabronionych, 403 lub niektórych odmian

Konta Uruchom jako nie mogą mieć tych samych uprawnień do zasobów platformy Azure co bieżące konto. Upewnij się, że konto Uruchom jako ma [uprawnienia dostępu do wszystkich zasobów](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) używanych w skrypcie.

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Elementy runbook działały, ale nagle przestały

* Jeśli elementy Runbook były wcześniej wykonywane, ale zatrzymane, upewnij się, że konto Uruchom jako [nie wygasło](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Jeśli używasz elementu webhook do uruchamiania elementów Runbook, upewnij się, że element webhook [nie wygasł](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

## <a name="passing-parameters-into-webhooks"></a>Przekazywanie parametrów do elementów webhook

Aby uzyskać pomoc w przekazywaniu parametrów do elementów webhook, zobacz [Uruchamianie elementu Runbook z elementu webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

## <a name="using-az-modules"></a>Korzystanie z modułów Az

Używanie modułów Az i modułów AzureRM w ramach tego samego konta usługi Automation nie jest obsługiwane. Aby uzyskać więcej informacji, zobacz [AZ modules in Runbook](https://docs.microsoft.com/azure/automation/az-modules) .

## <a name="using-self-signed-certificates"></a>Używanie certyfikatów z podpisem własnym

Aby korzystać z certyfikatów z podpisem własnym, należy postępować zgodnie z przewodnikiem po [utworzeniu nowego certyfikatu](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

## <a name="recommended-documents"></a>Zalecane dokumenty

* [Uruchamianie elementu runbook w usłudze Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Wykonywanie elementu runbook w usłudze Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
