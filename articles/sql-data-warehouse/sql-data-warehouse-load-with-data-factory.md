---
title: "Ładowanie danych do usługi Azure SQL Data Warehouse — fabryki danych | Dokumentacja firmy Microsoft"
description: "W tym samouczku ładuje dane do usługi Azure SQL Data Warehouse przy użyciu fabryki danych Azure i korzysta z bazy danych programu SQL Server jako źródła danych."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse;azure-data-factory
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: loading
ms.date: 02/08/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: 6399f1a3390119685c1c9fd7332937e0cdb6f9ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-into-sql-data-warehouse-with-data-factory"></a>Ładowanie danych do usługi SQL Data Warehouse z fabryką danych

Fabryka danych Azure umożliwia ładowanie danych do usługi Azure SQL Data Warehouse za pomocą dowolnego [obsługiwane magazyny danych źródła](../data-factory/copy-activity-overview.md). Na przykład można ładowania danych z bazy danych Azure SQL lub z bazą danych programu Oracle do magazynu danych SQL przy użyciu fabryki danych. Samouczek, w tym artykule przedstawiono sposób ładowanie danych z lokalną bazą danych programu SQL Server do usługi SQL data warehouse.

**Szacowanie czasu**: tego samouczka trwa około 10 – 15 minut do wykonania po spełnieniu wymagań wstępnych.

## <a name="prerequisites"></a>Wymagania wstępne

- Należy **bazy danych programu SQL Server** z tabel zawierających dane do kopiowana za pośrednictwem usługi SQL data warehouse.  

- Należy online **SQL Data Warehouse**. Jeśli nie masz już magazyn danych, Dowiedz się jak [Utwórz magazyn danych SQL Azure](sql-data-warehouse-get-started-provision.md).

- Należy **konta magazynu Azure**. Dowiedz się, jeśli nie masz już konto magazynu, jak [Utwórz konto magazynu](../storage/common/storage-create-storage-account.md). Aby uzyskać najlepszą wydajność Zlokalizuj konto magazynu i magazynu danych w tym samym regionie Azure.

## <a name="configure-a-data-factory"></a>Skonfiguruj fabryki danych
1. Zaloguj się do witryny [Azure Portal][].
2. Znajdź magazynu danych i kliknij, aby go otworzyć.
3. W głównym bloku, kliknij przycisk **danych obciążenia** > **fabryki danych Azure**.

    ![Uruchom Kreatora ładowanie danych](media/sql-data-warehouse-load-with-data-factory/launch-load-data-wizard.png)

4. Jeśli nie ma fabryki danych w ramach subskrypcji platformy Azure, zobacz **nowa fabryka danych** okno dialogowe w osobnej karcie przeglądarki. Podaj żądane informacje, a następnie kliknij przycisk **Utwórz**. Po utworzeniu fabryki danych **nowa fabryka danych** okno dialogowe zostanie zamknięte i zostanie wyświetlony **fabryki danych wybierz** okno dialogowe.

    Jeśli masz co najmniej jeden fabryki danych już w subskrypcji platformy Azure, zobacz **fabryki danych wybierz** okno dialogowe. W tym oknie można wybrać istniejącą fabrykę danych lub kliknij przycisk **Utwórz nowy fabryki danych** Aby utworzyć nowy.

    ![Konfigurowanie usługi fabryka danych](media/sql-data-warehouse-load-with-data-factory/configure-data-factory.png)

5. W **fabryki danych wybierz** okno dialogowe **ładowanie danych** opcja jest domyślnie zaznaczona. Kliknij przycisk **dalej** aby rozpocząć tworzenie danych podczas ładowania zadania.

## <a name="configure-the-data-factory-properties"></a>Skonfiguruj właściwości fabryki danych
Teraz, po utworzeniu fabryki danych, następnym krokiem jest skonfigurowanie harmonogramu ładowania danych.

1. Aby uzyskać **Nazwa zadania**, wprowadź **DWLoadData fromSQLServer**.
2. Użyj domyślnej **uruchom raz teraz** kliknij przycisk **dalej**.

    ![Skonfiguruj harmonogram obciążenia](media/sql-data-warehouse-load-with-data-factory/configure-load-schedule.png)

## <a name="configure-the-source-data-store-and-gateway"></a>Konfigurowanie magazynu danych źródła i bramy
Teraz nakaż fabryki danych o lokalną bazą danych programu SQL Server, z którego chcesz załadować danych.

1. Wybierz **programu SQL Server** danych źródłowych obsługiwanych przechowywania katalogu, a następnie kliknij przycisk **dalej**.

    ![Wybierz źródło programu SQL Server](media/sql-data-warehouse-load-with-data-factory/choose-sql-server-source.png)

