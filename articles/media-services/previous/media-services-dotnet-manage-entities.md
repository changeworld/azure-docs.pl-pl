---
title: Zarządzanie zasobami i powiązanymi jednostkami za pomocą usługi Media Services .NET SDK
description: Dowiedz się, jak zarządzać zasobami i powiązanymi jednostkami za pomocą zestawu Media Services SDK dla platformy .NET.
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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58802681"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Zarządzanie zasobami i powiązanymi jednostkami za pomocą usługi Media Services .NET SDK
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

W tym temacie pokazano, jak zarządzać jednostkami usługi Azure Media Services przy użyciu platformy .NET.

Począwszy od 1 kwietnia 2017 roku, wszystkie rekordy zadań na Twoim koncie, które są starsze niż 90 dni, będą automatycznie usuwane wraz ze skojarzonymi rekordami zadań podrzędnych nawet wtedy, gdy całkowita liczba rekordów jest mniejsza niż maksymalny limit przydziału. Na przykład 1 kwietnia 2017 r. wszystkie rekordy zadań na Twoim koncie, które są starsze niż 31 grudnia 2016 r. zostaną automatycznie usunięte. Jeśli chcesz zarchiwizować informacje zadania lub zadania podrzędnego, można użyć kodu opisanego w tym temacie.

## <a name="prerequisites"></a>Wymagania wstępne

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Pobierz odwołanie do zasobu
Często zadanie jest aby odwołać się do istniejącego zasobu w usłudze Media Services. Poniższy przykład kodu pokazuje, jak można uzyskać odwołanie do zasobu z kolekcji zasobów na serwerze obiektu kontekstu, w oparciu o identyfikator zasobu Poniższy przykład kodu używa zapytania Linq można pobrać odwołania do istniejącego obiektu IAsset.

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
Wraz z rozwojem liczby zasobów, które masz w magazynie jest przydatne wyświetlić listę zasobów. W poniższym przykładzie kodu pokazano, jak do iterowania po kolekcji zasobów na obiekt kontekstu serwera. Z każdego zasobu w przykładzie kodu zapisuje także niektóre wartości właściwości do konsoli. Na przykład każdy element zawartości może zawierać wiele plików multimedialnych. Przykład kodu zapisuje się wszystkie pliki skojarzone z każdym elementem zawartości.

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

## <a name="get-a-job-reference"></a>Pobierz odwołanie do zadania

Podczas pracy z przetwarzaniem zadań w kod usługi Media Services, często musisz odwołać się do istniejącego zadania na podstawie identyfikatora. W poniższym przykładzie kodu pokazano, jak można pobrać odwołania do obiektu IJob z kolekcji zadań.

Może być konieczne odwołać zadania podczas uruchamiania zadania kodowania długotrwałych i trzeba sprawdzić stan zadania w wątku. W takich sytuacjach w przypadku zwrotu metody z wątku, należy pobrać odświeżony odwołanie do zadania.

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
Jest ważnym zadaniem powiązanych do listy zasobów za pomocą ich skojarzone zadania w usłudze Media Services. Poniższy przykład kodu pokazuje sposób wyświetlenia listy każdy obiekt IJob i następnie dla każdego zadania, wyświetla właściwości o zadaniu, wszystkie powiązane zadania, wszystkie dane wejściowe, zasoby i wszystkie zasoby danych wyjściowych. Kod w tym przykładzie może być przydatne w przypadku wielu innych zadań. Na przykład jeśli chcesz wyświetlić listę zasobów danych wyjściowych z co najmniej jedno zadanie kodowania, które były uruchomione poprzednio, ten kod przedstawia sposób uzyskiwać dostęp do zasobów danych wyjściowych. W przypadku odwołania do elementu zawartości wyjściowej, możesz następnie dostarczanie zawartości do innych użytkowników lub aplikacji ją pobrać lub udostępniając adresów URL. 

Aby uzyskać więcej informacji na temat opcji dostarczania zasobów, zobacz [dostarczania zasobów za pomocą usługi Media Services SDK dla platformy .NET](media-services-deliver-streaming-content.md).

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
W usłudze Media Services można zdefiniować zasadę dostępu dla zasobu lub jego pliki. Zasady dostępu definiuje uprawnienia dla pliku lub zasobu (jakiego rodzaju dostępu i czas trwania). W kodzie usługi Media Services zwykle zdefiniować zasadę dostępu, tworząc obiekt IAccessPolicy i kojarzenie go z istniejącego zasobu. Następnie możesz utworzyć obiekt ILocator, który umożliwia dostarczanie bezpośredni dostęp do zasobów w usłudze Media Services. Dołączona ta seria artykułów dokumentacji projektu programu Visual Studio zawiera kilka przykładów kodu, które pokazują, jak utworzyć i przypisać zasady dostępu i lokalizatorów zasobów.

