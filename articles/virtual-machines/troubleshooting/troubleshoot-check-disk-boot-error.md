---
title: Sprawdzanie systemu plików, podczas rozruchu maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać ten problem, czy maszyna wirtualna Pokaż sprawdzanie systemu plików, podczas rozruchu | Dokumentacja firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 51a97443f6b9ba2a37fa2db708b8520a9c450000
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594802"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Pokazuje Windows "Sprawdzanie systemu plików" podczas rozruchu maszyny Wirtualnej platformy Azure

W tym artykule opisano błędu "Sprawdzanie systemu plików", które można napotkać podczas rozruchu Windows maszyn wirtualnych (VM) w systemie Microsoft Azure.

> [!NOTE] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager, w którym firma Microsoft zaleca używanie w przypadku nowych wdrożeń zamiast klasycznego modelu wdrażania.

## <a name="symptom"></a>Objaw 

Nie zaczyna się maszyny Wirtualnej z systemem Windows. Podczas ewidencjonowania zrzuty ekranu rozruchu [diagnostykę rozruchu](boot-diagnostics.md), zobaczysz, że proces Sprawdź dysk (chkdsk.exe) jest uruchomiony przy użyciu jednego z następujących komunikatów:

- Skanowania i naprawy dysku (C:)
- Sprawdzanie systemu plików na dysku C:

## <a name="cause"></a>Przyczyna

Jeśli zostanie znaleziony błąd systemu plików NTFS w systemie plików, Windows będzie sprawdzania i naprawiania spójność dysku przy następnym ponownym uruchomieniu. Zwykle dzieje się tak, czy maszyna wirtualna ma wszelkie nieoczekiwane ponowne uruchomienie, czy procesu zamykania maszyny Wirtualnej zostało nagle przerwane.

## <a name="solution"></a>Rozwiązanie 

Windows będzie normalny rozruch po ukończeniu procesu sprawdzanie dysku. Jeśli maszyna wirtualna utkwiła w procesie sprawdzanie dysku, spróbuj uruchomić sprawdzanie dysku na maszynie Wirtualnej w trybie offline:
1.  Utworzenie migawki dysku systemu operacyjnego, których to dotyczy maszyny wirtualnej służy do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).
2.  [Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](troubleshoot-recovery-disks-portal-windows.md).  
3.  Na maszynie Wirtualnej odzyskiwania należy uruchomić sprawdzanie dysku na dołączonym dysku systemu operacyjnego. W poniższym przykładzie literę z dołączonym dysku systemu operacyjnego jest E: 
        
        chkdsk E: /f
4.  Po zakończeniu sprawdź dysk, Odłącz dysk od maszyny Wirtualnej odzyskiwania, a następnie ponownie Dołącz dysku, do których to dotyczy maszyny Wirtualnej jako dysk systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z maszyny Wirtualnej z systemem Windows, dołączając dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](troubleshoot-recovery-disks-portal-windows.md).
