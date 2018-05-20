---
title: Integracja Azure datacenter stosu - tożsamości
description: Dowiedz się, jak zintegrować stosu Azure AD FS z centrum danych usług AD FS
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: cc15c92037e18800a6f919d0ca18acb20ed5e893
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="azure-stack-datacenter-integration---identity"></a>Integracja Azure datacenter stosu - tożsamości
Stos Azure za pomocą usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS) można wdrożyć jako dostawcy tożsamości. Wybór należy przed wdrożeniem stosu Azure. Wdrożenia przy użyciu usług AD FS jest również nazywany wdrażanie stosu Azure w trybie rozłączonym.

W poniższej tabeli przedstawiono różnice między tożsamości dwóch opcji:

||Połączenia z Internetem|Połączenie z Internetem|
|---------|---------|---------|
|Rozliczenia|Musi być pojemności<br> Tylko Enterprise Agreement (EA)|Pojemności lub płatności jako — użytkownik użycia<br>Umowa EA lub dostawca rozwiązań w chmurze (CSP)|
|Tożsamość|Musi być usług AD FS|Azure AD lub AD FS|
|Syndykacja Marketplace|Obsługiwane<br>BYOL licencjonowania|Obsługiwane<br>BYOL licencjonowania|
|Rejestracja|Zalecane, wymaga nośnika wymiennego<br> i oddzielne podłączonego urządzenia.|Automatyczne|
|Poprawek i aktualizacji|Wymagane, wymaga nośnika wymiennego<br> i oddzielne podłączonego urządzenia.|Pakiet aktualizacji, które mogą być pobierane bezpośrednio<br> z Internetu do stosu platformy Azure.|

> [!IMPORTANT]
> Nie można przełączyć dostawcy tożsamości bez ponownego wdrożenia całego rozwiązania Azure stosu.

## <a name="active-directory-federation-services-and-graph"></a>Wykres i usług federacyjnych Active Directory

Wdrażanie z usługami AD FS umożliwia tożsamości w istniejącym lesie usługi Active Directory do uwierzytelniania z zasobami w stosie Azure. Ta istniejącego lasu usługi Active Directory wymaga wdrożenia usług AD FS, aby umożliwić tworzenie relacji zaufania federacji usług AD FS.

Uwierzytelnianie jest częścią tożsamości. Aby zarządzać na podstawie dostępu kontroli ról (RBAC) w stosie Azure, musi być skonfigurowany składnik Wykres. Po oddelegowaniu dostęp do zasobu składnika Wykres wyszukuje konta użytkownika w istniejącym lesie usługi Active Directory przy użyciu protokołu LDAP.

