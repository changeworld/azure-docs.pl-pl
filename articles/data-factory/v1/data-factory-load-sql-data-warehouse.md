---
title: Ładowanie terabajtów danych do usługi SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Pokazuje, jak 1 TB danych mogą być ładowane do usługi Azure SQL Data Warehouse z fabryką danych Azure w obszarze 15 minut
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 957183e1aad225af6655d0cb65aa62d356f48567
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620395"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Ładowanie 1 TB do usługi Azure SQL Data Warehouse w obszarze 15 minut przy użyciu fabryki danych
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [kopiowanie danych do i z usługi Azure SQL Data Warehouse przy użyciu fabryki danych w wersji 2](../connector-azure-sql-data-warehouse.md).


[Usługa Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) oparte na chmurze, skalowalnych w poziomie bazy danych jest w stanie przetwarzanie bardzo dużych woluminów danych relacyjnych i nierelacyjnych.  Zbudowany w oparciu o architekturę masowego przetwarzania równoległego (MPP), usługa SQL Data Warehouse jest zoptymalizowana pod kątem obciążeń magazynu danych przedsiębiorstwa.  Zapewnia elastyczność chmury elastycznie skalować magazyn i zasobów obliczeniowych niezależnie.

Wprowadzenie do korzystania z usługi Azure SQL Data Warehouse jest teraz łatwiejsze niż kiedykolwiek przy użyciu **fabryki danych Azure**.  Fabryka danych Azure to usługa integracji pełni zarządzanych danych oparte na chmurze, używany do wypełniania SQL Data Warehouse przy użyciu danych z istniejącego systemu i zapisywanie cenny czas podczas oceny usługi SQL Data Warehouse i tworzenia rozwiązań analizy. Poniżej przedstawiono najważniejsze zalety ładowania danych do usługi Azure SQL Data Warehouse przy użyciu fabryki danych Azure:

* **Można skonfigurować**: Kreator intuicyjne krok 5 z bez skryptu wymagane.
* **Obsługa magazynu danych sformatowanego**: wbudowaną obsługę bogaty zestaw lokalnymi i magazyny danych oparte na chmurze.
* **Bezpieczne i zgodne**: dane są przesyłane za pośrednictwem protokołu HTTPS lub ExpressRoute i zapewnia obecności usługi globalne dane nigdy nie przekracza granic geograficznych
* **Bezkonkurencyjne wydajności przy użyciu programu PolyBase** — przy użyciu programu Polybase jest najbardziej wydajnym sposobem przenoszenia danych do usługi Azure SQL Data Warehouse. Funkcja tymczasowych obiektów blob, można osiągnąć szybkości wysokie obciążenie ze wszystkich typów magazynów danych oprócz magazynu obiektów Blob platformy Azure, który aparat Polybase obsługuje domyślnie.

W tym artykule przedstawiono sposób korzystania z Kreatora kopiowania fabryki danych ładowanie 1 TB danych z magazynu obiektów Blob Azure do usługi Azure SQL Data Warehouse w obszarze 15 minut na ponad 1,2 GB/s przepustowości.

Ten artykuł zawiera instrukcje krok po kroku przenoszenie danych do usługi Azure SQL Data Warehouse przy użyciu Kreatora kopiowania.

> [!NOTE]
>  Aby uzyskać ogólne informacje o możliwościach fabryki danych podczas przenoszenia danych do/z usługi Azure SQL Data Warehouse, zobacz [przenoszenie danych do i z usługi Azure SQL Data Warehouse przy użyciu fabryki danych Azure](data-factory-azure-sql-data-warehouse-connector.md) artykułu.
>
> Można również tworzyć przy użyciu portalu Azure, programu Visual Studio, programu PowerShell, potoki itp. Zobacz [samouczek: kopiowanie danych z obiektu Blob Azure do usługi Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) szybkie przewodnik krok po kroku instrukcje dotyczące używania działania kopiowania w fabryce danych Azure.  
>
>

