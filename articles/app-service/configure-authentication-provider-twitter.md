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
ms.openlocfilehash: d49b8bf9c62813023c1a1e06e0f8fc0d7809f48d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232058"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Jak skonfigurować aplikację App Service do korzystania z logowania do usługi Twitter
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie opisano sposób konfigurowania Azure App Service do korzystania z usługi Twitter jako dostawcy uwierzytelniania.

Aby wykonać procedurę opisaną w tym temacie, musisz mieć konto w usłudze Twitter z zweryfikowanym adresem e-mail i numerem telefonu. Aby utworzyć nowe konto w usłudze Twitter, przejdź do <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">Twitter.com</a>.

## <a name="register"> </a>Rejestrowanie aplikacji w usłudze Twitter
1. Zaloguj się do [Azure Portal]i przejdź do swojej aplikacji. Skopiuj **adres URL**. Zostanie ona użyta do skonfigurowania aplikacji usługi Twitter.
2. Przejdź do witryny sieci Web [Deweloperzy usługi Twitter] , zaloguj się przy użyciu poświadczeń konta w usłudze Twitter, a następnie kliknij pozycję **Utwórz nową aplikację**.
3. Wpisz **nazwę** i **Opis** nowej aplikacji. Wklej **adres URL** aplikacji do wartości **witryny sieci Web** . Następnie w polu **adres URL wywołania zwrotnego**wpisz adres url aplikacji App Service i dołącz ścieżkę `/.auth/login/aad/callback`. Na przykład `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Upewnij się, że używasz schematu HTTPS.
4. W dolnej części strony Przeczytaj i zaakceptuj warunki. Następnie kliknij pozycję **Utwórz aplikację w usłudze Twitter**. Zostaną wyświetlone szczegóły aplikacji.
5. Kliknij kartę **Ustawienia** , zaznacz pole wyboru **Zezwalaj tej aplikacji na używanie do logowania się przy użyciu usługi Twitter**, a następnie kliknij przycisk **Aktualizuj ustawienia**.
6. Wybierz kartę **klucze i tokeny dostępu** . Zanotuj wartości **klucza klienta (klucz interfejsu API)** i klucz tajny **klienta (wpis tajny interfejsu API)** .
   
   > [!NOTE]
   > Wpis tajny klienta jest ważnym poświadczeniem zabezpieczeń. Nie należy udostępniać tego klucza tajnego nikomu ani rozpowszechniać go wraz z Twoją aplikacją.
   > 
   > 

## <a name="secrets"> </a>Dodawanie informacji usługi Twitter do aplikacji
1. W [Azure Portal]przejdź do swojej aplikacji. Kliknij przycisk **Ustawienia**, a następnie pozycję **uwierzytelnianie/autoryzacja**.
2. Jeśli funkcja uwierzytelniania/autoryzacji nie jest włączona, Przełącz przełącznik na wartość **włączone**.
3. Kliknij pozycję **Twitter**. Wklej wartości identyfikatora aplikacji i klucza tajnego aplikacji, które zostały uzyskane wcześniej. Następnie kliknij przycisk **OK**.
   
   ![][1]
   
   Domyślnie usługa App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji.
4. Obowiązkowe Aby ograniczyć dostęp do witryny tylko do użytkowników uwierzytelnionych przez serwis Twitter, ustaw **akcję do wykonania, gdy żądanie nie zostanie uwierzytelnione** do usługi **Twitter**. Wymaga to uwierzytelnienia wszystkich żądań, a wszystkie nieuwierzytelnione żądania są przekierowywane do usługi Twitter w celu uwierzytelnienia.

> [!NOTE]
> Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być odpowiednie dla aplikacji, które chcą korzystać z publicznie dostępnej strony głównej, tak jak w przypadku aplikacji jednostronicowych. W przypadku takich aplikacji **Zezwalanie na żądania anonimowe (nie akcja)** może być preferowane, z aplikacją ręcznie rozpoczynającą logowanie, zgodnie z opisem w [tym miejscu](overview-authentication-authorization.md#authentication-flow).

5. Kliknij polecenie **Zapisz**.

Możesz teraz przystąpić do uwierzytelniania w aplikacji za pomocą usługi Twitter.

## <a name="related-content"> </a>Powiązana zawartość
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Deweloperzy usługi Twitter]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
