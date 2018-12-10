---
title: Samouczek — włączanie uwierzytelniania aplikacji internetowej przy użyciu kont w usłudze Azure Active Directory B2C | Microsoft Docs
description: Samouczek dotyczący sposobu użycia usługi Azure Active Directory B2C w celu określenia nazwy logowania użytkownika na potrzeby aplikacji internetowej platformy ASP.NET.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 8b482391dfafdda0e54b3f9e2b8a3a7de2f2d5cd
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834727"
---
# <a name="tutorial-enable-a-web-application-to-authenticate-with-accounts-using-azure-active-directory-b2c"></a>Samouczek — włączanie uwierzytelniania aplikacji internetowej przy użyciu kont w usłudze Azure Active Directory B2C

W tym samouczku pokazano, jak używać usługi Azure Active Directory (Azure AD) B2C do logowania i rejestracji użytkowników w aplikacji internetowej platformy ASP.NET. Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie się na kontach społecznościowych, kontach przedsiębiorstw i kontach usługi Azure Active Directory za pomocą otwartych standardowych protokołów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie przykładowej aplikacji internetowej ASP.NET w dzierżawie usługi Azure AD B2C.
> * Tworzenie przepływów użytkownika na potrzeby tworzenia konta, logowania, edytowania profilów i resetowania haseł użytkowników.
> * Konfigurowanie przykładowej aplikacji internetowej w celu korzystania z dzierżawy usługi Azure AD B2C. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz własną [dzierżawę usługi Azure AD B2C](active-directory-b2c-get-started.md).
* Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z pakietem roboczym **Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych**.

## <a name="register-web-app"></a>Rejestrowanie aplikacji internetowej

