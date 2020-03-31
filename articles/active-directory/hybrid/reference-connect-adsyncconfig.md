---
title: 'Usługa Azure AD Connect: odwołanie programu AdSyncConfig powershell | Dokumenty firmy Microsoft'
description: Ten dokument zawiera informacje referencyjne dla modułu programu PowerShell programu ADSyncConfig.psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 554bb99121190198982f64deb6ee0674aa8831ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381199"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Usługa Azure AD Connect: odwołanie programu ADSyncConfig powershell
Poniższa dokumentacja zawiera informacje referencyjne dla modułu programu PowerShell programu ADSyncConfig.psm1 dołączonego do usługi Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Konto Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>STRESZCZENIE
Pobiera nazwę konta i domenę skonfigurowany w każdym łączniku usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>OPIS
Ta funkcja używa polecenia cmdlet "Get-ADSyncConnector", które jest obecne w UID Connect, aby pobrać z parametrów łączności tabelę przedstawiającą konto łączników usług AD.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>STRESZCZENIE
Pobiera obiekty usługi AD z wyłączonym dziedziczeniem uprawnień

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Wyszukiwanie w u. w uł.01, począwszy od parametru SearchBase i zwraca wszystkie obiekty filtrowane według parametru ObjectClass, które mają obecnie wyłączone dziedziczenie listy ACL.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Find objects with disabled inheritance in 'Contoso' domain (by default returns 'organizationalUnit' objects only)
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso'

