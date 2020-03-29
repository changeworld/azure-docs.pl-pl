---
title: Testowanie kodu do nauki o danych za pomocą usług Azure DevOps — proces nauki o danych zespołowych
description: Testowanie kodu nauki o danych na platformie Azure za pomocą zestawu danych prognozowania dochodów uci za pomocą procesu nauki o danych zespołu i usług Azure DevOps
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: 9612114bb368898ccf31b2c8692869b84544b652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722066"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Testowanie kodu nauki o danych na platformie Azure za pomocą procesu nauki o danych zespołu i usług Azure DevOps
Ten artykuł zawiera wstępne wskazówki dotyczące testowania kodu w przepływie pracy nauki o danych. Takie testy zapewniają analitykom danych systematyczny i skuteczny sposób sprawdzania jakości i oczekiwanego wyniku ich kodu. Używamy projektu procesu nauki o danych zespołu (TDSP), [który używa zestawu danych UCI Adult Income,](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) który opublikowaliśmy wcześniej, aby pokazać, jak można przeprowadzić testowanie kodu. 

## <a name="introduction-on-code-testing"></a>Wprowadzenie do testowania kodu
"Testowanie jednostkowe" jest od dawna praktyką w zakresie tworzenia oprogramowania. Ale dla nauki o danych często nie jest jasne, co oznacza "testowanie jednostkowe" i jak należy przetestować kod dla różnych etapów cyklu życia nauki o danych, takich jak:

* Przygotowywanie danych
* Badanie jakości danych
* Modelowanie
* Wdrażanie modelu 

Ten artykuł zastępuje termin "testowanie jednostkowe" terminem "testowanie kodu". Odnosi się do testowania jako funkcje, które pomagają ocenić, czy kod dla pewnego kroku cyklu życia nauki o danych przynosi wyniki "zgodnie z oczekiwaniami". Osoba, która pisze test definiuje, co jest "zgodnie z oczekiwaniami", w zależności od wyniku funkcji — na przykład sprawdzanie jakości danych lub modelowanie.

Ten artykuł zawiera odwołania jako przydatne zasoby.

## <a name="azure-devops-for-the-testing-framework"></a>Usługa Azure DevOps dla struktury testowania
W tym artykule opisano sposób wykonywania i automatyzacji testowania przy użyciu usługi Azure DevOps. Możesz skorzystać z alternatywnych narzędzi. Pokazujemy również, jak skonfigurować automatyczną kompilację przy użyciu usługi Azure DevOps i agentów kompilacji. Dla agentów kompilacji używamy maszyn wirtualnych usługi Azure Data Science (DSVM).

## <a name="flow-of-code-testing"></a>Przepływ testowania kodu
Ogólny przepływ pracy kodu testowania w projekcie nauki o danych wygląda następująco: 

