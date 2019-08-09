---
title: Jak zaplanować indeksatory — Azure Search
description: Zaplanuj Azure Search indeksatory, aby okresowo indeksować zawartość lub w określonych godzinach.
ms.date: 05/31/2019
author: HeidiSteen
manager: HeidiSteen
ms.author: heidist
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.openlocfilehash: 245a2139aae0910ea1415811234667f2c06500ec
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855793"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Jak zaplanować indeksatory dla Azure Search
Indeksator jest zwykle uruchamiany jednokrotnie, natychmiast po jego utworzeniu. Można uruchomić je ponownie na żądanie przy użyciu portalu, interfejsu API REST lub zestawu .NET SDK. Można również skonfigurować indeksator do uruchamiania okresowo zgodnie z harmonogramem.

Niektóre sytuacje, w których planowanie indeksatora jest przydatne:

* Dane źródłowe ulegną zmianie z upływem czasu i chcesz, aby indeksatory Azure Search automatycznie przetwarzać zmienione dane.
* Indeks zostanie wypełniony z wielu źródeł danych i chcesz się upewnić, że Indeksatory są uruchamiane w różnych porach, aby zmniejszyć liczbę konfliktów.
* Dane źródłowe są bardzo duże i chcesz rozłożyć przetwarzanie indeksatora w czasie. Aby uzyskać więcej informacji na temat indeksowania dużych ilości danych, zobacz [jak indeksować duże zestawy danych w Azure Search](search-howto-large-index.md).

Harmonogram jest wbudowaną funkcją Azure Search. Nie można użyć zewnętrznego harmonogramu do kontrolowania indeksatorów wyszukiwania.

## <a name="define-schedule-properties"></a>Definiowanie właściwości harmonogramu

Harmonogram indeksatora ma dwie właściwości:
* **Interwał**, który określa ilość czasu między wykonaniami zaplanowanego indeksatora. Najmniejszy dozwolony interwał wynosi 5 minut, a największe to 24 godziny.
* **Godzina rozpoczęcia (UTC)** , która wskazuje czas pierwszego uruchomienia indeksatora.

