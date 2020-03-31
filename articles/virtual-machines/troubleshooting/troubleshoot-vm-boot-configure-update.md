---
title: Uruchamianie maszyny Wirtualnej jest zablokowany na "Przygotowanie systemu Windows. Nie wyłączaj komputera" na platformie Azure | Dokumenty firmy Microsoft
description: Zapoznaj się z instrukcjami rozwiązywania problemu, w którym uruchamianie maszyny Wirtualnej utknęło w programie "Przygotowanie systemu Windows. Nie wyłączaj komputera”.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: da45e24898bc3b5aead250077af69a61bdb33bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749641"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Uruchamianie maszyny Wirtualnej jest zablokowany na "Przygotowanie systemu Windows. Nie wyłączaj komputera" na platformie Azure

W tym artykule opisano ekrany "Przygotowanie" i "Przygotowanie systemu Windows", które mogą wystąpić podczas uruchamiania maszyny wirtualnej systemu Windows (VM) na platformie Microsoft Azure. Zawiera kroki ułatwiające zbieranie danych dla biletu pomocy technicznej.

 

## <a name="symptoms"></a>Objawy

Maszyna wirtualna systemu Windows nie uruchamia się. Podczas korzystania z **diagnostyki rozruchu,** aby uzyskać zrzut ekranu maszyny Wirtualnej, może się okazać, że maszyna wirtualna wyświetla komunikat "Przygotowanie" lub "Przygotowanie systemu Windows".

![Przykład komunikatu dla systemu Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Przykład wiadomości](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Przyczyna

Zwykle ten problem występuje, gdy serwer robi ostatecznego ponownego uruchomienia po zmianie konfiguracji. Zmiana konfiguracji może zostać zainicjowana przez aktualizacje systemu Windows lub przez zmiany ról/funkcji serwera. W przypadku usługi Windows Update, jeśli rozmiar aktualizacji był duży, system operacyjny potrzebuje więcej czasu na ponowne skonfigurowanie zmian.

## <a name="collect-an-os-memory-dump"></a>Zbieranie zrzutu pamięci systemu operacyjnego

Jeśli problem nie zostanie rozwiązany po odczekaniu na proces zmian, należy zebrać plik zrzutu pamięci i skontaktować się z pomocą techniczną. Aby zebrać plik zrzutu, wykonaj następujące kroki:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania

1. Wykonaj migawkę dysku systemu operacyjnego maszyny wirtualnej, którego dotyczy problem, jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md).
2. [Podłącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md).
3. Pulpit zdalny do maszyny Wirtualnej odzyskiwania. 
4. Jeśli dysk systemu operacyjnego jest zaszyfrowany, przed przejściem do następnego kroku należy wyłączyć szyfrowanie. Aby uzyskać więcej informacji, zobacz [Odszyfrowywanie zaszyfrowanego dysku systemu operacyjnego na maszynie wirtualnej, na który nie można uruchomić .](troubleshoot-bitlocker-boot-error.md#solution)

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
4. Uruchom maszynę wirtualną i uzyskaj dostęp do konsoli szeregowej.
5. Wybierz **pozycję Wyślij przerwanie niemaskalne (NMI),** aby wyzwolić zrzut pamięci.
    ![obraz o tym, gdzie wysłać niemaskowanie Przerwania](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Ponownie dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania, zbierz plik zrzutu.

## <a name="contact-microsoft-support"></a>Kontaktowanie się z pomocą techniczną firmy Microsoft

Po zebraniu pliku zrzutu skontaktuj się z [pomocą techniczną firmy Microsoft,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby przeanalizować główną przyczynę.