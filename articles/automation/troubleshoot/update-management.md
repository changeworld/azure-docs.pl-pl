---
title: Rozwiązywanie problemów z zarządzaniem aktualizacjami
description: Dowiedz się, jak rozwiązywać problemy związane z zarządzaniem aktualizacjami
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: f52767058ef69d29465f1274109b6d3ffe58296c
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092631"
---
# <a name="troubleshooting-issues-with-update-management"></a>Rozwiązywanie problemów z zarządzaniem aktualizacjami

W tym artykule omówiono rozwiązania, aby rozwiązać problemy, które może działać w przypadku korzystania z rozwiązania Update Management.

Brak agenta narzędzia do rozwiązywania problemów dla agenta hybrydowego procesu roboczego, aby określić pierwotny problem. Aby dowiedzieć się więcej na temat rozwiązywania problemów, zobacz [problemy dotyczące rozwiązywania problemów aktualizacji agenta](update-agent-issues.md). W przypadku pozostałych problemów Zobacz szczegółowe informacje poniżej o możliwych problemach.

## <a name="general"></a>Ogólne

### <a name="components-enabled-not-working"></a>Scenariusz: Składniki rozwiązania "Update Management" zostały włączone i teraz skonfigurowane tej maszyny wirtualnej

#### <a name="issue"></a>Problem

Nadal wyświetlony następujący komunikat na maszynie wirtualnej w ciągu 15 minut po dołączeniu:

