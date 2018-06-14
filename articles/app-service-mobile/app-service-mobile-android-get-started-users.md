---
title: Dodawanie uwierzytelniania w systemie Android w usłudze Mobile Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać funkcji Mobile Apps w usłudze Azure App Service uwierzytelniać użytkowników aplikacji systemu Android za pomocą różnych dostawców tożsamości, obejmującej Google, Facebook, Twitter i firmy Microsoft.
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
ms.openlocfilehash: 4ee71e00807fcfe698a7e965979434f338f5b870
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27595231"
---
# <a name="add-authentication-to-your-android-app"></a>Dodawanie uwierzytelniania do aplikacji systemu Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Podsumowanie
W tym samouczku można dodać uwierzytelniania do listy zadań projektu Szybki Start w systemie Android przy użyciu dostawcy tożsamości obsługiwane. W tym samouczku jest oparta na [Rozpoczynanie pracy z usługą Mobile Apps] — samouczek, należy najpierw wykonać.

## <a name="register"></a>Zarejestrować aplikację do uwierzytelniania i konfigurowanie usługi Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Dodaj aplikację do dozwolonych przekierowania zewnętrzne adresy URL

Bezpieczne uwierzytelnianie wymaga Zdefiniuj nowy schemat adresu URL dla aplikacji. Dzięki temu system uwierzytelniania przekierować z powrotem do aplikacji po zakończeniu procesu uwierzytelniania. W tym samouczku używamy schemat adresu URL _appname_ w ciągu. Można jednak użyć dowolnego wybranego schematu URL. Powinien być unikatowy w aplikacji mobilnej. Aby włączyć przekierowywanie po stronie serwera:

1. W [portalu Azure], wybierz usługę aplikacji.

2. Kliknij przycisk **uwierzytelniania / autoryzacji** opcji menu.

3. W **dozwolone zewnętrznych adresów URL przekierowań**, wprowadź `appname://easyauth.callback`.  _Appname_ ten ciąg jest schemat adresu URL dla aplikacji mobilnej.  Musi on być zgodny normalne Specyfikacja adresu URL dla protokołu (Użyj litery i tylko cyfry i rozpoczyna się od litery).  Należy zanotuj ciąg, który wybrany jako trzeba będzie dostosować kodu aplikacji mobilnej przy użyciu schemat adresu URL w kilku miejscach.

4. Kliknij przycisk **OK**.

5. Kliknij pozycję **Zapisz**.

## <a name="permissions"></a>Ogranicz uprawnienia dla uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* W programie Android Studio Otwórz projekt, zostały ukończone z samouczka [Rozpoczynanie pracy z usługą Mobile Apps]. Z **Uruchom** menu, kliknij przycisk **uruchamianie aplikacji**i sprawdź, czy wystąpił nieobsługiwany wyjątek przy użyciu kodu stanu 401 (bez autoryzacji) jest wywoływane po uruchomieniu aplikacji.

     Ten wyjątek spowodowany aplikacja próbuje uzyskać dostęp zaplecza jako nieuwierzytelniony użytkownik, ale *TodoItem* tabeli teraz wymaga uwierzytelniania.

Następnie należy zaktualizować aplikację do uwierzytelniania użytkowników przed wysłaniem żądania zasobów z zaplecza aplikacji mobilnej.

## <a name="add-authentication-to-the-app"></a>Dodawanie uwierzytelniania do aplikacji
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Tokeny uwierzytelniania pamięci podręcznej na kliencie
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Kolejne kroki
Teraz, aby ukończyć w tym samouczku uwierzytelnianie podstawowe, należy wziąć pod uwagę kontynuowanie na jedno z następujących samouczków:

* [Dodawanie powiadomień wypychanych do aplikacji systemu Android](app-service-mobile-android-get-started-push.md).
  Dowiedz się, jak skonfigurować Twoje aplikacje mobilne zaplecza na potrzeby usługi Azure notification hubs wysyłać powiadomienia wypychane.
* [Włączanie synchronizacji w trybie offline dla aplikacji systemu Android](app-service-mobile-android-get-started-offline-data.md).
  Dowiedz się, jak dodać obsługę w trybie offline do aplikacji przy użyciu zaplecza aplikacji mobilnej. Z synchronizacji w trybie offline, użytkownicy mogą korzystać z aplikacji mobilnej&mdash;przeglądanie, dodawanie lub modyfikowanie danych&mdash;nawet wtedy, gdy istnieje połączenie sieciowe.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Rozpoczynanie pracy z usługą Mobile Apps]: app-service-mobile-android-get-started.md
[portalu Azure]: https://portal.azure.com/
