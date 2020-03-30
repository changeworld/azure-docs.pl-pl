---
title: Rozwiązywanie problemów przy użyciu polecenia dsregcmd — Usługa Azure Active Directory
description: Używanie danych wyjściowych z dsregcmd do zrozumienia stanu urządzeń w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd782cdab625934fe60617142e5ac0baf756398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128757"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Rozwiązywanie problemów z urządzeniami za pomocą polecenia dsregcmd

Narzędzie dsregcmd /status musi być uruchamiane jako konto użytkownika domeny.

## <a name="device-state"></a>Stan urządzenia

W tej sekcji wymieniono parametry stanu sprzężenia urządzenia. W poniższej tabeli wymieniono kryteria, według które urządzenie ma znajdować się w różnych stanach sprzężenia.

| AzureAdJoined | EnterpriseJoined (Dołączenie przedsiębiorstwa) | DomainJoined | Stan urządzenia |
| ---   | ---   | ---   | ---   |
| TAK | NO | NO | Przyłączona usługa Azure AD |
| NO | NO | TAK | Przyłączona do domeny |
| TAK | NO | TAK | Hybrydowa przyłączona do usługi AD |
| NO | TAK | TAK | Lokalna drs przyłączone |

> [!NOTE]
> Stan dołączania do miejsca pracy (zarejestrowany w usłudze Azure AD) jest wyświetlany w sekcji "Stan użytkownika"

- **AzureAdJoined:** — ustaw na "TAK", jeśli urządzenie jest przyłączone do usługi Azure AD. "NIE" w przeciwnym razie.
- **EnterpriseJoined:** - Ustaw na "TAK", jeśli urządzenie jest połączone z lokalnym drs. Urządzenie nie może być zarówno EnterpriseJoined i AzureAdJoined.
- **DomainJoined:** - Ustaw na "TAK", jeśli urządzenie jest przyłączone do domeny (AD).
- **Nazwa domeny:** — ustaw nazwę domeny, jeśli urządzenie jest przyłączone do domeny.

### <a name="sample-device-state-output"></a>Przykładowe dane wyjściowe stanu urządzenia

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Szczegóły urządzenia

Wyświetlane tylko wtedy, gdy urządzenie jest przyłączone do usługi Azure AD lub przyłączone do hybrydowej usługi Azure AD (nie zarejestrowane w usłudze Azure AD). W tej sekcji wymieniono dane identyfikujące urządzenie przechowywane w chmurze.

- **Identyfikator urządzenia:** — unikatowy identyfikator urządzenia w dzierżawie usługi Azure AD
- **Odcisk palca:** - Odcisk palca certyfikatu urządzenia 
- **Certyfikat DeviceCertificateValidity:** - Ważność certyfikatu urządzenia
- **KeyContainerId:** - ContainerId klucza prywatnego urządzenia skojarzonego z certyfikatem urządzenia
- **KeyProvider:** - KeyProvider (Sprzęt/ Oprogramowanie) używany do przechowywania klucza prywatnego urządzenia.
- **TpmProchowane:** - "TAK", jeśli klucz prywatny urządzenia jest przechowywany w modułze TPM sprzętu.

### <a name="sample-device-details-output"></a>Przykładowe dane urządzenia wyjściowe

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Szczegóły dzierżawy

Wyświetlane tylko wtedy, gdy urządzenie jest przyłączone do usługi Azure AD lub przyłączone do hybrydowej usługi Azure AD (nie zarejestrowane w usłudze Azure AD). W tej sekcji wymieniono szczegółowe informacje o wspólnej dzierżawie, gdy urządzenie jest przyłączone do usługi Azure AD.

> [!NOTE]
> Jeśli adresy URL mdm w tej sekcji są puste, oznacza to, że mdm nie został skonfigurowany lub bieżący użytkownik nie jest w zakresie rejestracji MDM. Sprawdź ustawienia mobilności w usłudze Azure AD, aby przejrzeć konfigurację mdm.

> [!NOTE]
> Nawet jeśli widzisz adresy URL mdm, nie oznacza to, że urządzenie jest zarządzane przez mdm. Informacje są wyświetlane, jeśli dzierżawca ma konfigurację MDM do automatycznej rejestracji, nawet jeśli samo urządzenie nie jest zarządzane. 

