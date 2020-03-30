---
title: Rozwiązywanie problemów z rozszerzeniem maszyny Wirtualnej usługi Azure Log Analytics
description: Opisz symptomy, przyczyny i rozwiązanie najczęstszych problemów z rozszerzeniem maszyny wirtualnej usługi Log Analytics dla maszyn wirtualnych systemu Windows i Linux Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: e16531484505f055c1383aff5adb40518719d98a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054575"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej usługi Log Analytics w usłudze Azure Monitor
Ten artykuł zawiera pomoc w rozwiązywaniu problemów z błędami, które mogą wystąpić z rozszerzeniem maszyny wirtualnej usługi Log Analytics dla maszyn wirtualnych z systemem Windows i Linux uruchomionych na platformie Microsoft Azure i sugeruje możliwe rozwiązania umożliwiające ich rozwiązanie.

Aby zweryfikować stan rozszerzenia, wykonaj następujące kroki z witryny Azure portal.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
2. W witrynie Azure portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz typ **maszyny wirtualne**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Maszyny wirtualne**.
3. Na liście maszyn wirtualnych znajdź ją i wybierz.
3. Na maszynie wirtualnej kliknij pozycję **Rozszerzenia**.
4. Na liście sprawdź, czy rozszerzenie usługi Log Analytics jest włączone, czy nie.  W przypadku systemu Linux agent jest wymieniony jako **OMSAgentforLinux,** a dla systemu Windows agent jest wymieniony jako **MicrosoftMonitoringAgent**.

   ![Widok rozszerzenia maszyny Wirtualnej](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Kliknij rozszerzenie, aby wyświetlić szczegóły. 

   ![Szczegóły rozszerzenia maszyny Wirtualnej](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Rozwiązywanie problemów z rozszerzeniem maszyny Wirtualnej systemu Windows platformy Azure

Jeśli rozszerzenie maszyny Wirtualnej *agenta monitorowania firmy Microsoft* nie instaluje się lub nie zgłasza, można wykonać następujące kroki, aby rozwiązać ten problem.

1. Sprawdź, czy agent maszyny Wirtualnej platformy Azure jest zainstalowany i działa poprawnie, wykonując kroki opisane w [kb 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Można również przejrzeć plik dziennika agenta maszyny Wirtualnej`C:\WindowsAzure\logs\WaAppAgent.log`
   * Jeśli dziennik nie istnieje, agent maszyny Wirtualnej nie jest zainstalowany.
   * [Instalowanie agenta maszyny wirtualnej platformy Azure](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Przeglądanie plików dziennika rozszerzeń programu Microsoft Monitoring Agent VM w`C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Upewnij się, że maszyna wirtualna może uruchamiać skrypty programu PowerShell
4. Upewnij się, że uprawnienia do C:\Windows\temp nie zostały zmienione
5. Wyświetlanie stanu agenta monitorowania firmy Microsoft przez wpisanie następujących wpisów w podwyższonym zakresie okna programu PowerShell na maszynie wirtualnej`(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Przeglądanie plików dziennika konfiguracji programu Microsoft Monitoring Agent`C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Aby uzyskać więcej informacji, zobacz [rozwiązywanie problemów z rozszerzeniami systemu Windows](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Rozwiązywanie problemów z rozszerzeniem maszyny Wirtualnej systemu Linux
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Jeśli *agent usługi Log Analytics dla* rozszerzenia maszyny Wirtualnej systemu Linux nie jest instalowanie lub raportowanie, można wykonać następujące kroki, aby rozwiązać problem.

1. Jeśli stan rozszerzenia jest *nieznany,* sprawdź, czy agent maszyny Wirtualnej platformy Azure jest zainstalowany i działa poprawnie, przeglądając plik dziennika agenta maszyny Wirtualnej`/var/log/waagent.log`
   * Jeśli dziennik nie istnieje, agent maszyny Wirtualnej nie jest zainstalowany.
   * [Instalowanie agenta maszyn wirtualnych platformy Azure na maszynach wirtualnych z systemem Linux](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. W przypadku innych stanów w złej kondycji przejrzyj agenta usługi Log Analytics dla rozszerzenia maszyny Wirtualnej systemu Linux i `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log``/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Jeśli stan rozszerzenia jest w dobrej kondycji, ale dane nie są przekazywane, przejrzyj agenta usługi Log Analytics dla plików dziennika systemu Linux w`/var/opt/microsoft/omsagent/log/omsagent.log`

Aby uzyskać więcej informacji, zobacz [rozwiązywanie problemów z rozszerzeniami systemu Linux](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe wskazówki dotyczące rozwiązywania problemów związanych z agentem usługi Log Analytics dla systemu Linux hostowanym na komputerach spoza platformy Azure, zobacz [Rozwiązywanie problemów z agentem systemu Linux usługi Azure Log Analytics](agent-linux-troubleshoot.md).  
