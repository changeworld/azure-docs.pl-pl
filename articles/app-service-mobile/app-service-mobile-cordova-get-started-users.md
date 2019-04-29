---
title: Dodawanie uwierzytelniania na Apache Cordova z usługą Mobile Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Mobile Apps w usłudze Azure App Service do uwierzytelniania użytkowników w swojej aplikacji Apache Cordova za pomocą różnych dostawców tożsamości, m.in. Google, Facebook, Twitter i Microsoft.
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
ms.openlocfilehash: 23b5967782cf237ed5af2b802aabbbf9c2f781e7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114214"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Dodawanie uwierzytelniania do aplikacji Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Podsumowanie
W tym samouczku można dodać do listy zadań projektu quickstart na Apache Cordova przy użyciu dostawcy tożsamości obsługiwanych uwierzytelniania. Ten samouczek opiera się na [Rozpoczynanie pracy z usługą Mobile Apps] — samouczek, należy najpierw wykonać.

## <a name="register"></a>Rejestrowanie aplikacji do uwierzytelniania i konfigurowanie usługi App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Obejrzyj wideo przedstawiające podobne kroki](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Ogranicz uprawnienia do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Można teraz, upewnij się, że anonimowy dostęp do swojej wewnętrznej bazy danych zostały wyłączone. W programie Visual Studio:

* Otwórz projekt, który został utworzony po ukończeniu tego samouczka [Rozpoczynanie pracy z usługą Mobile Apps].
* Uruchom aplikację w **Emulator systemu Google Android**.
* Sprawdź, czy wystąpił nieoczekiwany błąd połączenia jest wyświetlany po uruchomieniu aplikacji.

Następnie zaktualizuj aplikację do uwierzytelniania kont użytkowników przed zażądaniem zasoby z zaplecza aplikacji mobilnej.

## <a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
1. Otwórz projekt w programie **programu Visual Studio**, a następnie otwórz `www/index.html` plik do edycji.
2. Znajdź `Content-Security-Policy` metatag elementu head.  Dodaj hosta protokołu OAuth do listy dozwolonych źródeł.

   | Dostawca | Nazwa dostawcy zestawu SDK | Host protokołu OAuth |
   |:--- |:--- |:--- |
   | Usługa Azure Active Directory | usługi AAD | https://login.microsoftonline.com |
   | Facebook | facebook | https://www.facebook.com |
   | Google | google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | W usłudze Twitter | https://api.twitter.com |

    Przykładem zawartości-Security-Policy (zaimplementowano usługi Azure Active Directory), jest następujący:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Zastąp `https://login.microsoftonline.com` z hostem OAuth z powyższej tabeli.  Aby uzyskać więcej informacji na temat zawartości security-policy metatag zobacz [Dokumentacja zawartości-Security-Policy].

    Niektórzy dostawcy uwierzytelniania nie wymagają zmian zawartości-Security-Policy, gdy jest używana na urządzeniach przenośnych odpowiednie.  Na przykład bez zmian zawartości, zabezpieczeń, zasady są wymagane w przypadku urządzenia z systemem Android przy użyciu uwierzytelniania serwisu Google.

3. Otwórz `www/js/index.js` plik do edycji, odszukaj `onDeviceReady()` metody i w ramach tworzenia klienta kodu Dodaj następujący kod:

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

    Metoda: login() uruchamia uwierzytelniania za pomocą dostawcy. Metoda: login() jest zwracające JavaScript Promise funkcji asynchronicznej.  Pozostałe inicjowanie jest umieszczony wewnątrz odpowiedzi promise, tak aby nie jest wykonywany do momentu ukończenia metoda: login().

4. W kodzie, który właśnie został dodany, Zastąp `SDK_Provider_Name` o nazwie dostawcy logowania. Na przykład usługi Azure Active Directory, należy użyć `client.login('aad')`.
5. Uruchom projekt.  Po zakończeniu projektu Inicjowanie aplikacji pokazuje strony logowania OAuth dla dostawcy uwierzytelniania wybrany.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej [Uwierzytelnianie — informacje] za pomocą usługi Azure App Service.
* Kontynuuj samouczka, dodając [Powiadomienia wypychane] do swojej aplikacji Apache Cordova.

Dowiedz się, jak korzystać z zestawów SDK.

* [Zestaw Apache Cordova SDK]
* [Zestaw ASP.NET Server SDK]
* [Zestaw Node.js Server SDK]

<!-- URLs. -->
[Rozpoczynanie pracy z usługą Mobile Apps]: app-service-mobile-cordova-get-started.md
[Dokumentacja zawartości-Security-Policy]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Powiadomienia wypychane]: app-service-mobile-cordova-get-started-push.md
[Uwierzytelnianie — informacje]: app-service-mobile-auth.md
[Zestaw Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[Zestaw ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Zestaw Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
