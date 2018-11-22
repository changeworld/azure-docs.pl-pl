---
title: Instalowanie programu PowerShell dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować program PowerShell dla usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: 2e31b9f88857d84bd0b507ccd1622279e72aa575
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282723"
---
# <a name="install-powershell-for-azure-stack"></a>Instalowanie programu PowerShell dla usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Aby pracować z chmurą, należy zainstalować zgodne modułów programu PowerShell usługi Azure Stack. Zgodność jest włączana za pomocą funkcji zwanej *profilami interfejsu API*.

Profilami interfejsu API umożliwiają zarządzanie wersją różnice między platformą Azure i usługi Azure Stack. Profilu wersji interfejsu API to zbiór modułów Azure PowerShell Resource Manager przy użyciu określonych wersji interfejsu API. Każda platforma w chmurze ma zestaw obsługiwanych profilami wersji interfejsu API. Na przykład usługi Azure Stack obsługuje wersji określonego profilu Legitymacja, takie jak **2018-03-01-hybrydowego**, a platforma Azure obsługuje **najnowsze** profilu wersji interfejsu API. Po zainstalowaniu profilu, są zainstalowane moduły usługi Azure Resource Manager w programie PowerShell, które odpowiadają określony profil.

Można zainstalować usługi Azure Stack zgodne modułów programu PowerShell w programie Internet połączenia, częściowo połączone lub rozłączonych scenariuszy. W tym artykule przedstawiono szczegółowe instrukcje, aby zainstalować program PowerShell dla usługi Azure Stack dla tych scenariuszy.

## <a name="1-verify-your-prerequisites"></a>1. Sprawdź swoje wymagania wstępne

Przed rozpoczęciem pracy z usługi Azure Stack i programu PowerShell, musisz mieć następujące wymagania wstępne:

- **Program PowerShell w wersji 5.0** Aby sprawdzić swoją wersję, uruchom **$PSVersionTable.PSVersion** i porównaj **głównych** wersji. Jeśli nie masz programu PowerShell w wersji 5.0, postępuj zgodnie z [Instalowanie programu Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]
  > PowerShell 5.0 wymaga maszyny Windows.

- **Uruchom program Powershell w wierszu polecenia z podwyższonym poziomem uprawnień** należy uruchomić program PowerShell z uprawnieniami administracyjnymi.

