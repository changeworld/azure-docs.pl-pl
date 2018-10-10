---
title: Integracja usługi Azure datacenter stosu — tożsamość
description: Dowiedz się, jak zintegrować usług AD FS usługi Azure Stack z centrum danych usług AD FS
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 10/02/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 4ba890f4763fc77981917d9311cf2bf6c97ec80f
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902447"
---
# <a name="azure-stack-datacenter-integration---identity"></a>Integracja usługi Azure datacenter stosu — tożsamość
Za pomocą usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS) w usłudze Azure Stack można wdrożyć jako dostawcy tożsamości. Należy wybrać przed wdrożeniem usługi Azure Stack. Wdrażanie przy użyciu usług AD FS jest również określany jako wdrażanie usługi Azure Stack w trybie rozłączonym.

W poniższej tabeli przedstawiono różnice między tożsamości dwie opcje:

||Połączenia z Internetem|Połączenie z Internetem|
|---------|---------|---------|
|Rozliczenia|Musi być pojemności<br> Umowy Enterprise Agreement (EA) tylko|Pojemność i płatność za użycie<br>EA lub dostawcy rozwiązań w chmurze (CSP)|
|Tożsamość|Musi być usług AD FS|Azure AD lub AD FS|
|Portal Marketplace |Obsługiwane<br>BYOL licencjonowania|Obsługiwane<br>BYOL licencjonowania|
|Rejestracja|Zaleca się, wymaga nośnik wymienny<br> i oddzielnych podłączonego urządzenia.|Automatyczne|
|Poprawek i aktualizacji|Wymagane, wymaga nośnik wymienny<br> i oddzielnych podłączonego urządzenia.|Pakiet aktualizacji, które mogą być pobierane bezpośrednio<br> z Internetu do usługi Azure Stack.|

> [!IMPORTANT]
> Nie można przełączyć dostawcę tożsamości, bez konieczności ponownego wdrażania całe rozwiązanie usługi Azure Stack.

## <a name="active-directory-federation-services-and-graph"></a>Usługi federacyjne Active Directory i programu Graph

Wdrażanie przy użyciu usług AD FS umożliwia tożsamości w istniejącym lesie usługi Active Directory do uwierzytelniania przy użyciu zasobów w usłudze Azure Stack. Tego istniejącego lasu usługi Active Directory wymaga wdrożenia usług AD FS, aby umożliwić tworzenie relacji zaufania federacji usług AD FS.

Uwierzytelnianie jest częścią tożsamości. Do zarządzania rolą na podstawie kontroli dostępu (RBAC) w usłudze Azure Stack, musi być skonfigurowany składnik programu Graph. Dostęp do zasobu jest delegowane, składnik programu Graph wyszukuje konta użytkownika w istniejącym lesie usługi Active Directory przy użyciu protokołu LDAP.

