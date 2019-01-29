---
title: Samouczek — włączanie uwierzytelniania aplikacji klasycznej przy użyciu kont w usłudze Azure Active Directory B2C | Microsoft Docs
description: Samouczek dotyczący sposobu użycia usługi Azure Active Directory B2C w celu określenia nazwy logowania użytkownika na potrzeby aplikacji klasycznej platformy .NET.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 357b9f4d307624db838b22581097799d7d7fef4c
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856999"
---
# <a name="tutorial-enable-desktop-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Samouczek: włączanie uwierzytelniania aplikacji klasycznej przy użyciu kont w usłudze Azure Active Directory B2C

W tym samouczku pokazano, jak używać usługi Azure Active Directory (Azure AD) B2C do logowania użytkowników i tworzenia nowych kont w aplikacji klasycznej systemu Windows Presentation Foundation (WPF). Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie się na kontach społecznościowych, kontach przedsiębiorstw i kontach usługi Azure Active Directory za pomocą otwartych standardowych protokołów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie przykładowej aplikacji klasycznej w dzierżawie usługi Azure AD B2C.
> * Tworzenie przepływów użytkownika na potrzeby tworzenia konta, logowania, edytowania profilów i resetowania haseł użytkowników.
> * Konfigurowanie przykładowej aplikacji w celu korzystania z dzierżawy usługi Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz własną [dzierżawę usługi Azure AD B2C](active-directory-b2c-get-started.md).
* Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z obciążeniami **Programowanie aplikacji klasycznych dla platformy .NET** oraz **Tworzenie aplikacji na platformie ASP.NET i tworzenie aplikacji internetowych**.

## <a name="register-desktop-app"></a>Rejestrowanie aplikacji klasycznej