- **Dostęp do galerii programu PowerShell** muszą mieć dostęp do [galerii programu PowerShell](https://www.powershellgallery.com). Galeria jest centralnym repozytorium zawartość programu PowerShell. **PowerShellGet** moduł zawiera polecenia cmdlet na potrzeby odnajdywania, instalowania, aktualizowania i publikowania programu PowerShell artefaktów, takich jak moduły, zasoby DSC, możliwości roli i skryptów z galerii programu PowerShell i innych prywatne repozytoriów. Jeśli używasz programu PowerShell w przypadku odłączony, należy pobrać zasoby z komputera z połączeniem z Internetem i przechowywać je w lokalizacji dostępnej dla komputera bez połączenia.


## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Weryfikowanie dostępności galerii programu PowerShell

Sprawdź, czy galerii programu PowerShell jest zarejestrowany jako repozytorium.

> [!Note]
> Ten krok wymaga dostępu do Internetu.

Otwórz wiersz PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia cmdlet:

````PowerShell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
````

Jeśli repozytorium nie jest zarejestrowany, otwórz sesję programu PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie:

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Odinstaluj istniejące wersje modułów platformy Azure Stack PowerShell

Przed rozpoczęciem instalacji wymaganej wersji, upewnij się, odinstalowanie wszelkich uprzednio zainstalowanych modułów AzureRM PowerShell w usłudze Azure Stack. Można je odinstalować, wykonując jedną z następujących dwóch metod:

1. Aby odinstalować istniejące moduły programu PowerShell usługi AzureRM, zamknąć wszystkich aktywnych sesji programu PowerShell i uruchom następujące polecenia cmdlet:

    ````PowerShell
    Uninstall-Module -Name AzureRM.AzureStackAdmin -Force 
    Uninstall-Module -Name AzureRM.AzureStackStorage -Force 
    Uninstall-Module -Name AzureStack -Force -Verbose
    Uninstall-Module -Name AzureRM -Force -Verbose
    Uninstall-Module -Name Azure.Storage -Force -Verbose
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name AzureRM.* -ListAvailable | Uninstall-Module -Force -Verbose
    ````
    Jeśli napotkasz żadnych błędów, takich jak "moduł jest już w użyciu", zamknij sesji programu PowerShell, które korzystają z modułów i ponownie uruchom skrypt powyżej.

2. Usuń wszystkie foldery, które zaczyna się `Azure` z `C:\Program Files\WindowsPowerShell\Modules` i `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` folderów. Usunięcie tych folderów usuwa wszystkie istniejące moduły programu PowerShell.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Połączono: Instalowanie programu PowerShell dla usługi Azure Stack z łącznością z Internetem

Usługa Azure Stack wymaga **2018-03-01-hybrydowego** profilu wersji interfejsu API dla usługi Azure Stack w wersji 1808. Profil jest dostępna po zainstalowaniu **AzureRM.Bootstrapper** modułu. Ponadto z modułami usługi AzureRM, należy również zainstalować moduły programu PowerShell usługi Azure Stack specyficzne dla. Profilu wersji interfejsu API i moduły programu Azure Stack PowerShell wymagane będzie zależeć od usługi Azure Stack w wersji usługi są uruchomione.

Uruchom poniższy skrypt programu PowerShell, aby zainstalować te moduły na deweloperskiej stacji roboczej:

  - Usługa Azure Stack 1808 lub nowszej.

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

> [!Note]
> Aby uaktualnić program Azure PowerShell z **2017-03-09-profile** do **2018-03-01-hybrydowego**, można znaleźć [Przewodnik po migracji](https://github.com/bganapa/azure-powershell/blob/migration-guide/documentation/migration-guides/Stack/migration-guide.2.3.0.md).

  - Usługa Azure Stack 1807 lub starszym.

    ```PowerShell
    Install-Module -Name AzureRm.BootStrapper
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.4.0
    ```

  - Usługa Azure Stack 1804 lub starszym.

    ```PowerShell
    Install-Module -Name AzureRm.BootStrapper
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.2.11
    ```

Aby sprawdzić, uruchamiając następujące polecenie:

```PowerShell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Jeśli instalacja się powiodła, moduł AzureRM i AzureStack są wyświetlane w danych wyjściowych.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Odłączone: Instalowanie programu PowerShell bez połączenia z Internetem

W przypadku odłączonych należy najpierw pobrać modułów programu PowerShell na komputerze, który ma łączność z Internetem, a następnie przenieść je do usługi Azure Stack Development Kit dla instalacji.

Zaloguj się na komputerze z łącznością z Internetem i pobierania pakietów usługi Azure Resource Manager i AzureStack, w zależności od używanej wersji programu Azure Stack za pomocą następujących skryptów:

  - Usługa Azure Stack 1808 lub nowszej.

    ````PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ````

  - Usługa Azure Stack 1807 lub starszym.

    > [!Note]
    Aby uaktualnić 1.2.11 wersji, zobacz [Przewodnik po migracji](https://aka.ms/azspowershellmigration).

    ````PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - Usługa Azure Stack 1804 lub starszym.

    ````PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.3.0
    ````

2. Kopiowanie pakietów do pobrania na urządzenie USB.

3. Zaloguj się do stacji roboczej, a następnie skopiuj pakiety z urządzenia USB do lokalizacji na stacji roboczej.

4. Teraz zarejestrować tę lokalizację jako repozytorium domyślne i zainstaluj moduł AzureRM i AzureStack z tego repozytorium:

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Konfigurowanie programu PowerShell do korzystania z serwera proxy

W scenariuszach, które wymagają serwera proxy, aby uzyskać dostęp do Internetu należy najpierw skonfigurować programu PowerShell, aby korzystał z istniejącego serwera proxy:

1. Otwórz wiersz PowerShell z podwyższonym poziomem uprawnień.
2. Uruchom następujące polecenia:

   ```PowerShell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>Kolejne kroki

 - [Pobierz narzędzia usługi Azure Stack z usługi GitHub](azure-stack-powershell-download.md)
 - [Konfigurowanie środowiska PowerShell użytkownika usługi Azure Stack](user/azure-stack-powershell-configure-user.md)
 - [Konfigurowanie środowiska PowerShell usługi Azure Stack — operator](azure-stack-powershell-configure-admin.md)
 - [Zarządzanie profilami wersji interfejsu API w usłudze Azure Stack](user/azure-stack-version-profiles.md)
