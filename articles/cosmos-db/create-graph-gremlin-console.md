---
title: 'Samouczek Azure Cosmos DB: Tworzenie, wykonywanie zapytań i przechodzenie w konsoli programu Apache TinkerPops Gremlin'
description: Azure Cosmos DB przewodniku szybki start do tworzenia wierzchołków, krawędzi i zapytań przy użyciu interfejsu API Azure Cosmos DB Gremlin.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: lbosq
ms.openlocfilehash: 3f25bbbbc8b3f34bdb89ba8797b042826a88ca8d
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815966"
---
# <a name="quickstart-create-query-and-traverse-an-azure-cosmos-db-graph-database-using-the-gremlin-console"></a>Szybki Start: Tworzenie, wykonywanie zapytań i przechodzenie Azure Cosmos DB bazy danych grafów przy użyciu konsoli Gremlin

> [!div class="op_single_selector"]
> * [Konsola Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [Obsługa](create-graph-php.md)
>  

Azure Cosmos DB to globalnie dystrybuowana wielomodelowa usługa bazy danych firmy Microsoft. Można szybko tworzyć i badać bazy danych dokumentów, kluczy/wartości i grafów, a wszystkie korzyści wynikające z dystrybucji globalnej i możliwości skalowania poziomego w ramach podstawy Azure Cosmos DB. 

Ten przewodnik Szybki Start przedstawia sposób Azure Cosmos DB tworzenia konta [interfejsu API Gremlin](graph-introduction.md) , bazy danych i grafu (kontenera) przy użyciu Azure Portal, a następnie używania [konsoli Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) z platformy [Apache TinkerPop](https://tinkerpop.apache.org) do pracy z danymi interfejsu API programu Gremlin. W tym samouczku utworzysz wierzchołki i krawędzie zapytania, zaktualizujesz Właściwość wierzchołka, wierzchołki zapytań, przechodzą na wykres i upuszczasz wierzchołek.

![Azure Cosmos DB z poziomu konsoli Apache Gremlin](./media/create-graph-gremlin-console/gremlin-console.png)

Konsola Gremlin jest oparta na groovy/Java i działa w systemach Linux, Mac i Windows. Można go pobrać z [witryny Apache TinkerPop](https://tinkerpop.apache.org/downloads.html).

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć subskrypcję platformy Azure, aby utworzyć konto Azure Cosmos DB dla tego przewodnika Szybki Start.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Należy również zainstalować [konsolę Gremlin](https://tinkerpop.apache.org/). Użyj wersji 3.2.5 lub nowszej. (Aby użyć konsoli Gremlin w systemie Windows, należy zainstalować [środowisko uruchomieniowe języka Java](https://www.oracle.com/technetwork/java/javase/overview/index.html)).

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Dodawanie wykresu

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a id="ConnectAppService"></a>Nawiązywanie połączenia z usługą App Service
1. Przed uruchomieniem konsoli Gremlin Utwórz lub zmodyfikuj plik konfiguracji Remote-Secure. YAML w katalogu `apache-tinkerpop-gremlin-console-3.2.5/conf`.
2. Wypełnij ustawienia konfiguracji *host*, *port*, *username*, *Password*, *connectionPool*i *Serializer* zdefiniowane w poniższej tabeli:

    Ustawienie|Sugerowana wartość|Opis
    ---|---|---
    Pracując|[*nazwa konta*. **Gremlin**. Cosmos.Azure.com]|Zobacz poniższy zrzut ekranu. Jest to wartość **identyfikatora URI Gremlin** na stronie Przegląd Azure Portal w nawiasach kwadratowych z końcowym: 443/usunięty. Uwaga: Pamiętaj, aby użyć wartości Gremlin, a **nie** identyfikatora URI kończącego się na [*account-name*. Documents.Azure.com], co prawdopodobnie spowoduje, że "host nie odpowiedział w odpowiednim czasie" podczas próby wykonania zapytań Gremlin w późniejszym czasie. 
    port|443|Ustaw wartość na 443.
    uż|*Twoja nazwa użytkownika*|Zasób formularza `/dbs/<db>/colls/<coll>` gdzie `<db>` to nazwa bazy danych, a `<coll>` to nazwa kolekcji.
    Hasło|*Klucz podstawowy*| Zobacz drugi zrzut ekranu poniżej. Jest to klucz podstawowy, który można pobrać ze strony klucze Azure Portal w polu klucz podstawowy. Użyj przycisku kopiowania po lewej stronie pola, aby skopiować wartość.
    connectionPool|{enableSsl: true}|Ustawienie puli połączeń protokołu SSL.
    Serializator|{className: org. Apache. tinkerpop. Gremlin.<br>Driver. SQL. GraphSONMessageSerializerV2d0,<br> Konfiguracja: {serializeResultToString: true}}|Ustaw tę wartość i Usuń wszystkie @no__t podziały wierszy podczas wklejania wartości.

    W przypadku wartości hosts skopiuj wartość **Identyfikator URI Gremlin** ze strony **Przegląd** : ![VIEW i skopiuj wartość identyfikator URI Gremlin na stronie Przegląd w Azure Portal @ no__t-3

    W polu wartość hasła Skopiuj **klucz podstawowy** ze strony **klucze** : ![View i skopiuj klucz podstawowy w Azure Portal, Strona klucze @ no__t-3

Plik Remote-Secure. YAML powinien wyglądać następująco:

```
hosts: [your_database_server.gremlin.cosmos.azure.com] 
port: 443
username: /dbs/your_database_account/colls/your_collection
password: your_primary_key
connectionPool: {
  enableSsl: true
}
serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV2d0, config: { serializeResultToString: true }}
```

Pamiętaj, aby zawinąć wartość parametru hosty w nawiasy kwadratowe []. 

1. W terminalu uruchom `bin/gremlin.bat` lub `bin/gremlin.sh`, aby uruchomić [konsolę Gremlin](https://tinkerpop.apache.org/docs/3.2.5/tutorials/getting-started/).
1. W terminalu uruchom `:remote connect tinkerpop.server conf/remote-secure.yaml`, aby nawiązać połączenie z usługą App Service.

    > [!TIP]
    > Jeśli wystąpi błąd `No appenders could be found for logger` upewnij się, że Zaktualizowano wartość serializatora w pliku Remote-Secure. YAML, zgodnie z opisem w sekcji Krok 2. Jeśli konfiguracja jest poprawna, to ostrzeżenie można bezpiecznie zignorować, ponieważ nie ma to wpływu na korzystanie z konsoli programu. 

1. Następne uruchomienie `:remote console`, aby przekierować wszystkie polecenia konsoli do serwera zdalnego.

   > [!NOTE]
   > Jeśli nie uruchomisz polecenia `:remote console`, ale chcesz przekierować wszystkie polecenia konsoli do serwera zdalnego, należy prefiksować polecenie z `:>`, na przykład należy uruchomić polecenie jako `:> g.V().count()`. Ten prefiks jest częścią polecenia i jest ważne w przypadku korzystania z konsoli Gremlin z Azure Cosmos DB. Pominięcie tego prefiksu nakazuje konsoli wykonywanie polecenia lokalnie, często w odniesieniu do grafu w pamięci. Za pomocą tego prefiksu `:>` instruuje konsolę do wykonania polecenia zdalnego, w tym przypadku względem Azure Cosmos DB (emulatora localhost lub wystąpienia platformy Azure).

Znakomity! Po zakończeniu instalacji uruchom ponownie niektóre polecenia konsoli.

Wypróbujmy proste polecenie Count (). W wierszu polecenia wpisz następujący tekst:

```
g.V().count()
```

## <a name="create-vertices-and-edges"></a>Tworzenie wierzchołków i krawędzi

Zacznijmy od dodania pięciu wierzchołków osoby dla *Thomas*, *Mary Kay*, *Robin*, *Ben*i *Jack*.

Dane wejściowe (Thomas):

```
g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44).property('userid', 1).property('pk', 'pk')
```

Dane wyjściowe:

```
==>[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d,label:person,type:vertex,properties:[firstName:[[id:f02a749f-b67c-4016-850e-910242d68953,value:Thomas]],lastName:[[id:f5fa3126-8818-4fda-88b0-9bb55145ce5c,value:Andersen]],age:[[id:f6390f9c-e563-433e-acbf-25627628016e,value:44]],userid:[[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d|userid,value:1]]]]
```
Dane wejściowe (Maria Kay):

```
g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39).property('userid', 2).property('pk', 'pk')

```

Dane wyjściowe:

```
==>[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e,label:person,type:vertex,properties:[firstName:[[id:ea0604f8-14ee-4513-a48a-1734a1f28dc0,value:Mary Kay]],lastName:[[id:86d3bba5-fd60-4856-9396-c195ef7d7f4b,value:Andersen]],age:[[id:bc81b78d-30c4-4e03-8f40-50f72eb5f6da,value:39]],userid:[[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e|userid,value:2]]]]

```

Dane wejściowe (Robin):

```
g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield').property('userid', 3).property('pk', 'pk')
```

Dane wyjściowe:

```
==>[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e,label:person,type:vertex,properties:[firstName:[[id:ec65f078-7a43-4cbe-bc06-e50f2640dc4e,value:Robin]],lastName:[[id:a3937d07-0e88-45d3-a442-26fcdfb042ce,value:Wakefield]],userid:[[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e|userid,value:3]]]]
```

Dane wejściowe (Ben):

```
g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller').property('userid', 4).property('pk', 'pk')

```

Dane wyjściowe:

```
==>[id:ee86b670-4d24-4966-9a39-30529284b66f,label:person,type:vertex,properties:[firstName:[[id:a632469b-30fc-4157-840c-b80260871e9a,value:Ben]],lastName:[[id:4a08d307-0719-47c6-84ae-1b0b06630928,value:Miller]],userid:[[id:ee86b670-4d24-4966-9a39-30529284b66f|userid,value:4]]]]
```

Wejście (gniazdo):

```
g.addV('person').property('firstName', 'Jack').property('lastName', 'Connor').property('userid', 5).property('pk', 'pk')
```

Dane wyjściowe:

```
==>[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469,label:person,type:vertex,properties:[firstName:[[id:4250824e-4b72-417f-af98-8034aa15559f,value:Jack]],lastName:[[id:44c1d5e1-a831-480a-bf94-5167d133549e,value:Connor]],userid:[[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469|userid,value:5]]]]
```


Następnie Dodajmy krawędzie dla relacji między naszymi osobami.

Dane wejściowe (Thomas-> Mary Kay):

```
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

Dane wyjściowe:

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Dane wejściowe (Thomas->):

```
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

Dane wyjściowe:

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Dane wejściowe (Robin-> Ben):

```
g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

Dane wyjściowe:

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>Aktualizowanie wierzchołka

Zaktualizujmy wierzchołek *Thomas* przy użyciu nowego wieku *45*.

Klawiatur
```
g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
Dane wyjściowe:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>Tworzenie zapytań względem grafu

Teraz Uruchommy wiele zapytań względem grafu.

Najpierw Spróbujmy użyć zapytania z filtrem, aby zwrócić tylko osoby, które są starsze niż 40 lat.

Dane wejściowe (zapytanie filtru):

```
g.V().hasLabel('person').has('age', gt(40))
```

Dane wyjściowe:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

Następnie Przyjrzyjmy się pierwszej nazwie osobom, które są starsze niż 40 lat.

Dane wejściowe (zapytanie + projekcja):

```
g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

Dane wyjściowe:

```
==>Thomas
```

## <a name="traverse-your-graph"></a>Przechodzenie wykresu

Przejdźmy do grafu, aby zwrócić wszystkich znajomych z firmy Thomas.

Dane wejściowe (znajomi z Thomas):

```
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

Dane wyjściowe: 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

Następnie Przyjrzyjmy się następnej warstwie wierzchołków. Przechodzenie grafu w celu zwrócenia wszystkich znajomych znajomych.

Dane wejściowe (znajomi znajomych z Thomasów):

```
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
Dane wyjściowe:

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>Upuszczanie wierzchołka

Teraz Usuń wierzchołek z bazy danych grafów.

Wejście (inicjał wierzchołka):

```
g.V().hasLabel('person').has('firstName', 'Jack').drop()
```

## <a name="clear-your-graph"></a>Wyczyść wykres

Na koniec wyczyśćmy bazę danych wszystkich wierzchołków i krawędzi.

Klawiatur

```
g.E().drop()
g.V().drop()
```

Nabycia! Ukończono to Azure Cosmos DB: samouczek interfejsu API Gremlin.

## <a name="review-slas-in-the-azure-portal"></a>Przejrzyj umowy SLA w Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta Azure Cosmos DB, tworzenia wykresu przy użyciu Eksplorator danych, tworzenia wierzchołków i krawędzi oraz przechodzenia do wykresu przy użyciu konsoli Gremlin. Teraz można tworzyć bardziej złożone zapytania i implementować zaawansowane logiki przechodzenia wykresu przy użyciu Gremlin. 

> [!div class="nextstepaction"]
> [Zapytanie przy użyciu Gremlin](tutorial-query-graph.md)
