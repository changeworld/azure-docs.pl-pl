---
title: Pozyskiwanie danych z centrum zdarzeń do usługi Azure Eksplorator danych
description: W tym artykule dowiesz się, jak pozyskiwanie (ładować) danych do usługi Azure Eksplorator danych z centrum zdarzeń.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: a0568d3c3258fe082fe8451820fe7a25390cfe78
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996770"
---
# <a name="ingest-data-from-event-hub-into-azure-data-explorer"></a>Pozyskiwanie danych z centrum zdarzeń do usługi Azure Eksplorator danych

Azure Eksplorator danych to szybka i wysoce skalowalna usługa eksploracji danych dla danych dzienników i telemetrii. Usługa Azure Eksplorator danych zapewnia pozyskiwanie (ładowanie danych) z Event Hubs, usługi przesyłania strumieniowego danych Big Data i usług pozyskiwania zdarzeń. [Event Hubs](/azure/event-hubs/event-hubs-about) może przetwarzać miliony zdarzeń na sekundę niemal w czasie rzeczywistym. W tym artykule opisano tworzenie centrum zdarzeń, nawiązywanie z nim połączenia z usługi Azure Eksplorator danych i wyświetlanie przepływu danych przez system.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

* [Klaster testowy i baza danych](create-cluster-database-portal.md).

* [Przykładowa aplikacja](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) , która generuje dane i wysyła je do centrum zdarzeń. Pobierz przykładową aplikację do systemu.

