---
title: Rozwiązywanie problemów związanych z dołączaniem Update Management, Change Tracking i spisu
description: Dowiedz się, jak rozwiązywać problemy z dołączaniem za pomocą rozwiązań Update Management, Change Tracking i spisu
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 8b4ee999bb23abdcea3411720bde244b2da4e89f
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516396"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Rozwiązywanie problemów podczas dołączania rozwiązań

Mogą wystąpić błędy podczas dołączania rozwiązań, takich jak Update Management lub Change Tracking i spisu. W tym artykule opisano różne błędy, które mogą wystąpić i sposoby ich rozwiązywania.

## <a name="known-issues"></a>Znane problemy

### <a name="node-rename"></a>Scenariusz Zmiana nazwy zarejestrowanego węzła wymaga ponownego zarejestrowania/rejestracji

#### <a name="issue"></a>Problem

Węzeł jest zarejestrowany w Azure Automation a następnie zmieniany jest system operacyjny ComputerName.  Raporty z węzła nadal pojawiają się z oryginalną nazwą.

#### <a name="cause"></a>Przyczyna

Zmiana nazwy zarejestrowanych węzłów nie aktualizuje nazwy węzła w Azure Automation.

#### <a name="resolution"></a>Rozwiązanie

Wyrejestruj węzeł z konfiguracji stanu Azure Automation, a następnie zarejestruj go ponownie.  Raporty publikowane w usłudze przed upływem tego czasu nie będą już dostępne.


### <a name="resigning-cert"></a>Scenariusz Certyfikaty ponownego podpisywania za pośrednictwem serwera proxy HTTPS nie są obsługiwane

#### <a name="issue"></a>Problem

Klienci zgłosili, że podczas łączenia się za pośrednictwem rozwiązania serwera proxy, które przerywa ruch https, a następnie ponownie szyfruje ruch przy użyciu nowego certyfikatu, usługa nie zezwala na połączenie.

#### <a name="cause"></a>Przyczyna

Azure Automation nie obsługuje certyfikatów ponownego podpisywania używanych do szyfrowania ruchu.

#### <a name="resolution"></a>Rozwiązanie

Nie istnieje obejście tego problemu.

## <a name="general-errors"></a>Błędy ogólne

### <a name="missing-write-permissions"></a>Scenariusz Nie można włączyć dołączania z komunikatem — rozwiązanie nie jest możliwe.

#### <a name="issue"></a>Problem

