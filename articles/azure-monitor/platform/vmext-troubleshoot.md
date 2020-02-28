---
title: Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej Log Analytics platformy Azure w Azure Monitor | Microsoft Docs
description: Opisz objawy, przyczyny i rozwiązania dla typowych problemów z rozszerzeniem maszyny Wirtualnej programu Log Analytics dla Windows i maszyn wirtualnych platformy Azure z systemem Linux.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: 88d76fc0c215653cf732ba7b827d82187d738fd9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658476"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej Log Analytics w programie Azure Monitor
Ten artykuł zawiera pomocy w rozwiązywaniu problemów błędy mogą wystąpić przy użyciu rozszerzenia maszyny Wirtualnej programu Log Analytics dla Windows i Linux maszyn wirtualnych z systemem Microsoft Azure i sugeruje możliwe rozwiązania, aby je rozwiązać.

Aby sprawdzić stan rozszerzenia, należy wykonać następujące kroki w witrynie Azure portal.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **maszyny wirtualne**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **maszyny wirtualne**.
3. Na liście maszyn wirtualnych Znajdź i zaznacz go.
3. Na maszynie wirtualnej kliknij pozycję **rozszerzenia**.
4. Z listy Sprawdź, czy rozszerzenie usługi Log Analytics jest włączony, czy nie.  W przypadku systemu Linux Agent jest wymieniony jako **OMSAgentforLinux** i w systemie Windows Agent jest wymieniony jako **MicrosoftMonitoringAgent**.

   ![Widok rozszerzenia maszyny Wirtualnej](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Kliknij rozszerzenie Aby wyświetlić szczegóły. 

   ![Szczegóły rozszerzenia maszyny Wirtualnej](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Rozwiązywanie problemów z rozszerzeniem maszyny Wirtualnej Windows Azure

Jeśli rozszerzenie maszyny wirtualnej *Microsoft Monitoring Agent* nie jest instalowane ani raportowania, można wykonać poniższe kroki, aby rozwiązać problem.

1. Sprawdź, czy Agent maszyny wirtualnej platformy Azure jest zainstalowany i działa poprawnie, wykonując czynności opisane w temacie [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Możesz również przejrzeć plik dziennika agenta maszyny wirtualnej `C:\WindowsAzure\logs\WaAppAgent.log`
   * Jeśli dziennika nie istnieje, nie jest zainstalowany agent maszyny Wirtualnej.
   * [Zainstaluj agenta maszyny wirtualnej platformy Azure](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Przejrzyj pliki dziennika rozszerzenia maszyny wirtualnej Microsoft Monitoring Agent w `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Upewnij się, że maszyna wirtualna może uruchamiać skrypty programu PowerShell
4. Upewnij się, że nie zostały zmienione uprawnienia C:\Windows\temp
5. Aby wyświetlić stan Microsoft Monitoring Agent, wpisz następujące polecenie w oknie programu PowerShell z podwyższonym poziomem uprawnień na maszynie wirtualnej `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Przejrzyj pliki dziennika instalacji Microsoft Monitoring Agent w programie `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z rozszerzeniami systemu Windows](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Rozwiązywanie problemów z rozszerzenia maszyny Wirtualnej systemu Linux
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Jeśli rozszerzenie maszyny wirtualnej *log Analytics Agent dla systemu Linux* nie jest instalowane ani raportowane, można wykonać poniższe kroki, aby rozwiązać problem.

1. Jeśli stan rozszerzenia jest *nieznany* , sprawdź, czy Agent maszyny wirtualnej platformy Azure jest zainstalowany i działa poprawnie, przeglądając plik dziennika agenta maszyny wirtualnej `/var/log/waagent.log`
   * Jeśli dziennika nie istnieje, nie jest zainstalowany agent maszyny Wirtualnej.
   * [Instalowanie agenta maszyny wirtualnej platformy Azure na maszynach wirtualnych z systemem Linux](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. W przypadku innych stanów złej kondycji Przejrzyj pliki dzienników rozszerzenia maszyny wirtualnej Log Analytics Agent dla systemu Linux w `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` i `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Jeśli stan rozszerzenia jest w dobrej kondycji, ale dane nie są przekazywane Przejrzyj pliki dziennika Log Analytics Agent dla systemu Linux w `/var/opt/microsoft/omsagent/log/omsagent.log`

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z rozszerzeniami systemu Linux](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Następne kroki

Dodatkowe wskazówki dotyczące rozwiązywania problemów dotyczące agenta Log Analytics dla systemu Linux hostowanego na komputerach poza platformą Azure można znaleźć w temacie [Rozwiązywanie problemów z agentem usługi azure log Analytics Linux](agent-linux-troubleshoot.md).  
