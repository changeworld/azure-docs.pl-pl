---
title: Rejestracja i logowanie za pomocą zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak wysyłać hasła jednorazowe w wiadomościach tekstowych do telefonów użytkowników aplikacji przy użyciu zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 96bf088e6db29817d1ae4276c254a84723e5d03d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480192"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Skonfiguruj konto i zaloguj się przy użyciu zasad niestandardowych w Azure AD B2C

Rejestracja i logowanie za pomocą telefonu w Azure Active Directory B2C (Azure AD B2C) umożliwia użytkownikom rejestrowanie się i logowanie do aplikacji przy użyciu hasła jednorazowego (OTP) wysyłanego w wiadomości tekstowej do telefonu. Hasła jednorazowe mogą pomóc zminimalizować ryzyko naruszenia lub naruszania haseł przez użytkowników.

Wykonaj kroki opisane w tym artykule, aby użyć zasad niestandardowych, aby umożliwić klientom rejestrowanie się i logowanie się do aplikacji przy użyciu hasła jednorazowego wysyłanego do telefonu.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md)
* [Aplikacja sieci Web zarejestrowana](tutorial-register-applications.md) w dzierżawie
* [Zasady niestandardowe](active-directory-b2c-get-started-custom.md) przekazane do dzierżawy

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Pobierz konto startowe & logowania za pomocą telefonu

Zacznij od aktualizacji niestandardowych plików zasad rejestracji i logowania na telefonie, aby współpracowały z dzierżawą Azure AD B2C.

W poniższych krokach przyjęto założenie, że zostały spełnione [wymagania wstępne](#prerequisites) i zostało już Sklonowane repozytorium [zasad niestandardowych][starter-pack] na komputerze lokalnym.

1. Znajdź [pliki zasad niestandardowych rejestracji i logowania na telefonie][starter-pack-phone] w lokalnym klonie repozytorium pakietu początkowego lub Pobierz je bezpośrednio. Pliki zasad XML znajdują się w następującym katalogu:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. W każdym pliku Zastąp ciąg `yourtenant` nazwą Azure AD B2C dzierżawy. Na przykład jeśli nazwa dzierżawy usługi B2C jest *contosob2c*, wszystkie wystąpienia `yourtenant.onmicrosoft.com` stają się `contosob2c.onmicrosoft.com`.

1. Wykonaj kroki opisane w sekcji [Dodawanie identyfikatorów aplikacji do zasad niestandardowych](active-directory-b2c-get-started-custom.md#add-application-ids-to-the-custom-policy) [w temacie Rozpoczynanie pracy z zasadami niestandardowymi w Azure Active Directory B2C](active-directory-b2c-get-started-custom.md). W takim przypadku należy zaktualizować `/phone-number-passwordless/` **`Phone_Email_Base.xml`** przy użyciu **identyfikatorów aplikacji (klienta)** dwóch aplikacji zarejestrowanych podczas kończenia wymagań wstępnych, *IdentityExperienceFramework* i *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Przekazywanie plików zasad

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do dzierżawy Azure AD B2C.
1. W obszarze **zasady**wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **Przekaż zasady niestandardowe**.
1. Przekaż pliki zasad w następującej kolejności:
    1. *Phone_Email_Base. XML*
    1. *SignUpOrSignInWithPhone. XML*
    1. *SignUpOrSignInWithPhoneOrEmail. XML*
    1. *ProfileEditPhoneOnly. XML*
    1. *ProfileEditPhoneEmail. XML*
    1. *ChangePhoneNumber. XML*
    1. *PasswordResetEmail. XML*

Podczas przekazywania każdego pliku platforma Azure dodaje prefiks `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. W obszarze **zasady niestandardowe**wybierz pozycję **B2C_1A_SignUpOrSignInWithPhone**.
1. W obszarze **Wybierz aplikację**wybierz aplikację *webapp1* , która została zarejestrowana przy wypełnianiu wymagań wstępnych.
1. W obszarze **adres URL odpowiedzi wybierz**opcję `https://jwt.ms`.
1. Wybierz pozycję **Uruchom teraz** i zarejestruj się przy użyciu adresu e-mail lub numeru telefonu.
1. Wybierz pozycję **Uruchom teraz** ponownie i zaloguj się przy użyciu tego samego konta, aby upewnić się, że konfiguracja jest poprawna.

## <a name="next-steps"></a>Następne kroki

W witrynie GitHub możesz znaleźć pakiet startowy zasad dotyczących rejestracji i logowania na telefonie.

[Azure-Samples/Active-Directory-B2C-Custom-Policy-starterpack/scenariuszach/numer telefonu — bezhasło][starter-pack-phone]

Pliki zasad pakietu startowego używają profilów technicznych usługi uwierzytelniania wieloskładnikowego i przekształceń numerów telefonów:

* [Zdefiniuj profil techniczny usługi Azure Multi-Factor Authentication](multi-factor-auth-technical-profile.md)
* [Definiowanie przekształceń oświadczeń numeru telefonu](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
