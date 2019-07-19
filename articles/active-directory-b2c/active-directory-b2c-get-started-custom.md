---
title: Wprowadzenie do zasad niestandardowych — Azure Active Directory B2C
description: Dowiedz się, jak rozpocząć pracę z zasadami niestandardowymi w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 83269a5ae0d2e5fb7ae2651dbc27926c910a0e03
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302488"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Wprowadzenie do zasad niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Zasady niestandardowe](active-directory-b2c-overview-custom.md) to pliki konfiguracji, które definiują zachowanie dzierżawy usługi Azure Active Directory (Azure AD) B2C. W tym artykule opisano tworzenie niestandardowych zasad, które obsługują rejestrację lub logowanie do konta lokalnego przy użyciu adresu e-mail i hasła. Możesz również przygotować środowisko do dodawania dostawców tożsamości.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli jeszcze tego nie zrobiono, należy [utworzyć dzierżawę Azure AD B2C](tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.
- [Zarejestruj swoją aplikację](tutorial-register-applications.md) w utworzonej dzierżawie, aby mogła ona komunikować się z Azure AD B2C.

## <a name="add-signing-and-encryption-keys"></a>Dodaj klucze podpisywania i szyfrowania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. Kliknij pozycję **katalog i subskrypcja** w górnym menu, a następnie wybierz katalog, który zawiera dzierżawcę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.

### <a name="create-the-signing-key"></a>Utwórz klucz podpisywania

1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
2. W obszarze **Opcje**wybierz `Generate`opcję.
3. W polu **Nazwa**wprowadź `TokenSigningKeyContainer`. Prefiks `B2C_1A_` może zostać dodany automatycznie.
4. W obszarze **Typ klucza**wybierz pozycję **RSA**.
5. W obszarze **użycie klucza**wybierz pozycję **podpis**.
6. Kliknij przycisk **Utwórz**.

### <a name="create-the-encryption-key"></a>Utwórz klucz szyfrowania

1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
2. W obszarze **Opcje**wybierz `Generate`opcję.
3. W polu **Nazwa**wprowadź `TokenEncryptionKeyContainer`. Prefiks `B2C_1A`_ może zostać dodany automatycznie.
4. W obszarze **Typ klucza**wybierz pozycję **RSA**.
5. W obszarze **użycie klucza**wybierz pozycję **szyfrowanie**.
6. Kliknij przycisk **Utwórz**.

### <a name="create-the-facebook-key"></a>Tworzenie klucza Facebook

Jeśli masz już [klucz tajny aplikacji w serwisie Facebook](active-directory-b2c-setup-fb-app.md), Dodaj go jako klucz zasad do dzierżawy. W przeciwnym razie należy utworzyć klucz z wartością zastępczą, aby zasady przechodziły do walidacji.

1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
2. W obszarze **Opcje**wybierz `Manual`opcję.
3. W obszarze **Nazwa**wprowadź `FacebookSecret`. Prefiks `B2C_1A_` może zostać dodany automatycznie.
4. W **kluczu tajnym**wprowadź klucz tajny serwisu `0` Facebook z Developers.Facebook.com lub jako symbol zastępczy. Ta wartość jest kluczem tajnym, a nie IDENTYFIKATORem aplikacji.
5. W obszarze **użycie klucza**wybierz pozycję **podpis**.
6. Kliknij przycisk **Utwórz**.

## <a name="register-identity-experience-framework-applications"></a>Rejestrowanie aplikacji platformy obsługi tożsamości

Azure AD B2C wymaga zarejestrowania dwóch aplikacji, które są używane do rejestrowania i logowania użytkowników: IdentityExperienceFramework (aplikacja sieci Web) i ProxyIdentityExperienceFramework (aplikacja natywna) z delegowanym uprawnieniem z aplikacji IdentityExperienceFramework. Konta lokalne istnieją tylko w Twojej dzierżawie. Użytkownicy rejestrują się przy użyciu unikatowej kombinacji adresu e-mail/hasła w celu uzyskania dostępu do aplikacji zarejestrowanych przez dzierżawcę.

### <a name="register-the-identityexperienceframework-application"></a>Rejestrowanie aplikacji IdentityExperienceFramework

