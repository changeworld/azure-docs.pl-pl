---
title: Przed wdrożeniem usługi aplikacji Azure stosu | Dokumentacja firmy Microsoft
description: Kroki, aby zakończyć przed wdrożeniem usługi aplikacji Azure stosu
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2018
ms.author: anwestg
ms.openlocfilehash: ae21a7cc5c38fefd40a2676e15308b027c6f95d5
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796737"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Przed rozpoczęciem pracy z usługi aplikacji Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

> [!IMPORTANT]
> Zastosowanie aktualizacji 1804 systemu Azure stosu zintegrowane lub wdrożyć najnowszy zestaw deweloperski stosu Azure przed wdrożeniem usługi Azure App Service 1.2.
>
>

Przed wdrożeniem usługi Azure App Service na stosie Azure musi spełnić wymagania wstępne w tym artykule.

## <a name="download-the-installer-and-helper-scripts"></a>Pobierz skrypty Instalatora i pomocnika

1. Pobierz [usługi aplikacji na skryptów pomocnika wdrażania stosu Azure](https://aka.ms/appsvconmashelpers).
2. Pobierz [usługi aplikacji na stos Azure Instalatora](https://aka.ms/appsvconmasinstaller).
3. Wyodrębnij pliki z pliku zip skryptów pomocnika. Są wyświetlane następujące pliki i struktury folderów:
   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Moduły
     - GraphAPI.psm1

## <a name="high-availability"></a>Wysoka dostępność

Z powodu wersji 1802 Azure stosu, w którym dodano obsługę domen błędów, nowe wdrożenia usługi Azure App Service na stosie Azure będą dystrybuowane między domenami usterek i zapewnienia odporności na uszkodzenia.  Aby istniejące wdrożenia usługi Azure App Service na stosie Azure, które zostały wdrożone przed wydaniem 1802 aktualizacji, zobacz [dokumentacji](azure-stack-app-service-fault-domain-update.md) jak Równoważenie wdrożenia.

Ponadto usługi Azure App Service na stosie Azure wysokiej dostępności, Wdróż wymaganego pliku serwer i wystąpienie programu SQL Server w konfiguracji o wysokiej dostępności.

## <a name="get-certificates"></a>Uzyskanie certyfikatów

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Usługa Azure Resource Manager certyfikat główny dla stosu Azure

W sesji programu PowerShell uruchomiona jako azurestack\CloudAdmin na komputerze, który można uzyskać dostęp do uprzywilejowanych punktu końcowego Azure stosu zintegrowany System lub Azure stosu Development Kit Host, uruchom skrypt Get AzureStackRootCert.ps1 z folderu, w którym została rozpakowana skrypty pomocnika. Skrypt tworzy certyfikat główny, w tym samym folderze, skrypt, który wymaga usługi aplikacji do tworzenia certyfikatów.

```PowerShell
    Get-AzureStackRootCert.ps1
```

| Parametr | Wymagane lub opcjonalne | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Wymagane | AzS-ERCS01 | Uprzywilejowane punktu końcowego |
| CloudAdminCredential | Wymagane | AzureStack\CloudAdmin | Poświadczenia konta domeny dla administratorów chmury Azure stosu |

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Certyfikaty wymagane dla zestawu SDK usługi Azure stosu

Pierwszy skryptu współpracuje z urzędu certyfikacji stosu Azure można utworzyć cztery certyfikaty, które wymaga usługi aplikacji:

| Nazwa pliku | Użycie |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Hasło domyślnego certyfikatu protokołu SSL usługi App Service |
| api.appservice.local.azurestack.external.pfx | Certyfikat SSL interfejsu API usługi aplikacji |
| ftp.appservice.local.azurestack.external.pfx | Certyfikat SSL wydawcy usługi aplikacji |
| sso.appservice.local.azurestack.external.pfx | Certyfikat aplikacji tożsamości usługi aplikacji |

Uruchom skrypt na hoście Azure stosu Development Kit i upewnij się, że używasz programu PowerShell jako azurestack\CloudAdmin:

1. W sesji programu PowerShell, uruchomione jako azurestack\AzureStackAdmin Uruchom skrypt AppServiceCerts.ps1 Utwórz z folderu, w którym została rozpakowana skrypty pomocnika. Skrypt tworzy cztery certyfikatów, w tym samym folderze, skrypt, który wymaga usługi aplikacji do tworzenia certyfikatów.
2. Wprowadź hasło, aby zabezpieczyć pliki PFX i zapisz go. Wprowadź go w usłudze App Service w Instalatorze stosu Azure.

#### <a name="create-appservicecertsps1-parameters"></a>Utwórz AppServiceCerts.ps1 parametrów

```PowerShell
    Create-AppServiceCerts.ps1
```

| Parametr | Wymagane lub opcjonalne | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| pfxPassword | Wymagane | Null | Hasła, która pomaga chronić klucz prywatny certyfikatu |
| DomainName | Wymagane | local.azurestack.external | Azure sufiks regionu i domeny stosu |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Certyfikaty wymagane w przypadku wdrożenia produkcyjnego usługi Azure App Service na stosie Azure

Działanie dostawcy zasobów w środowisku produkcyjnym, należy podać cztery następujących certyfikatów:

#### <a name="default-domain-certificate"></a>Domyślny certyfikat domeny

Domyślny certyfikat domeny jest umieszczany w roli Serwer sieci Web. Aplikacje użytkownika żądań domeny symboli wieloznacznych lub wartość domyślną w usłudze Azure App Service użycie tego certyfikatu. Certyfikat służy także do operacji kontroli źródła (Kudu).

Należy certyfikat uniwersalny trzech podmiotu certyfikatu i musi być w formacie pfx. To wymaganie umożliwia jeden certyfikat pokrywał się zarówno w domenie domyślnej, jak i punktu końcowego SCM operacji kontroli źródła.

| Format | Przykład |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certyfikat interfejsu API

Certyfikat interfejsu API zostanie umieszczony w roli zarządzania. Dostawca zasobów używa go, aby ułatwić bezpieczne wywołania interfejsu API. Certyfikat do opublikowania musi zawierać którego podmiot odpowiada wpis DNS interfejsu API.

| Format | Przykład |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Publikowanie certyfikatów

Certyfikat dla roli wydawcy zabezpiecza ruch FTPS dla właścicieli aplikacji, gdy ich przekazać zawartość. Certyfikat do opublikowania musi zawierać którego podmiot odpowiada wpis FTPS DNS.

| Format | Przykład |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certyfikat tożsamości

Certyfikat tożsamości aplikacji umożliwia:

- Integrację usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS) katalogu stosu Azure i usługi aplikacji do obsługi integracji z dostawcy zasobów obliczeniowych.
- Pojedynczy logowania jednokrotnego scenariusze dotyczące narzędzia dla zaawansowanych programistów w usłudze Azure App Service na stosie Azure.

Certyfikat tożsamości musi zawierać którego podmiot odpowiada następujący format:

| Format | Przykład |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

## <a name="virtual-network"></a>Virtual Network

Usługa aplikacji Azure na stosie Azure umożliwia wdrażanie dostawcy zasobów do albo istniejącej sieci wirtualnej lub usługi aplikacji — zostanie utworzona jako część wdrożenia.  Za pomocą istniejącej sieci wirtualnej umożliwia korzystanie z wewnętrznych adresów IP do nawiązania połączenia przez serwer plików i programu SQL server wymagane przez usługę Azure App Service na stosie Azure.  Sieć wirtualna musi być skonfigurowany z następujące podsieci i zakres adresów, przed zainstalowaniem usługi Azure App Service na stosie Azure:

Sieć wirtualna — / 16 do /

Podsieci

- ControllersSubnet prefiksie/24
- ManagementServersSubnet /24
- FrontEndsSubnet prefiksie/24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="prepare-the-file-server"></a>Przygotowywanie serwera plików

Usługa aplikacji Azure wymaga użycia serwera plików. Wdrożeń produkcyjnych serwer plików musi być skonfigurowane jako wysokiej dostępności i może obsługiwać błędy.

Azure stosu Development Kit tylko w przypadku wdrożeń, można użyć [Szablon wdrożenia usługi Azure Resource Manager przykład](https://aka.ms/appsvconmasdkfstemplate) do wdrożenia serwera skonfigurowanego pliku jednym węzłem. Serwer plików z jednym węzłem będą należeć do grupy roboczej.

>[!IMPORTANT]
> Jeśli wybierzesz do wdrożenia usługi aplikacji w ramach istniejącej sieci wirtualnej z serwerem plików powinny zostać wdrożone w osobnej podsieci z usługi aplikacji.
>

### <a name="provision-groups-and-accounts-in-active-directory"></a>Zapewnij grup i kont w usłudze Active Directory

1. Utwórz następujące grupy zabezpieczeń globalnych usługi Active Directory:
   - FileShareOwners
   - FileShareUsers
2. Utwórz następujące konta usługi Active Directory jako konta usług:
   - FileShareOwner
   - FileShareUser

   Bezpieczeństwa najlepiej użytkowników dla tych kont (i dla wszystkich ról sieci web) powinny się różnić od siebie i ma silnej nazwy użytkowników i hasła. Ustaw hasła z następujących warunków:
   - Włącz **hasło nigdy nie wygasa**.
   - Włącz **użytkownik nie może zmienić hasła**.
   - Wyłącz **użytkownik musi zmienić hasło przy następnym logowaniu**.
3. Dodaj konta do członkostwa w grupach w następujący sposób:
   - Dodaj **właściciel udziału plików** do **FileShareOwners** grupy.
   - Dodaj **FileShareUser** do **FileShareUsers** grupy.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Zapewnij grup i kont w grupie roboczej

>[!NOTE]
> Podczas konfiguracji serwera plików, uruchom następujące polecenia w oknie wiersza polecenia administratora. *Nie należy używać środowiska PowerShell.*

Gdy używasz szablonu usługi Azure Resource Manager, użytkownicy, zostały już utworzone.

1. Uruchom następujące polecenia, aby utworzyć konta Właściciel udziału plików i FileShareUser. Zastąp `<password>` z własne wartości.
    ``` DOS
    net user FileShareOwner <password> /add /expires:never /passwordchg:no
    net user FileShareUser <password> /add /expires:never /passwordchg:no
    ```
2. Ustaw hasła dla konta, które nigdy nie wygasa, uruchamiając następujące polecenia WMIC:
    ``` DOS
    WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
    WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
    ```
3. Utwórz grupy lokalne FileShareUsers i FileShareOwners i dodać konta w pierwszym kroku do nich:
    ``` DOS
    net localgroup FileShareUsers /add
    net localgroup FileShareUsers FileShareUser /add
    net localgroup FileShareOwners /add
    net localgroup FileShareOwners FileShareOwner /add
    ```

### <a name="provision-the-content-share"></a>Udostępnianie zawartości udostępnionej

Udział zawartości zawiera zawartość witryny sieci Web dzierżawy. Procedura udostępniania zawartości udostępnionej na jednym serwerze plików jest taka sama dla środowisk usługi Active Directory, jak i grupy roboczej. Ale różni się w klastrze pracy awaryjnej w usłudze Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Udostępnianie zawartości udostępnionej na jednym serwerze plików (usługi Active Directory lub grupy roboczej)

Na jednym serwerze plików, uruchom następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień. Zastąp wartość `C:\WebSites` z odpowiednich ścieżkach w danym środowisku.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="add-the-fileshareowners-group-to-the-local-administrators-group"></a>Dodaj grupę FileShareOwners do lokalnej grupy administratorów

Dla zdalnego zarządzania systemem Windows działała prawidłowo należy dodać grupę FileShareOwners do lokalnej grupy Administratorzy.

#### <a name="active-directory"></a>Usługa Active Directory

Uruchom następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień na serwerze plików lub na każdym serwerze plików, który działa jako węzeł klastra pracy awaryjnej. Zastąp wartość `<DOMAIN>` wraz z nazwą domeny, którego chcesz używać.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Grupa robocza

Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień na serwerze plików:

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>Konfigurowanie kontroli dostępu do udziałów

Uruchom następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień na serwerze plików lub w węźle klastra trybu failover, który jest bieżącym właścicielem zasobu klastra. Zastąp wartości kursywą wartości, które są specyficzne dla danego środowiska.

#### <a name="active-directory"></a>Usługa Active Directory

```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Grupa robocza

```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server-instance"></a>Przygotowanie wystąpienie programu SQL Server

Usługi aplikacji Azure na zliczania baz danych i hostingu Azure stosu należy przygotować wystąpienia programu SQL Server do przechowywania bazy danych usługi aplikacji.

W przypadku wdrożeń Azure stosu Development Kit można użyć programu SQL Server Express 2014 z dodatkiem SP2 lub nowszym.

Do celów wysokiej dostępności i produkcji, należy użyć pełną wersję programu SQL Server 2014 SP2 lub później, Włącz uwierzytelnianie w trybie mieszanym i wdrożyć w [konfiguracji wysokiej dostępności](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Wystąpienie programu SQL Server dla usługi Azure App Service na stosie Azure musi być dostępny ze wszystkich ról usługi aplikacji. Można wdrożyć program SQL Server w ramach subskrypcji domyślny dostawca w stosie Azure. Można też użyć istniejącej infrastruktury, w ramach organizacji (o ile istnieje łączność stos Azure). Jeśli używasz portalu Azure Marketplace obrazu, należy odpowiednio skonfigurować zaporę.

>[!NOTE]
> Liczba obrazów maszyn wirtualnych SQL IaaS są dostępne za pośrednictwem funkcji zarządzania Marketplace. Upewnij się, że zawsze pobieranie najnowszej wersji rozszerzenia IaaS SQL, przed wdrożeniem maszyny Wirtualnej przy użyciu elementu portalu Marketplace. Obrazy SQL są takie same jak SQL maszyn wirtualnych, które są dostępne w systemie Azure. Dla maszyn wirtualnych SQL utworzony na podstawie tych obrazów, rozszerzenia IaaS i odpowiedniego rozszerzenia portalu udostępnia funkcje, takie jak automatyczne stosowanie poprawek i możliwości tworzenia kopii zapasowej.
>
Dla każdej z ról serwera SQL można użyć wystąpienie domyślne lub nazwane wystąpienie. Jeśli używasz nazwanego wystąpienia, należy ręcznie uruchomić usługę SQL Server Browser i otworzyć port 1434.

>[!IMPORTANT]
> Jeśli wybierzesz do wdrożenia usługi aplikacji w ramach istniejącej sieci wirtualnej programu SQL Server powinny zostać wdrożone w osobnej podsieci z usługi aplikacji i serwerze plików.
>

## <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji usługi Azure Active Directory

Skonfiguruj nazwę główną usługi Azure AD do obsługi następujących operacji:

- Zestawu skalowania maszyn wirtualnych integracji, w warstwach procesu roboczego.
- Usługa rejestracji Jednokrotnej dla narzędzi deweloperskich portalu i zaawansowane funkcje platformy Azure.

Czynności te dotyczą tylko w środowiskach stosu Azure zabezpieczonej przez usługi AD platformy Azure.

Administratorzy, należy skonfigurować logowanie Jednokrotne:

- Włącz narzędzia dla zaawansowanych programistów w ramach usługi App Service (Kudu).
- Korzystanie z obsługi portalu usługi Azure Functions.

Wykonaj następujące kroki:

1. Otwórz wystąpienie programu PowerShell jako azurestack\AzureStackAdmin.
2. Przejdź do lokalizacji skrypty, które pobrane i wyodrębnione w [wymagań wstępnych krok](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instalowanie programu PowerShell dla usługi Azure stosu](azure-stack-powershell-install.md).
4. Uruchom **AADIdentityApp.ps1 Utwórz** skryptu. Po wyświetleniu monitu wprowadź identyfikator dzierżawy usługi Azure AD, używany dla danego wdrożenia stosu Azure. Na przykład wprowadź **myazurestack.onmicrosoft.com**.
5. W **poświadczeń** okna, wprowadź konto administratora usługi Azure AD i hasło. Kliknij przycisk **OK**.
6. Wprowadź hasło certyfikatu i ścieżka do pliku certyfikatu [wcześniej utworzony certyfikat](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Certyfikat utworzony dla tego kroku, domyślnie jest **sso.appservice.local.azurestack.external.pfx**.
7. Skrypt tworzy nową aplikację w wystąpieniu usługi Azure AD dzierżawy. Zanotuj identyfikator aplikacji, który jest zwracany w danych wyjściowych programu PowerShell. To potrzebne podczas instalacji.
8. Otwórz nowe okno przeglądarki i zaloguj się do [portalu Azure](https://portal.azure.com) jako administratora usługi Azure Active Directory.
9. Otwórz dostawcy zasobów usługi Azure AD.
10. Wybierz **rejestracji aplikacji**.
11. Wyszukaj identyfikator aplikacji zwracane w ramach kroku 7. Aplikacja usługi aplikacji znajduje się.
12. Wybierz **aplikacji** na liście.
13. Kliknij przycisk **ustawienia**.
14. Wybierz **wymagane uprawnienia** > **udzielić uprawnień** > **tak**.

```PowerShell
    Create-AADIdentityApp.ps1
```

| Parametr | Wymagane lub opcjonalne | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| DirectoryTenantName | Wymagane | Null | Identyfikator dzierżawy usługi Azure AD Podaj identyfikator GUID lub ciąg. Przykładem jest myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Wymagane | Null | Punkt końcowy administratora usługi Azure Resource Manager. Przykładem jest adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Wymagane | Null | Punkt końcowy usługi Azure Resource Manager dzierżawy. Przykładem jest management.local.azurestack.external. |
| AzureStackAdminCredential | Wymagane | Null | Poświadczenie administratora usługi Azure AD. |
| CertificateFilePath | Wymagane | Null | **Pełna ścieżka** wcześniej wygenerowany plik certyfikatu tożsamości aplikacji. |
| CertificatePassword | Wymagane | Null | Hasło chroni klucz prywatny certyfikatu. |

## <a name="create-an-active-directory-federation-services-application"></a>Tworzenie aplikacji usług federacyjnych Active Directory

W przypadku środowisk Azure stosu zabezpieczonej przez usługi AD FS należy skonfigurować nazwy głównej usługi AD FS do obsługi następujących operacji:

- Zestawu skalowania maszyn wirtualnych integracji, w warstwach procesu roboczego.
- Usługa rejestracji Jednokrotnej dla narzędzi deweloperskich portalu i zaawansowane funkcje platformy Azure.

Administratorzy, należy skonfigurować logowanie Jednokrotne:

- Konfigurowanie nazwy głównej usługi integracji zestawu skali maszyny wirtualnej w warstwach procesu roboczego.
- Włącz narzędzia dla zaawansowanych programistów w ramach usługi App Service (Kudu).
- Korzystanie z obsługi portalu usługi Azure Functions.

Wykonaj następujące kroki:

1. Otwórz wystąpienie programu PowerShell jako azurestack\AzureStackAdmin.
2. Przejdź do lokalizacji skrypty, które pobrane i wyodrębnione w [wymagań wstępnych krok](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instalowanie programu PowerShell dla usługi Azure stosu](azure-stack-powershell-install.md).
4. Uruchom **ADFSIdentityApp.ps1 Utwórz** skryptu.
5. W **poświadczeń** okna, wprowadź konto administratora usług AD FS chmury i hasło. Kliknij przycisk **OK**.
6. Ścieżka do pliku certyfikatu i hasła certyfikatu dla [wcześniej utworzony certyfikat](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Certyfikat utworzony dla tego kroku, domyślnie jest **sso.appservice.local.azurestack.external.pfx**.

```PowerShell
    Create-ADFSIdentityApp.ps1
```

| Parametr | Wymagane lub opcjonalne | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| AdminArmEndpoint | Wymagane | Null | Punkt końcowy administratora usługi Azure Resource Manager. Przykładem jest adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Wymagane | Null | Punkt końcowy uprzywilejowanych. Przykładem jest AzS ERCS01. |
| CloudAdminCredential | Wymagane | Null | Poświadczenia konta domeny dla administratorów chmury Azure stosu. Przykładem jest Azurestack\CloudAdmin. |
| CertificateFilePath | Wymagane | Null | **Pełna ścieżka** do pliku PFX certyfikatu tożsamości aplikacji. |
| CertificatePassword | Wymagane | Null | Hasło chroni klucz prywatny certyfikatu. |

## <a name="next-steps"></a>Kolejne kroki

[Zainstaluj dostawcę zasobów usługi aplikacji](azure-stack-app-service-deploy.md)