![Architektura usługi Azure stosu usług AD FS](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

Istniejących usług AD FS jest konto usługi tokenu zabezpieczającego (STS) wysyłanej oświadczenia do stosu usługi Azure AD FS (STS zasobu). W stosie Azure automatyzacji tworzy zaufania dostawcy oświadczeń z punktem końcowym metadanych dla istniejących usług AD FS.

W istniejących usług AD FS można skonfigurować zaufanie jednostki uzależnionej. Ten krok nie jest określany przez automatyzację i musi być skonfigurowany przez operatora. Punktu końcowego metadanych stosu Azure opisano w pliku AzureStackStampDeploymentInfo.JSON lub za pośrednictwem uprzywilejowanych punktu końcowego za pomocą polecenia `Get-AzureStackInfo`.

Konfigurację zaufania jednostki uzależnionej strony wymaga skonfigurowania reguły przekształcania oświadczeń, które są obsługiwane przez firmę Microsoft.

W przypadku konfiguracji wykresu konto usługi musi być pod warunkiem, że ma uprawnienie w istniejącej usługi Active Directory do odczytu. To konto jest wymagane jako dane wejściowe dla automatyzacji na potrzeby scenariuszy z RBAC.

Ostatni krok skonfigurowano nowego właściciela subskrypcji dostawcy domyślnego. To konto ma pełny dostęp do wszystkich zasobów, gdy jest zalogowany w usłudze Azure stosu portalu administratora.

Wymagania:


|Składnik|Wymaganie|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Konfigurowanie integracji wykresu

Wykres tylko obsługuje integrację z jednego lasu usługi Active Directory. Jeśli istnieje wiele lasów, lasu, określona w konfiguracji będzie służyć do pobierania użytkowników i grup.

Jako dane wejściowe dla parametrów automatyzacji wymagane są następujące informacje:


|Parametr|Opis|Przykład|
|---------|---------|---------|
|CustomADGlobalCatalog|Nazwa FQDN docelowego lasu usługi Active Directory<br>Czy chcesz zintegrować z|Contoso.com|
|CustomADAdminCredentials|Użytkownik z uprawnieniami odczytu LDAP|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Utwórz konta użytkowników w istniejącej usługi Active Directory (opcjonalnie)

Opcjonalnie można utworzyć konto usługi wykresu w istniejącej usługi Active Directory. Wykonaj ten krok, jeśli nie masz już konto, którego chcesz używać.

1. W istniejącej usługi Active Directory należy utworzyć następujące konto użytkownika (zalecane):
   - **Nazwa użytkownika**: graphservice
   - **Hasło**: należy używać silnych haseł<br>Skonfiguruj hasło nigdy nie wygasa.

   Nie specjalnych uprawnień lub członkostwa jest wymagana.

#### <a name="trigger-automation-to-configure-graph"></a>Wyzwalacz automatyzacji do konfigurowania wykresu

Do wykonania tej procedury należy użyć komputera w sieci centrum danych, który może komunikować się z punktem końcowym uprzywilejowanych w stosie Azure.

2. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator) i łączyć się z adresem IP uprzywilejowanych punktu końcowego. Użyj poświadczeń dla **CloudAdmin** do uwierzytelniania.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. Teraz, gdy masz połączenie z punktem końcowym uprzywilejowane, uruchom następujące polecenie: 

   ```powershell
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Po wyświetleniu monitu podaj poświadczenia dla konta użytkownika, którego chcesz używać usługi wykresu (na przykład graphservice). Dane wejściowe dla polecenia cmdlet Register-DirectoryService musi być nazwą lasu / główny domeny w lesie, a nie inne domeny w lesie.

   > [!IMPORTANT]
   > Poczekaj, aż wyskakujących poświadczeń (Get-Credential nie jest obsługiwana w punkcie końcowym uprzywilejowanych), a następnie wprowadź poświadczenia konta usługi wykresu.

#### <a name="graph-protocols-and-ports"></a>Wykres protokoły i porty

Graph usługi Azure stosu używa następujące protokoły i porty do komunikacji z zapisywalnej serwera wykazu globalnego (GC) i Centrum dystrybucji kluczy (KDC), który może przetwarzać żądań logowania w docelowych lasu usługi Active Directory.

Graph usługi Azure stosu używane następujące protokoły i porty do komunikowania się z elementem docelowym usługi Active Directory:

|Typ|Port|Protokół|
|---------|---------|---------|
|LDAP|389|TCP I UDP|
|PROTOKÓŁ LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|PROTOKÓŁ LDAP SSL WYKAZU GLOBALNEGO|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Konfigurowanie integracji usług AD FS, pobierając metadanych Federacji

Wymagane są następujące informacje jako dane wejściowe dla parametrów automatyzacji:

|Parametr|Opis|Przykład|
|---------|---------|---------|
|CustomAdfsName|Nazwa dostawcy oświadczeń. <cr>Wygląda w ten sposób na strony docelowej usług AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Łącze metadanych Federacji|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Wyzwalacz automatyzacji do skonfigurowania zaufania dostawcy oświadczeń w stosie Azure

Do wykonania tej procedury należy użyć komputera, który może komunikować się z punktem końcowym uprzywilejowanych w stosie Azure. Oczekuje się, że certyfikat używany przez konto **Zabezpieczającego usług AD FS** jest uważany za zaufany przez stos Azure.

1. Otwórz sesję programu Windows PowerShell z podwyższonym poziomem uprawnień, a następnie połączyć z punktem końcowym uprzywilejowanych.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Teraz, gdy masz połączenie z punktem końcowym uprzywilejowane, uruchom następujące polecenie z parametrami, które są odpowiednie dla danego środowiska:

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Uruchom następujące polecenie, aby zaktualizować właściciela subskrypcji dostawcy domyślnego, przy użyciu parametrów w danym środowisku:

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Konfigurowanie integracji usług AD FS, zapewniając pliku metadanych Federacji

Użyj tej metody, jeśli jest spełniony jeden z następujących warunków:

- Łańcuch certyfikatów jest inny dla usług AD FS w porównaniu do wszystkich innych punktów końcowych w stosie Azure.
- Nie ma żadnej łączności sieciowej do istniejącego serwera usług AD FS z wystąpienia usług AD FS Azure stosu.

Wymagane są następujące informacje jako dane wejściowe dla parametrów automatyzacji:


|Parametr|Opis|Przykład|
|---------|---------|---------|
|CustomAdfsName|Nazwa dostawcy oświadczeń. Wygląda na to w ten sposób na strony docelowej usług AD FS.|Contoso|
|CustomADFSFederationMetadataFile|Plik metadanych Federacji|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|

### <a name="create-federation-metadata-file"></a>Utwórz plik metadanych Federacji

Aby wykonać poniższą procedurę należy użyć komputera, który ma łączność sieciową do istniejącego wdrożenia usług AD FS, która staje się kontem usługi STS. Ponadto można zainstalować wymagane certyfikaty.

1. Otwórz sesję programu Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie, korzystając z parametrów w danym środowisku:

   ```powershell
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. Skopiuj plik metadanych do udziału, która jest dostępna z uprzywilejowanym punktu końcowego.


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Wyzwalacz automatyzacji do skonfigurowania zaufania dostawcy oświadczeń w stosie Azure

Do wykonania tej procedury należy użyć komputera, który może komunikować się z punktem końcowym uprzywilejowanych w stosie Azure.

