---
title: Rozwiązywanie problemów z elementu Runbook usługi Automatyzacja Azure
description: Dowiedz się, jak rozwiązać problemy z elementu runbook usługi Automatyzacja Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: bb340b8439927f191bc4a22f385d85d4e21b1cdb
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063945"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Rozwiązywanie problemów z elementami runbook

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Błędy uwierzytelniania podczas pracy z elementami runbook usługi Automatyzacja Azure

### <a name="sign-in-failed"></a>Scenariusz: Zaloguj się do konta platformy Azure nie powiodło się

#### <a name="issue"></a>Problem

Zostanie wyświetlony następujący błąd podczas pracy z `Add-AzureAccount` lub `Connect-AzureRmAccount` polecenia cmdlet.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, jeśli nazwa zasobu poświadczeń jest nieprawidłowa lub nie są prawidłową nazwę użytkownika i hasło używane do konfigurowania zasób poświadczenia usługi Automatyzacja.

#### <a name="resolution"></a>Rozwiązanie

W celu ustalenia przyczyny problemu, wykonaj następujące czynności:  

1. Upewnij się, że nie masz żadnych znaków specjalnych, w tym **@** znak w nazwie zasobów poświadczenie automatyzacji, używanej do połączenia z platformą Azure.  
2. Sprawdź, czy można użyć nazwy użytkownika i hasła, które są przechowywane w poświadczenia usługi Automatyzacja Azure w Edytorze lokalnych PowerShell ISE. Można to zrobić, uruchamiając następujące polecenia cmdlet w programie PowerShell ISE:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management   
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Jeśli uwierzytelnianie nie powiedzie się lokalnie, oznacza to, że nie skonfigurowano poświadczeń usługi Azure Active Directory poprawnie. Zapoznaj się [uwierzytelnianie na platformie Azure przy użyciu usługi Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) wpis w blogu można uzyskać konta usługi Azure Active Directory są nieprawidłowo skonfigurowane.  

### <a name="unable-to-find-subscription"></a>Scenariusz: Nie można odnaleźć subskrypcji platformy Azure

#### <a name="issue"></a>Problem

Zostanie wyświetlony następujący błąd podczas pracy z `Select-AzureSubscription` lub `Select-AzureRmSubscription` polecenia cmdlet.:

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Błąd

Ten błąd występuje, jeśli nazwa subskrypcji jest nieprawidłowa lub jeśli użytkownik usługi Azure Active Directory, który próbuje uzyskać szczegółów subskrypcji nie jest skonfigurowany jako administrator subskrypcji.

#### <a name="resolution"></a>Rozwiązanie

Aby ustalić, czy poprawnie uwierzytelnionych na platformie Azure i mieć dostępu do subskrypcji, którą chcesz wybrać, wykonaj następujące czynności:  

1. Upewnij się, że uruchamiasz **Add-AzureAccount** przed uruchomieniem **AzureSubscription wybierz** polecenia cmdlet.  
2. Jeśli ten komunikat o błędzie, należy zmodyfikować kod, dodając **Get-AzureSubscription** następujące polecenia cmdlet **Add-AzureAccount** polecenia cmdlet, a następnie wykonaj kod. Teraz Sprawdź, czy dane wyjściowe Get AzureSubscription zawiera szczegółowe informacje dotyczące Twojej subskrypcji.  

   * Jeśli nie widać żadnych szczegółów subskrypcji w danych wyjściowych, oznacza to, subskrypcja nie jest jeszcze zainicjowana.  
   * Jeśli widzisz Szczegóły subskrypcji w danych wyjściowych, potwierdź używasz subskrypcji poprawna nazwa lub identyfikator z **AzureSubscription wybierz** polecenia cmdlet.

### <a name="auth-failed-mfa"></a>Scenariusz: Uwierzytelnianie nie powiodło się, ponieważ jest włączone uwierzytelnianie wieloskładnikowe Azure

#### <a name="issue"></a>Problem

Zostanie wyświetlony następujący błąd podczas uwierzytelniania na platformie Azure z Azure nazwę użytkownika i hasło:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Przyczyna

Jeśli uwierzytelnianie wieloskładnikowe na konto platformy Azure, nie można użyć użytkownika usługi Azure Active Directory do uwierzytelniania na platformie Azure. Zamiast tego należy użyć certyfikatu lub nazwy głównej usługi do uwierzytelniania na platformie Azure.

#### <a name="resolution"></a>Rozwiązanie

