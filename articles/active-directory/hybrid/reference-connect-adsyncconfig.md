---
title: 'Program Azure AD Connect: Dokumentacja programu PowerShell ADSyncConfig | Dokumentacja firmy Microsoft'
description: Ten dokument zawiera informacje dotyczące modułu programu ADSyncConfig.psm1 PowerShell.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381199"
---
# <a name="azure-ad-connect--adsyncconfig-powershell-reference"></a>Program Azure AD Connect:  Dokumentacja programu PowerShell ADSyncConfig
Poniższa dokumentacja zawiera informacje dotyczące modułu programu PowerShell ADSyncConfig.psm1, dostępnej w programie Azure AD Connect.


## <a name="get-adsyncadconnectoraccount"></a>Get-ADSyncADConnectorAccount

### <a name="synopsis"></a>STRESZCZENIE
Pobiera nazwę konta i domeny, która jest skonfigurowana w każdym łączniku AD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncADConnectorAccount
```

### <a name="description"></a>OPIS
Ta funkcja korzysta z polecenia cmdlet "Get-ADSyncConnector", który znajduje się w AAD Connect można pobrać z parametrów połączenia tabelę przedstawiającą konta złącza AD.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Get-ADSyncADConnectorAccount
```

## <a name="get-adsyncobjectswithinheritancedisabled"></a>Get-ADSyncObjectsWithInheritanceDisabled

