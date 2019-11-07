---
title: Wizualizuj dane z usługi Azure Eksplorator danych przy użyciu Grafana
description: W tym instruktażu dowiesz się, jak skonfigurować Eksplorator danych platformy Azure jako źródło danych dla Grafana, a następnie wizualizować dane z przykładowego klastra.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 6/30/2019
ms.openlocfilehash: f1eb9fb0d81d1e9cdf3dd8628a6d7ad1f0ccce92
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582033"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Wizualizuj dane z usługi Azure Eksplorator danych w Grafana

Grafana to platforma analityczna, która umożliwia wykonywanie zapytań i wizualizacji danych, a następnie Tworzenie i udostępnianie pulpitów nawigacyjnych na podstawie wizualizacji. Grafana zapewnia *wtyczkę*Eksplorator danych platformy Azure, która umożliwia nawiązywanie połączeń z usługą Azure Eksplorator danych i wizualizowanie danych. W tym artykule dowiesz się, jak skonfigurować Eksplorator danych platformy Azure jako źródło danych dla Grafana, a następnie wizualizować dane z przykładowego klastra.

Korzystając z poniższego filmu wideo, możesz dowiedzieć się, jak korzystać z wtyczki Eksplorator danych platformy Azure Grafana, skonfigurować platformę Azure Eksplorator danych jako źródło danych dla Grafana, a następnie wizualizować dane. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Alternatywnie możesz [skonfigurować źródło danych](#configure-the-data-source) i [wizualizować dane](#visualize-data) zgodnie z opisem w artykule poniżej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby dokończyć następujące czynności:

* [Grafana wersja 5.3.0 lub nowszą](https://docs.grafana.org/installation/) dla danego systemu operacyjnego

* [Wtyczka Eksplorator danych platformy Azure dla usługi](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) Grafana

* Klaster zawierający przykładowe dane StormEvents. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie klastra Eksplorator danych platformy Azure i bazy danych](create-cluster-database-portal.md) oraz pozyskiwanie [przykładowych danych do usługi Azure Eksplorator danych](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Określ właściwości i przetestuj połączenie

Przy użyciu jednostki usługi przypisanej do roli *osoby przeglądające* możesz teraz określić właściwości w wystąpieniu Grafana i przetestować połączenie z usługą Azure Eksplorator danych.

1. W Grafana, w menu po lewej stronie wybierz ikonę koła zębatego, a następnie pozycję **źródła danych**.

    ![Źródła danych](media/grafana/data-sources.png)

1. Wybierz pozycję **Dodaj źródło danych**.

1. Na stronie **źródła danych/Nowa** wprowadź nazwę źródła danych, a następnie wybierz typ **Azure Eksplorator danych DataSource**.

    ![Nazwa i typ połączenia](media/grafana/connection-name-type.png)

1. Wprowadź nazwę klastra w postaci https://{ClusterName}. {Region}. Kusto. Windows. NET. Wprowadź inne wartości z Azure Portal lub interfejsu wiersza polecenia. Zapoznaj się z tabelą poniżej poniższej ilustracji.

    ![Connection properties (Właściwości połączenia)](media/grafana/connection-properties.png)

    | Interfejs użytkownika Grafana | Azure Portal | Interfejs wiersza polecenia platformy Azure |
    | --- | --- | --- |
    | Identyfikator subskrypcji | IDENTYFIKATOR SUBSKRYPCJI | SubscriptionId |
    | Identyfikator dzierżawy | Identyfikator katalogu | dzierżaw |
    | Identyfikator klienta | Identyfikator aplikacji | appId |
    | Klucz tajny klienta | Hasło | hasło |
    | | | |

1. Wybierz pozycję **zapisz & test**.

    Jeśli test zakończy się pomyślnie, przejdź do następnej sekcji. Jeśli napotkasz jakiekolwiek problemy, sprawdź wartości określone w Grafana i Przejrzyj poprzednie kroki.

## <a name="visualize-data"></a>Wizualizowanie danych

Po skonfigurowaniu usługi Azure Eksplorator danych jako źródła danych dla Grafana czas na wizualizację danych. Pokażemy tutaj podstawowy przykład, ale istnieje dużo więcej możliwości. Zaleca się, aby podczas wykonywania [zapytań dotyczących zapisu na platformie Azure Eksplorator danych](write-queries.md) na przykład inne zapytania, które zostaną uruchomione względem przykładowego zestawu danych.

1. W Grafana, w menu po lewej stronie wybierz ikonę znaku plus, a następnie pozycję **pulpit nawigacyjny**.

    ![Utwórz pulpit nawigacyjny](media/grafana/create-dashboard.png)

1. Na karcie **Dodaj** wybierz pozycję **Graph**.

    ![Dodaj wykres](media/grafana/add-graph.png)

1. W panelu Graf wybierz pozycję **Tytuł panelu** , a następnie **Edytuj**.

    ![Panel Edytuj](media/grafana/edit-panel.png)

1. W dolnej części panelu wybierz pozycję **Źródło danych** , a następnie wybierz skonfigurowane źródło danych.

    ![Wybieranie źródła danych](media/grafana/select-data-source.png)

1. W okienku zapytania Skopiuj poniższe zapytanie, a następnie wybierz pozycję **Uruchom**. Zapytanie przedziałuje liczbę zdarzeń dziennie dla przykładowego zestawu danych.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Uruchamianie zapytania](media/grafana/run-query.png)

1. Wykres nie pokazuje żadnych wyników, ponieważ jest domyślnie objęty zakresem danych z ostatnich sześciu godzin. W górnym menu wybierz pozycję **ostatnie 6 godzin**.

    ![Ostatnie sześć godzin](media/grafana/last-six-hours.png)

1. Określ zakres niestandardowy obejmujący 2007, rok uwzględniony w zestawie danych przykładowych StormEvents. Wybierz przycisk **Zastosuj**.

    ![Niestandardowy zakres dat](media/grafana/custom-date-range.png)

    Teraz wykres pokazuje dane z 2007, przedzielonych na dobę.

    ![Ukończony wykres](media/grafana/finished-graph.png)

1. W górnym menu wybierz ikonę Zapisz: ![Ikona zapisywania](media/grafana/save-icon.png).

## <a name="next-steps"></a>Następne kroki

* [Pisanie zapytań dla usługi Azure Data Explorer](write-queries.md)

* [Samouczek: Wizualizacja danych z platformy Azure Eksplorator danych w Power BI](visualize-power-bi.md)
