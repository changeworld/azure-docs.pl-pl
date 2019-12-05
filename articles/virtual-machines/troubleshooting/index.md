---
layout: LandingPage
description: Dowiedz się, jak rozwiązywać problemy z wdrożeniami maszyn wirtualnych.
title: Dokumentacja dotycząca rozwiązywania problemów z usługą Azure Virtual Machines | Microsoft Docs
services: virtual-machines
author: genlin
manager: dcscontentpm
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: bdb459a69557269a20d36f05acc2da502064efb9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851353"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Rozwiązywanie problemów z maszynami wirtualnymi platformy Azure

## <a name="tools-for-troubleshooting"></a>Narzędzia do rozwiązywania problemów

- [Konsola szeregowa](serial-console-overview.md)
- [Diagnostyka rozruchu](boot-diagnostics.md)
- [Windows VM: Attach the OS disk to another VM for troubleshooting (Maszyna wirtualna z systemem Windows: dołączanie dysku twardego systemu operacyjnego do innej maszyny wirtualnej w celu rozwiązania problemów)](troubleshoot-recovery-disks-portal-windows.md)
- [Linux VM: Attach the OS disk to another VM for troubleshooting (Maszyna wirtualna z systemem Linux: dołączanie dysku twardego systemu operacyjnego do innej maszyny wirtualnej w celu rozwiązania problemów)](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>Nie można nawiązać połączenia z maszyną wirtualną

### <a name="windows"></a>Windows

**Typowe rozwiązanie**

- [Resetowanie protokołu RDP](reset-rdp.md)
- [Rozwiązywanie problemów z protokołem RDP](troubleshoot-rdp-connection.md)
- [Szczegółowe rozwiązywanie problemów z protokołem RDP](detailed-troubleshoot-rdp.md)

**Błędy protokołu RDP**

- [Brak serwera licencji](troubleshoot-rdp-no-license-server.md)
- [Wewnętrzne ](Troubleshoot-rdp-internal-error.md)
- [błędy uwierzytelniania](troubleshoot-authentication-error-rdp-vm.md)
- [Rozwiązywanie problemów z konkretnymi błędami](troubleshoot-specific-rdp-errors.md)

**Błąd rozruchu maszyny wirtualnej**

* [Błąd rozruchu funkcji BitLocker](troubleshoot-bitlocker-boot-error.md)
* [W systemie Windows jest wyświetlany komunikat „Sprawdzanie systemu plików” podczas rozruchu](troubleshoot-check-disk-boot-error.md)
* [Błędy z błękitnym ekranem](troubleshoot-common-blue-screen-error.md)
* [Uruchamianie maszyny wirtualnej zatrzymuje się na komunikacie „Przygotowywanie systemu Windows”](troubleshoot-vm-boot-configure-update.md)
* [Błąd „NIEPOWODZENIE USŁUGI O ZNACZENIU KRYTYCZNYM” na niebieskim ekranie](troubleshoot-critical-service-failed-boot-error.md)
* [Problem z ponownym uruchamianiem pętli](troubleshoot-reboot-loop.md)
* [Proces uruchamiania maszyny wirtualnej zablokował się na etapie witryny Windows Update](troubleshoot-stuck-updating-boot-error.md)
* [Maszyna wirtualna uruchamia się w trybie awaryjnym](troubleshoot-rdp-safe-mode.md)

**Inne**
- [Resetowanie hasła maszyny wirtualnej dla maszyny wirtualnej z systemem Windows w trybie offline](reset-local-password-without-agent.md)
- [Resetowanie karty sieciowej po błędzie konfiguracji](reset-network-interface.md)

### <a name="linux"></a>Linux

- [Rozwiązywanie problemów z protokołem SSH](troubleshoot-ssh-connection.md)
- [Szczegółowe rozwiązywanie problemów z protokołem RDP](detailed-troubleshoot-ssh-connection.md)
- [Typowe komunikaty o błędach](error-messages.md)
- [Resetowanie hasła maszyny wirtualnej dla maszyny wirtualnej z systemem Linux w trybie offline](reset-password.md)

## <a name="vm-deployment-issues"></a>Problemy z wdrażaniem maszyny wirtualnej

- [Błędy alokacji](allocation-failure.md)
- Ponowne wdrażanie maszyny wirtualnej
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- Rozwiązywanie problemów z wdrożeniami
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [Nazwy urządzeń ulegają zmianie](troubleshoot-device-names-problems.md)
- [Instalowanie agenta maszyny wirtualnej z systemem Windows w trybie offline](install-vm-agent-offline.md)
- [Ponowne uruchamianie maszyny wirtualnej lub zmienianie jej rozmiaru](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>Problem z wydajnością maszyny wirtualnej
- [Problemy z wydajnością maszyn wirtualnych](performance-diagnostics.md)
- Windows
    - [Korzystanie z narzędzia PerfInsights](how-to-use-perfinsights.md)
    - [Rozszerzenie diagnostyki wydajności](performance-diagnostics-vm-extension.md)
- Linux
    - [Korzystanie z narzędzia PerfInsights](how-to-use-perfinsights-linux.md)