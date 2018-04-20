---
title: Jak skonfigurować hybrydowe usługi Azure Active Directory przyłączone do urządzeń | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania hybrydowych urządzeń przyłączonych do usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 34d1ba2e1e84c268442d47d8865d3e3bebb53e53
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="how-to-configure-hybrid-azure-active-directory-joined-devices"></a>Konfigurowanie hybrydowego urządzeń przyłączonych do usługi Azure Active Directory

Z zarządzania urządzeniami w usłudze Azure Active Directory (Azure AD) można zapewnić, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają standardy zabezpieczeń i zgodności. Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](device-management-introduction.md).

Jeśli masz w lokalnym środowisku Active Directory i chcesz przyłączyć urządzeń przyłączonych do domeny do usługi Azure AD, można to zrobić, konfigurując hybrydowe usługi Azure AD przyłączone do urządzenia. Temat zawiera powiązane kroki. 


## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfigurowania urządzeń usługi Azure AD przyłączone do hybrydowych w danym środowisku, należy zapoznać się z obsługiwanych scenariuszach i ograniczeniach.  

Jeśli używasz [narzędzie przygotowywania systemu (Sysprep)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc721940(v=ws.10)), upewnij się, tworzenie obrazów z instalacji systemu Windows, która nie została jeszcze zarejestrowana z usługą Azure AD.

Wszystkie urządzenia przyłączone do domeny uruchomiony system Windows 10 Anniversary Update i Windows Server 2016 automatycznego rejestrowania z usługą Azure AD na ponowne uruchomienie urządzenia lub użytkownika logowania po zakończeniu kroków konfiguracji wymienione poniżej. **Jeśli to zachowanie automatyczne rejestru nie jest preferowana, lub jeśli wymagane jest kontrolowany przez wdrożenie**, wykonaj instrukcje podane w sekcji "Krok 4: kontroli i wdrażania" poniżej, aby selektywnie włączać lub wyłączać automatyczną wdrożenia przed następujące kroki konfiguracji.  

Aby zwiększyć czytelność opisy, w tym temacie używa następujących termin: 

- **Urządzenia z systemem Windows bieżącego** -określenie to odnosi się do urządzeń przyłączonych do domeny z systemem Windows 10 lub Windows Server 2016.
- **Urządzenia z systemem Windows niższego poziomu** -określenie to odnosi się do wszystkich **obsługiwane** przyłączonych do domeny urządzenia z systemem Windows, które nie są uruchomione systemu Windows 10 ani Windows Server 2016.  

### <a name="windows-current-devices"></a>Bieżący urządzeń z systemem Windows

- W przypadku urządzeń z pulpitu systemu operacyjnego Windows, obsługiwana wersja to Windows 10 Anniversary Update (w wersji 1607) lub nowszym. 
- Rejestracja urządzeń z systemem Windows bieżącego **jest** obsługiwane w niefederacyjnych środowiskach, takich jak konfiguracje synchronizacji skrótu hasła.  


### <a name="windows-down-level-devices"></a>Urządzenia z systemem Windows niższego poziomu