### <a name="sample-tenant-details-output"></a>Przykładowe dane wyjściowe dzierżawy

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Stan użytkownika

W tej sekcji wymieniono stan różnych atrybutów dla aktualnie zalogowanego użytkownika do urządzenia.

> [!NOTE]
> Polecenie musi być uruchamiane w kontekście użytkownika, aby pobrać prawidłowy stan.

- **NgcSet:** - Ustaw na "TAK", jeśli klucz Windows Hello jest ustawiony dla bieżącego zalogowanego użytkownika.
- **NgcKeyId:** - Identyfikator klucza Windows Hello, jeśli jest ustawiony dla bieżącego zalogowanego użytkownika.
- **CanReset:** - Oznacza, czy klucz Windows Hello może zostać zresetowany przez użytkownika. 
- **Możliwe wartości:** - DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive lub Unknown if error. 
- **WorkplaceJoined:** - Ustaw wartość "TAK", jeśli konta zarejestrowane w usłudze Azure AD zostały dodane do urządzenia w bieżącym kontekście NTUSER.
- **WamDefaultSet:** - Ustaw wartość "TAK", jeśli dla zalogowanego użytkownika zostanie utworzone domyślne konto WebAccount WAM. To pole może wyświetlać błąd, jeśli dsreg /status jest uruchamiany z wiersza polecenia z podwyższonym poziomem uprawnień. 
- **WamDefaultAuthority:** — ustawiono wartość "organizacje" dla usługi Azure AD.
- **WamDefaultId:** -https://login.microsoft.comZawsze " dla usługi Azure AD.
- **WamDefaultGUID:** — identyfikator GUID dostawcy WAM (konto Usługi Azure AD/Microsoft) dla domyślnego konta INTERNETOWEGO WAM. 

### <a name="sample-user-state-output"></a>Przykładowe dane wyjściowe stanu użytkownika

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>Stan SSO

Tę sekcję można zignorować dla urządzeń zarejestrowanych w usłudze Azure AD.

> [!NOTE]
> Polecenie musi być uruchamiane w kontekście użytkownika, aby pobrać prawidłowy stan dla tego użytkownika.

- **AzureAdPrt:** — ustaw na "TAK", jeśli PRT jest obecny na urządzeniu dla zalogowanego użytkownika.
- **AzureAdPrtUpdateTime:** — ustaw czas w czasie UTC, gdy PRT został ostatnio zaktualizowany.
- **AzureAdPrtExpiryTime:** — ustaw czas w czasie UTC, gdy PRT wygaśnie, jeśli nie zostanie odnowiony.
- **AzureAdPrtAuthority:** — adres URL urzędu usługi Azure AD
- **EnterprisePrt:** - Ustaw na "TAK", jeśli urządzenie ma PRT z lokalnego systemu ADFS. W przypadku hybrydowych urządzeń przyłączonych do usługi Azure AD urządzenie może jednocześnie mieć PRT zarówno z usługi Azure AD, jak i lokalnej usługi AD. Urządzenia przyłączone lokalnie będą miały tylko PRT dla przedsiębiorstw.
- **EnterprisePrtUpdateTime:** - Ustaw czas w utc, kiedy prt przedsiębiorstwa został ostatnio zaktualizowany.
- **EnterprisePrtExpiryTime:** - Ustaw czas w utc, gdy PRT ma wygasnąć, jeśli nie jest odnawiany.
- **EnterprisePrtAuthority:** - Adres URL urzędu ADFS

### <a name="sample-sso-state-output"></a>Przykładowe wyjście stanu SSO

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Dane diagnostyczne

### <a name="pre-join-diagnostics"></a>Diagnostyka przed sprzężenia

Ta sekcja jest wyświetlana tylko wtedy, gdy urządzenie jest przyłączone do domeny i nie może hybrydowego sprzężenia usługi Azure AD.

W tej sekcji wykonuje się różne testy ułatwiające diagnozowanie niepowodzeń sprzężenia. Ta sekcja zawiera również szczegóły poprzedniego (?). Informacje te obejmują fazę błędu, kod błędu, identyfikator żądania serwera, stan http odpowiedzi serwera, komunikat o błędzie odpowiedzi serwera.

