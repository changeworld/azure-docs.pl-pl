---
title: Włączanie synchronizacji w trybie offline za pomocą aplikacji mobilnych systemu iOS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać aplikacji mobilnych w usłudze Azure App Service do pamięci podręcznej i synchronizacji danych w trybie offline w aplikacjach systemu iOS.
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
ms.openlocfilehash: f7ae3e7a33ae7df70214ed171b00cc2accbaccb5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446370"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Włączanie synchronizacji w trybie offline za pomocą aplikacji mobilnych systemu iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center jest inwestujemy w nowe i zintegrowane usługi decydujące znaczenie dla aplikacji mobilnych. Deweloperzy mogą używać **kompilacji**, **testu** i **dystrybucji** usług do konfigurowania potoku ciągłej integracji i ciągłego dostarczania. Gdy aplikacja jest wdrażana, deweloperzy mogą monitorować stan i użycie ich przy użyciu aplikacji **Analytics** i **diagnostyki** usług i angażuj użytkowników za pomocą **wypychania** Usługa. Deweloperzy mogą również wykorzystać **uwierzytelniania** do uwierzytelniania użytkowników i **danych** usługę, aby utrwalić i synchronizowanie danych aplikacji w chmurze. Zapoznaj się z [platformy App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started-offline-data) już dziś.
>

## <a name="overview"></a>Przegląd
W tym samouczku opisano, synchronizacji w trybie offline przy użyciu funkcji Mobile Apps w usłudze Azure App Service dla systemu iOS. Za pomocą synchronizacji w trybie offline w użytkownicy końcowi mogą wchodzić w interakcje z aplikacją mobilną, aby wyświetlić, dodać ani modyfikować dane nawet wtedy, gdy mają one braku połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie jest w trybie online, zmiany są synchronizowane za pomocą zdalnego zaplecza.

