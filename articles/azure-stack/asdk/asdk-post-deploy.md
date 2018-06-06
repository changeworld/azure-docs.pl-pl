---
title: Po konfiguracji wdrażania dla usługi Azure stosu Development Kit (ASDK) | Dokumentacja firmy Microsoft
description: W tym artykule opisano zmiany zalecaną konfigurację, aby po zainstalowaniu Azure stosu Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: ec5947bc68ba95a7b1e1588c444f4b28a7435f1c
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801547"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Po zakończeniu instalacji ASDK zadania konfiguracji

Po [instalowanie Azure stosu Development Kit (ASDK)](asdk-install.md), trzeba będzie wprowadzić zmiany niektórych zalecaną konfiguracją po instalacji.

## <a name="install-azure-stack-powershell"></a>Instalowanie programu Azure Stack PowerShell

Azure stosu zgodne Azure moduły programu PowerShell są wymagane do pracy z stosu Azure.

Polecenia programu PowerShell dla usługi Azure stosu są instalowane za pośrednictwem galerii programu PowerShell. Aby zarejestrować repozytorium PSGallery, otwórz sesję programu PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie:

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

Profile w wersji interfejsu API umożliwia Określ stosu Azure zgodne AzureRM modułów.  Profile w wersji interfejsu API umożliwiają zarządzanie różnice wersji platformy Azure i stosu Azure. Profil wersji interfejsu API to zbiór modułów programu AzureRM PowerShell z określonych wersji interfejsu API. **AzureRM.Bootstrapper** moduł, który jest dostępny za pośrednictwem galerii programu PowerShell zawiera polecenia cmdlet programu PowerShell, które są wymagane do pracy z profilami wersji interfejsu API.

Można zainstalować najnowsze modułu Azure PowerShell stosu lub bez łączności z Internetem na komputerze-hoście ASDK:

