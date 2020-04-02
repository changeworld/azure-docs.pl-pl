---
title: Analizowanie danych w Eksploratorze danych platformy Azure za pomocą notesów platformy Azure
description: W tym temacie pokazano, jak utworzyć kwerendę przy użyciu notesu platformy Azure
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f99e11be99f22feec73b72397b27522b90dbf49
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522409"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Analizowanie danych w Eksploratorze danych platformy Azure za pomocą notesów platformy Azure

[Notesy platformy Azure](https://notebooks.azure.com/) to usługa w chmurze platformy Azure, która upraszcza tworzenie i udostępnianie [notesów Jupyter.](https://jupyter.org/) Notesy platformy Azure ułatwia łączenie dokumentacji, kodu i wyniki uruchamiania kodu.

> [!Note]
> * Poniższa procedura używa klienta języka Python w środowisku notesów platformy Azure do wykonywania zapytań do Eksploratora danych platformy Azure. Jednak można również użyć [KQLmagic](https://docs.microsoft.com/azure/data-explorer/kqlmagic) do kwerendy Usługi Azure Data Explorer.
> * Niektórzy użytkownicy zgłaszali problemy z uwierzytelnianiem przy użyciu krawędzi; dopóki takie problemy nie zostaną rozwiązane, użyj innej przeglądarki.

## <a name="create-a-project"></a>Tworzenie projektu

1. Otwórz [notesy platformy Azure](https://notebooks.azure.com/) w przeglądarce i zaloguj się.

1. W nagłówku wybierz pozycję **Moje projekty.** 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. Wybierz **+ Nowe projekty**.
    
1. W oknie dialogowym **Tworzenie nowego projektu:**
    1. Wprowadź **nazwę projektu**.
    1. Wyczyść pole wyboru **Publiczne.**
        >[!Important]
        > Jeśli nie wyczyścisz pola wyboru Publiczne, projekt zostanie ujawniony w otwartym Internecie.
    1. Wybierz **pozycję Utwórz**.
    
    ![Tworzenie nowego projektu](media/azurenotebooks/an-create-new-project-blank.png)

    Identyfikator projektu jest tworzony automatycznie.

## <a name="create-a-notebook"></a>Tworzenie notesu

1. W nowym projekcie utwórz notes. Notes powinien używać [obsługiwanego języka](https://github.com/Azure/azure-kusto-python#minimum-requirements).
Aby utworzyć notes, wybierz pozycję **+ Nowy** i wybierz pozycję **Notes**.

    ![Tworzenie nowego notesu](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. W oknie dialogowym **Tworzenie nowego notesu** wprowadź nazwę notesu.

1. Wybierz **Python 3.6** i wybierz **pozycję Nowy**.
    
    ![Tworzenie nowego notesu](media/azurenotebooks/an-create-new-notebook.png) 
    
1. W projekcie wybierz nowy notes.

    ![Wybieranie nowego notesu](media/azurenotebooks/an-select-notebook.png)

    Poczekaj, aż jądro Pythona zaiwrze. Gdy wypełniona ikona okręgu zmieni się w ikonę pustego okręgu, możesz kontynuować.

    ![Inicjuje jądro](media/azurenotebooks/an-python-init-icon.png)

1. Aby zaimportować moduł systemowy, wprowadź następujące polecenia i wybierz **przycisk Uruchom:**
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > Aby uruchomić komórkę, można również nacisnąć klawisze Shift+Enter.

1.  Aby zaimportować klasy SDK, wprowadź następujące polecenia i wybierz **przycisk Uruchom:**
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  Aby utworzyć parametry połączenia i uruchomić klienta Kusto, wprowadź następujące polecenia i wybierz przycisk **Uruchom:**  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. W wierszu otwórz nową kartę [https://aka.ms/devicelogin](https://aka.ms/devicelogin)przeglądarki w obszarze . 
   
1. Wprowadź kod autoryzacji i zaloguj się na@microsoft.comswoje konto AAD ( ) . Klient `kc` Kusto może teraz uwierzytelniać się w Eksploratorze danych platformy Azure przy użyciu twojej tożsamości.

1. Wróć do notesu, aby zobaczyć wynik uwierzytelniania. 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>Wykonywanie kwerendy Kusto

1. Wprowadź zapytanie Kusto i wybierz **run**. Przykład:

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* [Repo GitHub Kusto-python](https://github.com/Azure/azure-kusto-python)
