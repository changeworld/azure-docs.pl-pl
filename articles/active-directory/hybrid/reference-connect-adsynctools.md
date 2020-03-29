---
title: 'Usługa Azure AD Connect: odwołanie programu PowerShell usługi ADSyncTools | Dokumenty firmy Microsoft'
description: Ten dokument zawiera informacje referencyjne dla modułu programu POWERShell programu ADSyncTools.psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a1b8abf15233c06e8ff9e507b315cc8a3703970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60454663"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Usługa Azure AD Connect: odwołanie programu ADSyncTools powershell
Poniższa dokumentacja zawiera informacje referencyjne dla modułu programu PowerShell programu ADSyncTools.psm1 dołączonego do usługi Azure AD Connect.

## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>STRESZCZENIE
Czyszczenie mS-Ds-ConsistencyGuid z użytkownika usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Czyszczenie wartości w mS-Ds-ConsistencyGuid dla docelowego użytkownika usługi AD

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
Użytkownik docelowy w uerce do ustawienia

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Potwierdź-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnij opis}}

### <a name="syntax"></a>SKŁADNIA

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>OPIS
{{Wypełnij opis}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Dodaj przykładowy opis tutaj }}

## <a name="connect-adsyncdatabase"></a>Połącz-AdSyncDatabase

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnij opis}}

### <a name="syntax"></a>SKŁADNIA

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
{{Wypełnij opis}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Dodaj przykładowy opis tutaj }}

### <a name="parameters"></a>PARAMETRY

#### <a name="-database"></a>-Baza danych
{{Opis bazy danych wypełniania}}

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
{{Opis wystąpienia wypełnienia}}

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
{{Wypełnij opis hasła}}

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
{{Opis serwera wypełnienia}}

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
{{Opis nazwy użytkownika wypełnienia}}

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigracja

### <a name="synopsis"></a>STRESZCZENIE
Raport Eksportuj spójnośćGuid

### <a name="syntax"></a>SKŁADNIA

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Generuje raport ConsistencyGuid na podstawie pliku CSV importu z importu-ADSyncToolsImmutableIdMigration

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
Użyj alternatywnego identyfikatora logowania (poczty)

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

#### <a name="-immutableidguid"></a>-NiezmienneIdguid
NiezmiennyJudguid

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

#### <a name="-output"></a>-Wyjście
Wyjściowa nazwa pliku dla plików CSV i LOG

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="get-adsyncsqlbrowserinstances"></a>Nieinstancje brwi Get-ADSyncSQL

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnij opis}}

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>OPIS
{{Wypełnij opis}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Dodaj przykładowy opis tutaj }}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-nazwa hosta
{{Opis nazwy hosta wypełnienia}}

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
Pobierz użytkownika z usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Zwraca obiekt USŁUGI AD DO DO: Obsługa wielu lasów

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
Użytkownik docelowy w u. w u. do ustawiania consistencyguid

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>STRESZCZENIE
Pobierz mS-Ds-ConsistencyGuid od użytkownika usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Zwraca wartość atrybutu mS-Ds-ConsistencyGuid docelowego użytkownika usługi AD w formacie GUID

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
Użytkownik docelowy w uerce do ustawienia

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>STRESZCZENIE
Pobierz ObjectGuid od użytkownika usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Zwraca wartość atrybutu ObjectGUID docelowego użytkownika usługi AD w formacie GUID

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
Użytkownik docelowy w uerce do ustawienia

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="get-adsynctoolsrunhistory"></a>Historia Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>STRESZCZENIE
Pobierz historię uruchamiania połączenia AAD

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja zwracana historia przebiegu połączenia AAD w formacie XML

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

#### <a name="-days"></a>-Dni
{{Opis dni wypełnienia}}

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorZmienił

### <a name="synopsis"></a>STRESZCZENIE
Pobierz użytkowników z SourceAnchor zmienionych błędów

