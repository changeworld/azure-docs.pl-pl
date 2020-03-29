---
title: Sprawdzanie systemu plików podczas uruchamiania maszyny Wirtualnej platformy Azure| Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać problem, który podczas uruchamiania pokazuje system sprawdzania systemu plików Checking? Dokumenty firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 86938c582745cb0759eda9cd0693f407471a0529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921491"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>System Windows pokazuje "sprawdzanie systemu plików" podczas uruchamiania maszyny Wirtualnej platformy Azure

W tym artykule opisano błąd "Sprawdzanie systemu plików", który może wystąpić podczas uruchamiania maszyny wirtualnej systemu Windows (VM) na platformie Microsoft Azure.


## <a name="symptom"></a>Objaw 

Maszyna wirtualna systemu Windows nie uruchamia się. Po sprawdzeniu zrzutów ekranu rozruchu w [diagnostyce rozruchu](boot-diagnostics.md)widać, że proces sprawdź dysk (chkdsk.exe) jest uruchomiony z jednym z następujących komunikatów:

- Napęd skanujący i naprawczy (C:)
- Sprawdzanie systemu plików w języku C:

## <a name="cause"></a>Przyczyna

Jeśli w systemie plików zostanie znaleziony błąd NTFS, system Windows sprawdzi i naprawi spójność dysku przy następnym ponownym uruchomieniu. Zwykle dzieje się tak, jeśli maszyna wirtualna miała nieoczekiwane ponowne uruchomienie lub jeśli proces zamykania maszyny Wirtualnej został nagle przerwany.

## <a name="solution"></a>Rozwiązanie 

System Windows uruchomi się normalnie po zakończeniu procesu Sprawdź dysk. Jeśli maszyna wirtualna utknęła w procesie Sprawdź dysk, spróbuj uruchomić check disk na maszynie Wirtualnej w trybie offline:
1.  Wykonaj migawkę dysku systemu operacyjnego maszyny wirtualnej, którego dotyczy problem, jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md).
2.  [Podłącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).  
3.  Na maszynie wirtualnej odzyskiwania uruchom program Check Disk na podłączonym dysku systemu operacyjnego. W poniższym przykładzie literą sterownika dołączonego dysku systemu operacyjnego jest E: 
        
        chkdsk E: /f
4.  Po zakończeniu funkcji Sprawdź dysk odłącz dysk od maszyny wirtualnej odzyskiwania, a następnie ponownie dołącz dysk do maszyny Wirtualnej, którego dotyczy problem, jako dysk systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z maszyną wirtualną systemu Windows, dołączając dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).
