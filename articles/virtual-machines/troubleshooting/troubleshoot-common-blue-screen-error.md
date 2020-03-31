---
title: Błędy niebieskiego ekranu podczas uruchamiania maszyny Wirtualnej platformy Azure| Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać problem, który został odebrany podczas uruchamiania błędu niebieskiego ekranu| Dokumenty firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: beb1562738699bbcede58d8214e69342abbb7c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266938"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>System Windows wyświetla błąd niebieskiego ekranu podczas uruchamiania maszyny Wirtualnej platformy Azure
W tym artykule opisano błędy niebieskiego ekranu, które mogą wystąpić podczas uruchamiania maszyny wirtualnej systemu Windows (VM) na platformie Microsoft Azure. Zawiera kroki ułatwiające zbieranie danych dla biletu pomocy technicznej. 


## <a name="symptom"></a>Objaw 

Maszyna wirtualna systemu Windows nie uruchamia się. Po sprawdzeniu zrzutów ekranu rozruchu w [diagnostyce rozruchu](./boot-diagnostics.md)na niebieskim ekranie zostanie wyświetlony jeden z następujących komunikatów o błędach:

- nasz komputer wpadł na problem i musi ponownie uruchomić. Po prostu zbieramy informacje o błędach, a następnie możesz ponownie uruchomić komputer.
- Komputer napotkał problem i musi zostać ponownie uruchomiony.

W tej sekcji wymieniono typowe komunikaty o błędach, które mogą wystąpić podczas zarządzania maszynami wirtualnymi:

## <a name="cause"></a>Przyczyna

Może istnieć wiele powodów, dlaczego chcesz uzyskać błąd zatrzymania. Najczęstsze przyczyny to:

- Problem ze sterownikiem
- Uszkodzony plik systemowy lub pamięć
- Aplikacja uzyskuje dostęp do zakazanego sektora pamięci

## <a name="collect-memory-dump-file"></a>Zbieranie pliku zrzutu pamięci

Aby rozwiązać ten problem, należy najpierw zebrać plik zrzutu dla awarii i skontaktować się z pomocą techniczną z pliku zrzutu. Aby zebrać plik zrzutu, wykonaj następujące kroki:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania

1. Wykonaj migawkę dysku systemu operacyjnego maszyny wirtualnej, którego dotyczy problem, jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md).
2. [Podłącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md). 
3. Pulpit zdalny do maszyny Wirtualnej odzyskiwania.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Lokalizowanie pliku zrzutu i przesyłanie biletu pomocy technicznej

1. Na maszynie wirtualnej odzyskiwania przejdź do folderu systemu Windows na podłączonym dysku systemu operacyjnego. Jeśli literą sterownika przypisaną do dołączonego dysku systemu operacyjnego jest F, należy przejść do pliku F:\Windows.
2. Zlokalizuj plik memory.dmp, a następnie [prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) z plikiem zrzutu. 

Jeśli nie możesz znaleźć pliku zrzutu, przenieś następny krok, aby włączyć dziennik zrzutu i konsolę szeregową.

### <a name="enable-dump-log-and-serial-console"></a>Włącz dziennik zrzutu i konsolę szeregową

Aby włączyć dziennik zrzutu i konsolę szeregową, uruchom następujący skrypt.

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom następujący skrypt:

    W tym skrypcie zakładamy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F.  Wymień go na odpowiednią wartość w maszynie wirtualnej.

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

    1. Upewnij się, że na dysku jest wystarczająco dużo miejsca, aby przydzielić tyle pamięci, ile pamięci RAM, która zależy od rozmiaru wybranego dla tej maszyny Wirtualnej.
    2. Jeśli nie ma wystarczającej ilości miejsca lub jest to duża maszyna wirtualna (seria G, GS lub E), można następnie zmienić lokalizację, w której zostanie utworzony ten plik i odwołać się do dowolnego innego dysku danych, który jest dołączony do maszyny Wirtualnej. Aby to zrobić, należy zmienić następujący klucz:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Odłącz dysk systemu operacyjnego, a następnie ponownie podłącz dysk systemu operacyjnego do dotkniętej maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md).
4. Uruchom maszynę wirtualną, aby odtworzyć problem, a następnie zostanie wygenerowany plik zrzutu.
5. Dołącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej, zbierać plik zrzutu, a następnie [przesłać bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) z plikiem zrzutu.



