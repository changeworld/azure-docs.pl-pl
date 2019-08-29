---
title: Konfigurowanie uwierzytelniania Azure Active Directory — Azure App Service
description: Dowiedz się, jak skonfigurować uwierzytelnianie Azure Active Directory dla aplikacji App Services.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 41084c3532e29b3a52c121d48226c5a45857d5dc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088232"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Konfigurowanie aplikacji App Service do korzystania z Azure Active Directory logowania

> [!NOTE]
> W tej chwili usługa AAD v2 (w tym MSAL) nie jest obsługiwana w przypadku usługi Azure App Services i Azure Functions. Sprawdź aktualizacje.
>

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym artykule opisano sposób konfigurowania usługi Azure App Services do korzystania z Azure Active Directory jako dostawcy uwierzytelniania.

## <a name="express"> </a>Skonfiguruj przy użyciu ustawień ekspresowych

1. W [Azure Portal]przejdź do aplikacji App Service. W lewym okienku nawigacji wybierz pozycję **uwierzytelnianie/autoryzacja**.
2. Jeśli **uwierzytelnianie/autoryzacja** nie jest włączona, wybierz pozycję **włączone**.
3. Wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Express** w obszarze **tryb zarządzania**.
4. Wybierz **przycisk OK** , aby zarejestrować aplikację App Service w Azure Active Directory. Spowoduje to utworzenie nowej rejestracji aplikacji. Jeśli chcesz zamiast tego wybrać istniejącą rejestrację aplikacji, kliknij pozycję **Wybierz istniejącą aplikację** , a następnie wyszukaj nazwę wcześniej utworzonej rejestracji aplikacji w ramach dzierżawy.
   Kliknij pozycję Rejestracja aplikacji, aby ją zaznaczyć, a następnie kliknij przycisk **OK**. Następnie kliknij przycisk **OK** na stronie Ustawienia Azure Active Directory.
   Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji.
5. Obowiązkowe Aby ograniczyć dostęp do witryny tylko do użytkowników uwierzytelnionych przez Azure Active Directory, należy ustawić **akcję podejmowaną, gdy żądanie nie zostanie uwierzytelnione** w celu zalogowania się **przy użyciu Azure Active Directory**. Wymaga to uwierzytelnienia wszystkich żądań, a wszystkie nieuwierzytelnione żądania są przekierowywane do Azure Active Directory na potrzeby uwierzytelniania.

