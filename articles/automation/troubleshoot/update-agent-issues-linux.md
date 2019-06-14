---
title: Zrozumienie wyniki sprawdzania agenta systemu Linux w usłudze Azure Update Management
description: Dowiedz się, jak rozwiązywać problemy z agentem rozwiązania Update Management.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 075cf254dbf7c5d03f1afac64315f6c6f773159c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60597833"
---
# <a name="understand-the-linux-agent-check-results-in-update-management"></a>Zrozumienie wyniki sprawdzania agenta systemu Linux w zarządzania aktualizacjami

Może istnieć wiele przyczyn, na tym komputerze nie jest wyświetlany **gotowe** w zarządzania aktualizacjami. W zarządzania aktualizacjami można sprawdzić kondycję agenta hybrydowy proces roboczy, aby określić pierwotny problem. W tym artykule omówiono sposób uruchamiania narzędzia do rozwiązywania problemów dla maszyn platformy Azure z witryny Azure portal i maszyny spoza platformy Azure w [scenariusza w trybie offline](#troubleshoot-offline).

Poniżej przedstawiono stanów gotowości trzy, maszyna może być w:

* **Gotowe** — usługi Windows update agent jest wdrażany i ostatnio zaobserwowano mniej niż 1 godzinę temu.
* **Odłączony** — usługi Windows update agent jest wdrażany i ostatnio zaobserwowano ponad 1 godzinę temu.
* **Nieskonfigurowane** — usługi Windows update agent nie zostanie odnaleziony lub nie zakończono dołączania.

> [!NOTE]
> Może to być niewielkie opóźnienie między pokazuje witryny Azure portal i bieżący stan maszyny.

## <a name="start-the-troubleshooter"></a>Uruchom narzędzie do rozwiązywania problemów

Dla maszyn, klikając **Rozwiązywanie problemów** łącze w obszarze **Aktualizuj gotowość agenta** kolumny w portalu zostanie uruchomiona **Rozwiązywanie problemów z usługi Windows Update Agent** strony. W przypadku komputerów spoza platformy Azure linku oferuje do tego artykułu. Zapoznaj się z instrukcjami w trybie offline, rozwiązywać problemy z maszyny spoza platformy Azure.

![Strona listy maszyn wirtualnych](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Kontrole wymagają na maszynie Wirtualnej i działać. Jeśli maszyna wirtualna nie działa, zostanie wyświetlony przycisk, aby **Uruchom maszynę Wirtualną**.

Na **Rozwiązywanie problemów z usługi Windows Update Agent** kliknij **uruchomienia sprawdza**, aby uruchomić narzędzie do rozwiązywania problemów. Narzędzie do rozwiązywania problemów używa [Uruchom polecenie](../../virtual-machines/linux/run-command.md) do uruchamiania skryptu na maszynie, aby zweryfikować zależności, które ma agenta. Po zakończeniu rozwiązywania problemów, zwraca wynik kontroli.

![Rozwiązywanie problemów z strony](../media/update-agent-issues-linux/troubleshoot-page.png)

Po zakończeniu wyniki są zwracane w oknie. Sekcje wyboru zawierają informacje na każdym sprawdzaniu poszukuje programu.

![Usługi Windows Update agent sprawdza, czy strony](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Sprawdzanie wymagań wstępnych

### <a name="operating-system"></a>System operacyjny

Sprawdzanie systemu operacyjnego, sprawdza, czy hybrydowego procesu roboczego Runbook działa jeden z następujących systemów operacyjnych:

|System operacyjny  |Uwagi  |
|---------|---------|
|CentOS 6 (x86/x64) i 7 (x64)      | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji. Oparte na klasyfikacji poprawek wymaga "yum", aby zwrócić dane zabezpieczeń, które CentOS nie ma gotowych.         |
|Red Hat Enterprise 6 (x86/x64) i 7 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|SUSE Linux Enterprise Server 11 (x86/x64) i 12 (x64)     | Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.        |
|Ubuntu 14.04 LTS, 16.04 LTS i 18.04 LTS — x86/x64 64      |Agenci dla systemu Linux muszą mieć dostęp do repozytorium aktualizacji.         |

## <a name="monitoring-agent-service-health-checks"></a>Monitorowania kontrole kondycji usługi agenta

### <a name="oms-agent"></a>Agenta pakietu OMS

To sprawdzenie gwarantuje, czy jest zainstalowany Agent pakietu OMS dla systemu Linux. Aby uzyskać instrukcje dotyczące sposobu jego instalacji, zobacz [Instalacja agenta dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="oms-agent-status"></a>Stan agenta pakietu OMS

To sprawdzenie gwarantuje, że działa OMS Agent for Linux. Jeśli agent nie jest uruchomiony, możesz uruchomić następujące polecenie, aby podejmować próby ponownego uruchomienia. Aby uzyskać więcej informacji na temat rozwiązywania problemów z agentem, zobacz [Linux hybrydowego Runbook Worker struktury rozwiązywania problemów](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Wiele kart sieciowych

To sprawdzenie Określa, czy agent jest raportowanie do wielu obszarów roboczych. Obsługa wielu regionów nie jest obsługiwana przez rozwiązanie Update Management.

### <a name="hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook

Ten test sprawdza, czy pakiet hybrydowego procesu roboczego Runbook agenta pakietu OMS dla systemu Linux. Ten pakiet jest wymagane do zarządzania aktualizacjami do pracy.

### <a name="hybrid-runbook-worker-status"></a>Hybrydowy proces roboczy elementu Runbook stanu

Ta kontrola zapewnia, że hybrydowy proces roboczy elementu Runbook jest uruchomiony na komputerze. Następujące procesy powinny znajdować się, jeśli hybrydowego procesu roboczego Runbook działa poprawnie. Aby dowiedzieć się więcej, zobacz [Rozwiązywanie problemów z agenta usługi Log Analytics dla systemu Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Sprawdzanie łączności

### <a name="general-internet-connectivity"></a>Ogólna łączność z Internetem

Ten test sprawdza, czy komputer ma dostęp do Internetu.

### <a name="registration-endpoint"></a>Punkt końcowy rejestracji

To sprawdzenie decyduje o tym, jeśli agent prawidłowo może komunikować się z usługą agenta.

Konfiguracji serwera proxy i zapory muszą zezwalać na agenta hybrydowego procesu roboczego Runbook, aby komunikował się z punktem końcowym rejestracji. Aby uzyskać listę adresów i portów, otwieranych zobacz [sieci, planowanie hybrydowych procesów roboczych](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Punkt końcowy operacji

To sprawdzenie decyduje o tym, jeśli agent prawidłowo może komunikować się z usługą danych czasu wykonywania zadania.

Konfiguracji serwera proxy i zapory muszą zezwalać na agenta hybrydowego procesu roboczego Runbook do komunikacji z usługą danych czasu wykonywania zadania. Aby uzyskać listę adresów i portów, otwieranych zobacz [sieci, planowanie hybrydowych procesów roboczych](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Punkt końcowy usługi log Analytics 1

Ten test sprawdza, czy komputer ma dostęp do punktów końcowych wymaganych przez agenta usługi Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Punkt końcowy usługi log Analytics 2

Ten test sprawdza, czy komputer ma dostęp do punktów końcowych wymaganych przez agenta usługi Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Punkt końcowy usługi log Analytics 3

Ten test sprawdza, czy komputer ma dostęp do punktów końcowych wymaganych przez agenta usługi Log Analytics.

## <a name="troubleshoot-offline"></a>Rozwiązywanie problemów w trybie offline

Narzędzie do rozwiązywania problemów w trybie offline można użyć w hybrydowym procesie roboczym elementu Runbook, uruchamiając skrypt lokalnie. Skrypt języka python [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) znajdują się w Centrum skryptów. W poniższym przykładzie przedstawiono przykładowy wynik tego skryptu:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Kolejne kroki

Aby rozwiązać problemy dodatkowe problemy za pomocą hybrydowych procesów roboczych Runbook, zobacz [Rozwiązywanie problemów — hybrydowych procesów roboczych Runbook](hybrid-runbook-worker.md)

