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
ms.date: 02/08/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 7e631281405b173405f28c134432e870c757b3da
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648406"
---
# <a name="install-powershell-for-azure-stack"></a>Instalowanie programu PowerShell dla usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Aby pracować z chmurą, należy zainstalować zgodne modułów programu PowerShell usługi Azure Stack. Zgodność jest włączana za pomocą funkcji zwanej *profilami interfejsu API*.

Profilami interfejsu API umożliwiają zarządzanie wersją różnice między platformą Azure i usługi Azure Stack. Profilu wersji interfejsu API to zbiór modułów Azure PowerShell Resource Manager przy użyciu określonych wersji interfejsu API. Każda platforma w chmurze ma zestaw obsługiwanych profilami wersji interfejsu API. Na przykład usługi Azure Stack obsługuje wersji określonego profilu, takie jak **2018-03-01-hybrydowego**. Po zainstalowaniu profilu, są zainstalowane moduły usługi Azure Resource Manager w programie PowerShell, które odpowiadają określony profil.

Można zainstalować usługi Azure Stack zgodne modułów programu PowerShell w programie Internet połączenia, częściowo połączone lub rozłączonych scenariuszy. W tym artykule przedstawiono szczegółowe instrukcje, aby zainstalować program PowerShell dla usługi Azure Stack dla tych scenariuszy.

## <a name="1-verify-your-prerequisites"></a>1. Sprawdź swoje wymagania wstępne

Przed rozpoczęciem pracy z usługi Azure Stack i programu PowerShell, musisz mieć następujące wymagania wstępne:

- **Program PowerShell w wersji 5.0** Aby sprawdzić swoją wersję, uruchom **$PSVersionTable.PSVersion** i porównaj **głównych** wersji. Jeśli nie masz programu PowerShell w wersji 5.0, postępuj zgodnie z [Instalowanie programu Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]
  > PowerShell 5.0 wymaga maszyny Windows.

- **Uruchom program Powershell w wierszu polecenia z podwyższonym poziomem uprawnień**.
  Należy uruchomić program PowerShell z uprawnieniami administracyjnymi.

