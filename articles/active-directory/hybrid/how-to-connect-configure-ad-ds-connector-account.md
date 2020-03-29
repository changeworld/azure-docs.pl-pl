---
title: 'Usługa Azure AD Connect: konfigurowanie uprawnień kont łącznika usług AD DS | Dokumenty firmy Microsoft'
description: Ten dokument zawiera szczegółowe informacje o konfigurowaniu konta łącznika usług AD DS za pomocą nowego modułu programu ADSyncConfig PowerShell
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb80c3a94e63a886e4a16c0b8fa445b2a8a34e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72515823"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Usługa Azure AD Connect: konfigurowanie uprawnień kont łącznika usług AD DS 

Moduł programu PowerShell o nazwie [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) został wprowadzony z kompilacją 1.1.880.0 (wydaną w sierpniu 2018 r.), która zawiera kolekcję poleceń cmdlet ułatwiające konfigurowanie prawidłowych uprawnień usługi Active Directory dla wdrożenia usługi Azure AD Connect. 

## <a name="overview"></a>Omówienie 
Następujące polecenia cmdlet programu PowerShell mogą służyć do konfigurowania uprawnień usługi Active Directory konta łącznika usług AD DS dla każdej funkcji wybranej do włączenia w usłudze Azure AD Connect. Aby zapobiec wszelkim problemom, należy z wyprzedzeniem przygotować uprawnienia usługi Active Directory za każdym razem, gdy chcesz zainstalować usługę Azure AD Connect przy użyciu niestandardowego konta domeny w celu nawiązania połączenia z lasem. Ten moduł ADSyncConfig może również służyć do konfigurowania uprawnień po wdrożeniu usługi Azure AD Connect.

