---
title: Pozyskiwanie danych z IoT Hub na platformie Azure Eksplorator danych
description: W tym artykule dowiesz się, jak pozyskiwanie (ładować) danych do platformy Azure Eksplorator danych z IoT Hub.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 83f5339dbc4f093ba0b7287b53c053e319f928c9
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937388"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer-preview"></a>Pozyskiwanie danych z IoT Hub na platformie Azure Eksplorator danych (wersja zapoznawcza)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Eksplorator danych oferuje pozyskiwanie (ładowanie danych) z IoT Hub, platformy przesyłania strumieniowego danych Big Data i usługi pozyskiwania IoT.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* Utwórz [klaster testowy i bazę danych](create-cluster-database-portal.md) z nazwą bazy danych *TestDB*.

* [Przykładowa aplikacja](https://github.com/Azure-Samples/azure-iot-samples-csharp) i dokumentacja dotycząca symulowania urządzenia.

* [Program Visual Studio 2019](https://visualstudio.microsoft.com/vs/) do uruchamiania przykładowej aplikacji.

## <a name="create-an-iot-hub"></a>Tworzenie Centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Rejestrowanie urządzenia w IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Tworzenie tabeli docelowej w usłudze Azure Data Explorer

Teraz utworzysz tabelę na platformie Azure Eksplorator danych, do której będą wysyłane dane centra IoT Hub. Należy utworzyć tabelę w klastrze i w bazie danych, która została zainicjowana w [**wymaganiach wstępnych**](#prerequisites).

1. W witrynie Azure Portal przejdź do klastra, a następnie wybierz pozycję **Zapytanie**.

    ![Zapytanie ADX w portalu](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Skopiuj poniższe polecenie w oknie, a następnie wybierz pozycję **Uruchom**, aby utworzyć tabelę (TestTable), w której będą umieszczane pozyskiwane dane.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Uruchamianie zapytania create](media/ingest-data-iot-hub/run-create-query.png)

1. Skopiuj poniższe polecenie w oknie, a następnie wybierz pozycję **Uruchom**, aby zamapować przychodzące dane w formacie JSON na nazwy kolumn i typy danych tabeli (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Łączenie tabeli Eksplorator danych platformy Azure z usługą IoT Hub

Teraz nawiążesz połączenie z IoT Hubą z usługi Azure Eksplorator danych. Po zakończeniu tego połączenia dane, które przechodzą do strumienia usługi IoT Hub, do [utworzonej tabeli docelowej](#create-a-target-table-in-azure-data-explorer).

1. Wybierz pozycję **powiadomienia** na pasku narzędzi, aby sprawdzić, czy wdrożenie IoT Hub powiodło się.

1. W obszarze utworzony klaster wybierz pozycję **bazy danych** , a następnie wybierz bazę danych, która została utworzona **TestDB**.
    
    ![Wybieranie testowej bazy danych](media/ingest-data-iot-hub/select-database.png)

1. Wybierz kolejno pozycje **Pozyskiwanie danych** i **Dodaj połączenie danych**. Następnie wypełnij formularz, używając poniższych informacji. Po zakończeniu wybierz pozycję **Utwórz** .

    ![Połączenie IoT Hub](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Źródło danych**:

    **Ustawienie** | **Opis pola**
    |---|---|
    | Nazwa połączenia danych | Nazwa połączenia, które chcesz utworzyć w usłudze Azure Data Explorer.
    | IoT Hub | Nazwa IoT Hub. |
    | Zasady dostępu współdzielonego | Nazwa zasad dostępu współdzielonego. Musi mieć uprawnienia do odczytu. |
    | Grupa konsumentów |  Grupa odbiorców zdefiniowana w IoT Hub wbudowanym punkcie końcowym. |
    | Właściwości systemu zdarzeń | Właściwości systemu zdarzeń IoT Hub. W przypadku, gdy istnieje wiele rekordów na komunikat o zdarzeniu, właściwości systemu zostaną dodane do pierwszej z nich. |
    | | 

    > [!NOTE]
    > W przypadku [ręcznego przełączania do trybu failover](/azure/iot-hub/iot-hub-ha-dr#manual-failover)należy ponownie utworzyć połączenie danych.

    **Tabela docelowa**:

    Dostępne są dwie opcje routingu pozyskiwanych danych: *statyczne* i *dynamiczne*. 
    W tym artykule należy używać routingu statycznego, w którym można określić nazwę tabeli, format danych i mapowanie. W związku z tym pozostaw pole **Moje dane zawierają informacje o routingu** niezaznaczone.

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Tabela | *TestTable* | Tabela utworzona w **TestDB**. |
    | Format danych | *JSON* | Obsługiwane formaty to: Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV i TXT. |
    | Mapowanie kolumn | *TestMapping* | Mapowanie utworzone w **TestDB**, które mapuje przychodzące dane JSON do nazw kolumn i typów danych **TestDB**. Wymagane dla notacji JSON, wielowierszowego kodu JSON i AVRO oraz opcjonalne dla innych formatów.|
    | | |

    > [!NOTE]
    > * Wybierz pozycję **moje dane zawiera informacje o routingu** , aby użyć routingu dynamicznego, gdzie dane zawierają niezbędne informacje dotyczące routingu, jak pokazano w komentarzach [przykładowych aplikacji](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) . Jeśli są ustawione właściwości static i Dynamic, właściwości dynamiczne zastępują statyczne. 
    > * Zostaną pozyskane tylko zdarzenia znajdujące się w kolejce po utworzeniu połączenia danych.

## <a name="generate-sample-data-for-testing"></a>Generuj przykładowe dane do testowania

Aplikacja urządzenia symulowanego łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury oraz wilgotności.

1. Pobierz przykładowy projekt C# z https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip i wyodrębnij archiwum ZIP.

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu C#. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device**.

1. Otwórz plik **SimulatedDevice.cs** w wybranym edytorze.

    Zastąp wartość zmiennej `s_connectionString` parametrami połączenia urządzenia, [Aby zarejestrować urządzenie w IoT Hub](#register-a-device-to-the-iot-hub). Następnie zapisz zmiany w pliku **SimulatedDevice.cs**.

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

Za pomocą aplikacji generującej dane można teraz zobaczyć przepływ danych z Centrum IoT Hub do tabeli w klastrze.

1. W Azure Portal w usłudze IoT Hub zobaczysz w działaniu, gdy aplikacja jest uruchomiona.

    ![Metryki IoT Hub](media/ingest-data-iot-hub/iot-hub-metrics.png)

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
    
    ![Pokaż wyniki pozyskiwanych danych](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * W systemie Azure Data Explorer istnieją zasady agregacji (dzielenie na partie) dotyczące pozyskiwania danych opracowane w celu optymalizacji procesu pozyskiwania. Zasady są domyślnie skonfigurowane do 5 minut lub 500 MB danych, dzięki czemu mogą wystąpić opóźnienia. Zobacz temat [zasady tworzenia wsadowego](/azure/kusto/concepts/batchingpolicy) dla opcji agregacji. 
    > * Skonfiguruj tabelę do obsługi przesyłania strumieniowego i Usuń opóźnienie w czasie odpowiedzi. Zobacz [zasady przesyłania strumieniowego](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie planujesz ponownie używać IoT Hub, wyczyść polecenie **test-Hub-RG**, aby uniknąć ponoszenia kosztów.

1. W witrynie Azure Portal wybierz **grupy zasobów** daleko po lewej stronie, a następnie wybierz utworzoną grupę zasobów.  

    Jeśli menu po lewej stronie jest zwinięte, wybierz ![przycisk Rozwiń,](media/ingest-data-event-hub/expand.png) aby je rozwinąć.

   ![Wybieranie grupy zasobów do usunięcia](media/ingest-data-event-hub/delete-resources-select.png)

1. W obszarze **test-resource-group** wybierz pozycję **Usuń grupę zasobów**.

1. W nowym oknie wpisz nazwę grupy zasobów do usunięcia (*test-hub-rg*), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań dotyczących danych w usłudze Azure Eksplorator danych](web-query-data.md)
