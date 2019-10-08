---
title: Pozyskiwanie obiektów blob platformy Azure w usłudze Azure Eksplorator danych
description: W tym artykule dowiesz się, jak wysyłać dane konta magazynu do usługi Azure Eksplorator danych przy użyciu subskrypcji Event Grid.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 3c2407472cd15326c295f70c69606fc5ee663f72
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996797"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Pozyskiwanie obiektów BLOB na platformie Azure Eksplorator danych przez Subskrybowanie powiadomień Event Grid

Azure Eksplorator danych to szybka i skalowalna usługa eksploracji danych dla danych dzienników i telemetrii. Oferuje ciągłe pozyskiwanie (ładowanie danych) z obiektów blob, które są zapisywane do kontenerów obiektów BLOB. 

W tym artykule dowiesz się, jak ustawić subskrypcję [Azure Event Grid](/azure/event-grid/overview) i kierować zdarzenia do usługi Azure Eksplorator danych za pośrednictwem centrum zdarzeń. Aby rozpocząć, musisz mieć konto magazynu z subskrypcją usługi Event Grid, która wysyła powiadomienia do Event Hubs platformy Azure. Następnie utworzysz połączenie danych Event Grid i przepływ danych zostanie wyświetlony w całym systemie.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Klaster i baza danych](create-cluster-database-portal.md).
* [Konto magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Tworzenie subskrypcji Event Grid na koncie magazynu

1. W Azure Portal Znajdź konto magazynu.
1. Wybierz pozycję **zdarzenia** > **subskrypcja zdarzeń**.

    ![Link do aplikacji zapytania](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. W oknie **Tworzenie subskrypcji zdarzeń** na karcie **podstawowa** podaj następujące wartości:

    **Konfigurowania** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa | *Testowanie siatki — połączenie* | Nazwa siatki zdarzeń, która ma zostać utworzona.|
    | Schemat zdarzenia | *Schemat Event Grid* | Schemat, który ma być używany dla usługi Event Grid. |
    | Typ tematu | *Konto magazynu* | Typ tematu siatki zdarzeń. |
    | Zasób tematu | *gridteststorage* | Nazwa konta magazynu. |
    | Subskrybowanie wszystkich typów zdarzeń | *Wyczyść* | Nie Otrzymuj powiadomienia o wszystkich zdarzeniach. |
    | Zdefiniowane typy zdarzeń | *Utworzono obiekt BLOB* | Określone zdarzenia, które mają zostać powiadomione. |
    | Typ punktu końcowego | *Centra zdarzeń* | Typ punktu końcowego, do którego są wysyłane zdarzenia. |
    | Punkt końcowy | *Centrum testów* | Utworzone centrum zdarzeń. |
    | | |

1. Wybierz kartę **dodatkowe funkcje** , jeśli chcesz śledzić pliki z określonego kontenera. Ustaw filtry dla powiadomień w następujący sposób:
    * **Temat rozpoczyna się od** pola jest prefiksem *literału* kontenera obiektów BLOB. Ponieważ stosowany wzorzec to *StartsWith*, może on obejmować wiele kontenerów. Nie są dozwolone symbole wieloznaczne.
     *Musi* być ustawiona w następujący sposób: *`/blobServices/default/containers/`* [prefiks kontenera]
    * **Temat koniec** pola jest sufiksem *literału* obiektu BLOB. Nie są dozwolone symbole wieloznaczne.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Tworzenie tabeli docelowej na platformie Azure Eksplorator danych

Utwórz tabelę w usłudze Azure Eksplorator danych, w której Event Hubs będą wysyłać dane. Utwórz tabelę w klastrze i bazie danych przygotowanej w wymaganiach wstępnych.

1. W Azure Portal w ramach klastra wybierz opcję **zapytanie**.

    ![Link do aplikacji zapytania](media/ingest-data-event-grid/query-explorer-link.png)

1. Skopiuj następujące polecenie do okna i wybierz polecenie **Uruchom** , aby utworzyć tabelę (Tester), która będzie odbierać dane pozyskiwane.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Uruchom Tworzenie zapytania](media/ingest-data-event-grid/run-create-table.png)

1. Skopiuj następujące polecenie do okna i wybierz pozycję **Uruchom** , aby zmapować przychodzące dane JSON do nazw kolumn i typów danych tabeli (testów).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Tworzenie połączenia danych Event Grid na platformie Azure Eksplorator danych

Teraz Połącz się z Event Grid z poziomu usługi Azure Eksplorator danych, aby dane przepływające do kontenera obiektów BLOB były przesyłane strumieniowo do tabeli testów. 

1. Wybierz pozycję **powiadomienia** na pasku narzędzi, aby sprawdzić, czy wdrożenie centrum zdarzeń zakończyło się pomyślnie.

1. W obszarze utworzonego klastra wybierz pozycję **bazy danych** > **TestDatabase**.

    ![Wybierz testową bazę danych](media/ingest-data-event-grid/select-test-database.png)

1. Wybierz pozycję pozyskiwanie **danych** > **Dodaj połączenie danych**.

    ![Pozyskiwanie danych](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Wybierz typ połączenia: **BLOB Storage**.

1. Wypełnij formularz poniższymi informacjami, a następnie wybierz pozycję **Utwórz**.

    ![Połączenie centrum zdarzeń](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Źródło danych:

    **Konfigurowania** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa połączenia danych | *Test-Hub — połączenie* | Nazwa połączenia, które ma zostać utworzone w usłudze Azure Eksplorator danych.|
    | Subskrypcja konta magazynu | Identyfikator subskrypcji | Identyfikator subskrypcji, w której znajduje się konto magazynu.|
    | Konto magazynu | *gridteststorage* | Nazwa konta magazynu, które zostało utworzone wcześniej.|
    | Event Grid | *Testowanie siatki — połączenie* | Nazwa utworzonej siatki zdarzeń. |
    | Nazwa centrum zdarzeń | *Centrum testów* | Utworzone centrum zdarzeń. To pole jest wypełniane automatycznie po wybraniu siatki zdarzeń. |
    | Grupa konsumentów | *Grupa testów* | Grupa odbiorców zdefiniowana w utworzonym centrum zdarzeń. |
    | | |

    Tabela docelowa:

     **Konfigurowania** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | tabela | *Testy* | Tabela utworzona w **TestDatabase**. |
    | Format danych | *KODU* | Obsługiwane formaty to Avro, CSV, JSON, WIELOWIERSZOWY kod JSON, PSV, raport o kondycji, SCSV, TSV i TXT. Obsługiwane opcje kompresji: zip i GZip |
    | Mapowanie kolumn | *TestMapping* | Mapowanie utworzone w **TestDatabase**, które mapuje przychodzące dane JSON do nazw kolumn i typów **danych.**|
    | | |
    
## <a name="generate-sample-data"></a>Generowanie danych przykładowych

Teraz, gdy usługa Azure Eksplorator danych i konto magazynu są połączone, można utworzyć przykładowe dane i przekazać je do magazynu obiektów BLOB.

Będziemy współpracować z małym skryptem powłoki, który wystawia kilka podstawowych poleceń interfejsu wiersza polecenia platformy Azure w celu współdziałania z zasobami usługi Azure Storage. Ten skrypt tworzy nowy kontener na koncie magazynu, przekazuje istniejący plik (jako obiekt BLOB) do tego kontenera, a następnie wyświetla listę obiektów BLOB w kontenerze. Za pomocą [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) można wykonać skrypt bezpośrednio w portalu.

Zapisz dane w pliku i przekaż je za pomocą tego skryptu:

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
> Usługa Azure Eksplorator danych ma zasady agregacji (wsadowe) na potrzeby pozyskiwania danych, które pozwalają zoptymalizować proces pozyskiwania.
Domyślnie zasady są skonfigurowane na 5 minut.
W razie potrzeby będzie można zmienić zasady w późniejszym czasie. W tym artykule można oczekiwać opóźnienia kilku minut.

1. W Azure Portal w usłudze Event Grid zobaczysz w działaniu, gdy aplikacja jest uruchomiona.

    ![Wykres w usłudze Event Grid](media/ingest-data-event-grid/event-grid-graph.png)

1. Aby sprawdzić, ile komunikatów zostało wprowadzonych do bazy danych do tej pory, uruchom następujące zapytanie w bazie danych testów.

    ```Kusto
    TestTable
    | count
    ```

1. Aby wyświetlić zawartość komunikatów, uruchom następujące zapytanie w testowej bazie danych.

    ```Kusto
    TestTable
    ```

    Zestaw wyników powinien wyglądać podobnie do poniższego.

    ![Zestaw wyników komunikatów](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie planujesz ponownie korzystać z centrum zdarzeń, Oczyść **test-Hub-RG**, aby uniknąć ponoszenia kosztów.

1. W Azure Portal wybierz pozycję **grupy zasobów** po lewej stronie, a następnie wybierz utworzoną grupę zasobów.  

    Jeśli menu po lewej stronie jest zwinięte, wybierz pozycję ![Przycisk rozwijania](media/ingest-data-event-grid/expand.png) , aby je rozwinąć.

   ![Wybierz grupę zasobów do usunięcia](media/ingest-data-event-grid/delete-resources-select.png)

1. W obszarze **test-zasób-Grupa**wybierz pozycję **Usuń grupę zasobów**.

1. W nowym oknie wprowadź nazwę grupy zasobów do usunięcia (*test-Hub-RG*), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań dotyczących danych w usłudze Azure Eksplorator danych](web-query-data.md)