#### <a name="example-2"></a>PRZYKŁAD 2
```
Find 'user' objects with disabled inheritance in 'Contoso' domain
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase 'Contoso' -ObjectClass 'user'

#### <a name="example-3"></a>PRZYKŁAD 3
```
Find all types of objects with disabled inheritance in a OU
```

Get-ADSyncObjectsWithInheritanceDisabled -SearchBase OU=AzureAD,DC=Contoso,DC=com -ObjectClass '*'

### <a name="parameters"></a>PARAMETRY

#### <a name="-searchbase"></a>-SearchBase
Baza wyszukiwania kwerendy LDAP, która może być domeną AD DistinguishedName lub FQDN

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Klasa obiektów do wyszukiwania, które mogą być "*" (dla dowolnej klasy obiektu), "user", "group", "container" itp. Domyślnie ta funkcja wyszuka klasę obiektu "organizationalUnit".

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: OrganizationalUnit
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>STRESZCZENIE
Inicjowanie lasu i domeny usługi Active Directory w celu uzyskania podstawowych uprawnień do odczytu.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>Domena użytkowników
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncBasicReadPermissions daje wymagane uprawnienia do konta synchronizacji usługi AD, które obejmują następujące elementy:
1.
Odczyt dostępu do właściwości dla wszystkich atrybutów dla wszystkich obiektów komputera podrzędnego
2.
Odczyt dostępu do właściwości we wszystkich atrybutach dla wszystkich obiektów urządzenia podrzędnego
3.
Odczyt dostępu do właściwości dla wszystkich atrybutów dla wszystkich obiektów podrzędnych obcych bezpieczeństwa
5.
Odczyt dostępu do właściwości dla wszystkich atrybutów dla wszystkich obiektów użytkownika podrzędnego
6.
Odczytu dostępu do właściwości dla wszystkich atrybutów dla wszystkich obiektów inetorgperson podrzędnych
7.
Odczyt dostępu do właściwości dla wszystkich atrybutów dla wszystkich obiektów grupy podrzędnej
8.
Odczyt dostępu do właściwości dla wszystkich atrybutów dla wszystkich obiektów kontaktu podrzędnego

Uprawnienia te są stosowane do wszystkich domen w lesie.
Opcjonalnie można podać parametr DistinguishedName w pliku ADobjectDN, aby ustawić te uprawnienia tylko dla tego obiektu usługi AD (w tym dziedziczenie do obiektów podrzędnych).

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PRZYKŁAD 3
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PRZYKŁAD 4
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorNajnak
Nazwa konta usługi Active Directory, które jest lub będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta usługi Active Directory, która jest lub będzie używana przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nazwa wyróżniająca konta usługi Active Directory, które jest lub będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName docelowego obiektu AD, aby ustawić uprawnienia (opcjonalnie)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Opcjonalny parametr wskazujący, czy kontener AdminSDHolder nie powinien być aktualizowany o te uprawnienia

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-CoIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>STRESZCZENIE
Inicjowanie lasu i domeny usługi Active Directory dla funkcji hybrydowej programu Exchange.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>Domena użytkowników
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncExchangeHybridPermissions daje wymagane uprawnienia do konta synchronizacji usługi AD, które obejmują następujące elementy:
1.
Dostęp do właściwości odczytu/zapisu we wszystkich atrybutach dla wszystkich obiektów użytkownika podrzędnego
2.
Dostęp do właściwości odczytu/zapisu we wszystkich atrybutach dla wszystkich obiektów inetorgperson podrzędnych
3.
Dostęp do właściwości odczytu/zapisu we wszystkich atrybutach dla wszystkich obiektów grupy podrzędnej
4.
Dostęp do właściwości odczytu/zapisu dla wszystkich atrybutów dla wszystkich obiektów kontaktu podrzędnego

Uprawnienia te są stosowane do wszystkich domen w lesie.
Opcjonalnie można podać parametr DistinguishedName w pliku ADobjectDN, aby ustawić te uprawnienia tylko dla tego obiektu usługi AD (w tym dziedziczenie do obiektów podrzędnych).

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PRZYKŁAD 3
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PRZYKŁAD 4
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorNajnak
Nazwa konta usługi Active Directory, które jest lub będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta usługi Active Directory, która jest lub będzie używana przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nazwa wyróżniająca konta usługi Active Directory, które jest lub będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName docelowego obiektu AD, aby ustawić uprawnienia (opcjonalnie)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Opcjonalny parametr wskazujący, czy kontener AdminSDHolder nie powinien być aktualizowany o te uprawnienia

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-CoIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zaakwuj funkcję lasu i domeny usługi Active Directory dla folderu publicznego poczty programu Exchange.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>Domena użytkowników
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncExchangeMailPublicFolderPermissions daje wymagane uprawnienia do konta synchronizacji usługi AD, które obejmują następujące elementy:
1.
Odczyt dostępu do właściwości dla wszystkich atrybutów dla wszystkich obiektów podrzędnego publicfolder

Uprawnienia te są stosowane do wszystkich domen w lesie.
Opcjonalnie można podać parametr DistinguishedName w pliku ADobjectDN, aby ustawić te uprawnienia tylko dla tego obiektu usługi AD (w tym dziedziczenie do obiektów podrzędnych).

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PRZYKŁAD 3
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PRZYKŁAD 4
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorNajnak
Nazwa konta usługi Active Directory, które jest lub będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta usługi Active Directory, która jest lub będzie używana przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nazwa wyróżniająca konta usługi Active Directory, które jest lub będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName docelowego obiektu AD, aby ustawić uprawnienia (opcjonalnie)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Opcjonalny parametr wskazujący, czy kontener AdminSDHolder nie powinien być aktualizowany o te uprawnienia

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-CoIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>STRESZCZENIE
Inicjowanie lasu i domeny usługi Active Directory dla funkcji mS-DS-ConsistencyGuid.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>Domena użytkowników
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncMsDsConsistencyGuidPermissions daje wymagane uprawnienia do konta synchronizacji usługi AD, które obejmują następujące elementy:
1.
Dostęp do właściwości odczytu/zapisu w atrybutie mS-DS-ConsistencyGuid dla wszystkich obiektów użytkownika podrzędnego

Uprawnienia te są stosowane do wszystkich domen w lesie.
Opcjonalnie można podać parametr DistinguishedName w pliku ADobjectDN, aby ustawić te uprawnienia tylko dla tego obiektu usługi AD (w tym dziedziczenie do obiektów podrzędnych).

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PRZYKŁAD 3
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PRZYKŁAD 4
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorNajnak
Nazwa konta usługi Active Directory, które jest lub będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta usługi Active Directory, która jest lub będzie używana przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nazwa wyróżniająca konta usługi Active Directory, które jest lub będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName docelowego obiektu AD, aby ustawić uprawnienia (opcjonalnie)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Opcjonalny parametr wskazujący, czy kontener AdminSDHolder nie powinien być aktualizowany o te uprawnienia

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-CoIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>STRESZCZENIE
Inicjowanie lasu i domeny usługi Active Directory w celu synchronizacji skrótów haseł.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>Domena użytkowników
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncPasswordHashSyncPermissions daje wymagane uprawnienia do konta synchronizacji usługi AD, które obejmują następujące elementy:
1.
Replikowanie zmian w katalogu
2.
Replikowanie katalogu zmienia wszystko

Uprawnienia te są przyznawane wszystkim domenom w lesie.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorNajnak
Nazwa konta usługi Active Directory, które będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta usługi Active Directory, która będzie używana przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nazwa wyróżniająca konta usługi Active Directory, które będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-CoIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>STRESZCZENIE
Inicjowanie lasu i domeny usługi Active Directory w celu uzyskania odpisu hasła z usługi Azure AD.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>Domena użytkowników
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncPasswordWritebackPermissions daje wymagane uprawnienia do konta synchronizacji usługi AD, które obejmują następujące elementy:
1.
Resetowanie hasła w obiektach użytkownika podrzędnego
2.
Zapisz dostęp do właściwości w lockoutTime atrybut dla wszystkich obiektów użytkownika podrzędnego
3.
Write Property access on pwdLastSet attribute for all descendant user objects Write Property access on pwdLastSet attribute for all descendant user objects Write Property access on pwdLastSet attribute for all descendant user objects Write Property

Uprawnienia te są stosowane do wszystkich domen w lesie.
Opcjonalnie można podać parametr DistinguishedName w pliku ADobjectDN, aby ustawić te uprawnienia tylko dla tego obiektu usługi AD (w tym dziedziczenie do obiektów podrzędnych).

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PRZYKŁAD 3
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PRZYKŁAD 4
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorNajnak
Nazwa konta usługi Active Directory, które jest lub będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta usługi Active Directory, która jest lub będzie używana przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nazwa wyróżniająca konta usługi Active Directory, które jest lub będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName docelowego obiektu AD, aby ustawić uprawnienia (opcjonalnie)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Opcjonalny parametr wskazujący, czy kontener AdminSDHolder nie powinien być aktualizowany o te uprawnienia

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-CoIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>STRESZCZENIE
Dokręć uprawnienia do obiektu usługi AD, który w przeciwnym razie nie jest uwzględniony w żadnej chronionej grupie zabezpieczeń usługi AD.
Typowym przykładem jest konto AD Connect (MSOL) utworzone automatycznie przez AAD Connect.
To konto ma uprawnienia replikacji dla wszystkich domen, jednak można łatwo zniego, ponieważ nie jest chroniony.

### <a name="syntax"></a>SKŁADNIA

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncRestrictedPermissions zaostrzy uprawnienia oo podanego konta.
Dokręcanie uprawnień obejmuje następujące kroki:
1.
Wyłącz dziedziczenie określonego obiektu
2.
Usuń wszystkie wpisy ACE na określonym obiekcie, z wyjątkiem obiektów ACE specyficznych dla SELF.
Chcemy zachować domyślne uprawnienia nienaruszone, jeśli chodzi o SELF.
3.
Przypisz te określone uprawnienia:

        Type    Name                                        Access              Applies To
        =============================================================================================
        Allow   SYSTEM                                      Full Control        This object
        Allow   Enterprise Admins                           Full Control        This object
        Allow   Domain Admins                               Full Control        This object
        Allow   Administrators                              Full Control        This object

        Allow   Enterprise Domain Controllers               List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

        Allow   Authenticated Users                         List Contents
                                                            Read All Properties
                                                            Read Permissions    This object

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN "CN=TestAccount1,CN=Users,DC=Contoso,DC=com" -Credential $(Get-Credential)
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
DistinguishedName konta usługi Active Directory, którego uprawnienia muszą zostać zaostrzone.
Zazwyczaj jest to konto MSOL_nnnnnnnnnn lub konto domeny niestandardowej skonfigurowane w łączniku usługi AD.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credential"></a>-Credential
Poświadczenia administratora, które mają uprawnienia niezbędne do ograniczenia uprawnień na koncie ADConnectorAccountDN. Zazwyczaj jest to administrator przedsiębiorstwa lub domeny. Użyj w pełni kwalifikowanej nazwy domeny konta administratora, aby uniknąć błędów wyszukiwania kont.
Przykład: CONTOSO\admin

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialWalidation
Gdy jest używana funkcja DisableCredentialValidation, funkcja nie sprawdza, czy poświadczenia podane w programie -Credential są prawidłowe w udaniu AD i czy podane konto ma uprawnienia niezbędne do ograniczenia uprawnień na koncie ADConnectorAccountDN.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-CoIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>STRESZCZENIE
Inicjowanie lasu i domeny usługi Active Directory w celu zapisania storamentu grupy z usługi Azure AD.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>Domena użytkowników
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>Distinguishedname
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set-ADSyncUnifiedGroupWritebackPermissions daje wymagane uprawnienia do konta synchronizacji usługi AD, które obejmują następujące elementy:
1.
Ogólne odczytywanie/pisanie, usuwanie, usuwanie drzewa i tworzenie\usuń element podrzędny dla wszystkich typów obiektów grupy i podobjectów

Uprawnienia te są stosowane do wszystkich domen w lesie.
Opcjonalnie można podać parametr DistinguishedName w pliku ADobjectDN, aby ustawić te uprawnienia tylko dla tego obiektu usługi AD (w tym dziedziczenie do obiektów podrzędnych).
W takim przypadku ADobjectDN będzie Distinguished Name kontenera, który chcesz połączyć z GroupWriteback funkcji.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com'
```