### <a name="synopsis"></a>STRESZCZENIE
Pobiera obiekty usługi AD za pomocą dziedziczenia uprawnień wyłączone

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncObjectsWithInheritanceDisabled [-SearchBase] <String> [[-ObjectClass] <String>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Wyszukuje w AD od parametru SearchBase i zwraca wszystkich obiektów, przefiltrowane przez parametr ObjectClass dziedziczenie list ACL aktualnie wyłączone.

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

Get ADSyncObjectsWithInheritanceDisabled - SearchBase jednostki Organizacyjnej usługi Azure AD, DC = Contoso, DC = com - ObjectClass = "*"

### <a name="parameters"></a>PARAMETRY

#### <a name="-searchbase"></a>-SearchBase
SearchBase dla kwerendy LDAP, która może być elementem DistinguishedName domeny usługi AD lub nazwy FQDN

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
Klasa obiektów do wyszukiwania, który może być "*" (dla każdej klasy obiektu), "user", "group", "kontener" itd. Domyślnie ta funkcja wyszuka klasę obiektu "organizationalUnit".

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
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncbasicreadpermissions"></a>Set-ADSyncBasicReadPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zainicjuj lasu usługi Active Directory i domeny dla podstawowych uprawnień do odczytu.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set ADSyncBasicReadPermissions zapewni wymaganych uprawnień kontu synchronizacja usługi AD, takich jak:
1.
Dostęp do właściwości odczytu wszystkie atrybuty dla wszystkich obiektów podrzędnych komputera
2.
Dostęp do właściwości odczytu wszystkie atrybuty dla wszystkich obiektów podrzędnych urządzenia
3.
Dostęp do właściwości odczytu wszystkie atrybuty dla wszystkich obiektów podrzędnych foreignsecurityprincipal
5.
Dostęp do właściwości odczytu wszystkie atrybuty dla wszystkich obiektów podrzędnych użytkownika
6.
Dostęp do właściwości odczytu wszystkie atrybuty dla wszystkich obiektów podrzędnych inetorgperson
7.
Dostęp do właściwości odczytu wszystkie atrybuty dla wszystkich obiektów podrzędnych grupy
8.
Dostęp do właściwości odczytu wszystkie atrybuty dla wszystkich elementów podrzędnych obiektów kontaktu

Te uprawnienia są stosowane do wszystkich domen w lesie.
Opcjonalnie możesz podać DistinguishedName w parametrze ADobjectDN, aby ustawić te uprawnienia dla tego obiektu AD tylko (z uwzględnieniem dziedziczenia z obiektami sub).

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
Domena konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
DistinguishedName konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
DistinguishedName obiektu docelowego usługi AD, aby ustawić uprawnienia (opcjonalnie)

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
Opcjonalny parametr, aby wskazać, jeśli nie można zaktualizować kontenera AdminSDHolder z odpowiednimi uprawnieniami

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

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie jest uruchomione.

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

#### <a name="-confirm"></a>-Upewnij się
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
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncexchangehybridpermissions"></a>Set-ADSyncExchangeHybridPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zainicjuj lasu usługi Active Directory i domeny dla funkcji hybrydowego programu Exchange.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [-SkipAdminSdHolders]
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set ADSyncExchangeHybridPermissions zapewni wymaganych uprawnień kontu synchronizacja usługi AD, takich jak:
1.
Dostęp do właściwości odczytu/zapisu dla wszystkich atrybutów dla wszystkich obiektów podrzędnych użytkownika
2.
Dostęp do właściwości odczytu/zapisu dla wszystkich atrybutów dla wszystkich obiektów podrzędnych inetorgperson
3.
Dostęp do właściwości odczytu/zapisu dla wszystkich atrybutów dla wszystkich obiektów podrzędnych grupy
4.
Dostęp do właściwości odczytu/zapisu dla wszystkich atrybutów dla wszystkich elementów podrzędnych obiektów kontaktu

Te uprawnienia są stosowane do wszystkich domen w lesie.
Opcjonalnie możesz podać DistinguishedName w parametrze ADobjectDN, aby ustawić te uprawnienia dla tego obiektu AD tylko (z uwzględnieniem dziedziczenia z obiektami sub).

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
Domena konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
DistinguishedName konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
DistinguishedName obiektu docelowego usługi AD, aby ustawić uprawnienia (opcjonalnie)

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
Opcjonalny parametr, aby wskazać, jeśli nie można zaktualizować kontenera AdminSDHolder z odpowiednimi uprawnieniami

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

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie jest uruchomione.

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

#### <a name="-confirm"></a>-Upewnij się
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
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncexchangemailpublicfolderpermissions"></a>Set-ADSyncExchangeMailPublicFolderPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zainicjuj lasu usługi Active Directory i domeny dla funkcji folderu publiczne poczty programu Exchange.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String>
 -ADConnectorAccountDomain <String> [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm]
 [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set ADSyncExchangeMailPublicFolderPermissions zapewni wymaganych uprawnień kontu synchronizacja usługi AD, takich jak:
1.
Dostęp do właściwości odczytu wszystkie atrybuty dla wszystkich obiektów podrzędnych publicfolder

Te uprawnienia są stosowane do wszystkich domen w lesie.
Opcjonalnie możesz podać DistinguishedName w parametrze ADobjectDN, aby ustawić te uprawnienia dla tego obiektu AD tylko (z uwzględnieniem dziedziczenia z obiektami sub).

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
Domena konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
DistinguishedName konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
DistinguishedName obiektu docelowego usługi AD, aby ustawić uprawnienia (opcjonalnie)

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
Opcjonalny parametr, aby wskazać, jeśli nie można zaktualizować kontenera AdminSDHolder z odpowiednimi uprawnieniami

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

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie jest uruchomione.

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

#### <a name="-confirm"></a>-Upewnij się
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
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncmsdsconsistencyguidpermissions"></a>Set-ADSyncMsDsConsistencyGuidPermissions

### <a name="synopsis"></a>STRESZCZENIE
Inicjowanie usługi Active Directory lasu i domeny, mS-DS-ConsistencyGuid funkcji.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set ADSyncMsDsConsistencyGuidPermissions zapewni wymaganych uprawnień kontu synchronizacja usługi AD, takich jak:
1.
Dostęp do właściwości odczytu/zapisu atrybutu mS-DS-ConsistencyGuid dla wszystkich obiektów podrzędnych użytkownika

Te uprawnienia są stosowane do wszystkich domen w lesie.
Opcjonalnie możesz podać DistinguishedName w parametrze ADobjectDN, aby ustawić te uprawnienia dla tego obiektu AD tylko (z uwzględnieniem dziedziczenia z obiektami sub).

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
Domena konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
DistinguishedName konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
DistinguishedName obiektu docelowego usługi AD, aby ustawić uprawnienia (opcjonalnie)

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
Opcjonalny parametr, aby wskazać, jeśli nie można zaktualizować kontenera AdminSDHolder z odpowiednimi uprawnieniami

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

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie jest uruchomione.

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

#### <a name="-confirm"></a>-Upewnij się
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
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncpasswordhashsyncpermissions"></a>Set-ADSyncPasswordHashSyncPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zainicjuj lasu usługi Active Directory i domeny do synchronizacji skrótów haseł.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set ADSyncPasswordHashSyncPermissions zapewni wymaganych uprawnień kontu synchronizacja usługi AD, takich jak:
1.
Replikowanie zmian katalogów
2.
Replikowanie zmian katalogów wszystkie

Te uprawnienia są podane dla wszystkich domen w lesie.

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta usługi Active Directory, który będzie używany przez usługi Azure AD Connect Sync zarządzania obiektami w katalogu.

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
Domena konta usługi Active Directory, który będzie używany przez usługi Azure AD Connect Sync zarządzania obiektami w katalogu.

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
DistinguishedName konta usługi Active Directory, który będzie używany przez usługi Azure AD Connect Sync zarządzania obiektami w katalogu.

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

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie jest uruchomione.

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

#### <a name="-confirm"></a>-Upewnij się
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
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncpasswordwritebackpermissions"></a>Set-ADSyncPasswordWritebackPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zainicjuj lasu usługi Active Directory i domeny do zapisywania zwrotnego haseł z usługi Azure AD.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set ADSyncPasswordWritebackPermissions zapewni wymaganych uprawnień kontu synchronizacja usługi AD, takich jak:
1.
Resetuj hasło na obiekty podrzędne użytkownika
2.
Zapis dostęp do właściwości w atrybucie lockoutTime dla wszystkich obiektów podrzędnych użytkownika
3.
Zapis dostęp do właściwości w atrybucie pwdLastSet dla wszystkich obiektów podrzędnych użytkownika

Te uprawnienia są stosowane do wszystkich domen w lesie.
Opcjonalnie możesz podać DistinguishedName w parametrze ADobjectDN, aby ustawić te uprawnienia dla tego obiektu AD tylko (z uwzględnieniem dziedziczenia z obiektami sub).

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
Domena konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
DistinguishedName konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
DistinguishedName obiektu docelowego usługi AD, aby ustawić uprawnienia (opcjonalnie)

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
Opcjonalny parametr, aby wskazać, jeśli nie można zaktualizować kontenera AdminSDHolder z odpowiednimi uprawnieniami

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

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie jest uruchomione.

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

#### <a name="-confirm"></a>-Upewnij się
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
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncrestrictedpermissions"></a>Set-ADSyncRestrictedPermissions

### <a name="synopsis"></a>STRESZCZENIE
Podwyższenie poziomu uprawnień do obiektu usługi AD, w przeciwnym razie nieuwzględnione w dowolnej grupie chronionych zabezpieczeń AD.
Typowym przykładem jest automatycznie tworzone przez usługę AAD Connect konta AD Connect (MSOL).
To konto ma uprawnienia replikacji na wszystkich domen, jednak można zabezpieczenia można łatwo złamać, ponieważ nie jest chroniony.

### <a name="syntax"></a>SKŁADNIA

```
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential>
 [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set ADSyncRestrictedPermissions będzie podwyższenie poziomu uprawnień wprowadzaniem podane konto.
Obostrzenie uprawnień obejmuje następujące czynności:
1.
Wyłącz dziedziczenie dla określonego obiektu
2.
Usuń wszystkie wpisy kontroli dostępu dla określonego obiektu, z wyjątkiem ACE specyficzne dla siebie.
Chcemy zachować uprawnienia domyślne, jeśli chodzi o SAMODZIELNIE.
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
DistinguishedName konta usługi Active Directory, w której uprawnienia muszą być ściągane.
Zazwyczaj jest to konto MSOL_nnnnnnnnnn lub konta domeny niestandardowej, który jest skonfigurowany w łączniku usługi AD.

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
Poświadczenia administratora, który ma niezbędne uprawnienia, aby ograniczyć uprawnienia na koncie ADConnectorAccountDN. Zazwyczaj jest to administrator przedsiębiorstwa lub domeny. Użyj w pełni kwalifikowana nazwa domeny konta administratora, aby uniknąć niepowodzenia wyszukiwania kont.
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

#### <a name="-disablecredentialvalidation"></a>-DisableCredentialValidation
W przypadku DisableCredentialValidation funkcja nie będzie sprawdzać, jeśli poświadczenia podane — poświadczenia są prawidłowe w AD, a jeśli podane konto ma niezbędne uprawnienia, aby ograniczyć uprawnienia na koncie ADConnectorAccountDN.

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

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie jest uruchomione.

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

#### <a name="-confirm"></a>-Upewnij się
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
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="set-adsyncunifiedgroupwritebackpermissions"></a>Set-ADSyncUnifiedGroupWritebackPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zainicjuj lasu usługi Active Directory i domeny dla zapisu zwrotnego grup z usługi Azure AD.

### <a name="syntax"></a>SKŁADNIA

#### <a name="userdomain"></a>UserDomain
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String>
 [-ADobjectDN <String>] [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="distinguishedname"></a>DistinguishedName
```
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>]
 [-SkipAdminSdHolders] [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja Set ADSyncUnifiedGroupWritebackPermissions zapewni wymaganych uprawnień kontu synchronizacja usługi AD, takich jak:
1.
Ogólny odczytu/zapisu, Delete, usuwania drzewa i Create\Delete podrzędnych dla wszystkich grup typów obiektów i podobiektów

Te uprawnienia są stosowane do wszystkich domen w lesie.
Opcjonalnie możesz podać DistinguishedName w parametrze ADobjectDN, aby ustawić te uprawnienia dla tego obiektu AD tylko (z uwzględnieniem dziedziczenia z obiektami sub).
W tym przypadku ADobjectDN będzie nazwę wyróżniającą kontenera, który chcesz połączyć z funkcją GroupWriteback.

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

#### <a name="-adconnectoraccountname"></a>-ADConnectorAccountName
Nazwa konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
Domena konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
DistinguishedName konta usługi Active Directory lub Azure AD Connect Sync posłuży do zarządzania obiektami w katalogu.

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
DistinguishedName obiektu docelowego usługi AD, aby ustawić uprawnienia (opcjonalnie)

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
Opcjonalny parametr, aby wskazać, jeśli nie można zaktualizować kontenera AdminSDHolder z odpowiednimi uprawnieniami

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

#### <a name="-whatif"></a>-WhatIf
Pokazuje, co się stanie po uruchomieniu polecenia cmdlet.
Polecenie cmdlet nie jest uruchomione.

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

#### <a name="-confirm"></a>-Upewnij się
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
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="show-adsyncadobjectpermissions"></a>Show-ADSyncADObjectPermissions

### <a name="synopsis"></a>STRESZCZENIE
Zawiera uprawnienia określonego obiektu usługi AD.

### <a name="syntax"></a>SKŁADNIA

```
Show-ADSyncADObjectPermissions [-ADobjectDN] <String> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Ta funkcja zwraca wszystkie uprawnienia AD aktualnie ustawione dla danego obiektu AD podany w parametrze - ADobjectDN.
ADobjectDN musi być podana w formacie DistinguishedName.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Show-ADSyncADObjectPermissions -ADobjectDN 'OU=AzureAD,DC=Contoso,DC=com'
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adobjectdn"></a>-ADobjectDN
{{Fill ADobjectDN Description}}

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
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).
