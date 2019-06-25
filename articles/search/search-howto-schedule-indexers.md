---
title: Jak zaplanować indeksatory — usługa Azure Search
description: Zaplanuj indeksatorów usługi Azure Search na indeksowanie zawartości, okresowo lub w określonym czasie.
ms.date: 05/31/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 4bf931b19b7490a94f30afde49038cdc7573fab3
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302244"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Jak zaplanować indeksatorów usługi Azure Search
Indeksator zwykle jest uruchamiane jeden raz, natychmiast, po jego utworzeniu. Można uruchomić go ponownie na żądanie przy użyciu portalu, interfejsu API REST lub zestawu .NET SDK. Można również skonfigurować indeksator okresowe uruchamianie zgodnie z harmonogramem.

Sytuacje, gdzie planowanie indeksatora jest przydatne:

* Źródło danych zmieni się wraz z upływem czasu i chcesz, aby indeksatorów usługi Azure Search, aby automatycznie przetwarzać zmienionych danych.
* Indeks zostaną wypełnione z wielu źródeł danych, a użytkownik chce upewnij się, że indeksatorów, uruchom w różnym czasie, aby zmniejszyć konflikty.
* Źródło danych jest bardzo duży i chcesz się indeksatora przetwarzania wraz z upływem czasu. Aby uzyskać więcej informacji dotyczących indeksowania dużych ilości danych, zobacz [jak indeksować dużych zestawów danych w usłudze Azure Search](search-howto-large-index.md).

Harmonogram jest wbudowana funkcja usługi Azure Search. Zewnętrzny harmonogram nie można użyć do kontrolowania indeksatory wyszukiwania.

## <a name="define-schedule-properties"></a>Zdefiniuj właściwości harmonogramu

Planowanie uruchamiania indeksatora ma dwie właściwości:
* **Interwał**, czas, który definiuje między zaplanowane wykonań indeksatora. Najmniejsza dozwolony interwał wynosi 5 minut, a największa wynosi 24 godziny.
* **Rozpocznij czasu (UTC)** , co oznacza indeksator powinien zostać uruchomiony po raz pierwszy.