Poniższy przykład kodu ilustruje sposób wyświetlenia listy wszystkich zasad dostępu na serwerze i zawiera typ skojarzone z każdą uprawnienia. Jest innym wygodny sposób, aby wyświetlić zasady dostępu, aby wyświetlić listę wszystkich obiektów ILocator na serwerze, a następnie dla każdego lokalizatora, możesz wyświetlić listę swoich zasad dostępu skojarzonych za pomocą jego właściwości AccessPolicy.

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
    
## <a name="limit-access-policies"></a>Limit zasad dostępu 

>[!NOTE]
> Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). 

Na przykład można utworzyć zestaw ogólnych zasad następującym kodem, który można uruchomić tylko jeden raz w aplikacji. Identyfikatory Zaloguj się do pliku dziennika w celu późniejszego użycia:

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

Następnie można użyć identyfikatorów istniejących w kodzie następująco:

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

## <a name="list-all-locators"></a>Lista wszystkie Lokalizatory
Lokalizator jest adres URL, który zapewnia bezpośrednią ścieżkę do dostępu do zasobów oraz uprawnień do elementu zawartości zgodnie z definicją zasad dostępu skojarzone lokalizatora. Każdy zasób może mieć kolekcji ILocator obiektów skojarzonych z nim na jego właściwość lokalizatorów. Kontekstu serwera ma również kolekcji lokalizatorów, która zawiera wszystkie lokalizatory.

Poniższy przykład kodu wyświetla wszystkie lokalizatory na serwerze. Dla każdego lokalizatora pokazuje identyfikator powiązane zasoby i dostęp do zasad. Wyświetla również typ uprawnień, datę wygaśnięcia i pełną ścieżkę do elementu zawartości.

Należy pamiętać, że ścieżka lokalizatora do elementu zawartości tylko podstawowy adres URL do elementu zawartości. Aby utworzyć bezpośrednią ścieżkę do poszczególnych plików, które użytkownik lub aplikacja może przejść do, kod należy dodać ścieżki konkretnego pliku do ścieżki lokalizatora. Aby uzyskać więcej informacji na temat jak to zrobić, zobacz temat [dostarczania zasobów za pomocą usługi Media Services SDK dla platformy .NET](media-services-deliver-streaming-content.md).

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

## <a name="enumerating-through-large-collections-of-entities"></a>Wyliczanie dużych kolekcjach jednostek
Podczas wykonywania zapytań dotyczących jednostek, istnieje limit 1000 jednostek zwrócona w tym samym czasie, ponieważ publiczny v2 REST ogranicza wyniki zapytania do 1000 wyników. Należy użyć Skip i Take podczas wyliczania za pośrednictwem dużych kolekcjach jednostek. 

Używając następującej funkcji w pętli wszystkich zadań w podanego konta usługi Media Services. Usługa Media Services zwraca 1000 zadań w kolekcji zadań. Funkcja korzysta z Skip i Take, aby upewnić się, że wszystkie zadania są wyliczane (w przypadku, gdy masz więcej niż 1000 zadań na Twoim koncie).

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

## <a name="delete-an-asset"></a>Usuń zasób
Poniższy przykład usuwa element zawartości.

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

## <a name="delete-a-job"></a>Usuń zadanie
Aby usunąć zadanie, możesz sprawdzić stan zadania, zgodnie z instrukcjami w właściwości stanu. Można usunąć zadania, które zostały zakończone lub anulowane, podczas zadania, które znajdują się w niektórych innych państw, takie jak umieszczonych w kolejce, zaplanowane lub przetwarzania, należy najpierw anulować, a następnie można je usunąć.

Poniższy przykład kodu przedstawia metodę usuwania zadania, zaznaczając stany zadań, a następnie usuwając, gdy stan to zostało zakończone lub anulowane. Ten kod zależy od poprzedniej sekcji, w tym temacie w celu uzyskania odwołanie do zadania: Pobierz odwołanie do zadania.

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


## <a name="delete-an-access-policy"></a>Usuń zasady dostępu
Poniższy przykład kodu pokazuje, jak odwołać się do zasad dostępu na podstawie zasad Id, a następnie usunąć zasady.

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

