---
title: Rozwiązywanie problemów z rozszerzeniem serwera NPS usługi Azure MFA — usługa Azure Active Directory
description: Uzyskaj pomoc w rozwiązywaniu problemów z rozszerzeniem NPS dla uwierzytelniania wieloskładnikowego platformy Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3462ada0864d2d8321b1936e94f947c55c754879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294499"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Resolve error messages from the NPS extension for Azure Multi-Factor Authentication (Rozstrzyganie komunikatów o błędach z rozszerzenia serwera NPS dotyczących usługi Azure Multi-Factor Authentication)

Jeśli wystąpią błędy z rozszerzeniem NPS dla uwierzytelniania wieloskładnikowego platformy Azure, użyj tego artykułu, aby szybciej osiągnąć rozwiązanie. Dzienniki rozszerzeń serwera NPS znajdują się w Podglądzie zdarzeń w obszarze Zasady sieciowe **ról serwerów niestandardowych** > **widoków** > **i usługi dostępu dostępu** na serwerze, na którym jest zainstalowane rozszerzenie NPS.

## <a name="troubleshooting-steps-for-common-errors"></a>Kroki rozwiązywania problemów z typowymi błędami

| Kod błędu | Kroki rozwiązywania problemów |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Skontaktuj się z pomocą techniczną](#contact-microsoft-support)i wymień listę kroków do zbierania dzienników. Podaj jak najwięcej informacji o tym, co się stało przed wystąpieniem błędu, w tym identyfikator dzierżawy i nazwę głównej użytkownika (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Może występować problem z tym, jak certyfikat klienta został zainstalowany lub skojarzony z dzierżawą. Postępuj zgodnie z instrukcjami w [rozwiązywaniu problemów z rozszerzeniem serwera NPS usługi MFA,](howto-mfa-nps-extension.md#troubleshooting) aby zbadać problemy z certyfikatem klienta. |
| **ESTS_TOKEN_ERROR** | Postępuj zgodnie z instrukcjami w [rozwiązywaniu problemów z rozszerzeniem serwera NPS usługi MFA,](howto-mfa-nps-extension.md#troubleshooting) aby zbadać problemy z certyfikatem klienta i tokenem ADAL. |
| **HTTPS_COMMUNICATION_ERROR** | Serwer serwera NPS nie może odbierać odpowiedzi z usługi Azure MFA. Sprawdź, czy zapory są otwarte dwukierunkowo dla ruchu do i zhttps://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Na serwerze z rozszerzeniem NPS sprawdź, `https://adnotifications.windowsazure.com` `https://login.microsoftonline.com/`czy można dotrzeć i . Jeśli te witryny nie ładują się, rozwiąż problemy z łącznością na tym serwerze. |
| **Rozszerzenie serwera NPS dla usługi Azure MFA:** <br> Rozszerzenie serwera NPS dla usługi Azure MFA wykonuje tylko pomocniczą uwierzytelniania dla żądań promienia w stanie AccessAccept. Żądanie odebrane dla użytkownika nazwy użytkownika ze stanem odpowiedzi AccessReject, ignorując żądanie. | Ten błąd zwykle odzwierciedla błąd uwierzytelniania w usłudze AD lub że serwer NPS nie może odbierać odpowiedzi z usługi Azure AD. Sprawdź, czy zapory są otwarte dwukierunkowo `https://adnotifications.windowsazure.com` dla `https://login.microsoftonline.com` ruchu do i z i przy użyciu portów 80 i 443. Ważne jest również, aby sprawdzić, czy na karcie DIAL-IN uprawnień dostępu do sieci ustawienie jest ustawione na "kontrola dostępu za pośrednictwem zasad sieciowych NPS". Ten błąd może również wywołać, jeśli użytkownikowi nie przypisano licencji. |
| **REGISTRY_CONFIG_ERROR** | Brakuje klucza w rejestrze dla aplikacji, co może być spowodowane [skryptem programu PowerShell](howto-mfa-nps-extension.md#install-the-nps-extension) nie został uruchomiony po instalacji. Komunikat o błędzie powinien zawierać brakujący klucz. Upewnij się, że masz klucz w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Radius Request brak obowiązkowego atrybutu Radius userName\Identifier. Sprawdzanie, czy serwer NPS odbiera żądania USŁUGI RADIUS | Ten błąd zwykle odzwierciedla problem z instalacją. Rozszerzenie NPS musi być zainstalowane na serwerach NPS, które mogą odbierać żądania RADIUS. Serwery SERWERA NPS, które są zainstalowane jako zależności dla usług, takich jak RDG i RRAS, nie odbierają żądań promienia. Rozszerzenie NPS nie działa po zainstalowaniu przez takie instalacje i błędy, ponieważ nie można odczytać szczegóły z żądania uwierzytelniania. |
| **REQUEST_MISSING_CODE** | Upewnij się, że protokół szyfrowania haseł między serwerami NPS i NAS obsługuje używaną metodę uwierzytelniania pomocniczego. **Pap** obsługuje wszystkie metody uwierzytelniania usługi Azure MFA w chmurze: połączenie telefoniczne, jednokierunkową wiadomość tekstową, powiadomienie aplikacji mobilnej i kod weryfikacyjny aplikacji mobilnej. **CHAPV2** i **EAP** obsługują połączenia telefoniczne i powiadomienia o aplikacji mobilnej. |
| **USERNAME_CANONICALIZATION_ERROR** | Sprawdź, czy użytkownik jest obecny w lokalnym wystąpieniu usługi Active Directory i czy usługa NPS ma uprawnienia dostępu do katalogu. Jeśli korzystasz z relacji zaufania między lasami, [skontaktuj się z pomocą techniczną,](#contact-microsoft-support) aby uzyskać dalszą pomoc. |

### <a name="alternate-login-id-errors"></a>Błędy alternatywnego identyfikatora logowania

| Kod błędu | Komunikat o błędzie | Kroki rozwiązywania problemów |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Błąd: wyszukiwanie identyfikatorów użytkownikaObjectSid nie powiodło się | Sprawdź, czy użytkownik istnieje w lokalnym wystąpieniu usługi Active Directory. Jeśli korzystasz z relacji zaufania między lasami, [skontaktuj się z pomocą techniczną,](#contact-microsoft-support) aby uzyskać dalszą pomoc. |
| **ALTERNATE_LOGIN_ID_ERROR** | Błąd: wyszukiwanie alternatywnego identyfikatora logowania nie powiodło się | Sprawdź, czy LDAP_ALTERNATE_LOGINID_ATTRIBUTE jest ustawiona na [prawidłowy atrybut usługi Active Directory](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Jeśli LDAP_FORCE_GLOBAL_CATALOG jest ustawiona na True lub LDAP_LOOKUP_FORESTS jest skonfigurowany z wartością niepustą, sprawdź, czy skonfigurowano wykaz globalny i czy atrybut AlternateLoginId został do niego dodany. <br><br> Jeśli LDAP_LOOKUP_FORESTS jest skonfigurowany z wartością niepustą, sprawdź, czy wartość jest poprawna. Jeśli istnieje więcej niż jedna nazwa lasu, nazwy muszą być oddzielone średnikami, a nie spacjami. <br><br> Jeśli te kroki nie rozwiążą problemu, [skontaktuj się z pomocą techniczną,](#contact-microsoft-support) aby uzyskać więcej pomocy. |
| **ALTERNATE_LOGIN_ID_ERROR** | Błąd: wartość alternatywnego identyfikatora logowania jest pusta | Sprawdź, czy atrybut AlternateLoginId jest skonfigurowany dla użytkownika. |

## <a name="errors-your-users-may-encounter"></a>Błędy, które mogą napotkać użytkownicy

| Kod błędu | Komunikat o błędzie | Kroki rozwiązywania problemów |
| ---------- | ------------- | --------------------- |
| **Accessdenied** | Dzierżawa obiektu wywołującego nie ma uprawnień dostępu do uwierzytelniania dla użytkownika | Sprawdź, czy domena dzierżawy i domena głównej nazwy użytkownika (UPN) są takie same. Na przykład upewnij user@contoso.com się, że próbuje uwierzytelnić do dzierżawy Contoso. Nazwa UPN reprezentuje prawidłowego użytkownika dla dzierżawy na platformie Azure. |
| **AuthenticationMethodNotconed** | Określona metoda uwierzytelniania nie została skonfigurowana dla użytkownika | Z należy dodać lub zweryfikować metody weryfikacji zgodnie z instrukcjami w [sekcji Zarządzaj ustawieniami weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **UwierzytelnianieMetodNotSupportowane** | Określona metoda uwierzytelniania nie jest obsługiwana. | Zbierz wszystkie dzienniki, które zawierają ten błąd, i [skontaktuj się z pomocą techniczną](#contact-microsoft-support). Po skontaktowaniu się z pomocą techniczną podaj nazwę użytkownika i pomocniczą metodę weryfikacji, która wyzwoliła błąd. |
| **BecAccessDenied (BecAccessDenied)** | MSODS Bec wywołanie zwrócony dostęp odmówiono, prawdopodobnie nazwa użytkownika nie jest zdefiniowana w dzierżawie | Użytkownik jest obecny w usłudze Active Directory lokalnie, ale nie jest synchronizowany z usługą Azure AD przez usługę AD Connect. Lub użytkownik brakuje dzierżawy. Dodaj użytkownika do usługi Azure AD i poproś go o dodanie metod weryfikacji zgodnie z instrukcjami w sekcji [Zarządzaj ustawieniami weryfikacji dwuetapowej.](../user-help/multi-factor-authentication-end-user-manage-settings.md) |
| **Nieprawidłowy format lub** **strongauthenticationServiceInvalidParametr** | Numer telefonu jest w nierozpoznawalnym formacie | Niech użytkownik poprawi swoje numery telefonów weryfikacyjnych. |
| **Inwalidysesja** | Określona sesja jest nieprawidłowa lub mogła wygasnąć | Sesja zajęła ponad trzy minuty. Sprawdź, czy użytkownik wprowadza kod weryfikacyjny lub odpowiada na powiadomienie aplikacji w ciągu trzech minut od zainicjowania żądania uwierzytelnienia. Jeśli to nie rozwiąże problemu, sprawdź, czy nie ma opóźnień w sieci między klientem, serwerem NAS, serwerem NPS i punktem końcowym usługi Azure MFA.  |
| **Konfiguracja NoDefaultAuthenticationMethodIsConfigured** | Dla użytkownika nie skonfigurowano domyślnej metody uwierzytelniania | Z należy dodać lub zweryfikować metody weryfikacji zgodnie z instrukcjami w [sekcji Zarządzaj ustawieniami weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-manage-settings.md). Sprawdź, czy użytkownik wybrał domyślną metodę uwierzytelniania i skonfigurował tę metodę dla swojego konta. |
| **Kora Kodu OathCodePin** | Wprowadzony nieprawidłowy kod i pin. | Ten błąd nie jest oczekiwany w rozszerzeniu NPS. Jeśli użytkownik napotka na to, [skontaktuj się z pomocą techniczną,](#contact-microsoft-support) aby uzyskać pomoc dotyczącą rozwiązywania problemów. |
| **ProofDataNotFound (ProofDataNotFound)** | Dane próbne nie zostały skonfigurowane dla określonej metody uwierzytelniania. | Niech użytkownik wypróbuje inną metodę weryfikacji lub doda nową metodę weryfikacji zgodnie z instrukcjami w [sekcji Zarządzaj ustawieniami weryfikacji dwuetapowej.](../user-help/multi-factor-authentication-end-user-manage-settings.md) Jeśli użytkownik nadal widzi ten błąd po potwierdzenie, że jego metoda weryfikacji jest poprawnie skonfigurowana, [skontaktuj się z pomocą techniczną](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePiInowany** | Wprowadzony nieprawidłowy kod i pin. (OneWaysMs) | Ten błąd nie jest oczekiwany w rozszerzeniu NPS. Jeśli użytkownik napotka na to, [skontaktuj się z pomocą techniczną,](#contact-microsoft-support) aby uzyskać pomoc dotyczącą rozwiązywania problemów. |
| **TenantIsBlocked** | Dzierżawa jest zablokowana | [Skontaktuj się](#contact-microsoft-support) z pomocą techniczną za pomocą identyfikatora katalogu ze strony właściwości usługi Azure AD w witrynie Azure portal. |
| **UserNotFound (Nieukrycie użytkownika)** | Nie znaleziono określonego użytkownika | Dzierżawa nie jest już widoczna jako aktywna w usłudze Azure AD. Sprawdź, czy twoja subskrypcja jest aktywna i masz wymagane aplikacje pierwszej firmy. Upewnij się również, że dzierżawca w temacie certyfikatu jest zgodnie z oczekiwaniami, a certyfikat jest nadal ważny i zarejestrowany w ramach jednostki usługi. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Wiadomości, które użytkownicy mogą napotkać, które nie są błędami

Czasami użytkownicy mogą otrzymywać wiadomości z uwierzytelniania wieloskładnikowego, ponieważ ich żądanie uwierzytelnienia nie powiodło się. Nie są to błędy w produkcie konfiguracji, ale są zamierzone ostrzeżenia wyjaśniające, dlaczego żądanie uwierzytelniania zostało odrzucone.

| Kod błędu | Komunikat o błędzie | Zalecane czynności | 
| ---------- | ------------- | ----------------- |
| **Kora kodu OathCode** | Wprowadzony nieprawidłowy kod\Nieprawidłowy kod OATH | Użytkownik wprowadził niewłaściwy kod. Poproś ich o ponowną próbę, żądając nowego kodu lub logując się ponownie. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Osiągnięto maksymalną dozwoloną próbę ponawiania kodu | Użytkownik zbyt wiele razy nie powiódł się z wyzwaniem weryfikacji. W zależności od ustawień może być konieczne odblokowanie ich teraz przez administratora.  |
| **SMSAuthFailedWrongCodeEntered** | Wprowadzony nieprawidłowy kod/Wiadomość tekstowa OTP Nieprawidłowa | Użytkownik wprowadził niewłaściwy kod. Poproś ich o ponowną próbę, żądając nowego kodu lub logując się ponownie. |

## <a name="errors-that-require-support"></a>Błędy wymagające wsparcia

Jeśli napotkasz jeden z tych błędów, zalecamy [skontaktowanie się z pomocą techniczną](#contact-microsoft-support) w celu uzyskania pomocy diagnostycznej. Nie ma standardowego zestawu kroków, które mogą rozwiązać te błędy. Po skontaktowaniu się z pomocą techniczną należy podać jak najwięcej informacji o krokach, które doprowadziły do błędu i informacje o dzierżawie.

| Kod błędu | Komunikat o błędzie |
| ---------- | ------------- |
| **Nieprawidłowyparametr** | Żądanie nie może mieć wartości null |
| **Nieprawidłowyparametr** | ObjectId nie może mieć wartości null ani pustej dla replicationscope:{0} |
| **Nieprawidłowyparametr** | Długość nazwy firmy \{0}\ jest dłuższa niż maksymalna dozwolona długość{1} |
| **Nieprawidłowyparametr** | UserPrincipalName nie może mieć wartości null ani pustych |
| **Nieprawidłowyparametr** | Podany identyfikator dzierżawy nie jest w prawidłowym formacie |
| **Nieprawidłowyparametr** | Identyfikator sesji nie może mieć wartości null ani pusty |
| **Nieprawidłowyparametr** | Nie można rozpoznać żadnych ProofData z żądania lub Msods. ProofData jest unKnown |
| **InternalError** |  |
| **Kora Kodu OathCodePin** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Następne kroki

### <a name="troubleshoot-user-accounts"></a>Rozwiązywanie problemów z kontami użytkowników

Jeśli użytkownicy [mają problemy z weryfikacją dwuetapową,](../user-help/multi-factor-authentication-end-user-troubleshoot.md)pomóż im samodzielnie zdiagnozować problemy.

### <a name="health-check-script"></a>Skrypt sprawdzania kondycji

[Skrypt sprawdzania kondycji rozszerzenia serwera NPS usługi Azure mfa](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) wykonuje podstawową kontrolę kondycji podczas rozwiązywania problemów z rozszerzeniem serwera NPS. Uruchom skrypt i wybierz opcję 3.

### <a name="contact-microsoft-support"></a>Kontaktowanie się z pomocą techniczną firmy Microsoft

Jeśli potrzebujesz dodatkowej pomocy, skontaktuj się z pracownikiem pomocy technicznej za pośrednictwem [pomocy technicznej serwera uwierzytelniania wieloskładnikowego platformy Azure.](https://support.microsoft.com/oas/default.aspx?prid=14947) Kontaktując się z nami, warto, jeśli możesz podać jak najwięcej informacji o swoim problemie. Informacje, które można podać, obejmują stronę, na której był błąd, określony kod błędu, określony identyfikator sesji, identyfikator użytkownika, który widział błąd, oraz dzienniki debugowania.

Aby zebrać dzienniki debugowania do diagnostyki pomocy technicznej, użyj następujących kroków na serwerze NPS:

1. Otwórz Edytor rejestru i przejdź do HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa ustawione **VERBOSE_LOG** na **TRUE**
2. Otwórz wiersz polecenia Administrator i uruchom następujące polecenia:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Odtwórz problem

4. Zatrzymaj śledzenie za pomocą następujących poleceń:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Otwórz Edytor rejestru i przejdź do HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa ustawione **VERBOSE_LOG** na **FALSE**
6. Siąź zawartość folderu C:\NPS i dołącz spakowany plik do skrzynki pomocy technicznej.
