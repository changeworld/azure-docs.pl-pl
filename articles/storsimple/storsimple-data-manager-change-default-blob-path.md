---
title: Ścieżka obiektu blob zmiany z domyślnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować funkcję platformy Azure, aby zmienić nazwę ścieżki pliku obiektu blob
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
ms.openlocfilehash: cdaf991c25c23dee4f87b44142c1482bf892bcf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723803"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Zmień ścieżkę obiektu blob ze ścieżki domyślnej

Gdy usługa StorSimple Data Manager przekształca dane, domyślnie trafi przekształconych obiektów blob w kontenerze magazynu określony podczas tworzenia repozytorium docelowego. Nadejścia nowych obiektów blob w tym miejscu, można przenieść tych obiektów blob do lokalizacji alternatywnej. W tym artykule opisano sposób konfigurowania funkcji platformy Azure, aby zmienić nazwę domyślnej ścieżki pliku obiektu blob i dlatego przenoszenie obiektów blob do innej lokalizacji.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że definicja zadania prawidłowo skonfigurowaną w usłudze StorSimple Data Manager.

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Aby utworzyć funkcję platformy Azure, wykonaj następujące czynności:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/).

2. Kliknij przycisk **+ Utwórz zasób**. W **wyszukiwania** wpisz **aplikacji funkcji** i naciśnij klawisz **Enter**. Wybierz i kliknij przycisk **aplikacji funkcji** na liście aplikacji wyświetlane.

    ![W polu wyszukiwania wpisz "Aplikacji funkcji"](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Kliknij pozycję **Utwórz**.

    ![Przycisk "Utwórz" okna aplikacji funkcji](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Na **aplikacji funkcji** bloku konfiguracja, wykonaj następujące czynności:

    1. Podaj unikatową **nazwy aplikacji**.
    2. Z listy rozwijanej wybierz **subskrypcji**. Ta subskrypcja powinna być taka sama jak skojarzone z Twoją usługą StorSimple Data Manager.
    3. Wybierz **Utwórz nową** grupy zasobów.
    4. Aby uzyskać **planie hostingu** listy rozwijanej wybierz **Plan zużycie**.
    5. Określ lokalizację, w którym jest uruchomiona funkcja. Chcesz, aby tym samym regionie, w którym znajdują się usługi StorSimple Data Manager i konta magazynu skojarzonego z definicją zadania.
    6. Wybierz istniejące konto magazynu lub utwórz nowe. Konto magazynu jest używana wewnętrznie do funkcji.

        ![Wprowadź nowe dane konfiguracji aplikacji funkcji](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Kliknij pozycję **Utwórz**. Aplikacja funkcji jest tworzona.
     
        ![Utworzona aplikacja funkcji](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Wybierz **funkcje**i kliknij przycisk **+ nowa funkcja**.

    ![Kliknij pozycję + Nowa funkcja](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Wybierz **C#** dla języka. W tablicy Kafelki szablonu, wybierz **C#** w **QueueTrigger-CSharp** kafelka.

7. W **wyzwalacz kolejki**:

    1. Wprowadź **nazwa** funkcji.
    2. W **Nazwa kolejki** wpisz nazwę definicji zadania przekształcania danych.
    3. W obszarze **połączenie konta magazynu**, kliknij przycisk **nowe**. Z listy kont magazynu wybierz na koncie skojarzonym z definicji zadania. Zanotuj nazwę połączenia (wyróżnione). Nazwa jest wymagana w dalszej części funkcji platformy Azure.

        ![Utwórz nową C# — funkcja](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Kliknij pozycję **Utwórz**. **Funkcja** zostanie utworzony.

     
10. W oknie funkcji Uruchom _csx_ pliku.

    ![Utwórz nową C# — funkcja](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
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

    2. Zastąp **STORAGE_CONNECTIONNAME** w wierszu 11 z połączenia konta magazynu (zobacz krok 7 c).

        ![Skopiuj nazwę połączenia magazynu](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Zapisz** funkcji.

        ![Zapisz — funkcja](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Aby wykonać funkcję, Dodaj więcej plików, wykonując następujące czynności:

    1. Kliknij przycisk **wyświetlać pliki**.

       ![Łącze "Wyświetl pliki"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Kliknij pozycję **+ Dodaj**.
        
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

Utworzono funkcję platformy Azure. Ta funkcja jest wyzwalana każdorazowo, gdy nowy obiekt blob jest generowany przez zadanie przekształcania danych.

## <a name="next-steps"></a>Kolejne kroki

[Użyj usługi StorSimple Data Manager interfejsu użytkownika w celu przekształcania danych](storsimple-data-manager-ui.md)