* [Program Visual Studio 2019](https://visualstudio.microsoft.com/vs/) do uruchamiania przykładowej aplikacji.

## <a name="sign-in-to-the-azure-portal"></a>Zaloguj się do Azure Portal

Zaloguj się do [Azure Portal](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

W tym artykule opisano generowanie przykładowych danych i wysyłanie ich do centrum zdarzeń. Pierwszym krokiem jest utworzenie centrum zdarzeń. W tym celu należy użyć szablonu Azure Resource Manager w Azure Portal.

1. Aby utworzyć centrum zdarzeń, użyj następującego przycisku, aby rozpocząć wdrażanie. Kliknij prawym przyciskiem myszy i wybierz polecenie **Otwórz w nowym oknie**, aby móc wykonać pozostałe kroki opisane w tym artykule.

    [@no__t — 1Deploy na platformę Azure](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    Przycisk **Wdróż na platformie Azure** przenosi do Azure Portal, aby wypełnić formularz wdrożenia.

    ![Wdrażanie na platformie Azure](media/ingest-data-event-hub/deploy-to-azure.png)

1. Wybierz subskrypcję, w której chcesz utworzyć centrum zdarzeń, a następnie utwórz grupę zasobów o nazwie *test-Hub-RG*.

    ![Tworzenie grupy zasobów](media/ingest-data-event-hub/create-resource-group.png)

1. Wypełnij formularz poniższymi informacjami.

    ![Formularz wdrożenia](media/ingest-data-event-hub/deployment-form.png)

    Użyj wartości domyślnych dla wszystkich ustawień, które nie są wymienione w poniższej tabeli.

    **Konfigurowania** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Ramach | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla centrum zdarzeń.|
    | Grupa zasobów | *Test-Hub — RG* | Utwórz nową grupę zasobów. |
    | Lokalizacja | *Zachodnie stany USA* | W tym artykule wybierz pozycję *zachodnie stany USA* . W przypadku systemu produkcyjnego wybierz region, który najlepiej odpowiada Twoim potrzebom. Utwórz przestrzeń nazw centrum zdarzeń w tej samej lokalizacji co klaster Kusto, aby uzyskać najlepszą wydajność (najważniejsze dla przestrzeni nazw centrum zdarzeń o dużej przepływności).
    | Nazwa przestrzeni nazw | Unikatowa nazwa przestrzeni nazw | Wybierz unikatową nazwę identyfikującą przestrzeń nazw. Na przykład *mytestnamespace*. Nazwa domeny *ServiceBus.Windows.NET* jest dołączana do podania nazwy. Nazwa może zawierać tylko litery, cyfry i łączniki. Nazwa musi rozpoczynać się od litery i musi kończyć się literą lub cyfrą. Wartość musi mieć długość od 6 do 50 znaków.
    | Nazwa centrum zdarzeń | *Centrum testów* | Centrum zdarzeń znajduje się pod przestrzenią nazw, która zapewnia unikatowy kontener określania zakresu. Nazwa centrum zdarzeń musi być unikatowa w przestrzeni nazw. |
    | Nazwa grupy odbiorców | *Grupa testów* | Grupy konsumentów umożliwiają wielu aplikacjom korzystającym z osobnego widoku strumienia zdarzeń. |
    | | |

1. Wybierz pozycję **Kup**, która potwierdza, że tworzysz zasoby w ramach subskrypcji.

1. Wybierz pozycję **powiadomienia** na pasku narzędzi, aby monitorować proces aprowizacji. Wdrożenie może potrwać kilka minut, ale teraz możesz przejść do następnego kroku.

    ![Powiadomienia](media/ingest-data-event-hub/notifications.png)

## <a name="create-a-target-table-in-azure-data-explorer"></a>Tworzenie tabeli docelowej na platformie Azure Eksplorator danych

Teraz utworzysz tabelę na platformie Azure Eksplorator danych, do której Event Hubs będą wysyłać dane. Należy utworzyć tabelę w klastrze i w bazie danych, która została zainicjowana w **wymaganiach wstępnych**.

1. W Azure Portal przejdź do klastra, a następnie wybierz pozycję **zapytanie**.

    ![Link do aplikacji zapytania](media/ingest-data-event-hub/query-explorer-link.png)

1. Skopiuj następujące polecenie do okna i wybierz polecenie **Uruchom** , aby utworzyć tabelę (Tester), która będzie odbierać dane pozyskiwane.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Uruchom Tworzenie zapytania](media/ingest-data-event-hub/run-create-query.png)

1. Skopiuj następujące polecenie do okna i wybierz pozycję **Uruchom** , aby zmapować przychodzące dane JSON do nazw kolumn i typów danych tabeli (testów).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

## <a name="connect-to-the-event-hub"></a>Nawiązywanie połączenia z centrum zdarzeń

Teraz nawiążesz połączenie z centrum zdarzeń z usługi Azure Eksplorator danych. Gdy to połączenie jest stosowane, dane przesyłane do usługi Event Hub strumienie do tabeli testów utworzonej wcześniej w tym artykule.

1. Wybierz pozycję **powiadomienia** na pasku narzędzi, aby sprawdzić, czy wdrożenie centrum zdarzeń zakończyło się pomyślnie.

1. W obszarze utworzonego klastra wybierz pozycję **bazy danych** , a następnie **TestDatabase**.

    ![Wybierz testową bazę danych](media/ingest-data-event-hub/select-test-database.png)

1. Wybierz pozycję pozyskiwanie **danych** i **Dodaj połączenie danych**. Następnie wypełnij formularz poniższymi informacjami. Po zakończeniu wybierz pozycję **Utwórz** .

    ![Połączenie centrum zdarzeń](media/ingest-data-event-hub/event-hub-connection.png)

    Źródło danych:

    **Konfigurowania** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa połączenia danych | *Test-Hub — połączenie* | Nazwa połączenia, które ma zostać utworzone w usłudze Azure Eksplorator danych.|
    | Przestrzeń nazw centrum zdarzeń | Unikatowa nazwa przestrzeni nazw | Wybrana wcześniej nazwa identyfikująca przestrzeń nazw. |
    | Centrum zdarzeń | *Centrum testów* | Utworzone centrum zdarzeń. |
    | Grupa konsumentów | *Grupa testów* | Grupa odbiorców zdefiniowana w utworzonym centrum zdarzeń. |
    | Właściwości systemu zdarzeń | Wybierz odpowiednie właściwości | [Właściwości systemu centrum zdarzeń](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations). Jeśli istnieje wiele rekordów dla każdego komunikatu o zdarzeniu, właściwości systemu zostaną dodane do pierwszej z nich. Podczas dodawania właściwości systemu [Utwórz](/azure/kusto/management/tables#create-table) lub [zaktualizuj](/azure/kusto/management/tables#alter-table-and-alter-merge-table) schemat i [Mapowanie](/azure/kusto/management/mappings) tabeli w celu uwzględnienia wybranych właściwości. |
    | | |

    Tabela docelowa:

    Dostępne są dwie opcje routingu pozyskiwanych danych: *statyczne* i *dynamiczne*. 
    W tym artykule należy używać routingu statycznego, w którym można określić nazwę tabeli, format danych i mapowanie. W związku z tym Zostaw **moje dane zawiera niewybrane informacje o routingu** .

     **Konfigurowania** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | tabela | *Testy* | Tabela utworzona w **TestDatabase**. |
    | Format danych | *KODU* | Obsługiwane formaty to Avro, CSV, JSON, WIELOWIERSZOWY kod JSON, PSV, SOHSV, SCSV, TSV, TSVE i TXT. Obsługiwane opcje kompresji: GZip |
    | Mapowanie kolumn | *TestMapping* | [Mapowanie](/azure/kusto/management/mappings) utworzone w **TestDatabase**, które mapuje przychodzące dane JSON do nazw kolumn i typów **danych.** Wymagane dla formatu JSON, wielowierszowego kodu JSON lub AVRO oraz opcjonalne dla innych formatów.|
    | | |

    > [!NOTE]
    > * Wybierz pozycję **moje dane zawiera informacje o routingu** , aby użyć routingu dynamicznego, gdzie dane zawierają niezbędne informacje dotyczące routingu, jak pokazano w komentarzach [przykładowych aplikacji](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) . Jeśli są ustawione właściwości static i Dynamic, właściwości dynamiczne zastępują statyczne. 
    > * Zostaną pozyskane tylko zdarzenia znajdujące się w kolejce po utworzeniu połączenia danych.

## <a name="copy-the-connection-string"></a>Kopiuj parametry połączenia

Po uruchomieniu [przykładowej aplikacji](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) wymienionej w sekcji wymagania wstępne wymagane są parametry połączenia dla przestrzeni nazw centrum zdarzeń.

1. W obszarze Przestrzeń nazw centrum zdarzeń, które utworzono, wybierz pozycję **zasady dostępu współdzielonego**, a następnie **RootManageSharedAccessKey**.

    ![Zasady dostępu współdzielonego](media/ingest-data-event-hub/shared-access-policies.png)

1. Kopiuj **Parametry połączenia — klucz podstawowy**. Wkleisz ją w następnej sekcji.

    ![Parametry połączenia](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Generowanie danych przykładowych

Użyj pobranej [przykładowej aplikacji](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) , aby wygenerować dane.

1. Otwórz przykładowe rozwiązanie aplikacji w programie Visual Studio.

1. W pliku *program.cs* zaktualizuj stałą `connectionString` do parametrów połączenia, które zostały skopiowane z przestrzeni nazw centrum zdarzeń.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Skompiluj i uruchom aplikację. Aplikacja wysyła komunikaty do centrum zdarzeń i drukuje stan co dziesięć sekund.

1. Gdy aplikacja wyśle kilka komunikatów, przejdź do następnego kroku: przeglądanie przepływu danych w centrum zdarzeń i tabeli testów.

## <a name="review-the-data-flow"></a>Przeglądanie przepływu danych

Za pomocą aplikacji generującej dane można teraz zobaczyć przepływ tych danych z centrum zdarzeń do tabeli w klastrze.

1. W Azure Portal w centrum zdarzeń w trakcie działania aplikacji zobaczysz, jak działa.

    ![Wykres centrum zdarzeń](media/ingest-data-event-hub/event-hub-graph.png)

1. Aby sprawdzić, ile komunikatów zostało wprowadzonych do bazy danych do tej pory, uruchom następujące zapytanie w bazie danych testów.

    ```Kusto
    TestTable
    | count
    ```

1. Aby wyświetlić zawartość komunikatów, uruchom następujące zapytanie:

    ```Kusto
    TestTable
    ```

    Zestaw wyników powinien wyglądać następująco:

    ![Zestaw wyników komunikatów](media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    > * Usługa Azure Eksplorator danych ma zasady agregacji (wsadowe) do pozyskiwania danych, zaprojektowane w celu optymalizacji procesu pozyskiwania. Zasady są domyślnie skonfigurowane do 5 minut lub 500 MB danych, dzięki czemu mogą wystąpić opóźnienia. Zobacz temat [zasady tworzenia wsadowego](/azure/kusto/concepts/batchingpolicy) dla opcji agregacji. 
    > * Pozyskanie centrum zdarzeń obejmuje czas odpowiedzi z centrum zdarzeń wynoszący 10 sekund lub 1 MB. 
    > * Skonfiguruj tabelę do obsługi przesyłania strumieniowego i Usuń opóźnienie w czasie odpowiedzi. Zobacz [zasady przesyłania strumieniowego](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie planujesz ponownie używać centrum zdarzeń, wyczyść polecenie **test-Hub-RG**, aby uniknąć ponoszenia kosztów.

1. W Azure Portal wybierz pozycję **grupy zasobów** po lewej stronie, a następnie wybierz utworzoną grupę zasobów.  

    Jeśli menu po lewej stronie jest zwinięte, wybierz pozycję ![Przycisk rozwijania](media/ingest-data-event-hub/expand.png) , aby je rozwinąć.

   ![Wybierz grupę zasobów do usunięcia](media/ingest-data-event-hub/delete-resources-select.png)

1. W obszarze **test-zasób-Grupa**wybierz pozycję **Usuń grupę zasobów**.

1. W nowym oknie wpisz nazwę grupy zasobów do usunięcia (*test-Hub-RG*), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań dotyczących danych w usłudze Azure Eksplorator danych](web-query-data.md)