Aby korzystać z certyfikatu z poleceń cmdlet programu Azure wdrażania klasycznego modelu, należy zapoznać się [tworzenie i dodawanie certyfikatu do zarządzania usługami Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Aby użyć nazwy głównej usługi za pomocą poleceń cmdlet usługi Azure Resource Manager, zapoznaj się [Tworzenie usługi głównej, przy użyciu portalu Azure](../../azure-resource-manager/resource-group-create-service-principal-portal.md) i [uwierzytelniania nazwy głównej usługi z usługą Azure Resource Manager.](../../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>Typowe błędy podczas pracy z elementami runbook

### <a name="job-attempted-3-times"></a>Scenariusz: Podjęto próbę uruchomienia zadania elementu runbook trzy razy, ale nie została uruchomiona zawsze

#### <a name="issue"></a>Problem

Element runbook nie powiodło się z powodu błędu:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przez następujących powodów:

1. Limit pamięci. Są udokumentowane ograniczenia na ilość pamięci przydzielona do piaskownicy [ograniczenia usługi automatyzacja](../../azure-subscription-service-limits.md#automation-limits) tak zadania może się nie powieść go, gdy używa ponad 400 MB pamięci.

2. Niezgodny moduł. Taka sytuacja może wystąpić, jeśli moduł zależności nie są prawidłowe, a jeśli nie, element runbook zwykle zwraca wartość "Nie można odnaleźć polecenia" lub "Nie można powiązać parametr" wiadomości.

#### <a name="resolution"></a>Rozwiązanie

Żadnego z następujących rozwiązania problemu:

* Sugerowane metody do pracy w ramach limitu pamięci są podziału obciążenia między wieloma elementami runbook, nie przetwarzają jak najwięcej danych w pamięci, a nie do zapisu niepotrzebne dane wyjściowe z elementy runbook lub należy wziąć pod uwagę liczbę punktów kontrolnych zapisu do przepływu pracy programu PowerShell elementy runbook.  

* Aktualizacja Azure moduły, wykonując kroki opisane [aktualizacji programu Azure PowerShell modułów w automatyzacji Azure](../automation-update-azure-modules.md).  

### <a name="fails-deserialized-object"></a>Scenariusz: Element Runbook nie powiodło się z powodu obiektu po deserializacji

#### <a name="issue"></a>Problem

Element runbook nie powiodło się z powodu błędu:

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Przyczyna

Jeśli element runbook przepływu pracy programu PowerShell, przechowuje złożonych obiektów w formacie zdeserializowany Aby zachować swój stan elementu runbook, jeśli zostanie wstrzymany przepływ pracy.

#### <a name="resolution"></a>Rozwiązanie

Jedną z następujących trzech rozwiązania tego problemu:

1. Jeśli użytkownik są przekazanie w potoku obiektów złożonych z jednego polecenia cmdlet do innego, otoczenie tych poleceń cmdlet w bloku skryptu InlineScript.
2. Przekaż nazw ani wartości, które należy z obiekt złożony zamiast przekazywanie całego obiektu.
3. Użyj elementu runbook programu PowerShell zamiast element runbook przepływu pracy programu PowerShell.

### <a name="quota-exceeded"></a>Scenariusz: Zadanie elementu Runbook nie powiodło się, ponieważ przekroczono przydział przydzielone

#### <a name="issue"></a>Problem

Zadanie elementu runbook nie powiodło się z powodu błędu:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje podczas wykonywania zadania przekracza przydział bezpłatne minuty 500 dla Twojego konta. Ten przydział dotyczy wszystkich typów zadania wykonywania zadań, takich jak testowanie zadania uruchamiania zadania z portalu, wykonywanie zadania za pomocą elementów webhook i planowania zadania do wykonania przy użyciu usługi Azure portalu lub w centrum danych. Aby dowiedzieć się więcej o cenach automatyzacji, zobacz [cennik automatyzacji](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Rozwiązanie

Jeśli chcesz użyć więcej niż 500 minut przetwarzania na miesiąc, musisz zmienić subskrypcję bezpłatna warstwy do warstwy podstawowa. Można uaktualnić do warstwy podstawowa, wykonując następujące czynności:  

1. Zaloguj się do Twojej subskrypcji platformy Azure.  
2. Wybierz konto usługi Automation, które chcesz zmienić.  
3. Polecenie **ustawienia** > **cennik**.
4. Kliknij przycisk **włączyć** na dole strony, aby uaktualnić swoje konto do **podstawowe** warstwy.

### <a name="cmdlet-not-recognized"></a>Scenariusz: Polecenia Cmdlet nierozpoznany podczas wykonywania elementu runbook

#### <a name="issue"></a>Problem

Zadanie elementu runbook nie powiodło się z powodu błędu:

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany przez aparat programu PowerShell nie można znaleźć polecenia cmdlet, którego używasz w elemencie runbook. Może to być spowodowane moduł zawierający polecenie cmdlet nie ma konta, istnieje konflikt nazw z nazwą elementu runbook lub polecenia cmdlet również istnieje w innym module i automatyzacji nie może rozpoznać nazwy.

#### <a name="resolution"></a>Rozwiązanie

Żadnego z następujących rozwiązania problemu:  

* Sprawdź, czy nazwa polecenia cmdlet mają wprowadzony poprawnie.  
* Upewnij się, polecenia cmdlet istnieje na koncie automatyzacji i że nie ma żadnych konfliktów. Aby sprawdzić, czy polecenie cmdlet jest obecne, otwórz element runbook w trybie edycji i wyszukaj ma zostać znaleziona w bibliotece lub uruchom polecenie cmdlet `Get-Command <CommandName>`. Po sprawdzeniu poprawności, które polecenia cmdlet jest dostępny dla konta, oraz czy nie ma konfliktów nazw z innych poleceń cmdlet lub elementy runbook, dodaj go do obszaru roboczego i upewnij się, że używasz prawidłowego parametru w elemencie runbook.  
* Jeśli występuje konflikt nazwy i polecenia cmdlet jest dostępny w dwóch różnych modułach, możesz rozwiązać ten problem przy użyciu w pełni kwalifikowana nazwa polecenia cmdlet. Na przykład można użyć **ModuleName\CmdletName**.  
* Jeśli wykonywanych w grupy hybrydowych procesów roboczych runbook lokalnej, a następnie upewnij się, że moduł/polecenia cmdlet jest zainstalowany na komputerze, który jest hostem hybrydowy proces roboczy.

### <a name="evicted-from-checkpoint"></a>Scenariusz: Długotrwała runbook spójnie zakończy się niepowodzeniem z wyjątkiem: "zadanie nie może kontynuować wykonywania, ponieważ wielokrotnie został wykluczony z tego samego punktu kontrolnego"

#### <a name="issue"></a>Problem

To zachowanie jest celowe z powodu "Odpowiedni udział" Monitorowanie procesów w automatyzacji Azure, która automatycznie wstrzymuje wykonywanie elementu runbook, jeśli wykonywania dłuższy niż 3 godziny. Jednak zwracany komunikat o błędzie nie zapewnia "co dalej" Opcje.

#### <a name="cause"></a>Przyczyna

Element runbook może zostać zawieszone kilku powodów. Wstrzymuje wystąpić, przede wszystkim z powodu błędów. Na przykład nieprzechwycony wyjątek w elemencie runbook, awarii sieci lub awarii w procesie roboczym Runbook uruchamiania elementu runbook wszystkie Przyczyna zawieszone i rozpocząć od ostatniego punktu kontrolnego po wznowieniu elementu runbook.

#### <a name="resolution"></a>Rozwiązanie

Udokumentowane rozwiązania, aby uniknąć tego problemu jest używać punktów kontrolnych w przepływie pracy. Aby dowiedzieć się więcej, zapoznaj się [przepływów pracy programu PowerShell Learning](../automation-powershell-workflow.md#checkpoints). Bardziej szczegółowe wyjaśnienie "Odpowiedni udział" i punkt kontrolny można znaleźć w tym artykule blog [przy użyciu punktów kontrolnych w elementach Runbook](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

## <a name="common-errors-when-importing-modules"></a>Typowe błędy podczas importowania modułów

### <a name="module-fails-to-import"></a>Scenariusz: Moduł nie powiedzie się zaimportować lub poleceń cmdlet nie może zostać wykonana po zaimportowaniu

#### <a name="issue"></a>Problem

Moduł nie powiedzie się zaimportować lub importuje pomyślnie, ale są wyodrębniane żadnych poleceń cmdlet.

#### <a name="cause"></a>Przyczyna

Niektóre typowe przyczyny, które moduł nie może pomyślnie zaimportować do usługi Automatyzacja Azure są:

* Struktura jest niezgodna z strukturze Automatyzacja musi się on w.
* Moduł jest zależny od innego modułu, który nie został wdrożony na koncie automatyzacji.
* Moduł nie ma jego zależności w folderze.
* `New-AzureRmAutomationModule` Polecenia cmdlet jest używany do przekazywania modułu, a nie spowodowały ścieżki pełne magazynu lub nie zostały załadowane moduł za pomocą publicznie dostępny adres URL.

#### <a name="resolution"></a>Rozwiązanie

Żadnego z następujących rozwiązania problemu:

* Upewnij się, że moduł postępuje następujący format: ModuleName.Zip **->** Nazwa_modułu lub numer wersji **->** (ModuleName.psm1, ModuleName.psd1)
* Otwórz plik psd1 i sprawdzić, czy moduł ma zależności. Jeśli tak, Przekaż te moduły do konta automatyzacji.
* Upewnij się, czy wszystkie biblioteki przywoływanego znajdują się w folderze modułu.

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie została uwzględniona problemu lub nie można rozwiązać problemu, odwiedź jedną z następujących kanałów więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy możesz pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać obsługuje**.