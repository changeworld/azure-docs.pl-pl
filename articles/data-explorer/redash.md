---
title: Wizualizuj Eksplorator danych platformy Azure za pomocą rozłącznika
description: W tym artykule dowiesz się, jak wizualizować dane w usłudze Azure Eksplorator danych przy użyciu natywnego łącznika rekreskowanego.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8bb8711bc4a6134ec740a55d9f5d5794b2de77ca
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588592"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Wizualizuj dane z usługi Azure Eksplorator danych w odkreskowaniu

Polecenie [rekreska](https://www.redash.io/) łączy się ze źródłami danych i wysyła do nich zapytania, kompiluje pulpity nawigacyjne, aby wizualizować dane i udostępniać je równorzędnym. W tym artykule dowiesz się, jak skonfigurować Eksplorator danych platformy Azure jako źródło danych dla połącznika, a następnie wizualizować dane.

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz klaster i bazę danych](create-cluster-database-portal.md).
1. Pozyskiwanie danych w sposób wyjaśniony w pozyskaniu [przykładowych danych do usługi Azure Eksplorator danych](ingest-sample-data.md). Aby uzyskać więcej opcji pozyskiwania, zobacz [Omówienie](ingest-data-overview.md)pozyskiwania.

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Utwórz łącznik usługi Azure Eksplorator danych w odkreskowaniu 

1. Zaloguj się w celu [rozłącznika](https://www.redash.io/). Wybierz pozycję **Rozpocznij** , aby utworzyć konto.
1. W obszarze Zacznijmy **pracę**wybierz pozycję **Połącz źródło danych**.

    ![Łączenie ze źródłem danych](media/redash/connect-data-source.png)

1. W oknie **Tworzenie nowego źródła danych** wybierz pozycję **Azure Eksplorator danych (Kusto)** , a następnie wybierz pozycję **Utwórz**. 

    ![Wybierz pozycję Azure Eksplorator danych Data Source](media/redash/select-adx-data-source.png)

1. W oknie **Azure Eksplorator danych (Kusto)** Wypełnij poniższe formularz i wybierz pozycję **Utwórz**.

    ![Okno ustawień usługi Azure Eksplorator danych (Kusto)](media/redash/adx-settings-window.png)

1. W oknie **Ustawienia** wybierz pozycję **Zapisz** i **Testuj połączenie** , aby przetestować połączenie ze źródłem danych **platformy Azure Eksplorator danych (Kusto)** .

## <a name="create-queries-in-redash"></a>Utwórz zapytania w odkreskowaniu

1. W lewym górnym rogu ekranu wybierz pozycję **utwórz** > **zapytanie**. Kliknij pozycję **nowe zapytanie** i Zmień nazwę zapytania.

    ![Utwórz zapytanie](media/redash/create-query.png)

1. Wpisz zapytanie w górnym okienku edycji, a następnie wybierz pozycję **Zapisz** i **Wykonaj**. Wybierz pozycję **Opublikuj** , aby opublikować zapytanie do użycia w przyszłości.

    ![Zapisz i wykonaj zapytanie](media/redash/save-and-execute-query.png)

    W lewym okienku można zobaczyć nazwę połączenia ze źródłem danych (**Łącznik usługi GitHub** w naszym przepływie) w menu rozwijanym i tabele w wybranej bazie danych. 

1. Wyświetl wyniki zapytania w dolnym okienku środkowym. Utwórz wizualizację, aby przejść do zapytania, wybierając przycisk **Nowa Wizualizacja** .

    ![Nowa Wizualizacja](media/redash/new-visualization.png)

1. Na ekranie Wizualizacja wybierz **typ wizualizacji** i odpowiednie pola, takie jak kolumna **X** i **kolumna Y**. **Zapisz** wizualizację.

    ![Konfigurowanie i zapisywanie wizualizacji](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Tworzenie zapytania przy użyciu parametru

1. **Utwórz** **kwerendę** > , aby utworzyć nowe zapytanie. Dodaj do niego parametr przy użyciu nawiasów klamrowych {{}}. Wybierz pozycję **{{}}** , aby otworzyć okno **Dodawanie parametrów** . Możesz również wybrać *ikonę Ustawienia* , aby zmodyfikować atrybuty istniejącego parametru i otworzyć okno **< parameter_name >** . 

    ![Wstaw parametr](media/redash/insert-parameter.png)

1. Nazwij parametr. Wybierz **Typ**: **Lista rozwijana oparta na zapytaniu** z menu rozwijanego. Kliknij przycisk **OK**

    ![Lista rozwijana oparta na zapytaniu](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > Zapytanie używa wielu wartości, dlatego należy uwzględnić następującą składnię `| where Type in ((split('{{Type}}', ',')))`. Aby uzyskać więcej informacji, zobacz [w operatorze](/azure/kusto/query/inoperator). Powoduje to [wiele opcji parametrów zapytania w aplikacji rekreskowana](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>Tworzenie pulpitu nawigacyjnego w oddzielu

1. Aby utworzyć pulpit nawigacyjny , Utwórz **pulpit nawigacyjny** > . Możesz też wybrać pozycję istniejący pulpit nawigacyjny, **pulpity nawigacyjne** > wybrać pulpit nawigacyjny z listy.

    ![Utwórz pulpit nawigacyjny](media/redash/create-dashboard.png)

1. W oknie **nowy pulpit nawigacyjny** Nazwij pulpit nawigacyjny i wybierz pozycję **Zapisz**. W oknie **< Dashboard_name >** wybierz pozycję **Dodaj widżet** , aby utworzyć nowy element widget. 

1. W oknie **Dodawanie widżetu** wybierz opcję Nazwa zapytania, **Wybierz wizualizację**i **Parametry**. Wybierz pozycję **Dodaj do pulpitu nawigacyjnego**

   ![Wybierz wizualizacje i Dodaj do pulpitu nawigacyjnego](media/redash/add-widget-window.png)

1. Wybierz pozycję **gotowe do edycji** , aby ukończyć tworzenie pulpitu nawigacyjnego.

1.  W trybie edycji pulpitu nawigacyjnego wybierz opcję **Użyj filtrów na poziomie pulpitu nawigacyjnego** , aby użyć wcześniej zdefiniowanego parametru **typu** .

    ![Ukończ tworzenie pulpitu nawigacyjnego](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Następne kroki

* [Pisanie zapytań dla usługi Azure Data Explorer](write-queries.md)


