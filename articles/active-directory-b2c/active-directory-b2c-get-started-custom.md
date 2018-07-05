---
title: Wprowadzenie do zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z niestandardowych zasad usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e25103d2fcbfc70be7f96f5c0e5fa6abe13fe393
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446745"
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Usługa Azure Active Directory B2C: Wprowadzenie do zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Po wykonaniu kroków opisanych w tym artykule, niestandardowe zasady będzie obsługiwać "konto" rejestracji lub logowania za pomocą adresu e-mail i hasła. Zostanie również przygotować środowisko na potrzeby dodawania dostawcy tożsamości (np. Facebook lub Azure Active Directory). Zachęcamy do wykonania tych kroków, zanim informacje o innych zastosowań struktura środowiska tożsamości B2C usługi Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że masz dzierżawę usługi Azure AD B2C, który jest kontenerem dla wszystkich użytkowników, aplikacje, zasady i. Jeśli nie masz jeszcze, musisz [tworzenie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md). Zdecydowanie Zachęcamy wszystkich deweloperów, aby zakończyć wskazówki dotyczące wbudowanych zasad usługi Azure AD B2C i konfigurowanie aplikacji przy użyciu wbudowanych zasad, przed kontynuowaniem. Aplikacje będą działać z obu typów zasad, po dokonaniu zmiany pośredniej do nazwy zasad, aby wywołać niestandardowe zasady.

>[!NOTE]
>Aby uzyskać dostęp do edycji zasady niestandardowe, należy ważnej subskrypcji platformy Azure, połączone do swojej dzierżawy. Jeśli jeszcze tego nie [połączonej dzierżawy usługi Azure AD B2C z subskrypcją platformy Azure](active-directory-b2c-how-to-enable-billing.md) lub subskrypcji platformy Azure jest wyłączona, przycisk struktura środowiska tożsamości nie będzie dostępny.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Dodaj klucze podpisywania i szyfrowania z dzierżawą B2C do użytku przez zasady niestandardowe

1. Otwórz **struktura środowiska tożsamości** bloku w ustawieniach dzierżawy usługi Azure AD B2C.
2. Wybierz **klucze zasad** Aby wyświetlić klucze, które są dostępne w Twojej dzierżawie.
3. Jeśli nie istnieje, Utwórz B2C_1A_TokenSigningKeyContainer:<br>
    a. Wybierz pozycję **Dodaj**. <br>
    b. Wybierz **Generowanie**.<br>
    c. Aby uzyskać **nazwa**, użyj `TokenSigningKeyContainer`. <br> 
    Prefiks `B2C_1A_` mogą być dodawane automatycznie.<br>
    d. Aby uzyskać **typ klucza**, użyj **RSA**.<br>
    e. Aby uzyskać **daty**, użyj wartości domyślnych. <br>
    f. Aby uzyskać **użycie klucza**, użyj **podpisu**.<br>
    g. Wybierz pozycję **Utwórz**.<br>
4. Jeśli nie istnieje, Utwórz B2C_1A_TokenEncryptionKeyContainer:<br>
 a. Wybierz pozycję **Dodaj**.<br>
 b. Wybierz **Generowanie**.<br>
 c. Aby uzyskać **nazwa**, użyj `TokenEncryptionKeyContainer`. <br>
   Prefiks `B2C_1A`_, które mogą być dodawane automatycznie.<br>
 d. Aby uzyskać **typ klucza**, użyj **RSA**.<br>
 e. Aby uzyskać **daty**, użyj wartości domyślnych.<br>
 f. Aby uzyskać **użycie klucza**, użyj **szyfrowania**.<br>
 g. Wybierz pozycję **Utwórz**.<br>
