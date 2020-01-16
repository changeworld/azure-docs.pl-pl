---
title: Narzędzie danych do przyrostowego kopiowania nowych i zaktualizowanych plików
description: Utwórz fabrykę danych platformy Azure, a następnie użyj narzędzia Kopiowanie danych, aby przyrostowo ładować nowe pliki na podstawie LastModifiedDate.
services: data-factory
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
ms.openlocfilehash: 291a991542f9d535d2450dfd465196b755c623ac
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982633"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Przyrostowe kopiowanie nowych i zmienionych plików na podstawie LastModifiedDate przy użyciu narzędzia Kopiowanie danych

W tym samouczku utworzysz fabrykę danych za pomocą Azure Portal. Następnie użyjesz narzędzia Kopiowanie danych, aby utworzyć potok, który przyrostowo kopiuje nowe i zmienione pliki, na podstawie ich **LastModifiedDate** z usługi Azure Blob Storage do usługi Azure Blob Storage.

Dzięki temu funkcja ADF przeskanuje wszystkie pliki z magazynu źródłowego, zastosuje filtr plików według ich LastModifiedDate, a następnie skopiuje nowy i zaktualizowany plik tylko od czasu ostatniego do magazynu docelowego.  Należy pamiętać, że jeśli system ADF skanuje ogromne ilości plików, ale tylko kilka plików do miejsca docelowego, nadal oczekuje się, że długi czas trwania jest również czasochłonny.   

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

W tym samouczku zostaną wykonane następujące zadania:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto usługi Azure Storage**: Użyj magazynu obiektów BLOB jako magazynu danych _źródłowych_ i _ujścia_ . Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Tworzenie dwóch kontenerów w usłudze BLOB Storage

Aby przygotować magazyn obiektów BLOB do samouczka, wykonaj te kroki.

1. Utwórz kontener o nazwie **Source**. Do wykonania tego zadania można użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

2. Utwórz kontener o nazwie **Destination**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz zasób** > **dane + analiza** > **Data Factory**:

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa Twojej fabryki danych musi być _globalnie unikatowa_. Może zostać wyświetlony następujący komunikat o błędzie:

   ![Komunikat o błędzie dotyczący nowej fabryki danych](./media/doc-common-process/name-not-available-error.png)

   Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_ **ADFTutorialDataFactory**. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
3. Wybierz **subskrypcję** platformy Azure, w której chcesz utworzyć nową fabrykę danych.
4. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    * Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej.

    * Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md).

5. W obszarze **wersja**wybierz pozycję **v2**.
6. W obszarze **lokalizacja** wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (na przykład Azure Storage i SQL Database) i obliczenia (na przykład usługa Azure HDInsight) używane przez fabrykę danych mogą znajdować się w innych lokalizacjach i regionach.
7. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.
8. Wybierz pozycję **Utwórz**.
9. Na pulpicie nawigacyjnym zapoznaj się z kafelkiem **wdrażanie Data Factory** , aby wyświetlić stan procesu.

    ![Wdrażanie kafelka Data Factory](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Po zakończeniu tworzenia zostanie wyświetlona strona główna usługi **Data Factory**.

    ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)
11. Aby otworzyć Azure Data Factory interfejs użytkownika na oddzielnej karcie, wybierz kafelek **tworzenie & monitor** .

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. **Na stronie Wprowadzenie** wybierz tytuł **Kopiowanie danych** , aby otworzyć narzędzie kopiowanie danych.

   ![Kafelek narzędzia do kopiowania danych](./media/doc-common-process/get-started-page.png)

2. Na stronie **Właściwości** wykonaj następujące czynności:

    a. W obszarze **Nazwa zadania**wprowadź **DeltaCopyFromBlobPipeline**.

    b. W obszarze **zadanie erze** lub **harmonogram zadań**wybierz pozycję **Uruchom regularnie zgodnie z harmonogramem**.

    d. W obszarze **Typ wyzwalacza**wybierz pozycję **okno wirowania**.

    d. W obszarze **cykl**wprowadź **15 minut**.

    e. Wybierz opcję **Dalej**.

    Interfejs użytkownika usługi Data Factory tworzy potok o określonej nazwie zadania.

    ![Strona właściwości](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Na stronie **Źródłowy magazyn danych** wykonaj następujące czynności:

    a. Wybierz pozycję **+ Utwórz nowe połączenie**, aby dodać połączenie.

    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Wybierz pozycję **Azure Blob Storage** z galerii, a następnie wybierz pozycję **Kontynuuj**.

    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    d. Na stronie **Nowa połączona usługa** wybierz konto magazynu z listy **nazwa konta magazynu** , a następnie wybierz pozycję **Zakończ**.

    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Wybierz nowo utworzoną połączoną usługę, a następnie wybierz pozycję **dalej**.

   ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące czynności:

    a. Przeglądaj i wybierz folder **źródłowy** , a następnie wybierz pozycję **Wybierz**.

    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. W obszarze **zachowanie ładowania pliku**wybierz pozycję **obciążenie przyrostowe: LastModifiedDate**.

    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)

    d. Sprawdź **kopię binarną** i wybierz pozycję **dalej**.

     ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Na stronie **docelowy magazyn danych** wybierz pozycję **AzureBlobStorage**. To konto magazynu jest takie samo jak magazyn danych źródłowych. Następnie wybierz przycisk **Dalej**.

    ![Strona Docelowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)

