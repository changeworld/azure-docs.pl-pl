---
title: Przyrostowe kopiowanie nowych i zmienionych plików, w oparciu o Data ostatniej modyfikacji przy użyciu narzędzia do kopiowania danych | Dokumentacja firmy Microsoft
description: Tworzenie fabryki danych platformy Azure, a następnie użyj narzędzia do kopiowania danych przyrostowe ładowanie nowych plików, w oparciu o Data ostatniej modyfikacji.
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
ms.openlocfilehash: 8308190e0e68365343fb50ca33f9bea75c3e4e66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61099052"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Przyrostowe kopiowanie nowych i zmienionych plików, w oparciu o Data ostatniej modyfikacji przy użyciu narzędzia do kopiowania danych

W tym samouczku użyjesz witryny Azure portal do utworzenia fabryki danych. Następnie użyjemy narzędzia do kopiowania danych utworzysz potok, który przyrostowo kopiuje nowych i zmienionych plików, na podstawie ich **Data ostatniej modyfikacji** z usługi Azure Blob storage do usługi Azure Blob storage.

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto usługi Azure Storage**: Użyj usługi Blob storage jako _źródła_ i _ujścia_ magazynu danych. Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Utworzenia dwóch kontenerów w magazynie obiektów Blob

Przygotuj usługi Blob storage na potrzeby samouczka, wykonując następujące kroki.

