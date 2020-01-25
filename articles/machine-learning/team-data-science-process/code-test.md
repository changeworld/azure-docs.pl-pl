---
title: Testowanie kodu do nauki o danych dzięki usługom DevOps platformy Azure — zespołu danych dla celów naukowych
description: Kod do nauki o danych, testowanie na platformie Azure przy użyciu UCI treści dla dorosłych dochodu prognozowania zestaw danych o procesie nauki o danych zespołu i usługom DevOps platformy Azure
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722066"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Kod do nauki o danych, testowanie na platformie Azure za pomocą zespołowego danych naukowych i usługom DevOps platformy Azure
Ten artykuł zawiera wskazówki wstępne do testowania kodu w przepływie pracy do analizy danych. Takie testy daje analitykom danych systematyczne i wydajny sposób kontroli jakości i oczekiwany wynik swój kod. Używamy Team Data Science naukowych [projektu, który używa zestawu danych na rachunku treści dla dorosłych](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) , opublikowaliśmy wcześniej pokazanie sposobu testowania kodu może odbywać się. 

## <a name="introduction-on-code-testing"></a>Wprowadzenie na testowanie kodu
"Testy jednostkowe" jest rozwiązaniem w zakresie od rozwoju oprogramowania. Jednak w przypadku nauki danych często nie jest jasne, co oznacza "testy jednostkowe" i jak należy przetestować kod dla różnych etapów cyklu życia analizy danych, na przykład:

* Przygotowywanie danych
* Badanie jakości danych
* Modelowanie
* Wdrażanie modelu 

W tym artykule zastępuje termin "testów jednostkowych" z "Testowanie kodu." Odnosi się do testowania jako funkcje, które pomagają w celu ustalenia, czy kod kroku cykl życia analizy danych jest przedstawiania wyników "zgodnie z oczekiwaniami." Osobę piszącą testu definiuje, co to jest "zgodnie z oczekiwaniami," w zależności od wyniku funkcji — na przykład, kontrola jakości danych lub modelowania.

Ten artykuł zawiera odwołania, jak przydatne zasoby.

## <a name="azure-devops-for-the-testing-framework"></a>Usługa Azure DevOps dla struktury testowania
W tym artykule opisano sposób wykonywania i zautomatyzować testowanie za pomocą usługi Azure DevOps. Można zdecydować się za pomocą narzędzi alternatywne. Ponadto przedstawiono, jak skonfigurować automatyczne kompilacji za pomocą DevOps platformy Azure i agentów kompilacji. Agenci kompilacji używamy maszyn wirtualnych do nauki o danych platformy Azure (maszyny).

## <a name="flow-of-code-testing"></a>Przepływ testowania kodu
Ogólny przepływ pracy testowania kodu w projekcie do nauki o danych wygląda następująco: 

