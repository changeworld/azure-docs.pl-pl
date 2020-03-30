---
title: Rozwiązywanie problemów z hybrydowymi urządzeniami przyłączanym do usługi Azure Active Directory
description: Rozwiązywanie problemów z hybrydową usługą Azure Active Directory dołączył do urządzeń z systemem Windows 10 i Windows Server 2016.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26e52930211611673b6fe2309e2dca067a91ebc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331770"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Rozwiązywanie problemów z hybrydowymi urządzeniami przyłączanym do usługi Azure Active Directory 

Zawartość tego artykułu dotyczy urządzeń z systemem Windows 10 lub Windows Server 2016.

W przypadku innych klientów systemu Windows zobacz artykuł [Rozwiązywanie problemów z hybrydowymi urządzeniami usługi Azure Active Directory przyłączanych do urządzeń w dół](troubleshoot-hybrid-join-windows-legacy.md).

W tym artykule przyjęto założenie, że [skonfigurowano hybrydowe urządzenia przyłączone do usługi Azure Active Directory](hybrid-azuread-join-plan.md) w następujących scenariuszach:

- Dostęp warunkowy oparty na urządzeniach
- [Roaming w przedsiębiorstwie ustawień](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Windows Hello dla firm](../active-directory-azureadjoin-passport-deployment.md)

Ten dokument zawiera wskazówki dotyczące rozwiązywania problemów w celu rozwiązania potencjalnych problemów. 

W systemach Windows 10 i Windows Server 2016 hybrydowe dołączanie do usługi Azure Active Directory obsługuje aktualizację systemu Windows 10 z listopada 2015 r. i więcej.

## <a name="troubleshoot-join-failures"></a>Rozwiązywanie problemów z błędami sprzężenia

### <a name="step-1-retrieve-the-join-status"></a>Krok 1: Pobierz stan sprzężenia 

**Aby pobrać stan sprzężenia:**