2. A **Określ lokalną bazą danych programu SQL Server** zostanie wyświetlone okno dialogowe. Pierwszy **nazwa połączenia** pole jest wypełniane automatycznie. Drugie pole poprosi o podanie nazwy **bramy**. Jeśli korzystasz z istniejącą fabrykę danych, która ma już bramy, można ponownie użyć bramy, wybierając ją z listy rozwijanej. Kliknij przycisk **Tworzenie bramy** łącze, aby utworzyć bramę zarządzania danymi.  

    > [!NOTE]
    > Jeśli źródła magazynu danych działa lokalnie lub w maszynie wirtualnej platformy Azure IaaS, jest wymagana brama zarządzania danymi. Brama ma relację 1-1 z fabryką danych. Nie można użyć z innego fabryki danych, ale mogą służyć przez wiele danych podczas ładowania zadania związane z z tej samej fabryki danych. Brama może służyć do łączenia się wiele magazynów danych podczas uruchamiania zadania ładowania danych.
    >
    > Aby uzyskać szczegółowe informacje dotyczące bramy, zobacz [brama zarządzania danymi](../data-factory/v1/data-factory-data-management-gateway.md) artykułu.

3. A **Tworzenie bramy** zostanie wyświetlone okno dialogowe. Wprowadź nazwę, **GatewayForDWLoading**i kliknij przycisk **Utwórz**.