## <a name="prerequisites"></a>Wymagania wstępne
* Azure Blob Storage: tego eksperymentu używa magazynu obiektów Blob Azure (GRS) do przechowywania TPC-H testowych w zestawie danych.  Dowiedz się, jeśli nie masz konta magazynu platformy Azure, [jak utworzyć konto magazynu](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* [TPC-H](http://www.tpc.org/tpch/) danych: Firma Microsoft będzie używany TPC-H jako testowego zestawu danych.  W tym celu należy użyć `dbgen` z zestawu narzędzi TPC-H, który pomaga wygenerowania zestawu dataset.  Albo można pobrać kodu źródłowego dla `dbgen` z [narzędzia TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) i skompiluj go samodzielnie lub pobrać skompilowanym plikiem binarnym z [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Uruchom dbgen.exe za pomocą następujących poleceń, aby wygenerować plik prosty 1 TB dla `lineitem` tabeli rozpowszechniania przez 10 plików:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Teraz skopiuj wygenerowany pliki do obiektów Blob platformy Azure.  Zapoznaj się [przenoszenie danych do i z lokalnego systemu plików przy użyciu fabryki danych Azure](data-factory-onprem-file-system-connector.md) dla, jak to zrobić przy użyciu kopii ADF.    
* Usługa Azure SQL Data Warehouse: tego eksperymentu ładuje dane do magazynu danych SQL Azure utworzonych za pomocą dwu 6000

    Zapoznaj się [Utwórz magazyn danych SQL Azure](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) szczegółowe informacje dotyczące sposobu tworzenia bazy danych SQL Data Warehouse.  Aby uzyskać najlepszą wydajność obciążenia możliwych do usługi SQL Data Warehouse przy użyciu programu Polybase, możemy wybierz maksymalną liczbę jednostek magazynu danych (dwu) dozwolone w ustawieniu wydajności jest 6000 jednostek dwu.

  > [!NOTE]
  > Podczas ładowania z obiektów Blob platformy Azure, ładowanie wydajności danych jest wprost proporcjonalny do liczby jednostek dwu Skonfiguruj na magazyn danych SQL:
  >
  > Ładowanie 1 TB do 1000 DWU usługa SQL Data Warehouse uwzględnia 87 minut (przepływność ~ 200 MB/s) podczas ładowania 1 TB 2000 DWU usługa SQL Data Warehouse przyjmuje 46 minut (przepływność ~ 380 MB/s) podczas ładowania 1 TB w 6000 DWU usługa SQL Data Warehouse trwa 14 minut (przepływność ~1.2 GB/s)
  >
  >

    Aby utworzyć magazyn danych SQL z dwu 6000, przesuń suwak wydajności do prawej:

    ![Suwak wydajności](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Dla istniejącej bazy danych nie jest skonfigurowany z dwu 6000 można go skalować przy użyciu portalu Azure.  Przejdź do bazy danych w portalu Azure, a nie **skali** przycisk **omówienie** panelu pokazano na poniższej ilustracji:

    ![Przycisk skali](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Kliknij przycisk **skali** przycisk Otwórz panel następujące, przesuń suwak na wartość maksymalna, a następnie kliknij przycisk **zapisać** przycisku.

    ![Okno dialogowe skalowania](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Tego eksperymentu ładuje dane do usługi Azure SQL Data Warehouse przy użyciu `xlargerc` klasy zasobów.

    Aby uzyskać najlepsze możliwe przepływności, kopii musi zostać wykonana przy użyciu usługi SQL Data Warehouse użytkownik należący do `xlargerc` klasy zasobów.  Dowiedz się, jak to zrobić, postępując [zmienić przykład klasy zasobów użytkownika](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Tworzenie schematu tabeli docelowej bazy danych Azure SQL Data Warehouse, uruchamiając następującą instrukcję DDL:

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
Kroki wymagań wstępnych zakończone firma Microsoft teraz przystąpić do konfigurowania działanie kopiowania za pomocą Kreatora kopiowania.

## <a name="launch-copy-wizard"></a>Uruchamianie Kreatora kopiowania
1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **Utwórz zasób** z lewego górnego rogu, kliknij przycisk **analizy i analiza**i kliknij przycisk **fabryki danych**.
3. W **nowa fabryka danych** okienka:

   1. Wprowadź **LoadIntoSQLDWDataFactory** dla **nazwa**.
       Nazwa fabryki danych Azure musi być globalnie unikatowa. Jeśli zostanie wyświetlony błąd: **nazwa fabryki danych "LoadIntoSQLDWDataFactory" nie jest dostępna**, Zmień nazwę fabryki danych (na przykład yournameLoadIntoSQLDWDataFactory) i spróbuj ponownie utworzyć. Artykuł [Data Factory — Naming Rules](data-factory-naming-rules.md) (Fabryka danych — zasady nazewnictwa) zawiera zasady nazewnictwa artefaktów usługi Fabryka danych.  
   2. Wybierz swoją **subskrypcję** platformy Azure.
   3. Wykonaj jedną z następujących czynności dotyczącą grupy zasobów:
      1. Wybierz pozycję **Użyj istniejącej**, aby wybrać istniejącą grupę zasobów.
      2. Wybierz pozycję **Utwórz nowy**, aby wprowadzić nazwę grupy zasobów.
   4. Wybierz **lokalizację** fabryki danych.
   5. Zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego** u dołu bloku.  
   6. Kliknij przycisk **Utwórz**.
4. Po zakończeniu tworzenia zostanie wyświetlony blok **Fabryka danych**, jak pokazano na poniższej ilustracji:

   ![Strona główna fabryki danych](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Na stronie głównej usługi Fabryka danych kliknij kafelek **Kopiowanie danych**, aby uruchomić **Kreatora kopiowania**.

   > [!NOTE]
   > Jeśli widać, że przeglądarka sieci Web jest zablokowana podczas wyświetlania komunikatu „Autoryzowanie...”, wyłącz ustawienie **Blokuj pliki cookie i dane witryn innych firm** lub pozostaw je włączone i utwórz wyjątek dla witryny **login.microsoftonline.com**, a następnie spróbuj ponownie uruchomić kreatora.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Krok 1: Skonfiguruj harmonogram ładowanie danych
Pierwszym krokiem jest aby skonfigurować harmonogram dla ładowania danych.  

Na stronie **Właściwości**:

1. Wprowadź **CopyFromBlobToAzureSqlDataWarehouse** dla **Nazwa zadania**
2. Wybierz **uruchom raz teraz** opcji.   
3. Kliknij przycisk **Dalej**.  

    ![Kreator kopiowania — strona właściwości](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Krok 2: Konfigurowanie źródła
W tej sekcji przedstawiono kroki, aby skonfigurować źródło: obiektów Blob platformy Azure zawierającą TPC 1 TB-H pozycji plików.

1. Wybierz **magazyn obiektów Blob Azure** przechowywania danych, a następnie kliknij przycisk **dalej**.

    ![Kreator kopiowania — wybierz źródło strony](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Podaj dane połączenia dla konta magazynu obiektów Blob platformy Azure, a następnie kliknij przycisk **dalej**.

    ![Kreator kopiowania — informacje o źródle połączenia](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Wybierz **folderu** zawierający TPC-H wiersz plików z elementami, a następnie kliknij przycisk **dalej**.

    ![Kreator kopiowania — wybierz folder wejściowy](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Po kliknięciu **dalej**, ustawienia formatu pliku są wykrywane automatycznie.  Sprawdź, upewnij się, że ogranicznik kolumny jest ' | 'zamiast przecinkami domyślne",".  Kliknij przycisk **dalej** po przejrzeniu danych.

    ![Kreator kopiowania — ustawienia format pliku](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Krok 3: Konfigurowanie docelowej
W tej sekcji przedstawiono sposób skonfigurowane miejsce docelowe: `lineitem` tabeli w bazie danych magazynu danych SQL Azure.

1. Wybierz **magazyn danych SQL Azure** jako miejsce docelowe przechowywania, a następnie kliknij przycisk **dalej**.

    ![Kreator kopiowania — wybierz miejsce docelowe magazynu danych](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Wprowadź informacje o połączeniu dla usługi Azure SQL Data Warehouse.  Upewnij się, że należy określić użytkownika należącego do roli `xlargerc` (zobacz **wymagania wstępne** sekcji, aby uzyskać szczegółowe instrukcje) i kliknij przycisk **dalej**.

    ![Kreator kopiowania — informacje o połączeniu docelowego](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Wybierz tabelę docelową, a następnie kliknij przycisk **dalej**.

    ![Kreator kopiowania — strona mapowania tabeli](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Na stronie mapowania schematu, nie zaznaczaj opcji "Zastosować mapowanie kolumn" wyboru i kliknij przycisk **dalej**.

## <a name="step-4-performance-settings"></a>Krok 4: Ustawienia wydajności

**Zezwalaj na polybase** jest domyślnie zaznaczone.  Kliknij przycisk **Dalej**.

![Kreator kopiowania — strona mapowanie schematu](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Krok 5: Wdrażanie i monitorowanie wyników obciążenia
1. Kliknij przycisk **Zakończ** przycisk, aby wdrożyć.

    ![Kreator kopiowania — strona podsumowania](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Po zakończeniu wdrożenia, kliknij przycisk `Click here to monitor copy pipeline` monitorowanie kopii Uruchom postępu. Wybierz utworzony w potoku kopiowania **okien działania** listy.

    ![Kreator kopiowania — strona podsumowania](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Możesz wyświetlić szczegóły uruchomienia kopii **działania okna Eksploratora** w prawym panelu, w tym ilość danych ze źródła do odczytu i zapisywane w docelowym, czas trwania i to średnia przepływność dla przebiegu.

    Jak widać na poniższym zrzucie ekranu, kopiowanie 1 TB danych z magazynu obiektów Blob Azure do usługi SQL Data Warehouse trwało 14 minut, efektywnie uzyskanie 1,22 przepływności GB/s!

    ![Kreator kopiowania — okno dialogowe zakończyło się pomyślnie](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Najlepsze praktyki
Poniżej przedstawiono kilka najlepszych rozwiązań dotyczących bazy danych Azure SQL Data Warehouse:

* Używanie większych klasy zasobu, podczas ładowania do KLASTROWANEGO INDEKSU magazynu kolumn.
* Dla bardziej wydajne sprzężeń należy wziąć pod uwagę przy użyciu skrótu dystrybucji przez wybierz kolumnę, zamiast domyślnej round dystrybucji działania okrężnego.
* Szybsze szybkości obciążenia należy wziąć pod uwagę przy użyciu sterty przejściowej danych.
* Tworzenie statystyk po zakończeniu ładowania magazyn danych SQL Azure.

Zobacz [najlepsze rozwiązania dotyczące usługi Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) szczegółowe informacje.

## <a name="next-steps"></a>Kolejne kroki
* [Kreator kopiowania fabryki danych](data-factory-copy-wizard.md) — ten artykuł zawiera szczegółowe informacje o kreatorze kopiowania.
* [Skopiuj wydajności działania i dostrajania przewodnik](data-factory-copy-activity-performance.md) — ten artykuł zawiera odwołanie do pomiaru wydajności i dostrajania przewodnik.
