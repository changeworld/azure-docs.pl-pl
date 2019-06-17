---
title: Ładowanie danych do usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Kopiowanie danych do usługi Azure SQL Data Warehouse za pomocą usługi Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 6a7e0a27d3cda4193a04467d541f851a9e57fa46
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60549096"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Ładowanie danych do usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory

[Usługa Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) to oparta na chmurze, skalowalnego w poziomie baza danych, która może przetwarzać ogromne ilości danych relacyjnych i nierelacyjnych. Usługa SQL Data Warehouse jest oparta na architekturę masowego przetwarzania równoległego (MPP), która jest zoptymalizowana pod kątem obciążeń magazynu danych przedsiębiorstwa. Elastycznie Skaluj magazyn i obliczenia niezależnie oferuje elastyczność chmury.

Wprowadzenie do usługi Azure SQL Data Warehouse jest teraz łatwiejsze niż kiedykolwiek gdy używasz usługi Azure Data Factory. Usługa Azure Data Factory to usługa integracji danych w pełni zarządzane w chmurze. Korzystać z niej, aby wypełnić usłudze SQL Data Warehouse przy użyciu danych z istniejącego systemu i zaoszczędzić czas podczas tworzenia rozwiązań do analizy.

Usługa Azure Data Factory oferuje następujące korzyści dotyczące ładowania danych do usługi Azure SQL Data Warehouse:

