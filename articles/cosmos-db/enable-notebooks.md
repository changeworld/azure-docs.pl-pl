---
title: Włączanie notesów na koncie usługi Azure Cosmos DB (wersja zapoznawcza)
description: Wbudowane notesy usługi Azure Cosmos DB umożliwiają analizowanie i wizualizowanie danych z poziomu portalu. W tym artykule opisano sposób włączania tej funkcji dla kont usługi Cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768018"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Włączanie notesów dla kont usługi Azure Cosmos DB (wersja zapoznawcza)

> [!IMPORTANT]
> Wbudowane notesy usługi Azure Cosmos DB są obecnie dostępne w następujących regionach platformy Azure: Australia Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Europa Północna, Południowo-Środkowe stany USA, Azja Południowo-Wschodnia, Wielka Brytania Południowa, Europa Zachodnia i Zachodnie stany USA 2. Aby używać notesów, [utwórz nowe konto z notesami](#enable-notebooks-in-a-new-cosmos-account) lub [włącz notesy na istniejącym koncie](#enable-notebooks-in-an-existing-cosmos-account) w jednym z tych regionów.

Wbudowane notesy Jupyter w usłudze Azure Cosmos DB umożliwiają analizowanie i wizualizowanie danych z witryny Azure portal. W tym artykule opisano sposób włączania tej funkcji dla konta usługi Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Włączanie notesów na nowym koncie usługi Cosmos
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).
1. Wybierz **pozycję Utwórz zasoby** > **Bazy danych** > **usługi Azure Cosmos DB**.
1. Na stronie **Tworzenie konta usługi Azure Cosmos DB** wybierz pozycję **Notesy**. 
 
    ![Wybierz opcję notesów w bloku Tworzenia bazy danych Usługi Azure Cosmos DB](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Wybierz pozycję **Przegląd + utwórz**. Możesz pominąć opcję **Sieć** i **Znaczniki.** 
1. Przejrzyj ustawienia konta, a następnie wybierz pozycję **Utwórz**. Utworzenie konta trwa kilka minut. Poczekaj na wyświetlenie komunikatu **Wdrożenie zostało ukończone** na stronie portalu. 

    ![Okienko Powiadomienia w witrynie Azure Portal](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Wybierz pozycję **Przejdź do zasobu**, aby przejść do strony konta usługi Azure Cosmos DB. 

    ![Strona konta usługi Azure Cosmos DB](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Przejdź do okienka **Eksploratora danych.** Teraz powinieneś zobaczyć obszar roboczy notesów.

    ![Nowy obszar roboczy notesów usługi Azure Cosmos DB](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Włączanie notesów na istniejącym koncie usługi Cosmos
Notesy można również włączyć na istniejących kontach. Ten krok należy wykonać tylko raz na konto.

1. Przejdź do okienka **Eksploratora danych** na koncie usługi Cosmos.
1. Wybierz **pozycję Włącz notesy**.

    ![Tworzenie nowego obszaru roboczego notesów w Eksploratorze danych](media/enable-notebooks/enable-notebooks-workspace.png)
1. Spowoduje to wyświetlenie monitu o utworzenie nowego obszaru roboczego notesów. Wybierz **pozycję Zakończ konfigurację.**
1. Twoje konto jest teraz włączone do korzystania z notesów!

## <a name="create-and-run-your-first-notebook"></a>Tworzenie i uruchamianie pierwszego notesu

Aby sprawdzić, czy można używać notesów, wybierz jeden z notesów w obszarze Przykładowe notesy. Spowoduje to zapisanie kopii notesu w obszarze roboczym i otwarcie go.

W tym przykładzie użyjemy **GettingStarted.ipynb**. 

![Wyświetlanie notesu GettingStarted.ipynb](media/enable-notebooks/select-getting-started-notebook.png)

Aby uruchomić notes:
1. Wybierz pierwszą komórkę kodu zawierającą kod Języka Python. 
1. Wybierz **pozycję Uruchom,** aby uruchomić komórkę. Można również użyć **klawiszy Shift + Enter,** aby uruchomić komórkę.
1. Odśwież okienko zasobów, aby wyświetlić utworzoną bazę danych i kontener.

    ![Uruchamianie notesu wprowadzenie](media/enable-notebooks/run-first-notebook-cell.png)

Możesz również wybrać pozycję **Nowy notes,** aby utworzyć nowy notes lub przekazać istniejący plik notesu (.ipynb), wybierając **polecenie Przekaż plik** z menu Moje **notesy.** 

![Tworzenie lub przekazywanie nowego notesu](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o zaletach [notebooków usługi Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