### <a name="syntax"></a>SKŁADNIA

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Funkcja odpytuje historię uruchamiania AAD Connect i eksportuje wszystkich użytkowników zgłaszających błąd: "Atrybut SourceAnchor został zmieniony".

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
#Required Parameters
```

$sourcePath = Read-Host -Prompt "Enter your log file path\<with file\>name" #" Source_Path " $outputPath = Read-Host -Prompt "Enter your out file path with file name" #"\<Out_Path\>"
 
 Get-ADSyncToolsUsersSourceAnchorZmienia -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>PRZYKŁAD 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-sourcepath"></a>-sourcePath
{{Opis ścieżki źródłowej wypełnienia}}

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
{{Opis ścieżki wyjściowej wypełnienia}}

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>STRESZCZENIE
Importowanie identyfikatora niezmiennego z aad

### <a name="syntax"></a>SKŁADNIA

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>OPIS
Generuje plik ze wszystkimi zsynchronizowanymi użytkownikami usługi Azure AD zawierającymi wartość ImmutableID w wymaganiach dotyczących formatu GUID: Moduł MSOnline PowerShell

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

#### <a name="-output"></a>-Wyjście
Wyjściowy plik CSV

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
Pobierz zsynchronizowanych użytkowników z Kosza usługi Azure AD

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnij opis}}

### <a name="syntax"></a>SKŁADNIA

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
{{Wypełnij opis}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Dodaj przykładowy opis tutaj }}

### <a name="parameters"></a>PARAMETRY

#### <a name="-query"></a>-Zapytanie
{{Opis kwerendy wypełniającej}}

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
{{Wypełnij opis połączenia sqlconnection}}

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="remove-adsynctoolsexpiredcertificates"></a>Usuń-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>STRESZCZENIE
Skrypt do usuwania wygasłych certyfikatów z atrybutu UserCertificate

### <a name="syntax"></a>SKŁADNIA

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>OPIS
Ten skrypt pobiera wszystkie obiekty z docelowej jednostki organizacyjnej w domenie usługi Active Directory — filtrowane według klasy obiektu (Użytkownik/komputer) i usuwa wszystkie wygasłe certyfikaty obecne w atrybucie UserCertificate.
Domyślnie (BackupOnly mode) będzie tylko kopia zapasowa wygasłe certyfikaty do pliku i nie robić żadnych zmian w u.
Jeśli używasz -BackupOnly $false następnie wygasły certyfikat obecny w UserCertificate atrybut dla tych obiektów zostaną usunięte z usługi AD po skopiowaniu do pliku.
Kopia zapasowa każdego certyfikatu zostanie utworzona na oddzielonej nazwy pliku: ObjectClass_ObjectGUID_CertThumprint.cer Skrypt utworzy również plik dziennika w formacie CSV przedstawiający wszystkich użytkowników z certyfikatami, które są prawidłowe lub wygasłe, w tym rzeczywistą podjętą akcją (Pominięte/Wyeksportowane/Usunięte).

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Usuń-ADSyncToolsExpiredCertificates -TargetOU "OU=Użytkownicy,OU=Corp,DC=Contoso,DC=com" -ObjectClass użytkownika

#### <a name="example-2"></a>PRZYKŁAD 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Usuń-ADSyncToolsExpiredCertificates -TargetOU "OU=Komputery,OU=Corp,DC=Contoso,DC=com" -ObjectClass komputer -BackupOnly $false

### <a name="parameters"></a>PARAMETRY

#### <a name="-targetou"></a>-TargetOU
Docelowa do umadłań w poszukiwaniu obiektów usługi AD

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
BackupOnly nie usunie żadnych certyfikatów z usługi AD

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="repair-adsynctoolsautoupgradestate"></a>Naprawa-ADSyncToolsAutoUpgradeState

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

## <a name="resolve-adsynchostaddress"></a>Rozwiązanie-AdSyncHostAddress

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnij opis}}

### <a name="syntax"></a>SKŁADNIA

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>OPIS
{{Wypełnij opis}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Dodaj przykładowy opis tutaj }}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-nazwa hosta
{{Opis nazwy hosta wypełnienia}}

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

## <a name="restore-adsynctoolsexpiredcertificates"></a>Narzędzie przywracania-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>STRESZCZENIE
(DO ZROBIENIA) Przywraca atrybut Ad UserCertificate z pliku certyfikatu

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
Ustawianie mS-Ds-ConsistencyGuid dla użytkownika usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>OPIS
Ustawianie wartości w atrybucie mS-Ds-ConsistencyGuid dla docelowego użytkownika usługi AD

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
Użytkownik docelowy w u. w u. do ustawiania consistencyguid

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
ImpmutableId (tablica bajtów, identyfikator GUID, ciąg GUID lub ciąg Base64)

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>STRESZCZENIE
{{Wypełnij opis}}

### <a name="syntax"></a>SKŁADNIA

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>OPIS
{{Wypełnij opis}}

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>Przykład 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Dodaj przykładowy opis tutaj }}

### <a name="parameters"></a>PARAMETRY

#### <a name="-hostname"></a>-nazwa hosta
{{Opis nazwy hosta wypełnienia}}

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
{{Opis portu wypełnienia}}

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
Tworzy plik śledzenia z i krok importu usługi AD

### <a name="syntax"></a>SKŁADNIA

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Śledzi wszystkie kwerendy ldap importu usługi AAD Connect AD z danego punktu kontrolnego znaku wodnego AD (plik cookie partycji). Tworzy plik śledzenia '.\ADimportTrace_yyyyMMddHHmmss.log' w bieżącym folderze.

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
{{Wypełnij opis ADConnectorXML}}

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
Plik XML eksportu łącznika usługi AD

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

#### <a name="-filter"></a>-filtr
Główny katalog główny lasu

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
Typy obiektów USŁUGI \> AD do śledzenia * = wszystkie typy obiektów

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

#### <a name="-adwatermark"></a>-Znak adwodny
Jeśli jest już uruchomiony jako administrator domeny, nie ma potrzeby podawania poświadczeń usługi AD.
Ręczne wprowadzanie znaku wodnego zamiast pliku XML $ADwatermark np.

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

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

#### <a name="-context"></a>-Kontekst
Opis pomocy Param1

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

#### <a name="-filter"></a>-Filtr
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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Aktualizacja-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>STRESZCZENIE
Aktualizuje użytkowników za pomocą nowego ConsistencyGuid (ImmutableId)

### <a name="syntax"></a>SKŁADNIA

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>OPIS
Aktualizuje użytkowników o nową wartość ConsistencyGuid (ImmutableId) pobraną z raportu ConsistencyGuid Ta funkcja obsługuje przełącznik WhatIf Uwaga: Raport ConsistencyGuid musi zostać zaimportowany z tab Demiliter

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

#### <a name="-distinguishedname"></a>-DistinguishedName
Distinguishedname

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

#### <a name="-immutableidguid"></a>-NiezmienneIdguid
NiezmiennyJudguid

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

#### <a name="-action"></a>-Działanie
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

#### <a name="-output"></a>-Wyjście
Wyjściowa nazwa pliku dla plików LOG

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