![Architektura usługi Azure Stack usług AD FS](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

Istniejące usługi AD FS jest konto usługi tokenu zabezpieczającego (STS), która wysyła oświadczenia do usługi Azure Stack usług AD FS (zasób usługi STS). W usłudze Azure Stack usługa automation tworzy relacji zaufania dostawcy oświadczeń z punktem końcowym metadanych dla istniejących usług AD FS.

W istniejących usługach AD FS można skonfigurować zaufanie jednostki uzależnionej. Ten krok nie jest wykonywane przez automatyzację i musi zostać skonfigurowane przez operatora. Punkt końcowy metadanych usługi Azure Stack jest udokumentowany w pliku AzureStackStampDeploymentInfo.JSON lub za pośrednictwem uprzywilejowanych punktu końcowego, uruchamiając polecenie `Get-AzureStackInfo`.

Konfiguracji jednostki uzależnionej zaufania strony wymaga skonfigurowania reguły przekształcania oświadczeń, które są dostarczane przez firmę Microsoft.

W przypadku konfiguracji wykres konto usługi musi być pod warunkiem, że ma uprawnienia do odczytu w istniejącej usługi Active Directory. To konto jest wymagane dane wejściowe dla usługi automation RBAC scenariuszy.

Ostatnim krokiem skonfigurowano nowego właściciela dla subskrypcji dostawcy domyślnego. To konto ma pełny dostęp do wszystkich zasobów, po zalogowaniu się do portalu administratora usługi Azure Stack.

Wymagania:


|Składnik|Wymaganie|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|System Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Konfigurowanie integracji programu Graph

Wykres obsługuje tylko integrację z pojedynczym lasem usługi Active Directory. Jeśli istnieje wiele lasów, lasu, określona w konfiguracji będzie służyć do pobierania użytkowników i grup.

Wymagane jako dane wejściowe dla parametrów automatyzacji są następujące informacje:


|Parametr|Opis|Przykład|
|---------|---------|---------|
|CustomADGlobalCatalog|Nazwa FQDN docelowego lasu usługi Active Directory<br>chcesz zintegrować z|Contoso.com|
|CustomADAdminCredentials|Użytkownik z uprawnieniami odczytu LDAP|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Tworzenie konta użytkownika w istniejącej usłudze Active Directory (opcjonalnie)

Opcjonalnie można utworzyć konto usługi programu Graph w istniejącej usłudze Active Directory. Wykonaj ten krok, jeśli nie masz jeszcze konta, które chcesz użyć.

1. W istniejącej usłudze Active Directory utwórz następujące konta użytkownika (zalecenie):
   - **Nazwa użytkownika**: graphservice
   - **Hasło**: Użyj silnego hasła.<br>Skonfiguruj hasło nigdy nie wygasa.

   Nie specjalnych uprawnień lub członkostwa jest wymagany.

#### <a name="trigger-automation-to-configure-graph"></a>Wyzwalania automatyzacji, aby skonfigurować wykresu

Do wykonania tej procedury należy użyć komputera w sieci centrum danych, który może komunikować się z punktem końcowym uprzywilejowanych w usłudze Azure Stack.

2. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator) i nawiązać połączenie z adresu IP uprzywilejowanych punktu końcowego. Użyj poświadczeń dla **CloudAdmin** do uwierzytelniania.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. Skoro masz połączenie do uprzywilejowanych punktu końcowego, uruchom następujące polecenie: 

   ```PowerShell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Po wyświetleniu monitu podaj poświadczenia dla konta użytkownika, które chcesz użyć na potrzeby usługi Graph (na przykład graphservice). Dane wejściowe dla polecenia cmdlet Register-DirectoryService musi być nazwą lasu / główny domeny w lesie, a nie innej domeny w lesie.

   > [!IMPORTANT]
   > Poczekaj, aż wyskakującego poświadczeń (Get-Credential nie jest obsługiwany w punkcie końcowym uprzywilejowanych) i wprowadź poświadczenia konta usługi programu Graph.

#### <a name="graph-protocols-and-ports"></a>Wykres protokoły i porty

Usługa programu Graph w usłudze Azure Stack używa następujących protokoły i porty do komunikacji z zapisywalnej serwera wykazu globalnego (GC) i Centrum dystrybucji kluczy (KDC), która może przetwarzać żądań logowania w lesie usługi Active Directory w miejscu docelowym.

Usługa programu Graph w usłudze Azure Stack używa następujących protokoły i porty do komunikacji z obiektem docelowym usługi Active Directory:

|Typ|Port|Protokół|
|---------|---------|---------|
|LDAP|389|TCP I UDP|
|PROTOKÓŁ LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|PROTOKÓŁ LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Konfigurowanie integracji usług AD FS, pobierając metadanych Federacji

Wymagane są następujące informacje jako dane wejściowe dla parametrów usługi automation:

|Parametr|Opis|Przykład|
|---------|---------|---------|
|CustomAdfsName|Nazwa dostawcy oświadczeń. <cr>Wygląda na to w ten sposób na stronie docelowej usług AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Łącza do metadanych Federacji|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Wyzwalania automatyzacji, aby skonfigurować zaufanie dostawcy oświadczeń w usłudze Azure Stack

Do wykonania tej procedury należy użyć komputera, który może komunikować się z punktem końcowym uprzywilejowanych w usłudze Azure Stack. Oczekuje się, że certyfikat używany przez konto **usług AD FS usługi STS** jest zaufany przez usługę Azure Stack.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień i nawiązać połączenie z uprzywilejowanym punktu końcowego.

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Skoro masz połączenie do uprzywilejowanych punktu końcowego, uruchom następujące polecenie, używając parametrów, które są odpowiednie dla danego środowiska:

   ```PowerShell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Uruchom następujące polecenie, aby zaktualizować właściciela subskrypcji dostawcy domyślnego, przy użyciu parametrów, które są odpowiednie dla danego środowiska:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Konfigurowanie integracji usług AD FS, zapewniając pliku metadanych Federacji

