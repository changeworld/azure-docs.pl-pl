---
title: Włącz synchronizację offline (Cordova)
description: Dowiedz się, jak używać aplikacji mobilnej usługi App Service do buforowania i synchronizowania danych offline w aplikacji Cordova.
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5a2d5ec8da5c1a317039e656f6df884a10efe7c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459422"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Włącz synchronizację offline dla swojej aplikacji mobilnej Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Omówienie
W tym samouczku przedstawiono funkcję synchronizacji w trybie offline usługi Azure Mobile Apps for Cordova. Synchronizacja w trybie offline umożliwia&mdash;użytkownikom końcowym interakcję z&mdash;przeglądaniem, dodawaniem lub modyfikowaniem danych aplikacji mobilnej, nawet jeśli nie ma połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych.  Po powrocie urządzenia do trybu online zmiany te są synchronizowane z usługą zdalną.

Ten samouczek jest oparty na rozwiązaniu Szybki start Cordova dla aplikacji mobilnych, które można utworzyć po zakończeniu samouczka [Apache Cordova szybki start]. W tym samouczku zaktualizujesz rozwiązanie szybkiego startu, aby dodać funkcje usługi Azure Mobile Apps w trybie offline.  Wyróżnimy również kod specyficzny dla trybu offline w aplikacji.

Aby dowiedzieć się więcej o funkcji synchronizacji w trybie offline, zobacz temat [Synchronizacja danych w trybie offline w aplikacjach mobilnych platformy Azure]. Aby uzyskać szczegółowe informacje na temat użycia interfejsu API, zobacz [dokumentację interfejsu API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Dodawanie synchronizacji w trybie offline do rozwiązania szybki start
Kod synchronizacji w trybie offline musi zostać dodany do aplikacji. Synchronizacja w trybie offline wymaga wtyczki cordova-sqlite-storage, która automatycznie zostanie dodana do aplikacji, gdy wtyczka usługi Azure Mobile Apps jest uwzględniona w projekcie. Projekt Szybki start obejmuje obie te wtyczki.

1. W Eksploratorze rozwiązań programu Visual Studio otwórz plik index.js i zastąp następujący kod

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    z tym kodem:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Następnie zastąp następujący kod:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    z tym kodem:

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

    Poprzednie dodatki kodu inicjują magazyn lokalny i definiują tabelę lokalną, która odpowiada wartościom kolumn używanym w zapleczu platformy Azure. (Nie musisz dołączać wszystkich wartości kolumn do tego kodu).  Pole `version` jest obsługiwane przez mobilną wytchnienie i jest używane do rozwiązywania konfliktów.

    Otrzymasz odwołanie do kontekstu synchronizacji, wywołując **getSyncContext**. Kontekst synchronizacji pomaga zachować relacje tabeli, śledząc i wypychając `.push()` zmiany we wszystkich tabelach, które aplikacja kliencka zmodyfikowała, gdy jest wywoływana.

3. Zaktualizuj adres URL aplikacji do adresu URL aplikacji mobilnej.

4. Następnie zastąp ten kod:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    z tym kodem:

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

    Poprzedni kod inicjuje kontekst synchronizacji, a następnie wywołuje getSyncTable (zamiast getTable), aby uzyskać odwołanie do tabeli lokalnej.

    Ten kod używa lokalnej bazy danych dla wszystkich operacji tworzenia, odczytu, aktualizacji i usuwania (CRUD).

    Ten przykład wykonuje prostą obsługę błędów w konfliktach synchronizacji. Prawdziwa aplikacja będzie obsługiwać różne błędy, takie jak warunki sieciowe, konflikty serwera i inne. Przykłady kodu można znaleźć w [przykładzie synchronizacji offline].

5. Następnie dodaj tę funkcję, aby wykonać rzeczywistą synchronizację.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Użytkownik decyduje, kiedy wcisnąć zmiany do zaplecza aplikacji mobilnej, wywołując **syncContext.push()**. Na przykład można **wywołać syncBackend** w programie obsługi zdarzeń przycisku powiązany z przyciskiem synchronizacji.

## <a name="offline-sync-considerations"></a>Zagadnienia dotyczące synchronizacji w trybie offline

W przykładzie metoda **wypychania** **syncContext** jest wywoływana tylko podczas uruchamiania aplikacji w funkcji wywołania zwrotnego dla logowania.  W rzeczywistych aplikacjach można również uruchomić tę funkcję synchronizacji ręcznie lub po zmianie stanu sieci.

Gdy ściąganie jest wykonywane względem tabeli, która oczekuje na aktualizacje lokalne śledzone przez kontekst, ta operacja ściągania automatycznie wyzwala wypychanie. Podczas odświeżania, dodawania i uzupełniania elementów w tym przykładzie można pominąć jawne **wywołanie wypychane,** ponieważ może być zbędne.

W podanym kodzie wszystkie rekordy w zdalnej tabeli todoItem są wyszukiwane, ale możliwe jest również filtrowanie rekordów, przekazując identyfikator zapytania i kwerendę do **wypchnięcia**. Aby uzyskać więcej informacji, zobacz sekcję *Synchronizacja przyrostowa* w synchronizacji danych w [trybie offline w aplikacjach mobilnych platformy Azure].

## <a name="optional-disable-authentication"></a>(Opcjonalnie) Wyłączanie uwierzytelniania

Jeśli nie chcesz skonfigurować uwierzytelniania przed testowaniem synchronizacji w trybie offline, skomentuj funkcję wywołania zwrotnego dla logowania, ale pozostaw kod wewnątrz funkcji wywołania zwrotnego bez komentarza.  Po skomentowaniu linii logowania kod jest następujący:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Teraz aplikacja synchronizuje się z zapleczem platformy Azure po uruchomieniu aplikacji.

## <a name="run-the-client-app"></a>Uruchamianie aplikacji klienckiej
Po włączeniu synchronizacji w trybie offline można uruchomić aplikację kliencką co najmniej raz na każdej platformie, aby wypełnić lokalną bazę danych magazynu. Później symuluj scenariusz w trybie offline i zmodyfikuj dane w magazynie lokalnym, gdy aplikacja jest w trybie offline.

## <a name="optional-test-the-sync-behavior"></a>(Opcjonalnie) Testowanie zachowania synchronizacji
W tej sekcji zmodyfikujesz projekt klienta, aby symulować scenariusz w trybie offline przy użyciu nieprawidłowego adresu URL aplikacji dla wewnętrznej bazy danych. Po dodaniu lub zmianie elementów danych te zmiany są przechowywane w magazynie lokalnym, ale nie są synchronizowane z magazynem danych wewnętrznej bazy danych, dopóki połączenie nie zostanie ponownie nawiązane.

1. W Eksploratorze rozwiązań otwórz plik projektu index.js i zmień adres URL aplikacji, aby wskazywał nieprawidłowy adres URL, taki jak następujący kod:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. W pliku index.html zaktualizuj element CSP `<meta>` o tym samym nieprawidłowym adresie URL.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Skompiluj i uruchom aplikację kliencką i zwróć uwagę, że wyjątek jest rejestrowany w konsoli, gdy aplikacja próbuje zsynchronizować z wewnętrznej bazy danych po zalogowaniu. Wszystkie nowe elementy, które dodajesz, istnieją tylko w magazynie lokalnym, dopóki nie zostaną wypchnięte do zaplecza mobilnego. Aplikacja kliencka zachowuje się tak, jakby jest podłączona do wewnętrznej bazy danych.

4. Zamknij aplikację i uruchom ją ponownie, aby sprawdzić, czy nowe utworzone elementy są utrwalone w magazynie lokalnym.

5. (Opcjonalnie) Użyj programu Visual Studio, aby wyświetlić tabelę bazy danych SQL azure, aby zobaczyć, że dane w bazie danych wewnętrznej bazy danych nie uległy zmianie.

    W programie Visual Studio otwórz **Eksploratora serwera**. Przejdź do bazy danych w **usłudze Azure**->**SQL Databases**. Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie **Otwórz w Eksploratorze obiektów programu SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jej zawartości.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Opcjonalnie) Testowanie ponownego połączenia z zapleczem mobilnym