1. Otwórz sesję programu Windows PowerShell z podwyższonym poziomem uprawnień, a następnie połączyć z punktem końcowym uprzywilejowanych.

   ```powershell
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Teraz, gdy masz połączenie z punktem końcowym uprzywilejowane, uruchom następujące polecenie z parametrami, które są odpowiednie dla danego środowiska:

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml
   ```

3. Uruchom następujące polecenie, aby zaktualizować właściciela subskrypcji dostawcy domyślnego, przy użyciu parametrów w danym środowisku:

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Skonfiguruj jednostki uzależnionej na istniejącym wdrożeniu usług AD FS (konto usługi STS)

Firma Microsoft udostępnia skrypt, który konfiguruje relację zaufania jednostki uzależnionej, w tym reguły przekształcania oświadczeń. Przy użyciu skryptu jest opcjonalne, jak można ręcznie uruchom polecenia.

Można pobrać skryptu pomocnika z [narzędzi stosu Azure](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) w witrynie Github.

Jeśli zdecydujesz się ręcznie uruchom polecenia, wykonaj następujące kroki:

1. Skopiuj następującą zawartość do pliku txt (na przykład zapisane jako c:\ClaimRules.txt) w sieci centrum danych Członkowskim wystąpienia lub farmy usług AD FS:

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname]
   => issue(claim = c);
   ```

2. Aby włączyć uwierzytelnianie oparte na formularzach systemu Windows, otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień użytkownika, a następnie uruchom następujące polecenie:

   ```powershell
   Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")
   ```

3. Aby dodać zaufanie jednostki uzależnionej, uruchom następujące polecenie programu Windows PowerShell na wystąpienie usług AD FS lub element członkowski farmy. Upewnij się, że można zaktualizować punktu końcowego usług AD FS, a następnie wskaż plik utworzony w kroku 1.

   **Dla usług AD FS 2016**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"
   ```

   **Dla programu AD FS 2012/2012 R2**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true
   ```

   > [!IMPORTANT]
   > Przystawka programu MMC programu AD FS należy użyć do konfigurowania reguł autoryzacji wystawiania, korzystając z systemu Windows Server 2012 lub 2012 R2 AD FS.

4. Użycie programu Internet Explorer lub przeglądarki Microsoft Edge można uzyskać dostępu do stosu Azure, należy zignorować tokenu powiązania. W przeciwnym razie próby logowania się niepowodzeniem. Wystąpienie usług AD FS lub element członkowski farmy uruchom następujące polecenie:

   > [!note]  
   > Ten krok nie ma zastosowania w przypadku korzystania z systemu Windows Server 2012 lub 2012 R2 AD FS. Jest bezpieczne pominąć to polecenie, a następnie kontynuuj integracji.

   ```powershell
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

5. Aby włączyć tokeny odświeżania, otwórz sesję programu Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie:

   ```powershell
   Set-ADFSRelyingPartyTrust -TargetName AzureStack -TokenLifeTime 1440
   ```

## <a name="spn-creation"></a>Tworzenie nazwy SPN

Istnieje wiele scenariuszy, które korzystają z główną nazwę usługi (SPN) dla uwierzytelniania. Poniżej przedstawiono kilka przykładów:

- Sposób użycia interfejsu wiersza polecenia z wdrożeniem usług AD FS Azure stosu
- Pakiet administracyjny programu System Center dla stosu Azure podczas wdrażania z usługami AD FS
- Dostawcy zasobów Azure stosu podczas wdrażania z usługami AD FS
- Różne aplikacje
- Wymagaj logowania nieinterakcyjnego

> [!Important]  
> Usługi AD FS obsługuje jedynie sesje logowania interakcyjnego. Jeśli potrzebujesz nieinterakcyjnym logowania automatycznego scenariusz, należy użyć nazwy SPN.

Aby uzyskać więcej informacji na temat tworzenia głównej nazwy usługi, zobacz [Tworzenie nazwy głównej usługi dla usług AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs).


## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="configuration-rollback"></a>Wycofywanie konfiguracji

Jeśli wystąpi błąd powodujący, że środowisko w stanie, gdy nie będzie można uwierzytelniać, opcja wycofywania jest dostępna.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia:

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Następnie uruchom następujące polecenie cmdlet:

   ```powershell
   Reset-DatacenterIntegationConfiguration
   ```

   Po uruchomieniu akcji wycofywania, wszystkie zmiany konfiguracji zostały wycofane. Tylko uwierzytelnianie za pomocą wbudowanych **CloudAdmin** użytkownika jest możliwe.

   > [!IMPORTANT]
   > Należy skonfigurować pierwotny właściciel subskrypcji dostawcy domyślnego

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Zbieranie dodatkowych dzienników

Jeśli dowolnych poleceniach cmdlet nie powiedzie się, można zbierać dodatkowe dzienniki przy użyciu `Get-Azurestacklogs` polecenia cmdlet.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia:

   ```powershell
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Następnie uruchom następujące polecenie cmdlet:

   ```powershell
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>Kolejne kroki

[Zintegrowanie rozwiązań monitorowania zewnętrznych](azure-stack-integrate-monitor.md)