1. Otwieranie wiersza polecenia jako administrator
2. Wpisz polecenie `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Krok 2: Oceń stan sprzężenia 

Przejrzyj następujące pola i upewnij się, że mają oczekiwane wartości:

#### <a name="domainjoined--yes"></a>DomainJoined : TAK  

To pole wskazuje, czy urządzenie jest przyłączone do lokalnej usługi Active Directory, czy nie. Jeśli wartość to **NO**, urządzenie nie może wykonać hybrydowego sprzężenia usługi Azure AD.  

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NIE  

To pole wskazuje, czy urządzenie jest zarejestrowane w usłudze Azure AD jako urządzenie osobiste (oznaczone jako *Przyłączone do miejsca pracy).* Ta wartość powinna być **nie** dla komputera przyłączony do domeny, który jest również hybrydowy usługi Azure AD przyłączony. Jeśli wartość jest **TAK**, konto pracy lub szkoły został dodany przed zakończeniem hybrydowego sprzężenia usługi Azure AD. W takim przypadku konto jest ignorowane podczas korzystania z wersji anniversary update systemu Windows 10 (1607).

#### <a name="azureadjoined--yes"></a>AzureAdJoined : TAK  

To pole wskazuje, czy urządzenie jest połączone. Wartość będzie **TAK,** jeśli urządzenie jest urządzeniem przyłączanym do usługi Azure AD lub hybrydowym urządzeniem przyłączanym do usługi Azure AD.
Jeśli wartość jest **NIE**, sprzężenie do usługi Azure AD nie zostało jeszcze zakończone. 

Przejdź do następnych kroków w celu dalszego rozwiązywania problemów.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Krok 3: Znajdź fazę, w której sprzężenie nie powiodło się i kod błędu

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 i powyżej

Poszukaj podsekcji "Poprzednia rejestracja" w sekcji "Dane diagnostyczne" danych wyjściowych stanu sprzężenia. Ta sekcja jest wyświetlana tylko wtedy, gdy urządzenie jest przyłączone do domeny i nie może hybrydowego sprzężenia usługi Azure AD.
Pole "Faza błędu" oznacza fazę niepowodzenia sprzężenia, podczas gdy "Client ErrorCode" oznacza kod błędu operacji Sprzężenia.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Starsze wersje systemu Windows 10

Użyj dzienników Podglądu zdarzeń, aby zlokalizować kod fazy i błędu dla niepowodzeń sprzężenia.

1. Otwórz dzienniki zdarzeń **rejestracji urządzenia użytkownika** w podglądzie zdarzeń. Znajduje się w obszarze **Rejestracja** > **Microsoft** > **urządzeń użytkowników** microsoft**windows** > 
2. Poszukaj zdarzeń z następującymi identyfikatorami eventowymi 304, 305, 307.

![Zdarzenie dziennika awarii](./media/troubleshoot-hybrid-join-windows-current/1.png)

![Zdarzenie dziennika awarii](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Krok 4: Sprawdź możliwe przyczyny i rozwiązania z poniższych list

#### <a name="pre-check-phase"></a>Faza wstępnego sprawdzania

Możliwe przyczyny awarii:

- Urządzenie nie ma linii wzroku do kontrolera domeny.
   - Urządzenie musi znajdować się w sieci wewnętrznej organizacji lub w sieci VPN z siecią wzroku do lokalnego kontrolera domeny usługi Active Directory (AD).

#### <a name="discover-phase"></a>Odkryj fazę

Możliwe przyczyny awarii:

- Obiekt punktu połączenia usługi (SCP) został błędnie skonfigurowany/nie można odczytać obiektu SCP z kontrolera domeny.
   - Prawidłowy obiekt SCP jest wymagany w lesie AD, do którego należy urządzenie, który wskazuje zweryfikowaną nazwę domeny w usłudze Azure AD.
   - Szczegóły można znaleźć w sekcji [Konfigurowanie punktu połączenia serwisowego](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Nie można połączyć i pobrać metadanych odnajdywania z punktu końcowego odnajdywania.
   - Urządzenie powinno mieć dostęp `https://enterpriseregistration.windows.net`do punktu końcowego rejestracji i autoryzacji w kontekście SYSTEM. 
   - Jeśli środowisko lokalne wymaga wychodzącego serwera proxy, administrator IT musi upewnić się, że konto komputera urządzenia jest w stanie odnajdować i dyskretnie uwierzytelnić się w wychodzącym serwerze proxy.
- Niepowodzenie połączenia z punktem końcowym obszaru użytkownika i wykonania odnajdywania obszaru. (Windows 10 w wersji 1809 lub nowszej)
   - Urządzenie powinno mieć dostęp `https://login.microsoftonline.com`do odnajdywania obszaru dla zweryfikowanej domeny i określania typu domeny (zarządzanego/federacyjnego).
   - Jeśli środowisko lokalne wymaga wychodzącego serwera proxy, administrator IT musi upewnić się, że kontekst systemowy na urządzeniu jest w stanie odnajdować i dyskretnie uwierzytelnić się w wychodzącym serwerze proxy.

