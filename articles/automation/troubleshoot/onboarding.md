---
title: Rozwiązywanie problemów dotyczących błędów przechodzenia do zarządzania aktualizacjami, śledzenia zmian i spisu
description: Dowiedz się, jak rozwiązywanie problemów przy dołączaniu z zarządzania aktualizacjami, śledzenia zmian i rozwiązania magazynu
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 044cb56b8991a1eb2dd6a1d35be621f2ffab3250
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063912"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Rozwiązywanie problemów podczas przechodzenia do rozwiązania

Mogą wystąpić błędy podczas przechodzenia do rozwiązania, takie jak zarządzanie aktualizacjami lub śledzenia zmian i spisu. W tym artykule opisano różne błędy, które mogą wystąpić i sposobu ich rozwiązania.

## <a name="general-errors"></a>Ogólne błędy

### <a name="computer-grou-query-format-error"></a>Scenariusz: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problem

Ten kod błędu oznacza, że zapytania grupy komputera zapisanego wyszukiwania używane pod kątem rozwiązania nie został poprawnie sformatowany. 

#### <a name="cause"></a>Przyczyna

Zmieniono zapytanie, lub został zmieniony przez system.

#### <a name="resolution"></a>Rozwiązanie

Można usunąć zapytania dla tego rozwiązania, a reonboard rozwiązanie, polegające na zapytanie. Zapytanie znajduje się w obszarze roboczym, w obszarze **zapisane wyszukiwania**. Nazwa zapytania jest **MicrosoftDefaultComputerGroup**, a kategoria zapytania jest nazwą rozwiązania skojarzoną z tym zapytaniem. W przypadku wielu rozwiązań są włączone, **MicrosoftDefaultComputerGroup** zawiera wiele razy w obszarze **zapisane wyszukiwania**.

### <a name="policy-violation"></a>Scenariusz: PolicyViolation

#### <a name="issue"></a>Problem

Ten kod błędu oznacza, że wdrożenie nie powiodło się z powodu naruszenia co najmniej jedne zasady.

#### <a name="cause"></a>Przyczyna 

Zasady zostaną spełnione, która blokuje ukończenie operacji.

#### <a name="resolution"></a>Rozwiązanie

Aby pomyślnie wdrożyć rozwiązanie, należy wziąć pod uwagę zmiany wskazanych zasad. Ponieważ istnieje wiele różnych typów zasad, które mogą być definiowane, wymaganych zmian są zależne od naruszenia zasad. Na przykład zdefiniowano zasady grupy zasobów, której odmówiono uprawnień do zmiany zawartości niektórych typów zasobów w grupie zasobów, można na przykład wykonać jedną z następujących czynności:

* Usuń zasady całkowicie.
* Spróbuj dodać do innej grupie zasobów.
* Popraw zasady, na przykład:
  * Ponownego przypisywania zasad do określonego zasobu (np. określone konto automatyzacji).
  * Zmiana zestawu zasobów tej zasady zostało skonfigurowane do odrzucania.

Sprawdź powiadomienia w prawym górnym rogu portalu Azure lub przejdź do grupę zasobów, która zawiera konta automatyzacji i wybierz **wdrożeń** w obszarze **ustawienia** Aby wyświetlić nieudane wdrożenia. Aby dowiedzieć się więcej na temat zasad usługi Azure, odwiedź stronę: [Omówienie zasad Azure](../../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="mma-extension-failures"></a>Błędy rozszerzenia MMA

W przypadku wdrażania rozwiązania, różnych powiązane zasoby są wdrażane. Jeden z tych zasobów jest rozszerzenie agenta monitorowania firmy Microsoft lub Agent pakietu OMS dla systemu Linux. Są to zainstalowanych przez agenta gościa maszyny wirtualnej, która jest odpowiedzialna za komunikację z roboczym Operations Management Suite (OMS) skonfigurowany na potrzeby pobierania plików binarnych nowszych koordynacji rozszerzeń maszyny wirtualnej i inne pliki, które rozwiązania są dołączania zależne raz rozpoczyna się wykonanie.
Należy zwykle najpierw zostaną powiadomieni o MMA lub Agent pakietu OMS dla systemu Linux błędów instalacji dla powiadomienia w Centrum powiadomień. Kliknięcie powiadomienia zapewnia dodatkowe informacje na temat określonego błędu. Nawigacji zasobów grup zasobów, a następnie element wdrożeń w nim także szczegółowe informacje dotyczące niepowodzeń wdrażania, które wystąpiły.
Instalacja MMA lub Agent pakietu OMS Linux może się nie powieść z różnych powodów i kroki, aby podjąć w celu rozwiązania tych błędów zależy, w zależności od tego problemu. Postępuj zgodnie z określonym kroki rozwiązywania problemów.

W poniższej sekcji opisano różne problemy, które mogą wystąpić podczas dołączania który spowodować niepowodzenie we wdrożeniu MMA rozszerzenia.

### <a name="webclient-exception"></a>Scenariusz: Wystąpił wyjątek podczas żądania klienta WebClient

MMA rozszerzenia na maszynie wirtualnej nie może nawiązać połączenia z zasobów zewnętrznych i wdrożenie kończy się niepowodzeniem.

#### <a name="issue"></a>Problem

Poniżej przedstawiono przykłady komunikaty o błędach, które są zwracane:

```
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Przyczyna

Niektóre możliwe przyczyny tego błędu to:

* Brak skonfigurowanych na maszynie wirtualnej, który zezwala tylko na określone porty serwera proxy.

* Ustawienia zapory zablokował dostęp do wymagane porty i adresy.

#### <a name="resolution"></a>Rozwiązanie

Sprawdź, czy masz odpowiednie porty i adresy Otwórz komunikacji. Aby uzyskać listę portów i adresów, zobacz [planowania sieci](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Scenariusz: Instalacja nie powiodła się z powodu problemów środowiska przejściowe

Podczas wdrażania z powodu innej instalacji lub akcji blokowania instalacji nie można zainstalować rozszerzenia usługi Microsoft Monitoring Agent

#### <a name="issue"></a>Problem

Poniżej przedstawiono przykłady komunikaty o błędach mogą być zwrócone:

```
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Przyczyna

Niektóre możliwe przyczyny tego błędu to:

* Inna instalacja jest w toku
* System jest zostało wyzwolone ponownego uruchomienia podczas wdrażania szablonu

#### <a name="resolution"></a>Rozwiązanie

Ten błąd jest błąd charakter przejściowy. Spróbuj ponownie, aby zainstalować to rozszerzenie.

### <a name="installation-timeout"></a>Scenariusza: Limit czasu instalacji

Instalacja rozszerzenia MMA nie została ukończona z powodu przekroczenia limitu czasu.

#### <a name="issue"></a>Problem

Poniżej przedstawiono przykładowy komunikat o błędzie, który może być zwrócony:

```
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Przyczyna

Ten błąd wynika z maszyny wirtualnej jest mocno obciążony podczas instalacji.

### <a name="resolution"></a>Rozwiązanie

Spróbuj zainstalować rozszerzenie MMA, gdy maszyna wirtualna jest niższe obciążenie.

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie została uwzględniona problemu lub nie można rozwiązać problemu, odwiedź jedną z następujących kanałów więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy możesz pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskać obsługuje**.
