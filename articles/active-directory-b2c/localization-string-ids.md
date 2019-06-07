---
title: Lokalizacja ciągu identyfikatorów — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Określ identyfikatory dla definicji zawartości o identyfikatorze api.signuporsignin w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 41a72013f1538b0a857c76bc949a7109e1cd54b4
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510920"
---
# <a name="localization-string-ids"></a>Lokalizacja ciągu identyfikatorów

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Lokalizacji** elementu pozwala na obsługę wielu ustawień regionalnych lub języków w ramach zasad dla podróży użytkownika. Ten artykuł zawiera listę lokalizacji identyfikatory, które można użyć w zasadach. Aby zapoznać się z lokalizacją interfejsu użytkownika, zobacz [lokalizacji](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Elementy strony rejestracji lub logowania

Następujące identyfikatory są używane na potrzeby definicji zawartości o identyfikatorze `api.signuporsignin`.

| ID | Wartość domyślna |
| -- | ------------- |
| **local_intro_email** | Zaloguj się przy użyciu istniejącego konta |
| **logonIdentifier_email** | Adres e-mail |
| **requiredField_email** | Wprowadź swój adres e-mail |
| **invalid_email** | Wprowadź prawidłowy adres e-mail |
| **email_pattern** | ^ [a-zA-Z0-9.! #$% & " *+/ =? ^ _\`{\|} ~-]+@[a-zA-Z0-9-]+ (?:\\. [ -zA-Z0 - 9-] +)* $ |
| **local_intro_username** | Zaloguj się przy użyciu nazwy użytkownika |
| **logonIdentifier_username** | Nazwa użytkownika |
| **requiredField_username** | Wprowadź nazwę użytkownika |
| **Hasło** | Hasło |
| **requiredField_password** | Wprowadź hasło |
| **invalid_password** | Wprowadzone hasło nie jest w oczekiwanym formacie. |
| **forgotpassword_link** | Nie pamiętasz hasła? |
| **createaccount_intro** | Nie masz jeszcze konta? |
| **createaccount_link** | Utwórz konto teraz |
| **divider_title** | LUB |
| **cancel_message** | Użytkownik zapomniał hasła |
| **button_signin** | Logowanie |
| **social_intro** | Zaloguj się przy użyciu konta społecznościowego |
  **remember_me** |Nie wylogowuj mnie|
| **unknown_error** | Mamy problem z logowaniem. Spróbuj ponownie później. |

Poniższy przykład przedstawia użycie niektórych użytkownika elementów interfejsu w stronę tworzenia konta lub logowania:

![Elementy interfejsu użytkownika strony rejestracji lub logowania](./media/localization-string-ids/localization-susi.png)

Identyfikator dostawcy tożsamości jest skonfigurowany w podróży użytkownika **elementu ClaimsExchange** elementu. Aby zlokalizować tytuł dostawcy tożsamości **ElementType** jest ustawiona na `ClaimsProvider`, podczas gdy **StringId** jest ustawiona na identyfikator `ClaimsExchange`.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Poniższy przykład lokalizuje dostawcy tożsamości usługi Facebook na arabski:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Komunikaty o błędach rejestracji lub logowania

| ID | Wartość domyślna |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Hasło jest niepoprawne. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Firma Microsoft nie wiem, jak Znajdź swoje konto. |
| **UserMessageIfOldPasswordUsed** | Wygląda na to użyte stare hasło. |  
| **DefaultMessage** | Nieprawidłowa nazwa użytkownika lub hasło. |  
| **UserMessageIfUserAccountDisabled** | Twoje konto zostało zablokowane. Skontaktuj się z pracownikiem pomocy technicznej do jego odblokowania, a następnie spróbuj ponownie. |  
| **UserMessageIfUserAccountLocked** | Twoje konto zostało tymczasowo zablokowane, aby zapobiec nieautoryzowanemu użyciu. Spróbuj ponownie później. |  
| **AADRequestsThrottled** | W tej chwili istnieje zbyt wiele żądań. Poczekaj przez pewien czas i spróbuj ponownie. |  

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Zarejestruj się i self określonych stron elementy interfejsu użytkownika

Poniżej przedstawiono identyfikatorów definicji zawartości o identyfikatorze `api.localaccountsignup` lub definicji zawartości, która rozpoczyna się od `api.selfasserted`, takich jak `api.selfasserted.profileupdate` i `api.localaccountpasswordreset`.

| ID | Wartość domyślna |
| -- | ------------- |
| **ver_sent** | Kod weryfikacyjny została wysłana do: |
| **ver_but_default** | Domyślne |
| **cancel_message** | Użytkownik anulował, wprowadzając informacje samodzielnie |
| **preloader_alt** | Czekaj |
| **ver_but_send** | Wyślij kod weryfikacyjny |
| **alert_yes** | Tak |
| **error_fieldIncorrect** | Co najmniej jednego pola są wypełniane niepoprawnie. Sprawdź wpisy i spróbuj ponownie. |
| **Rok** | Rok |
| **verifying_blurb** | Poczekaj na przetworzenie informacji. |
| **button_cancel** | Cancel |
| **ver_fail_no_retry** | Wprowadzono zbyt wiele prób niepoprawne. Spróbuj ponownie później. |
| **Miesiąc** | Miesiąc |
| **ver_success_msg** | Adres e-mail został zweryfikowany. Teraz można kontynuować. |
| **months** | Stycznia, lutego i marca, kwietnia, może czerwca, lipca, sierpnia, września, października, listopada, grudnia |
| **ver_fail_server** | Mamy problem z weryfikacją swój adres e-mail. Wprowadź prawidłowy adres e-mail i spróbuj ponownie. |
| **error_requiredFieldMissing** | Brak wymaganego pola. Wypełnij wszystkie wymagane pola i spróbuj ponownie. |
| **initial_intro** | Podaj następujące informacje. |
| **ver_but_resend** | Wyślij nowy kod |
| **button_continue** | Przycisk Utwórz |
| **error_passwordEntryMismatch** | Pola wprowadzania hasła są niezgodne. Wprowadź to samo hasło w obu polach, a następnie spróbuj ponownie. |
| **ver_incorrect_format** | Niepoprawny format. |
| **ver_but_edit** | Zmień adres e-mail |
| **ver_but_verify** | Sprawdź kod |
| **alert_no** | Nie |
| **ver_info_msg** | Kod weryfikacyjny został wysłany do Twojej skrzynki odbiorczej. Skopiuj go do pola wejściowego poniżej. |
| **dzień** | Dzień |
| **ver_fail_throttled** | Było zbyt wiele żądań, aby zweryfikować tego adresu e-mail. Poczekaj chwilę, a następnie spróbuj ponownie. |
| **helplink_text** | Co to jest? |
| **ver_fail_retry** | Ten kod jest nieprawidłowy. Spróbuj ponownie. |
| **alert_title** | Anulowanie wprowadzania swoje dane |
| **required_field** | Te informacje są wymagane. |
| **alert_message** | Czy na pewno chcesz anulować, wprowadzając szczegóły? |
| **ver_intro_msg** | Weryfikacja jest to konieczne. Kliknij przycisk Wyślij. |
| **ver_input** | Kod weryfikacyjny |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Zarejestruj się i self określonych stron komunikaty o błędach

| ID | Wartość domyślna |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Użytkownik o określonym identyfikatorze już istnieje. Wybierz inną. |
| **UserMessageIfClaimNotVerified** | Oświadczenia nie zweryfikować: {0} |
| **UserMessageIfIncorrectPattern** | Nieprawidłowy wzorzec dla: {0} |
| **UserMessageIfMissingRequiredElement** | Brak wymaganego elementu: {0} |
| **UserMessageIfValidationError** | Błąd podczas sprawdzania poprawności przez: {0} |
| **UserMessageIfInvalidInput** | {0} zawiera nieprawidłowe dane wejściowe. |
| **ServiceThrottled** | W tej chwili istnieje zbyt wiele żądań. Poczekaj przez pewien czas i spróbuj ponownie. |

Poniższy przykład pokazuje użycie niektórych elementów interfejsu użytkownika na stronie tworzenia konta:

![Elementy interfejsu użytkownika strony rejestracji](./media/localization-string-ids/localization-sign-up.png)

Poniższy przykład pokazuje użycie niektórych elementów interfejsu użytkownika na stronie tworzenia konta, po kliknięciu przez użytkownika Wyślij przycisk kod weryfikacyjny:

![Elementy interfejsu użytkownika weryfikacji wiadomości e-mail stronę rejestracji](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Telefon elementy interfejsu użytkownika strony uwierzytelnianie wieloskładnikowe

Poniżej przedstawiono identyfikatorów definicji zawartości o identyfikatorze `api.phonefactor`. 

| ID | Wartość domyślna |
| -- | ------------- |
| **button_verify** | Zadzwoń do mnie |
| **country_code_label** | Numer kierunkowy kraju |
| **cancel_message** | Użytkownik anulował uwierzytelnianie wieloskładnikowe |
| **text_button_send_second_code** | Wyślij nowy kod |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Mamy dla Ciebie następujący ciąg liczb w rekordzie. Firma Microsoft może wysłać kod za pomocą wiadomości SMS lub przez telefon do uwierzytelniania. |
| **intro_mixed_p** | Mamy dla Ciebie następujące numery w rekordzie. Wybierz numer, że firma Microsoft telefonu lub Wyślij kod za pomocą wiadomości SMS do uwierzytelniania. |
| **button_verify_code** | Sprawdź kod |
| **requiredField_code** | Wprowadź otrzymany kod weryfikacyjny |
| **invalid_code** | Wprowadź 6-cyfrowy kod, który otrzymałeś |
| **button_cancel** | Cancel |
| **local_number_input_placeholder_text** | Numer telefonu |
| **button_retry** | Ponawianie próby |
| **alternative_text** | Nie mam telefonu |
| **intro_phone_p** | Mamy dla Ciebie następujące numery w rekordzie. Wybierz numer, który firma Microsoft może telefon do uwierzytelniania. |
| **intro_phone** | Mamy dla Ciebie następujący ciąg liczb w rekordzie. Firma Microsoft będzie telefon do uwierzytelniania. |
| **enter_code_text_intro** | Wprowadź kod weryfikacyjny poniżej, lub  |
| **intro_entry_phone** | Wprowadź numer poniżej, firma Microsoft może telefon do uwierzytelniania. |
| **intro_entry_sms** | Wprowadź numer poniżej, że możemy wysłać kod za pomocą wiadomości SMS do uwierzytelniania. |
| **button_send_code** | Wyślij kod |
| **invalid_number** | Wprowadź prawidłowy numer telefonu |
| **intro_sms** | Mamy dla Ciebie następujący ciąg liczb w rekordzie. Wyślemy kod za pomocą wiadomości SMS do uwierzytelniania. |
| **intro_entry_mixed** | Wprowadź numer poniżej, możemy wysłać kod za pomocą wiadomości SMS lub telefon do uwierzytelniania. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Mamy dla Ciebie następujące numery w rekordzie. Wybierz liczbę, możemy wysłać kod za pomocą wiadomości SMS do uwierzytelniania. |
| **requiredField_countryCode** | Wybierz kod kraju |
| **requiredField_number** | Wprowadź numer telefonu |
| **country_code_input_placeholder_text** |Kraj lub region |
| **number_label** | Numer telefonu |
| **error_tryagain** | Numer telefonu, które podałeś jest zajęty lub niedostępny. Sprawdź numer i spróbuj ponownie. |
| **error_incorrect_code** | Kod weryfikacyjny, który został wprowadzony jest niezgodna z posiadanymi przez nas danymi. Spróbuj ponownie lub poproś o nowy kod. |
| **countryList** | {\"Domyślne\":\"Kraj/Region\",\"AF\":\"Afganistan\",\"AX\":\"Alandzkie Wyspy\",\"AL\":\"Albania\",\"DZ\":\"Algieria\",\"AS\":\" Samoa Amerykańskie\",\"AD\":\"Andora\",\"AO\":\"Angola\",\"sztucznej Inteligencji\": \"Anguilla\",\"AQ\":\"Antarktyda\",\"AG\":\"Antigua i Barbuda\",\"AR\":\"Argentyny\",\"AM\":\"Armenia\",\"AW\":\"Aruba \",\"AU\":\"Australii\",\"na\":\"Austria\",\" AZ\":\"Azerbejdżan\",\"BS\":\"Bahamy\",\"BH\":\" Bahrajn\",\"BD\":\"Bangladesz\",\"BB\":\"Barbados\",\" PRZEZ\":\"Białoruś\",\"BE\":\"Belgia\",\"BZ\":\" Belize\",\"BJ\":\"Benin\",\"BM\":\"Bermudy\",\"BT\":\"Bhutan\",\"BO\":\"Boliwia\",\"BQ\":\" Bonaire\",\"BA\":\"Bośnia i Hercegowina\",\"BW\":\"Botswany<span class="notransla class=""></span class="notransla> Odległe Mniejsze Wyspy\",\"VI\":\"Stanów Zjednoczonych Wyspy Dziewicze\",\"g\":\"ugandyjski\",\"UA\":\"Ukraina\",\"AE\":\" Zjednoczone Emiraty Arabskie\",\"GB\":\"Zjednoczonego Królestwa\",\"USA\":\"Stanów Zjednoczonych\",\"UY \":\"Urugwaj\",\"UZ\":\"Uzbekistan\",\"VU\":\"Vanuatu\", \"Oceny luk w zabezpieczeniach\":\"Watykan\",\"VE\":\"Wenezuela\",\"VN\":\"Wietnam \",\"WF\":\"Wallis i Futuna\",\"YE\":\"Jemen\",\"ZM\":\"Zambia\",\"ZW\":\"Zimbabwe\"} |
| **error_448** | Podany numer telefonu jest nieosiągalny. |
| **error_449** | Użytkownik przekroczył liczbę ponownych prób. |
| **verification_code_input_placeholder_text** | Kod weryfikacyjny |

Poniższy kod przedstawia użycie niektórych elementów interfejsu użytkownika na stronie rejestracji usługi MFA:

![Elementy interfejsu użytkownika weryfikacji wiadomości e-mail stronę rejestracji](./media/localization-string-ids/localization-mfa1.png)

Poniższy kod przedstawia użycie niektórych elementów interfejsu użytkownika na stronie Sprawdzanie poprawności usługi MFA:

![Elementy interfejsu użytkownika weryfikacji wiadomości e-mail stronę rejestracji](./media/localization-string-ids/localization-mfa2.png)







