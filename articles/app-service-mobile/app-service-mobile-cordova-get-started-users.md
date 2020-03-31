---
title: Dodawanie uwierzytelniania w Apache Cordova
description: Dowiedz się, jak używać aplikacji mobilnych w usłudze Azure App Service do uwierzytelniania użytkowników aplikacji Apache Cordova u dostawców tożsamości, takich jak Google, Facebook, Twitter i Microsoft.
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3714ce2a8098608851991115aa82afdc00d08a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459392"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Dodawanie uwierzytelniania do aplikacji Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Podsumowanie
W tym samouczku można dodać uwierzytelnianie do projektu szybkiego startu listy todolist na Apache Cordova przy użyciu obsługiwanego dostawcy tożsamości. Ten samouczek jest oparty na samouczku [Wprowadzenie do aplikacji mobilnych,] który należy wykonać najpierw.

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Zarejestruj aplikację do uwierzytelniania i skonfiguruj usługę App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Obejrzyj wideo przedstawiające podobne kroki](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Teraz możesz sprawdzić, czy anonimowy dostęp do wewnętrznej bazy danych został wyłączony. W programie Visual Studio:

* Otwórz projekt utworzony po zakończeniu samouczka [Wprowadzenie do aplikacji mobilnych].
* Uruchom aplikację w **emulatorze Google Android**.
* Sprawdź, czy po uruchomieniu aplikacji jest wyświetlany nieoczekiwany błąd połączenia.

Następnie zaktualizuj aplikację, aby uwierzytelnić użytkowników przed żądaniem zasobów z zaplecza aplikacji mobilnej.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
1. Otwórz projekt w programie Visual `www/index.html` **Studio,** a następnie otwórz plik do edycji.
2. Znajdź `Content-Security-Policy` metatag w sekcji head.  Dodaj hosta OAuth do listy dozwolonych źródeł.

   | Dostawca | Nazwa dostawcy SDK | OAuth Host |
   |:--- |:--- |:--- |
   | Usługa Azure Active Directory | Aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    Przykładowa zasada zabezpieczeń zawartości (zaimplementowana dla usługi Azure Active Directory) jest następująca:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Zamień `https://login.microsoftonline.com` na hosta OAuth z poprzedniej tabeli.  Aby uzyskać więcej informacji na temat metatagu zasad zabezpieczeń zawartości, zobacz [dokumentację zasad zabezpieczeń zawartości].

    Niektórzy dostawcy uwierzytelniania nie wymagają zmian zasad zabezpieczeń zawartości, gdy są używane na odpowiednich urządzeniach przenośnych.  Na przykład podczas korzystania z uwierzytelniania Google na urządzeniu z Androidem nie są wymagane żadne zmiany w zasadach bezpieczeństwa treści.

3. Otwórz `www/js/index.js` plik do edycji, `onDeviceReady()` znajdź metodę, a pod kodem tworzenia klienta dodaj następujący kod:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Ten kod zastępuje istniejący kod, który tworzy odwołanie do tabeli i odświeża interfejs użytkownika.

    Metoda login() rozpoczyna uwierzytelnianie u dostawcy. Metoda login() jest funkcją asynchronizową, która zwraca wartość JavaScript Promise.  Pozostała część inicjowania jest umieszczana wewnątrz odpowiedzi obietnicy, dzięki czemu nie jest wykonywana do czasu zakończenia metody login().

4. W kodzie, który właśnie `SDK_Provider_Name` dodano, zastąp nazwą dostawcy logowania. Na przykład w usłudze Azure `client.login('aad')`Active Directory należy użyć pliku .
5. Uruchom swój projekt.  Po zakończeniu inicjowania projektu aplikacja pokazuje stronę logowania OAuth dla wybranego dostawcy uwierzytelniania.

## <a name="next-steps"></a><a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej [o uwierzytelnianiu] za pomocą usługi Azure App Service.
* Kontynuuj samouczek, dodając [powiadomienia wypychane] do aplikacji Apache Cordova.

Dowiedz się, jak korzystać z zestawów SDK.

* [Zestaw Apache Cordova SDK]
* [Zestaw ASP.NET Server SDK]
* [Zestaw Node.js Server SDK]

<!-- URLs. -->
[Wprowadzenie do aplikacji mobilnych]: app-service-mobile-cordova-get-started.md
[Dokumentacja zasad zabezpieczeń zawartości]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Powiadomienia wypychane]: app-service-mobile-cordova-get-started-push.md
[Informacje o uwierzytelnianiu]: app-service-mobile-auth.md
[Zestaw Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[Zestaw ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Zestaw Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
