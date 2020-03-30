---
title: Używanie pliku .NET SDK dla zadań programu Microsoft Azure StorSimple Data Manager
description: Dowiedz się, jak uruchamiać zadania programu StorSimple Data Manager za pomocą sdk .NET
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b7cf1d3b9d4a9d751348c4792f904062b00ac104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76270724"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Inicjowanie przekształcania danych za pomocą zestawu SDK .NET

## <a name="overview"></a>Omówienie

W tym artykule wyjaśniono, jak można użyć funkcji przekształcania danych w ramach usługi StorSimple Data Manager do przekształcania danych urządzenia StorSimple. Przekształcone dane są następnie używane przez inne usługi platformy Azure w chmurze.

Zadanie przekształcania danych można uruchomić na dwa sposoby:

- Korzystanie z zestawu SDK dla platformy .NET
- Korzystanie z podręcznika azure automation
 
  W tym artykule opisano sposób tworzenia przykładowej aplikacji konsoli .NET w celu zainicjowania zadania przekształcania danych, a następnie śledzenia go do ukończenia. Aby dowiedzieć się więcej o inicjowaniu transformacji danych za pośrednictwem automatyzacji, przejdź do [artykułu Używanie łańczy wiązań usługi Azure Automation w celu wyzwalania zadań przekształcania danych.](storsimple-data-manager-job-using-automation.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:
*   Komputer z uruchomionym komputerem:

    - Visual Studio 2012, 2013, 2015 lub 2017.

    - Azure Powershell. [Pobierz program Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Poprawnie skonfigurowana definicja zadania w Menedżerze danych StorSimple w grupie zasobów.
*   Wszystkie wymagane biblioteki DLL. Pobierz te biblioteki DLL z [repozytorium GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)skrypt z repozytorium GitHub.

## <a name="step-by-step-procedure"></a>Procedura krok po kroku

Wykonaj następujące kroki, aby użyć platformy .NET do uruchomienia zadania przekształcania danych.

1. Aby pobrać parametry konfiguracji, wykonaj następujące czynności:
    1. Pobierz `Get-ConfigurationParams.ps1` go ze skryptu repozytorium `C:\DataTransformation` GitHub w lokalizacji.
    1. Uruchom `Get-ConfigurationParams.ps1` skrypt z repozytorium GitHub. Wpisz następujące polecenie:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Można przekazać w dowolnych wartościach activedirectorykey i AppName.

2. Ten skrypt wyprowadza następujące wartości:
    * Identyfikator klienta
    * Identyfikator dzierżawy
    * Klucz usługi Active Directory (taki sam jak wprowadzony powyżej)
    * Identyfikator subskrypcji

        ![Dane wyjściowe skryptu parametry konfiguracji](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Korzystając z programu Visual Studio 2012, 2013 lub 2015, utwórz aplikację konsoli języka C# .NET.

    1. Uruchom **program Visual Studio 2012/2013/2015**.
    1. Wybierz pozycję **Plik > Nowy > Projekt**.

        ![Tworzenie projektu 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Wybierz **pozycję Zainstalowane szablony > > aplikacji konsoli > Visual C#**.
    3. Wprowadź **DataTransformationApp** dla **nazwy**.
    4. Wybierz **C:\DataTransformation** dla **lokalizacji**.
    6. Kliknij przycisk **OK**, aby utworzyć projekt.

        ![Tworzenie projektu 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Teraz dodaj wszystkie biblioteki DLL obecne w [folderze dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) jako **odwołania** w utworzonym projekcie. Aby dodać pliki dll, wykonaj następujące czynności:

   1. W programie Visual Studio przejdź do **programu Wyświetl > Eksploratora rozwiązań**.
   2. Kliknij strzałkę po lewej stronie projektu aplikacji Transformacja danych. Kliknij **pozycję Odwołania,** a następnie kliknij prawym przyciskiem myszy pozycję **Dodaj odwołanie**.
    
       ![Dodawanie bibliotek dll 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Przejdź do lokalizacji folderu pakietów, zaznacz wszystkie biblioteki Dll i kliknij przycisk **Dodaj**, a następnie kliknij przycisk **OK**.

       ![Dodawanie bibliotek dll 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Dodaj następujące instrukcje **using** do pliku źródłowego (Program.cs) w projekcie.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Poniższy kod inicjuje wystąpienie zadania transformacji danych. Dodaj to w **Main metody**. Zastąp wartości parametrów konfiguracji, jak uzyskano wcześniej. Podłącz wartości **nazwy grupy zasobów** i nazwy **zasobu**. **ResourceGroupName** jest skojarzony z StorSimple Data Manager, na którym zdefiniowano definicję zadania. **Nazwa zasobów** to nazwa usługi StorSimple Data Manager.

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
   
7. Określ parametry, z którymi należy uruchomić definicję zadania

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (LUB)

    Jeśli chcesz zmienić parametry definicji zadania w czasie wykonywania, dodaj następujący kod:

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

8. Po inicjacji dodaj następujący kod, aby wyzwolić zadanie przekształcania danych w definicji zadania. Podłącz odpowiednią **nazwę definicji zadania**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Po wklejeniu kodu skompiluj rozwiązanie. Oto zrzut ekranu fragmentu kodu, aby zainicjować wystąpienie zadania transformacji danych.

   ![Fragment kodu do zainicjowania zadania przekształcania danych](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. To zadanie przekształca dane, które są zgodne z katalogiem głównym i filtrami plików w woluminie StorSimple i umieszcza je w określonym udziale kontenera/pliku. Po przekształceniu pliku do kolejki magazynu (na tym samym koncie magazynu co udział kontenera/pliku) jest dodawany komunikat o takiej samej nazwie jak definicja zadania. Ten komunikat może służyć jako wyzwalacz do inicjowania dalszego przetwarzania pliku.

10. Po wyzwoleniu zadania można użyć następującego kodu do śledzenia zadania do wykonania. Nie jest obowiązkowe, aby dodać ten kod dla zadania.

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
    Oto zrzut ekranu całego przykładu kodu używanego do wyzwalania zadania przy użyciu platformy .NET.

    ![Pełny fragment kodu do wyzwolenia zadania .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Następne kroki

[Aby przekształcić dane, użyj interfejsu użytkownika Programu StorSimple Data Manager.](storsimple-data-manager-ui.md)
