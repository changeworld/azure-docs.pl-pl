---
title: 'Usługa Azure AD Connect: odwołanie do programu PowerShell adconnectivitytools | Dokumenty firmy Microsoft'
description: Ten dokument zawiera informacje referencyjne dla modułu programu ADConnectivityTools.psm1 PowerShell.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6b90ff82601acca1249c7d8c353944e39e89f95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66473788"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Usługa Azure AD Connect: odwołanie do programu PowerShell adconnectivitytools

Poniższa dokumentacja zawiera informacje referencyjne dla modułu programu AdConnectivityTools.psm1 PowerShell, który jest dołączony do usługi Azure AD Connect.

## <a name="confirm-dnsconnectivity"></a>Potwierdź-DnsConnectivity

### <a name="synopsis"></a>STRESZCZENIE

Wykrywa lokalne problemy z systemem Dns.

### <a name="syntax"></a>SKŁADNIA

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Uruchamia lokalne testy łączności dns.
Aby skonfigurować łącznik usługi Active Directory, użytkownik musi mieć zarówno rozpoznawanie nazw dla lasu, z jakim próbuje się połączyć, jak i na kontrolerach domeny skojarzonych z tym lasem.

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

#### <a name="-forest"></a>-Las

Określa nazwę lasu, na który chcesz przetestować.

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

#### <a name="-dcs"></a>-Kontrolery domeny

Określ kontrolery domeny, z się przetestować.

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

Zwraca wynik tej diagnozy w postaci PSObject.
Nie jest to konieczne podczas ręcznej interakcji z tym narzędziem.

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="confirm-forestexists"></a>Potwierdź-ForestExists

### <a name="synopsis"></a>STRESZCZENIE

Określa, czy istnieje określony las.

### <a name="syntax"></a>SKŁADNIA

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>OPIS

Wysyła kwerendy do serwera DNS dla adresów IP skojarzonych z lasem.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Las

Określa nazwę lasu, na który chcesz przetestować.

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="confirm-functionallevel"></a>Potwierdź-FunkcjonalnyPoziom

### <a name="synopsis"></a>STRESZCZENIE

Weryfikuje poziom funkcjonalności lasu AD.

### <a name="syntax"></a>SKŁADNIA

#### <a name="samaccount"></a>Konto SamAccount

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>Leśnafqdn

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Sprawdza, czy poziom funkcjonalności lasu usługi AD jest równy lub większy niż dana minadforestversion (WindowsServer2003).
Można zażądać konta (domena\nazwa użytkownika) i hasła.

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

#### <a name="-forest"></a>-Las

Las docelowy.
Wartością domyślną jest Las aktualnie zalogowanego użytkownika.

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

Docelowy obiekt ForestfQDN.

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

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, zamiast żądać niestandardowych poświadczeń od użytkownika.

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="confirm-networkconnectivity"></a>Potwierdzanie połączeń sieciowych

### <a name="synopsis"></a>STRESZCZENIE

Wykrywa problemy z łącznością sieci lokalnej.

### <a name="syntax"></a>SKŁADNIA

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Uruchamia testy łączności sieci lokalnej.

W przypadku testów sieci lokalnej usługa AAD Connect musi mieć możliwość komunikowania się z nazwanymi kontrolerami domeny na portach 53 (DNS), 88 (Kerberos) i 389 (LDAP) Większość organizacji uruchamia system DNS na kontrolerach domeny, dlatego ten test jest obecnie zintegrowany.
Port 53 należy pominąć, jeśli określono inny serwer DNS.

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

#### <a name="-dcs"></a>-Kontrolery domeny

Określ kontrolery domeny, z się przetestować.

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

Jeśli użytkownik nie korzysta z usług DNS dostarczanych przez witrynę USŁUGI AD / kontroler domeny logowania, może chcieć pominąć sprawdzanie portu 53.
Użytkownik musi nadal być w stanie rozpoznać _.ldap._tcp. \<forestfqdn\> w celu pomyślnego pomyślnego wykonania konfiguracji łącznika usługi Active Directory.

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

Zwraca wynik tej diagnozy w postaci PSObject.
Nie jest to konieczne podczas ręcznej interakcji z tym narzędziem.

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="confirm-targetsarereachable"></a>Potwierdzanie celówReachable

### <a name="synopsis"></a>STRESZCZENIE

Określa, czy określony las i skojarzone z nim kontrolery domeny są osiągalne.

### <a name="syntax"></a>SKŁADNIA

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>OPIS

Uruchamia testy "ping" (czy komputer może dotrzeć do komputera docelowego za pośrednictwem sieci i/lub Internetu)

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

#### <a name="-forest"></a>-Las

Określa nazwę lasu, na który chcesz przetestować.

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

#### <a name="-dcs"></a>-Kontrolery domeny

Określ kontrolery domeny, z się przetestować.

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="confirm-validdomains"></a>Potwierdzanie-Prawidłowośćdomy