Jeśli jest to Twój pierwszy kontakt z usługą Mobile Apps, najpierw należy Ukończ samouczek [tworzenie aplikacji systemu iOS]. Jeśli nie używasz projektu pobrany serwer szybki start, należy dodać pakiety rozszerzeń dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzeń serwera, zobacz [pracy z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz [Synchronizacja danych offline w aplikacjach mobilnych].

## <a name="review-sync"></a>Przejrzyj kod klienta synchronizacji
Projekt klienta, który został pobrany dla [tworzenie aplikacji systemu iOS] samouczek już zawiera kod, który obsługuje synchronizację w trybie offline przy użyciu lokalnej bazy danych na podstawie danych podstawowych. Ta sekcja zawiera podsumowanie, co jest już uwzględniony w kodu z samouczka dotyczącego. Aby uzyskać omówienie pojęć dotyczących tej funkcji, zobacz [Synchronizacja danych offline w aplikacjach mobilnych].

Za pomocą funkcji synchronizacji danych w trybie offline funkcji Mobile Apps, użytkownicy końcowi mogą wchodzić w interakcje z lokalnej bazy danych nawet wtedy, gdy sieć jest niedostępna. Korzystanie z tych funkcji w aplikacji należy zainicjować kontekstu synchronizacji `MSClient` i odwoływać się do magazynu lokalnego. Następnie możesz odwoływać się do tabeli za pomocą **MSSyncTable** interfejsu.

W **QSTodoService.m** (Objective-C) lub **ToDoTableViewController.swift** (Swift), należy zauważyć, że typ elementu członkowskiego **syncTable** jest **MSSyncTable** . Synchronizacja offline używa tego interfejsu table synchronizacji zamiast **MSTable**. W przypadku tabeli synchronizacji wszystkich operacji przejdź do magazynu lokalnego i zostaną zsynchronizowane tylko ze zdalnym zaplecza za pomocą jawnego operacje wypychania i ściągania.

 Aby uzyskać odwołanie do tabeli synchronizacji, użyj **syncTableWithName** metody `MSClient`. Aby usunąć funkcje synchronizacji w trybie offline, użyj **tableWithName** zamiast tego.

Można było wykonać żadnych operacji tabeli, musi zostać zainicjowany Magazyn lokalny. W tym miejscu jest odpowiedni kod:

* **Języka Objective-C**. W **QSTodoService.init** metody:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Kod SWIFT**. W **ToDoTableViewController.viewDidLoad** metody:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Ta metoda tworzy magazynu lokalnego przy użyciu `MSCoreDataStore` interfejs, który zawiera zestaw SDK usługi Mobile Apps. Alternatywnie możesz podać inny magazyn lokalny poprzez implementację `MSSyncContextDataSource` protokołu. Ponadto pierwszy parametr **MSSyncContext** służy do określania obsługi konfliktów. Ponieważ firma Microsoft przeszły `nil`, uzyskujemy obsługi konfliktów domyślny, który kończy się niepowodzeniem w przypadku dowolnego konfliktu.

Teraz możemy wykonania operacji synchronizacji rzeczywiste i pobierania danych z zdalnego zaplecza:

* **Języka Objective-C**. `syncData` najpierw wypchnięcia nowych zmian, a następnie wywołuje **pullData** pobierać dane z zdalnego zaplecza. Z kolei **pullData** metoda pobiera nowe dane, który odpowiada na kwerendę:

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

W wersji języka Objective-C w `syncData`, pierwsze wywołanie **pushWithCompletion** kontekstu synchronizacji. Ta metoda jest elementem członkowskim `MSSyncContext` (i nie tabela synchronizacji, sam), ponieważ umieszcza ono zmiany we wszystkich tabel. Tylko te rekordy, które zostały zmodyfikowane w jakiś sposób lokalnie (za pośrednictwem operacje CUD) są wysyłane do serwera. Następnie Pomocnika **pullData** nosi nazwę, która wywołuje metodę **MSSyncTable.pullWithQuery** pobierania zdalnych danych i zapisać ją w lokalnej bazie danych.

W wersji Swift operacji wypychania nie jest bezwzględnie konieczne, nie istnieje żadne wywołania **pushWithCompletion**. W przypadku zmiany oczekujące w kontekście synchronizacji dla tabeli, który wykonuje operację wypychania, ściągnięcia zawsze wysyła powiadomienie wypychane najpierw. Jeśli masz więcej niż jedną tabelą synchronizacji, najlepiej jawnie wywołać wypychania, aby upewnić się, że wszystko jest spójny w ramach powiązanych tabel.

W języku Objective-C i Swift wersji, można użyć **pullWithQuery** metodę, aby określić zapytania do filtrowania rekordów do pobrania. W tym przykładzie zapytanie pobiera wszystkie rekordy w zdalnym `TodoItem` tabeli.

Drugi parametr **pullWithQuery** jest identyfikator zapytania, który służy do *synchronizacja przyrostowa*. Synchronizacja przyrostowa pobiera tylko te rekordy, które zostały zmodyfikowane od ostatniej synchronizacji, przy użyciu rekordu `UpdatedAt` sygnatura czasowa (o nazwie `updatedAt` w magazynie lokalnym.) Identyfikator zapytania powinien być opisowy ciąg, który jest unikatowy dla każdego zapytania logiczne w aplikacji. Aby zrezygnować z synchronizacja przyrostowa, należy przekazać `nil` jako identyfikator zapytania. Takie podejście może być potencjalnie nieefektywne, ponieważ pobiera wszystkie rekordy na każdej operacji ściągania.

Aplikacja języka Objective-C synchronizuje podczas modyfikowania lub dodawanie danych, gdy użytkownik wykona gestu odświeżania, a w momencie uruchomienia.

Swift aplikacji synchronizuje, gdy użytkownik wykonuje gestu odświeżania, a w momencie uruchomienia.

Ponieważ synchronizacje aplikacji zawsze wtedy, gdy dane zmodyfikowany (Objective-C) lub po każdym uruchomieniu aplikacji (Objective-C i Swift), aplikacja zakłada, użytkownik jest w trybie online. W dalszej części tego tematu będzie zaktualizować aplikację, dzięki czemu użytkownicy mogą edytować nawet wtedy, gdy są w trybie offline.

## <a name="review-core-data"></a>Zapoznaj się z modelem danych podstawowych
Korzystając z magazynu offline danych podstawowych, należy zdefiniować konkretne tabele i pola w modelu danych. Przykładowa aplikacja już zawiera model danych z odpowiednim formacie. W tej sekcji, w jaki sposób za pomocą tych tabel, aby pokazać, jak są one używane.

Otwórz **QSDataModel.xcdatamodeld**. Cztery tabele są zdefiniowane — elementy na samych trzech, które są używane przez zestaw SDK i taki, który jest używany do zadania do wykonania:
  * MS_TableOperations: Śledzenie elementów, które muszą zostać zsynchronizowane z serwerem.
  * MS_TableOperationErrors: Śledzi wszystkie błędy, które występują podczas synchronizacji w trybie offline.
  * MS_TableConfig: Śledzi ostatniej aktualizacji podczas ostatniej operacji synchronizacji dla wszystkich operacji ściągania.
  * Czynność do wykonania: Przechowuje elementy zadań do wykonania. Kolumny systemowe **createdAt**, **updatedAt**, i **wersji** są właściwości opcjonalne systemu.

> [!NOTE]
> Zestaw SDK usługi Mobile Apps rezerwuje nazwy kolumn, które zaczynają się od " **``** ". Nie należy używać tego prefiksu z coś innego niż system kolumn. W przeciwnym razie nazwy kolumn są modyfikowane, gdy używasz zdalnego zaplecza.
>
>

Gdy używasz funkcji synchronizacji w trybie offline, należy zdefiniować tabele systemowe trzy i tabelę danych.

### <a name="system-tables"></a>Tabele systemowe

**MS_TableOperations**  

![Atrybuty tabeli MS_TableOperations][defining-core-data-tableoperations-entity]

| Atrybut | Typ |
| --- | --- |
| id | Liczba całkowita 64 |
| itemId | String |
| properties | Dane binarne |
| table | String |
| tableKind | Liczba całkowita 16 |


**MS_TableOperationErrors**

 ![Atrybuty tabeli MS_TableOperationErrors][defining-core-data-tableoperationerrors-entity]

| Atrybut | Typ |
| --- | --- |
| id |String |
| operationId |Liczba całkowita 64 |
| properties |Dane binarne |
| tableKind |Liczba całkowita 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Atrybut | Typ |
| --- | --- |
| id |String |
| key |String |
| keyType |Liczba całkowita 64 |
| table |String |
| value |String |

### <a name="data-table"></a>Tabela danych

**TodoItem**

| Atrybut | Typ | Uwaga |
| --- | --- | --- |
| id | Ciąg, oznaczone jako wymagane |klucz podstawowy w magazynie zdalnym |
| Wykonaj | Boolean | Pole elementu do wykonania |
| tekst |String |Pole elementu do wykonania |
| createdAt | Date | (opcjonalnie) Mapuje **createdAt** właściwości systemu |
| updatedAt | Date | (opcjonalnie) Mapuje **updatedAt** właściwości systemu |
| version | String | (opcjonalnie) Używane do wykrywania konfliktów, mapy do wersji |

## <a name="setup-sync"></a>Zmienianie działania synchronizacji aplikacji
W tej sekcji zmodyfikujesz aplikację tak, aby nie synchronizuje się na uruchomienie aplikacji lub podczas wstawiania i aktualizowania elementów. Synchronizuje tylko wtedy, gdy przycisk Odśwież gestu jest wykonywane.

**Objective-C**:

1. W **QSTodoListViewController.m**, zmień **viewDidLoad** metodę, aby usunąć wywołanie `[self refresh]` na końcu metody. Teraz dane nie jest synchronizowany z serwerem na uruchomienie aplikacji. Zamiast tego jest synchronizowany z zawartością magazynu lokalnego.
2. W **QSTodoService.m**, zmodyfikować definicję `addItem` tak, aby nie synchronizować, po wstawieniu elementu. Usuń `self syncData` blokowania i zastąp go następującym kodem:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Modyfikowanie definicji `completeItem` jak wspomniano wcześniej. Usuń blok dla `self syncData` i zastąp go następującym kodem:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Kod SWIFT**:

W `viewDidLoad`w **ToDoTableViewController.swift**, komentarz dwa wiersze, pokazano poniżej, aby zatrzymać synchronizację w aplikacji w menu start. W momencie pisania tego dokumentu aplikacja języka Swift zadań do wykonania nie powoduje aktualizacji usługi, gdy ktoś doda lub zakończeniu elementu. Aktualizuje usługę tylko na uruchomienie aplikacji.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testowanie aplikacji
W tej sekcji możesz nawiązać nieprawidłowy adres URL, aby zasymulować scenariusz w trybie offline. Podczas dodawania elementów danych są przechowywane w przechowywania danych podstawowych lokalnej, ale nie są one zsynchronizowane z zapleczem aplikacji mobilnej.

1. Zmień adres URL aplikacji mobilnej w **QSTodoService.m** nieprawidłowy adres URL i ponownie uruchom aplikację:

   **Języka Objective-C**. In QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Kod SWIFT**. In ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Dodaj kilka elementów do wykonania. Zamknij symulator (lub wymuszanie zamknięcia aplikacji), a następnie uruchom go ponownie. Upewnij się, że zmiany zostaną zachowane.

3. Wyświetlanie zawartości zdalnego **TodoItem** tabeli:
   * Dla środowiska Node.js zaplecza, przejdź do [witryny Azure portal](https://portal.azure.com/) i zaplecza aplikacji mobilnej, kliknij **łatwych tabel** > **TodoItem**.  
   * Dla platformy .NET ponownie zakończyć, za pomocą narzędzia SQL, takiego jak SQL Server Management Studio, lub klienta REST, takich jak narzędzia Fiddler lub Postman.  

4. Sprawdź, czy nowe elementy *nie* zostały zsynchronizowane z serwerem.

5. Zmień adres URL do właściwą **QSTodoService.m**, a następnie ponownie uruchom aplikację.

6. Wykonaj gest odświeżania, przenosząc je w dół na liście elementów.  
Pokrętło postępu jest wyświetlana.

7. Widok **TodoItem** danych ponownie. Nowe i zmienione funkcje wykonania powinno zostać wyświetlone.

## <a name="summary"></a>Podsumowanie
Aby zapewnić obsługę funkcji synchronizacji w trybie offline, użyliśmy `MSSyncTable` interfejs i zainicjować `MSClient.syncContext` z magazynu lokalnego. W takim przypadku Magazyn lokalny został bazy danych opartej na danych podstawowych.

Korzystając z lokalnego magazynu danych podstawowych, należy zdefiniować kilka tabel z [poprawić właściwości systemu](#review-core-data).

Normalne tworzenie, odczytywanie, aktualizowanie i usuwanie operacji (CRUD) dla aplikacji mobilnych pracy w przypadku, tak, jakby aplikacji jest nadal połączony, ale wszystkie operacje są wykonywane względem magazynu lokalnego.

Magazyn lokalny firma Microsoft synchronizowane z serwerem, użyliśmy **MSSyncTable.pullWithQuery** metody.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Synchronizacja danych offline w aplikacjach mobilnych]
* [Cloud Cover: Synchronizacja offline w usługach Azure Mobile Services] \(wideo dotyczy usług Mobile Services, ale w trybie offline funkcji Mobile Apps synchronizowanie działa w podobny sposób.\)

<!-- URLs. -->


[Tworzenie aplikacji systemu iOS]: app-service-mobile-ios-get-started.md
[Synchronizacja danych offline w aplikacjach mobilnych]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Synchronizacja offline w usługach Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
