---
title: Przygotowywanie i dostosowywanie głównego obrazu VHD — Azure
description: Jak przygotować, dostosować i przekazać obraz wzorcowy pulpitu wirtualnego systemu Windows na platformę Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 867b327ac25d51cd3955e622da9d8067ae6d9ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127728"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Przygotowywanie i dostosowywanie głównego obrazu wirtualnego dysku twardego

W tym artykule opisano, jak przygotować obraz głównego wirtualnego dysku twardego (VHD) do przekazania na platformę Azure, w tym jak tworzyć maszyny wirtualne (maszyny wirtualne) i instalować na nich oprogramowanie. Te instrukcje są przeznaczone dla konfiguracji specyficznej dla pulpitu wirtualnego systemu Windows, która może być używana z istniejącymi procesami organizacji.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Wielosesyjna sesja systemu Windows 10 Enterprise jest dostępna w Galerii obrazów platformy Azure. Istnieją dwie opcje dostosowywania tego obrazu.

Pierwszą opcją jest aprowiztwo maszyny wirtualnej (VM) na platformie Azure, postępując zgodnie z instrukcjami w [obszarze Tworzenie maszyny wirtualnej z zarządzanego obrazu,](../virtual-machines/windows/create-vm-generalized-managed.md)a następnie przejdź do [przygotowania i instalacji oprogramowania.](set-up-customize-master-image.md#software-preparation-and-installation)

Drugą opcją jest utworzenie obrazu lokalnie przez pobranie obrazu, inicjowanie obsługi administracyjnej maszyny Wirtualnej funkcji Hyper-V i dostosowywanie go do własnych potrzeb, które omówimy w poniższej sekcji.

### <a name="local-image-creation"></a>Tworzenie obrazów lokalnych

Po pobraniu obrazu do lokalizacji lokalnej otwórz **Menedżera funkcji Hyper-V,** aby utworzyć maszynę wirtualną z skopiowaną dyskiem wirtualnym. Poniższe instrukcje są prostą wersją, ale bardziej szczegółowe instrukcje można znaleźć w [obszarze Tworzenie maszyny wirtualnej w programie Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Aby utworzyć maszynę wirtualną ze skopiowanym dyskiem VHD:

1. Otwórz **Kreatora nowej maszyny wirtualnej**.

2. Na stronie Określanie generacji wybierz pozycję **Generacja 1**.

    ![Zrzut ekranu strony Określ generację. Zostanie wybrana opcja "Generacja 1".](media/a41174fd41302a181e46385e1e701975.png)

3. W obszarze Typ punktu kontrolnego wyłącz punkty kontrolne, odznaczając to pole wyboru.

    ![Zrzut ekranu sekcji Typ punktu kontrolnego na stronie Punkty kontrolne.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Można również uruchomić następujące polecenie cmdlet w programie PowerShell, aby wyłączyć punkty kontrolne.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Dysk stały

Jeśli utworzysz maszynę wirtualną z istniejącej dysku wirtualnego, domyślnie utworzy dysk dynamiczny. Można go zmienić na stały dysk, wybierając **pozycję Edytuj dysk...** jak pokazano na poniższej ilustracji. Aby uzyskać bardziej szczegółowe instrukcje, zobacz [Przygotowywanie dysku VHD lub VHDX systemu Windows do przekazania na platformę Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

![Zrzut ekranu przedstawiający opcję Edytuj dysk.](media/35772414b5a0f81f06f54065561d1414.png)

Można również uruchomić następujące polecenie cmdlet programu PowerShell, aby zmienić dysk na stały dysk.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Przygotowanie i instalacja oprogramowania

W tej sekcji opisano sposób przygotowania i instalacji programów FSLogix i Windows Defender, a także kilka podstawowych opcji konfiguracji aplikacji i rejestru obrazu. 

Jeśli instalujesz usługi Office 365 ProPlus i OneDrive na maszynie Wirtualnej, przejdź do [strony Instalowanie pakietu Office na głównym obrazie dysku VHD](install-office-on-wvd-master-image.md) i postępuj zgodnie z instrukcjami, aby zainstalować aplikacje. Po zakończeniu wróć do tego artykułu.

Jeśli użytkownicy muszą uzyskać dostęp do niektórych aplikacji LOB, zaleca się zainstalowanie ich po wykonaniu instrukcji tej sekcji.

### <a name="set-up-user-profile-container-fslogix"></a>Konfigurowanie kontenera profilu użytkownika (FSLogix)

Aby dołączyć kontener FSLogix jako część obrazu, postępuj zgodnie z instrukcjami w [polu Tworzenie kontenera profilu dla puli hostów przy użyciu udziału plików](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Funkcję kontenera FSLogix można przetestować za pomocą [tego przewodnika Szybki start.](/fslogix/configure-cloud-cache-tutorial/)

### <a name="configure-windows-defender"></a>Konfigurowanie usługi Windows Defender

Jeśli usługa Windows Defender jest skonfigurowana na maszynie Wirtualnej, upewnij się, że jest skonfigurowana do nieskanowania całej zawartości plików VHD i VHDX podczas załącznika.

Ta konfiguracja usuwa tylko skanowanie plików VHD i VHDX podczas załącznika, ale nie wpływa na skanowanie w czasie rzeczywistym.

Aby uzyskać bardziej szczegółowe instrukcje dotyczące konfigurowania usługi Windows Defender w systemie Windows Server, zobacz [Konfigurowanie wykluczeń antywirusowych usługi Windows Defender w systemie Windows Server](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/).

Aby dowiedzieć się więcej o konfigurowaniu usługi Windows Defender do wykluczania niektórych plików ze skanowania, zobacz [Konfigurowanie i sprawdzanie poprawności wykluczeń na podstawie rozszerzenia pliku i lokalizacji folderów](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/).

### <a name="disable-automatic-updates"></a>Wyłączanie aktualizacji automatycznych

Aby wyłączyć aktualizacje automatyczne za pośrednictwem lokalnych zasad grupy:

1. Otwórz **szablony\\administracyjne\\Edytora\\zasad grupy lokalnej Windows Components Windows Update**.
2. Kliknij prawym przyciskiem myszy **pozycję Konfiguruj aktualizację automatyczną** i ustaw ją **na Wyłączona**.

Można również uruchomić następujące polecenie w wierszu polecenia, aby wyłączyć aktualizacje automatyczne.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Określ układ Start dla komputerów z systemem Windows 10 (opcjonalnie)

Uruchom to polecenie, aby określić układ Start dla komputerów z systemem Windows 10.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Konfigurowanie przekierowania strefy czasowej

Przekierowanie strefy czasowej można wymusić na poziomie zasad grupy, ponieważ wszystkie maszyny wirtualne w puli hostów są częścią tej samej grupy zabezpieczeń.

Aby przekierować strefy czasowe:

1. Na serwerze usługi Active Directory otwórz **konsolę zarządzania zasadami grupy**.
2. Rozwiń swoją domenę i obiekty zasad grupy.
3. Kliknij prawym przyciskiem myszy **obiekt zasad grupy** utworzony dla ustawień zasad grupy i wybierz polecenie **Edytuj**.
4. W **Edytorze zarządzania zasadami grupy**przejdź do pozycji**Zasady** >  **konfiguracji** > komputera**Szablony administracyjne** > **składników Pulpitu** > **zdalnego Usługi** > pulpitu zdalnego Urządzenia**hosta** > sesji usług**pulpitu zdalnego i przekierowanie zasobów**.
5. Włącz ustawienie **Zezwalaj na przekierowanie strefy czasowej.**

Można również uruchomić to polecenie na obrazie głównym, aby przekierować strefy czasowe:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Wyłącz czujnik pamięci masowej

W przypadku hosta sesji pulpitu wirtualnego systemu Windows, który korzysta z wielu sesji systemu Windows 10 Enterprise lub Windows 10 Enterprise, zalecamy wyłączenie czujnika pamięci masowej. Czujnik pamięci można wyłączyć w menu Ustawienia w obszarze **Magazyn**, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający menu Magazyn w obszarze Ustawienia. Opcja "Czujnik pamięci" jest wyłączona.](media/storagesense.png)

Ustawienie w rejestrze można również zmienić, uruchamiając następujące polecenie:

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Dołącz dodatkową obsługę języka

W tym artykule nie opisano sposobu konfigurowania obsługi języka i pomocy regionalnej. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Dodawanie języków do obrazów systemu Windows](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Funkcje na żądanie](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Funkcje języka i regionu na żądanie (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Inne aplikacje i konfiguracja rejestru

Ta sekcja obejmuje konfigurację aplikacji i systemu operacyjnego. Cała konfiguracja w tej sekcji odbywa się za pomocą wpisów rejestru, które mogą być wykonywane za pomocą narzędzi wiersza polecenia i regedit.

>[!NOTE]
>Można zaimplementować najlepsze rozwiązania w konfiguracji za pomocą obiektów zasad grupy (OBIEKTÓW ZASAD) lub importu rejestru. Administrator może wybrać jedną z opcji na podstawie wymagań organizacji.

Aby uzyskać zbieranie danych telemetrycznych przez Centrum opinii w wielu sesjach systemu Windows 10 Enterprise, uruchom to polecenie:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Uruchom następujące polecenie, aby naprawić awarie programu Watson:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Wprowadź następujące polecenia do edytora rejestru, aby naprawić obsługę rozdzielczości 5k. Należy uruchomić polecenia, zanim będzie można włączyć stos obok siebie.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Przygotowanie obrazu do przekazania na platformę Azure

Po zakończeniu konfiguracji i zainstalowaniu wszystkich aplikacji postępuj zgodnie z instrukcjami w [przygotowanie dysku VHD lub VHDX systemu Windows do przekazania na platformę Azure,](../virtual-machines/windows/prepare-for-upload-vhd-image.md) aby przygotować obraz.

Po przygotowaniu obrazu do przekazania, upewnij się, że maszyna wirtualna pozostaje w stanie off lub cofnięto alokacji.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Przekazywanie obrazu wzorcowego na konto magazynu na platformie Azure

Ta sekcja ma zastosowanie tylko wtedy, gdy obraz główny został utworzony lokalnie.

Poniższe instrukcje dowiesz się, jak przekazać obraz wzorcowy do konta magazynu platformy Azure. Jeśli nie masz jeszcze konta magazynu platformy Azure, postępuj zgodnie z instrukcjami w [tym artykule,](/azure/javascript/tutorial-vscode-static-website-node-03) aby je utworzyć.

1. Konwertuj obraz maszyny Wirtualnej (VHD) na Stały, jeśli jeszcze tego nie zrobiłeś. Jeśli obraz nie zostanie przekonwertowany na Naprawiony, nie możesz go pomyślnie utworzyć.

2. Przekaż dysk VHD do kontenera obiektów blob na koncie magazynu. Narzędzie [Eksplorator magazynu](https://azure.microsoft.com/features/storage-explorer/)można szybko przesłać. Aby dowiedzieć się więcej o narzędziu Eksploratora magazynu, zobacz [ten artykuł](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![Zrzut ekranu przedstawiający okno wyszukiwania narzędzia Do eksploratora usługi Microsoft Azure Storage. Pole wyboru "Przekaż pliki .vhd lub vhdx jako obiekty blob strony (zalecane)" jest zaznaczone.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Następnie przejdź do witryny Azure portal w przeglądarce i wyszukaj hasło "Obrazy". Wyszukiwanie powinno prowadzić do strony **Utwórz obraz,** jak pokazano na poniższym zrzucie ekranu:

    ![Zrzut ekranu strony Tworzenie obrazu w witrynie Azure portal, wypełniona przykładowymi wartościami obrazu.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Po utworzeniu obrazu powinno zostać wyświetlone powiadomienie, takie jak na poniższym zrzucie ekranu:

    ![Zrzut ekranu powiadomienia o "pomyślnie utworzonym obrazie".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz obraz, możesz utworzyć lub zaktualizować pule hostów. Aby dowiedzieć się więcej o tworzeniu i aktualizowaniu pul hostów, zobacz następujące artykuły:

- [Tworzenie puli hostów za pomocą szablonu usługi Azure Resource Manager](create-host-pools-arm-template.md)
- [Samouczek: Tworzenie puli hostów w portalu Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell.md)
- [Tworzenie kontenera profilu dla puli hostów za pomocą udziału plików](create-host-pools-user-profile.md)
- [Konfigurowanie metody równoważenia obciążenia usługi Windows Virtual Desktop](configure-host-pool-load-balancing.md)
