---
title: Kopiowanie danych przy użyciu narzędzia do kopiowania danych platformy Azure | Microsoft Docs
description: Utwórz fabrykę danych platformy Azure, a następnie za pomocą narzędzia do kopiowania danych skopiuj dane z jednej lokalizacji w usłudze Azure Blob Storage do innej lokalizacji.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: yexu
ms.openlocfilehash: b330c6010ddb5401dbf9753c2ea91bfeedf35c3b
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020053"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Szybki start: Kopiowanie danych przy użyciu narzędzia do kopiowania danych

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](quickstart-create-data-factory-copy-data-tool.md)

W tym przewodniku Szybki start utworzysz fabrykę danych przy użyciu witryny Azure Portal. Następnie za pomocą narzędzia do kopiowania danych utworzysz potok służący do kopiowania danych z folderu w magazynie Azure Blob Storage do innego folderu. 

> [!NOTE]
> Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, przed wykonaniem kroków zawartych w tym przewodniku Szybki start zobacz [Wprowadzenie do usługi Azure Data Factory](data-factory-introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). 
1. Wybierz pozycję **Utwórz zasób** w menu po lewej stronie, a następnie pozycje **Analiza** i **Data Factory**. 
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na stronie **Nowa fabryka danych** wprowadź wartość **ADFTutorialDataFactory** w polu **Nazwa**. 
 
   Nazwa fabryki danych platformy Azure musi być *globalnie unikatowa*. Jeśli wystąpi poniższy błąd, zmień nazwę fabryki danych (na przykład **&lt;twojanazwa&gt;ADFTutorialDataFactory**) i spróbuj utworzyć ją ponownie. Artykuł [Usługa Data Factory — reguły nazewnictwa](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
   ![Komunikat o błędzie występujący, jeśli nazwa jest niedostępna](./media/doc-common-process/name-not-available-error.png)
1. W obszarze **Subskrypcja** wybierz subskrypcję platformy Azure, w której chcesz utworzyć fabrykę danych. 
1. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy. 
   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
   Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
1. W obszarze **Wersja** wybierz pozycję **V2**.
1. W obszarze **Lokalizacja** wybierz lokalizację fabryki danych.

   Ta lista zawiera tylko lokalizacje, które są obsługiwane przez usługę Data Factory i w których będą przechowywane metadane usługi Azure Data Factory. Skojarzone magazyny danych (np. usługi Azure Storage i Azure SQL Database) i usługi obliczeniowe (np. usługa Azure HDInsight) używane przez Data Factory mogą działać w innych regionach.

1. Wybierz pozycję **Utwórz**.

1. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**. Wybierz kafelek **Tworzenie i monitorowanie**, aby na osobnej karcie uruchomić aplikację interfejsu użytkownika usługi Azure Data Factory.
   
   ![Strona główna fabryki danych z kafelkiem „Tworzenie i monitorowanie”](./media/doc-common-process/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Uruchamianie narzędzia do kopiowania danych

1. Na stronie **Wprowadzenie** wybierz kafelek **Kopiowanie danych**, aby uruchomić narzędzie do kopiowania danych. 

   ![Kafelek „Kopiowanie danych”](./media/doc-common-process/get-started-page.png)

1. Na stronie **Właściwości** narzędzia do kopiowania danych możesz określić nazwę potoku i jego opis, a następnie wybrać przycisk **Dalej**. 

   ![Strona „Właściwości”](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Na stronie **Źródłowy magazyn danych** wykonaj następujące czynności:

    a. Kliknij pozycję **+Utwórz nowe połączenie**, aby dodać połączenie.

    b. Wybierz pozycję **Azure Blob Storage** z galerii, a następnie wybierz pozycję **Kontynuuj**.

    c. Na stronie **Nowa połączona usługa (Azure Blob Storage)** Określ nazwę połączonej usługi. Wybierz konto magazynu z listy **nazwa konta magazynu** , Testuj połączenie, a następnie wybierz pozycję **Zakończ**. 

   ![Konfigurowanie konta usługi Azure Blob Storage](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Wybierz nowo utworzoną połączoną usługę jako źródło, a następnie kliknij przycisk **dalej**.


1. Na stronie **Wybieranie pliku lub folderu wejściowego** wykonaj następujące czynności:

   a. Kliknij przycisk **Przeglądaj** , aby przejść do folderu **adftutorial/Input** , wybierz plik **EMP. txt** , a następnie kliknij przycisk **Wybierz**. 

   d. Zaznacz pole wyboru **Kopiuj binarnie** , aby skopiować plik jako-is, a następnie wybierz przycisk **dalej**. 

   ![Strona „Wybieranie pliku lub folderu wejściowego”](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. Na stronie **docelowy magazyn danych** Wybierz utworzoną usługę połączoną **BLOB Storage Azure** , a następnie wybierz pozycję **dalej**. 

1. Na stronie **Wybieranie pliku lub folderu wyjściowego** wprowadź **adftutorial/Output** dla ścieżki folderu, a następnie wybierz przycisk **dalej**. 

   ![Strona „Wybieranie pliku lub folderu wyjściowego”](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. Na stronie **Ustawienia** wybierz przycisk **Dalej**, aby użyć konfiguracji domyślnych. 

1. Sprawdź wszystkie ustawienia na stronie **Podsumowanie**, a następnie wybierz przycisk **Dalej**. 

1. Na stronie **Zakończono wdrożenie** wybierz pozycję **Monitoruj**, aby monitorować utworzony potok. 

    ![Strona „Zakończono wdrożenie”](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. Aplikacja zostanie przełączona na kartę **Monitorowanie**. Na tej karcie wyświetlany jest stan potoku. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 
    
1. Wybierz link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Potok ma tylko jedno działanie typu **Kopiowanie**. 
    
1. Aby wyświetlić szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (obraz okularów) w kolumnie **Akcje**. Aby uzyskać więcej informacji o właściwościach, zobacz [Omówienie działania kopiowania](copy-activity-overview.md).

1. Sprawdź, czy plik **emp.txt** został utworzony w folderze **output** w kontenerze **adftutorial**. Jeśli folder wyjściowy nie istnieje, Usługa Data Factory automatycznie go utworzy. 

1. Przejdź do karty **Tworzenie** powyżej karty **Monitorowanie** na lewym panelu, aby móc edytować połączone usługi, zestawy danych i potoki. Aby dowiedzieć się więcej o edytowaniu tych elementów w interfejsie użytkownika usługi Data Factory, zobacz [Tworzenie fabryki danych za pomocą witryny Azure Portal](quickstart-create-data-factory-portal.md).

## <a name="next-steps"></a>Następne kroki
Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. Zapoznaj się z [samouczkami](tutorial-copy-data-portal.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach. 