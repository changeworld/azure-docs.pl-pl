---
title: Ładowania terabajtów danych do usługi SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Pokazuje, jak 1 TB danych może być załadowany do usługi Azure SQL Data Warehouse mniej niż 15 minut przy użyciu usługi Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e275411f9fd9dfb672bb0815e83e37bcd5d1dda9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60825424"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Ładowanie 1 TB w usłudze Azure SQL Data Warehouse w niecałe 15 minut przy użyciu usługi fabryka danych
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [kopiowanie danych do i z usługi Azure SQL Data Warehouse przy użyciu usługi Data Factory](../connector-azure-sql-data-warehouse.md).


[Usługa Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) to oparta na chmurze, skalowalnego w poziomie baza danych jest w stanie przetwarzać ogromne ilości danych relacyjnych i nierelacyjnych.  Oparta na architekturę masowego przetwarzania równoległego (MPP), usługa SQL Data Warehouse jest zoptymalizowana pod kątem obciążeń magazynu danych przedsiębiorstwa.  Elastycznie Skaluj magazyn i obliczenia niezależnie oferuje elastyczność chmury.

Wprowadzenie do usługi Azure SQL Data Warehouse jest teraz łatwiejsze niż kiedykolwiek wcześniej przy użyciu **usługi Azure Data Factory**.  Azure Data Factory to w pełni zarządzane dane oparte na chmurze Usługa integracji, która może służyć do wypełniania usłudze SQL Data Warehouse przy użyciu danych z istniejącego systemu i zapisywanie cenny czas podczas oceny usługa SQL Data Warehouse i tworzenia analizy rozwiązania. Poniżej przedstawiono główne zalety ładowania danych do usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory:

* **Łatwe do skonfigurowania**: krok 5 intuicyjny Kreator z bez skryptu wymagane.
* **Obsługa magazynu danych sformatowanego**: wbudowaną obsługę bogaty zestaw w środowisku lokalnym i magazynami danych w chmurze.
* **Bezpieczeństwo i zgodność**: dane są przesyłane za pośrednictwem protokołu HTTPS lub usługi ExpressRoute i obecności usługi global service zapewnia dane nigdy nie opuszcza granicy geograficznej
* **Zapewnia niezrównaną wydajność przy użyciu programu PolyBase** — przy użyciu technologii Polybase jest najbardziej skutecznym sposobem przenoszenia danych do usługi Azure SQL Data Warehouse. Funkcja przejściowego obiektu blob, można osiągnąć szybkość dużym obciążeniem z wszystkich typów magazynów danych oprócz usługi Azure Blob storage, która domyślnie obsługuje programu Polybase.

W tym artykule pokazano, jak ładowanie 1 TB danych z usługi Azure Blob Storage do usługi Azure SQL Data Warehouse w mniej niż 15 minut na ponad 1,2 GB/s przepływności za pomocą Kreatora kopiowania usługi Data Factory.

Ten artykuł zawiera szczegółowe instrukcje dotyczące przenoszenia danych do usługi Azure SQL Data Warehouse przy użyciu Kreatora kopiowania.

