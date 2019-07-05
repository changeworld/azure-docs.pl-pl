---
title: Włączanie synchronizacji offline dla aplikacji mobilnej platformy Azure (system Android)
description: Dowiedz się, jak używać usługi App Service Mobile Apps do pamięci podręcznej i synchronizacji danych w trybie offline do aplikacji dla systemu Android
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
ms.openlocfilehash: 3fe5b176d864fd4cdd1ff49d8c064495663aa3b0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443577"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Włączanie synchronizacji offline dla aplikacji mobilnej systemu Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center jest inwestujemy w nowe i zintegrowane usługi decydujące znaczenie dla aplikacji mobilnych. Deweloperzy mogą używać **kompilacji**, **testu** i **dystrybucji** usług do konfigurowania potoku ciągłej integracji i ciągłego dostarczania. Gdy aplikacja jest wdrażana, deweloperzy mogą monitorować stan i użycie ich przy użyciu aplikacji **Analytics** i **diagnostyki** usług i angażuj użytkowników za pomocą **wypychania** Usługa. Deweloperzy mogą również wykorzystać **uwierzytelniania** do uwierzytelniania użytkowników i **danych** usługę, aby utrwalić i synchronizowanie danych aplikacji w chmurze. Zapoznaj się z [platformy App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-get-started-offline-data) już dziś.
>

## <a name="overview"></a>Przegląd
Ten samouczek obejmuje funkcję synchronizacji w trybie offline z usługą Azure Mobile Apps dla systemu Android. Synchronizacja w trybie offline umożliwia użytkownikom końcowym interakcję z aplikacją mobilną&mdash;wyświetlanie, dodawanie lub modyfikowanie danych&mdash;nawet w przypadku braku połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie jest w trybie online, zmiany te są synchronizowane za pomocą zdalnego wewnętrznej bazy danych.

Jeśli jest to Twój pierwszy kontakt z usługą Azure Mobile Apps, najpierw należy Ukończ samouczek [tworzenie aplikacji dla systemu Android]. Jeśli nie używasz projektu serwera pobranego — szybki start, należy dodać pakiety rozszerzeń dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz temat [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps].

## <a name="update-the-app-to-support-offline-sync"></a>Aktualizowanie aplikacji w celu obsługi synchronizacji w trybie offline
Synchronizacja w trybie offline, do odczytu i zapisu z *zsynchronizuj spis* (przy użyciu *IMobileServiceSyncTable* interfejs), który jest częścią **SQLite** bazy danych na urządzeniu.

Aby wypychanie i ściąganie zmian między wersjami urządzeń i usług Azure Mobile Services, należy użyć *kontekst synchronizacji* (*MobileServiceClient.SyncContext*), który można zainicjować z lokalnej bazy danych do przechowywania dane lokalnie.

1. W `TodoActivity.java`, komentarz definicja `mToDoTable` i usuń znaczniki komentarza wersja tabeli synchronizacji:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. W `onCreate` metody komentarz istniejących inicjowanie `mToDoTable` i usuń znaczniki komentarza tę definicję:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. W `refreshItemsFromTable` komentarz definicję `results` i usuń znaczniki komentarza tę definicję:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Komentarz definicję `refreshItemsFromMobileServiceTable`.
5. Komentarz definicji `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Komentarz definicji `sync`:
   
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
W tej sekcji Testowanie zachowania przy użyciu sieci Wi-Fi na, a następnie wyłącz sieci Wi-Fi, aby utworzyć scenariusz w trybie offline.

Podczas dodawania elementów danych są przechowywane w magazynie lokalnym SQLite, ale nie zostały zsynchronizowane z usługą mobilną, dopóki nie zostanie naciśnięty klawisz **Odśwież** przycisku. Inne aplikacje mogą mieć różne wymagania dotyczące po danych muszą być zsynchronizowane, ale dla celów demonstracyjnych w tym samouczku ma użytkownik jawnie wysłać żądanie.

Po naciśnięciu tego przycisku, rozpoczyna nowe zadanie w tle. Umieszcza ono najpierw wszystkie zmiany wprowadzone do magazynu lokalnego przy użyciu kontekstu synchronizacji, a następnie ściąga zmienione dane z platformy Azure do lokalnej tabeli.

### <a name="offline-testing"></a>Testowanie offline
1. Umieść urządzenie lub symulator w *tryb samolotowy*. Spowoduje to utworzenie scenariusz w trybie offline.
2. Dodaj *ToDo* elementy lub znacznik, niektóre elementy jako ukończone. Zamknij urządzenie lub symulator (lub wymuszanie zamknięcia aplikacji) i uruchom ponownie. Sprawdź, czy zmiany zostały utrwalone w urządzeniu, ponieważ są one przechowywane w magazynie lokalnym bazy danych SQLite.
3. Wyświetlanie zawartości dotyczącej platformy Azure *TodoItem* tabeli za pomocą narzędzia SQL, takich jak *SQL Server Management Studio*, lub klienta REST, takie jak *Fiddler* lub  *Postman*. Sprawdź, czy nowe elementy *nie* zostały zsynchronizowane z serwerem
   
       + Przypadku zaplecza Node.js, przejdź do [witryny Azure portal](https://portal.azure.com/), a w aplikacji mobilnej kliknij zaplecza **łatwych tabel** > **TodoItem** do wyświetlania zawartości `TodoItem`tabeli.
       + Zaplecze platformy .NET, można wyświetlić w treści, albo za pomocą SQL narzędzia takie jak *SQL Server Management Studio*, lub klienta REST, takie jak *Fiddler* lub *Postman*.
4. Włącz sieć Wi-Fi w urządzenia lub symulatora. Następnie naciśnij klawisz **Odśwież** przycisku.
5. Wyświetl dane TodoItem ponownie w witrynie Azure portal. Teraz powinien pojawić się nowe i zmienione funkcje TodoItems.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]
* [Cloud Cover: Synchronizacja offline w usługach Azure Mobile Services] \(Uwaga: wideo jest w usłudze Mobile Services, ale synchronizacja w trybie offline, działa w podobny sposób jak w usłudze Azure Mobile Apps\)

<!-- URLs. -->

[Synchronizowanie danych w trybie offline w usłudze Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[Tworzenie aplikacji dla systemu Android]: app-service-mobile-android-get-started.md

[Cloud Cover: Synchronizacja offline w usługach Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

