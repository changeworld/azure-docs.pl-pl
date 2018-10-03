---
title: Rozwiązywanie konfliktów wielu wzorców w usłudze Azure Cosmos DB
description: W tym artykule opisano kategorie konfliktu i tryby rozwiązania konfliktu takie jak ostatni składnik zapisywania usługi Wins (LWW), niestandardowe niestandardowe — procedury zdefiniowane przez użytkownika — asynchroniczne w usłudze Azure DB Comsos Multi-Master.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 39fd393e78a2b66749c6aa34a758b185b38effdf
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041191"
---
# <a name="multi-master-conflict-resolution-in-azure-cosmos-db"></a>Rozwiązywanie konfliktów wielu wzorców w usłudze Azure Cosmos DB 

Multi-Master usługi Azure Cosmos DB udostępnia globalny konflikt tryby rozwiązania do upewnij się, że dane są spójne we wszystkich regionach, w których są replikowane.

## <a name="conflict-categories"></a>Konflikt kategorii

Istnieją trzy kategorie konfliktów, które mogą wystąpić podczas pracy z danymi usługi Azure Cosmos DB:

* **Wstaw konfliktów:** ten typ konfliktu się dzieje, gdy aplikacja wstawia dwie lub więcej dokumentów za pomocą tego samego unikatowy indeks (na przykład identyfikator) z co najmniej dwóch regionów jednocześnie. W takim przypadku wszystkie zapisy może się powieść, początkowo, ale oparte na zasady rozwiązywania konfliktów, które wybierzesz, tylko jeden dokument o identyfikatorze oryginalnego dba.

* **Zastąp konfliktów:** ten typ konfliktu się dzieje, gdy aplikacja aktualizuje pojedynczy dokument równocześnie z co najmniej dwóch regionach.

* **Usuń konfliktów:** ten typ konfliktu się dzieje w przypadku aplikacji spowoduje usunięcie dokumentu z jednego regionu i aktualizuje go w jednym lub kilku regionach. 

## <a name="conflict-resolution-modes"></a>Tryby rozwiązywania konfliktów

Istnieją trzy tryby rozwiązywania konfliktów, oferowane przez usługę Azure Cosmos DB. Tryby rozwiązywania konfliktów są zdefiniowane dla każdej kolekcji.

> [!NOTE]
> Interfejs API SQL użytkownicy mogą wybrać spośród trzech trybów rozwiązania konfliktu z inną. W przypadku wszystkich innych interfejsów API modeli (bazy danych MongoDB, Cassandra, wykres i tabela) są rozwiązywane konflikty przy użyciu ostatnia zapisywania usługi Wins.

### <a name="last-writer-wins-lww"></a>Ostatni składnik zapisywania usługi Wins (LWW)

Ostatni składnik zapisywania usługi Wins jest to domyślny tryb rozwiązywania konfliktów. W tym trybie konflikty są rozwiązywane oparte na podstawie wartości liczbowej przekazywane we właściwości dokumentu.

Poniższy fragment kodu znajduje się przykład sposobu konfigurowania zasady rozwiązywania konfliktów ostatni składnik zapisywania usługi Wins w przypadku korzystania z zestawu .net SDK. "ConflictResolutionPath" Określa ścieżkę do właściwości, który jest używany w celu rozwiązania konfliktu. W tym przykładzie `/userDefinedId` jest ścieżka rozpoznawania konfliktu i dokumentu z największego `userDefinedId` wartość będzie zawsze win konflikt. Aby zarejestrować tryb rozdzielczości ostatni składnik zapisywania usługi Wins, aprowizować kolekcji za pomocą ConflictResolutionPolicy, jak pokazano poniżej.

```csharp
DocumentCollection lwwCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
   Id = "myCollection", 
   ConflictResolutionPolicy = new ConflictResolutionPolicy
   {
      Mode = ConflictResolutionMode.LastWriterWins,
      ConflictResolutionPath = "/userDefinedId"
   }
});
```

 Tryb rozdzielczości ostatni składnik zapisywania usługi Wins jest stosowane do różnych danych konflikt kategorii:

* **Wstawianie i mogą wystąpić konflikty aktualizacji:** dwóch lub więcej dokumentów kolizji podczas wstawiania operacji i zamieniania, dokument, który zawiera największą wartość dla ścieżki rozwiązywania konfliktów staje się zwycięzcę (czyli userDefinedId). Jeśli wiele dokumentów ma tę samą wartość liczbową dla ścieżki rozwiązywania konfliktów, wybranych zwycięzca jest niedeterministyczny. Jednak we wszystkich regionach zbierze do pojedynczego zwycięzca.

