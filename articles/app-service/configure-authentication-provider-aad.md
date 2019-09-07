---
title: Konfigurowanie uwierzytelniania Azure Active Directory — Azure App Service
description: Dowiedz się, jak skonfigurować Azure Active Directory uwierzytelnianie dla aplikacji App Service.
author: cephalin
services: app-service
documentationcenter: ''
manager: gwallace
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service
ms.workload: web,mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: cephalin
ms.custom: fasttrack-edit
ms.openlocfilehash: 8de464a00867dd397f28de1dc35cf264244f6905
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743253"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Konfigurowanie aplikacji App Service do korzystania z Azure Active Directory logowania

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

> [!NOTE]
> W tej chwili usługi AAD v2 (w tym MSAL) nie są obsługiwane w przypadku Azure App Service i Azure Functions.
>

W tym artykule opisano sposób konfigurowania Azure App Service, aby użyć Azure Active Directory jako dostawcy uwierzytelniania.

Zaleca się skonfigurowanie każdej aplikacji App Service ze swoją rejestracją, więc ma ona własne uprawnienia i zgodę. Należy również rozważyć użycie oddzielnych rejestracji aplikacji dla oddzielnych miejsc wdrożenia. Pozwala to uniknąć udostępniania uprawnień między środowiskami, co sprawia, że problem w nowym kodzie, który jest testowany, nie ma wpływu na produkcję.

## <a name="express"> </a>Skonfiguruj przy użyciu ustawień ekspresowych

