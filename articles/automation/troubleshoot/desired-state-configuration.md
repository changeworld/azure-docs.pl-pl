---
title: Rozwiązywanie problemów przy użyciu usługi Azure Automation Desired State Configuration (DSC)
description: Ten artykuł zawiera informacje na temat rozwiązywania problemów Desired State Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7cb0d77a266dbe8afd331782965e7e9a44663671
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514461"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Rozwiązywanie problemów z Desired State Configuration (DSC)

Ten artykuł zawiera informacje na temat rozwiązywania problemów z Desired State Configuration (DSC).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Typowe błędy podczas pracy z Desired State Configuration (DSC)

### <a name="unsupported-characters"></a>Scenariusz: Nie można usunąć konfiguracji przy użyciu znaków specjalnych w portalu

#### <a name="issue"></a>Problem

Podczas próby usunięcia konfiguracji DSC w portalu, zostanie wyświetlony następujący błąd:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest tymczasowy planowanej pod kątem można rozwiązać problem.

#### <a name="resolution"></a>Rozwiązanie

* Użyj polecenia cmdlet "Remove-AzAutomationDscConfiguration" Az, aby usunąć konfigurację.
* Dokumentacja dla tego polecenia cmdlet nie zostało jeszcze zaktualizowana.  W międzyczasie można znaleźć w dokumentacji modułu AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Scenariusz: Nie można zarejestrować agenta rozszerzenia Dsc

#### <a name="issue"></a>Problem

Podczas próby uruchomienia `Set-DscLocalConfigurationManager` lub inne polecenie cmdlet DSC, zostanie wyświetlony błąd:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

#### <a name="cause"></a>Przyczyna

Zapora maszyny jest związany z serwera proxy lub inne błędy sieci zazwyczaj przyczyną tego błędu.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy komputer ma dostęp do odpowiednich punktów końcowych dla usługi Azure Automation DSC i spróbuj ponownie. Aby uzyskać listę portów i adresów potrzebne, zobacz [Planowanie sieci](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>Scenariusz: Węzeł jest w stanie nie powiodło się z powodu błędu "Nie znaleziono"

#### <a name="issue"></a>Problem

Węzeł ma raport o stanu, zawiera kod **błędu**:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje zazwyczaj gdy węzeł zostanie przypisany do nazwy konfiguracji (na przykład ABC) zamiast Nazwa konfiguracji węzła (np. ABC. Serwer sieci Web).

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że jesteś przypisywanie węzeł z "Nazwa konfiguracji węzła", a nie "konfiguracji nazwę".
* Można przypisać konfiguracji węzła do węzła przy użyciu witryny Azure portal lub za pomocą polecenia cmdlet programu PowerShell.

  * Aby przypisać konfiguracji węzła do węzła przy użyciu witryny Azure portal, otwórz **węzłów DSC** strona, a następnie wybierz węzeł i kliknij **przypisywanie konfiguracji węzła** przycisku.  
  * Aby przypisać konfiguracji węzła do węzła przy użyciu polecenia cmdlet programu PowerShell, użyj **AzureRmAutomationDscNode zestaw** polecenia cmdlet

### <a name="no-mof-files"></a>Scenariusz: Brak konfiguracji węzła (pliki MOF), zostały utworzone podczas kompilowania konfiguracji

#### <a name="issue"></a>Problem

Zadania kompilacji DSC zawiesza się z powodu błędu:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Przyczyna

Podczas następujące wyrażenie **węzła** — słowo kluczowe w konfiguracji DSC, które daje w wyniku `$null`, a następnie są tworzone nie konfiguracje węzłów.

#### <a name="resolution"></a>Rozwiązanie

Dowolne z poniższych rozwiązań rozwiązać ten problem:

* Upewnij się, że wyrażenie obok **węzła** — słowo kluczowe w definicji konfiguracji nie jest dokonanie oceny oprogramowania do $null.
* Jeśli przekazujesz ConfigurationData podczas kompilowania konfiguracji upewnij się, czy przekazujesz oczekiwanych wartości, których konfiguracja wymaga od [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Scenariusz: Raport węzła DSC staje się została zablokowana w stanie "w toku"

#### <a name="issue"></a>Problem

Dane wyjściowe agenta DSC:

```error
No instance found with given property values
```

#### <a name="cause"></a>Przyczyna

Uaktualniono używanej wersji programu WMF i spowodować uszkodzenie usługi WMI.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, postępuj zgodnie z instrukcjami [DSC — znane problemy i ograniczenia](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) artykułu.

### <a name="issue-using-credential"></a>Scenariusz: Nie można użyć poświadczeń w konfiguracji DSC

#### <a name="issue"></a>Problem

Zadania kompilacji DSC zostało wstrzymane z powodu błędu:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Przyczyna

Użyto poświadczeń w konfiguracji, ale nie zapewniają odpowiedniego **ConfigurationData** można ustawić **PSDscAllowPlainTextPassword** na wartość true dla każdej konfiguracji węzła.

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się przekazać właściwego **ConfigurationData** można ustawić **PSDscAllowPlainTextPassword** na wartość true dla każdej konfiguracji węzła, który jest wymieniony w konfiguracji. Aby uzyskać więcej informacji, zobacz [zasobów w usłudze Azure Automation DSC](../automation-dsc-compile.md#assets).

### <a name="failure-processing-extension"></a>Scenariusz: Dołączanie z rozszerzenia dsc, błąd "Wystąpił błąd podczas przetwarzania rozszerzenia"

#### <a name="issue"></a>Problem

Podczas dołączania za pomocą rozszerzenia DSC, błąd występuje, zawierające błąd:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Przyczyna

Ten błąd zazwyczaj występuje, gdy węzeł zostanie przypisany Nazwa konfiguracji węzła, który nie istnieje w usłudze.

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że w przypadku przypisywania węzeł z nazwa konfiguracji węzła, który dokładnie pasuje do nazwy w usłudze.
* Użytkownik może nie zawierać nazwa konfiguracji węzła, co spowoduje, że dołączania węzła, ale nie przypisywanie konfiguracji węzła

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie był widoczny problemu lub są w stanie rozwiązać problemu, odwiedź jedną z następujących kanałów obsługi więcej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.
