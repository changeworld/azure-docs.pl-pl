---
title: Wprowadzenie do zasad niestandardowych — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozpocząć pracę przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f4ecc01f42d491f5d9d967b9983e773c1df22298
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65986827"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Wprowadzenie do zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Zasady niestandardowe](active-directory-b2c-overview-custom.md) są pliki konfiguracji definiujące zachowanie Twojej dzierżawy usługi Azure Active Directory (Azure AD) B2C. W tym artykule można utworzyć zasad niestandardowych, który obsługuje konta lokalnego, zarejestruj się lub zaloguj się przy użyciu adresu e-mail i hasła. Należy również przygotować środowisko do dodawania dostawcy tożsamości.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz jeszcze, musisz [tworzenie dzierżawy usługi Azure AD B2C](tutorial-create-tenant.md) połączony z subskrypcją platformy Azure.
- [Zarejestruj swoją aplikację](tutorial-register-applications.md) w dzierżawie, który został utworzony tak, aby umożliwić komunikację z usługą Azure AD B2C.

## <a name="add-signing-and-encryption-keys"></a>Dodaj klucze podpisywania i szyfrowania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C. Kliknij przycisk **filtr katalogów i subskrypcji** w górnym menu i wybierz katalog, który zawiera Twojej dzierżawy. 
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz **struktura środowiska tożsamości**.

### <a name="create-the-signing-key"></a>Tworzenie klucza podpisywania

1. Wybierz **klucze zasad** , a następnie wybierz **Dodaj**.
2. Aby uzyskać **opcje**, wybierz `Generate`.
3. W **nazwa**, wprowadź `TokenSigningKeyContainer`. Prefiks `B2C_1A_` mogą być dodawane automatycznie.
4. Aby uzyskać **typ klucza**, wybierz opcję **RSA**.
5. Aby uzyskać **użycie klucza**, wybierz opcję **podpisu**.
6. Kliknij pozycję **Utwórz**.

### <a name="create-the-encryption-key"></a>Utwórz klucz szyfrowania

1. Wybierz **klucze zasad** , a następnie wybierz **Dodaj**.
2. Aby uzyskać **opcje**, wybierz `Generate`.
3. W **nazwa**, wprowadź `TokenEncryptionKeyContainer`. Prefiks `B2C_1A`_, które mogą być dodawane automatycznie.
4. Aby uzyskać **typ klucza**, wybierz opcję **RSA**.
5. Aby uzyskać **użycie klucza**, wybierz opcję **szyfrowania**.
6. Kliknij pozycję **Utwórz**.

### <a name="create-the-facebook-key"></a>Utwórz klucz usługi Facebook

Jeśli masz już [klucz tajny aplikacji Facebook](active-directory-b2c-setup-fb-app.md), dodaj go jako klucza zasad do swojej dzierżawy. W przeciwnym razie należy utworzyć klucz z wartość symbolu zastępczego, tak, aby zasady pomyślnie weryfikacji.

1. Wybierz **klucze zasad** , a następnie wybierz **Dodaj**.
2. Aby uzyskać **opcje**, wybierz `Manual`.
3. Aby uzyskać **nazwa**, wprowadź `FacebookSecret`. Prefiks `B2C_1A_` mogą być dodawane automatycznie.
4. W **klucz tajny**, wprowadź klucz tajny usługi Facebook z developers.facebook.com lub `0` jako symbol zastępczy. Ta wartość jest klucz tajny, a nie identyfikator aplikacji.
5. Aby uzyskać **użycie klucza**, wybierz opcję **podpisu**.
6. Kliknij pozycję **Utwórz**.

## <a name="register-identity-experience-framework-applications"></a>Rejestrowanie aplikacji platformy środowiska tożsamości

Usługa Azure AD B2C wymaga zarejestrowania dwóch aplikacji, które umożliwiają rejestrowanie i logowanie użytkowników: IdentityExperienceFramework (aplikacji sieci web), a ProxyIdentityExperienceFramework (aplikacja natywna) przy użyciu delegowanych uprawnień z aplikacji IdentityExperienceFramework. Konta lokalne istnieje tylko w Twojej dzierżawie. Użytkownicy Zarejestruj się przy użyciu kombinacji adresu lub nieprawidłowego hasła unikatowego adresu e-mail, aby dostęp do aplikacji zarejestrowana dzierżawa.

### <a name="register-the-identityexperienceframework-application"></a>Rejestrowanie aplikacji IdentityExperienceFramework

1. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal Wyszukaj i wybierz pozycję **usługi Azure Active Directory**.
2. Wybierz z menu **rejestracje aplikacji (starsza wersja)**.
3. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
4. Aby uzyskać **nazwa**, wprowadź `IdentityExperienceFramework`.
5. Aby uzyskać **typ aplikacji**, wybierz **aplikacji/interfejs API sieci Web**.
6. Aby uzyskać **adres URL logowania**, wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, gdzie `your-tenant-name` to nazwa domeny dzierżawy usługi Azure AD B2C. Wszystkie adresy URL powinny teraz być za pomocą [z usługi b2clogin.com](b2clogin.md).
7. Kliknij pozycję **Utwórz**. Po jego utworzeniu, skopiuj identyfikator aplikacji i zapisz go do późniejszego użycia.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Rejestrowanie aplikacji ProxyIdentityExperienceFramework