![Schemat blokowy testowania kodu](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Szczegółowe procedury

Aby skonfigurować i uruchomić testowanie kodu i automatycznej kompilacji za pomocą agenta kompilacji i DevOps platformy Azure, wykonaj następujące kroki:

1. Utwórz projekt w aplikacji klasycznej w programie Visual Studio:

    ![Ekran "Tworzenie nowego projektu" w programie Visual Studio](./media/code-test/create_project.PNG)

   Po utworzeniu projektu, znajdziesz go w Eksploratorze rozwiązań w okienku po prawej stronie:
    
    ![Procedury służące do tworzenia projektu](./media/code-test/create_python_project_in_vs.PNG)

    ![Eksplorator rozwiązań](./media/code-test/solution_explorer_in_vs.PNG)

1. Źródła danych projektu kodu do repozytorium kodu projektu DevOps platformy Azure: 

    ![Repozytorium kodu projektu](./media/code-test/create_repo.PNG)

1. Załóżmy, że wykonano pewne przygotowywaniem danych, takich jak pozyskiwanie danych, technicznego opracowywania funkcji i tworzenia etykiety kolumn. Chcesz upewnić się, że Twój kod generuje wyniki, których można oczekiwać. Poniżej przedstawiono niektóre kod, który służy do sprawdzenia, czy kod przetwarzania danych działa prawidłowo:

    * Sprawdź, czy odpowiednie nazwy kolumn:
    
      ![Kod w celu dopasowania nazw kolumn](./media/code-test/check_column_names.PNG)

    * Sprawdź, czy odpowiednie poziomy odpowiedzi:

      ![Kod do dopasowania Poziomy](./media/code-test/check_response_levels.PNG)

    * Sprawdź, czy procent odpowiedzi jest uzasadnione:

      ![Kod na procent odpowiedzi](./media/code-test/check_response_percentage.PNG)

    * Sprawdź Brak stopień każdej kolumny w danych:
    
      ![Kod dla brakujących stawki](./media/code-test/check_missing_rate.PNG)


1. Po wykonaniu przetwarzania danych i funkcji pracy inżynierów i już skonfigurowanych pod kątem dobry model, upewnij się, że model który szkolenia można poprawnie wynik nowe zestawy danych. Następujących dwóch testów służy do sprawdzania poziomy prognoz i rozkładu wartości etykiety:

    * Sprawdź poziom prognozowania:
    
      ![Kod do sprawdzania poziomy prognoz](./media/code-test/check_prediction_levels.PNG)

    * Sprawdzanie rozkładu wartości prognozowania:

      ![Kod do sprawdzania wartości prognozy](./media/code-test/check_prediction_values.PNG)

1. Umieść wszystkie testowanie functions razem w skrypcie języka Python o nazwie **test_funcs.py**:

    ![Skrypt języka Python dla usługi functions testu](./media/code-test/create_file_test_func.PNG)


1. Po przygotowaniu są kody testu, możesz skonfigurować środowisko testowe w programie Visual Studio.

   Utwórz plik w języku Python o nazwie **test1.py**. W tym pliku należy utworzyć klasę, która zawiera wszystkie testy, które chcesz wykonać. Poniższy przykład przedstawia sześć testy przygotować:
    
    ![Plik języka Python z listy testów w klasie](./media/code-test/create_file_test1_class.PNG)

1. Te testy mogą być automatycznie wykrywane, jeżeli umieścisz **codetest.testCase** po Twoja nazwa klasy. Otwórz Eksplorator testów w okienku po prawej stronie, a następnie wybierz **Uruchom wszystkie**. Wszystkie testy uruchomią się sekwencyjnie i poinformuje, jeśli test wypadnie pomyślnie.

    ![Uruchamianie testów](./media/code-test/run_tests.PNG)

1. Zaewidencjonuj kod do repozytorium projektu przy użyciu poleceń usługi Git. Najbardziej aktualną pracy zostaną odzwierciedlone wkrótce w DevOps platformy Azure.

    ![Polecenia usługi Git, sprawdzania kodu pod kątem](./media/code-test/git_check_in.PNG)

    ![Najbardziej aktualną pracy w DevOps platformy Azure](./media/code-test/git_check_in_most_recent_work.PNG)

1. Skonfiguruj automatyczne kompilację i testowanie w metodyce DevOps platformy Azure:

    a. W repozytorium projektu wybierz **kompilowania i wydawania**, a następnie wybierz pozycję **+ nowy** można utworzyć nowego procesu kompilacji.

    ![Wybory dotyczące rozpoczynania nowego procesu kompilacji](./media/code-test/create_new_build.PNG)

    b. Postępuj zgodnie z monitami, aby wybrać lokalizacji kodu źródłowego, nazwę projektu, repozytorium i informacji o gałęzi.
    
    ![Informacje źródłowe, nazwa, repozytorium i gałąź](./media/code-test/fill_in_build_info.PNG)

    d. Wybierz szablon. Ponieważ nie istnieje żaden szablon projektu języka Python, należy rozpocząć od wybrania **pusty procesu**. 

    ![Lista szablonów i przycisk "pusty proces"](./media/code-test/start_empty_process_template.PNG)

    d. Nadaj nazwę kompilacji i wybierz agenta. W tym miejscu możesz wybrać wartość domyślną, jeśli chcesz użyć DSVM do ukończenia procesu kompilacji. Aby uzyskać więcej informacji na temat ustawień agentów, zobacz [Build and release agents i](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts).
    
    ![Kompilacje i wybór agentów](./media/code-test/select_agent.PNG)

    e. Wybierz **+** w okienku po lewej stronie, aby dodać zadanie dla tej fazy kompilacji. Ponieważ będziemy **Test1.py** skrypt języka Python, aby zakończyć wszystkie testy, to zadanie używa polecenia programu PowerShell do uruchomienia kodu w języku Python.
    
    ![Okienko "Dodawanie zadań" z wybranym programem PowerShell](./media/code-test/add_task_powershell.PNG)

    f. W szczegółach programu PowerShell Podaj wymagane informacje, takie jak nazwa i wersja programu PowerShell. Wybierz **wbudowany skrypt** jako typu. 
    
    W polu w obszarze **skrypt wbudowany**można wpisać **Python test1.py**. Upewnij się, że zmienna środowiskowa została prawidłowo skonfigurowana dla języka Python. Jeśli potrzebna jest inna wersja lub jądro języka Python, można jawnie określić ścieżkę, jak pokazano na rysunku: 
    
    ![Szczegóły programu PowerShell](./media/code-test/powershell_scripts.PNG)

    g. Wybierz pozycję **zapisz & kolejkę** , aby ukończyć proces kompilacji potoku.

    ![Przycisk "Zapisz & kolejki"](./media/code-test/save_and_queue_build_definition.PNG)

Teraz za każdym razem, gdy nowe zatwierdzenia są wypychane do repozytorium kodu, proces kompilacji rozpocznie się automatycznie. (W tym miejscu użyjemy wzorca jako repozytorium, ale możesz zdefiniować dowolną gałąź). Proces uruchamia plik **Test1.py** na maszynie agenta, aby upewnić się, że wszystko zdefiniowane w kodzie działa poprawnie. 

Jeśli alerty są prawidłowo skonfigurowane, zostanie wyświetlone powiadomienie w wiadomości e-mail po zakończeniu kompilacji. Możesz również sprawdzić stan kompilacji w DevOps platformy Azure. Jeśli nie powiedzie się, można sprawdzić szczegóły kompilacji i Dowiedz się, które jest uszkodzona.

![Powiadomienia e-mail o powodzeniu kompilacji](./media/code-test/email_build_succeed.PNG)

![Powiadomienie usługi Azure DevOps sukcesu kompilacji](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Następne kroki
* Zobacz [repozytorium Prognozowanie przychodów UCI](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) konkretne przykłady testów jednostkowych dla scenariuszy analizy danych.
* Postępuj zgodnie z poprzednim konturu i przykłady z tego scenariusza Prognozowanie przychodów UCI do własnych projektów do nauki o danych.

## <a name="references"></a>Informacje
* [Zespołowe przetwarzanie danych dla celów naukowych](https://aka.ms/tdsp)
* [Narzędzia testowania programu Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Zasoby testowania DevOps platformy Azure](https://www.visualstudio.com/team-services/)
* [Maszyny wirtualne do analizy danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
