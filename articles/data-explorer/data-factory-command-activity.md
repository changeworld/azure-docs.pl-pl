---
title: Korzystanie z poleceń sterowania Eksplorator danych platformy Azure w programie Azure Data Factory
description: W tym temacie Użyj poleceń sterowania Eksplorator danych platformy Azure w programie Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264492"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Użyj działania Azure Data Factory polecenia, aby uruchomić polecenia usługi Azure Eksplorator danych Control

[Azure Data Factory](/azure/data-factory/) (ADF) to oparta na chmurze usługa integracji danych, która umożliwia wykonywanie kombinacji działań na danych. Użyj podajnika APD do tworzenia przepływów pracy opartych na danych na potrzeby organizowania i automatyzowania przenoszenia i przekształcania danych. Działanie **polecenia platformy azure Eksplorator danych** w Azure Data Factory umożliwia uruchamianie [poleceń sterowania Eksplorator danych platformy Azure](/azure/kusto/concepts/#control-commands) w ramach przepływu pracy usługi ADF. W tym artykule przedstawiono sposób tworzenia potoku za pomocą działania Lookup i działania ForEach zawierającego działanie polecenia platformy Azure Eksplorator danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* [Klaster Eksplorator danych i baza danych platformy Azure](create-cluster-database-portal.md)
* Źródło danych.
* [Fabryka danych](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Tworzenie nowego potoku

1. Wybierz narzędzie **autor** ołówka. 
1. Utwórz nowy potok, wybierając **+** , a następnie wybierz pozycję **potok** z listy rozwijanej.

   ![Utwórz nowy potok](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Tworzenie działania wyszukiwania

[Działanie Lookup](/azure/data-factory/control-flow-lookup-activity) może pobrać zestaw danych ze wszystkich obsługiwanych przez Azure Data Factory źródeł danych. Dane wyjściowe działania Lookup mogą być używane w działaniu ForEach lub innym.

1. W okienku **działania** w obszarze **Ogólne**wybierz działanie **Wyszukiwanie** . Przeciągnij i upuść je do głównej kanwy po prawej stronie.
 
    ![Wybierz działanie Lookup](media/data-factory-command-activity/select-activity.png)

1. Kanwa zawiera teraz utworzone działanie Lookup. Użyj kart poniżej kanwy, aby zmienić wszystkie odpowiednie parametry. **Ogólnie**rzecz biorąc, Zmień nazwę działania. 

    ![Edytuj działanie Lookup](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Kliknij pusty obszar kanwy, aby wyświetlić właściwości potoku. Użyj karty **Ogólne** , aby zmienić nazwę potoku. Nasz potok nosi nazwę *potok-4-docs*.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Tworzenie zestawu danych Eksplorator danych platformy Azure w działaniu Lookup

1. W obszarze **Ustawienia**Wybierz wstępnie utworzony **źródłowy zestaw danych**platformy Azure Eksplorator danych lub wybierz pozycję **+ Nowy** , aby utworzyć nowy zestaw danych.
 
    ![Dodaj zestaw danych w ustawieniach wyszukiwania](media/data-factory-command-activity/lookup-settings.png)

1. Wybierz zestaw danych **Eksplorator danych platformy Azure (Kusto)** z okna **nowego zestawu danych** . Wybierz pozycję **Kontynuuj** , aby dodać nowy zestaw danych.

   ![Wybierz nowy zestaw danych](media/data-factory-command-activity/select-new-dataset.png) 

1. Nowe parametry zestawu danych usługi Azure Eksplorator danych są widoczne w **ustawieniach**. Aby zaktualizować parametry, wybierz pozycję **Edytuj**.

    ![Ustawienia wyszukiwania za pomocą zestawu danych usługi Azure Eksplorator danych](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. Na głównej kanwie zostanie otwarta nowa karta **AzureDataExplorerTable** . 
    * Wybierz pozycję **Ogólne** i Edytuj nazwę zestawu danych. 
    * Wybierz pozycję **połączenie** , aby edytować właściwości zestawu danych. 
    * Wybierz **połączoną usługę** z listy rozwijanej lub wybierz pozycję **+ Nowa** , aby utworzyć nową połączoną usługę.

    ![Edytuj właściwości zestawu danych Eksplorator danych platformy Azure](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Podczas tworzenia nowej połączonej usługi zostanie otwarta strona **Nowa połączona usługa (Azure Eksplorator danych)** :

    ![ADX nową połączoną usługę](media/data-factory-command-activity/adx-new-linked-service.png)

   * Wybierz **nazwę** połączonej usługi Azure Eksplorator danych. W razie konieczności Dodaj **Opis** .
   * W obszarze **Połącz za pośrednictwem środowiska Integration Runtime**Zmień bieżące ustawienia, jeśli jest to potrzebne. 
   * W obszarze **Metoda wyboru konta** wybierz klaster przy użyciu jednej z dwóch metod: 
        * Wybierz przycisk radiowy **z subskrypcji platformy Azure** i wybierz konto **subskrypcji platformy Azure** . Następnie wybierz **klaster**. Należy zauważyć, że lista rozwijana będzie dotyczyć tylko klastrów należących do użytkownika.
        * Zamiast tego wybierz pozycję **wprowadź ręcznie** przycisk radiowy, a następnie wprowadź **punkt końcowy** (adres URL klastra).
    * Określ **dzierżawcę**.
    * Wprowadź **Identyfikator jednostki usługi**. Identyfikator podmiotu zabezpieczeń musi mieć odpowiednie uprawnienia, zgodnie z poziomem uprawnień wymaganym przez używane polecenie.
    * Wybierz przycisk **klucz jednostki usługi** i wprowadź **klucz jednostki usługi**.
    * Wybierz **bazę danych** z menu rozwijanego. Alternatywnie wybierz opcję **Edytuj** pole wyboru i wprowadź nazwę bazy danych.
    * Wybierz pozycję **Testuj połączenie** , aby przetestować utworzone połączenie połączonej usługi. Jeśli można nawiązać połączenie z instalatorem, zostanie wyświetlone zielonego **znacznika wyboru.**
    * Wybierz pozycję **Zakończ** , aby ukończyć tworzenie połączonej usługi.

1. Po skonfigurowaniu połączonej usługi w **AzureDataExplorerTable** > **połączenie**należy dodać nazwę **tabeli** . Wybierz pozycję **Podgląd danych**, aby upewnić się, że dane są poprawnie wyświetlane.

   Zestaw danych jest teraz gotowy i możesz kontynuować edytowanie potoku.

### <a name="add-a-query-to-your-lookup-activity"></a>Dodawanie zapytania do działania wyszukiwania

1. W obszarze **potok-4-docs** > **Ustawienia** Dodaj zapytanie w polu tekstowym **zapytania** , na przykład:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. W razie konieczności zmień właściwości **limit czasu zapytania** lub **bez obcinania** i **pierwszego wiersza** . W tym przepływie utrzymujemy domyślny **limit czasu zapytania** i usuń zaznaczenie pól wyboru. 

    ![Ustawienia końcowe działania Lookup](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Utwórz działanie for-each 

Działanie [for-each](/azure/data-factory/control-flow-for-each-activity) służy do iterowania kolekcji i wykonywania określonych działań w pętli. 

1. Teraz dodasz działanie for-each do potoku. To działanie będzie przetwarzać dane zwrócone przez działanie wyszukiwania. 
    * W okienku **działania** w obszarze **iteracja & Conditional**wybierz działanie **foreach** , a następnie przeciągnij i upuść je na kanwie.
    * Narysuj linię między wyjściem działania Lookup i wejściem działania ForEach na kanwie, aby je połączyć.

        ![Działanie ForEach](media/data-factory-command-activity/for-each-activity.png)

1.  Wybierz działanie ForEach na kanwie. Na karcie **Ustawienia** poniżej:
    * Zaznacz pole wyboru **sekwencyjnego** , aby przeprowadzić sekwencyjne przetwarzanie wyników wyszukiwania, lub pozostaw niezaznaczone, aby utworzyć równoległe przetwarzanie.
    * Ustaw **liczbę partii**.
    * W obszarze **elementy**podaj następujące odwołanie do wartości wyjściowej: *@activity ("Lookup1"). Output. Value*

       ![Ustawienia działania ForEach](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Tworzenie działania polecenia platformy Azure Eksplorator danych w ramach działania ForEach

1. Kliknij dwukrotnie działanie ForEach na kanwie, aby otworzyć je na nowej kanwie, aby określić działania w ciągu ForEach.
1. W okienku **działania** w obszarze **Azure Eksplorator danych**wybierz działanie **polecenie Azure Eksplorator danych** , a następnie przeciągnij i upuść je na kanwie.

    ![Działanie polecenia Eksplorator danych platformy Azure](media/data-factory-command-activity/adx-command-activity.png)

1.  Na karcie **połączenie** wybierz wcześniej utworzoną usługę połączoną.

    ![Karta połączenie działania z poleceniem Azure Data Explorer](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. Na karcie **polecenie** podaj następujące polecenie:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    **Polecenie** instruuje usługę Azure Eksplorator danych, aby wyeksportować wyniki danego zapytania do magazynu obiektów BLOB w formacie skompresowanym. Działa asynchronicznie (przy użyciu modyfikatora asynchronicznego).
    Zapytanie odnosi się do kolumny bazy danych każdego wiersza w wyniku działania Lookup. **Limit czasu polecenia** można pozostawić bez zmian.

    ![działanie polecenia](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > Działanie polecenia ma następujące limity:
    > * Limit rozmiaru: 1 MB, rozmiar odpowiedzi
    > * Limit czasu: 20 minut (wartość domyślna), 1 godzina (maksymalna).
    > * W razie potrzeby można dołączyć zapytanie do wyniku przy użyciu [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands), aby zmniejszyć wynikowy rozmiar/czas.

1.  Teraz potok jest gotowy. Możesz wrócić do widoku głównego potoku, klikając nazwę potoku.

    ![Potok poleceń Eksplorator danych platformy Azure](media/data-factory-command-activity/adx-command-pipeline.png)

1. Wybierz opcję **Debuguj** przed opublikowaniem potoku. Postęp potoku można monitorować na karcie **dane wyjściowe** .

    ![dane wyjściowe działania dotyczącego polecenia Eksploratora danych platformy Azure](media/data-factory-command-activity/command-activity-output.png)

1. Można **opublikować wszystko** , a następnie **dodać wyzwalacz** do uruchomienia potoku. 

## <a name="control-command-outputs"></a>Dane wyjściowe polecenia sterowania

Struktura danych wyjściowych działania polecenia jest szczegółowo opisana poniżej. Dane wyjściowe mogą być używane przez następne działanie w potoku.

### <a name="returned-value-of-a-non-async-control-command"></a>Zwrócona wartość polecenia sterowania nieasynchronicznego

W nieasynchronicznym poleceniu sterowania struktura zwracanej wartości jest podobna do struktury wyniku działania Lookup. Pole `count` wskazuje liczbę zwróconych rekordów. Pole stałej tablicy `value` zawiera listę rekordów. 

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
 
### <a name="returned-value-of-an-async-control-command"></a>Zwrócona wartość polecenia sterowania asynchronicznego

W przypadku polecenia sterowania asynchronicznego działanie sonduje tabelę operacji w tle, dopóki nie zostanie ukończona operacja asynchroniczna lub upłynie limit czasu. W związku z tym zwrócona wartość będzie zawierać wynik `.show operations OperationId` dla danej właściwości **OperationId** . Sprawdź wartości właściwości **stan** i **stan** , aby sprawdzić pomyślne zakończenie operacji.

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

* Dowiedz się więcej na temat sposobu [kopiowania danych do usługi Azure Eksplorator danych przy użyciu Azure Data Factory](data-factory-load-data.md).
* Dowiedz się więcej o używaniu [szablonu Azure Data Factory do kopiowania zbiorczego z bazy danych na platformę Azure Eksplorator danych](data-factory-template.md).
