---
title: Nagłówki odpowiedzi usługi Azure Cosmos DB Gremlin
description: Dokumentacja referencyjna dotycząca metadanych odpowiedzi serwera, która umożliwia dodatkowe rozwiązywanie problemów
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755077"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Nagłówki odpowiedzi serwera usługi Azure Cosmos DB Gremlin
W tym artykule opisano nagłówki, które serwer Gremlin usługi Cosmos DB zwraca do obiektu wywołującego po wykonaniu żądania. Te nagłówki są przydatne podczas rozwiązywania problemów z wydajnością żądań, tworzenia aplikacji integrującej się natywnie z usługą Cosmos DB i upraszczania obsługi klientów.

Należy pamiętać, że biorąc zależność od tych nagłówków są ograniczenie przenoszenia aplikacji do innych implementacji Gremlin. W zamian zyskujesz ściślejszą integrację z Cosmos DB Gremlin. Te nagłówki nie są standardem TinkerPop.

## <a name="headers"></a>Nagłówki

| Nagłówek | Typ | Wartość próbki | Po uwzględnieniu | Wyjaśnienie |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11.3243 | Sukces i niepowodzenie | Ilość przepływności kolekcji lub bazy danych używane w [jednostkach żądań (RU/s lub RU)](request-units.md) dla komunikatu częściowej odpowiedzi. Ten nagłówek jest obecny w każdej kontynuacji dla żądań, które mają wiele fragmentów. Odzwierciedla ładunek określonego fragmentu odpowiedzi. Tylko dla żądań, które składają się z jednego fragmentu odpowiedzi ten nagłówek odpowiada całkowity koszt przechodzenia. Jednak dla większości złożonych traversals ta wartość reprezentuje koszt częściowy. |
| **x-ms-total-request-charge** | double | 423.987 | Sukces i niepowodzenie | Ilość zbierania lub przepływności bazy danych zużywane w [jednostkach żądania (RU/s lub RU)](request-units.md) dla całego żądania. Ten nagłówek jest obecny w każdej kontynuacji dla żądań, które mają wiele fragmentów. Wskazuje ona opłatę skumulowaną od początku żądania. Wartość tego nagłówka w ostatnim fragmencie wskazuje pełną opłatę za żądanie. |
| **x-ms-server-time-ms** | double | 13.75 | Sukces i niepowodzenie | Ten nagłówek jest dołączony do celów rozwiązywania problemów z opóźnieniami. Wskazuje ilość czasu w milisekundach, który serwer Programu Cosmos DB Gremlin zajęło wykonanie i wykonanie komunikatu odpowiedzi częściowej. Przy użyciu wartości tego nagłówka i porównując go do ogólnych aplikacji opóźnienia żądania można obliczyć opóźnienie sieci obciążenie. |
| **x-ms-total-server-time-ms** | double | 130.512 | Sukces i niepowodzenie | Całkowity czas ( w milisekundach) serwera Programu Cosmos DB Gremlin, który zajął wykonanie całego przechodzenia. Ten nagłówek jest uwzględniony w każdej odpowiedzi częściowej. Reprezentuje skumulowany czas wykonywania od początku żądania. Ostatnia odpowiedź wskazuje całkowity czas wykonywania. Ten nagłówek jest przydatne do rozróżnienia między klientem a serwerem jako źródło opóźnienia. Można porównać czas wykonywania przechodzenia na klienta do wartości tego nagłówka. |
| **x-ms-status-code** | long | 200 | Sukces i niepowodzenie | Nagłówek wskazuje wewnętrzną przyczynę zakończenia lub zakończenia żądania. Aplikacja zaleca się przyjrzeć wartość tego nagłówka i podjąć działania naprawcze. |
| **x-ms-substatus-code** | long | 1003 | Tylko awaria | Cosmos DB to wielomodelowa baza danych, która jest zbudowana na ujednoliconej warstwie magazynu. Ten nagłówek zawiera dodatkowe szczegółowe informacje na temat przyczyny awarii, gdy wystąpi błąd w niższych warstwach stosu wysokiej dostępności. Aplikacji zaleca się przechowywanie tego nagłówka i używać go podczas kontaktowania się z obsługą klienta usługi Cosmos DB. Wartość tego nagłówka jest przydatna dla inżyniera usługi Cosmos DB do szybkiego rozwiązywania problemów. |
| **x-ms-retry-after-ms** | ciąg (TimeSpan) | "00:00:03.9500000" | Tylko awaria | Ten nagłówek jest reprezentacją ciągu typu [.NET TimeSpan.](https://docs.microsoft.com/dotnet/api/system.timespan) Ta wartość będzie uwzględniana tylko w żądaniach nie powiodło się z powodu wyczerpania przepływności aprowizowanej. Aplikacja powinna ponownie przesłać przechodzenie ponownie po poleconych okresach. |
| **x-ms-activity-id** | (Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Sukces i niepowodzenie | Nagłówek zawiera unikatowy identyfikator żądania po stronie serwera. Każde żądanie jest przypisywane przez serwer unikatowy identyfikator do celów śledzenia. Aplikacje powinny rejestrować identyfikatory działań zwracanych przez serwer dla żądań, o których klienci mogą chcieć skontaktować się z działem obsługi klienta. Personel pomocy technicznej usługi Cosmos DB może znaleźć określone żądania według tych identyfikatorów w danych telemetrycznych usługi usługi Usługi Cosmos DB. |

## <a name="status-codes"></a>Kody stanu

Poniżej wymieniono większość typowych kodów stanu zwracanych przez serwer.

| Stan | Wyjaśnienie |
| --- | --- |
| **401** | Komunikat `"Unauthorized: Invalid credentials provided"` o błędzie jest zwracany, gdy hasło uwierzytelniania nie jest zgodne z kluczem konta usługi Cosmos DB. Przejdź do konta Usługi Cosmos DB Gremlin w witrynie Azure portal i upewnij się, że klucz jest poprawny.|
| **404** | Równoczesne operacje, które próbują usunąć i zaktualizować tę samą krawędź lub wierzchołek jednocześnie. Komunikat o błędzie `"Owner resource does not exist"` wskazuje, że określona baza danych lub kolekcja jest niepoprawna w ramach parametrów połączenia w formacie `/dbs/<database name>/colls/<collection or graph name>`.|
| **408** | `"Server timeout"`oznacza, że przechodzenie trwało ponad **30 sekund** i zostało anulowane przez serwer. Zoptymalizuj przechodzenie, aby szybko działały, filtrując wierzchołki lub krawędzie przy każdym przeskoku przechodzenia, aby zawęzić zakres wyszukiwania.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` Taka sytuacja zwykle występuje, gdy wierzchołek lub krawędź z danym identyfikatorem już istnieje w grafie.| 
| **412** | Kod stanu jest uzupełniony `"PreconditionFailedException": One of the specified pre-condition is not met`komunikatem o błędzie . Ten błąd wskazuje na naruszenie kontroli współbieżności optymistyczne między odczytywanie krawędzi lub wierzchołka i zapisywanie go z powrotem do magazynu po modyfikacji. Najczęstsze sytuacje, gdy wystąpi ten błąd `g.V('identifier').property('name','value')`jest modyfikacja właściwości, na przykład . Aparat Gremlin będzie odczytywać wierzchołek, modyfikować go i zapisywać go z powrotem. Jeśli istnieje inny przechodzenie uruchomione równolegle próbuje napisać ten sam wierzchołek lub krawędzi, jeden z nich otrzyma ten błąd. Aplikacja powinna ponownie przesłać przechodzenie do serwera.| 
| **429** | Żądanie zostało ograniczone i powinno zostać ponowione po wartości w **x-ms-retry-after-ms**| 
| **500** | Komunikat o błędzie zawierający informację `"NotFoundException: Entity with the specified id does not exist in the system."` wskazuje, że utworzono ponownie bazę danych i/lub kolekcję z tą samą nazwą. Ten błąd zniknie w ciągu 5 minut, gdy zmiana zostanie rozpropagowana i unieważni pamięci podręczne w różnych składnikach usługi Cosmos DB. Aby uniknąć tego problemu, za każdym razem używaj unikatowych nazw baz danych i kolekcji.| 
| **1000** | Ten kod stanu jest zwracany, gdy serwer pomyślnie przeanalizował wiadomość, ale nie mógł wykonać. Zwykle wskazuje na problem z kwerendą.| 
| **1001** | Ten kod jest zwracany po zakończeniu wykonywania przez przechodzenie serwera, ale nie można serializować odpowiedzi z powrotem do klienta. Ten błąd może się zdarzyć, gdy traversal generuje złożony wynik, który jest zbyt duży lub nie jest zgodny ze specyfikacją protokołu TinkerPop. Aplikacja powinna uprościć przechodzenie, gdy napotka ten błąd. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`jest zwracany, gdy traversal przekracza dozwolony limit pamięci. Limit pamięci wynosi **2 GB** na przechodzenie.| 
| **1004** | Ten kod stanu wskazuje nieprawidłowo sformułowane żądanie wykresu. Żądanie może być zniekształcone, gdy nie powiedzie się deserializacji, typ niewartości jest deserialized jako typ wartości lub nieobsługiwane operacji gremlin wymagane. Aplikacja nie należy ponowić próbę żądania, ponieważ nie zakończy się pomyślnie. | 
| **1007** | Zazwyczaj ten kod stanu jest `"Could not process request. Underlying connection has been closed."`zwracany z komunikatem o błędzie . Taka sytuacja może się zdarzyć, jeśli sterownik klienta próbuje użyć połączenia, które jest zamykane przez serwer. Aplikacja powinna ponowić próbę przechodzenia na inne połączenie.
| **1008** | Serwer Programu Cosmos DB Gremlin może zakończyć połączenia w celu zrównoważenia ruchu w klastrze. Sterowniki klienta powinny obsługiwać tę sytuację i używać tylko połączeń na żywo do wysyłania żądań do serwera. Czasami sterowniki klienta mogą nie wykryć, że połączenie zostało zamknięte. Gdy aplikacja napotka `"Connection is too busy. Please retry after sometime or open more connections."` błąd, należy ponowić próbę przechodzenia na inne połączenie.

## <a name="samples"></a>Samples

Przykładowa aplikacja kliencka oparta na Gremlin.Net, która odczytuje jeden atrybut stanu:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Przykład, który pokazuje, jak odczytać atrybut stanu z klienta Java Gremlin:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Następne kroki
* [Kody stanu HTTP dla usługi Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Typowe nagłówki odpowiedzi REST usługi Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Wymagania dostawcy sterowników tinkerpop graph]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