Począwszy od wersji 1807, należy użyć tej metody, jeśli jest spełniony jeden z następujących warunków:

- Łańcuch certyfikatu różni się dla usług AD FS w porównaniu do wszystkich innych punktów końcowych w usłudze Azure Stack.
- Brak braku łączności z siecią do istniejącego serwera usług AD FS z wystąpienia usług AD FS usługi Azure Stack.

Wymagane są następujące informacje jako dane wejściowe dla parametrów usługi automation:


|Parametr|Opis|Przykład|
|---------|---------|---------|
|CustomAdfsName|Nazwa dostawcy oświadczeń. Wygląda na to w ten sposób na stronie docelowej usług AD FS.|Contoso|
|CustomADFSFederationMetadataFileContent|Metadane zawartości|$using: federationMetadataFileContent|



### <a name="create-federation-metadata-file"></a>Tworzenie pliku metadanych Federacji

Aby wykonać poniższą procedurę należy użyć komputera, który ma łączność sieciową do istniejącego wdrożenia usług AD FS, która staje się kontem usługi STS. Ponadto wymagane certyfikaty musi być zainstalowany.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenie, przy użyciu parametrów, które są odpowiednie dla danego środowiska:

   ```PowerShell  
    $metadata = (Invoke-WebRequest -URI " https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml " -UseBasicParsing).Content
    Set-Content -Path c:\metadata.xml -Encoding Unicode -Value $metadata 

   ```

2. Skopiuj plik metadanych do komputera, który może komunikować się z punktem końcowym uprzywilejowanych.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Wyzwalania automatyzacji, aby skonfigurować zaufanie dostawcy oświadczeń w usłudze Azure Stack

Do wykonania tej procedury należy użyć komputera, który może komunikować się z punktem końcowym uprzywilejowanych w usłudze Azure Stack i ma dostęp do pliku metadanych, który został utworzony w poprzednim kroku.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień.

   ```PowerShell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
   ```

2. Uruchom następujące polecenie, aby zaktualizować właściciela subskrypcji dostawcy domyślnego, przy użyciu parametrów, które są odpowiednie dla danego środowiska:

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Konfigurowanie jednostki uzależnionej na istniejącym wdrożeniu usług AD FS (konto usługi STS)

Firma Microsoft udostępnia skryptu konfigurującego zaufania jednostki uzależnionej, w tym reguły przekształcania oświadczeń. Za pomocą skryptu jest opcjonalne, jak ręcznie uruchomić polecenia.

Możesz pobrać skrypt pomocnika z [narzędzia usługi Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) w witrynie Github.

Jeśli postanowisz ręcznie uruchomić polecenia, wykonaj następujące czynności:

