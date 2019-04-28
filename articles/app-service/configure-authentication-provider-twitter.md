---
title: Konfigurowanie uwierzytelniania usługi Twitter — usłudze Azure App Service
description: Dowiedz się, jak skonfigurować uwierzytelnianie serwisu Twitter do aplikacji usługi App Services.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 51a2ac93fd2d863855c820ba147418c5397c2a89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60851557"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Jak skonfigurować aplikację App Service, aby używała logowania do usługi Twitter
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie przedstawiono sposób konfigurowania usługi Azure App Service, aby użyć usługi Twitter jako dostawcy uwierzytelniania.

Aby ukończyć tę procedurę w tym temacie, musi mieć konto w usłudze Twitter, zawierającej e-mail zweryfikowanej adres i numer telefonu. Aby utworzyć nowe konto w usłudze Twitter, przejdź do <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Zarejestrować aplikację w usłudze Twitter
1. Zaloguj się do [Azure Portal], a następnie przejdź do aplikacji. Kopiuj swoje **adresu URL**. Użytkownik zostanie ona użyta do konfigurowania aplikacji usługi Twitter.
2. Przejdź do [Deweloperzy w serwisie Twitter] witryny sieci Web, zaloguj się przy użyciu poświadczeń konta usługi Twitter, a następnie kliknij przycisk **Utwórz nową aplikację**.
3. Wpisz **nazwa** i **opis** dla nowej aplikacji. Wklej do aplikacji **adresu URL** dla **witryny sieci Web** wartość. Następnie dla **adresów URL wywołania zwrotnego**, Wklej **adresów URL wywołania zwrotnego** wcześniej zostały skopiowane. To jest dołączany przy użyciu ścieżki, Centrum aplikacji mobilnej */.auth/login/twitter/callback*. Na przykład `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Upewnij się, że będą używać schematu HTTPS.
4. W dolnej części strony należy przeczytać i zaakceptować warunki. Następnie kliknij przycisk **tworzenie aplikacji usługi Twitter**. Rejestruje to aplikacja wyświetla szczegóły aplikacji.
5. Kliknij przycisk **ustawienia** karcie wyboru **zezwalasz tej aplikacji, które ma być używany do logowania się przy użyciu usługi Twitter**, następnie kliknij przycisk **ustawienia aktualizacji**.
6. Wybierz **klucze i tokeny dostępu** kartę. Zanotuj wartości **konsumenta (klucz interfejsu API)** i **klucz tajny klienta (klucz tajny interfejsu API)**.
   
   > [!NOTE]
   > Klucz tajny klienta jest ważnym poświadczeniem zabezpieczeń. Udostępnij ten wpis tajny z dowolnymi osobami lub nie rozpowszechnienie go z aplikacją.
   > 
   > 

## <a name="secrets"> </a>Dodaj informacje o usłudze Twitter do aplikacji
1. Ponownie [Azure Portal], przejdź do aplikacji. Kliknij przycisk **ustawienia**, a następnie **uwierzytelniania / autoryzacji**.
2. Jeśli uwierzytelnianie / Autoryzacja nie jest aktywna, ustaw przełącznik na **na**.
3. Kliknij przycisk **Twitter**. Wklej wartości, które wcześniej uzyskanych Identyfikatora aplikacji i klucz tajny aplikacji. Następnie kliknij przycisk **OK**.
   
   ![][1]
   
   Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do zawartości witryny i interfejsów API. W kodzie aplikacji musi autoryzować użytkowników.
4. (Opcjonalnie) Aby ograniczyć dostęp do witryny tylko użytkownicy uwierzytelnieni Twitter, należy ustawić **akcji do wykonania w przypadku nieuwierzytelnionego żądania** do **Twitter**. Wymaga to, że wszystkie żądania uwierzytelnienia, a wszystkie nieuwierzytelnione żądania są przekierowywane do usługi Twitter do uwierzytelniania.
5. Kliknij pozycję **Zapisz**.

Teraz można przystąpić do korzystania z usługi Twitter w ramach uwierzytelniania w aplikacji.

## <a name="related-content"> </a>Powiązana zawartość
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Deweloperzy w serwisie Twitter]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
