---
title: Kopiowanie danych przy użyciu narzędzia do kopiowania danych platformy Azure | Microsoft Docs
description: Utwórz fabrykę danych platformy Azure, a następnie za pomocą narzędzia do kopiowania danych skopiuj dane z jednej lokalizacji w usłudze Azure Blob Storage do innej lokalizacji.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: aea76544f244adba8368a1d8fbe268746060b5a6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30169829"
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>Kopiowanie danych przy użyciu narzędzia do kopiowania danych 
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Wersja 2 — wersja zapoznawcza](quickstart-create-data-factory-copy-data-tool.md)

W tym przewodniku Szybki start utworzysz fabrykę danych przy użyciu witryny Azure Portal. Następnie za pomocą narzędzia do kopiowania danych utworzysz potok służący do kopiowania danych z folderu w magazynie Azure Blob Storage do innego folderu. 

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, przed wykonaniem kroków zawartych w tym przewodniku Szybki start zobacz [Wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md). 
>
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz ogólnie dostępnej wersji 1 usługi, zobacz [Wprowadzenie do usługi Data Factory w wersji 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Wybierz pozycję **Nowy** w menu po lewej stronie, wybierz pozycję **Dane + analiza**, a następnie wybierz pozycję **Data Factory**. 
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. Na stronie **Nowa fabryka danych** wprowadź wartość **ADFTutorialDataFactory** w polu **Nazwa**. 
      
   ![Strona „Nowa fabryka danych”](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Nazwa fabryki danych platformy Azure musi być *globalnie unikatowa*. Jeśli wystąpi poniższy błąd, zmień nazwę fabryki danych (na przykład **&lt;twojanazwa&gt;ADFTutorialDataFactory**) i spróbuj utworzyć ją ponownie. Artykuł [Usługa Data Factory — reguły nazewnictwa](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
   ![Komunikat o błędzie występujący, jeśli nazwa jest niedostępna](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
4. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy. 
   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
   Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
4. W obszarze **Wersja** wybierz pozycję **V2 (wersja zapoznawcza)**.
5. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych. 

   Na liście wyświetlane są tylko obsługiwane lokalizacje. Magazyny danych (na przykład Azure Storage lub Azure SQL Database) i jednostki obliczeniowe (na przykład HDInsight) używane przez fabrykę danych mogą mieścić się w innych lokalizacjach/regionach.

6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
7. Wybierz pozycję **Utwórz**.
8. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem **Wdrażanie fabryki danych**: 

    ![Kafelek „Wdrażanie fabryki danych”](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**. Wybierz kafelek **Tworzenie i monitorowanie**, aby na osobnej karcie uruchomić aplikację interfejsu użytkownika usługi Azure Data Factory.
   
   ![Strona główna fabryki danych z kafelkiem „Tworzenie i monitorowanie”](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Uruchamianie narzędzia do kopiowania danych

1. Na stronie **Wprowadzenie** wybierz kafelek **Kopiowanie danych**, aby uruchomić narzędzie do kopiowania danych. 

   ![Kafelek „Kopiowanie danych”](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)
2. Na stronie **Właściwości** narzędzia do kopiowania danych wybierz przycisk **Dalej**. Na tej stronie możesz określić nazwę potoku i jego opis. 

   ![Strona „Właściwości”](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. Na stronie **Źródłowy magazyn danych** wybierz pozycję **Azure Blob Storage**, a następnie wybierz przycisk **Dalej**.

   ![Strona „Źródłowy magazyn danych”](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. Na stronie **Określanie konta usługi Azure Blob Storage** wybierz swoje konto magazynu z listy **Nazwa konta magazynu**, a następnie wybierz przycisk **Dalej**. 

   ![Strona „Określanie konta usługi Azure Blob Storage”](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące czynności:

   a. Przejdź do folderu **adftutorial/input**.

   b. Wybierz plik **emp.txt**.

   d. Wybierz pozycję **Wybierz**. Możesz dwukrotnie kliknąć plik **emp.txt**, aby pominąć ten krok.

   d. Wybierz opcję **Dalej**. 

   ![Strona „Wybieranie pliku lub folderu wejściowego”](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. Na stronie **Ustawienia formatu pliku** zwróć uwagę, że narzędzie automatycznie wykrywa ograniczniki kolumn i wierszy, a następnie wybierz przycisk **Dalej**. Na tej stronie możesz także wyświetlić podgląd danych i wyświetlić schematy danych wejściowych. 

   ![Strona „Ustawienia formatu pliku”](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. Na stronie **Magazyn danych docelowych** wybierz pozycję **Azure Blob Storage**, a następnie wybierz przycisk **Dalej**. 

   ![Strona „Magazyn danych docelowych”](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. Na stronie **Określanie konta usługi Azure Blob Storage** wybierz swoje konto usługi Azure Blob Storage, a następnie wybierz przycisk **Dalej**. 

   ![Strona „Określanie konta usługi Azure Blob Storage”](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. Na stronie **Wybieranie pliku lub folderu wyjściowego** wykonaj następujące czynności: 

   a. Wprowadź wartość **adftutorial/output** jako ścieżkę do folderu.

   b. Wprowadź wartość **emp.txt** jako nazwę pliku.

   d. Wybierz opcję **Dalej**. 

   ![Strona „Wybieranie pliku lub folderu wyjściowego”](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. Na stronie **Ustawienia formatu pliku** wybierz przycisk **Dalej**. 

    ![Strona „Ustawienia formatu pliku”](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. Na stronie **Ustawienia** wybierz przycisk **Dalej**. 

    ![Strona „Ustawienia”](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. Sprawdź wszystkie ustawienia na stronie **Podsumowanie**, a następnie wybierz przycisk **Dalej**. 

    ![Strona „Podsumowanie”](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. Na stronie **Zakończono wdrożenie** wybierz pozycję **Monitoruj**, aby monitorować utworzony potok. 

    ![Strona „Zakończono wdrożenie”](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. Aplikacja zostanie przełączona na kartę **Monitorowanie**. Na tej karcie wyświetlany jest stan potoku. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 
    
    ![Karta monitorowania przebiegów potoków z przyciskiem „Odśwież”](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. Wybierz link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Potok ma tylko jedno działanie typu **Kopiowanie**. 

    ![Lista uruchomień działania](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. Aby wyświetlić szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (obraz okularów) w kolumnie **Akcje**. Aby uzyskać więcej informacji o właściwościach, zobacz [Omówienie działania kopiowania](copy-activity-overview.md). 

    ![Szczegóły operacji kopiowania](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. Sprawdź, czy plik **emp.txt** został utworzony w folderze **output** w kontenerze **adftutorial**. Jeśli folder wyjściowy nie istnieje, usługa Data Factory automatycznie go utworzy. 
18. Przejdź do karty **Edycja**, aby móc edytować połączone usługi, zestawy danych i potoki. Aby dowiedzieć się więcej o edytowaniu tych elementów w interfejsie użytkownika usługi Data Factory, zobacz [Tworzenie fabryki danych za pomocą witryny Azure Portal](quickstart-create-data-factory-portal.md).

    ![Karta Edycja](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. Zapoznaj się z [samouczkami](tutorial-copy-data-portal.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach. 