![Schemat blokowy testowania kodu](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Szczegółowe procedury

Aby skonfigurować i uruchomić testowanie kodu oraz automatyczną kompilację przy użyciu agenta kompilacji i usługi Azure DevOps, należy wykonać następujące kroki:

1. Tworzenie projektu w aplikacji klasycznej Visual Studio:

    ![Ekran "Tworzenie nowego projektu" w programie Visual Studio](./media/code-test/create_project.PNG)

   Po utworzeniu projektu znajdziesz go w Eksploratorze rozwiązań w prawym okienku:
    
    ![Kroki tworzenia projektu](./media/code-test/create_python_project_in_vs.PNG)

    ![Eksplorator rozwiązań](./media/code-test/solution_explorer_in_vs.PNG)

1. Podaj kod projektu do repozytorium kodu projektu Usługi Azure DevOps: 

    ![Repozytorium kodu projektu](./media/code-test/create_repo.PNG)

1. Załóżmy, że wykonaliśmy pewne prace nad przygotowaniem danych, takie jak pozyskiwania danych, inżynierii funkcji i tworzenia kolumn etykiet. Chcesz upewnić się, że kod generuje oczekiwane wyniki. Oto kod, którego można użyć do sprawdzenia, czy kod przetwarzania danych działa poprawnie:

    * Sprawdź, czy nazwy kolumn są odpowiednie:
    
      ![Kod pasujących nazw kolumn](./media/code-test/check_column_names.PNG)

    * Sprawdź, czy poziomy odpowiedzi są odpowiednie:

      ![Kod dopasowywania poziomów](./media/code-test/check_response_levels.PNG)

    * Sprawdź, czy odsetek odpowiedzi jest rozsądny:

      ![Kod procentu odpowiedzi](./media/code-test/check_response_percentage.PNG)

    * Sprawdź brakujące stawki każdej kolumny w danych:
    
      ![Kod brakującego kursu](./media/code-test/check_missing_rate.PNG)


1. Po wykonaniu przetwarzania danych i pracy inżynieryjnej funkcji i przeszkolonych dobry model, upewnij się, że model, który przeszkolony można uzyskać nowe zestawy danych poprawnie. Aby sprawdzić poziomy prognozowania i rozkład wartości etykiet, można użyć następujących dwóch testów:

    * Sprawdź poziomy prognozowania:
    
      ![Kod do sprawdzania poziomów prognozowania](./media/code-test/check_prediction_levels.PNG)

    * Sprawdź rozkład wartości prognozowania:

      ![Kod do sprawdzania wartości prognozowania](./media/code-test/check_prediction_values.PNG)

1. Umieść wszystkie funkcje testowe razem w skrypcie Pythona o nazwie **test_funcs.py:**

    ![Skrypt Języka Python dla funkcji testowych](./media/code-test/create_file_test_func.PNG)


1. Po przygotowaniu kodów testowych można skonfigurować środowisko testowe w programie Visual Studio.

   Utwórz plik języka Python o nazwie **test1.py**. W tym pliku utwórz klasę, która zawiera wszystkie testy, które chcesz wykonać. Poniższy przykład przedstawia sześć testów przygotowanych:
    
    ![Plik Języka Python z listą testów w klasie](./media/code-test/create_file_test1_class.PNG)

1. Te testy mogą być automatycznie wykrywane, jeśli umieścisz **codetest.testCase** po nazwie klasy. Otwórz Eksploratora testów w prawym okienku i wybierz pozycję **Uruchom wszystko**. Wszystkie testy będą uruchamiane sekwencyjnie i powiedzą, czy test zakończy się pomyślnie, czy nie.

    ![Uruchamianie testów](./media/code-test/run_tests.PNG)

1. Zaewidencjonuj swój kod do repozytorium projektu za pomocą poleceń Git. Najnowsze prace zostaną wkrótce odzwierciedlone w usłudze Azure DevOps.

    ![Polecenia Git do sprawdzania kodu](./media/code-test/git_check_in.PNG)

    ![Najnowsze prace w usłudze Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Konfigurowanie automatycznej kompilacji i testowania w usłudze Azure DevOps:

    a. W repozytorium projektu wybierz pozycję **Buduj i zwolnij**, a następnie wybierz **+Nowy,** aby utworzyć nowy proces kompilacji.

    ![Wybór do rozpoczęcia nowego procesu kompilacji](./media/code-test/create_new_build.PNG)

    b. Postępuj zgodnie z instrukcjami, aby wybrać lokalizację kodu źródłowego, nazwę projektu, repozytorium i informacje o gałęzi.
    
    ![Źródło, nazwa, repozytorium i informacje o gałęzi](./media/code-test/fill_in_build_info.PNG)

    d. Wybierz szablon. Ponieważ nie ma szablonu projektu języka Python, zacznij od wybrania **opcji Pusty proces**. 

    ![Lista szablonów i przycisk "Opróżnij proces"](./media/code-test/start_empty_process_template.PNG)

    d. Nazwij kompilację i wybierz agenta. W tym miejscu można wybrać wartość domyślną, jeśli chcesz użyć dsvm do ukończenia procesu kompilacji. Aby uzyskać więcej informacji na temat ustawiania agentów, zobacz [Tworzenie i zwalnianie agentów](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts).
    
    ![Wybór kompilacji i agentów](./media/code-test/select_agent.PNG)

    e. Wybierz **+** w lewym okienku, aby dodać zadanie dla tej fazy kompilacji. Ponieważ mamy zamiar uruchomić skrypt Języka Python **test1.py,** aby zakończyć wszystkie kontrole, to zadanie jest za pomocą polecenia programu PowerShell do uruchamiania kodu języka Python.
    
    ![Okienko "Dodawanie zadań" z zaznaczoną wybraną podaniem programu PowerShell](./media/code-test/add_task_powershell.PNG)

    f. W szczegółach programu PowerShell wprowadź wymagane informacje, takie jak nazwa i wersja programu PowerShell. Wybierz **skrypt wbudowany** jako typ. 
    
    W polu w obszarze **Skrypt wbudowany**można wpisać **python test1.py**. Upewnij się, że zmienna środowiskowa jest poprawnie skonfigurowana dla języka Python. Jeśli potrzebujesz innej wersji lub jądra Języka Python, możesz jawnie określić ścieżkę, jak pokazano na rysunku: 
    
    ![Szczegóły programu PowerShell](./media/code-test/powershell_scripts.PNG)

    g. Wybierz **pozycję Zapisz & kolejki,** aby zakończyć proces potoku kompilacji.

    ![Przycisk "Zapisz & kolejki"](./media/code-test/save_and_queue_build_definition.PNG)

Teraz za każdym razem, gdy nowe zatwierdzenie jest wypychane do repozytorium kodu, proces kompilacji rozpocznie się automatycznie. (W tym przypadku używamy wzorca jako repozytorium, ale można zdefiniować dowolną gałąź. Proces uruchamia plik **test1.py** na komputerze agenta, aby upewnić się, że wszystko zdefiniowane w kodzie działa poprawnie. 

Jeśli alerty są poprawnie skonfigurowane, otrzymasz powiadomienie w wiadomości e-mail po zakończeniu kompilacji. Można również sprawdzić stan kompilacji w usłudze Azure DevOps. Jeśli to się nie powiedzie, możesz sprawdzić szczegóły kompilacji i dowiedzieć się, który kawałek jest uszkodzony.

![Powiadomienie e-mail o sukcesie kompilacji](./media/code-test/email_build_succeed.PNG)

![Powiadomienie o sukcesie kompilacji w usłudze Azure DevOps](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Następne kroki
* Zobacz [repozytorium prognozowania dochodów UCI,](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) aby uzyskać konkretne przykłady testów jednostkowych dla scenariuszy analizy danych.
* Postępuj zgodnie z poprzednim konspektem i przykładami ze scenariusza prognozowania dochodów UCI w własnych projektach nauki o danych.

## <a name="references"></a>Dokumentacja
* [Zespołowe przetwarzanie danych dla celów naukowych](https://aka.ms/tdsp)
* [Narzędzia testowania programu Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Zasoby testowe usługi Azure DevOps](https://www.visualstudio.com/team-services/)
* [Maszyny wirtualne Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