1. Utwórz kontener o nazwie **źródła**. Można użyć różnych narzędzi do wykonania tego zadania, takie jak [Eksploratora usługi Azure Storage](https://storageexplorer.com/).

2. Utwórz kontener o nazwie **docelowy**. 

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz **Utwórz zasób** > **dane + analiza** > **usługi Data Factory**: 
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**. 
      
     ![Nowa fabryka danych](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Nazwa Twojej fabryki danych musi być _globalnie unikatowa_. Może zostać wyświetlony następujący komunikat o błędzie:
   
   ![Komunikat o błędzie dotyczący nowej fabryki danych](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_**ADFTutorialDataFactory**. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
3. Wybierz pozycję Azure **subskrypcji** , w której utworzysz nową fabrykę danych. 
4. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
    * Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    * Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/resource-group-overview.md).

5. W obszarze **wersji**, wybierz opcję **V2**.
6. W obszarze **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (na przykład Azure Storage i SQL Database) oraz jednostki obliczeniowe (na przykład, Azure HDInsight) używane przez fabrykę danych mogą być w innych lokalizacjach i regionach.
7. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**. 
8. Wybierz pozycję **Utwórz**.
9. Na pulpicie nawigacyjnym dotyczą **wdrażanie fabryki danych** Kafelek, aby wyświetlić stan procesu.

    ![Kafelek wdrażanie fabryki danych](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Po zakończeniu tworzenia zostanie wyświetlona strona główna usługi **Data Factory**.
   
    ![Strona główna fabryki danych](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Aby otworzyć Interfejs użytkownika usługi Azure Data Factory (UI) na osobnej karcie, wybierz **tworzenie i monitorowanie** kafelka. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. Na **zaczynajmy** wybierz opcję **kopiowania danych** tytuł, aby otworzyć narzędzie do kopiowania danych. 

   ![Kafelek narzędzia do kopiowania danych](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Na **właściwości** strony, wykonaj następujące czynności:

    a. W obszarze **Nazwa zadania**, wprowadź **DeltaCopyFromBlobPipeline**.

    b. W obszarze **tempa zadań** lub **harmonogramu zadań**, wybierz opcję **regularnie uruchamiana zgodnie z harmonogramem**.

    c. W obszarze **typu wyzwalacza**, wybierz opcję **okno wirowania**.
    
    d. W obszarze **cyklu**, wprowadź **15 min**. 
    
    e. Wybierz opcję **Dalej**. 
    
    Interfejs użytkownika usługi Data Factory tworzy potok o określonej nazwie zadania. 

    ![Strona właściwości](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Na stronie **Źródłowy magazyn danych** wykonaj następujące czynności:

    a. Wybierz **+ Utwórz nowe połączenie**, aby dodać połączenie.
    
    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Wybierz **usługi Azure Blob Storage** z galerii, a następnie wybierz **Kontynuuj**.
    
    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na **nowa połączona usługa** stronie, wybierz konto magazynu z **nazwa konta magazynu** listy, a następnie wybierz pozycję **Zakończ**.
    
    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Wybierz nowo utworzony połączoną usługę, a następnie wybierz pozycję **dalej**. 
    
   ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące czynności:
    
    a. Wyszukaj i wybierz **źródła** folder, a następnie wybierz **wybierz**.
    
    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. W obszarze **zachowanie podczas ładowania pliku**, wybierz opcję **ładowanie przyrostowe: LastModifiedDate**.
    
    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Sprawdź **kopia binarna** i wybierz **dalej**.
    
     ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Na **docelowego magazynu danych** wybierz opcję **usłudze Azure blob Storage**. To samo konto magazynu do przechowywania danych źródłowych. Następnie wybierz przycisk **Dalej**.

    ![Strona Docelowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Na stronie **Wybieranie pliku lub folderu wyjściowego** wykonaj następujące czynności:
    
    a. Wyszukaj i wybierz **docelowy** folder, a następnie wybierz **wybierz**.
    
    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Wybierz opcję **Dalej**.
    
     ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Na stronie **Ustawienia** wybierz przycisk **Dalej**. 

    ![Strona Ustawienia](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Na **Podsumowanie** strony, przejrzyj ustawienia a następnie wybierz **dalej**.

    ![Strona podsumowania](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie).

    ![Strona Wdrażanie](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Kolumna **Akcje** zawiera linki służące do wyświetlania szczegółów działań i ponownego uruchamiania potoku. Wybierz **Odśwież** Aby odświeżyć listę, a następnie wybierz **Wyświetl uruchomienia działania** łącze w **akcje** kolumny. 

    ![Odśwież listę i wybierz pozycję Wyświetl uruchomienia działania](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Potok jest tylko jedno działanie (działanie kopiowania), tak aby było widać tylko jedna pozycja. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Akcje**. 

    ![Działanie kopiowania w potoku jest](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Ponieważ nie ma pliku w **źródła** kontenera na koncie magazynu obiektów Blob, nie będzie mógł przeglądać dowolnego pliku skopiowane do **docelowy** kontenera na koncie usługi Blob storage.
    
    ![Nie plików w kontenerze źródłowy lub docelowy kontener](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Utwórz pusty plik tekstowy i nadaj mu **więc Plik1.txt**. Przekazywanie pliku tekstowego **źródła** kontenera na koncie magazynu. Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).   

    ![Utwórz więc Plik1.txt i przekazać do kontenera źródłowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Aby wrócić do **uruchomienia potoku** widoku, wybierz opcję **wszystkie uruchomienia potoków**i poczekaj na tym samym potoku ponownie automatyczne wyzwolenie.  

    ![Wybierz wszystkie uruchomienia potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Wybierz **uruchamiania działania widoku** do drugiego potoku uruchamiać, gdy zostanie wyświetlony. Następnie przejrzyj szczegóły w taki sam sposób jak dla pierwszego uruchomienia potoku.  

    ![Wybierz opcję uruchamiania działania widoku i przejrzyj szczegóły](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Dowiesz się, że zobacz jeden plik (więc Plik1.txt) został skopiowany z **źródła** kontener **docelowy** kontenera konta magazynu obiektów Blob.
    
    ![Więc Plik1.txt zostały skopiowane z kontenera źródłowego do docelowego kontenera](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Utwórz inny pusty plik tekstowy i nadaj mu **Plik2.txt**. Przekazywanie pliku tekstowego **źródła** kontenera na koncie usługi Blob storage.   
    
16. Powtórz kroki od 13 i 14 ten drugi plik tekstowy. Zobaczysz, że tylko nowy plik (Plik2.txt) zostały skopiowane z **źródła** kontener **docelowy** kontenera konta magazynu w ramach następnego uruchomienia potoku.  
    
    ![Plik2.txt zostały skopiowane z kontenera źródłowego do docelowego kontenera](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Można również sprawdzić to za pomocą [Eksploratora usługi Azure Storage](https://storageexplorer.com/) skanowanie plików.
    
    ![Skanuj pliki za pomocą Eksploratora usługi Azure Storage](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Kolejne kroki
Przejdź do poniższego samouczka, aby dowiedzieć się więcej na temat przekształcania danych przy użyciu klastra Apache Spark na platformie Azure:

> [!div class="nextstepaction"]
>[Przekształcanie danych w chmurze przy użyciu klastra Apache Spark](tutorial-transform-data-spark-portal.md)