Możesz określić harmonogram podczas pierwszego tworzenia indeksatora lub przez aktualizację właściwości indeksatora później. Harmonogramy indeksatorów można ustawiać przy użyciu [portalu](#portal), [interfejsu API REST](#restApi)lub [zestawu .NET SDK](#dotNetSdk).

Tylko jedno wykonanie indeksatora może być uruchamiane w danym momencie. Jeśli indeksator jest już uruchomiony po zaplanowaniu następnego wykonania, to wykonanie jest odroczone do czasu następnego zaplanowanego czasu.

Rozważmy przykład, aby zwiększyć tę liczbę. Załóżmy, że skonfigurowano harmonogram indeksatora z **interwałem** co godzinę i godziną **rozpoczęcia** od 1 czerwca 2019 8:00:00 o godz. UTC. Oto, co się dzieje, gdy działanie indeksatora trwa dłużej niż godzinę:

* Pierwsze wykonanie indeksatora rozpocznie się o 1 czerwca 2019 o godzinie 8:00 czasu UTC. Załóżmy, że wykonanie tej operacji trwa 20 minut (lub w dowolnym czasie krótszym niż 1 godzina).
* Drugie wykonanie jest uruchamiane o lub około 1 czerwca 2019 9:00 czasu UTC. Załóżmy, że to wykonanie trwa 70 minut — więcej niż godzinę — i nie zostanie ukończone do 10:10 czasu UTC.
* Zaplanowano rozpoczęcie trzeciego wykonania o godzinie 10:00 czasu UTC, ale w tym momencie poprzednie wykonanie jest nadal uruchomione. To zaplanowane wykonanie jest następnie pomijane. Następne wykonanie indeksatora nie zostanie uruchomione do czasu 11:00 czasu UTC.

> [!NOTE]
> Jeśli indeksator jest ustawiony na określony harmonogram, ale wielokrotnie powtarza się w tym samym dokumencie za każdym razem, gdy zostanie on uruchomiony, indeksator zacznie działać w krótszym interwale (maksymalnie co 24 godziny), aż do pomyślnego przekroczenia postępu Aga podczas.  Jeśli uważasz, że Rozwiązano problem, który spowodował zablokowanie indeksatora w określonym punkcie, można wykonać uruchomienie na żądanie indeksatora, a jeśli ten proces pomyślnie przejdzie, indeks ponownie powróci do jego ustawionego interwału harmonogramu.

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>Definiowanie harmonogramu w portalu

Kreator importu danych w portalu umożliwia zdefiniowanie harmonogramu indeksatora podczas tworzenia. Domyślne ustawienie harmonogramu jest **co godzinę**, co oznacza, że indeksator jest uruchamiany po utworzeniu i ponownie uruchamiany co godzinę.

Jeśli nie chcesz, aby indeksator był uruchamiany automatycznie lub **codziennie** do uruchamiania raz dziennie, możesz zmienić ustawienie harmonogramu na **jeden raz** . Ustaw wartość na **Custom** , jeśli chcesz określić inny interwał lub określony przyszły czas rozpoczęcia.

Po ustawieniu harmonogramu na **niestandardowy**, pola są wyświetlane, aby umożliwić określenie interwału i **godziny rozpoczęcia (UTC)** . Najkrótszy dozwolony interwał wynosi 5 minut, a najdłuższy czas to 1440 minut (24 godziny).

   ![Ustawianie harmonogramu indeksatora w Kreatorze importu danych](media/search-howto-schedule-indexers/schedule-import-data.png "Ustawianie harmonogramu indeksatora w Kreatorze importu danych")

Po utworzeniu indeksatora można zmienić ustawienia harmonogramu za pomocą panelu edycji indeksatora. Pola harmonogramu są takie same jak w Kreatorze importu danych.

   ![Ustawianie harmonogramu w panelu edytowania indeksatora](media/search-howto-schedule-indexers/schedule-edit.png "Ustawianie harmonogramu w panelu edytowania indeksatora")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>Definiowanie harmonogramu przy użyciu interfejsu API REST

Można zdefiniować harmonogram dla indeksatora przy użyciu interfejsu API REST. W tym celu należy uwzględnić Właściwość **Schedule** podczas tworzenia lub aktualizowania indeksatora. Poniższy przykład przedstawia żądanie PUT, aby zaktualizować istniejący indeksator:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Parametr **interwału** jest wymagany. Interwał odnosi się do czasu między rozpoczęciem dwóch kolejnych wykonań indeksatora. Najmniejszy dozwolony interwał wynosi 5 minut; Najdłuższa wartość to jeden dzień. Musi być sformatowana jako wartość XSD "dayTimeDuration" (ograniczony podzbiór wartości [Duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Wzorzec dla tego elementu to: `P(nD)(T(nH)(nM))`. Przykłady: `PT15M` co 15 minut, `PT2H` przez co 2 godziny.

Opcjonalny parametr **StartTime** wskazuje, kiedy mają zostać rozpoczęte zaplanowane wykonania. W przypadku pominięcia jest używany bieżący czas UTC. Ten czas może znajdować się w przeszłości, w tym przypadku pierwsze wykonanie jest zaplanowane tak, jakby indeksator działał w sposób ciągły od pierwotnego czasu **rozpoczęcia**.

Indeksator można również uruchomić na żądanie w dowolnym momencie przy użyciu wywołania Uruchom indeksator. Aby uzyskać więcej informacji o uruchamianiu indeksatorów i ustawianiu harmonogramów indeksatora, zobacz [Uruchamianie indeksatora](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [pobieranie indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer)i [Aktualizowanie indeksatora](https://docs.microsoft.com/rest/api/searchservice/update-indexer) w dokumentacji interfejsu API REST.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>Definiowanie harmonogramu przy użyciu zestawu .NET SDK

Można zdefiniować harmonogram dla indeksatora przy użyciu zestawu SDK platformy .NET Azure Search. W tym celu należy uwzględnić Właściwość **Schedule** podczas tworzenia lub aktualizowania indeksatora.

Poniższy C# przykład tworzy indeksator przy użyciu wstępnie zdefiniowanego źródła danych i indeksu i ustawia jego harmonogram do uruchomienia raz dziennie od 30 minut od teraz:

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
Jeśli parametr **Schedule** zostanie pominięty, indeksator zostanie uruchomiony tylko natychmiast po jego utworzeniu.

Parametr **StartTime** można ustawić na godzinę w przeszłości. W takim przypadku pierwsze wykonanie jest zaplanowane tak, jakby indeksator działał w sposób ciągły od danego momentu **rozpoczęcia**.

Harmonogram jest definiowany przy użyciu klasy [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) . Konstruktor **IndexingSchedule** wymaga parametru **interwału** określonego przy użyciu obiektu **TimeSpan** . Najmniejsza dozwolona wartość interwału wynosi 5 minut, a największe to 24 godziny. Drugi parametr **StartTime** określony jako obiekt **DateTimeOffset** jest opcjonalny.

Zestaw SDK platformy .NET umożliwia kontrolowanie operacji indeksatora przy użyciu klasy [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) i [](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) jej właściwości indeksatorów, która implementuje metody z interfejsu **IIndexersOperations** . 

Indeksator można uruchomić na żądanie w dowolnym momencie przy użyciu jednej z metod [Run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)lub [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) .

Aby uzyskać więcej informacji na temat tworzenia, aktualizowania i uruchamiania indeksatorów, zobacz [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
