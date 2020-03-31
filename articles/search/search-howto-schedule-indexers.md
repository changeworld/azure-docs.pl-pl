---
title: Planowanie wykonywania indeksatora
titleSuffix: Azure Cognitive Search
description: Zaplanuj indeksatory usługi Azure Cognitive Search, aby indeksować zawartość okresowo lub w określonych godzinach.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72326413d463d449d339b1f3fd241ba2c27b4b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112945"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Jak zaplanować indeksatory w usłudze Azure Cognitive Search

Indeksator zwykle działa raz, natychmiast po jego utworzeniu. Można go uruchomić ponownie na żądanie za pomocą portalu, interfejsu API REST lub sdk .NET. Można również skonfigurować indeksator do okresowego uruchamiania zgodnie z harmonogramem.

Niektóre sytuacje, w których planowanie indeksatora jest przydatne:

* Dane źródłowe będą się zmieniać wraz z czasem i chcesz, aby indeksatory usługi Azure Cognitive Search automatycznie przetwarzały zmienione dane.
* Indeks zostanie wypełniony z wielu źródeł danych i chcesz upewnić się, że indeksatory są uruchamiane w różnym czasie w celu zmniejszenia konfliktów.
* Dane źródłowe są bardzo duże i chcesz rozłożyć przetwarzania indeksatora w czasie. Aby uzyskać więcej informacji na temat indeksowania dużych ilości danych, zobacz [Jak indeksować duże zestawy danych w usłudze Azure Cognitive Search](search-howto-large-index.md).

Harmonogram jest wbudowaną funkcją usługi Azure Cognitive Search. Nie można użyć zewnętrznego harmonogramu do kontrolowania indeksatorów wyszukiwania.

## <a name="define-schedule-properties"></a>Definiowanie właściwości harmonogramu

Harmonogram indeksatora ma dwie właściwości:
* **Interval**, który określa ilość czasu pomiędzy zaplanowanymi wykonaniami indeksatora. Najmniejszy dozwolony interwał to 5 minut, a największy 24 godziny.
* **Godzina rozpoczęcia (UTC),** który wskazuje pierwszy czas, w którym indeksator powinien być uruchamiany.

