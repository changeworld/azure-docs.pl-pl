---
title: Pozyskiwanie obiektów blob platformy Azure do usługi Azure Data Explorer
description: W tym artykule dowiesz się, jak wysyłać dane konta magazynu do Usługi Azure Data Explorer przy użyciu subskrypcji usługi Event Grid.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ec218b1638183db463ff09488c988cad64d78c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370444"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Pozyskiwania obiektów blob do Eksploratora danych platformy Azure przez subskrybowanie powiadomień dotyczących siatki zdarzeń

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Szablon usługi Azure Resource Manager](data-connection-event-grid-resource-manager.md)

Usługa Azure Data Explorer to szybka i skalowalna usługa eksploracji danych dla danych dziennika i telemetrii. Oferuje ciągłe pozyskiwania (ładowanie danych) z obiektów blob zapisywanych do kontenerów obiektów blob. 

W tym artykule dowiesz się, jak ustawić subskrypcję [usługi Azure Event Grid](/azure/event-grid/overview) i kierować zdarzenia do Usługi Azure Data Explorer za pośrednictwem Centrum zdarzeń. Aby rozpocząć, należy mieć konto magazynu z subskrypcją siatki zdarzeń, która wysyła powiadomienia do usługi Azure Event Hubs. Następnie utworzysz połączenie danych z siatką zdarzeń i zobaczysz przepływ danych w całym systemie.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Klaster i baza danych](create-cluster-database-portal.md).
* [Konto magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Tworzenie subskrypcji usługi Event Grid na koncie magazynu

1. W witrynie Azure portal znajdź swoje konto magazynu.
1.  > Wybierz **Events****subskrypcję zdarzeń**.

    ![Link do aplikacji Zapytanie](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. W oknie **Utwórz subskrypcję zdarzeń** na karcie **Podstawowe** podaj następujące wartości:

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa | *test-grid-connection* | Nazwa siatki zdarzeń, którą chcesz utworzyć.|
    | Schemat zdarzeń | *Schemat usługi Event Grid* | Schemat, który powinien być używany dla siatki zdarzeń. |
    | Typ tematu | *Konto magazynu* | Typ tematu siatki zdarzeń. |
    | Zasób tematu | *gridteststorage* | Nazwa konta magazynu. |
    | Subskrybuj wszystkie typy zdarzeń | *Wyczyść* | Nie będziesz otrzymywać powiadomień o wszystkich zdarzeniach. |
    | Zdefiniowane typy zdarzeń | *Utworzony obiekt blob* | O jakich konkretnych zdarzeniach chcesz otrzymywać powiadomienia. |
    | Typ punktu końcowego | *Centra zdarzeń* | Typ punktu końcowego, do którego wysyłasz zdarzenia. |
    | Endpoint | *test-hub* | Utworzone przez Ciebie centrum zdarzeń. |
    | | |

1. Wybierz kartę **Filtry,** jeśli chcesz śledzić pliki z określonego kontenera. Filtry dla powiadomień ustaw w następujący sposób:
    * **Temat zaczyna się od** pola jest *dosłownym* prefiksem kontenera obiektu blob. Jak wzór stosowany jest *uruchamiaz*, może obejmować wiele pojemników. Symbole wieloznaczne nie są dozwolone.
     Ustawienie *musi* wyglądać następująco: *`/blobServices/default/containers/`*[prefiks kontenera]
    * **Temat kończy się na** — to pole jest sufiksem *literału* obiektu blob. Symbole wieloznaczne nie są dozwolone.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Tworzenie tabeli docelowej w usłudze Azure Data Explorer

Utwórz tabelę w Eksploratorze danych platformy Azure, w której centra zdarzeń będą wysyłać dane. Utwórz tabelę w klastrze i bazie danych przygotowanej w wymaganiach wstępnych.

1. W witrynie Azure Portal w obszarze klastra wybierz pozycję **Zapytanie**.

    ![Link do aplikacji Zapytanie](media/ingest-data-event-grid/query-explorer-link.png)

1. Skopiuj następujące polecenie do okna i wybierz przycisk **Uruchom,** aby utworzyć tabelę (TestTable), która otrzyma pochłonięto dane.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Uruchamianie zapytania create](media/ingest-data-event-grid/run-create-table.png)

1. Skopiuj poniższe polecenie w oknie, a następnie wybierz pozycję **Uruchom**, aby zamapować przychodzące dane w formacie JSON na nazwy kolumn i typy danych tabeli (TestTable).

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Tworzenie połączenie danych usługi Event Grid w usłudze Azure Data Explorer

Teraz połącz się z siatką zdarzeń z Usługi Azure Data Explorer, dzięki czemu dane przepływające do kontenera obiektów blob są przesyłane strumieniowo do tabeli testowej. 

1. Wybierz pozycję **Powiadomienia** na pasku narzędzi, aby sprawdzić, czy wdrożenie centrum zdarzeń zakończyło się pomyślnie.

1. W utworzonym klastrze wybierz pozycję **Baza danych** > **TestDatabase**.

    ![Wybieranie testowej bazy danych](media/ingest-data-event-grid/select-test-database.png)

1. Wybierz **pozycję Pozyskiwania danych** > **Dodaj połączenie danych**.

    ![Wprowadzanie danych](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Wybierz typ połączenia: **Magazyn obiektów blob**.

1. Wypełnij formularz następującymi informacjami i wybierz pozycję **Utwórz**.

    ![Połączenie centrum zdarzeń](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Źródło danych:

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa połączenia danych | *test-hub-connection* | Nazwa połączenia, które chcesz utworzyć w Eksploratorze danych platformy Azure.|
    | Subskrypcja konta magazynu | Identyfikator Twojej subskrypcji | Identyfikator subskrypcji, w której znajduje się konto magazynu.|
    | Konto magazynu | *gridteststorage* | Nazwa utworzonego wcześniej konta magazynu.|
    | Event Grid | *test-grid-connection* | Nazwa utworzonej siatki zdarzeń. |
    | Nazwa centrum zdarzeń | *test-hub* | Utworzone centrum zdarzeń. To pole jest wypełniane automatycznie po wybraniu siatki zdarzeń. |
    | Grupa konsumentów | *test-group* | Grupa odbiorców zdefiniowana w utworzonym centrum zdarzeń. |
    | | |

    Tabela docelowa:

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Tabela | *TestTable* | Tabela utworzona przez Ciebie w obszarze **TestDatabase**. |
    | Format danych | *Json* | Obsługiwane formaty to Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV, RAW i TXT. Obsługiwane opcje kompresji: Zip i GZip |
    | Mapowanie kolumn | *TestMapping* | Mapowanie utworzone przez Ciebie w obszarze **TestDatabase**, które mapuje przychodzące dane JSON na nazwy kolumn i typy danych tabeli **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Generowanie danych przykładowych

Teraz, gdy Usługa Azure Data Explorer i konto magazynu są połączone, można utworzyć przykładowe dane i przekazać je do magazynu obiektów blob.

Będziemy pracować z krótkim skryptem powłoki, który wykona kilka podstawowych poleceń interfejsu wiersza polecenia platformy Azure umożliwiających interakcje z zasobami usługi Azure Storage. Ten skrypt tworzy nowy kontener na koncie magazynu, przekazuje istniejący plik (jako obiekt blob) do tego kontenera, a następnie wyświetla listę obiektów blob w kontenerze. Za pomocą [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) można wykonać skrypt bezpośrednio w portalu.

Zapisz dane w pliku i przekaż go za pomocą tego skryptu:

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
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
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> Aby osiągnąć najlepszą wydajność pozyskiwania, należy przekazać *nieskompresowany* rozmiar skompresowanych obiektów blob przesłanych do spożycia. Ponieważ powiadomienia w uścisku zdarzeń zawierają tylko podstawowe szczegóły, informacje o rozmiarze muszą być jawnie przekazywane. Nieskompresowane informacje o rozmiarze `rawSizeBytes` mogą być dostarczane przez ustawienie właściwości na metadanych obiektu blob z *nieskompresowanego* rozmiaru danych w bajtach.

### <a name="ingestion-properties"></a>Właściwości pozyskiwania

Można określić [właściwości pozyskiwania](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#ingestion-properties) obiektu blob pozyskiwania za pośrednictwem metadanych obiektu blob.

Te właściwości można ustawić:

|**Właściwość** | **Opis właściwości**|
|---|---|
| `rawSizeBytes` | Rozmiar surowych (nieskompresowanych) danych. W przypadku avro/ORC/Parkiet jest to rozmiar przed zastosowaniem kompresji specyficznej dla formatu.|
| `kustoTable` |  Nazwa istniejącej tabeli docelowej. Zastępuje `Table` zestaw na `Data Connection` bloku. |
| `kustoDataFormat` |  Format danych. Zastępuje `Data format` zestaw na `Data Connection` bloku. |
| `kustoIngestionMappingReference` |  Nazwa istniejącego mapowania pozyskiwania, które ma być używane. Zastępuje `Column mapping` zestaw na `Data Connection` bloku.|
| `kustoIgnoreFirstRecord` | Jeśli ustawiona na `true`, Kusto ignoruje pierwszy wiersz obiektu blob. Użyj w formacie tabelarycznym danych (CSV, TSV lub podobne), aby zignorować nagłówki. |
| `kustoExtentTags` | Ciąg [reprezentujący znaczniki,](/azure/kusto/management/extents-overview#extent-tagging) które zostaną dołączone do wynikowy zakres. |
| `kustoCreationTime` |  Zastępuje [$IngestionTime](/azure/kusto/query/ingestiontimefunction?pivots=azuredataexplorer) dla obiektu blob sformatowany jako ciąg ISO 8601. Służy do zasypywania. |

> [!NOTE]
> Usługa Azure Data Explorer nie będzie usuwać obiektów blob po spożyciu.
> Zachowaj plamy na thrre do pięciu dni.
> Zarządzanie usuwaniem obiektów blob za pomocą [cyklu życia magazynu obiektów Blob](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) za pomocą usługi Azure Blob Storage. 

## <a name="review-the-data-flow"></a>Przeglądanie przepływu danych

> [!NOTE]
> Usługa Azure Data Explorer ma zasady agregacji (wsadowania) dla pozyskiwania danych przeznaczone do optymalizacji procesu pozyskiwania.
Domyślna zasada jest skonfigurowana na 5 minut.
W razie potrzeby będzie można zmienić zasady w późniejszym czasie. W tym artykule można oczekiwać opóźnienia kilku minut.

1. W witrynie Azure Portal w obszarze siatki zdarzeń zobaczysz wzrost aktywności, gdy aplikacja jest uruchomiona.

    ![Wykres siatki zdarzeń](media/ingest-data-event-grid/event-grid-graph.png)

1. Aby sprawdzić, ile komunikatów zostało przekazanych do tej pory do bazy danych, uruchom poniższe zapytanie w testowej bazie danych.

    ```kusto
    TestTable
    | count
    ```

1. Aby sprawdzić zawartość komunikatów, uruchom następujące zapytanie w swojej testowej bazie danych.

    ```kusto
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

1. W nowym oknie wprowadź nazwę grupy zasobów do usunięcia (*test-hub-rg*), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* [wykonywanie zapytań o dane w usłudze Azure Data Explorer](web-query-data.md)
