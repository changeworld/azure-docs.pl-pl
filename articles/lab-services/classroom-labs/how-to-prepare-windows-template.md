---
title: Przewodnik dotyczący konfigurowania komputera z szablonami systemu Windows | Dokumenty firmy Microsoft
description: Ogólne kroki, aby przygotować komputer szablonu systemu Windows w programie Lab Services.  Te kroki obejmują ustawienie harmonogramu usługi Windows Update, zainstalowanie usługi OneDrive i zainstalowanie pakietu Office.
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: c1aaf588f61b329fa3b838b8a92f3e287897315b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521185"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Przewodnik dotyczący konfigurowania komputera z szablonami systemu Windows w usługach Azure Lab Services

Jeśli konfigurujesz maszynę szablonu systemu Windows 10 dla usług Azure Lab Services, oto kilka najlepszych rozwiązań i wskazówek, które należy wziąć pod uwagę. Poniższe kroki konfiguracji są opcjonalne.  Jednak te kroki przygotowawcze mogą pomóc zwiększyć produktywność uczniów, zminimalizować przerwy w zajęciach i zapewnić, że korzystają z najnowszych technologii.

>[!IMPORTANT]
>Ten artykuł zawiera fragmenty kodu programu PowerShell w celu usprawnienia procesu modyfikacji szablonu komputera.  W przypadku wszystkich pokazanych skryptów programu PowerShell należy uruchomić je w programie Windows PowerShell z uprawnieniami administratora. W systemie Windows 10, szybki sposób, aby to zrobić, aby kliknąć prawym przyciskiem myszy menu Start i wybrać "Windows PowerShell (Admin)".

## <a name="install-and-configure-onedrive"></a>Instalowanie i konfigurowanie usługi OneDrive

Aby chronić dane uczniów przed utratą, jeśli maszyna wirtualna zostanie zresetowana, zalecamy uczniom powrót ich danych do chmury.  Usługa Microsoft OneDrive może pomóc uczniom chronić ich dane.  

### <a name="install-onedrive"></a>Instalowanie usługi OneDrive