> [!NOTE]
>  Aby uzyskać ogólne informacje dotyczące możliwości usługi Data Factory podczas przenoszenia danych do i z usługi Azure SQL Data Warehouse, zobacz [przenoszenie danych do i z usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) artykułu.
>
> Możesz także tworzyć potoki przy użyciu witryny Azure portal, programu Visual Studio, PowerShell, itp. Zobacz [samouczka: Kopiowanie danych z obiektów Blob platformy Azure do usługi Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) szybki Przewodnik z instrukcjami krok po kroku, za pomocą działania kopiowania w usłudze Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Wymagania wstępne
* Usługa Azure Blob Storage: tego eksperymentu przy użyciu usługi Azure Blob Storage (GRS) TPC-H testowych w zestawie danych.  Dowiedz się, jeśli nie masz konta usługi Azure storage, [sposób tworzenia konta magazynu](../../storage/common/storage-quickstart-create-account.md).
* [TPC-H](http://www.tpc.org/tpch/) danych: będziemy używać TPC-H jako testowego zestawu danych.  Aby to zrobić, należy użyć `dbgen` z zestawu narzędzi TPC-H, która ułatwia generowanie zestawu danych.  Możesz pobrać kod źródłowy `dbgen` z [narzędzia TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) i skompilować samodzielnie, lub pobrać skompilowanym plikiem binarnym z [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Uruchamianie dbgen.exe za pomocą następujących poleceń do generowania pliku prostego 1 TB dla `lineitem` tabeli rozprzestrzeniania się między 10 plików:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Teraz skopiuj wygenerowane pliki do obiektów Blob platformy Azure.  Zapoznaj się [przenoszenie danych do i z systemu plików w środowisku lokalnym za pomocą usługi Azure Data Factory](data-factory-onprem-file-system-connector.md) jak to zrobić przy użyciu kopiowania usługi ADF.    
* Usługa Azure SQL Data Warehouse: tego eksperymentu powoduje załadowanie danych do usługi Azure SQL Data Warehouse utworzonych za pomocą 6000 jednostek dwu

    Zapoznaj się [Tworzenie usługi Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) szczegółowe instrukcje dotyczące sposobu tworzenia bazy danych SQL Data Warehouse.  Aby uzyskać najlepszą wydajność ładowania możliwych do usługi SQL Data Warehouse przy użyciu technologii Polybase, firma Microsoft wybierz maksymalną liczbę jednostek magazynu danych (dwu) dozwolone w ustawieniach wydajności jest 6000 jednostek dwu.

  > [!NOTE]
  > Podczas ładowania z obiektów Blob platformy Azure, wydajność ładowania danych jest wprost proporcjonalna do liczby jednostek dwu, skonfiguruj w usłudze SQL Data Warehouse:
  >
  > Ładowanie 1 TB do 1000 jednostek DWU usługa SQL Data Warehouse uwzględnia 87 minut (~ 200 MB/s przepływności) ładowanie 1 TB 2000 jednostek DWU usługa SQL Data Warehouse przyjmuje 46 minut (~ 380 MB/s przepływności) ładowanie 1 TB w 6000 jednostek DWU usługa SQL Data Warehouse ma 14 minut (przepływność ~1.2 GB/s)
  >
  >

    Aby utworzyć w usłudze SQL Data Warehouse przy użyciu 6000 jednostek dwu, przesuń suwak wydajności aż po prawej stronie:

    ![Suwak wydajności](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Dla istniejącej bazy danych nie jest skonfigurowany z 6000 jednostek dwu możesz skalować go przy użyciu witryny Azure portal.  Przejdź do bazy danych w witrynie Azure portal, a istnieje **skalowania** znajdujący się w **Przegląd** panelu pokazano na poniższej ilustracji:

    ![Przycisk skali](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Kliknij przycisk **skalowania** przycisk, aby otworzyć panel następujące, przesuń suwak na wartość maksymalna, a następnie kliknij przycisk **Zapisz** przycisku.

    ![Okno dialogowe skalowania](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Ten eksperyment powoduje załadowanie danych do usługi Azure SQL Data Warehouse przy użyciu `xlargerc` klasy zasobów.

    Aby osiągnąć najlepsze możliwe przepływności, kopia musi zostać wykonana przy pomocy użytkownika SQL Data Warehouse, należący do `xlargerc` klasy zasobów.  Dowiedz się, jak to zrobić, wykonując [zmienić przykład klasy zasobów użytkownika](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Tworzenie schematu tabeli docelowej w bazie danych Azure SQL Data Warehouse, uruchamiając następującą instrukcję DDL:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  Dzięki usłudze kroki wymagań wstępnych zakończone możemy teraz wszystko gotowe do skonfigurowania działania kopiowania przy użyciu Kreatora kopiowania.

## <a name="launch-copy-wizard"></a>Uruchamianie Kreatora kopiowania
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **Utwórz zasób** w lewym górnym rogu, kliknij **rozwiązania inteligentne + analiza**i kliknij przycisk **usługi Data Factory**.
3. W **nowa fabryka danych** okienka:

   1. Wprowadź **LoadIntoSQLDWDataFactory** dla **nazwa**.
       Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd: **Nazwa fabryki danych "LoadIntoSQLDWDataFactory" nie jest dostępna**, Zmień nazwę fabryki danych (na przykład yournameLoadIntoSQLDWDataFactory) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.  
   2. Wybierz swoją **subskrypcję** platformy Azure.
   3. Wykonaj jedną z następujących czynności dotyczącą grupy zasobów:
      1. Wybierz pozycję **Użyj istniejącej**, aby wybrać istniejącą grupę zasobów.
      2. Wybierz pozycję **Utwórz nowy**, aby wprowadzić nazwę grupy zasobów.
   4. Wybierz **lokalizację** fabryki danych.
   5. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego** u dołu bloku.  
   6. Kliknij pozycję **Utwórz**.
4. Po zakończeniu tworzenia zostanie wyświetlony blok **Fabryka danych**, jak pokazano na poniższej ilustracji:

   ![Strona główna fabryki danych](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Na stronie głównej usługi Fabryka danych kliknij kafelek **Kopiowanie danych**, aby uruchomić **Kreatora kopiowania**.

   > [!NOTE]
   > Jeśli widać, że przeglądarka sieci Web jest zablokowana podczas wyświetlania komunikatu „Autoryzowanie...”, wyłącz ustawienie **Blokuj pliki cookie i dane witryn innych firm** lub pozostaw je włączone i utwórz wyjątek dla witryny **login.microsoftonline.com**, a następnie spróbuj ponownie uruchomić kreatora.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Krok 1: Skonfiguruj harmonogram ładowania danych
Pierwszym krokiem jest skonfigurować harmonogram ładowania danych.  

Na stronie **Właściwości**:

1. Wprowadź **CopyFromBlobToAzureSqlDataWarehouse** dla **Nazwa zadania**
2. Wybierz **uruchom raz teraz** opcji.   
3. Kliknij przycisk **Dalej**.  

    ![Kreator kopiowania — strona właściwości](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Krok 2: Konfigurowanie źródła
W tej sekcji przedstawiono kroki, aby skonfigurować źródła: Obiekt Blob platformy Azure, zawierający TPC 1 TB pojemności-H pozycji pliki.

1. Wybierz **usługi Azure Blob Storage** przechowywania danych, a następnie kliknij przycisk **dalej**.

    ![Kreator kopiowania — strona wybierz źródło](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Podaj informacje o połączeniu dla konta usługi Azure Blob storage i kliknij przycisk **dalej**.

    ![Kreator kopiowania — informacje o źródle połączenia](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Wybierz **folderu** zawierający TPC-H wiersz pliki elementu, a następnie kliknij przycisk **dalej**.

    ![Kreator kopiowania — Wybieranie folderu danych wejściowych](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Po kliknięciu **dalej**, ustawienia formatu pliku są wykrywane automatycznie.  Upewnij się, że ogranicznik kolumny jest "|"zamiast przecinek domyślną",".  Kliknij przycisk **dalej** po przejrzeniu danych.

    ![Kreator kopiowania — ustawienia formatu pliku](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Krok 3: Skonfigurowane miejsce docelowe
W tej sekcji dowiesz się, jak skonfigurować miejsce docelowe: `lineitem` tabeli w bazie danych Azure SQL Data Warehouse.

1. Wybierz **Azure SQL Data Warehouse** jako miejsce docelowe przechowywania, a następnie kliknij przycisk **dalej**.

    ![Kreator kopiowania — wybierz docelowy magazyn danych](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Wypełnij informacje o połączeniu dla usługi Azure SQL Data Warehouse.  Upewnij się, że użytkownik, który jest elementem członkowskim roli `xlargerc` (zobacz **wymagania wstępne** sekcji, aby uzyskać szczegółowe instrukcje) i kliknij przycisk **dalej**.

    ![Kreator kopiowania — informacje o połączeniu docelowego](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Wybierz tabelę docelową, a następnie kliknij przycisk **dalej**.

    ![Skopiuj Kreator — strona Mapowanie tabeli](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na stronie mapowanie schematu zaznaczaj opcji "Zastosuj mapowania kolumn", a następnie kliknij przycisk **dalej**.

## <a name="step-4-performance-settings"></a>Krok 4: Ustawienia wydajności

**Zezwól na program polybase** jest zaznaczone domyślnie.  Kliknij przycisk **Dalej**.

![Skopiuj Kreator — strona mapowanie schematu](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Krok 5. Wdrażanie i monitorowanie wyników obciążenia
1. Kliknij przycisk **Zakończ** przycisk, aby wdrożyć.

    ![Kreator kopiowania — strona podsumowania](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Po zakończeniu wdrożenia kliknij `Click here to monitor copy pipeline` do monitorowania kopiowania Uruchom postępu. Wybierz utworzony w potok kopiowania **Windows działanie** listy.

    ![Kreator kopiowania — strona podsumowania](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Możesz wyświetlić Kopiuj szczegóły przebiegu **Eksploratorze okien działania** w prawym panelu, w tym ilość danych odczytu ze źródła i zapisane w docelowym, czas trwania i średniej przepływności dla przebiegu.

    Jak widać na poniższym zrzucie ekranu, kopiowania 1 TB z usługi Azure Blob Storage do usługi SQL Data Warehouse trwało 14 min, efektywnie osiągnięcia 1,22 GB/s przepływności!

    ![Kreator kopiowania — okno dialogowe zostało zakończone pomyślnie](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Najlepsze praktyki
Poniżej przedstawiono kilka najlepszych rozwiązań dotyczących uruchamiania bazy danych Azure SQL Data Warehouse:

* Użycie większej klasy zasobów, podczas ładowania do INDEKSU KLASTROWANEGO magazynu kolumn.
* Dla bardziej wydajne sprzężeń należy wziąć pod uwagę przy użyciu dystrybucji skrótów, zaznacz kolumnę, zamiast domyślnego round robin dystrybucji.
* Dla szybkość ładowania należy wziąć pod uwagę przy użyciu sterty dla danych przejściowych.
* Tworzenie statystyk po zakończeniu ładowania usługi Azure SQL Data Warehouse.

Zobacz [najlepsze rozwiązania dotyczące usługi Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) Aby uzyskać szczegółowe informacje.

## <a name="next-steps"></a>Kolejne kroki
* [Kreator kopiowania usługi Data Factory](data-factory-copy-wizard.md) — ten artykuł zawiera szczegółowe informacje o kreatorze kopiowania.
* [Skopiuj wydajności i działania przewodnika dostrajania](data-factory-copy-activity-performance.md) — ten artykuł zawiera Przewodnik dostosowywania i pomiarów wydajności.
