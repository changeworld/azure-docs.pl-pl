---
title: Wizualizuj dane za pomocą łącznika usługi Azure Eksplorator danych dla Power BI
description: 'W tym artykule dowiesz się, jak użyć jednej z trzech opcji wizualizacji danych w Power BI: Łącznik Power BI dla platformy Azure Eksplorator danych.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a95d45481bed17e46429e3a22dff4b8cc62354a9
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560494"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Wizualizuj dane przy użyciu łącznika Eksplorator danych platformy Azure dla Power BI

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Power BI to rozwiązanie do analizy biznesowej, które pozwala wizualizować dane i udostępniać wyniki w organizacji. Usługa Azure Data Explorer oferuje trzy opcje łączenia się z danymi w usłudze Power BI: za pomocą wbudowanego łącznika, przez zaimportowanie zapytania z usługi Azure Data Explorer lub za pomocą zapytania SQL. W tym artykule pokazano, jak za pomocą wbudowanego łącznika pobierać dane i wizualizować je w raporcie Power BI. Używanie łącznika natywnego Eksplorator danych platformy Azure do tworzenia pulpitów nawigacyjnych Power BI jest proste. Łącznik Power BI obsługuje [tryby połączeń zapytań import i Direct](https://docs.microsoft.com/power-bi/desktop-directquery-about). Pulpity nawigacyjne można tworzyć przy użyciu trybu **importu** lub **zapytania bezpośredniego** , w zależności od wymagań dotyczących scenariusza, skali i wydajności. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, potrzebne są następujące elementy:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* Konto e-mail organizacji należące do usługi Azure Active Directory, aby możliwe było łączenie się z [klastrem pomocy usługi Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples).
* Program [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (wybierz pozycję **POBIERZ BEZPŁATNIE**)

## <a name="get-data-from-azure-data-explorer"></a>Pobieranie danych z usługi Azure Data Explorer

Najpierw nawiąż połączenie z klastrem pomocy usługi Azure Data Explorer, a następnie wprowadź podzestaw danych z tabeli *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. W programie Power BI Desktop na karcie **Narzędzia główne** wybierz pozycję **Pobierz dane**, a następnie pozycję **Więcej**.

    ![Pobieranie danych](media/power-bi-connector/get-data-more.png)

1. Wyszukaj *Eksplorator danych Azure*, wybierz pozycję **Azure Eksplorator danych** a następnie **Połącz**.

    ![Wyszukiwanie i pobieranie danych](media/power-bi-connector/search-get-data.png)

1. Na ekranie **Azure Eksplorator danych (Kusto)** Wypełnij formularz poniższymi informacjami.

    ![Opcje klastra, bazy danych, tabeli](media/power-bi-connector/cluster-database-table.png)

    **Ustawienie** | **Wartość** | **Opis pola**
    |---|---|---|
    | Klaster | *https://help.kusto.windows.net* | Adres URL klastra pomocy. W przypadku innych klastrów adres URL ma postać *https://\<Nazwa_klastra\>.\<Region\>.kusto.windows.net*. |
    | Baza danych | Pozostaw puste | Baza danych hostowana w klastrze, z którą nawiązujesz połączenie. Wybierzemy ją w późniejszym kroku. |
    | Nazwa tabeli | Pozostaw puste | Jedna z tabel w bazie danych lub zapytanie, takie jak <code>StormEvents \| take 1000</code>. Wybierzemy ją w późniejszym kroku. |
    | Opcje zaawansowane | Pozostaw puste | Opcje zapytań, na przykład rozmiar zestawu wyników. |
    | Tryb łączności danych | Tryb *DirectQuery* | Określa, czy usługa Power BI importuje dane, czy łączy się bezpośrednio ze źródłem danych. Korzystając z tego łącznika, możesz wybrać dowolną z tych opcji. |
    | | | |
    
    > [!NOTE]
    > W trybie **importu** dane są przenoszone do Power BI. W trybie **zapytania bezpośredniego** dane są wysyłane bezpośrednio z klastra usługi Azure Eksplorator danych.
    >
    > Użyj trybu **importu** , gdy:
    > * Zestaw danych jest mały.
    > * Dane nie są potrzebne niemal w czasie rzeczywistym. 
    > * Twoje dane są już agregowane lub agregacja jest przeprowadzana [w Kusto](/azure/kusto/query/summarizeoperator#list-of-aggregation-functions)    
    >
    > Użyj trybu **DirectQuery** w przypadku:
    > * Zestaw danych jest bardzo duży. 
    > * Potrzebujesz danych niemal w czasie rzeczywistym.   

1. Jeśli jeszcze nie masz połączenia z klastrem pomocy, zaloguj się. Zaloguj się przy użyciu konta organizacyjnego, a następnie wybierz pozycję **Połącz**.

    ![Logowanie](media/power-bi-connector/sign-in.png)

1. Na ekranie **Nawigator** rozwiń bazę danych **Samples**, wybierz pozycję **StormEvents**, a następnie pozycję **Edytuj**.

    ![Wybieranie tabeli](media/power-bi-connector/select-table.png)

    W edytorze Power Query zostanie otwarta tabela, w której przed zaimportowaniem danych można edytować wiersze i kolumny.

1. W edytorze Power Query wybierz strzałkę obok kolumny **DamageCrops**, a następnie wybierz pozycję **Sortuj malejąco**.

    ![Sortowanie kolumny DamageCrops malejąco](media/power-bi-connector/sort-descending.png)

1. Na karcie **Narzędzia główne** wybierz pozycję **Zachowaj wiersze**, a następnie pozycję **Zachowaj początkowe wiersze**. Wprowadź wartość *1000*, aby uzyskać pierwszych 1000 wierszy posortowanej tabeli.

    ![Zachowywanie początkowych wierszy](media/power-bi-connector/keep-top-rows.png)

1. Na karcie **Narzędzia główne** wybierz pozycję **Zamknij i zastosuj**.

    ![Zamknięcie i zastosowanie](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Wizualizacja danych w raporcie

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli raport utworzony w tym artykule nie jest już potrzebny, usuń plik Power BI Desktop (pbix).

## <a name="next-steps"></a>Następne kroki

[Porady dotyczące używania łącznika usługi Azure Eksplorator danych na potrzeby Power BI do wykonywania zapytań dotyczących danych](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