Można określić harmonogram podczas pierwszego tworzenia indeksatora lub aktualizując właściwości indeksatora później. Harmonogramy indeksatora można ustawić za pomocą [portalu,](#portal) [interfejsu API REST](#restApi)lub zestawu [.NET SDK](#dotNetSdk).

Tylko jedno wykonanie indeksatora można uruchomić w czasie. Jeśli indeksator jest już uruchomiony, gdy planowane jest jego następne wykonanie, to wykonanie jest odroczone do następnego zaplanowanego czasu.

Rozważmy przykład, aby uczynić to bardziej konkretnym. Załóżmy, że konfigurujemy harmonogram indeksatora z **interwałem** godzinowym i **godziną rozpoczęcia** 1 czerwca 2019 r. o godzinie 8:00:00 CZASU UTC. Oto, co może się zdarzyć, gdy uruchomienie indeksatora trwa dłużej niż godzinę:

* Pierwsze wykonanie indeksatora rozpoczyna się o godzinie 1 czerwca 2019 r. o godzinie 8:00 CZASU UTC. Załóżmy, że to wykonanie trwa 20 minut (lub w dowolnym momencie mniej niż 1 godzinę).
* Drugie wykonanie rozpoczyna się około 1 czerwca 2019 09:00 CZASU UTC. Załóżmy, że to wykonanie trwa 70 minut — ponad godzinę — i nie zostanie ukończone do 10:10 AM UTC.
* Trzecie wykonanie ma się rozpocząć o godzinie 10:00, ale w tym czasie poprzednie wykonanie jest nadal uruchomione. To zaplanowane wykonanie jest następnie pomijane. Następne wykonanie indeksatora rozpocznie się dopiero o godzinie 11:00 CZASU UTC.

> [!NOTE]
> Jeśli indeksator jest ustawiony na określony harmonogram, ale wielokrotnie kończy się niepowodzeniem w tym samym dokumencie za każdym razem, gdy działa, indeksator zacznie działać w rzadszym interwału (maksymalnie raz na 24 godziny), dopóki nie poniesie postępu. Ponownie.  Jeśli uważasz, że zostały ustalone niezależnie od problemu, który był przyczyną indeksatora utknąć w pewnym momencie, można wykonać na żądanie uruchomić indeksatora, a jeśli to pomyślnie sprawia, że postęp, indeksator powróci do ustawionego interwału harmonogramu ponownie.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Harmonogram w portalu

Kreator importu danych w portalu umożliwia zdefiniowanie harmonogramu indeksatora w czasie tworzenia. Domyślnym ustawieniem harmonogramu jest **Co godzinę**, co oznacza, że indeksator jest uruchamiany raz po jego utworzeniu i uruchamia się ponownie co godzinę później.

Można zmienić ustawienie Harmonogram na **Raz,** jeśli nie chcesz, aby indeksator uruchamiał ponownie automatycznie lub **na Codzienne** uruchamianie raz dziennie. Ustaw go na **Niestandardowy,** jeśli chcesz określić inny interwał lub określoną przyszłą godzinę rozpoczęcia.

Po ustawieniu harmonogramu na **Niestandardowy**pojawiają się pola, które umożliwiają określenie **interwału** i **czasu rozpoczęcia (UTC).** Najkrótszy dozwolony przedział czasu wynosi 5 minut, a najdłuższy to 1440 minut (24 godziny).

   ![Ustawianie harmonogramu indeksatora w Kreatorze importu danych](media/search-howto-schedule-indexers/schedule-import-data.png "Ustawianie harmonogramu indeksatora w Kreatorze importu danych")

Po utworzeniu indeksatora można zmienić ustawienia harmonogramu za pomocą panelu Edycja indeksatora. Pola Harmonogram są takie same jak w Kreatorze importu danych.

   ![Ustawianie harmonogramu w panelu Edycja indeksatora](media/search-howto-schedule-indexers/schedule-edit.png "Ustawianie harmonogramu w panelu Edycja indeksatora")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Planowanie korzystania z interfejsów API REST

Harmonogram indeksatora można zdefiniować przy użyciu interfejsu API REST. Aby to zrobić, należy uwzględnić właściwość **harmonogram** podczas tworzenia lub aktualizowania indeksatora. Poniższy przykład przedstawia żądanie PUT, aby zaktualizować istniejący indeksator:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Wymagany jest parametr **interwału.** Interwał odnosi się do czasu między rozpoczęciem dwóch kolejnych wykonań indeksatora. Najmniejszy dozwolony interwał wynosi 5 minut; najdłuższy jest jeden dzień. Musi być sformatowany jako XSD "dayTimeDuration" wartość (ograniczony podzbiór [iso 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) wartość czasu trwania). Wzór do tego `P(nD)(T(nH)(nM))`jest: . Przykłady: `PT15M` co 15 `PT2H` minut, co 2 godziny.

Opcjonalny **startTime** wskazuje, kiedy zaplanowane wykonania powinny się rozpocząć. Jeśli zostanie pominięty, używany jest bieżący czas UTC. Ten czas może być w przeszłości, w którym to przypadku pierwsze wykonanie jest zaplanowane tak, jakby indeksator został uruchomiony w sposób ciągły od oryginalnego **startTime**.

Można również uruchomić indeksatora na żądanie w dowolnym momencie przy użyciu wywołania Uruchom indeksatora. Aby uzyskać więcej informacji na temat uruchamiania indeksatorów i ustawiania harmonogramów indeksatora, zobacz [Uruchamianie indeksatora,](https://docs.microsoft.com/rest/api/searchservice/run-indexer) [Pobierz indeksator](https://docs.microsoft.com/rest/api/searchservice/get-indexer)i [Indeksator aktualizacji](https://docs.microsoft.com/rest/api/searchservice/update-indexer) w odwołaniu interfejsu API REST.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Planowanie przy użyciu sdk .NET

Harmonogram indeksatora można zdefiniować przy użyciu narzędzia Azure Cognitive Search .NET SDK. Aby to zrobić, należy uwzględnić właściwość **harmonogram** podczas tworzenia lub aktualizowania indeksatora.

Poniższy przykład języka C# tworzy indeksatora przy użyciu wstępnie zdefiniowanego źródła danych i indeksu i ustawia jego harmonogram do uruchamiania raz dziennie, począwszy od 30 minut od teraz:

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
Jeśli parametr **harmonogramu** zostanie pominięty, indeksator zostanie uruchomiony tylko raz natychmiast po jego utworzeniu.

Parametr **startTime** można ustawić na czas w przeszłości. W takim przypadku pierwsze wykonanie jest zaplanowane tak, jakby indeksator był uruchomiony w sposób ciągły od czasu **startTime.**

Harmonogram jest zdefiniowany przy użyciu [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) klasy. **Konstruktor IndexingSchedule** wymaga parametru **interwału** określonego przy użyciu **timespan** obiektu. Najmniejsza dozwolona wartość interwału wynosi 5 minut, a największa to 24 godziny. Drugi parametr **startTime,** określony jako obiekt **DateTimeOffset,** jest opcjonalny.

.NET SDK umożliwia sterowanie operacjami indeksatora przy użyciu [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) klasy i jego [właściwości Indeksatory,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) która implementuje metody z interfejsu **IIndexersOperations.** 

Indeksatora można uruchomić na żądanie w dowolnym momencie przy użyciu jednej z metod [Uruchom](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)lub [RunWithHttpMessagesAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync)

Aby uzyskać więcej informacji na temat tworzenia, aktualizowania i uruchamiania indeksatorów, zobacz [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
