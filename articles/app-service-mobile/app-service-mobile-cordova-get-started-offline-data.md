---
title: Włączanie synchronizacji w trybie offline dla aplikacji mobilnej platformy Azure (Cordova) | Microsoft Docs
description: Dowiedz się, jak używać aplikacji mobilnej App Service do buforowania i synchronizowania danych w trybie offline w aplikacji Cordova
documentationcenter: cordova
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: c5fcc98bf3da79d1978da6e67535a5a93a8fe321
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388850"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji mobilnej Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Usługa Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi mające kluczowe znaczenie podczas tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług do **tworzenia**, **testowania** i **dystrybuowania** w celu konfigurowania potoku ciągłej integracji i ciągłego wdrażania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie aplikacji za pomocą usług do **analizy** i **diagnostyki**, a także współpracować z użytkownikami za pomocą usługi do **wypychania**. Deweloperzy mogą również korzystać z usługi **uwierzytelniania** do uwierzytelniania użytkowników oraz usługi **danych** do utrwalania i synchronizowania danych aplikacji w chmurze.
>
> Jeśli chcesz zintegrować usługi w chmurze w aplikacji mobilnej, zarejestruj się w usłudze [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Omówienie
W tym samouczku przedstawiono funkcję synchronizacji w trybie offline w usłudze Azure Mobile Apps dla oprogramowania Cordova. Synchronizacja w trybie offline umożliwia użytkownikom końcowym współdziałanie z aplikacją mobilną&mdash;wyświetlanie, Dodawanie lub modyfikowanie danych&mdash;nawet w przypadku braku połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych.  Gdy urządzenie przewróci do trybu online, te zmiany są synchronizowane z usługą zdalną.

Ten samouczek jest oparty na rozwiązaniu Cordova szybkiego startu dla Mobile Apps tworzonego po zakończeniu samouczka [Apache Cordova Szybki Start]. W tym samouczku należy zaktualizować rozwiązanie szybkiego startu, aby dodać funkcje w trybie offline Mobile Apps platformy Azure.  W aplikacji zostanie również wyróżniony kod specyficzny dla trybu offline.

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]. Szczegóły dotyczące użycia interfejsu API znajdują się w [dokumentacji interfejsu API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Dodawanie synchronizacji offline do rozwiązania szybkiego startu
Kod synchronizacji w trybie offline należy dodać do aplikacji. Synchronizacja w trybie offline wymaga wtyczki Cordova-SQLite-Storage, która jest automatycznie dodawana do aplikacji po dołączeniu wtyczki Mobile Apps platformy Azure do projektu. Projekt szybkiego startu zawiera oba te wtyczki.

1. W Eksplorator rozwiązań programu Visual Studio Otwórz program index. js i Zastąp następujący kod

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    przy użyciu tego kodu:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Następnie Zastąp następujący kod:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    przy użyciu tego kodu:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    Powyższe Dodatki kodu inicjują magazyn lokalny i definiują tabelę lokalną zgodną z wartościami kolumn używanymi na zapleczu platformy Azure. (Nie trzeba uwzględniać wszystkich wartości kolumn w tym kodzie).  Pole `version` jest obsługiwane przez zaplecze mobilne i służy do rozwiązywania konfliktów.

    Odwołanie do kontekstu synchronizacji uzyskuje się przez wywołanie **getSyncContext**. Kontekst synchronizacji pomaga zachować relacje między tabelami przez śledzenie i wypychanie zmian we wszystkich tabelach, gdy aplikacja kliencka została zmodyfikowana po wywołaniu `.push()`.

3. Zaktualizuj adres URL aplikacji do adresu URL aplikacji mobilnej.

4. Następnie Zastąp ten kod:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    przy użyciu tego kodu:

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    Poprzedni kod inicjuje kontekst synchronizacji, a następnie wywołuje metodę getsync (zamiast GetTable), aby uzyskać odwołanie do tabeli lokalnej.

    Ten kod używa lokalnej bazy danych dla wszystkich operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD) tabeli.

    Ten przykład wykonuje prostą obsługę błędów podczas konfliktów synchronizacji. Rzeczywista aplikacja będzie obsługiwać różne błędy, takie jak warunki sieci, konflikty serwera i inne. Aby zapoznać się z przykładami kodu, zobacz [przykładzie synchronizacji w trybie offline].

5. Następnie Dodaj tę funkcję, aby przeprowadzić rzeczywistą synchronizację.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Należy zdecydować, kiedy należy wypchnąć zmiany zaplecza aplikacji mobilnej przez wywołanie **syncContext. push ()** . Na przykład można wywołać **syncBackend** w procedurze obsługi zdarzeń przycisku powiązanej z przyciskiem Synchronizuj.

## <a name="offline-sync-considerations"></a>Zagadnienia dotyczące synchronizacji w trybie offline

W przykładzie metoda **push** elementu **syncContext** jest wywoływana tylko w przypadku uruchamiania aplikacji w funkcji wywołania zwrotnego dla logowania.  W aplikacji rzeczywistej można również wykonać tę funkcję synchronizacji ręcznie lub w przypadku zmiany stanu sieci.

