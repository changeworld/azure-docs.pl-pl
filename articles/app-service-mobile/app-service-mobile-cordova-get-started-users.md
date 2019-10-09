---
title: Dodawanie uwierzytelniania na Apache Cordova z Mobile Apps | Microsoft Docs
description: Dowiedz się, jak używać Mobile Apps w Azure App Service do uwierzytelniania użytkowników aplikacji Apache Cordova za pomocą różnych dostawców tożsamości, w tym Google, Facebook, Twitter i Microsoft.
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: d5e124c6f73285efdaef515deba5816093a27e98
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027552"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Dodawanie uwierzytelniania do aplikacji Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi centralne do tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług **kompilowania**, **testowania** i **dystrybucji** , aby skonfigurować ciągłą integrację i potok dostarczania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie swojej aplikacji przy użyciu usług **analizy** i **diagnostyki** oraz angażować się z użytkownikami za pomocą usługi **wypychania** . Deweloperzy mogą również korzystać z **uwierzytelniania** w celu uwierzytelniania użytkowników i usługi **danych** w celu utrwalania i synchronizowania danych aplikacji w chmurze.
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

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
2. Znajdź tag meta `Content-Security-Policy` w sekcji nagłówkowej.  Dodaj hosta OAuth do listy dozwolonych źródeł.

   | Dostawca | Nazwa dostawcy zestawu SDK | Host OAuth |
   |:--- |:--- |:--- |
   | Usługa Active Directory systemu Azure | AAD | https://login.microsoftonline.com |
   | Serwis Facebook | Serwis | https://www.facebook.com |
   | Google | usłudze | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Serwis Twitter | Ekran | https://api.twitter.com |

    Przykładowa zawartość — zasady zabezpieczeń (zaimplementowane dla Azure Active Directory) są następujące:

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Zastąp `https://login.microsoftonline.com` hostem OAuth z powyższej tabeli.  Aby uzyskać więcej informacji na temat meta Content-Security-Policy tag, zobacz [Zawartość — dokumentacja dotycząca zasad].

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
