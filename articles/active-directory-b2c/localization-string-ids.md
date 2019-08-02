---
title: Identyfikatory ciągów lokalizacji — Azure Active Directory B2C | Microsoft Docs
description: Określ identyfikatory dla definicji zawartości z identyfikatorem API. signuporsignin w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: da1390de4e2eb0624032dc490416e7b6e5d61baa
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "67846849"
---
# <a name="localization-string-ids"></a>Identyfikatory ciągów lokalizacji

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **Lokalizacja** umożliwia obsługę wielu ustawień regionalnych lub języków w zasadach dla podróży użytkownika. Ten artykuł zawiera listę identyfikatorów lokalizacji, których można użyć w ramach zasad. Aby zapoznać się z lokalizacją interfejsu użytkownika, zobacz [Lokalizacja](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Elementy strony do rejestracji lub logowania

Następujące identyfikatory są używane dla definicji zawartości o IDENTYFIKATORze `api.signuporsignin`.

| id | Wartość domyślna |
| -- | ------------- |
| **local_intro_email** | Zaloguj się za pomocą istniejącego konta |
| **logonIdentifier_email** | Adres e-mail |
| **requiredField_email** | Wprowadź swój adres e-mail |
| **invalid_email** | Wprowadź prawidłowy adres e-mail |
| **email_pattern** | ^ [a-za-Z0-9.! # $% & "" *+/=? ^ _\`{\|} ~-] + @ [a-za-Z0-9-] + (?:\\. [ a-za-Z0-9-] +)* $ |
| **local_intro_username** | Zaloguj się przy użyciu swojej nazwy użytkownika |
| **logonIdentifier_username** | Nazwa użytkownika |
| **requiredField_username** | Wprowadź swoją nazwę użytkownika |
| **Hasło** | Hasło |
| **requiredField_password** | Wprowadź swoje hasło |
| **invalid_password** | Wprowadzone hasło ma nieoczekiwany format. |
| **forgotpassword_link** | Nie pamiętasz hasła? |
| **createaccount_intro** | Nie masz jeszcze konta? |
| **createaccount_link** | Zarejestruj się teraz |
| **divider_title** | LUB |
| **cancel_message** | Użytkownik zapomniał hasła |
| **button_signin** | Logowanie |
| **social_intro** | Zaloguj się za pomocą swojego konta społecznościowego |
  **remember_me** |Nie wylogowuj mnie|
| **unknown_error** | Mamy problemy z zalogowaniem się. Spróbuj ponownie później. |

Poniższy przykład pokazuje użycie niektórych elementów interfejsu użytkownika na stronie rejestracji lub logowania:

![Elementy środowiska użytkownika dotyczące rejestracji lub logowania](./media/localization-string-ids/localization-susi.png)

Identyfikator dostawców tożsamości jest konfigurowany w elemencie **ClaimsExchange** podróży użytkownika. Aby zlokalizować tytuł dostawcy tożsamości, **ElementType** ma wartość `ClaimsProvider`, a **StringId** jest `ClaimsExchange`ustawiony na identyfikator.

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

Poniższy przykład lokalizuje dostawcę tożsamości w serwisie Facebook do języka arabskiego:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Komunikaty o błędach rejestracji lub logowania

| id | Wartość domyślna |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Twoje hasło jest nieprawidłowe. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Nie możemy znaleźć Twojego konta. |
| **UserMessageIfOldPasswordUsed** | Prawdopodobnie używasz starego hasła. |
| **DefaultMessage** | Nieprawidłowa nazwa użytkownika lub nieprawidłowe hasło. |
| **UserMessageIfUserAccountDisabled** | Twoje konto zostało zablokowane. Skontaktuj się z pomocą techniczną, aby ją odblokować, a następnie spróbuj ponownie. |
| **UserMessageIfUserAccountLocked** | Twoje konto jest tymczasowo zablokowane, aby zapobiec nieautoryzowanemu użyciu. Spróbuj ponownie później. |
| **AADRequestsThrottled** | W tej chwili jest zbyt wiele żądań. Poczekaj chwilę i spróbuj ponownie. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Elementy interfejsu użytkownika dotyczące rejestracji i samopotwierdzonej strony

Poniżej przedstawiono identyfikatory dla definicji zawartości z identyfikatorem `api.localaccountsignup` lub dowolną definicją zawartości, która `api.selfasserted`rozpoczyna się od, takich jak `api.selfasserted.profileupdate` i `api.localaccountpasswordreset`.

| id | Wartość domyślna |
| -- | ------------- |
| **ver_sent** | Kod weryfikacyjny został wysłany na adres: |
| **ver_but_default** | Domyślny |
| **cancel_message** | Użytkownik anulował wprowadzanie niepotwierdzonych informacji |
| **preloader_alt** | Czekaj |
| **ver_but_send** | Wyślij kod weryfikacyjny |
| **alert_yes** | Tak |
| **error_fieldIncorrect** | Co najmniej jedno pole jest wypełniane nieprawidłowo. Sprawdź swoje wpisy i spróbuj ponownie. |
| **czteroletniego** | Rok |
| **verifying_blurb** | Poczekaj na przetworzenie Twoich informacji. |
| **button_cancel** | Cancel |
| **ver_fail_no_retry** | Wykonano zbyt wiele nieudanych prób. Spróbuj ponownie później. |
| **bieżącym** | Miesiąc |
| **ver_success_msg** | Zweryfikowano adres E-mail. Teraz można kontynuować. |
| **months** | styczeń, luty, marzec, kwiecień, maj, czerwiec, lipiec, sierpień, wrzesień, październik, listopad, grudzień |
| **ver_fail_server** | Wystąpił problem podczas weryfikowania Twojego adresu e-mail. Wprowadź prawidłowy adres e-mail i spróbuj ponownie. |
| **error_requiredFieldMissing** | Brak wymaganego pola. Wypełnij wszystkie wymagane pola i spróbuj ponownie. |
| **initial_intro** | Podaj następujące szczegóły. |
| **ver_but_resend** | Wyślij nowy kod |
| **button_continue** | Create |
| **error_passwordEntryMismatch** | Pola wprowadzania hasła nie są zgodne. Wprowadź to samo hasło w obu polach i spróbuj ponownie. |
| **ver_incorrect_format** | Niepoprawny format. |
| **ver_but_edit** | Zmień adres e-mail |
| **ver_but_verify** | Zweryfikuj kod |
| **alert_no** | Nie |
| **ver_info_msg** | Kod weryfikacyjny został wysłany do Twojej skrzynki odbiorczej. Skopiuj ją do poniższego pola wejściowego. |
| **dzień** | Dzień |
| **ver_fail_throttled** | Zbyt wiele żądań do zweryfikowania tego adresu e-mail. Zaczekaj chwilę, a następnie spróbuj ponownie. |
| **helplink_text** | Co to jest? |
| **ver_fail_retry** | Ten kod jest nieprawidłowy. Spróbuj ponownie. |
| **alert_title** | Anuluj wprowadzanie swoich danych |
| **required_field** | Te informacje są wymagane. |
| **alert_message** | Czy na pewno chcesz anulować wprowadzanie danych? |
| **ver_intro_msg** | Konieczna jest weryfikacja. Kliknij przycisk Wyślij. |
| **ver_input** | Kod weryfikacyjny |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Komunikaty o błędach dotyczące rejestracji i niepotwierdzonych stron

| id | Wartość domyślna |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Użytkownik o określonym IDENTYFIKATORze już istnieje. Wybierz inną. |
| **UserMessageIfClaimNotVerified** | Nie zweryfikowano zgłoszenia:{0} |
| **UserMessageIfIncorrectPattern** | Nieprawidłowy wzorzec dla:{0} |
| **UserMessageIfMissingRequiredElement** | Brak wymaganego elementu:{0} |
| **UserMessageIfValidationError** | Błąd weryfikacji przez:{0} |
| **UserMessageIfInvalidInput** | {0}ma nieprawidłowe dane wejściowe. |
| **ServiceThrottled** | W tej chwili jest zbyt wiele żądań. Poczekaj chwilę i spróbuj ponownie. |

W poniższym przykładzie pokazano użycie niektórych elementów interfejsu użytkownika na stronie rejestracji:

![Strona rejestracji z nazwami elementów interfejsu użytkownika z etykietą](./media/localization-string-ids/localization-sign-up.png)

Poniższy przykład pokazuje użycie niektórych elementów interfejsu użytkownika na stronie rejestracji, po kliknięciu przycisku Wyślij kod weryfikacyjny przez użytkownika:

![Strona rejestracji — elementy środowiska użytkownika weryfikacji wiadomości e-mail](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Elementy interfejsu użytkownika na stronie uwierzytelnianie w ramach wskaźnika telefonu

Poniżej przedstawiono identyfikatory dla definicji zawartości o IDENTYFIKATORze `api.phonefactor`.

| id | Wartość domyślna |
| -- | ------------- |
| **button_verify** | Zadzwoń do mnie |
| **country_code_label** | Numer kierunkowy kraju |
| **cancel_message** | Użytkownik anulował uwierzytelnianie wieloskładnikowe |
| **text_button_send_second_code** | wyślij nowy kod |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Mamy dla Ciebie następujący numer rekordu. Możemy wysłać kod za pośrednictwem wiadomości SMS lub telefonu w celu uwierzytelnienia użytkownika. |
| **intro_mixed_p** | W rekordach są dostępne następujące numery. Wybierz numer telefonujący lub Wyślij kod za pośrednictwem wiadomości SMS w celu uwierzytelnienia użytkownika. |
| **button_verify_code** | Zweryfikuj kod |
| **requiredField_code** | Wprowadź otrzymany kod weryfikacyjny |
| **invalid_code** | Wprowadź otrzymany 6-cyfrowy kod |
| **button_cancel** | Cancel |
| **local_number_input_placeholder_text** | Numer telefonu |
| **button_retry** | Ponawianie próby |
| **alternative_text** | Nie mam swojego telefonu |
| **intro_phone_p** | W rekordach są dostępne następujące numery. Wybierz numer telefonu, z którym możemy się uwierzytelnić. |
| **intro_phone** | Mamy dla Ciebie następujący numer rekordu. Będziemy telefonować. |
| **enter_code_text_intro** | Wprowadź kod weryfikacyjny poniżej lub  |
| **intro_entry_phone** | Wprowadź poniżej numer, na który możemy zadzwonić, aby Cię uwierzytelnić. |
| **intro_entry_sms** | Wprowadź poniżej numer, na który możemy wysłać wiadomość SMS z kodem, aby Cię uwierzytelnić. |
| **button_send_code** | Wyślij kod |
| **invalid_number** | Wprowadź prawidłowy numer telefonu |
| **intro_sms** | Mamy dla Ciebie następujący numer rekordu. Wyślemy kod za pośrednictwem wiadomości SMS w celu uwierzytelnienia użytkownika. |
| **intro_entry_mixed** | Wprowadź poniżej numer, na który możemy wysłać wiadomość SMS z kodem lub zadzwonić, aby Cię uwierzytelnić. |
| **number_pattern** | ^\\+ (?: [0-9] [\\x20-]?) {6,14}[0-9] $ |
| **intro_sms_p** |W rekordach są dostępne następujące numery. Wybierz numer, który możemy wysłać kod za pośrednictwem wiadomości SMS w celu uwierzytelnienia użytkownika. |
| **requiredField_countryCode** | Wybierz kod kraju |
| **requiredField_number** | Wprowadź numer telefonu |
| **country_code_input_placeholder_text** |Kraj lub region |
| **number_label** | Numer telefonu |
| **error_tryagain** | Podany numer telefonu jest zajęty lub niedostępny. Sprawdź numer i spróbuj ponownie. |
| **error_incorrect_code** | Wprowadzony kod weryfikacyjny nie jest zgodny z naszymi rekordami. Spróbuj ponownie lub zażądaj nowego kodu. |
| **countryList** | {\"Domyślny\":kraj/\"region,AF\":\"Afganistan,\"AX:Åland\"\"\"\"\" Wyspy\",\"Al:\"Albania\",Dz\":Algieria,AS\":\"\"\"\"\"\" Samoa\"amerykańskie\",\"AD:\"Andora,\"Ao:\"Angoli,AI\":\"\"\"\" \"Anguilla,\"AQ\":Antarktyda\",AG: Antigua iBarbuda\",\"\"\"\"\"\"AR\":Argentyna\",\"am:\"Armenia, aw:\"Aruba\"\"\"\"\" \",Au\":\"Australia,\"o:Austria,\"\"\"\"\"\" AZ\":\"Azerbejdżan,\"BS\":Bahamy\",BH:\"\"\"\"\" Bahrajn\",\"BD:\"Bangladesz\",BB\":Barbados,\"\"\"\"\" Według\":\"Białoruś,\"to\":Belgia\",BZ:\"\"\"\"\" Belize\",\"BJ:\"Benin\",BM\":Bermudy,\"\"\"\"\"BT\":Bhutan\",\"bo:\"Boliwia,BQ:\"\"\"\"\"\" Bonaire\",\"ba:Bośnia\"iHercegowina\",\"BW:Botswany\"\"\"<span class="notransla class=""></span class="notransla> Odległe wyspy\",\"VI\":\"Stany Zjednoczone \"Wyspy Dziewicze,\"g\":Uganda\",\"ua: Ukraina, AE:\"\"\"\"\"\"\" Zjednoczone Emiraty Arabskie\",\"GB\":ZjednoczoneKrólestwo\",USA:\"Stany Zjednoczone,\"uy\"\"\"\" \":Urugwaj\",\"uz:\"Uzbekistan, VU:Vanuatu\",\"\"\"\"\"\" \"VA:\"Watykan\",ve\":Wenezuela,VN\": Wietnam\"\"\"\"\"\" \",WF\":Wallisi\"Futuna,Ye:\"Jemen,\"zm\"\"\"\"\"\":Zambii,\"zw:Zimbabwe\"}\"\"\"\" |
| **error_448** | Podany numer telefonu jest nieosiągalny. |
| **error_449** | Użytkownik przekroczył liczbę ponownych prób. |
| **verification_code_input_placeholder_text** | Kod weryfikacyjny |

W poniższym przykładzie pokazano użycie niektórych elementów interfejsu użytkownika na stronie rejestracji usługi MFA:

![Strona rejestracji — elementy środowiska użytkownika weryfikacji wiadomości e-mail](./media/localization-string-ids/localization-mfa1.png)

Poniższy przykład pokazuje użycie niektórych elementów interfejsu użytkownika na stronie walidacji usługi MFA:

![Strona rejestracji — elementy środowiska użytkownika weryfikacji wiadomości e-mail](./media/localization-string-ids/localization-mfa2.png)







