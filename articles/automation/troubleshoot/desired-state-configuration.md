---
title: Rozwiązywanie problemów z żądanego stanu konfiguracji (Konfiguracja DSC automatyzacji Azure)
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z konfiguracji żądanego stanu (DSC)
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5d2eae67fcff74a7016f7f6125e31a9c8c2bda97
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064006"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Rozwiązywanie problemów z konfiguracji żądanego stanu (DSC)

Ten artykuł zawiera informacje na temat rozwiązywania problemów z konfiguracji żądanego stanu (DSC).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Typowe błędy podczas pracy z konfiguracji żądanego stanu (DSC)

### <a name="failed-not-found"></a>Scenariusz: Węzeł jest w stanie błędu z powodu błędu "Nie została odnaleziona"

#### <a name="issue"></a>Problem

Węzeł ma raport o stanu, zawiera kod **błędu**:

```
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje zazwyczaj gdy węzeł jest przypisany do nazwy konfiguracji (na przykład ABC) zamiast Nazwa konfiguracji węzła (na przykład ABC. Serwer sieci Web).

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, przypisujesz węzeł z "Nazwa konfiguracji węzła", a nie "Nazwa konfiguracji".
* Można przypisać konfiguracji węzła do węzła przy użyciu portalu Azure lub za pomocą polecenia cmdlet programu PowerShell.

  * Aby przypisać konfiguracji węzła do węzła przy użyciu portalu Azure, otwórz **węzłów DSC** , a następnie wybierz węzeł i kliknij przycisk na **przypisać konfiguracji węzła** przycisku.  
  * Aby przypisać konfiguracji węzła do węzła przy użyciu polecenia cmdlet programu PowerShell, użyj **AzureRmAutomationDscNode zestaw** polecenia cmdlet

### <a name="no-mof-files"></a>Scenariusz: Nie ma żadnych konfiguracji węzła (pliki MOF) zostały utworzone podczas kompilowania konfiguracji

#### <a name="issue"></a>Problem

Zadania kompilacji DSC zawiesza się z powodu błędu:

```
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Przyczyna

Gdy następujące wyrażenie **węzła** — słowo kluczowe w konfiguracji DSC daje w wyniku `$null`, a następnie są tworzone nie konfiguracje węzłów.

#### <a name="resolution"></a>Rozwiązanie

Żadnego z następujących rozwiązania problemu:

* Upewnij się, że wyrażenie dalej, aby **węzła** — słowo kluczowe w definicji konfiguracji nie jest obliczane do $null.
* Jeśli ConfigurationData podczas kompilowania konfiguracji, upewnij się, przekazywane oczekiwanych wartości, które wymaga konfiguracji z [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Scenariusz: Raport węzła DSC staje się zablokował stanu "w toku"

#### <a name="issue"></a>Problem

Dane wyjściowe DSC agenta:

```
No instance found with given property values
```

#### <a name="cause"></a>Przyczyna

Przeprowadzono uaktualnienie z wersji platformy WMF i spowodować uszkodzenie usługi WMI.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać problem wykonaj instrukcje w [DSC znane problemy i ograniczenia](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) artykułu.

### <a name="issue-using-credential"></a>Scenariusz: Nie można użyć poświadczeń w konfiguracji DSC

#### <a name="issue"></a>Problem

Zadania kompilacji DSC zostało wstrzymane z powodu błędu:

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Przyczyna

Zostały użyte poświadczenia w konfiguracji, ale nie dostarczył prawidłowego **ConfigurationData** można ustawić **PSDscAllowPlainTextPassword** na wartość true dla każdej konfiguracji węzła.

#### <a name="resolution"></a>Rozwiązanie

* Upewnij się, że przekazywanie właściwego **ConfigurationData** można ustawić **PSDscAllowPlainTextPassword** na wartość true dla każdej konfiguracji węzła wymienionych w konfiguracji. Aby uzyskać więcej informacji, zobacz [zasoby w konfiguracji DSC automatyzacji Azure](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie została uwzględniona problemu lub nie można rozwiązać problemu, odwiedź jedną z następujących kanałów więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy możesz pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać obsługuje**.