![przegląd konta ad ds](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

W przypadku instalacji usługi Azure AD Connect Express w usłudze Active Directory tworzone jest automatycznie generowane konto (MSOL_nnnnnnnnnn) ze wszystkimi niezbędnymi uprawnieniami, więc nie ma potrzeby używania tego modułu ADSyncConfig, chyba że masz zablokowane uprawnienia dziedziczenie na jednostkach organizacyjnych lub na określonych obiektach usługi Active Directory, które chcesz zsynchronizować z usługą Azure AD. 
 
### <a name="permissions-summary"></a>Podsumowanie uprawnień 
Poniższa tabela zawiera podsumowanie uprawnień wymaganych dla obiektów usługi AD: 

| Funkcja | Uprawnienia |
| --- | --- |
| funkcja ms-DS-ConsistencyGuid |Uprawnienia do odczytu i zapisu do atrybutu ms-DS-ConsistencyGuid udokumentowane w [pojęciach projektowych — używanie ms-DS-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synchronizacja skrótu hasła |<li>Replikowanie zmian w katalogu</li>  <li>Wszystkie zmiany katalogu replikacji |
| Wdrażanie hybrydowe programu Exchange |Uprawnienia do odczytu i zapisu do atrybutów udokumentowanych w [programie Exchange hybrydowy zapis zwrotny](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) dla użytkowników, grup i kontaktów. |
| Folder publiczny programu Exchange Mail |Uprawnienia do odczytu atrybutów udokumentowanych w [folderze publicznym poczty programu Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) dla folderów publicznych. | 
| Zapisywanie zwrotne haseł |Uprawnienia do odczytu i zapisu do atrybutów udokumentowanych w [sekcji Wprowadzenie do zarządzania hasłami](../authentication/howto-sspr-writeback.md) dla użytkowników. |
| Zapisywanie zwrotne urządzeń |Uprawnienia do odczytu i zapisu do obiektów urządzeń i kontenerów udokumentowanych w [storcowanie urządzenia](how-to-connect-device-writeback.md). |
| Zapisywanie zwrotne grup |Obiekty grupy odczytu, tworzenia, aktualizowania i usuwania dla zsynchronizowanych **grup usługi Office 365**.  Aby uzyskać więcej informacji, zobacz [Grupowe odpisy .](how-to-connect-preview.md#group-writeback)|

## <a name="using-the-adsyncconfig-powershell-module"></a>Korzystanie z modułu programu ADSyncConfig PowerShell 
Moduł ADSyncConfig wymaga [narzędzia RSAT (Remote Server Administration Tools) dla usług AD DS,](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) ponieważ zależy od modułu i narzędzi programu AD DS PowerShell. Aby zainstalować RSAT dla usług AD DS, otwórz okno programu Windows PowerShell z "Uruchom jako administrator" i wykonaj: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Konfigurowanie](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Można również skopiować plik **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** do kontrolera domeny, który ma już zainstalowany moduł RSAT dla usług AD DS i stamtąd użyć tego modułu programu PowerShell.

Aby rozpocząć korzystanie z usługi ADSyncConfig, należy załadować moduł w oknie programu Windows PowerShell: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Aby sprawdzić wszystkie polecenia cmdlet zawarte w tym module, można wpisać:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Zaznacz](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Każde polecenie cmdlet ma te same parametry do wprowadzania konta łącznika usług AD DS i przełącznika AdminSDHolder. Aby określić konto łącznika usług AD DS, można podać nazwę konta i domenę lub tylko nazwę wyróżniającą konta( DN),

Na przykład:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Lub;

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Pamiętaj, aby `<ADAccountName>` `<ADDomainName>` zastąpić `<ADAccountDN>` i z odpowiednimi wartościami dla środowiska.

Jeśli nie chcesz modyfikować uprawnień w kontenerze AdminSDHolder, `-SkipAdminSdHolders`użyj przełącznika . 

Domyślnie wszystkie polecenia cmdlet zestaw uprawnień będą próbowały ustawić uprawnienia usług AD DS w katalogu głównym każdej domeny w lesie, co oznacza, że użytkownik uruchamiacy sesję programu PowerShell wymaga praw administratora domeny w każdej domenie w lesie.  Z powodu tego wymagania zaleca się użycie administratora przedsiębiorstwa z katalogu głównego lasu. Jeśli wdrożenie usługi Azure AD Connect ma wiele łączników usług AD DS, będzie wymagane uruchomienie tego samego polecenia cmdlet w każdym lesie, który ma łącznik usług AD DS. 

Można również ustawić uprawnienia do określonego obiektu OU lub `-ADobjectDN` USŁUG AD DS przy użyciu parametru, po którym następuje nazwa DN obiektu docelowego, w którym chcesz ustawić uprawnienia. W przypadku korzystania z docelowej nazwy ADobjectDN polecenie cmdlet ustawi uprawnienia tylko do tego obiektu, a nie do katalogu głównego domeny lub kontenera AdminSDHolder. Ten parametr może być przydatny, gdy niektóre obiekty U lub USŁUGI AD DS mają wyłączone dziedziczenie uprawnień (zobacz Lokalizowanie obiektów USŁUG AD DS z wyłączonym dziedziczeniem uprawnień) 

Wyjątkami od tych typowych parametrów `Set-ADSyncRestrictedPermissions` są polecenie cmdlet, które służy do ustawiania uprawnień do samego konta łącznika usług AD DS, oraz `Set-ADSyncPasswordHashSyncPermissions` polecenie cmdlet, ponieważ `-ObjectDN` `-SkipAdminSdHolders` uprawnienia wymagane dla synchronizacji skrótów haseł są ustawiane tylko w katalogu głównym domeny, w związku z czym to polecenie cmdlet nie zawiera parametrów ani parametrów.

### <a name="determine-your-ad-ds-connector-account"></a>Określanie konta łącznika usług AD DS 
W przypadku, gdy usługa Azure AD Connect jest już zainstalowana i chcesz sprawdzić, co jest kontem łącznika usług AD DS aktualnie używanym przez usługę Azure AD Connect, możesz wykonać polecenie cmdlet: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Lokalizowanie obiektów usług AD DS z wyłączonym dziedziczeniem uprawnień 
Jeśli chcesz sprawdzić, czy istnieje dowolny obiekt usług AD DS z wyłączonym dziedziczeniem uprawnień, możesz uruchomić: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Domyślnie to polecenie cmdlet będzie szukać tylko ulegów z wyłączonym dziedziczeniem, ale można określić inne klasy obiektów usług AD DS w `-ObjectClass` parametrze lub użyć "*" dla wszystkich klas obiektów, w następujący sposób: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Wyświetlanie uprawnień obiektu usług AD DS 
Za pomocą polecenia cmdlet poniżej można wyświetlić listę uprawnień aktualnie ustawionych w obiekcie usługi Active Directory, udostępniając jego DistinguishedName: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Azure AD Connect: konfigurowanie konta łącznika usługi AD DS 
 
### <a name="configure-basic-read-only-permissions"></a>Konfigurowanie podstawowych uprawnień tylko do odczytu 
Aby ustawić podstawowe uprawnienia tylko do odczytu dla konta łącznika usług AD DS, gdy nie jest ona korzystać z żadnej funkcji usługi Azure AD Connect, uruchom: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


lub; 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


To polecenie cmdlet ustawi następujące uprawnienia: 
 

|Typ |Nazwa |Dostęp |Dotyczy:| 
|-----|-----|-----|-----|
|Zezwalaj |Konto łącznika usług AD DS |Przeczytaj wszystkie właściwości |Obiekty urządzenia podrzędnego| 
|Zezwalaj |Konto łącznika usług AD DS|Przeczytaj wszystkie właściwości |Obiekty InetOrgPerson podrzędne| 
|Zezwalaj |Konto łącznika usług AD DS |Przeczytaj wszystkie właściwości |Obiekty Komputer podrzędny| 
|Zezwalaj |Konto łącznika usług AD DS |Przeczytaj wszystkie właściwości |Obiekt obcsecurityprincipal potomek podrzędny| 
|Zezwalaj |Konto łącznika usług AD DS |Przeczytaj wszystkie właściwości |Obiekty Grupy podrzędnej| 
|Zezwalaj |Konto łącznika usług AD DS |Przeczytaj wszystkie właściwości |Obiekty użytkownika podrzędnego| 
|Zezwalaj |Konto łącznika usług AD DS |Przeczytaj wszystkie właściwości |Obiekty kontaktu podrzędnego| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Konfigurowanie uprawnień ms-ds-consistency-guid 
Aby ustawić uprawnienia dla konta łącznika usług AD DS podczas używania atrybutu ms-Ds-Consistency-Guid jako kotwicy źródłowej (aka opcja "Zezwalaj platformie Azure na zarządzanie kotwicą źródłową dla mnie" ), uruchom: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

lub; 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

To polecenie cmdlet ustawi następujące uprawnienia: 

|Typ |Nazwa |Dostęp |Dotyczy:|
|-----|-----|-----|-----| 
|Zezwalaj|Konto łącznika usług AD DS|Właściwość odczytu/zapisu|Obiekty użytkownika podrzędnego|

### <a name="permissions-for-password-hash-synchronization"></a>Uprawnienia do synchronizacji skrótów haseł 
Aby ustawić uprawnienia do konta łącznika usług AD DS podczas korzystania z synchronizacji skrótów haseł, uruchom: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


lub; 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

To polecenie cmdlet ustawi następujące uprawnienia: 

|Typ |Nazwa |Dostęp |Dotyczy:|
|-----|-----|-----|-----| 
|Zezwalaj |Konto łącznika usług AD DS |Replikowanie zmian w katalogu |Tylko ten obiekt (katalog główny domeny)| 
|Zezwalaj |Konto łącznika usług AD DS |Replikowanie katalogu zmienia wszystko |Tylko ten obiekt (katalog główny domeny)| 
  
### <a name="permissions-for-password-writeback"></a>Uprawnienia do zapisywania haseł 
Aby ustawić uprawnienia do konta łącznika usług AD DS podczas korzystania z funkcji Zapisywanie hasła, uruchom: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


lub;

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
To polecenie cmdlet ustawi następujące uprawnienia: 

|Typ |Nazwa |Dostęp |Dotyczy:|
|-----|-----|-----|-----| 
|Zezwalaj |Konto łącznika usług AD DS |Resetowanie hasła |Obiekty użytkownika podrzędnego| 
|Zezwalaj |Konto łącznika usług AD DS |Zapisz lockout właściwościTime |Obiekty użytkownika podrzędnego| 
|Zezwalaj |Konto łącznika usług AD DS |Zapis właściwość pwdLastSet |Obiekty użytkownika podrzędnego| 

### <a name="permissions-for-group-writeback"></a>Uprawnienia do zapisywania grup 
Aby ustawić uprawnienia do konta łącznika usług AD DS podczas korzystania z grupowego zapisu, uruchom: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
lub; 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
To polecenie cmdlet ustawi następujące uprawnienia: 

|Typ |Nazwa |Dostęp |Dotyczy:|
|-----|-----|-----|-----| 
|Zezwalaj |Konto łącznika usług AD DS |Ogólny odczyt/zapis |Wszystkie atrybuty grupy typów obiektów i podobiektów| 
|Zezwalaj |Konto łącznika usług AD DS |Tworzenie/usuwanie obiektu podrzędnego |Wszystkie atrybuty grupy typów obiektów i podobiektów| 
|Zezwalaj |Konto łącznika usług AD DS |Usuwanie/usuwanie obiektów drzewa|Wszystkie atrybuty grupy typów obiektów i podobiektów|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Uprawnienia do wdrażania hybrydowego programu Exchange 
Aby ustawić uprawnienia dla konta łącznika usług AD DS podczas korzystania z wdrożenia hybrydowego programu Exchange, uruchom: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


lub; 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

To polecenie cmdlet ustawi następujące uprawnienia:  
 

|Typ |Nazwa |Dostęp |Dotyczy:|
|-----|-----|-----|-----| 
|Zezwalaj |Konto łącznika usług AD DS |Odczyt/Zapis wszystkich właściwości |Obiekty użytkownika podrzędnego| 
|Zezwalaj |Konto łącznika usług AD DS |Odczyt/Zapis wszystkich właściwości |Obiekty InetOrgPerson podrzędne| 
|Zezwalaj |Konto łącznika usług AD DS |Odczyt/Zapis wszystkich właściwości |Obiekty Grupy podrzędnej| 
|Zezwalaj |Konto łącznika usług AD DS |Odczyt/Zapis wszystkich właściwości |Obiekty kontaktu podrzędnego| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Uprawnienia do folderów publicznych poczty programu Exchange (wersja zapoznawcza) 
Aby ustawić uprawnienia do konta łącznika usług AD DS podczas korzystania z funkcji Foldery publiczne programu Exchange Mail, uruchom: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


lub; 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
To polecenie cmdlet ustawi następujące uprawnienia: 

|Typ |Nazwa |Dostęp |Dotyczy:|
|-----|-----|-----|-----| 
|Zezwalaj |Konto łącznika usług AD DS |Przeczytaj wszystkie właściwości |Obiekt Podrzędny publicfolder| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Ograniczanie uprawnień do konta łącznika usług AD DS 
Ten skrypt programu PowerShell dokręca uprawnienia dla konta łącznika usługi AD podane jako parametr. Dokręcanie uprawnień obejmuje następujące kroki: 

- Wyłącz dziedziczenie określonego obiektu 
- Usuń wszystkie wpisy ACE na określonym obiekcie, z wyjątkiem obiektów ACE specyficzne dla SELF, ponieważ chcemy zachować domyślne uprawnienia nienaruszone, jeśli chodzi o SELF. 
 
  Parametr -ADConnectorAccountDN to konto usługi AD, którego uprawnienia muszą zostać zaostrzone. Zazwyczaj jest to MSOL_nnnnnnnnnnnn konto domeny skonfigurowane w łączniku usług AD DS (zobacz Określanie konta łącznika usług AD DS). Parametr -Credential jest niezbędny do określenia konta administratora, które ma uprawnienia niezbędne do ograniczenia uprawnień usługi Active Directory do docelowego obiektu USŁUGI AD. Zazwyczaj jest to administrator przedsiębiorstwa lub domeny.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Na przykład: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

To polecenie cmdlet ustawi następujące uprawnienia: 

|Typ |Nazwa |Dostęp |Dotyczy:|
|-----|-----|-----|-----| 
|Zezwalaj |SYSTEM |Pełna kontrola |Ten obiekt 
|Zezwalaj |Enterprise Admins |Pełna kontrola |Ten obiekt 
|Zezwalaj |Administratorzy domeny |Pełna kontrola |Ten obiekt 
|Zezwalaj |Administratorzy |Pełna kontrola |Ten obiekt 
|Zezwalaj |Kontrolery domeny przedsiębiorstwa |Zawartość listy |Ten obiekt 
|Zezwalaj |Kontrolery domeny przedsiębiorstwa |Odczyt wszystkich właściwości |Ten obiekt 
|Zezwalaj |Kontrolery domeny przedsiębiorstwa |Uprawnienia do odczytu |Ten obiekt 
|Zezwalaj |Uwierzytelnieni użytkownicy |Zawartość listy |Ten obiekt 
|Zezwalaj |Uwierzytelnieni użytkownicy |Odczyt wszystkich właściwości |Ten obiekt 
|Zezwalaj |Uwierzytelnieni użytkownicy |Uprawnienia do odczytu |Ten obiekt 

## <a name="next-steps"></a>Następne kroki
- [Azure AD Connect: Accounts and permissions](reference-connect-accounts-permissions.md) (Azure AD Connect: konta i uprawnienia)
- [Instalacja ekspresowa](how-to-connect-install-express.md)
- [Instalacja niestandardowa](how-to-connect-install-custom.md)
- [Informacje o module ADSyncConfig](reference-connect-adsyncconfig.md)

