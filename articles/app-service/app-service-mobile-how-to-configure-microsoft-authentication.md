---
title: Jak skonfigurować uwierzytelnianie Microsoft Account aplikacji usługi aplikacji
description: Dowiedz się, jak skonfigurować uwierzytelnianie Microsoft Account aplikacji usługi aplikacji.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 4fb5bdf30502dbca3eba961165a1ab643427abd6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Konfigurowanie aplikacji usługi aplikacji umożliwiająca użycie Account Microsoft logowania
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie przedstawiono sposób konfigurowania usługi Azure App Service służące Account Microsoft jako dostawcy uwierzytelniania. 

## <a name="register-microsoft-account"> </a>Zarejestrować aplikację z kontem Microsoft
1. Zaloguj się do [portalu Azure], a następnie przejdź do aplikacji. Kopia programu **adres URL**, umożliwiający później skonfigurować aplikację z Account Microsoft.
2. Przejdź do [Moje aplikacje] strony w programie Microsoft Account Developer Center i zaloguj się przy użyciu konta Microsoft, jeśli jest to wymagane.
3. Kliknij przycisk **Dodaj aplikację**, następnie wpisz nazwę aplikacji i kliknij przycisk **Utwórz**.
4. Zanotuj **identyfikator aplikacji**, ponieważ będzie potrzebny później. 
5. W obszarze "Platform," kliknij **dodać platformy** i wybierz pozycję "Web".
6. W obszarze "Identyfikator URI przekierowania" Podaj punktu końcowego dla aplikacji, a następnie kliknij przycisk **zapisać**. 
   
   > [!NOTE]
   > Twoje przekierowania URI jest adres URL aplikacji dołączany wraz ze ścieżką */.auth/login/microsoftaccount/callback*. Na przykład `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Upewnij się, że używasz schematu HTTPS.
   
7. W obszarze "Klucze tajne aplikacji," kliknij **wygenerować nowe hasło**. Zanotuj wartość wyświetlana. Po wyjściu strony go nie pojawi się ponownie.

    > [!IMPORTANT]
    > Hasło jest ważne poświadczenie zabezpieczeń. Nie udostępniaj nikomu hasła lub opublikować go w aplikacji klienta.

## <a name="secrets"> </a>Dodaj informacje o Microsoft Account do aplikacji usługi aplikacji
1. W [portalu Azure], przejdź do aplikacji, kliknij przycisk **ustawienia** > **uwierzytelniania / autoryzacji**.
2. Jeśli uwierzytelniania / autoryzacji funkcja nie jest włączona, przełącz go **na**.
3. Kliknij przycisk **konta Microsoft**. Wklej w wartości Identyfikatora aplikacji i hasło, które uzyskany wcześniej i opcjonalnie włączyć wszystkie zakresy wymaganych przez aplikację. Następnie kliknij przycisk **OK**.
   
    ![][1]
   
    Domyślnie usługi App Service zapewnia uwierzytelnianie, ale nie ogranicza autoryzowanego dostępu do interfejsów API i zawartości witryny. Musisz zezwolić użytkownikom w kodzie aplikacji.
4. (Opcjonalnie) Aby ograniczyć dostęp do witryny użytkownikom tylko uwierzytelnione przez konto Microsoft, należy ustawić **działania należy podjąć w przypadku nieuwierzytelnionego żądania** do **Account Microsoft**. Wymaga to, że wszystkie żądania uwierzytelnienia, a wszystkie nieuwierzytelnione żądania są przekierowywane do konta Microsoft do uwierzytelniania.
5. Kliknij pozycję **Zapisz**.

Teraz można przystąpić do Account Microsoft uwierzytelniania w aplikacji.

## <a name="related-content"> </a>Zawartość pokrewna
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Moje aplikacje]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[portalu Azure]: https://portal.azure.com/
