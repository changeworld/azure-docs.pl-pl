---
title: Włączanie synchronizacji w trybie offline za pomocą aplikacji mobilnych dla systemu iOS | Microsoft Docs
description: Dowiedz się, jak używać Azure App Service Mobile Apps do buforowania i synchronizowania danych w trybie offline w aplikacjach systemu iOS.
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: f29a28f9a80b64ef0a6890fa8fc7ecd0ca205e66
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388770"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Włączanie synchronizacji w trybie offline za pomocą aplikacji mobilnych dla systemu iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Usługa Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi mające kluczowe znaczenie podczas tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług do **tworzenia**, **testowania** i **dystrybuowania** w celu konfigurowania potoku ciągłej integracji i ciągłego wdrażania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie aplikacji za pomocą usług do **analizy** i **diagnostyki**, a także współpracować z użytkownikami za pomocą usługi do **wypychania**. Deweloperzy mogą również korzystać z usługi **uwierzytelniania** do uwierzytelniania użytkowników oraz usługi **danych** do utrwalania i synchronizowania danych aplikacji w chmurze.
>
> Jeśli chcesz zintegrować usługi w chmurze w aplikacji mobilnej, zarejestruj się w usłudze [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Omówienie
Ten samouczek dotyczy synchronizacji w trybie offline z Mobile Apps funkcją Azure App Service dla systemu iOS. Dzięki synchronizacji w trybie offline użytkownicy końcowi mogą korzystać z aplikacji mobilnej w celu wyświetlania, dodawania i modyfikowania danych, nawet jeśli nie mają połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Po powrocie urządzenia do trybu online zmiany są synchronizowane ze zdalnym zapleczem.

Jeśli jest to pierwsze środowisko pracy z Mobile Apps, należy najpierw ukończyć samouczek [Tworzenie aplikacji dla systemu iOS]. Jeśli nie używasz pobranego projektu serwera szybkiego startu, musisz dodać pakiety rozszerzeń dostępu do danych do projektu. Aby uzyskać więcej informacji o pakietach rozszerzeń serwera, zobacz [Work with the .NET zaplecz Server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej o funkcji synchronizacji w trybie offline, zobacz [Synchronizacja danych w trybie offline w Mobile Apps].

## <a name="review-sync"></a>Przejrzyj kod synchronizacji klienta
Projekt klienta pobrany dla samouczka [Tworzenie aplikacji dla systemu iOS] zawiera już kod obsługujący synchronizację w trybie offline przy użyciu lokalnej podstawowej bazy danych opartej na danych. Ta sekcja zawiera podsumowanie informacji o tym, co zostało już zawarte w kodzie samouczka. Aby zapoznać się z omówieniem koncepcyjnym tej funkcji, zobacz [Synchronizacja danych w trybie offline w Mobile Apps].

Korzystając z funkcji synchronizacji danych w trybie offline Mobile Apps, użytkownicy końcowi mogą współdziałać z lokalną bazą danych, nawet jeśli sieć jest niedostępna. Aby korzystać z tych funkcji w aplikacji, należy zainicjować kontekst synchronizacji `MSClient` i odwołać się do magazynu lokalnego. Następnie należy odwołać się do tabeli za pomocą interfejsu **MSSyncTable** .

W **QSTodoService. m** (cel-C) lub **ToDoTableViewController. Swift** (SWIFT) Zwróć uwagę, że typ elementu członkowskiego **synchronizacji** to **MSSyncTable**. Synchronizacja w trybie offline używa tego interfejsu tabeli synchronizacji zamiast **MSTable**. Gdy używana jest tabela synchronizacji, wszystkie operacje są wykonywane do lokalnego magazynu i są synchronizowane tylko z zapleczem zdalnym z jawnymi operacjami wypychania i ściągania.

 Aby uzyskać odwołanie do tabeli synchronizacji, należy użyć metody **syncTableWithName** na `MSClient`. Aby usunąć funkcje synchronizacji w trybie offline, zamiast tego należy użyć **tableWithName** .

Przed wykonaniem jakiejkolwiek operacji na tabeli należy zainicjować magazyn lokalny. Oto odpowiedni kod:

* **Cel-C**. W metodzie **QSTodoService. init** :

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Kod SWIFT**. W metodzie **ToDoTableViewController. viewDidLoad** :

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Ta metoda tworzy magazyn lokalny przy użyciu interfejsu `MSCoreDataStore`, który zawiera Mobile Apps SDK. Alternatywnie można podać inny magazyn lokalny, implementując protokół `MSSyncContextDataSource`. Ponadto pierwszy parametr **MSSyncContext** jest używany do określania procedury obsługi konfliktów. Ponieważ przeszedł `nil`, otrzymamy domyślną procedurę obsługi konfliktów, która kończy się niepowodzeniem na żadnym konflikcie.

Teraz wykonajmy rzeczywistą operację synchronizacji i Pobierz dane z zdalnego zaplecza:

* **Cel-C**. `syncData` najpierw wypychanie nowych zmian, a następnie wywołuje **pullData** , aby pobrać dane z zdalnego zaplecza. Z kolei Metoda **pullData** pobiera nowe dane, które pasują do zapytania:

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **Kod SWIFT**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc. via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
                      if (opError.operation == .Insert || opError.operation == .Delete) {
                          print("Insert/Delete, failed discarding changes")
                          opError.cancelOperationAndDiscardItemWithCompletion(nil)
                      } else {
                          print("Update failed, reverting to server's copy")
                          opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                      }
                  }
              }
          }
          self.refreshControl?.endRefreshing()
      }
   }
   ```

W wersji celu języka C w `syncData`najpierw Wywołaj **pushWithCompletion** w kontekście synchronizacji. Ta metoda jest składową `MSSyncContext` (a nie tabeli synchronizacji), ponieważ wypycha zmiany we wszystkich tabelach. Tylko rekordy, które zostały zmodyfikowane w sposób lokalny (za pomocą operacji CUD), są wysyłane do serwera. Następnie pomocnik **pullData** jest wywoływany, który wywołuje **MSSyncTable. pullWithQuery** , aby pobrać dane zdalne i zapisać je w lokalnej bazie danych.

W wersji SWIFT, ponieważ operacja push nie była absolutnie konieczna, nie istnieje wywołanie do **pushWithCompletion**. Jeśli w kontekście synchronizacji w tabeli, która wykonuje operację wypychania, istnieją zmiany oczekujące, ściąganie zawsze wystawia wypychanie jako pierwsze. Jednak jeśli masz więcej niż jedną tabelę synchronizacji, najlepiej jawnie wywołać wypychanie, aby upewnić się, że wszystkie elementy są spójne w powiązanych tabelach.

W przypadku wersji zamierzenia języka C i Swift można użyć metody **pullWithQuery** , aby określić zapytanie w celu odfiltrowania rekordów, które mają zostać pobrane. W tym przykładzie zapytanie pobiera wszystkie rekordy w zdalnej tabeli `TodoItem`.

Drugi parametr **pullWithQuery** jest identyfikatorem zapytania, który jest używany do *synchronizacji przyrostowej*. Synchronizacja przyrostowa pobiera tylko rekordy, które zostały zmodyfikowane od ostatniej synchronizacji, przy użyciu sygnatury czasowej `UpdatedAt` rekordu (o nazwie `updatedAt` w magazynie lokalnym). Identyfikator zapytania musi być ciągiem opisowym, który jest unikatowy dla każdej kwerendy logicznej w aplikacji. Aby zrezygnować z synchronizacji przyrostowej, Przekaż `nil` jako identyfikator zapytania. Takie podejście może być potencjalnie niewydajne, ponieważ pobiera wszystkie rekordy dla każdej operacji ściągania.

Aplikacja "cel-C" jest synchronizowana podczas modyfikowania lub dodawania danych, gdy użytkownik wykonuje gest odświeżenia i przy uruchamianiu.

Aplikacja SWIFT synchronizuje się, gdy użytkownik wykonuje gest odświeżenia i przy uruchamianiu.

Ponieważ aplikacja jest synchronizowana, gdy dane są modyfikowane (cel-C) lub za każdym razem, gdy aplikacja jest uruchamiana (zamierzenia C i SWIFT), aplikacja założono, że użytkownik jest w trybie online. W dalszej części należy zaktualizować aplikację tak, aby użytkownicy mogli ją edytować nawet wtedy, gdy są w trybie offline.

## <a name="review-core-data"></a>Zapoznaj się z podstawowym modelem danych
W przypadku korzystania z magazynu danych podstawowych w trybie offline należy zdefiniować określone tabele i pola w modelu danych. Przykładowa aplikacja zawiera już model danych o odpowiednim formacie. W tej sekcji przeglądamy te tabele, aby zobaczyć, jak są używane.

Otwórz **QSDataModel. xcdatamodeld**. Zdefiniowano cztery tabele — trzy, które są używane przez zestaw SDK i które są używane dla samych elementów do wykonania:
  * MS_TableOperations: śledzi elementy, które muszą być synchronizowane z serwerem programu.
  * MS_TableOperationErrors: śledzi wszystkie błędy występujące podczas synchronizacji w trybie offline.
  * MS_TableConfig: śledzi czas ostatniej aktualizacji dla ostatniej operacji synchronizacji dla wszystkich operacji ściągania.
  * TodoItem: przechowuje elementy do wykonania. Kolumny systemowe **createdAt**, **updatedAt**i **Version** są opcjonalnymi właściwościami systemowymi.

> [!NOTE]
> Zestaw SDK Mobile Apps rezerwuje nazwy kolumn zaczynające się od " **``** ". Nie należy używać tego prefiksu dla elementów innych niż kolumny systemowe. W przeciwnym razie nazwy kolumn są modyfikowane w przypadku korzystania z zdalnego zaplecza.
>
>

W przypadku korzystania z funkcji synchronizacji w trybie offline Zdefiniuj trzy tabele systemowe i tabelę danych.

### <a name="system-tables"></a>Tabele systemowe

**MS_TableOperations**  

![Atrybuty tabeli MS_TableOperations][defining-core-data-tableoperations-entity]

| Atrybut | Typ |
| --- | --- |
| id | Integer 64 |
| Elementów | Ciąg |
| Właściwości | Binary Data |
| table | Ciąg |
| tableKind | Integer 16 |


**MS_TableOperationErrors**

 ![Atrybuty tabeli MS_TableOperationErrors][defining-core-data-tableoperationerrors-entity]

| Atrybut | Typ |
| --- | --- |
| id |Ciąg |
| operationId |Integer 64 |
| Właściwości |Binary Data |
| tableKind |Integer 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Atrybut | Typ |
| --- | --- |
| id |Ciąg |
| key |Ciąg |
| KeyType |Integer 64 |
| table |Ciąg |
| wartość |Ciąg |

### <a name="data-table"></a>Tabela danych

**TodoItem**

| Atrybut | Typ | Uwaga |
| --- | --- | --- |
| id | Ciąg, oznaczone jako wymagane |Klucz podstawowy w magazynie zdalnym |
| wykonanie | Wartość logiczna | Pole elementu do wykonania |
| tekst |Ciąg |Pole elementu do wykonania |
| createdAt | Date | obowiązkowe Mapowanie do właściwości systemowej **createdAt** |
| updatedAt | Date | obowiązkowe Mapowanie do właściwości systemowej **updatedAt** |
| version | Ciąg | obowiązkowe Służy do wykrywania konfliktów, mapowania do wersji |

## <a name="setup-sync"></a>Zmiana zachowania synchronizacji aplikacji
W tej sekcji należy zmodyfikować aplikację tak, aby nie była synchronizowana przy uruchamianiu aplikacji ani gdy wstawiasz i aktualizujesz elementy. Synchronizacja jest wykonywana tylko wtedy, gdy jest wykonywany przycisk gestu odświeżania.

**Cel-C**:

1. W **QSTodoListViewController. m**Zmień metodę **viewDidLoad** , aby usunąć wywołanie do `[self refresh]` na końcu metody. Teraz dane nie są synchronizowane z serwerem podczas uruchamiania aplikacji. Zamiast tego jest synchronizowany z zawartością magazynu lokalnego.
2. W **QSTodoService. m**zmodyfikuj definicję `addItem` tak, aby nie była synchronizowana po wstawieniu elementu. Usuń blok `self syncData` i zastąp go następującym:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Zmodyfikuj definicję `completeItem` jak wspomniano wcześniej. Usuń blok dla `self syncData` i zastąp go następującym:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Kod SWIFT**:

W `viewDidLoad`, w **ToDoTableViewController. Swift**, Dodaj komentarz do dwóch wierszy przedstawionych tutaj, aby zatrzymać synchronizację podczas uruchamiania aplikacji. Podczas tego pisania aplikacja SWIFT do zrobienia nie aktualizuje usługi, gdy ktoś doda lub ukończy element. Usługa aktualizuje usługę tylko przy uruchamianiu aplikacji.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testowanie aplikacji
W tej sekcji połączysz się z nieprawidłowym adresem URL w celu symulowania scenariusza w trybie offline. Po dodaniu elementów danych są one przechowywane w lokalnym podstawowym magazynie danych, ale nie są synchronizowane z zapleczem aplikacji mobilnej.

1. Zmień adres URL aplikacji mobilnej w **QSTodoService. m** na nieprawidłowy adres URL i ponownie uruchom aplikację:

   **Cel-C**. W QSTodoService. m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Kod SWIFT**. In ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Dodaj niektóre elementy do wykonania. Zamknij symulator (lub Wymuś zamknięcie aplikacji), a następnie uruchom go ponownie. Sprawdź, czy zmiany są zachowywane.

3. Wyświetl zawartość zdalnej tabeli **TodoItem** :
   * W przypadku zaplecza Node. js przejdź do [Azure Portal](https://portal.azure.com/) i w zapleczu aplikacji mobilnej kliknij pozycję **łatwe tabele** > **TodoItem**.  
   * W przypadku zaplecza platformy .NET Użyj narzędzia SQL, takiego jak SQL Server Management Studio, lub klienta REST, takiego jak programu Fiddler lub Poster.  

4. Sprawdź, czy nowe elementy *nie* zostały zsynchronizowane z serwerem.

5. Zmień adres URL z powrotem na poprawny w **QSTodoService. m**i ponownie uruchom aplikację.

6. Wykonaj gest odświeżenia, pobierając listę elementów.  
Zostanie wyświetlony pokrętło postępu.

7. Ponownie Wyświetl dane **TodoItem** . Nowe i zmienione elementy do wykonania powinny teraz być wyświetlane.

## <a name="summary"></a>Podsumowanie
W celu obsługi funkcji synchronizacji w trybie offline użyto interfejsu `MSSyncTable` i zainicjowany `MSClient.syncContext` z magazynem lokalnym. W takim przypadku lokalnym magazynem jest podstawowa baza danych oparta na danych.

W przypadku korzystania z podstawowego magazynu danych lokalnych, należy zdefiniować kilka tabel z [poprawnymi właściwościami systemu](#review-core-data).

Normalne operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla aplikacji mobilnych działają tak, jakby aplikacja była nadal połączona, ale wszystkie operacje są wykonywane w odniesieniu do lokalnego magazynu.

Po zsynchronizowaniu lokalnego magazynu z serwerem użyto metody **MSSyncTable. pullWithQuery** .

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Synchronizacja danych w trybie offline w Mobile Apps]
* [Pokrycie w chmurze: synchronizacja w trybie offline na platformie Azure Mobile Services] \(film wideo ma Mobile Services, ale Mobile Apps synchronizacja w trybie offline działa w podobny sposób.\)

<!-- URLs. -->


[Tworzenie aplikacji dla systemu iOS]: app-service-mobile-ios-get-started.md
[Synchronizacja danych w trybie offline w Mobile Apps]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Pokrycie w chmurze: synchronizacja w trybie offline na platformie Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
