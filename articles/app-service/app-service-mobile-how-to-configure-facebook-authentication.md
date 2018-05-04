---
title: Konfigurowanie uwierzytelniania serwisu Facebook dla aplikacji usługi aplikacji
description: Informacje o sposobie konfigurowania uwierzytelniania serwisu Facebook dla aplikacji usługi aplikacji.
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
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 1d2b294fc0663770f9a699e300672695225dfdfd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Jak skonfigurować aplikację App Service, aby używała logowania do usługi Facebook
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie przedstawiono sposób konfigurowania usługi aplikacji Azure do użycia jako dostawcę uwierzytelniania serwisu Facebook.

Aby ukończyć tę procedurę w tym temacie, musi mieć konto usługi Facebook, które ma ze zweryfikowanym adresem e-mail i numer telefonu komórkowego. Aby utworzyć nowe konto usługi Facebook, przejdź do [facebook.com].

## <a name="register"> </a>Zarejestrować aplikację w usłudze Facebook
1. Zaloguj się do [portalu Azure], a następnie przejdź do aplikacji. Kopiowanie z **adres URL**. Umożliwia to konfigurowanie aplikacji usługi Facebook.
2. W innym oknie przeglądarki, przejdź do [deweloperzy Facebook] poświadczenia konta witryny sieci Web i logowania z użytkownika usługi Facebook.
3. (Opcjonalnie) Jeśli nie została jeszcze zarejestrowana, kliknij przycisk **aplikacje** > **Zarejestruj się jako deweloper**, Zaakceptuj zasady i wykonaj kroki rejestracji.
4. Kliknij przycisk **Moje aplikacje** > **Dodaj nową aplikację**.
5. W **Nazwa wyświetlana**, wpisz unikatową nazwę dla aplikacji. Udostępniają Twojej **E-mail kontaktu**, a następnie kliknij przycisk **utworzyć identyfikator aplikacji** i ukończyć sprawdzania zabezpieczeń. Powoduje to przejście do projektanta pulpitu nawigacyjnego dla nowej aplikacji usługi Facebook.
7. W obszarze **logowania usługi Facebook**, kliknij przycisk **Konfigurowanie**, a następnie wybierz pozycję **ustawienia** w obszarze nawigacji po lewej stronie w obszarze **logowania usługi Facebook**.
8. Dodawanie aplikacji **identyfikator URI przekierowania** do **prawidłowy OAuth przekierowania URI**, następnie kliknij przycisk **Zapisz zmiany**.
   
   > [!NOTE]
   > Twoje przekierowania URI jest adres URL aplikacji dołączany wraz ze ścieżką */.auth/login/facebook/callback*. Na przykład `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Upewnij się, że używasz schematu HTTPS.
   > 
   > 
7. W obszarze nawigacji po lewej stronie, kliknij przycisk **ustawienia** > **podstawowe**. Na **klucz tajny aplikacji** kliknij **Pokaż**, podaj hasło, jeśli żądanie, a następnie zanotuj wartości **identyfikator aplikacji** i **klucz tajny aplikacji**. Można użyć tych później do konfigurowania aplikacji na platformie Azure.
   
   > [!IMPORTANT]
   > Klucz tajny aplikacji jest ważne poświadczenie zabezpieczeń. Nie udostępniaj nikomu ten klucz tajny i rozpowszechnienie go w aplikacji klienta.
   > 
   > 
8. Konto usługi Facebook, która była używana do rejestrowania aplikacji jest administrator aplikacji. W tym momencie tylko administratorzy mogą Zaloguj się do tej aplikacji. Do uwierzytelniania innych kont usługi Facebook, kliknij przycisk **aplikacji przejrzyj** i Włącz **publicznego upewnij < your-app-name >** umożliwia ogólne dostęp publiczny przy użyciu uwierzytelniania serwisu Facebook.

## <a name="secrets"> </a>Dodawanie do aplikacji usługi Facebook informacji
1. W [portalu Azure], przejdź do aplikacji. Kliknij przycisk **ustawienia** > **uwierzytelniania / autoryzacji**i upewnij się, że **aplikacji uwierzytelniania usługi** jest **na**.
2. Kliknij przycisk **Facebook**, Wklej wartości Identyfikatora aplikacji i klucz tajny aplikacji, które uzyskany wcześniej, opcjonalnie włączyć wszystkie zakresy wymagane przez aplikację, a następnie kliknij **OK**.
   
    ![][0]
   
    Domyślnie usługi App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do interfejsów API i zawartości witryny. Musisz zezwolić użytkownikom w kodzie aplikacji.
3. (Opcjonalnie) Aby ograniczyć dostęp do witryny użytkownikom tylko uwierzytelniony przez usługi Facebook, ustaw **działania należy podjąć w przypadku nieuwierzytelnionego żądania** do **Facebook**. Wymaga to, że wszystkie żądania uwierzytelnienia, a wszystkie nieuwierzytelnione żądania są przekierowywane do serwisu Facebook dla uwierzytelniania.
4. Po zakończeniu konfigurowania uwierzytelniania, kliknij przycisk **zapisać**.

Teraz można przystąpić do uwierzytelniania w aplikacji usługi Facebook.

## <a name="related-content"> </a>Zawartość pokrewna
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[deweloperzy Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[portalu Azure]: https://portal.azure.com/