Podczas próby dołączenia maszyny wirtualnej do rozwiązania zostanie wyświetlony jeden z następujących komunikatów:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany przez nieprawidłowe lub brakujące uprawnienia do maszyny wirtualnej, obszaru roboczego lub dla użytkownika.

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że masz odpowiednie uprawnienia do dołączania maszyny wirtualnej. Przejrzyj [uprawnienia konieczne do](../automation-role-based-access-control.md#onboarding) dołączenia maszyn i spróbuj ponownie dołączyć rozwiązanie. Jeśli wystąpi błąd `The solution cannot be enabled on this VM because the permission to read the workspace is missing`, upewnij się, że `Microsoft.OperationalInsights/workspaces/read` masz uprawnienia do sprawdzenia, czy maszyna wirtualna została dołączona do obszaru roboczego.

### <a name="diagnostic-logging"></a>Scenariusz Nie można wykonać dołączania, ponieważ nie powiodło się skonfigurowanie konta usługi Automation na potrzeby rejestrowania diagnostycznego

#### <a name="issue"></a>Problem

Podczas próby dołączenia maszyny wirtualnej do rozwiązania zostanie wyświetlony następujący komunikat:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany tym, że warstwa cenowa nie jest zgodna z modelem rozliczania subskrypcji. Aby uzyskać więcej informacji, zobacz [monitorowanie użycia i szacowane koszty w Azure monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Rozwiązanie

Utwórz ręcznie obszar roboczy Log Analytics i powtórz proces dołączania w celu wybrania utworzonego obszaru roboczego.

### <a name="computer-group-query-format-error"></a>Scenariusz ComputerGroupQueryFormatError

#### <a name="issue"></a>Problem

Ten kod błędu oznacza, że zapisane zapytanie grupy komputerów wyszukiwania używane jako docelowe rozwiązania nie zostało prawidłowo sformatowane. 

#### <a name="cause"></a>Przyczyna

Użytkownik mógł zmienić zapytanie lub mógł zostać zmodyfikowany przez system.

#### <a name="resolution"></a>Rozwiązanie

Można usunąć zapytanie dla tego rozwiązania i reonboard rozwiązanie, które ponownie tworzy zapytanie. Zapytanie można znaleźć w obszarze roboczym, w obszarze **zapisane wyszukiwania**. Nazwa zapytania to **MicrosoftDefaultComputerGroup**, a kategoria zapytania to nazwa rozwiązania skojarzonego z tym zapytaniem. Jeśli włączono wiele rozwiązań, **MicrosoftDefaultComputerGroup** wyświetla wiele razy w obszarze **zapisane wyszukiwania**.

### <a name="policy-violation"></a>Scenariusz PolicyViolation

#### <a name="issue"></a>Problem

Ten kod błędu oznacza, że wdrożenie nie powiodło się z powodu naruszenia co najmniej jednej zasady.

#### <a name="cause"></a>Przyczyna 

Istnieją zasady blokujące wykonywanie operacji.

#### <a name="resolution"></a>Rozwiązanie

Aby można było pomyślnie wdrożyć rozwiązanie, należy rozważyć zmianę wskazanych zasad. Istnieje wiele różnych typów zasad, które można zdefiniować, ale określone zmiany są zależne od zasad, które zostały naruszone. Na przykład jeśli zasady zostały zdefiniowane w grupie zasobów, która odmówiła uprawnienia do zmiany zawartości niektórych typów zasobów w ramach tej grupy zasobów, można na przykład wykonać dowolną z następujących czynności:

* Usuń zasady całkowicie.
* Spróbuj dołączyć do innej grupy zasobów.
* Popraw zasady, na przykład:
  * Ponowne kierowanie zasad do określonego zasobu (na przykład do określonego konta usługi Automation).
  * Poprawianie zestawu zasobów, dla których zasady zostały skonfigurowane do odmowy.

Sprawdź powiadomienia w prawym górnym rogu Azure Portal lub przejdź do grupy zasobów zawierającej konto usługi Automation, a następnie wybierz pozycję **wdrożenia** w obszarze **Ustawienia** , aby wyświetlić niepowodzenie wdrożenia. Aby dowiedzieć się więcej na temat Azure Policy odwiedzenia: [Przegląd Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="unlink"></a>Scenariusz Błędy podczas próby odłączenia obszaru roboczego

#### <a name="issue"></a>Problem

Podczas próby odłączenia obszaru roboczego pojawia się następujący błąd:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy w obszarze roboczym Log Analytics nadal są aktywne rozwiązania, które są zależne od konta usługi Automation i połączonego obszaru roboczego Analytics.

### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy usunąć następujące rozwiązania z obszaru roboczego, jeśli są używane:

* Zarządzanie aktualizacjami
* Śledzenie zmian
* Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy

Po usunięciu rozwiązań możesz odłączyć obszar roboczy. Ważne jest, aby wyczyścić wszystkie istniejące artefakty z tych rozwiązań z obszaru roboczego i konta usługi Automation.  

* Zarządzanie aktualizacjami
  * Usuń wdrożenia aktualizacji (harmonogramy) z konta usługi Automation
* Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy
  * Usuń wszystkie blokady składników rozwiązania na koncie usługi Automation w obszarze **Ustawienia** > **blokady**.
  * Aby uzyskać dodatkowe kroki usuwania maszyn wirtualnych lub zatrzymywania z rozwiązania w ciągu kilku godzin, zobacz sekcję [Uruchamianie/zatrzymywanie maszyny wirtualnej w rozwiązaniu off-godzinnym](../automation-solution-vm-management.md##remove-the-solution).

## <a name="mma-extension-failures"></a>Błędy rozszerzenia MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Podczas wdrażania rozwiązania wdrożone są różne powiązane zasoby. Jeden z tych zasobów jest rozszerzeniem Microsoft Monitoring Agent lub agentem Log Analytics dla systemu Linux. Są to rozszerzenia maszyny wirtualnej zainstalowane przez agenta gościa maszyny wirtualnej, który jest odpowiedzialny za komunikowanie się ze skonfigurowanym obszarem roboczym Log Analytics, na potrzeby późniejszej koordynacji pobierania plików binarnych i innych plików, które rozwiązanie, które dołączasz, jest zależne od tego, kiedy rozpoczyna wykonywanie.
Zazwyczaj należy zwrócić uwagę na to, że w przypadku niepowodzenia instalacji systemu Linux Log Analytics MMA się z powiadomieniem o błędach instalacyjnych w centrum powiadomień. Kliknięcie tego powiadomienia powoduje uzyskanie dalszych informacji o konkretnym błędzie. Przechodzenie do zasobu grupy zasobów, a następnie do elementu wdrożenia w ramach niego również zawiera szczegółowe informacje o błędach wdrożenia, które wystąpiły.
Instalacja agenta MMA lub Log Analytics dla systemu Linux może zakończyć się niepowodzeniem z różnych powodów, a kroki, które należy podjąć w celu rozwiązania tych błędów, różnią się w zależności od problemu. Poniżej opisano kroki rozwiązywania problemów.

W poniższej sekcji opisano różne problemy, które można napotkać podczas dołączania, które powodują awarię wdrożenia rozszerzenia MMA.

### <a name="webclient-exception"></a>Scenariusz Wystąpił wyjątek podczas żądania klienta WebClient

Rozszerzenie MMA na maszynie wirtualnej nie może komunikować się z zasobami zewnętrznymi i wdrożenie kończy się niepowodzeniem.

#### <a name="issue"></a>Problem

Poniżej przedstawiono przykłady komunikatów o błędach, które są zwracane:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Przyczyna

Niektóre potencjalne przyczyny tego błędu to:

* Na maszynie wirtualnej skonfigurowano serwer proxy, który zezwala tylko na określone porty.

* Ustawienie zapory blokuje dostęp do wymaganych portów i adresów.

#### <a name="resolution"></a>Rozwiązanie

Upewnij się, że masz odpowiednie porty i adresy otwarte do komunikacji. Lista portów i adresów znajduje się w temacie [Planning The Network](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Scenariusz Instalacja nie powiodła się z powodu przejściowych problemów ze środowiskiem

Instalacja rozszerzenia Microsoft Monitoring Agent nie powiodła się podczas wdrażania, ponieważ inna instalacja lub akcja blokująca instalację

#### <a name="issue"></a>Problem

Poniżej przedstawiono przykłady komunikatów o błędach, które mogą zostać zwrócone:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Przyczyna

Niektóre potencjalne przyczyny tego błędu to:

* Inna instalacja jest w toku
* System jest wyzwalany w celu ponownego uruchomienia podczas wdrażania szablonu

#### <a name="resolution"></a>Rozwiązanie

Ten błąd jest przejściowym błędem. Ponów wdrożenie, aby zainstalować rozszerzenie.

### <a name="installation-timeout"></a>Scenariusz Limit czasu instalacji

Instalacja rozszerzenia MMA nie została ukończona z powodu przekroczenia limitu czasu.

#### <a name="issue"></a>Problem

Poniższy przykład zawiera komunikat o błędzie, który może zostać zwrócony:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Przyczyna

Ten błąd występuje, ponieważ maszyna wirtualna jest w dużym obciążeniu podczas instalacji.

### <a name="resolution"></a>Rozwiązanie

Spróbuj zainstalować rozszerzenie MMA, gdy maszyna wirtualna jest w niższym obciążeniu.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
