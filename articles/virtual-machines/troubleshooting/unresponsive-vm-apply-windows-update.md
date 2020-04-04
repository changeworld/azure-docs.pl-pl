---
title: Maszyna wirtualna platformy Azure nie odpowiada z powodu błędu C01A001D podczas stosowania usługi Windows Update
description: Ten artykuł zawiera kroki, aby rozwiązać problemy, w przypadku gdy aktualizacja systemu Windows generuje błąd i przestaje odpowiadać na maszynie Wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 16c8eed3377c2191b4345ec59ec1eba8be01369d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633959"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>Maszyna wirtualna nie odpowiada z powodu błędu "C01A001D" podczas stosowania usługi Windows Update

Ten artykuł zawiera kroki w celu rozwiązania problemów, w których usługa Windows Update (KB) generuje błąd i przestaje odpowiadać na maszynie Wirtualnej platformy Azure.

## <a name="symptoms"></a>Objawy

Podczas korzystania z [diagnostyki rozruchu,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) aby wyświetlić zrzut ekranu maszyny Wirtualnej, windows update (KB) w toku jest wyświetlany, ale kończy się niepowodzeniem z kodem błędu: "C01A001D".

![Nie odpowiada usługa Windows Update](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Przyczyna

Nie można utworzyć pliku podstawowego w systemie plików. System operacyjny nie może zapisywać plików na dysku.

## <a name="resolution"></a>Rozwiązanie

### <a name="process-overview"></a>Omówienie procesu

1. [Tworzenie i uzyskiwanie dostępu do naprawy maszyny Wirtualnej](#create-and-access-a-repair-vm).
2. [Zwolnić miejsce na dysku twardym](#free-up-space-on-the-hard-disk).
3. [Zalecane: Przed przebudową maszyny Wirtualnej włącz zbieranie konsoli szeregowej i zrzutu pamięci](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. [Odbuduj maszynę wirtualną](#rebuild-the-vm).

> [!NOTE]
> W przypadku wystąpienia tego błędu system operacyjny gościa nie działa. Aby rozwiązać ten problem, należy rozwiązać ten problem w trybie offline.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie i uzyskiwanie dostępu do naprawionej maszyny Wirtualnej

1. Wykonaj [kroki 1-3 poleceń naprawy maszyny Wirtualnej,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) aby przygotować maszynę wirtualną naprawy.
2. Połącz się z maszyną wirtualną naprawy przy użyciu połączenia pulpitu zdalnego.

### <a name="free-up-space-on-the-hard-disk"></a>Zwalnianie miejsca na dysku twardym

Jeśli dysk nie jest jeszcze 1 Tb, należy zmienić jego rozmiar. Gdy dysk ma 1 TB, wykonaj oczyszczanie dysku i defragmentację dysku.

1. Sprawdź, czy dysk jest zapełniony. Jeśli dysk jest poniżej 1 Tb, [rozwiń go do maksymalnie 1 Tb za pomocą programu PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
2. Gdy dysk jest 1 Tb, należy wykonać oczyszczanie dysku.
    - [Odłącz dysk danych od uszkodzonej maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk).
    - [Dołącz dysk danych do działającej maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm).
    - Użyj [narzędzia Oczyszczanie dysku,](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) aby zwolnić miejsce.
3. Po zdestrukturyzowaniu i oczyszczeniu zdefragmentuj dysk:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    W zależności od poziomu fragmentacji może to potrwać wiele godzin.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Zalecane: Przed przebudową maszyny Wirtualnej włącz zbieranie konsoli szeregowej i zrzutu pamięci

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom następujące polecenia:

    Włącz konsolę szeregową:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Upewnij się, że wolne miejsce na dysku systemu operacyjnego jest co najmniej równe rozmiarowi pamięci ram maszyny Wirtualnej.

    Jeśli na dysku systemu operacyjnego nie ma wystarczającej ilości miejsca, zmień lokalizację, w której zostanie utworzony plik zrzutu pamięci, i skieruj go na dysk danych dołączony do maszyny Wirtualnej i z wystarczającą ilością wolnego miejsca. Aby zmienić lokalizację, zastąp `%SystemRoot%` literą dysku (na przykład "F:") dysku danych w poniższych poleceniach:

    **Włącz sugerowaną konfigurację zrzutu systemu operacyjnego:**

    Obciążenie uszkodzonego dysku systemu operacyjnego:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Włącz na ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Włącz na ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Rozładuj uszkodzony dysk systemu operacyjnego:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Odbuduj maszynę wirtualną

Użyj [kroku 5 poleceń naprawy maszyny Wirtualnej,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) aby ponownie zmontować maszynę wirtualną.
