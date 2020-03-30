---
title: Narzędzie Dane do kopiowania nowych i zaktualizowanych plików przyrostowo
description: Utwórz fabrykę danych platformy Azure, a następnie użyj narzędzia Kopiuj dane, aby stopniowo ładować nowe pliki na podstawie LastModifiedDate.
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
ms.date: 3/18/2020
ms.openlocfilehash: 743f9dd8f7998178a75d716f4da22efee2b3bc79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131006"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Przyrostowo kopiuj nowe i zmienione pliki na podstawie lastmodifieddate za pomocą narzędzia Kopiuj dane

W tym samouczku użyjesz witryny Azure portal do utworzenia fabryki danych. Następnie użyjesz narzędzia Kopiuj dane, aby utworzyć potok, który przyrostowo kopiuje tylko nowe i zmienione pliki, na podstawie ich **lastmodifiedDate** z magazynu obiektów Blob platformy Azure do magazynu obiektów blob platformy Azure.

W ten sposób ADF przeskanuje wszystkie pliki ze sklepu źródłowego, zastosuje filtr plików przez ich LastModifiedDate i skopiuje nowy i zaktualizowany plik tylko od ostatniego czasu do magazynu docelowego.  Należy pamiętać, że jeśli pozwolisz ADF skanować ogromne ilości plików, ale tylko skopiować kilka plików do miejsca docelowego, nadal można oczekiwać, że długi czas trwania ze względu na skanowanie plików jest czasochłonne, jak również.   

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie potoku za pomocą narzędzia do kopiowania danych.
> * Monitorowanie uruchomień potoku i działań.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto magazynu platformy Azure:** Użyj magazynu obiektów Blob jako magazynu danych _źródłowych_ i _magazynu ujścia._ Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Tworzenie dwóch kontenerów w magazynie obiektów Blob

Przygotuj magazyn obiektów Blob do samouczka, wykonując te kroki.

1. Utwórz kontener o nazwie **źródło**. Do wykonania tego zadania można użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

2. Utwórz kontener o nazwie **miejsce docelowe**.

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz pozycję **Utwórz źródło** > **Data + Analytics** > **Data Factory**:

   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na stronie **Nowa fabryka danych** w polu **Nazwa** wprowadź wartość **ADFTutorialDataFactory**.

   Nazwa Twojej fabryki danych musi być _globalnie unikatowa_. Może zostać wyświetlony następujący komunikat o błędzie:

   ![Komunikat o błędzie dotyczący nowej fabryki danych](./media/doc-common-process/name-not-available-error.png)

   Jeśli zostanie wyświetlony komunikat o błędzie dotyczącym wartości nazwy, wprowadź inną nazwę dla fabryki danych. Na przykład użyj nazwy _**twojanazwa**_**ADFTutorialDataFactory**. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
3. Wybierz **subskrypcję** platformy Azure, w której utworzysz nową fabrykę danych.
4. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:

    * Wybierz **pozycję Użyj istniejącej** i wybierz istniejącą grupę zasobów z listy rozwijanej.

    * Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów. 
         
    Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources (Używanie grup zasobów do zarządzania zasobami platformy Azure)](../azure-resource-manager/management/overview.md).

