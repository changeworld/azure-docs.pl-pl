---
title: Przyrostowe kopiowanie nowych plików na podstawie nazwy pliku podzielonego na partycje
description: Utwórz fabrykę danych platformy Azure, a następnie użyj narzędzia Kopiowanie danych, aby przyrostowo ładować nowe pliki na podstawie nazwy pliku podzielonego na partycje.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 1/24/2019
ms.openlocfilehash: b6826fa53e9810bdd30af4faf9fbf6bc3c3492e8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439245"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Przyrostowe kopiowanie nowych plików na podstawie nazwy pliku podzielonego na partycje przy użyciu narzędzia Kopiowanie danych

W tym samouczku utworzysz fabrykę danych za pomocą witryny Azure Portal. Następnie użyj narzędzia Kopiowanie danych, aby utworzyć potok, który przyrostowo kopiuje nowe pliki na podstawie nazwy pliku z podziałem czasu z usługi Azure Blob Storage do usługi Azure Blob Storage. 

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Ten samouczek obejmuje wykonanie następujących kroków:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto usługi Azure Storage**: Użyj magazynu obiektów BLOB jako magazynu danych _źródłowych_ i _ujścia_ . Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Tworzenie dwóch kontenerów w usłudze BLOB Storage

Aby przygotować magazyn obiektów BLOB do samouczka, wykonaj te kroki.