- **Dostęp do galerii programu PowerShell** muszą mieć dostęp do [galerii programu PowerShell](https://www.powershellgallery.com). Galeria jest centralnym repozytorium zawartość programu PowerShell. **PowerShellGet** moduł zawiera polecenia cmdlet na potrzeby odnajdywania, instalowania, aktualizowania i publikowania programu PowerShell artefaktów, takich jak moduły, zasoby DSC, możliwości roli i skryptów z galerii programu PowerShell i innych prywatne repozytoriów. Jeśli używasz programu PowerShell w przypadku odłączony, należy pobrać zasoby z komputera z połączeniem z Internetem i przechowywać je w lokalizacji dostępnej dla komputera bez połączenia.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Weryfikowanie dostępności galerii programu PowerShell

Sprawdź, czy galerii programu PowerShell jest zarejestrowany jako repozytorium.

> [!Note]  
> Ten krok wymaga dostępu do Internetu.

Otwórz wiersz PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia cmdlet:

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Jeśli repozytorium nie jest zarejestrowany, otwórz sesję programu PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie:

```powershell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Odinstaluj istniejące wersje modułów platformy Azure Stack PowerShell

Przed rozpoczęciem instalacji wymaganej wersji, upewnij się, odinstalowanie wszelkich uprzednio zainstalowanych modułów AzureRM PowerShell w usłudze Azure Stack. Można je odinstalować, wykonując jedną z następujących dwóch metod:

1. Aby odinstalować istniejące moduły programu PowerShell usługi AzureRM, zamknąć wszystkich aktywnych sesji programu PowerShell i uruchom następujące polecenia cmdlet:

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    Jeśli napotkasz błąd, takie jak "moduł jest już w użyciu", zamknij sesji programu PowerShell, które korzystają z modułów i ponownie uruchom skrypt powyżej.

2. Usuń wszystkie foldery, które zaczyna się `Azure` lub `Azs.` z `C:\Program Files\WindowsPowerShell\Modules` i `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` folderów. Usunięcie tych folderów usuwa wszystkie istniejące moduły programu PowerShell.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Połączone: Instalowanie programu PowerShell dla usługi Azure Stack z łącznością z Internetem

Usługa Azure Stack wymaga **2018-03-01-hybrydowego** profilu wersji interfejsu API dla usługi Azure Stack w wersji 1808 lub nowszej. Profil jest dostępna po zainstalowaniu **AzureRM.BootStrapper** modułu. Ponadto z modułami usługi AzureRM, należy również zainstalować moduły programu PowerShell usługi Azure Stack specyficzne dla. Profilu wersji interfejsu API i moduły programu Azure Stack PowerShell wymagane będzie zależeć od usługi Azure Stack w wersji usługi są uruchomione.

Instalacja ma trzy kroki:

1. Instalowanie usługi Azure Stack PowerShell w zależności od używanej wersji usługi Azure Stack
2. Włącz funkcje dodatkowego miejsca do magazynowania
3. Potwierdzenie instalacji programu PowerShell

### <a name="install-azure-stack-powershell"></a>Instalowanie programu Azure Stack PowerShell

Uruchom poniższy skrypt programu PowerShell, aby zainstalować te moduły na deweloperskiej stacji roboczej:

- Usługa Azure Stack 1901 lub nowszej:

    ```powershell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.

    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Wersja modułu usługi Azure Stack 1.7.1 jest wersja zawierająca przełomowe zmiany. Migrowanie z usługi Azure Stack 1.6.0 można znaleźć w temacie [Przewodnik po migracji](https://aka.ms/azspshmigration171).
    > AzureRm wersja modułu 2.4.0 jest powiązana z istotną zmianę dla polecenia cmdlet Remove-AzureRmStorageAccount. To polecenie cmdlet oczekuje - parametru Force, aby określić usuwania konta magazynu bez potwierdzenia.

- Azure Stack 1811:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

- Usługa Azure Stack 1810 lub starszy:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

> [!Note]  
> Aby uaktualnić program Azure PowerShell z **2017-03-09-profile** do **2018-03-01-hybrydowego**, można znaleźć [Przewodnik po migracji](https://github.com/azure/azure-powershell/blob/AzureRM/documentation/migration-guides/Stack/migration-guide.2.3.0.md).

### <a name="enable-additional-storage-features"></a>Włącz funkcje dodatkowego miejsca do magazynowania

Aby użyć funkcji dodatkowego miejsca do magazynowania (opisanego w sekcji połączonych), Pobierz i zainstaluj następujące pakiety także.

```powershell
# Install the Azure.Storage module version 4.5.0
Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

# Install the AzureRm.Storage module version 5.0.4
Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

# Remove incompatible storage module installed by AzureRM.Storage
Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

# Load the modules explicitly specifying the versions
Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
```

### <a name="confirm-the-installation-of-powershell"></a>Potwierdzenie instalacji programu PowerShell

Aby sprawdzić, uruchamiając następujące polecenie:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Jeśli instalacja się powiodła, moduł AzureRM i AzureStack są wyświetlane w danych wyjściowych.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Odłączone: Instalowanie programu PowerShell bez połączenia z Internetem

W przypadku odłączonych należy najpierw pobrać modułów programu PowerShell na komputerze, który ma łączność z Internetem, a następnie przenieść je do usługi Azure Stack Development Kit dla instalacji.

Zaloguj się na komputerze z łącznością z Internetem i pobierania pakietów usługi Azure Resource Manager i AzureStack, w zależności od używanej wersji programu Azure Stack za pomocą następujących skryptów.

Instalacja obejmuje cztery kroki:

1. Zainstaluj maszynę połączoną usługi Azure Stack PowerShell
2. Włącz funkcje dodatkowego miejsca do magazynowania
3. Transport pakietów programu PowerShell z odłączonej stacji roboczej
4. Potwierdzenie instalacji programu PowerShell

### <a name="install-azure-stack-powershell"></a>Instalowanie programu Azure Stack PowerShell

- Usługa Azure Stack 1901 lub nowszej.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Wersja modułu usługi Azure Stack 1.7.1 jest zmianą przerywającą. Aby przeprowadzić migrację z AzureStack 1.6.0 można znaleźć [Przewodnik po migracji](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

  - Usługa Azure Stack 1811 lub starszym.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

  - Usługa Azure Stack 1809 lub starszym.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

    > [!NOTE]
    > Na komputerach bez połączenia z Internetem zaleca się wykonanie następującego polecenia cmdlet, aby wyłączyć zbieranie danych telemetrii. Może wystąpić spadek wydajności, poleceń cmdlet bez konieczności wyłączania zbierania danych telemetrycznych. Ta opcja ma zastosowanie tylko w przypadku maszyn bez połączenia z Internetem
    > ```powershell
    > Disable-AzureRmDataCollection
    > ```

### <a name="enable-additional-storage-features"></a>Włącz funkcje dodatkowego miejsca do magazynowania

Aby użyć funkcji dodatkowego miejsca do magazynowania (opisanego w sekcji połączonych), Pobierz i zainstaluj następujące pakiety także.

```powershell
$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
```

### <a name="add-your-packages-to-your-workstation"></a>Dodaj pakiety do stacji roboczej

1. Kopiowanie pakietów do pobrania na urządzenie USB.

2. Zaloguj się w odłączonej stacji roboczej, a następnie skopiuj pakiety z urządzenia USB do lokalizacji na stacji roboczej.

3. Teraz zarejestrować tę lokalizację jako repozytorium domyślne i zainstaluj moduł AzureRM i AzureStack z tego repozytorium:

   ```powershell
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

### <a name="confirm-the-installation-of-powershell"></a>Potwierdzenie instalacji programu PowerShell

Aby sprawdzić, uruchamiając następujące polecenie:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Konfigurowanie programu PowerShell do korzystania z serwera proxy

W scenariuszach, które wymagają serwera proxy, aby uzyskać dostęp do Internetu należy najpierw skonfigurować programu PowerShell, aby korzystał z istniejącego serwera proxy:

1. Otwórz wiersz PowerShell z podwyższonym poziomem uprawnień.
2. Uruchom następujące polecenia:

   ```powershell
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
