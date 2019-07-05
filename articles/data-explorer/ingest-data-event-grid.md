---
title: Pozyskiwanie obiektów blob platformy Azure do usługi Azure Data Explorer
description: W tym artykule dowiesz się, jak wysyłać dane do konta magazynu do Eksploratora danych Azure przy użyciu subskrypcji usługi Event Grid.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 5854a8974a4d2a9dbc1aa690dc2340fd806f4219
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490129"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Przesyłanie obiektów blob do Eksploratora danych usługi Azure dzięki subskrypcji usługi Event Grid powiadomienia

Eksplorator danych usługi Azure to usługa eksploracji danych szybkich i skalowalnych danych dzienników i danych telemetrycznych. Oferuje ona ciągłe wprowadzanie (Ładowanie danych) z obiektów blob, zapisywane w kontenerach obiektów blob. 

W tym artykule dowiesz się, jak ustawić [usługi Azure Event Grid](/azure/event-grid/overview) subskrypcji i kierowanie zdarzeń do Eksploratora danych platformy Azure za pośrednictwem Centrum zdarzeń. Aby rozpocząć, należy mieć konto magazynu z subskrypcji usługi event grid wysyłającego powiadomienia o usłudze Azure Event Hubs. Następnie utworzysz połączenie danych usługi Event Grid i wyświetlić dane przepływu w całym systemie.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Tworzenie [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Klaster i bazy danych](create-cluster-database-portal.md).
* [Konto magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Tworzenie subskrypcji usługi Event Grid na koncie magazynu

1. W witrynie Azure portal Znajdź swoje konto magazynu.
1. Wybierz **zdarzenia** > **subskrypcji zdarzeń**.

    ![Link do aplikacji Zapytanie](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. W oknie **Utwórz subskrypcję zdarzeń** na karcie **Podstawowe** podaj następujące wartości:

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Name (Nazwa) | *test-grid-connection* | Nazwa usługi event grid, która ma zostać utworzona.|
    | Schemat zdarzeń | *Schemat siatki zdarzeń* | Schemat, które mają być używane dla usługi event grid. |
    | Typ tematu | *Konto magazynu* | Typ tematu siatki zdarzeń. |
    | Zasób tematu | *gridteststorage* | Nazwa konta magazynu. |
    | Subskrybuj wszystkie typy zdarzeń | *Usuń zaznaczenie* | Nie będziesz otrzymywać powiadomień o wszystkich zdarzeniach. |
    | Zdefiniowane typy zdarzeń | *Utworzony obiekt blob* | O jakich konkretnych zdarzeniach chcesz otrzymywać powiadomienia. |
    | Typ punktu końcowego | *Usługa Event hubs* | Typ punktu końcowego, do którego wysyłasz zdarzenia. |
    | Endpoint | *test-hub* | Utworzone przez Ciebie centrum zdarzeń. |
    | | |

1. Wybierz kartę **Dodatkowe funkcje**, jeśli chcesz śledzić pliki z określonego kontenera. Filtry dla powiadomień ustaw w następujący sposób:
    * **Podmiotu zaczyna się od** pole jest *literału* prefiksu określonego kontenera obiektów blob. Wzorzec stosowany jest *startswith*, mogą znajdować się wiele kontenerów. Symbole wieloznaczne nie są dozwolone.
     Ustawienie *musi* wyglądać następująco: *`/blobServices/default/containers/`* [prefiks kontenera]
    * **Temat kończy się na** — to pole jest sufiksem *literału* obiektu blob. Symbole wieloznaczne nie są dozwolone.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Tworzenie tabeli docelowej w usłudze Azure Data Explorer

Utwórz tabelę w Eksploratorze danych platformy Azure, gdzie usługa Event Hubs będą wysyłały danych. Tworzenie tabeli w klastrze i baza danych została przygotowana w wymaganiach wstępnych.

1. W witrynie Azure Portal w obszarze klastra wybierz pozycję **Zapytanie**.

    ![Link do aplikacji Zapytanie](media/ingest-data-event-grid/query-explorer-link.png)

1. Skopiuj poniższe polecenie w oknie, a następnie wybierz pozycję **Uruchom** można utworzyć tabeli (TestTable), który będzie otrzymywał pozyskiwanych danych.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Uruchamianie zapytania create](media/ingest-data-event-grid/run-create-table.png)

1. Skopiuj poniższe polecenie w oknie, a następnie wybierz pozycję **Uruchom**, aby zamapować przychodzące dane w formacie JSON na nazwy kolumn i typy danych tabeli (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Tworzenie połączenie danych usługi Event Grid w usłudze Azure Data Explorer

Teraz połączyć się z usługi event grid z Eksploratora danych usługi Azure, tak, aby dane trafiające do kontenera obiektów blob jest przesyłany strumieniowo do tabeli testu.

1. Wybierz pozycję **Powiadomienia** na pasku narzędzi, aby sprawdzić, czy wdrożenie centrum zdarzeń zakończyło się pomyślnie.

1. W obszarze utworzonego klastra, wybierz **baz danych** > **TestDatabase**.

    ![Wybieranie testowej bazy danych](media/ingest-data-event-grid/select-test-database.png)

1. Wybierz **pozyskiwanie danych** > **Dodaj połączenie danych**.

    ![Wprowadzanie danych](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Wybierz typ połączenia: **Blob Storage**.

1. Wypełnij formularz następującymi informacjami, a następnie wybierz pozycję **Utwórz**.

    ![Połączenie centrum zdarzeń](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Źródło danych:

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa połączenia danych | *test-hub-connection* | Nazwa połączenia, które ma zostać utworzona w Eksploratorze danych platformy Azure.|
    | Subskrypcja konta magazynu | Identyfikator Twojej subskrypcji | Identyfikator subskrypcji, w której znajduje się konto magazynu.|
    | Konto magazynu | *gridteststorage* | Nazwa konta magazynu, który został utworzony wcześniej.|
    | Event Grid | *test-grid-connection* | Nazwa usługi event grid, który został utworzony. |
    | Nazwa centrum zdarzeń | *test-hub* | Centrum zdarzeń, który został utworzony. To pole zostanie wypełnione automatycznie, podczas pobierania usługi event grid. |
    | Grupa konsumentów | *test-group* | Grupa odbiorców zdarzeń zdefiniowanych koncentratora, który został utworzony. |
    | | |

    Tabela docelowa:

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Tabela | *TestTable* | Tabela utworzona przez Ciebie w obszarze **TestDatabase**. |
    | Format danych | *JSON* | Obsługiwane formaty to: Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV i TXT. Obsługiwane opcje kompresji: Zip i GZip |
    | Mapowanie kolumn | *TestMapping* | Mapowanie utworzone przez Ciebie w obszarze **TestDatabase**, które mapuje przychodzące dane JSON na nazwy kolumn i typy danych tabeli **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Generowanie danych przykładowych

Teraz, gdy są połączone Eksplorator danych platformy Azure i konto magazynu, możesz utworzyć przykładowe dane i przekaż go do magazynu obiektów blob.

Będziemy pracować z krótkim skryptem powłoki, który wykona kilka podstawowych poleceń interfejsu wiersza polecenia platformy Azure umożliwiających interakcje z zasobami usługi Azure Storage. Ten skrypt tworzy nowy kontener na koncie magazynu, przekazuje istniejącego pliku (jako obiekt blob) do tego kontenera i następnie wyświetla listę obiektów blob w kontenerze. Możesz użyć [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) można wykonać skryptu bezpośrednio w portalu.

Zapisz dane w pliku i przekaż go za pomocą tego skryptu:

```Json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```bash
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

## <a name="review-the-data-flow"></a>Przeglądanie przepływu danych

> [!NOTE]
> Eksplorator usługi Azure Data ma zasady agregacji (przetwarzanie wsadowe) w celu pozyskiwania danych opracowana w celu optymalizacji procesu pozyskiwania.
Domyślna zasada jest skonfigurowana na 5 minut.
Będzie można zmienić zasady w późniejszym czasie, jeśli to konieczne. W tym artykule można spodziewać się opóźnienia za kilka minut.

1. W witrynie Azure Portal w obszarze siatki zdarzeń zobaczysz wzrost aktywności, gdy aplikacja jest uruchomiona.

    ![Wykres siatki zdarzeń](media/ingest-data-event-grid/event-grid-graph.png)

1. Aby sprawdzić, ile komunikatów zostało przekazanych do tej pory do bazy danych, uruchom poniższe zapytanie w testowej bazie danych.

    ```Kusto
    TestTable
    | count
    ```

1. Aby sprawdzić zawartość komunikatów, uruchom następujące zapytanie w swojej testowej bazie danych.

    ```Kusto
    TestTable
    ```

    Zestaw wyników powinien wyglądać tak jak na poniższej ilustracji.

    ![Zestaw wyników komunikatów](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz ponownie używać siatki zdarzeń, wyczyść grupę zasobów **test-hub-rg**, aby uniknąć ponoszenia kosztów.

1. W witrynie Azure Portal wybierz **grupy zasobów** daleko po lewej stronie, a następnie wybierz utworzoną grupę zasobów.  

    Jeśli menu po lewej stronie jest zwinięte, wybierz ![przycisk Rozwiń,](media/ingest-data-event-grid/expand.png) aby je rozwinąć.

   ![Wybieranie grupy zasobów do usunięcia](media/ingest-data-event-grid/delete-resources-select.png)

1. W obszarze **test-resource-group** wybierz pozycję **Usuń grupę zasobów**.

1. W nowym oknie, wprowadź nazwę grupy zasobów do usunięcia (*test-hub-rg*), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

* [Wykonywanie zapytań dotyczących danych w Eksploratorze danych platformy Azure](web-query-data.md)
