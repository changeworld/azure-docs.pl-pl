---
title: Kod analizy danych testowania na platformie Azure za pomocą dataset UCI prognozowania przychodów dla dorosłych — proces nauki danych zespołu i Visual Studio Team Services
description: Testowanie za pomocą dane prognozowania dla dorosłych dochodu UCI kod nauki danych
services: machine-learning, team-data-science-process
documentationcenter: ''
author: weig
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2018
ms.author: weig
ms.openlocfilehash: de1ed0b85957413a254503fc72375866dfd1bea1
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837161"
---
# <a name="data-science-code-testing-with-the-uci-adult-income-prediction-dataset"></a>Testowanie za pomocą zestawu danych prognozowania dla dorosłych dochodu UCI kod nauki danych
Ten artykuł zawiera wskazówki wstępnego testowania kodu w pracy do analizy danych. Takie testowania daje analityków danych systematyczne i skuteczny sposób sprawdzania jakości i oczekiwany wynik ich kodu. Używamy zespołu danych nauki procesu (TDSP) [projektu, który korzysta z zestawu danych dla dorosłych dochodu UCI](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) opublikowaną możemy wcześniej pokazanie, jak kod będzie można wykonać testy. 

## <a name="introduction-on-code-testing"></a>Wprowadzenie testowania kodu
"Testy jednostkowe" jest dawna rozwiązaniem dla rozwoju oprogramowania. Jednak do analizy danych, często nie jest jasne, jakie środki i jak należy przetestować kodu dla różnych etapach życia analizy danych, takich jak:

* Przygotowywanie danych
* Badanie jakości danych
* Modelowanie
* Model wdrażania 

W tym artykule zastępuje termin "testów jednostkowych" z "testowania kodu." Odnosi się do testowania jako funkcje, które pomagają w celu ustalenia, czy kod kroku cykl nauki danych jest tworzenie wyniki "zgodnie z oczekiwaniami." Osoba, która jest napisanie testu definiuje, co to jest "zgodnie z oczekiwaniami," w zależności od wyniku funkcji — na przykład, kontrola jakości danych lub modelowania.

Ten artykuł zawiera odwołania do jako przydatne zasoby.

## <a name="visual-studio-team-services-for-the-testing-framework"></a>Visual Studio Team Services dla platformy testowych
W tym artykule opisano sposób wykonywania i zautomatyzowania testów za pomocą programu Visual Studio Team Services (VSTS). Możesz użyć alternatywnych narzędzi. Możemy również pokazują, jak skonfigurować automatyczne kompilacji za pomocą programu VSTS i agentów kompilacji. Agenci kompilacji używamy maszyn wirtualnych nauki danych Azure (DSVMs).

## <a name="flow-of-code-testing"></a>Przepływ testowania kodu
Ogólny przepływ pracy testowania kodu w projekcie nauki danych wygląda następująco: 