6. Na stronie **Wybieranie pliku lub folderu wyjściowego** wykonaj następujące czynności:

    a. Przeglądaj i wybierz folder **docelowy** , a następnie wybierz pozycję **Wybierz**.

    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Wybierz opcję **Dalej**.

     ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)

7. Na stronie **Ustawienia** wybierz przycisk **Dalej**.

    ![Strona Ustawienia](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)

8. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz przycisk **dalej**.

    ![Strona podsumowania](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie).

    ![Strona Wdrażanie](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Kolumna **Akcje** zawiera linki służące do wyświetlania szczegółów działań i ponownego uruchamiania potoku. Wybierz pozycję **Odśwież** , aby odświeżyć listę, a następnie wybierz link **Wyświetl uruchomienia działania** w kolumnie **Akcje** .

    ![Odśwież listę i wybierz pozycję Wyświetl uruchomienia działań](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. W potoku jest tylko jedno działanie (działanie kopiowania), więc zobaczysz tylko jeden wpis. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Akcje**.

    ![Działanie kopiowania jest w potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Ponieważ nie ma pliku w kontenerze **źródłowym** na koncie magazynu obiektów blob, nie będzie widoczny żaden plik skopiowany do kontenera **docelowego** na koncie magazynu obiektów BLOB.

    ![Brak pliku w kontenerze źródłowym lub kontenerze docelowym](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Utwórz pusty plik tekstowy i nadaj mu nazwę **plik1. txt**. Przekaż ten plik tekstowy do kontenera **źródłowego** na koncie magazynu. Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

    ![Utwórz plik1. txt i Przekaż do kontenera źródłowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Aby powrócić do widoku **uruchomienia potoków** , zaznacz **wszystkie uruchomienia potoków**i poczekaj na automatyczne wyzwolenie tego samego potoku.  

    ![Zaznacz wszystkie uruchomienia potoków](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Wybierz pozycję **Wyświetl przebieg aktywności** dla drugiego uruchomienia potoku, gdy zostanie wyświetlony. Następnie przejrzyj szczegóły w taki sam sposób jak w przypadku pierwszego uruchomienia potoku.  

    ![Wybierz pozycję Wyświetl uruchomienie działania i przejrzyj szczegóły](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Zobaczysz, że jeden plik (plik1. txt) został skopiowany z kontenera **źródłowego** do **docelowego** kontenera konta usługi BLOB Storage.

    ![Plik1. txt skopiowano z kontenera źródłowego do kontenera docelowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Utwórz kolejny pusty plik tekstowy i nadaj mu nazwę **plik2. txt**. Przekaż ten plik tekstowy do kontenera **źródłowego** na koncie magazynu obiektów BLOB.

16. Powtórz kroki od 13 do 14 dla tego drugiego pliku tekstowego. Zobaczysz, że tylko nowy plik (plik2. txt) został skopiowany z kontenera **źródłowego** do **docelowego** kontenera konta magazynu w następnym uruchomieniu potoku.  

    ![Plik2. txt został skopiowany z kontenera źródłowego do kontenera docelowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Można to również sprawdzić za pomocą [Eksplorator usługi Azure Storage](https://storageexplorer.com/) , aby skanować pliki.

    ![Skanuj pliki przy użyciu Eksplorator usługi Azure Storage](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Następne kroki
Przejdź do następującego samouczka, aby dowiedzieć się więcej na temat przekształcania danych przy użyciu klastra Apache Spark na platformie Azure:

> [!div class="nextstepaction"]
>[Przekształcanie danych w chmurze przy użyciu klastra Apache Spark](tutorial-transform-data-spark-portal.md)
