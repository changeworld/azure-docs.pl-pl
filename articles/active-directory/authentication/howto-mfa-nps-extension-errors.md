---
title: Rozwiązywanie problemów z kodami błędów rozszerzenia serwera NPS dla usługi Azure MFA — usługi Azure Active Directory
description: Uzyskaj pomoc w rozwiązywaniu problemów z rozszerzeniem serwera NPS dla usługi Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80ecf02a7e517300c41e84986659a66cfa11c90
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60414940"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Komunikatami o błędach z rozszerzenia serwera NPS dla usługi Azure Multi-Factor Authentication

Jeśli wystąpią błędy rozszerzenia serwera NPS dla usługi Azure Multi-Factor Authentication, należy użyć w tym artykule Aby szybciej dotrzeć do rozwiązania. Dzienników rozszerzenia serwera NPS znajdują się w Podglądzie zdarzeń w obszarze **widoki niestandardowe** > **ról serwera** > **usług zasad sieciowych i dostępu** na serwer, na którym zainstalowano rozszerzenia serwera NPS.

## <a name="troubleshooting-steps-for-common-errors"></a>Kroki rozwiązywania problemów dla typowych błędów

| Kod błędu | Kroki rozwiązywania problemów |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Skontaktuj się z działem pomocy technicznej](#contact-microsoft-support)i wymienić na liście kroków do zbierania dzienników. Podaj jak najwięcej informacji, jak to możliwe, o co się stało przed wystąpieniem błędu, w tym identyfikator dzierżawy i główna nazwa użytkownika (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Może to być problem z jak certyfikat klienta został zainstalowany lub skojarzony z dzierżawą. Postępuj zgodnie z instrukcjami w [Rozwiązywanie problemów z rozszerzenia serwera NPS dla usługi MFA](howto-mfa-nps-extension.md#troubleshooting) do badania problemów certyfikatu klienta. |
| **ESTS_TOKEN_ERROR** | Postępuj zgodnie z instrukcjami w [Rozwiązywanie problemów z rozszerzenia serwera NPS dla usługi MFA](howto-mfa-nps-extension.md#troubleshooting) do badania certyfikatu klienta i biblioteki ADAL token problemów. |
| **HTTPS_COMMUNICATION_ERROR** | Serwer NPS nie może otrzymywać odpowiedzi z usługi Azure MFA. Sprawdź, czy zapory są otwarte dwukierunkowo dla ruchu do i z https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Na serwerze, który uruchamia rozszerzenia serwera NPS, upewnij się, że może osiągnąć https://adnotifications.windowsazure.com i https://login.microsoftonline.com/. Jeśli te lokacje nie są ładowane, rozwiązywać problemy z łącznością, na tym serwerze. |
| **Rozszerzenia serwera NPS dla usługi Azure MFA:** <br> Rozszerzenia serwera NPS dla usługi Azure MFA wykonuje tylko dodatkowej uwierzytelnianie żądań usługi Radius w stanie AccessAccept. Żądanie dla użytkownika username ze stanem odpowiedzi AccessReject, ignorowanie żądania. | Ten błąd zazwyczaj odzwierciedla niepowodzenia uwierzytelniania w AD lub serwer NPS nie może otrzymywać odpowiedzi z usługi Azure AD. Sprawdź, czy zapory są otwarte dwukierunkowo dla ruchu do i z https://adnotifications.windowsazure.com i https://login.microsoftonline.com przy użyciu portu 80 i 443. Jest również ważne sprawdzić, czy na karcie DIAL-IN uprawnień dostępu do sieci, ustawienie ma wartość "kontroli dostępu za pomocą zasad sieciowych NPS". |
| **REGISTRY_CONFIG_ERROR** | Brak klucza w rejestrze dla aplikacji, która może być spowodowane [skrypt programu PowerShell](howto-mfa-nps-extension.md#install-the-nps-extension) nie można uruchomić po zakończeniu instalacji. Komunikat o błędzie powinien zawierać Brak klucza. Upewnij się, że masz klucza pod HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Brak obowiązkowego atrybutu userName\Identifier Radius żądanie usługi RADIUS. Sprawdź, czy odbiera żądań usługi RADIUS serwera NPS | Ten błąd zazwyczaj odzwierciedla problem z instalacją. Rozszerzenia serwera NPS musi być zainstalowany na serwerach NPS, które mogą odbierać żądań usługi RADIUS. Serwery NPS, które są zainstalowane jako zależności usług, takich jak brama usług pulpitu zdalnego i usługi RRAS nie odbierania żądań radius. Rozszerzenia serwera NPS nie działa podczas instalacji za pośrednictwem takiego instalacje i występuje, ponieważ nie może odczytać dane z żądania uwierzytelniania. |
| **REQUEST_MISSING_CODE** | Upewnij się, że Protokół szyfrowania hasła między serwerami zasad Sieciowych i NAS obsługuje dodatkowej metody uwierzytelniania używanej. **PAP** obsługuje wszystkie metody uwierzytelniania usługi Azure MFA w chmurze: połączenie telefoniczne, wiadomość tekstowa jednokierunkowe, powiadomienie aplikacji mobilnej i kod weryfikacyjny aplikacji mobilnej. **CHAPV2** i **EAP** obsługi połączeń telefonicznych i powiadomienia przez aplikacje mobilne. |
| **USERNAME_CANONICALIZATION_ERROR** | Sprawdź, czy użytkownik musi być obecny w Twoim wystąpieniu usługi Active Directory w środowisku lokalnym i czy usługa NPS ma uprawnienia do uzyskiwania dostępu do katalogu. Jeśli używasz relacje zaufania między lasami, [się z pomocą techniczną](#contact-microsoft-support) Aby uzyskać dalszą pomoc. |

### <a name="alternate-login-id-errors"></a>Błędy Identyfikatora logowania alternatywnej

| Kod błędu | Komunikat o błędzie | Kroki rozwiązywania problemów |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Błąd: userObjectSid wyszukiwania nie powiodło się | Sprawdź, czy użytkownik istnieje w Twoim wystąpieniu usługi Active Directory w środowisku lokalnym. Jeśli używasz relacje zaufania między lasami, [się z pomocą techniczną](#contact-microsoft-support) Aby uzyskać dalszą pomoc. |
| **ALTERNATE_LOGIN_ID_ERROR** | Błąd: Alternatywne wyszukiwania LoginId nie powiodło się | Sprawdź, czy ma ustawioną LDAP_ALTERNATE_LOGINID_ATTRIBUTE [prawidłowego katalogu active directory atrybut](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> LDAP_FORCE_GLOBAL_CATALOG jest ustawiona na wartość True lub LDAP_LOOKUP_FORESTS jest skonfigurowany z wartością niepustym, sprawdź skonfigurowano wykazu globalnego i że atrybut AlternateLoginId został dodany do niego. <br><br> Jeśli LDAP_LOOKUP_FORESTS jest skonfigurowany z wartością niepustym, sprawdź, czy wartość jest poprawna. Jeśli istnieje więcej niż jedną nazwę lasu, nazwy muszą być oddzielone średnikami, nie miejsca do magazynowania. <br><br> Jeśli te czynności nie rozwiąże problemu, [się z pomocą techniczną](#contact-microsoft-support) Aby uzyskać dalszą pomoc. |
| **ALTERNATE_LOGIN_ID_ERROR** | Błąd: Alternatywne LoginId wartość jest pusta | Sprawdź, czy atrybut AlternateLoginId została skonfigurowana dla użytkownika. |

## <a name="errors-your-users-may-encounter"></a>Mogą wystąpić błędy użytkowników

| Kod błędu | Komunikat o błędzie | Kroki rozwiązywania problemów |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Dzierżawa obiekt wywołujący nie ma uprawnień dostępu w celu uwierzytelniania użytkownika | Sprawdź, czy domena dzierżawy i domena główna nazwa użytkownika (UPN) są takie same. Na przykład, upewnij się, że user@contoso.com próby uwierzytelniania w dzierżawie Contoso. Nazwa UPN jest adresem prawidłowego użytkownika dzierżawcy na platformie Azure. |
| **AuthenticationMethodNotConfigured** | Metoda uwierzytelniania określonej nie zostało skonfigurowane dla użytkownika | Użytkownik powinien dodania lub sprawdzenia ich metod weryfikacji, zgodnie z instrukcjami w [Zarządzanie ustawieniami weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Metoda uwierzytelniania określonej nie jest obsługiwana. | Zbieranie wszystkich dzienników, które zawierają ten błąd, i [się z pomocą techniczną](#contact-microsoft-support). Gdy się z pomocą techniczną, podaj nazwę użytkownika i metodę dodatkowej weryfikacji, które go spowodowało błąd. |
| **BecAccessDenied** | Wywołanie MSODS Bec zwróciło odmowa dostępu, prawdopodobnie nazwa użytkownika nie jest zdefiniowany w ramach dzierżawy | Użytkownik znajduje się w usłudze Active Directory w środowisku lokalnym, ale nie jest synchronizowany do usługi Azure AD przez usługi AD Connect. Lub użytkownik nie istnieje dla dzierżawy. Dodaj użytkownika do usługi Azure AD i poprosić o dodanie ich metod weryfikacji, zgodnie z instrukcjami w go [Zarządzanie ustawieniami weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** lub **StrongAuthenticationServiceInvalidParameter** | Numer telefonu ma nierozpoznawalny format. | Należy mieć użytkownika poprawić swoje numery telefonów weryfikacji. |
| **InvalidSession** | Określonej sesji jest nieprawidłowy lub wygasł | Sesja zajęło więcej niż trzech minut. Sprawdź, czy użytkownik jest wprowadzenie kodu weryfikacyjnego lub odpowiada na powiadomienie aplikacji, w ciągu trzech minut inicjowania żądanie uwierzytelnienia. Jeśli to nie rozwiąże problemu, sprawdź, że nie istnieją żadne opóźnienia sieciowe między klientem, serwer NAS, serwer zasad Sieciowych i punktu końcowego usługi Azure MFA.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Nie domyślną metodą uwierzytelniania został skonfigurowany dla użytkownika | Użytkownik powinien dodania lub sprawdzenia ich metod weryfikacji, zgodnie z instrukcjami w [Zarządzanie ustawieniami weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-manage-settings.md). Sprawdź, czy użytkownik ma wybrać domyślną metodę uwierzytelniania i skonfigurować tej metody do swojego konta. |
| **OathCodePinIncorrect** | Nieprawidłowy kod i wprowadzono kodu pin. | Ten błąd nie jest oczekiwany w rozszerzenia serwera NPS. Jeśli użytkownik napotka w efekcie [się z pomocą techniczną](#contact-microsoft-support) pomocy w rozwiązywaniu problemów. |
| **ProofDataNotFound** | Dowód danych nie został skonfigurowany jako metodę uwierzytelniania określony. | Użytkownik, wypróbuj inną metodę weryfikacji lub dodać nowe metody weryfikacji, zgodnie z instrukcjami w [Zarządzanie ustawieniami weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-manage-settings.md). Jeśli użytkownik w dalszym ciągu wyświetlony ten błąd, po potwierdzeniu, że metody weryfikacji jest skonfigurowane prawidłowo, [się z pomocą techniczną](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Nieprawidłowy kod i wprowadzono kodu pin. (OneWaySMS) | Ten błąd nie jest oczekiwany w rozszerzenia serwera NPS. Jeśli użytkownik napotka w efekcie [się z pomocą techniczną](#contact-microsoft-support) pomocy w rozwiązywaniu problemów. |
| **TenantIsBlocked** | Dzierżawa jest zablokowany. | [Skontaktuj się z działem pomocy technicznej](#contact-microsoft-support) identyfikatorem katalogu na stronie właściwości usługi Azure AD w witrynie Azure portal. |
| **UserNotFound** | Nie można odnaleźć określonego użytkownika | Dzierżawca nie jest już widoczna jako aktywny w usłudze Azure AD. Sprawdź, czy Twoja subskrypcja jest aktywna i czy masz wymagane firmy aplikacje. Upewnij się również dzierżawy w podmiocie certyfikatu jest zgodnie z oczekiwaniami, a certyfikat jest nadal ważne i zarejestrowanych w ramach jednostki usługi. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Wiadomości, że użytkownicy mogą wystąpić, które nie są błędy

Czasami użytkownicy mogą być wyświetlane komunikaty z usługi Multi-Factor Authentication, ponieważ jego żądania uwierzytelnienia nie powiodło się. Te artykuły nie są błędy w produkcie konfiguracji, ale są zamierzone ostrzeżenia wyjaśniający, dlaczego żądanie uwierzytelnienia zostało odrzucone.

| Kod błędu | Komunikat o błędzie | Zalecane czynności | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Nieprawidłowy kod entered\OATH niepoprawny kod | Użytkownik wprowadził nieprawidłowy kod. Niech spróbuje skorzystać z ponownie przez zażądanie nowego kodu lub zalogować się ponownie. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Maksymalny dozwolony kodu ponawiania osiągnięty | Użytkownik nie powiodło się żądanie weryfikacji zbyt wiele razy. W zależności od ustawień może muszą teraz odblokowany przez administratora.  |
| **SMSAuthFailedWrongCodeEntered** | Problem kodu/tekstu wprowadzanego niepoprawna OTP wiadomości | Użytkownik wprowadził nieprawidłowy kod. Niech spróbuje skorzystać z ponownie przez zażądanie nowego kodu lub zalogować się ponownie. |

## <a name="errors-that-require-support"></a>Błędy, które wymagają obsługi

Jeśli wystąpi jeden z tych błędów, zalecamy możesz [się z pomocą techniczną](#contact-microsoft-support) diagnostyczne pomocy. Nie ma żadnych standardowy zestaw czynności, które można rozwiązać te błędy. W przypadku skontaktuj się z działem pomocy technicznej, należy uwzględnić jak najwięcej informacji jak to możliwe, o krokach, które spowodowały błąd i informacje o Twojej dzierżawy.

| Kod błędu | Komunikat o błędzie |
| ---------- | ------------- |
| **InvalidParameter** | Żądanie nie może mieć wartości null |
| **InvalidParameter** | Identyfikator obiektu nie może być wartość null lub jest pusta dla ReplicationScope:{0} |
| **InvalidParameter** | Długość CompanyName \{0} \ przekracza maksymalną dozwoloną długość {1} |
| **InvalidParameter** | UserPrincipalName nie może być zerowy lub pusty |
| **InvalidParameter** | Podany identyfikator dzierżawy nie jest w niepoprawnym formacie. |
| **InvalidParameter** | Identyfikator sesji nie może być zerowy lub pusty |
| **InvalidParameter** | Nie można rozpoznać wszelkie ProofData z żądania lub Msods. ProofData jest nieznany |
| **Błąd wewnętrzny** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Kolejne kroki

### <a name="troubleshoot-user-accounts"></a>Rozwiązywanie problemów z kont użytkowników

Jeśli użytkownicy znajdują się [występują problemy z weryfikacji dwuetapowej](../user-help/multi-factor-authentication-end-user-troubleshoot.md), pomóc w ich własnym diagnozowanie problemów.

### <a name="contact-microsoft-support"></a>Skontaktuj się z pomocą techniczną firmy Microsoft

Jeśli potrzebujesz dodatkowej pomocy, skontaktuj się z pracownikiem pomocy technicznej za pośrednictwem [pomocy technicznej usługi Azure Multi-Factor Authentication](https://support.microsoft.com/oas/default.aspx?prid=14947). Podczas nawiązywania kontaktu z nami, jest przydatne, Jeśli dołączysz jak najwięcej informacji o problemie, jak to możliwe. Możesz podać informacje dotyczące strony, w którym wystąpił błąd, kod błędu Identyfikatora określonej sesji, identyfikator użytkownika, który wystąpił błąd, dzienników i debugowania.

Aby zbierać dzienniki debugowania dla diagnostyki pomocy technicznej, należy użyć następujące czynności na serwerze zasad Sieciowych:

1. Otwórz Edytor rejestru i przejdź do zestawu HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa **VERBOSE_LOG** do **TRUE**
2. Otwórz wiersz polecenia administratora i uruchom następujące polecenia:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Odtwórz problem

4. Zatrzymaj śledzenie przy użyciu następujących poleceń:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Otwórz Edytor rejestru i przejdź do zestawu HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa **VERBOSE_LOG** do **FALSE**
6. Zawartość folderu C:\NPS zip i dołączyć plik zip do zgłoszenia do pomocy technicznej.
