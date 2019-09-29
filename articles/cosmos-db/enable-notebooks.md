---
title: Włącz Notesy na koncie Azure Cosmos DB
description: Wbudowane notesy Azure Cosmos DB umożliwiają analizowanie i wizualizowanie danych z poziomu portalu. W tym artykule opisano sposób włączania tej funkcji dla kont usługi Cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: 824a562cb5f0562a235d903fbd4e575bc9c22572
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672748"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts"></a>Włącz notesy dla kont Azure Cosmos DB

Wbudowane notesy Jupyter w Azure Cosmos DB umożliwiają analizowanie i wizualizowanie danych z Azure Portal. W tym artykule opisano, jak włączyć tę funkcję dla konta Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Włącz Notesy na nowym koncie Cosmos
1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
1. Wybierz kolejno pozycje **Utwórz zasób** > **Bazy danych** > **Azure Cosmos DB**.
1. Na stronie **Tworzenie konta Azure Cosmos DB** wybierz pozycję **notesy**. 
 
    ![Opcja Wybierz notesy w Azure Cosmos DB Utwórz blok](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Wybierz pozycję **Przegląd + utwórz**. Możesz pominąć opcję **Sieć** i **Tagi** . 
1. Przejrzyj ustawienia konta, a następnie wybierz pozycję **Utwórz**. Utworzenie konta może potrwać kilka minut. Poczekaj, aż Strona portalu **zostanie**wyświetlona. 

    ![Okienko Powiadomienia w witrynie Azure Portal](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Wybierz pozycję **Przejdź do zasobu** , aby przejść do strony konta Azure Cosmos DB. 

    ![Strona konta Azure Cosmos DB](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Przejdź do okienka **Eksplorator danych** . Zobaczysz teraz obszar roboczy notesy.

    ![Nowy obszar roboczy notesy Azure Cosmos DB](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Włącz Notesy na istniejącym koncie Cosmos
Możesz również włączyć Notesy na istniejących kontach. Ten krok należy wykonać tylko raz dla każdego konta.

1. Przejdź do okienka **Eksplorator danych** na koncie Cosmos.
1. Wybierz pozycję **Włącz notesy**.

    ![Utwórz nowy obszar roboczy notesy w Eksplorator danych](media/enable-notebooks/enable-notebooks-workspace.png)
1. Spowoduje to wyświetlenie monitu o utworzenie nowego obszaru roboczego notesów. Wybierz pozycję **Ukończ instalację.**
1. Twoje konto ma teraz możliwość korzystania z notesów!

## <a name="create-and-run-your-first-notebook"></a>Tworzenie i uruchamianie pierwszego notesu

Aby sprawdzić, czy możesz używać notesów, wybierz jeden z notesów w obszarze przykładowe notesy. Spowoduje to zapisanie kopii notesu w obszarze roboczym i otwarcie go.

W tym przykładzie użyjemy **GettingStarted. ipynb**. 

![Wyświetlanie notesu GettingStarted. ipynb](media/enable-notebooks/select-getting-started-notebook.png)

Aby uruchomić Notes:
1. Wybierz pierwszą komórkę kodu zawierającą kod języka Python. 
1. Wybierz pozycję **Uruchom** , aby uruchomić komórkę. Możesz również użyć **klawiszy SHIFT + ENTER** , aby uruchomić komórkę.
1. Odśwież okienko zasobów, aby wyświetlić bazę danych i kontener, które zostały utworzone.

    ![Uruchamianie notesu wprowadzającego](media/enable-notebooks/run-first-notebook-cell.png)

Możesz również wybrać opcję **Nowy Notes** , aby utworzyć nowy Notes lub przekazać istniejący plik notesu (. ipynb), wybierając opcję **Przekaż plik** z menu **Moje notesy** . 

![Utwórz lub Przekaż nowy Notes](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Następne kroki

- Poznaj zalety [Azure Cosmos DB notesów Jupyter](cosmosdb-jupyter-notebooks.md)
- Dowiedz się więcej [na temat korzystania z funkcji i poleceń notesu](use-notebook-features-and-commands.md)