#### <a name="example-3"></a>PRZYKŁAD 3
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN 'CN=ADConnector,OU=AzureAD,DC=Contoso,DC=com' -SkipAdminSdHolders
```

#### <a name="example-4"></a>PRZYKŁAD 4
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName 'ADConnector' -ADConnectorAccountDomain 'Contoso.com' -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectoraccountname"></a>-ADConnectorNajnak
Nazwa konta usługi Active Directory, które jest lub będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdomain"></a>-ADConnectorAccountDomain
Domena konta usługi Active Directory, która jest lub będzie używana przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: UserDomain
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adconnectoraccountdn"></a>-ADConnectorAccountDN
Nazwa wyróżniająca konta usługi Active Directory, które jest lub będzie używane przez usługę Azure AD Connect Sync do zarządzania obiektami w katalogu.

```yaml
Type: String
Parameter Sets: DistinguishedName
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adobjectdn"></a>-ADobjectDN
DistinguishedName docelowego obiektu AD, aby ustawić uprawnienia (opcjonalnie)

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipadminsdholders"></a>-SkipAdminSdHolders
Opcjonalny parametr wskazujący, czy kontener AdminSDHolder nie powinien być aktualizowany o te uprawnienia

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-CoIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie zostało uruchomione.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Monituje o potwierdzenie przed uruchomieniem polecenia cmdlet.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="show-adsyncadobjectpermissions"></a>Pokaż-ADSyncADObjectPermissions

### <a name="synopsis"></a>STRESZCZENIE
Pokazuje uprawnienia określonego obiektu USŁUGI AD.

### <a name="syntax"></a>SKŁADNIA

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Ta funkcja zwraca wszystkie uprawnienia usługi AD aktualnie ustawione dla danego obiektu AD podane w parametrze -ADobjectDN.
ADobjectDN musi być podana w formacie DistinguishedName.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Wpisz opis ADobjectDN}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .
