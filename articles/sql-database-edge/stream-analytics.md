---
title: Korzystanie z pakietów DAC bazy danych SQL i zadań usługi Stream Analytics za pomocą usługi Azure SQL Database Edge | Dokumenty firmy Microsoft
description: Dowiedz się więcej o korzystaniu z zadań usługi Stream Analytics w programie SQL Database Edge
keywords: krawędź bazy danych SQL, analiza strumienia, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74384155"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>Korzystanie z pakietów DAC bazy danych SQL i zadań usługi Stream Analytics z krawędzią bazy danych SQL

Usługa Azure SQL Database Edge Preview to zoptymalizowany aparat relacyjnej bazy danych przeznaczony do wdrożeń IoT i krawędzi. Jest on oparty na najnowszych wersjach aparatu bazy danych programu Microsoft SQL Server Database Engine, który zapewnia wiodącą w branży wydajność, zabezpieczenia i możliwości przetwarzania zapytań. Wraz z wiodącymi w branży możliwościami zarządzania relacyjnej bazy danych programu SQL Server usługa Azure SQL Database Edge zapewnia wbudowane funkcje przesyłania strumieniowego do analizy w czasie rzeczywistym i złożonego przetwarzania zdarzeń.

Usługa Azure SQL Database Edge zapewnia również natywną implementację programu SqlPackage.exe, która umożliwia wdrożenie pakietu [DAC bazy danych SQL](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) podczas wdrażania usługi SQL Database Edge.

Usługa Azure SQL Database Edge udostępnia `module twin's desired properties` dwa opcjonalne parametry za pomocą opcji modułu usługi IoT Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Pole | Opis |
|------|-------------|
| SqlPackage | Identyfikator URI magazynu obiektów Blob platformy Azure dla pliku *.zip zawierającego pakiet DAC bazy danych SQL.
| ASAJobInfo | Identyfikator URI magazynu obiektów Blob platformy Azure dla zadania usługi ASA Edge. Aby uzyskać więcej informacji, zobacz [Publikowanie zadania asa edge dla usługi SQL Database Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Korzystanie z pakietów DAC bazy danych SQL z krawędzią bazy danych SQL

Aby użyć pakietu DAC bazy danych SQL (*.dacpac) z krawędzią bazy danych SQL, wykonaj następujące kroki:

1. Tworzenie lub wyodrębnianie pakietu DAC bazy danych SQL. Zobacz [wyodrębnianie DAC z bazy danych,](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) aby uzyskać informacje na temat sposobu generowania pakietu DAC dla istniejącej bazy danych programu SQL Server.

2. Skonsułuj plik *.dacpac i przekaż go na konto magazynu obiektów Blob platformy Azure. Aby uzyskać więcej informacji na temat przekazywania plików do magazynu obiektów Blob platformy Azure, zobacz [Przekazywanie, pobieranie i lista obiektów blob za pomocą witryny Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Generowanie podpisu dostępu współdzielonego dla pliku zip przy użyciu witryny Azure portal. Aby uzyskać więcej informacji, zobacz [Delegowanie dostępu za pomocą sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md).

4. Zaktualizuj konfigurację modułu usługi SQL Database Edge, aby uwzględnić identyfikator URI dostępu współdzielonego dla pakietu DAC. Aby zaktualizować moduł sql database edge, wykonaj następujące kroki:

    1. W witrynie Azure portal przejdź do wdrożenia usługi IoT Hub.

    2. W lewym okienku wybierz pozycję **IoT Edge**.

    3. Na stronie **Usługi IoT Edge** znajdź i wybierz krawędź IoT, na której jest wdrażany moduł SQL Database Edge.

    4. Na stronie **Urządzenia urządzenia brzegowego IoT** wybierz pozycję **Ustaw moduł**.

    5. Na stronie **Ustaw moduły** wybierz pozycję **Konfiguruj** względem modułu SQL Database Edge.

    6. W okienku **Moduły niestandardowe usługi IoT Edge** wybierz opcję **Ustaw żądane właściwości bliźniaczej reprezentacji modułu**. Zaktualizuj żądane właściwości, `SQLPackage` aby uwzględnić identyfikator URI dla opcji, jak pokazano w poniższym przykładzie.

        > [!NOTE]
        > Identyfikator URI sygnatury dostępu Współdzielonego w następującym JSON jest tylko przykładem. Zastąp identyfikator URI rzeczywistym identyfikatorem URI z wdrożenia.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Wybierz **pozycję Zapisz**.

    8. Na stronie **Ustaw moduły** wybierz pozycję **Dalej**.

    9. Na stronie **Ustaw moduły** wybierz pozycję **Dalej,** a następnie **pozycję Prześlij**.

5. Po aktualizacji modułu plik pakietu DAC jest pobierany, rozpinany i wdrażany w wystąpieniu usługi SQL Database Edge.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Korzystanie z zadań przesyłania strumieniowego z krawędzią bazy danych SQL

Usługa Azure SQL Database Edge ma natywną implementację środowiska wykonawczego analizy strumienia. Ta implementacja umożliwia utworzenie zadania usługi Azure Stream Analytics i wdrożenie tego zadania jako zadania przesyłania strumieniowego usługi SQL Database Edge. Aby utworzyć zadanie krawędzi usługi Stream Analytics, wykonaj następujące kroki:

1. Przejdź do witryny Azure portal przy użyciu adresu [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)w wersji zapoznawczej . Ten adres URL w wersji zapoznawczej umożliwia skonfigurowanie danych wyjściowych bazy danych SQL dla zadania krawędzi usługi Stream Analytics.

2. Utwórz nowe zadanie **usługi Azure Stream Analytics w usłudze IoT Edge.** Wybierz środowisko hostingu, które jest przeznaczone dla **edge**.

3. Zdefiniuj dane wejściowe i wyjściowe dla zadania usługi Azure Stream Analytics. Każdy wynik SQL, który zostanie skonfigurowany w tym miejscu, jest powiązany z pojedynczą tabelą w bazie danych. Jeśli chcesz przesyłać strumieniowo dane do wielu tabel, musisz utworzyć wiele wyjść bazy danych SQL. Można skonfigurować dane wyjściowe SQL, aby wskazywały różne bazy danych.

    **Wejście**. Wybierz EdgeHub jako dane wejściowe dla zadania krawędzi i podaj informacje o zasobie.

    **Wyjście**. Wybierz bazę danych SQL jako dane wyjściowe. Wybierz **pozycję Podaj ustawienia bazy danych SQL ręcznie**. Podaj szczegóły konfiguracji bazy danych i tabeli.

    |Pole      | Opis |
    |---------------|-------------|
    |Alias danych wyjściowych | Nazwa aliasu wyjściowego.|
    |baza danych | Nazwa bazy danych SQL. Musi to być prawidłowa nazwa bazy danych, która istnieje w wystąpieniu usługi SQL Database Edge.|
    |Nazwa serwera | Nazwa (lub adres IP) i szczegóły numeru portu dla wystąpienia SQL. W przypadku wdrożenia usługi SQL Database Edge można użyć **tcp:.,1433** dla nazwy serwera.|
    |Nazwa użytkownika | Konto logowania SQL, które ma dostęp czytnika danych i moduł zapisujący dane do bazy danych, która została określona wcześniej.|
    |Hasło | Hasło dla konta logowania SQL, które zostało określone wcześniej.|
    |Tabela | Nazwa tabeli, która zostanie wyjęta dla zadania przesyłania strumieniowego.|
    |Dziedzicz partycjonowanie| Umożliwia dziedziczenie schematu partycjonowania poprzedniego kroku kwerendy lub danych wejściowych. Gdy ta opcja jest włączona, można oczekiwać, aby zobaczyć lepszą przepływność podczas zapisu w tabeli opartej na dysku i mają w pełni równoległe topologii dla zadania.|
    |Rozmiar wsadu| Maksymalna liczba rekordów wysyłanych z każdą transakcją wstawiania zbiorczego.|

    Oto przykładowa konfiguracja wejścia/wyjścia:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat karty wyjściowej SQL dla usługi Azure Stream Analytics, zobacz [Dane wyjściowe usługi Azure Stream Analytics do bazy danych SQL Azure.](../stream-analytics/stream-analytics-sql-output-perf.md)

4. Zdefiniuj kwerendę zadania ASA dla zadania krawędzi. Ta kwerenda powinna używać zdefiniowanych aliasów wejściowych/wyjściowych jako nazw danych wejściowych i wyjściowych w kwerendzie. Aby uzyskać więcej informacji, zobacz [Odwołanie do języka zapytań usługi Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Ustaw ustawienia konta magazynu dla zadania krawędzi. Konto magazynu jest używane jako miejsce docelowe publikowania dla zadania brzegowego.

6. W obszarze **Konfiguruj**wybierz pozycję **Publikuj**, a następnie wybierz przycisk **Publikuj.** Zapisz identyfikator URI sygnatury dostępu Współdzielonego do użytku z modułem SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Wdrażanie zadania krawędzi usługi Stream Analytics w programie SQL Database Edge

Aby wdrożyć zadanie przesyłania strumieniowego do modułu sql database edge, należy zaktualizować konfigurację modułu usługi SQL Database Edge, aby uwzględnić identyfikator URI sygnatury dostępu Współdzielonego dla zadania przesyłania strumieniowego z wcześniejszego kroku. Aby zaktualizować moduł sql database edge:

1. W witrynie Azure portal przejdź do wdrożenia usługi IoT Hub.

2. W lewym okienku wybierz pozycję **IoT Edge**.

3. Na stronie **Usługi IoT Edge** znajdź i wybierz krawędź IoT, na której jest wdrażany moduł SQL Database Edge.

4. Na stronie **Urządzenia urządzenia brzegowego IoT** wybierz pozycję **Ustaw moduł**.

5. Na stronie **Ustaw moduły** wybierz pozycję **Konfiguruj** względem modułu SQL Database Edge.

6. W okienku **Moduły niestandardowe usługi IoT Edge** wybierz opcję **Ustaw żądane właściwości bliźniaczej reprezentacji modułu**. Zaktualizuj żądane właściwości, `ASAJobInfo` aby uwzględnić identyfikator URI dla opcji, jak pokazano w poniższym przykładzie.

    > [!NOTE]
    > Identyfikator URI sygnatury dostępu Współdzielonego w następującym JSON jest tylko przykładem. Zastąp identyfikator URI rzeczywistym identyfikatorem URI z wdrożenia.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Wybierz **pozycję Zapisz**.

8. Na stronie **Ustaw moduły** wybierz pozycję **Dalej**.

9. Na stronie **Ustaw moduły** wybierz pozycję **Dalej,** a następnie **pozycję Prześlij**.

10. Po aktualizacji modułu plik zadania analizy strumienia jest pobierany, rozpinany i wdrażany w wystąpieniu usługi SQL Database Edge.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje na temat cen i dostępności, zobacz [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Żądanie włączenia usługi Azure SQL Database Edge dla subskrypcji.
- Aby rozpocząć, zobacz [Wdrażanie usługi SQL Database Edge za pośrednictwem witryny Azure portal](deploy-portal.md).
