---
title: Włączanie synchronizacji w trybie offline dla aplikacji mobilnej Azure (Cordova) | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: c12328a441a8cc438fa3e974863cc8adf8651b50
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27593718"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji mobilnej oprogramowania Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Ten samouczek przedstawia funkcję synchronizacji w trybie offline w usłudze Azure Mobile Apps dla oprogramowania Cordova. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną&mdash;przeglądanie, dodawanie lub modyfikowanie danych&mdash;nawet wtedy, gdy istnieje połączenie sieciowe. Zmiany są przechowywane w lokalnej bazie danych.  Gdy urządzenie jest w trybie online, te zmiany są synchronizowane z usługi zdalnej.

W tym samouczku opiera się na rozwiązanie szybkiego startu Cordova Mobile Apps, utworzony po ukończeniu samouczka [szybki start dla oprogramowania Apache Cordova]. W tym samouczku należy zaktualizować rozwiązania Szybki Start można dodać funkcji w trybie offline z usługą Azure Mobile Apps.  Możemy również zaznacz kodu w trybie offline określonych w aplikacji.

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps]. Aby uzyskać szczegółowe informacje dotyczące użycia interfejsu API, zobacz [dokumentacji interfejsu API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Dodaj synchronizacji w trybie offline do rozwiązania Szybki Start
Kod synchronizacji w trybie offline, należy dodać do aplikacji. Synchronizacja w trybie offline wymaga wtyczki cordova sqlite magazynu, który automatycznie pobiera dodany do aplikacji po wtyczkę Azure Mobile Apps jest dołączony do projektu. Projekt szybkiego startu zawiera oba te wtyczki.

1. W Eksploratorze rozwiązań programu Visual Studio Otwórz index.js i Zastąp następujący kod

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    o tym kodzie:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Następnie zastąp następujący kod:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    o tym kodzie:

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

    Dodatki kod z powyższej zainicjować magazynu lokalnego i zdefiniuj lokalnej tabeli, która odpowiada wartości w kolumnie używane w sieci Azure zaplecza. (Nie trzeba obejmują wszystkie wartości w kolumnie w tym kodu).  `version` Pole jest obsługiwany przez zaplecze aplikacji mobilnej, służy do rozwiązywania konfliktów.

    Możesz odwołać się do kontekstu synchronizacji przez wywołanie metody **getSyncContext**. Kontekst synchronizacji pomaga zachować relacje między tabelami, śledzenia i wypychanie zmiany we wszystkich tabelach aplikacji klienckiej został zmodyfikowany podczas obliczania `.push()` jest wywoływana.

3. Zaktualizuj adres URL aplikacji do adresu URL aplikacji w Twojej aplikacji mobilnej.

4. Następnie Zastąp ten kod:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    o tym kodzie:

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

    Poprzedni kod inicjuje kontekstu synchronizacji, a następnie wywołuje getSyncTable (zamiast getTable), aby pobrać odwołanie do lokalnej tabeli.

    To kod korzysta z lokalnej bazy danych dla wszystkich tworzenia, odczytu, aktualizacji i usuwania (CRUD) operacje tabeli.

    W tym przykładzie przeprowadza prosty błąd obsługi na konfliktów synchronizacji. Rzeczywistej aplikacji będzie obsługiwać różne błędy, takie jak warunków sieciowych, powoduje konflikt z serwera i inne. Aby uzyskać przykłady kodu, zobacz [próbki synchronizacji w trybie offline].

5. Następnie dodaj tę funkcję, aby wykonać rzeczywiste synchronizacji.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Podjęcie decyzji dotyczącej Wypchnij zmiany do zaplecza aplikacji mobilnej, wywołując **syncContext.push()**. Na przykład można wywołać **syncBackend** w obsłudze zdarzeń przycisk powiązane przycisk synchronizacji.

## <a name="offline-sync-considerations"></a>Zagadnienia dotyczące synchronizacji w trybie offline

W przykładzie **wypychania** metody **syncContext** wywołać tylko podczas uruchamiania aplikacji w funkcji wywołania zwrotnego dla nazwy logowania.  W przypadku aplikacji rzeczywistych można także spowodować tej funkcji synchronizacji wywołane ręcznie lub zmiany stanu sieci.

Podczas wykonywania ściąganie względem tabeli, która ma oczekujące aktualizacje z lokalnego śledzone przez kontekstu, która ściągnięcia operacji automatycznie wyzwalacze wypychania. Podczas odświeżania, dodawanie i wykonując elementów w tym przykładzie można pominąć jawnych **wypychania** wywołać, ponieważ może być nadmiarowe.

Podany kod odpytywane będą wszystkie rekordy w tabeli todoItem zdalnego, ale jest również możliwe filtrowanie rekordów przez przekazanie identyfikator zapytania i kwerendę **wypychania**. Aby uzyskać więcej informacji, zobacz sekcję *synchronizacja przyrostowa* w [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps].

## <a name="optional-disable-authentication"></a>(Opcjonalnie) Wyłącz uwierzytelnianie

Jeśli nie chcesz skonfigurować uwierzytelnianie przed testowych synchronizacją w trybie offline, funkcja wywołania zwrotnego dla logowania w komentarz, ale pozostawić kodu wewnątrz odkomentowana funkcja wywołania zwrotnego.  Po komentowania wierszy logowania, następujący kod:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Teraz aplikacja przeprowadza synchronizację z wewnętrznej bazy danych Azure po uruchomieniu aplikacji.

## <a name="run-the-client-app"></a>Uruchamianie aplikacji klienta
Teraz włączony synchronizacja w trybie offline umożliwia uruchamianie aplikacji klienckiej co najmniej raz na każdej z platform do wypełniania bazy danych magazynu lokalnego. Później symulować scenariusz w trybie offline i modyfikować danych w magazynie lokalnym, gdy aplikacja jest w trybie offline.

## <a name="optional-test-the-sync-behavior"></a>(Opcjonalnie) Testowanie zachowanie synchronizacji
W tej sekcji możesz zmodyfikować projekt klienta, aby symulować scenariusz w trybie offline za pomocą nieprawidłowy adres URL aplikacji w danym zapleczu. Podczas dodawania lub zmienić elementów danych, zmiany te są przechowywane w lokalnym magazynie, ale nie są zsynchronizowane z wewnętrznej bazy danych magazynu danych, dopóki nie zostanie ponownie nawiązać połączenie.

1. W Eksploratorze rozwiązań Otwórz plik projektu index.js i zmień adres URL aplikacji, aby wskazywał nieprawidłowy adres URL, podobnie do następującego kodu:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Dostawca usług Kryptograficznych aktualizacji index.html, `<meta>` element o tej samej nieprawidłowy adres URL.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Skompiluj i uruchom aplikację klienta i zwróć uwagę, że wyjątek jest rejestrowane w konsoli, gdy aplikacja próbuje synchronizować z wewnętrznej bazy danych po logowaniu. Nowe elementy, które można dodać istnieje tylko w lokalnym magazynie, dopóki nie są one przenoszone do zaplecza aplikacji mobilnych. Aplikacja kliencka zachowuje się tak, jakby nie jest połączona z wewnętrznej bazy danych.

4. Zamknij aplikację i uruchom ponownie, aby sprawdzić, czy nowe elementy utworzone zostały utrwalone w magazynie lokalnym.

5. (Opcjonalnie) Aby wyświetlić tabelę bazy danych SQL Azure, aby zobaczyć, czy dane w bazie danych zaplecza nie uległy zmianie, należy użyć programu Visual Studio.

    W programie Visual Studio Otwórz **Eksploratora serwera**. Przejdź do bazy danych w **Azure**->**baz danych SQL**. Kliknij prawym przyciskiem myszy bazę danych i wybierz **Otwórz w Eksploratorze obiektów SQL Server**. Możesz teraz przejść do tabeli bazy danych SQL i jego zawartość.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Opcjonalnie) Testowanie ponowne nawiązanie połączenia z wewnętrzną bazą danych w przenośnych

