---
title: Przyrostowe kopiowanie nowych plików za pomocą usługi Azure Data Factory tylko na podstawie czasu partycjonowane nazwy pliku | Dokumentacja firmy Microsoft
description: Tworzenie fabryki danych platformy Azure, a następnie użyj narzędzia do kopiowania danych do przyrostowego ładowania nowe pliki tylko na podstawie czasu partycjonowane nazwy pliku.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2019
ms.openlocfilehash: c89764d746f07e6100b1f250d4c107bb700fe014
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445849"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Przyrostowe kopiowanie nowych plików, na podstawie czasu partycjonowane nazwy pliku przy użyciu narzędzia do kopiowania danych

W tym samouczku utworzysz fabrykę danych za pomocą witryny Azure Portal. Następnie przy użyciu narzędzia do kopiowania danych utworzysz potok, który przyrostowo kopiuje nowe pliki, na podstawie nazwy pliku partycjonowane czasu z usługi Azure Blob storage do usługi Azure Blob storage. 

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Ten samouczek obejmuje wykonanie następujących kroków:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto usługi Azure Storage**: Użyj usługi Blob storage jako _źródła_ i _ujścia_ magazynu danych. Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Utworzenia dwóch kontenerów w magazynie obiektów Blob

Przygotuj usługi Blob storage na potrzeby samouczka, wykonując następujące kroki.

