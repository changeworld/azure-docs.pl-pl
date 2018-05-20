---
title: Instalowanie programu PowerShell dla usługi Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować program PowerShell Azure stosu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F8D99A91-15B5-4073-BE07-A43514A6D2CF
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: mabrigg
ms.openlocfilehash: 9d09fb60722865a75ea0825f5ca54f792642980a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="install-powershell-for-azure-stack"></a>Instalowanie programu PowerShell dla usługi Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Azure stosu zgodne Azure moduły programu PowerShell są wymagane do pracy z stosu Azure. W tym artykule przedstawiono kroki wymagane do zainstalowania programu PowerShell dla usługi Azure stosu. Korzystając z procedury opisanej w tym artykule z Development Kit stosu Azure lub z systemem Windows klienta zewnętrznych po nawiązaniu połączenia za pośrednictwem sieci VPN.

Ten artykuł zawiera szczegółowe instrukcje dotyczące instalowania programu PowerShell. Jednak jeśli chcesz szybko zainstalować i skonfigurować program PowerShell, można użyć skryptu zawarte w artykule "Get i uruchomić przy użyciu programu PowerShell".

> [!NOTE]
> Poniższe kroki wymagają programu PowerShell 5.0. Aby sprawdzić swoją wersję, uruchom $PSVersionTable.PSVersion i porównaj "Główny" numer wersji.

Polecenia programu PowerShell dla usługi Azure stosu są instalowane za pośrednictwem galerii programu PowerShell. Aby zarejestrować repozytorium PSGallery, otwórz sesję programu PowerShell z podwyższonym poziomem uprawnień z zestaw deweloperski lub klient zewnętrznych z systemem Windows Jeśli są połączone za pośrednictwem sieci VPN i uruchom następujące polecenie:

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>Odinstaluj istniejące wersje programu PowerShell

Przed zainstalowaniem wersji wymagane, upewnij się, odinstalowanie żadnych istniejących modułów programu Azure PowerShell. Można je odinstalować, wykonując jedną z następujących dwóch metod:

* Aby odinstalować istniejące moduły programu PowerShell, zaloguj się development Kit lub do klienta zewnętrznego systemu Windows Jeśli planujesz ustanowić połączenie sieci VPN. Zamknij wszystkie aktywne sesje programu PowerShell i uruchom następujące polecenie:

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* Zaloguj się development Kit lub do systemu Windows klienta zewnętrznego Jeśli planujesz ustanowić połączenie sieci VPN. Usuń wszystkie foldery, które zaczynają się "Azure" z `C:\Program Files (x86)\WindowsPowerShell\Modules` i `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` folderów. Usunięcie tych folderów usuwa wszystkie istniejące moduły programu PowerShell z "AzureStackAdmin" i "globalny" użytkownik zakresów.

W poniższych sekcjach opisano kroki wymagane do zainstalowania programu PowerShell dla usługi Azure stosu. Na stosie Azure, która jest świadczona w połączone, częściowo połączone lub w scenariuszu bez połączenia można zainstalować programu PowerShell.

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Instalowanie programu PowerShell w scenariuszu połączonych (z połączeniem internetowym)

Azure stosu zgodne AzureRM moduły są instalowane za pośrednictwem interfejsu API w wersji profilów. Stos Azure wymaga **2017-03-09-profilu** profilu wersji interfejsu API, który jest dostępny przez zainstalowanie modułu AzureRM.Bootstrapper. Aby dowiedzieć się więcej o profilach wersji interfejsu API i udostępniane przez nich polecenia cmdlet, zapoznaj się [zarządzania profilami wersji interfejsu API](azure-stack-version-profiles-powershell.md). Oprócz modułów AzureRM należy również zainstalować moduł Azure PowerShell dotyczące stosu. Uruchom poniższy skrypt programu PowerShell, aby zainstalować te moduły na deweloperskiej stacji roboczej:

  ```powershell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```

Aby sprawdzić instalację, uruchom następujące polecenie:

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```

  Jeśli instalacja się powiodła, moduły AzureRM i stosu Azure są wyświetlane w danych wyjściowych.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Instalowanie programu PowerShell w scenariuszu częściowo połączonych lub rozłączona (ograniczone z łącznością z Internetem)

W scenariuszu bez połączenia lub częściowo połączonych musi najpierw Pobierz moduły programu PowerShell na komputerze, na którym ma połączenie z Internetem, a następnie przesyła je Azure stosu Development Kit dla instalacji.

1. Zaloguj się do komputera, na którym jest połączony z Internetem i użyj następującego skryptu do pobierania AzureRM i pakiety AzureStack na komputerze lokalnym:

   ```powershell
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11
   ```

2. Skopiować pobranych pakietów przez urządzenie USB.

3. Zaloguj się w zestawie i skopiuj pakiety z urządzenia USB do lokalizacji w zestawie.

4. Teraz musisz zarejestrować tej lokalizacji jako repozytorium domyślne i zainstalować moduły AzureRM i AzureStack z tego repozytorium:

   ```powershell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   ```powershell
   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName
   ```

## <a name="next-steps"></a>Kolejne kroki

* [Pobieranie narzędzia Azure stosu z usługi GitHub](azure-stack-powershell-download.md)
* [Konfigurowanie środowiska PowerShell użytkownika Azure stosu](azure-stack-powershell-configure-user.md)
* [Zarządzanie profilami wersji interfejsu API Azure stosu](azure-stack-version-profiles-powershell.md)
