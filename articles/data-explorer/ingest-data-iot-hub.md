---
title: Pozyskiwania danych z usługi IoT Hub do Eksploratora danych platformy Azure
description: W tym artykule dowiesz się, jak pozyskiwania (ładowania) danych do usługi Azure Data Explorer z Usługi IoT Hub.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 78455c90bab694b77a5e4a56d0b40518867d8d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188368"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>Pozyskiwania danych z usługi IoT Hub do Eksploratora danych platformy Azure 

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Szablon usługi Azure Resource Manager](data-connection-iot-hub-resource-manager.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Data Explorer oferuje pozyskiwania (ładowanie danych) z Usługi IoT Hub, platformy przesyłania strumieniowego danych big data i usługi pozyskiwania IoT.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Tworzenie [klastra testowego i bazy danych](create-cluster-database-portal.md) z *testbem*nazwy bazy danych .
* [Przykładowa aplikacja](https://github.com/Azure-Samples/azure-iot-samples-csharp) i dokumentacja do symulowania urządzenia.
* [Visual Studio 2019,](https://visualstudio.microsoft.com/vs/) aby uruchomić przykładową aplikację.

## <a name="create-an-iot-hub"></a>Tworzenie centrum Iot

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Rejestrowanie urządzenia w Centrum IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Tworzenie tabeli docelowej w usłudze Azure Data Explorer

Teraz utworzysz tabelę w Eksploratorze danych platformy Azure, do której usługi IoT Hubs będą wysyłać dane. Tworzenie tabeli w klastrze i bazy danych aprowizowanych w [**wymagania wstępne**](#prerequisites).

1. W witrynie Azure Portal przejdź do klastra, a następnie wybierz pozycję **Zapytanie**.

    ![Kwerenda ADX w portalu](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Skopiuj poniższe polecenie w oknie, a następnie wybierz pozycję **Uruchom**, aby utworzyć tabelę (TestTable), w której będą umieszczane pozyskiwane dane.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Uruchamianie zapytania create](media/ingest-data-iot-hub/run-create-query.png)

1. Skopiuj poniższe polecenie w oknie, a następnie wybierz pozycję **Uruchom**, aby zamapować przychodzące dane w formacie JSON na nazwy kolumn i typy danych tabeli (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Łączenie tabeli Eksploratora danych platformy Azure z centrum IoT

Teraz łączysz się z Centrum IoT z Usługi Azure Data Explorer. Po zakończeniu tego połączenia dane przepływane do koncentratora iot strumieni do [utworzonej tabeli docelowej](#create-a-target-table-in-azure-data-explorer).

1. Wybierz **pozycję Powiadomienia** na pasku narzędzi, aby sprawdzić, czy wdrożenie usługi IoT Hub zakończyło się pomyślnie.

1. W utworzonym klastrze wybierz pozycję **Bazy danych,** a następnie wybierz bazę danych, która została utworzona **jako testdb**.
    
    ![Wybieranie testowej bazy danych](media/ingest-data-iot-hub/select-database.png)

1. Wybierz kolejno pozycje **Pozyskiwanie danych** i **Dodaj połączenie danych**. Następnie wypełnij formularz, używając poniższych informacji. Po zakończeniu wybierz **pozycję Utwórz.**

    ![Połączenie z centrum IoT](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Źródło danych**:

    **Ustawienie** | **Opis pola**
    |---|---|
    | Nazwa połączenia danych | Nazwa połączenia, które chcesz utworzyć w Eksploratorze danych platformy Azure
    | Usługa IoT Hub | Nazwa Centrum IoT |
    | Zasady dostępu współdzielonego | Nazwa zasady dostępu współdzielonego. Musi mieć uprawnienia do odczytu |
    | Grupa konsumentów |  Grupa odbiorców zdefiniowana we wbudowanym punkcie końcowym usługi IoT Hub |
    | Właściwości systemu zdarzeń | [Właściwości systemu zdarzeń Usługi IoT Hub](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages). Podczas dodawania właściwości systemu [należy utworzyć](/azure/kusto/management/create-table-command) lub [zaktualizować](/azure/kusto/management/alter-table-command) schemat tabeli i [mapowanie](/azure/kusto/management/mappings) w celu uwzględnienia wybranych właściwości. | | | 

    > [!NOTE]
    > W przypadku [ręcznego trybu failover](/azure/iot-hub/iot-hub-ha-dr#manual-failover)należy ponownie utworzyć połączenie danych.

    **Tabela docelowa:**

    Istnieją dwie opcje routingu pochłoniętych danych: *statyczne* i *dynamiczne*. 
    W tym artykule należy użyć routingu statycznego, w którym określa się nazwę tabeli, format danych i mapowanie. W związku z tym pozostaw pole **Moje dane zawierają informacje o routingu** niezaznaczone.

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Tabela | *TestTable* | Tabela utworzona w **testdb**. |
    | Format danych | *Json* | Obsługiwane formaty to Avro, CSV, JSON, MULTILINE JSON, PSV, SOHSV, SCSV, TSV, TSVE i TXT. |
    | Mapowanie kolumn | *TestMapping* | [Mapowanie](/azure/kusto/management/mappings) utworzone w **testdb**, które mapuje przychodzące dane JSON do nazw kolumn i typów danych **testdb**. Wymagane dla JSON, MULTILINE JSON i AVRO oraz opcjonalne dla innych formatów.|
    | | |

    > [!NOTE]
    > * Wybierz **pozycję Moje dane zawiera informacje o routingu,** aby używać routingu dynamicznego, gdzie dane zawierają niezbędne informacje o routingu, które są widoczne w przykładowych komentarzach [aplikacji.](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) Jeśli ustawiono właściwości statyczne i dynamiczne, właściwości dynamiczne zastępują właściwości statyczne. 
    > * Tylko zdarzenia w kolejce po utworzeniu połączenia danych są połajane.

[!INCLUDE [data-explorer-container-system-properties](../../includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>Generowanie przykładowych danych do testowania

Aplikacja urządzenia symulowanego łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury oraz wilgotności.

1. Pobierz przykładowy projekt C# z https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip i wyodrębnij archiwum ZIP.

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu C#. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device**.

1. Otwórz plik **SimulatedDevice.cs** w wybranym edytorze.

    Zastąp `s_connectionString` wartość zmiennej ciągiem połączenia urządzenia z [rejestru urządzenia do Centrum IoT Hub](#register-a-device-to-the-iot-hub). Następnie zapisz zmiany w pliku **SimulatedDevice.cs**.

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane pakiety dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    dotnet restore
    ```

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby utworzyć i uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    dotnet run
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>Przeglądanie przepływu danych

Dzięki aplikacji generującej dane można teraz zobaczyć przepływ danych z centrum IoT hub do tabeli w klastrze.

1. W witrynie Azure portal w obszarze Centrum IoT hub zobaczysz skok aktywności, gdy aplikacja jest uruchomiona.

    ![Metryki usługi IoT Hub](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. Aby sprawdzić, ile komunikatów zostało przekazanych do tej pory do bazy danych, uruchom poniższe zapytanie w testowej bazie danych.

    ```Kusto
    TestTable
    | count
    ```

1. Aby sprawdzić zawartość komunikatów, uruchom następujące zapytanie:

    ```Kusto
    TestTable
    ```

    Zestaw wyników:
    
    ![Pokaż pogoń za wynikami danych](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * W systemie Azure Data Explorer istnieją zasady agregacji (dzielenie na partie) dotyczące pozyskiwania danych opracowane w celu optymalizacji procesu pozyskiwania. Zasady są domyślnie skonfigurowane do 5 minut lub 500 MB danych, więc może wystąpić opóźnienie. Zobacz [zasady przetwarzania wsadowego](/azure/kusto/concepts/batchingpolicy) dla opcji agregacji. 
    > * Skonfiguruj tabelę do obsługi przesyłania strumieniowego i usuń opóźnienie w czasie odpowiedzi. Zobacz [zasady przesyłania strumieniowego](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie planujesz ponownie korzystać z usługi IoT Hub, posprzątaj **test-hub-rg**, aby uniknąć ponoszenia kosztów.

1. W witrynie Azure Portal wybierz **grupy zasobów** daleko po lewej stronie, a następnie wybierz utworzoną grupę zasobów.  

    Jeśli menu po lewej stronie jest zwinięte, wybierz ![przycisk Rozwiń,](media/ingest-data-event-hub/expand.png) aby je rozwinąć.

   ![Wybieranie grupy zasobów do usunięcia](media/ingest-data-event-hub/delete-resources-select.png)

1. W obszarze **test-resource-group** wybierz pozycję **Usuń grupę zasobów**.

1. W nowym oknie wpisz nazwę grupy zasobów do usunięcia (*test-hub-rg*), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* [wykonywanie zapytań o dane w usłudze Azure Data Explorer](web-query-data.md)
