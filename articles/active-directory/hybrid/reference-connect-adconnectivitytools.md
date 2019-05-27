---
title: 'Program Azure AD Connect: Dokumentacja programu PowerShell ADConnectivityTools | Dokumentacja firmy Microsoft'
description: Ten dokument zawiera informacje dotyczące modułu programu ADConnectivityTools.psm1 PowerShell.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1084a3e546a234739fbede46612dce96537c7d4a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65950637"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Program Azure AD Connect:  Dokumentacja programu PowerShell ADConnectivityTools

Poniższa dokumentacja zawiera informacje dotyczące modułu programu PowerShell ADConnectivityTools.psm1, dostępnej w programie Azure AD Connect.

## <a name="confirm-dnsconnectivity"></a>Confirm-DnsConnectivity

### <a name="synopsis"></a>SYNOPSIS

Wykrywa lokalne problemy z usługą Dns.

### <a name="syntax"></a>SYNTAX

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Uruchamia testy lokalnych łączność Dns.
Aby skonfigurować łącznik usługi Active Directory, użytkownik musi mieć zarówno rozpoznawania nazw w lesie, próbuje połączyć się także, jak kontrolery domeny dla tego lasu.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Forest

Określa nazwę lasu na potrzeby testowania wstępnego.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-Kontrolerów domeny

Określ kontrolerów domeny na potrzeby testowania wstępnego.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Zwraca wynik tej diagnozy w formie PSObject.
Nie są niezbędne podczas ręcznego interakcji za pomocą tego narzędzia.

```yml
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

## <a name="confirm-forestexists"></a>Upewnij się, ForestExists

### <a name="synopsis"></a>SYNOPSIS

Określa, czy istnieje określonego lasu.

### <a name="syntax"></a>SYNTAX

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>OPIS

Wysyła zapytanie do serwera DNS dla adresów IP skojarzone z lasem.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Forest

Określa nazwę lasu na potrzeby testowania wstępnego.

```yml
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

## <a name="confirm-functionallevel"></a>Confirm-FunctionalLevel

### <a name="synopsis"></a>SYNOPSIS

Sprawdza poziom funkcjonalności lasu usługi AD.

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Sprawdza, czy poziom funkcjonalności lasu usługi AD jest równy lub większy niż danego MinAdForestVersion (WindowsServer2003).
Mogą być wymagane konto (DOMENA\nazwa_użytkownika) i hasło.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>PRZYKŁAD 3

```powershell
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Forest

Wybierz las.
Wartość domyślna to lasu aktualnie zalogowanego użytkownika.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

Docelowy obiekt ForestFQDN.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, a nie żądanie niestandardowych poświadczeń użytkownika.

```yml
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

## <a name="confirm-networkconnectivity"></a>Confirm-NetworkConnectivity

### <a name="synopsis"></a>SYNOPSIS

Wykrywa problemy z łącznością w sieci lokalnej.

### <a name="syntax"></a>SYNTAX

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Uruchamia testy łączność w sieci lokalnej.

Dla lokalnych testów sieci program AAD Connect musi mieć możliwość komunikowania się z kontrolerami domeny o nazwie na portach 53 (DNS), 88 (Kerberos) i 389 (LDAP), serwer DNS w większości organizacji jest uruchamiany na swoich kontrolerach domeny, dlatego ten test jest obecnie zintegrowany.
Port 53 należy pominąć, jeśli określono innego serwera DNS.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-dcs"></a>-Kontrolerów domeny

Określ kontrolerów domeny na potrzeby testowania wstępnego.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort

Jeśli użytkownik nie korzysta z usług DNS udostępnianych przez witrynę AD / kontrolera domeny logowania, a następnie ich może chcesz pominąć sprawdzanie, czy port 53.
Użytkownik musi być nadal w stanie rozpoznać _.ldap._tcp. \<forestfqdn\> w kolejności dla konfiguracji łącznika usługi Active Directory została wykonana pomyślnie.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Zwraca wynik tej diagnozy w formie PSObject.
Nie są niezbędne podczas ręcznego interakcji za pomocą tego narzędzia.

```yml
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

## <a name="confirm-targetsarereachable"></a>Upewnij się, TargetsAreReachable

### <a name="synopsis"></a>SYNOPSIS

Określa, czy określonego lasu i jego skojarzone kontrolery domeny są dostępne.

### <a name="syntax"></a>SYNTAX

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>OPIS

Przebiegi "polecenie ping" testów (czy komputer może nawiązać połączenie na komputerze docelowym za pośrednictwem sieci i/lub Internetu)

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Forest

Określa nazwę lasu na potrzeby testowania wstępnego.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-Kontrolerów domeny

Określ kontrolerów domeny na potrzeby testowania wstępnego.

```yml
Type: Array
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