- Obsługiwane są następujące urządzeń z systemem Windows niższego poziomu:
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- Rejestracja urządzeń z systemem Windows niższego poziomu **jest** obsługiwane w środowiskach niefederacyjnych za pośrednictwem bezproblemowe rejestracji jednokrotnej [Azure Active Directory bezproblemowe logowanie jednokrotne](https://aka.ms/hybrid/sso).
- Rejestracja urządzeń z systemem Windows niższego poziomu **nie jest** obsługiwane na urządzeniach przy użyciu profilów mobilnych. Jeśli używasz mobilnego profile i ustawienia, należy użyć systemu Windows 10.



## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem włączenie hybrydowe usługi Azure AD przyłączone do urządzeń w Twojej organizacji, należy upewnić się, że:

- Używasz zaktualizowanej wersji programu Azure AD connect.

- Azure AD connect ma synchronizowane obiekty komputerów, urządzeń, aby być hybrydowe usługi Azure AD do usługi Azure AD. Jeśli obiekty komputerów należą do określonej jednostki organizacyjnej (OU), a następnie tych jednostek organizacyjnych, należy skonfigurować do synchronizacji w usłudze Azure AD connect również.

  

Azure AD Connect:

- Umożliwia zachowanie skojarzenia między konta komputera w lokalnej usłudze Active Directory (AD) i obiekt urządzenia w usłudze Azure AD. 
- Umożliwia urządzeniu innych powiązanych funkcji, takich jak Windows Hello dla firm.

Upewnij się, że następujące adresy URL są dostępne z komputerów w sieci organizacji rejestrację komputerów z usługą Azure AD:

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com

- https://device.login.microsoftonline.com

Jeśli w Twojej organizacji wymaga dostępu do Internetu za pośrednictwem serwera proxy ruchu wychodzącego, musi implementować autowykrywania serwera Proxy sieci Web (WPAD) umożliwia komputerom z systemem Windows 10 można zarejestrować się w usłudze Azure AD.


## <a name="configuration-steps"></a>Kroki konfiguracji

Możesz skonfigurować urządzenia usługi Azure AD przyłączone hybrydowego dla różnych typów platform urządzeń z systemem Windows. Ten temat zawiera kroki wymagane w przypadku wszystkich scenariuszy typowej konfiguracji.  

Skorzystaj z poniższej tabeli, aby uzyskać przegląd kroków, które są wymagane dla danego scenariusza:  



| Kroki                                      | Synchronizacja skrótów bieżące i hasło systemu Windows | Bieżącego systemu Windows i Federacji | Niższego poziomu z systemem Windows |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Krok 1: Konfigurowanie punktu połączenia usługi | ![Zaznacz][1]                            | ![Zaznacz][1]                    | ![Zaznacz][1]        |
| Krok 2: Skonfiguruj wystawiania oświadczeń           |                                        | ![Zaznacz][1]                    | ![Zaznacz][1]        |
| Krok 3: Włącz urządzenia z systemem innym niż Windows 10      |                                        |                                | ![Zaznacz][1]        |
| Krok 4: Wdrażanie formantu i wdrożenia     | ![Zaznacz][1]                            | ![Zaznacz][1]                    | ![Zaznacz][1]        |
| Krok 5: Sprawdzanie połączonych urządzeń          | ![Zaznacz][1]                            | ![Zaznacz][1]                    | ![Zaznacz][1]        |



## <a name="step-1-configure-service-connection-point"></a>Krok 1: Konfigurowanie punktu połączenia usługi

Obiekt (SCP) punkt połączenia usługi jest używany przez urządzenia podczas rejestrowania informacji dzierżawy usługi Azure AD. W lokalnej usługi Active Directory (AD) obiektu SCP dla hybrydowych urządzeń usługi Azure AD przyłączony musi istnieć w kontekście partycji komputera lesie nazw konfiguracji. Istnieje tylko jeden kontekście nazewnictwa konfiguracji w każdym lesie. W konfiguracji obejmującego wiele lasów usługi Active Directory punkt połączenia z usługą musi istnieć we wszystkich lasach środowiska zawierającego komputery przyłączone do domeny.

Można użyć [ **Get-ADRootDSE** ](https://technet.microsoft.com/library/ee617246.aspx) polecenia cmdlet, aby pobrać kontekście nazewnictwa konfiguracji w lesie.  

Dla lasu o nazwie domeny usługi Active Directory *fabrikam.com*, jest w kontekście nazewnictwa konfiguracji:

`CN=Configuration,DC=fabrikam,DC=com`

W lesie obiektu SCP automatycznej rejestracji urządzeń przyłączonych do domeny znajduje się na:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

W zależności od tego, jak zostały wdrożone usługi Azure AD Connect obiektu SCP może zostały już skonfigurowane.
Można sprawdzić istnienia obiektu i pobierania wartości odnajdywania przy użyciu następujący skrypt programu Windows PowerShell: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

**$Scp. Słowa kluczowe** dane wyjściowe zawierają informacje o dzierżawy usługi Azure AD, na przykład:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Jeśli punkt połączenia usługi nie istnieje, należy go utworzyć, uruchamiając `Initialize-ADSyncDomainJoinedComputerSync` polecenia cmdlet na serwerze programu Azure AD Connect. Poświadczenia administratora przedsiębiorstwa jest wymagana do uruchamiania tego polecenia cmdlet.  
Polecenia cmdlet:

- Tworzy punkt połączenia z usługą w lesie usługi Active Directory, którą połączony jest Azure AD Connect. 
- Wymaga podania `AdConnectorAccount` parametru. Jest to konto, która jest skonfigurowana jako usługi Active Directory connector konta w usłudze Azure AD connect. 


Poniższy skrypt pokazuje przykład za pomocą polecenia cmdlet. W tym skrypcie `$aadAdminCred = Get-Credential` wymaga nazwy użytkownika. Należy podać nazwę użytkownika w formacie (UPN) Nazwa główna użytkownika (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

`Initialize-ADSyncDomainJoinedComputerSync` Polecenia cmdlet:

- Korzysta z modułu programu PowerShell usługi Active Directory i narzędzia usług AD DS, które zależą od usługi Active Directory w sieci Web uruchomiony na kontrolerze domeny. Usługi sieci Web w usłudze Active Directory jest obsługiwana na kontrolerach domeny z systemem Windows Server 2008 R2 lub nowszej.
- Jest obsługiwana tylko przez **MSOnline PowerShell w wersji modułu 1.1.166.0**. Aby pobrać ten moduł, użyj [łącze](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Jeśli nie zainstalowano narzędzi usług AD DS, `Initialize-ADSyncDomainJoinedComputerSync` zakończy się niepowodzeniem.  Narzędzia usług AD DS można zainstalować za pomocą Menedżera serwera w obszarze funkcje — narzędzia administracji zdalnej serwera — narzędzia do administrowania rolami.

Dla kontrolerów domeny z systemem Windows Server 2008 lub starszej wersji Użyj poniższego skryptu, aby utworzyć punkt połączenia usługi.

W konfiguracji wielu lasów należy użyć następującego skryptu można utworzyć punktu połączenia z usługą w każdym lesie, w którym istnieje komputerów:
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your AD configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC

    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()


## <a name="step-2-setup-issuance-of-claims"></a>Krok 2: Skonfiguruj wystawiania oświadczeń

W federacyjnych Azure konfiguracji AD urządzeń zależą od usługi Active Directory Federation Services (AD FS) lub 3 strona lokalnej usługi federacyjnej do uwierzytelniania usługi Azure AD. Uwierzytelnianie urządzeń do uzyskania tokenu dostępu, aby zarejestrować przed Azure Active Directory usługi rejestracji urządzeń (Azure DRS).

Systemu Windows, które urządzenia bieżące uwierzytelniania przy użyciu zintegrowanego uwierzytelniania systemu Windows do aktywnego punktu końcowego usługi WS-Trust (w wersji 1.3 lub 2005) obsługiwanych przez usługę federacyjną lokalnymi.

> [!NOTE]
> Korzystając z usług AD FS, albo **adfs/services/zaufania/13/windowstransport** lub **adfs/services/zaufania/2005/windowstransport** musi być włączona. Jeśli korzystasz z uwierzytelniania serwera Proxy sieci Web, również upewnić się, czy ten punkt końcowy został opublikowany przez serwer proxy. Widać, jakie punkty końcowe są włączane za pośrednictwem konsoli zarządzania usług AD FS, w obszarze **usługi > punkty końcowe**.
>
>Jeśli nie masz jako lokalnej usługi federacyjnej usług AD FS, postępuj zgodnie z instrukcjami dostawcy, aby upewnić się na obsługę protokołu WS-Trust 1.3 lub punkty końcowe 2005, a te są publikowane za pomocą pliku wymiany metadanych (MEX).

Następujące oświadczeń musi istnieć w tokenie odebranych przez Azure usługi rejestracji urządzeń w czasie rejestracji urządzenia do ukończenia. Usługi rejestracji urządzeń usługi Azure utworzy obiekt urządzenia w usłudze Azure AD, niektóre z tych informacji, która jest następnie używany przez program Azure AD Connect do skojarzenia obiektu urządzenia nowo utworzony z komputera konta lokalnego.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Jeśli masz więcej niż jedną nazwę zweryfikowanej domeny, musisz podać następujące oświadczenie dla komputerów:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Jeśli są już wystawiania oświadczeń nazwę ImmutableID (np. alternatywnego Identyfikatora logowania) musisz podać jedno oświadczenie odpowiednie dla komputerów:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

W poniższych sekcjach można znaleźć informacje o:
 
- Wartości Każde oświadczenie ma
- Jak definicji będzie wyglądać w usługach AD FS

Definicja pomaga Sprawdź, czy wartości są obecne lub należy je utworzyć.

> [!NOTE]
> Jeśli nie używasz usług AD FS dla lokalnego serwera federacyjnego, wykonaj instrukcje z dostawcą, aby utworzyć odpowiednią konfigurację wystawiać te oświadczenia.

### <a name="issue-account-type-claim"></a>Problem konta typu oświadczenia

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** — To oświadczenie musi zawierać wartość **DJ**, które identyfikują urządzenie jako komputer przyłączony do domeny. W usługach AD FS można dodawać reguł przekształcania wystawiania, która wygląda następująco:

    @RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Wystawiać objectGUID komputera konta lokalnego

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** — To oświadczenie musi zawierać **objectGUID** wartość Konto komputera lokalnego. W usługach AD FS można dodawać reguł przekształcania wystawiania, która wygląda następująco:

    @RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Wystawiać objectSID komputera konta lokalnego

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** — To oświadczenie musi zawierać **objectSid** wartość Konto komputera lokalnego. W usługach AD FS można dodawać reguł przekształcania wystawiania, która wygląda następująco:

    @RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Wystawiać issuerID dla komputera, gdy wiele zweryfikować nazwy domeny w usłudze Azure AD

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** — To oświadczenie musi zawierać jednolity identyfikator zasobów (URI) dowolnej nazwy domeny zweryfikowane, łączących się z usługą federacyjną lokalnego (AD FS lub strona 3) wystawiania tokenu. W usługach AD FS można dodać reguły przekształcania wystawiania przypominających widocznych poniżej w tej kolejności, po nich powyżej. Należy pamiętać, że konieczne jest tym co najmniej jedna reguła jawnie wystawiania reguły dla użytkowników. W poniższych reguł dodawany jest pierwsza reguła identyfikacji użytkownika, a uwierzytelnianie komputera.

    @RuleName = "Issue account type with the value User when its not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://<verified-domain-name>/adfs/services/trust/"
    );


W powyższym oświadczeń

- `<verified-domain-name>` jest to symbol zastępczy, który należy zastąpić nazwy domeny zweryfikowane w usłudze Azure AD. Na przykład, wartość = "http://contoso.com/adfs/services/trust/"



Aby uzyskać więcej informacji o nazwach zweryfikowanej domeny, zobacz [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](active-directory-domains-add-azure-portal.md).  
Aby uzyskać listę domeny zweryfikowane firmy, można użyć [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) polecenia cmdlet. 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Wystawiać nazwę ImmutableID dla komputera, gdy istnieje jeden dla użytkowników (np. alternatywny logowania identyfikatora)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** — To oświadczenie musi zawierać prawidłową wartość dla komputerów. W usługach AD FS można utworzyć reguły przekształcania wystawiania w następujący sposób:

    @RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Skrypt pomocnika do tworzenia reguł przekształcania wystawiania usług AD FS

Poniższy skrypt pomaga z tworzeniem wystawiania przekształcenie reguł opisanych powyżej.

    $multipleVerifiedDomainNames = $false
    $immutableIDAlreadyIssuedforUsers = $false
    $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains
    
    $rule1 = '@RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );'

    $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'

    $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);'

    $rule4 = ''
    if ($multipleVerifiedDomainNames -eq $true) {
    $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
    );'
    }

    $rule5 = ''
    if ($immutableIDAlreadyIssuedforUsers -eq $true) {
    $rule5 = '@RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'
    }

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

### <a name="remarks"></a>Uwagi 

- Ten skrypt dołącza reguły do istniejących reguł. Nie uruchamiaj skryptu dwa razy, ponieważ zestaw reguł zostanie dodany dwa razy. Upewnij się, że odpowiednie nie istnieją żadne reguły dla tych oświadczeń (zgodnie z warunkami odpowiedniego) przed ponownym uruchomieniem skryptu.

- Jeśli masz wiele nazw zweryfikowanej domeny (jak pokazano w portalu usługi Azure AD lub przy użyciu polecenia cmdlet Get-MsolDomains), ustaw wartość **$multipleVerifiedDomainNames** w skrypcie **$true**. Upewnij się również usunięcie istniejących oświadczenie issuerid został utworzony przez program Azure AD Connect lub za pomocą innych środków. Oto przykład dla tej reguły:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Jeśli już wystawiły **nazwę ImmutableID** oświadczenia dla kont użytkowników, ustaw wartość **$immutableIDAlreadyIssuedforUsers** w skrypcie **$true**.

## <a name="step-3-enable-windows-down-level-devices"></a>Krok 3: Włącz urządzeń z systemem Windows niższego poziomu

Jeśli niektóre urządzenia przyłączone do domeny z systemem Windows niższego poziomu urządzeń, należy:

- Ustawienie zasad w usłudze Azure AD, aby umożliwić użytkownikom rejestrowanie urządzeń.
 
- Skonfiguruj lokalną usługę federacyjną wystawiać oświadczenia do obsługi **zintegrowane uwierzytelnianie systemu Windows (IWA)** w czasie rejestracji urządzenia.
 
- Dodawanie punktu końcowego uwierzytelniania urządzenia usługi Azure AD do lokalnej strefy intranetowej do monity certyfikatu podczas uwierzytelniania urządzenia.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Ustawienie zasad w usłudze Azure AD, aby umożliwić użytkownikom rejestrowanie urządzeń

Aby zarejestrować urządzenia z systemem Windows niższego poziomu, musisz upewnij się, że ustawiony jest ustawienie, aby umożliwić użytkownikom rejestrowanie urządzeń w usłudze Azure AD. To ustawienie, w obszarze można znaleźć w portalu Azure:

`Azure Active Directory > Users and groups > Device settings`
    
Następująca zasada musi mieć ustawioną **wszystkie**: **użytkownicy mogą zarejestrować swoje urządzenia w usłudze Azure AD**

![Rejestrowanie urządzeń](./media/active-directory-conditional-access-automatic-device-registration-setup/23.png)


### <a name="configure-on-premises-federation-service"></a>Konfigurowanie usługi federacyjnej lokalnej 

Lokalne usługi federacyjnej musi obsługiwać wystawianie **authenticationmehod** i **wiaormultiauthn** oświadczeń, gdy odbiera żądanie uwierzytelnienia do jednostki uzależnionej usługi Azure AD zawierający resouce_params parametr o wartości zakodowanej, jak pokazano poniżej:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Jeśli takie żądanie pochodzi, lokalną usługę federacyjną musi uwierzytelnić użytkownika przy użyciu zintegrowanego uwierzytelniania systemu Windows i na sukces, należy wydać następujące dwa oświadczenia:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

W usługach AD FS należy dodać reguły przekształcania wystawiania, który przechodzi przez metodę uwierzytelniania.  

**Aby dodać tę regułę:**

1. W konsoli zarządzania usług AD FS przejdź do `AD FS > Trust Relationships > Relying Party Trusts`.
2. Kliknij prawym przyciskiem myszy obiekt zaufania jednostki uzależnionej strony platformy tożsamości pakietu Microsoft Office 365, a następnie wybierz **Edycja reguł oświadczeń**.
3. Na **reguły przekształcania wystawiania** wybierz opcję **Dodaj regułę**.
4. W **reguły oświadczeń** szablon z listy wybierz **wysyłanie oświadczeń przy użyciu reguły niestandardowej**.
5. Wybierz opcję **Dalej**.
6. W **nazwy reguły oświadczeń** wpisz **reguły oświadczeń metody uwierzytelniania**.
7. W **reguły oświadczeń** wpisz następującą regułę:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. Na serwerze federacyjnym, wpisz poniższe polecenie programu PowerShell po zastąpieniu **\<RPObjectName\>** nazwą jednostki uzależnionej strony obiektu dla obiekt zaufania jednostki uzależnionej strony usługi Azure AD. Ten obiekt zwykle nosi nazwę **Microsoft Office 365 tożsamość platformy**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Dodawanie punktu końcowego uwierzytelniania urządzenia usługi Azure AD do strefy Lokalny Intranet

Aby uniknąć certyfikatu monity podczas uwierzytelniania użytkowników w rejestrze urządzeń do usługi Azure AD, możesz wypchnąć zasad na urządzeniach przyłączonych do domeny, aby dodać następujący adres URL do strefy Lokalny Intranet w programie Internet Explorer:

`https://device.login.microsoftonline.com`

## <a name="step-4-control-deployment-and-rollout"></a>Krok 4: Wdrażanie formantu i wdrożenia

Po wykonaniu czynności wymagane urządzenia przyłączone do domeny są gotowe do dołączy usługi Azure AD:

- Wszystkich przyłączonych do domeny urządzeń z systemem Windows 10 Anniversary Update i Windows Server 2016 automatycznie zarejestrować w usłudze Azure AD na urządzeniu ponownego uruchomienia lub logowania użytkownika. 

- Zarejestrować nowe urządzenia z usługą Azure AD po ponownym uruchomieniu urządzenia po zakończeniu operacji przyłączania do domeny.

- Zarejestrowane urządzenia, które były wcześniej usługą Azure AD (na przykład dla usługi Intune) przejścia do "*przyłączonych do domeny, zarejestrowane w usłudze AAD*"; jednak zajmuje trochę czasu, zanim ten proces, aby ukończyć dla wszystkich urządzeń z powodu normalnym przepływie domeny i działanie użytkownika.

### <a name="remarks"></a>Uwagi

- Obiekt zasad grupy umożliwia kontrolować dystrybucję automatycznej rejestracji systemu Windows 10 i komputerów przyłączonych do domeny systemu Windows Server 2016. **Jeśli nie chcesz, aby te urządzenia do automatycznego rejestrowania w usłudze Azure AD lub chcesz kontrolować rejestracji**, a następnie musi wprowadzana wyłączanie automatycznej rejestracji na tych urządzeniach, przed rozpoczęciem z konfiguracją zasad grupy kroki. Po zakończeniu konfigurowania, i gdy wszystko będzie gotowe do testowania, należy wdrożyć zasady grupy Włączenie automatycznej rejestracji tylko do urządzeń testowych, a następnie do wszystkich urządzeń podczas wybierz pozycję.

- Windows 10 listopada 2015 aktualizacja automatycznie tworzy sprzężenie z usługą Azure AD **tylko** Jeśli obiekt zasad grupy wdrożenia jest ustawiony.

- Aby wdrażanie komputerów z systemem Windows niższego poziomu, można wdrożyć [pakiet Instalatora Windows](#windows-installer-packages-for-non-windows-10-computers) na komputerach, które można wybrać.

- Jeśli obiekt zasad grupy jest wypychana na Windows 8.1 przyłączonych do domeny urządzenia, nastąpiła sprzężenia; jednak zaleca się, że używasz [pakiet Instalatora Windows](#windows-installer-packages-for-non-windows-10-computers) sprzęgać wszystkich urządzeń z systemem Windows niższego poziomu. 

### <a name="create-a-group-policy-object"></a>Utwórz obiekt zasad grupy 

Aby kontrolować dystrybucję bieżącego komputerów z systemem Windows, należy wdrożyć **rejestrować komputery przyłączone do domeny jako urządzenia** obiektu zasad grupy dla urządzeń, które chcesz zarejestrować. Na przykład można wdrożyć zasady, do jednostki organizacyjnej lub grupie zabezpieczeń.

**Aby ustawić zasady:**

1. Otwórz **Menedżera serwera**, a następnie przejdź do `Tools > Group Policy Management`.
2. Przejdź do węzła domeny, która odnosi się do domeny, w której chcesz aktywować rejestracji automatycznej bieżącego komputerów z systemem Windows.
3. Kliknij prawym przyciskiem myszy **obiektów zasad grupy**, a następnie wybierz **nowy**.
4. Wpisz nazwę obiektu zasad grupy. Na przykład * hybrydowej usługi Azure AD join. 
5. Kliknij przycisk **OK**.
6. Kliknij prawym przyciskiem myszy nowy obiekt zasad grupy, a następnie wybierz **Edytuj**.
7. Przejdź do **Konfiguracja komputera** > **zasady** > **Szablony administracyjne** > **systemu Windows Składniki** > **Rejestracja urządzenia**. 
8. Kliknij prawym przyciskiem myszy **rejestrować komputery przyłączone do domeny jako urządzenia**, a następnie wybierz **Edytuj**.
   
   > [!NOTE]
   > Ten szablon zasad grupy została zmieniona z wcześniejszych wersji konsoli zarządzania zasadami grupy. Jeśli używasz starszej wersji konsoli przejdź do `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`. 

7. Wybierz **włączone**, a następnie kliknij przycisk **Zastosuj**. Musisz wybrać **wyłączone** Jeśli chcesz, aby zasady blokowania urządzeń kontrolowane przez zasady grupy z automatycznego rejestrowania za pomocą usługi Azure AD.

8. Kliknij przycisk **OK**.
9. Połącz obiekt zasad grupy z wybraną lokalizację. Na przykład można je połączyć do określonej jednostki organizacyjnej. Możesz również można połączyć go do grupy zabezpieczeń określonych komputerów, które automatycznie dołączyć za pomocą usługi Azure AD. Aby ustawić te zasady dla wszystkich przyłączonych do domeny systemu Windows 10 i Windows Server 2016 komputerów w organizacji, należy połączyć obiekt zasad grupy do domeny.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Pakietów Instalatora Windows na komputerach z systemem innym niż Windows 10

Aby przyłączyć przyłączonych do domeny komputerów z systemem Windows niższego poziomu w środowisku federacyjnym, można pobrać i zainstalować te pakiet Instalatora Windows (msi) z Centrum pobierania [dołączanie firmy Microsoft dla komputerów z systemem innym niż Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=53554) Strona.

Pakiet można wdrożyć przy użyciu system dystrybucji oprogramowania, takich jak System Center Configuration Manager. Pakiet obsługuje opcje standardowa Instalacja dyskretna z *quiet* parametru. System Center Configuration Manager Current Branch oferuje dodatkowe korzyści z wcześniejszych wersji, takich jak możliwość śledzenia rejestracji ukończone. Aby uzyskać więcej informacji, zobacz [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager).

Instalator tworzy zaplanowane zadanie w systemie, który jest uruchamiany w kontekście użytkownika. Zadanie jest wyzwalane, gdy użytkownik loguje się do systemu Windows. Zadanie dyskretnie nie przyłączy urządzenia z usługą Azure AD przy użyciu poświadczeń użytkownika po uwierzytelnieniu przy użyciu zintegrowanego uwierzytelniania systemu Windows. Aby wyświetlić zaplanowane zadanie, na urządzeniu, przejdź do **Microsoft** > **dołączania**, a następnie przejdź do biblioteki harmonogramu zadań.

## <a name="step-5-verify-joined-devices"></a>Krok 5: Sprawdzanie połączonych urządzeń

Można sprawdzić pomyślnie przyłączone do urządzeń w Twojej organizacji za pomocą [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) polecenia cmdlet w [modułu programu PowerShell usługi Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Dane wyjściowe tego polecenia cmdlet zawierają urządzeń, które są zarejestrowane i połączony z usługą Azure AD. Aby uzyskać wszystkie urządzenia, należy użyć **— wszystkie** parametr, a następnie przeprowadź filtrowanie ich przy użyciu **deviceTrustType** właściwości. Przyłączony do domeny urządzenia mają wartość **przyłączonych do domeny**.

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](device-management-introduction.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
