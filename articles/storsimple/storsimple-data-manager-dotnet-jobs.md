---
title: Korzystanie z zestawu SDK .NET dla programu Microsoft Azure StorSimple Data zadań | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć zestawu .NET SDK w celu uruchomienia zadania Menedżer StorSimple danych
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: d15a5cbda2f0c2a363b40e94c38fed6631aa81b5
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27928240"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Użyj zestawu SDK programu .net do zainicjowania transformacji danych

## <a name="overview"></a>Przegląd

W tym artykule opisano sposób korzystania z funkcji przekształcania danych w usłudze Menedżer StorSimple danych do przekształcania danych urządzenia StorSimple. Przekształcone dane są następnie używane przez innych usług platformy Azure w chmurze.

Można uruchomić zadania przekształcania danych na dwa sposoby:

 - Korzystanie z zestawu SDK dla platformy .NET
 - Użyj elementu runbook usługi Automatyzacja Azure
 
 Ten artykuł zawiera szczegóły dotyczące sposobu tworzenia przykładowej aplikacji konsoli .NET do inicjowania zadania przekształcania danych i śledzenie go do ukończenia. Aby dowiedzieć się więcej o tym, jak zainicjować transformacji danych przez automatyzację, przejdź do [runbook usługi Automatyzacja Azure używana do zadania przekształcania danych wyzwalacza](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:
*   Komputer z systemem:

    - Visual Studio 2012, 2013, 2015, or 2017.

    - Azure Powershell. [Pobieranie programu Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Definicji zadania poprawnie skonfigurowana w StorSimple Data Manager w grupie zasobów.
*   Wszystkie wymagane biblioteki dll. Pobierz te biblioteki dll z [repozytorium GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)skrypt z repozytorium GitHub.

## <a name="step-by-step-procedure"></a>Procedury krok po kroku

Wykonaj poniższe kroki, aby użyć .NET w celu uruchomienia zadania przekształcania danych.

1. Aby pobrać parametry konfiguracji, wykonaj następujące czynności:
    1. Pobierz `Get-ConfigurationParams.ps1` ze skryptu repozytorium GitHub w `C:\DataTransformation` lokalizacji.
    1. Uruchom `Get-ConfigurationParams.ps1` skryptu z repozytorium GitHub. Wpisz następujące polecenie:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Można przekazać wartości ActiveDirectoryKey i AppName.

2. Ten skrypt generuje następujące wartości:
    * Identyfikator klienta
    * Identyfikator dzierżawy
    * Kluczy Active Directory (tak samo, jak to objęte powyżej)
    * Identyfikator subskrypcji

        ![Dane wyjściowe skryptu parametry konfiguracji](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Za pomocą programu Visual Studio 2012, 2013 lub 2015, Utwórz aplikację konsolową C# .NET.

    1. Uruchom **programu Visual Studio 2012/2013/2015**.
    1. Wybierz **Plik > Nowy > Projekt**.

        ![Tworzenie projektu 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Wybierz **zainstalowane > Szablony > Visual C# > Aplikacja Konsolowa**.
    3. Wprowadź **DataTransformationApp** dla **nazwa**.
    4. Wybierz **C:\DataTransformation** dla **lokalizacji**.
    6. Kliknij przycisk **OK**, aby utworzyć projekt.

        ![Utwórz projekt 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4.  Teraz Dodaj wszystkie biblioteki dll w [folder biblioteki dll](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) jako **odwołania** w projekcie, który został utworzony. Aby dodać pliki dll, wykonaj następujące czynności:

    1. W programie Visual Studio, przejdź do **Widok > Eksploratora rozwiązań**.
    2. Kliknij strzałkę w lewo projektu aplikacji przekształcania danych. Kliknij przycisk **odwołania** , a następnie kliknij prawym przyciskiem myszy, aby **Dodaj odwołanie**.
    
        ![Dodawanie biblioteki DLL 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

    3. Przejdź do lokalizacji folderu pakietów, wybierz wszystkie biblioteki dll i kliknij przycisk **Dodaj**, a następnie kliknij przycisk **OK**.

        ![Dodawanie biblioteki DLL 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Dodaj następujące instrukcje **using** do pliku źródłowego (Program.cs) w projekcie.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Poniższy kod inicjuje wystąpienie zadania przekształcania danych. Dodaj ją w **metody Main**. Zastąp wartości parametrów konfiguracji uzyskany wcześniej. Podłącz wartości **Nazwa grupy zasobów** i **ResourceName**. **ResourceGroupName** jest skojarzony z danych Menedżer StorSimple na którym skonfigurowano definicji zadania. **ResourceName** to nazwa usługi Menedżer StorSimple danych.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. Określ parametry, z których trzeba uruchomić definicji zadania

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (OR)

    Jeśli chcesz zmienić parametrów definicji zadania w czasie wykonywania, Dodaj następujący kod:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. Po zainicjowaniu Dodaj następujący kod do wyzwalania zadania przekształcania danych w definicji zadania. Podłącz odpowiednie **Nazwa definicji zadania**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Po wklejeniu kodu, Skompiluj rozwiązanie. Poniżej przedstawiono zrzut ekranu fragmentu kodu, aby zainicjować wystąpienia zadania przekształcania danych.

   ![Fragment kodu zainicjować zadania przekształcania danych](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. To zadanie przekształcenia danych, zgodny z katalogiem głównym i pliku filtrów w obrębie woluminu StorSimple i umieszcza udziału określonego kontenera/pliku. Gdy plik jest przekształcana, komunikat zostanie dodane do kolejki magazynu (w ramach tego samego konta magazynu udziału pliku/kontenera) o nazwie identycznej z nazwą definicji zadania. Ten komunikat może służyć jako wyzwalacz zainicjować dalszego przetwarzania pliku.

10. Po wyzwoleniu zadania można użyć następującego kodu można śledzić zadania do wykonania. Nie jest to konieczne, aby dodać ten kod do uruchomienia zadania.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
 Poniżej przedstawiono zrzut ekranu przedstawiający przykładowy całego kodu użyty do wyzwolenia zadania przy użyciu platformy .NET.

 ![Pełna fragment kodu do wyzwalania zadania .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Następne kroki

[Użyj Menedżera danych StorSimple interfejsu użytkownika Służącego do przekształcenia danych](storsimple-data-manager-ui.md).