4. A **konfigurowania bramy** zostanie wyświetlone okno dialogowe. Kliknij przycisk **uruchamiania instalacji ekspresowej na tym komputerze** do automatycznego pobierania Zainstaluj i zarejestruj bramę zarządzania danymi na bieżącym komputerze. Postęp jest wyświetlany w oknie podręcznym. Jeśli komputer nie może połączyć się z magazynem danych, możesz ręcznie [Pobierz i zainstaluj bramę](https://www.microsoft.com/download/details.aspx?id=39717) na komputerze, na którym można połączyć się z danymi przechowywania, a następnie użyć klucza do zarejestrowania.
    > [!NOTE]
    > Instalacja ekspresowa natywnie współpracuje z Microsoft Edge i przeglądarki Internet Explorer. Jeśli używasz przeglądarki Google Chrome, należy najpierw zainstalować rozszerzenia ClickOnce ze sklepu Chrome web store.

    ![Uruchamianie instalacji ekspresowej](media/sql-data-warehouse-load-with-data-factory/launch-express-setup.png)

5. Poczekaj, aż do ukończenia instalacji bramy. Po bramy zostało pomyślnie zarejestrowane i jest w trybie online, zamyka okno podręczne, a nowej bramy są wyświetlane w polu Brama. Następnie wypełnij pozostałe wymagane pola w następujący sposób, kliknij przycisk **dalej**.
    - **Nazwa serwera**: Nazwa lokalnego serwera SQL.
    - **Nazwa bazy danych**: baza danych SQL Server.
    - **Poświadczeń szyfrowania**: Użyj domyślnej "przez przeglądarkę sieci web".
    - **Typ uwierzytelniania**: Wybierz typ uwierzytelniania są używane.
    - **Nazwa użytkownika** i **hasło**: Wprowadź nazwę użytkownika i hasło dla użytkownika, który ma uprawnienia do kopiowania danych.

    ![Uruchamianie instalacji ekspresowej](media/sql-data-warehouse-load-with-data-factory/configure-sql-server.png)

6. Następnym krokiem jest wybierz tabele, z których chcesz skopiować dane. Tabele można filtrować przy użyciu słów kluczowych. I można wyświetlić podgląd danych i tabeli schematu w dolnego panelu. Po zakończeniu wybór, kliknij przycisk **dalej**.

    ![Wybór tabel](media/sql-data-warehouse-load-with-data-factory/select-tables.png)

## <a name="configure-the-destination-your-sql-data-warehouse"></a>Skonfigurowane miejsce docelowe magazynu danych SQL

Teraz nakaż fabryki danych o informacji o lokalizacji docelowej.

1. Informacje o połączeniu magazyn danych SQL jest wypełniane automatycznie. Wprowadź hasło dla nazwy użytkownika. i kliknij przycisk **dalej**.

    ![Skonfigurowane miejsce docelowe](media/sql-data-warehouse-load-with-data-factory/configure-destination.png)

2. Mapowania tabeli inteligentnego pojawia się, że mapy źródła do tabel docelowych, na podstawie tabeli nazw. Jeśli tabela nie istnieje w docelowym, domyślnie ADF będzie utworzyć o takiej samej nazwie (dotyczy to SQL Server lub baza danych SQL Azure jako źródła). Można również mapować do istniejącej tabeli. Przejrzyj i kliknij przycisk **dalej**.

    ![Mapowanie tabel](media/sql-data-warehouse-load-with-data-factory/table-mapping.png)

3. Przejrzyj mapowanie schematu i odszukaj ostrzeżeń i komunikatów o błędach. Mapowanie inteligentnego jest oparta na nazwę kolumny. W przypadku konwersji typu nieobsługiwanych danych między kolumną źródłowym i docelowym, zostanie wyświetlony komunikat o błędzie równolegle z tej tabeli. Jeśli chcesz umożliwić fabryki danych automatycznego tworzenia tabel, konwersja typu danych właściwe może się tak zdarzyć w razie potrzeby, aby naprawić niezgodności między magazynami źródłowym i docelowym.

    ![Mapowanie schematu](media/sql-data-warehouse-load-with-data-factory/schema-mapping.png)

4. Kliknij przycisk **Dalej**.

## <a name="configure-the-performance-settings"></a>Skonfigurowanie ustawień wydajności
W konfiguracji wydajności, skonfiguruj konta magazynu platformy Azure używana do przemieszczania danych przed załadowaniem do usługi SQL Data Warehouse performantly przy użyciu [PolyBase](sql-data-warehouse-best-practices.md#use-polybase-to-load-and-export-data-quickly). Po zakończeniu kopiowania Tymczasowe dane w magazynie zostaną automatycznie wyczyszczone.

Wybierz istniejące konto magazynu Azure, a następnie kliknij przycisk **dalej**.

![Skonfiguruj tymczasowych obiektów blob](media/sql-data-warehouse-load-with-data-factory/configure-staging-blob.png)

## <a name="review-summary-information-and-deploy-the-pipeline"></a>Przejrzyj podsumowanie i wdróż potok

Przejrzyj konfigurację i kliknij przycisk **Zakończ** przycisk, aby wdrożyć potoku.

![Wdrażanie usługi fabryka danych](media/sql-data-warehouse-load-with-data-factory/deploy-data-factory.png)

## <a name="monitor-data-loading-progress"></a>Trwa ładowanie danych monitora

Można wyświetlić postęp wdrażania i powoduje **wdrożenia** strony.

1. Po zakończeniu wdrożenia kliknij łącze **kliknij tutaj, aby monitor kopiowania potoku** do monitorowania postępu ładowania danych.

    ![Monitorowanie potoku](media/sql-data-warehouse-load-with-data-factory/monitor-pipeline.png)

2. Nowo utworzony **DWLoadData fromSQLServer** potoku podczas ładowania danych zostanie automatycznie wybrany z lewym **Eksploratora zasobów**.

    ![Widok procesu](media/sql-data-warehouse-load-with-data-factory/view-pipeline.png)

3. Kliknij przycisk z potokiem w środkowej panelu, aby wyświetlić szczegółowy stan dla każdej tabeli, który jest mapowany na działanie.

    ![Widok tabeli działania](media/sql-data-warehouse-load-with-data-factory/view-table-activity.png)

4. Kliknij dalej do działania i zostaną wyświetlone dane ładowania szczegółów na prawym panelu, takich jak rozmiar danych, wiersze, przepływności, itp.

    ![Wyświetl szczegóły działania tabeli](media/sql-data-warehouse-load-with-data-factory/view-table-activity-details.png)

5. Można uruchomić tego widoku monitorowania nowsze, przejdź do magazynu danych SQL, kliknij przycisk **danych obciążenia > fabryki danych Azure**, wybierz fabryką i wybierz **monitorowanie istniejących podczas ładowania zadania**.

## <a name="next-steps"></a>Następne kroki

Aby przeprowadzić migrację bazy danych SQL Data Warehouse, zobacz [Omówienie migracji](sql-data-warehouse-overview-migrate.md).

Aby dowiedzieć się więcej na temat fabryki danych Azure i jego możliwości przepływu danych, zobacz następujące artykuły:

- [Wprowadzenie do usługi Azure Data Factory](../data-factory/introduction.md)
- [Przenoszenie danych za pomocą działania kopiowania](../data-factory/copy-activity-overview.md)
- [Przenoszenie danych do i z usługi SQL Data Warehouse przy użyciu usługi Azure Data Factory](../data-factory/connector-azure-sql-data-warehouse.md)

Aby eksplorować dane w usłudze SQL Data Warehouse, zobacz następujące artykuły:

- [Nawiązać połączenia SQL Data Warehouse przy użyciu programu Visual Studio i narzędzi SSDT](sql-data-warehouse-query-visual-studio.md)
- [Visual danych za pomocą usługi Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

<!-- Azure references -->
[Azure Portal]: https://portal.azure.com