5. W **wersji**wybierz **V2**.
6. W obszarze **Lokalizacja**wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych (na przykład usługa Azure Storage i baza danych SQL) i oblicza (na przykład usługi Azure HDInsight), które używa fabryka danych w innych lokalizacjach i regionach.
8. Wybierz **pozycję Utwórz**.
9. Po zakończeniu tworzenia zostanie wyświetlona strona główna usługi **Data Factory**.
10. Aby otworzyć interfejs użytkownika usługi Azure Data Factory (UI) na osobnej karcie, wybierz kafelek **Autor & Monitor.**

    ![Strona główna fabryki danych](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Tworzenie potoku za pomocą narzędzia do kopiowania danych

1. Na stronie **Rozpocznijmy pracę** wybierz tytuł **Kopiuj dane,** aby otworzyć narzędzie Kopiuj dane.

   ![Kafelek narzędzia do kopiowania danych](./media/doc-common-process/get-started-page.png)

2. Na stronie **Właściwości** należy wykonać następujące czynności:

    a. W **obszarze Nazwa zadania**wpisz **DeltaCopyFromBlobPipeline**.

    b. W obszarze **Rytm zadania** lub **Harmonogram zadań**wybierz pozycję Uruchom regularnie zgodnie **z harmonogramem**.

    d. W obszarze **Typ wyzwalacza**wybierz pozycję **Okno tumbling**.

    d. W obszarze **Cykl**wprowadź **15 minut.**

    e. Wybierz **pozycję Dalej**.

    Interfejs użytkownika usługi Data Factory tworzy potok o określonej nazwie zadania.

    ![Strona właściwości](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Na stronie **Źródłowy magazyn danych** wykonaj następujące czynności:

    a. Wybierz **+ Utwórz nowe połączenie**, aby dodać połączenie.

    b. Wybierz **pozycję Usługa Azure Blob Storage** z galerii, a następnie wybierz pozycję **Kontynuuj**.

    ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    d. Na stronie **Nowa usługa połączona (usługa Azure Blob Storage)** wybierz konto magazynu z listy **Nazwa konta magazynu.** Przetestuj połączenie, a następnie wybierz pozycję **Utwórz**.

    d. Wybierz nowo utworzoną usługę połączeniową, a następnie wybierz pozycję **Dalej**.

   ![Strona Źródłowy magazyn danych](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące czynności:

    a. Przeglądaj i zaznacz folder **źródłowy,** a następnie wybierz pozycję **Wybierz**.

    ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. W obszarze **Zachowanie ładowania plików**wybierz pozycję Obciążenie **przyrostowe: Ostatnia data duszna**.

    d. Zaznacz **opcję Kopia binarna** i wybierz pozycję **Dalej**.

     ![Wybieranie pliku lub folderu wejściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Na stronie **Docelowy magazyn danych** wybierz **azureblobStorage,** który został utworzony. Jest to to samo konto magazynu co źródłowy magazyn danych. Następnie wybierz **przycisk Dalej**.

6. Na stronie **Wybieranie pliku lub folderu wyjściowego** wykonaj następujące czynności:

    a. Przeglądaj i zaznacz folder **docelowy,** a następnie wybierz pozycję **Wybierz**.

    ![Wybieranie pliku lub folderu wyjściowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Wybierz **pozycję Dalej**.

7. Na stronie **Ustawienia** wybierz przycisk **Dalej**.

8. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz pozycję **Dalej**.

    ![Strona podsumowania](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Na **stronie Wdrażanie** wybierz pozycję **Monitorowanie**, aby monitorować potok (zadanie).

    ![Strona Wdrażanie](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. Aplikacja przełączy się na kartę **Monitor.** Zobaczysz stan potoku. Wybierz pozycję **Odśwież**, aby odświeżyć listę. Kliknij łącze w obszarze **NAZWA POTOKU,** aby wyświetlić szczegóły uruchomienia działania lub ponownie uruchomić potok. 

    ![Lista Odświeżanie i wybierz pozycję Wyświetl przebiegi aktywności](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Istnieje tylko jedno działanie (działanie kopiowania) w potoku, więc widzisz tylko jeden wpis. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz **łącze Szczegóły** (ikona okularów) w kolumnie **NAZWA DZIAŁANIA.** Aby uzyskać więcej informacji o właściwościach, zobacz [Omówienie działania kopiowania](copy-activity-overview.md). 

    ![Działanie kopiowania jest w toku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Ponieważ nie ma pliku w kontenerze **źródłowym** na koncie magazynu obiektów Blob, nie będzie widoczny żaden plik skopiowany do kontenera **docelowego** na koncie magazynu obiektów Blob.

    ![Brak pliku w kontenerze źródłowym lub docelowym](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Utwórz pusty plik tekstowy i nadaj jego nazwę **plikowi1.txt**. Przekaż ten plik tekstowy do kontenera **źródłowego** na koncie magazynu. Do wykonania tych zadań możesz użyć różnych narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

    ![Tworzenie pliku file1.txt i przekazywanie do kontenera źródłowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Aby wrócić do widoku **Przebiegi potoku,** wybierz **opcję Wszystkie uruchomienia potoku**i poczekaj, aż ten sam potok zostanie automatycznie uruchomiony.  

    ![Wybierz wszystkie przebiegi potoku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Po zakończeniu drugiego uruchomienia potoku wykonaj te same kroki wymienione powyżej, aby przejrzeć szczegóły uruchomienia działania.  

    Zobaczysz, że jeden plik (file1.txt) został skopiowany z **kontenera źródłowego** do kontenera **docelowego** konta magazynu obiektów Blob.

    ![Plik File1.txt został skopiowany z kontenera źródłowego do kontenera docelowego](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Utwórz kolejny pusty plik tekstowy i nadaj jego nazwę **plikowi file2.txt**. Przekaż ten plik tekstowy do kontenera **źródłowego** na koncie magazynu obiektów Blob.

16. Powtórz kroki 13 i 14 dla tego drugiego pliku tekstowego. Zobaczysz, że tylko nowy plik (file2.txt) został skopiowany z **kontenera źródłowego** do kontenera **docelowego** konta magazynu w następnym uruchomieniu potoku.  

    Można to również sprawdzić za pomocą [Usługi Azure Storage Explorer](https://storageexplorer.com/) do skanowania plików.

    ![Skanowanie plików przy użyciu Eksploratora usługi Azure Storage](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Następne kroki
Przejdź do następującego samouczka, aby dowiedzieć się więcej o przekształcaniu danych przy użyciu klastra Platformy Spark apache na platformie Azure:

> [!div class="nextstepaction"]
>[Przekształcanie danych w chmurze przy użyciu klastra Platformy Spark Apache](tutorial-transform-data-spark-portal.md)
