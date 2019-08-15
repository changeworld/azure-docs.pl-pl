---
title: Konfigurowanie usługi uwierzytelnianie Google — Azure App Service
description: Dowiedz się, jak skonfigurować uwierzytelnianie Google dla aplikacji App Services.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 7b56d4e8d179e4ff073e74b4bc5242f936dc983e
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033730"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Jak skonfigurować aplikację App Service do korzystania z logowania Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie opisano sposób konfigurowania Azure App Service do korzystania z usługi Google jako dostawcy uwierzytelniania.

Aby wykonać procedurę opisaną w tym temacie, musisz mieć konto Google, które ma zweryfikowany adres e-mail. Aby utworzyć nowe konto Google, przejdź do strony [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Zarejestruj swoją aplikację w usłudze Google
1. Zaloguj się do [Azure Portal]i przejdź do swojej aplikacji. Skopiuj **adres URL**, którego używasz później do skonfigurowania aplikacji Google.
2. Przejdź do witryny sieci Web usługi [Google API](https://go.microsoft.com/fwlink/p/?LinkId=268303) , zaloguj się przy użyciu poświadczeń konta Google, kliknij pozycję **Utwórz projekt**, podaj **nazwę projektu**, a następnie kliknij pozycję **Utwórz**.
3. Po utworzeniu projektu zaznacz go. Na pulpicie nawigacyjnym projektu kliknij pozycję **Przejdź do interfejsów API przegląd**.
4. Wybierz pozycję **Włącz interfejsy API i usługi**. Wyszukaj pozycję **Google + interfejs API**i wybierz ją. Następnie kliknij pozycję **Włącz**.
5. Na ekranie nawigacji po lewej stronie, **poświadczenia** > **uwierzytelniania OAuth**, a następnie **Wybierz adres e-mail**, wprowadź **nazwę produktu**, a następnie kliknij przycisk **Zapisz**.
6. Na karcie **poświadczenia** kliknij pozycję **Utwórz poświadczenia** > **Identyfikator klienta OAuth**.
7. Na ekranie "Tworzenie identyfikatora klienta" Wybierz pozycję **aplikacja sieci Web**.
8. Wklej **adres URL** App Service skopiowany wcześniej do **autoryzowanych źródeł języka JavaScript**, a następnie wklej identyfikator URI przekierowania do **autoryzowanego identyfikatora URI przekierowania**. Identyfikator URI przekierowania to adres URL aplikacji dołączonej ze ścieżką */.auth/login/Google/callback*. Na przykład `https://contoso.azurewebsites.net/.auth/login/google/callback`. Upewnij się, że używasz schematu HTTPS. Następnie kliknij pozycję **Utwórz**.
9. Na następnym ekranie Zanotuj wartości identyfikator klienta i klucz tajny klienta.

    > [!IMPORTANT]
    > Klucz tajny klienta jest ważnym poświadczeniem zabezpieczeń. Nie należy udostępniać tego klucza tajnego nikomu ani rozpowszechniać go w aplikacji klienckiej.


## <a name="secrets"> </a>Dodawanie informacji Google do aplikacji
1. W [Azure Portal]przejdź do swojej aplikacji. Kliknij przycisk **Ustawienia**, a następnie pozycję **uwierzytelnianie/autoryzacja**.
2. Jeśli funkcja uwierzytelniania/autoryzacji nie jest włączona, Przełącz przełącznik na wartość **włączone**.
3. Kliknij pozycję **Google**. Wklej wartości identyfikatora aplikacji i klucza tajnego aplikacji, które zostały uzyskane wcześniej, i opcjonalnie Włącz wszystkie zakresy wymagane przez aplikację. Następnie kliknij przycisk **OK**.
   
   ![][1]
   
   Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji.
4. Obowiązkowe Aby ograniczyć dostęp do witryny tylko do użytkowników uwierzytelnionych przez firmę Google, ustaw **akcję do wykonania, gdy żądanie nie zostanie uwierzytelnione** do firmy **Google**. Wymaga to uwierzytelnienia wszystkich żądań, a wszystkie nieuwierzytelnione żądania są przekierowywane do usługi Google w celu uwierzytelnienia.

> [!CAUTION]
> Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być odpowiednie dla aplikacji, które chcą korzystać z publicznie dostępnej strony głównej, tak jak w przypadku aplikacji jednostronicowych. W przypadku takich aplikacji **Zezwalanie na żądania anonimowe (nie akcja)** może być preferowane, z aplikacją ręcznie rozpoczynającą logowanie, zgodnie z opisem w [tym miejscu](overview-authentication-authorization.md#authentication-flow).

5. Kliknij polecenie **Zapisz**.

Teraz można przystąpić do uwierzytelniania w aplikacji za pomocą usługi Google.

## <a name="related-content"> </a>Powiązana zawartość
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

