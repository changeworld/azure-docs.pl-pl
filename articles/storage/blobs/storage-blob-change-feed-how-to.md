---
title: Źródło danych zmian procesu w usłudze Azure Blob Storage (wersja zapoznawcza) | Dokumenty firmy Microsoft
description: Dowiedz się, jak przetwarzać dzienniki zamiennych w aplikacji klienckiej .NET
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74111856"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Źródło danych zmian procesu w usłudze Azure Blob Storage (wersja zapoznawcza)

Źródło danych change zawiera dzienniki transakcji wszystkich zmian, które występują w obiektach blob i metadanych obiektu blob na koncie magazynu. W tym artykule pokazano, jak odczytywać rekordy zdawania danych, korzystając z biblioteki procesora danych strumieniowych zmian obiektu blob.

Aby dowiedzieć się więcej o pliku danych o zmianach, zobacz [Zmienianie kanału informacyjnego w usłudze Azure Blob Storage (Wersja zapoznawcza)](storage-blob-change-feed.md).

> [!NOTE]
> Kanał zmian jest w publicznej wersji zapoznawczej i jest dostępny w regionach **westcentralus** i **westus2.** Aby dowiedzieć się więcej o tej funkcji wraz ze znanymi problemami i ograniczeniami, zobacz [Obsługa kanału informacyjnego zmiany w usłudze Azure Blob Storage](storage-blob-change-feed.md). Biblioteka procesora kanału informacyjnego zmian może ulec zmianie między teraz a kiedy ta biblioteka stanie się ogólnie dostępna.

## <a name="get-the-blob-change-feed-processor-library"></a>Pobierz bibliotekę procesora danych strumieniowych zmian obiektu blob

1. W programie Visual Studio `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` dodaj adres URL do źródeł pakietu NuGet. 

   Aby dowiedzieć się, jak to zrobić, zobacz [źródła pakietów](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. W Menedżerze pakietów NuGet znajdź pakiet **Microsoft.Azure.Storage.Changefeed** i zainstaluj go w projekcie. 

   Aby dowiedzieć się, jak to zrobić, zobacz [Znajdowanie i instalowanie pakietu](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

## <a name="connect-to-the-storage-account"></a>Łączenie się z kontem magazynu

Przeanalizuj parametry połączenia, wywołując metodę [CloudStorageAccount.TryParse.](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) 

Następnie utwórz obiekt, który reprezentuje magazyn obiektów blob na koncie magazynu, wywołując [metodę CloudStorageAccount.CreateCloudBlobClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>Inicjowanie źródła danych zmian

Dodaj następujące instrukcje przy użyciu górnej części pliku kodu. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Następnie należy utworzyć wystąpienie **ChangeFeed** klasy wywołując **GetContainerReference** metody. Przekaż nazwę kontenera zmienić podawania.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>Odczytywanie rekordów

> [!NOTE]
> Plik danych o zmianach jest niezmienną i tylko do odczytu jednostką na koncie magazynu. Dowolna liczba aplikacji może odczytywać i przetwarzać kanał zmian jednocześnie i niezależnie, dla własnego własnego dogodnego. Rekordy nie są usuwane z kanału informacyjnego zmian, gdy aplikacja je odczytuje. Stan odczytu lub iteracji każdego czytnika zużywającego jest niezależny i obsługiwany tylko przez aplikację.

Najprostszym sposobem odczytywania rekordów jest utworzenie wystąpienia **changefeedreader** klasy. 

W tym przykładzie iteruje wszystkie rekordy w zestawieniu zmian, a następnie drukuje do konsoli kilka wartości z każdego rekordu. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>Wznawianie odczytu rekordów z zapisanej pozycji

Możesz zapisać pozycję odczytu w pliku danych zmian i wznowić iterację rekordów w przyszłości. Stan iteracji kanału informacyjnego zmian można zapisać w dowolnym momencie za pomocą metody **ChangeFeedReader.SerializeState().** Stan jest **ciągiem** znaków i aplikacja może zapisać ten stan na podstawie projektu aplikacji (na przykład: do bazy danych lub pliku).

```csharp
    string currentReadState = processor.SerializeState();
```

Można kontynuować iteracji za pośrednictwem rekordów z ostatniego stanu, tworząc **ChangeFeedReader** przy użyciu **CreateChangeFeedReaderFromPointerAsync** metody.

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>Przetwarzanie strumienia rekordów

Możesz przetworzyć zmienić rekordy pliku danych po ich nadejściu. Zobacz [Dane techniczne](storage-blob-change-feed.md#specifications).

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>Odczytywanie rekordów w zakresie czasowym

Kanał informacyjny zmian jest zorganizowany w segmenty godzinowe na podstawie czasu zmiany zdarzenia. Zobacz [Dane techniczne](storage-blob-change-feed.md#specifications). Można odczytywać rekordy z segmentów kanału informacyjnego zmian, które mieszczą się w określonym zakresie czasowym.

W tym przykładzie pobiera czasy rozpoczęcia wszystkich segmentów. Następnie iteruje przez tę listę, aż czas rozpoczęcia jest albo poza czas ostatniego segmentu materiałów eksploatacyjnych lub poza czas zakończenia żądanego zakresu. 

### <a name="selecting-segments-for-a-time-range"></a>Wybieranie segmentów dla przedziału czasu

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>Odczytywanie rekordów w segmencie

Można odczytywać rekordy z poszczególnych segmentów lub zakresów segmentów.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>Odczytywanie rekordów od czasu

Można odczytać rekordy kanału informacyjnego zmian od segmentu początkowego do końca. Podobnie jak w rzeczywistości odczytywanie rekordów w zakresie czasowym, można wyświetlić listę segmentów i wybrać segment, z którego można rozpocząć iterację.

W tym przykładzie pobiera [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) pierwszego segmentu do przetworzenia.

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

W tym przykładzie przetwarza zmienić rekordy pliku danych, począwszy od [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) segmentu początkowego.

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> Segment może mieć dzienniki kanału informacyjnego zmian w jednym lub kilku *fragmentachFilePath*. W przypadku wielu *chunkFilePath* system wewnętrznie podzielony rekordy na wiele fragmentów do zarządzania przepływnością publikowania. Jest gwarantowana, że każda partycja segmentu będzie zawierać zmiany dla wzajemnie wykluczających się obiektów blob i mogą być przetwarzane niezależnie bez naruszania kolejności. Można użyć **ChangeFeedSegmentShardReader** klasy do iteracji za pośrednictwem rekordów na poziomie niezależnego fragmentu, jeśli jest to najbardziej efektywne dla scenariusza.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o dziennikach z plikami danych zmian. Zobacz [Zmienianie źródła danych w usłudze Azure Blob Storage (wersja zapoznawcza)](storage-blob-change-feed.md)
