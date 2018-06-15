---
title: Rozwiązywanie problemów z kodów błędów dla rozszerzenia usługi Azure MFA NPS | Dokumentacja firmy Microsoft
description: Uzyskaj pomoc dotyczącą rozwiązywania problemów z rozszerzeniem NPS uwierzytelnianie wieloskładnikowe Azure
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 07/14/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: aa140bceb5f7ad5e638f747fa8d88803c27f02a3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33867669"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Komunikatami o błędach z rozszerzenia serwera NPS uwierzytelnianie wieloskładnikowe Azure

Jeśli wystąpią błędy z rozszerzeniem NPS uwierzytelnianie wieloskładnikowe Azure umożliwia szybsze osiągnięcie rozwiązania w tym artykule. 

## <a name="troubleshooting-steps-for-common-errors"></a>Kroki rozwiązywania problemów dotyczących typowych błędów

| Kod błędu | Kroki rozwiązywania problemów |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Skontaktuj się z pomocą techniczną](#contact-microsoft-support)i opisz lista czynności do zbierania dzienników. Podaj te informacje, które można o co się stało przed wystąpieniem błędu, w tym identyfikator dzierżawcy i główna nazwa użytkownika (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Może to być problem z jak certyfikat klienta został zainstalowany lub skojarzony z dzierżawą. Postępuj zgodnie z instrukcjami [Rozwiązywanie problemów z rozszerzenia serwera NPS MFA](howto-mfa-nps-extension.md#troubleshooting) do badania problemów certyfikatu klienta. |
| **ESTS_TOKEN_ERROR** | Postępuj zgodnie z instrukcjami [Rozwiązywanie problemów z rozszerzenia serwera NPS MFA](howto-mfa-nps-extension.md#troubleshooting) do sprawdzania, czy certyfikat klienta i ADAL token problemów. |
| **HTTPS_COMMUNICATION_ERROR** | Nie można odebrać odpowiedzi z usługi Azure MFA jest serwer NPS. Sprawdź, czy zapory są otwarte dwukierunkowo dla ruchu do i z https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Na serwerze, na którym działa serwer NPS rozszerzenia, sprawdź, czy można osiągnąć https://adnotifications.windowsazure.com i https://login.microsoftonline.com/. Jeśli te lokacje nie są ładowane, rozwiązywania problemów z łącznością na tym serwerze. |
| **REGISTRY_CONFIG_ERROR** | Brak klucza w rejestrze dla aplikacji, która może być spowodowane [skrypt programu PowerShell](howto-mfa-nps-extension.md#install-the-nps-extension) nie został wykonany po instalacji. Komunikat o błędzie powinna zawierać Brak klucza. Upewnij się, że masz klucz w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Brak obowiązkowego atrybutu userName\Identifier promień żądania RADIUS. Sprawdź, czy serwer NPS jest odbierania żądań RADIUS | Ten błąd zazwyczaj odzwierciedla problemu. Rozszerzenia serwera zasad Sieciowych muszą być zainstalowane w serwerach NPS, które mogą odbierać żądań RADIUS. Serwery NPS, które są zainstalowane jako zależności dla usług takich jak brama usług pulpitu zdalnego i usługa RRAS nie odbierania żądań radius. Rozszerzenia serwera NPS nie działa, gdy zainstalowany na takie instalacje i błędy out, ponieważ nie można odczytać szczegółów z żądania uwierzytelniania. |
| **REQUEST_MISSING_CODE** | Upewnij się, że hasło szyfrowania protokołu między serwery NPS i NAS obsługuje dodatkowej metody uwierzytelniania używany. **PAP** obsługuje wszystkie metody uwierzytelniania usługi Azure MFA w chmurze: połączenie telefoniczne, wiadomość tekstowa jednokierunkowe powiadomienie aplikacji mobilnej i kod weryfikacyjny z aplikacji mobilnej. **CHAPV2** i **EAP** obsługi połączeń telefonicznych i powiadomienia aplikacji mobilnej. |
| **USERNAME_CANONICALIZATION_ERROR** | Sprawdź, czy użytkownik znajduje się w lokalnym wystąpieniem usługi Active Directory i że usługi serwera NPS ma uprawnienia dostępu do katalogu. Jeśli używane są relacje zaufania między lasami, [się z pomocą techniczną](#contact-microsoft-support) Aby uzyskać dalszą pomoc. |


   

### <a name="alternate-login-id-errors"></a>Alternatywnego Identyfikatora błędów

| Kod błędu | Komunikat o błędzie | Kroki rozwiązywania problemów |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Błąd: userObjectSid wyszukiwania nie powiodło się | Sprawdź, czy użytkownik istnieje w lokalnym wystąpieniu usługi Active Directory. Jeśli używane są relacje zaufania między lasami, [się z pomocą techniczną](#contact-microsoft-support) Aby uzyskać dalszą pomoc. |
| **ALTERNATE_LOGIN_ID_ERROR** | Błąd: Wyszukiwanie alternatywny LoginId nie powiodło się. | Sprawdź, czy wartość jest LDAP_ALTERNATE_LOGINID_ATTRIBUTE [atrybut prawidłowe usługi active directory](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> LDAP_FORCE_GLOBAL_CATALOG ma wartość True lub LDAP_LOOKUP_FORESTS jest skonfigurowany z niepustą wartość, sprawdź skonfigurowano wykazu globalnego oraz że atrybut AlternateLoginId został dodany do niego. <br><br> Jeśli LDAP_LOOKUP_FORESTS jest skonfigurowana z wartością niepustym, sprawdź, czy wartość jest poprawna. Jeśli istnieje więcej niż jedną nazwę lasu, nazwy muszą być oddzielone średnikami, nie spacji. <br><br> Jeśli te czynności nie rozwiąże problemu, [się z pomocą techniczną](#contact-microsoft-support) Aby uzyskać dalszą pomoc. |
| **ALTERNATE_LOGIN_ID_ERROR** | Błąd: Alternatywne LoginId wartość jest pusta | Sprawdź, czy atrybut AlternateLoginId jest skonfigurowane dla użytkownika. |


## <a name="errors-your-users-may-encounter"></a>Mogą wystąpić błędy użytkowników

| Kod błędu | Komunikat o błędzie | Kroki rozwiązywania problemów |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Dzierżawy obiekt wywołujący nie ma uprawnień dostępu do uwierzytelniania użytkownika | Sprawdź, czy domena dzierżawy i domena główna nazwa użytkownika (UPN) są takie same. Na przykład, upewnij się, że user@contoso.com próby uwierzytelnienia dzierżawie Contoso. Nazwa UPN jest adresem prawidłowego użytkownika dzierżawcy na platformie Azure. |
| **AuthenticationMethodNotConfigured** | Podanej metody uwierzytelniania nie zostało skonfigurowane dla użytkownika | Użytkownik dodania lub sprawdzenia ich metod weryfikacji, zgodnie z instrukcjami w [zarządzać ustawieniami na potrzeby weryfikacji dwuetapowej](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Podanej metody uwierzytelniania nie jest obsługiwana. | Zbieraj wszystkie dzienniki zawierające ten błąd i [się z pomocą techniczną](#contact-microsoft-support). Kontakt z pomocą techniczną, podaj nazwę użytkownika i metodę dodatkowej weryfikacji, które go spowodowało błąd. |
| **BecAccessDenied** | Wywołanie MSODS Bec zwróciło odmowa dostępu, prawdopodobnie nazwa użytkownika nie jest zdefiniowany w dzierżawie | Użytkownik znajduje się w lokalnym usługi Active Directory, ale nie jest synchronizowany do usługi Azure AD przez AD Connect. Lub użytkownik nie istnieje dla dzierżawcy. Dodaj użytkownika do usługi Azure AD i je dodać ich metod weryfikacji, zgodnie z instrukcjami w [zarządzać ustawieniami na potrzeby weryfikacji dwuetapowej](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** lub **StrongAuthenticationServiceInvalidParameter** | Numer telefonu ma nierozpoznawalny format | Ma użytkownika, popraw swoje numery telefonów weryfikacji. |
| **InvalidSession** | Określona sesja jest nieprawidłowa lub mogło wygasnąć | Sesja jest zajęta więcej niż trzy minuty, aby zakończyć. Sprawdź, czy użytkownik jest wprowadzenie kodu weryfikacyjnego lub odpowiada na powiadomienie aplikacji, w ciągu trzech minut inicjowania żądania uwierzytelnienia. Jeśli to nie rozwiąże problemu, sprawdź, czy nie występują żadne opóźnień sieci między klienta, serwer NAS, serwer zasad Sieciowych i punktu końcowego usługi Azure MFA.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Dla użytkownika nie skonfigurowano żadnych domyślną metodą uwierzytelniania | Użytkownik dodania lub sprawdzenia ich metod weryfikacji, zgodnie z instrukcjami w [zarządzać ustawieniami na potrzeby weryfikacji dwuetapowej](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md). Sprawdź, czy użytkownik ma wybrany domyślną metodą uwierzytelniania i skonfigurowane tej metody dla swojego konta. |
| **OathCodePinIncorrect** | Nieprawidłowy kod i wprowadzony numer pin. | Ten błąd nie jest oczekiwany w rozszerzeniu zasad Sieciowych. Jeśli użytkownikowi napotkał, [się z pomocą techniczną](#contact-microsoft-support) dla pomocy w rozwiązywaniu problemów. |
| **ProofDataNotFound** | Dowód danych nie została skonfigurowana dla podanej metody uwierzytelniania. | Użytkownik, spróbuj innej metody weryfikacji, lub Dodaj nowych metod weryfikacji zgodnie z instrukcjami w [zarządzać ustawieniami na potrzeby weryfikacji dwuetapowej](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md). Jeśli użytkownik będzie nadal występować wyświetlić ten błąd, po potwierdzeniu, że ich metodę weryfikacji jest prawidłowo skonfigurowany, [się z pomocą techniczną](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Nieprawidłowy kod i wprowadzony numer pin. (OneWaySMS) | Ten błąd nie jest oczekiwany w rozszerzeniu zasad Sieciowych. Jeśli użytkownikowi napotkał, [się z pomocą techniczną](#contact-microsoft-support) dla pomocy w rozwiązywaniu problemów. |
| **TenantIsBlocked** | Dzierżawy jest zablokowany. | [Skontaktuj się z pomocą techniczną](#contact-microsoft-support) o identyfikatorze katalogu na stronie właściwości usługi Azure AD w portalu Azure. |
| **UserNotFound** | Nie można odnaleźć określonego użytkownika | Dzierżawca nie jest już widoczna jako aktywny w usłudze Azure AD. Sprawdź, czy Twoja subskrypcja jest aktywna i czy masz wymagane najpierw strony aplikacji. Upewnij się również jest dostępna dzierżawa w podmiocie certyfikatu zgodnie z oczekiwaniami, a certyfikat jest nadal prawidłowa i zarejestrowanych w obszarze nazwy głównej usługi. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Komunikaty dotyczące użytkowników mogą wystąpić, które nie są błędy

Czasami użytkownicy mogą pobrać wiadomości z usługi Multi-Factor Authentication, ponieważ żądanie ich uwierzytelnianie nie powiodło się. Te nie są błędy w produkcie konfiguracji, ale są zamierzone ostrzeżenia wyjaśniający, dlaczego żądanie uwierzytelniania zostało odrzucone.

| Kod błędu | Komunikat o błędzie | Zalecane czynności | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Nieprawidłowy kod entered\OATH niepoprawny kod | To nie jest błąd, użytkownik wprowadził nieprawidłowy kod. | Użytkownik wprowadził nieprawidłowy kod. Niech ponów żądanie o nowy kod lub zaloguj się ponownie. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Osiągnięto ponawiania maksymalny dozwolony kodu | Użytkownik nie powiodło się żądanie weryfikacji zbyt wiele razy. W zależności od ustawień może muszą teraz zostać odblokowany przez administratora.  |
| **SMSAuthFailedWrongCodeEntered** | Problem kodu wprowadzona/tekstu nieprawidłową OTP wiadomości | Użytkownik wprowadził nieprawidłowy kod. Niech ponów żądanie o nowy kod lub zaloguj się ponownie. |

## <a name="errors-that-require-support"></a>Błędy, które wymagają obsługi

Jeśli wystąpią jeden z tych błędów, zaleca się możesz [się z pomocą techniczną](#contact-microsoft-support) diagnostycznego pomocy. Brak nie standardowy zestaw czynności rozwiązujących te błędy. Kontakt z pomocą techniczną, należy uwzględnić jako dużej ilości informacji jak to możliwe o czynnościach, które spowodowały błąd, a informacje o Twojej dzierżawy.

| Kod błędu | Komunikat o błędzie |
| ---------- | ------------- |
| **InvalidParameter** | Żądanie nie może mieć wartości null |
| **InvalidParameter** | Identyfikator obiektu nie może być zerowe ani puste dla ReplicationScope:{0} |
| **InvalidParameter** | Długość NazwaFirmy \{0} \ przekracza maksymalną dozwoloną długość {1} |
| **InvalidParameter** | UserPrincipalName nie może być zerowy lub pusty |
| **InvalidParameter** | Podanego identyfikatora dzierżawcy nie znajduje się w poprawnym formacie |
| **InvalidParameter** | Identyfikator sesji nie może być zerowy lub pusty |
| **InvalidParameter** | Nie można rozpoznać żadnych ProofData z żądania lub Msods. ProofData jest nieznany |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Kolejne kroki

### <a name="troubleshoot-user-accounts"></a>Rozwiązywanie problemów z kontami użytkownika

Jeśli użytkownicy są [wystąpił problem w trakcie weryfikacji dwuetapowej](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-troubleshoot.md), pomóc w ich własnym diagnozowanie problemów. 

### <a name="contact-microsoft-support"></a>Skontaktuj się z pomocą techniczną firmy Microsoft

Jeśli potrzebujesz dodatkowej pomocy, skontaktuj się z pracownikiem pomocy technicznej za pośrednictwem [obsługi serwera usługi Azure Multi-Factor Authentication](https://support.microsoft.com/oas/default.aspx?prid=14947). Podczas kontaktowania się z nami, jest przydatne, jeśli te informacje mogą obejmować o problem jak to możliwe. Możesz podać informacje zawiera strony, na którym był wyświetlany błąd, kod błędu identyfikator określonej sesji, identyfikator użytkownika, który był wyświetlany błąd, dzienników i debugowania.

Do zbierania dzienników debugowania dla diagnostyki pomocy technicznej, wykonaj następujące kroki na serwerze zasad Sieciowych:

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
6. Pliku zip zawartość folderu C:\NPS i Dołącz plik zip do sprawę pomocy technicznej.


