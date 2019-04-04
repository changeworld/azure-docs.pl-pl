---
title: Opublikuj konfiguracji wdrażania dla usługi Azure Stack Development Kit (ASDK) | Dokumentacja firmy Microsoft
description: W tym artykule opisano zmiany zalecanej konfiguracji, aby po zainstalowaniu usługi Azure Stack Development Kit (ASDK).
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
ms.date: 02/15/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/10/2018
ms.openlocfilehash: 7699c9279138aedfdcfe63fb42e65ad102ac92c9
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652471"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Instalacja ASDK zadania po konfiguracji

Po [instalowania usługi Azure Stack Development Kit (ASDK)](asdk-install.md), należy wprowadzić kilka zmian zalecanej konfiguracji po instalacji, po zalogowaniu się jako AzureStack\AzureStackAdmin na komputerze-hoście ASDK. 

## <a name="install-azure-stack-powershell"></a>Instalowanie programu Azure Stack PowerShell

Usługa Azure Stack, które zgodna modułów programu Azure PowerShell są wymagane do pracy z usługą Azure Stack.

Polecenia programu PowerShell dla usługi Azure Stack są instalowane za pośrednictwem galerii programu PowerShell. Aby zarejestrować repozytorium galerii programu PowerShell, otwórz sesję programu PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Profilami wersji interfejsu API można użyć do określenia zgodne modułów AzureRM usługi Azure Stack.  Profilami wersji interfejsu API umożliwiają zarządzanie wersją różnice między platformą Azure i usługi Azure Stack. Profilu wersji interfejsu API to zbiór modułów AzureRM PowerShell w określonych wersjach interfejsu API. **AzureRM.Bootstrapper** moduł, który jest dostępny za pośrednictwem galerii programu PowerShell zawiera polecenia cmdlet programu PowerShell, które są wymagane do pracy z profilami wersji interfejsu API.

Najnowszy moduł Azure Stack PowerShell można zainstalować z lub bez połączenia internetowego z komputera hosta ASDK:

> [!IMPORTANT]
> Przed rozpoczęciem instalacji wymaganej wersji, upewnij się, że możesz [Odinstaluj wszystkie istniejące moduły programu Azure PowerShell](../azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Połączenie z Internetem** z komputera hosta ASDK. Uruchom poniższy skrypt programu PowerShell, aby zainstalować te moduły w rozwoju instalacji zestawu:

- Usługa Azure Stack 1901 lub nowszej:

    ```powershell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Wersja modułu usługi Azure Stack 1.7.1 jest zmianą przerywającą. Migrowanie z usługi Azure Stack 1.6.0 można znaleźć w temacie [Przewodnik po migracji](https://aka.ms/azspshmigration171).

  - Azure Stack 1811:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.6.0.
    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

  - Usługa Azure Stack 1810 lub starszy:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.5.0.
    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

  Jeśli instalacja się powiodła, moduł AzureRM i AzureStack są wyświetlane w danych wyjściowych.

- **Bez połączenia z Internetem** z komputera hosta ASDK. W przypadku odłączony należy najpierw pobrać modułów programu PowerShell na komputerze, który ma łączność z Internetem przy użyciu następujących poleceń programu PowerShell:

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Następnie skopiuj pakiety pobrane na komputer ASDK i zarejestrować lokalizację jako repozytorium domyślne i zainstaluj moduł AzureRM i AzureStack z tego repozytorium:

    ```powershell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

    Install-Module AzureRM -Repository $RepoName

    Install-Module AzureStack -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Pobierz narzędzia usługi Azure Stack

[Narzędzia AzureStack](https://github.com/Azure/AzureStack-Tools) jest repozytorium GitHub, który jest hostem modułów programu PowerShell do zarządzania i wdrażania zasobów w usłudze Azure Stack. Do uzyskania tych narzędzi, sklonuj repozytorium GitHub lub pobrać folderu Narzędzia AzureStack, uruchamiając następujący skrypt:

  ```powershell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip -DestinationPath . -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Zweryfikuj instalację ASDK

Aby upewnić się, że wdrożenie ASDK zakończyło się pomyślnie, można użyć polecenia cmdlet Test-AzureStack wykonaj następujące czynności:

1. Zaloguj się jako AzureStack\AzureStackAdmin na komputerze-hoście ASDK.
2. Otwórz program PowerShell jako administrator (nie PowerShell ISE).
3. Uruchom polecenie: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Uruchom polecenie: `Test-AzureStack`

Testy potrwać kilka minut. Jeśli instalacja się powiodła, dane wyjściowe wyglądają mniej więcej tak:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Jeśli wystąpił błąd, wykonaj kroki rozwiązywania problemów w celu uzyskania pomocy.

## <a name="reset-the-password-expiration-policy"></a>Resetuj zasady wygasania haseł 

Po wdrożeniu ASDK, aby upewnić się, że hasło dla hosta kit rozwoju nie wygasa przed zakończeniem okresu Twojej oceny, wykonaj następujące kroki.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Aby zmienić zasady wygasania haseł za pomocą programu Powershell

W konsoli programu Powershell z podwyższonym poziomem uprawnień uruchom polecenie:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Aby ręcznie zmienić zasady wygasania haseł

1. Na hoście development kit Otwórz **Zarządzanie zasadami grupy** (Konsola zarządzania zasadami grupy. MMC) i przejdź do **Zarządzanie zasadami grupy** — **lasu: azurestack.local** — **domen** — **azurestack.local**.
2. Kliknij prawym przyciskiem myszy **domyślne zasady domeny** i kliknij przycisk **Edytuj**.
3. W edytorze zasad grupy zarządzania, przejdź do **konfiguracji komputera** — **zasady** — **ustawienia Windows** — **ustawienia zabezpieczeń**— **Zasady konta** — **zasady haseł**.
4. W okienku po prawej stronie, kliknij dwukrotnie **maksymalny okres ważności hasła**.
5. W **maksymalny okres ważności hasła właściwości** okno dialogowe, zmiana **hasło wygaśnie** wartość **180**, a następnie kliknij przycisk **OK**.

![Konsola zarządzania zasadami grupy](media/asdk-post-deploy/gpmc.png)

## <a name="enable-multi-tenancy"></a>Włączanie wielodostępu

W przypadku wdrożeń za pomocą usługi Azure AD, musisz [Włączanie wielodostępu](../azure-stack-enable-multitenancy.md#enable-multi-tenancy) ASDK instalacji.

> [!NOTE]
> W przypadku używania konta administratora lub użytkownika z domen innych niż ta, używane do rejestrowania usługi Azure Stack zalogować się do portalu Azure Stack nazwy domeny używane do rejestrowania usługi Azure Stack musi być przypisany do portalu adresu url. Na przykład, jeśli usługi Azure Stack został zarejestrowany za pomocą fabrikam.onmicrosoft.com i konto użytkownika, logowanie jest admin@contoso.com, adres URL na potrzeby zaloguj się do portalu użytkownika: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Kolejne kroki

[Zarejestruj ASDK z platformą Azure](asdk-register.md)
