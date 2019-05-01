---
title: Niebieski błędy podczas rozruchu maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać ten problem, błękitny ekran jest błąd podczas rozruchu | Dokumentacja firmy Microsoft
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
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: 26306489b11e24ab50f0ae893f11137d279c6127
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64719794"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows pokazuje błękitny ekran błędu podczas rozruchu maszyny Wirtualnej platformy Azure
W tym artykule opisano niebieskich ekranów, które można napotkać podczas rozruchu Windows maszyn wirtualnych (VM) w systemie Microsoft Azure. Zapewnia kroki ułatwiające zbieranie danych dla biletu pomocy technicznej. 

> [!NOTE] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager, w którym firma Microsoft zaleca używanie w przypadku nowych wdrożeń zamiast klasycznego modelu wdrażania.

## <a name="symptom"></a>Objaw 

Nie zaczyna się maszyny Wirtualnej z systemem Windows. Podczas ewidencjonowania zrzuty ekranu rozruchu [diagnostykę rozruchu](./boot-diagnostics.md), zostanie wyświetlony jeden z następujących komunikatów o błędach w błękitny ekran:

- nasz komputer napotkał problem i musi ponownie uruchomić. Trwa zbieranie danych tylko niektóre informacje o błędzie, a następnie uruchomisz ponownie.
- Komputer PC napotkał problem i musi ponownie uruchomić.

W tej sekcji przedstawiono typowe komunikaty o błędach, które można napotkać podczas zarządzania maszynami wirtualnymi:

## <a name="cause"></a>Przyczyna

Może istnieć kilka przyczyn jako Dlaczego otrzymamy błędu zatrzymania. Najbardziej typowe przyczyny są następujące:

- Problem ze sterownikiem
- Uszkodzeniem systemu plików lub ilości pamięci
- Aplikacja uzyskuje dostęp do zabronione sektora pamięci

## <a name="collect-memory-dump-file"></a>Zbieranie pliku zrzutu pamięci

Aby rozwiązać ten problem, będziesz potrzebować pierwszy do gromadzenia plik zrzutu na potrzeby tej awarii, a następnie skontaktuj się z pomocą techniczną przy użyciu pliku zrzutu. Aby zebrać plik zrzutu, wykonaj następujące kroki:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania

1. Utworzenie migawki dysku systemu operacyjnego, których to dotyczy maszyny wirtualnej służy do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).
2. [Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](../windows/troubleshoot-recovery-disks-portal.md). 
3. Pulpit zdalny dla maszyny Wirtualnej odzyskiwania.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Zlokalizuj plik zrzutu i Wyślij bilet pomocy technicznej

1. Na maszynie Wirtualnej odzyskiwania przejdź do folderu systemu windows w dołączonym dysku systemu operacyjnego. Litera sterownika, która jest przypisana do dołączonym dysku systemu operacyjnego jest F, musisz przejść do F:\Windows.
2. Znajdź plik memory.dmp, a następnie [Wyślij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) przy użyciu pliku zrzutu. 

Jeśli nie można odnaleźć pliku zrzutu, Przenieś do następnego kroku, aby włączyć dziennik zrzutu i konsoli szeregowej.

### <a name="enable-dump-log-and-serial-console"></a>Włącz dziennik zrzutu i konsoli szeregowej

Aby włączyć dziennik zrzutu i konsoli szeregowej, uruchom następujący skrypt.

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom następujący skrypt:

    W tym skrypcie przyjęto założenie, że litery dysku, która jest przypisana do dołączonym dysku systemu operacyjnego jest F.  Zastąp go odpowiednią wartość w maszynie Wirtualnej.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Upewnij się, że jest wystarczająca ilość miejsca na dysku przydzielić tak dużej ilości pamięci jako pamięci RAM, która zależy od rozmiaru, który wybierasz dla tej maszyny Wirtualnej.
    2. Jeśli nie ma wystarczającej ilości miejsca lub jest to duży rozmiar maszyn wirtualnych (serii G i GS E), można następnie zmienić lokalizację, w którym ten plik zostanie utworzony i które dotyczą innych dysków danych, który jest dołączony do maszyny Wirtualnej. Aby to zrobić, należy zmienić następującego klucza:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Odłącz dysk systemu operacyjnego, a następnie ponownie Dołącz dysk systemu operacyjnego, do których to dotyczy maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md).
4. Uruchom maszynę Wirtualną do odtworzenia problemu, a następnie zostanie wygenerowany plik zrzutu.
5. Dołącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej, plik zrzutu zbieranie, a następnie [Wyślij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) przy użyciu pliku zrzutu.



