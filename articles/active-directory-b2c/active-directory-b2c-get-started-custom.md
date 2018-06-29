---
title: Wprowadzenie do zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z niestandardowych zasad usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8ff69942971950e2d4a274e7d3502379a26326c7
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "34709295"
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Usługa Azure Active Directory B2C: Wprowadzenie do zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Po wykonaniu kroków w tym artykule zasad niestandardowych będzie obsługiwać "konta lokalnego" rejestracji lub logowania za pomocą adresu e-mail i hasło. Zostanie również przygotować swoje środowisko do dodawania dostawcy tożsamości (takich jak Facebook lub Azure Active Directory). Firma Microsoft zachęca do wykonania tych czynności przed informacji o innych celów struktury obsługi tożsamości usługi Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że masz dzierżawę usługi Azure AD B2C, który jest kontenerem dla wszystkich użytkowników, aplikacji, zasad i. Jeśli nie masz już, musisz [tworzenie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md). Zdecydowanie zaleca wszystkich deweloperów ukończenie wskazówki dotyczące wbudowanych zasad usługi Azure AD B2C i konfigurowanie aplikacji przy użyciu wbudowanych zasad przed kontynuowaniem. Aplikacje będą działać z obydwu typach zasad po wprowadzeniu zmiany pośredniej do nazwy zasad do wywołania zasady niestandardowe.

>[!NOTE]
>Aby uzyskać dostęp do edycji zasad niestandardowych, należy ważnej subskrypcji platformy Azure powiązany dzierżawy. Jeśli nie zostało to jeszcze [połączone z subskrypcją platformy Azure Twojej dzierżawy usługi Azure AD B2C](active-directory-b2c-how-to-enable-billing.md) lub subskrypcji platformy Azure jest wyłączona, przycisk Identity Framework środowisko nie będzie dostępny.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Dodaj klucze podpisywania i szyfrowania dla Twojej dzierżawy B2C do użytku przez zasady niestandardowe

1. Otwórz **Framework obsługi tożsamości** bloku w ustawieniach dzierżawy usługi Azure AD B2C.
2. Wybierz **zasad kluczy** Aby wyświetlić dostępne w Twojej dzierżawie kluczy.
3. Utwórz B2C_1A_TokenSigningKeyContainer, jeśli nie istnieje:<br>
    a. Wybierz pozycję **Dodaj**. <br>
    b. Wybierz **Generowanie**.<br>
    c. Aby uzyskać **nazwa**, użyj `TokenSigningKeyContainer`. <br> 
    Prefiks `B2C_1A_` mogą być dodawane automatycznie.<br>
    d. Aby uzyskać **typ klucza**, użyj **RSA**.<br>
    e. Aby uzyskać **dat**, użyj wartości domyślnych. <br>
    f. Aby uzyskać **użycie klucza**, użyj **podpisu**.<br>
    g. Wybierz pozycję **Utwórz**.<br>
4. Utwórz B2C_1A_TokenEncryptionKeyContainer, jeśli nie istnieje:<br>
 a. Wybierz pozycję **Dodaj**.<br>
 b. Wybierz **Generowanie**.<br>
 c. Aby uzyskać **nazwa**, użyj `TokenEncryptionKeyContainer`. <br>
   Prefiks `B2C_1A`_ mogą być dodawane automatycznie.<br>
 d. Aby uzyskać **typ klucza**, użyj **RSA**.<br>
 e. Aby uzyskać **dat**, użyj wartości domyślnych.<br>
 f. Aby uzyskać **użycie klucza**, użyj **szyfrowania**.<br>
 g. Wybierz pozycję **Utwórz**.<br>
