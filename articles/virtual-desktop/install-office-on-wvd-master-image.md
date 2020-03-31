---
title: Instalowanie pakietu Office na głównym obrazie VHD — Azure
description: Jak zainstalować i dostosować pakiet Office na obrazie wzorcowym pulpitu wirtualnego systemu Windows na platformie Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b93f26a6799a50868feb1f3350a3dc4a73a0b2e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127840"
---
# <a name="install-office-on-a-master-vhd-image"></a>Instalowanie pakietu Office w głównym obrazie wirtualnego dysku twardego

W tym artykule opisano, jak zainstalować usługę Office 365 ProPlus, OneDrive i inne typowe aplikacje na obrazie głównego wirtualnego dysku twardego (VHD) do przekazania na platformę Azure. Jeśli użytkownicy muszą uzyskać dostęp do niektórych aplikacji biznesowych (LOB), zalecamy zainstalowanie ich po wypełnieniu instrukcji w tym artykule.

W tym artykule przyjęto założenie, że maszyna wirtualna została już utworzona. Jeśli nie, zobacz [Przygotowywanie i dostosowywanie głównego obrazu VHD](set-up-customize-master-image.md#create-a-vm)

W tym artykule przyjęto również założenie, że masz podwyższony dostęp na maszynie Wirtualnej, niezależnie od tego, czy jest ona aprowizowana na platformie Azure, czy w usłudze Hyper-V Manager. Jeśli nie, zobacz [Podnoszą poziom dostępu, aby zarządzać wszystkimi subskrypcjami platformy Azure i grupami zarządzania](../role-based-access-control/elevate-access-global-admin.md).

>[!NOTE]
>Te instrukcje są przeznaczone dla konfiguracji specyficznej dla pulpitu wirtualnego systemu Windows, która może być używana z istniejącymi procesami organizacji.

## <a name="install-office-in-shared-computer-activation-mode"></a>Instalowanie pakietu Office w trybie aktywacji komputera udostępnionego

Aktywacja komputera udostępnionego umożliwia wdrożenie usługi Office 365 ProPlus na komputerze w organizacji, do którego uzyskuje dostęp wielu użytkowników. Aby uzyskać więcej informacji na temat aktywacji udostępnionego komputera, zobacz [Omówienie aktywacji udostępnionego komputera dla usługi Office 365 ProPlus](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/).

Użyj [Narzędzia wdrażania pakietu Office,](https://www.microsoft.com/download/details.aspx?id=49117) aby zainstalować pakiet Office. Windows 10 Enterprise multi-session obsługuje tylko następujące wersje pakietu Office:
- Office 365 ProPlus
- Usługa Office 365 Business z subskrypcją usługi Microsoft 365 Business

Narzędzie wdrażania pakietu Office wymaga konfiguracji pliku XML. Aby dostosować poniższy przykład, zobacz [Opcje konfiguracji narzędzia wdrażania pakietu Office](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Ta przykładowa konfiguracja XML podana przez nas wykona następujące czynności:

- Zainstaluj pakiet Office z kanału miesięcznego i dostarczaj aktualizacje z kanału miesięcznego po ich wykonaniu.
- Użyj architektury x64.
- Wyłącz aktualizacje automatyczne.
- Usuń wszystkie istniejące instalacje pakietu Office i przenieś ich ustawienia.
- Włącz aktywację komputera udostępnionego.

>[!NOTE]
>Funkcja wyszukiwania wzorników programu Visio może nie działać zgodnie z oczekiwaniami na pulpicie wirtualnym systemu Windows.

Oto, czego nie zrobi ta przykładowa konfiguracja XML:

- Instalowanie programu Skype dla firm
- Zainstaluj usługę OneDrive w trybie dla użytkownika. Aby dowiedzieć się więcej, zobacz [Instalowanie usługi OneDrive w trybie na komputerze](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Aktywację komputera współużytkowanego można skonfigurować za pomocą obiektów zasad grupy (OBIEKTÓW ZASAD) lub ustawień rejestru. Obiekt zasad grupy znajduje się w witrynie **\\Szablony\\administracyjne szablonów administracyjnych\\pakietu Microsoft Office 2016 (Komputer)\\**

Narzędzie wdrażania pakietu Office zawiera plik setup.exe. Aby zainstalować pakiet Office, uruchom następujące polecenie w wierszu polecenia:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Przykładowa konfiguracja.xml

W poniższym przykładzie XML zostanie zainstalowana wersja miesięczna.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Zespół pakietu Office zaleca użycie 64-bitowej instalacji dla parametru **OfficeClientEdition.**

Po zainstalowaniu pakietu Office można zaktualizować domyślne zachowanie pakietu Office. Uruchom następujące polecenia pojedynczo lub w pliku wsadowym, aby zaktualizować zachowanie.

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

## <a name="install-onedrive-in-per-machine-mode"></a>Instalowanie usługi OneDrive w trybie na maszynę

Usługa OneDrive jest zwykle instalowana na użytkownika. W tym środowisku należy go zainstalować na komputerze.

Aby zainstalować usługę OneDrive w trybie na komputer:

1. Najpierw utwórz lokalizację, aby zorganizować instalatora usługi OneDrive. Folder dysku lokalnego\\\\lub lokalizacja [ unc] (file://unc) jest w porządku.

2. Pobierz plik OneDriveSetup.exe do lokalizacji etapnej za pomocą tego linku:<https://aka.ms/OneDriveWVD-Installer>

3. Jeśli pakiet Office został zainstalowany w usłudze OneDrive, pomijając ** \<excludeApp\>ID="OneDrive" /**, odinstaluj wszystkie istniejące instalacje usługi OneDrive na użytkownika z wiersza polecenia z podwyższonym poziomem uprawnień, uruchamiając następujące polecenie:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Uruchom to polecenie z wiersza polecenia z podwyższonym poziomem uprawnień, aby ustawić wartość rejestru **AllUsersInstall:**

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Uruchom to polecenie, aby zainstalować usługę OneDrive w trybie na komputer:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Uruchom to polecenie, aby skonfigurować usługę OneDrive do uruchamiania przy logowaniech dla wszystkich użytkowników:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Włącz **dyskretnie skonfigurować konto użytkownika,** uruchamiając następujące polecenie.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Przekieruj i przenieś foldery znane z systemu Windows do usługi OneDrive, uruchamiając następujące polecenie.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Zespoły i Skype

Pulpit wirtualny systemu Windows nie obsługuje programu Skype dla firm i zespołów.

## <a name="next-steps"></a>Następne kroki

Po dodaniu pakietu Office do obrazu możesz nadal dostosowywać główny obraz VHD. Zobacz [Przygotowywanie i dostosowywanie głównego obrazu VHD](set-up-customize-master-image.md).
