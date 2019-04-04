---
title: Przed wdrożeniem usługi App Service w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Kroki, aby ukończyć przed wdrożeniem usługi App Service w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: eb2c01052b8dc5fe346196a64e3fcbf7d1b69c2b
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485754"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Przed rozpoczęciem pracy z usługą App Service w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Przed wdrożeniem usługi Azure App Service w usłudze Azure Stack, należy wykonać kroki wymagań wstępnych, w tym artykule.

> [!IMPORTANT]
> Zastosowanie aktualizacji 1901 do systemu Azure Stack zintegrowane, lub wdrożyć najnowszą usługi Azure Stack Development Kit (ASDK) przed wdrożeniem Azure App Service w wersji 1.5.

## <a name="download-the-installer-and-helper-scripts"></a>Pobieranie skryptów Instalatora i pomocnika

1. Pobierz [usługi App Service na skrypty pomocnika wdrożenia usługi Azure Stack](https://aka.ms/appsvconmashelpers).
2. Pobierz [usługi App Service w usłudze Azure Stack Instalatora](https://aka.ms/appsvconmasinstaller).
3. Wyodrębnij pliki z pliku zip skrypty pomocnika. Wyodrębnione są następujące pliki i foldery:

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Folder modułów
     - GraphAPI.psm1

## <a name="syndicate-the-custom-script-extension-from-the-marketplace"></a>Zespól rozszerzenia niestandardowego skryptu z witryny Marketplace

Usługa Azure App Service w usłudze Azure Stack wymaga v1.9.1 rozszerzenia niestandardowego skryptu.  Musi mieć rozszerzenie [zespolone z witryny Marketplace](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) przed rozpoczęciem wdrażania lub uaktualnienie usługi Azure App Service w usłudze Azure Stack

## <a name="get-certificates"></a>Pobierz certyfikaty

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Usługa Azure Resource Manager certyfikat główny dla usługi Azure Stack

Otwórz sesję programu PowerShell z podwyższonym poziomem uprawnień na komputerze, który może osiągnąć uprzywilejowanych punktu końcowego usługi Azure Stack zintegrowany System lub Azure stosu Development Kit Host.

Uruchom *Get AzureStackRootCert.ps1* skryptów z folderu, w którym została rozpakowana skrypty pomocnika. Skrypt utworzy certyfikat główny, w tym samym folderze co skrypt, który potrzebuje tworzenia certyfikatów usługi App Service.

Po uruchomieniu następującego polecenia programu PowerShell, należy przewidzieć AzureStack\CloudAdmin uprzywilejowanych punktu końcowego i poświadczenia.

```powershell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-AzureStackRootCert.ps1 script parameters

| Parametr | Wymagane lub opcjonalne | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Wymagane | AzS-ERCS01 | Punkt końcowy uprzywilejowanych |
| CloudAdminCredential | Wymagane | AzureStack\CloudAdmin | Poświadczenia konta domeny dla administratorów chmury Azure Stack |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Certyfikaty wymagane do wdrożenia ASDK usługi Azure App Service

*AppServiceCerts.ps1 Utwórz* działania skryptu z urzędem certyfikacji usługi Azure Stack, aby utworzyć cztery certyfikatów, których usługa App Service.

| Nazwa pliku | Użycie |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Hasło domyślnego certyfikatu protokołu SSL usługi App Service |
| api.appservice.local.azurestack.external.pfx | Certyfikat protokołu SSL interfejsu API usługi aplikacji |
| ftp.appservice.local.azurestack.external.pfx | Certyfikat protokołu SSL wydawcy usługi App Service |
| sso.appservice.local.azurestack.external.pfx | Certyfikat aplikacji tożsamości usługi App Service |

Aby utworzyć certyfikaty, wykonaj następujące kroki:

1. Zaloguj się przy użyciu konta AzureStack\AzureStackAdmin hosta usługi Azure Stack Development Kit.
2. Otwórz sesję programu PowerShell z podwyższonym poziomem uprawnień.
3. Uruchom *AppServiceCerts.ps1 Utwórz* skryptów z folderu, w którym została rozpakowana skrypty pomocnika. Ten skrypt tworzy cztery certyfikaty, w tym samym folderze co skrypt, który potrzebuje tworzenia certyfikatów usługi App Service.
4. Wprowadź hasło, aby zabezpieczyć pliki PFX, a następnie zanotuj go. Musisz wprowadzić go w usłudze App Service na temat Instalatora usługi Azure Stack.

#### <a name="create-appservicecertsps1-script-parameters"></a>Create-AppServiceCerts.ps1 script parameters

| Parametr | Wymagane lub opcjonalne | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| pfxPassword | Wymagane | Null | Hasło, które chroni klucz prywatny certyfikatu |
| DomainName | Wymagane | local.azurestack.external | Azure Stack region i domeny sufiks |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>Certyfikaty wymagane do wdrożenia produkcyjnego usługi Azure Stack w usłudze Azure App Service

Aby uruchomić dostawcy zasobów w środowisku produkcyjnym, należy podać następujące certyfikaty:

- Domyślny certyfikat domeny
- Certyfikat interfejsu API
- Publikowanie certyfikatów
- Certyfikat tożsamości

#### <a name="default-domain-certificate"></a>Domyślny certyfikat domeny

Domyślny certyfikat domeny jest umieszczany w roli frontonu. Aplikacje użytkownika dla symboli wieloznacznych lub domyślne żądania domeny w usłudze Azure App Service, użyj tego certyfikatu. Certyfikat służy także do operacji kontroli źródła (Kudu).

Certyfikat musi być w formacie pfx i powinna być certyfikat wieloznaczny podmiotu trzeciego. To wymaganie umożliwia jeden certyfikat pokrywać zarówno domyślnej domeny i punkt końcowy SCM dla operacji kontroli źródła.

| Format | Przykład |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certyfikat interfejsu API

Certyfikat interfejsu API jest umieszczany w roli zarządzania. Dostawca zasobów używa go, aby pomóc bezpiecznych wywołań interfejsu API. Certyfikat do publikacji musi zawierać podmiotem, który odpowiada wpis DNS interfejsu API.

| Format | Przykład |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Publikowanie certyfikatów

Certyfikat dla roli wydawcy zabezpiecza ruch FTPS dla właścicieli aplikacji, gdy ich przekazywanie zawartości. Certyfikat do publikacji musi zawierać podmiotem, który odpowiada wpis FTPS DNS.

| Format | Przykład |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certyfikat tożsamości

Certyfikat aplikacji tożsamości umożliwia:

- Integrację usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS) katalogu usługi Azure Stack i App Service pozwala na integrację z dostawcą zasobów obliczeniowych.
- Pojedynczego logowania scenariusze dotyczące narzędzi dla zaawansowanych programistów w ramach usługi Azure App Service w usłudze Azure Stack.

Certyfikat tożsamości musi zawierać podmiotem, który odpowiada następujący format.

| Format | Przykład |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

### <a name="validate-certificates"></a>Sprawdzanie poprawności certyfikatów

Przed wdrożeniem dostawcy zasobów usługi app service, wykonaj następujące czynności [weryfikują certyfikaty, które ma być używany](azure-stack-validate-pki-certs.md#perform-platform-as-a-service-certificate-validation) za pomocą narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack dostępnym [galerii programu PowerShell](https://aka.ms/AzsReadinessChecker). Narzędzie sprawdzania gotowości stosu Azure sprawdza, czy wygenerowane certyfikaty PKI są odpowiednie na potrzeby wdrożenia usług aplikacji.

Najlepszym rozwiązaniem, pracując ze wszystkimi niezbędne [certyfikatów infrastruktury kluczy publicznych do usługi Azure Stack](azure-stack-pki-certs.md), należy zaplanować zostawić czas do testowania i ponownego wystawienia certyfikatów, jeśli to konieczne.

## <a name="virtual-network"></a>Sieć wirtualna

> [!NOTE]
> Wstępne tworzenie niestandardowa sieć wirtualna jest opcjonalne, ponieważ usługi Azure App Service w usłudze Azure Stack można utworzyć wymagane sieci wirtualnej, ale konieczne będzie komunikować się z języków SQL i serwera plików przy użyciu publicznych adresów IP.

Usługa Azure App Service w usłudze Azure Stack umożliwia wdrażanie dostawcy zasobów do istniejącej sieci wirtualnej lub umożliwia tworzenie sieci wirtualnej jako część wdrożenia. Korzystanie z istniejącej sieci wirtualnej umożliwia użycie wewnętrznych adresów IP, aby nawiązać połączenie serwera plików i programu SQL server wymagane przez usługę Azure App Service w usłudze Azure Stack. Sieć wirtualna musi być skonfigurowany z następujących podsieci i zakres adresów, przed zainstalowaniem usługi Azure App Service w usłudze Azure Stack:

Virtual Network - /16

Podsieci

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="licensing-concerns-for-required-file-server-and-sql-server"></a>Licencjonowanie uwagi dotyczące wymaganego pliku server i SQL Server

Usługa Azure App Service w usłudze Azure Stack wymaga serwera plików i programu SQL Server do działania.  Mogą korzystać z istniejących zasobów znajdujących się poza wdrożenia usługi Azure Stack lub wdrożenie zasobów w ramach ich domyślne dostawcy subskrypcji usługi Azure Stack.

Jeśli zdecydujesz się do wdrażania zasobów w ramach usługi Azure Stack domyślne dostawcy subskrypcji licencji dla tych zasobów (licencjom systemu Windows Server i licencji programu SQL Server) są wliczane w koszt usługi Azure App Service w usłudze Azure Stack zgodnie z następującymi ograniczenia:

- infrastruktura jest wdrażana w **domyślne subskrypcję dostawcy**;
- Infrastruktura wyłącznie jest używany przez usługę Azure App Service dla dostawcy zasobów usługi Azure Stack.  Nie ma innych obciążeń administracyjne (innych dostawców zasobów, na przykład SQL-RP) lub dzierżawy (na przykład dzierżawy aplikacji, które wymagają bazy danych), zezwala korzystanie z tej infrastruktury.

## <a name="prepare-the-file-server"></a>Przygotowanie serwera plików

Usługa Azure App Service wymaga użycia serwera plików. W przypadku wdrożeń produkcyjnych serwera plików musi być skonfigurowane wysoko dostępne i zdolne do obsługi błędów.

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>Szablon szybkiego startu dla serwera plików w przypadku wdrożeń usługi Azure App Service na ASDK.

W przypadku usługi Azure Stack Development Kit tylko w przypadku wdrożeń, można użyć [przykładowy szablon wdrożenia usługi Azure Resource Manager](https://aka.ms/appsvconmasdkfstemplate) do wdrożenia skonfigurowano jednowęzłowy serwer plików. Serwer plików z pojedynczym węzłem będzie należeć do grupy roboczej.

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>Szablon szybkiego startu dla serwera o wysokiej dostępności plików i programu SQL Server

A [szablon szybkiego startu architektury odwołanie](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha) jest teraz dostępna, który będzie wdrożyć serwer plików, programu SQL Server, usługi Active Directory do obsługi infrastruktury w sieci wirtualnej jest skonfigurowany do obsługi wysoko dostępne wdrożenie programu Usługa Azure App Service w usłudze Azure Stack.

### <a name="steps-to-deploy-a-custom-file-server"></a>Kroki, aby wdrożyć serwer plików niestandardowe

>[!IMPORTANT]
> Jeśli wybierzesz do wdrożenia usługi App Service w istniejącej sieci wirtualnej, serwer plików powinny być wdrażane w osobnej podsieci z usługi App Service.

#### <a name="provision-groups-and-accounts-in-active-directory"></a>Aprowizowanie grup i kont w usłudze Active Directory

1. Utwórz następujące grupy zabezpieczeń globalnych usługi Active Directory:

   - FileShareOwners
   - FileShareUsers

2. Utwórz następujące konta usługi Active Directory jako konta usług:

   - FileShareOwner
   - FileShareUser

   Zabezpieczeń najlepszym rozwiązaniem jest użytkowników dla tych kont (i dla wszystkich ról sieci web) powinna być unikatowa i ma silnej nazwy użytkowników i hasła. Ustawianie haseł, które z następujących warunków:

   - Włącz **hasło nigdy nie wygasa**.
   - Włącz **użytkownik nie może zmienić hasła**.
   - Wyłącz **użytkownik musi zmienić hasło przy następnym logowaniu**.

3. Dodaj konta do członkostwa w grupach w następujący sposób:

   - Dodaj **FileShareOwner** do **FileShareOwners** grupy.
   - Dodaj **FileShareUser** do **FileShareUsers** grupy.

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Aprowizowanie grup i kont w grupie roboczej

>[!NOTE]
> Gdy konfigurujesz serwer plików, uruchom następujące polecenia z **wiersza polecenia administratora**. <br>***Nie należy używać programu PowerShell.***

W przypadku użycia szablonu usługi Azure Resource Manager, użytkownicy, zostały już utworzone.

1. Uruchom następujące polecenia, aby utworzyć konta FileShareOwner i FileShareUser. Zastąp `<password>` własnymi wartościami.

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. Ustawianie hasła dla kont, które nigdy nie wygasa, uruchamiając następujące polecenia WMIC:

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

#### <a name="provision-the-content-share"></a>Aprowizowanie udział zawartości

Udział zawartości zawiera zawartość witryny sieci Web w dzierżawie. Procedura do udostępniania zawartości udostępnionej na pojedynczy serwer plików jest taka sama dla środowisk usługi Active Directory, jak i grupy roboczej. Ale różni się dla klastra trybu failover w usłudze Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Aprowizowanie udziału zawartości na jednym serwerze plików (usługi Active Directory lub grupie roboczej)

Na jednym serwerze plików, uruchom następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień. Zastąp wartość `C:\WebSites` ścieżkami odpowiedniego w danym środowisku.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>Skonfiguruj kontrolę dostępu do udziałów

Uruchom następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień na serwerze plików lub węzła klastra trybu failover, który jest bieżącym właścicielem zasobu klastra. Zastąp wartości kursywą wartościami, które są specyficzne dla danego środowiska.

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

## <a name="prepare-the-sql-server-instance"></a>Przygotowywanie wystąpienia programu SQL Server

Azure App Service dla hostingu usługi Azure Stack i pomiarów baz danych należy przygotować wystąpienia programu SQL Server do przechowywania bazy danych usługi App Service.

W przypadku wdrożeń usługi Azure Stack Development Kit, można użyć programu SQL Server Express 2014 z dodatkiem SP2 lub nowszym.

Do produkcji i wysokiej dostępności należy użyć pełnej wersji programu SQL Server 2014 z dodatkiem SP2 lub później, Włącz uwierzytelnianie trybu mieszanego i wdrażanie w [konfiguracji wysokiej dostępności](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Wystąpienie programu SQL Server dla usługi Azure App Service w usłudze Azure Stack muszą być dostępne ze wszystkich ról usługi App Service. Możesz wdrożyć program SQL Server w ramach subskrypcji dostawcy domyślne w usłudze Azure Stack. Czy też mają być wykorzystanie istniejącej infrastruktury w Twojej organizacji (o ile ma łączność do usługi Azure Stack). Jeśli używasz obrazu witryny Azure Marketplace, pamiętaj, aby odpowiednio skonfigurować zaporę.

> [!NOTE]
> Liczba obrazów maszyn wirtualnych SQL IaaS są dostępne za pomocą funkcji zarządzania portalu Marketplace. Upewnij się, że zawsze pobieranie najnowszej wersji rozszerzenie SQL IaaS, zanim wdrożysz maszynę Wirtualną przy użyciu elementu portalu Marketplace. Obrazy SQL są takie same jak maszyn wirtualnych SQL, które są dostępne na platformie Azure. Dla maszyn wirtualnych SQL z utworzonego na podstawie tych obrazów, rozszerzenie IaaS i odpowiadających im rozszerzenia portalu oferują funkcje, takie jak automatyczne stosowanie poprawek i możliwości tworzenia kopii zapasowej.
> 
> Dla każdej z ról programu SQL Server można użyć wystąpienia domyślnego lub nazwanego wystąpienia. Jeśli używasz nazwane wystąpienie, należy ręcznie uruchomić usługę SQL Server Browser i otwórz port 1434.

Instalator usługi App Service sprawdzi, upewnij się, że program SQL Server ma zawierania bazy danych, włączone. Aby włączyć zawierania bazy danych w programie SQL Server, który będzie hostem bazy danych usługi App Service, uruchom następujące polecenia SQL:

```sql
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO
```

>[!IMPORTANT]
> Jeśli wybierzesz do wdrożenia usługi App Service w istniejącej sieci wirtualnej programu SQL Server powinny być wdrażane w osobnej podsieci z usługi App Service i serwera plików.
>

## <a name="create-an-azure-active-directory-application"></a>Tworzenie aplikacji usługi Azure Active Directory

Konfigurowanie jednostki usługi Azure AD, aby obsługiwać następujące operacje:

- Zestaw skalowania maszyn wirtualnych integracji, na warstwy procesu roboczego.
- Logowanie Jednokrotne do usługi Azure Functions narzędzi deweloperskich portalu, jak i zaawansowanych.

Te kroki mają zastosowanie tylko w środowiskach platformy Azure zabezpieczonych przez usługi AD Azure Stack.

Administratorzy, należy skonfigurować logowanie Jednokrotne:

- Włącz narzędzia dla zaawansowanych programistów w ramach usługi App Service (Kudu).
- Korzystanie z środowisko pracy w portalu usługi Azure Functions.

Wykonaj następujące kroki:

1. Otwórz wystąpienie programu PowerShell jako azurestack\AzureStackAdmin.
2. Przejdź do lokalizacji, skryptów, które pobrane i wyodrębnione w [kroku wymagań wstępnych](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started).
3. [Instalowanie programu PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md).
4. Uruchom **AADIdentityApp.ps1 Utwórz** skryptu. Po wyświetleniu monitu wprowadź identyfikator dzierżawy usługi Azure AD, który używany w przypadku wdrożenia usługi Azure Stack. Na przykład, wprowadź **myazurestack.onmicrosoft.com**.
5. W **poświadczeń** okna, podaj konto administratora usługi Azure AD i hasło. Kliknij przycisk **OK**.
6. Wprowadź hasło certyfikatu i ścieżka do pliku certyfikatu [certyfikatu utworzony wcześniej](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started). Certyfikat utworzony dla tego kroku, domyślnie jest **sso.appservice.local.azurestack.external.pfx**.
7. Skrypt tworzy nową aplikację w dzierżawie wystąpienia usługi Azure AD. Zanotuj identyfikator aplikacji, który jest zwracany w danych wyjściowych programu PowerShell. Potrzebujesz tych informacji podczas instalacji.
8. Otwórz nowe okno przeglądarki i zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administratora usługi Azure Active Directory
9. Otwórz dostawcy zasobów usługi Azure AD.
10. Wybierz **rejestracje aplikacji**.
11. Wyszukaj identyfikator aplikacji zwracane w ramach kroku 7. Znajduje się aplikacja usługi App Service.
12. Wybierz **aplikacji** na liście.
13. Wybierz **ustawienia**.
14. Wybierz **wymagane uprawnienia** > **udzielić uprawnień** > **tak**.

```powershell
    Create-AADIdentityApp.ps1
```

| Parametr | Wymagane lub opcjonalne | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| DirectoryTenantName | Wymagane | Null | Identyfikatora dzierżawy usługi Azure AD Podaj identyfikator GUID lub ciąg. Przykładem jest myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Wymagane | Null | Punkt końcowy administratora usługi Azure Resource Manager. Przykładem jest adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Wymagane | Null | Punkt końcowy dzierżawy usługi Azure Resource Manager. Przykładem jest management.local.azurestack.external. |
| AzureStackAdminCredential | Wymagane | Null | Poświadczenia administratora usługi Azure AD. |
| CertificateFilePath | Wymagane | Null | **Pełna ścieżka** do wcześniej wygenerowany plik certyfikatu aplikacji tożsamości. |
| CertificatePassword | Wymagane | Null | Hasło, która pomaga chronić klucz prywatny certyfikatu. |
| Środowisko | Optional (Opcjonalność) | AzureCloud | Nazwa obsługiwane środowiska chmury, w której element docelowy usługi Azure Active Directory Graph jest dostępny.  Dozwolone wartości: 'AzureCloud', 'AzureChinaCloud', 'AzureUSGovernment', 'AzureGermanCloud'.|

## <a name="create-an-active-directory-federation-services-application"></a>Tworzenie aplikacji usług federacyjnych Active Directory

W środowiskach usługi Azure Stack, zabezpieczone przez usługi AD FS należy skonfigurować z jednostki usługi AD FS do obsługi następujących operacji:

- Zestaw skalowania maszyn wirtualnych integracji, na warstwy procesu roboczego.
- Logowanie Jednokrotne do usługi Azure Functions narzędzi deweloperskich portalu, jak i zaawansowanych.

Administratorzy, należy skonfigurować logowanie Jednokrotne:

- Skonfiguruj nazwę główną usługi do integracji zestawu skalowania maszyn wirtualnych na warstwy procesu roboczego.
- Włącz narzędzia dla zaawansowanych programistów w ramach usługi App Service (Kudu).
- Korzystanie z środowisko pracy w portalu usługi Azure Functions.

Wykonaj następujące kroki:

1. Otwórz wystąpienie programu PowerShell jako azurestack\AzureStackAdmin.
2. Przejdź do lokalizacji, skryptów, które pobrane i wyodrębnione w [kroku wymagań wstępnych](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started).
3. [Instalowanie programu PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md).
4. Uruchom **ADFSIdentityApp.ps1 Utwórz** skryptu.
5. W **poświadczeń** okna, podaj konto administratora chmury usług AD FS i hasło. Kliknij przycisk **OK**.
6. Ścieżka do pliku certyfikatu i hasła certyfikatu dla [certyfikatu utworzony wcześniej](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started). Certyfikat utworzony dla tego kroku, domyślnie jest **sso.appservice.local.azurestack.external.pfx**.

```powershell
    Create-ADFSIdentityApp.ps1
```

| Parametr | Wymagane lub opcjonalne | Wartość domyślna | Opis |
| --- | --- | --- | --- |
| AdminArmEndpoint | Wymagane | Null | Punkt końcowy administratora usługi Azure Resource Manager. Przykładem jest adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Wymagane | Null | Punkt końcowy uprzywilejowanych. Przykładem jest AzS ERCS01. |
| CloudAdminCredential | Wymagane | Null | Poświadczenia konta domeny dla administratorów chmury Azure Stack. Przykładem jest Azurestack\CloudAdmin. |
| CertificateFilePath | Wymagane | Null | **Pełna ścieżka** do pliku PFX certyfikatu aplikacji tożsamości. |
| CertificatePassword | Wymagane | Null | Hasło, która pomaga chronić klucz prywatny certyfikatu. |

## <a name="next-steps"></a>Kolejne kroki

[Zainstaluj dostawcę zasobów usługi App Service](azure-stack-app-service-deploy.md)
