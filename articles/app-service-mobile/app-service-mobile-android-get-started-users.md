---
title: Dodawanie uwierzytelniania w systemie Android w usłudze Mobile Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać funkcji Mobile Apps w usłudze Azure App Service do uwierzytelniania użytkowników aplikacji systemu Android za pomocą różnych dostawców tożsamości, m.in. Google, Facebook, Twitter i Microsoft.
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: crdun
ms.openlocfilehash: 7b80c1148cf2716e71308d953ac445c4bb50cbc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62119775"
---
# <a name="add-authentication-to-your-android-app"></a>Dodawanie uwierzytelniania do aplikacji systemu Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Podsumowanie
W tym samouczku dodasz uwierzytelnianie do projektu quickstart listy zadań w systemie Android przy użyciu dostawcy tożsamości obsługiwanych. Ten samouczek opiera się na [Rozpoczynanie pracy z usługą Mobile Apps] — samouczek, należy najpierw wykonać.

## <a name="register"></a>Rejestrowanie aplikacji do uwierzytelniania i konfigurowanie usługi Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodawanie aplikacji do adresów URL dozwolonych zewnętrznego przekierowania

Bezpieczne uwierzytelnianie wymaga, zdefiniuj nowy schemat adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy schemat adresu URL _appname_ w całym. Można jednak użyć dowolnego wybranego schematu URL. Powinien on być unikatowy dla twojej aplikacji mobilnej. Aby włączyć przekierowywanie po stronie serwera:

1. W [Azure Portal], wybierz swoją usługę aplikacji.

2. Kliknij przycisk **uwierzytelniania / autoryzacji** opcji menu.

3. W **dozwolone zewnętrzne adresy URL przekierowania**, wprowadź `appname://easyauth.callback`.  _Appname_ w tym ciągu jest schemat adresu URL aplikacji mobilnej.  Powinien on być zgodny z normalnej Specyfikacja adresu URL dla protokołu (litery użytkowania i tylko cyfry oraz rozpoczynać się literą).  Należy zapamiętać, ciąg, który wybierzesz, ponieważ trzeba będzie dostosować kod aplikacji mobilnych za pomocą schemat adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij pozycję **Zapisz**.

## <a name="permissions"></a>Ogranicz uprawnienia do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* W programie Android Studio, otwórz projekt, należy wykonać czynności opisane z samouczkiem dotyczącym [Rozpoczynanie pracy z usługą Mobile Apps]. Z **Uruchom** menu, kliknij przycisk **uruchamianie aplikacji**i sprawdź, czy wystąpił nieobsługiwany wyjątek, kod stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji.

     Ten wyjątek spowodowany aplikacja próbuje uzyskać dostęp zaplecze jako użytkownik nieuwierzytelniony do ale *TodoItem* tabeli teraz wymaga uwierzytelnienia.

Następnie należy zaktualizować aplikację do uwierzytelniania kont użytkowników przed zażądaniem zasoby z zaplecze funkcji Mobile Apps.

## <a name="add-authentication-to-the-app"></a>Dodawanie uwierzytelniania do aplikacji
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Tokeny uwierzytelniania na komputerze klienckim pamięci podręcznej
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy została zakończona w tym samouczku uwierzytelnianie podstawowe, należy wziąć pod uwagę przejściem do jednej z następujących samouczków:

* [Dodawanie powiadomień wypychanych do aplikacji systemu Android](app-service-mobile-android-get-started-push.md).
  Dowiedz się, jak skonfigurować swoje zaplecze funkcji Mobile Apps do wysyłania powiadomień wypychanych przy użyciu usługi Azure notification hubs.
* [Włączanie synchronizacji offline dla aplikacji systemu Android](app-service-mobile-android-get-started-offline-data.md).
  Dowiedz się, jak dodać obsługę trybu offline do aplikacji przy użyciu zaplecze funkcji Mobile Apps. Synchronizacja w trybie offline, użytkownicy mogą wchodzić w interakcje z aplikacją mobilną&mdash;wyświetlanie, dodawanie lub modyfikowanie danych&mdash;nawet w przypadku braku połączenia sieciowego.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Rozpoczynanie pracy z usługą Mobile Apps]: app-service-mobile-android-get-started.md
[Azure Portal]: https://portal.azure.com/
