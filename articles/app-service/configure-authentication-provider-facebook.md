---
title: Konfigurowanie uwierzytelniania serwisu Facebook — usłudze Azure App Service
description: Dowiedz się, jak skonfigurować uwierzytelnianie serwisu Facebook dla aplikacji usługi App Services.
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
ms.openlocfilehash: e91d55c29d325301b8ac70ddc63fb408961fbb2c
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742982"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Jak skonfigurować aplikację App Service, aby używała logowania do usługi Facebook
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie przedstawiono sposób konfigurowania usługi Azure App Service, aby użyć usługi Facebook jako dostawcy uwierzytelniania.

Aby ukończyć tę procedurę w tym temacie, muszą mieć konta w serwisie Facebook, która ma ze zweryfikowanym adresem e-mail i numer telefonu komórkowego. Aby utworzyć nowe konto usługi Facebook, przejdź do [facebook.com].

## <a name="register"> </a>Zarejestruj swoją aplikację za pomocą usługi Facebook
1. Przejdź do [deweloperzy w serwisie Facebook] poświadczeń konta w witrynie sieci Web i zaloguj się przy użyciu usługi Facebook.
3. (Opcjonalnie) Jeśli nie masz Facebook dla deweloperów konta, kliknij przycisk **wprowadzenie** i postępuj zgodnie z instrukcjami rejestracji.
4. Kliknij przycisk **Moje aplikacje** > **Dodaj nową aplikację**.
5. W **nazwę wyświetlaną**, wpisz unikatową nazwę aplikacji. Zapewniają również swoje **E-mail kontaktowy**, a następnie kliknij przycisk **utworzyć identyfikator aplikacji** i ukończyć sprawdzanie zabezpieczeń. Spowoduje to przejście do pulpitu nawigacyjnego dla deweloperów dla nowej aplikacji usługi Facebook.
6. Kliknij przycisk **pulpit nawigacyjny** > **logowania do usługi Facebook** > **Konfigurowanie** > **Web**.
1. W obszarze nawigacji po lewej stronie w obszarze **logowania do usługi Facebook**, kliknij przycisk **ustawienia**.
1. W **identyfikatory URI przekierowania OAuth prawidłowe**, typ `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` i Zastąp  *\<Nazwa aplikacji >* nazwą aplikacji usługi Azure App Service. Kliknij przycisk **Zapisz zmiany**.
8. W obszarze nawigacji po lewej stronie, kliknij przycisk **ustawienia** > **podstawowe**. Na **klucz tajny aplikacji** kliknij **Pokaż**. Skopiuj wartości z **Identyfikatora aplikacji** i **klucz tajny aplikacji**. Używasz tych później skonfigurować aplikację App Service na platformie Azure.
   
   > [!IMPORTANT]
   > Klucz tajny aplikacji jest ważnym poświadczeniem zabezpieczeń. Udostępnij ten wpis tajny osobom lub nie rozpowszechnić ją w aplikacji klienckiej.
   > 
   > 
9. Konta w serwisie Facebook, który został użyty do zarejestrowania aplikacji jest administratorem aplikacji. Tylko administratorzy mogą na tym etapie Zaloguj się do tej aplikacji. Na potrzeby uwierzytelniania innych kont usługi Facebook, kliknij przycisk **przeglądu aplikacji** i włączyć **upewnij \<your-app-name > publicznych** umożliwiające ogólnego dostępu publicznego przy użyciu uwierzytelniania serwisu Facebook.

## <a name="secrets"> </a>Dodaj informacje o usłudze Facebook do aplikacji
1. Zaloguj się do [Azure Portal] i przejdź do aplikacji usługi app Service. Kliknij przycisk **ustawienia** > **uwierzytelniania / autoryzacji**i upewnij się, że **uwierzytelnianie usługi App Service** jest **na**.
2. Kliknij przycisk **Facebook**, Wklej wartości Identyfikatora aplikacji i klucz tajny aplikacji, które wcześniej uzyskanych, opcjonalnie włączyć wszystkie zakresy, wymagane przez aplikację, a następnie kliknij przycisk **OK**.
   
    ![][0]
   
    Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do zawartości witryny i interfejsów API. W kodzie aplikacji musi autoryzować użytkowników.
3. (Opcjonalnie) Aby ograniczyć dostęp do witryny tylko użytkownikom uwierzytelniony przez usługi Facebook, należy ustawić **akcji do wykonania w przypadku nieuwierzytelnionego żądania** do **Facebook**. Wymaga to, że wszystkie żądania uwierzytelnienia, a wszystkie nieuwierzytelnione żądania są przekierowywane do serwisu Facebook do uwierzytelniania.
4. Po zakończeniu konfigurowania uwierzytelniania kliknij **Zapisz**.

Teraz można przystąpić do użycia usługi Facebook na potrzeby uwierzytelniania w aplikacji.

## <a name="related-content"> </a>Powiązana zawartość
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Deweloperzy w serwisie Facebook]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
