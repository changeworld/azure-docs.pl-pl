---
title: Zainstaluj pakiet Office na główny obraz wirtualnego dysku twardego - Azure
description: Sposób instalowania i dostosowywania pakietu Office w obrazie głównym pulpitu wirtualnego Windows (wersja zapoznawcza) na platformie Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: 0e89d37011ccdfc3acdace5b45faa8e9a64e4d3e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620461"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalowanie pakietu Office w głównym obrazie wirtualnego dysku twardego

W tym artykule wyjaśniono, jak zainstalować usługi Office 365 ProPlus, OneDrive i innymi aplikacjami w obrazie głównym wirtualnego dysku twardego (VHD) w celu przekazania na platformę Azure. Jeśli użytkownicy potrzebują dostępu do pewnych linii aplikacje biznesowe (LOB), zalecamy instalowanie po wykonaniu instrukcji zawartych w tym artykule.

W tym artykule założono, że już utworzono maszynę wirtualną (VM). Jeśli nie, zobacz [przygotowanie i dostosowanie głównego obrazu wirtualnego dysku twardego](set-up-customize-master-image.md#create-a-vm)

W tym artykule również założono, że mają podwyższony poziom dostępu na maszynie Wirtualnej, czy jest zainicjowany na platformie Azure lub Menedżera funkcji Hyper-V. Jeśli nie, zobacz [podniesienie uprawnień dostępu do zarządzania platformy Azure wszystkich subskrypcji i zarządzania grupami](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Te instrukcje dotyczą konfiguracji specyficznych dla Windows pulpitu wirtualnego (wersja zapoznawcza), który może być używany z istniejącymi procesami w Twojej organizacji.

## <a name="install-office-in-shared-computer-activation-mode"></a>Instalowanie pakietu Office w trybie Aktywacja współużytkowanego komputera

Aktywacja współużytkowanego komputera umożliwia wdrażanie usługi Office 365 ProPlus do komputera w Twojej organizacji, która jest dostępna przez wielu użytkowników. Aby uzyskać więcej informacji na temat aktywacji na komputerze udostępnionym zobacz [Omówienie aktywacji na komputerze udostępnionym dla usługi Office 365 ProPlus](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Użyj [narzędzia wdrażania pakietu Office](https://www.microsoft.com/download/details.aspx?id=49117) do zainstalowania pakietu Office. Wiele sesji systemu Windows 10 Enterprise obsługuje tylko następujące wersje pakietu Office:
- Office 365 ProPlus
- Office 365 Business, który pochodzi z subskrypcją Microsoft 365 Business

Narzędzia wdrażania pakietu Office wymaga pliku XML konfiguracji. Aby dostosować poniższego przykładu, zobacz [opcji konfiguracji dla narzędzia wdrażania pakietu Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Tej przykładowej konfiguracji XML udostępniliśmy będzie wykonywać następujące czynności:

- Instalowanie pakietu Office z kanału dla niejawnych testerów i dostarczać aktualizacje z kanału dla niejawnych testerów, gdy są one wykonywane.
- Architektura użycia x64.
- Wyłącz Aktualizacje automatyczne.
- Instalowanie programu Visio i projektu.
- Usuń wszystkie istniejące instalacje pakietu Office, a następnie przeprowadzić migrację ustawień.
- Włączanie aktywacji na komputerze udostępnionym.

>[!NOTE]
>Wzornik funkcji wyszukiwania w programie Visio nie będzie działać w Windows pulpitu wirtualnego podczas konfiguracji (wersja zapoznawcza).

Oto, co nie zrobi tej przykładowej konfiguracji XML:

- Instalację programu Skype dla firm
- Instalowanie usługi OneDrive w trybie użytkownika. Aby dowiedzieć się więcej, zobacz [zainstalować usługi OneDrive w trybie poszczególnych komputerów](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Udostępnione aktywacji na komputerze można skonfigurować za pomocą obiektów zasad grupy (GPO) lub ustawienia rejestru. Obiekt zasad grupy znajduje się w **konfiguracji komputera\\zasady\\Szablony administracyjne\\Microsoft Office 2016 (komputer)\\ustawienia licencjonowania**

Narzędzia wdrażania pakietu Office zawiera setup.exe. Aby zainstalować pakiet Office, uruchom następujące polecenie w wierszu polecenia:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Przykładowy konfiguracja.XML

Poniższy przykładowy plik XML zainstaluje wydania dla niejawnych testerów.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Zespół usługi Office zaleca się za pomocą 64-bitowej instalacji dla **OfficeClientEdition** parametru.

Po zainstalowaniu pakietu Office, należy zaktualizować domyślne zachowanie pakietu Office. Uruchom następujące polecenia, pojedynczo lub w pliku wsadowym, aby zaktualizować zachowanie.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>Instalowanie usługi OneDrive w trybie poszczególnych komputerów

OneDrive jest zazwyczaj instalowana dla poszczególnych użytkowników. W tym środowisku, należy go zainstalować na maszynie.

Poniżej przedstawiono sposób instalowania usługi OneDrive w trybie poszczególnych komputerów:

1. Najpierw utwórz lokalizację w której etap Instalatora usługi OneDrive. Folder na dysku lokalnym lub [\\\\unc] (file://unc) znajduje się w dobrym stanie.

2. Pobierz OneDriveSetup.exe do lokalizacji przygotowanych za pomocą tego łącza: <https://aka.ms/OneDriveWVD-Installer>

3. Po zainstalowaniu pakietu office z usługą OneDrive, pomijając  **\<ExcludeApp ID = "OneDrive" /\>** , odinstaluj wszelkie istniejące instalacje na użytkownika OneDrive z wiersza polecenia z podwyższonym poziomem uprawnień, uruchamiając następujące polecenie:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Uruchom następujące polecenie z wiersza polecenia z podwyższonym poziomem uprawnień, aby ustawić **AllUsersInstall** wartości rejestru:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Uruchom następujące polecenie, aby zainstalować usługi OneDrive w trybie poszczególnych komputerów:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Uruchom następujące polecenie, aby skonfigurować usługi OneDrive do uruchomienia podczas logowania dla wszystkich użytkowników:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Włącz **dyskretnie skonfigurować konto użytkownika** , uruchamiając następujące polecenie.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Przekierowywanie i Przenieś Windows znanych folderów w usłudze OneDrive, uruchamiając następujące polecenie.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Zespoły i Skype

Windows pulpitu wirtualnego nie obsługuje programu Skype dla firm i zespołów.

## <a name="next-steps"></a>Kolejne kroki

Teraz, po dodaniu pakietu Office w obrazie, można dostosować wzorca obraz wirtualnego dysku twardego. Zobacz [przygotowanie i dostosowanie głównego obrazu wirtualnego dysku twardego](set-up-customize-master-image.md).