Można określić harmonogramu, tworząc po raz pierwszy indeksatora lub aktualizując właściwości indeksatora w dalszej części. Indeksator harmonogramy można ustawić za pomocą [portal](#portal), [interfejsu API REST](#restApi), lub [zestawu .NET SDK](#dotNetSdk).

Tylko jedno wykonywanie indeksatora można uruchamiać jednocześnie. Jeśli indeksatora jest już uruchomiona, podczas jego następnego wykonania jest zaplanowane, tego wykonania jest odroczone do czasu następnego zaplanowanego czasu.

Rozważmy przykład się to bardziej konkretne. Załóżmy, że możemy skonfigurować harmonogram indeksatora **interwał** programu co godzinę i **czas rozpoczęcia** od 1 czerwca 2019, 8:00:00 czasu UTC. Oto, co może się zdarzyć, gdy uruchomienie indeksatora trwa dłużej niż godzinę:

* Pierwsze wykonanie indeksatora rozpoczyna się o lub około 1 czerwca 2019, 8:00 czasu UTC. Załóżmy, że to wykonywanie trwa 20 minut (lub w dowolnej chwili mniejsza niż 1 godzina).
* Wykonanie drugiej rozpoczyna się o lub około 1 czerwca 2019 9:00:00 czasu UTC. Załóżmy, że to wykonanie zajmuje 70 minut — więcej niż jedna godzina —, a nie zakończy się aż do 10:10:00 czasu UTC.
* Trzeci wykonywania jest zaplanowane na 10:00 czasu UTC, ale w tym czasie wykonywania jest nadal uruchomiona. To zaplanowane wykonanie następnie zostało pominięte. Kolejne wykonanie indeksatora nie zostaną uruchomione aż do 11:00 czasu UTC.

> [!NOTE]
> Jeśli indeksatora jest ustawiona na niektórych harmonogramu, ale wielokrotnie kończy się niepowodzeniem, w tym samym dokumentu wielokrotnie każdorazowo jego uruchomienia, rozpocznie się indeksatora uruchomione na dłuższe interwały interwału (maksymalnie do co najmniej raz na 24 godziny), dopóki nie jest pomyślnie sprawia, że aga postępu w programie.  Jeśli uważasz, że rozwiązaniu niezależnie od rodzaju problemu, który powodował indeksatora zostać zatrzymane w pewnym momencie, możesz wykonać na żądanie uruchomienia indeksatora, i jeśli pomyślnie sprawia to, że postęp, indeksator powróci do jego ustaw interwał harmonogramu ponownie.

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>Zdefiniowanie harmonogramu w portalu

Kreatora importu danych w witrynie portal pozwala zdefiniować harmonogram dla indeksatora w czasie jego tworzenia. Ustawieniem domyślnym harmonogramem jest **godzinowe**, co oznacza, że indeksator jest uruchamiane jeden raz po utworzeniu i jest uruchamiany ponownie co godzinę później.

Można zmienić ustawienie harmonogramu, aby **po** Jeśli nie chcesz indeksatora, aby automatycznie ponownie uruchomić lub **codzienne** do uruchamiania raz dziennie. Ustaw ją na **niestandardowe** Jeśli chcesz określić inny interwał lub określonych Start przyszłości.

Po ustawieniu harmonogramu na **niestandardowe**, pola są wyświetlane, aby określić **interwał** i **Start Time (UTC)** . Jak najkrótszy czas, dozwolony interwał to 5 minut, a najdłuższej jest 1440 minut (24 godziny).

   ![Planowanie uruchamiania indeksatora ustawienia w Kreatorze importu danych](media/search-howto-schedule-indexers/schedule-import-data.png "planowanie uruchamiania indeksatora ustawienia w Kreatorze importu danych")

Po utworzeniu indeksatora, można zmienić ustawienia harmonogramu, za pomocą panelu edycji indeksatora. Pola harmonogramu są takie same jak Kreatora importu danych.

   ![Ustawianie harmonogramu w panelu edycji indeksatora](media/search-howto-schedule-indexers/schedule-edit.png "ustawienie harmonogramu w panelu edycji indeksatora")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>Zdefiniowanie harmonogramu przy użyciu interfejsu API REST

Można zdefiniować harmonogram dla indeksatora przy użyciu interfejsu API REST. Aby to zrobić, należy dołączyć **harmonogram** właściwości podczas tworzenia lub aktualizowania indeksatora. W poniższym przykładzie przedstawiono żądanie PUT, aby zaktualizować istniejącego indeksatora:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**Interwał** parametr jest wymagany. Interwał odnosi się do czasu między rozpoczęciem dwóch następujących po sobie indeksatora wykonań. Najmniejszy dozwolony interwał wynosi 5 minut; najdłuższej to jeden dzień. Musi być sformatowany jako wartość XSD "dayTimeDuration" (ograniczony podzestaw [czas trwania ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) wartości). Jest to wzorzec: `P(nD)(T(nH)(nM))`. Przykłady: `PT15M` co 15 minut `PT2H` co 2 godziny.

Opcjonalny **startTime** wskazuje rozpoczęcia zaplanowanego wykonania. Jeśli zostanie pominięty, używany jest bieżący czas UTC. Tym razem mogą znajdować się w przeszłości, w których przypadku pierwszego wykonania jest zaplanowane tak, jakby indeksatora działał nieprzerwanie od oryginalnego **startTime**.

Można również uruchomić indeksatora na żądanie w dowolnym momencie za pomocą wywołania uruchomienia indeksatora. Aby uzyskać więcej informacji o uruchamianiu indeksatory i ustawianie harmonogramów indeksatora, zobacz [uruchomić indeksator](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [uzyskać indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer), i [indeksatora aktualizacji](https://docs.microsoft.com/rest/api/searchservice/update-indexer) w dokumentacji interfejsu API REST.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>Zdefiniowanie harmonogramu przy użyciu zestawu .NET SDK

Można zdefiniować harmonogram dla indeksatora przy użyciu zestawu .NET SDK usługi Azure Search. Aby to zrobić, należy dołączyć **harmonogram** właściwości podczas tworzenia lub aktualizowania indeksatora.

Następujące C# przykład tworzy indeksatora, przy użyciu indeksu i źródła danych wstępnie zdefiniowanych i ustawia jego harmonogram, aby uruchomić jeden raz każdego dnia, od 30 minut od teraz:

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
Jeśli **harmonogram** parametr zostanie pominięty, indeksator będzie uruchamiane tylko jeden raz natychmiast, po jego utworzeniu.

**StartTime** parametr może być ustawiony na godzinę w przeszłości. W takim przypadku pierwszego wykonania jest zaplanowane tak, jakby indeksatora działał nieprzerwanie od danego **startTime**.

Harmonogram jest definiowana za pomocą [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) klasy. **IndexingSchedule** Konstruktor wymaga **interwał** określony za pomocą parametru **TimeSpan** obiektu. Najmniejsza dozwolona wartość interwału wynosi 5 minut, a największa to 24 godziny. Drugi **startTime** określono jako parametr **DateTimeOffset** obiektu, jest opcjonalny.

Zestaw SDK platformy .NET umożliwia Operacje indeksatora kontroli przy użyciu [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) klasy i jego [indeksatory](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) właściwość, która implementuje metody z **IIndexersOperations**interfejsu. 

Indeksator można uruchomić na żądanie w dowolnym momencie przy użyciu jednej z [Uruchom](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync), lub [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) metody.

Aby uzyskać więcej informacji na temat tworzenia aktualizacji i systemem indeksatorów, zobacz [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