Aplikacje należy [zarejestrować](../active-directory/develop/developer-glossary.md#application-registration) w dzierżawie, zanim będą mogły otrzymywać [tokeny dostępu](../active-directory/develop/developer-glossary.md#access-token) z usługi Azure Active Directory. Rejestracja aplikacji powoduje utworzenie [identyfikatora aplikacji](../active-directory/develop/developer-glossary.md#application-id-client-id) dla aplikacji w dzierżawie. 

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**. Teraz powinna być używana dzierżawa utworzona w poprzednim samouczku. 

2. W ustawieniach usługi B2C kliknij pozycję **Aplikacje**, a następnie kliknij pozycję **Dodaj**. 

    Aby zarejestrować przykładową aplikację internetową w dzierżawie, użyj następujących ustawień:

    ![Dodawanie nowej aplikacji](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)
    
    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nazwa** | Moja przykładowa aplikacja internetowa | Wprowadź wartość **Nazwa** opisującą aplikację dla użytkowników. | 
    | **Uwzględnij aplikację internetową/internetowy interfejs API** | Yes | Wybierz pozycję **Tak** dla aplikacji internetowej. |
    | **Zezwalaj na niejawny przepływ** | Yes | Wybierz pozycję **Tak**, ponieważ aplikacja używa [logowania OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **Adres URL odpowiedzi** | `https://localhost:44316` | Adresy URL odpowiedzi to punkty końcowe, do których usługa Azure AD B2C zwraca wszelkie tokeny żądane przez aplikację. W tym samouczku przykład jest uruchamiany lokalnie (localhost) i nasłuchuje na porcie 44316. |
    | **Dołącz klienta natywnego** | Nie | Ponieważ jest to aplikacja internetowa, a nie klient natywny, wybierz pozycję Nie. |
    
3. Kliknij pozycję **Utwórz**, aby zarejestrować aplikację.

Zarejestrowane aplikacje są wyświetlane na liście aplikacji dla dzierżawy usługi Azure AD B2C. Wybierz swoją aplikację internetową z listy. Zostanie wyświetlone okienko właściwości aplikacji internetowej.

![Właściwości aplikacji internetowej](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

Zanotuj wartość **Identyfikatora aplikacji**. Ten identyfikator unikatowo identyfikuje aplikację i jest potrzebny podczas późniejszego konfigurowania aplikacji w samouczku.

### <a name="create-a-client-password"></a>Tworzenie hasła klienta

Usługa Azure AD B2C używa autoryzacji OAuth2 dla [aplikacji klienckich](../active-directory/develop/developer-glossary.md#client-application). Aplikacje internetowe są [klientami poufnymi](../active-directory/develop/developer-glossary.md#web-client) i wymagają identyfikatora klienta lub identyfikatora aplikacji oraz klucza tajnego klienta, hasła klienta lub klucza aplikacji.

1. Wybierz stronę Klucze dla rejestrowanej aplikacji internetowej i kliknij pozycję **Generuj klucz**.

2. Kliknij pozycję **Zapisz**, aby wyświetlić klucz aplikacji.

    ![strona aplikacji: ogólne — klucze](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

Klucz jest wyświetlany raz w portalu. Ważne jest, aby skopiować i zachować wartość klucza. Ta wartość będzie potrzebna do skonfigurowania aplikacji. Przechowuj klucz w bezpiecznym miejscu. Nie udostępniaj klucza publicznie.

## <a name="create-user-flows"></a>Tworzenie przepływów użytkownika

Przepływ użytkownika usługi Azure AD B2C definiuje środowisko użytkownika na potrzeby zadania obsługi tożsamości. Typowe przepływy użytkownika to na przykład logowanie, tworzenie kont, zmienianie haseł i edytowanie profilów.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego tworzenia konta lub logowania

Aby umożliwić użytkownikom tworzenie kont w celu uzyskiwania dostępu i logowanie się do aplikacji internetowej, utwórz **przepływ użytkownika dotyczący tworzenia konta lub logowania**.

1. Na stronie portalu usługi Azure AD B2C wybierz pozycję **Przepływy użytkownika** i kliknij pozycję **Nowy przepływ użytkownika**.
2. Na karcie **Zalecane** kliknij pozycję **Tworzenie konta i logowanie**.

    Aby skonfigurować przepływ użytkownika, użyj następujących ustawień:

    ![Dodawanie przepływu użytkownika dotyczącego tworzenia konta lub logowania](media/active-directory-b2c-tutorials-web-app/add-susi-user-flow.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nazwa** | SiUpIn | Wprowadź **nazwę** przepływu użytkownika. Nazwa przepływu użytkownika ma prefiks **b2c_1_**. W przykładowym kodzie jest używana pełna nazwa przepływu użytkownika **b2c_1_SiUpIn**. | 
    | **Dostawcy tożsamości** | Rejestracja e-mail | Dostawca tożsamości używany do unikatowego identyfikowania użytkownika. |

3. W obszarze **Atrybuty użytkownika i oświadczenia** kliknij pozycję **Pokaż więcej** i wybierz następujące ustawienia:

    ![Dodawanie przepływu użytkownika dotyczącego tworzenia konta lub logowania](media/active-directory-b2c-tutorials-web-app/add-attributes-and-claims.png)

    | Kolumna      | Sugerowane wartości  | Opis                                        |
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
    | **Nazwa** | SiPe | Wprowadź **nazwę** przepływu użytkownika. Nazwa przepływu użytkownika ma prefiks **b2c_1_**. W przykładowym kodzie jest używana pełna nazwa przepływu użytkownika **b2c_1_SiPe**. | 
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

1. Na stronie portalu usługi Azure AD B2C wybierz pozycję **Zasady resetowania hasła** i kliknij pozycję **Dodaj**.
2. Na karcie **Zalecane** kliknij pozycję **Resetowanie hasła**.

    Aby skonfigurować przepływ użytkownika, użyj następujących ustawień.

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nazwa** | SSPR | Wprowadź **nazwę** przepływu użytkownika. Nazwa przepływu użytkownika ma prefiks **b2c_1_**. W przykładowym kodzie jest używana pełna nazwa przepływu użytkownika **b2c_1_SSPR**. | 
    | **Dostawcy tożsamości** | Resetuj hasło przy użyciu adresu e-mail | Jest to dostawca tożsamości używany do unikatowego identyfikowania użytkownika. |

3. W obszarze **Oświadczenia aplikacji** kliknij pozycję **Pokaż więcej** i wybierz następujące ustawienie:
    | Kolumna      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Zwróć oświadczenie** | Identyfikator obiektu użytkownika | Wybierz [oświadczenia](../active-directory/develop/developer-glossary.md#claim), które mają być zawarte w [tokenie dostępu](../active-directory/develop/developer-glossary.md#access-token) po pomyślnym zresetowaniu hasła. |

4. Kliknij przycisk **OK**.
5. Kliknij przycisk **Utwórz**, aby utworzyć przepływ użytkownika. 

## <a name="update-web-app-code"></a>Aktualizowanie kodu aplikacji internetowej

Po zarejestrowaniu aplikacji internetowej i utworzeniu przepływu użytkownika należy skonfigurować aplikację w celu korzystania z dzierżawy usługi Azure AD B2C. W tym samouczku skonfigurujesz przykładową aplikację internetową, którą można pobrać z witryny GitHub. 

[Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) lub sklonuj przykładową aplikację internetową z usługi GitHub. Upewnij się, że przykładowy plik wyodrębniono w folderze, którego łączna liczba znaków ścieżki jest mniejsza niż 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Przykładowa aplikacja internetowa platformy ASP.NET to prosta aplikacja z listą zadań, umożliwiająca tworzenie i aktualizowanie listy zadań do wykonania. Aplikacja używa [składników oprogramowania pośredniczącego Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/), aby umożliwić użytkownikom rejestrowanie się w celu korzystania z aplikacji w dzierżawie usługi Azure AD B2C. Dzięki utworzeniu przepływu użytkownika usługi Azure AD B2C użytkownicy będą mogli używać kont społecznościowych lub tworzyć konta stosowane jako ich tożsamość przy dostępie do aplikacji. 

Przykładowe rozwiązanie zawiera dwa projekty:

**Przykładowa aplikacja internetowa (TaskWebApp):** aplikacja internetowa do tworzenia i edytowania listy zadań. Ta aplikacja internetowa używa przepływu użytkownika dotyczącego **tworzenia konta lub logowania** na potrzeby tworzenia kont lub logowania użytkowników.

**Przykładowa aplikacja internetowego interfejsu API (TaskService):** internetowy interfejs API, który obsługuje następujące funkcje listy zadań: tworzenie, odczytywanie, aktualizowanie i usuwanie. Internetowy interfejs API jest chroniony przez usługę Azure AD B2C i wywoływany przez aplikację internetową.

Należy zmienić aplikację w celu korzystania z rejestracji aplikacji w dzierżawie; obejmuje to zanotowane wcześniej identyfikator aplikacji i klucz. Należy też skonfigurować utworzone przepływy użytkownika. Przykładowa aplikacja internetowa definiuje wartości konfiguracji jako ustawienia aplikacji w pliku Web.config. Aby zmienić ustawienia aplikacji:

1. Otwórz rozwiązanie **B2C-WebAPI-DotNet** w programie Visual Studio.

2. W projekcie aplikacji internetowej **TaskWebApp** otwórz plik **Web.config**. Zastąp wartość elementu `ida:Tenant` nazwą utworzonej dzierżawy. Zastąp wartość elementu `ida:ClientId` zanotowanym identyfikatorem aplikacji. Zastąp wartość elementu `ida:ClientSecret` zanotowanym kluczem.

3. W pliku **Web.config** zastąp wartość elementu `ida:SignUpSignInPolicyId` ciągiem `b2c_1_SiUpIn`. Zastąp wartość elementu `ida:EditProfilePolicyId` cięgiem `b2c_1_SiPe`. Zastąp wartość elementu `ida:ResetPasswordPolicyId` cięgiem `b2c_1_SSPR`.

## <a name="run-the-sample-web-app"></a>Uruchamianie przykładowej aplikacji internetowej

W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **TaskWebApp** i kliknij pozycję **Ustaw jako projekt startowy**.

Naciśnij klawisz **F5**, aby uruchomić aplikację internetową. Zostanie uruchomiona domyślna przeglądarka z adresem lokalnej witryny internetowej `https://localhost:44316/`. 

Przykładowa aplikacja obsługuje rejestrację, logowanie, edytowanie profilów i resetowanie haseł. Ten samouczek przedstawia proces logowania użytkownika przy użyciu adresu e-mail w celu użycia aplikacji. Możesz samodzielnie wypróbować inne scenariusze.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

1. Kliknij link **Sign up / Sign in** (Zarejestruj się / Zaloguj się) na górnym banerze, aby zarejestrować się jako użytkownik aplikacji internetowej. W tym procesie jest używany przepływ użytkownika **b2c_1_SiUpIn** zdefiniowany w poprzednim kroku.

2. Usługa Azure AD B2C wyświetli stronę logowania z linkiem rejestracji. Ponieważ nie masz jeszcze konta, kliknij link **Sign up now** (Zarejestruj się teraz). 

3. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów. 

    ![Przepływ pracy rejestracji](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Kliknij pozycję **Utwórz**, aby utworzyć konto lokalne w dzierżawie usługi Azure AD B2C.

Teraz użytkownik może logować się i korzystać z aplikacji internetowej, używając swojego adresu e-mail.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wypróbować inne samouczki usługi Azure AD B2C, możesz użyć swojej dzierżawy usługi Azure AD B2C. Możesz [usunąć dzierżawę usługi Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant), gdy nie będzie już potrzebna.

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak utworzyć dzierżawę usługi Azure AD B2C, utworzyć przepływy użytkownika i zaktualizować przykładową aplikację internetową w celu korzystania z dzierżawy usługi Azure AD B2C. Przejdź do następnego samouczka, aby dowiedzieć się, jak zarejestrować, skonfigurować i wywoływać internetowy interfejs API platformy ASP.NET chroniony przez dzierżawę usługi Azure AD B2C.

> [!div class="nextstepaction"]
> [Samouczek: chronienie internetowego interfejsu API platformy ASP.NET za pomocą usługi Azure Active Directory B2C](active-directory-b2c-tutorials-web-api.md)