5. Create B2C_1A_FacebookSecret. <br>
Jeśli masz już klucz tajny aplikacji Facebook, dodaj go jako klucza zasad dla Twojej dzierżawy. W przeciwnym razie należy utworzyć klucz z wartość symbolu zastępczego, tak, aby zasady pomyślnie weryfikacji.<br>
 a. Wybierz pozycję **Dodaj**.<br>
 b. Aby uzyskać **opcje**, użyj **ręczne**.<br>
 c. Aby uzyskać **nazwa**, użyj `FacebookSecret`. <br>
 Prefiks `B2C_1A_` mogą być dodawane automatycznie.<br>
 d. W **klucz tajny** wprowadź swoje FacebookSecret z developers.facebook.com lub `0` jako symbol zastępczy. *Nie jest to identyfikator aplikacji usługi Facebook.* <br>
 e. Aby uzyskać **użycie klucza**, użyj **podpisu**. <br>
 f. Wybierz **Utwórz** i upewnij się, tworzenie.

## <a name="register-identity-experience-framework-applications"></a>Rejestrowanie aplikacji platformy środowiska tożsamości

Usługa Azure AD B2C wymaga zarejestrowania dwóch dodatkowych aplikacji, które są używane przez aparat się zarejestrować i logowania użytkowników.

>[!NOTE]
>Należy utworzyć dwie aplikacje tej włączenie logowania za pomocą konta lokalnego: IdentityExperienceFramework (aplikacji sieci web) i ProxyIdentityExperienceFramework (aplikacja natywna) przy użyciu delegowane uprawnienia z aplikacji IdentityExperienceFramework. Konta lokalne istnieje tylko w Twojej dzierżawie. Użytkownicy Zarejestruj się przy użyciu kombinacji adresu lub nieprawidłowego hasła unikatowego adresu e-mail, aby dostęp do aplikacji zarejestrowana dzierżawa.

### <a name="create-the-identityexperienceframework-application"></a>Tworzenie aplikacji IdentityExperienceFramework