> [!CAUTION]
> Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być odpowiednie dla aplikacji, które chcą korzystać z publicznie dostępnej strony głównej, tak jak w przypadku aplikacji jednostronicowych. W przypadku takich aplikacji **Zezwalanie na żądania anonimowe (nie akcja)** może być preferowane, z aplikacją ręcznie rozpoczynającą logowanie, zgodnie z opisem w [tym miejscu](overview-authentication-authorization.md#authentication-flow).

6. Kliknij polecenie **Zapisz**.

## <a name="advanced"> </a>Konfiguruj z ustawieniami zaawansowanymi

Ustawienia konfiguracji można także podać ręcznie. Jest to preferowane rozwiązanie, jeśli dzierżawa Azure Active Directory, której chcesz użyć, różni się od dzierżawy, w której zalogowanie się do platformy Azure. Aby ukończyć konfigurację, musisz najpierw utworzyć rejestrację w Azure Active Directory, a następnie podać niektóre szczegóły rejestracji, aby App Service.

### <a name="register"> </a>Zarejestruj aplikację App Service przy użyciu Azure Active Directory

1. Zaloguj się do [Azure Portal]i przejdź do aplikacji App Service. Skopiuj **adres URL**aplikacji. Ta funkcja zostanie użyta do skonfigurowania rejestracji aplikacji Azure Active Directory.
2. Przejdź do **Active Directory**, a następnie wybierz **rejestracje aplikacji**, a następnie kliknij pozycję **rejestracja nowej aplikacji** u góry, aby rozpocząć nową rejestrację aplikacji. 
3. Na stronie **Tworzenie** wprowadź **nazwę** rejestracji aplikacji, wybierz typ **aplikacji sieci Web/interfejsu API** , w polu **adres URL logowania** Wklej adres URL aplikacji (z kroku 1). Następnie kliknij, aby **utworzyć**.
4. W ciągu kilku sekund powinna zostać wyświetlona nowa Rejestracja aplikacji, która została właśnie utworzona.
5. Po dodaniu rejestracji aplikacji kliknij nazwę rejestracji aplikacji, kliknij pozycję **Ustawienia** u góry, a następnie kliknij pozycję **Właściwości** . 
6. W polu **Identyfikator URI aplikacji** Wklej adres URL aplikacji (z kroku 1), a także **adres URL strony głównej** wklejania w adresie URL aplikacji (z kroku 1), a następnie kliknij przycisk **Zapisz** .
7. Teraz kliknij adresy URL **odpowiedzi**, Edytuj **adres URL odpowiedzi**, wklej adres URL aplikacji (z kroku 1), a następnie dołącz go na końcu adresu URL, */.auth/login/AAD/callback* (na przykład `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Kliknij polecenie **Zapisz**.

   > [!NOTE]
   > Możesz użyć tej samej rejestracji aplikacji w wielu domenach przez dodanie dodatkowych **adresów URL odpowiedzi**. Upewnij się, że modeluje każde wystąpienie App Service ze swoją rejestracją, więc ma własne uprawnienia i zgodę. Należy również rozważyć użycie oddzielnych rejestracji aplikacji dla oddzielnych gniazd witryny. Ma to na celu uniknięcie udostępniania uprawnień między środowiskami, dzięki czemu usterka w nowym kodzie, który jest testowany, nie ma wpływu na produkcję.
    
8. W tym momencie Skopiuj **Identyfikator aplikacji** dla aplikacji. Zachowaj je do późniejszego użycia. Będzie ona potrzebna do skonfigurowania aplikacji App Service.
9. Zamknij stronę **zarejestrowanej aplikacji** . Na stronie **rejestracje aplikacji** kliknij przycisk **punkty końcowe** u góry, a następnie skopiuj adres URL **punktu końcowego logowania do usługi WS-Federation** , a następnie usuń `/wsfed` koniec z adresu URL. Wynik końcowy powinien wyglądać następująco `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000`. Nazwa domeny może być inna dla niesuwerennej chmury. Będzie on używany jako adres URL wystawcy w przyszłości.

### <a name="secrets"> </a>Dodawanie Azure Active Directory informacji do aplikacji App Service

1. Wróć do [Azure Portal], przejdź do aplikacji App Service. Kliknij pozycję **uwierzytelnianie/autoryzacja**. Jeśli funkcja uwierzytelniania/autoryzacji nie jest włączona, Przełącz przełącznik na wartość **włączone**. Aby skonfigurować aplikację, kliknij pozycję **Azure Active Directory**w obszarze dostawcy uwierzytelniania.

    Obowiązkowe Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji. Ustaw **akcję do wykonania, gdy żądanie nie zostanie uwierzytelnione** w celu zalogowania się **za pomocą Azure Active Directory**. Ta opcja wymaga uwierzytelnienia wszystkich żądań, a wszystkie nieuwierzytelnione żądania są przekierowywane do Azure Active Directory na potrzeby uwierzytelniania.
2. W obszarze Konfiguracja uwierzytelniania Active Directory kliknij pozycję **Zaawansowane** w obszarze **tryb zarządzania**. Wklej identyfikator aplikacji do pola Identyfikator klienta (z kroku 8) i wklej adres URL (z kroku 9) do wartości adresu URL wystawcy. Następnie kliknij przycisk **OK**.
3. Na stronie Konfiguracja uwierzytelniania Active Directory kliknij przycisk **Zapisz**.

Teraz można przystąpić do uwierzytelniania w aplikacji App Service za pomocą Azure Active Directory.

## <a name="configure-a-native-client-application"></a>Konfigurowanie natywnej aplikacji klienckiej
Można rejestrować natywnych klientów, co zapewnia większą kontrolę nad mapowaniem uprawnień. Jest to konieczne, jeśli chcesz wykonać logowania przy użyciu biblioteki klienckiej, takiej jak **Active Directory Authentication Library**.

1. Przejdź do **Azure Active Directory** w [Azure Portal].
2. Na lewym pasku nawigacyjnym wybierz pozycję **rejestracje aplikacji**. Kliknij pozycję **rejestracja nowej aplikacji** u góry.
4. Na stronie **Tworzenie** wprowadź **nazwę** rejestracji aplikacji. W polu **Typ aplikacji**wybierz opcję **natywne** .
5. W polu **URI przekierowania** wprowadź punkt końcowy */.auth/login/done* witryny przy użyciu schematu https. Ta wartość powinna być podobna do *https://contoso.azurewebsites.net/.auth/login/done* . W przypadku tworzenia aplikacji systemu Windows zamiast identyfikatora URI Użyj [identyfikatora SID pakietu](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) .
5. Kliknij przycisk **Utwórz**.
6. Po dodaniu rejestracji aplikacji wybierz ją, aby ją otworzyć. Znajdź **Identyfikator aplikacji** i zanotuj tę wartość.
7. Kliknij pozycję **wszystkie ustawienia** > **wymagane uprawnienia** > **Dodaj** > **Wybierz interfejs API**.
8. Wpisz nazwę aplikacji App Service zarejestrowanej wcześniej, aby ją wyszukać, a następnie wybierz ją, a następnie kliknij przycisk **Wybierz**.
9. Wybierz **pozycję \<Access APP_NAME >** . Następnie kliknij pozycję **Wybierz**. Następnie kliknij przycisk **Gotowe**.

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