5. Create B2C_1A_FacebookSecret. <br>
Jeśli masz już klucz tajny aplikacji Facebook, dodaj go jako klucz zasad dla Twojej dzierżawy. W przeciwnym razie należy utworzyć klucz o wartości symbolu zastępczego, aby zasad przeszedł pomyślnie weryfikacji.<br>
 a. Wybierz pozycję **Dodaj**.<br>
 b. Aby uzyskać **opcje**, użyj **ręcznego**.<br>
 c. Aby uzyskać **nazwa**, użyj `FacebookSecret`. <br>
 Prefiks `B2C_1A_` mogą być dodawane automatycznie.<br>
 d. W **klucz tajny** wprowadź FacebookSecret Twojego z developers.facebook.com lub `0` jako symbol zastępczy. *To nie jest identyfikatorem aplikacji usługi Facebook* <br>
 e. Aby uzyskać **użycie klucza**, użyj **podpisu**. <br>
 f. Wybierz **Utwórz** i potwierdzić tworzenie.

## <a name="register-identity-experience-framework-applications"></a>Rejestrowanie aplikacji w ramach obsługi tożsamości

Usługa Azure AD B2C wymaga zarejestrować dwa dodatkowe aplikacje, które są używane przez aparat zarejestrować się i zaloguj się użytkownicy.

>[!NOTE]
>Należy utworzyć dwie aplikacje tego włączyć logowanie przy użyciu kont lokalnych: IdentityExperienceFramework (aplikacji sieci web) i ProxyIdentityExperienceFramework (aplikacji natywnej) z delegowane uprawnienia z aplikacji IdentityExperienceFramework. Konta lokalne istnieje tylko w dzierżawie. Użytkownicy konto z kombinacja adresu i hasła unikatowego adresu e-mail dostępu do Twojej aplikacji zarejestrowany dzierżawy.

### <a name="create-the-identityexperienceframework-application"></a>Tworzenie aplikacji IdentityExperienceFramework