1. Skopiuj następującą zawartość do pliku txt, (na przykład zapisane jako c:\ClaimRules.txt) członka wystąpienia lub farmy usług AD FS Twojego Centrum danych:

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
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. Sprawdzanie poprawności dla uwierzytelniania opartego na formularzach Windows ekstranetu i sieci intranet jest włączone. Najpierw sprawdź, czy jego już włączony, uruchamiając następujące polecenie cmdlet:

   ```PowerShell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > Windows zintegrowanego uwierzytelniania (WIA) agent użytkownika obsługiwanych, ciągi mogą przestarzałe rozmieszczania użytkownik usługi AD FS może wymagać zostać zaktualizowany do obsługi najnowszej klientów. Możesz przeczytać więcej na temat aktualizowania WIA obsługiwanych ciągów agenta użytkownika w artykule [uwierzytelniania opartego na formularzach pakietu Konfigurowanie intranetu dla urządzeń, które nie obsługują WIA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia).<br>Kroki, aby włączyć zasady uwierzytelniania opartego na formularzach są udokumentowane w artykule [skonfigurować zasady uwierzytelniania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies).

3. Aby dodać relację zaufania jednostki uzależnionej, uruchom następujące polecenie programu Windows PowerShell na wystąpienia usług AD FS lub elementu członkowskiego z farmy. Upewnij się zaktualizować punkt końcowy usług AD FS, a następnie wskaż plik utworzony w kroku 1.

   **Dla usług AD FS 2016**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **Dla usługi AD FS 2012/2012 R2**

   ```PowerShell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]
   > Za pomocą przystawki programu MMC programu AD FS należy skonfigurować reguły autoryzacji wystawiania, podczas korzystania z systemu Windows Server 2012 lub 2012 R2 AD FS.

4. Użycie programu Internet Explorer lub przeglądarki Microsoft Edge można uzyskać dostępu do stosu Azure, należy zignorować tokenu powiązania. W przeciwnym razie logowanie zakończy się niepowodzeniem. Na wystąpienie usług AD FS lub członek farmy uruchom następujące polecenie:

   > [!note]  
   > Ten krok nie ma zastosowania w przypadku korzystania z systemu Windows Server 2012 lub 2012 R2 AD FS. Jest bezpieczne pominąć to polecenie i kontynuować dzięki integracji z usługą.

   ```PowerShell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>Tworzenie nazwy SPN

Istnieje wiele scenariuszy, które korzystają z główną nazwę usługi (SPN) dla uwierzytelniania. Poniżej przedstawiono kilka przykładów:

- Użycia interfejsu wiersza polecenia za pomocą usług AD FS wdrożenie usługi Azure Stack
- Pakiet administracyjny programu System Center dla usługi Azure Stack po wdrożeniu za pomocą usług AD FS
- Dostawcy zasobów w usłudze Azure Stack po wdrożeniu za pomocą usług AD FS
- Różne aplikacje
- Wymaga logowania nieinterakcyjnego

> [!Important]  
> Usługi AD FS obsługuje tylko logowanie interakcyjne sesje. Jeśli potrzebujesz nieinterakcyjne logowanie automatyczne scenariusz, należy użyć nazwy SPN.

Aby uzyskać więcej informacji na temat tworzenia nazwy głównej usługi, zobacz [Tworzenie jednostki usługi dla usług AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs).


## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="configuration-rollback"></a>Wycofywanie konfiguracji

Jeśli wystąpi błąd powodujący, że środowisko w stanie, gdy użytkownik nie mogą już uwierzytelniać dostępnej opcji wycofywania.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia:

   ```PowerShell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Następnie uruchom następujące polecenie cmdlet:

   ```PowerShell  
   Reset-DatacenterIntegationConfiguration
   ```

   Po uruchomieniu akcji wycofywania, wszystkie zmiany konfiguracji zostaną wycofane. Tylko uwierzytelnianie za pomocą wbudowanych **CloudAdmin** użytkownika jest możliwe.

   > [!IMPORTANT]
   > Należy skonfigurować pierwotny właściciel domyślną subskrypcję dostawcy

   ```PowerShell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Zbieranie dodatkowych dzienników

W przypadku awarii dowolnych poleceń cmdlet, można zbierać dodatkowe dzienniki przy użyciu `Get-Azurestacklogs` polecenia cmdlet.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia:

   ```PowerShell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Następnie uruchom następujące polecenie cmdlet:

   ```PowerShell  
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>Kolejne kroki

[Integrowanie rozwiązań w zakresie monitorowania zewnętrznych](azure-stack-integrate-monitor.md)
