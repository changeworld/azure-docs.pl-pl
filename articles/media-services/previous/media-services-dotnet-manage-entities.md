---
title: Zarządzanie zasobami i jednostek powiązanych z programem Media Services zestawu .NET SDK
description: Dowiedz się, jak zarządzać zasobów i powiązanych jednostek przy użyciu zestawu SDK usługi multimediów dla platformy .NET.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juliako
ms.openlocfilehash: af5baf3444196e5a0e8412d9ab4f019fdccb033e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Zarządzanie zasobami i jednostek powiązanych z programem Media Services zestawu .NET SDK
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

W tym temacie przedstawiono sposób zarządzania jednostki usługi Azure Media Services z platformy .NET. 

>[!NOTE]
> Począwszy od 1 kwietnia 2017 roku, wszystkie rekordy zadań na Twoim koncie, które są starsze niż 90 dni, będą automatycznie usuwane wraz ze skojarzonymi rekordami zadań podrzędnych nawet wtedy, gdy całkowita liczba rekordów jest mniejsza niż maksymalny limit przydziału. Na przykład na 1 kwietnia 2017 dowolnego rekordu zadania konta starsze niż 31 grudnia 2016 r., zostaną automatycznie usunięte. Jeśli chcesz zarchiwizować informacje zadania lub zadanie, można użyć kodu opisanych w tym temacie.

## <a name="prerequisites"></a>Wymagania wstępne

Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Pobierz odwołanie do zasobu
Częstych zadań jest odwołać się do istniejącego zasobu w usłudze Media Services. Poniższy przykład kodu pokazuje, jak można uzyskać odwołania do zasobów z kolekcji zasobów na serwerze obiektu kontekstu, w oparciu o identyfikator zasobu Poniższy przykład kodu wykorzystuje zapytania Linq, aby pobrać odwołanie do istniejącego obiektu IAsset.

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

## <a name="list-all-assets"></a>Wyświetl listę wszystkich zasobów
Miarę zwiększania się liczby zasobów, do których masz w magazynie, jest przydatne do listy zasobów. Poniższy przykład kodu pokazuje, jak do iterowania po kolekcji zasobów obiektu context z serwera. Z każdym zawartości przykładowy kod zapisuje także niektóre z jej wartości właściwości do konsoli. Na przykład poszczególnych zasobów może zawierać wiele plików multimedialnych. Przykładowy kod zapisuje się wszystkie pliki skojarzone z każdym zasobów.

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

Podczas pracy z przetwarzania zadań w kodzie usługi Media Services, często konieczne jest odwołać się do istniejącego zadania na podstawie identyfikatora. Poniższy przykład kodu pokazuje, jak można pobrać odwołania do obiektu IJob z kolekcji zadań.

Konieczne może być odwołać zadania podczas uruchamiania długotrwałe zadania kodowania i muszą sprawdzać stan zadania w wątku. W takich sytuacjach gdy metoda zwróci wartość wprowadzanych przez wątek należy pobrać odświeżyć odwołanie do zadania.

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

## <a name="list-jobs-and-assets"></a>Lista zadań i zasoby
Jest ważnym zadaniem powiązane do listy zasobów z ich zadaniem skojarzony w usłudze Media Services. Poniższy przykład kodu pokazuje sposób wyświetlania każdego obiektu IJob i następnie dla każdego zadania zawiera właściwości o zadaniu, wszystkie powiązane zadania, wprowadź wszystkie zasoby i wszystkie zasoby danych wyjściowych. Kod w tym przykładzie może być przydatne w przypadku wielu innych zadań. Na przykład jeśli mają być wyświetlone zasoby danych wyjściowych z co najmniej jednego zadania kodowania, które został przeprowadzony wcześniej, ten kod przedstawia sposób uzyskać dostępu do zasobów danych wyjściowych. Gdy odwołanie do zasobu danych wyjściowych, możesz następnie dostarczania zawartości do innych użytkowników lub aplikacji ją pobrać lub udostępniając adresów URL. 

Aby uzyskać więcej informacji na temat opcji dostarczania zasoby, zobacz [dostarczania zasobów przy użyciu zestawu SDK usługi multimediów dla platformy .NET](media-services-deliver-streaming-content.md).

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

