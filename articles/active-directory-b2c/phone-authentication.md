---
title: Rejestracja telefonu i logowanie się za pomocą zasad niestandardowych (wersja zapoznawcza)
titleSuffix: Azure AD B2C
description: Wysyłanie haseł jednorazowych (OTP) w wiadomościach tekstowych do telefonów użytkowników aplikacji z niestandardowymi zasadami w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eadac0e973b361b1fdee63dcc9cfa848a0b2bacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183962"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Konfigurowanie rejestracji telefonu i logowanie się za pomocą zasad niestandardowych w usłudze Azure AD B2C (wersja zapoznawcza)

Rejestracja telefonu i logowanie się w usłudze Azure Active Directory B2C (Azure AD B2C) umożliwia użytkownikom rejestrowanie się i logowanie do aplikacji przy użyciu hasła jednorazowego (OTP) wysłanego w wiadomości tekstowej na telefon. Hasła jednorazowe mogą zminimalizować ryzyko, że użytkownicy zapomną lub zdejmują ze sobą hasła.

Wykonaj kroki opisane w tym artykule, aby użyć zasad niestandardowych, aby umożliwić klientom rejestrację i logowanie się do aplikacji przy użyciu jednorazowego hasła wysłanego na ich telefon.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Cennik

Hasła jednorazowe są wysyłane do użytkowników za pomocą wiadomości TEKSTOWYCH SMS i mogą być naliczane opłaty za każdą wysłaną wiadomość. Aby uzyskać informacje o cenach, zobacz sekcję **Oddzielne opłaty** w [cenniku B2C usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem protokołu OTP należy wprowadzić następujące zasoby.

* [Dzierżawa usługi Azure AD B2C](tutorial-create-tenant.md)
* [Aplikacja sieci Web zarejestrowana](tutorial-register-applications.md) w dzierżawie
* [Zasady niestandardowe](custom-policy-get-started.md) przesłane do dzierżawy

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Pobierz pakiet startowy rejestracji telefonu & logowanie

Zacznij od aktualizacji plików zasad rejestracji telefonu i logowania do pracy z dzierżawą usługi Azure AD B2C.

W [poniższych krokach](#prerequisites) założono, że zostały ukończone wymagania wstępne i zostały już sklonowane repozytorium [pakietów startowych zasad niestandardowych][starter-pack] do komputera lokalnego.

1. Znajdź [pliki zasad niestandardowych rejestracji telefonu i logowania w][starter-pack-phone] lokalnym klonie repozytorium pakietu startowego lub pobierz je bezpośrednio. Pliki zasad XML znajdują się w następującym katalogu:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. W każdym pliku zastąp ciąg `yourtenant` nazwą dzierżawy usługi Azure AD B2C. Na przykład, jeśli nazwa dzierżawy B2C jest *contosob2c*, wszystkie wystąpienia `yourtenant.onmicrosoft.com` stają się `contosob2c.onmicrosoft.com`.

1. Wykonaj kroki opisane w sekcji [Dodawanie identyfikatorów aplikacji do](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) sekcji Zasady niestandardowe w sekcji Wprowadzenie do zasad [niestandardowych w usłudze Azure Active Directory B2C](custom-policy-get-started.md). W takim przypadku `/phone-number-passwordless/` **`Phone_Email_Base.xml`** należy zaktualizować **identyfikatory aplikacji (klienta)** dwóch aplikacji zarejestrowanych podczas wypełniania wymagań wstępnych, *IdentityExperienceFramework* i *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Przekazywanie plików zasad

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do dzierżawy usługi Azure AD B2C.
1. W obszarze **Zasady**wybierz pozycję **Identity Experience Framework**.
1. Wybierz **pozycję Przekaż zasady niestandardowe**.
1. Przekaż pliki zasad w następującej kolejności:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfilEditPhoneOnly.xml*
    1. *ProfilEditPhoneEmail.xml*
    1. *Zmień numer telefonu.xml*
    1. *Plik PasswordResetEmail.xml*

Podczas przekazywania każdego pliku platforma `B2C_1A_`Azure dodaje prefiks .

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. W **obszarze Zasady niestandardowe**wybierz **pozycję B2C_1A_SignUpOrSignInWithPhone**.
1. W obszarze **Wybierz aplikację**wybierz aplikację *webapp1* zarejestrowaną podczas wypełniania wymagań wstępnych.
1. W obszarze Wybierz `https://jwt.ms`adres URL **odpowiedzi**wybierz pozycję .
1. Wybierz **pozycję Uruchom teraz** i zarejestruj się przy użyciu adresu e-mail lub numeru telefonu.
1. Wybierz **uruchom teraz ponownie** i zaloguj się przy tym samym koncie, aby potwierdzić, że masz prawidłową konfigurację.

## <a name="get-user-account-by-phone-number"></a>Pobierz konto użytkownika według numeru telefonu

Użytkownik, który zarejestruje się przy pomocy numeru telefonu, ale nie podaje odzyskiwania adresu e-mail jest rejestrowany w katalogu usługi Azure AD B2C z ich numer telefonu jako nazwę logowania. Jeśli użytkownik chce zmienić swój numer telefonu, twój zespół pomocy technicznej lub zespół pomocy technicznej musi najpierw znaleźć swoje konto, a następnie zaktualizować swój numer telefonu.

Użytkownik można znaleźć pod jego numerem telefonu (nazwa logowania) za pomocą [programu Microsoft Graph:](manage-user-accounts-graph-api.md)

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Przykład:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Następne kroki

Możesz znaleźć pakiet startowy rejestracji telefonu i logowania się na zasadach (i inne pakiety startowe) w Serwisie GitHub:

[Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless Azure-][starter-pack-phone]

Pliki zasad pakietu startowego używają wieloskładnikowych profili technicznych i przekształceń oświadczeń dotyczących numerów telefonów:

* [Definiowanie profilu technicznego uwierzytelniania wieloskładnikowego platformy Azure](multi-factor-auth-technical-profile.md)
* [Definiowanie przekształceń oświadczeń dotyczących numerów telefonów](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