1. W [Azure Portal]przejdź do aplikacji App Service. W lewym okienku nawigacji wybierz pozycję **uwierzytelnianie/autoryzacja**.
2. Jeśli **uwierzytelnianie/autoryzacja** nie jest włączona, wybierz pozycję **włączone**.
3. Wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Express** w obszarze **tryb zarządzania**.
4. Wybierz **przycisk OK** , aby zarejestrować aplikację App Service w Azure Active Directory. Spowoduje to utworzenie nowej rejestracji aplikacji. Jeśli chcesz zamiast tego wybrać istniejącą rejestrację aplikacji, kliknij pozycję **Wybierz istniejącą aplikację** , a następnie wyszukaj nazwę wcześniej utworzonej rejestracji aplikacji w ramach dzierżawy. Kliknij pozycję Rejestracja aplikacji, aby ją zaznaczyć, a następnie kliknij przycisk **OK**. Następnie kliknij przycisk **OK** na stronie Ustawienia Azure Active Directory.
Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji.
5. Obowiązkowe Aby ograniczyć dostęp do aplikacji tylko do użytkowników uwierzytelnionych przez Azure Active Directory, należy ustawić **akcję podejmowaną, gdy żądanie nie zostanie uwierzytelnione** w celu **zalogowania się przy użyciu Azure Active Directory**. Wymaga to uwierzytelnienia wszystkich żądań, a wszystkie nieuwierzytelnione żądania są przekierowywane do Azure Active Directory na potrzeby uwierzytelniania.

    > [!NOTE]
    > Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być odpowiednie dla aplikacji, które chcą korzystać z publicznie dostępnej strony głównej, tak jak w przypadku aplikacji jednostronicowych. W przypadku takich aplikacji **Zezwalanie na żądania anonimowe (nie akcja)** może być preferowane, z aplikacją ręcznie rozpoczynającą logowanie, zgodnie z opisem w [tym miejscu](overview-authentication-authorization.md#authentication-flow).
6. Kliknij polecenie **Zapisz**.

## <a name="advanced"> </a>Konfiguruj z ustawieniami zaawansowanymi

Ustawienia konfiguracji można także podać ręcznie, jeśli dzierżawa Azure Active Directory, której chcesz użyć, różni się od dzierżawy, w której zalogowanie się do platformy Azure. Aby ukończyć konfigurację, musisz najpierw utworzyć rejestrację w Azure Active Directory, a następnie podać niektóre szczegóły rejestracji, aby App Service.

### <a name="register"> </a>Tworzenie rejestracji aplikacji w usłudze Azure AD dla aplikacji App Service

Podczas ręcznego tworzenia rejestracji aplikacji należy zwrócić uwagę na trzy informacje, które będą potrzebne później podczas konfigurowania aplikacji App Service: identyfikator klienta, identyfikator dzierżawy oraz opcjonalnie klucz tajny klienta i identyfikator URI aplikacji.

1. W [Azure Portal]przejdź do aplikacji App Service i zanotuj **adres URL**aplikacji. Zostanie ona użyta do skonfigurowania rejestracji aplikacji Azure Active Directory.
1. W [Azure Portal]z menu po lewej stronie wybierz pozycję **Active Directory** > **rejestracje aplikacji** > **nową rejestrację**. 
1. Na stronie **zarejestruj aplikację** wprowadź **nazwę** rejestracji aplikacji.
1. W obszarze **Identyfikator URI przekierowania**wybierz pozycję **Sieć Web** , a następnie wpisz adres URL aplikacji App Service `/.auth/login/aad/callback`i dołącz ścieżkę. Na przykład `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Następnie wybierz przycisk **Utwórz**.
1. Po utworzeniu rejestracji aplikacji Skopiuj **Identyfikator aplikacji (klienta)** i **Identyfikator katalogu (dzierżawcy)** w przyszłości.
1. Wybierz **znakowanie**. W polu **adres URL strony głównej**wpisz adres url aplikacji App Service i wybierz pozycję **Zapisz**.
1. Wybierz opcję **Uwidocznij zestaw interfejsów API** > . Wklej adres URL aplikacji App Service i wybierz pozycję **Zapisz**.

    > [!NOTE]
    > Ta wartość to **Identyfikator URI identyfikatora aplikacji** rejestracji aplikacji. Jeśli chcesz mieć aplikację sieci Web frontonu do uzyskiwania dostępu do interfejsu API zaplecza, na przykład, aby zaplecze jawnie udzielić dostępu do frontonu, musisz mieć **Identyfikator URI identyfikatora aplikacji** *frontonu* po skonfigurowaniu zasobu aplikacji App Service na < C2 zaplecze >.
1. Wybierz polecenie **Dodaj zakres**. W polu **Nazwa zakresu**wpisz *user_impersonation*. W polach tekstowych wpisz nazwę i opis zakresu zgody, które użytkownicy mają zobaczyć na stronie zgody, na przykład *dostęp do mojej aplikacji*. Po zakończeniu kliknij pozycję **Dodaj zakres**.
1. Obowiązkowe Aby utworzyć klucz tajny klienta, wybierz pozycję **Certyfikaty &**  > wpisy tajne**Nowy klient Dodaj wpis tajny** > . Skopiuj wartość klucza tajnego klienta podaną na stronie. Gdy przejdziesz dalej, nie będzie on ponownie wyświetlany.
1. Obowiązkowe Aby dodać wiele **adresów URL odpowiedzi**, wybierz pozycję **uwierzytelnianie** w menu.

### <a name="secrets"> </a>Dodawanie Azure Active Directory informacji do aplikacji App Service

1. W [Azure Portal]przejdź do aplikacji App Service. Z menu po lewej stronie wybierz pozycję **uwierzytelnianie/autoryzacja**. Jeśli funkcja uwierzytelniania/autoryzacji nie jest włączona, wybierz pozycję **włączone**. 
1. Obowiązkowe Domyślnie uwierzytelnianie App Service zezwala na nieuwierzytelniony dostęp do aplikacji. Aby wymusić uwierzytelnianie użytkowników, należy ustawić **akcję podejmowaną, gdy żądanie nie zostanie uwierzytelnione** w celu **zalogowania się za pomocą Azure Active Directory**.
1. W obszarze dostawcy uwierzytelniania wybierz pozycję **Azure Active Directory**.
1. W obszarze **tryb zarządzania**wybierz pozycję **Zaawansowane** i Skonfiguruj uwierzytelnianie App Service zgodnie z poniższą tabelą:

    |Pole|Opis|
    |-|-|
    |Identyfikator klienta| Użyj **identyfikatora aplikacji (klienta)** rejestracji aplikacji. |
    |Identyfikator wystawcy| Użyj `https://login.microsoftonline.com/<tenant-id>`i Zastąp  *\<identyfikator dzierżawcy >* identyfikatorem **katalogu (dzierżawy)** rejestracji aplikacji. |
    |Klucz tajny klienta (opcjonalnie)| Użyj klucza tajnego klienta wygenerowanego podczas rejestracji aplikacji.|
    |Dozwoleni odbiorcy z tokenem| Jeśli jest to aplikacja *zaplecza* i chcesz zezwolić na tokeny uwierzytelniania z aplikacji frontonu, Dodaj **Identyfikator URI identyfikatora aplikacji** *frontonu* . |

    > [!NOTE]
    > Skonfigurowany **Identyfikator klienta** jest *zawsze* niejawnie traktowany jako dozwolony odbiorca, niezależnie od tego, jak skonfigurowano **dozwolonych odbiorców tokenu**.
1. Wybierz pozycję **OK**, a następnie wybierz pozycję **Zapisz**.

Teraz można przystąpić do uwierzytelniania w aplikacji App Service za pomocą Azure Active Directory.

## <a name="configure-a-native-client-application"></a>Konfigurowanie natywnej aplikacji klienckiej
Możesz zarejestrować natywnych klientów, jeśli chcesz wykonać logowania przy użyciu biblioteki klienckiej, takiej jak **Active Directory Authentication Library**.

1. W [Azure Portal]z menu po lewej stronie wybierz pozycję **Active Directory** > **rejestracje aplikacji** > **nową rejestrację**. 
1. Na stronie **zarejestruj aplikację** wprowadź **nazwę** rejestracji aplikacji.
1. W polu **Identyfikator URI przekierowania**wybierz pozycję **Klient publiczny (Mobile & Desktop)** , a następnie wpisz adres URL aplikacji App Service i `/.auth/login/aad/callback`Dołącz ścieżkę. Na przykład `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Następnie wybierz przycisk **Utwórz**.

    > [!NOTE]
    > W przypadku aplikacji systemu Windows zamiast tego użyj [identyfikatora SID pakietu](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) jako identyfikatora URI.
1. Po utworzeniu rejestracji aplikacji skopiuj wartość **Identyfikator aplikacji (klienta)** .
1. W menu po lewej stronie wybierz pozycję >  **uprawnienia interfejsu API** **Dodaj uprawnienie** > **Moje interfejsy API**.
1. Wybierz rejestrację aplikacji utworzoną wcześniej dla aplikacji App Service. Jeśli nie widzisz rejestracji aplikacji, sprawdź, czy został dodany zakres **user_impersonation** w temacie [Tworzenie rejestracji aplikacji w usłudze Azure AD dla aplikacji App Service](#register).
1. Wybierz pozycję **user_impersonation** , a następnie kliknij pozycję **Dodaj uprawnienia**.

Skonfigurowano natywną aplikację kliencką, która może uzyskiwać dostęp do aplikacji App Service.

## <a name="related-content"> </a>Powiązana zawartość

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