1. Zaznacz **wszystkie usługi** w lewym górnym rogu Azure Portal.
1. W polu wyszukiwania wpisz `Azure Active Directory`.
1. W wynikach wyszukiwania wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** w menu po lewej stronie wybierz pozycję **rejestracje aplikacji (starsza wersja)** .
1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
1. W obszarze **Nazwa**wprowadź `IdentityExperienceFramework`.
1. W obszarze **Typ aplikacji**wybierz pozycję **aplikacja sieci Web/interfejs API**.
1. W obszarze **adres URL logowania**wpisz `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, gdzie `your-tenant-name` to Azure AD B2C nazwa domeny dzierżawy. Wszystkie adresy URL powinny teraz używać [b2clogin.com](b2clogin.md).
1. Kliknij przycisk **Utwórz**. Po jego utworzeniu Skopiuj identyfikator aplikacji i Zapisz go do późniejszego użycia.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Rejestrowanie aplikacji ProxyIdentityExperienceFramework

1. W **rejestracje aplikacji (starsza wersja)** wybierz pozycję **rejestracja nowej aplikacji**.
2. W obszarze **Nazwa**wprowadź `ProxyIdentityExperienceFramework`.
3. W obszarze **Typ aplikacji**wybierz opcję **natywny**.
4. W obszarze **Identyfikator URI przekierowania**wpisz `your-tenant-name` `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, gdzie jest dzierżawcą Azure AD B2C.
5. Kliknij przycisk **Utwórz**. Po jego utworzeniu Skopiuj identyfikator aplikacji i Zapisz go do późniejszego użycia.
6. Na stronie Ustawienia wybierz pozycję **wymagane uprawnienia**, a następnie wybierz pozycję **Dodaj**.
7. Wybierz **pozycję Wybierz interfejs API**, Wyszukaj i wybierz pozycję **IdentityExperienceFramework**, a następnie kliknij pozycję **Wybierz**.
9. Zaznacz pole wyboru obok pozycji **dostęp do IdentityExperienceFramework**, kliknij pozycję **Wybierz**, a następnie kliknij pozycję **gotowe**.
10. Wybierz pozycję **Udziel uprawnień**, a następnie potwierdź, wybierając pozycję **tak**.

## <a name="custom-policy-starter-pack"></a>Pakiet startowy zasad niestandardowych

Zasady niestandardowe są zestawem plików XML przekazywanym do dzierżawy Azure AD B2C, aby zdefiniować profile techniczne i przedziały użytkowników. Udostępniamy pakiety początkowe z kilkoma wstępnie utworzonymi zasadami, aby szybko rozpocząć pracę. Każdy z tych pakietów początkowych zawiera najmniejszą liczbę profilów technicznych i podróży użytkowników, które są konieczne do osiągnięcia opisanych scenariuszy:

- **LocalAccounts** — umożliwia korzystanie tylko z kont lokalnych.
- **SocialAccounts** — umożliwia korzystanie tylko z kont społecznościowych (lub federacyjnych).
- **SocialAndLocalAccounts** — umożliwia korzystanie z kont lokalnych i społecznościowych.
- **SocialAndLocalAccountsWithMFA** — umożliwia korzystanie z opcji uwierzytelniania społecznościowego, lokalnego i wieloskładnikowego.

Każdy początkowy pakiet zawiera:

- **Plik podstawowy** — do podstawy wymagane są kilka modyfikacji. Przykład: *TrustFrameworkBase. XML*
- **Plik rozszerzenia** — ten plik jest miejscem, w którym wprowadzane są większość zmian konfiguracji. Przykład: *TrustFrameworkExtensions. XML*
- **Pliki jednostek uzależnionych** — pliki specyficzne dla zadania wywoływane przez aplikację. Przykłady: *SignUpOrSignin. XML*, *ProfileEdit. XML*, *PasswordReset. XML*

