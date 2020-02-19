---
title: Dodawanie uwierzytelniania na Apache Cordova
description: Dowiedz się, w jaki sposób używać Mobile Apps w Azure App Service do uwierzytelniania użytkowników aplikacji Apache Cordova z dostawcami tożsamości, takimi jak Google, Facebook, Twitter i Microsoft.
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3714ce2a8098608851991115aa82afdc00d08a47
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459392"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Dodawanie uwierzytelniania do aplikacji Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Podsumowanie
W tym samouczku dodasz uwierzytelnianie do projektu szybkiego startu todolist na Apache Cordova przy użyciu obsługiwanego dostawcy tożsamości. Ten samouczek jest oparty na samouczku [wprowadzenie do Mobile Apps] , który należy wykonać w pierwszej kolejności.

## <a name="register"></a>Zarejestruj aplikację pod kątem uwierzytelniania i skonfiguruj App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Obejrzyj wideo przedstawiające podobne kroki](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Ograniczanie uprawnień do uwierzytelnionych użytkowników
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Teraz możesz sprawdzić, czy dostęp anonimowy do zaplecza został wyłączony. W programie Visual Studio:

* Otwórz projekt, który został utworzony po ukończeniu samouczka [wprowadzenie do Mobile Apps].
* Uruchom aplikację w **usłudze Google emulator systemu Android**.
* Upewnij się, że po uruchomieniu aplikacji jest wyświetlany nieoczekiwany błąd połączenia.

Następnie zaktualizuj aplikację, aby uwierzytelnić użytkowników przed zażądaniem zasobów z zaplecza aplikacji mobilnej.

## <a name="add-authentication"></a>Dodawanie uwierzytelniania do aplikacji
1. Otwórz projekt w programie **Visual Studio**, a następnie otwórz plik `www/index.html` do edycji.
2. Zlokalizuj tag meta `Content-Security-Policy` w sekcji nagłówkowej.  Dodaj hosta OAuth do listy dozwolonych źródeł.

   | Dostawca | Nazwa dostawcy zestawu SDK | Host OAuth |
   |:--- |:--- |:--- |
   | Azure Active Directory | AAD | https://login.microsoftonline.com |
   | Facebook | facebook | https://www.facebook.com |
   | Google | usłudze | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | ekran | https://api.twitter.com |

    Przykładowa zawartość — zasady zabezpieczeń (zaimplementowane dla Azure Active Directory) są następujące:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Zastąp `https://login.microsoftonline.com` z hostem OAuth z powyższej tabeli.  Aby uzyskać więcej informacji na temat meta Content-Security-Policy tag, zobacz [Zawartość — dokumentacja dotycząca zasad].

    Niektórzy dostawcy uwierzytelniania nie wymagają zmian zawartości Content-Security-Policy, gdy są używane na odpowiednich urządzeniach przenośnych.  Na przykład w przypadku korzystania z uwierzytelniania Google na urządzeniu z systemem Android nie są wymagane żadne zmiany w zakresie zabezpieczeń zawartości.

3. Otwórz plik `www/js/index.js` do edycji, Znajdź metodę `onDeviceReady()` i w obszarze kod tworzenia klienta Dodaj następujący kod:

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

    Metoda Login () uruchamia uwierzytelnianie u dostawcy. Metoda Login () jest funkcją asynchroniczną zwracającą obietnicę języka JavaScript.  Reszta inicjalizacji jest umieszczana wewnątrz odpowiedzi Promise, aby nie była wykonywana do momentu ukończenia metody Login ().

4. W właśnie dodany kod Zastąp `SDK_Provider_Name` nazwą dostawcy logowania. Na przykład dla Azure Active Directory użyj `client.login('aad')`.
5. Uruchom projekt.  Po zainicjowaniu projektu w aplikacji zostanie wyświetlona strona logowania OAuth dla wybranego dostawcy uwierzytelniania.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej [Informacje o uwierzytelnianiu] za pomocą Azure App Service.
* Kontynuuj pracę z samouczkiem, dodając [powiadomienia wypychane] do aplikacji Apache Cordova.

Dowiedz się, jak korzystać z zestawów SDK.

* [Zestaw Apache Cordova SDK]
* [Zestaw ASP.NET Server SDK]
* [Zestaw Node.js Server SDK]

<!-- URLs. -->
[Wprowadzenie do Mobile Apps]: app-service-mobile-cordova-get-started.md
[Zawartość — dokumentacja dotycząca zasad]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Powiadomienia wypychane]: app-service-mobile-cordova-get-started-push.md
[Informacje o uwierzytelnianiu]: app-service-mobile-auth.md
[Zestaw Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[Zestaw ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Zestaw Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
