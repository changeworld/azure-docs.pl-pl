---
title: Elastyczne rozwoju projektów nauki danych - Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jak deweloperzy mogą wykonywać projektu analizy danych w wersji systematyczne, kontrolowane i sposób współpracy w zespole projektu przy użyciu procesu nauki danych zespołu.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: 03bc287969559308e743a5b8cc192cddfdc085cc
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="agile-development-of-data-science-projects"></a>Elastyczne programowanie projektów analizy danych

W tym dokumencie opisano, jak deweloperzy wykonywać projektu analizy danych w systematyczne, niepoddany kontroli wersji i sposób współpracy w zespole projektu za pomocą [proces nauki danych zespołu](overview.md) (TDSP). TDSP jest opracowanym przez firmę Microsoft, zapewniająca strukturalnych sekwencji działań do wykonania wydajne rozwiązania oparte na chmurze analizy predykcyjnej. Aby schematem ról pracowników i ich skojarzonych zadań, które są obsługiwane przez zespół nauki danych standaryzacji w tym procesie, zobacz [proces nauki danych zespołu ról i zadań](roles-tasks.md). 

Ten artykuł zawiera instrukcje dotyczące sposobu: 

1. czy **planowanie przebiegu** dla elementów roboczych związane z projektem.<br> Jeśli użytkownik nie zna planowanie przebiegu, można znaleźć szczegółowe informacje ogólne i [tutaj](https://en.wikipedia.org/wiki/Sprint_(software_development) "tutaj"). 
2. **Dodawanie elementów roboczych** do przebiegów. 

> [!NOTE]
> Kroki niezbędne do skonfigurowania środowiska zespołu TDSP, za pomocą programu Visual Studio Team Services (VSTS) są opisane w następujących zbiór instrukcji. Określa sposób wykonać te zadania z programu VSTS, ponieważ jest to implementowania TDSP firmy Microsoft.  Jeśli zdecydujesz się na używanie programu VSTS, elementy (3) i (4) na poprzedniej liście są korzyści, które otrzymujesz w sposób naturalny. Jeśli inny kod obsługujący platformy jest używany dla tej grupy, zadania, które muszą zostać wykonane przez realizacji zespołu zwykle nie należy zmieniać. Jednak sposób do wykonania tych zadań ma być różne. Na przykład element w sekcji sześciu, **Link elementu roboczego z gałęzi Git**, nie może być łatwym znajduje się na usługi VSTS.
>
>

Na poniższym rysunku przedstawiono typowe przebiegu planowania, kodowanie i związane z kontroli źródła przepływu pracy w realizacji projektu nauki danych:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologia 

Wypalenie w ramach planowania w TDSP, istnieją cztery typy często używanych **elementy robocze**: **funkcji**, **scenariusza**, **zadań**, i **Usterki**. Każdego projektu zespołowego obsługuje pojedynczy zaległości dla wszystkich elementów roboczych. Nie występuje Zaległość na poziomie repozytorium Git w projekcie zespołowym. Oto ich definicje:

- **Funkcja**: funkcja odpowiada zaangażowania projektu. Różne korzystają z klientem są traktowane jako różne funkcje. Podobnie warto wziąć pod uwagę różne fazy projektu za pomocą klienta jako różne funkcje. Jeśli wybierzesz takie jak schemat ***ClientName EngagementName*** nazwę funkcje, następnie możesz łatwo rozpoznać kontekstu projektu/zaangażowania z samych nazw.
- **Wątek**: wątki są elementy robocze różnych, które są niezbędne do ukończenia funkcji (projekt) end-to-end. Przykłady scenariuszy:
    - Pobieranie danych 
    - Eksplorowanie danych 
    - Generowanie funkcji
    - Tworzenie modeli
    - Modele operacyjnych 
    - Ponownego trenowania modeli
- **Zadanie**: zadania są elementy pracy można przypisać kodu lub dokument lub innych działań, które należy wykonać w celu przeprowadzenia określonego wątku. Na przykład zadania w wątku *pobierania danych* można:
    -  Uzyskiwanie poświadczeń programu SQL Server 
    -  Przekazywanie danych do usługi SQL Data Warehouse. 
- **Błąd**: błędy zazwyczaj odwoływać się do poprawki, które są potrzebne dla istniejącego kodu lub które są wykonywane po zakończeniu zadania. Jeśli ten błąd jest spowodowany przez odpowiednio Brak etapów lub zadań, można przekazać do artykułu lub zadania. 

> [!NOTE]
> Pojęcia dotyczące pobierają są funkcje, wątki, zadań i błędów z zarządzania kodu oprogramowania (SCM), który ma być używany podczas analizy danych. One może różnić się nieznacznie od ich z konwencjonalnej definicje SCM.
>
>

> [!NOTE]
> Analityków danych może bezpiecznie więcej przy użyciu szablonu usługi agile, w szczególności wyrównany z TDSP etapy cyklu życia. Z tym pamiętać został utworzony szablon planowania przebiegu pochodnych Agile, gdzie Epics itp. wątki są zastępowane etapy cyklu życia TDSP lub substages. Aby uzyskać instrukcje dotyczące sposobu tworzenia szablonu agile, zobacz [proces nauki agile danych w Visual Studio Online](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Planowanie przebiegu 

Planowanie przebiegu jest przydatne w przypadku priorytetów projektu i planowania zasobów i alokacji. Prowadzi wielu analityków danych z wielu projektów, z których każdy może zająć miesięcy, aby zakończyć. Projekty często realizowane w różnych nadać tempo. Na serwerze programu VSTS można łatwo utworzyć, zarządzać i śledzenia elementów roboczych w projekcie zespołowym i przeprowadzić przebiegu planowania zapewnienie przechodzenia do przodu, zgodnie z oczekiwaniami projektów. 

Postępuj zgodnie z [to łącze](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) instrukcje krok po kroku w przebiegu planowania w VSTS. 


## 3. <a name='AddFeature-3'></a>Dodawanie funkcji  

Po utworzeniu repozytorium projektu w projekcie zespołowym, przejdź do zespołu **omówienie** i kliknij przycisk **zarządzać pracą**.

![2](./media/agile-development/2-sprint-team-overview.png)

Aby dołączyć funkcji zaległości, kliknij przycisk **zaległości** --> **funkcje** --> **nowy**, typ funkcji **tytuł**(zazwyczaj nazwę projektu), a następnie kliknij przycisk **Dodaj** .

![3](./media/agile-development/3-sprint-team-add-work.png)

Kliknij dwukrotnie funkcji, który został utworzony. Wypełnij opisy, przypisz członków zespołu dla tej funkcji i ustaw planowania parametrów dla tej funkcji. 

Ta funkcja może także połączyć z repozytorium projektu. Kliknij przycisk **Dodaj** w obszarze **programowanie** sekcji. Po zakończeniu edycji funkcji, kliknij przycisk **Zapisz i Zamknij** aby zakończyć.


## 4. <a name='AddStoryunderfeature-4'></a>Dodaj wątku w funkcji 

W funkcji można dodać wątków do opisano najważniejsze czynności wymagane do zakończenia projektu (funkcja). Aby dodać nowy artykuł, kliknij przycisk **+** Zaloguj się na lewo od funkcji w widoku listy prac.  

![4](./media/agile-development/4-sprint-add-story.png)

Można edytować szczegóły wątku, takie jak stan, opis, komentarze, planowania i priorytetu w oknie podręcznym.

![5](./media/agile-development/5-sprint-edit-story.png)

Ten artykuł można połączyć istniejące repozytorium, klikając **+ Dodaj** w obszarze **programowanie**. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Dodaj zadanie do wątku 

Zadania są określone szczegółowy opis kroków, które są niezbędne do ukończenia każdego scenariusza. Po zakończeniu wszystkich zadań wątku zbyt należy wypełnić wątku. 

Można dodać zadania do artykułu, kliknij przycisk **+** znak obok elementu scenariusza, wybierz opcję **zadań**, a następnie wypełnij szczegółowe informacje tego zadania w wyskakującym oknie.

![7](./media/agile-development/7-sprint-add-task.png)

Po utworzeniu funkcji, scenariuszy i zadań, można wyświetlić je w **zaległości** lub **tablicy** widoków, które można śledzić jego stan.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a> Przygotuj szablon pracy Agile TDSP w Visual Studio Online

W tym artykule opisano sposób konfigurowania nauki szablonu procesu zwinnego danych, który używa etapy cyklu życia analizy danych TDSP i śledzenie elementów pracy za pomocą programu Visual Studio Online (vso). Szablonu procesu czynności krokach związanych z przykładem konfigurowania danych nauki specyficzne dla agile *AgileDataScienceProcess* i pokazują, jak tworzyć elementy pracy nauki danych na podstawie szablonu.

### <a name="agile-data-science-process-template-setup"></a>Ustawienia szablonu procesu nauki danych agile

1. Przejdź do strony głównej serwera, **Konfiguruj** -> **procesu**.

    ![10](./media/agile-development/10-settings.png) 

2. Przejdź do **wszystkie procesy** -> **procesów**w obszarze **Agile** i wybierz polecenie **Utwórz dziedziczone procesu**. Następnie przełącz nazwa procesu "AgileDataScienceProcess" i kliknij przycisk **utworzyć procesu**.

    ![11](./media/agile-development/11-agileds.png)

3. W obszarze **AgileDataScienceProcess** -> **typów elementów roboczych** Wyłącz **Epic**, **funkcji**,  **Scenariusza**, i **zadań** typów przez elementów roboczych **Disable -> Konfiguracja**

    ![12](./media/agile-development/12-disable.png)

4. Przejdź do **AgileDataScienceProcess** -> **zaległości poziomy** kartę. Zmień nazwę "Epics" do "TDSP projektów" klikając **Konfiguruj** -> **edycji/Zmień nazwę**. W tym samym oknie dialogowym kliknij **+ nowy typ elementu roboczego** w "Projekt nauki danych" i ustaw wartość **domyślnego typu elementu roboczego** "TDSP projektu" 

    ![13](./media/agile-development/13-rename.png)  

5. Podobnie, Zmień nazwę zaległości "Funkcji" do "TDSP etapów" i Dodaj następujący kod do **typ elementu roboczego nowych**:

    - Poznawanie firmy
    - Uzyskiwanie danych
    - Modelowanie
    - Wdrożenie

6. Zmień nazwę "Scenariusza" na "TDSP Substages" z domyślnego typu elementu roboczego typu nowo utworzony "TDSP Substage".

7. Ustaw "Zadania" do nowo utworzony typ elementu roboczego "TDSP zadania" 

8. Po wykonaniu tych kroków poziomy zaległości powinien wyglądać następująco:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Tworzenie elementów roboczych nauki danych

Po utworzeniu szablonu procesu nauki danych można tworzyć i śledzenia elementów roboczych nauki danych odpowiadające TDSP cykl życia.

1. Podczas tworzenia nowego projektu zespołowego, wybierz "Agile\AgileDataScienceProcess" jako **elementów roboczych procesu**:

    ![15](./media/agile-development/15-newproject.png)

2. Przejdź do projektu zespołowego nowo utworzony, a następnie kliknij polecenie **pracy** -> **zaległości**.

3. Wyświetlić, klikając "TDSP projektów" **ustawień zespołu** i sprawdź "TDSP projektów"; Zapisz.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Teraz można przystąpić do tworzenia elementów roboczych nauki określonych danych.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Poniżej przedstawiono przykładowy sposób wyświetlania elementów roboczych danych nauki projektu:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Kolejne kroki

[Kodowanie współpracy z usługą Git](collaborative-coding-with-git.md) opisuje czy programowanie zespołowe kodu dla projektów analizy danych przy użyciu narzędzia Git jako platforma programistyczna udostępnionego kodu oraz połączyć działań do pracy planowane z procesu zwinnego programowania.

Poniżej przedstawiono dodatkowe linki do zasobów na elastyczne procesów.

- Procesu agile   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Typy elementów roboczych procesu agile i przepływ pracy   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Wskazówki, które pokazują wszystkie kroki procesu **określonych scenariuszy** podawane są również. Wymieniono i połączone z opisami miniatur w [wskazówki przykład](walkthroughs.md) artykułu. Pokazują one sposób łączenia chmury, narzędzia lokalnych i usług w przepływie pracy lub potoku, aby utworzyć aplikację inteligentnego. 
