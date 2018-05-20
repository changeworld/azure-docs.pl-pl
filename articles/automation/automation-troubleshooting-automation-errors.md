---
title: Rozwiązywanie typowych problemów z usługi Automatyzacja Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów i Rozwiąż typowe błędy usługi Automatyzacja Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
tags: top-support-issue
keywords: Błąd automatyzacji, rozwiązywania problemów, problem
ms.openlocfilehash: 990422cb686c84501127c3fcf233f96aec5bc065
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshooting-common-issues-in-azure-automation"></a>Rozwiązywanie typowych problemów w usłudze Automatyzacja Azure 
Ten artykuł zawiera pomoc w rozwiązywaniu typowych błędów mogą wystąpić w automatyzacji Azure, a także sugeruje możliwe rozwiązania, aby je rozwiązać.

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Błędy uwierzytelniania podczas pracy z elementami runbook usługi Automatyzacja Azure
### <a name="scenario-sign-in-to-azure-account-failed"></a>Scenariusz: Zaloguj się do konta platformy Azure nie powiodło się
**Błąd:** podczas pracy z poleceniami cmdlet Add-AzureAccount lub Connect-AzureRmAccount zostanie wyświetlony błąd "Unknown_user_type: Nieznany typ użytkownika".

**Przyczyną tego błędu:** ten błąd występuje, jeśli nazwa zasobu poświadczeń jest nieprawidłowa lub nie są prawidłową nazwę użytkownika i hasło używane do konfigurowania zasób poświadczenia usługi Automatyzacja.

**Porady dotyczące rozwiązywania problemów:** w celu ustalenia przyczyny problemu, wykonaj następujące czynności:  

1. Upewnij się, że nie masz żadnych znaków specjalnych, w tym **@** znak w nazwie zasobów poświadczenie automatyzacji, używanej do połączenia z platformą Azure.  
2. Sprawdź, czy można użyć nazwy użytkownika i hasła, które są przechowywane w poświadczenia usługi Automatyzacja Azure w Edytorze lokalnych PowerShell ISE. Można to zrobić, uruchamiając następujące polecenia cmdlet w programie PowerShell ISE:  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Connect-AzureRmAccount –Credential $Cred
3. Jeśli uwierzytelnianie nie powiedzie się lokalnie, oznacza to, że nie skonfigurowano poświadczeń usługi Azure Active Directory poprawnie. Zapoznaj się [uwierzytelnianie na platformie Azure przy użyciu usługi Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) wpis w blogu można uzyskać konta usługi Azure Active Directory są nieprawidłowo skonfigurowane.  

### <a name="scenario-unable-to-find-the-azure-subscription"></a>Scenariusz: Nie można odnaleźć subskrypcji platformy Azure
**Błąd:** zostanie wyświetlony błąd "subskrypcję o nazwie ``<subscription name>`` nie można odnaleźć" podczas pracy z poleceniami cmdlet wybierz AzureSubscription lub Select-AzureRmSubscription.

**Przyczyną tego błędu:** ten błąd występuje, jeśli nazwa subskrypcji jest nieprawidłowa lub jeśli użytkownik usługi Azure Active Directory, który próbuje uzyskać szczegółów subskrypcji nie jest skonfigurowany jako administrator subskrypcji.

**Porady dotyczące rozwiązywania problemów:** w celu ustalenia, jeśli poprawnie uwierzytelnionych na platformie Azure i korzystać z subskrypcji chcesz wybrać, wykonaj następujące czynności:  