W tym artykule opisano edytowanie niestandardowych plików zasad XML w pakiecie **SocialAndLocalAccounts** Starter. Jeśli potrzebujesz edytora XML, wypróbuj [Visual Studio Code](https://code.visualstudio.com/download), lekki Edytor Międzyplatformowy.

### <a name="get-the-starter-pack"></a>Pobierz pakiet startowy

Pobierz pakiety początkowe dla zasad niestandardowych z usługi GitHub, a następnie zaktualizuj pliki XML w pakiecie SocialAndLocalAccounts Starter przy użyciu nazwy dzierżawy Azure AD B2C.

1. [Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) lub Sklonuj repozytorium:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. We wszystkich plikach w katalogu **SocialAndLocalAccounts** Zastąp ciąg `yourtenant` nazwą dzierżawy Azure AD B2C.

    Na przykład jeśli nazwa dzierżawy usługi B2C jest *contosotenant*, wszystkie wystąpienia `yourtenant.onmicrosoft.com` stają się `contosotenant.onmicrosoft.com`dostępne.

### <a name="add-application-ids-to-the-custom-policy"></a>Dodawanie identyfikatorów aplikacji do zasad niestandardowych

Dodaj identyfikatory aplikacji do pliku rozszerzeń *TrustFrameworkExtensions. XML*.

1. Otwórz `SocialAndLocalAccounts/` `<TechnicalProfile Id="login-NonInteractive">`i **Znajdźelement.`TrustFrameworkExtensions.xml`**
1. Zastąp oba wystąpienia `IdentityExperienceFrameworkAppId` z identyfikatorem aplikacji utworzonej wcześniej aplikacji IdentityExperienceFramework.
1. Zastąp oba wystąpienia `ProxyIdentityExperienceFrameworkAppId` z identyfikatorem aplikacji utworzonej wcześniej aplikacji ProxyIdentityExperienceFramework.
1. Zapisz plik.

## <a name="upload-the-policies"></a>Przekazywanie zasad

1. Wybierz element menu **Struktura środowiska tożsamości** w dzierżawie B2C w Azure Portal.
1. Wybierz pozycję **Przekaż zasady niestandardowe**.
1. W tej kolejności należy przekazać pliki zasad:
    1. *TrustFrameworkBase. XML*
    1. *TrustFrameworkExtensions. XML*
    1. *SignUpOrSignin. XML*
    1. *ProfileEdit. XML*
    1. *PasswordReset. XML*

Podczas przekazywania plików platforma Azure dodaje prefiks `B2C_1A_` do każdego z nich.

> [!TIP]
> Jeśli Edytor XML obsługuje walidację, sprawdź poprawność plików `TrustFrameworkPolicy_0.3.0.0.xsd` względem schematu XML, który znajduje się w katalogu głównym pakietu początkowego. Walidacja schematu XML identyfikuje błędy przed przekazaniem.

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. W obszarze **zasady niestandardowe**wybierz pozycję **B2C_1A_signup_signin**.
1. Dla **opcji wybierz aplikację** na stronie Przegląd zasad niestandardowych wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana.
1. Upewnij się, że **adres URL odpowiedzi** to `https://jwt.ms`.
1. Wybierz pozycję **Uruchom teraz**.
1. Utwórz konto przy użyciu adresu e-mail.
1. Ponownie wybierz pozycję **Uruchom teraz** .
1. Zaloguj się przy użyciu tego samego konta, aby potwierdzić, że konfiguracja jest poprawna.

## <a name="add-facebook-as-an-identity-provider"></a>Dodawanie usługi Facebook jako dostawcy tożsamości

1. Wykonaj kroki opisane w temacie [Konfigurowanie rejestracji i logowania przy użyciu konta w serwisie Facebook](active-directory-b2c-setup-fb-app.md) , aby skonfigurować aplikację w serwisie Facebook.
1. W pliku Zastąp wartość `client_id` identyfikatorem aplikacji Facebook: **`TrustFrameworkExtensions.xml`** `SocialAndLocalAccounts/`

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Przekaż plik *TrustFrameworkExtensions. XML* do dzierżawy.
1. W obszarze **zasady niestandardowe**wybierz pozycję **B2C_1A_signup_signin**.
1. Wybierz pozycję **Uruchom teraz** i wybierz pozycję Facebook, aby zalogować się za pomocą usługi Facebook i przetestować zasady niestandardowe. Lub wywołaj zasady bezpośrednio z zarejestrowanej aplikacji.

## <a name="next-steps"></a>Następne kroki

Następnie spróbuj dodać Azure Active Directory (Azure AD) jako dostawcę tożsamości. Plik podstawowy używany w tym przewodniku wprowadzającym zawiera już część zawartości potrzebną do dodawania innych dostawców tożsamości, takich jak usługa Azure AD.

Aby uzyskać informacje o konfigurowaniu usługi Azure AD jako dostawcy tożsamości, zobacz [Konfigurowanie logowania i logowanie przy użyciu konta Azure Active Directory za pomocą zasad niestandardowych Active Directory B2C](active-directory-b2c-setup-aad-custom.md).