1. Utwórz kontener o nazwie **źródła**.  Utwórz ścieżkę folderu jako **2019 r/02/26/14** w kontenerze. Utwórz pusty plik tekstowy i nadaj mu jako **więc Plik1.txt**. Przekaż więc Plik1.txt w ścieżce folderu **źródło/2019 r/02/26/14** na koncie magazynu.  Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).
    
    ![Przekazywanie plików](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Dostosuj nazwy folderu przy użyciu czasu UTC.  Na przykład, jeśli bieżący czas UTC jest 14:03:00 w 26 lutego 2019 r możesz utworzyć jako ścieżkę do folderu **źródło/2019 r/02/26/14/** przez regułę z **źródło / {Year} / {Month} / {Day} / {godzina} /**.

2. Utwórz kontener o nazwie **docelowy**. Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz **Utwórz zasób** > **dane + analiza** > **usługi Data Factory**: 
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**. 
      
    ![Nowa fabryka danych](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
    
    Nazwa Twojej fabryki danych musi być _globalnie unikatowa_. Może zostać wyświetlony następujący komunikat o błędzie:
   
   ![Komunikat o błędzie dotyczący nowej fabryki danych](./media/tutorial-copy-data-tool/name-not-available-error.png)
   
   Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_**ADFTutorialDataFactory**. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
3. Wybierz **subskrypcję** platformy Azure, w której utworzysz nową fabrykę danych. 
4. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/resource-group-overview.md).

5. W obszarze **Wersja** wybierz wersję **V2**.
6. W obszarze **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez Twoją fabrykę danych mogą mieścić się w innych lokalizacjach i regionach.
7. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**. 
8. Wybierz pozycję **Utwórz**.
9. Na pulpicie nawigacyjnym kafelek **Wdrażanie fabryki danych** pokazuje stan procesu.

    ![Kafelek Wdrażanie fabryki danych](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Po zakończeniu tworzenia zostanie wyświetlona strona główna usługi **Data Factory**.
   
    ![Strona główna fabryki danych](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory, kliknij kafelek **Tworzenie i monitorowanie**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. Na **zaczynajmy** wybierz opcję **kopiowania danych** tytuł, aby uruchomić narzędzie do kopiowania danych. 

   ![Kafelek narzędzia do kopiowania danych](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Na **właściwości** strony, wykonaj następujące czynności:

    a. W obszarze **Nazwa zadania**, wprowadź **DeltaCopyFromBlobPipeline**.

    b. W obszarze **tempa zadania lub harmonogram zadań**, wybierz opcję **regularnie uruchamiana zgodnie z harmonogramem**.

    c. W obszarze **typu**, wybierz opcję **okno wirowania**.
    
    d. W obszarze **cyklu**, wprowadź **1 godz**. 
    
    e. Wybierz opcję **Dalej**. 
    
    Interfejs użytkownika usługi Data Factory tworzy potok o określonej nazwie zadania. 

    ![Strona właściwości](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Na stronie **Źródłowy magazyn danych** wykonaj następujące czynności:

    a. Kliknij przycisk **+ Utwórz nowe połączenie**, aby dodać połączenie.

    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Wybierz **usługi Azure Blob Storage** z galerii, a następnie kliknij **Kontynuuj**.

    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. Na **nowa połączona usługa** stronie, wybierz konto magazynu z **nazwa konta magazynu** listy, a następnie kliknij przycisk **Zakończ**.
    
    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Wybierz nowo utworzony połączoną usługę, a następnie kliknij przycisk **dalej**. 
    
   ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące kroki:
    
    a. Wyszukaj i wybierz **źródła** kontenera, następnie wybierz pozycję **wybierz**.
    
    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. W obszarze **zachowanie podczas ładowania pliku**, wybierz opcję **ładowanie przyrostowe: nazwy folderów i plików na partycje czasu**.
    
    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Ścieżka folderu dynamiczne jako zapisu **źródło / {year} / {month} / {day} / {godzina} /** i zmień format jako następujących tematach:
    
    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Sprawdź **kopia binarna** i kliknij przycisk **dalej**.
    
    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. Na **docelowego magazynu danych** wybierz opcję **usłudze Azure blob Storage**, który jest tego samego magazynu konta jako ze źródła danych, a następnie kliknij **dalej**.

    ![Strona Docelowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. Na **Wybieranie wyjściowego pliku lub folderu** wykonaj następujące czynności:
    
    a. Wyszukaj i wybierz **docelowy** folderu, następnie kliknij przycisk **wybierz**.
    
    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Ścieżka folderu dynamiczne jako zapisu **źródło / {year} / {month} / {day} / {godzina} /** i zmień format jako następujących tematach:
    
    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Kliknij przycisk **Dalej**.
    
    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. Na stronie **Ustawienia** wybierz przycisk **Dalej**. 

    ![Strona Ustawienia](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Na stronie **Podsumowanie** sprawdź ustawienia, a następnie kliknij przycisk **Dalej**.

    ![Strona podsumowania](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie).
    ![Strona wdrożenia](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana.  Należy poczekać do uruchomienia po wyzwoleniu automatycznie potoku (o po godzinie).  Po jego uruchomieniu, **akcje** kolumna zawiera linki, aby wyświetlić szczegóły uruchamiania działania i ponowne uruchamianie potoku. Wybierz **Odśwież** Aby odświeżyć listę, a następnie wybierz **Wyświetl uruchomienia działania** łącze w **akcje** kolumny. 

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Zostanie wyświetlony, zostały skopiowane z pliku źródłowego (więc Plik1.txt) **źródło/2019 r/02/26/14/** do **docelowego/2019r/02/26/14/** o takiej samej nazwie pliku.  

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    Można również sprawdzić takie same za pomocą Eksploratora usługi Azure Storage (https://storageexplorer.com/) skanowanie plików.
    
    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Utwórz inny pusty plik tekstowy z nową nazwą, jako **Plik2.txt**. Przekaż plik Plik2.txt w ścieżce folderu **źródło/2019 r/02/26/15** na koncie magazynu.   Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).   
    
    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > Należy pamiętać, że nowa ścieżka folderu jest wymagane do utworzenia. Dostosuj nazwy folderu przy użyciu czasu UTC.  Na przykład, jeśli bieżący czas UTC jest 3:20 PM 26 lutego 2019 r możesz utworzyć jako ścieżkę do folderu **źródło/2019 r/02/26/15/** przez regułę z **{Year} / {Month} / {Day} / {godzina} /**.
    
13. Aby wrócić do **uruchomienia potoku** widoku, wybierz opcję **wszystkie uruchomienia potoków**i czeka na potok tego samego ponownie wyzwolona automatycznie po inną godzinę.  

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Wybierz **uruchamiania działania widoku** do drugiego potoku uruchamiać, gdy go pochodzi, a następnie wykonaj takie same, aby poznać szczegóły.  

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    Zostanie wyświetlony, zostały skopiowane z pliku źródłowego (Plik2.txt) **źródło/2019 r/02/26/15/** do **docelowego/2019r/02/26/15/** o takiej samej nazwie pliku.
    
    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    Można również sprawdzić takie same za pomocą Eksploratora usługi Azure Storage (https://storageexplorer.com/) skanuje pliki w **docelowy** kontenera
    
    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Kolejne kroki
Przejdź do następującego samouczka, aby dowiedzieć się więcej o przekształcaniu danych za pomocą klastra Spark na platformie Azure:

> [!div class="nextstepaction"]
>[Przekształcanie danych przy użyciu klastra Spark w chmurze](tutorial-transform-data-spark-portal.md)