1. W [portalu Azure](https://portal.azure.com), przejdź do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
2. Otwórz **usługi Azure Active Directory** bloku (nie **usługi Azure AD B2C** bloku). Może być konieczne wybranie **więcej usług** go znaleźć.
3. Wybierz pozycję **Rejestracje aplikacji**.
4. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
   * Aby uzyskać **nazwa**, użyj `IdentityExperienceFramework`.
   * Aby uzyskać **typu aplikacji**, użyj **aplikacji/interfejs API sieci Web**.
   * Dla **adres URL logowania**, użyj `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, gdzie `yourtenant` oznacza nazwę domeny dzierżawy usługi Azure AD B2C.
5. Wybierz pozycję **Utwórz**.
6. Po utworzeniu, wybierz nowo utworzonej aplikacji **IdentityExperienceFramework**.<br>
   * Wybierz **właściwości**.<br>
   * Skopiuj identyfikator aplikacji i zapisz je.

### <a name="create-the-proxyidentityexperienceframework-application"></a>Tworzenie aplikacji ProxyIdentityExperienceFramework

1. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
   * Aby uzyskać **nazwa**, użyj `ProxyIdentityExperienceFramework`.
   * Dla **typu aplikacji**, użyj **natywnego**.
   * Dla **identyfikator URI przekierowania**, użyj `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, gdzie `yourtenant` jest dzierżawy usługi Azure AD B2C.
1. Wybierz pozycję **Utwórz**.
1. Po utworzeniu, wybierz aplikację **ProxyIdentityExperienceFramework**.<br>
   * Wybierz **właściwości**. <br>
   * Skopiuj identyfikator aplikacji i zapisz je.
1. Wybierz **wymagane uprawnienia**.
1. Wybierz pozycję **Dodaj**.
1. Wybierz **wybierz interfejs API**.
1. Wyszukaj nazwę IdentityExperienceFramework. Wybierz **IdentityExperienceFramework** wyników, a następnie kliknij polecenie **wybierz**.
1. Zaznacz pole wyboru obok pozycji **IdentityExperienceFramework dostępu**, a następnie kliknij przycisk **wybierz**.
1. Wybierz pozycję **Done** (Gotowe).
1. Wybierz **udzielanie uprawnień**, a następnie potwierdź wybierając **tak**.

## <a name="download-starter-pack-and-modify-policies"></a>Pobierz pakiet starter i modyfikowania zasad

Zasady niestandardowe są zestawem plików XML, które muszą zostać przekazana do dzierżawy usługi Azure AD B2C. Udostępniamy pakiety startowe umożliwiające szybkie przejście. Każdy pakiet początkowy na poniższej liście zawiera najmniejszą liczbę profilów technicznych i podróże użytkownika wymagane do osiągnięcia scenariuszy opisanych:
 * LocalAccounts. Umożliwia używanie tylko kont lokalnych.
 * SocialAccounts. Umożliwia używanie tylko kont społecznościowych (lub federacyjnych).
 * **SocialAndLocalAccounts**. Możemy użyć tego pliku dla przewodnika.
 * SocialAndLocalAccountsWithMFA. Włącza się tu opcje społecznych, lokalne i uwierzytelniania wieloskładnikowego.

Każdy pakiet początkowy zawiera:

* [Pliku podstawowego](active-directory-b2c-overview-custom.md#policy-files) zasad. Kilka zmian są wymagane do podstawy.
* [Plik rozszerzenia](active-directory-b2c-overview-custom.md#policy-files) zasad.  Ten plik jest, gdzie większość zmiany konfiguracji zostały wprowadzone.
* [Jednostki uzależnionej plików firm](active-directory-b2c-overview-custom.md#policy-files) są pliki specyficzne dla zadania o nazwie przez aplikację.

>[!NOTE]
>Jeśli edytora XML obsługuje sprawdzanie poprawności, sprawdź pliki względem schematu TrustFrameworkPolicy_0.3.0.0.xsd XML, który znajduje się w katalogu głównym pakietu starter. Sprawdzanie poprawności schematu XML identyfikuje błędy przed przesłaniem.

 Rozpocznijmy:

1. Pobierz active-directory-b2c-custom-policy-starterpack z usługi GitHub. [Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) lub uruchom

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Otwórz SocialAndLocalAccounts folder.  Plik podstawowy (TrustFrameworkBase.xml) w tym folderze zawartość wymagane dla kont lokalnych i społecznego/firmowej. Zawartości społecznościowej nie koliduje z kroków do pobierania kont lokalnych w i uruchamiania.
3. Open TrustFrameworkBase.xml. Jeśli potrzebujesz edytora XML [spróbuj Visual Studio Code](https://code.visualstudio.com/download), lekkie edytora i platform.
4. W folderze głównym `TrustFrameworkPolicy` element, aktualizacja `TenantId` i `PublicPolicyUri` atrybuty, zastępując `yourtenant.onmicrosoft.com` o nazwę domeny dzierżawy usługi Azure AD B2C:
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
   >`PolicyId` jest widoczny w portalu nazwę zasady i nazwy za pomocą którego ten plik zasad jest używany przez inne pliki zasad.

5. Zapisz plik.
6. Otwórz TrustFrameworkExtensions.xml. Wprowadzenie identycznych zmian dwóch zastępując `yourtenant.onmicrosoft.com` z dzierżawą usługi Azure AD B2C. Należy tego samego zastąpienia w `<TenantId>` elementu dla wszystkich trzech zmian. Zapisz plik.
7. Open SignUpOrSignIn.xml. Wprowadzenie identycznych zmian przez zastąpienie `yourtenant.onmicrosoft.com` z dzierżawą usługi Azure AD B2C w trzech miejscach. Zapisz plik.
8. Otwórz resetowania hasła i edytować pliki profilu. Wprowadzenie identycznych zmian przez zastąpienie `yourtenant.onmicrosoft.com` z dzierżawą usługi Azure AD B2C w trzech miejscach w każdym pliku. Zapisz plik.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Dodaj identyfikatory aplikacji do zasad niestandardowych
Dodaj identyfikatory aplikacji do pliku rozszerzenia (`TrustFrameworkExtensions.xml`):

1. W pliku rozszerzenia (TrustFrameworkExtensions.xml), Znajdź element `<TechnicalProfile Id="login-NonInteractive">`.
2. Zastąp oba wystąpienia `IdentityExperienceFrameworkAppId` z identyfikator aplikacji Framework obsługi tożsamości, który został utworzony wcześniej. Oto przykład:

   ```xml
   <Item Key="IdTokenAudience">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. Zastąp oba wystąpienia `ProxyIdentityExperienceFrameworkAppId` z identyfikator aplikacji Framework obsługi tożsamości serwera Proxy, który został utworzony wcześniej.
4. Zapisz plik rozszerzenia.

## <a name="upload-the-policies-to-your-tenant"></a>Przekaż zasady dla Twojej dzierżawy

1. W [portalu Azure](https://portal.azure.com), przejdź do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie otwórz **usługi Azure AD B2C** bloku.
2. Wybierz **Framework obsługi tożsamości**.
3. Wybierz **przekazywać zasady**.

    >[!WARNING]
    >Pliki zasad niestandardowych, należy przekazać w następującej kolejności:

1. Przekaż TrustFrameworkBase.xml.
2. Przekaż TrustFrameworkExtensions.xml.
3. Upload SignUpOrSignin.xml.
4. Przekazywanie plików zasad.

Po przekazaniu pliku, nazwa pliku zasad jest poprzedzony przez `B2C_1A_`.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testowanie zasad niestandardowych przy użyciu Uruchom teraz

1. Otwórz **ustawienia usługi Azure AD B2C** i przejdź do **Framework obsługi tożsamości**.

   >[!NOTE]
   >**Uruchom teraz** wymaga co najmniej jednej aplikacji można preregistered dla dzierżawcy. Aplikacje muszą być zarejestrowane w dzierżawy B2C, za pomocą **aplikacji** zaznaczenia menu w usłudze Azure AD B2C lub przy użyciu tożsamości Framework środowisko do wywołania zarówno wbudowanych i niestandardowych zasad. Wymagany jest tylko jedna rejestracja na aplikację.<br><br>
   Aby dowiedzieć się, jak zarejestrować aplikacji, zapoznaj się z usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [Rejestracja aplikacji](active-directory-b2c-app-registration.md) artykułu.  

2. Otwórz B2C_1A_signup_signin, jednostki uzależnionej zasady niestandardowe strony (RP), który został przekazany. Wybierz **Uruchom teraz**.

3. Należy zalogowanie przy użyciu adresu e-mail.

4. Zaloguj się przy użyciu tego samego konta, aby upewnić się, że masz prawidłowej konfiguracji.

>[!NOTE]
>Częstą przyczyną niepowodzenia logowania jest nieprawidłowo skonfigurowana aplikacji IdentityExperienceFramework.


## <a name="next-steps"></a>Kolejne kroki

### <a name="add-facebook-as-an-identity-provider"></a>Dodaj funkcję dostawcy tożsamości usługi Facebook
Aby zdefiniować usługi Facebook
1. [Konfigurowanie aplikacji usługi Facebook w developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [Dodaj klucz tajny aplikacji Facebook dla Twojej dzierżawy usługi Azure AD B2C](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. W pliku zasad TrustFrameworkExtensions Zastąp wartość `client_id` identyfikator aplikacji Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. Przekaż plik zasad TrustFrameworkExtensions.xml dzierżawcy.
5. Test przy użyciu **Uruchom teraz** lub za pomocą zasad bezpośrednio w zarejestrowany aplikacji.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Dodaj funkcję dostawcy tożsamości usługi Azure Active Directory
Użyty w tym przewodniku Rozpoczęto pobieranie pliku podstawowego zawiera niektóre zawartość, która należy do dodawania innych dostawców tożsamości. Aby uzyskać informacje na temat konfigurowania logowania, zobacz [usługi Azure Active Directory B2C: Zaloguj się przy użyciu konta usługi Azure AD](active-directory-b2c-setup-aad-custom.md) artykułu.

Omówienie zasad niestandardowych w usłudze Azure AD B2C, które używają Framework obsługi tożsamości, zobacz [usługi Azure Active Directory B2C: zasady niestandardowe](active-directory-b2c-overview-custom.md) artykułu. 
