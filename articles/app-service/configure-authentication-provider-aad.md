---
title: Konfigurowanie uwierzytelniania usługi Azure AD
description: Dowiedz się, jak skonfigurować uwierzytelnianie Azure Active Directory jako dostawcę tożsamości dla aplikacji App Service.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b833604ce18873e22c22990a26dcbae1d9928628
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670885"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>Konfigurowanie aplikacji App Service do korzystania z usługi Azure AD login

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym artykule opisano sposób konfigurowania Azure App Service do używania Azure Active Directory (Azure AD) jako dostawcy uwierzytelniania.

> [!NOTE]
> W tej chwili Azure App Service i Azure Functions są obsługiwane tylko przez usługę Azure AD v 1.0. Nie są one obsługiwane przez [firmę Microsoft Identity platform v 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-overview), która obejmuje biblioteki uwierzytelniania firmy Microsoft (MSAL).

Podczas konfigurowania aplikacji i uwierzytelniania postępuj zgodnie z następującymi najlepszymi rozwiązaniami:

- Nadaj każdej aplikacji App Service swoje własne uprawnienia i zgodę.
- Skonfiguruj każdą aplikację App Service ze swoją rejestracją.
- Unikaj udostępniania uprawnień między środowiskami przy użyciu oddzielnych rejestracji aplikacji dla oddzielnych miejsc wdrożenia. W przypadku testowania nowego kodu to rozwiązanie może pomóc zapobiec problemom związanym z aplikacją produkcyjną.

## <a name="express"> </a>Skonfiguruj przy użyciu ustawień ekspresowych

1. W [Azure Portal]przejdź do aplikacji App Service.
1. Wybierz pozycję **ustawienia** > **uwierzytelnianie/autoryzacja** w okienku po lewej stronie i upewnij się, że **App Service uwierzytelnianie** jest **włączone**.
1. Wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Express** w obszarze **tryb zarządzania**.
1. Wybierz **przycisk OK** , aby zarejestrować aplikację App Service w Azure Active Directory. Zostanie utworzona nowa Rejestracja aplikacji.

   Jeśli chcesz wybrać istniejącą rejestrację aplikacji:

   1. Wybierz **pozycję Wybierz istniejącą aplikację** , a następnie wyszukaj nazwę wcześniej utworzonej rejestracji aplikacji w ramach dzierżawy.
   1. Wybierz rejestrację aplikacji, a następnie wybierz przycisk **OK**.
   1. Następnie wybierz przycisk **OK** na stronie Ustawienia Azure Active Directory.

   Domyślnie App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji.
1. Obowiązkowe Aby ograniczyć dostęp do aplikacji tylko do użytkowników uwierzytelnionych przez Azure Active Directory, ustaw **akcję do wykonania, gdy żądanie nie zostanie uwierzytelnione** w celu **zalogowania się przy użyciu Azure Active Directory**. Po ustawieniu tej funkcji aplikacja wymaga uwierzytelnienia wszystkich żądań. Przekierowuje także wszystkie nieuwierzytelnione do Azure Active Directory na potrzeby uwierzytelniania.

    > [!CAUTION]
    > Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być pożądane dla aplikacji, które mają publicznie dostępną stronę główną, tak jak w przypadku aplikacji jednostronicowych. W przypadku takich aplikacji **Zezwalaj na żądania anonimowe (żadna akcja)** może być preferowana, a aplikacja ręcznie rozpoczyna logowanie się. Aby uzyskać więcej informacji, zobacz temat [przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).
1. Wybierz pozycję **Zapisz**.

## <a name="advanced"> </a>Konfiguruj z ustawieniami zaawansowanymi

Ustawienia aplikacji można skonfigurować ręcznie, jeśli chcesz używać dzierżawy usługi Azure AD innej niż ta, która jest używana do logowania się do platformy Azure. Aby ukończyć tę konfigurację niestandardową, należy wykonać następujące czynności:

1. Utwórz rejestrację w usłudze Azure AD.
1. Podaj niektóre szczegóły rejestracji do App Service.

### <a name="register"> </a>Tworzenie rejestracji aplikacji w usłudze Azure AD dla aplikacji App Service

Podczas konfigurowania aplikacji App Service będą potrzebne następujące informacje:

- Identyfikator klienta
- Identyfikator dzierżawy
- Klucz tajny klienta (opcjonalnie)
- Identyfikator URI identyfikatora aplikacji

Wykonaj poniższe czynności:

1. Zaloguj się do [Azure Portal] i przejdź do aplikacji App Service. Zanotuj **adres URL**aplikacji. Zostanie ona użyta do skonfigurowania rejestracji aplikacji Azure Active Directory.
1. Wybierz pozycję **Azure Active Directory** > **rejestracje aplikacji** > **Nowa rejestracja**.
1. Na stronie **zarejestruj aplikację** wprowadź **nazwę** rejestracji aplikacji.
1. W obszarze **Identyfikator URI przekierowania**wybierz pozycję **Sieć Web** i wprowadź adres URL aplikacji App Service i Dołącz `/.auth/login/aad/callback`ścieżki. Na przykład `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Wybierz pozycję **Utwórz**.
1. Po utworzeniu rejestracji aplikacji Skopiuj **Identyfikator aplikacji (klienta)** i **Identyfikator katalogu (dzierżawcy)** w przyszłości.
1. Wybierz **znakowanie**. W polu **adres URL strony głównej**wprowadź adres url aplikacji App Service i wybierz pozycję **Zapisz**.
1. Wybierz opcję **Uwidocznij zestaw interfejsów API** > . Wklej adres URL aplikacji App Service i wybierz pozycję **Zapisz**.

   > [!NOTE]
   > Ta wartość to **Identyfikator URI identyfikatora aplikacji** rejestracji aplikacji. Jeśli aplikacja sieci Web wymaga dostępu do interfejsu API w chmurze, podczas konfigurowania zasobu App Service chmury potrzebny jest **Identyfikator URI aplikacji** sieci Web. Możesz użyć tego przykładu, jeśli chcesz, aby usługa w chmurze jawnie zezwalała na dostęp do aplikacji sieci Web.

1. Wybierz polecenie **Dodaj zakres**.
   1. W polu **Nazwa zakresu**wprowadź *user_impersonation*.
   1. W polach tekstowych wprowadź nazwę i opis zakresu zgody, które użytkownicy mają zobaczyć na stronie zgody. Na przykład wprowadź *dostęp do mojej aplikacji*. 
   1. Wybierz pozycję **Dodaj zakres**.
1. Obowiązkowe Aby utworzyć klucz tajny klienta, wybierz kolejno pozycje **certyfikaty &** wpisy tajne > **Nowy wpis tajny klienta** > **Dodaj**. Skopiuj wartość klucza tajnego klienta podaną na stronie. Nie zostanie on wyświetlony ponownie.
1. Obowiązkowe Aby dodać wiele **adresów URL odpowiedzi**, wybierz pozycję **uwierzytelnianie**.

### <a name="secrets"> </a>Dodawanie Azure Active Directory informacji do aplikacji App Service

1. W [Azure Portal]przejdź do aplikacji App Service. 
1. Wybierz pozycję **ustawienia > uwierzytelnianie/autoryzacja** w okienku po lewej stronie i upewnij się, że **App Service uwierzytelnianie** jest **włączone**.
1. Obowiązkowe Domyślnie uwierzytelnianie App Service zezwala na nieuwierzytelniony dostęp do aplikacji. Aby wymusić uwierzytelnianie użytkowników, należy ustawić **akcję podejmowaną, gdy żądanie nie zostanie uwierzytelnione** w celu **zalogowania się za pomocą Azure Active Directory**.
1. W obszarze dostawcy uwierzytelniania wybierz pozycję **Azure Active Directory**.
1. W obszarze **tryb zarządzania**wybierz pozycję **Zaawansowane** i Skonfiguruj uwierzytelnianie App Service zgodnie z poniższą tabelą:

    |Pole|Opis|
    |-|-|
    |Identyfikator klienta| Użyj **identyfikatora aplikacji (klienta)** rejestracji aplikacji. |
    |Identyfikator wystawcy| Użyj `https://login.microsoftonline.com/<tenant-id>`i Zastąp *\<identyfikator dzierżawy >* **identyfikatorem katalogu (dzierżawy)** rejestracji aplikacji. |
    |Klucz tajny klienta (opcjonalnie)| Użyj klucza tajnego klienta wygenerowanego podczas rejestracji aplikacji.|
    |Dozwolone odbiorcy tokenu| Jeśli jest to aplikacja w chmurze lub na serwerze i chcesz zezwolić na tokeny uwierzytelniania z aplikacji sieci Web, w tym miejscu Dodaj **Identyfikator URI aplikacji** sieci Web. |

    > [!NOTE]
    > Skonfigurowany **Identyfikator klienta** jest *zawsze* niejawnie traktowany jako dozwolony odbiorca, niezależnie od tego, jak skonfigurowano **dozwolonych odbiorców tokenu**.
1. Wybierz przycisk **OK**, a następnie wybierz pozycję **Zapisz**.

Teraz można przystąpić do uwierzytelniania w aplikacji App Service za pomocą Azure Active Directory.

## <a name="configure-a-native-client-application"></a>Konfigurowanie natywnej aplikacji klienckiej

Możesz zarejestrować natywnych klientów, aby zezwalać na uwierzytelnianie przy użyciu biblioteki klienckiej, takiej jak **Active Directory Authentication Library**.

1. W [Azure Portal]wybierz pozycję **Active Directory** > **rejestracje aplikacji** > **nową rejestrację**.
1. Na stronie **zarejestruj aplikację** wprowadź **nazwę** rejestracji aplikacji.
1. W obszarze **Identyfikator URI przekierowania**wybierz pozycję **Klient publiczny (Mobile & Desktop)** i wprowadź adres URL aplikacji App Service i Dołącz `/.auth/login/aad/callback`ścieżkę. Na przykład `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Wybierz pozycję **Utwórz**.

    > [!NOTE]
    > W przypadku aplikacji systemu Windows zamiast tego użyj [identyfikatora SID pakietu](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) jako identyfikatora URI.
1. Po utworzeniu rejestracji aplikacji skopiuj wartość **Identyfikator aplikacji (klienta)** .
1. Wybierz pozycję **uprawnienia interfejsu API** > **Dodaj uprawnienie** > **Moje interfejsy API**.
1. Wybierz rejestrację aplikacji utworzoną wcześniej dla aplikacji App Service. Jeśli nie widzisz rejestracji aplikacji, upewnij się, że dodano zakres **user_impersonation** w temacie [Tworzenie rejestracji aplikacji w usłudze Azure AD dla aplikacji App Service](#register).
1. Wybierz pozycję **user_impersonation**, a następnie wybierz pozycję **Dodaj uprawnienia**.

Skonfigurowano natywną aplikację kliencką, która może uzyskiwać dostęp do aplikacji App Service.

## <a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[alternative method]:#advanced
