---
title: Zmienianie ścieżki obiektów blob z wartości domyślnej
description: Dowiedz się, jak skonfigurować funkcję platformy Azure w celu zmiany nazwy ścieżki pliku obiektu blob
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 5ba1709ae195631371e4ea72667ba9b2a4bf279e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76270628"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Zmienianie ścieżki obiektu blob ze ścieżki domyślnej

Gdy Usługa StorSimple Data Manager przekształca dane, domyślnie umieszcza przekształcone obiekty blob w kontenerze magazynu, jak określono podczas tworzenia repozytorium docelowego. Gdy obiekty blob docierają do tej lokalizacji, można przenieść te obiekty blob do lokalizacji alternatywnej. W tym artykule opisano sposób konfigurowania funkcji platformy Azure w celu zmiany nazwy domyślnej ścieżki pliku obiektu blob i w związku z tym przeniesienia obiektów blob do innej lokalizacji.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że masz poprawnie skonfigurowaną definicję zadania w usłudze StorSimple Data Manager.

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Aby utworzyć funkcję platformy Azure, wykonaj następujące kroki:

1. Przejdź do [witryny Azure portal](https://portal.azure.com/).

2. Kliknij **pozycję + Utwórz zasób**. W polu **Wyszukiwania** wpisz **aplikację funkcji** i naciśnij klawisz **Enter**. Wybierz i kliknij **pozycję Aplikacja Funkcja** na wyświetlonej liście aplikacji.

    ![Wpisz "Aplikacja funkcyjna" w polu wyszukiwania](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Kliknij przycisk **Utwórz**.

    ![Przycisk "Utwórz" w oknie aplikacji funkcji](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. W bloku konfiguracji **aplikacji funkcji** wykonaj następujące czynności:

    1. Podaj unikatową **nazwę aplikacji**.
    2. Z listy rozwijanej wybierz **pozycję Subskrypcja**. Ta subskrypcja powinna być taka sama jak ta skojarzona z usługą StorSimple Data Manager.
    3. Wybierz **pozycję Utwórz nową** grupę zasobów.
    4. W przypadku listy rozwijanej **Plan hostingu** wybierz pozycję **Plan zużycia**.
    5. Określ lokalizację, w której działa funkcja. Ma zostać położony ten sam region, w którym znajduje się usługa StorSimple Data Manager i konto magazynu skojarzone z definicją zadania.
    6. Wybierz istniejące konto magazynu lub utwórz nowe. Konto magazynu jest używane wewnętrznie dla tej funkcji.

        ![Wprowadzanie nowych danych konfiguracyjnych aplikacji funkcji](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Kliknij przycisk **Utwórz**. Zostanie utworzona aplikacja funkcji.
     
        ![Utworzono aplikację funkcji](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Wybierz pozycję **Funkcje**i kliknij przycisk **+ Nowa funkcja**.

    ![Kliknij + Nowa funkcja](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Wybierz **C#** dla języka. W tablicy kafelków szablonów wybierz pozycję **C#** na kafelku **QueueTrigger-CSharp.**

7. W **wyzwalaczu kolejki:**

    1. Wprowadź **nazwę** funkcji.
    2. W polu **Nazwa kolejki** wpisz nazwę definicji zadania transformacji danych.
    3. W obszarze **Połączenie z kontem magazynowania**kliknij **nowy**. Z listy kont magazynu wybierz konto skojarzone z definicją zadania. Zanotuj nazwę połączenia (wyróżnioną). Nazwa jest wymagana później w funkcji platformy Azure.

        ![Tworzenie nowej funkcji Języka C#](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Kliknij przycisk **Utwórz**. **Funkcja** jest tworzona.

     
10. W oknie Funkcja uruchom plik _csx._

    ![Tworzenie nowej funkcji Języka C#](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
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

    2. Zastąp **STORAGE_CONNECTIONNAME** w wierszu 11 połączeniem z kontem pamięci masowej (patrz krok 7c).

        ![Nazwa połączenia kopiowania magazynu](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Zapisz** funkcję.

        ![Zapisz, funkcja](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Aby ukończyć funkcję, dodaj jeszcze jeden plik, wykonując następujące czynności:

    1. Kliknij **pozycję Wyświetl pliki**.

       ![Łącze "Wyświetl pliki"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Kliknij pozycję **+ Dodaj**.
        
        ![Łącze "Wyświetl pliki"](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Wpisz **project.json**, a następnie naciśnij klawisz **Enter**. W pliku **project.json** wklej następujący kod:

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

    
    4. Kliknij przycisk **Zapisz**.

        ![Łącze "Wyświetl pliki"](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Utworzono funkcję platformy Azure. Ta funkcja jest wyzwalana za każdym razem, gdy nowy obiekt blob jest generowany przez zadanie przekształcania danych.

## <a name="next-steps"></a>Następne kroki

[Przekształcanie danych za pomocą interfejsu użytkownika menedżera danych StorSimple](storsimple-data-manager-ui.md)
