---
title: Używanie poleceń sterujących usługi Azure Data Explorer w fabryce danych platformy Azure
description: W tym temacie użyj poleceń sterowania Usługi Azure Data Explorer w usłudze Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264492"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Uruchamianie poleceń sterujących usługi Azure Data Explorer za pomocą działania polecenia usługi Azure Data Factory

[Usługa Azure Data Factory](/azure/data-factory/) (ADF) to usługa integracji danych oparta na chmurze, która umożliwia wykonywanie kombinacji działań na danych. Za pomocą podajnika ADF można tworzyć przepływy pracy oparte na danych do organizowania i automatyzacji przenoszenia danych i przekształcania danych. Działanie **polecenia usługi Azure Data Explorer** w usłudze Azure Data Factory umożliwia [uruchamianie poleceń sterujących usługi Azure Data Explorer](/azure/kusto/concepts/#control-commands) w ramach przepływu pracy usługi ADF. W tym artykule dowiesz się, jak utworzyć potok z działaniem odnośnika i forEach działania zawierające działanie polecenia Usługi Azure Data Explorer.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Klaster i baza danych usługi Azure Data Explorer](create-cluster-database-portal.md)
* Źródło danych.
* [Fabryka danych](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Tworzenie nowego potoku

1. Wybierz narzędzie **Autor** ołówka. 
1. Utwórz nowy potok, **+** wybierając, a następnie wybierz **potok** z listy rozwijanej.

   ![tworzenie nowego potoku](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Tworzenie działania odnośnika

[Działanie odnośnika](/azure/data-factory/control-flow-lookup-activity) można pobrać zestaw danych z dowolnego źródła danych obsługiwanych przez usługę Azure Data Factory. Dane wyjściowe z działania odnośnika mogą być używane w ForEach lub inne działanie.

1. W okienku **Działania** w obszarze **Ogólne**wybierz aktywność **odnośnika.** Przeciągnij i upuść go do głównego płótna po prawej stronie.
 
    ![wybieranie działania odnośnika](media/data-factory-command-activity/select-activity.png)

1. Kanwa zawiera teraz utworzone działanie odnośnika. Użyj kart pod kanwą, aby zmienić odpowiednie parametry. **Ogólnie rzecz biorąc**, zmień nazwę działania. 

    ![edytowanie aktywności odnośnika](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Kliknij pusty obszar kanwy, aby wyświetlić właściwości potoku. Użyj **ogólne** kartę, aby zmienić nazwę potoku. Nasz rurociąg nosi nazwę *pipeline-4-docs*.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Tworzenie zestawu danych Usługi Azure Data Explorer w działaniu odnośnym

1. W **obszarze Ustawienia**wybierz wstępnie utworzony zestaw danych usługi Azure Data Explorer **Source**lub wybierz pozycję **+ Nowy,** aby utworzyć nowy zestaw danych.
 
    ![dodawanie zestawu danych w ustawieniach odnośnych](media/data-factory-command-activity/lookup-settings.png)

1. Wybierz zestaw danych **Usługi Azure Data Explorer (Kusto)** z okna Nowy zestaw **danych.** Wybierz **pozycję Kontynuuj,** aby dodać nowy zestaw danych.

   ![wybieranie nowego zestawu danych](media/data-factory-command-activity/select-new-dataset.png) 

1. Nowe parametry zestawu danych Usługi Azure Data Explorer są widoczne w **ustawieniach**. Aby zaktualizować parametry, wybierz pozycję **Edytuj**.

    ![ustawienia wyszukiwania z zestawem danych Usługi Azure Data Explorer](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. **AzureDataExplorerTable** nowa karta otwiera się w głównym kanwy. 
    * Wybierz **pozycję Ogólne** i edytuj nazwę zestawu danych. 
    * Wybierz **opcję Połączenie,** aby edytować właściwości zestawu danych. 
    * Wybierz **usługę połączona** z listy rozwijanej lub wybierz **+ Nowy,** aby utworzyć nową usługę połączona.

    ![Edytowanie właściwości zestawu danych Usługi Azure Data Explorer](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Podczas tworzenia nowej połączonej usługi zostanie otwarta strona **Nowa usługa połączona (Azure Data Explorer):**

    ![Nowa usługa połączona adx](media/data-factory-command-activity/adx-new-linked-service.png)

   * Wybierz **nazwę** usługi połączonej z Eksploratorem danych platformy Azure. W razie potrzeby dodaj **opis.**
   * W **obszarze Połącz za pośrednictwem środowiska uruchomieniowego integracji,** w razie potrzeby zmień bieżące ustawienia. 
   * W **opcji Wyboru konta** wybierz klaster przy użyciu jednej z dwóch metod: 
        * Wybierz przycisk opcji **z subskrypcji platformy Azure** i wybierz swoje konto subskrypcji platformy **Azure.** Następnie wybierz **klaster**. Należy zauważyć, że lista rozwijana będzie tylko lista klastrów, które należą do użytkownika.
        * Zamiast tego wybierz pozycję Wprowadź przycisk opcji **ręcznej** i wprowadź **punkt końcowy** (adres URL klastra).
    * Określ **dzierżawcę**.
    * Wprowadź **identyfikator jednostki usługi**. Identyfikator jednostki musi mieć odpowiednie uprawnienia, zgodnie z poziomem uprawnień wymaganym przez używane polecenie.
    * Wybierz przycisk **klucza głównego usługi** i wprowadź **klucz główny usługi**.
    * Wybierz **bazę danych** z menu rozwijanego. Alternatywnie zaznacz pole wyboru **Edytuj** i wprowadź nazwę bazy danych.
    * Wybierz **opcję Testuj połączenie,** aby przetestować utworzone połączenie połączonej usługi. Jeśli można połączyć się z konfiguracją, pojawi się zielony znacznik wyboru **Połączenie powiodło.**
    * Wybierz **zakończ,** aby zakończyć tworzenie połączonej usługi.

1. Po skonfigurowaniu połączonej usługi w **usłudze AzureDataExplorerTable** > **Connection**dodaj nazwę **tabeli.** Wybierz **opcję Podgląd danych**, aby upewnić się, że dane są poprawnie prezentowane.

   Zestaw danych jest już gotowy i można kontynuować edycję potoku.

### <a name="add-a-query-to-your-lookup-activity"></a>Dodawanie kwerendy do aktywności odnośnika

1. W**ustawieniach** **pipeline-4-docs** > dodaj kwerendę w polu tekstowym **kwerendy,** na przykład:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. W razie potrzeby zmień limit **czasu kwerendy** lub **Brak obcinania** i **Tylko pierwszy wiersz.** W tym przepływie zachowujemy domyślny **limit czasu kwerendy** i wyczyść zaznaczenie pól wyboru. 

    ![Ostateczne ustawienia aktywności odnośnika](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Tworzenie działania dla każdego 

[For-Each](/azure/data-factory/control-flow-for-each-activity) działania jest używany do iteracji za kolekcji i wykonywania określonych działań w pętli. 

1. Teraz można dodać for-each działania do potoku. To działanie będzie przetwarzać dane zwrócone z działania odnośnika. 
    * W okienku **Działania** w obszarze **Iteracja & warunkowe**wybierz działanie **ForEach** i przeciągnij je i upuść na kanwę.
    * Narysuj linię między dane wyjściowe działania odnośnika i dane wejściowe forEach działania w kanwie, aby je połączyć.

        ![Działanie ForEach](media/data-factory-command-activity/for-each-activity.png)

1.  Wybierz działanie ForEach na kanwie. Na karcie **Ustawienia** poniżej:
    * Sprawdź **sekwencyjne** pole wyboru, aby uzyskać sekwencyjne przetwarzanie wyników wyszukiwania lub pozostaw niezaznaczone, aby utworzyć przetwarzanie równoległe.
    * Ustaw **liczbę partii**.
    * W **pozycji ,** podaj następujące odwołanie do wartości wyjściowej: * @activity('Lookup1').output.value*

       ![Ustawienia działania ForEach](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Tworzenie działania polecenia usługi Azure Data Explorer w ramach działania ForEach

1. Kliknij dwukrotnie forEach działania na kanwie, aby otworzyć go w nowym kanwie, aby określić działania w ForEach.
1. W okienku **Działania** w obszarze **Eksplorator danych platformy Azure**wybierz działanie polecenia usługi Azure Data **Explorer** i przeciągnij je i upuść na kanwę.

    ![Działanie polecenia Usługi Azure Data Explorer](media/data-factory-command-activity/adx-command-activity.png)

1.  Na karcie **Połączenie** wybierz tę samą wcześniej utworzoną usługę połączeniową.

    ![karta połączenie działania polecenia usługi Azure data explorer](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. Na karcie **Polecenie** podaj następujące polecenie:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    **Polecenie** nakazuje Eksploratorowi danych platformy Azure wyeksportowanie wyników danej kwerendy do magazynu obiektów blob w skompresowanym formacie. Działa asynchronicznie (przy użyciu modyfikatora asynchronicznego).
    Kwerenda dotyczy kolumny bazy danych każdego wiersza w wyniku działania odnośnika. Limit **czasu polecenia** może pozostać bez zmian.

    ![działanie polecenia](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > Działanie polecenia ma następujące limity:
    > * Limit rozmiaru: 1 MB rozmiaru odpowiedzi
    > * Limit czasu: 20 minut (domyślnie), 1 godzina (maksymalnie).
    > * W razie potrzeby można dołączyć kwerendę do wyniku za pomocą [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands), aby zmniejszyć wynikowy rozmiar/czas.

1.  Teraz rurociąg jest gotowy. Możesz wrócić do głównego widoku potoku, klikając nazwę potoku.

    ![Potok poleceń Eksploratora danych platformy Azure](media/data-factory-command-activity/adx-command-pipeline.png)

1. Wybierz **debugowanie** przed opublikowaniem potoku. Postęp potoku można monitorować na karcie **Dane wyjściowe.**

    ![dane wyjściowe działania polecenia usługi Azure data explorer](media/data-factory-command-activity/command-activity-output.png)

1. Można **opublikować wszystkie,** a następnie **Dodaj wyzwalacz,** aby uruchomić potok. 

## <a name="control-command-outputs"></a>Wyjmują polecenia sterowania

Struktura danych wyjściowych działania polecenia jest szczegółowo opisana poniżej. To dane wyjściowe mogą być używane przez następne działanie w potoku.

### <a name="returned-value-of-a-non-async-control-command"></a>Zwracana wartość polecenia sterującego nieaskroniencyjnego

W poleceniu kontroli nieaskroniencyjnej struktura zwracanej wartości jest podobna do struktury wyniku działania odnośnika. To `count` pole wskazuje liczbę zwróconych rekordów. Stałe pole `value` tablicy zawiera listę rekordów. 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>Zwracana wartość polecenia sterującego asynchronią

W poleceniu kontroli asynchronii aktywność sonduje tabelę operacji za kulisami, dopóki operacja asynchroniza nie zostanie ukończona lub przekroczony czas. W związku z tym zwracana `.show operations OperationId` wartość będzie zawierać wynik dla danej **właściwości OperationId.** Sprawdź wartości **state** i **status** właściwości, aby sprawdzić pomyślne zakończenie operacji.

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [kopiować dane do Eksploratora danych platformy Azure przy użyciu usługi Azure Data Factory](data-factory-load-data.md).
* Dowiedz się więcej o używaniu [szablonu usługi Azure Data Factory do kopiowania zbiorczego z bazy danych do Eksploratora danych platformy Azure.](data-factory-template.md)