Aby ręcznie pobrać i zainstalować usługę OneDrive, zobacz strony pobierania [usługi OneDrive](https://onedrive.live.com/about/download/) lub [OneDrive dla Firm.](https://onedrive.live.com/about/business/)

Można również użyć następującego skryptu programu PowerShell.  Automatycznie pobierze i zainstaluje najnowszą wersję usługi OneDrive.  Po zainstalowaniu klienta usługi OneDrive uruchom instalator.  W naszym przykładzie `/allUsers` używamy przełącznika, aby zainstalować usługę OneDrive dla wszystkich użytkowników na komputerze. Używamy również `/silent` przełącznika, aby dyskretnie zainstalować onedrive.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>Dostosowania usługi OneDrive

Istnieje wiele [dostosowań, które można wykonać w usłudze OneDrive.](https://docs.microsoft.com/onedrive/use-group-policy) Przyjmijmy niektóre z bardziej typowych dostosowań.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Dyskretne przenoszenie folderów znanych systemu Windows do usługi OneDrive

Foldery, takie jak Dokumenty, Pliki do pobrania i Obrazy, są często używane do przechowywania plików uczniów. Aby upewnić się, że te foldery są archiwizowane w usłudze OneDrive, zalecamy przeniesienie tych folderów do usługi OneDrive.

Jeśli korzystasz z komputera, który nie korzysta z usługi Active Directory, użytkownicy mogą ręcznie przenieść te foldery do usługi OneDrive po uwierzytelnieniu do usługi OneDrive.

1. Otwórz Eksploratora plików
2. Kliknij prawym przyciskiem myszy folder Dokumenty, Pobrane lub Obrazy.
3. Przejdź do właściwości > lokalizacji.  Przenieś folder do nowego folderu w katalogu usługi OneDrive.

Jeśli maszyna wirtualna jest połączona z usługą Active Directory, można ustawić komputer szablonu, aby automatycznie monitować uczniów o przeniesienie znanych folderów do usługi OneDrive.  

Najpierw musisz pobrać identyfikator dzierżawy pakietu Office.  Aby uzyskać dalsze instrukcje, zobacz [znajdowanie identyfikatora dzierżawy usługi Office 365](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).  Identyfikator dzierżawy usługi Office 365 można również uzyskać przy użyciu następującego programu PowerShell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Po uzyskaniu identyfikatora dzierżawy usługi Office 365 ustaw usługę OneDrive, aby monitowała o przeniesienie znanych folderów do usługi OneDrive przy użyciu następującego programu PowerShell.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Korzystanie z plików usługi OneDrive na żądanie

Uczniowie mogą mieć wiele plików na swoich kontach usługi OneDrive. Aby zaoszczędzić miejsce na komputerze i skrócić czas pobierania, zalecamy, aby wszystkie pliki przechowywane na koncie usługi OneDrive ucznia były na żądanie.  Pliki na żądanie są pobierane tylko wtedy, gdy użytkownik uzyskuje do niego dostęp.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Dyskretne logowanie użytkowników do usługi OneDrive

Usługę OneDrive można ustawić tak, aby automatycznie logował się przy użyciu poświadczeń systemu Windows zalogowanego użytkownika.  Automatyczne logowanie jest przydatne w przypadku zajęć, w których uczeń loguje się przy użyciu poświadczeń szkolnych usługi Office 365.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Wyłączanie samouczka wyświetlanego na końcu instalacji usługi OneDrive

To ustawienie umożliwia uniemożliwienie uruchamiania samouczka w przeglądarce internetowej na końcu instalatora usługi OneDrive.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Ustawianie maksymalnego rozmiaru pliku, który ma być pobierany automatycznie

To ustawienie jest używane w połączeniu z użytkownikami logowania dyskretnego do klienta synchronizacji usługi OneDrive z ich poświadczeniami systemu Windows na urządzeniach, na których nie ma włączonej funkcji Pliki usługi OneDrive na żądanie. Każdy użytkownik, który ma usługę OneDrive, która jest większa niż określony próg (w MB), zostanie poproszony o wybranie folderów, które chce zsynchronizować, zanim klient synchronizacji usługi OneDrive (OneDrive.exe) pobierze pliki.  W naszym przykładzie "1111-2222-3333-4444" to identyfikator dzierżawy usługi Office 365, a 0005000 ustawia próg 5 GB.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>Instalowanie i konfigurowanie usługi Microsoft Office 365

### <a name="install-microsoft-office-365"></a>Instalowanie usługi Microsoft Office 365

Jeśli komputer szablonu wymaga pakietu Office, zalecamy instalację pakietu Office za pośrednictwem [narzędzia office deployment tool (ODT)](https://www.microsoft.com/download/details.aspx?id=49117 ). Należy utworzyć plik konfiguracyjny wielokrotnego użytku przy użyciu [usługi konfiguracji klienta usługi Office 365,](https://config.office.com/) aby wybrać architekturę, jakie funkcje będą potrzebne z pakietu Office i jak często są aktualizowane.

1. Przejdź do [usługi konfiguracji klienta usługi Office 365](https://config.office.com/) i pobierz własny plik konfiguracyjny.
2. Pobierz [Narzędzie wdrażania pakietu Office](https://www.microsoft.com/download/details.aspx?id=49117).  Pobrany plik `setup.exe`będzie .
3. Uruchom, `setup.exe /download configuration.xml` aby pobrać składniki pakietu Office.
4. Uruchom, `setup.exe /configure configuration.xml` aby zainstalować składniki pakietu Office.

### <a name="change-the-microsoft-office-365-update-channel"></a>Zmienianie kanału aktualizacji usługi Microsoft Office 365

Za pomocą narzędzia Konfiguracji pakietu Office można określić, jak często pakiet Office odbiera aktualizacje. Jeśli jednak musisz zmodyfikować częstotliwość odchylić aktualizacje pakietu Office po instalacji, możesz zmienić adres URL kanału aktualizacji. Aktualizuj adresy URL kanału można znaleźć w [witrynie Zmień kanał aktualizacji usługi Office 365 ProPlus dla urządzeń w organizacji](https://docs.microsoft.com/deployoffice/change-update-channels). W poniższym przykładzie pokazano, jak ustawić usługę Office 365 do korzystania z kanału aktualizacji miesięcznej.

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Instalowanie i konfigurowanie aktualizacji

### <a name="install-the-latest-windows-updates"></a>Instalowanie najnowszych aktualizacji systemu Windows

Przed opublikowaniem szablonu maszyny Wirtualnej należy zainstalować najnowsze aktualizacje firmy Microsoft na komputerze szablonu ze względów bezpieczeństwa.  Potencjalnie zapobiega to również zakłóceniom uczniów w pracy, gdy aktualizacje są uruchamiane w nieoczekiwanych momentach.

1. Uruchom **ustawienia** z menu Start
2. Kliknij **na Aktualizuj** & zabezpieczenia
3. Kliknij **pozycję Sprawdź dostępność aktualizacji**
4. Aktualizacje zostaną pobrane i zainstalowane.

Można również użyć programu PowerShell, aby zaktualizować komputer szablonu.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Niektóre aktualizacje mogą wymagać ponownego uruchomienia komputera.  Jeśli wymagane jest ponowne uruchomienie komputera, zostanie wyświetlony monit.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Instalowanie najnowszych aktualizacji aplikacji ze Sklepu Microsoft

Zalecamy aktualizację wszystkich aplikacji ze Sklepu Microsoft do ich najnowszych wersji.  Poniżej przedstawiono instrukcje dotyczące ręcznego aktualizowania aplikacji ze sklepu Microsoft Store.  

1. Uruchom aplikację **Microsoft Store.**
2. Kliknij elipsę (...) obok zdjęcia użytkownika w górnym rogu aplikacji.
3. Wybierz **pobierz** i aktualizuje z menu rozwijanego.
4. Kliknij przycisk **Pobierz aktualizację.**

Za pomocą programu PowerShell można również zaktualizować aplikacje ze sklepu Microsoft Store, które są już zainstalowane.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Zatrzymywać automatyczne aktualizacje systemu Windows

Po zaktualizowaniu systemu Windows do najnowszej wersji można rozważyć zatrzymanie aktualizacji systemu Windows.  Aktualizacje automatyczne mogą potencjalnie zakłócać zaplanowany czas zajęć.  Jeśli kurs trwa dłużej, możesz poprosić uczniów, aby ręcznie sprawdzali dostępność aktualizacji lub ustawiali automatyczne aktualizacje przez czas poza zaplanowanymi godzinami zajęć.  Aby uzyskać więcej informacji na temat opcji dostosowywania witryny Windows Update, zobacz [zarządzanie dodatkowymi ustawieniami usługi Windows Update](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings).

Automatyczne aktualizacje systemu Windows mogą zostać zatrzymane przy użyciu następującego skryptu programu PowerShell.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Instalowanie pakietów języków obcych

Jeśli potrzebujesz dodatkowych języków zainstalowanych na maszynie wirtualnej, możesz dodać je za pośrednictwem sklepu Microsoft Store.

1. Uruchamianie sklepu Microsoft Store
2. Wyszukaj "pakiet językowy"
3. Wybieranie języka do zainstalowania

Jeśli jesteś już zalogowany do szablonu maszyny Wirtualnej, użyj [skrótu "Zainstaluj pakiet językowy",](ms-settings:regionlanguage?activationSource=SMC-IA-4027670) aby przejść bezpośrednio do odpowiedniej strony ustawień.

## <a name="remove-unneeded-built-in-apps"></a>Usuwanie niepotrzebnych wbudowanych aplikacji

System Windows 10 zawiera wiele wbudowanych aplikacji, które mogą nie być potrzebne dla danej klasy. Aby uprościć obraz komputera dla studentów, można odinstalować niektóre aplikacje z komputera szablonu.  Aby wyświetlić listę zainstalowanych aplikacji, `Get-AppxPackage` użyj polecenia cmdlet programu PowerShell.  Poniższy przykład przedstawia wszystkie zainstalowane aplikacje, które można usunąć.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Aby usunąć aplikację, użyj polecenia cmdlet Remove-Appx.  W poniższym przykładzie pokazano, jak usunąć wszystko związane z XBox.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Instalowanie typowych aplikacji związanych z nauczaniem

Zainstaluj inne aplikacje powszechnie używane do nauczania za pośrednictwem aplikacji ze Sklepu Windows. Sugestie obejmują aplikacje, takie jak [aplikacja Microsoft Whiteboard](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)i Minecraft [Education Edition](https://education.minecraft.net/). Te aplikacje muszą być instalowane ręcznie za pośrednictwem Sklepu Windows lub za pośrednictwem odpowiednich witryn sieci Web na szablonie maszyny Wirtualnej.

## <a name="conclusion"></a>Podsumowanie

W tym artykule przedstawiono opcjonalne kroki, aby przygotować maszynę wirtualną szablonu systemu Windows dla klasy skutecznej.  Kroki obejmują instalowanie usługi OneDrive i instalowanie usługi Office 365, instalowanie aktualizacji dla systemu Windows i instalowanie aktualizacji dla aplikacji ze Sklepu Microsoft.  Omówiliśmy również sposób ustawiania aktualizacji do harmonogramu, który najlepiej sprawdza się w twojej klasie.  

## <a name="next-steps"></a>Następne kroki
Zobacz artykuł dotyczący kontrolowania zachowania zamknięcia systemu Windows, aby ułatwić zarządzanie kosztami: [Przewodnik po kontrolowaniu zachowania zamknięcia systemu Windows](how-to-windows-shutdown.md)
