---
title: Rozwiązywanie problemów z rozszerzenia Azure Log Analytics VM Extension w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Opisz objawy, przyczyny i rozwiązania dla typowych problemów z rozszerzeniem maszyny Wirtualnej programu Log Analytics dla Windows i maszyn wirtualnych platformy Azure z systemem Linux.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2019
ms.author: magoedte
ms.openlocfilehash: dd5e0749116ef335887ea634b9d2790c63bf171d
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66751929"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Rozwiązywanie problemów z rozszerzenia maszyny Wirtualnej programu Log Analytics w usłudze Azure Monitor
Ten artykuł zawiera pomocy w rozwiązywaniu problemów błędy mogą wystąpić przy użyciu rozszerzenia maszyny Wirtualnej programu Log Analytics dla Windows i Linux maszyn wirtualnych z systemem Microsoft Azure i sugeruje możliwe rozwiązania, aby je rozwiązać.

Aby sprawdzić stan rozszerzenia, należy wykonać następujące kroki w witrynie Azure portal.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
2. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **maszyn wirtualnych**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **maszyn wirtualnych**.
3. Na liście maszyn wirtualnych Znajdź i zaznacz go.
3. Na maszynie wirtualnej, kliknij przycisk **rozszerzenia**.
4. Z listy Sprawdź, czy rozszerzenie usługi Log Analytics jest włączony, czy nie.  W przypadku systemu Linux agent jest wymieniony jako **OMSAgentforLinux** i dla Windows, agent jest wyświetlany jako **MicrosoftMonitoringAgent**.

   ![Widok rozszerzenia maszyny Wirtualnej](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Kliknij rozszerzenie Aby wyświetlić szczegóły. 

   ![Szczegóły rozszerzenia maszyny Wirtualnej](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Rozwiązywanie problemów z rozszerzeniem maszyny Wirtualnej Windows Azure

Jeśli *Microsoft Monitoring Agent* nie instaluje rozszerzenia maszyny Wirtualnej lub raportowania, można wykonać poniższe kroki, aby rozwiązać ten problem.

1. Sprawdź, czy agent maszyny Wirtualnej platformy Azure jest zainstalowany i działa poprawnie przy użyciu kroków w [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Możesz też przejrzeć plik dziennika agenta maszyny Wirtualnej `C:\WindowsAzure\logs\WaAppAgent.log`
   * Jeśli dziennika nie istnieje, nie jest zainstalowany agent maszyny Wirtualnej.
   * [Zainstaluj agenta maszyny Wirtualnej platformy Azure](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Przejrzyj pliki dziennika rozszerzeń maszyn wirtualnych firmy Microsoft Monitoring Agent w `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Upewnij się, że maszyna wirtualna może uruchamiać skrypty programu PowerShell
4. Upewnij się, że nie zostały zmienione uprawnienia C:\Windows\temp
5. Wyświetlanie stanu programu Microsoft Monitoring Agent, wpisując następujące polecenie w oknie programu PowerShell z podwyższonym poziomem uprawnień na maszynie wirtualnej `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Przejrzyj pliki dziennika instalacji programu Microsoft Monitoring Agent w `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z rozszerzeniami Windows](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Rozwiązywanie problemów z rozszerzenia maszyny Wirtualnej systemu Linux
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Jeśli *agenta usługi Log Analytics dla systemu Linux* nie instaluje rozszerzenia maszyny Wirtualnej lub raportowania, można wykonać poniższe kroki, aby rozwiązać ten problem.

1. Jeśli stan rozszerzenia jest *nieznany* Sprawdź, czy agent maszyny Wirtualnej platformy Azure jest zainstalowany i działa poprawnie, przeglądając plik dziennika agenta maszyny Wirtualnej `/var/log/waagent.log`
   * Jeśli dziennika nie istnieje, nie jest zainstalowany agent maszyny Wirtualnej.
   * [Zainstaluj agenta maszyny Wirtualnej platformy Azure na maszynach wirtualnych systemu Linux](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Inne stany złej kondycji, znaleźć agenta usługi Log Analytics dla rozszerzenia maszyny Wirtualnej systemu Linux pliki dziennika `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` i `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Jeśli rozszerzenie jest w dobrej kondycji, ale nie można przekazać danych zapoznaj się z agenta usługi Log Analytics dla plików dziennika systemu Linux w `/var/opt/microsoft/omsagent/log/omsagent.log`

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z rozszerzeniami Linux](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać dodatkowe wskazówki dotyczące rozwiązywania problemów związanych z agentem usługi Log Analytics dla systemu Linux na komputerach spoza platformy Azure, zobacz [Rozwiązywanie problemów z usługi Azure Log Analytics Linux Agent](agent-linux-troubleshoot.md).  
