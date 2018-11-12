---
title: 'Szybki start: tworzenie klastra i bazy danych usługi Azure Data Explorer'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć klaster i bazę danych usługi Azure Data Explorer oraz pozyskiwać (ładować) dane.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: 4b4230397da7d97b613b546f33a0cbb764675720
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214821"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>Szybki start: tworzenie klastra i bazy danych usługi Azure Data Explorer

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Aby używać usługi Azure Data Explorer, najpierw utwórz *klaster*, a następnie utwórz w tym klastrze co najmniej jedną *bazę danych*. Następnie *pozyskaj* (załaduj) dane do bazy danych, aby uruchamiać w niej zapytania. W tym przewodniku Szybki start utworzysz klaster i bazę danych. W kolejnych artykułach pokażemy, jak pozyskiwać dane.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-cluster"></a>Tworzenie klastra

Klaster usługi Azure Data Explorer tworzy się w grupie zasobów platformy Azure, korzystając ze zdefiniowanego zestawu zasobów obliczeniowych i magazynowych.

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu.

1. Wyszukaj pozycję *Azure Data Explorer*.

   ![Wyszukiwania zasobów](media/create-cluster-database-portal/search-resources.png)

1. W obszarze **Azure Data Explorer** w dolnej części ekranu wybierz pozycję **Utwórz**.

1. Wprowadź unikatową nazwę klastra, wybierz swoją subskrypcję i utwórz grupę zasobów o nazwie *test-resource-group*.

    ![Tworzenie grupy zasobów](media/create-cluster-database-portal/create-resource-group.png)

1. Wypełnij formularz, używając poniższych informacji.

   ![Formularz tworzenia klastra](media/create-cluster-database-portal/create-cluster-form.png)

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa klastra | Unikatowa nazwa klastra | Wybierz unikatową nazwę, która identyfikuje Twój klaster. Na przykład *mytestcluster*. Do podanej nazwy klastra jest dołączana nazwa domeny *[region].kusto.windows.net*. Nazwa może zawierać tylko małe litery i cyfry. Musi zawierać od 3 do 22 znaków.
    | Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla klastra.|
    | Grupa zasobów | *test-resource-group* | Utwórz nową grupę zasobów. |
    | Lokalizacja | *Zachodnie stany USA* | Na potrzeby tego przewodnika Szybki start wybierz wartość *Zachodnie stany USA*. W przypadku systemu produkcyjnego wybierz region, który najlepiej odpowiada Twoim potrzebom.
    | Specyfikacja środowiska obliczeniowego | *D13_v2* | Na potrzeby tego przewodnika Szybki start wybierz najtańszą specyfikację. W przypadku systemu produkcyjnego wybierz specyfikację, która najlepiej odpowiada Twoim potrzebom.
    | | |

1. Wybierz pozycję **Utwórz**, aby aprowizować klaster. Aprowizacja zwykle trwa około dziesięciu minut. Wybierz pozycję **Powiadomienia** na pasku narzędzi, aby monitorować proces aprowizacji.

    ![Powiadomienia](media/create-cluster-database-portal/notifications.png)

1. Po zakończeniu procesu wybierz pozycję **Powiadomienia**, a następnie **Przejdź do zasobu**.

    ![Przechodzenie do zasobu](media/create-cluster-database-portal/notification-resource.png)

## <a name="create-a-database"></a>Tworzenie bazy danych

Teraz możesz przystąpić do wykonania drugiego kroku w procesie — do utworzenia bazy danych.

1. Na karcie **Przegląd** wybierz pozycję **Utwórz bazę danych**.

    ![Krok drugi: tworzenie bazy danych](media/create-cluster-database-portal/database-creation.png)

1. Wypełnij formularz, używając poniższych informacji.

    ![Formularz tworzenia bazy danych](media/create-cluster-database-portal/create-database.png)

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | Nazwa bazy danych | *TestDatabase* | Nazwa bazy danych musi być unikatowa w obrębie klastra.
    | Okres przechowywania | *3650* | Przedział czasu, w którym jest gwarantowana dostępność danych dla zapytania. Przedział czasu jest mierzony od momentu pozyskania danych.
    | Okres pamięci podręcznej | *31* | Przedział czasu, w którym często używane w zapytaniach dane mają być dostępne na dysku SSD lub w pamięci RAM, zamiast w magazynie długoterminowym.
    | | | |

1. Wybierz pozycję **Zapisz**, aby utworzyć bazę danych. Tworzenie zazwyczaj zajmuje mniej niż minutę. Po zakończeniu procesu wrócisz na kartę **Przegląd** klastra.

## <a name="run-basic-commands-in-the-database"></a>Uruchamianie podstawowych poleceń w bazie danych

Gdy masz już klaster i bazę danych, możesz uruchamiać zapytania i polecenia. W bazie danych nie ma jeszcze żadnych danych, ale mimo to możesz zobaczyć, jak działają narzędzia.

1. W obszarze klastra wybierz pozycję **Zapytanie**.

    ![Zapytanie bazy danych](media/create-cluster-database-portal/query-database.png)

1. W oknie zapytania wklej następujące polecenie: `.show databases`, a następnie wybierz pozycję **Uruchom**.

    ![Polecenie wyświetlenia baz danych](media/create-cluster-database-portal/show-databases.png)

    W zestawie wyników zostanie wyświetlona pozycja **TestDatabase** — jedyna baza danych w klastrze.

1. W oknie zapytania wklej następujące polecenie: `.show tables`, a następnie wybierz to polecenie w oknie. Wybierz pozycję **Uruchom**.

    To polecenie zwraca pusty zestaw wyników, ponieważ nie masz jeszcze żadnych tabel. Tabelę dodasz w następnym artykule w tej serii.

## <a name="stop-and-restart-the-cluster"></a>Zatrzymywanie i ponowne uruchamianie klastra

W zależności od Twoich potrzeb biznesowych możesz zatrzymywać i ponownie uruchamiać klaster.

1. Aby zatrzymać klaster, w górnej części karty **Przegląd** wybierz pozycję **Zatrzymaj**.

    Po zatrzymaniu klastra dane są niedostępne dla zapytań i nie można pozyskiwać nowych danych.

1. Aby ponownie uruchomić klaster, w górnej części karty **Przegląd** wybierz pozycję **Uruchom**.

    Po ponownym uruchomieniu klastra jego udostępnienie trwa około dziesięciu minut (podobnie jak podczas pierwotnej aprowizacji). Dodatkowy czas zajmuje załadowanie danych do pamięci podręcznej w warstwie magazynowania Gorąca.  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz skorzystać z naszych pozostałych przewodników Szybki start i samouczków, zachowaj utworzone zasoby. W przeciwnym razie wyczyść grupę **test-resource-group**, aby uniknąć ponoszenia kosztów.

1. W witrynie Azure Portal wybierz **grupy zasobów** daleko po lewej stronie, a następnie wybierz utworzoną grupę zasobów.  

    Jeśli menu po lewej stronie jest zwinięte, wybierz ![przycisk Rozwiń,](media/create-cluster-database-portal/expand.png) aby je rozwinąć.

   ![Wybieranie grupy zasobów do usunięcia](media/create-cluster-database-portal/delete-resources-select.png)

1. W obszarze **test-resource-group** wybierz pozycję **Usuń grupę zasobów**.

1. W nowym oknie wpisz nazwę grupy zasobów do usunięcia (*test-resource-group*), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki start: pozyskiwanie danych z centrum zdarzeń do usługi Azure Data Explorer](ingest-data-event-hub.md)


