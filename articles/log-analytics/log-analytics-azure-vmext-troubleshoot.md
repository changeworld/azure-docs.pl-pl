---
title: Rozwiązywanie problemów z rozszerzenia maszyny Wirtualnej usługi Analiza dzienników Azure | Dokumentacja firmy Microsoft
description: Opisz objawy, przyczyny i rozwiązania dla typowych problemów z rozszerzeniem wirtualna analizy dziennika systemu Windows i Linux maszynach wirtualnych platformy Azure.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.openlocfilehash: d1e70d8f9fb929e3877c88fd4c1169a0c76ac2a6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29394990"
---
# <a name="troubleshooting-the-log-analytics-vm-extension"></a>Rozwiązywanie problemów z rozszerzenia maszyny Wirtualnej analizy dzienników
W tym artykule zawiera informacje pomocne przy rozwiązywaniu problemów z błędami, mogą wystąpić z rozszerzeniem wirtualna analizy dzienników dla systemu Windows i Linux maszynami wirtualnymi w systemie Microsoft Azure, a także sugeruje możliwe rozwiązania ich rozwiązania.

Aby sprawdzić stan rozszerzenia, wykonaj następujące kroki w portalu Azure.

1. Zaloguj się do [Azure Portal](http://portal.azure.com).
2. W portalu Azure kliknij **wszystkie usługi**. Na liście zasobów wpisz **maszyn wirtualnych**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **maszyn wirtualnych**.
3. Na liście maszyn wirtualnych Znajdź i zaznacz go.
3. Na maszynie wirtualnej, kliknij przycisk **rozszerzenia**.
4. Z listy Sprawdź, czy rozszerzenie Log Analytics jest włączone.  Dla systemu Linux, agent będzie wyświetlany jako **OMSAgentforLinux** i dla systemu Windows, agent będzie wyświetlany jako **MicrosoftMonitoringAgent**.

   ![Widok rozszerzenia maszyny Wirtualnej](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Kliknij rozszerzenie, aby wyświetlić szczegóły. 

   ![Szczegóły rozszerzenia maszyny Wirtualnej](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Rozwiązywanie problemów z rozszerzenia maszyny Wirtualnej systemu Windows Azure

Jeśli *programu Microsoft Monitoring Agent* nie instaluje rozszerzenia maszyny Wirtualnej lub raporty, można wykonać następujące kroki, aby rozwiązać ten problem.

1. Sprawdź, czy agent maszyny Wirtualnej platformy Azure jest zainstalowany i działa prawidłowo przy użyciu kroków w [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Można również przejrzeć plik dziennika agenta maszyny Wirtualnej `C:\WindowsAzure\logs\WaAppAgent.log`
   * Jeśli dziennika nie istnieje, nie jest zainstalowany agent maszyny Wirtualnej.
   * [Zainstaluj agenta maszyny Wirtualnej platformy Azure](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Upewnij się, że zadanie pulsu rozszerzenie programu Microsoft Monitoring Agent działa, wykonując następujące czynności:
   * Zaloguj się do maszyny wirtualnej
   * Otwórz Harmonogram zadań i Znajdź `update_azureoperationalinsight_agent_heartbeat` zadań
   * Potwierdź zadania jest włączona i działa co minutę.
   * Sprawdź w pliku dziennika pulsu `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Przejrzyj pliki dziennika rozszerzenia maszyny Wirtualnej agenta monitorowania firmy Microsoft w `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
4. Upewnij się, że maszyna wirtualna może uruchamiać skrypty programu PowerShell
5. Upewnij się, że nie zostały zmienione uprawnienia C:\Windows\temp
6. Wyświetlanie stanu programu Microsoft Monitoring Agent, wpisując następujące polecenie w oknie programu PowerShell z podwyższonym poziomem uprawnień na maszynie wirtualnej `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. Przejrzyj pliki dziennika instalacji programu Microsoft Monitoring Agent w `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z rozszerzeń systemu Windows](../virtual-machines/windows/extensions-oms.md).

## <a name="troubleshooting-linux-vm-extension"></a>Rozwiązywanie problemów z rozszerzenia maszyny Wirtualnej systemu Linux
Jeśli *Agent pakietu OMS Linux* nie instaluje rozszerzenia maszyny Wirtualnej lub raporty, można wykonać następujące kroki, aby rozwiązać ten problem.

1. Jeśli stan rozszerzenia *nieznany* Sprawdź, czy agent maszyny Wirtualnej platformy Azure jest zainstalowany i działa poprawnie, przeglądając plik dziennika agenta maszyny Wirtualnej `/var/log/waagent.log`
   * Jeśli dziennika nie istnieje, nie jest zainstalowany agent maszyny Wirtualnej.
   * [Zainstaluj agenta maszyny Wirtualnej platformy Azure na maszynach wirtualnych systemu Linux](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Dla innych stanów złej kondycji, przejrzyj Agent pakietu OMS dla rozszerzenia maszyny Wirtualnej systemu Linux pliki dziennika `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` i `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Jeśli stan rozszerzenia jest w dobrej kondycji, ale nie można przekazać danych Przejrzyj Agent pakietu OMS dla plików dziennika systemu Linux `/var/opt/microsoft/omsagent/log/omsagent.log`

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z rozszerzeniem Linux](../virtual-machines/linux/extensions-oms.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać dodatkowe wskazówki dotyczące rozwiązywania problemów związanych z agentem pakietu OMS dla systemu Linux na komputerach poza platformą Azure, zobacz [Rozwiązywanie problemów z agenta systemu Linux Analiza dzienników Azure](log-analytics-agent-linux-support.md).  
