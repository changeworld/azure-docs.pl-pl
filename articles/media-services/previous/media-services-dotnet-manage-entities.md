---
title: Zarządzanie zasobami i jednostkami pokrewne za pomocą sdk usługi Media Services .NET
description: Dowiedz się, jak zarządzać zasobami i encjami pokrewne za pomocą sdk usługi Media Services dla platformy .NET.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: a686465b0006c2e9aac6e06cb4ab12d30921e8c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251143"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Zarządzanie zasobami i jednostkami pokrewne za pomocą sdk usługi Media Services .NET
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [Reszta](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

W tym temacie pokazano, jak zarządzać jednostkami usługi Azure Media Services za pomocą platformy .NET.

Począwszy od 1 kwietnia 2017 roku, wszystkie rekordy zadań na Twoim koncie, które są starsze niż 90 dni, będą automatycznie usuwane wraz ze skojarzonymi rekordami zadań podrzędnych nawet wtedy, gdy całkowita liczba rekordów jest mniejsza niż maksymalny limit przydziału. Na przykład 1 kwietnia 2017 r. każdy rekord zadania na koncie starszym niż 31 grudnia 2016 r. zostanie automatycznie usunięty. Jeśli chcesz zarchiwizować informacje o zadaniu/zadaniu, możesz użyć kodu opisanego w tym temacie.

## <a name="prerequisites"></a>Wymagania wstępne

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Uzyskaj odwołanie do zasobu
Częstym zadaniem jest uzyskanie odwołania do istniejącego zasobu w umiań Multi Services. Poniższy przykład kodu pokazuje, jak można uzyskać odwołanie do zasobu z kolekcji Zasoby w obiekcie kontekstu serwera, na podstawie identyfikatora zasobu. Poniższy przykład kodu używa kwerendy Linq, aby uzyskać odwołanie do istniejącego obiektu IAsset.

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>Lista wszystkich zasobów
Wraz ze wzrostem liczby zasobów w magazynie warto wyświetlić listę zasobów. W poniższym przykładzie kodu pokazano, jak iterować za pośrednictwem kolekcji zasoby w obiekcie kontekstu serwera. W przypadku każdego zasobu przykład kodu zapisuje również niektóre jego wartości właściwości do konsoli. Na przykład każdy zasób może zawierać wiele plików multimedialnych. Przykład kodu zapisuje wszystkie pliki skojarzone z każdym zasobem.

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>Uzyskaj informacje o pracy

Podczas pracy z przetwarzania zadań w kodzie usługi Media Services, często trzeba uzyskać odwołanie do istniejącego zadania na podstawie identyfikatora. Poniższy przykład kodu pokazuje, jak uzyskać odwołanie do obiektu IJob z jobs kolekcji.

Może być konieczne uzyskanie odwołania do zadania podczas uruchamiania zadania kodowania długotrwałego i trzeba sprawdzić stan zadania w wątku. W takich przypadkach, gdy metoda zwraca z wątku, należy pobrać odświeżone odwołanie do zadania.

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>Lista zadań i zasobów
Ważnym powiązanym zadaniem jest wyliowanie zasobów ze skojarzonym zadaniem w umiań Media Services. Poniższy przykład kodu pokazuje, jak wyświetlić listę każdego obiektu IJob, a następnie dla każdego zadania, wyświetla właściwości dotyczące zadania, wszystkie powiązane zadania, wszystkie zasoby wejściowe i wszystkie zasoby wyjściowe. Kod w tym przykładzie może być przydatne dla wielu innych zadań. Na przykład jeśli chcesz wyświetlić listę zasobów wyjściowych z jednego lub więcej zadań kodowania, które uruchomiono wcześniej, ten kod pokazuje, jak uzyskać dostęp do zasobów wyjściowych. Jeśli masz odwołanie do zasobu wyjściowego, możesz dostarczyć zawartość innym użytkownikom lub aplikacjom, pobierając ją lub udostępniając adresy URL. 

Aby uzyskać więcej informacji na temat opcji dostarczania zasobów, zobacz [Dostarczanie zasobów za pomocą sdk usług multimedialnych dla platformy .NET](media-services-deliver-streaming-content.md).

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>Lista wszystkich zasad dostępu
W umieniu Multi services można zdefiniować zasady dostępu dla zasobu lub jego plików. Zasady dostępu definiują uprawnienia do pliku lub zasobu (jaki typ dostępu i czas trwania). W kodzie usługi Media Services zazwyczaj definiujesz zasady dostępu, tworząc obiekt IAccessPolicy, a następnie kojarząc go z istniejącym zasobem. Następnie należy utworzyć obiekt ILocator, który umożliwia bezpośredni dostęp do zasobów w umiaście w umiań. Projekt programu Visual Studio, który towarzyszy tej serii dokumentacji zawiera kilka przykładów kodu, które pokazują, jak tworzyć i przypisywać zasady dostępu i lokalizatory do zasobów.

Poniższy przykład kodu pokazuje, jak wyświetlić listę wszystkich zasad dostępu na serwerze i pokazuje typ uprawnień skojarzonych z każdym z nich. Innym przydatnym sposobem wyświetlania zasad dostępu jest lista wszystkich obiektów ILocator na serwerze, a następnie dla każdego lokalizatora, można wyświetlić jego skojarzone zasady dostępu przy użyciu jego AccessPolicy właściwości.

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>Zgraj zasady dostępu 

>[!NOTE]
> Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). 

