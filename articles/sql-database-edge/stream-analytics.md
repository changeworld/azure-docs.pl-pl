---
title: Używanie SQL Database pakietu DAC i zadań Stream Analytics z Azure SQL Database Edge | Microsoft Docs
description: Dowiedz się więcej o używaniu Stream Analytics zadań w SQL Database Edge
keywords: Baza danych SQL Database, Stream Analytics, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: c3ed84e06f693925ed8b484070616e223929e401
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108747"
---
# <a name="using-sql-database-dac-package-and-stream-analytics-job-with-sql-database-edge"></a>Używanie SQL Database pakietu DAC i zadania Stream Analytics z SQL Database Edge

Azure SQL Database Edge — wersja zapoznawcza to zoptymalizowany aparat relacyjnej bazy danych umożliwiający wdrażanie IoT i Edge. Jest on oparty na najnowszych wersjach aparatu bazy danych Microsoft SQL Server, który zapewnia wiodącą w branży wydajność, funkcje zabezpieczeń i przetwarzania zapytań. Dzięki wiodącym w branży funkcjom zarządzania relacyjnymi bazami danych SQL Server Azure SQL Database Edge zapewnia wbudowaną funkcję przesyłania strumieniowego na potrzeby analiz w czasie rzeczywistym i skomplikowanego przetwarzania zdarzeń.

Azure SQL Database Edge zawiera również natywną implementację programu sqlpackage. exe, która umożliwia użytkownikom wdrożenie pakietu [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) podczas wdrażania SQL Database Edge.

Azure SQL Database Edge uwidacznia dwa opcjonalne parametry za pomocą opcji *pożądanych właściwości w module dla sznurka modułu* IoT Edge.

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
| SQLPackage | Identyfikator URI Blob Storage platformy Azure dla pliku *. zip zawierającego pakiet SQL Database DAC.
| ASAJobInfo | Identyfikator URI Blob Storage platformy Azure dla zadania ASA Edge. Aby uzyskać więcej informacji o publikowaniu zadania ASA Edge, zapoznaj się z tematem [Publikowanie zadania ASA Edge dla SQL Database Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Używanie SQL Database pakietów DAC z SQL Database Edge

Aby użyć SQL Database pakietu DAC (*. dacpac) z SQL Database Edge, wykonaj kroki wymienione poniżej.

1. Utwórz lub Wyodrębnij SQL Database pakiet DAC. Aby wygenerować DacPac dla istniejących SQL Database, można użyć pojęć wymienionych w temacie [Wyodrębnianie DAC z istniejącej bazy danych](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) .

2. Załaduj plik * *. dacpac* i przekaż go do konta usługi Azure Blob Storage. Aby uzyskać więcej informacji na temat przekazywania plików do usługi Azure Blob Storage, zobacz [przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB przy użyciu Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Generuj podpis sygnatury dostępu współdzielonego dla pliku zip przy użyciu Azure Portal. Aby uzyskać więcej informacji, zobacz [delegowanie dostępu za pomocą sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md).

4. Zaktualizuj konfigurację modułu SQL Database Edge, aby uwzględnić identyfikator URI sygnatury dostępu współdzielonego dla pakietu DAC. Aby zaktualizować moduł SQL Database Edge

    1. Na Azure Portal przejdź do wdrożenia IoT Hub.

    2. W okienku po lewej stronie kliknij pozycję **IoT Edge**.

    3. Na stronie **IoT Edge** Znajdź i kliknij IoT Edge, w którym wdrożono moduł SQL Database Edge.

    4. Na stronie *IoT Edge urządzenie urządzenia* kliknij pozycję **Ustaw moduł**. 

    5. Na stronie **Ustawianie modułów** kliknij pozycję *konfiguruj* względem modułu SQL Database Edge. 

    6. W okienku **IoT Edge modułów niestandardowych** wybierz *odpowiednie właściwości* , a następnie zaktualizuj odpowiednie właściwości, aby uwzględnić identyfikator URI dla opcji sqlpackage, jak pokazano w poniższym przykładzie. 

        > [!NOTE]
        > Identyfikator URI sygnatury dostępu współdzielonego poniżej dotyczy tylko ilustracji. Zastąp identyfikator URI rzeczywistym identyfikatorem URI z wdrożenia.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Kliknij pozycję **Zapisz**.

    8. Na stronie **Ustawianie modułów** kliknij przycisk *dalej*.

    9. Na stronie **Ustawianie modułów** kliknij przycisk *dalej* , a następnie kliknij przycisk **Prześlij**.

5. Opublikuj aktualizację modułu, plik DACPAC jest pobierany, rozpakowany i wdrażany względem wystąpienia SQL Database Edge.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Używanie zadań przesyłania strumieniowego z SQL Database Edge

Azure SQL Database Edge ma natywną implementację środowiska uruchomieniowego usługi Stream Analytics. Dzięki temu użytkownicy mogą utworzyć zadanie Azure Stream Analytics Edge i wdrożyć to zadanie jako SQL Database zadanie przesyłania strumieniowego Edge. Aby utworzyć zadanie usługi Stream Analytics Edge, wykonaj poniższe czynności.

1. Przejdź do Azure Portal przy użyciu [adresu URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)w wersji zapoznawczej. Ten adres URL wersji zapoznawczej umożliwia użytkownikom konfigurowanie SQL Database danych wyjściowych dla zadania usługi Stream Analytics Edge.

2. Utwórz nowe zadanie **Azure Stream Analytics na urządzeniach IoT Edge** i wybierz pozycję platforma hostingu dla **krawędzi**docelowej.

3. Zdefiniuj *dane wejściowe* i *wyjściowe* dla zadania Azure Stream Analytics. Wszystkie dane wyjściowe SQL (skonfigurowane poniżej) są powiązane z pojedynczą tabelą w bazie danych. Jeśli istnieje potrzeba przesyłania strumieniowego danych do wielu tabel, należy utworzyć wiele SQL Database wyjść. Dane wyjściowe SQL można skonfigurować tak, aby wskazywały na różne bazy danych.

    *Wejście — wybierz EdgeHub jako dane wejściowe dla zadania brzegowego i Wypełnij informacje o zasobie.*

    *Dane wyjściowe — wybierz SQL Database jako dane wyjściowe, "Podaj ustawienia SQL Database ręcznie" i podaj szczegóły konfiguracji bazy danych i tabeli.*

    |Pole      | Opis |
    |---------------|-------------|
    |Alias danych wyjściowych | Nazwa aliasu danych wyjściowych.|
    |Database (Baza danych) | Nazwa SQL Database. Musi to być prawidłowa nazwa bazy danych, która istnieje w wystąpieniu SQL Database krawędzi.|
    |Nazwa serwera | Nazwa (lub adres IP) i numer portu dla wystąpienia programu SQL Server. W przypadku wdrożenia SQL Database Edge można użyć **protokołu TCP:., 1433** jako nazwy serwera.|
    |Nazwa użytkownika | Konto logowania SQL, które ma dostęp do czytnika danych i składnika zapisywania danych do bazy danych wymienionej powyżej.|
    |Hasło | Hasło do konta logowania SQL wymienione powyżej.|
    |Tabela | Nazwa tabeli, która będzie wyprowadzana dla zadania przesyłania strumieniowego.|
    |Dziedzicz partycjonowanie| Ta opcja konfiguracji danych wyjściowych SQL umożliwia dziedziczenie schematu partycjonowania poprzedniego kroku zapytania lub danych wejściowych. Dzięki temu można zapisywać w tabeli opartej na dyskach i mieć w pełni równoległą topologię dla danego zadania, co pozwala na zwiększenie przepływności.|
    |Rozmiar wsadu| Rozmiar wsadu to maksymalna liczba rekordów wysyłanych z każdą zbiorczą transakcją wstawiania.|

    Przykładowa konfiguracja danych wejściowych/wyjściowych poniżej:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output :
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
    > Aby uzyskać więcej informacji na temat karty danych wyjściowych SQL dla Azure Stream Analytics, zobacz [Azure Stream Analytics danych wyjściowych do Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Zdefiniuj zapytanie dotyczące zadania ASA dla zadania Edge. To zapytanie powinno używać zdefiniowanych aliasów Input/Output jako nazw wejściowych i wyjściowych w zapytaniu. Aby uzyskać więcej informacji, zobacz [Stream Analytics Dokumentacja języka zapytań](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Ustaw ustawienia konta magazynu dla zadania brzegowego. Konto magazynu jest używane jako miejsce docelowe publikowania dla zadania brzegowego.

6. W obszarze Konfiguracja wybierz pozycję Opublikuj, a następnie kliknij przycisk Publikuj. Zapisz adres URL sygnatury dostępu współdzielonego do użycia z modułem SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-the-sql-database-edge"></a>Wdróż zadanie usługi Stream Analytics Edge na SQL Database Edge

Aby wdrożyć zadanie przesyłania strumieniowego do modułu SQL Database Edge, zaktualizuj konfigurację modułu SQL Database Edge w celu uwzględnienia identyfikatora URI sygnatury dostępu współdzielonego dla zadania przesyłania strumieniowego z powyższego kroku. Aby zaktualizować moduł SQL Database Edge

1. Na Azure Portal przejdź do wdrożenia IoT Hub.

2. W okienku po lewej stronie kliknij pozycję **IoT Edge**.

3. Na stronie **IoT Edge** Znajdź i kliknij IoT Edge, w którym wdrożono moduł SQL Database Edge.

4. Na stronie *IoT Edge urządzenie urządzenia* kliknij pozycję **Ustaw moduł**. 

5. Na stronie **Ustawianie modułów** kliknij pozycję *konfiguruj* względem modułu SQL Database Edge. 

6. W okienku **IoT Edge modułów niestandardowych** wybierz *odpowiednie właściwości* , a następnie zaktualizuj odpowiednie właściwości, aby uwzględnić identyfikator URI dla opcji ASAJobInfo, jak pokazano w poniższym przykładzie. 

    > [!NOTE]
    > Identyfikator URI sygnatury dostępu współdzielonego poniżej dotyczy tylko ilustracji. Zastąp identyfikator URI rzeczywistym identyfikatorem URI z wdrożenia.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Kliknij pozycję **Zapisz**.

8. Na stronie **Ustawianie modułów** kliknij przycisk *dalej*.

9. Na stronie **Ustawianie modułów** kliknij przycisk *dalej* , a następnie kliknij przycisk **Prześlij**.

10. Opublikuj aktualizację modułu, plik zadania usługi Stream Analytics jest pobierany, rozpakowany i wdrażany względem wystąpienia SQL Database Edge.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje dotyczące cen i dostępności, zobacz [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Żądanie włączenia Azure SQL Database Edge dla subskrypcji.
- Aby rozpocząć, zobacz następujące tematy:
  - [Wdróż SQL Database Edge przez Azure Portal](deploy-portal.md)