Gdy ściąganie jest wykonywane względem tabeli, która ma oczekujące aktualizacje lokalne śledzone przez kontekst, ta operacja ściągania automatycznie wyzwala wypychanie. Podczas odświeżania, dodawania i kończenia elementów w tym przykładzie można pominąć jawne wywołanie **wypychane** , ponieważ może być nadmiarowe.

W podanym kodzie wszystkie rekordy w zdalnej tabeli todoItem są badane, ale można również filtrować rekordy, przekazując identyfikator zapytania i kwerendę do **wypchnięcia**. Aby uzyskać więcej informacji, zobacz sekcję *Synchronizacja przyrostowa* w obszarze [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="optional-disable-authentication"></a>Obowiązkowe Wyłącz uwierzytelnianie

Jeśli nie chcesz konfigurować uwierzytelniania przed przetestowaniem synchronizacji w trybie offline, Skomentuj funkcję wywołania zwrotnego dla logowania, ale pozostaw kod wewnątrz funkcji wywołania zwrotnego bez komentarzy.  Po wprowadzeniu komentarza do wierszy logowania kod jest następujący:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Teraz aplikacja jest synchronizowana z zapleczem platformy Azure podczas uruchamiania aplikacji.

## <a name="run-the-client-app"></a>Uruchom aplikację kliencką
Po włączeniu synchronizacji w trybie offline można uruchomić aplikację kliencką co najmniej raz na każdej platformie, aby wypełnić lokalną bazę danych magazynu. Później można symulować scenariusz w trybie offline i modyfikować dane w magazynie lokalnym, gdy aplikacja jest w trybie offline.

## <a name="optional-test-the-sync-behavior"></a>Obowiązkowe Testowanie zachowania synchronizacji
W tej sekcji opisano modyfikowanie projektu klienta w celu symulowania scenariusza w trybie offline przy użyciu nieprawidłowego adresu URL aplikacji dla zaplecza. W przypadku dodawania lub zmieniania elementów danych te zmiany są przechowywane w magazynie lokalnym, ale nie są synchronizowane z magazynem danych zaplecza, dopóki połączenie nie zostanie ponownie nawiązane.

1. W Eksplorator rozwiązań otwórz plik projektu index. js i Zmień adres URL aplikacji tak, aby wskazywał nieprawidłowy adres URL, podobny do następującego:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. W pliku index. html zaktualizuj `<meta>` elementu CSP o takim samym nieprawidłowym adresie URL.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Skompiluj i uruchom aplikację kliencką i zwróć uwagę, że wyjątek jest rejestrowany w konsoli programu, gdy aplikacja próbuje przeprowadzić synchronizację z zapleczem po zalogowaniu się. Wszelkie nowe dodane elementy istnieją tylko w magazynie lokalnym, dopóki nie zostaną wypchnięte do zaplecza mobilnego. Aplikacja kliencka działa tak, jakby była podłączona do zaplecza.

4. Zamknij aplikację i uruchom ją ponownie, aby sprawdzić, czy nowe utworzone elementy są utrwalane w magazynie lokalnym.

5. Obowiązkowe Użyj programu Visual Studio, aby wyświetlić tabelę Azure SQL Database, aby zobaczyć, że dane w bazie danych zaplecza nie uległy zmianie.

    W programie Visual Studio Otwórz **Eksplorator serwera**. Przejdź do bazy danych w **usłudze Azure**->**bazy danych SQL**. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Otwórz w Eksplorator obiektów SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jej zawartości.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>Obowiązkowe Testowanie ponownego połączenia z zapleczem mobilnym

W tej sekcji należy ponownie połączyć aplikację z zapleczem mobilnym, co symuluje aplikację z powrotem do stanu online. Po zalogowaniu dane są synchronizowane z zapleczem mobilnym.

1. Otwórz ponownie index. js i Przywróć adres URL aplikacji.
2. Ponownie otwórz plik index. html i popraw adres URL aplikacji w elemencie `<meta>` dostawcy usług kryptograficznych.
3. Skompiluj ponownie i uruchom aplikację kliencką. Aplikacja próbuje przeprowadzić synchronizację z zapleczem aplikacji mobilnej po zalogowaniu. Sprawdź, czy w konsoli debugowania nie zarejestrowano żadnych wyjątków.
4. Obowiązkowe Wyświetl zaktualizowane dane przy użyciu Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak programu Fiddler. Zauważ, że dane zostały zsynchronizowane między bazą danych zaplecza a magazynem lokalnym.

    Zwróć uwagę, że dane zostały zsynchronizowane między bazą danych programu i magazynem lokalnym i zawierają elementy dodane w czasie, gdy aplikacja została odłączona.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]
* [Visual Studio Tools for Apache Cordova]

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z bardziej zaawansowanymi funkcjami synchronizacji w trybie offline, takimi jak rozwiązywanie konfliktów w [przykładzie synchronizacji w trybie offline]
* Przejrzyj informacje o interfejsie API synchronizacji w trybie offline w [dokumentacji interfejsu API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova Szybki Start]: app-service-mobile-cordova-get-started.md
[przykładzie synchronizacji w trybie offline]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