Na przykład można utworzyć ogólny zestaw zasad z następującym kodem, który uruchamiałby tylko jeden raz w aplikacji. Identyfikatory można rejestrować w pliku dziennika w celu późniejszego użycia:

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

Następnie można użyć istniejących identyfikatorów w kodzie w ten sposób:

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>Lista wszystkich lokalizatorów
Lokalizator to adres URL, który zapewnia bezpośrednią ścieżkę dostępu do zasobu wraz z uprawnieniami do zasobu zdefiniowanymi przez skojarzone z lokalizatorem zasady dostępu. Każdy zasób może mieć kolekcję obiektów ILocator skojarzonych z nim na jego lokalizatorów właściwości. Kontekst serwera ma również kolekcję lokalizatorów, która zawiera wszystkie lokalizatory.

Poniższy przykład kodu zawiera listę wszystkich lokalizatorów na serwerze. Dla każdego lokalizatora pokazuje identyfikator powiązanego zasobu i zasad dostępu. Wyświetla również typ uprawnień, datę wygaśnięcia i pełną ścieżkę do zasobu.

Należy zauważyć, że ścieżka lokalizatora do zasobu jest tylko podstawowym adresem URL zasobu. Aby utworzyć bezpośrednią ścieżkę do poszczególnych plików, do których użytkownik lub aplikacja może przeglądać, kod musi dodać określoną ścieżkę pliku do ścieżki lokalizatora. Aby uzyskać więcej informacji na temat tego, zobacz temat [Dostarczanie zasobów za pomocą sdk usług multimedialnych dla platformy .NET](media-services-deliver-streaming-content.md).

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>Wyliczanie dużych zbiorów jednostek
Podczas wykonywania zapytań jednostek istnieje limit 1000 jednostek zwracanych jednocześnie, ponieważ publiczna rest w wersji 2 ogranicza wyniki kwerendy do 1000 wyników. Należy użyć Skip i Take podczas wyliczania za pośrednictwem dużych kolekcji jednostek. 

Następująca funkcja pętli za pośrednictwem wszystkich zadań na koncie usługi Media Services dostarczone. Usługi Media Services zwracają 1000 zadań w kolekcji zadań. Funkcja korzysta z Pomiń i Weź, aby upewnić się, że wszystkie zadania są wyliczone (w przypadku, gdy masz więcej niż 1000 zadań na koncie).

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>Usuwanie zasobu
Poniższy przykład usuwa zasób.

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>Usuwanie zadania
Aby usunąć zadanie, należy sprawdzić stan zadania, jak wskazano we właściwości Państwo. Zadania, które zostały zakończone lub anulowane, można usunąć, podczas gdy zadania, które znajdują się w niektórych innych stanach, takich jak kolejkowane, zaplanowane lub przetwarzania, muszą zostać anulowane najpierw, a następnie można je usunąć.

Poniższy przykład kodu przedstawia metodę usuwania zadania przez sprawdzanie stanów zadań, a następnie usuwanie po zakończeniu lub anulowaniu stanu. Ten kod zależy od poprzedniej sekcji w tym temacie do uzyskania odwołania do zadania: Pobierz odwołanie do zadania.

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>Usuwanie zasad dostępu
Poniższy przykład kodu pokazuje, jak uzyskać odwołanie do zasad dostępu na podstawie identyfikatora zasad, a następnie usunąć zasady.

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

