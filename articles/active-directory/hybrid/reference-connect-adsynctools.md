---
title: 'Program Azure AD Connect: Dokumentacja programu PowerShell ADSyncTools | Dokumentacja firmy Microsoft'
description: Ten dokument zawiera informacje dotyczące modułu programu ADSyncTools.psm1 PowerShell.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
origin.date: 10/19/2018
ms.date: 03/15/2019
ms.subservice: hybrid
ms.author: v-junlch
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a1b8abf15233c06e8ff9e507b315cc8a3703970
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60454663"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Program Azure AD Connect:  Dokumentacja programu PowerShell ADSyncTools
Poniższa dokumentacja zawiera informacje dotyczące modułu programu PowerShell ADSyncTools.psm1, dostępnej w programie Azure AD Connect.

## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>STRESZCZENIE
Wyczyść mS-Ds-ConsistencyGuid użytkownika usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Wyczyszczenie tej wartości w mS-Ds-ConsistencyGuid dla użytkownika docelowego AD

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Użytkownik docelowy w AD, aby ustawić

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Upewnij się, ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnij streszczenie}}

### <a name="syntax"></a>SKŁADNIA

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>OPIS
{{Wprowadź opis}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Przykład tutaj Dodaj opis}}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnij streszczenie}}

### <a name="syntax"></a>SKŁADNIA

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
{{Wprowadź opis}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Przykład tutaj Dodaj opis}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-database"></a>-Database
{{Wprowadź opis bazy danych}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Wystąpienie
{{Wprowadź opis wystąpienia}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Wprowadź opis hasła}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{Wprowadź opis serwera}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{Wprowadź opis NazwaUżytkownika}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>STRESZCZENIE
Eksportuj raport ConsistencyGuid

### <a name="syntax"></a>SKŁADNIA

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Generuje raport ConsistencyGuid na podstawie importu pliku CSV z ADSyncToolsImmutableIdMigration importu

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Użyj Identyfikatora logowania alternatywnej (poczta)

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

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
Nazwa pliku wyjściowego dla woluminu CSV i plików dziennika

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnij streszczenie}}

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>OPIS
{{Wprowadź opis}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Przykład tutaj Dodaj opis}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-hostName
{{Wprowadź nazwę hosta opis}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>STRESZCZENIE
Pobieranie użytkownika z usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Zwraca obiekt AD, aby wykonać: Obsługa lasu Multi

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Użytkownik docelowy w AD, aby ustawić ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>STRESZCZENIE
Uzyskiwanie mS-Ds-ConsistencyGuid użytkownika usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Zwraca wartość atrybutu mS-Ds-ConsistencyGuid docelowego AD użytkownika w formacie identyfikatora GUID

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Użytkownik docelowy w AD, aby ustawić

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>STRESZCZENIE
Pobierz wartość ObjectGuid z użytkownika usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Zwraca wartość atrybutu ObjectGUID docelowego AD użytkownika w formacie identyfikatora GUID

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Użytkownik docelowy w AD, aby ustawić

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>STRESZCZENIE
Pobierz AAD Connect historii uruchamiania

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja, która zwraca AAD Connect historii uruchamiania w formacie XML

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-days"></a>— Dni
{{Wypełnij dniami opis}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>STRESZCZENIE
Zyskaj użytkowników z błędami SourceAnchor zmienione

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja zapytania usługi AAD Connect historii uruchamiania i eksportuje wszystkich użytkowników zgłaszających błąd: "Atrybut SourceAnchor został zmieniony."

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
#Required Parameters
```

$sourcePath = Read-Host - monitu o "Wprowadź ścieżkę do pliku dziennika usługi z nazwą pliku" #"\<Source_Path\>" $outputPath = Read-Host-monitu "Wprowadź swoje poza ścieżka pliku o nazwie pliku" #"\<Out_Path\>"
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-sourcepath"></a>-sourcePath
{{Wypełnienia Ścieżka_źródłowa opis}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{Wypełnienia outputPath opis}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>STRESZCZENIE
ImmutableID importowania z usługi AAD

### <a name="syntax"></a>SKŁADNIA

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>OPIS
Generuje plik wszystkim użytkownikom usługi Azure AD Synchronized, zawierającego wartość ImmutableID w formacie identyfikatora GUID wymagania: Moduł programu PowerShell MSOnline

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-output"></a>-Output
Dane wyjściowe pliku CSV

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

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Do zsynchronizowania Kosz użytkowników z usługi Azure AD

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

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnij streszczenie}}

### <a name="syntax"></a>SKŁADNIA

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
{{Wprowadź opis}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Przykład tutaj Dodaj opis}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-query"></a>-Query
{{Wprowadź opis kwerendy}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{Fill SqlConnection Description}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>STRESZCZENIE
Skrypt, aby usunąć wygasłe certyfikaty z atrybutu UserCertificate

### <a name="syntax"></a>SKŁADNIA

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>OPIS
Ten skrypt pobiera wszystkie obiekty z docelowej jednostki organizacyjnej w domenie usługi Active Directory — filtrowane według klasy obiektu (komputer/użytkownika) i usuwa wszystkie certyfikaty wygasłe obecny w atrybucie certyfikatu użytkownika.
Domyślnie (tryb BackupOnly) tylko wykona kopię zapasową wygasłe certyfikaty, które do pliku, a nie wszystkie zmiany w AD.
Jeśli używasz - BackupOnly $false, wówczas każdy obecny w atrybucie UserCertificate dla tych obiektów wygasł certyfikat zostanie usunięty z usługi AD, po której są kopiowane do pliku.
Każdy certyfikat będzie można utworzyć kopie zapasowe rozdzielonych nazwa pliku: ObjectClass_ObjectGUID_CertThumprint.cer skrypt utworzy plik dziennika w formacie CSV Pokazywanie wszystkich użytkowników z certyfikatami, które są albo nieprawidłowa lub wygasła, włączając rzeczywiste akcję podejmowaną (pomijane wyeksportowane/usunięte).

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

{Remove ADSyncToolsExpiredCertificates - TargetOU "OU = jednostka Organizacyjna użytkowników = Corp, DC = Contoso, DC = com" - ObjectClass użytkownika

#### <a name="example-2"></a>PRZYKŁAD 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove ADSyncToolsExpiredCertificates-{TargetOU "OU = Computers, OU = Corp, DC = Contoso, DC = com" komputera - ObjectClass - BackupOnly $false

### <a name="parameters"></a>PARAMETRY

#### <a name="-targetou"></a>-TargetOU
Docelowej jednostki Organizacyjnej do wyszukiwania dla obiektów usługi AD

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

#### <a name="-backuponly"></a>-BackupOnly
BackupOnly nie usunie wszystkie certyfikaty z usługi AD

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Filtr klasy obiektu

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>STRESZCZENIE
Krótki opis

### <a name="syntax"></a>SKŁADNIA

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>OPIS
Długi opis

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnij streszczenie}}

### <a name="syntax"></a>SKŁADNIA

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>OPIS
{{Wprowadź opis}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Przykład tutaj Dodaj opis}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-hostName
{{Wprowadź nazwę hosta opis}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>STRESZCZENIE
(ABY WYKONAĆ) Przywraca atrybutu AD UserCertificate z pliku certyfikatu

### <a name="syntax"></a>SKŁADNIA

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>OPIS
Długi opis

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>STRESZCZENIE
Nastavit mS-Ds-ConsistencyGuid użytkownika usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Ustaw wartość atrybutu mS-Ds-ConsistencyGuid dla elementu docelowego użytkownika usługi AD

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-user"></a>-User
Użytkownik docelowy w AD, aby ustawić ConsistencyGuid

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Wartość
Wartość ImmutableId (tablicę bajtów, GUID, GUID lub ciągu Base64)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnij streszczenie}}

### <a name="syntax"></a>SKŁADNIA

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>OPIS
{{Wprowadź opis}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Przykład tutaj Dodaj opis}}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-hostName
{{Wprowadź nazwę hosta opis}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-port
{{Wprowadź opis portu}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>STRESZCZENIE
Tworzy plik śledzenia z i kroku importu usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Ślady wszystkich zapytań ldap AAD Connect AD importu Uruchom z danym AD znaku wodnego punkt kontrolny (plik cookie partycji). Tworzy plik śledzenia ".\ADimportTrace_yyyyMMddHHmmss.log" w bieżącym folderze.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Wprowadź opis ADConnectorXML}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-dc
Plik XML wyeksportować łącznika AD

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
Docelowy kontroler domeny

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-filtru
Forest Root DN

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
Typy obiektów usługi AD do śledzenia \> * = wszystkie typy obiektów

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

#### <a name="-adwatermark"></a>-ADwatermark
Jeśli już uruchomione jako Administrator domeny ma nie trzeba podawać poświadczeń usługi AD.
Ręczne wprowadzanie znaku wodnego, zamiast XML pliku, np. $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>STRESZCZENIE
Krótki opis

### <a name="syntax"></a>SKŁADNIA

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>OPIS
Długi opis

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-context"></a>-Kontekstu
Opis elementu Param1 pomocy

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

#### <a name="-server"></a>-Server
Opis pomocy Param2

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Opis pomocy Param2

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filter
Opis pomocy Param2

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Typowe parametry
To polecenie cmdlet obsługuje typowe parametry: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction i -WarningVariable.
Aby uzyskać więcej informacji, zobacz informacje o parametrach wspólnych (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>STRESZCZENIE
Aktualizuje użytkowników przy użyciu nowego ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SKŁADNIA

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Aktualizuje użytkowników przy użyciu nowej wartości ConsistencyGuid (ImmutableId) wykonane z tego raportu ConsistencyGuid obsługuje funkcję przełącznika WhatIf Uwaga: Raport ConsistencyGuid muszą zostać zaimportowane z Demiliter kartę

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>PRZYKŁAD 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-distinguishedname"></a>-DistinguishedName.
DistinguishedName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Action
Akcja

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
Nazwa pliku wyjściowego dla plików dziennika

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
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

<!-- Update_Description: wording update -->