W tej sekcji ponowne łączenie aplikacji zaplecza mobilnego, która symuluje aplikacji powracające do trybu online. Podczas logowania danych jest synchronizowany z przenośnymi wewnętrzną bazą danych.

1. Otwórz ponownie index.js i przywrócić adres URL aplikacji.
2. Otwórz ponownie index.html i popraw adres URL aplikacji w dostawcy CSP `<meta>` elementu.
3. Ponownie skompilować i uruchomić aplikację klienta. Aplikacja próbuje synchronizować z zaplecza aplikacji mobilnej po logowania. Upewnij się, że żadne wyjątki są rejestrowane w konsoli debugowania.
4. (Opcjonalnie) Wyświetlanie zaktualizowanych danych przy użyciu Eksplorator obiektów SQL Server lub narzędzia REST, takiego jak Fiddler. Należy zauważyć, że zsynchronizowano dane między wewnętrzna baza danych i magazynu lokalnego.

    Zwróć uwagę, dane zostały zsynchronizowane między bazy danych i Magazyn lokalny i zawiera elementy, które dodano odłączeniu aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe
* [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps]
* [Visual Studio Tools for Apache Cordova]

## <a name="next-steps"></a>Kolejne kroki
* Przejrzyj bardziej zaawansowane funkcje synchronizacji w trybie offline, takie jak rozwiązywania konfliktów w [próbki synchronizacji w trybie offline]
* Przejrzyj odwołanie do synchronizacji w trybie offline interfejsu API w [dokumentacji interfejsu API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[szybki start dla oprogramowania Apache Cordova]: app-service-mobile-cordova-get-started.md
[próbki synchronizacji w trybie offline]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
