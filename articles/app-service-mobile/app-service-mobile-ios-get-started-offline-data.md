---
title: Włączanie synchronizacji w trybie offline (iOS)
description: Dowiedz się, jak używać aplikacji mobilnych usługi Azure App Service do buforowania i synchronizowania danych w trybie offline w aplikacjach systemu iOS.
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: d943213814b999f101a541abb0195a9fdd5a7423
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459178"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Włączanie synchronizacji w trybie offline z aplikacjami mobilnymi systemu iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Omówienie
W tym samouczku opisano synchronizację w trybie offline z funkcją aplikacji mobilnych usługi Azure App Service dla systemu iOS. Dzięki synchronizacji w trybie offline użytkownicy końcowi mogą wchodzić w interakcje z aplikacją mobilną, aby wyświetlać, dodawać lub modyfikować dane, nawet jeśli nie mają połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Po powrocie urządzenia do trybu online zmiany są synchronizowane ze zdalnym zapleczem.

Jeśli jest to twoje pierwsze doświadczenie z aplikacjami mobilnymi, należy najpierw ukończyć samouczek [Tworzenie aplikacji na iOS]. Jeśli nie używasz pobranego projektu serwera szybkiego startu, należy dodać pakiety rozszerzeń dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [Praca z zestawem SDK serwera wewnętrznej bazy danych .NET dla aplikacji azure mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej o funkcji synchronizacji w trybie offline, zobacz [Synchronizacja danych w trybie offline w aplikacjach mobilnych].

## <a name="review-the-client-sync-code"></a><a name="review-sync"></a>Przejrzyj kod synchronizacji klienta
Projekt klienta pobrany dla [samouczka Tworzenie aplikacji] dla systemu iOS zawiera już kod obsługujący synchronizację w trybie offline przy użyciu lokalnej bazy danych opartej na danych podstawowych. W tej sekcji podsumowano, co jest już zawarte w kodzie samouczka. Aby zapoznać się z koncepcyjnym omówieniem tej funkcji, zobacz [Synchronizacja danych w trybie offline w aplikacjach mobilnych].

Korzystając z funkcji synchronizacji danych w trybie offline aplikacji mobilnych, użytkownicy końcowi mogą wchodzić w interakcje z lokalną bazą danych, nawet jeśli sieć jest niedostępna. Aby korzystać z tych funkcji w aplikacji, `MSClient` należy zainicjować kontekst synchronizacji i odwoływać się do sklepu lokalnego. Następnie odwołać się do tabeli za pośrednictwem interfejsu **MSSyncTable.**

W **pliku QSTodoService.m** (Objective-C) lub **ToDoTableViewController.swift** (Swift) należy zauważyć, że typem **elementu członkowskiego syncTable** jest **tabela MSSyncTable**. Synchronizacja w trybie offline używa tego interfejsu tabeli synchronizacji zamiast **mstable**. Gdy używana jest tabela synchronizacji, wszystkie operacje przejść do magazynu lokalnego i są synchronizowane tylko ze zdalnym zapleczu z jawnych operacji wypychania i ściągania.

 Aby uzyskać odwołanie do tabeli synchronizacji, użyj metody `MSClient` **syncTableWithName** w programie . Aby usunąć funkcje synchronizacji w trybie offline, użyj **tableWithName** zamiast tego.

Przed wykonaniem operacji tabeli należy zainicjować magazyn lokalny. Oto odpowiedni kod:

* **Objective-C**. W **metodzie QSTodoService.init:**

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Szybki**. W **ToDoTableViewController.viewDidLoad** metody:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Ta metoda tworzy magazyn lokalny `MSCoreDataStore` przy użyciu interfejsu, który zapewnia SDK aplikacji mobilnych. Alternatywnie można podać inny magazyn lokalny, `MSSyncContextDataSource` implementując protokół. Ponadto pierwszy parametr **MSSyncContext** jest używany do określenia obsługi konfliktów. Ponieważ przeszliśmy, `nil`otrzymujemy domyślny program obsługi konfliktów, który kończy się niepowodzeniem w każdym konflikcie.

Teraz wykonajmy rzeczywistą operację synchronizacji i pobierzmy dane ze zdalnego zaplecza:

* **Objective-C**. `syncData`najpierw wypycha nowe zmiany, a następnie wywołuje **pullData,** aby uzyskać dane ze zdalnego zaplecza. Z kolei **pullData** metoda pobiera nowe dane, które pasuje do kwerendy:

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
* **Szybki**:
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

W wersji Objective-C `syncData`w programie , najpierw nazywamy **pushWithCompletion** w kontekście synchronizacji. Ta metoda jest `MSSyncContext` członkiem (a nie samej tabeli synchronizacji), ponieważ wypycha zmiany we wszystkich tabelach. Tylko rekordy, które zostały zmodyfikowane w jakiś sposób lokalnie (za pośrednictwem operacji CUD) są wysyłane do serwera. Następnie wywoływane jest **pullData** pomocnika, który wywołuje **mssynctable.pullWithQuery** do pobierania danych zdalnych i przechowywania ich w lokalnej bazie danych.

W wersji Swift, ponieważ operacja push nie była absolutnie konieczna, nie ma połączenia **pushWithCompletion**. Jeśli w kontekście synchronizacji dla tabeli, która wykonuje operację wypychania, zawładnienie jest zawsze powodem do uzyskania wypychania, należy wykonać jakiekolwiek zmiany w kontekście synchronizacji. Jednak jeśli masz więcej niż jedną tabelę synchronizacji, najlepiej jest jawnie wywołać wypychanie, aby upewnić się, że wszystko jest spójne w powiązanych tabelach.

W wersjach Objective-C i Swift można użyć **metody pullWithQuery,** aby określić kwerendę do filtrowania rekordów, które mają zostać pobrane. W tym przykładzie kwerenda pobiera wszystkie `TodoItem` rekordy w tabeli zdalnej.

Drugi parametr **pullWithQuery** jest identyfikatorem zapytania, który jest używany do *synchronizacji przyrostowej*. Synchronizacja przyrostowa pobiera tylko rekordy, które zostały zmodyfikowane od ostatniej `UpdatedAt` synchronizacji, `updatedAt` przy użyciu sygnatury czasowej rekordu (wywoływanej w magazynie lokalnym). Identyfikator kwerendy powinien być ciągiem opisowym, który jest unikatowy dla każdej kwerendy logicznej w aplikacji. Aby zrezygnować z synchronizacji przyrostowej, przekaż `nil` jako identyfikator kwerendy. Takie podejście może być potencjalnie nieefektywne, ponieważ pobiera wszystkie rekordy dla każdej operacji ściągania.

Aplikacja Objective-C synchronizuje się podczas modyfikowania lub dodawania danych, gdy użytkownik wykonuje gest odświeżania i po uruchomieniu.

Aplikacja Swift synchronizuje się, gdy użytkownik wykonuje gest odświeżania i uruchamia.

Ponieważ aplikacja synchronizuje się za każdym razem, gdy dane są modyfikowane (Objective-C) lub przy każdym uruchomieniu aplikacji (Objective-C i Swift), aplikacja zakłada, że użytkownik jest w trybie online. W dalszej części zaktualizujesz aplikację, aby użytkownicy mogli edytować nawet wtedy, gdy są w trybie offline.

## <a name="review-the-core-data-model"></a><a name="review-core-data"></a>Przejrzyj model podstawowych danych
Korzystając z magazynu Core Data w trybie offline, należy zdefiniować określone tabele i pola w modelu danych. Przykładowa aplikacja zawiera już model danych o odpowiednim formacie. W tej sekcji przechodzimy przez te tabele, aby pokazać, jak są używane.

Otwórz **QSDataModel.xcdatamodeld**. Zdefiniowane są cztery tabele — trzy, które są używane przez SDK i jedna używana dla samych elementów do wykonania:
  * MS_TableOperations: Śledzi elementy, które muszą być zsynchronizowane z serwerem.
  * MS_TableOperationErrors: śledzi wszelkie błędy, które zdarzają się podczas synchronizacji w trybie offline.
  * MS_TableConfig: Śledzi ostatni zaktualizowany czas dla ostatniej operacji synchronizacji dla wszystkich operacji ściągania.
  * TodoItem: Przechowuje elementy do wykonania. Kolumny systemowe **createdAt**, **updatedAt**i **version** są opcjonalnymi właściwościami systemu.

> [!NOTE]
> SDK aplikacji mobilnych zastrzega nazwy kolumn, które zaczynają się od "**``**". Nie należy używać tego prefiksu z innymi niż kolumny systemowe. W przeciwnym razie nazwy kolumn są modyfikowane podczas korzystania ze zdalnego zaplecza.
>
>

Podczas korzystania z funkcji synchronizacji w trybie offline należy zdefiniować trzy tabele systemowe i tabelę danych.

### <a name="system-tables"></a>Tabele systemowe

**MS_TableOperations**  

![atrybuty tabeli MS_TableOperations][defining-core-data-tableoperations-entity]

| Atrybut | Typ |
| --- | --- |
| id | 10 000 000 000 000 |
| Itemid | Ciąg |
| properties | Dane binarne |
| tabela | Ciąg |
| tableKind | 16 całkowitej liczby całkowitej |


**MS_TableOperationErrors**

 ![atrybuty tabeli MS_TableOperationErrors][defining-core-data-tableoperationerrors-entity]

| Atrybut | Typ |
| --- | --- |
| id |Ciąg |
| operationId |10 000 000 000 000 |
| properties |Dane binarne |
| tableKind |16 całkowitej liczby całkowitej |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Atrybut | Typ |
| --- | --- |
| id |Ciąg |
| key |Ciąg |
| Keytype |10 000 000 000 000 |
| tabela |Ciąg |
| value |Ciąg |

### <a name="data-table"></a>Tabela danych

**TodoItem (TodoItem)**

| Atrybut | Typ | Uwaga |
| --- | --- | --- |
| id | Wymagany ciąg znaków |Klucz podstawowy w magazynie zdalnym |
| kończenie | Wartość logiczna | Pole Zapas do wykonania |
| tekst |Ciąg |Pole Zapas do wykonania |
| utworzonyW | Data | (opcjonalnie) Mapy do **utworzeniaW** właściwość systemu |
| aktualizacjaW | Data | (opcjonalnie) Mapy do **updatedAt** właściwość systemu |
| version | Ciąg | (opcjonalnie) Służy do wykrywania konfliktów, mapy do wersji |

## <a name="change-the-sync-behavior-of-the-app"></a><a name="setup-sync"></a>Zmienianie zachowania synchronizacji aplikacji
W tej sekcji zmodyfikujesz aplikację tak, aby nie była synchronizowana podczas uruchamiania aplikacji lub podczas wstawiania i aktualizowania elementów. Synchronizuje się tylko wtedy, gdy przycisk gestu odświeżania jest wykonywany.

**Cel C**:

1. W **QSTodoListViewController.m**, zmień **viewDidLoad** metody, `[self refresh]` aby usunąć wywołanie na końcu metody. Teraz dane nie są synchronizowane z serwerem w aplikacji start. Zamiast tego jest synchronizowany z zawartością magazynu lokalnego.
2. W **QSTodoService.m**, zmodyfikuj `addItem` definicję tak, aby nie synchronizować po wstawieniu elementu. Zdejmij `self syncData` blok i wymień go na następujący:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Zmodyfikuj `completeItem` definicję, jak wspomniano wcześniej. Zdejmij blok `self syncData` i wymień go na następujący:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Szybki**:

W `viewDidLoad`, w **ToDoTableViewController.swift**, skomentować dwa wiersze pokazane tutaj, aby zatrzymać synchronizację na początku aplikacji. W momencie pisania tego tekstu aplikacja Swift Todo nie aktualizuje usługi, gdy ktoś doda lub ukończy element. Aktualizuje usługę tylko przy uruchomieniu aplikacji.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-the-app"></a><a name="test-app"></a>Testowanie aplikacji
W tej sekcji można połączyć się z nieprawidłowym adresem URL, aby symulować scenariusz w trybie offline. Gdy dodasz elementy danych, są one przechowywane w lokalnym magazynie danych podstawowych, ale nie są synchronizowane z zapleczem aplikacji mobilnej.

1. Zmień adres URL aplikacji mobilnej w **QSTodoService.m** na nieprawidłowy adres URL i uruchom aplikację ponownie:

   **Objective-C**. W QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Szybki**. W todotableviewcontroller.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Dodaj kilka rzeczy do wykonania. Zamknij symulator (lub przymusowo zamknij aplikację), a następnie uruchom go ponownie. Sprawdź, czy zmiany są kontynuowane.

3. Wyświetl zawartość zdalnej tabeli **TodoItem:**
   * W przypadku zaplecza Node.js przejdź do [witryny Azure portal,](https://portal.azure.com/) a w aplikacji mobilnej kliknij przycisk **Łatwe tabele** > **TodoItem**.  
   * W przypadku zaplecza .NET użyj narzędzia SQL, takiego jak SQL Server Management Studio, lub klienta REST, takiego jak Fiddler lub Postman.  

4. Sprawdź, czy nowe elementy *nie* zostały zsynchronizowane z serwerem.

5. Zmień adres URL z powrotem na poprawny w **QSTodoService.m**i uruchom ponownie aplikację.

6. Wykonaj gest odświeżania, ściągając w dół listę elementów.  
Zostanie wyświetlony pokrętło postępu.

7. Ponownie wyświetl dane **TodoItem.** Nowe i zmienione elementy do wykonania powinny być teraz wyświetlane.

## <a name="summary"></a>Podsumowanie
Aby obsługiwać funkcję synchronizacji `MSSyncTable` w trybie `MSClient.syncContext` offline, użyliśmy interfejsu i zainicjowaliśmy w sklepie lokalnym. W takim przypadku magazyn lokalny był podstawową bazą danych opartą na danych.

Korzystając z lokalnego magazynu Core Data, należy zdefiniować kilka tabel z [prawidłowymi właściwościami systemu](#review-core-data).

Normalne operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dla aplikacji mobilnych działają tak, jakby aplikacja była nadal połączona, ale wszystkie operacje występują w sklepie lokalnym.

Podczas synchronizacji magazynu lokalnego z serwerem użyliśmy metody **MSSyncTable.pullWithQuery.**

## <a name="additional-resources"></a>Zasoby dodatkowe
* [Synchronizacja danych w trybie offline w aplikacjach mobilnych]
* [Zachmurzenie: Synchronizacja w trybie offline w usługach] \(mobilnych platformy Azure Film wideo dotyczy usług mobilnych, ale synchronizacja aplikacji mobilnych w trybie offline działa w podobny sposób.\)

<!-- URLs. -->


[Tworzenie aplikacji na iOS]: app-service-mobile-ios-get-started.md
[Synchronizacja danych w trybie offline w aplikacjach mobilnych]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Zachmurzenie: synchronizacja w trybie offline w usługach azure dla urządzeń przenośnych]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