* **Konflikt usuwania:** w przypadku usunięcia konflikty związane, Usuń jest zawsze wins przez inne konflikty Zastąp niezależnie od wartości Ścieżka rozpoznawania konfliktu.

### <a name="custom--user-defined-procedure"></a>Niestandardowe — procedury zdefiniowanych przez użytkownika

W tym trybie rozwiązywania konfliktów kontrolki użytkownika, rejestrując zdefiniowane procedury UDP (User) do kolekcji. Ta UDP ma określonej sygnatury. Jeśli ta opcja, ale nie można zarejestrować UDP lub protokołu UDP zgłasza wyjątek w czasie wykonywania, konflikty są zapisywane do konfliktów, źródła danych, gdzie one mogą zostać rozwiązane indywidualnie.

Aby zarejestrować niestandardowy — tryb rozwiązania konfliktu zdefiniowanych przez użytkownika procedury, aprowizować kolekcji za pomocą ConflictResolutionPolicy, jak pokazano poniżej.

```csharp
DocumentCollection udpCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
  Id = "myCollection",
  ConflictResolutionPolicy = new ConflictResolutionPolicy
  {
     Mode = ConflictResolutionMode.Custom,
     ConflictResolutionProcedure = UriFactory.CreateStoredProcedureUri("myDatabase","myCollection","myUdpStoredProcedure").ToString()
  }
});
```

Następnie dodaj procedury zdefiniowanych przez użytkownika do kolekcji, jak pokazano poniżej.

```csharp
StoredProcedure myUdpStoredProc = new StoredProcedure
{
   Id = "myUdpStoredProcedure",
   Body = myUdpStoredProcText
};
await myClient.UpsertStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("myDatabase", "myCollection"), myUdpStoredProc);
```

Procedura składowana zarejestrowany w kolekcji ma specjalne podpis. W tym przykładzie protokołu UDP używa właściwości `UserDefinedId` rozwiązywania konfliktów. Dokument o największej wartości wins konflikt.

```javascript
function myUdpStoredProcedure(incomingDocument, existingDocument, isDeleteConflict, conflictingDocuments){
    var collection = getContext().getCollection();

    if (!incomingDocument) {
        if (existingDocument) {

            collection.deleteDocument(existingDocument._self, {}, function(err, responseOptions) {
                if (err) throw err;
            });
        }
    } else if (isDeleteConflict) {
        // delete always wins.
    } else {
        var documentToUse = incomingDocument;

        if (existingDocument) {
            if (documentToUse.userDefinedId < existingDocument.userDefinedId) {
                documentToUse = existingDocument;
            }
        }

        var i;
        for (i = 0; i < conflictingDocuments.length; i++) {
            if (documentToUse.userDefinedId < conflictingDocuments[i].userDefinedId) {
                documentToUse = conflictingDocuments[i];
            }
        }

        tryDelete(conflictingDocuments, incomingDocument, existingDocument, documentToUse);
    }

    function tryDelete(documents, incoming, existing, documentToInsert) {
        if (documents.length > 0) {
            collection.deleteDocument(documents[0]._self, {}, function(err, responseOptions) {
                if (err) throw err;

                documents.shift();
                tryDelete(documents, incoming, existing, documentToInsert);
            });
        } else if (existing) {
            collection.replaceDocument(existing._self, documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        } else {
            collection.createDocument(collection.getSelfLink(), documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        }
    }
}

```

Procedura ma cztery parametry:

* **incomingDocument:** określa powodujące konflikt wersji dokumentu. Konflikt można instrukcji insert, replace lub konflikt usuwania. Konflikt usuwania będzie usunąć obrazu (reliktu) bez zawartości.

* **existingDocument:** określa wartość jako incomingDocument zatwierdzonej wersji dokumentu, który ma taką samą "rid". Ten parametr ma wartość null dla wstawiania i usuwania konflikt.

* **isDeleteConflict:** flagę logiczną wskazującą, jeśli dokument przychodzących powoduje konflikt z poprzednio usuniętą dokumentu. Jeśli ten parametr jest równa existingDocument ma wartość true, będzie również o wartości null.

* **conflictingDocuments:** określa zbiór zatwierdzonej wersji wszystkie dokumenty w bazie danych, które są sprzeczne z incomingDocument w kolumnie identyfikator lub inne pola unikatowego indeksu. Te dokumenty będą miały różne wartości "rid" w porównaniu do incomingDocument.