1. W **rejestracje aplikacji (starsza wersja)**, wybierz opcję **rejestrowanie nowej aplikacji**.
2. Aby uzyskać **nazwa**, wprowadź `ProxyIdentityExperienceFramework`.
3. Aby uzyskać **typ aplikacji**, wybierz **natywnych**.
4. Aby uzyskać **identyfikator URI przekierowania**, wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, gdzie `yourtenant` jest Twoja dzierżawa usługi Azure AD B2C.
5. Kliknij pozycję **Utwórz**. Po jego utworzeniu, skopiuj identyfikator aplikacji i zapisz go do późniejszego użycia.
6. Na stronie ustawień wybierz **wymagane uprawnienia**, a następnie wybierz pozycję **Dodaj**.
7. Wybierz **wybierz interfejs API**, wyszukiwanie i wybieranie **IdentityExperienceFramework**, a następnie kliknij przycisk **wybierz**.
9. Zaznacz pole wyboru obok pozycji **IdentityExperienceFramework dostępu**, kliknij przycisk **wybierz**, a następnie kliknij przycisk **gotowe**.
10. Wybierz **Udziel uprawnień**, a następnie upewnij się, wybierając **tak**.

## <a name="download-starter-pack-and-modify-policies"></a>Pobierz pakiet startowy i modyfikować zasady

Zasady niestandardowe są zestawem plików XML, które muszą być przekazywane do dzierżawy usługi Azure AD B2C. Pakiety Starter plików znajdują się ułatwią Ci szybkie wdrożenie. Każdy pakiet startowy z poniższej listy zawiera najmniejszą liczbę profile techniczne i podróży użytkownika niezbędne do osiągnięcia opisanych scenariuszy:

- LocalAccounts — umożliwia używanie tylko kont lokalnych.
- SocialAccounts — umożliwia używanie tylko kont społecznościowych (lub federacyjnych).
- SocialAndLocalAccounts — umożliwia użycie lokalnego konta i kont społecznościowych.
- SocialAndLocalAccountsWithMFA - włącza social, lokalne i opcje uwierzytelniania wieloskładnikowego.

Każdy pakiet startowy zawiera:

- Pliku podstawowego. Kilka zmian są wymagane do podstawowego.
- Rozszerzenie pliku.  Ten plik jest, gdzie większość zmiany konfiguracji zostały wprowadzone.
- Pliki strony jednostki uzależnionej. Pliki specyficzne dla zadania o nazwie przez aplikację.

>[!NOTE]
>Jeśli edytora XML obsługuje walidację, sprawdź pliki względem schematu TrustFrameworkPolicy_0.3.0.0.xsd XML, który znajduje się w katalogu głównym pakietu startowego. Sprawdzanie poprawności schematu XML, identyfikuje błędy przed przekazaniem.

1. [Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) lub uruchomić:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. W folderze SocialAndLocalAccounts edytować wszystkie pliki, zastępując `yourtenant` o nazwie dla Twojej dzierżawy. Na przykład `contosoTenant.onmicrosoft.com`. Jeśli potrzebujesz edytora XML [spróbuj programu Visual Studio Code](https://code.visualstudio.com/download), lekki edytor dla wielu platform.

### <a name="add-application-ids-to-the-custom-policy"></a>Dodawanie identyfikatorów aplikacji do zasad niestandardowych

Dodaj identyfikatory aplikacji do pliku rozszerzenia *TrustFrameworkExtensions.xml*.

1. Otwórz *TrustFrameworkExtensions.xml* plików i Znajdź element `<TechnicalProfile Id="login-NonInteractive">`.
2. Zastąp oba wystąpienia elementu `IdentityExperienceFrameworkAppId` z Identyfikatorem aplikacji w aplikacji platformy środowiska tożsamości, która została utworzona wcześniej. Zastąp oba wystąpienia elementu `ProxyIdentityExperienceFrameworkAppId` z Identyfikatorem aplikacji w aplikacji platformy środowiska tożsamości serwera Proxy, która została utworzona wcześniej.
3. Zapisz plik rozszerzenia.

## <a name="upload-the-policies"></a>Przekazywanie zasad

1. Na stronie zasad niestandardowych struktura środowiska tożsamości, wybierz **zasady przekazywania**.
1. W tej kolejności, Przekaż *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* , a *PasswordReset.xml*. Gdy plik zostanie przekazany, nazwę pliku zasady jest poprzedzonej ciągiem `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. Na stronie zasad niestandardowych wybierz **B2C_1A_signup_signin**.
2. Aby uzyskać **wybierz aplikację** na stronie Przegląd zasad niestandardowych, wybierz aplikację sieci web o nazwie *webapp1* który został wcześniej zarejestrowany. Upewnij się, że **adres URL odpowiedzi** jest `https://jwt.ms`.
3. Wybierz **Uruchom teraz**.
4. Powinien móc zarejestrować się przy użyciu adresu e-mail.
5. Zaloguj się przy użyciu tego samego konta, aby upewnić się, że masz prawidłowej konfiguracji.

## <a name="add-facebook-as-an-identity-provider"></a>Dodawanie usługi Facebook jako dostawcy tożsamości

1. Konfigurowanie [aplikacji usługi Facebook](active-directory-b2c-setup-fb-app.md).
2. W *TrustFrameworkExtensions.xml* pliku, zastępując wartość `client_id` identyfikator aplikacji Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
3. Przekaż *TrustFrameworkExtensions.xml* plik dla Twojej dzierżawy.
4. Test przy użyciu **Uruchom teraz** lub wywołując zasad bezpośrednio z zarejestrowanych aplikacji.

## <a name="next-steps"></a>Kolejne kroki

- Dodaj usługę Azure Active Directory jako dostawcy tożsamości. Podstawowy plik użyty w tym przewodniku z wprowadzeniem zawiera niektóre z zawartości, która należy do dodawania innych dostawców tożsamości. Aby uzyskać informacji na temat konfigurowania logowania, zobacz [Konfigurowanie Zarejestruj się i zaloguj się przy użyciu konta usługi Azure Active Directory za pomocą zasad niestandardowych Active Directory B2C](active-directory-b2c-setup-aad-custom.md) artykułu.
