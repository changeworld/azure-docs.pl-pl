---
title: Ręczne konfigurowanie urządzeń przyłączonych do usługi Azure Active Directory hybrydowe | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ręcznie skonfigurować hybrydowe przyłączone do usługi Azure Active Directory urządzeń.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: ba47223f86005809189214f26a63b75b21449e3a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630623"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Samouczek: Konfigurowanie urządzeń przyłączonych do usługi Azure Active Directory hybrydowego ręcznie 

Za pomocą zarządzania urządzeniami w usłudze Azure Active Directory (Azure AD) można zagwarantować, że użytkownicy uzyskują dostęp do zasobów z urządzeń, które spełniają Twoje standardy dotyczące bezpieczeństwa i zgodności. Aby uzyskać więcej informacji, zobacz [wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](overview.md).

Jeśli masz środowisko usługi Active Directory w środowisku lokalnym i chcesz dołączyć do urządzeń przyłączonych do domeny do usługi Azure AD, można to zrobić, konfigurowanie urządzeń przyłączonych do usługi Azure AD hybrydowych. Ten artykuł zawiera kroki powiązane. 



> [!TIP]
> Jeśli za pomocą usługi Azure AD Connect jest dostępną opcją w, zobacz [wybierz scenariusz](hybrid-azuread-join-plan.md#select-your-scenario). Za pomocą usługi Azure AD Connect, można znacznie ułatwić konfigurację dołączenie do hybrydowej usługi Azure AD.




## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że znasz:
    
-  [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](../device-management-introduction.md)
    
-  [Jak planować implementację z hybrydowym dołączaniem do usługi Azure Active Directory](hybrid-azuread-join-plan.md)

-  [Jak kontrolować hybrydowe dołączanie Twoich urządzeń do usługi Azure AD](hybrid-azuread-join-control.md)


Przed rozpoczęciem, konfigurowanie urządzeń przyłączonych do usługi Azure AD hybrydowe w Twojej organizacji, musisz upewnij się, że:

- Używasz aktualnej wersji programu Azure AD connect.

- Usługa Azure AD connect została zsynchronizowana obiektów komputerów, urządzeń, mają być hybrydowe do usługi Azure AD do usługi Azure AD. Jeśli obiekty komputerów należą do określonej jednostki organizacyjne (OU), te jednostki organizacyjne, które muszą być skonfigurowane do synchronizacji w usłudze Azure AD, a następnie nawiązać połączenie także.

  

Azure AD Connect:

- Powoduje skojarzenie konta komputera w lokalnej usłudze Active Directory (AD) i obiekt urządzenia w usłudze Azure AD. 
- Umożliwia innym urządzeniu powiązane funkcje, takie jak Windows Hello dla firm.

Upewnij się, że następujące adresy URL są dostępne z komputerów w sieci organizacji rejestrację komputerów z usługą Azure AD:

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com Zezwalaj na
- https://device.login.microsoftonline.com

- Twoja organizacja usługi STS (domen federacyjnych)

Jeśli nie zostało zrobione, STS w organizacji (w przypadku domen federacyjnych) powinny być objęte ustawień lokalny intranet.

Jeśli Twoja organizacja planuje użyć bezproblemowe logowanie Jednokrotne, następujące adresy URL muszą być dostępne z komputerów w organizacji, i musi być również dodany do strefy Lokalny intranet użytkownika:

- https://autologon.microsoftazuread-sso.com

- Ponadto należy włączyć następujące ustawienie w strefie intranetu użytkownika: "Zezwalaj na aktualizacje paska stanu za pomocą skryptu."

Jeśli Twoja organizacja korzysta z zarządzanego Instalatora (inne niż federacyjne) za pomocą lokalnej usługi AD, a nie za pomocą usług AD FS do Federację z usługą Azure AD, a następnie dołączenie do hybrydowej usługi Azure AD w systemie Windows 10 opiera się na obiekty komputerów w AD jako sync'ed do usługi Azure AD. Upewnij się, że wszystkie jednostki organizacyjne (OU) zawierające obiekty komputerów, które muszą być przyłączone do usługi Azure AD hybrydowej są włączone do celów synchronizacji w konfiguracji synchronizacji usługi Azure AD Connect.

Dla urządzeń systemu Windows 10 w wersji 1703 lub starszej Jeśli Twoja organizacja wymaga dostępu do Internetu za pośrednictwem serwera proxy ruchu wychodzącego, należy zaimplementować autowykrywania serwera Proxy sieci Web (WPAD) aby umożliwić komputerom systemu Windows 10 zarejestrować się do usługi Azure AD. 

## <a name="configuration-steps"></a>Kroki konfiguracji

Możesz skonfigurować urządzenia dołączone do usługi Azure AD hybrydowych dla różnych typów platform urządzeń Windows. Ten temat zawiera kroki wymagane w przypadku wszystkich scenariuszy typowej konfiguracji.  

Skorzystaj z poniższej tabeli, aby uzyskać przegląd czynności, które są wymagane dla danego scenariusza:  



| Kroki                                      | Synchronizacja skrótów Windows bieżącego i hasło | Bieżąca Windows i Federacji | Windows niższego poziomu |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Konfigurowanie punktu połączenia usługi | ![Zaznacz][1]                            | ![Zaznacz][1]                    | ![Zaznacz][1]        |
| Konfigurowanie wystawiania oświadczeń           |                                        | ![Zaznacz][1]                    | ![Zaznacz][1]        |
| Włącz urządzeń do systemu Windows 10      |                                        |                                | ![Zaznacz][1]        |
| Sprawdź urządzenia przyłączone          | ![Zaznacz][1]                            | ![Zaznacz][1]                    | ![Zaznacz][1]        |



## <a name="configure-service-connection-point"></a>Konfigurowanie punktu połączenia usługi

Obiekt punkt połączenia usługi jest używany przez urządzenia podczas rejestracji do wykrywania informacji o dzierżawie usługi Azure AD. W Twojej lokalnej usługi Active Directory (AD) obiektu punktu połączenia usługi dla hybrydowych urządzeń przyłączonych do usługi Azure AD musi istnieć w kontekście partycji lasem komputera nazw konfiguracji. Istnieje tylko jeden kontekst nazewnictwa konfiguracji jednego lasu. W konfiguracji o wielu lasów usługi Active Directory punktu połączenia usługi, musi istnieć w wszystkich lasach zawierających komputery przyłączone do domeny.

Możesz użyć [ **Get ADRootDSE** ](https://technet.microsoft.com/library/ee617246.aspx) polecenie cmdlet do pobierania w kontekście nazewnictwa konfiguracji w lesie.  

Dla lasu z nazwą domeny usługi Active Directory *fabrikam.com*, jest w kontekście nazewnictwa konfiguracji:

`CN=Configuration,DC=fabrikam,DC=com`

W lesie obiektu SCP automatycznej rejestracji urządzeń przyłączonych do domeny znajduje się w:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

W zależności od tego, jak zostały wdrożone usługi Azure AD Connect obiektu SCP może zostały już skonfigurowane.
Umożliwia sprawdzenia istnienia obiektu i pobieranie wartości odnajdowania, za pomocą następującego skryptu programu Windows PowerShell: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

**$Scp. Słowa kluczowe** dane wyjściowe zawierają informacje o dzierżawy usługi Azure AD, na przykład:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Jeśli punkt połączenia usługi nie istnieje, możesz ją utworzyć, uruchamiając `Initialize-ADSyncDomainJoinedComputerSync` polecenia cmdlet na serwerze programu Azure AD Connect. Poświadczenia administratora przedsiębiorstwa jest wymagana do uruchamiania tego polecenia cmdlet.  
Polecenie cmdlet:

- Tworzy punkt połączenia usługi w lesie usługi Active Directory, którą jest połączona usługa Azure AD Connect. 
- Wymaga określenia `AdConnectorAccount` parametru. To jest konto, który jest skonfigurowany jako usługi Active Directory connect konta łącznika w usłudze Azure AD. 


Poniższy skrypt pokazuje przykład za pomocą polecenia cmdlet. W tym skrypcie `$aadAdminCred = Get-Credential` wymaga wpisania nazwy użytkownika. Należy podać nazwę użytkownika w formacie głównej nazwy (UPN) użytkownika (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

`Initialize-ADSyncDomainJoinedComputerSync` Polecenia cmdlet:

- Używa modułu programu PowerShell usługi Active Directory i narzędzia usług AD DS, które zależą od usług Active Directory w sieci Web uruchomionego na kontrolerze domeny. Usługi sieci Web w usłudze Active Directory jest obsługiwana na kontrolerach domeny z systemem Windows Server 2008 R2 lub nowszej.
- Jest obsługiwana tylko przez **MSOnline modułu PowerShell w wersji 1.1.166.0**. Aby pobrać ten moduł, użyj tego [łącze](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Jeśli nie zainstalowano narzędzia usług AD DS, `Initialize-ADSyncDomainJoinedComputerSync` zakończy się niepowodzeniem.  Narzędzia usług AD DS można zainstalować za pomocą Menedżera serwera w obszarze funkcje — narzędzia administracji zdalnej serwera — narzędzia do administrowania rolami.

Dla kontrolerów domeny z systemem Windows Server 2008 i jego wcześniejsze wersje Użyj poniższego skryptu, aby utworzyć punkt połączenia usługi.

W Konfiguracja obejmującego wiele lasów należy używać poniższy skrypt, aby utworzyć punkt połączenia usługi w każdym lesie, w którym istnieje komputerów:
 
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

W skrypcie powyżej

- `$verifiedDomain = "contoso.com"` to symbol zastępczy, który należy zastąpić nazwa zweryfikowanej domeny w usłudze Azure AD. Należy być właścicielem domeny, przed jego użyciem.

Aby uzyskać więcej informacji na temat nazw zweryfikowanej domeny, zobacz [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](../active-directory-domains-add-azure-portal.md).  
Aby uzyskać listę domen zweryfikowanych firmy, można użyć [Get AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0) polecenia cmdlet. 

![Get-AzureADDomain](./media/hybrid-azuread-join-manual-steps/01.png)

## <a name="setup-issuance-of-claims"></a>Konfigurowanie wystawiania oświadczeń

W federacyjnych Azure AD konfiguracji urządzeń zależą od usługi Active Directory Federation Services (AD FS) lub stronę 3 usługi federacyjnej do uwierzytelniania w usłudze Azure AD lokalnie. Uwierzytelnianie urządzenia w celu uzyskania tokenu dostępu, aby zarejestrować względem usługi Azure Active Directory urządzenia rejestracji (Azure DRS).

Windows, które bieżące urządzenia uwierzytelniania przy użyciu zintegrowanego uwierzytelniania Windows do aktywnego punktu końcowego protokołu WS-Trust (w wersji 2005 lub 1.3) hostowanych przez usługi federacyjnej w środowisku lokalnym.

> [!NOTE]
> Korzystając z usług AD FS, albo **adfs/services/trust/13/windowstransport** lub **adfs/services/trust/2005/windowstransport** musi być włączona. Jeśli używasz uwierzytelniającego serwera Proxy sieci Web, upewnij się również, że ten punkt końcowy zostanie opublikowana przy użyciu serwera proxy. Możesz zobaczyć, jakie punkty końcowe są włączone za pomocą konsoli zarządzania usług AD FS w obszarze **usługi > punkty końcowe**.
>
>Jeśli nie masz programu AD FS jako usługi federacyjnej w środowisku lokalnym, postępuj zgodnie z instrukcjami dostawcy, aby upewnić się, że obsługują WS-Trust 1.3 lub punkty końcowe 2005, a te są publikowane za pomocą pliku wymiany metadanych (MEX).

Poniższe oświadczenia musi istnieć w tokenie odebranych przez Azure usługi rejestracji urządzeń do rejestracji urządzeń do wykonania. Usługi rejestracji urządzeń w usłudze Azure utworzy obiekt urządzenia w usłudze Azure AD za pomocą niektórych z tych informacji, która jest następnie używany przez program Azure AD Connect do skojarzenia obiektu urządzenia nowo utworzonego za pomocą komputera konta lokalnego.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Jeśli masz więcej niż jedną zweryfikowaną nazwą domeny, należy podać następujące oświadczenie dla komputerów:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Jeśli są już wystawiających oświadczenia ImmutableID (np. alternatywny identyfikator logowania) należy podać jedną odpowiadające oświadczenie dla komputerów:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

W poniższych sekcjach znajdziesz informacje dotyczące:
 
- Wartości, które powinny mieć Każde oświadczenie
- Jak definicja będzie wyglądać w usługach AD FS

Definicja pomaga sprawdzić, czy wartości są obecne, lub jeśli musisz je utworzyć.

> [!NOTE]
> Jeśli nie używasz usług AD FS dla serwera federacyjnego w środowisku lokalnym, postępuj zgodnie z instrukcjami z dostawcą, aby utworzyć odpowiednią konfigurację, aby wystawiać oświadczenia te.

### <a name="issue-account-type-claim"></a>Oświadczenie typu konta problem

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** — W tym roszczenia musi zawierać wartość **DJ**, który identyfikuje urządzeniem jako komputerem przyłączonym do domeny. W usługach AD FS można dodać reguły przekształcania wystawiania, który wygląda w następujący sposób:

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

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** — W tym oświadczenia mogą zawierać **objectGUID** wartość konta komputera lokalnego. W usługach AD FS można dodać reguły przekształcania wystawiania, który wygląda w następujący sposób:

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

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** — W tym oświadczenia mogą zawierać **objectSid** wartość konta komputera lokalnego. W usługach AD FS można dodać reguły przekształcania wystawiania, który wygląda w następujący sposób:

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

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Wydawanie issuerID dla komputera, gdy wiele zweryfikować nazwy domeny w usłudze Azure AD

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** — W tym roszczenia musi zawierać identyfikator (URI) z żadnej z nazw zweryfikowanej domeny, które łączą się z usługą federacyjną lokalnie (AD FS lub innych firm 3) wystawiania tokenu. W usługach AD FS można dodać reguły przekształcania wystawiania, które mają postać podane poniżej w tej kolejności po nich powyżej. Należy pamiętać, że co najmniej jedna reguła jawnie wystawiania reguły dla użytkowników jest wymagane. W poniższych reguł pierwszej reguły identyfikacji użytkownika, a uwierzytelnianie komputera jest dodawany.

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

- `<verified-domain-name>` to symbol zastępczy, który należy zastąpić nazwa zweryfikowanej domeny w usłudze Azure AD. Na przykład, wartość = "http://contoso.com/adfs/services/trust/"



Aby uzyskać więcej informacji na temat nazw zweryfikowanej domeny, zobacz [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](../active-directory-domains-add-azure-portal.md).  

Aby uzyskać listę domen zweryfikowanych firmy, można użyć [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) polecenia cmdlet. 

![Get-MsolDomain](./media/hybrid-azuread-join-manual-steps/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Wydawanie ImmutableID dla komputera, gdy istnieje jeden dla użytkowników (np. alternatywny identyfikator logowania ustawionych ID)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** — W tym roszczenia musi zawierać prawidłową wartość dla komputerów. W usługach AD FS można utworzyć reguły przekształcania wystawiania w następujący sposób:

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

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Pomocnik skrypt, aby utworzyć reguły przekształcania wystawiania usług AD FS

Poniższy skrypt pomagają z tworzeniem wystawiania przekształcić opisane powyżej reguły.

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

- Ten skrypt dołącza reguły do istniejących reguł. Nie należy uruchamiać skryptu dwa razy, ponieważ zestaw reguł zostaną dodane dwa razy. Upewnij się, że żadne odpowiednie reguły dla tych oświadczeń (zgodnie z warunkami odpowiedniego) przed ponownym uruchomieniem skryptu.

- Jeśli masz wiele nazw zweryfikowanej domeny (jak pokazano w portalu usługi Azure AD lub przy użyciu polecenia cmdlet Get-MsolDomains), ustaw wartość **$multipleVerifiedDomainNames** w skrypcie **$true**. Upewnij się również usunięcie wszelkich istniejących oświadczenie issuerid, który został utworzony przy użyciu usługi Azure AD Connect lub przy użyciu innych metod. Oto przykład dla tej reguły:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Jeśli masz już wystawiony **ImmutableID** oświadczeń dla kont użytkowników, ustaw wartość **$immutableIDAlreadyIssuedforUsers** w skrypcie **$true**.

## <a name="enable-windows-down-level-devices"></a>Włączanie na urządzeniach Windows niższego poziomu

W przypadku niektórych urządzeń przyłączonych do domeny systemu Windows niższego poziomu urządzeń, należy:

- Ustaw zasady w usłudze Azure AD, aby umożliwić użytkownikom rejestrowanie urządzeń.
 
- Konfigurowanie usługi federacyjnej w środowisku lokalnym, aby wystawiać oświadczenia do obsługi **zintegrowane Windows Authentication (Zintegrowane)** rejestracji urządzeń.
 
- Dodawanie punktu końcowego uwierzytelniania urządzeń usługi Azure AD do strefy Intranet lokalne, aby monity certyfikatu podczas uwierzytelniania na urządzeniu.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Ustawianie zasad w usłudze Azure AD, aby umożliwić użytkownikom rejestrowanie urządzeń

Aby zarejestrować urządzenia niskiego poziomu Windows, należy się upewnić, że ustawienie, aby umożliwić użytkownikom rejestrowanie urządzeń w usłudze Azure AD jest ustawiona. W witrynie Azure portal możesz znaleźć tego ustawienia:

`Azure Active Directory > Users and groups > Device settings`
    
Następujące zasady musi być równa **wszystkich**: **użytkownicy mogą rejestrować swoje urządzenia w usłudze Azure AD**

![Rejestrowanie urządzeń](./media/hybrid-azuread-join-manual-steps/23.png)


### <a name="configure-on-premises-federation-service"></a>Skonfiguruj usługę federacyjną w środowisku lokalnym 

Lokalną usługę federacyjną musi obsługiwać wystawianie **authenticationmethod** i **wiaormultiauthn** oświadczeń, gdy odbiera żądanie uwierzytelnienia do jednostki uzależnionej usługi Azure AD przytrzymanie resouce_params parametrem zakodowaną wartość, jak pokazano poniżej:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Jeśli takie żądanie pochodzi, lokalnej usługi federacyjnej musi uwierzytelnić użytkownika przy użyciu zintegrowanego uwierzytelniania Windows i w razie powodzenia, należy wydać następujące dwa oświadczenia:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

W usługach AD FS należy dodać reguły przekształcania wystawiania, który przechodzi przez metodę uwierzytelniania.  

**Aby dodać tę regułę:**

1. W konsoli zarządzania usług AD FS, przejdź do `AD FS > Trust Relationships > Relying Party Trusts`.
2. Kliknij prawym przyciskiem myszy obiekt zaufania jednostki uzależnionej strona platforma tożsamości usługi Microsoft Office 365, a następnie wybierz **Edycja reguł oświadczeń**.
3. Na **reguły przekształcania wystawiania** zaznacz **Dodaj regułę**.
4. W **reguła oświadczenia** listy szablonów wybierz **wysyłanie oświadczeń przy użyciu reguły niestandardowej**.
5. Wybierz opcję **Dalej**.
6. W **Nazwa reguły oświadczeń** wpisz **reguła oświadczenia metoda uwierzytelniania**.
7. W **reguła oświadczenia** wpisz następującą regułę:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. Na serwerze federacyjnym, wpisz poniższe polecenie programu PowerShell po zastąpieniu **\<RPObjectName\>** nazwą jednostki uzależnionej strona obiektu dla obiektu relacji zaufania jednostki uzależnionej strona usługi Azure AD. Ten obiekt zazwyczaj nosi nazwę **platforma tożsamości usługi Microsoft Office 365**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Dodawanie punktu końcowego uwierzytelniania urządzeń usługi Azure AD do strefy Lokalny Intranet

Aby uniknąć certyfikatu monity podczas uwierzytelniania użytkowników w rejestrze urządzeń usługi Azure AD, możesz wypchnąć zasad na urządzeniach przyłączonych do domeny można dodać następującego adresu URL do strefy Lokalny Intranet w przeglądarce Internet Explorer:

`https://device.login.microsoftonline.com`


## <a name="verify-joined-devices"></a>Sprawdź urządzenia przyłączone

Można sprawdzić pomyślne dołączonym do urządzenia w Twojej organizacji za pomocą [Get MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) polecenia cmdlet w [modułu programu PowerShell usługi Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Dane wyjściowe tego polecenia cmdlet są wyświetlane urządzenia, które są zarejestrowane i przyłączone w usłudze Azure AD. Aby uzyskać wszystkie urządzenia, użyj **— wszystkie** parametru, a następnie filtrować je przy użyciu **deviceTrustType** właściwości. Przyłączone do domeny urządzenia mają wartość **przyłączone do domeny**.



## <a name="troubleshoot-your-implementation"></a>Rozwiązywanie problemów z implementacji

Jeśli występują problemy z ukończeniem poszczególnych hybrydowych urządzeń Windows w przyłączonych do usługi Azure AD join dla domeny, zobacz:

- [Rozwiązywanie problemów z dołączenie do hybrydowej usługi Azure AD dla Windows bieżące urządzenia](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z dołączenie do hybrydowej usługi Azure AD dla Windows niższego poziomu urządzeń](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do zarządzania urządzeniami w usłudze Azure Active Directory](overview.md)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual-steps/12.png