![Schemat blokowy testowania kodu](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Szczegółowe procedury

Wykonaj następujące kroki konfigurowania i uruchamiania kodu, testowania i automatyczne kompilacji za pomocą agenta kompilacji i VSTS:

1. Tworzenie projektu w aplikacji klasycznych programu Visual Studio:

    !["Tworzenie nowego projektu" ekranu w programie Visual Studio](./media/code-test/create_project.PNG)

   Po utworzeniu projektu, znajdziesz je w Eksploratorze rozwiązań w okienku po prawej stronie:
    
    ![Procedura tworzenia projektu](./media/code-test/create_python_project_in_vs.PNG)

    ![Eksplorator rozwiązań](./media/code-test/solution_explorer_in_vs.PNG)

3. Źródła danych projektu kod do repozytorium kodu projektu programu VSTS: 

    ![Repozytorium kodu projektu](./media/code-test/create_repo.PNG)

4. Załóżmy, że jego wykonaniu dodatkowych czynności przygotowywania danych, takich jak wprowadzanie danych, funkcja inżynieryjne i tworzenia etykiety kolumn. Chcesz upewnij się, że kod jest generowanie oczekiwanych wyników. Oto niektóre kod, który służy do sprawdzenia, czy kod przetwarzania danych działa prawidłowo:

    * Sprawdź, czy nazwy kolumn są prawidłowe:
    
      ![Kod do dopasowania nazwy kolumn](./media/code-test/check_column_names.PNG)

    * Sprawdź, czy poziomy odpowiedzi są prawidłowe:

      ![Kod do dopasowania Poziomy](./media/code-test/check_response_levels.PNG)

    * Sprawdź, czy procent odpowiedzi jest uzasadnione:

      ![Kod procent odpowiedzi](./media/code-test/check_response_percentage.PNG)

    * Sprawdź Brak częstotliwość każdej kolumny danych:
    
      ![Kod Brak szybkości](./media/code-test/check_missing_rate.PNG)


5. Po wykonaniu funkcji engineering pracy i przetwarzania danych i gdy udało się nauczyć model dobry, upewnij się, że model, który szkolenia można poprawnie wynik nowy zestaw danych. Za pomocą następujących dwóch testów można Sprawdź poziomy prognozowania i dystrybucji wartości etykiet:

    * Poziomy prognozowania wyboru:
    
      ![Kod do sprawdzania poziomy prognozowania](./media/code-test/check_prediction_levels.PNG)

    * Sprawdź dystrybucji prognozowania wartości:

      ![Kod do sprawdzania wartości prognozowania](./media/code-test/check_prediction_values.PNG)

6. Funkcje wszystkich testów ze sobą w skrypt w języku Python o nazwie Put **test_funcs.py**:

    ![Skrypt w języku Python dla funkcji testu](./media/code-test/create_file_test_func.PNG)


7. Po przygotowaniu są kody testu, można skonfigurować środowiska testowego w programie Visual Studio.

   Utwórz plik Python o nazwie **test1.py**. W tym pliku Utwórz klasę, która zawiera wszystkie testy, które chcesz wykonać. W poniższym przykładzie przedstawiono sześć testy przygotować:
    
    ![Plik Python z listy testów w klasie](./media/code-test/create_file_test1_class.PNG)

8. Te testy mogą być automatycznie wykrywane, jeśli umieścisz **codetest.testCase** po na nazwę klasy. Otwórz Eksploratora testów w okienku po prawej stronie, a następnie wybierz **Uruchom wszystkie**. Wszystkie testy będą wykonywane sekwencyjnie i informuje, czy test jest pomyślne.

    ![Uruchamianie testów](./media/code-test/run_tests.PNG)

9. Sprawdź kod w celu repozytorium projektu za pomocą polecenia usługi Git. Najnowsze pracy zostaną odzwierciedlone wkrótce w VSTS.

    ![Polecenia usługi Git sprawdzania w kodzie](./media/code-test/git_check_in.PNG)

    ![Najnowsze pracy w VSTS](./media/code-test/git_check_in_most_recent_work.PNG)

10. Konfigurowanie automatycznego kompilacji i testowania w VSTS:

    a. W repozytorium projektu, zaznacz **kompilacji i wydania**, a następnie wybierz **+ nowy** do utworzenia nowego procesu kompilacji.

       ![Opcje uruchamiania nowego procesu kompilacji](./media/code-test/create_new_build.PNG)

    b. Postępuj zgodnie z monitami, aby wybrać lokalizacji kodu źródłowego, nazwę projektu, repozytorium i informacji o gałęzi.
    
       ![Źródło, nazwę repozytorium i informacji o gałęzi](./media/code-test/fill_in_build_info.PNG)

    c. Wybierz szablon. Ponieważ nie istnieje żaden szablon projektu języka Python, najpierw wybrać **pusta procesu**. 

       ![Lista szablonów i przycisk "Pusty proces"](./media/code-test/start_empty_process_template.PNG)

    d. Nazwij kompilacji i wybierz agenta. Wartość domyślna w tym miejscu można wybrać, jeśli chcesz użyć DSVM na zakończenie procesu kompilacji. Aby uzyskać więcej informacji na temat ustawienia agentów, zobacz [kompilacji i wydania agentów](https://docs.microsoft.com/en-us/vsts/build-release/concepts/agents/agents?view=vsts).
    
       ![Opcje kompilacji i agenta](./media/code-test/select_agent.PNG)

    e. Wybierz **+** w okienku po lewej stronie, aby dodać zadanie dla tej fazy kompilacji. Ponieważ chcemy się uruchomić skrypt w języku Python **test1.py** na zakończenie wszystkich kontroli, to zadanie jest za pomocą polecenia programu PowerShell do uruchomienia kodu języka Python.
    
       !["Dodaj zadania" okienko przy użyciu programu PowerShell wybrane](./media/code-test/add_task_powershell.PNG)

    f. Informacje dotyczące programu PowerShell wprowadź wymagane informacje, takie jak nazwa i wersja programu PowerShell. Wybierz **wbudowanego skryptu** jako typu. 
    
       W obszarze **wbudowanego skryptu**, można wpisać **python test1.py**. Upewnij się, że zmienna środowiskowa jest poprawnie skonfigurowany dla języka Python. Jeśli potrzebujesz różnych wersji lub jądra Python, można jawnie określ ścieżkę, jak pokazano na rysunku: 
    
       ![Szczegóły programu PowerShell](./media/code-test/powershell_scripts.PNG)

    g. Wybierz **Zapisz & kolejka** na zakończenie procesu definicji kompilacji.

       ![Przycisk "Zapisz & Kolejka"](./media/code-test/save_and_queue_build_definition.PNG)

Teraz każdorazowego nowe zatwierdzenia jest przypisany do repozytorium kodu, proces kompilacji zostanie uruchomiony automatycznie. (W tym miejscu możemy użyć wzorca jako repozytorium, ale można zdefiniować oddziałów). Uruchamia proces **test1.py** pliku na maszynie agenta, aby upewnić się, że wszystkie elementy zdefiniowane w kodzie działa poprawnie. 

Jeśli alerty są poprawnie skonfigurowane, zostanie wyświetlone powiadomienie w wiadomości e-mail po zakończeniu kompilacji. Można również sprawdzić stan kompilacji w VSTS. Jeśli nie powiedzie się, można sprawdzić szczegóły kompilacji i dowiedzieć się, które zostało przerwane.

![Powiadomienia e-mail o Powodzenie kompilacji](./media/code-test/email_build_succeed.PNG)

![Powiadomienia programu VSTS sukcesu kompilacji](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [UCI dochodu prognozowania repozytorium](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) konkretnych przykłady testów jednostkowych dla scenariuszy analizy danych.
* Postępuj zgodnie z poprzednim konspektu i przykłady z scenariusza prognozowania przychodów UCI do własnych projektów analizy danych.

## <a name="references"></a>Dokumentacja
* [Zespołowe przetwarzanie danych dla celów naukowych](https://aka.ms/tdsp)
* [Narzędzia testowania programu Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [VSTS testowania zasobów](https://www.visualstudio.com/team-services/)
* [Maszyny wirtualne nauki danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)