* **Łatwe do skonfigurowania**: Intuicyjny Kreator krok 5 z bez skryptu wymagane.
* **Szczegółowe dane sklepu obsługują**: Wbudowana obsługa bogaty zestaw w środowisku lokalnym i magazynami danych w chmurze. Aby uzyskać szczegółową listę, zobacz tabelę [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
* **Bezpieczeństwo i zgodność**: Dane są przesyłane za pośrednictwem protokołu HTTPS lub usługi ExpressRoute. Obecność usługi global service gwarantuje, że danych nigdy nie opuszcza granicy geograficznej.
* **Zapewnia niezrównaną wydajność przy użyciu programu PolyBase**: Program Polybase jest najbardziej skutecznym sposobem przenoszenia danych do usługi Azure SQL Data Warehouse. Funkcja przejściowego obiektu blob do osiągnięcia szybkości dużym obciążeniem z wszystkich typów magazynów danych, w tym usługi Azure Blob storage i Data Lake Store. (Funkcja Polybase obsługuje usługi Azure Blob storage i Azure Data Lake Store domyślnie). Aby uzyskać więcej informacji, zobacz [wydajności działania kopiowania](copy-activity-performance.md).

W tym artykule pokazano, jak za pomocą narzędzia Kopia fabryki danych do _ładowanie danych z usługi Azure SQL Database do usługi Azure SQL Data Warehouse_. Możesz wykonać podobne kroki w celu skopiowania danych z innych typów magazynów danych.

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz [kopiowanie danych do i z usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Azure SQL Data Warehouse: Magazyn danych przechowuje dane, które jest kopiowana z bazy danych SQL. Jeśli nie masz usługi Azure SQL Data Warehouse, zapoznaj się z instrukcjami w [utworzyć SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: W tym samouczku kopiuje dane z usługi Azure SQL database za pomocą Adventure Works LT przykładowych danych. Można utworzyć bazę danych SQL, postępując zgodnie z instrukcjami wyświetlanymi w [utworzyć bazę danych Azure SQL](../sql-database/sql-database-get-started-portal.md). 
* Konto magazynu Azure: Usługa Azure Storage jest używana jako _przemieszczania_ obiektu blob w operacji kopiowania zbiorczego. Jeśli nie masz konta usługi Azure Storage, zobacz instrukcje podane w temacie [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md).

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. W menu po lewej stronie wybierz **Utwórz zasób** > **dane + analiza** > **usługi Data Factory**: 
   
   ![Wybór usługi Data Factory w okienku „Nowy”](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. W **nowa fabryka danych** Podaj wartości dla pól, które są wyświetlane na poniższej ilustracji:
      
   ![Strona Nowa fabryka danych](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Nazwa**: Wprowadź unikatową w skali globalnej nazwę fabryki danych platformy Azure. Jeśli zostanie wyświetlony błąd "Nazwa fabryki danych \"LoadSQLDWDemo\" jest niedostępny," Wprowadź inną nazwę dla fabryki danych. Na przykład można użyć nazwy  _**twojanazwa**_ **ADFTutorialDataFactory**. Spróbuj ponownie utworzyć fabrykę danych. Artykuł [Data Factory naming rules (Zasady nazewnictwa fabryki danych)](naming-rules.md) zawiera zasady nazewnictwa artefaktów usługi Data Factory.
    * **Subskrypcja**: Wybieranie subskrypcji platformy Azure, w której chcesz utworzyć fabrykę danych. 
    * **Grupa zasobów**: Wybierz istniejącą grupę zasobów z listy rozwijanej lub **Utwórz nową** opcji, a następnie wprowadź nazwę grupy zasobów. Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
    * **Wersja**: Wybierz **V2**.
    * **Lokalizacja**: Wybierz lokalizację fabryki danych. Na liście rozwijanej są wyświetlane tylko obsługiwane lokalizacje. Magazyny danych, które są używane przez fabrykę danych mogą być w innych lokalizacjach i regionach. Te magazyny danych obejmują usługi Azure Data Lake Store, Azure Storage, Azure SQL Database i tak dalej.

3. Wybierz pozycję **Utwórz**.
4. Po zakończeniu tworzenia przejdź z fabryką danych. Zostanie wyświetlony **usługi Data Factory** strony głównej, jak pokazano na poniższej ilustracji:
   
   ![Strona główna fabryki danych](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Wybierz **tworzenie i monitorowanie** Kafelek, aby uruchomić aplikację integracji danych w osobnej karcie.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Ładowanie danych do usługi Azure SQL Data Warehouse

1. W **wprowadzenie** wybierz opcję **kopiowania danych** Kafelek, aby uruchomić narzędzie do kopiowania danych:

   ![Kafelek narzędzia do kopiowania danych](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. W **właściwości** określ **CopyFromSQLToSQLDW** dla **Nazwa zadania** i wybierz przycisk **dalej**:

    ![Strona właściwości](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. W **magazynu danych źródłowych** strony, wykonaj następujące czynności:

    a. Kliknij przycisk **+ Utwórz nowe połączenie**:

    ![Strona Źródłowy magazyn danych](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Wybierz **usługi Azure SQL Database** z galerii, a następnie wybierz pozycję **Kontynuuj**. Możesz wpisać "SQL" w polu wyszukiwania, aby filtrować łączników.

    ![Wybieranie bazy danych Azure SQL](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. W **nowa połączona usługa** stronie, wybierz nazwę serwera i nazwa bazy danych z listy rozwijanej i określić nazwę użytkownika i hasło. Kliknij przycisk **Testuj połączenie** Aby zweryfikować ustawienia, zaznacz opcję **Zakończ**.
   
    ![Konfigurowanie bazy danych Azure SQL](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Wybierz nowo utworzoną połączoną usługę jako źródło, a następnie kliknij pozycję **Dalej**.

    ![Wybieranie połączonej usługi źródłowej](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. W **Wybieranie tabel, z których mają być kopiowane dane, lub używanie zapytania niestandardowego** wpisz **SalesLT** filtrującą dane w tabelach. Wybierz **(Zaznacz wszystko)** korzystać ze wszystkich tabel kopii, a następnie wybierz **dalej**: 

    ![Wybieranie tabel źródłowych](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. W **docelowego magazynu danych** strony, wykonaj następujące czynności:

    a. Kliknij pozycję **+Utwórz nowe połączenie**, aby dodać połączenie.

    ![Strona magazyn danych ujścia](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Wybierz **Azure SQL Data Warehouse** z galerii, a następnie wybierz pozycję **dalej**.

    ![Wybierz magazyn danych Azure SQL](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. W **nowa połączona usługa** stronie, wybierz nazwę serwera i nazwa bazy danych z listy rozwijanej i określić nazwę użytkownika i hasło. Kliknij przycisk **Testuj połączenie** Aby zweryfikować ustawienia, zaznacz opcję **Zakończ**.
   
    ![Skonfiguruj Magazyn danych Azure SQL](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Wybierz nowo utworzoną połączoną usługę jako ujście, a następnie kliknij pozycję **Dalej**.

    ![Wybieranie połączonej usługi ujścia](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. W **Mapowanie tabeli** strony, przejrzyj zawartość i wybierz **dalej**. Mapowanie tabeli inteligentne są wyświetlane. Tabel źródłowych są mapowane do tabel docelowych na podstawie nazw tabeli. Jeśli tabela źródłowa nie istnieje w miejscu docelowym, usługi Azure Data Factory tworzy tabelę docelową o takiej samej nazwie, domyślnie. Tabela źródłowa może również mapować do istniejącej tabeli docelowej. 

   > [!NOTE]
   > Tworzenie automatycznego tabeli ujścia SQL Data Warehouse ma zastosowanie, gdy program SQL Server lub usługi Azure SQL Database jest źródłem. Kopiujesz dane z innego źródła danych magazynu, należy wstępnie tworzony jest schemat w ujściu usługi Azure SQL Data Warehouse przed wykonaniem kopii danych.

   ![Strona Mapowanie tabeli](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. W **mapowanie schematu** strony, przejrzyj zawartość i wybierz **dalej**. Mapowanie tabeli inteligentne opiera się na nazwę kolumny. Jeśli wybierzesz opcję fabryki danych, automatyczne tworzenie tabel, konwersja typu danych może wystąpić, gdy występują niezgodności między źródłowym i docelowym magazynami. W przypadku konwersji typu nieobsługiwanych danych między kolumnami źródłowym i docelowym, zobaczysz komunikat o błędzie obok odpowiedniej tabeli.

    ![Strona Mapowanie schematu](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. W **ustawienia** strony, wykonaj następujące czynności:

    a. W **przemieszczania ustawienia** kliknij **+ nowy** do nowego magazynu przejściowego. Magazyn jest używany do przemieszczania danych przed załadowaniem do usługi SQL Data Warehouse przy użyciu programu PolyBase. Po zakończeniu kopiowania pośrednie dane w usłudze Azure Storage jest automatycznie czyszczone. 

    ![Konfigurowanie przemieszczania](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. W **nowa połączona usługa** stronie, wybierz konto magazynu, a następnie wybierz **Zakończ**.
   
    ![Skonfiguruj usługę Azure Storage](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. W **Zaawansowane ustawienia** sekcji, usuń zaznaczenie **domyślny typ użycia** opcji, a następnie wybierz **dalej**.

    ![Konfigurowanie funkcji PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. W **Podsumowanie** strony, przejrzyj ustawienia i wybierz **dalej**:

    ![Strona podsumowania](./media/load-azure-sql-data-warehouse/summary-page.png)
1. W **strony wdrażania**, wybierz opcję **Monitor** monitorować potok (zadanie):

    ![Strona Wdrażanie](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Zwróć uwagę, że karta **Monitor** po lewej stronie jest automatycznie wybrana. **Akcje** kolumna zawiera linki, aby wyświetlić szczegóły uruchamiania działania i ponowne uruchamianie potoku: 

    ![Monitorowanie uruchomień potoku](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Aby wyświetlić uruchomienia działań, które są skojarzone z uruchomieniem potoku, wybierz pozycję **Wyświetl uruchomienia działań** łącze w **akcje** kolumny. Aby wrócić do widoku uruchomienia potoku, wybierz **potoki** link u góry. Wybierz pozycję **Odśwież**, aby odświeżyć listę. 

    ![Monitorowanie uruchomień działania](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Aby monitorować szczegóły wykonania dla każdego działania kopiowania, wybierz **szczegóły** łącze w obszarze **akcje** w działaniu, w widoku monitorowania. Możesz monitorować szczegółowe informacje, takie jak ilość danych skopiowanych ze źródła do ujścia, przepływność danych, wykonywania kroków za pomocą odpowiedni czas i konfiguracje używane:

    ![Szczegóły uruchamiania działania monitora](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Kolejne kroki

Przejdź z następującym artykułem, aby dowiedzieć się więcej o obsłudze usługi Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Łącznik usługi Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)
