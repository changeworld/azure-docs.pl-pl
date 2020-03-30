---
title: Konfigurowanie uwierzytelniania Google
description: Dowiedz się, jak skonfigurować uwierzytelnianie Google jako dostawcę tożsamości dla aplikacji Usługi app service.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom: seodec18
ms.openlocfilehash: 81ce3e393d308323c8d5a3d688c16c9b45e7be9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74670827"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>Konfigurowanie aplikacji Usługi App Service do korzystania z logowania Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie pokazano, jak skonfigurować usługę Azure App Service do używania Google jako dostawcy uwierzytelniania.

Aby wykonać procedurę opisaną w tym temacie, musisz mieć konto Google ze zweryfikowanym adresem e-mail. Aby utworzyć nowe konto Google, przejdź do strony [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Zarejestruj swoją aplikację w Google

1. Postępuj zgodnie z dokumentacją Google w [Google Sign-In dla aplikacji po stronie serwera,](https://developers.google.com/identity/sign-in/web/server-side-flow) aby utworzyć identyfikator klienta i klucz tajny klienta. Nie ma potrzeby wprowadzania żadnych zmian w kodzie. Wystarczy użyć następujących informacji:
    - W przypadku **autoryzowanych plików JavaScript Origin należy**używać `https://<app-name>.azurewebsites.net` z nazwą aplikacji w * \<>nazwy aplikacji *.
    - W przypadku **identyfikatora URI autoryzowanego przekierowania**należy użyć `https://<app-name>.azurewebsites.net/.auth/login/google/callback`pliku .
1. Skopiuj identyfikator aplikacji i wartości tajne aplikacji.

    > [!IMPORTANT]
    > Klucz tajny aplikacji jest ważnym poświadczeniem zabezpieczeń. Nie udostępniaj nikomu tego klucza tajnego ani nie rozpowszechniaj go w aplikacji klienckiej.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Dodawanie informacji Google do aplikacji

1. W [witrynie Azure portal]przejdź do aplikacji usługi App Service.
1. Wybierz **pozycję Uwierzytelnianie ustawienia** > **/ autoryzacja**i upewnij się, że **uwierzytelnianie usługi aplikacji** jest **włączone**.
1. Wybierz **Google**, a następnie wklej wartości identyfikator aplikacji i klucz tajny aplikacji, które zostały uzyskane wcześniej. Włącz wszystkie zakresy wymagane przez aplikację.
1. Kliknij przycisk **OK**.

   Usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do zawartości witryny i interfejsów API. Aby uzyskać więcej informacji, zobacz [Autoryzowanie lub odmawianie użytkownikom](app-service-authentication-how-to.md#authorize-or-deny-users).

1. (Opcjonalnie) Aby ograniczyć dostęp do witryny tylko do użytkowników uwierzytelnionych przez Google, ustaw **akcję do podjęcia, gdy żądanie nie zostanie uwierzytelnione w** **Google**. Po ustawieniu tej funkcji aplikacja wymaga uwierzytelnionych wszystkich żądań. Przekierowuje również wszystkie nieuwierzyte żądania do Google w celu uwierzytelnienia.

    > [!CAUTION]
    > Ograniczenie dostępu w ten sposób ma zastosowanie do wszystkich wywołań aplikacji, co może nie być pożądane w przypadku aplikacji, które mają publicznie dostępną stronę główną, jak w wielu aplikacjach jednostronicowych. W przypadku takich aplikacji **można zezwolić na żądania anonimowe (brak akcji),** aby aplikacja ręcznie uruchamiała uwierzytelnianie. Aby uzyskać więcej informacji, zobacz [Przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).

1. Wybierz **pozycję Zapisz**.

Teraz możesz używać Google do uwierzytelniania w swojej aplikacji.

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal Azure]: https://portal.azure.com/