### <a name="synopsis"></a>STRESZCZENIE

Sprawdzanie, czy domeny w uzyskanej nazwy FQDN lasu są osiągalne

### <a name="syntax"></a>SKŁADNIA

#### <a name="samaccount"></a>Konto SamAccount

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>Leśnafqdn

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Sprawdź, czy wszystkie domeny w uzyskanej nazwy FQDN lasu są osiągalne, próbując pobrać DomainGuid i DomainDN.
Można zażądać konta (domena\nazwa użytkownika) i hasła.

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

#### <a name="-forest"></a>-Las

Las docelowy.

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

Docelowy obiekt ForestfQDN.

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

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, zamiast żądać niestandardowych poświadczeń od użytkownika.

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="confirm-validenterpriseadmincredentials"></a>Potwierdź-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>STRESZCZENIE

Sprawdza, czy użytkownik ma poświadczenia administratora przedsiębiorstwa.

### <a name="syntax"></a>SKŁADNIA

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Wyszukiwanie, jeśli podany użytkownik ma poświadczenia administratora przedsiębiorstwa.
Można zażądać konta (domena\nazwa użytkownika) i hasła.

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

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, zamiast żądać niestandardowych poświadczeń od użytkownika.

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>STRESZCZENIE

Pobiera domainfqdn z połączenia konta i hasła.

### <a name="syntax"></a>SKŁADNIA

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Próbuje uzyskać obiekt domainFQDN z podanych poświadczeń.
Jeśli domainFQDN jest prawidłowy, DomainFQDNName lub RootDomainName zostaną zwrócone, w zależności od wyboru użytkownika.
Można zażądać konta (domena\nazwa użytkownika) i hasła.

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

Żądany rodzaj danych, które zostaną pobrane.
Obecnie ograniczone do "DomainFQDNName" lub "RootDomainName".

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

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, zamiast żądać niestandardowych poświadczeń od użytkownika.

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

Parametr pomocniczy używany przez funkcję Start-NetworkConnectivityDiagnosisTools

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>STRESZCZENIE

Pobiera forestfqdn z kombinacji konta i hasła.

### <a name="syntax"></a>SKŁADNIA

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Próby uzyskania ForestFQDN z podanych poświadczeń.
Można zażądać konta (domena\nazwa użytkownika) i hasła.

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

#### <a name="-forest"></a>-Las

Las docelowy. Wartość domyślna to domena aktualnie zalogowanego użytkownika.

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

Funkcja użyje poświadczeń użytkownika, który jest aktualnie zalogowany na komputerze, zamiast żądać niestandardowych poświadczeń od użytkownika.

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>STRESZCZENIE

Funkcja główna.

### <a name="syntax"></a>SKŁADNIA

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>OPIS

Uruchamia wszystkie dostępne mechanizmy, które weryfikują poświadczenia usługi AD są prawidłowe.

### <a name="examples"></a>PRZYKŁADY

#### <a name="example-1"></a>PRZYKŁAD 1

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMETRY

#### <a name="-forest"></a>-Las

Las docelowy.

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

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorKontat

W przypadku instalacji niestandardowych: Flaga, która jest $True, jeśli użytkownik wybrał "Utwórz nowe konto usługi AD" w oknie Konto lasu usługi AD kreatora usługi AADConnect.
$False, jeśli użytkownik wybierze opcję "Użyj istniejącego konta USŁUGI AD".
W przypadku instalacji ekspresowych: Wartość tej zmiennej musi być $True dla instalacji ekspresowych.

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

Parametr, który wstępnie wypełnia pole Nazwa użytkownika, gdy wymagane są poświadczenia użytkownika.

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>STRESZCZENIE

Główna funkcja testów łączności sieciowej.

### <a name="syntax"></a>SKŁADNIA

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>OPIS

Uruchamia testy łączności sieci lokalnej.

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

#### <a name="-forest"></a>-Las

Określa nazwę lasu do przetestowania.

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

#### <a name="-credentials"></a>-Poświadczenia

Nazwa użytkownika i hasło użytkownika, który jest uruchomiony test.
Wymaga tego samego poziomu uprawnień, który jest wymagany do uruchomienia kreatora Azure AD Connect.

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

Określa lokalizację pliku dziennika, który będzie zawierał dane wyjściowe tej funkcji.

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

#### <a name="-dcs"></a>-Kontrolery domeny

Określ kontrolery domeny, z się przetestować.

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

Flaga, która umożliwia wyświetlanie komunikatu o celu tej funkcji.

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

Zwraca wynik tej diagnozy w postaci PSObject.
Nie jest konieczne określenie podczas ręcznej interakcji z tym narzędziem.

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

Wskazuje, czy poświadczenia wpisane przez użytkownika są prawidłowe.
Nie jest konieczne określenie podczas ręcznej interakcji z tym narzędziem.

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
Aby uzyskać więcej informacji, zobaczhttps://go.microsoft.com/fwlink/?LinkID=113216)about_CommonParameters ( .
