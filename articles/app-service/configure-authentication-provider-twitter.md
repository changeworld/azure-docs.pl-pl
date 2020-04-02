---
title: Konfigurowanie uwierzytelniania twitterowego
description: Dowiedz się, jak skonfigurować uwierzytelnianie na Twitterze jako dostawcę tożsamości dla usługi aplikacji lub aplikacji Usługi Azure Functions.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 11c913b12b4dcb7d2a5ffa532064b347b82904ef
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519910"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Konfigurowanie usługi aplikacji lub aplikacji Usługi Azure Do korzystania z logowania na Twitterze

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym artykule pokazano, jak skonfigurować usługę Azure App Service lub usługi Azure Functions do używania Twittera jako dostawcy uwierzytelniania.

Aby wykonać procedurę opisaną w tym artykule, potrzebujesz konta na Twitterze, które ma zweryfikowany adres e-mail i numer telefonu. Aby utworzyć nowe konto na Twitterze, przejdź do [twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Zarejestruj swoją aplikację za pomocą Twittera

1. Zaloguj się do [witryny Azure portal] i przejdź do aplikacji. Skopiuj **swój adres URL**. Użyjesz go do skonfigurowania aplikacji Twitter.
1. Przejdź do [witryny twitter deweloperzy,] zaloguj się przy użyciu poświadczeń konta twitter i wybierz pozycję **Utwórz aplikację**.
1. Wprowadź **nazwę aplikacji** i **opis aplikacji** dla nowej aplikacji. Wklej **adres URL** aplikacji w polu Adres **URL witryny.** W sekcji **Adresy URL wywołania zwrotnego** wprowadź adres URL HTTPS `/.auth/login/twitter/callback`w aplikacji App Service i dołącz ścieżkę . Na przykład `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. U dołu strony wpisz co najmniej 100 znaków w **obszarze Powiedz nam, jak ta aplikacja będzie używana,** a następnie wybierz pozycję **Utwórz**. W wyskakującym okienku **kliknij pozycję Utwórz** ponownie. Zostaną wyświetlone szczegóły aplikacji.
1. Wybierz kartę **Klucze i Tokeny dostępu.**

   Zanotuj następujące wartości:
   - Klucz INTERFEJSU API
   - Klucz tajny interfejsu API

   > [!NOTE]
   > Klucz tajny interfejsu API jest ważnym poświadczeniem zabezpieczeń. Nie udostępniaj nikomu tego klucza tajnego ani nie rozpowszechniaj go za pomocą aplikacji.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Dodawanie informacji z Twittera do aplikacji

1. Przejdź do aplikacji w [witrynie Azure portal].
1. Wybierz **pozycję Uwierzytelnianie ustawienia** > **/ autoryzacja**i upewnij się, że **uwierzytelnianie usługi aplikacji** jest **włączone**.
1. Wybierz **Twitter**.
1. Wklej `API key` wartości `API secret key` i wartości uzyskane wcześniej.
1. Kliknij przycisk **OK**.

   ![Zrzut ekranu przedstawiający ustawienia aplikacji mobilnej na Twitterze][1]

   Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do zawartości witryny i interfejsów API. Musisz autoryzować użytkowników w kodzie aplikacji.

1. (Opcjonalnie) Aby ograniczyć dostęp do witryny tylko do użytkowników uwierzytelnionych przez Twittera, ustaw **akcję do podjęcia, gdy żądanie nie zostanie uwierzytelnione** w **serwisie Twitter.** Po ustawieniu tej funkcji aplikacja wymaga uwierzytelnienia wszystkich żądań. Przekierowuje również wszystkie nieuwierzyte żądania do Twittera w celu uwierzytelnienia.

   > [!CAUTION]
   > Ograniczenie dostępu w ten sposób ma zastosowanie do wszystkich wywołań aplikacji, co może nie być pożądane w przypadku aplikacji, które mają publicznie dostępną stronę główną, jak w wielu aplikacjach jednostronicowych. W przypadku takich aplikacji **można zezwolić na żądania anonimowe (brak akcji),** aby aplikacja ręcznie uruchamiała uwierzytelnianie. Aby uzyskać więcej informacji, zobacz [Przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).

1. Wybierz **pozycję Zapisz**.

Teraz możesz używać Twittera do uwierzytelniania w aplikacji.

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twórcy Twittera]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
