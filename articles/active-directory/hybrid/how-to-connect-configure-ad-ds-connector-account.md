---
title: 'Program Azure AD Connect: Skonfiguruj uprawnienia dla konta usługi AD DS łącznika | Dokumentacja firmy Microsoft'
description: Ten dokument zawiera szczegółowe informacje dotyczące konfigurowania konta łącznik usługi AD DS za pomocą nowego modułu ADSyncConfig PowerShell
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6911b19c680c2fdb8c372347c4dd0fca60bb0e0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245523"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Program Azure AD Connect: Skonfiguruj uprawnienia dla konta usługi AD DS łącznika 

Moduł programu PowerShell o nazwie [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) wprowadzono w systemie kompilacji 1.1.880.0 (wydanej w sierpniu 2018 r.), który zawiera zbiór poleceń cmdlet ułatwiają konfigurowanie odpowiednich uprawnień usługi Active Directory dla usługi Azure AD Połącz z wdrożenia. 

## <a name="overview"></a>Omówienie 
Następujące polecenia cmdlet programu PowerShell może służyć do ustawienia uprawnień usługi Active Directory, konta usługi AD DS łącznika dla każdej funkcji, którą wybrano do włączenia w programie Azure AD Connect. Aby uniknąć problemów, przygotuj uprawnień usługi Active Directory z wyprzedzeniem zawsze wtedy, gdy chcesz zainstalować program Azure AD Connect przy użyciu konta domeny niestandardowej, aby nawiązać połączenie z lasem usługi. Ten moduł ADSyncConfig można również skonfigurować uprawnienia, po wdrożeniu usługi Azure AD Connect.

