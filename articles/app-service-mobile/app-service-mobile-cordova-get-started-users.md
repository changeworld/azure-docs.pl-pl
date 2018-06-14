---
title: Dodawanie uwierzytelniania na Apache Cordova w usłudze Mobile Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z aplikacji mobilnych w usłudze Azure App Service uwierzytelniać użytkowników aplikacji oprogramowania Apache Cordova za pomocą różnych dostawców tożsamości, obejmującej Google, Facebook, Twitter i Microsoft.
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: b5cce832ae7ae83552c2a5ded2f5f5bda0ac76bf
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27591950"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Dodawanie uwierzytelniania do aplikacji platformy Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Podsumowanie
W tym samouczku należy dodać do listy zadań projektu szybkiego startu w programie Apache Cordova przy użyciu dostawcy tożsamości obsługiwanych uwierzytelniania. W tym samouczku jest oparta na [Rozpoczynanie pracy z usługą Mobile Apps] — samouczek, należy najpierw wykonać.

## <a name="register"></a>Zarejestrować aplikację do uwierzytelniania i konfigurowanie usługi aplikacji
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Obejrzyj wideo przedstawiające podobne kroki](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Ogranicz uprawnienia dla uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Teraz można sprawdzić dostępu anonimowego z wewnętrzną bazą danych zostało wyłączone. W programie Visual Studio:

* Otwórz projekt, który został utworzony po ukończeniu samouczka [Rozpoczynanie pracy z usługą Mobile Apps].
* Uruchom aplikację w **Emulator systemu Google Android**.
* Sprawdź, czy wystąpił nieoczekiwany błąd połączenia jest wyświetlany po uruchomieniu aplikacji.

Następnie zaktualizuj aplikację do uwierzytelniania użytkowników przed wysłaniem żądania zasobów z zaplecza aplikacji mobilnej.

## <a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
1. Otwórz projekt w **programu Visual Studio**, następnie otwórz `www/index.html` plik do edycji.
2. Zlokalizuj `Content-Security-Policy` metatag elementu head.  Dodaj hosta OAuth do listy dozwolonych źródeł.

   | Dostawca | Nazwa dostawcy zestawu SDK | OAuth Host |
   |:--- |:--- |:--- |
   | Usługa Azure Active Directory | usługi AAD | https://login.microsoftonline.com |
   | Facebook | Usługi Facebook | https://www.Facebook.com |
   | Google | Google | https://accounts.Google.com |
   | Microsoft | MicrosoftAccount | https://login.Live.com |
   | Twitter | W usłudze Twitter | https://API.twitter.com |

    Przykład zawartość--zasady zabezpieczeń (zaimplementowany dla usługi Azure Active Directory) jest następujący:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Zastąp `https://login.microsoftonline.com` z hostem OAuth z powyższej tabeli.  Aby uzyskać więcej informacji o metatag zawartości zabezpieczeń zasad, zobacz [zasadę zawartość dokumentacji].

    Niektórzy dostawcy uwierzytelniania nie wymaga się, że zawartość zabezpieczeń zasady zostanie zmieniona, gdy na urządzeniach przenośnych odpowiednie.  Na przykład żadnych zmian zawartości zabezpieczeń zasady są wymagane w przypadku przy użyciu uwierzytelniania serwisu Google na urządzeniu z systemem Android.

3. Otwórz `www/js/index.js` pliku do edycji, Znajdź `onDeviceReady()` metody i w obszarze tworzenia klienta kodzie Dodaj następujący kod:

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

    Ten kod zastępuje istniejący kod, który tworzy odwołania do tabeli i odświeża interfejsu użytkownika.

    Metoda: login() uruchamia uwierzytelniania z dostawcą. Metoda: login() jest z funkcji asynchronicznych, która zwraca JavaScript Promise.  Pozostałe inicjowanie jest umieszczony wewnątrz odpowiedzi promise, tak aby nie jest wykonywany przed zakończeniem metody: login().

4. W kodzie, który właśnie został dodany, Zastąp `SDK_Provider_Name` o nazwie dostawcą logowania. Na przykład dla usługi Azure Active Directory, należy użyć `client.login('aad')`.
5. Uruchom projekt.  Po zakończeniu projektu podczas inicjowania aplikacji zawiera strony logowania OAuth dla dostawcy uwierzytelniania wybrany.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej [o uwierzytelniania] z usługi Azure App Service.
* Kontynuuj samouczek, dodając [powiadomień wypychanych] do swojej aplikacji Apache Cordova.

Dowiedz się, jak korzystać z zestawów SDK.

* [Zestaw Apache Cordova SDK]
* [Zestaw ASP.NET Server SDK]
* [Zestaw Node.js Server SDK]

<!-- URLs. -->
[Rozpoczynanie pracy z usługą Mobile Apps]: app-service-mobile-cordova-get-started.md
[zasadę zawartość dokumentacji]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[powiadomień wypychanych]: app-service-mobile-cordova-get-started-push.md
[o uwierzytelniania]: app-service-mobile-auth.md
[Zestaw Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[Zestaw ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Zestaw Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
