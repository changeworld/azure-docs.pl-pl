---
title: Włączanie synchronizacji w trybie offline dla aplikacji mobilnej platformy Azure (Android)
description: Dowiedz się, jak używać App Service Mobile Apps do buforowania i synchronizowania danych w trybie offline w aplikacji systemu Android
documentationcenter: android
author: elamalani
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 0180e100432ac34b876af04ad99c9a5d189455c3
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025463"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Włączanie synchronizacji w trybie offline dla aplikacji mobilnej systemu Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi centralne do tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług **kompilowania**, **testowania** i **dystrybucji** , aby skonfigurować ciągłą integrację i potok dostarczania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie swojej aplikacji przy użyciu usług **analizy** i **diagnostyki** oraz angażować się z użytkownikami za pomocą usługi **wypychania** . Deweloperzy mogą również korzystać z **uwierzytelniania** w celu uwierzytelniania użytkowników i usługi **danych** w celu utrwalania i synchronizowania danych aplikacji w chmurze.
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze App Center [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd
W tym samouczku omówiono funkcję synchronizacji w trybie offline w usłudze Azure Mobile Apps dla systemu Android. Synchronizacja w trybie offline umożliwia użytkownikom końcowym korzystanie z aplikacji mobilnej @ no__t-0viewing, Dodawanie lub modyfikowanie danych @ no__t-1even, gdy nie ma połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie przewróci do trybu online, te zmiany są synchronizowane ze zdalną zapleczem.

Jeśli jest to pierwsze środowisko pracy z usługą Azure Mobile Apps, należy najpierw zakończyć pracę z samouczkiem [Tworzenie aplikacji dla systemu Android]. Jeśli nie używasz pobranego projektu szybkiego startu serwera, musisz dodać pakiety rozszerzenia dostępu do danych do projektu. Aby uzyskać więcej informacji o pakietach rozszerzeń serwera, zobacz [Work with the .NET zaplecz Server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="update-the-app-to-support-offline-sync"></a>Aktualizowanie aplikacji w celu obsługi synchronizacji w trybie offline
Synchronizacja w trybie offline umożliwia odczytywanie i zapisywanie z *tabeli synchronizacji* (przy użyciu interfejsu *IMobileServiceSyncTable* ), która jest częścią bazy danych programu **SQLite** na urządzeniu.

Aby wypchnąć i ściągnąć zmiany między urządzeniem i Mobile Services platformy Azure, należy użyć *kontekstu synchronizacji* (*MobileServiceClient. SyncContext*), który jest inicjowany z lokalną bazą danych w celu lokalnego przechowywania danych.

1. W `TodoActivity.java` Dodaj komentarz do istniejącej definicji `mToDoTable` i Usuń komentarz z wersji tabeli synchronizacji:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. W metodzie `onCreate` Dodaj komentarz do istniejącej inicjalizacji `mToDoTable` i Usuń komentarz z tej definicji:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. W `refreshItemsFromTable` Dodaj komentarz do definicji `results` i Usuń komentarz z tej definicji:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Dodaj komentarz do definicji `refreshItemsFromMobileServiceTable`.
5. Usuń komentarz z definicji `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Usuń komentarz z definicji `sync`:
   
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
W tej sekcji przetestujesz zachowanie przy użyciu sieci Wi-Fi w systemie, a następnie wyłączysz usługę Wi-Fi w celu utworzenia scenariusza w trybie offline.

Po dodaniu elementów danych są one przechowywane w lokalnym magazynie oprogramowania SQLite, ale nie są synchronizowane z usługą mobilną do momentu naciśnięcia przycisku **Odśwież** . Inne aplikacje mogą mieć różne wymagania dotyczące sytuacji, w których dane wymagają synchronizacji, ale w celach demonstracyjnych ten samouczek ma jawnie żądanie użytkownika.

Po naciśnięciu tego przycisku zostanie uruchomione nowe zadanie w tle. Najpierw wypychanie wszystkich zmian wprowadzonych do lokalnego magazynu przy użyciu kontekstu synchronizacji, a następnie ściąga wszystkie zmienione dane z platformy Azure do tabeli lokalnej.

### <a name="offline-testing"></a>Testowanie w trybie offline
1. Umieść urządzenie lub symulator w *trybie samolotowym*. Spowoduje to utworzenie scenariusza w trybie offline.
2. Dodaj elementy do *zrobienia* lub Oznacz niektóre elementy jako ukończone. Wyjdź z urządzenia lub symulatora (lub Wymuś zamknięcie aplikacji) i uruchom ponownie. Sprawdź, czy zmiany zostały utrwalone na urządzeniu, ponieważ są przechowywane w lokalnym magazynie oprogramowania SQLite.
3. Zapoznaj się z zawartością tabeli usługi Azure *TodoItem* za pomocą narzędzia SQL, takiego jak *SQL Server Management Studio*, lub klienta REST, takiego jak *programu Fiddler* lub *Poster*. Sprawdź, czy nowe elementy *nie* zostały zsynchronizowane z serwerem
   
       + W przypadku zaplecza Node. js przejdź do [Azure Portal](https://portal.azure.com/), a w zaplecze aplikacji mobilnej kliknij pozycję **łatwe tabele** > **TodoItem** , aby wyświetlić zawartość tabeli `TodoItem`.
       + W przypadku zaplecza platformy .NET Wyświetl zawartość tabeli przy użyciu narzędzia SQL, takiego jak *SQL Server Management Studio*, lub klienta REST, takiego jak *programu Fiddler* lub *Poster*.
4. Włącz sieć Wi-Fi na urządzeniu lub symulatorze. Następnie naciśnij przycisk **Odśwież** .
5. Ponownie Wyświetl dane TodoItem w Azure Portal. Nowe i zmienione TodoItems powinny teraz pojawić się.

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]
* [Pokrycie w chmurze: synchronizacja w trybie offline na platformie azure Mobile Services] @no__t — 1note: film wideo znajduje się na Mobile Services, ale synchronizacja offline działa w podobny sposób na platformie Azure Mobile Apps @ no__t-2

<!-- URLs. -->

[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[Tworzenie aplikacji dla systemu Android]: app-service-mobile-android-get-started.md

[Pokrycie w chmurze: synchronizacja w trybie offline na platformie Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

