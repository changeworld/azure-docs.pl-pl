---
title: Wizualizuj dane z Eksploratora danych platformy Azure przy użyciu grawany
description: W tym artykule nauczysz się konfigurowania usługi Azure Data Explorer jako źródła danych dla grawany, a następnie wizualizować dane z przykładowego klastra.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038008"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Wizualizuj dane z Eksploratora danych platformy Azure w Grafanie

Grafana to platforma analityczna, która umożliwia wykonywanie zapytań i wizualizacji danych, a następnie tworzenie i udostępnianie pulpitów nawigacyjnych na podstawie wizualizacji. Grafana udostępnia *wtyczkę*Usługi Azure Data Explorer, która umożliwia łączenie się i wizualizowanie danych z Eksploratora danych platformy Azure. W tym artykule nauczysz się konfigurowania usługi Azure Data Explorer jako źródła danych dla grawany, a następnie wizualizować dane z przykładowego klastra.

Skorzystaj z poniższego klipu wideo, aby dowiedzieć się, jak korzystać z wtyczki Usługi AzureExlor explorer firmy Grafana, skonfigurować Eksploratora danych platformy Azure jako źródło danych dla graginany, a następnie wizualizować dane. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Alternatywnie można [skonfigurować źródło danych](#configure-the-data-source) i [wizualizować dane,](#visualize-data) jak opisano w poniższym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, potrzebujesz następujących czynności:

* [Grafana w wersji 5.3.0 lub nowszej](https://docs.grafana.org/installation/) dla systemu operacyjnego

* [Wtyczka Usługi Azure Data Explorer](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) dla grawany

* Klaster zawierający przykładowe dane StormEvents. Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie klastra i bazy danych usługi Azure Data Explorer](create-cluster-database-portal.md) oraz pozyskiwania [przykładowych danych do Eksploratora danych platformy Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Określanie właściwości i testowanie połączenia

Za pomocą jednostki usługi przypisanej do roli *przeglądarki* można teraz określić właściwości w wystąpieniu grawany i przetestować połączenie z Eksploratorem danych platformy Azure.

1. W Grafanie po lewej stronie menu wybierz ikonę koła zębatego, a następnie **pozycję Źródła danych**.

    ![Źródła danych](media/grafana/data-sources.png)

1. Wybierz **pozycję Dodaj źródło danych**.

1. Na stronie **Źródła danych / Nowe** wprowadź nazwę źródła danych, a następnie wybierz typ Usługi Azure Data Explorer **Datasource**.

    ![Nazwa i typ połączenia](media/grafana/connection-name-type.png)

1. Wprowadź nazwę klastra w formularzu https://{ClusterName}. {Region}.kusto.windows.net. Wprowadź inne wartości z witryny Azure portal lub interfejsu wiersza polecenia. Zobacz tabelę poniżej poniższej ilustracji do mapowania.

    ![Connection properties (Właściwości połączenia)](media/grafana/connection-properties.png)

    | Interfejs użytkownika Grafana | Portal Azure | Interfejs wiersza polecenia platformy Azure |
    | --- | --- | --- |
    | Identyfikator subskrypcji | IDENTYFIKATOR SUBSKRYPCJI | SubscriptionId |
    | Identyfikator dzierżawy | Identyfikator katalogu | Dzierżawy |
    | Identyfikator klienta | Identyfikator aplikacji | appId |
    | Klucz tajny klienta | Hasło | hasło |
    | | | |

1. Wybierz **opcję Zapisz & Test**.

    Jeśli test zakończy się pomyślnie, przejdź do następnej sekcji. Jeśli natkniesz się na jakiekolwiek problemy, sprawdź wartości określone w Grafanie i przejrzyj poprzednie kroki.

## <a name="visualize-data"></a>Wizualizowanie danych

Teraz skończyłeś konfigurację Usługi Azure Data Explorer jako źródła danych dla grawany, nadszedł czas, aby wizualizować dane. Pokażemy tutaj podstawowy przykład, ale możesz zrobić o wiele więcej. Firma Microsoft zaleca [przeglądanie napisz kwerendy dla Usługi Azure Data Explorer](write-queries.md) dla przykładów innych zapytań do uruchomienia względem przykładowego zestawu danych.

1. W Grafanie po lewej stronie menu wybierz ikonę plus, a następnie **pozycję Dashboard**.

    ![Tworzenie pulpitu nawigacyjnego](media/grafana/create-dashboard.png)

1. W obszarze karty **Dodawanie** wybierz pozycję **Wykres**.

    ![Dodawanie wykresu](media/grafana/add-graph.png)

1. Na panelu wykresu wybierz pozycję **Tytuł panelu,** a następnie **edytuj**.

    ![Panel Edytuj](media/grafana/edit-panel.png)

1. U dołu panelu wybierz pozycję **Źródło danych,** a następnie wybierz skonfigurowane źródło danych.

    ![Wybieranie źródła danych](media/grafana/select-data-source.png)

1. W okienku kwerendy skopiuj w następnej kwerendzie, a następnie wybierz polecenie **Uruchom**. Wy zestawienia kwerendy liczba zdarzeń według dnia dla przykładowego zestawu danych.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Uruchamianie zapytania](media/grafana/run-query.png)

1. Wykres nie pokazuje żadnych wyników, ponieważ jest domyślnie zakres danych z ostatnich sześciu godzin. W górnym menu wybierz **pozycję Ostatnie 6 godzin**.

    ![Ostatnie sześć godzin](media/grafana/last-six-hours.png)

1. Określ niestandardowy zakres, który obejmuje rok 2007, rok uwzględniony w naszym przykładowym zestawie danych StormEvents. Wybierz przycisk **Zastosuj**.

    ![Niestandardowy zakres dat](media/grafana/custom-date-range.png)

    Teraz wykres pokazuje dane z 2007 r., wiadro w dzień.

    ![Gotowy wykres](media/grafana/finished-graph.png)

1. W górnym menu wybierz ikonę zapisz: ![Ikona zapisywania](media/grafana/save-icon.png).

## <a name="create-alerts"></a>Tworzenie alertów

1. Na pulpicie nawigacyjnym narzędzia do domu wybierz**pozycję Kanały powiadomień alertów,** **Alerting** > aby utworzyć nowy kanał powiadomień

    ![tworzenie kanału powiadomień](media/grafana/create-notification-channel.png)

1. Utwórz nowy **kanał powiadomień**, a następnie **Zapisz**.

    ![Tworzenie nowego kanału powiadomień](media/grafana/new-notification-channel-adx.png)

1. Na **pulpicie nawigacyjnym**wybierz pozycję **Edytuj** z listy rozwijanej.

    ![wybieranie edycji na pulpicie nawigacyjnym](media/grafana/edit-panel-4-alert.png)

1. Wybierz ikonę dzwonka alertu, aby otworzyć okienko **alertów.** Wybierz **pozycję Utwórz alert**. Wykonaj następujące właściwości w okienku **Alert.**

    ![właściwości alertu](media/grafana/alert-properties.png)

1. Wybierz ikonę **Zapisz pulpit nawigacyjny,** aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki

* [Pisanie zapytań dla Eksploratora danych platformy Azure](write-queries.md)
