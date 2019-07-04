---
title: Przygotuj i dostosowywania obrazu wirtualnego dysku twardego master - Azure
description: Jak przygotować, dostosować i przekazać obrazu wzorcowego pulpitu wirtualnego Windows w wersji zapoznawczej na platformie Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2019
ms.author: helohr
ms.openlocfilehash: 186086a94470faaf19fa8a3c07939b9856ae2ec9
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466814"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Przygotowywanie i dostosowywanie głównego obrazu wirtualnego dysku twardego

W tym artykule wyjaśniono, jak przygotować głównego obrazu wirtualnego dysku twardego (VHD) w celu przekazania na platformę Azure, w tym sposobu tworzenia maszyn wirtualnych (VM) i instalować oprogramowanie na nich. Te instrukcje dotyczą konfiguracji specyficznych dla Windows pulpitu wirtualnego (wersja zapoznawcza), który może być używany z istniejącymi procesami w Twojej organizacji.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Wiele sesji systemu Windows 10 Enterprise jest dostępna w galerii obrazów systemu Azure. Dostępne są dwie opcje dostosowywania tego obrazu.

Pierwsza opcja jest aprowizowanie maszyny wirtualnej (VM) na platformie Azure, postępując zgodnie z instrukcjami w [Utwórz Maszynę wirtualną z obrazu zarządzanego](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed), a następnie przejdź do sekcji [przygotowania oprogramowania i instalacji](set-up-customize-master-image.md#software-preparation-and-installation).

Drugą opcją jest do utworzenia obrazu lokalnie przez pobieranie obrazu, inicjowanie obsługi administracyjnej maszyny Wirtualnej funkcji Hyper-V i dostosowania jej do swoich potrzeb, które zostaną przedstawione w poniższej sekcji.

### <a name="local-image-creation"></a>Tworzenie obrazu lokalnego

Po pobraniu obrazu w lokalizacji lokalnej, należy otworzyć **Menedżera funkcji Hyper-V** utworzyć maszynę Wirtualną z dyskiem VHD został skopiowany. Poniższe instrukcje dotyczą wersji proste, ale można znaleźć bardziej szczegółowe instrukcje w [Utwórz maszynę wirtualną funkcji Hyper-v](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).

Aby utworzyć Maszynę wirtualną za pomocą skopiowanego wirtualnego dysku twardego:

1. Otwórz **Kreatora nowej maszyny wirtualnej**.

2. Na stronie Określanie generacji wybierz **generacja 1**.

    ![Zrzut ekranu przedstawiający stronę Określanie generacji. Opcja "Generacji 1" jest zaznaczona.](media/a41174fd41302a181e46385e1e701975.png)

3. W obszarze Typ punktu kontrolnego należy wyłączyć punkty kontrolne, usuwając zaznaczenie pola wyboru.

    ![Zrzut ekranu przedstawiający sekcję typ punktu kontrolnego na stronie punkty kontrolne.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Można również uruchomić następujące polecenie cmdlet programu PowerShell, aby wyłączyć punkty kontrolne.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Dysku stałego

Jeśli tworzysz Maszynę wirtualną z istniejącego wirtualnego dysku twardego, tworzy dysk dynamiczny domyślnie. Można ją zmienić na dysk stały, wybierając **Edytuj dysk...**  jak pokazano na poniższej ilustracji. Aby uzyskać szczegółowe instrukcje, zobacz [przygotowanie Windows dysku VHD lub VHDX można przekazać na platformę Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

![Zrzut ekranu przedstawiający opcję Edytuj dysk.](media/35772414b5a0f81f06f54065561d1414.png)

Można również uruchomić następujące polecenie cmdlet programu PowerShell, aby zmienić dysku na dysk stały.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Przygotowanie oprogramowania i instalacji

W tej sekcji opisano, jak przygotować i zainstaluj FSLogix, usługa Windows Defender i innymi aplikacjami. 

Jeśli instalujesz Office 365 ProPlus oraz usługi OneDrive na maszynie Wirtualnej, zobacz [instalacji pakietu Office dla głównego obrazu wirtualnego dysku twardego](install-office-on-wvd-master-image.md). Skorzystaj z linku w następnych krokach tego artykułu, aby wrócić do tego artykułu i ukończyć wzorca procesu wirtualnego dysku twardego.

Jeśli użytkownicy potrzebują dostępu do niektórych aplikacji biznesowych, zalecamy instalowanie po wykonaniu instrukcji w tej sekcji.

### <a name="disable-automatic-updates"></a>Wyłącz Aktualizacje automatyczne

Aby wyłączyć automatyczne aktualizacje za pomocą lokalnych zasad grupy:

1. Otwórz **Edytora lokalnych zasad grupy\\Szablony administracyjne\\składników Windows\\Windows Update**.
2. Kliknij prawym przyciskiem myszy **Konfigurowanie aktualizacji automatycznych** i ustaw ją na **wyłączone**.

Można również uruchomić następujące polecenie w wierszu polecenia, aby wyłączyć automatyczne aktualizacje.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Określ układ Start 10 komputerów z systemem Windows (opcjonalnie)

Uruchom następujące polecenie, aby określić układ Start dla komputerów z systemem Windows 10.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-user-profile-container-fslogix"></a>Konfigurowanie kontenera profil użytkownika (FSLogix)

Aby dołączyć kontener FSLogix jako część obrazu, postępuj zgodnie z instrukcjami [skonfiguruj udział profilu użytkownika dla puli hosta](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Możesz przetestować funkcje FSLogix kontener za pomocą [ten przewodnik Szybki Start](https://docs.fslogix.com/display/20170529/Profile+Containers+-+Quick+Start).

### <a name="configure-windows-defender"></a>Konfigurowanie usługi Windows Defender

Jeśli usługa Windows Defender jest skonfigurowana w maszynie Wirtualnej, upewnij się, że jej została skonfigurowana do skanowania nie całą zawartość plików VHD i VHDX podczas załącznika.

Ta konfiguracja tylko usuwa skanowanie plików VHD i VHDX podczas załącznik, ale nie mają wpływu na skanowanie w czasie rzeczywistym.

Aby uzyskać szczegółowe instrukcje dotyczące sposobu konfigurowania usługi Windows Defender w systemie Windows Server, zobacz [wykluczenia konfigurowania programu antywirusowego Windows Defender w systemie Windows Server](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus).

Aby dowiedzieć się więcej na temat sposobu konfigurowania usługi Windows Defender, aby wykluczyć pewne pliki ze skanowania, zobacz [Konfigurowanie i weryfikowanie wykluczeń na podstawie lokalizacji pliku rozszerzenie i folder](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus).

### <a name="configure-session-timeout-policies"></a>Konfigurowanie zasad limitu czasu sesji

Zasady sesji zdalnej, mogą być wymuszane na poziomie zasad grupy, ponieważ wszystkie maszyny wirtualne w puli hostów są częścią tej samej grupy zabezpieczeń.

Aby skonfigurować zasady sesji zdalnej:

1. Przejdź do **Szablony administracyjne** > **składników Windows** > **usług pulpitu zdalnego**  >  **Hosta sesji usług pulpitu zdalnego** > **limity czasu sesji**.
2. W panelu po prawej stronie zaznacz **ustawiony limit czasu dla aktywnych, ale bezczynności sesji usług pulpitu zdalnego** zasad.
3. Po pojawi się okno modalne, należy zmienić opcję zasady z **nieskonfigurowane** do **włączone** można aktywować zasad.
4. W menu rozwijanym poniżej opcji zasad, należy ustawić ilość czasu **4 godziny**.

Zasady sesji zdalnego można również skonfigurować ręcznie, uruchamiając następujące polecenia:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fResetBroken /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 10800000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 7200000 /f
```

### <a name="set-up-time-zone-redirection"></a>Konfigurowanie przekierowania strefy czasowej

Ponieważ wszystkie maszyny wirtualne w puli hostów są częścią tej samej grupy zabezpieczeń, można wymusić przekierowanie strefy czasowej na poziomie zasad grupy.

Aby przekierować stref czasowych:

1. Na serwerze usługi Active Directory otwórz **konsoli zarządzania zasadami grupy**.
2. Rozwiń węzeł swojej domeny i obiekty zasad grupy.
3. Kliknij prawym przyciskiem myszy **obiektu zasad grupy** utworzony dla ustawień zasad grupy i wybierz pozycję **Edytuj**.
4. W **Edytor zarządzania zasadami grupy**, przejdź do **konfiguracji komputera** > **zasady** > **administracyjne Szablony** > **składników Windows** > **usług pulpitu zdalnego** > **hosta sesji usług pulpitu zdalnego**   >  **Przekierowywanie urządzeń i zasobów**.
5. Włącz **Zezwalaj na przekierowanie strefy czasowej** ustawienie.

To polecenie można również uruchomić na obrazu wzorcowego przekierowywanie stref czasowych:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Wyłączyć wykrywanie magazynu

Dla hosta sesji pulpitu wirtualnego Windows korzystających z systemu Windows 10 Enterprise lub Windows 10 Enterprise sesji zaleca się wyłączenie wykrywanie magazynu. Wykrywanie magazynu można wyłączyć w menu Ustawienia w obszarze **magazynu**, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający menu magazynu, w obszarze Ustawienia. Opcja "Wykrywanie magazynu" jest wyłączona.](media/storagesense.png)

Możesz również zmienić ustawienie z rejestru, uruchamiając następujące polecenie:

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Zapewnia obsługę dodatkowych języków

W tym artykule nie opisano, jak skonfigurować język i wsparcie regionalne. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Dodaj języki do Windows obrazów](https://docs.microsoft.com/windows-hardware/manufacture/desktop/add-language-packs-to-windows)
- [Funkcje na żądanie](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities)
- [Funkcje języka i regionu na żądanie (FDZ)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

### <a name="other-applications-and-registry-configuration"></a>Inne aplikacje i konfiguracji rejestru

W tej sekcji omówiono aplikacji i konfiguracji systemu operacyjnego. Wszystkie konfiguracje w tej sekcji odbywa się za pośrednictwem wpisów rejestru, które mogą być wykonywane przez wiersza polecenia i narzędzia regedit.

>[!NOTE]
>Najlepsze rozwiązania można wdrożyć w konfiguracji za pomocą obiektów zasad grupy (GPO) lub importuje rejestru. Administrator może wybrać jedną z opcji na podstawie wymagań swojej organizacji.

W przypadku opinii centrum zbierania danych telemetrycznych na wiele sesji systemu Windows 10 Enterprise uruchom następujące polecenie:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Uruchom następujące polecenie, aby naprawić Watson awarii:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Wprowadź następujące polecenia do edytora rejestru, aby naprawić 5 obsługę rozwiązywania k. Aby można było włączyć stosu side-by-side, należy uruchomić polecenia.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Przygotowywanie obrazu do przekazywania na platformie Azure

Po zakończeniu konfiguracji i wszystkie aplikacje zainstalowane, postępuj zgodnie z instrukcjami [przygotowanie Windows dysku VHD lub VHDX można przekazać na platformę Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) Aby przygotować obraz.

Po przygotowaniu obrazu do przekazywania, upewnij się, że maszyna wirtualna pozostaje w stanie wyłączone lub przydział zostanie cofnięty.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Przekazywanie obrazu wzorcowego na konto magazynu na platformie Azure

Ta sekcja dotyczy tylko podczas tworzenia obrazu wzorcowego lokalnie.

Poniżej opisano, jak do przekazania obrazu wzorcowego na konto magazynu platformy Azure. Jeśli nie masz jeszcze konta usługi Azure storage, postępuj zgodnie z instrukcjami [w tym artykule](https://code.visualstudio.com/tutorials/static-website/create-storage) ją utworzyć.

1. Konwertuj obraz maszyny Wirtualnej (VHD) na stałe, jeśli jeszcze go. Jeśli obraz nie Konwertuj na stałe, nie można pomyślnie utworzyć obrazu.

2. Przekazywanie wirtualnego dysku twardego do kontenera obiektów blob na koncie magazynu. Możesz przekazać szybko przy użyciu [narzędzia Eksploratora usługi Storage](https://azure.microsoft.com/features/storage-explorer/). Aby dowiedzieć się więcej o narzędziu do Eksploratora usługi Storage, zobacz [w tym artykule](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![Zrzut ekranu przedstawiający okno Wyszukiwanie narzędzie Eksplorator magazynu platformy Microsoft. Zaznaczono pole wyboru "Przekaż pliki VHD lub vhdx jako stronicowe obiekty BLOB (zalecane)".](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Następnie przejdź do witryny Azure portal w przeglądarce i wyszukaj "Obrazy". Wyszukiwanie powinny prowadzić do **Tworzenie obrazu** strony, jak pokazano na poniższym zrzucie ekranu:

    ![Zrzut ekranu strony tworzenia obrazu w witrynie Azure Portal, wypełniony przykładowe wartości dla obrazu.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Po utworzeniu obrazu powinno zostać wyświetlone powiadomienie, podobne do pokazanego na poniższym zrzucie ekranu:

    ![Zrzut ekranu przedstawiający powiadomienie "Pomyślnie utworzono obraz".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz obrazu, możesz utworzyć lub zaktualizować pul hosta. Aby dowiedzieć się więcej o sposobie tworzenia i aktualizowania pule hosta, zobacz następujące artykuły:

- [Utwórz pulę hosta przy użyciu szablonu usługi Azure Resource Manager](create-host-pools-arm-template.md)
- [Samouczek: Utwórz pulę hosta za pomocą portalu Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Utwórz pulę hosta przy użyciu programu PowerShell](create-host-pools-powershell.md)
- [Skonfiguruj udział profilu użytkownika dla puli hosta](create-host-pools-user-profile.md)
- [Konfigurowanie metody równoważenia obciążenia wirtualnego pulpitu Windows](configure-host-pool-load-balancing.md)