![](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Dla usługi Azure AD Connect instalacji programu Express, automatycznie generowanych (MSOL_nnnnnnnnnn) utworzono konto w usłudze Active Directory wszystkie niezbędne uprawnienia, więc nie trzeba używać tego modułu ADSyncConfig, chyba że został zablokowany uprawnień dziedziczenie w jednostkach organizacyjnych lub w określonych obiektach usługi Active Directory, które chcesz zsynchronizować z usługą Azure AD. 
 
### <a name="permissions-summary"></a>Podsumowanie uprawnień 
Poniższa tabela zawiera podsumowanie uprawnień wymaganych do obiektów usługi AD: 

| Cecha | Uprawnienia |
| --- | --- |
| funkcja MS-DS-ConsistencyGuid |Uprawnienia do zapisu do atrybutu ms-DS-ConsistencyGuid udokumentowane w artykule [pojęć dotyczących projektowania — przy użyciu ms-DS-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synchronizacja skrótów haseł |<li>Replikuj zmiany katalogu</li>  <li>Replikacja katalogu zmienia wszystkie |
| Wdrożenie hybrydowe programu Exchange |Uprawnienia do zapisu do atrybutów w [zapisywania zwrotnego hybrydowego programu Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) dla użytkowników, grup i kontakty. |
| Folder publiczne poczty programu Exchange |Odczyt atrybutów udokumentowane w artykule [folderu publicznego poczty Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) dla folderów publicznych. | 
| Zapisywanie zwrotne haseł |Uprawnienia do zapisu do atrybutów w [wprowadzenie do zarządzania hasłami](../authentication/howto-sspr-writeback.md) dla użytkowników. |
| Zapisywanie zwrotne urządzeń |Uprawnienia do zapisu do urządzenia obiektów i kontenerów, które opisano w [zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md). |
| Zapisywanie zwrotne grup |Przeczytaj, tworzenia, aktualizowania lub usuwania grupy obiektów zsynchronizowane **grup usługi Office 365**.  Aby uzyskać więcej informacji, zobacz [zapisu zwrotnego grup](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>Za pomocą modułu programu ADSyncConfig PowerShell 
Moduł ADSyncConfig wymaga [zdalnego serwera Administracja narzędzia (RSAT) dla usług AD DS](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) ponieważ zależy od modułu programu PowerShell usługi AD DS i narzędzia. Aby zainstalować narzędzia administracji zdalnej serwera dla usług AD DS, Otwórz okno programu Windows PowerShell z "Uruchom jako Administrator" i wykonaj: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Konfigurowanie](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Możesz również skopiować plik **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** do poziomu kontrolera domeny, która ma już narzędzia administracji zdalnej serwera dla usług AD DS zainstalowane i używaj tego modułu programu PowerShell w tym miejscu.

Aby rozpocząć korzystanie z ADSyncConfig musisz załadować moduł w oknie programu Windows PowerShell: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Aby sprawdzić wszystkie poleceń cmdlet zawartych w tym module, który można wpisać:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Zaznacz](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Każde polecenie cmdlet ma takie same parametry wejściowe konta łącznika usługi AD DS i przełącznik AdminSDHolder. Aby określić konto łącznika usługi AD DS, można dostarczyć nazwę konta i domeny lub tylko konta wyróżniającą nazwę (DN)

np.:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Lub;

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Upewnij się zastąpić `<ADAccountName>`, `<ADDomainName>` i `<ADAccountDN>` odpowiednimi wartościami dla danego środowiska.

W przypadku, gdy nie chcesz zmodyfikować uprawnienia do kontenera AdminSDHolder, należy użyć przełącznika `-SkipAdminSdHolders`. 

Domyślnie wszystkie zestaw uprawnień poleceń cmdlet programu spróbuje ustawić uprawnienia usług AD DS w katalogu głównym każdej domeny w lesie, co oznacza, że użytkownik uruchamiający sesji programu PowerShell wymaga uprawnień administratora domeny w każdej domenie w lesie.  Ze względu na to wymaganie zaleca się używać Administrator przedsiębiorstwa z katalogu głównego lasu. Jeśli wdrożenie usługi Azure AD Connect ma wiele łączników usługi AD DS, będzie wymagane do uruchomienia tego samego polecenia cmdlet w każdym lesie, który ma łącznika usługi AD DS. 

Można również ustawić uprawnienia dla określonego obiektu jednostki Organizacyjnej lub usług AD DS, przy użyciu parametru `-ADobjectDN` następuje nazwa Wyróżniająca obiektu docelowego gdzie mają zostać ustawione uprawnienia. Korzystając z obiektu docelowego ADobjectDN, polecenia cmdlet ustawi uprawnienia dla tego obiektu, tylko, a nie na katalog główny domeny lub AdminSDHolder kontenera. Ten parametr może być przydatne, gdy masz niektórych jednostek organizacyjnych lub usług AD DS obiekty, że dziedziczenie uprawnień wyłączono (zobacz lokalizowania usługi AD DS obiekty za pomocą dziedziczenia uprawnień wyłączone) 

Są wyjątki od tych wspólnych parametrów `Set-ADSyncRestrictedPermissions` polecenia cmdlet, który jest używany, aby ustawić uprawnienia dla konta łącznika usługi AD DS, a `Set-ADSyncPasswordHashSyncPermissions` polecenia cmdlet, ponieważ w katalogu głównym domeny, dlatego tylko ustawiono uprawnienia wymagane do synchronizacji skrótów haseł to polecenie cmdlet nie obejmuje `-ObjectDN` lub `-SkipAdminSdHolders` parametrów.

### <a name="determine-your-ad-ds-connector-account"></a>Określić łącznika usługi AD DS konto 
W przypadku, gdy program Azure AD Connect jest już zainstalowana, i chcesz sprawdzić, co to jest konta łącznika usługi AD DS, obecnie w użyciu przy użyciu usługi Azure AD Connect, można wykonać polecenia cmdlet: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Znajdź obiekty usług AD DS za pomocą dziedziczenia uprawnień wyłączone 
W przypadku, gdy chcesz sprawdzić, czy dowolny obiekt usług AD DS za pomocą dziedziczenia uprawnień wyłączone, można uruchomić: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Domyślnie to polecenie cmdlet tylko wygląd dla jednostek organizacyjnych z dziedziczeniem wyłączone, ale można określić innych klas obiektów usług AD DS w `-ObjectClass` parametr lub użyj "*" dla wszystkich obiektów klasy, w następujący sposób: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Wyświetlanie uprawnień obiektu w usługach AD DS 
Poniższe polecenie cmdlet służy do wyświetlania listy uprawnień aktualnie ustawiona na obiekt usługi Active Directory, podając jego DistinguishedName: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Skonfiguruj uprawnienia dla konta usługi AD DS łącznika 
 
### <a name="configure-basic-read-only-permissions"></a>Konfiguruj podstawowe uprawnienia tylko do odczytu 
Aby ustawić podstawowe uprawnienia tylko do odczytu dla konta usługi AD DS łącznika, bez korzystania z dowolnej funkcji Azure AD Connect, uruchom polecenie: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Lub; 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


To polecenie cmdlet będzie ustawić następujące uprawnienia: 
 

|Type |Name (Nazwa) |Dostęp |Dotyczy| 
|-----|-----|-----|-----|
|Zezwalaj |Łącznik usługi AD DS Account |Odczyt wszystkich właściwości |Obiekty zależne urządzeń| 
|Zezwalaj |Łącznik usługi AD DS Account|Odczyt wszystkich właściwości |Obiekty zależne InetOrgPerson| 
|Zezwalaj |Łącznik usługi AD DS Account |Odczyt wszystkich właściwości |Podrzędne obiekty komputerów| 
|Zezwalaj |Łącznik usługi AD DS Account |Odczyt wszystkich właściwości |Obiekty zależne foreignSecurityPrincipal| 
|Zezwalaj |Łącznik usługi AD DS Account |Odczyt wszystkich właściwości |Obiekty grupy elementów podrzędnych| 
|Zezwalaj |Łącznik usługi AD DS Account |Odczyt wszystkich właściwości |Obiekty zależne użytkownika| 
|Zezwalaj |Łącznik usługi AD DS Account |Odczyt wszystkich właściwości |Obiekty zależne kontaktu| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Skonfiguruj uprawnienia MS-DS-spójności — identyfikator Guid 
Aby ustawić uprawnienia dla konta usługi AD DS łącznika, w przypadku używania atrybutu ms-Ds-spójności — identyfikator Guid jako zakotwiczenie źródła (zwane również "Let platforma Azure zarządzała zakotwiczeniem źródła dla mnie" opcja), uruchom polecenie: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

Lub; 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

To polecenie cmdlet będzie ustawić następujące uprawnienia: 

|Type |Name (Nazwa) |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwalaj|Łącznik usługi AD DS Account|Właściwości odczytu/zapisu|Obiekty zależne użytkownika|

### <a name="permissions-for-password-hash-synchronization"></a>Uprawnienia do synchronizacji skrótów haseł 
Aby ustawić uprawnienia dla konta usługi AD DS łącznika, korzystając z synchronizacji skrótów haseł, uruchom polecenie: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


Lub; 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

To polecenie cmdlet będzie ustawić następujące uprawnienia: 

|Type |Name (Nazwa) |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwalaj |Łącznik usługi AD DS Account |Replikowanie zmian katalogów |Tylko ten obiekt (katalog główny domeny)| 
|Zezwalaj |Łącznik usługi AD DS Account |Replikowanie zmian katalogów wszystkie |Tylko ten obiekt (katalog główny domeny)| 
  
### <a name="permissions-for-password-writeback"></a>Uprawnienia do zapisywania zwrotnego haseł 
Aby ustawić uprawnienia dla konta usługi AD DS łącznika, korzystając z funkcji zapisywania zwrotnego haseł, uruchom polecenie: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Lub;

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
To polecenie cmdlet będzie ustawić następujące uprawnienia: 

|Type |Name (Nazwa) |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwalaj |Łącznik usługi AD DS Account |Resetowanie hasła |Obiekty zależne użytkownika| 
|Zezwalaj |Łącznik usługi AD DS Account |Zapis właściwości lockoutTime |Obiekty zależne użytkownika| 
|Zezwalaj |Łącznik usługi AD DS Account |Zapis właściwości pwdLastSet |Obiekty zależne użytkownika| 

### <a name="permissions-for-group-writeback"></a>Uprawnienia do zapisu zwrotnego grup 
Aby ustawić uprawnienia dla konta usługi AD DS łącznika, używając zapisu zwrotnego grup, uruchom polecenie: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
Lub; 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
To polecenie cmdlet będzie ustawić następujące uprawnienia: 

|Type |Name (Nazwa) |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwalaj |Łącznik usługi AD DS Account |Ogólny odczytu/zapisu |Wszystkie atrybuty grupy typów obiektu i podobiektów| 
|Zezwalaj |Łącznik usługi AD DS Account |Tworzenie/usuwanie obiektu podrzędnego |Wszystkie atrybuty grupy typów obiektu i podobiektów| 
|Zezwalaj |Łącznik usługi AD DS Account |Usuń/usuwania drzewa obiektów|Wszystkie atrybuty grupy typów obiektu i podobiektów|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Uprawnienia dla wdrożenia hybrydowego programu Exchange 
Aby ustawić uprawnienia dla konta usługi AD DS łącznika, korzystając z wdrożenie hybrydowe programu Exchange, uruchom polecenie: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Lub; 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

To polecenie cmdlet będzie ustawić następujące uprawnienia:  
 

|Type |Name (Nazwa) |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwalaj |Łącznik usługi AD DS Account |Odczyt/zapis wszystkich właściwości |Obiekty zależne użytkownika| 
|Zezwalaj |Łącznik usługi AD DS Account |Odczyt/zapis wszystkich właściwości |Obiekty zależne InetOrgPerson| 
|Zezwalaj |Łącznik usługi AD DS Account |Odczyt/zapis wszystkich właściwości |Obiekty grupy elementów podrzędnych| 
|Zezwalaj |Łącznik usługi AD DS Account |Odczyt/zapis wszystkich właściwości |Obiekty zależne kontaktu| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Uprawnienia do folderów publicznych poczty programu Exchange (wersja zapoznawcza) 
Aby ustawić uprawnienia dla konta usługi AD DS łącznika, podczas korzystania z funkcji Foldery publiczne poczty programu Exchange, uruchom polecenie: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Lub; 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
To polecenie cmdlet będzie ustawić następujące uprawnienia: 

|Type |Name (Nazwa) |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwalaj |Łącznik usługi AD DS Account |Odczyt wszystkich właściwości |Obiekty zależne PublicFolder| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Ogranicz uprawnienia w łączniku usługi AD DS konto 
Ten skrypt programu PowerShell spowoduje podwyższenie poziomu uprawnień dla konta łącznika AD podać jako parametr. Obostrzenie uprawnień obejmuje następujące czynności: 

- Wyłącz dziedziczenie dla określonego obiektu 
- Usunąć wszystkie wpisy kontroli dostępu dla określonego obiektu, z wyjątkiem ACE specyficzne dla siebie, ponieważ chcemy zachować uprawnienia domyślne, jeśli chodzi o SAMODZIELNIE. 
 
  Parametr - ADConnectorAccountDN jest konto usługi AD, w której uprawnienia muszą być ściągane. Jest to zazwyczaj MSOL_nnnnnnnnnnnn konta domeny, który jest skonfigurowany w łączniku usługi AD DS (zobacz Określanie konta łącznika AD DS). Credential parametr niezbędne jest określenie konta administratora, które ma niezbędne uprawnienia, aby ograniczyć uprawnienia usługi Active Directory w obiekcie docelowym usługi AD. Jest to zazwyczaj, Enterprise lub administratora domeny.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Na przykład: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

To polecenie cmdlet będzie ustawić następujące uprawnienia: 

|Type |Name (Nazwa) |Dostęp |Dotyczy|
|-----|-----|-----|-----| 
|Zezwalaj |SYSTEM |Pełna kontrola |Ten obiekt 
|Zezwalaj |Enterprise Admins |Pełna kontrola |Ten obiekt 
|Zezwalaj |Administratorzy domeny |Pełna kontrola |Ten obiekt 
|Zezwalaj |Administratorzy |Pełna kontrola |Ten obiekt 
|Zezwalaj |Enterprise Domain Controllers |Wyświetl zawartość |Ten obiekt 
|Zezwalaj |Enterprise Domain Controllers |Odczyt wszystkich właściwości |Ten obiekt 
|Zezwalaj |Enterprise Domain Controllers |Uprawnienia do odczytu |Ten obiekt 
|Zezwalaj |Uwierzytelnieni użytkownicy |Wyświetl zawartość |Ten obiekt 
|Zezwalaj |Uwierzytelnieni użytkownicy |Odczyt wszystkich właściwości |Ten obiekt 
|Zezwalaj |Uwierzytelnieni użytkownicy |Uprawnienia do odczytu |Ten obiekt 

## <a name="next-steps"></a>Następne kroki
- [Azure AD Connect: Konta i uprawnienia](reference-connect-accounts-permissions.md)
- [Instalacja ekspresowa](how-to-connect-install-express.md)
- [Instalacja niestandardowa](how-to-connect-install-custom.md)
- [Odwołanie ADSyncConfig](reference-connect-adsyncconfig.md)