## <a name="list-all-access-policies"></a>Wyświetl listę wszystkich zasad dostępu
W usłudze Media Services można zdefiniować zasadę dostępu zasobów lub jego pliki. Zasady dostępu definiuje uprawnienia do pliku lub elementu zawartości (typ dostępu i czas trwania). W kodzie usługi Media Services zwykle zdefiniować zasadę dostępu przez utworzenie obiektu IAccessPolicy i kojarzenie go z istniejących zasobów. Następnie można utworzyć obiektu ILocator, który umożliwia zapewniać bezpośredni dostęp do zasobów w usłudze Media Services. Projekt programu Visual Studio dołączona ta seria dokumentacja zawiera kilka przykładów kodu, które pokazują, jak utworzyć i przypisać zasady dostępu i lokalizatorów do zasobów.

Poniższy przykładowy kod przedstawia sposób wyświetlania wszystkich zasad dostępu na serwerze i zawiera typ skojarzone z każdym z nich uprawnienia. Jest inny wygodny sposób, aby wyświetlić zasady dostępu do wyświetlenia wszystkich obiektów ILocator na serwerze, a następnie dla każdego lokalizatora można wyświetlić listę jego zasad dostępu skojarzonych za pomocą jego właściwość AccessPolicy.

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

Na przykład można utworzyć zestawu ogólnych zasad z następującym kodem, który można uruchomić tylko raz w aplikacji. Identyfikatory Zaloguj się do pliku dziennika w celu późniejszego użycia:

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

Następnie należy użyć istniejącego identyfikatorów w kodzie w następujący sposób:

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

## <a name="list-all-locators"></a>Wyświetl listę wszystkich lokalizatorów
Lokalizator jest adres URL, który zapewnia bezpośrednią ścieżkę na dostęp do zasobów, oraz uprawnienia do elementu zawartości, zgodnie z definicją zasad dostępu skojarzonych lokalizatora. Każdy zasobów może mieć kolekcji ILocator obiektów skojarzonych z nim na jego właściwość lokalizatorów. Kontekst serwera ma również lokalizatorów kolekcję, która zawiera wszystkie lokalizatorów.

Poniższy przykład kodu wyświetla listę wszystkich lokalizatorów na serwerze. Dla każdego lokalizatora zawiera identyfikator pokrewnych zasad dostępu i zasobów. Wyświetla również typ uprawnień, datę wygaśnięcia i Pełna ścieżka do elementu zawartości.

Należy pamiętać, że lokalizatora ścieżki do zawartości tylko podstawowy adres URL do elementu zawartości. Aby utworzyć bezpośrednią ścieżkę do poszczególnych plików, które użytkownik lub aplikacja może przejść do, kodu musi dodać ścieżkę do określonego pliku do lokalizatora ścieżki. Aby uzyskać więcej informacji, jak to zrobić, zobacz temat [dostarczania zasobów przy użyciu zestawu SDK usługi multimediów dla platformy .NET](media-services-deliver-streaming-content.md).

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

## <a name="enumerating-through-large-collections-of-entities"></a>Wyliczanie za pośrednictwem dużych kolekcjach jednostek
Podczas wykonywania zapytania jednostek, istnieje limit 1000 jednostek zwracane w tym samym czasie, ponieważ publiczny v2 REST ogranicza wyniki zapytania do 1000 wyników. Należy użyć Skip i Take podczas wyliczania dużych kolekcjach jednostek. 

Następująca funkcja w pętli wszystkich zadań w podanego konta usługi Media Services. Usługa Media Services zwraca 1000 zadań w kolekcji zadań. Funkcja korzysta z Skip i Take, aby upewnić się, że wszystkie zadania są wyliczyć (w przypadku, gdy masz więcej niż 1000 zadania konta).

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
Aby usunąć zadania, należy sprawdzić stan zadania określonych we właściwości stanu. Zadania, które zostały zakończone lub anulowane mogą zostać usunięte podczas zadania, które znajdują się w niektórych innych stanów, np. w kolejce, według harmonogramu lub przetwarzania, należy najpierw anulować, a następnie można go usunąć.

Poniższy przykład kodu pokazuje metoda usuwania zadania sprawdzania stany zadań, a następnie usuwając podczas stanu zostało zakończone lub anulowane. Ten kod jest zależny od poprzedniej sekcji, w tym temacie przeznaczonym dla pobierania odwołanie do zadania: odwołać zadania.

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


## <a name="delete-an-access-policy"></a>Usunięcie zasad dostępu
Poniższy przykładowy kod przedstawia sposób pobrać odwołanie do zasad dostępu na podstawie zasad Id, a następnie usunąć zasady.

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

