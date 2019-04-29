---
title: Włączanie synchronizacji offline dla aplikacji mobilnej platformy Azure (Cordova) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać aplikacji usługi Mobile App do pamięci podręcznej i synchronizacji danych w trybie offline w aplikacji Cordova
documentationcenter: cordova
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 44c54b570a38eb1a3b9ca773893599d1d497dfa2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110991"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Włączanie synchronizacji offline dla aplikacji mobilnej Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

W tym samouczku wprowadzono funkcję synchronizacji w trybie offline z usługą Azure Mobile Apps Cordova. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną&mdash;wyświetlanie, dodawanie lub modyfikowanie danych&mdash;nawet w przypadku braku połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych.  Gdy urządzenie jest w trybie online, zmiany te są synchronizowane z usługi zdalnej.

Ten samouczek opiera się na rozwiązanie szybkiego startu Cordova dla aplikacji mobilnych, utworzony po ukończeniu tego samouczka [Apache Cordova — szybki start]. W tym samouczku należy zaktualizować szybkiego startu rozwiązania można dodać funkcji w trybie offline z usługą Azure Mobile Apps.  Możemy też wyróżnić kodu specyficznego dla trybu offline w aplikacji.

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]. Aby uzyskać szczegółowe informacje o użycie interfejsu API, zobacz [dokumentacji interfejsu API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Dodawanie synchronizacji offline do szybkiego startu rozwiązania
Kod synchronizacji w trybie offline, należy dodać do aplikacji. Synchronizacja w trybie offline wymaga wtyczki cordova sqlite magazynu, który automatycznie pobiera dodane do Twojej aplikacji, gdy wtyczka Azure Mobile Apps znajduje się w projekcie. Projekt szybkiego startu zawiera oba te wtyczki.

1. W Eksploratorze rozwiązań programu Visual Studio Otwórz index.js, a następnie zastąp następujący kod

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    przy użyciu tego kodu:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Następnie zastąp następujący kod:

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

    Dodatki kodu z poprzednim inicjowanie magazynu lokalnego i zdefiniuj lokalnej tabeli, która odpowiada wartości w kolumnach używane w usłudze Azure zaplecza. (Nie trzeba uwzględnić wszystkie wartości kolumny w tym kodzie).  `version` Pola jest obsługiwana przez zaplecza aplikacji mobilnych i służy do rozwiązywania konfliktów.

    Pobieranie odwołania do kontekstu synchronizacji przez wywołanie metody **getSyncContext**. Kontekst synchronizacji pomaga zachować relacje między tabelami, śledzenia i wypychaniu zmian można znaleźć we wszystkich tabelach aplikacji klienckiej został zmodyfikowany podczas `.push()` jest wywoływana.

3. Zaktualizuj adres URL aplikacji na adres URL aplikacji w aplikacji mobilnej.

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

    Powyższy kod inicjalizuje kontekst synchronizacji, a następnie wywołuje getSyncTable (zamiast getTable), aby odwołać się do lokalnej tabeli.

    Ten kod używa lokalnej bazy danych w przypadku tworzenia, odczytu, aktualizacji i usuwania (CRUD) operacje tabeli.

    W tym przykładzie wykonuje prostą obsługę na konfliktów synchronizacji błędów. Rzeczywistej aplikacji będzie obsługiwać różne błędy, takich jak warunki sieciowe, konflikty serwera i inne. Aby uzyskać przykłady kodu, zobacz [przykładowe synchronizacji w trybie offline].

5. Następnie dodaj tę funkcję, aby wykonać rzeczywiste synchronizacji.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Zdecyduj, kiedy należy wypchnąć zmiany do zaplecza aplikacji mobilnej, wywołując **syncContext.push()**. Na przykład, można wywołać **syncBackend** programu obsługi zdarzeń przycisku powiązane przycisk synchronizacji.

## <a name="offline-sync-considerations"></a>Zagadnienia dotyczące synchronizacji w trybie offline

W tym przykładzie **wypychania** metody **syncContext** jest wywoływany tylko podczas uruchamiania aplikacji w funkcji wywołania zwrotnego dla nazwy logowania.  W przypadku aplikacji rzeczywistych można także spowodować tę funkcję synchronizacji wyzwolone ręcznie lub po zmianie stanu sieci.

Podczas ściągania jest wykonywane względem tabeli, która ma oczekujące lokalne aktualizacje śledzone przez kontekst, operacji ściągania automatycznie wyzwala powiadomienie wypychane. Podczas odświeżania, dodawania i kończenie elementów w tym przykładzie można pominąć jawną **wypychania** wywołanie, ponieważ może być nadmiarowe.

Podany kod badane są wszystkie rekordy w tabeli todoItem zdalnego, ale jest również możliwe do filtrowania rekordów, przekazując identyfikator zapytania i Wyślij zapytanie w celu **wypychania**. Aby uzyskać więcej informacji, zobacz sekcję *synchronizacja przyrostowa* w [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="optional-disable-authentication"></a>(Opcjonalnie) Wyłącz uwierzytelnianie

Jeśli nie chcesz skonfigurować uwierzytelnianie przed testowania synchronizacji w trybie offline, komentarz funkcji wywołania zwrotnego dla nazwy logowania, ale pozostawić kod wewnątrz odkomentowana funkcji wywołania zwrotnego.  Po zakomentowując wierszy logowania, następujący kod:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Teraz aplikacja przeprowadza synchronizację z zaplecza platformy Azure po uruchomieniu aplikacji.

## <a name="run-the-client-app"></a>Uruchom aplikację kliencką
Synchronizacja w trybie offline teraz włączone aplikacja kliencka można uruchomić co najmniej raz, na każdej platformie, aby wypełnić bazę danych magazynu lokalnego. Później symulować scenariusz w trybie offline i modyfikować dane w magazynie lokalnym, gdy aplikacja działa w trybie offline.

## <a name="optional-test-the-sync-behavior"></a>(Opcjonalnie) Testowanie zachowania synchronizacji
W tej sekcji możesz zmodyfikować projekt klienta, aby symulować scenariusz w trybie offline przy użyciu nieprawidłowy adres URL aplikacji dla zaplecza. Podczas dodawania lub zmiany elementów danych, zmiany te są przechowywane w magazynie lokalnym, ale nie są synchronizowane z magazynem danych zaplecza, przed ponownym nawiązaniu połączenia.

1. W Eksploratorze rozwiązań Otwórz plik index.js i projektu i zmień adres URL aplikacji, aby wskazywał nieprawidłowy adres URL, podobnie do poniższego kodu:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. W index.html, zaktualizuj dostawcy usług Kryptograficznych `<meta>` element o tej samej nieprawidłowy adres URL.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Tworzenie i uruchom aplikację kliencką i zwróć uwagę, że wyjątek jest rejestrowane w konsoli, gdy aplikacja próbuje zsynchronizować z wewnętrznej bazy danych po zalogowaniu. Nowe elementy, które można dodać istnieje tylko w magazynie lokalnym, dopóki nie są one przekazywane do zaplecza aplikacji mobilnych. Aplikacja kliencka zachowuje się tak, jest ona dołączona do wewnętrznej bazy danych.

4. Zamknij aplikację i uruchom ponownie, aby sprawdzić, czy nowych elementów, który został utworzony są utrwalane w magazynie lokalnym.

5. (Opcjonalnie) Użyj programu Visual Studio, aby wyświetlić swoje tabeli Azure SQL Database, aby zobaczyć, że dane w wewnętrznej bazy danych nie została zmieniona.

    W programie Visual Studio, otwórz **Eksploratora serwera**. Przejdź do bazy danych w **Azure**->**baz danych SQL**. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Otwórz w Eksploratorze obiektów SQL Server**. Teraz możesz przejść do tabeli bazy danych SQL i jego zawartość.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Opcjonalnie) Testowanie ponowne łączenie do usługi zaplecza aplikacji mobilnych

W tej sekcji ponowne łączenie aplikacji zaplecza mobilnego, który symuluje aplikacji powracające do trybu online. Po zalogowaniu się w danych jest synchronizowana z usługą w zapleczu mobilnym.

1. Otwórz ponownie index.js i przywrócić adres URL aplikacji.
2. Otwórz ponownie index.html i popraw adres URL aplikacji w programie CSP `<meta>` elementu.
3. Ponownie skompiluj i uruchom aplikację kliencką. Aplikacja próbuje synchronizować z zapleczem aplikacji mobilnej po zalogowaniu. Upewnij się, że żadne wyjątki są rejestrowane w konsoli debugowania.
4. (Opcjonalnie) Wyświetl zaktualizowane dane za pomocą Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak Fiddler. Zwróć uwagę, że dane zostały synchronizowane między wewnętrznej bazy danych i magazynu lokalnego.

    Zwróć uwagę, dane zostały zsynchronizowane między bazy danych i magazynem lokalnym i zawiera elementy, które dodano odłączeniu Twojej aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]
* [Visual Studio Tools for Apache Cordova]

## <a name="next-steps"></a>Kolejne kroki
* Przegląd bardziej zaawansowane funkcje synchronizacji w trybie offline, takie jak rozwiązywanie konfliktów w [przykładowe synchronizacji w trybie offline]
* Przejrzyj odwołania API synchronizacji w trybie offline w [dokumentacji interfejsu API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova — szybki start]: app-service-mobile-cordova-get-started.md
[Przykładowe synchronizacji w trybie offline]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
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