**Typowe kody błędów:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Przyczyna: Nie można odczytać obiektu SCP i uzyskać informacji o dzierżawie usługi Azure AD.
   - Rozwiązanie: Zapoznaj się z [sekcją Konfigurowanie punktu połączenia serwisowego](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Przyczyna: Błąd odnajdywania ogólnego. Nie można uzyskać metadanych odnajdywania z drs.
   - Rozwiązanie: Znajdź poderror poniżej, aby zbadać dalej.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** (0x801c001f/-2145648609)
   - Przyczyna: Limit czasu operacji podczas wykonywania odnajdywania.
   - Rozwiązanie: Upewnij `https://enterpriseregistration.windows.net` się, że jest dostępny w kontekście SYSTEM. Aby uzyskać więcej informacji, zobacz sekcję [Wymagania dotyczące łączności sieciowej](hybrid-azuread-join-managed-domains.md#prerequisites).
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Przyczyna: Ogólny błąd odnajdywania obszaru. Nie można określić typu domeny (zarządzanego/federacyjnego) z usługi STS. 
   - Rozwiązanie: Znajdź poderror poniżej, aby zbadać dalej.

**Typowe kody podoktorowe:**

Aby znaleźć kod podoproferu dla kodu błędu odnajdywania, należy użyć jednej z następujących metod.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 i powyżej

Poszukaj "DRS Discovery Test" w sekcji "Dane diagnostyczne" danych wyjściowych stanu sprzężenia. Ta sekcja jest wyświetlana tylko wtedy, gdy urządzenie jest przyłączone do domeny i nie może hybrydowego sprzężenia usługi Azure AD.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Starsze wersje systemu Windows 10

Użyj dzienników Podglądu zdarzeń, aby zlokalizować fazę i kod błędu dla błędów sprzężenia.

1. Otwórz dzienniki zdarzeń **rejestracji urządzenia użytkownika** w podglądzie zdarzeń. Znajduje się w obszarze **Rejestracja** > **Microsoft** > **urządzeń użytkowników** microsoft**windows** > 
2. Poszukaj zdarzeń z następującymi identyfikatorami zdarzeń 201

![Zdarzenie dziennika awarii](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>Błędy sieciowe

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Przyczyna: Nie można nawiązać połączenia z serwerem
   - Rozwiązanie: Upewnij się, że łączność sieciowa z wymaganymi zasobami firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące łączności sieciowej](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Przyczyna: Ogólny limit czasu sieci.
   - Rozwiązanie: Upewnij się, że łączność sieciowa z wymaganymi zasobami firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące łączności sieciowej](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Przyczyna: Stos sieciowy nie może zdekodować odpowiedzi z serwera.
   - Rozwiązanie: Upewnij się, że serwer proxy sieciowy nie zakłóca i nie modyfikuje odpowiedzi serwera.

###### <a name="http-errors"></a>Błędy HTTP

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Przyczyna: Obiekt SCP skonfigurowany z niewłaściwym identyfikatorem dzierżawy. Lub nie znaleziono żadnych aktywnych subskrypcji w dzierżawie.
   - Rozwiązanie: Upewnij się, że obiekt SCP jest skonfigurowany przy przy tym przy tym przydzielony przy odpowiednim identyfikatorze dzierżawy usługi Azure AD i aktywnych subskrypcjach lub w dzierżawie.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Powód: HTTP 503 z serwera DRS.
   - Rozwiązanie: Serwer jest obecnie niedostępny. przyszłe próby sprzężenia prawdopodobnie zakończy się powodzeniem, gdy serwer powróci do trybu online.

###### <a name="other-errors"></a>Inne błędy

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Przyczyna: Nie można przeanalizować odpowiedzi serwera JSON. Prawdopodobnie z powodu serwera proxy zwracającego HTTP 200 ze stroną omyskową HTML.
   - Rozwiązanie: Jeśli środowisko lokalne wymaga wychodzącego serwera proxy, administrator IT musi upewnić się, że kontekst systemowy na urządzeniu jest w stanie odnajdować i dyskretnie uwierzytelnić się w wychodzącym serwerze proxy.

#### <a name="authentication-phase"></a>Faza uwierzytelniania

Dotyczy tylko kont domen federacyjnej.

Przyczyny awarii:

- Nie można po cichu uzyskać tokenu dostępu dla zasobu DRS.
   - Urządzenia z systemem Windows 10 uzyskać token uwierzytelniania z usługi federacyjnej przy użyciu zintegrowanego uwierzytelniania systemu Windows do aktywnego punktu końcowego zaufania WS. Szczegóły: [Konfiguracja usługi federacyjnej](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Typowe kody błędów:**

Użyj dzienników Podglądu zdarzeń, aby zlokalizować kod błędu, kod podrzeka, kod błędu serwera i komunikat o błędzie serwera.

1. Otwórz dzienniki zdarzeń **rejestracji urządzenia użytkownika** w podglądzie zdarzeń. Znajduje się w obszarze **Rejestracja** > **Microsoft** > **urządzeń użytkowników** microsoft**windows** > 
2. Poszukaj zdarzeń z następującym identyfikatorem zdarzeń 305

![Zdarzenie dziennika awarii](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>Błędy konfiguracji

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Przyczyna: Protokół uwierzytelniania nie jest ws-trust.
   - Rozwiązanie: Lokalny dostawca tożsamości musi obsługiwać program WS-Trust 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Przyczyna: Lokalna usługa federacyjne nie zwróciła odpowiedzi XML.
   - Rozwiązanie: Upewnij się, że punkt końcowy MEX zwraca prawidłowy kod XML. Upewnij się, że serwer proxy nie zakłóca i zwraca odpowiedzi innych niż xml.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Przyczyna: Nie można odnaleźć punktu końcowego dla uwierzytelniania nazwy użytkownika/hasła.
   - Rozwiązanie: Sprawdź ustawienia lokalnego dostawcy tożsamości. Upewnij się, że punkty końcowe WS-Trust są włączone i upewnij się, że odpowiedź MEX zawiera te poprawne punkty końcowe.

##### <a name="network-errors"></a>Błędy sieciowe

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Przyczyna: Ogólny limit czasu sieci.
   - Rozwiązanie: Upewnij `https://login.microsoftonline.com` się, że jest dostępny w kontekście SYSTEM. Upewnij się, że dostawca tożsamości lokalnej jest dostępny w kontekście SYSTEM. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące łączności sieciowej](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Przyczyna: Połączenie z punktem końcowym omytionego zostało przerwane.
   - Rozwiązanie: Ponów próbę po pewnym czasie lub spróbuj dołączyć z alternatywnej stabilnej lokalizacji sieciowej.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Przyczyna: Nie można zweryfikować certyfikatu wysyłanego przez serwer certyfikatu SSL (Secure Sockets Layer Security), znanego wcześniej jako Secure Sockets Layer (SSL).
   - Rozwiązanie: Sprawdź pochylenie czasu klienta. Ponów próbę po pewnym czasie lub spróbuj dołączyć z alternatywnej stabilnej lokalizacji sieciowej. 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Przyczyna: Próba nawiązania połączenia nie powiodła się. `https://login.microsoftonline.com`
   - Rozwiązanie: Sprawdź połączenie `https://login.microsoftonline.com`sieciowe z .

##### <a name="other-errors"></a>Inne błędy

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Przyczyna: Token SAML z lokalnego dostawcy tożsamości nie został zaakceptowany przez usługę Azure AD.
   - Rozwiązanie: Sprawdź ustawienia serwera federacyjnego. Poszukaj kodu błędu serwera w dziennikach uwierzytelniania.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Przyczyna: Odpowiedź serwera WS-Trust zgłosiła wyjątek błędu i nie udało się uzyskać potwierdzenia
   - Rozwiązanie: Sprawdź ustawienia serwera federacyjnego. Poszukaj kodu błędu serwera w dziennikach uwierzytelniania.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Przyczyna: Odebrano błąd podczas próby uzyskania tokenu dostępu z punktu końcowego tokenu.
   - Rozwiązanie: Poszukaj podstawowego błędu w dzienniku ADAL. 
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Powód: Ogólna awaria ADAL
   - Rozwiązanie: Poszukaj kodu podrzędnego lub kodu błędu serwera z dzienników uwierzytelniania.
    
#### <a name="join-phase"></a>Faza dołączania

Przyczyny awarii:

Znajdź typ rejestracji i poszukaj kodu błędu z poniższej listy.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 i powyżej

Poszukaj podsekcji "Poprzednia rejestracja" w sekcji "Dane diagnostyczne" danych wyjściowych stanu sprzężenia. Ta sekcja jest wyświetlana tylko wtedy, gdy urządzenie jest przyłączone do domeny i nie może hybrydowego sprzężenia usługi Azure AD.
Pole "Typ rejestracji" oznacza typ wykonanego sprzężenia.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Starsze wersje systemu Windows 10

Użyj dzienników Podglądu zdarzeń, aby zlokalizować fazę i kod błędu dla błędów sprzężenia.

1. Otwórz dzienniki zdarzeń **rejestracji urządzenia użytkownika** w podglądzie zdarzeń. Znajduje się w obszarze **Rejestracja** > **Microsoft** > **urządzeń użytkowników** microsoft**windows** > 
2. Poszukaj zdarzeń z następującymi identyfikatorami zdarzeń 204

![Zdarzenie dziennika awarii](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>Błędy HTTP zwrócone z serwera DRS

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Przyczyna: Odebrano odpowiedź błędu z DRS z kodem errorcode: "DirectoryError"
   - Rozwiązanie: Zapoznaj się z kodem błędu serwera z możliwych przyczyn i rozdzielczości.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Przyczyna: Odebrano odpowiedź na błąd z DRS z Kodem Błędu: "AuthenticationError" i ErrorSubCode nie jest "DeviceNotFound". 
   - Rozwiązanie: Zapoznaj się z kodem błędu serwera z możliwych przyczyn i rozdzielczości.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Przyczyna: Odebrano odpowiedź błędu z DRS z kodem errorcode: "DirectoryError"
   - Rozwiązanie: Zapoznaj się z kodem błędu serwera z możliwych przyczyn i rozdzielczości.

##### <a name="tpm-errors"></a>Błędy modułu TPM

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Przyczyna: operacja modułu TPM nie powiodła się lub była nieprawidłowa
   - Rozdzielczość: Prawdopodobnie z powodu złego obrazu sysprep. Upewnij się, że komputer, z którego utworzono obraz sysprep nie jest przyłączony do usługi Azure AD, przyłączono hybrydową usługę Azure AD ani zarejestrowaną usługą Azure AD.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Przyczyna: Ogólny błąd modułu TPM. 
   - Rozdzielczość: Wyłącz moduł TPM na urządzeniach z tym błędem. System Windows 10 w wersji 1809 i nowszych automatycznie wykrywa błędy modułu TPM i kończy hybrydowe sprzężenie usługi Azure AD bez użycia modułu TPM.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Przyczyna: Moduł TPM w trybie FIPS nie jest obecnie obsługiwany.
   - Rozdzielczość: Wyłącz moduł TPM na urządzeniach z tym błędem. System Windows 1809 automatycznie wykrywa błędy modułu TPM i kończy hybrydowe sprzężenie usługi Azure AD bez użycia modułu TPM.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Powód: Moduł TPM zablokowany.
   - Rozdzielczość: Błąd przejściowy. Poczekaj na okres odnowienia. Próba sprzężenia po pewnym czasie powinna zakończyć się pomyślnie. Więcej informacji można znaleźć w artykule [TPM podstawy](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>Błędy sieciowe

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Powód: Ogólny limit czasu sieciowy podczas próby zarejestrowania urządzenia w drs
   - Rozwiązanie: Sprawdź łączność `https://enterpriseregistration.windows.net`sieciową z .
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - Przyczyna: Nie można rozpoznać nazwy lub adresu serwera.
   - Rozwiązanie: Sprawdź łączność `https://enterpriseregistration.windows.net`sieciową z . Upewnij się, że rozdzielczość DNS dla nazwy hosta jest dokładna w n /w i na urządzeniu.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Powód: Połączenie z serwerem zostało zakończone nieprawidłowo.
   - Rozwiązanie: Ponów próbę po pewnym czasie lub spróbuj dołączyć z alternatywnej stabilnej lokalizacji sieciowej.

##### <a name="federated-join-server-errors"></a>Błędy serwera sprzężenia federacyjnego

| Kod błędu serwera | Komunikat o błędzie serwera | Możliwe przyczyny | Rozwiązanie |
| --- | --- | --- | --- |
| DirectoryError | Twoje żądanie jest tymczasowo ograniczane. Spróbuj po 300 sekundach. | Oczekiwany błąd. Prawdopodobnie ze względu na składanie wielu wniosków rejestracyjnych w krótkim odstępie czasu. | Ponów próbę dołączenia po okresie odnowienia |

##### <a name="sync-join-server-errors"></a>Synchronizacja serwera sprzężenia Błędy

| Kod błędu serwera | Komunikat o błędzie serwera | Możliwe przyczyny | Rozwiązanie |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: <UUID> Nie znaleziono dzierżawy. Ten błąd może się zdarzyć, jeśli nie ma żadnych aktywnych subskrypcji dla dzierżawy. Skontaktuj się z administratorem subskrypcji. | Identyfikator dzierżawy w obiekcie SCP jest niepoprawny | Upewnij się, że obiekt SCP jest skonfigurowany przy przy tym przy tym przydzielony przy odpowiednim identyfikatorze dzierżawy usługi Azure AD i aktywnych subskrypcjach oraz w dzierżawie. |
| DirectoryError | Obiekt urządzenia o podany identyfikator nie został znaleziony. | Oczekiwany błąd sprzężenia synchronizacji. Obiekt urządzenia nie został zsynchronizowany z usługi AD na usługę Azure AD | Poczekaj na zakończenie synchronizacji usługi Azure AD Connect, a następna próba sprzężenia po zakończeniu synchronizacji rozwiąże problem |
| AuthenticationError | Weryfikacja identyfikatora SID komputera docelowego | Certyfikat na urządzeniu usługi Azure AD nie jest zgodny z certyfikatem używanym do podpisywania obiektu blob podczas sprzężenia synchronizacji. Ten błąd zazwyczaj oznacza, że synchronizacja nie została jeszcze zakończona. |  Poczekaj na zakończenie synchronizacji usługi Azure AD Connect, a następna próba sprzężenia po zakończeniu synchronizacji rozwiąże problem |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Krok 5: Zbieraj dzienniki i skontaktuj się z pomocą techniczną firmy Microsoft

Pobierz skrypty publiczne tutaj: [ https://1drv.ms/u/s! AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. Otwórz wiersz polecenia administratora i uruchom polecenie `start_ngc_tracing_public.cmd`.
2. Wykonaj kroki, aby odtworzyć problem.
3. Przestań uruchamiać skrypt rejestrowania, wykonując `stop_ngc_tracing_public.cmd`program .
4. Zip i wysłać `%SYSTEMDRIVE%\TraceDJPP\*` dzienniki pod do analizy.

## <a name="troubleshoot-post-join-issues"></a>Rozwiązywanie problemów z połączeniem

### <a name="retrieve-the-join-status"></a>Pobieranie stanu sprzężenia 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: TAK i AzureADPrt: TAK
  
Te pola wskazują, czy użytkownik pomyślnie uwierzytelnił się w usłudze Azure AD podczas logowania się do urządzenia. Jeśli wartości są **NIE**, może to być spowodowane:

- Zły klucz magazynu w modułze TPM skojarzony z urządzeniem podczas rejestracji (sprawdź KeySignTest podczas uruchamiania z podwyższonym poziomem uprawnień).
- Alternatywny identyfikator logowania
- Nie znaleziono serwera proxy HTTP

## <a name="known-issues"></a>Znane problemy
- W obszarze Ustawienia -> konta -> Access Work or School hybrydowe urządzenia przyłączone do usługi Azure AD mogą wyświetlać dwa różne konta, jedno dla usługi Azure AD i jedno dla lokalnej usługi AD, po podłączeniu do mobilnych punktów aktywnych lub zewnętrznych sieci Wi-Fi. Jest to tylko problem z interfejsem użytkownika i nie ma żadnego wpływu na funkcjonalność. 
 
## <a name="next-steps"></a>Następne kroki

Kontynuuj [rozwiązywanie problemów z urządzeniami za pomocą polecenia dsregcmd](troubleshoot-device-dsregcmd.md)

W przypadku pytań zapoznaj się z [często zadawanymi pytaniami dotyczącymi zarządzania urządzeniami](faq.md)