1. Utwórz kontener o nazwie **Source**.  Utwórz ścieżkę folderu jako **2019/02/26/14** w kontenerze. Utwórz pusty plik tekstowy i nadaj mu nazwę **plik1. txt**. Przekaż plik1. txt do folderu **Source Path/2019/02/26/14** na koncie magazynu.  Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).
    
    ![Przekaż pliki](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Dostosuj nazwę folderu o czas UTC.  Na przykład, jeśli bieżący czas UTC to 2:03 PM w dniu 26 lutego 2019, można utworzyć ścieżkę folderu jako **Źródło/2019/02/26/14/** według reguły **źródłowej/{Year}/{Month}/{Day}/{Hour}/** .

2. Utwórz kontener o nazwie **Destination**. Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz zasób** > **dane + analiza** > **Data Factory**: 
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**. 
    
    Nazwa Twojej fabryki danych musi być _globalnie unikatowa_. Może zostać wyświetlony następujący komunikat o błędzie:
   
   ![Komunikat o błędzie dotyczący nowej fabryki danych](./media/doc-common-process/name-not-available-error.png)
   
   Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_ **ADFTutorialDataFactory**. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
3. Wybierz **subskrypcję** platformy Azure, w której utworzysz nową fabrykę danych. 
4. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
    a. Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    b. Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md).

5. W obszarze **Wersja** wybierz wersję **V2**.
6. W obszarze **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (np. usługi Azure Storage i SQL Database) oraz jednostki obliczeniowe (np. usługa Azure HDInsight) używane przez Twoją fabrykę danych mogą mieścić się w innych lokalizacjach i regionach.
7. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**. 
8. Wybierz pozycję **Utwórz**.
9. Na pulpicie nawigacyjnym kafelek **Wdrażanie fabryki danych** pokazuje stan procesu.

    ![Kafelek Wdrażanie fabryki danych](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Po zakończeniu tworzenia zostanie wyświetlona strona główna usługi **Data Factory**.
   
    ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)
11. Aby w osobnej karcie uruchomić interfejs użytkownika usługi Azure Data Factory, kliknij kafelek **Tworzenie i monitorowanie**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. **Na stronie Wprowadzenie** wybierz tytuł **Kopiowanie danych** , aby uruchomić narzędzie kopiowanie danych. 

   ![Kafelek narzędzia do kopiowania danych](./media/doc-common-process/get-started-page.png)
   
2. Na stronie **Właściwości** wykonaj następujące czynności:

    a. W obszarze **Nazwa zadania**wprowadź **DeltaCopyFromBlobPipeline**.

    b. W obszarze **zadanie erze lub harmonogram zadań**wybierz pozycję **Uruchom regularnie zgodnie z harmonogramem**.

    d. W obszarze **Typ wyzwalacza**wybierz pozycję **okno wirowania**.
    
    d. W obszarze **cykl**wprowadź **1 godzinę**. 
    
    e. Wybierz opcję **Dalej**. 
    
    Interfejs użytkownika usługi Data Factory tworzy potok o określonej nazwie zadania. 

    ![Strona właściwości](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Na stronie **Źródłowy magazyn danych** wykonaj następujące czynności:

    a. Kliknij pozycję **+ Utwórz nowe połączenie**, aby dodać połączenie.

    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Wybierz pozycję **Azure Blob Storage** z galerii, a następnie kliknij przycisk **Kontynuuj**.

    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    d. Na stronie **Nowa połączona usługa** wybierz konto magazynu z listy **nazwa konta magazynu** , a następnie kliknij przycisk **Zakończ**.
    
    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Wybierz nowo utworzoną połączoną usługę, a następnie kliknij przycisk **dalej**. 
    
   ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące kroki:
    
    a. Przeglądaj i wybierz kontener **źródłowy** , a następnie wybierz pozycję **Wybierz**.
    
    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. W obszarze **zachowanie ładowania plików**wybierz pozycję **obciążenie przyrostowe: nazwy folderów/plików z podziałem na partycje**.
    
    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    d. Zapisz ścieżkę do folderu dynamicznego jako **Źródło/{Year}/{Month}/{Day}/{Hour}/** , a następnie Zmień format w następujący sposób:
    
    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Sprawdź **kopię binarną** i kliknij przycisk **dalej**.
    
    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. Na stronie **docelowy magazyn danych** wybierz **AzureBlobStorage**, który jest tym samym kontem magazynu co magazyn źródła danych, a następnie kliknij przycisk **dalej**.

    ![Strona Docelowy magazyn danych](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. Na stronie **Wybieranie pliku lub folderu wyjściowego** wykonaj następujące czynności:
    
    a. Przeglądaj i wybierz folder **docelowy** , a następnie kliknij przycisk **Wybierz**.
    
    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Zapisz ścieżkę do folderu dynamicznego jako **Źródło/{Year}/{Month}/{Day}/{Hour}/** , a następnie Zmień format w następujący sposób:
    
    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    d. Kliknij przycisk **Dalej**.
    
    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. Na stronie **Ustawienia** wybierz przycisk **Dalej**. 

    ![Strona Ustawienia](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Na stronie **Podsumowanie** sprawdź ustawienia, a następnie kliknij przycisk **Dalej**.

    ![Strona podsumowania](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie).
    ](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png) strony wdrożenia ![
    
10. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana.  Musisz poczekać na uruchomienie potoku, gdy zostanie on wyzwolony automatycznie (około godzinę).  Po uruchomieniu kolumna **Actions** zawiera linki do wyświetlania szczegółów uruchomienia działania i ponownego uruchomienia potoku. Wybierz pozycję **Odśwież** , aby odświeżyć listę, a następnie wybierz link **Wyświetl uruchomienia działania** w kolumnie **Akcje** . 

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. W potoku jest tylko jedno działanie (działanie kopiowania), dlatego na liście jest wyświetlana tylko jedna pozycja. Plik źródłowy (plik1. txt) został skopiowany ze **źródła/2019/02/26/14/** do **lokalizacji docelowej/2019/02/26/14/** o tej samej nazwie pliku.  

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    Możesz również sprawdzić to samo przy użyciu Eksplorator usługi Azure Storage (https://storageexplorer.com/) do skanowania plików.
    
    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Utwórz kolejny pusty plik tekstowy o nowej nazwie jako **plik2. txt**. Przekaż plik plik2. txt do folderu **Source Path/2019/02/26/15** na koncie magazynu.   Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).   
    
    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > Należy pamiętać, że należy utworzyć nową ścieżkę folderu. Dostosuj nazwę folderu o czas UTC.  Na przykład, jeśli bieżący czas UTC to 3:20 PM 26 lutego 2019, można utworzyć ścieżkę folderu jako **Źródło/2019/02/26/15/** według reguły **{Year}/{Month}/{Day}/{Hour}/** .
    
13. Aby powrócić do widoku **uruchomienia potoków** , wybierz pozycję **wszystkie uruchomienia**, a następnie poczekaj na automatyczne wyzwolenie tego samego potoku po upływie kolejnej godziny.  

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Wybierz pozycję **Wyświetl przebieg aktywności** dla drugiego przebiegu potoku, a następnie wykonaj te same czynności, aby przejrzeć szczegóły.  

    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    Plik źródłowy (plik2. txt) został skopiowany ze **źródła/2019/02/26/15/** do **lokalizacji docelowej/2019/02/26/15/** o tej samej nazwie pliku.
    
    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    Możesz również sprawdzić to samo przy użyciu Eksplorator usługi Azure Storage (https://storageexplorer.com/) do skanowania plików w kontenerze **docelowym**
    
    ![Monitorowanie uruchomień potoku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Następne kroki
Przejdź do następującego samouczka, aby dowiedzieć się więcej o przekształcaniu danych za pomocą klastra Spark na platformie Azure:

> [!div class="nextstepaction"]
>[Przekształcanie danych przy użyciu klastra Spark w chmurze](tutorial-transform-data-spark-portal.md)