> [!IMPORTANT]
> Przed zainstalowaniem wersji wymagane, upewnij się, że możesz [odinstaluj wszelkie istniejące modułów programu Azure PowerShell](.\.\azure-stack-powershell-install.md#uninstall-existing-versions-of-powershell).

- **W przypadku połączenia internetowego** z ASDK komputera hosta. Uruchom poniższy skrypt programu PowerShell, aby zainstalować te moduły na projektowanie instalacji zestawu:

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  # Install Azure Stack Module Version 1.3.0. If running a pre-1804 version of Azure Stack, change the -RequiredVersion value to 1.2.11.
  Install-Module -Name AzureStack -RequiredVersion 1.3.0 

  ```

  Jeśli instalacja się powiodła, AzureRM i AzureStack moduły są wyświetlane w danych wyjściowych.

- **Bez połączenia z Internetem** z ASDK komputera hosta. W przypadku odłączonych należy najpierw pobrać moduły programu PowerShell na komputerze, na którym ma połączenie z Internetem przy użyciu następujących poleceń programu PowerShell:

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
  # Install Azure Stack Module Version 1.3.0. If running a pre-1804 version of Azure Stack, change the -RequiredVersion value to 1.2.11.  
    -RequiredVersion 1.3.0
  ```

  Następnie skopiuj pakiety pobrane na komputer ASDK i zarejestrować lokalizację jako repozytorium domyślne i zainstalować moduły AzureRM i AzureStack z tego repozytorium:

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

## <a name="download-the-azure-stack-tools"></a>Pobieranie narzędzia Azure stosu

[Narzędzia AzureStack](https://github.com/Azure/AzureStack-Tools) to repozytorium GitHub obsługującego moduły programu PowerShell do zarządzania i wdrażanie zasobów Azure stosu. Aby uzyskać te narzędzia, Klonuj repozytorium GitHub lub folder AzureStack narzędzia pobierania za pomocą następującego skryptu:

  ```PowerShell
  # Change directory to the root directory. 
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip `
    -DestinationPath . `
    -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Sprawdź poprawność instalacji ASDK
Aby upewnić się, że wdrożenie ASDK zakończyła się pomyślnie, można użyć polecenia cmdlet Test-AzureStack wykonaj następujące czynności:

1. Zaloguj się jako AzureStack\AzureStackAdmin na komputerze-hoście ASDK.
2. Otwórz program PowerShell jako administrator (nie PowerShell ISE).
3. Uruchom: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Uruchom: `Test-AzureStack`

Testy zająć kilka minut. Jeśli instalacja zakończyła się pomyślnie, dane wyjściowe podobne:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Jeśli wystąpił błąd, wykonaj kroki rozwiązywania problemów, aby uzyskać pomoc.

## <a name="activate-the-administrator-and-tenant-portals"></a>Aktywować administratora i dzierżawcy portali
Po wdrożeń, które używają usługi Azure AD musisz aktywować zarówno stosu Azure administratora i dzierżawcy portali. Aktywacja zgadza się na zapewnieniu stosu Azure portalu i usługi Azure Resource Manager odpowiednie uprawnienia (wyświetlane na stronie zgoda) dla wszystkich użytkowników z katalogu.

- Portal administratora, przejdź do https://adminportal.local.azurestack.external/guest/signup, przeczytaj informacje, a następnie kliknij przycisk **Akceptuj**. Po zaakceptowaniu, możesz dodać Administratorzy usług, którzy nie są również administratorami dzierżawy katalogu.

- Przejdź do portalu dzierżawcy do https://portal.local.azurestack.external/guest/signup, przeczytaj informacje, a następnie kliknij przycisk **Akceptuj**. Po zaakceptowaniu, w tym katalogu może logowania użytkownika do portalu dzierżawcy. 

> [!NOTE] 
> Jeśli nie są uaktywnione portali, tylko administrator katalogu można zalogować i korzystać portali. Jeśli inny użytkownik się zaloguje, zostanie wyświetlony błąd, który informuje, czy administrator nie ma przyznane uprawnienia do innych użytkowników. Administrator nie natywnie należą do katalogu, który stosu Azure jest zarejestrowany, katalog Azure stos musi dołączany do adresu URL aktywacji. Na przykład jeśli stosu Azure jest zarejestrowany do fabrikam.onmicrosoft.com i użytkownika administratora jest admin@contoso.com, przejdź do https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com aktywować portalu. 

## <a name="reset-the-password-expiration-policy"></a>Resetuj zasady wygasania haseł 
Po wdrożeniu ASDK, aby upewnić się, że hasło dla hosta development kit nie wygasa przed zakończeniem okresu oceny, wykonaj następujące kroki.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Aby zmienić zasady wygasania haseł z programu Powershell:
W konsoli programu Powershell z podwyższonym poziomem uprawnień uruchom polecenie:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Aby ręcznie zmienić zasady wygasania haseł:
1. Na hoście development kit Otwórz **Zarządzanie zasadami grupy** (Konsola zarządzania zasadami grupy. MMC) i przejdź do **Zarządzanie zasadami grupy** — **lasu: azurestack.local** — **domen** — **azurestack.local**.
2. Kliknij prawym przyciskiem myszy **domyślne zasady domeny** i kliknij przycisk **Edytuj**.
3. W edytorze zasad grupy zarządzania, przejdź do **Konfiguracja komputera** — **zasady** — **ustawienia systemu Windows** — **ustawienia zabezpieczeń**— **Zasady konta** — **zasady haseł**.
4. W prawym okienku kliknij dwukrotnie **maksymalny okres ważności hasła**.
5. W **maksymalny okres ważności hasła właściwości** okno dialogowe, zmień **hasło wygaśnie za** do wartości **180**, a następnie kliknij przycisk **OK**.

![Konsola zarządzania zasadami grupy](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>Kolejne kroki
[Rejestr ASDK z platformy Azure](asdk-register.md)
