---
title: Włączanie synchronizacji w trybie offline dla aplikacji mobilnej Azure (Android)
description: Dowiedz się, jak używać App Service Mobile Apps do pamięci podręcznej i synchronizacji danych w trybie offline w aplikacji systemu Android
documentationcenter: android
author: conceptdev
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 152702bed0ea061c3cb86e2ff6f88bf204f9d243
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2018
ms.locfileid: "27594195"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Włączanie synchronizacji w trybie offline dla twojej aplikacji mobilnej systemu Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Przegląd
Ten samouczek obejmuje funkcję synchronizacji w trybie offline w usłudze Azure Mobile Apps dla systemu Android. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną&mdash;przeglądanie, dodawanie lub modyfikowanie danych&mdash;nawet wtedy, gdy istnieje połączenie sieciowe. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie jest w trybie online, te zmiany są synchronizowane z zdalnego wewnętrznej bazy danych.

Jeśli jest to środowiska pierwszy z usługą Azure Mobile Apps, należy najpierw Ukończ samouczek [Utwórz aplikację systemu Android]. Jeśli nie używasz szybki start pobrany Projekt serwera, należy dodać pakietów rozszerzenia dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzenia serwera, zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps].

## <a name="update-the-app-to-support-offline-sync"></a>Aktualizowanie aplikacji do obsługi synchronizacji w trybie offline
Z synchronizacji w trybie offline do odczytu i zapisu z *synchronizacji tabeli* (przy użyciu *IMobileServiceSyncTable* interface), który jest częścią **SQLite** bazy danych na urządzeniu.

Aby wypychania i ściągania zmiany między urządzeniami i usług Azure Mobile Services, należy użyć *kontekst synchronizacji* (*MobileServiceClient.SyncContext*), który zainicjować z lokalnej bazy danych do przechowywania dane lokalne.

1. W `TodoActivity.java`, komentarz definicja `mToDoTable` i Usuń komentarz wersję usługi synchronizacji tabeli:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. W `onCreate` metody komentarz istniejących inicjowanie `mToDoTable` i Usuń komentarz tej definicji:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. W `refreshItemsFromTable` komentarz definicję `results` i Usuń komentarz tej definicji:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Komentarz definicję `refreshItemsFromMobileServiceTable`.
5. Usuń znaczniki komentarza definicji `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Usuń znaczniki komentarza definicji `sync`:
   
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
W tej sekcji testy na zachowanie w przypadku sieci Wi-Fi, a następnie wyłącz sieci Wi-Fi, aby utworzyć scenariusz w trybie offline.

Po dodaniu elementów danych są przechowywane w lokalnym magazynie SQLite, ale nie zostały zsynchronizowane z usługi mobilnej, dopóki nie zostanie naciśnięty klawisz **Odśwież** przycisku. Inne aplikacje mogą mieć inne wymagania dotyczące po danych muszą być zsynchronizowane, ale dla celów demonstracyjnych w tym samouczku został jawnie żądania użytkownika.

Po naciśnięciu tego przycisku uruchamiania nowego zadania w tle. Najpierw wypycha dane wszystkich zmian w lokalnym magazynie przy użyciu kontekstu synchronizacji, a następnie ściąga zmienione dane platformy Azure z lokalnej tabeli.

### <a name="offline-testing"></a>Testowanie w trybie offline
1. Umieść urządzenie lub symulator w *tryb samolotowy*. Spowoduje to utworzenie scenariusz w trybie offline.
2. Dodaj raporty *ToDo* elementy lub znak niektóre elementy jako ukończone. Zamykania urządzenie lub symulator (lub Wymuś aplikacji) i uruchom ponownie. Sprawdź, czy zmiany zostały utrwalone na urządzeniu, ponieważ są one przechowywane w lokalnym magazynie SQLite.
3. Wyświetlanie zawartości platformy Azure *TodoItem* tabeli za pomocą narzędzia SQL, takich jak *programu SQL Server Management Studio*, lub klienta REST, takich jak *Fiddler* lub  *Postman*. Upewnij się, że nowe elementy *nie* zostały zsynchronizowane z serwerem
   
       + Dla zaplecza Node.js, przejdź do [portalu Azure](https://portal.azure.com/), a w aplikacji mobilnej kliknij wewnętrznej bazy danych **łatwe tabel** > **TodoItem** do wyświetlania zawartości `TodoItem`tabeli.
       + Dla zaplecza .NET, Wyświetl zawartość tabeli SQL za pomocą narzędzi takich jak *programu SQL Server Management Studio*, lub klienta REST, takich jak *Fiddler* lub *Postman*.
4. Włącz sieci Wi-Fi w urządzenie lub symulator. Następnie naciśnij klawisz **Odśwież** przycisku.
5. Wyświetl dane TodoItem ponownie w portalu Azure. Nowe i zmienione TodoItems powinien zostać wyświetlony.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps]
* [Okładce chmury: Synchronizacja w trybie Offline w usłudze Azure Mobile Services] \(Uwaga: plik wideo jest w usłudze Mobile Services, ale synchronizacji w trybie offline działa w podobny sposób jak w usłudze Azure Mobile Apps\)

<!-- URLs. -->

[synchronizacji danych w trybie Offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[Utwórz aplikację systemu Android]: app-service-mobile-android-get-started.md

[Okładce chmury: Synchronizacja w trybie Offline w usłudze Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

