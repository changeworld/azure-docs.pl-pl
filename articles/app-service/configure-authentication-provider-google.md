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
ms.openlocfilehash: fcbb284a0807ef88c5f40a7c8b65398d45bf73d7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232136"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Jak skonfigurować aplikację App Service do korzystania z logowania Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie opisano sposób konfigurowania Azure App Service do korzystania z usługi Google jako dostawcy uwierzytelniania.

Aby wykonać procedurę opisaną w tym temacie, musisz mieć konto Google, które ma zweryfikowany adres e-mail. Aby utworzyć nowe konto Google, przejdź do strony [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Zarejestruj swoją aplikację w usłudze Google
1. Aby utworzyć identyfikator klienta i klucz tajny klienta, należy zapoznać się z dokumentacją Google w temacie [Logowanie w usłudze Google dla aplikacji po stronie serwera](https://developers.google.com/identity/sign-in/web/server-side-flow) . w tym celu należy użyć następujących informacji (nie trzeba wprowadzać żadnych zmian w kodzie):
    - W przypadku **autoryzowanych źródeł języka JavaScript**Użyj `https://<app-name>.azurewebsites.net` nazwy aplikacji w polu  *\<nazwa aplikacji >* .
    - Aby uzyskać **Autoryzowany identyfikator URI przekierowania**, użyj `https://<app-name>.azurewebsites.net/.auth/login/google/callback`elementu.
1. Po utworzeniu identyfikatora klienta i wpisów tajnych klienta Skopiuj ich wartości.

    > [!IMPORTANT]
    > Klucz tajny klienta jest ważnym poświadczeniem zabezpieczeń. Nie należy udostępniać tego klucza tajnego nikomu ani rozpowszechniać go w aplikacji klienckiej.


## <a name="secrets"> </a>Dodawanie informacji Google do aplikacji
1. W [Azure Portal]przejdź do aplikacji App Service. Z menu po lewej stronie wybierz pozycję **uwierzytelnianie/autoryzacja**.
2. Jeśli funkcja uwierzytelniania/autoryzacji nie jest włączona, Przełącz przełącznik na wartość **włączone**.
3. Kliknij pozycję **Google**. Wklej wartości identyfikatora aplikacji i klucza tajnego aplikacji, które zostały uzyskane wcześniej, i opcjonalnie Włącz wszystkie zakresy wymagane przez aplikację. Następnie kliknij przycisk **OK**.

   App Service zapewnia uwierzytelnianie, ale nie ogranicza dostępu do zawartości witryny i interfejsów API. Aby uzyskać więcej informacji, zobacz [Autoryzuj lub Odmów użytkownikom](app-service-authentication-how-to.md#authorize-or-deny-users).
4. Obowiązkowe Aby ograniczyć dostęp do witryny tylko do użytkowników uwierzytelnionych przez firmę Google, ustaw **akcję do wykonania, gdy żądanie nie zostanie uwierzytelnione** do firmy **Google**. Wymaga to uwierzytelnienia wszystkich żądań, a wszystkie nieuwierzytelnione żądania są przekierowywane do usługi Google w celu uwierzytelnienia.

    > [!NOTE]
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

