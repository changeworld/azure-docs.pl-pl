---
title: Zamykanie maszyn wirtualnych platformy Azure utknęło w usłudze Ponowne uruchamianie, zamykanie lub zatrzymywanie usług | Dokumenty firmy Microsoft
description: Ten artykuł ułatwia rozwiązywanie problemów z błędami usługi w maszynach wirtualnych systemu Azure Windows.
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: 5d6396efc9ab25baa0d32e7c33c7715863516249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371355"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Zamknięcie maszyny Wirtualnej systemu Azure systemu Windows utknęło na "Ponowne uruchamianie", "Zamykanie" lub "Zatrzymywanie usług"

Ten artykuł zawiera kroki, aby rozwiązać problemy z komunikatami "Ponowne uruchamianie", "Zamykanie" lub "Zatrzymywanie usług", które mogą wystąpić podczas ponownego uruchamiania maszyny wirtualnej systemu Windows (VM) na platformie Microsoft Azure.

## <a name="symptoms"></a>Objawy

Podczas korzystania z [diagnostyki rozruchu,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) aby wyświetlić zrzut ekranu maszyny Wirtualnej, może się okazać, że zrzut ekranu wyświetla komunikat "Ponowne uruchamianie", "Zamykanie" lub "Zatrzymywanie usług".

![Ponowne uruchamianie, zamykanie i zatrzymywanie ekranów usług](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Przyczyna

System Windows używa procesu zamykania do wykonywania operacji konserwacji systemu i przetwarzania zmian, takich jak aktualizacje, role i funkcje. Nie zaleca się przerywania tego krytycznego procesu do czasu jego zakończenia. W zależności od liczby aktualizacji/zmian i rozmiaru maszyny Wirtualnej proces może zająć dużo czasu. Jeśli proces zostanie zatrzymany, jest możliwe, aby system operacyjny został uszkodzony. Przerywaj proces tylko wtedy, gdy trwa zbyt długo.

## <a name="solution"></a>Rozwiązanie

### <a name="collect-a-process-memory-dump"></a>Zbieranie zrzutu pamięci process

1. Pobierz [narzędzie Procdump](http://download.sysinternals.com/files/Procdump.zip) na nowy lub istniejący dysk danych, który jest dołączony do działającej maszyny Wirtualnej z tego samego regionu.

2. Odłącz dysk zawierający pliki potrzebne z działającej maszyny Wirtualnej i dołącz dysk do uszkodzonej maszyny Wirtualnej. Nazywamy ten dysk **dyskiem narzędziowym**.

Użyj [konsoli szeregowej,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) aby wykonać następujące czynności:

1. Otwórz administracyjny program Powershell i sprawdź usługę zawieszoną po zatrzymaniu.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. Na administracyjnej CMD, pobierz PID usługi zawieszone.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Pobierz próbkę zrzutu pamięci <STOPPING SERVICE>z procesu zawieszenia .

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Teraz zabić zawieszony proces, aby odblokować proces zamykania.

   ``
   taskkill /PID <PID> /t /f
   ``

Po ponownym uruchomieniu systemu operacyjnego, jeśli uruchamia się normalnie, po prostu upewnij się, że spójność systemu operacyjnego jest w porządku. Jeśli zostanie zgłoszona uszkodzenie, uruchom następujące polecenie, dopóki dysk nie będzie wolny od uszkodzenia:

``
dism /online /cleanup-image /restorehealth
``

Jeśli nie można zebrać zrzutu pamięci procesu lub ten problem jest cykliczny i wymaga analizy głównej przyczyny, należy przystąpić do zbierania zrzutu pamięci systemu operacyjnego poniżej, przejdź do otwarcia żądania pomocy technicznej.

### <a name="collect-an-os-memory-dump"></a>Zbieranie zrzutu pamięci systemu operacyjnego

Jeśli problem nie zostanie rozwiązany po odczekaniu na proces zmian, należy zebrać plik zrzutu pamięci i skontaktować się z pomocą techniczną. Aby zebrać plik zrzutu, wykonaj następujące kroki:

**Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania**

1. Wykonaj migawkę dysku systemu operacyjnego maszyny wirtualnej, którego dotyczy problem, jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).

2. [Podłącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

3. Pulpit zdalny do maszyny Wirtualnej odzyskiwania.

4. Jeśli dysk systemu operacyjnego jest zaszyfrowany, przed przejściem do następnego kroku należy wyłączyć szyfrowanie. Aby uzyskać więcej informacji, zobacz [Odszyfrowywanie zaszyfrowanego dysku systemu operacyjnego na maszynie wirtualnej, na który nie można uruchomić .](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution)

**Lokalizowanie pliku zrzutu i przesyłanie biletu pomocy technicznej**

1. Na maszynie wirtualnej odzyskiwania przejdź do folderu systemu Windows na podłączonym dysku systemu operacyjnego. Jeśli literą sterownika przypisaną do dołączonego dysku systemu operacyjnego jest F, należy przejść do pliku F:\Windows.

2. Zlokalizuj plik memory.dmp, a następnie [prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) z plikiem zrzutu.

Jeśli nie możesz znaleźć pliku zrzutu, przenieś następny krok, aby włączyć dziennik zrzutu i konsolę szeregową.

**Włącz dziennik zrzutu i konsolę szeregową**

Aby włączyć dziennik zrzutu i konsolę szeregową, uruchom następujący skrypt.

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).

2. Uruchom następujący skrypt:

   W tym skrypcie zakładamy, że litera dysku, który jest przypisany do dołączonego dysku systemu operacyjnego jest F. Zamień go z odpowiednią wartością w maszynie wirtualnej.

   ```
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
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. Sprawdź, czy na dysku jest wystarczająco dużo miejsca, aby przydzielić tyle pamięci, ile pamięci RAM, która zależy od rozmiaru wybranego dla tej maszyny Wirtualnej.

4. Jeśli nie ma wystarczającej ilości miejsca lub maszyna wirtualna jest duża (seria G, GS lub E), można zmienić lokalizację, w której zostanie utworzony ten plik i odwołać się do dowolnego innego dysku danych, który jest dołączony do maszyny Wirtualnej. Aby zmienić lokalizację, należy zmienić następujący klawisz:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Odłącz dysk systemu operacyjnego, a następnie podłącz ponownie dysk systemu operacyjnego do maszyny wirtualnej, którego dotyczy problem](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

6. Uruchom maszynę wirtualną i uzyskaj dostęp do konsoli szeregowej.

7. Wybierz pozycję Wyślij przerwanie niemaskalne (NMI), aby wyzwolić zrzut pamięci.

   ![Wyślij przerwanie niemaskowalne](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Ponownie dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania, zbierz plik zrzutu.

## <a name="contact-microsoft-support"></a>Kontaktowanie się z pomocą techniczną firmy Microsoft

Po zebraniu pliku zrzutu skontaktuj się z pomocą techniczną firmy Microsoft, aby ustalić główną przyczynę.
