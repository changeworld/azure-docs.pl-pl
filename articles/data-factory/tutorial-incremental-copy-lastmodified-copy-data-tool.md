---
title: Przyrostowe kopiowanie nowych i zmienionych plików przy użyciu usługi Azure Data Factory tworzony tylko na podstawie Data ostatniej modyfikacji | Dokumentacja firmy Microsoft
description: Tworzenie fabryki danych platformy Azure, a następnie użyj narzędzia do kopiowania danych do przyrostowego ładowania nowe pliki tylko na podstawie ich Data ostatniej modyfikacji.
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
ms.openlocfilehash: d79b44d0123d64d6280939767e5df7b5f64a5fcb
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445956"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Przyrostowe kopiowanie nowych i zmienionych plików, w oparciu o Data ostatniej modyfikacji przy użyciu narzędzia do kopiowania danych

W tym samouczku utworzysz fabrykę danych za pomocą witryny Azure Portal. Następnie przy użyciu narzędzia do kopiowania danych utworzysz potok, który przyrostowo kopiuje nowych i zmienionych plików tylko na podstawie ich ich "Data ostatniej modyfikacji" z usługi Azure Blob storage do usługi Azure Blob storage. 

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

1. Utwórz kontener o nazwie **źródła**. Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

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
    
    d. W obszarze **cyklu**, wprowadź **15 min**. 
    
    e. Wybierz opcję **Dalej**. 
    
    Interfejs użytkownika usługi Data Factory tworzy potok o określonej nazwie zadania. 

    ![Strona właściwości](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Na stronie **Źródłowy magazyn danych** wykonaj następujące czynności:

    a. Kliknij przycisk **+ Utwórz nowe połączenie**, aby dodać połączenie.
    
    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Wybierz **usługi Azure Blob Storage** z galerii, a następnie kliknij **Kontynuuj**.
    
    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na **nowa połączona usługa** stronie, wybierz konto magazynu z **nazwa konta magazynu** listy, a następnie kliknij przycisk **Zakończ**.
    
    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Wybierz nowo utworzony połączoną usługę, a następnie kliknij przycisk **dalej**. 
    
   ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące kroki:
    
    a. Wyszukaj i wybierz **źródła** folderu, następnie kliknij przycisk **wybierz**.
    
    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. W obszarze **zachowanie podczas ładowania pliku**, wybierz opcję **ładowanie przyrostowe: LastModifiedDate**.
    
    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Sprawdź **kopia binarna** i kliknij przycisk **dalej**.
    
     ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Na **docelowego magazynu danych** wybierz opcję **usłudze Azure blob Storage** co jest tego samego magazynu konta jako ze źródła danych, a następnie kliknij **dalej**.

    ![Strona Docelowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Na **Wybieranie wyjściowego pliku lub folderu** wykonaj następujące czynności:
    
    a. Wyszukaj i wybierz **docelowy** folderu, następnie kliknij przycisk **wybierz**.
    
    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Kliknij przycisk **Dalej**.
    
     ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Na stronie **Ustawienia** wybierz przycisk **Dalej**. 

    ![Strona Ustawienia](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Na stronie **Podsumowanie** sprawdź ustawienia, a następnie kliknij przycisk **Dalej**.

    ![Strona podsumowania](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie).

    ![Strona Wdrażanie](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Kolumna **Akcje** zawiera linki służące do wyświetlania szczegółów działań i ponownego uruchamiania potoku. Wybierz **Odśwież** Aby odświeżyć listę, a następnie wybierz **Wyświetl uruchomienia działania** łącze w **akcje** kolumny. 

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Akcje**. 

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Biorąc pod uwagę, że nie ma pliku w **źródła** kontenera na koncie magazynu obiektów blob, dzięki czemu nie będzie mógł przeglądać każdego pliku, który został skopiowany do **docelowy** kontenera na koncie usługi blob storage.
    
    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Utwórz pusty plik tekstowy i nadaj jej nazwę jako więc Plik1.txt. Przekaż plik więc Plik1.txt do **źródła** kontenera na koncie magazynu. Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).   

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Aby wrócić do **uruchomienia potoku** widoku, wybierz opcję **wszystkie uruchomienia potoków**i czeka na potok tego samego wyzwalane automatycznie ponownie.  

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Wybierz **uruchamiania działania widoku** do drugiego potoku wykonywania, gdy widzisz pochodzi, a tym samym, aby poznać szczegóły.  

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Zostanie wyświetlony jeden plik (więc Plik1.txt) zostały skopiowane z **źródła** kontener **docelowy** kontenera konta magazynu.
    
    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Utwórz inny pusty plik tekstowy i nadaj jej nazwę jako Plik2.txt. Przekaż plik Plik2.txt do **źródła** kontenera na koncie magazynu. Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).  
    
16. Wykonaj takie same jak kroki 13 do 14, a zobaczysz tylko nowy plik (Plik2.txt) zostały skopiowane z **źródła** kontener **docelowy** kontenera konta magazynu w ramach następnego uruchomienia potoku.  
    
    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Można również sprawdzić takie same za pomocą Eksploratora usługi Azure Storage (https://storageexplorer.com/) skanowanie plików.
    
    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Kolejne kroki
Przejdź do następującego samouczka, aby dowiedzieć się więcej o przekształcaniu danych za pomocą klastra Spark na platformie Azure:

> [!div class="nextstepaction"]
>[Przekształcanie danych przy użyciu klastra Spark w chmurze](tutorial-transform-data-spark-portal.md)