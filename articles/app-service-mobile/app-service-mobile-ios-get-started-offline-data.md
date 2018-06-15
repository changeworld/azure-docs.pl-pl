---
title: Włącz synchronizację w trybie offline z aplikacji mobilnych systemu iOS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać aplikacji mobilnych w usłudze Azure App Service na potrzeby pamięci podręcznej i synchronizacji danych w trybie offline w aplikacjach systemu iOS.
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 2f415f1886c654f3bdd880cdccaadc7aa3e69892
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32156499"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Włącz synchronizację w trybie offline z aplikacji mobilnych systemu iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Przegląd
Ten samouczek obejmuje synchronizacji w trybie offline z funkcji Mobile Apps w usłudze Azure App Service dla systemu iOS. Z synchronizacji w trybie offline w użytkownicy końcowi mogą współdziałać z aplikacji mobilnej, aby wyświetlić, dodawanie lub modyfikowanie danych, nawet w przypadku, gdy mają oni połączenia sieciowego. Zmiany są przechowywane w lokalnej bazie danych. Gdy urządzenie jest w trybie online, zmiany są synchronizowane z zdalnego zaplecza.

Jeśli jest to usprawnić pierwszy Mobile Apps, należy najpierw Ukończ samouczek [tworzenie aplikacji systemu iOS]. Jeśli projekt pobrany szybki start serwera nie jest używany, należy dodać pakietów rozszerzenia dostępu do danych do projektu. Aby uzyskać więcej informacji na temat pakietów rozszerzenia serwera, zobacz [pracować z serwera wewnętrznej bazy danych .NET SDK usługi Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Aby dowiedzieć się więcej na temat funkcji synchronizacji w trybie offline, zobacz [synchronizacji danych w trybie Offline w aplikacjach mobilnych].

## <a name="review-sync"></a>Przegląd kodu klienta synchronizacji
Projekt klienta, który został pobrany dla [tworzenie aplikacji systemu iOS] samouczek już zawiera kod, który obsługuje synchronizacji w trybie offline przy użyciu lokalnej bazy danych na podstawie danych podstawowych. Ta sekcja zawiera podsumowanie, co jest już uwzględniony w kodzie samouczka. Omówienie koncepcji funkcji, zobacz [synchronizacji danych w trybie Offline w aplikacjach mobilnych].

Za pomocą funkcji synchronizacji danych w trybie offline Mobile Apps, użytkownicy końcowi mogą współdziałać z lokalnej bazy danych nawet wtedy, gdy sieć jest niedostępna. Aby używać tych funkcji w aplikacji, zainicjować kontekstu synchronizacji z `MSClient` i referencyjne Magazyn lokalny. Odwołania do tabeli, a następnie **MSSyncTable** interfejsu.

W **QSTodoService.m** (Objective-C) lub **ToDoTableViewController.swift** (Swift), zwróć uwagę, że typ elementu członkowskiego **syncTable** jest **MSSyncTable** . Synchronizacja w trybie offline używa tego interfejsu tabeli synchronizacji zamiast **MSTable**. W przypadku tabeli synchronizacji wszystkie operacje przejdź do lokalnego magazynu i są synchronizowane tylko w przypadku zdalnego wewnętrznej z jawnym operacje wypychania i ściągania.

 Aby pobrać odwołanie do tabeli synchronizacji, użyj **syncTableWithName** metoda `MSClient`. Aby usunąć funkcji synchronizacji w trybie offline, użyj **tableWithName** zamiast tego.

Aby można było wykonać wszystkie operacje tabeli, muszą zostać zainicjowane magazynu lokalnego. W tym miejscu jest odpowiedni kod:

* **Objective-C**. W **QSTodoService.init** metody:

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
   Ta metoda tworzy magazynu lokalnego przy użyciu `MSCoreDataStore` interfejs, który zawiera zestaw SDK usługi Mobile Apps. Alternatywnie można udostępnić inny magazyn lokalny zaimplementowanie `MSSyncContextDataSource` protokołu. Ponadto pierwszy parametr **MSSyncContext** służy do określania obsługi konfliktu. Ponieważ firma Microsoft przekazanego `nil`, uzyskujemy domyślne obsługi konfliktu, który zakończy się niepowodzeniem w przypadku dowolnego konfliktu.

Teraz załóżmy operacji synchronizacji rzeczywiste i Pobierz dane z zdalnego zaplecza:

* **Objective-C**. `syncData` najpierw wypchnięcia nowych zmian, a następnie wywołuje **pullData** można pobrać danych z zdalnego zaplecza. Z kolei **pullData** — metoda pobiera nowe dane, odpowiadający kwerendy:

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
              // server conflicts, etc via the MSSyncContextDelegate
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

W wersji języka Objective-C w `syncData`, pierwsze wywołanie **pushWithCompletion** w kontekście synchronizacji. Ta metoda jest elementem członkowskim `MSSyncContext` (i nie w tabeli synchronizacji, sam), ponieważ wypycha dane zmiany we wszystkich tabel. Tylko te rekordy, które zostały zmodyfikowane w sposób lokalnie (za pośrednictwem operacji CUD) są wysyłane do serwera. Następnie Pomocnika **pullData** zostanie wywołany, które wywołuje **MSSyncTable.pullWithQuery** pobierania zdalnych danych i zapisz go w lokalnej bazie danych.

W wersji Swift operacji push nie jest bezwzględnie konieczne, nie istnieje żadne wywołanie **pushWithCompletion**. Jeśli istnieją oczekujące zmiany w kontekście synchronizacji dla tabeli, która wykonuje operację push, ściągania zawsze wystawia wypychanej najpierw. Jeśli masz więcej niż jednej tabeli synchronizacji, najlepiej jawnie wywołać wypychania upewnij się, że wszystko jest spójne w tabelach pokrewnych.

W języku Objective C i wersje Swift, można użyć **pullWithQuery** metodę, aby określić kwerendy do filtrowania rekordów do pobrania. W tym przykładzie zapytanie pobiera wszystkie rekordy w obiekcie zdalnym `TodoItem` tabeli.

Drugi parametr funkcji **pullWithQuery** jest identyfikator zapytania, który służy do *synchronizacja przyrostowa*. Synchronizacja przyrostowa pobiera tylko te rekordy, które zostały zmodyfikowane od ostatniej synchronizacji, przy użyciu rekordu `UpdatedAt` sygnaturę czasową (nazywane `updatedAt` w magazynie lokalnym.) Identyfikator kwerendy powinna być opisowe ciąg, który jest unikatowy dla każdego zapytania logicznych w aplikacji. Aby zrezygnować z synchronizacji przyrostowej, należy przekazać `nil` jako identyfikator zapytania. Takie podejście może być potencjalnie nieefektywne, ponieważ pobiera on wszystkie rekordy na każdej operacji ściągania.

Aplikacji języka Objective C jest synchronizowane podczas modyfikowania lub dodawanie danych, gdy użytkownik wykona gestu odświeżania i uruchamiania.

Przeprowadza synchronizację Swift aplikacji, gdy użytkownik wykonuje gestu odświeżania i uruchamiania.

Synchronizacje aplikacji zawsze, gdy dane są zmodyfikowana (Objective-C) lub przy każdym uruchomieniu aplikacji (Objective-C i Swift), aplikacji przyjęto założenie, że użytkownik jest w trybie online. W dalszej części artykułu spowoduje zaktualizowanie aplikacji, dzięki czemu użytkownicy mogą edytować nawet wtedy, gdy są one w trybie offline.

## <a name="review-core-data"></a>Przegląd modelu danych podstawowych
Korzystając z magazynu offline podstawowych danych, należy zdefiniować określonego tabel i pól w modelu danych. Przykładowa aplikacja już zawiera model danych z prawidłowym formacie. W tej sekcji możemy przeprowadzenie te tabele, aby pokazać, jak są one używane.

Otwórz **QSDataModel.xcdatamodeld**. Cztery tabele są zdefiniowane — trzech, które są używane przez zestaw SDK i używany dla zadania do wykonania elementy się:
  * MS_TableOperations: Śledzi elementy, które muszą zostać zsynchronizowane z serwerem.
  * MS_TableOperationErrors: Śledzi wszystkie błędy, które mają miejsce podczas synchronizacji w trybie offline.
  * MS_TableConfig: Śledzi ostatniej aktualizacji czas ostatniej operacji synchronizacji dla wszystkich operacji ściągania.
  * TodoItem: Przechowuje elementy zadań do wykonania. Kolumny systemowe **createdAt**, **updatedAt**, i **wersji** są właściwości opcjonalne systemu.

> [!NOTE]
> Zestaw SDK usługi Mobile Apps rezerwuje nazwy kolumn, które zaczynają się od "**``**". Nie należy używać tego prefiksu z innym niż system kolumn. W przeciwnym razie wartość nazwy kolumny są modyfikowane, gdy używasz zdalnego zaplecza.
>
>

Korzystając z funkcji synchronizacji w trybie offline, zdefiniuj tabel systemowych trzy i tabelę danych.

### <a name="system-tables"></a>Tabele systemowe

**MS_TableOperations**  

![Atrybuty tabeli MS_TableOperations][defining-core-data-tableoperations-entity]

| Atrybut | Typ |
| --- | --- |
| id | Liczba całkowita 64 |
| Identyfikator elementu | Ciąg |
| properties | Dane binarne |
| tabela | Ciąg |
| tableKind | Liczba całkowita 16 |


**MS_TableOperationErrors**

 ![Atrybuty tabeli MS_TableOperationErrors][defining-core-data-tableoperationerrors-entity]

| Atrybut | Typ |
| --- | --- |
| id |Ciąg |
| operationId |Liczba całkowita 64 |
| properties |Dane binarne |
| tableKind |Liczba całkowita 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Atrybut | Typ |
| --- | --- |
| id |Ciąg |
| key |Ciąg |
| Właściwość KeyType |Liczba całkowita 64 |
| tabela |Ciąg |
| wartość |Ciąg |

### <a name="data-table"></a>Tabela danych

**TodoItem**

| Atrybut | Typ | Uwaga |
| --- | --- | --- |
| id | Ciąg, oznaczona jako wymagana |klucz podstawowy w magazynie zdalnym |
| Zakończenie | Wartość logiczna | Pole elementu zadań do wykonania |
| Tekst |Ciąg |Pole elementu zadań do wykonania |
| CreatedAt | Date | (opcjonalnie) Mapuje **createdAt** właściwości systemu |
| updatedAt | Date | (opcjonalnie) Mapuje **updatedAt** właściwości systemu |
| wersja | Ciąg | (opcjonalnie) Używane do wykrywania konfliktów, mapy wersji |

## <a name="setup-sync"></a>Należy zmienić to zachowanie synchronizacji aplikacji
W tej sekcji możesz zmodyfikować aplikację tak, aby nie synchronizuje się na początku aplikacji lub podczas wstawiania i aktualizowania elementów. Synchronizuje tylko wtedy, gdy jest wykonywana na przycisku odświeżania gestu.

**Objective-C**:

1. W **QSTodoListViewController.m**, zmień **viewDidLoad** metodę, aby usunąć wywołanie `[self refresh]` na końcu metody. Teraz danych nie jest zsynchronizowane z serwerem, po uruchomieniu aplikacji. Zamiast tego jest synchronizowany z zawartością magazynu lokalnego.
2. W **QSTodoService.m**, zmodyfikować definicję `addItem` tak, aby nie synchronizacji, po wstawieniu elementu. Usuń `self syncData` blokować i zastąp go następującym kodem:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Zmodyfikować definicję `completeItem` jak wspomniano powyżej. Usuń blok dla `self syncData` i zastąp go następującym kodem:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Kod SWIFT**:

W `viewDidLoad`w **ToDoTableViewController.swift**, komentarz dwa wiersze, które są wyświetlane tutaj, aby zatrzymać synchronizację po uruchomieniu aplikacji. W momencie pisania tego dokumentu aplikacji Swift Todo nie powoduje aktualizacji usługi, gdy ktoś dodaje lub zakończeniu elementu. Aktualizuje usługę tylko po uruchomieniu aplikacji.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testowanie aplikacji
W tej sekcji można podłączyć się do nieprawidłowego adresu URL do symulowania scenariusz w trybie offline. Po dodaniu elementów danych są przechowywane w magazynu danych podstawowych lokalnym, ale nie są one zsynchronizowane z zaplecza aplikacji mobilnej.

1. Zmień adres URL aplikacji mobilnej w **QSTodoService.m** nieprawidłowy adres URL, i ponownie uruchom aplikację:

   **Objective-C**. W QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Kod SWIFT**. W ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Dodać niektóre elementy zadań do wykonania. Zamknij symulator (lub Wymuś Zamknij aplikację), a następnie uruchom go ponownie. Sprawdź, czy zachować zmiany.

3. Wyświetlanie zawartości zdalnego **TodoItem** tabeli:
   * Dla środowiska Node.js zaplecze, przejdź do [portalu Azure](https://portal.azure.com/) i w Twojej aplikacji mobilnej wewnętrznej, kliknij przycisk **łatwe tabel** > **TodoItem**.  
   * Dla programu .NET w ponownie zakończyć, użyj narzędzia SQL, takich jak SQL Server Management Studio lub klienta REST, takiego jak Fiddler lub Postman.  

4. Upewnij się, że nowe elementy *nie* zostały zsynchronizowane z serwerem.

5. Zmień adres URL właściwą **QSTodoService.m**, a następnie ponownie uruchom aplikację.

6. Wykonuje gestu odświeżania ściąganie w dół na liście elementów.  
Pokrętła postępu jest wyświetlana.

7. Widok **TodoItem** danych ponownie. Nowe i zmienione wykonania powinna być teraz wyświetlana.

## <a name="summary"></a>Podsumowanie
Aby zapewnić obsługę funkcji synchronizacji w trybie offline, użyliśmy `MSSyncTable` interfejsu i zainicjować `MSClient.syncContext` z lokalnego magazynu. W takim przypadku Magazyn lokalny jest bazy danych opartej na danych podstawowych.

Korzystając z lokalnego magazynu danych podstawowe, należy zdefiniować wiele tabel o [Popraw właściwości systemu](#review-core-data).

Normalne tworzenia, odczytu, aktualizacji operacji i usuwania (CRUD) na pracę aplikacji mobilnych tak, jakby aplikacji jest nadal połączony, ale wszystkie operacje są wykonywane przed magazynu lokalnego.

Lokalny magazyn firma Microsoft synchronizowane z serwerem, użyliśmy **MSSyncTable.pullWithQuery** metody.

## <a name="additional-resources"></a>Zasoby dodatkowe
* [synchronizacji danych w trybie Offline w aplikacjach mobilnych]
* [Okładce chmury: Synchronizacja w trybie Offline w usłudze Azure Mobile Services] \(jest wideo dotyczące usługi Mobile Services, ale działa synchronizacja Mobile Apps w tryb offline w podobny sposób.\)

<!-- URLs. -->


[tworzenie aplikacji systemu iOS]: app-service-mobile-ios-get-started.md
[synchronizacji danych w trybie Offline w aplikacjach mobilnych]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Okładce chmury: Synchronizacja w trybie Offline w usłudze Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
