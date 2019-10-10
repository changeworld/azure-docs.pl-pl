---
title: Konfigurowanie usługi uwierzytelnianie Google — Azure App Service
description: Dowiedz się, jak skonfigurować uwierzytelnianie Google dla aplikacji App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 917fa87a0cd0f7b0615a5139a7c15311f866739a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176969"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>Skonfiguruj aplikację App Service, aby używała logowania Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie opisano sposób konfigurowania Azure App Service do korzystania z usługi Google jako dostawcy uwierzytelniania.

Aby wykonać procedurę opisaną w tym temacie, musisz mieć konto Google, które ma zweryfikowany adres e-mail. Aby utworzyć nowe konto Google, przejdź do strony [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Zarejestruj swoją aplikację w usłudze Google

1. Aby utworzyć identyfikator klienta i klucz tajny klienta, postępuj zgodnie z dokumentacją Google w temacie [Logowanie do aplikacji po stronie serwera](https://developers.google.com/identity/sign-in/web/server-side-flow) . Nie ma potrzeby wprowadzania żadnych zmian w kodzie. Po prostu Użyj następujących informacji:
    - W przypadku **autoryzowanych źródeł języka JavaScript**Użyj `https://<app-name>.azurewebsites.net` z nazwą aplikacji w *\<app-Name >* .
    - Aby uzyskać **Autoryzowany identyfikator URI przekierowania**, użyj `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Skopiuj identyfikator aplikacji i wartości klucza tajnego aplikacji.

    > [!IMPORTANT]
    > Wpis tajny aplikacji jest ważnym poświadczeniem zabezpieczeń. Nie należy udostępniać tego klucza tajnego nikomu ani rozpowszechniać go w aplikacji klienckiej.

## <a name="secrets"> </a>Dodawanie informacji Google do aplikacji

1. W [Azure Portal]przejdź do aplikacji App Service.
1. Wybierz pozycję **ustawienia** > **uwierzytelnianie/autoryzacja**i upewnij się, że **App Service uwierzytelnianie** jest **włączone**.
1. Wybierz pozycję **Google**, a następnie wklej wartości w polu Identyfikator aplikacji i klucz tajny aplikacji, które zostały uzyskane wcześniej. Włącz wszystkie zakresy wymagane przez aplikację.
1. Kliknij przycisk **OK**.

   App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Aby uzyskać więcej informacji, zobacz [Autoryzuj lub Odmów użytkownikom](app-service-authentication-how-to.md#authorize-or-deny-users).

1. Obowiązkowe Aby ograniczyć dostęp do witryny tylko do użytkowników uwierzytelnionych przez firmę Google, należy ustawić **akcję podejmowaną, gdy żądanie nie zostanie uwierzytelnione** do firmy **Google**. Po ustawieniu tej funkcji aplikacja wymaga uwierzytelnienia wszystkich żądań. Przekierowuje także wszystkie nieuwierzytelnione żądania do usługi Google w celu uwierzytelnienia.

    > [!CAUTION]
    > Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być pożądane dla aplikacji, które mają publicznie dostępną stronę główną, tak jak w przypadku aplikacji jednostronicowych. W przypadku takich aplikacji **Zezwalanie na żądania anonimowe (żadna akcja)** może być preferowana, aby aplikacja ręcznie uruchamiała sam uwierzytelnienie. Aby uzyskać więcej informacji, zobacz temat [przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).

1. Wybierz pozycję **Zapisz**.

Teraz można przystąpić do uwierzytelniania w aplikacji za pomocą usługi Google.

## <a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