W tej sekcji ponownie połączyć aplikację z zaplecza mobilnego, który symuluje aplikacji wraca do stanu online. Po zalogowaniu dane są synchronizowane z zapleczem sieciowym telefonu komórkowego.

1. Otwórz ponownie plik index.js i przywróć adres URL aplikacji.
2. Otwórz ponownie index.html i popraw adres `<meta>` URL aplikacji w elemencie CSP.
3. Odbuduj i uruchom aplikację kliencką. Aplikacja próbuje zsynchronizować się z zapleczem aplikacji mobilnej po zalogowaniu. Sprawdź, czy żadne wyjątki nie są rejestrowane w konsoli debugowania.
4. (Opcjonalnie) Wyświetlanie zaktualizowanych danych przy użyciu Eksploratora obiektów programu SQL Server lub narzędzia REST, takiego jak Fiddler. Zwróć uwagę, że dane zostały zsynchronizowane między bazą danych wewnętrznej bazy danych a magazynem lokalnym.

    Zwróć uwagę, że dane zostały zsynchronizowane między bazą danych a magazynem lokalnym i zawiera elementy dodane podczas rozłączenia aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]
* [Narzędzia programu Visual Studio dla Apache Cordova]

## <a name="next-steps"></a>Następne kroki
* Przeglądanie bardziej zaawansowanych funkcji synchronizacji w trybie offline, takich jak rozwiązywanie konfliktów w [przykładzie synchronizacji w trybie offline]
* Przejrzyj odwołanie do interfejsu API synchronizacji w trybie offline w [dokumentacji interfejsu API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova szybki start]: app-service-mobile-cordova-get-started.md
[Próbka synchronizacji w trybie offline]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Narzędzia programu Visual Studio dla Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
