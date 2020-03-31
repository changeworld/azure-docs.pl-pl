---
title: Włącz synchronizację offline (Android)
description: Dowiedz się, jak używać aplikacji mobilnych usługi App Service do buforowania i synchronizowania danych offline w aplikacji na Androida.
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: c215105af5fe1ef8056b0d816cf2c2a6b96f2038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461627"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Włącz synchronizację offline dla aplikacji mobilnej na Androida
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Omówienie
W tym samouczku opisano funkcję synchronizacji w trybie offline usługi Azure Mobile Apps dla systemu Android. Synchronizacja w trybie offline umożliwia&mdash;użytkownikom końcowym interakcję z&mdash;przeglądaniem, dodawaniem lub modyfikowaniem danych aplikacji mobilnej, nawet jeśli nie ma połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Po powrocie urządzenia do trybu online zmiany te są synchronizowane ze zdalnym zapleczem.

Jeśli jest to twoje pierwsze środowisko z aplikacjami mobilnymi platformy Azure, należy najpierw ukończyć samouczek [Tworzenie aplikacji na Androida]. Jeśli nie używasz pobranego projektu serwera szybkiego startu, należy dodać pakiety rozszerzeń dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji azure mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej o funkcji synchronizacji w trybie offline, zobacz temat [Synchronizacja danych w trybie offline w aplikacjach mobilnych platformy Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Aktualizowanie aplikacji do obsługi synchronizacji w trybie offline
Synchronizacja w trybie offline umożliwia odczytywanie i pisanie z *tabeli synchronizacji* (przy użyciu interfejsu *IMobileServiceSyncTable),* która jest częścią bazy danych **SQLite** na urządzeniu.

Aby wypychać i ściągać zmiany między urządzeniem a usługami azure mobile, należy użyć *kontekstu synchronizacji* *(MobileServiceClient.SyncContext),* który można zainicjować z lokalnej bazy danych do przechowywania danych lokalnie.

1. W `TodoActivity.java`polu komentarz do `mToDoTable` istniejącej definicji tabeli synchronizacji i odkomentuj ich:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. W `onCreate` metodzie, skomentować istniejącą `mToDoTable` inicjację i uncomment tej definicji:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. W `refreshItemsFromTable` komentarzu definicji `results` i uncomment tej definicji:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Wypowiedzieć definicję `refreshItemsFromMobileServiceTable`.
5. Niekomentuj `refreshItemsFromMobileServiceTableSyncTable`definicji:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Niekomentuj `sync`definicji:
   
        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Testowanie aplikacji
W tej sekcji można przetestować zachowanie z Wi-Fi włączone, a następnie wyłączyć Wi-Fi, aby utworzyć scenariusz w trybie offline.

Po dodaniu elementów danych są one przechowywane w lokalnym magazynie SQLite, ale nie są synchronizowane z usługą mobilną, dopóki nie naciśniesz przycisku **Odśwież.** Inne aplikacje mogą mieć różne wymagania dotyczące, gdy dane muszą być synchronizowane, ale do celów demonstracyjnych ten samouczek ma użytkownika jawnie go zażądać.

Po naciśnięciu tego przycisku rozpoczyna się nowe zadanie w tle. Najpierw wypycha wszystkie zmiany wprowadzone do magazynu lokalnego przy użyciu kontekstu synchronizacji, a następnie pobiera wszystkie zmienione dane z platformy Azure do tabeli lokalnej.

### <a name="offline-testing"></a>Testowanie w trybie offline
1. Umieść urządzenie lub symulator w *trybie samolotowym*. Spowoduje to utworzenie scenariusza w trybie offline.
2. Dodaj niektóre elementy *do wykonania* lub oznacz niektóre elementy jako ukończone. Zamknij urządzenie lub symulator (lub przymusowo zamknij aplikację) i uruchom ponownie. Sprawdź, czy zmiany zostały utrwalone na urządzeniu, ponieważ są one przechowywane w lokalnym magazynie SQLite.
3. Wyświetlanie zawartości tabeli Azure *TodoItem* za pomocą narzędzia SQL, takiego jak *SQL Server Management Studio,* lub klienta REST, takiego jak *Fiddler* lub *Postman.* Sprawdź, czy nowe elementy *nie* zostały zsynchronizowane z serwerem
   
       + W przypadku zaplecza Node.js przejdź do [witryny Azure portal](https://portal.azure.com/), a w wewnętrznej bazy danych `TodoItem` aplikacji mobilnej kliknij przycisk Łatwe **tabele** > **TodoItem,** aby wyświetlić zawartość tabeli.
       + W przypadku wewnętrznej bazy danych .NET wyświetl zawartość tabeli za pomocą narzędzia SQL, takiego jak *SQL Server Management Studio,* lub klienta REST, takiego jak *Fiddler* lub *Postman.*
4. Włącz WiFi w urządzeniu lub symulatorze. Następnie naciśnij przycisk **Odśwież.**
5. Wyświetl dane TodoItem ponownie w witrynie Azure portal. Nowe i zmienione TodoItems powinny teraz pojawić się.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]
* [Chmura: Synchronizacja w trybie offline w usługach] \(azure mobile uwaga: klip wideo jest dotyczący usług mobilnych, ale synchronizacja w trybie offline działa w podobny sposób w aplikacjach mobilnych platformy Azure\)

<!-- URLs. -->

[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[Tworzenie aplikacji na Androida]: app-service-mobile-android-get-started.md

[Zachmurzenie: synchronizacja w trybie offline w usługach azure dla urządzeń przenośnych]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

