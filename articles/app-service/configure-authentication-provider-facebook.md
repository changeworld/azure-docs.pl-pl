---
title: Konfigurowanie uwierzytelniania w usłudze Facebook — Azure App Service
description: Dowiedz się, jak skonfigurować uwierzytelnianie w serwisie Facebook dla aplikacji App Services.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: c9767ff1e6f0b31270f37842cf99d71cab561505
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033846"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Jak skonfigurować aplikację App Service, aby używała logowania do usługi Facebook
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie opisano sposób konfigurowania Azure App Service do korzystania z serwisu Facebook jako dostawcy uwierzytelniania.

Aby wykonać procedurę opisaną w tym temacie, musisz mieć konto w serwisie Facebook z zweryfikowanym adresem e-mail i numerem telefonu komórkowego. Aby utworzyć nowe konto w usłudze Facebook, przejdź do [Facebook.com].

## <a name="register"> </a>Zarejestruj swoją aplikację w usłudze Facebook
1. Przejdź do witryny internetowej [Deweloperzy serwisu Facebook] i zaloguj się przy użyciu poświadczeń konta w serwisie Facebook.
3. Obowiązkowe Jeśli nie masz konta w serwisie Facebook for Developers, kliknij pozycję **Rozpocznij pracę** i postępuj zgodnie z instrukcjami rejestracji.
4. Kliknij kolejno pozycje **Moje aplikacje** > **Dodaj nową aplikację**.
5. W polu **Nazwa wyświetlana**wpisz unikatową nazwę aplikacji. Podaj również **kontaktowy adres e-mail**, a następnie kliknij pozycję **Utwórz identyfikator aplikacji** i Ukończ sprawdzanie zabezpieczeń. Spowoduje to przejście do pulpitu nawigacyjnego dewelopera dla nowej aplikacji w serwisie Facebook.
6. Kliknij **pozycję pulpit nawigacyjny** > w usłudze**Facebook** > **Konfiguracja** > **sieci Web**.
1. W okienku nawigacji po lewej stronie w obszarze Logowanie do usługi **Facebook**kliknij pozycję **Ustawienia**.
1. W prawidłowych identyfikatorach **URI przekierowania OAuth**wpisz `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` i Zastąp  *\<ciąg App-Name >* nazwą aplikacji Azure App Service. Kliknij przycisk **Zapisz zmiany**.
8. W okienku nawigacji po lewej stronie kliknij pozycję **Ustawienia** > **podstawowe**. W polu **klucz tajny aplikacji** kliknij przycisk **Pokaż**. Skopiuj wartości **identyfikatora aplikacji** i **klucza tajnego aplikacji**. Te aplikacje są używane później do konfigurowania aplikacji App Service na platformie Azure.
   
   > [!IMPORTANT]
   > Wpis tajny aplikacji jest ważnym poświadczeniem zabezpieczeń. Nie należy udostępniać tego klucza tajnego nikomu ani rozpowszechniać go w aplikacji klienckiej.
   > 
   > 
9. Konto w serwisie Facebook, które zostało użyte do zarejestrowania aplikacji, jest administratorem aplikacji. W tym momencie tylko Administratorzy mogą zalogować się do tej aplikacji. Aby uwierzytelnić inne konta w usłudze Facebook, kliknij pozycję **Przegląd aplikacji** i Włącz opcję **uczyń \<swoją nazwę aplikacji > publiczną** , aby umożliwić ogólny dostęp publiczny przy użyciu uwierzytelniania w serwisie Facebook.

## <a name="secrets"> </a>Dodawanie informacji w serwisie Facebook do aplikacji
1. Zaloguj się do [Azure Portal] i przejdź do aplikacji App Service. Kliknij pozycję **Ustawienia** > **uwierzytelnianie/autoryzacja**i upewnij się, że **App Service uwierzytelnianie** jest **włączone**.
2. Kliknij pozycję **Facebook**, Wklej wartości w polu Identyfikator aplikacji i klucz tajny aplikacji, które zostały uzyskane wcześniej, opcjonalnie Włącz wszystkie zakresy, które są potrzebne w aplikacji, a następnie kliknij przycisk **OK**.
   
    ![][0]
   
    Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji.
3. Obowiązkowe Aby ograniczyć dostęp do witryny tylko do użytkowników uwierzytelnionych przez serwis Facebook, ustaw **akcję do wykonania, gdy żądanie nie zostanie uwierzytelnione** do **serwisu Facebook**. Wymaga to uwierzytelnienia wszystkich żądań, a wszystkie nieuwierzytelnione żądania są przekierowywane do serwisu Facebook w celu uwierzytelnienia.
 
> [!CAUTION]
> Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być odpowiednie dla aplikacji, które chcą korzystać z publicznie dostępnej strony głównej, tak jak w przypadku aplikacji jednostronicowych. W przypadku takich aplikacji **Zezwalanie na żądania anonimowe (nie akcja)** może być preferowane, z aplikacją ręcznie rozpoczynającą logowanie, zgodnie z opisem w [tym miejscu](overview-authentication-authorization.md#authentication-flow).

4. Po zakończeniu konfigurowania uwierzytelniania kliknij przycisk **Zapisz**.

Teraz możesz przystąpić do uwierzytelniania w aplikacji za pomocą usługi Facebook.

## <a name="related-content"> </a>Powiązana zawartość
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Deweloperzy serwisu Facebook]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
