---
title: Jak skonfigurować uwierzytelnianie usługi Twitter dla aplikacji usługi aplikacji
description: Dowiedz się, jak skonfigurować uwierzytelnianie usługi Twitter dla aplikacji usługi aplikacji.
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
ms.openlocfilehash: f6449f99fda9c1a612ed9f9134751ff76b25904c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149956"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Konfigurowanie aplikacji usługi aplikacji umożliwiająca użycie logowania usługi Twitter
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie przedstawiono sposób konfigurowania usługi Azure App Service, aby użyć usługi Twitter jako dostawcy uwierzytelniania.

Aby ukończyć tę procedurę w tym temacie, musi mieć konto usługi Twitter, które ma zweryfikowano e-mail adres i numer telefonu. Aby utworzyć nowe konto usługi Twitter, przejdź do <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Zarejestrować aplikację w usłudze Twitter
1. Zaloguj się do [portalu Azure], a następnie przejdź do aplikacji. Kopiowanie z **adres URL**. Umożliwia to skonfigurować aplikację usługi Twitter.
2. Przejdź do [Twitter deweloperzy] witryny sieci Web, zaloguj się przy użyciu poświadczeń konta usługi Twitter, a następnie kliknij przycisk **Utwórz nową aplikację**.
3. Wpisz w **nazwa** i **opis** dla nowej aplikacji. Wklej do aplikacji **adres URL** dla **witryny sieci Web** wartość. Następnie dla **wywołania zwrotnego adresu URL**, Wklej **wywołania zwrotnego adresu URL** wcześniej zostały skopiowane. To jest dołączany wraz ze ścieżką Centrum aplikacji mobilnej */.auth/login/twitter/callback*. Na przykład `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Upewnij się, że używasz schematu HTTPS.
4. Na dole strony przeczytaj i zaakceptuj postanowienia. Następnie kliknij przycisk **tworzenie aplikacji Twitter**. Rejestruje to aplikacja jest wyświetlana szczegółów aplikacji.
5. Kliknij przycisk **ustawienia** karcie wyboru **zezwolić tej aplikacji należy się zalogować za pomocą usługi Twitter**, następnie kliknij przycisk **ustawienia aktualizacji**.
6. Wybierz **kluczy i tokenów dostępu** kartę. Zanotuj wartości **konsumenta (klucz interfejsu API)** i **klucz tajny klienta (klucz tajny interfejsu API)**.
   
   > [!NOTE]
   > Klucz tajny klienta jest ważne poświadczenie zabezpieczeń. Nie udostępniaj nikomu ten klucz tajny i rozpowszechnienie go z aplikacją.
   > 
   > 

## <a name="secrets"> </a>Dodawanie do aplikacji informacji usługi Twitter
1. W [portalu Azure], przejdź do aplikacji. Kliknij przycisk **ustawienia**, a następnie **uwierzytelniania / autoryzacji**.
2. Jeśli uwierzytelnianie / autoryzacja funkcja nie jest włączona, włącz przełącznik do **na**.
3. Kliknij przycisk **Twitter**. Wklej wartości, które uzyskany wcześniej identyfikator aplikacji i klucz tajny aplikacji. Następnie kliknij przycisk **OK**.
   
   ![][1]
   
   Domyślnie usługi App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do interfejsów API i zawartości witryny. Musisz zezwolić użytkownikom w kodzie aplikacji.
4. (Opcjonalnie) Aby ograniczyć dostęp do witryny użytkownikom tylko uwierzytelniony przez usługi Twitter, ustaw **działania należy podjąć w przypadku nieuwierzytelnionego żądania** do **Twitter**. Wymaga to, że wszystkie żądania uwierzytelnienia, a wszystkie nieuwierzytelnione żądania są przekierowywane do usługi Twitter dla uwierzytelniania.
5. Kliknij pozycję **Zapisz**.

Teraz można przystąpić do uwierzytelniania w aplikacji Twitter.

## <a name="related-content"> </a>Zawartość pokrewna
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter deweloperzy]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[portalu Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
