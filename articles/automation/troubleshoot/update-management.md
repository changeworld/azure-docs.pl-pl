---
title: Rozwiązywanie problemów z zarządzania aktualizacjami
description: Dowiedz się, jak rozwiązać problemy z zarządzania aktualizacjami
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b77d1210ff48a4bd30834fcbad64173bf77b1290
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063990"
---
# <a name="troubleshooting-issues-with-update-management"></a>Rozwiązywanie problemów z zarządzania aktualizacjami

W tym artykule omówiono rozwiązania, aby rozwiązać problemy, które można napotkać, korzystając z zarządzania aktualizacjami

## <a name="windows"></a>Windows

Jeśli wystąpią problemy podczas próby dołączenia rozwiązania na maszynie wirtualnej, sprawdź **programu Operations Manager** dziennika zdarzeń w obszarze **Dzienniki aplikacji i usług** na komputerze lokalnym dla zdarzeń z Identyfikator zdarzenia **4502** i zawierający komunikat zdarzenia **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

Poniższa sekcja zawiera wyróżnione określone komunikaty o błędach i możliwe rozwiązanie dla każdego. Aby uzyskać dołączania innych problemów, zobacz [Rozwiązywanie problemów z rozwiązania dołączania](onboarding.md).

### <a name="machine-already-registered"></a>Scenariusz: Maszyna jest już zarejestrowana dla innego konta

#### <a name="issue"></a>Problem

Pojawi się następujący komunikat o błędzie:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Przyczyna

Komputer jest już został załadowany do innego obszaru roboczego do zarządzania aktualizacjami.

#### <a name="resolution"></a>Rozwiązanie

Przeprowadzić czyszczenie starego artefaktów na komputerze przez [Usuwanie grupy hybrydowych runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) , a następnie spróbuj ponownie.

### <a name="machine-unable-to-communicate"></a>Scenariusz: Maszyny nie może komunikować się z usługą

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

Może to być serwer proxy, bramy lub zapora blokuje komunikacji sieciowej.

#### <a name="resolution"></a>Rozwiązanie

Zapoznaj się z sieci i upewnij się, że odpowiednie porty i adresy są dozwolone. Zobacz [wymagania sieciowego](../automation-hybrid-runbook-worker.md#network-planning), aby uzyskać listę portów i adresów, które są wymagane przez zarządzanie aktualizacjami i hybrydowych procesów roboczych elementu Runbook.

### <a name="unable-to-create-selfsigned-cert"></a>Scenariusz: Nie można utworzyć certyfikatu z podpisem własnym

#### <a name="issue"></a>Problem

Wyświetlany jest jeden z następujących komunikatów o błędzie:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Przyczyna

Hybrydowy proces roboczy elementu Runbook nie może wygenerować certyfikat z podpisem własnym

#### <a name="resolution"></a>Rozwiązanie

Sprawdź konto system ma dostęp do odczytu do folderu **C:\ProgramData\Microsoft\Crypto\RSA** , a następnie spróbuj ponownie.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scenariusz: Przebieg aktualizacji nie powiedzie się

#### <a name="issue"></a>Problem

Niepowodzenie przebiegów aktualizacji, można uruchomić na komputerze systemu Linux.

#### <a name="cause"></a>Przyczyna

Linux hybrydowy proces roboczy ma niepoprawny stan.

#### <a name="resolution"></a>Rozwiązanie

Utwórz kopię następującego pliku dziennika i zachować go na potrzeby rozwiązywania problemów:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scenariusz: Przebieg aktualizacji uruchamiany, jednak wystąpią błędy

#### <a name="issue"></a>Problem

Przebieg aktualizacji uruchamiany, ale napotkał błędy podczas uruchamiania.

#### <a name="cause"></a>Przyczyna

Możliwe przyczyny:

* Menedżer pakietów jest zła
* Określone pakiety może zakłócić poprawki w chmurze
* Inne przyczyny

#### <a name="resolution"></a>Rozwiązanie

Jeśli wystąpią błędy podczas przebieg aktualizacji po jego pomyślnym uruchomieniu w systemie Linux, sprawdź dane wyjściowe z zainfekowanego komputera w Uruchom zadanie. Może się okazać określone komunikaty o błędach z Menedżera pakietu na komputerze, który można badania i podejmij akcję. Zarządzanie aktualizacjami wymaga Menedżera pakietów wskazywać dobrej kondycji w przypadku wdrożeń Pomyślna aktualizacja.

W niektórych przypadkach pakietu aktualizacji może zakłócać zaktualizować zarządzania uniemożliwia wdrożenia aktualizacji na ukończenie. Jeśli zobaczysz, że, musisz zainstalować je ręcznie albo Wyklucz te pakiety z przebiegi aktualizacji przyszłych samodzielnie.

Jeśli nie możesz rozwiązać problem z poprawką, Utwórz kopię następującego pliku dziennika i zachować go **przed** uruchamia dalej wdrożenia aktualizacji na potrzeby rozwiązywania problemów:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie została uwzględniona problemu lub nie można rozwiązać problemu, odwiedź jedną z następujących kanałów więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy możesz pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać obsługuje**.