Zdefiniowane przez użytkownika procedury ma pełny dostęp do klucza partycji usługi Cosmos DB i mogą wykonywać dowolne operacje magazynu, aby rozwiązać konflikty. Jeśli zdefiniowane przez użytkownika procedury nie zatwierdzić konfliktów wersji, system będzie się zmniejszać konfliktu i existingDocument pozostanie zatwierdzone. Jeśli procedura zdefiniowanych przez użytkownika nie powiedzie się lub nie istnieje, usługi Azure Cosmos DB wszystkie doda konfliktu do konfliktów tylko do odczytu źródła danych, gdzie będzie można przetwarzać asynchronicznie, jak pokazano na [trybu rozwiązania konfliktu asynchroniczne](#custom--asynchronous). 

### <a name="custom--asynchronous"></a>Niestandardowe — asynchroniczne  

W tym trybie usługi Azure Cosmos DB z zatwierdzaniem nie obejmuje wszystkich konfliktów (insert, replace i delete) i rejestruje je w trybie tylko do odczytu powoduje konflikt kanału informacyjnego do rozpoznawania odroczone przez użytkownika aplikacji. Aplikacja asynchronicznie wykonać rozwiązywanie konfliktów i użyć dowolnej logiki lub odwoływać się do dowolnego źródła zewnętrznego, aplikację lub usługę rozwiązać konfliktu.

Aby zarejestrować niestandardowy — tryb rozwiązania konfliktu asynchronicznego, aprowizować kolekcji z ConflictResolutionPolicy, jak pokazano poniżej.

```csharp
DocumentCollection manualCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
    Id = "myCollection",
    ConflictResolutionPolicy = new ConflictResolutionPolicy
    {
        Mode = ConflictResolutionMode.Custom
    }
});
```

Może odczytywać i przetwarzać konflikty w kanale informacyjnym konflikty, należy zaimplementować kod pokazany poniżej. Dane przechowywane w konflikcie kanału informacyjnego dodaje pewne koszty magazynowania. Dlatego zaleca się usuwania danych przechowywanych w konflikcie, źródła danych po ich przetworzeniu.

```csharp
FeedResponse<Conflict> response = await myClient.ReadConflictFeedAsync(myCollectionUri);
  foreach (Conflict conflict in response)
  {
      switch(conflict.OperationKind)
      {
         case OperationKind.Create:
         //Process Insert Conflicts.
         …
         case OperationKind.Replace:
         //Process Replace Conflicts
         …
         case OperationKind.Delete:
         //Process Delete Conflicts
         …
      }
  //Optionally delete the conflict after processed
  await myClient.DeleteConflictAsync(conflict.SelfLink);
  }
```

> [!NOTE]
> Kanał informacyjny konfliktów nie obejmuje odbiornika do wysyłania powiadomień w celu przetwarzania podrzędnego, takich jak zmiana źródła danych w usłudze Cosmos DB. Musisz zaimplementować logikę do sondowania źródła danych powoduje konflikt i określić, jeśli występują konflikty.

## <a name="code-samples"></a>Przykłady kodu

Poniżej przedstawiono przykładowe aplikacje demonstrujące rozwiązywania konfliktów dla interfejsów API, na liście. Każdy przykład generuje konfliktów w kontenerze i pokazuje, jak są rozwiązywane konflikty dla każdego trybu rozwiązywania konfliktów obsługiwane.

|Interfejs API modelu  | SDK |Sample |
|---------|---------|---------|
|INTERFEJS API SQL    | .NET    |[Azure-cosmos-DB-SQL-DotNet-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-sql-dotnet-multi-master)  |
|INTERFEJS API SQL    | Węzeł    |[Azure-cosmos-js/samples/MultiRegionWrite /](https://github.com/Azure/azure-cosmos-js/tree/master/samples/MultiRegionWrite)  |
|INTERFEJS API SQL    | Java    |[Azure-cosmos-DB-SQL-Java-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-sql-java-multi-master)  |
|MongoDB  | .NET    |[Azure-cosmos-DB-mongodb-DotNet-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-multi-master)   |
|Interfejs API tabel  | .NET    |[Azure-cosmos-DB-TABLE-DotNet-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-multi-master)       |
|Interfejs API języka Gremlin | .NET | [Azure-cosmos-DB-gremlin-dontnet-Multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-gremlin-dontnet-multi-master)|

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano kategorie konfliktu i tryby rozwiązywania konfliktów dla usługi Azure Cosmos DB. Następnie zapoznaj się z następującymi zasobami:

* [Klientów bazy danych MongoDB za pomocą Multi-Master](multi-master-oss-nosql.md)