- **Kontekst użytkownika:** — kontekst, w którym są uruchamiane diagnostyki. Możliwe wartości: SYSTEM, UN-ELEVATED User, ELEVATED User. 

   > [!NOTE]
   > Ponieważ sprzężenie rzeczywiste jest wykonywane w kontekście systemu, uruchamianie diagnostyki w kontekście SYSTEM jest najbliżej rzeczywistego scenariusza sprzężenia. Aby uruchomić diagnostykę w kontekście SYSTEMU, polecenie dsregcmd /status musi być uruchamiane z wiersza polecenia z podwyższonym poziomem uprawnień.

- **Czas klienta:** - Czas systemowy w czasie UTC.
- **Test łączności usługi AD:** — test wykonuje test łączności z kontrolerem domeny. Błąd w tym teście prawdopodobnie spowoduje błędy sprzężenia w fazie wstępnej kontroli.
- **Test konfiguracji usługi AD:** — test odczytuje i sprawdza, czy obiekt SCP jest poprawnie skonfigurowany w lokalnym lesie ad. Błędy w tym teście prawdopodobnie spowoduje dołączyć błędy w fazie odnajdywanie z kodem błędu 0x801c001d.
- **Test odnajdowania DRS:** — test pobiera punkty końcowe DRS z punktu końcowego metadanych odnajdywania i wykonuje żądanie obszaru użytkownika. Błędy w tym teście prawdopodobnie spowoduje błędy sprzężenia w fazie odnajdywanie.
- **Test łączności DRS:** — test wykonuje podstawowy test łączności z punktem końcowym DRS.
- **Test pozyskiwania tokenów:** — test próbuje uzyskać token uwierzytelniania usługi Azure AD, jeśli dzierżawa użytkownika jest federowana. Błędy w tym teście prawdopodobnie spowoduje błędy sprzężenia w fazie eru. Jeśli auth nie powiedzie się sprzężenie synchronizacji zostanie podjęta jako rezerwowy, chyba że rezerwowy jest jawnie wyłączone z poniższych ustawień klucza rejestru.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Powrót do sync-join:** - Ustaw na "Włączone", jeśli powyższy klucz rejestru, aby zapobiec rezerwowej do synchronizacji sprzężenia z błędami akcesji, NIE jest obecny. Ta opcja jest dostępna w systemie Windows 10 1803 lub nowszym.
- **Poprzednia rejestracja:** - Czas poprzedniej próby sprzężenia. Rejestrowane są tylko nieudane próby sprzężenia.
- **Faza błędu:** - Etap sprzężenia, w którym został przerwany. Możliwe wartości to wstępne sprawdzanie, odnajdowanie, omów, sprzężenie.
- **Client ErrorCode:** - Zwrócony kod błędu klienta (HRESULT).
- **Kod błędu serwera:** — kod błędu serwera, jeśli żądanie zostało wysłane do serwera, a serwer odpowiedział z powrotem kodem błędu. 
- **Komunikat serwera:** - Komunikat serwera zwrócony wraz z kodem błędu.
- **Stan https:** - Stan http zwrócony przez serwer.
- **Identyfikator żądania:** — identyfikator żądania klienta wysłany do serwera. Przydatne do skorelowania z dziennikami po stronie serwera.

### <a name="sample-pre-join-diagnostics-output"></a>Przykładowe wyjście diagnostyczne przed sprzężenia

W poniższym przykładzie pokazano test diagnostyczny nie powiódł się z błędem odnajdywania.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

Poniższy przykład pokazuje testy diagnostyczne są przekazywania, ale próba rejestracji nie powiodło się z błędem katalogu, który jest oczekiwany dla sprzężenia synchronizacji. Po zakończeniu zadania synchronizacji usługi Azure AD Connect urządzenie będzie mogło dołączyć.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

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

### <a name="post-join-diagnostics"></a>Diagnostyka połączona

W tej sekcji są wyświetlane dane wyjściowe kontroli poczytalności wykonywane na urządzeniu przyłączanym do chmury.

