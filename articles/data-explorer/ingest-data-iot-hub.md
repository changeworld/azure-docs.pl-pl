---
title: Pozyskiwanie danych z IoT Hub na platformie Azure Eksplorator danych
description: W tym artykule dowiesz się, jak pozyskiwanie (ładować) danych do platformy Azure Eksplorator danych z IoT Hub.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: ef66e6a69b6d33b16a558293fe29b8adf51cd137
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996759"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer-preview"></a>Pozyskiwanie danych z IoT Hub na platformie Azure Eksplorator danych (wersja zapoznawcza)

Azure Eksplorator danych to szybka i wysoce skalowalna usługa eksploracji danych dla danych dzienników i telemetrii. Usługa Azure Eksplorator danych oferuje pozyskiwanie (ładowanie danych) z IoT Hub, platformy przesyłania strumieniowego danych Big Data i usługi pozyskiwania IoT.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

* Utwórz [klaster testowy i bazę danych](create-cluster-database-portal.md) z nazwą bazy danych *TestDB*.

* [Przykładowa aplikacja](https://github.com/Azure-Samples/azure-iot-samples-csharp) i dokumentacja dotycząca symulowania urządzenia.

* [Program Visual Studio 2019](https://visualstudio.microsoft.com/vs/) do uruchamiania przykładowej aplikacji.

## <a name="create-an-iot-hub"></a>Tworzenie Centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Rejestrowanie urządzenia w IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Tworzenie tabeli docelowej na platformie Azure Eksplorator danych

Teraz utworzysz tabelę na platformie Azure Eksplorator danych, do której będą wysyłane dane centra IoT Hub. Należy utworzyć tabelę w klastrze i w bazie danych, która została zainicjowana w [**wymaganiach wstępnych**](#prerequisites).

1. W Azure Portal przejdź do klastra, a następnie wybierz pozycję **zapytanie**.

    ![Zapytanie ADX w portalu](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Skopiuj następujące polecenie do okna i wybierz polecenie **Uruchom** , aby utworzyć tabelę (Tester), która będzie odbierać dane pozyskiwane.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Uruchom Tworzenie zapytania](media/ingest-data-iot-hub/run-create-query.png)

1. Skopiuj następujące polecenie do okna i wybierz pozycję **Uruchom** , aby zmapować przychodzące dane JSON do nazw kolumn i typów danych tabeli (testów).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Łączenie tabeli Eksplorator danych platformy Azure z usługą IoT Hub

Teraz nawiążesz połączenie z IoT Hubą z usługi Azure Eksplorator danych. Po zakończeniu tego połączenia dane, które przechodzą do strumienia usługi IoT Hub, do [utworzonej tabeli docelowej](#create-a-target-table-in-azure-data-explorer).

1. Wybierz pozycję **powiadomienia** na pasku narzędzi, aby sprawdzić, czy wdrożenie IoT Hub powiodło się.

1. W obszarze utworzony klaster wybierz pozycję **bazy danych** , a następnie wybierz bazę danych, która została utworzona **TestDB**.
    
    ![Wybierz testową bazę danych](media/ingest-data-iot-hub/select-database.png)

1. Wybierz pozycję pozyskiwanie **danych** i **Dodaj połączenie danych**. Następnie wypełnij formularz poniższymi informacjami. Po zakończeniu wybierz pozycję **Utwórz** .

    ![Połączenie IoT Hub](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Źródło danych**:

    **Konfigurowania** | **Opis pola**
    |---|---|
    | Nazwa połączenia danych | Nazwa połączenia, które chcesz utworzyć na platformie Azure Eksplorator danych
    | IoT Hub | Nazwa IoT Hub |
    | Zasady dostępu współdzielonego | Nazwa zasad dostępu współdzielonego. Musi mieć uprawnienia do odczytu |
    | Grupa konsumentów |  Grupa odbiorców zdefiniowana w IoT Hub wbudowanym punkcie końcowym |
    | Właściwości systemu zdarzeń | [Właściwości systemu zdarzeń IoT Hub](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages). Podczas dodawania właściwości systemu [Utwórz](/azure/kusto/management/tables#create-table) lub [zaktualizuj](/azure/kusto/management/tables#alter-table-and-alter-merge-table) schemat i [Mapowanie](/azure/kusto/management/mappings) tabeli w celu uwzględnienia wybranych właściwości. | | | 

    > [!NOTE]
    > W przypadku [ręcznego przełączania do trybu failover](/azure/iot-hub/iot-hub-ha-dr#manual-failover)należy ponownie utworzyć połączenie danych.

    **Tabela docelowa**:

    Dostępne są dwie opcje routingu pozyskiwanych danych: *statyczne* i *dynamiczne*. 
    W tym artykule należy używać routingu statycznego, w którym można określić nazwę tabeli, format danych i mapowanie. W związku z tym Zostaw **moje dane zawiera niewybrane informacje o routingu** .

     **Konfigurowania** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | tabela | *Testy* | Tabela utworzona w **TestDB**. |
    | Format danych | *KODU* | Obsługiwane formaty to Avro, CSV, JSON, WIELOWIERSZOWY kod JSON, PSV, SOHSV, SCSV, TSV, TSVE i TXT. |
    | Mapowanie kolumn | *TestMapping* | [Mapowanie](/azure/kusto/management/mappings) utworzone w **TestDB**, które mapuje przychodzące dane JSON do nazw kolumn i typów danych **TestDB**. Wymagane dla notacji JSON, wielowierszowego kodu JSON i AVRO oraz opcjonalne dla innych formatów.|
    | | |

    > [!NOTE]
    > * Wybierz pozycję **moje dane zawiera informacje o routingu** , aby użyć routingu dynamicznego, gdzie dane zawierają niezbędne informacje dotyczące routingu, jak pokazano w komentarzach [przykładowych aplikacji](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) . Jeśli są ustawione właściwości static i Dynamic, właściwości dynamiczne zastępują statyczne. 
    > * Zostaną pozyskane tylko zdarzenia znajdujące się w kolejce po utworzeniu połączenia danych.

## <a name="generate-sample-data-for-testing"></a>Generuj przykładowe dane do testowania

Aplikacja symulowanego urządzenia nawiązuje połączenie z punktem końcowym specyficznym dla urządzenia w centrum IoT Hub i wysyła symulowaną telemetrię temperatury i wilgotności.

1. Pobierz przykładowy C# projekt z https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip i Wyodrębnij archiwum zip.

1. W oknie terminalu lokalnego przejdź do folderu głównego przykładowego C# projektu. Następnie przejdź do folderu **IoT-hub\Quickstarts\simulated-Device** .

1. Otwórz plik **SimulatedDevice.cs** w wybranym edytorze tekstu.

    Zastąp wartość zmiennej `s_connectionString` parametrami połączenia urządzenia, [Aby zarejestrować urządzenie w IoT Hub](#register-a-device-to-the-iot-hub). Następnie Zapisz zmiany w pliku **SimulatedDevice.cs** .

1. W oknie terminalu lokalnego Uruchom następujące polecenia, aby zainstalować wymagane pakiety dla aplikacji symulowanego urządzenia:

    ```cmd/sh
    dotnet restore
    ```

1. W oknie terminalu lokalnego Uruchom następujące polecenie, aby skompilować i uruchomić aplikację symulowanego urządzenia:

    ```cmd/sh
    dotnet run
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w postaci symulowanej aplikacji urządzenia, która wysyła telemetrię do centrum IoT:

    ![Uruchamianie symulowanego urządzenia](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>Przeglądanie przepływu danych

Za pomocą aplikacji generującej dane można teraz zobaczyć przepływ danych z Centrum IoT Hub do tabeli w klastrze.

1. W Azure Portal w usłudze IoT Hub zobaczysz w działaniu, gdy aplikacja jest uruchomiona.

    ![Metryki IoT Hub](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. Aby sprawdzić, ile komunikatów zostało wprowadzonych do bazy danych do tej pory, uruchom następujące zapytanie w bazie danych testów.

    ```Kusto
    TestTable
    | count
    ```

1. Aby wyświetlić zawartość komunikatów, uruchom następujące zapytanie:

    ```Kusto
    TestTable
    ```

    Zestaw wyników:
    
    ![Pokaż wyniki pozyskiwanych danych](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Usługa Azure Eksplorator danych ma zasady agregacji (wsadowe) do pozyskiwania danych, zaprojektowane w celu optymalizacji procesu pozyskiwania. Zasady są domyślnie skonfigurowane do 5 minut lub 500 MB danych, dzięki czemu mogą wystąpić opóźnienia. Zobacz temat [zasady tworzenia wsadowego](/azure/kusto/concepts/batchingpolicy) dla opcji agregacji. 
    > * Skonfiguruj tabelę do obsługi przesyłania strumieniowego i Usuń opóźnienie w czasie odpowiedzi. Zobacz [zasady przesyłania strumieniowego](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie planujesz ponownie używać IoT Hub, wyczyść polecenie **test-Hub-RG**, aby uniknąć ponoszenia kosztów.

1. W Azure Portal wybierz pozycję **grupy zasobów** po lewej stronie, a następnie wybierz utworzoną grupę zasobów.  

    Jeśli menu po lewej stronie jest zwinięte, wybierz pozycję ![Przycisk rozwijania](media/ingest-data-event-hub/expand.png) , aby je rozwinąć.

   ![Wybierz grupę zasobów do usunięcia](media/ingest-data-event-hub/delete-resources-select.png)

1. W obszarze **test-zasób-Grupa**wybierz pozycję **Usuń grupę zasobów**.

1. W nowym oknie wpisz nazwę grupy zasobów do usunięcia (*test-Hub-RG*), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań dotyczących danych w usłudze Azure Eksplorator danych](web-query-data.md)