1. W [witryny Azure portal](https://portal.azure.com), przejdź do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
2. Otwórz **usługi Azure Active Directory** bloku (nie **usługi Azure AD B2C** bloku). Może być konieczne wybranie **więcej usług** możesz jej znaleźć.
3. Wybierz pozycję **Rejestracje aplikacji**.
4. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
   * Aby uzyskać **nazwa**, użyj `IdentityExperienceFramework`.
   * Aby uzyskać **typ aplikacji**, użyj **aplikacji/interfejs API sieci Web**.
   * Aby uzyskać **adres URL logowania**, użyj `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, gdzie `yourtenant` to nazwa domeny dzierżawy usługi Azure AD B2C.
5. Wybierz pozycję **Utwórz**.
6. Po jego utworzeniu wybierz nowo utworzoną aplikację **IdentityExperienceFramework**.<br>
   * Wybierz **właściwości**.<br>
   * Skopiuj identyfikator aplikacji i zapisz go na później.

### <a name="create-the-proxyidentityexperienceframework-application"></a>Tworzenie aplikacji ProxyIdentityExperienceFramework

1. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
   * Aby uzyskać **nazwa**, użyj `ProxyIdentityExperienceFramework`.
   * Aby uzyskać **typ aplikacji**, użyj **natywnych**.
   * Aby uzyskać **identyfikator URI przekierowania**, użyj `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, gdzie `yourtenant` jest Twoja dzierżawa usługi Azure AD B2C.
1. Wybierz pozycję **Utwórz**.
1. Od momentu utworzenia, wybierz aplikację **ProxyIdentityExperienceFramework**.<br>
   * Wybierz **właściwości**. <br>
   * Skopiuj identyfikator aplikacji i zapisz go na później.
1. Wybierz **wymagane uprawnienia**.
1. Wybierz pozycję **Dodaj**.
1. Wybierz **wybierz interfejs API**.
1. Wyszukaj nazwę IdentityExperienceFramework. Wybierz **IdentityExperienceFramework** wyniki, a następnie kliknij **wybierz**.
1. Zaznacz pole wyboru obok pozycji **IdentityExperienceFramework dostępu**, a następnie kliknij przycisk **wybierz**.
1. Wybierz pozycję **Done** (Gotowe).
1. Wybierz **Udziel uprawnień**, a następnie upewnij się, wybierając **tak**.

## <a name="download-starter-pack-and-modify-policies"></a>Pobierz pakiet startowy i modyfikować zasady

Zasady niestandardowe są zestawem plików XML, które muszą być przekazywane do dzierżawy usługi Azure AD B2C. Firma Microsoft oferuje pakiety starter ułatwią Ci szybkie wdrożenie. Każdy pakiet startowy z poniższej listy zawiera najmniejszą liczbę profile techniczne i podróży użytkownika niezbędne do osiągnięcia opisanych scenariuszy:
 * LocalAccounts. Umożliwia używanie tylko kont lokalnych.
 * SocialAccounts. Umożliwia używanie tylko kont społecznościowych (lub federacyjnych).
 * **SocialAndLocalAccounts**. Ten plik służy do instruktażu.
 * SocialAndLocalAccountsWithMFA. Włącza się tu opcje społecznego, lokalnego i uwierzytelniania wieloskładnikowego.

Każdy pakiet startowy zawiera:

* [Pliku podstawowego](active-directory-b2c-overview-custom.md#policy-files) zasad. Kilka zmian są wymagane do podstawowego.
* [Rozszerzenie pliku](active-directory-b2c-overview-custom.md#policy-files) zasad.  Ten plik jest, gdzie większość zmiany konfiguracji zostały wprowadzone.
* [Jednostki uzależnionej strona pliki](active-directory-b2c-overview-custom.md#policy-files) pliki specyficzne dla zadania o nazwie przez aplikację.

>[!NOTE]
>Jeśli edytora XML obsługuje walidację, sprawdź pliki względem schematu TrustFrameworkPolicy_0.3.0.0.xsd XML, który znajduje się w katalogu głównym pakietu startowego. Sprawdzanie poprawności schematu XML, identyfikuje błędy przed przekazaniem.

 Zaczynajmy:

1. Pobierz active-directory-b2c-custom-policy-starterpack z usługi GitHub. [Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) lub uruchomić

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Otwórz SocialAndLocalAccounts folder.  Plik podstawowy (TrustFrameworkBase.xml), w tym folderze zawiera zawartość wymaganą dla kont lokalnych i ubezpieczenia/firmy. Społecznościowych zawartości nie kolidują z instrukcjami dla kont lokalnych i przeprowadzanie.
3. Open TrustFrameworkBase.xml. Jeśli potrzebujesz edytora XML [spróbuj programu Visual Studio Code](https://code.visualstudio.com/download), lekki edytor dla wielu platform.
4. W katalogu głównym `TrustFrameworkPolicy` element, aktualizacja `TenantId` i `PublicPolicyUri` atrybutów, zastępując `yourtenant.onmicrosoft.com` z nazwą domeny dzierżawy usługi Azure AD B2C:
   ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
   >[!NOTE]
   >`PolicyId` to nazwa zasad, które pojawi się w portalu i nazwę, za pomocą którego ten plik zasad odwołuje się do innych plików zasad.

5. Zapisz plik.
6. Otwórz TrustFrameworkExtensions.xml. Wprowadzenie identycznych zmian dwóch, zastępując `yourtenant.onmicrosoft.com` w swojej dzierżawie usługi Azure AD B2C. Wprowadź ten sam zastąpienia w `<TenantId>` element łącznie trzy zmiany. Zapisz plik.
7. Open SignUpOrSignIn.xml. Wprowadzenie identycznych zmian, zastępując `yourtenant.onmicrosoft.com` w swojej dzierżawie usługi Azure AD B2C w trzech miejscach. Zapisz plik.
8. Otwórz resetowania hasła i edytować pliki profilu. Wprowadzenie identycznych zmian, zastępując `yourtenant.onmicrosoft.com` w swojej dzierżawie usługi Azure AD B2C w trzech miejscach w każdym pliku. Zapisz pliki.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Dodawanie identyfikatorów aplikacji do zdefiniowania zasad niestandardowych
Dodaj identyfikatory aplikacji do pliku rozszerzenia (`TrustFrameworkExtensions.xml`):

1. W pliku rozszerzeń (TrustFrameworkExtensions.xml) można znaleźć elementu `<TechnicalProfile Id="login-NonInteractive">`.
2. Zastąp oba wystąpienia elementu `IdentityExperienceFrameworkAppId` z Identyfikatorem aplikacji w aplikacji platformy środowiska tożsamości, która została utworzona wcześniej. Oto przykład:

   ```xml
   <Item Key="IdTokenAudience">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. Zastąp oba wystąpienia elementu `ProxyIdentityExperienceFrameworkAppId` z Identyfikatorem aplikacji w aplikacji platformy środowiska tożsamości serwera Proxy, która została utworzona wcześniej.
4. Zapisz plik rozszerzenia.

## <a name="upload-the-policies-to-your-tenant"></a>Przekaż zasady dla Twojej dzierżawy

1. W [witryny Azure portal](https://portal.azure.com), przejdź do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie otwórz **usługi Azure AD B2C** bloku.
2. Wybierz **struktura środowiska tożsamości**.
3. Wybierz **przekazywać zasady**.

    >[!WARNING]
    >Pliki zasad niestandardowych, należy przekazać w następującej kolejności:

1. Przekaż TrustFrameworkBase.xml.
2. Przekaż TrustFrameworkExtensions.xml.
3. Upload SignUpOrSignin.xml.
4. Przekaż pliki zasad.

Gdy plik zostanie przekazany, nazwę pliku zasady jest poprzedzonej ciągiem `B2C_1A_`.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz

1. Otwórz **ustawienia usługi Azure AD B2C** i przejdź do **struktura środowiska tożsamości**.

   >[!NOTE]
   >**Uruchom teraz** wymaga co najmniej jedną aplikację, aby być jest wstępnie zarejestrowane w ramach dzierżawy. Aplikacje muszą być zarejestrowane w dzierżawie B2C, za pomocą **aplikacje** wybór menu, w usłudze Azure AD B2C lub za pomocą platformy środowiska tożsamości do wywołania zasady niestandardowe i wbudowane. Potrzebny jest tylko jeden rejestracji na aplikację.<br><br>
   Aby dowiedzieć się, jak zarejestrować aplikacji, zobacz temat usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [rejestracji aplikacji](active-directory-b2c-app-registration.md) artykułu.  

2. Otwórz B2C_1A_signup_signin, jednostki uzależnionej strona (RP) zasad niestandardowych, który został przekazany. Wybierz **Uruchom teraz**.

3. Powinien móc zarejestrować się przy użyciu adresu e-mail.

4. Zaloguj się przy użyciu tego samego konta, aby upewnić się, że masz prawidłowej konfiguracji.

>[!NOTE]
>Częstą przyczyną niepowodzenia logowania jest aplikacją IdentityExperienceFramework nieprawidłowo skonfigurowane.


## <a name="next-steps"></a>Kolejne kroki

### <a name="add-facebook-as-an-identity-provider"></a>Dodawanie usługi Facebook jako dostawcy tożsamości
Aby skonfigurować usługi Facebook:
1. [Konfigurowanie aplikacji usługi Facebook w developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [Dodaj klucz tajny aplikacji Facebook do swojej dzierżawy usługi Azure AD B2C](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. W pliku zasad TrustFrameworkExtensions Zastąp wartość `client_id` identyfikator aplikacji Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. Przekaż plik zasad TrustFrameworkExtensions.xml dla Twojej dzierżawy.
5. Test przy użyciu **Uruchom teraz** lub wywołując zasad bezpośrednio z zarejestrowanych aplikacji.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Dodawanie usługi Azure Active Directory jako dostawcy tożsamości
Podstawowy plik użyty w tym przewodniku z wprowadzeniem zawiera niektóre z zawartości, która należy do dodawania innych dostawców tożsamości. Aby uzyskać informacje na temat konfigurowania logowania, zobacz [usługi Azure Active Directory B2C: Zaloguj się przy użyciu konta usługi Azure AD](active-directory-b2c-setup-aad-custom.md) artykułu.

Aby uzyskać omówienie zasad niestandardowych w usłudze Azure AD B2C, które używają platformy środowiska tożsamości, zobacz [usługi Azure Active Directory B2C: zasady niestandardowe](active-directory-b2c-overview-custom.md) artykułu. 