Aplikacje należy [zarejestrować](../active-directory/develop/developer-glossary.md#application-registration) w dzierżawie, zanim będą mogły otrzymywać [tokeny dostępu](../active-directory/develop/developer-glossary.md#access-token) z usługi Azure Active Directory. Rejestracja aplikacji powoduje utworzenie [identyfikatora aplikacji](../active-directory/develop/developer-glossary.md#application-id-client-id) dla aplikacji w dzierżawie. 

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Wybierz usługę **Azure AD B2C** z listy usług w witrynie Azure Portal. 

2. W ustawieniach usługi B2C kliknij pozycję **Aplikacje**, a następnie kliknij pozycję **Dodaj**. 

    Aby zarejestrować przykładową aplikację internetową w dzierżawie, użyj następujących ustawień:
    
    ![Dodawanie nowej aplikacji](media/active-directory-b2c-tutorials-desktop-app/desktop-app-registration.png)
    
    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nazwa** | Moja przykładowa aplikacja WPF | Wprowadź wartość **Nazwa** opisującą aplikację dla użytkowników. | 
    | **Uwzględnij aplikację internetową/internetowy interfejs API** | Nie | Wybierz pozycję **Nie** dla aplikacji klasycznej. |
    | **Dołącz klienta natywnego** | Yes | Ponieważ to jest aplikacja klasyczna i jest uznawana za klienta natywnego. |
    | **Identyfikator URI przekierowania** | Wartości domyślne | Unikatowy identyfikator, do którego usługa Azure AD B2C przekierowuje agenta użytkownika w odpowiedzi OAuth 2.0. |
    | **Niestandardowy identyfikator URI przekierowania** | `com.onmicrosoft.contoso.appname://redirect/path` | Wprowadź wartość `com.onmicrosoft.<your tenant name>.<any app name>://redirect/path` Przepływy użytkownika wysyłają tokeny do tego identyfikatora URI. |
    
3. Kliknij pozycję **Utwórz**, aby zarejestrować aplikację.

Zarejestrowane aplikacje są wyświetlane na liście aplikacji dla dzierżawy usługi Azure AD B2C. Wybierz aplikację klasyczną z listy. Zostanie wyświetlone okienko właściwości zarejestrowanej aplikacji klasycznej.

![Właściwości aplikacji klasycznej](./media/active-directory-b2c-tutorials-desktop-app/b2c-desktop-app-properties.png)

Zwróć uwagę na wartość **Identyfikator klienta aplikacji**. Ten identyfikator unikatowo identyfikuje aplikację i jest potrzebny podczas późniejszego konfigurowania aplikacji w samouczku.

## <a name="create-user-flows"></a>Tworzenie przepływów użytkownika

Przepływ użytkownika usługi Azure AD B2C definiuje środowisko użytkownika na potrzeby zadania obsługi tożsamości. Typowe przepływy użytkownika to na przykład logowanie, tworzenie kont, zmienianie haseł i edytowanie profilów.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego tworzenia konta lub logowania

Aby umożliwić użytkownikom tworzenie kont w celu uzyskiwania dostępu i logowanie się do aplikacji klasycznej, utwórz **przepływ użytkownika dotyczący tworzenia konta lub logowania**.

1. Na stronie portalu usługi Azure AD B2C wybierz pozycję **Przepływy użytkownika** i kliknij pozycję **Nowy przepływ użytkownika**.
2. Na karcie **Zalecane** kliknij pozycję **Tworzenie konta i logowanie**.

    Aby skonfigurować przepływ użytkownika, użyj następujących ustawień:

    ![Dodawanie przepływu użytkownika dotyczącego tworzenia konta lub logowania](media/active-directory-b2c-tutorials-desktop-app/add-susi-user-flow.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nazwa** | SiUpIn | Wprowadź **nazwę** przepływu użytkownika. Nazwa przepływu użytkownika ma prefiks **B2C_1_**. W przykładowym kodzie jest używana pełna nazwa przepływu użytkownika **B2C_1_SiUpIn**. | 
    | **Dostawcy tożsamości** | Rejestracja e-mail | Dostawca tożsamości używany do unikatowego identyfikowania użytkownika. |

3.  W obszarze **Atrybuty użytkownika i oświadczenia** kliknij pozycję **Pokaż więcej** i wybierz następujące ustawienia:

    ![Dodawanie oświadczeń i atrybutów użytkownika](media/active-directory-b2c-tutorials-desktop-app/add-attributes-and-claims.png)

    | Kolumna      | Sugerowane wartości  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Zbierz atrybut** | Nazwa wyświetlana i kod pocztowy | Wybierz atrybuty, które mają być zbierane od użytkownika podczas rejestracji. |
    | **Zwróć oświadczenie** | Nazwa wyświetlana, kod pocztowy, użytkownik jest nowy, identyfikator obiektu użytkownika | Wybierz [oświadczenia](../active-directory/develop/developer-glossary.md#claim), które mają być zawarte w [tokenie dostępu](../active-directory/develop/developer-glossary.md#access-token). |

4. Kliknij przycisk **OK**.

5. Kliknij przycisk **Utwórz**, aby utworzyć przepływ użytkownika. 

### <a name="create-a-profile-editing-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego edytowania profilu

Aby umożliwić użytkownikom samodzielne resetowanie informacji w ich profilach użytkownika, utwórz **przepływ użytkownika dotyczący edytowania profilów**.

1. Na stronie portalu usługi Azure AD B2C wybierz pozycję **Przepływ użytkownika** i kliknij pozycję **Nowy przepływ użytkownika**.
2. Na karcie **Zalecane** kliknij pozycję **Edytowanie profilu**.

    Aby skonfigurować przepływ użytkownika, użyj następujących ustawień:

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nazwa** | SiPe | Wprowadź **nazwę** przepływu użytkownika. Nazwa przepływu użytkownika ma prefiks **B2C_1_**. W przykładowym kodzie jest używana pełna nazwa przepływu użytkownika **B2C_1_SiPe**. | 
    | **Dostawcy tożsamości** | Logowanie za pomocą konta lokalnego | Dostawca tożsamości używany do unikatowego identyfikowania użytkownika. |

3. W obszarze **Atrybuty użytkownika** kliknij pozycję **Pokaż więcej** i wybierz następujące ustawienia:

    | Kolumna      | Sugerowane wartości  | Opis                                        |
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

## <a name="update-desktop-app-code"></a>Aktualizowanie kodu aplikacji klasycznej

Po zarejestrowaniu aplikacji klasycznej i utworzeniu przepływu użytkownika należy skonfigurować aplikację w celu korzystania z dzierżawy usługi Azure AD B2C. W tym samouczku skonfigurujesz przykładową aplikację klasyczną. 

[Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [przejrzyj repozytorium](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) lub sklonuj przykład z witryny GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Przykładowa aplikacja klasyczna WPF pokazuje, jak aplikacja klasyczna może używać usługi Azure AD B2C na potrzeby tworzenia nowych kont i logowania użytkowników, a także wywoływania chronionego internetowego interfejsu API.

Należy zmodyfikować aplikację tak, aby korzystała z rejestracji aplikacji w Twojej dzierżawie i konfigurowała utworzony przepływ użytkownika. 

Aby zmienić ustawienia aplikacji:

1. Otwórz rozwiązanie `active-directory-b2c-wpf` w programie Visual Studio.

2. W projekcie `active-directory-b2c-wpf` otwórz plik **App.xaml.cs** i wprowadź następujące aktualizacje:

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "The Application ID for your desktop app registered in your tenant";
    ```

3. Zaktualizuj zmienną **PolicySignUpSignIn** przy użyciu nazwy *przepływu użytkownika dotyczącego tworzenia kont lub logowania* utworzonej w poprzednim kroku. Pamiętaj, aby uwzględnić prefiks *B2C_1_*.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_SiUpIn";
    ```

## <a name="run-the-sample-desktop-application"></a>Uruchamianie przykładowej aplikacji klasycznej

Naciśnij klawisz **F5**, aby skompilować i uruchomić aplikację klasyczną. 

Przykładowa aplikacja obsługuje rejestrację, logowanie, edytowanie profilów i resetowanie haseł. Ten samouczek przedstawia proces logowania użytkownika przy użyciu adresu e-mail w celu użycia aplikacji. Możesz samodzielnie wypróbować inne scenariusze.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

1. Kliknij przycisk **Zaloguj się**, aby zarejestrować się jako użytkownik aplikacji klasycznej. W tym procesie jest używany przepływ użytkownika **B2C_1_SiUpIn** zdefiniowany w poprzednim kroku.

2. Usługa Azure AD B2C wyświetli stronę logowania z linkiem rejestracji. Ponieważ nie masz jeszcze konta, kliknij link **Sign up now** (Zarejestruj się teraz). 

3. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów. 

    ![Przepływ pracy rejestracji](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Kliknij pozycję **Utwórz**, aby utworzyć konto lokalne w dzierżawie usługi Azure AD B2C.

Teraz użytkownik może logować się i korzystać z aplikacji klasycznej, używając swojego adresu e-mail.

> [!NOTE]
> Kliknięcie przycisku **Wywołaj interfejs API** spowoduje wyświetlenie błędu „Brak autoryzacji”. Ten błąd jest wyświetlany, ponieważ próbujesz uzyskać dostęp do zasobu z dzierżawy pokazowej. Ponieważ token dostępu jest prawidłowy tylko dla dzierżawy usługi Azure AD, wywołanie interfejsu API nie ma autoryzacji. Przejdź do następnego samouczka, aby utworzyć chroniony internetowy interfejs API. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wypróbować inne samouczki usługi Azure AD B2C, możesz użyć swojej dzierżawy usługi Azure AD B2C. Możesz [usunąć dzierżawę usługi Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant), gdy nie będzie już potrzebna.

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak utworzyć dzierżawę usługi Azure AD B2C, utworzyć przepływy użytkownika i zaktualizować przykładową aplikację klasyczną w celu korzystania z dzierżawy usługi Azure AD B2C. Przejdź do następnego samouczka, aby dowiedzieć się, jak rejestrować, konfigurować i wywoływać chroniony internetowy interfejs API z poziomu aplikacji klasycznej.

> [!div class="nextstepaction"]
> 
