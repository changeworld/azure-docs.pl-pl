---
title: Wizualizuj Eksploratora danych platformy Azure za pomocą redash
description: W tym artykule dowiesz się, jak wizualizować dane w Eksploratorze danych platformy Azure za pomocą łącznika natywnego Redash.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773960"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Wizualizuj dane z eksploratora danych platformy Azure w redash

[Redash](https://redash.io/) łączy i wysyła zapytania do źródeł danych, tworzy pulpity nawigacyjne w celu wizualizacji danych i udostępniania ich partnerom równorzędnym. W tym artykule dowiesz się, jak skonfigurować Eksploratora danych platformy Azure jako źródło danych dla redash, a następnie wizualizować dane.

## <a name="prerequisites"></a>Wymagania wstępne

1. [Tworzenie klastra i bazy danych](create-cluster-database-portal.md).
1. Połknąć dane, jak wyjaśniono w [pozyskiwania przykładowych danych do Usługi Azure Data Explorer](ingest-sample-data.md). Aby uzyskać więcej opcji pozyskiwania, zobacz [przegląd pozyskiwania](ingest-data-overview.md).

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Tworzenie łącznika eksploratora danych platformy Azure w redash 

1. Zaloguj się do [Redash](https://www.redash.io/). Wybierz **pozycję Wprowadzenie,** aby utworzyć konto.
1. W obszarze **Rozpocznijmy wprowadzenie**wybierz pozycję **Połącz źródło danych**.

    ![Łączenie ze źródłem danych](media/redash/connect-data-source.png)

1. W oknie **Utwórz nowe źródło danych** wybierz pozycję **Azure Data Explorer (Kusto),** a następnie wybierz pozycję **Utwórz**. 

    ![Wybieranie źródła danych Usługi Azure Data Explorer](media/redash/select-adx-data-source.png)

1. W oknie **Eksploratora danych platformy Azure (Kusto)** wypełnij następujący formularz i wybierz pozycję **Utwórz**.

    ![Okno ustawień eksploratora danych platformy Azure (Kusto)](media/redash/adx-settings-window.png)

1. W oknie **Ustawienia** wybierz pozycję **Zapisz** i **przetestuj połączenie,** aby przetestować połączenie ze źródłem danych **usługi Azure Data Explorer (Kusto).**

## <a name="create-queries-in-redash"></a>Tworzenie zapytań w redash

1. W lewym górnym rogu opcji Redash wybierz pozycję **Utwórz** > **kwerendę**. Kliknij **pozycję Nowa kwerenda** i zmień nazwę kwerendy.

    ![Utwórz kwerendę](media/redash/create-query.png)

1. Wpisz zapytanie w górnym okienku edycji i wybierz pozycję **Zapisz** i **wykonaj**. Wybierz **pozycję Publikuj,** aby opublikować kwerendę do wykorzystania w przyszłości.

    ![Zapisywanie i wykonywanie kwerendy](media/redash/save-and-execute-query.png)

    W lewym okienku można zobaczyć nazwę połączenia źródła danych **(łącznik Github** w naszym przepływie) w menu rozwijanym i tabele w wybranej bazie danych. 

1. Wyświetlanie wyników kwerendy w dolnym centralnym okienku. Utwórz wizualizację, aby przejść do kwerendy, wybierając przycisk **Nowa wizualizacja.**

    ![Nowa wizualizacja](media/redash/new-visualization.png)

1. Na ekranie wizualizacji wybierz **typ wizualizacji** i odpowiednie pola, takie jak **Kolumna X** i **Kolumna Y**. **Zapisz** wizualizację.

    ![Konfigurowanie i zapisywanie wizualizacji](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Tworzenie kwerendy przy użyciu parametru

1. **Utwórz** > **kwerendę,** aby utworzyć nową kwerendę. Dodaj do niego parametr{}za pomocą { } nawiasów klamrowych. Wybierz **{}{ },** aby otworzyć okno **Dodaj parametr.** Można również wybrać *ikonę ustawień,* aby zmodyfikować atrybuty istniejącego parametru i otworzyć **okno<parameter_name parameter_name>.** 

    ![parametr insert](media/redash/insert-parameter.png)

1. Nazwij swój parametr. Wybierz **typ:** **Lista rozwijana oparta na kwerendach** z menu rozwijanego. Wybierz **przycisk OK**

    ![lista rozwijana oparta na kwerendach](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > Kwerenda używa wielu wartości, dlatego należy dołączyć następującą składnię `| where Type in ((split('{{Type}}', ',')))`. Aby uzyskać więcej informacji, zobacz [operator](/azure/kusto/query/inoperator). Powoduje to [wiele opcji parametrów kwerendy w aplikacji redash](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>Tworzenie pulpitu nawigacyjnego w aplikacji Redash

1. Aby utworzyć pulpit nawigacyjny, **utwórz** > **pulpit nawigacyjny**. Alternatywnie wybierz istniejący pulpit nawigacyjny, **pulpity nawigacyjne** > wybierz pulpit nawigacyjny z listy.

    ![Tworzenie pulpitu nawigacyjnego](media/redash/create-dashboard.png)

1. W oknie **Nowy pulpit nawigacyjny** nazwij pulpit nawigacyjny i wybierz pozycję **Zapisz**. W **<Dashboard_name>** okna wybierz pozycję **Dodaj widżet,** aby utworzyć nowy widżet. 

1. W oknie **Dodaj widżet** wybierz nazwę kwerendy, **wybierz wizualizację**i **Parametry**. Wybierz **pozycję Dodaj do pulpitu nawigacyjnego**

   ![Wybieranie wizualizacji i dodawanie do pulpitu nawigacyjnego](media/redash/add-widget-window.png)

1. Wybierz **pozycję Gotowe edytowanie,** aby zakończyć tworzenie pulpitu nawigacyjnego.

1.  W trybie edycji pulpitu nawigacyjnego wybierz pozycję **Użyj filtrów poziomu pulpitu nawigacyjnego,** aby użyć wcześniej zdefiniowanego parametru **Type.**

    ![Pełne tworzenie pulpitu nawigacyjnego](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Następne kroki

* [Pisanie zapytań dla Eksploratora danych platformy Azure](write-queries.md)


