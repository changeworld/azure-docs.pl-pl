---
title: Samouczek — włączanie uwierzytelniania aplikacji jednostronicowej przy użyciu kont w usłudze Azure Active Directory B2C | Microsoft Docs
description: Samouczek dotyczący sposobu użycia usługi Azure Active Directory B2C w celu określenia nazwy logowania użytkownika na potrzeby aplikacji jednostronicowej (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 08372700e9740ca69ba73dfba49f9d120dfabc6d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54850335"
---
# <a name="tutorial-enable-single-page-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Samouczek: włączanie uwierzytelniania aplikacji jednostronicowej przy użyciu kont w usłudze Azure Active Directory B2C

W tym samouczku pokazano, jak używać usługi Azure Active Directory (Azure AD) B2C do tworzenia nowych kont i logowania użytkowników w aplikacji jednostronicowej (SPA). Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie się na kontach społecznościowych, kontach przedsiębiorstw i kontach usługi Azure Active Directory za pomocą otwartych standardowych protokołów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie przykładowej aplikacji jednostronicowej w katalogu usługi Azure AD B2C.
> * Tworzenie przepływów użytkownika na potrzeby tworzenia konta, logowania, edytowania profilów i resetowania haseł użytkowników.
> * Konfigurowanie przykładowej aplikacji w celu korzystania z katalogu usługi Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz własny [katalog usługi Azure AD B2C](active-directory-b2c-get-started.md).
* Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z pakietem roboczym **Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych**.
* Zestaw [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) lub nowszy
* Zainstalować środowisko [Node.js](https://nodejs.org/en/download/).

## <a name="register-single-page-app"></a>Rejestrowanie aplikacji jednostronicowej

Aplikacje należy [zarejestrować](../active-directory/develop/developer-glossary.md#application-registration) w katalogu, zanim będą mogły otrzymywać [tokeny dostępu](../active-directory/develop/developer-glossary.md#access-token) z usługi Azure Active Directory. Rejestracja aplikacji powoduje utworzenie [identyfikatora aplikacji](../active-directory/develop/developer-glossary.md#application-id-client-id) dla aplikacji w katalogu. 

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny katalogu usługi Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Wybierz usługę **Azure AD B2C** z listy usług w witrynie Azure Portal. 

2. W ustawieniach usługi B2C kliknij pozycję **Aplikacje**, a następnie kliknij pozycję **Dodaj**. 

    Aby zarejestrować przykładową aplikację internetową w katalogu, użyj następujących ustawień:
    
    ![Dodawanie nowej aplikacji](media/active-directory-b2c-tutorials-spa/spa-registration.png)
    
    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nazwa** | Moja przykładowa aplikacja jednostronicowa | Wprowadź wartość **Nazwa** opisującą aplikację dla użytkowników. | 
    | **Uwzględnij aplikację internetową/internetowy interfejs API** | Yes | Wybierz pozycję **Tak** dla aplikacji jednostronicowej. |
    | **Zezwalaj na niejawny przepływ** | Yes | Wybierz pozycję **Tak**, ponieważ aplikacja używa [logowania OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **Adres URL odpowiedzi** | `http://localhost:6420` | Adresy URL odpowiedzi to punkty końcowe, do których usługa Azure AD B2C zwraca wszelkie tokeny żądane przez aplikację. W tym samouczku przykład jest uruchamiany lokalnie (localhost) i nasłuchuje na porcie 6420. |
    | **Dołącz klienta natywnego** | Nie | Ponieważ jest to aplikacja jednostronicowa, a nie klient natywny, wybierz pozycję Nie. |
    
3. Kliknij pozycję **Utwórz**, aby zarejestrować aplikację.

Zarejestrowane aplikacje są wyświetlane na liście aplikacji dla katalogu usługi Azure AD B2C. Wybierz aplikację jednostronicową z listy. Zostanie wyświetlone okienko właściwości zarejestrowanej aplikacji jednostronicowej.

![Właściwości aplikacji jednostronicowej](./media/active-directory-b2c-tutorials-spa/b2c-spa-properties.png)

Zwróć uwagę na wartość **Identyfikator klienta aplikacji**. Ten identyfikator unikatowo identyfikuje aplikację i jest potrzebny podczas późniejszego konfigurowania aplikacji w samouczku.

## <a name="create-user-flows"></a>Tworzenie przepływów użytkownika

Przepływ użytkownika usługi Azure AD B2C definiuje środowisko użytkownika na potrzeby zadania obsługi tożsamości. Typowe przepływy użytkownika to na przykład logowanie, tworzenie kont, zmienianie haseł i edytowanie profilów.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego tworzenia konta lub logowania

Aby umożliwić użytkownikom tworzenie kont w celu uzyskiwania dostępu i logowanie się do aplikacji internetowej, utwórz **przepływ użytkownika dotyczący tworzenia konta lub logowania**.

1. Na stronie portalu usługi Azure AD B2C wybierz pozycję **Przepływy użytkownika** i kliknij pozycję **Nowy przepływ użytkownika**.
2. Na karcie **Zalecane** kliknij pozycję **Tworzenie konta i logowanie**.

    Aby skonfigurować przepływ użytkownika, użyj następujących ustawień:

    ![Dodawanie przepływu użytkownika dotyczącego tworzenia konta lub logowania](media/active-directory-b2c-tutorials-spa/add-susi-user-flow.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nazwa** | SiUpIn | Wprowadź **nazwę** przepływu użytkownika. Nazwa przepływu użytkownika ma prefiks **B2C_1_**. W przykładowym kodzie jest używana pełna nazwa przepływu użytkownika **B2C_1_SiUpIn**. | 
    | **Dostawcy tożsamości** | Rejestracja e-mail | Dostawca tożsamości używany do unikatowego identyfikowania użytkownika. |

3. W obszarze **Atrybuty użytkownika i oświadczenia** kliknij pozycję **Pokaż więcej** i wybierz następujące ustawienia:

    ![Dodawanie przepływu użytkownika dotyczącego tworzenia konta lub logowania](media/active-directory-b2c-tutorials-spa/add-attributes-and-claims.png)

    | Kolumna      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Zbierz atrybut** | Nazwa wyświetlana i kod pocztowy | Wybierz atrybuty, które mają być zbierane od użytkownika podczas rejestracji. |
    | **Zwróć oświadczenie** | Nazwa wyświetlana, kod pocztowy, użytkownik jest nowy, identyfikator obiektu użytkownika | Wybierz [oświadczenia](../active-directory/develop/developer-glossary.md#claim), które mają być zawarte w [tokenie dostępu](../active-directory/develop/developer-glossary.md#access-token). |

4. Kliknij przycisk **OK**.
5. Kliknij przycisk **Utwórz**, aby utworzyć przepływ użytkownika. 

### <a name="create-a-profile-editing-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego edytowania profilu

Aby umożliwić użytkownikom samodzielne resetowanie informacji w ich profilach użytkownika, utwórz **przepływ użytkownika dotyczący edytowania profilów**.

1. Na stronie portalu usługi Azure AD B2C wybierz pozycję **Przepływy użytkownika** i kliknij pozycję **Nowy przepływ użytkownika**.
2. Na karcie **Zalecane** kliknij pozycję **Edytowanie profilu**.

    Aby skonfigurować przepływ użytkownika, użyj następujących ustawień:

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nazwa** | SiPe | Wprowadź **nazwę** przepływu użytkownika. Nazwa przepływu użytkownika ma prefiks **B2C_1_**. W przykładowym kodzie jest używana pełna nazwa przepływu użytkownika **B2C_1_SiPe**. | 
    | **Dostawcy tożsamości** | Logowanie za pomocą konta lokalnego | Dostawca tożsamości używany do unikatowego identyfikowania użytkownika. |

3.  W obszarze **Atrybuty użytkownika** kliknij pozycję **Pokaż więcej** i wybierz następujące ustawienia:

    | Kolumna      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Zbierz atrybut** | Nazwa wyświetlana i kod pocztowy | Wybierz atrybuty, które użytkownicy mogą modyfikować podczas edytowania profilu. |
    | **Zwróć oświadczenie** | Nazwa wyświetlana, kod pocztowy, identyfikator obiektu użytkownika | Wybierz [oświadczenia](../active-directory/develop/developer-glossary.md#claim), które mają być zawarte w [tokenie dostępu](../active-directory/develop/developer-glossary.md#access-token) po pomyślnym edytowaniu profilu. |

4. Kliknij przycisk **OK**.
5. Kliknij przycisk **Utwórz**, aby utworzyć przepływ użytkownika. 

### <a name="create-a-password-reset-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego resetowania haseł

Aby umożliwić resetowanie haseł w aplikacji, należy utworzyć **przepływ użytkownika dotyczący resetowania haseł**. Ten przepływ użytkownika opisuje środowisko pracy użytkownika podczas resetowania hasła oraz zawartość tokenów, które aplikacja otrzymuje po jego pomyślnym ukończeniu.

1. Na stronie portalu usługi Azure AD B2C wybierz pozycję **Przepływy użytkownika** i kliknij pozycję **Nowy przepływ użytkownika**.
2. Na karcie **Zalecane** kliknij pozycję **Resetowanie hasła**.

    Aby skonfigurować przepływ użytkownika, użyj następujących ustawień.

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nazwa** | SSPR | Wprowadź **nazwę** przepływu użytkownika. Nazwa przepływu użytkownika ma prefiks **B2C_1_**. W przykładowym kodzie jest używana pełna nazwa przepływu użytkownika **B2C_1_SSPR**. | 
    | **Dostawcy tożsamości** | Resetuj hasło przy użyciu adresu e-mail | Jest to dostawca tożsamości używany do unikatowego identyfikowania użytkownika. |

3. W obszarze **Oświadczenia aplikacji** kliknij pozycję **Pokaż więcej** i wybierz następujące ustawienie:

    | Kolumna      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Zwróć oświadczenie** | Identyfikator obiektu użytkownika | Wybierz [oświadczenia](../active-directory/develop/developer-glossary.md#claim), które mają być zawarte w [tokenie dostępu](../active-directory/develop/developer-glossary.md#access-token) po pomyślnym zresetowaniu hasła. |

4. Kliknij przycisk **OK**.
5. Kliknij przycisk **Utwórz**, aby utworzyć przepływ użytkownika. 

## <a name="update-single-page-app-code"></a>Aktualizowanie kodu aplikacji jednostronicowej

Po zarejestrowaniu aplikacji i utworzeniu przepływów użytkownika należy skonfigurować aplikację w celu korzystania z katalogu usługi Azure AD B2C. W tym samouczku skonfigurujesz przykładową aplikację SPA JavaScript, którą można pobrać z witryny GitHub. 

[Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) lub sklonuj przykładową aplikację internetową z usługi GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```
Przykładowa aplikacja pokazuje, jak aplikacja jednostronicowa może używać usługi Azure AD B2C na potrzeby tworzenia nowych kont i logowania użytkowników, a także wywoływania chronionego internetowego interfejsu API. Należy zmodyfikować aplikację tak, aby korzystała z rejestracji aplikacji w Twoim katalogu i konfigurowała utworzone przepływy użytkownika. 

Aby zmienić ustawienia aplikacji:

1. Otwórz plik `index.html` w przykładowej aplikacji jednostronicowej platformy Node.js.
2. Skonfiguruj przykład obejmujący informacje na temat rejestracji katalogu usługi Azure AD B2C. Zmień następujące wiersze kodu (pamiętaj, aby zamienić wartości na nazwy Twojego katalogu i interfejsów API):

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://fabrikamb2c.b2clogin.com/tfp/fabrikamb2c.onmicrosoft.com/B2C_1_<Sign-up or sign-in policy name>",
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://fabrikamb2chello.azurewebsites.net/hello',
    };
    ```

    Nazwa przepływu użytkownika używanego w tym samouczku to **B2C_1_SiUpIn**. Jeśli używasz innej nazwy przepływu użytkownika, użyj jej w obszarze wartości `authority`.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Uruchom wiersz polecenia platformy Node.js.
2. Przejdź do katalogu zawierającego przykład platformy Node.js. Na przykład `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Uruchom następujące polecenia:
    ```
    npm install && npm update
    node server.js
    ```

    W oknie konsoli jest wyświetlany numer portu, w którym jest hostowana aplikacja.
    
    ```
    Listening on port 6420...
    ```

4. W przeglądarce przejdź do adresu `http://localhost:6420`, aby wyświetlić aplikację.

Przykładowa aplikacja obsługuje rejestrację, logowanie, edytowanie profilów i resetowanie haseł. Ten samouczek przedstawia proces logowania użytkownika przy użyciu adresu e-mail w celu użycia aplikacji. Możesz samodzielnie wypróbować inne scenariusze.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

1. Kliknij przycisk **Zaloguj się**, aby zarejestrować się jako użytkownik aplikacji SPA. W tym procesie jest używany przepływ użytkownika **B2C_1_SiUpIn** zdefiniowany w poprzednim kroku.

2. Usługa Azure AD B2C wyświetli stronę logowania z linkiem rejestracji. Ponieważ nie masz jeszcze konta, kliknij link **Sign up now** (Zarejestruj się teraz). 

3. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów. 

    ![Przepływ pracy rejestracji](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Kliknij pozycję **Utwórz**, aby utworzyć konto lokalne w katalogu usługi Azure AD B2C.

Teraz użytkownik może logować się i korzystać z aplikacji SPA, używając swojego adresu e-mail.

> [!NOTE]
> Po zalogowaniu aplikacja wyświetla błąd „niewystarczające uprawnienia”. Ten błąd jest wyświetlany, ponieważ próbujesz uzyskać dostęp do zasobu z katalogu pokazowego. Ponieważ token dostępu jest prawidłowy tylko dla katalogu usługi Azure AD, wywołanie interfejsu API nie ma autoryzacji. Przejdź do następnego samouczka, aby utworzyć chroniony internetowy interfejs API dla Twojego katalogu. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wypróbować inne samouczki usługi Azure AD B2C, możesz użyć swojego katalogu usługi Azure AD B2C. Możesz [usunąć katalog usługi Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant), gdy nie będzie już potrzebny.

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak utworzyć katalog usługi Azure AD B2C, utworzyć przepływy użytkownika i zaktualizować przykładową aplikację jednostronicową w celu korzystania z katalogu usługi Azure AD B2C. Przejdź do następnego samouczka, aby dowiedzieć się, jak rejestrować, konfigurować i wywoływać chroniony internetowy interfejs API z poziomu aplikacji klasycznej.

> [!div class="nextstepaction"]
> [Przykłady kodu usługi Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