1. Upewnij się, że uruchamiasz **Add-AzureAccount** przed uruchomieniem **AzureSubscription wybierz** polecenia cmdlet.  
2. Jeśli ten komunikat o błędzie, należy zmodyfikować kod, dodając **Get-AzureSubscription** następujące polecenia cmdlet **Add-AzureAccount** polecenia cmdlet, a następnie wykonaj kod. Teraz Sprawdź, czy dane wyjściowe Get AzureSubscription zawiera szczegółowe informacje dotyczące Twojej subskrypcji.  

   * Jeśli nie widać żadnych szczegółów subskrypcji w danych wyjściowych, oznacza to, subskrypcja nie jest jeszcze zainicjowana.  
   * Jeśli widzisz Szczegóły subskrypcji w danych wyjściowych, potwierdź używasz subskrypcji poprawna nazwa lub identyfikator z **AzureSubscription wybierz** polecenia cmdlet.   

### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>Scenariusz: Uwierzytelnianie nie powiodło się, ponieważ jest włączone uwierzytelnianie wieloskładnikowe Azure
**Błąd:** zostanie wyświetlony błąd "Add-AzureAccount: AADSTS50079: rejestracji silnego uwierzytelniania (dowód do góry) jest wymagana" gdy uwierzytelnianie na platformie Azure z Azure nazwę użytkownika i hasło.

**Przyczyną tego błędu:** Jeśli uwierzytelnianie wieloskładnikowe na konto platformy Azure, nie można użyć użytkownika usługi Azure Active Directory do uwierzytelniania na platformie Azure. Zamiast tego należy użyć certyfikatu lub nazwy głównej usługi do uwierzytelniania na platformie Azure.

