---
title: Dodawanie uwierzytelniania w systemie Android
description: Dowiedz się, jak korzystać z usługi Azure App Service do uwierzytelniania użytkowników aplikacji na Androida za pomocą dostawców tożsamości, takich jak Google, Facebook, Twitter i Microsoft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 705ebb5809840155e6bbf3f8eef091eb95f63e63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461644"
---
# <a name="add-authentication-to-your-android-app"></a>Dodawanie uwierzytelniania do aplikacji na Androida
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Podsumowanie
W tym samouczku można dodać uwierzytelnianie do projektu szybkiego startu listy todolist w systemie Android przy użyciu obsługiwanego dostawcy tożsamości. Ten samouczek jest oparty na samouczku [Wprowadzenie do aplikacji mobilnych,] który należy wykonać najpierw.

## <a name="register-your-app-for-authentication-and-configure-azure-app-service"></a><a name="register"></a>Rejestrowanie aplikacji w celu uwierzytelniania i konfigurowanie usługi Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Dodawanie aplikacji do adresów URL dozwolonego przekierowania zewnętrznego

Bezpieczne uwierzytelnianie wymaga zdefiniowania nowego schematu adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy _appname_ schematu adresu URL w całym. Możesz jednak użyć dowolnego schematu adresu URL, który wybierzesz. Powinien być unikalny dla twojej aplikacji mobilnej. Aby włączyć przekierowanie po stronie serwera:

1. W [witrynie Azure portal]wybierz usługę app service.

2. Kliknij opcję menu **Uwierzytelnianie / Autoryzacja.**

3. W **adresach URL dozwolonego przekierowania zewnętrznego**wprowadź . `appname://easyauth.callback`  _Nazwa aplikacji_ w tym ciągu jest schemat url dla aplikacji mobilnej.  Powinien być zgodny ze specyfikacją normalnego adresu URL dla protokołu (użyj tylko liter i cyfr i zacznij od litery).  Należy zanotować ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnej za pomocą schematu adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij przycisk **Zapisz**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* W Android Studio otwórz projekt ukończony za pomocą samouczka [Wprowadzenie do aplikacji mobilnych]. W menu **Uruchom** kliknij polecenie **Uruchom aplikację**i sprawdź, czy po uruchomieniu aplikacji jest zgłaszany nieobsługiowany wyjątek o kodzie stanu 401 (Nieautoryzowane).

     Ten wyjątek występuje, ponieważ aplikacja próbuje uzyskać dostęp do zaplecza jako nieuwierzyfowany użytkownik, ale tabela *TodoItem* wymaga teraz uwierzytelniania.

Następnie zaktualizuj aplikację, aby uwierzytelnić użytkowników przed żądaniem zasobów z zaplecza aplikacji mobilnych.

## <a name="add-authentication-to-the-app"></a>Dodawanie uwierzytelniania do aplikacji
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-authentication-tokens-on-the-client"></a><a name="cache-tokens"></a>Tokeny uwierzytelniania pamięci podręcznej na kliencie
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Następne kroki
Teraz, po wykonaniu tego samouczka uwierzytelniania podstawowego, należy rozważyć kontynuowanie jednego z następujących samouczków:

* [Dodaj powiadomienia wypychane do aplikacji na Androida](app-service-mobile-android-get-started-push.md).
  Dowiedz się, jak skonfigurować zaplecze aplikacji mobilnych do wysyłania powiadomień wypychanych za pomocą centrów powiadomień platformy Azure.
* [Włącz synchronizację offline dla aplikacji na Androida](app-service-mobile-android-get-started-offline-data.md).
  Dowiedz się, jak dodać obsługę offline do aplikacji za pomocą zaplecza aplikacji mobilnych. Dzięki synchronizacji w trybie offline&mdash;użytkownicy mogą wchodzić&mdash;w interakcje z przeglądaniem, dodawaniem lub modyfikowaniem danych aplikacji mobilnej, nawet jeśli nie ma połączenia sieciowego.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Wprowadzenie do aplikacji mobilnych]: app-service-mobile-android-get-started.md
[Portal Azure]: https://portal.azure.com/
