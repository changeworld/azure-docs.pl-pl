---
title: Użyj zestawu SDK platformy .NET dla Microsoft Azure StorSimple Data Manager zadań | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać zestawu SDK .NET można uruchomić zadania usługi StorSimple Data Manager
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
ms.openlocfilehash: 80f01a926b94deebab59f8ef91bfc36a4600b5f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632404"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Zainicjuj przekształcania danych przy użyciu zestawu SDK platformy .NET

## <a name="overview"></a>Omówienie

W tym artykule wyjaśniono, jak funkcja transformacji danych w ramach usługi StorSimple Data Manager można użyć do przekształcania danych urządzenia StorSimple. Przekształcone dane są następnie używane przez inne usługi platformy Azure w chmurze.

Można uruchomić zadania przekształcania danych na dwa sposoby:

- Korzystanie z zestawu SDK dla platformy .NET
- Użyj elementu runbook usługi Azure Automation
 
  W tym artykule opisano, jak utworzyć aplikację konsolową .NET próbki, aby zainicjować zadanie przekształcania danych i śledzić go na ukończenie. Aby dowiedzieć się więcej o tym, jak zainicjować przekształcania danych za pomocą usługi Automation, przejdź do [użycia usługi Azure Automation runbook wyzwalanie zadań przekształcania danych](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:
*   Komputer z systemem:

    - Program Visual Studio 2012, 2013, 2015 lub 2017.

    - Azure Powershell. [Pobieranie programu Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Definicja zadania poprawnie skonfigurowana usługa StorSimple Data Manager w grupie zasobów.
*   Wszystkie wymagane biblioteki dll. Pobierz te biblioteki dll z [repozytorium GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) skrypt z repozytorium GitHub.

## <a name="step-by-step-procedure"></a>Procedura krok po kroku

Wykonaj poniższe kroki, aby uruchomić zadanie przekształcania danych za pomocą platformy .NET.

1. Aby pobrać parametry konfiguracji, wykonaj następujące czynności:
    1. Pobierz `Get-ConfigurationParams.ps1` ze skryptu repozytorium GitHub w `C:\DataTransformation` lokalizacji.
    1. Uruchom `Get-ConfigurationParams.ps1` skryptu z repozytorium GitHub. Wpisz następujące polecenie:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Możesz przekazać wszystkie wartości ActiveDirectoryKey i nazwa aplikacji.

2. Ten skrypt generuje następujące wartości:
    * Identyfikator klienta
    * Identyfikator dzierżawy
    * Aktywnego klucza katalogu (taki sam jak podanymi powyżej)
    * Identyfikator subskrypcji

        ![Dane wyjściowe skryptu parametry konfiguracji](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Za pomocą programu Visual Studio 2012, 2013 lub 2015 Utwórz C# aplikacji konsolowej programu .NET.

    1. Uruchom **Visual Studio 2012/2013/2015**.
    1. Wybierz pozycję **Plik > Nowy > Projekt**.

        ![Utwórz projekt 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Wybierz **zainstalowane > Szablony > Visual C# > aplikacji konsolowej programu**.
    3. Wprowadź **DataTransformationApp** dla **nazwa**.
    4. Wybierz **C:\DataTransformation** dla **lokalizacji**.
    6. Kliknij przycisk **OK**, aby utworzyć projekt.

        ![Utwórz projekt 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Teraz Dodaj wszystkie biblioteki dll, które są obecne w [folder biblioteki dll](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) jako **odwołania** w projekcie, który został utworzony. Aby dodać pliki dll, należy wykonać następujące czynności:

   1. W programie Visual Studio, przejdź do **Widok > Eksploratorze rozwiązań**.
   2. Kliknij strzałkę po lewej stronie projektu aplikacji przekształcania danych. Kliknij przycisk **odwołania** , a następnie kliknij prawym przyciskiem myszy, aby **Dodaj odwołanie**.
    
       ![Dodawanie bibliotek DLL 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Przejdź do lokalizacji folderu pakietów, wybierz wszystkie biblioteki dll i kliknij **Dodaj**, a następnie kliknij przycisk **OK**.

       ![Dodawanie bibliotek DLL 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Dodaj następujące instrukcje **using** do pliku źródłowego (Program.cs) w projekcie.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Poniższy kod inicjuje wystąpienie zadania przekształcania danych. Dodaj ją w **metody Main**. Zastąp wartości parametrów konfiguracji zgodnie z uzyskanymi wcześniej. Podłącz w wartościach **nazwy grupy zasobów** i **ResourceName**. **ResourceGroupName** jest skojarzony z usługi StorSimple Data Manager w której została skonfigurowana definicji zadania. **ResourceName** to nazwa usługi StorSimple Data Manager.

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
   
7. Określ parametry, z którymi musi zostać uruchomione definicję zadania

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
    Po wklejeniu kodu, Skompiluj rozwiązanie. Poniżej przedstawiono zrzut ekranu przedstawiający fragmentu kodu można zainicjować wystąpienia zadania przekształcania danych.

   ![Fragment kodu, aby zainicjować zadanie przekształcania danych](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. To zadanie przekształcenia danych, odpowiadający katalog główny i pliku filtrów w obrębie woluminu StorSimple i umieszcza go do udziału określonego kontenerów/plików. Gdy plik jest przekształcana, komunikat zostanie dodany do kolejki magazynu (w tym samym koncie magazynu jako udział kontenerów/plików) o nazwie identycznej z nazwą definicji zadania. Ten komunikat może służyć jako wyzwalacz do zainicjowania dalszego przetwarzania pliku.

10. Po wyzwoleniu zadania można użyć poniższego kodu do śledzenia zadań na zakończenie. Nie jest wymagane, aby dodać tego kodu w celu uruchomienia zadania.

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
    Poniżej przedstawiono zrzut ekranu przedstawiający przykładowy cały kod, używane do wyzwalania zadania za pomocą platformy .NET.

    ![Pełną fragment kodu do wyzwalania zadania platformy .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Kolejne kroki

[Użyj usługi StorSimple Data Manager interfejsu użytkownika w celu przekształcania danych](storsimple-data-manager-ui.md).