**Porady dotyczące rozwiązywania problemów:** do używania certyfikatu za pomocą poleceń cmdlet modelu wdrażania klasycznego Azure, zapoznaj się [tworzenie i dodawanie certyfikatu do zarządzania usługami Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Aby użyć nazwy głównej usługi za pomocą poleceń cmdlet usługi Azure Resource Manager, zapoznaj się [Tworzenie usługi głównej, przy użyciu portalu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md) i [uwierzytelniania nazwy głównej usługi z usługą Azure Resource Manager.](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>Typowe błędy podczas pracy z elementami runbook
### <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-it-failed-to-start-each-time"></a>Scenariusz: Podjęto próbę uruchomienia zadania elementu runbook trzy razy, ale nie została uruchomiona zawsze
**Błąd:** element runbook nie powiodło się z powodu błędu "" zadanie została zainstalowana trzy razy, ale nie powiodło się."

**Przyczyną tego błędu:** ten błąd może być spowodowany przez następujących powodów:  

1. Limit pamięci. Są udokumentowane ograniczenia na ilość pamięci przydzielona do piaskownicy [ograniczenia usługi automatyzacja](../azure-subscription-service-limits.md#automation-limits) tak zadania może się nie powieść go, gdy używa ponad 400 MB pamięci. 

2. Niezgodny moduł. Taka sytuacja może wystąpić, jeśli moduł zależności nie są prawidłowe, a jeśli nie, element runbook zwykle zwraca wartość "Nie można odnaleźć polecenia" lub "Nie można powiązać parametr" wiadomości. 

**Porady dotyczące rozwiązywania problemów:** żadnego z następujących rozwiązania problemu:

* Sugerowane metody do pracy w ramach limitu pamięci są podziału obciążenia między wieloma elementami runbook, nie przetwarzają jak najwięcej danych w pamięci, a nie do zapisu niepotrzebne dane wyjściowe z elementy runbook lub należy wziąć pod uwagę liczbę punktów kontrolnych zapisu w elementach runbook przepływu pracy programu PowerShell.  

* Aktualizacja Azure moduły, wykonując kroki opisane [aktualizacji programu Azure PowerShell modułów w automatyzacji Azure](automation-update-azure-modules.md).  


### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>Scenariusz: Element Runbook nie powiodło się z powodu obiektu po deserializacji
**Błąd:** element runbook nie powiodło się z powodu błędu "nie można powiązać parametr ``<ParameterName>``. Nie można przekonwertować ``<ParameterType>`` wartości typu Deserialized ``<ParameterType>`` na typ ``<ParameterType>``".

**Przyczyną tego błędu:** Jeśli element runbook przepływu pracy programu PowerShell, przechowuje złożonych obiektów w formacie zdeserializowany Aby zachować swój stan elementu runbook, jeśli zostanie wstrzymany przepływ pracy.

**Porady dotyczące rozwiązywania problemów:** żadnego z następujących trzech rozwiązania tego problemu:

1. Jeśli użytkownik są przekazanie w potoku obiektów złożonych z jednego polecenia cmdlet do innego, otoczenie tych poleceń cmdlet w bloku skryptu InlineScript.
2. Przekaż nazw ani wartości, które należy z obiekt złożony zamiast przekazywanie całego obiektu.
3. Użyj elementu runbook programu PowerShell zamiast element runbook przepływu pracy programu PowerShell.

### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>Scenariusz: Zadanie elementu Runbook nie powiodło się, ponieważ przekroczono przydział przydzielone
**Błąd:** zadania elementu runbook nie powiodło się z powodu błędu "limit przydziału miesięcznego łącznego czasu wykonywania zadania został osiągnięty dla tej subskrypcji".

**Przyczyną tego błędu:** ten błąd występuje podczas wykonywania zadania przekracza przydział bezpłatne minuty 500 dla Twojego konta. Ten przydział dotyczy wszystkich typów zadania wykonywania zadań, takich jak testowanie zadania uruchamiania zadania z portalu, wykonywanie zadania za pomocą elementów webhook i planowania zadania do wykonania przy użyciu usługi Azure portalu lub w centrum danych. Aby dowiedzieć się więcej o cenach automatyzacji, zobacz [cennik automatyzacji](https://azure.microsoft.com/pricing/details/automation/).

**Porady dotyczące rozwiązywania problemów:** Jeśli chcesz użyć więcej niż 500 minut przetwarzania na miesiąc, musisz zmienić subskrypcję bezpłatna warstwy do warstwy podstawowa. Można uaktualnić do warstwy podstawowa, wykonując następujące czynności:  

1. Zaloguj się do Twojej subskrypcji platformy Azure.  
2. Wybierz konto usługi Automation, które chcesz zmienić.  
3. Polecenie **ustawienia** > **cennik**.
4. Kliknij przycisk **włączyć** na dole strony, aby uaktualnić swoje konto do **podstawowe** warstwy.

### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>Scenariusz: Polecenia Cmdlet nierozpoznany podczas wykonywania elementu runbook
**Błąd:** zadania elementu runbook nie powiodło się z powodu błędu "``<cmdlet name>``: określenie ``<cmdlet name>`` nie został rozpoznany jako nazwa polecenia cmdlet, funkcji, pliku skryptu lub program wykonywalny."

**Przyczyną tego błędu:** ten błąd występuje, gdy aparat programu PowerShell nie można znaleźć polecenia cmdlet, jest używany w elemencie runbook. Może to być spowodowane moduł zawierający polecenie cmdlet nie ma konta, istnieje konflikt nazw z nazwą elementu runbook lub polecenia cmdlet również istnieje w innym module i automatyzacji nie może rozpoznać nazwy.

**Porady dotyczące rozwiązywania problemów:** żadnego z następujących rozwiązania problemu:  

* Sprawdź, czy nazwa polecenia cmdlet mają wprowadzony poprawnie.  
* Upewnij się, polecenia cmdlet istnieje na koncie automatyzacji i że nie ma żadnych konfliktów. Aby sprawdzić, czy polecenie cmdlet jest obecne, otwórz element runbook w trybie edycji i wyszukaj ma zostać znaleziona w bibliotece lub uruchom polecenie cmdlet **Get-Command ``<CommandName>``** . Po sprawdzeniu poprawności, które polecenia cmdlet jest dostępny dla konta, oraz czy nie ma konfliktów nazw z innych poleceń cmdlet lub elementy runbook, dodaj go do obszaru roboczego i upewnij się, że używasz prawidłowego parametru w elemencie runbook.  
* Jeśli występuje konflikt nazwy i polecenia cmdlet jest dostępny w dwóch różnych modułach, możesz rozwiązać ten problem przy użyciu w pełni kwalifikowana nazwa polecenia cmdlet. Na przykład można użyć **ModuleName\CmdletName**.  
* Jeśli wykonywanych w grupy hybrydowych procesów roboczych runbook lokalnej, a następnie upewnij się, że moduł/polecenia cmdlet jest zainstalowany na komputerze, który jest hostem hybrydowy proces roboczy.

### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>Scenariusz: Długotrwała runbook spójnie zakończy się niepowodzeniem z wyjątkiem: "zadanie nie może kontynuować wykonywania, ponieważ wielokrotnie został wykluczony z tego samego punktu kontrolnego".
**Przyczyną tego błędu:** to zachowanie jest celowe z powodu "Odpowiedni udział" Monitorowanie procesów w automatyzacji Azure, która automatycznie wstrzymuje wykonywanie elementu runbook, jeśli wykonywania dłuższy niż 3 godziny. Jednak zwracany komunikat o błędzie nie zapewnia "co dalej" Opcje. Element runbook może zostać zawieszone kilku powodów. Wstrzymuje wystąpić, przede wszystkim z powodu błędów. Na przykład nieprzechwycony wyjątek w elemencie runbook, awarii sieci lub awarii w procesie roboczym Runbook uruchamiania elementu runbook wszystkie Przyczyna zawieszone i rozpocząć od ostatniego punktu kontrolnego po wznowieniu elementu runbook.

**Porady dotyczące rozwiązywania problemów:** udokumentowane rozwiązania, aby uniknąć tego problemu jest użycie punktów kontrolnych w przepływie pracy. Aby dowiedzieć się więcej, zapoznaj się [przepływów pracy programu PowerShell Learning](automation-powershell-workflow.md#checkpoints). Bardziej szczegółowe wyjaśnienie "Odpowiedni udział" i punkt kontrolny można znaleźć w tym artykule blog [przy użyciu punktów kontrolnych w elementach Runbook](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).

## <a name="common-errors-when-importing-modules"></a>Typowe błędy podczas importowania modułów
### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>Scenariusz: Moduł nie powiedzie się zaimportować lub poleceń cmdlet nie może zostać wykonana po zaimportowaniu
**Błąd:** modułu nie powiedzie się zaimportować lub importuje pomyślnie, ale są wyodrębniane żadnych poleceń cmdlet.

**Przyczyną tego błędu:** są niektóre typowe przyczyny, które moduł nie może pomyślnie zaimportować do usługi Automatyzacja Azure:

* Struktura jest niezgodna z strukturze Automatyzacja musi się on w.
* Moduł jest zależny od innego modułu, który nie został wdrożony na koncie automatyzacji.
* Moduł nie ma jego zależności w folderze.
* **AzureRmAutomationModule nowy** polecenia cmdlet jest używany do przekazywania modułu, a nie spowodowały ścieżki pełne magazynu lub nie zostały załadowane moduł za pomocą publicznie dostępny adres URL.

**Porady dotyczące rozwiązywania problemów:** żadnego z następujących rozwiązania problemu:

* Upewnij się, że moduł postępuje następujący format: ModuleName.Zip **->** Nazwa_modułu lub numer wersji **->** (ModuleName.psm1, ModuleName.psd1)
* Otwórz plik psd1 i sprawdzić, czy moduł ma zależności. Jeśli tak, Przekaż te moduły do konta automatyzacji.
* Upewnij się, czy wszystkie biblioteki przywoływanego znajdują się w folderze modułu.

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Typowe błędy podczas pracy z konfiguracji żądanego stanu (DSC)
### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>Scenariusz: Węzeł jest w stanie błędu z powodu błędu "Nie została odnaleziona"
**Błąd:** węzeł ma raport o stanu i zawierające błąd "próba pobrania akcji z serwera https://``<url>``//accounts/ ``<account-id>`` /Nodes(AgentId=``<agent-id>``) / GetDscAction nie powiodło się, ponieważ prawidłowej konfiguracji ``<guid>`` nie można odnaleźć. "

**Przyczyną tego błędu:** ten błąd występuje zazwyczaj gdy węzeł jest przypisany do nazwy konfiguracji (na przykład ABC) zamiast Nazwa konfiguracji węzła (na przykład ABC. Serwer sieci Web).

**Porady dotyczące rozwiązywania problemów:**

* Upewnij się, przypisujesz węzeł z "Nazwa konfiguracji węzła", a nie "Nazwa konfiguracji".
* Można przypisać konfiguracji węzła do węzła przy użyciu portalu Azure lub za pomocą polecenia cmdlet programu PowerShell.

  * Aby przypisać konfiguracji węzła do węzła przy użyciu portalu Azure, otwórz **węzłów DSC** , a następnie wybierz węzeł i kliknij przycisk na **przypisać konfiguracji węzła** przycisku.  
  * Aby przypisać konfiguracji węzła do węzła przy użyciu polecenia cmdlet programu PowerShell, użyj **AzureRmAutomationDscNode zestaw** polecenia cmdlet

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>Scenariusz: Nie ma żadnych konfiguracji węzła (pliki MOF) zostały utworzone podczas kompilowania konfiguracji
**Błąd:** zadania kompilacji DSC zawiesza się z powodu błędu: "Kompilacja została ukończona pomyślnie, ale nie wygenerowano żadnych configuration.mofs węzła".

**Przyczyną tego błędu:** podczas następujące wyrażenie **węzła** — słowo kluczowe w konfiguracji DSC daje w wyniku `$null`, a następnie są tworzone nie konfiguracje węzłów.

**Porady dotyczące rozwiązywania problemów:** żadnego z następujących rozwiązania problemu:

* Upewnij się, że wyrażenie dalej, aby **węzła** — słowo kluczowe w definicji konfiguracji nie jest obliczane do $null.
* Jeśli ConfigurationData podczas kompilowania konfiguracji, upewnij się, przekazywane oczekiwanych wartości, które wymaga konfiguracji z [ConfigurationData](automation-dsc-compile.md#configurationdata).

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a>Scenariusz: Raport węzła DSC staje się zablokował stanu "w toku"
**Błąd:** agenta DSC danych wyjściowych "Nie odnaleziono wystąpienia z daną wartości właściwości."

**Przyczyną tego błędu:** przeprowadzono uaktualnienie z wersji platformy WMF i spowodować uszkodzenie usługi WMI.

**Porady dotyczące rozwiązywania problemów:** Aby rozwiązać problem wykonaj instrukcje w [DSC znane problemy i ograniczenia](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) artykułu.

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a>Scenariusz: Nie można użyć poświadczeń w konfiguracji DSC
**Błąd:** zadania kompilacji DSC zostało wstrzymane z powodu błędu: "Błąd System.InvalidOperationException właściwość poświadczenia i typ przetwarzania ``<some resource name>``: konwertowanie i przechowywanie zaszyfrowane hasło jako zwykły tekst jest dozwolone tylko wtedy, gdy PSDscAllowPlainTextPassword jest ustawiona na true ".

**Przyczyną tego błędu:** zostały użyte poświadczenia w konfiguracji, ale nie dostarczył prawidłowego **ConfigurationData** można ustawić **PSDscAllowPlainTextPassword** na wartość true dla każdego węzła Konfiguracja.

**Porady dotyczące rozwiązywania problemów:**

* Upewnij się, że przekazywanie właściwego **ConfigurationData** można ustawić **PSDscAllowPlainTextPassword** na wartość true dla każdej konfiguracji węzła wymienionych w konfiguracji. Aby uzyskać więcej informacji, zobacz [zasoby w konfiguracji DSC automatyzacji Azure](automation-dsc-compile.md#assets).

## <a name="common-errors-when-onboarding-solutions"></a>Typowe błędy podczas przechodzenia do rozwiązania

Podczas dołączania rozwiązania mogą wystąpić błędy. Poniżej znajduje się lista typowych błędów, które możesz spotkać.

### <a name="computergroupqueryformaterror"></a>ComputerGroupQueryFormatError

**Przyczyną tego błędu:**

Ten kod błędu oznacza, że zapytania grupy komputera zapisanego wyszukiwania używane pod kątem rozwiązania nie został poprawnie sformatowany. Zmieniono zapytanie, lub został zmieniony przez system.

**Porady dotyczące rozwiązywania problemów:**

Można usunąć zapytania dla tego rozwiązania, a reonboard rozwiązanie, polegające na zapytanie. Zapytanie znajduje się w obszarze roboczym, w obszarze **zapisane wyszukiwania**. Nazwa zapytania jest **MicrosoftDefaultComputerGroup**, a kategoria zapytania jest nazwą rozwiązania skojarzoną z tym zapytaniem. W przypadku wielu rozwiązań są włączone, **MicrosoftDefaultComputerGroup** zawiera wiele razy w obszarze **zapisane wyszukiwania**.

### <a name="policyviolation"></a>PolicyViolation

**Przyczyną tego błędu:**

Ten kod błędu oznacza, że wdrożenie nie powiodło się z powodu naruszenia co najmniej jedne zasady.

**Porady dotyczące rozwiązywania problemów:**

Aby pomyślnie wdrożyć rozwiązanie, należy wziąć pod uwagę zmiany wskazanych zasad. Ponieważ istnieje wiele różnych typów zasad, które mogą być definiowane, wymaganych zmian są zależne od naruszenia zasad. Na przykład zdefiniowano zasady grupy zasobów, której odmówiono uprawnień do zmiany zawartości niektórych typów zasobów w grupie zasobów, można na przykład wykonać jedną z następujących czynności:

*   Usuń zasady całkowicie.
* Spróbuj dodać do innej grupie zasobów.
* Popraw zasady, na przykład:
   * Ponownie, wybierając zasady dla konkretnego zasobu (np. określone konto automatyzacji).
   * Zmiana zestawu zasobów tej zasady zostało skonfigurowane do odrzucania.

Sprawdź powiadomienia w prawym górnym rogu portalu Azure lub przejdź do grupę zasobów, która zawiera konta automatyzacji i wybierz **wdrożeń** w obszarze **ustawienia** Aby wyświetlić nieudane wdrożenia. Aby dowiedzieć się więcej na temat zasad usługi Azure, odwiedź stronę: [Omówienie zasad Azure](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="next-steps"></a>Kolejne kroki

Jeśli wykonano poprzednie kroki rozwiązywania problemów i nie może znaleźć odpowiedzi, możesz przejrzeć dodatkowe wsparcie następujące opcje:

* Uzyskaj pomoc od ekspertów platformy Azure. Problem tak, aby przesłać [fora MSDN Azure lub przepełnienie stosu](https://azure.microsoft.com/support/forums/).
* Plik zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny Microsoft Azure obsługuje](https://azure.microsoft.com/support/options/) i kliknij przycisk **uzyskać pomoc techniczną** w obszarze **technicznych i działem rozliczeń**.
* Opublikuj żądanie skryptu na [Centrum skryptów](https://azure.microsoft.com/documentation/scripts/) Jeśli szukasz rozwiązanie elementu runbook usługi Automatyzacja Azure lub modułu integracji.
* Opublikuj żądania opinii lub informacji o funkcji automatyzacji Azure na [User Voice](https://feedback.azure.com/forums/34192--general-feedback).
