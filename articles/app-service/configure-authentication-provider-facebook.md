---
title: Konfigurowanie uwierzytelniania na Facebooku
description: Dowiedz się, jak skonfigurować uwierzytelnianie na Facebooku jako dostawcę tożsamości dla aplikacji Usługi app service.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: cd9c8a1bab3616b9b4eb1fe97ee3a9b2307ba77b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671936"
---
# <a name="configure-your-app-service-app-to-use-facebook-login"></a>Konfigurowanie aplikacji usługi App Service do korzystania z logowania na Facebooku

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym artykule pokazano, jak skonfigurować usługę Azure App Service do używania Facebooka jako dostawcy uwierzytelniania.

Aby wykonać procedurę opisaną w tym artykule, potrzebujesz konta na Facebooku ze zweryfikowanym adresem e-mail i numerem telefonu komórkowego. Aby utworzyć nowe konto na Facebooku, przejdź do [facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Zarejestruj swoją aplikację na Facebooku

1. Przejdź do [witryny facebook deweloperzy] i zaloguj się przy użyciu danych logowania do konta na Facebooku.

   Jeśli nie masz konta Na Facebooku dla programistów, wybierz **pozycję Wprowadzenie** i postępuj zgodnie z instrukcjami rejestracji.
1. Wybierz **pozycję Moje aplikacje** > **Dodaj nową aplikację**.
1. W **polu Nazwa wyświetlana:**
   1. Wpisz unikatową nazwę aplikacji.
   1. Podaj **kontaktowy adres e-mail**.
   1. Wybierz **pozycję Utwórz identyfikator aplikacji**.
   1. Zakończ kontrolę bezpieczeństwa.

   Zostanie otwarty pulpit nawigacyjny dla deweloperów nowej aplikacji Facebook.
1. Wybierz **dashboard** > **Facebook Login** > **Set up** > **Web**.
1. W lewej nawigacji w obszarze **Logowanie do Facebooka**wybierz **pozycję Ustawienia**.
1. W polu **Prawidłowe identyfikatory URI przekierowania OAuth** wprowadź . `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` Pamiętaj, `<app-name>` aby zastąpić nazwą aplikacji usługi Azure App Service.
1. Wybierz pozycję **Zapisz zmiany**.
1. W lewym okienku wybierz pozycję **Ustawienia** > **podstawowe**. 
1. W polu **Klucz tajności aplikacji** wybierz pozycję **Pokaż**. Skopiuj wartości **identyfikatora aplikacji** i **wpisu tajnego aplikacji**. Można ich użyć później, aby skonfigurować aplikację usługi App Service na platformie Azure.

   > [!IMPORTANT]
   > Klucz tajny aplikacji jest ważnym poświadczeniem zabezpieczeń. Nie udostępniaj nikomu tego klucza tajnego ani nie rozpowszechniaj go w aplikacji klienckiej.
   >

1. Konto na Facebooku użyte do zarejestrowania aplikacji jest administratorem aplikacji. W tym momencie tylko administratorzy mogą zalogować się do tej aplikacji.

   Aby uwierzytelnić inne konta na Facebooku, wybierz **pozycję Recenzja aplikacji** i włącz **upublicznianie \<nazwy aplikacji>,** aby umożliwić ogółowi społeczeństwa dostęp do aplikacji przy użyciu uwierzytelniania na Facebooku.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Dodawanie informacji z Facebooka do aplikacji

1. Zaloguj się do [witryny Azure portal] i przejdź do aplikacji usługi App Service.
1. Wybierz **pozycję Uwierzytelnianie ustawienia** > **/ autoryzacja**i upewnij się, że **uwierzytelnianie usługi aplikacji** jest **włączone**.
1. Wybierz **pozycję Facebook**, a następnie wklej wartości identyfikator aplikacji i klucz tajny aplikacji, które zostały uzyskane wcześniej. Włącz wszystkie zakresy wymagane przez aplikację.
1. Kliknij przycisk **OK**.

   ![Zrzut ekranu przedstawiający ustawienia aplikacji mobilnej na Facebooku][0]

    Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do zawartości witryny i interfejsów API. Musisz autoryzować użytkowników w kodzie aplikacji.
1. (Opcjonalnie) Aby ograniczyć dostęp tylko do użytkowników uwierzytelnionych przez Facebooka, ustaw **akcję do podjęcia, gdy żądanie nie zostanie uwierzytelnione** na **Facebooku**. Po ustawieniu tej funkcji aplikacja wymaga uwierzytelnienia wszystkich żądań. Przekierowuje również wszystkie nieuwierzyte żądania do Facebooka w celu uwierzytelnienia.

   > [!CAUTION]
   > Ograniczenie dostępu w ten sposób ma zastosowanie do wszystkich wywołań aplikacji, co może nie być pożądane w przypadku aplikacji, które mają publicznie dostępną stronę główną, jak w wielu aplikacjach jednostronicowych. W przypadku takich aplikacji **można zezwolić na żądania anonimowe (brak akcji),** aby aplikacja ręcznie uruchamiała uwierzytelnianie. Aby uzyskać więcej informacji, zobacz [Przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).

1. Wybierz **pozycję Zapisz**.

Teraz możesz używać Facebooka do uwierzytelniania w aplikacji.

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Twórcy Facebooka]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal Azure]: https://portal.azure.com/