```
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przez następujących przyczyn:

1. Konto usługi Automation komunikacja jest blokowana.
2. Maszyna wirtualna jest dołączona może pochodzić z klonowanym komputerze, który nie był Sysprep przy użyciu programu Microsoft Monitoring Agent zainstalowany.

#### <a name="resolution"></a>Rozwiązanie

1. Odwiedź stronę, [Planowanie sieci](../automation-hybrid-runbook-worker.md#network-planning) Aby dowiedzieć się więcej o tym, jakie adresy i porty muszą być dozwolone do zarządzania aktualizacjami do pracy.
2. Jeśli przy użyciu klonowanego obrazu, program sysprep obrazu najpierw, a następnie zainstaluj agenta MMA po fakcie.

## <a name="windows"></a>Windows

Jeśli wystąpią problemy podczas próby dołączyć rozwiązanie na maszynie wirtualnej, sprawdź **programu Operations Manager** zdarzeń dziennika **Dzienniki aplikacji i usług** na maszynę lokalną pod kątem zdarzeń za pomocą Identyfikator zdarzenia **4502** i komunikat zdarzenia zawierający **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

Poniższa sekcja wyróżnia określone komunikaty o błędach i możliwe rozwiązanie dla każdego. Dla dołączania innych problemów znajduje się pozycja [Rozwiązywanie problemów z dołączania rozwiązań](onboarding.md).

### <a name="machine-already-registered"></a>Scenariusz: Maszyna jest już zarejestrowana dla innego konta

#### <a name="issue"></a>Problem

Pojawi się następujący komunikat o błędzie:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Przyczyna

Komputer jest już dołączona do innego obszaru roboczego na potrzeby zarządzania aktualizacjami.

#### <a name="resolution"></a>Rozwiązanie

Przeprowadź czyszczenie starych artefaktów na komputerze przez [usuwanie grupę hybrydowych elementów runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) , a następnie spróbuj ponownie.

### <a name="machine-unable-to-communicate"></a>Scenariusz: Maszyny nie może nawiązać połączenia z usługą

#### <a name="issue"></a>Problem

Wyświetlany jest jeden z następujących komunikatów o błędzie:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Przyczyna

Może to być serwer proxy, bramy lub zapora blokuje komunikację sieciową.

#### <a name="resolution"></a>Rozwiązanie

Przejrzyj sieci i upewnij się, że odpowiednie porty i adresy są dozwolone. Zobacz [wymagania dotyczące sieciowej](../automation-hybrid-runbook-worker.md#network-planning), aby uzyskać listę portów i adresów, które są wymagane przez rozwiązanie Update Management i hybrydowych procesów roboczych Runbook.

### <a name="unable-to-create-selfsigned-cert"></a>Scenariusz: Nie można utworzyć certyfikatu z podpisem własnym

#### <a name="issue"></a>Problem

Wyświetlany jest jeden z następujących komunikatów o błędzie:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy elementu Runbook nie był w stanie wygenerować certyfikat z podpisem własnym

#### <a name="resolution"></a>Rozwiązanie

Sprawdź konto systemowe ma dostęp do odczytu do folderu **C:\ProgramData\Microsoft\Crypto\RSA** , a następnie spróbuj ponownie.

### <a name="nologs"></a>Scenariusz: Zarządzanie aktualizacjami dane nie są wyświetlane w usłudze Log Analytics dla maszyny

#### <a name="issue"></a>Problem

Masz maszyny, które pokazują, jak **nie oceniono** w obszarze **zgodności**, widzą danych pulsu w usłudze Log Analytics dla hybrydowego procesu roboczego Runbook, ale nie do zarządzania aktualizacjami.

#### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy elementu Runbook może być konieczne może być ponownie zarejestrowane i ponowna instalacja.

#### <a name="resolution"></a>Rozwiązanie

Wykonaj kroki opisane w temacie [wdrożenia Windows hybrydowego procesu roboczego Runbook](../automation-windows-hrw-install.md) ponowna instalacja hybrydowego procesu roboczego.

### <a name="hresult"></a>Scenariusz: Maszyna jest wyświetlana jako nie oceniono i przedstawia wyjątek HResult

#### <a name="issue"></a>Problem

Masz maszyny, które pokazują, jak **nie oceniono** w obszarze **zgodności**, i zostanie wyświetlony komunikat o wyjątku poniżej.

#### <a name="cause"></a>Przyczyna

Aktualizacja Windows nie jest poprawnie skonfigurowany na maszynie.

#### <a name="resolution"></a>Rozwiązanie

Kliknij dwukrotnie wyjątek wyświetlane na czerwono, aby wyświetlić komunikat o wyjątku całego. Przejrzyj poniższą tabelę, potencjalne rozwiązania lub akcje do wykonania:

|Wyjątek  |Rozdzielczość lub Akcja  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Wyszukiwanie kodu błędów w [Windows zaktualizować lista kodów błędów](https://support.microsoft.com/help/938205/windows-update-error-code-list) można znaleźć dodatkowe szczegóły na przyczyną wyjątku.        |
|`0x8024402C` lub `0x8024401C`     | Takie błędy występują problemy z połączeniem sieciowym. Upewnij się, że Twoja maszyna ma zapewniona odpowiednia łączność sieciowa do zarządzania aktualizacjami. Zobacz sekcję dotyczącą [Planowanie sieci](../automation-update-management.md#ports) lista portów i adresów, które są wymagane.        |
|`0x8024402C`     | Jeśli używasz serwera programu WSUS, upewnij się, wartości rejestru `WUServer` i `WUStatusServer` w kluczu rejestru `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` prawidłowy serwer WSUS.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Upewnij się, że usługa Windows Update (wuauserv) jest uruchomiony, a nie jest wyłączona.        |
|Inne wyjątek ogólny     | Przeprowadź wyszukiwanie internet w poszukiwaniu możliwych rozwiązań i pracować z lokalnej pomocy technicznej IT.         |

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scenariusz: Przebieg aktualizacji nie powiedzie się

#### <a name="issue"></a>Problem

Niepowodzenie uruchomienia aktualizacji można uruchomić na maszynie z systemem Linux.

#### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy Linux jest w złej kondycji.

#### <a name="resolution"></a>Rozwiązanie

Utwórz kopię w następującym pliku dziennika i ją zachować na potrzeby rozwiązywania problemów:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scenariusz: Uruchamia przebieg aktualizacji, ale napotka błędy

#### <a name="issue"></a>Problem

Uruchamia się podczas przebiegu aktualizacji, ale napotka błędy podczas uruchamiania.

#### <a name="cause"></a>Przyczyna

Może być możliwe przyczyny:

* Menedżer pakietów jest w złej kondycji
* Określone pakiety może zakłócać poprawek oparte na chmurze
* Inne przyczyny

#### <a name="resolution"></a>Rozwiązanie

Jeśli wystąpią błędy podczas aktualizacji uruchomienia po pomyślnym uruchomieniu w systemie Linux, sprawdź zadanie danych wyjściowych z zainfekowanego komputera w przebiegu. Z Menedżera pakietu na komputerze, który można zbadać i podejmować działania w może się okazać określone komunikaty o błędach. Rozwiązanie Update Management wymaga Menedżera pakietów, będące w dobrej kondycji, w przypadku wdrożeń Pomyślna aktualizacja.

W niektórych przypadkach aktualizacjami pakietów może zakłócać zarządzania aktualizacjami, co uniemożliwia ukończenie wdrożenia aktualizacji. Jeśli zobaczysz, że, musisz wykluczyć te pakiety z przebiegów w przyszłej aktualizacji albo ręcznie zainstalować je samodzielnie.

Jeśli nie możesz rozwiązać problem z poprawką, Utwórz kopię w następującym pliku dziennika i ją zachować **przed** następnego wdrażania aktualizacji, który rozpoczyna się na potrzeby rozwiązywania problemów:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie był widoczny problemu lub są w stanie rozwiązać problemu, odwiedź jedną z następujących kanałów obsługi więcej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.