- **AadRecoveryEnabled:** - Jeśli "TAK", klucze przechowywane w urządzeniu nie nadają się do użytku, a urządzenie jest oznaczone do odzyskania. Następne zalogowanie spowoduje wyzwolenie przepływu odzyskiwania i ponowne zarejestrowanie urządzenia.
- **KeySignTest:** - Jeśli "PASSED" klucze urządzenia są w dobrym stanie. Jeśli KeySignTest nie powiedzie się, urządzenie będzie zwykle oznaczone do odzyskiwania. Następne zalogowanie spowoduje wyzwolenie przepływu odzyskiwania i ponowne zarejestrowanie urządzenia. W przypadku urządzeń połączonych z usługą Azure AD odzyskiwanie jest dyskretne. Podczas gdy usługa Azure AD przyłączona lub usługa Azure AD została zarejestrowana, urządzenia będą monitować o uwierzytelnianie użytkowników w celu odzyskania i ponownego zarejestrowania urządzenia, jeśli to konieczne. **KeySignTest wymaga podwyższonych uprawnień.**

#### <a name="sample-post-join-diagnostics-output"></a>Przykładowe wyjście diagnostyczne po sprzęcie

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>Kontrola wymagań wstępnych NGC

W tej sekcji wykonywane są kontrole perquisite pod kątem inicjowania obsługi administracyjnej systemu Windows Hello dla firm (WHFB). 

> [!NOTE]
> W dsregcmd /status może nie być widoczne wstępne szczegóły sprawdzania NGC, jeśli użytkownik pomyślnie skonfigurował WHFB.

- **IsDeviceJoined:** - Ustaw na "TAK", jeśli urządzenie jest przyłączone do usługi Azure AD.
- **IsUserAzureAD:** - Ustaw na "TAK", jeśli zalogowany użytkownik jest obecny w usłudze Azure AD .
- **PolicyEnabled:** - Ustaw na "TAK", jeśli zasady WHFB jest włączona na urządzeniu.
- **PostLogonEnabled:** - Ustaw na "TAK", jeśli rejestracja WHFB jest wyzwalana natywnie przez platformę. Jeśli jest ustawiona na "NIE", oznacza to, że rejestracja funkcji Windows Hello dla firm jest wyzwalana przez mechanizm niestandardowy
- **DeviceEkwalifikowalne:** - Ustaw na "TAK", jeśli urządzenie spełnia wymagania sprzętowe dotyczące rejestracji w WHFB.
- **SessionIsNotRemote:** - Ustaw na "TAK", jeśli bieżący użytkownik jest zalogowany bezpośrednio do urządzenia, a nie zdalnie.
- **CertEnrollment:** — specyficzne dla wdrożenia zaufania certyfikatów WHFB, wskazujące urząd rejestracji certyfikatów dla WHFB. Ustaw na "urząd rejestracji", jeśli źródłem zasad WHFB jest zasady grupy, "zarządzanie urządzeniami przenośnymi", jeśli źródłem jest MDM. "none" w przeciwnym razie
- **AdfsRefreshToken:** — specyficzne dla wdrożenia zaufania certyfikatów WHFB. Obecny tylko wtedy, gdy CertEnrollment jest "urząd rejestracji". Wskazuje, czy urządzenie ma PRT dla przedsiębiorstwa dla użytkownika.
- **AdfsRaIsReady:** — specyficzne dla wdrożenia zaufania certyfikatów WHFB.  Obecny tylko wtedy, gdy CertEnrollment jest "urząd rejestracji". Ustaw na "TAK", jeśli usługa ADFS wskazuje w metadanych odnajdowania, że obsługuje WHFB *i* jeśli szablon certyfikatu logowania jest dostępny.
- **LognCertTemplateReady:** — specyficzne dla wdrożenia zaufania certyfikatów WHFB. Obecny tylko wtedy, gdy CertEnrollment jest "urząd rejestracji". Ustaw na "TAK", jeśli stan szablonu certyfikatu logowania jest prawidłowy i pomaga w rozwiązywaniu problemów z systemem ADFS RA.
- **PreReqResult:** - Zapewnia wynik wszystkich WHFB wstępnej oceny. Ustaw na "Will Provision", jeśli rejestracja WHFB zostanie uruchomiona jako zadanie po zalogowaniu, gdy użytkownik zaloguje się następnym razem.

### <a name="sample-ngc-prerequisite-check-output"></a>Przykładowe dane wyjściowe sprawdzania wymagań wstępnych NGC

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Następne kroki

W przypadku pytań zapoznaj się z [często zadawanymi pytaniami dotyczącymi zarządzania urządzeniami](faq.md)
