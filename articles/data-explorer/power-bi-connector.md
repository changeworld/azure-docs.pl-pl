---
title: 'Szybki start: Wizualizuj dane przy użyciu łącznika Eksplorator danych platformy Azure dla usługi Power BI'
description: 'Z tego przewodnika Szybki start nauczysz się, jak używać jednej z trzech opcji wizualizacji danych w usłudze Power BI: łącznika usługi Power BI dla usługi Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/14/2018
ms.openlocfilehash: 1be13090244a94c1bf3f731c8b6d73fa0848c393
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047606"
---
# <a name="quickstart-visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Szybki start: Wizualizuj dane przy użyciu łącznika Eksplorator danych platformy Azure dla usługi Power BI

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Power BI to rozwiązanie do analizy biznesowej, które pozwala wizualizować dane i udostępniać wyniki w organizacji.

Usługa Azure Data Explorer oferuje trzy opcje łączenia się z danymi w usłudze Power BI: za pomocą wbudowanego łącznika, przez zaimportowanie zapytania z usługi Azure Data Explorer lub za pomocą zapytania SQL. W tym przewodniku Szybki start pokazano, jak za pomocą wbudowanego łącznika uzyskać dane i zwizualizować je w raporcie usługi Power BI.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego przewodnika Szybki start potrzebne są następujące elementy:

* Konto e-mail organizacji należące do usługi Azure Active Directory, aby możliwe było łączenie się z [klastrem pomocy usługi Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples).

* Program [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (wybierz pozycję **POBIERZ BEZPŁATNIE**)

## <a name="get-data-from-azure-data-explorer"></a>Pobieranie danych z usługi Azure Data Explorer

Najpierw nawiąż połączenie z klastrem pomocy usługi Azure Data Explorer, a następnie wprowadź podzestaw danych z tabeli *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. W programie Power BI Desktop na karcie **Narzędzia główne** wybierz pozycję **Pobierz dane**, a następnie pozycję **Więcej**.

    ![Pobieranie danych](media/power-bi-connector/get-data-more.png)

1. Wyszukaj pozycję *Azure Data Explorer*, wybierz pozycję **Azure Data Explorer (beta)**, a następnie pozycję **Połącz**.

    ![Wyszukiwanie i pobieranie danych](media/power-bi-connector/search-get-data.png)

1. Na ekranie **Łącznik w wersji zapoznawczej** wybierz opcję **Kontynuuj**.

1. Na następnym ekranie wypełnij formularz, używając poniższych informacji.

    ![Opcje klastra, bazy danych, tabeli](media/power-bi-connector/cluster-database-table.png)

    **Ustawienie** | **Wartość** | **Opis pola**
    |---|---|---|
    | Klaster | *https://help.kusto.windows.net* | Adres URL klastra pomocy. W przypadku innych klastrów adres URL ma postać *https://\<Nazwa_klastra\>.\<Region\>.kusto.windows.net*. |
    | Database (Baza danych) | Pozostaw puste | Baza danych hostowana w klastrze, z którą nawiązujesz połączenie. Wybierzemy ją w późniejszym kroku. |
    | Nazwa tabeli | Pozostaw puste | Jedną z tabel w bazie danych lub zapytanie, takich jak <code>StormEvents \| take 1000</code>. Wybierzemy ją w późniejszym kroku. |
    | Opcje zaawansowane | Pozostaw puste | Opcje zapytań, na przykład rozmiar zestawu wyników. |
    | Tryb łączności danych | *Tryb DirectQuery* | Określa, czy usługa Power BI importuje dane, czy łączy się bezpośrednio ze źródłem danych. Korzystając z tego łącznika, możesz wybrać dowolną z tych opcji. |
    | | | |

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

Jeśli nie potrzebujesz już raportu, który został utworzony na potrzeby tego przewodnika Szybki start, usuń plik programu Power BI Desktop (pbix).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Szybki start: Wizualizuj dane przy użyciu importowanych zapytania w usłudze Power BI](power-bi-imported-query.md)
