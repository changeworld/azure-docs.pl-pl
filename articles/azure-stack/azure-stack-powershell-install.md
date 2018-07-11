---
title: Instalowanie programu PowerShell dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować program PowerShell dla usługi Azure Stack.
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
ms.date: 07/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: e2785b0beeab042d4b1ad9a9eb5f545dbb58b8b9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38487505"
---
# <a name="install-powershell-for-azure-stack"></a>Instalowanie programu PowerShell dla usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Usługa Azure Stack, które zgodna modułów programu Azure PowerShell są wymagane do pracy z usługą Azure Stack. W tym przewodniku przedstawiono czynności wymagane do zainstalowania programu PowerShell dla usługi Azure Stack.

Ten artykuł zawiera szczegółowe instrukcje dotyczące instalowania programu PowerShell dla usługi Azure Stack.

> [!Note]  
> Poniższe kroki wymagają programu PowerShell w wersji 5.0. Aby sprawdzić swoją wersję, uruchom $PSVersionTable.PSVersion i porównaj **głównych** wersji.

Polecenia programu PowerShell dla usługi Azure Stack są instalowane za pośrednictwem galerii programu PowerShell. Poniższa procedura służy do sprawdzania, czy w galerii programu PowerShell jest zarejestrowany jako repozytorium, otwórz sesję programu PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie:

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Jeśli repozytorium nie jest zarejestrowany, otwórz sesję programu PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie:

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Ten krok wymaga dostępu do Internetu. 

## <a name="uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>Odinstaluj istniejące wersje modułów platformy Azure Stack PowerShell

Przed rozpoczęciem instalacji wymaganej wersji, upewnij się, odinstalowanie wszelkich uprzednio zainstalowanych modułów AzureRM PowerShell w usłudze Azure Stack. Można je odinstalować, wykonując jedną z następujących dwóch metod:

 - Aby odinstalować istniejące moduły programu PowerShell usługi AzureRM, należy zamknąć wszystkich aktywnych sesji programu PowerShell i uruchom następujące polecenie:

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Usuń wszystkie foldery, rozpoczynające się od "Azure" z `C:\Program Files\WindowsPowerShell\Modules` i `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` folderów. Usunięcie tych folderów usuwa wszystkie istniejące moduły programu PowerShell.

W poniższych sekcjach opisano kroki wymagane do zainstalowania programu PowerShell dla usługi Azure Stack. Można zainstalować programu PowerShell w usłudze Azure Stack jest świadczona w połączony, częściowo połączone lub w przypadku odłączonych.

## <a name="install-the-azure-stack-powershell-modules-in-a-connected-scenario-with-internet-connectivity"></a>Instalowanie modułów programu Azure PowerShell stosu w przypadku połączonych (przy użyciu łączności z Internetem)

Moduły platformy Azure Stack zgodne AzureRM są instalowane za pośrednictwem profilami wersji interfejsu API. Usługa Azure Stack wymaga **2017-03-09-profile** profilu wersji interfejsu API, która jest dostępna po zainstalowaniu modułu AzureRM.Bootstrapper. Aby dowiedzieć się o profilami wersji interfejsu API i polecenia cmdlet, dostarczone przez nich, zapoznaj się [Zarządzanie profilami wersji interfejsu API](user/azure-stack-version-profiles.md). Oprócz moduły AzureRM należy również zainstalować moduły programu Azure PowerShell specyficzne dla stosu. Uruchom poniższy skrypt programu PowerShell, aby zainstalować te moduły na deweloperskiej stacji roboczej:

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

Aby sprawdzić instalację, uruchom następujące polecenie:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Jeśli instalacja się powiodła, moduł AzureRM i AzureStack są wyświetlane w danych wyjściowych.

## <a name="install-the-azure-stack-powershell-modules-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Instalowanie modułów Azure Stack PowerShell w odłączone lub częściowo połączone scenariusza (ograniczony z łącznością z Internetem)

W przypadku odłączonych należy najpierw pobrać modułów programu PowerShell na komputerze, który ma łączność z Internetem, a następnie przenieść je do usługi Azure Stack Development Kit dla instalacji.

> [!IMPORTANT]  
> Wersja modułu Azure PowerShell stosu 1.3.0 zawiera listę istotnych zmian. Aby uaktualnić 1.2.11 wersji, zobacz [Przewodnik po migracji](https://aka.ms/azspowershellmigration).

1. Zaloguj się do komputera, na którym mieć połączenie z Internetem i użyj następującego skryptu do pobrania AzureRM i pakietów AzureStack na komputer lokalny:

   ```PowerShell  
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
     -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > Jeśli nie są uruchomione usługi Azure Stack z aktualizacją update 1804 lub nowszego, zmień **requiredversion** wartości parametru `1.2.11`. 

2. Skopiuj pakietów do pobrania z urządzeniem USB.

3. Zaloguj się do stacji roboczej, a następnie skopiuj pakiety z urządzenia USB do lokalizacji na stacji roboczej.

4. Teraz musisz zarejestrować tę lokalizację jako repozytorium domyślne i zainstaluj moduł AzureRM i AzureStack z tego repozytorium:

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

## <a name="configure-powershell-to-use-a-proxy-server"></a>Konfigurowanie programu PowerShell do korzystania z serwera proxy

W scenariuszach, które wymagają serwera proxy, aby uzyskać dostęp do Internetu należy najpierw skonfigurować programu PowerShell, aby użyć istniejącego serwera proxy.

1. Otwórz wiersz PowerShell z podwyższonym poziomem uprawnień.
2. Uruchom następujące polecenia:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>Kolejne kroki

 - [Pobierz narzędzia usługi Azure Stack z usługi GitHub](azure-stack-powershell-download.md)
 - [Konfigurowanie środowiska PowerShell użytkownika usługi Azure Stack](user/azure-stack-powershell-configure-user.md)  
 - [Konfigurowanie środowiska PowerShell usługi Azure Stack — operator](azure-stack-powershell-configure-admin.md) 
 - [Zarządzanie profilami wersji interfejsu API w usłudze Azure Stack](user/azure-stack-version-profiles.md)  
