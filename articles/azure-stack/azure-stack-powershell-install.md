---
title: Instalowanie programu PowerShell dla usługi Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować program PowerShell Azure stosu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 8fea502457275c89d99084a5b025b620872d796b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="install-powershell-for-azure-stack"></a>Instalowanie programu PowerShell dla usługi Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Azure stosu zgodne Azure moduły programu PowerShell są wymagane do pracy z stosu Azure. W tym przewodniku możemy opisano kroki wymagane do zainstalowania programu PowerShell dla usługi Azure stosu.

W tym artykule przedstawiono szczegółowe instrukcje dotyczące instalacji programu PowerShell dla usługi Azure stosu.

> [!Note]
> Poniższe kroki wymagają programu PowerShell 5.0. Aby sprawdzić swoją wersję, uruchom $PSVersionTable.PSVersion i porównać **głównych** wersji.

Polecenia programu PowerShell dla usługi Azure stosu są instalowane za pośrednictwem galerii programu PowerShell. Poniższa procedura służy do sprawdzania poprawności, gdy PSGallery jest zarejestrowany jako repozytorium, otwórz sesję programu PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie:

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Jeśli repozytorium nie jest zarejestrowany, otwórz sesję programu PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie:

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Ten krok wymaga dostępu do Internetu. 

## <a name="uninstall-existing-versions-of-powershell"></a>Odinstaluj istniejące wersje programu PowerShell

Przed zainstalowaniem wersji wymagane, upewnij się, odinstalowanie żadnych wcześniej zainstalowanych modułów Azure PowerShell stosu. Można je odinstalować, wykonując jedną z następujących dwóch metod:

 - Aby odinstalować istniejące moduły programu PowerShell, zamknij wszystkie aktywne sesje programu PowerShell i uruchom następujące polecenie:

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Usuń wszystkie foldery, które zaczynają się "Azure" z `C:\Program Files\WindowsPowerShell\Modules` i `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` folderów. Usunięcie tych folderów spowoduje usunięcie istniejących moduły programu PowerShell.

W poniższych sekcjach opisano kroki wymagane do zainstalowania programu PowerShell dla usługi Azure stosu. Na stosie Azure, która jest świadczona w połączone, częściowo połączone lub w scenariuszu bez połączenia można zainstalować programu PowerShell.

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Instalowanie programu PowerShell w scenariuszu połączonych (z połączeniem internetowym)

Azure stosu zgodne AzureRM moduły są instalowane za pośrednictwem interfejsu API w wersji profilów. Stos Azure wymaga **2017-03-09-profilu** profilu wersji interfejsu API, który jest dostępny przez zainstalowanie modułu AzureRM.Bootstrapper. Aby dowiedzieć się więcej o profilach wersji interfejsu API i udostępniane przez nich polecenia cmdlet, zapoznaj się [zarządzania profilami wersji interfejsu API](user/azure-stack-version-profiles.md). Oprócz modułów AzureRM należy również zainstalować moduł Azure PowerShell dotyczące stosu. Uruchom poniższy skrypt programu PowerShell, aby zainstalować te moduły na deweloperskiej stacji roboczej:

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.2.12 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.2.12 

# Install Module Version 1.2.11 if Azure Stack is running a lower version then 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

Aby sprawdzić instalację, uruchom następujące polecenie:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Jeśli instalacja się powiodła, AzureRM i AzureStack moduły są wyświetlane w danych wyjściowych.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Instalowanie programu PowerShell w scenariuszu częściowo połączonych lub rozłączona (ograniczone z łącznością z Internetem)

W przypadku odłączonych należy najpierw Pobierz moduły programu PowerShell na komputerze, na którym ma połączenie z Internetem i przesyła je Azure stosu Development Kit dla instalacji.

> [!IMPORTANT]
> Wersja modułu PowerShell AzureRM pkt 1.2.12 zawiera listę fundamentalne zmiany. Aby uaktualnić 1.2.10 wersji, zobacz [Przewodnik po migracji](https://github.com/bganapa/azure-powershell/blob/stack-migration/documentation/migration-guides/Stack/migration-guide.1.2.12.md).

1. Zaloguj się do komputera, na którym jest połączony z Internetem i użyj następującego skryptu do pobierania AzureRM i pakiety AzureStack na komputerze lokalnym:

   ```PowerShell  
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.12

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.12 
   ```

> [!Important]  
> Jeśli nie zostały uruchomione stosu Azure z aktualizacją 1804 lub większą, zmień **requiredversion** wartości parametru `1.2.11`. 

2. Skopiować pobranych pakietów przez urządzenie USB.

3. Zaloguj się do stacji roboczej i skopiuj pakiety z urządzenia USB do lokalizacji na stacji roboczej.

4. Teraz musisz zarejestrować tej lokalizacji jako repozytorium domyślne i zainstalować moduły AzureRM i AzureStack z tego repozytorium:

   ```PowerShell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>Konfigurowanie programu PowerShell, aby użyć serwera proxy

W scenariuszach, które wymagają serwera proxy do uzyskania dostępu do Internetu należy najpierw skonfigurować programu PowerShell, aby korzystał z istniejącego serwera proxy.

1. Otwórz wiersz programu PowerShell z podwyższonym poziomem uprawnień.
2. Uruchom następujące polecenia:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>Kolejne kroki

 - [Pobieranie narzędzia Azure stosu z usługi GitHub](azure-stack-powershell-download.md)
 - [Konfigurowanie środowiska PowerShell użytkownika Azure stosu](user/azure-stack-powershell-configure-user.md)  
 - [Konfigurowanie środowiska PowerShell Azure stosu — operator](azure-stack-powershell-configure-admin.md) 
 - [Zarządzanie profilami wersji interfejsu API Azure stosu](user/azure-stack-version-profiles.md)  
