---
title: Konfigurowanie uwierzytelniania w usłudze Twitter — Azure App Service
description: Dowiedz się, jak skonfigurować uwierzytelnianie w usłudze Twitter dla aplikacji App Service.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 02008b7dc1609a5f28ac6ba2a582933a96428198
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176954"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>Skonfiguruj aplikację App Service, aby korzystała z logowania do usługi Twitter

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym artykule przedstawiono sposób konfigurowania Azure App Service do korzystania z usługi Twitter jako dostawcy uwierzytelniania.

Aby wykonać procedurę opisaną w tym artykule, musisz mieć konto w usłudze Twitter z zweryfikowanym adresem e-mail i numerem telefonu. Aby utworzyć nowe konto w usłudze Twitter, przejdź do [Twitter.com].

## <a name="register"> </a>Rejestrowanie aplikacji w usłudze Twitter

1. Zaloguj się do [Azure Portal] i przejdź do swojej aplikacji. Skopiuj **adres URL**. Zostanie ona użyta do skonfigurowania aplikacji usługi Twitter.
1. Przejdź do witryny sieci Web [Deweloperzy usługi Twitter] i zaloguj się przy użyciu poświadczeń konta w usłudze Twitter, a następnie wybierz pozycję **Utwórz nową aplikację**.
1. Wprowadź **nazwę** i **Opis** nowej aplikacji. Wklej **adres URL** aplikacji do pola **Witryna sieci Web** . W polu **adres URL wywołania zwrotnego** wprowadź adres URL aplikacji App Service i dołącz ścieżkę `/.auth/login/aad/callback`. Na przykład `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Upewnij się, że używasz schematu HTTPS.
1. W dolnej części strony Przeczytaj i zaakceptuj warunki. Wybierz pozycję **Utwórz aplikację w usłudze Twitter**. Zostaną wyświetlone szczegóły aplikacji.
1. Wybierz kartę **Ustawienia** , zaznacz opcję **Zezwól tej aplikacji na używanie do logowania się przy użyciu usługi Twitter**, a następnie wybierz pozycję **Ustawienia aktualizacji**.
1. Wybierz kartę **klucze i tokeny dostępu** .

   Zanotuj te wartości:
   - Klucz klienta (klucz interfejsu API)
   - Wpis tajny klienta (klucz tajny interfejsu API)

   > [!NOTE]
   > Wpis tajny klienta jest ważnym poświadczeniem zabezpieczeń. Nie należy udostępniać tego klucza tajnego nikomu ani rozpowszechniać go wraz z Twoją aplikacją.

## <a name="secrets"> </a>Dodawanie informacji usługi Twitter do aplikacji

1. Przejdź do aplikacji w [Azure Portal].
1. Wybierz pozycję **ustawienia** > **uwierzytelnianie/autoryzacja**i upewnij się, że **App Service uwierzytelnianie** jest **włączone**.
1. Wybierz pozycję **Twitter**.
1. Wklej wartości `API Key` i `API Secret`, które zostały uzyskane wcześniej.
1. Kliknij przycisk **OK**.

   ![Zrzut ekranu ustawień usługi Twitter aplikacji mobilnych][1]

   Domyślnie App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji.

1. Obowiązkowe Aby ograniczyć dostęp do witryny tylko do użytkowników uwierzytelnionych przez serwis Twitter, ustaw **akcję do wykonania, gdy żądanie nie zostanie uwierzytelnione** do usługi **Twitter**. Po ustawieniu tej funkcji aplikacja wymaga uwierzytelnienia wszystkich żądań. Przekierowuje także wszystkie nieuwierzytelnione żądania do usługi Twitter w celu uwierzytelnienia.

   > [!CAUTION]
   > Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być pożądane dla aplikacji, które mają publicznie dostępną stronę główną, tak jak w przypadku aplikacji jednostronicowych. W przypadku takich aplikacji **Zezwalanie na żądania anonimowe (żadna akcja)** może być preferowana, aby aplikacja ręcznie uruchamiała sam uwierzytelnienie. Aby uzyskać więcej informacji, zobacz temat [przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).

1. Wybierz pozycję **Zapisz**.

Możesz teraz przystąpić do uwierzytelniania w aplikacji za pomocą usługi Twitter.

## <a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Deweloperzy usługi Twitter]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
