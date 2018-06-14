---
title: Ścieżka obiektu blob zmiany z domyślnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można skonfigurować funkcję Azure, aby zmienić ścieżkę do pliku obiektu blob
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
ms.openlocfilehash: f73d9dcedee5165af752b9e10fb70de860e8e98b
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862404"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Zmienianie ścieżki obiektu blob ze ścieżki domyślnej

Przy usługi Menedżer StorSimple danych przekształca dane, domyślnie umieszcza on przekształcony obiektów blob w kontenerze magazynu określony podczas tworzenia repozytorium docelowe. Nadejścia nowych obiektów blob w tym miejscu, można przenieść te obiekty BLOB do lokalizacji alternatywnej. W tym artykule opisano, jak skonfigurować funkcję Azure zmienić nazwę domyślną ścieżkę pliku blob i dlatego przenieść obiekty BLOB do innej lokalizacji.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że masz definicji zadania poprawnie skonfigurowane w usłudze Menedżer StorSimple danych.

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Aby utworzyć funkcję platformy Azure, wykonaj następujące czynności:

1. Przejdź do witryny [Azure Portal](http://portal.azure.com/).

2. Kliknij przycisk **+ Utwórz zasób**. W **wyszukiwania** wpisz **aplikacji funkcji** i naciśnij klawisz **Enter**. Wybierz, a następnie kliknij przycisk **aplikacji funkcji** aplikacji wyświetlane na liście.

    ![W polu wyszukiwania wpisz "Funkcja aplikacji"](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Kliknij przycisk **Utwórz**.

    ![Przycisk "Utwórz" okna aplikacji — funkcja](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Na **aplikacji funkcji** blok konfiguracji, wykonaj następujące czynności:

    1. Podaj unikatową **Nazwa aplikacji**.
    2. Z listy rozwijanej wybierz **subskrypcji**. Ta subskrypcja powinna być taka sama jak skojarzony z usługą Menedżer StorSimple danych.
    3. Wybierz **Utwórz nowy** grupy zasobów.
    4. Dla **Plan hostingu** listy rozwijanej wybierz **zaplanować użycie**.
    5. Określ lokalizację, w którym jest uruchomiona funkcja. Ma tego samego regionu, w którym znajdują się usługi Menedżer StorSimple danych i konta magazynu skojarzone z definicji zadania.
    6. Wybierz istniejące konto magazynu lub utwórz nowe. Konto magazynu jest używana wewnętrznie do funkcji.

        ![Wprowadź nowe dane konfiguracji funkcji aplikacji](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Kliknij przycisk **Utwórz**. Funkcja aplikacji zostanie utworzona.
     
        ![Utworzona aplikacja — funkcja](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Wybierz **funkcje**i kliknij przycisk **+ nową funkcję**.

    ![Kliknij pozycję + nowej funkcji](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Wybierz **C#** dla języka. W tablicy Kafelki szablonu, wybierz **C#** w **QueueTrigger CSharp** kafelka.

7. W **wyzwalacza kolejki**:

    1. Wprowadź **nazwa** dla funkcji.
    2. W **Nazwa kolejki** wpisz nazwę definicji zadania przekształcania danych.
    3. W obszarze **połączenia konta magazynu**, kliknij przycisk **nowe**. Z listy kont magazynu wybierz konto skojarzone z Twojej definicji zadania. Zanotuj nazwę połączenia (wyróżniony). Wymagana jest nazwa później w funkcji platformy Azure.

        ![Utwórz nową funkcję C#](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Kliknij przycisk **Utwórz**. **Funkcja** jest tworzony.

     
10. W oknie funkcji Uruchom _csx_ pliku.

    ![Utwórz nową funkcję C#](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Wykonaj poniższe kroki.

    1. Wklej następujący kod:

        ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }

        ```

    2. Zastąp **STORAGE_CONNECTIONNAME** w wierszu 11 z połączeniem konta magazynu (zobacz krok 7 c).

        ![Nazwa połączenia magazynu kopii](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Zapisz** funkcji.

        ![Zapisz — funkcja](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Aby wykonać funkcję, Dodaj więcej plików, wykonując następujące czynności:

    1. Kliknij przycisk **wyświetlać pliki**.

       ![Łącze "Wyświetl pliki"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Kliknij przycisk **+ Dodaj**.
        
        ![Łącze "Wyświetl pliki"](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Typ **project.json**, a następnie naciśnij klawisz **Enter**. W **project.json** plików, wklej następujący kod:

        ```
        {
        "frameworks": {
            "net46":{
            "dependencies": {
                "windowsazure.storage": "8.1.1"
            }
            }
        }
        }

        ```

    
    4. Kliknij pozycję **Zapisz**.

        ![Łącze "Wyświetl pliki"](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Utworzono funkcji platformy Azure. Ta funkcja jest wywoływane zawsze, gdy nowy obiekt blob jest generowany przez zadania przekształcania danych.

## <a name="next-steps"></a>Następne kroki

[Użyj Menedżera danych StorSimple interfejsu użytkownika Służącego do przekształcenia danych](storsimple-data-manager-ui.md)