## <a name="confirm-validdomains"></a>Confirm-ValidDomains

### <a name="synopsis"></a>SYNOPSIS

Sprawdź, czy domen w uzyskanej FQDN lasu są dostępne

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Sprawdź, czy są wszystkie domeny w uzyskanej FQDN lasu osiągalny przez próby pobrania DomainGuid i DomainDN.
Mogą być wymagane konto (DOMENA\nazwa_użytkownika) i hasło.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>PRZYKŁAD 3

```powershell
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Forest

Wybierz las.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

Docelowy obiekt ForestFQDN.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, a nie żądanie niestandardowych poświadczeń użytkownika.

```yml
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

## <a name="confirm-validenterpriseadmincredentials"></a>Confirm-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>SYNOPSIS

Sprawdza, czy użytkownik ma poświadczenia administratora przedsiębiorstwa.

### <a name="syntax"></a>SYNTAX

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Wyszukuje Jeśli pod warunkiem, że użytkownik ma poświadczenia administratora przedsiębiorstwa.
Mogą być wymagane konto (DOMENA\nazwa_użytkownika) i hasło.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, a nie żądanie niestandardowych poświadczeń użytkownika.

```yml
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

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>SYNOPSIS

Pobiera DomainFQDN poza kombinację konta i hasła.

### <a name="syntax"></a>SYNTAX

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Próbuje uzyskać obiekt domainFQDN poza podanych poświadczeń.
Jeśli domainFQDN jest prawidłowy, DomainFQDNName lub RootDomainName zostaną zwrócone, w zależności od opcji wybranej przez użytkownika.
Mogą być wymagane konto (DOMENA\nazwa_użytkownika) i hasło.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType

Żądana rodzaju te dane, które mają zostać pobrane.
Obecnie są ograniczone do "DomainFQDNName" lub "RootDomainName".

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, a nie żądanie niestandardowych poświadczeń użytkownika.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError

Parametr pomocnicze używane przez funkcję Start NetworkConnectivityDiagnosisTools

```yml
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

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>SYNOPSIS

Pobiera ForestFQDN poza kombinację konta i hasła.

### <a name="syntax"></a>SYNTAX

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Próbuje uzyskać ForestFQDN poza podanych poświadczeń.
Mogą być wymagane konto (DOMENA\nazwa_użytkownika) i hasło.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Forest

Wybierz las. Wartość domyślna to domena aktualnie zalogowanego użytkownika.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, a nie żądanie niestandardowych poświadczeń użytkownika.

```yml
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

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>SYNOPSIS

Funkcja main.

### <a name="syntax"></a>SYNTAX

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>OPIS

Przebiegi wszystkich dostępnych mechanizmów, które zweryfikowania poświadczeń AD są prawidłowe.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Forest

Wybierz las.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount

W przypadku instalacji niestandardowe: Flaga, która jest $True, jeśli użytkownik wybrał "Utwórz nowe konto usługi AD" w oknie konto lasu usługi AD kreatora AADConnect firmy.
$False Jeśli użytkownik wybrał "Użyj istniejącego konta usługi AD".
W przypadku instalacji ekspresowej: Wartość tej zmiennej musi być $True dla instalacji ekspresowej.

```yml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName

Parametr, który wstępnie wypełnia pole nazwy użytkownika, gdy wymagane są poświadczenia użytkownika.

```yml
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

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>SYNOPSIS

Testy główną funkcję dla łączności sieciowej.

### <a name="syntax"></a>SYNTAX

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Uruchamia testy łączność w sieci lokalnej.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>PRZYKŁAD 2

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Forest

Określa nazwę lasu na potrzeby testowania wstępnego.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credentials"></a>-Poświadczeń

Nazwa użytkownika i hasło użytkownika, który uruchamia test.
Wymaga to ten sam poziom uprawnień, która jest wymagana do uruchamiania usługi Azure AD Kreatora programu Connect.

```yml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-logfilelocation"></a>-LogFileLocation

Określa lokalizację pliku dziennika, który będzie zawierać danymi wyjściowymi tej funkcji.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-Kontrolerów domeny

Określ kontrolerów domeny na potrzeby testowania wstępnego.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-DisplayInformativeMessage

Flaga, która umożliwia wyświetlenie komunikatu o celem tej funkcji.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Zwraca wynik tej diagnozy w formie PSObject.
Nie trzeba określać podczas ręcznego interakcji za pomocą tego narzędzia.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-validcredentials"></a>-ValidCredentials

Wskazuje, czy poświadczenia, o których wpisany przez użytkownika są prawidłowe.
Nie trzeba określać podczas ręcznego interakcji za pomocą tego narzędzia.

```yml
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
