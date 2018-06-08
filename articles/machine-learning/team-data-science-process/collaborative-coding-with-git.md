---
title: Kodowanie współpracy z usługą Git — usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jak przeprowadzić programowanie zespołowe kodu dla projektów nauki danych z planowania elastycznego za pomocą narzędzia Git.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: abb1c7a3f597804a84f06462b1e50bb5a63fb9b3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837365"
---
# <a name="collaborative-coding-with-git"></a>Wspólne kodowanie przy użyciu narzędzia Git

W tym artykule możemy wyjaśniają sposób wykonywania programowanie zespołowe kodu dla projektów analizy danych przy użyciu narzędzia Git jako platforma programistyczna udostępnionego kodu. Uwzględniono również sposób połączyć kodowania działań do pracy planowane w [elastyczne programowanie](agile-development.md) oraz sposób przeglądami kodu.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Łączenie elementu roboczego z gałęzi Git 

VSTS oferują wygodny sposób nawiązywania połączenia elementu pracy (wątku lub zadania) z gałęzi Git. Dzięki temu można połączyć z wątku lub zadanie bezpośrednio do kodu skojarzonego z nim. 

Połącz się nowa gałąź z elementu roboczego, kliknij dwukrotnie element roboczy, a w wyskakującym oknie kliknij **utworzyć nową gałąź** w obszarze **+ Dodaj**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Podaj informacje dotyczące nowej gałęzi, takie jak nazwa gałęzi, podstawowe repozytorium Git i oddziału. Repozytorium Git wybrany musi być repozytorium w ramach tego samego projektu zespołowego, należącego do elementu roboczego. Podstawowy gałęzi może być gałęzi głównej lub istniejących gałęzi.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Dobrym rozwiązaniem jest tworzenie gałęzi Git dla każdego elementu roboczego wątku. Następnie dla każdego elementu roboczego zadania, utwórz gałąź oparte na gałąź wątku. Organizowanie gałęzie w ten sposób hierarchiczne, umożliwiająca relacje wątku zadanie jest przydatne, gdy masz wiele osób pracuje różnych scenariuszy tego samego projektu lub ma wiele osób pracuje różne zadania tego samego wątku. Podczas każdego członka zespołu działa w różnych gałęzi i każdy element członkowski działa w różnych kodów lub pozostałych artefaktów, podczas udostępniania gałąź, można zminimalizować konflikty. 

Poniższej ilustracji przedstawiono zalecane strategii rozgałęziania dla TDSP. Nie trzeba wiele gałęzi, jak przedstawiono poniżej, zwłaszcza jeśli masz tylko jedną lub dwie osoby działa na tym samym projekcie lub tylko jedna osoba działa we wszystkich zadaniach wątku. Ale rozdzielających gałęzi programowanie z gałęzi głównej jest zawsze dobrym rozwiązaniem. Może to zapobiec gałęzi wersji przez zadania programistyczne. Bardziej szczegółowy opis modelu gałęzi Git znajdują się w [A pomyślnie Git rozgałęzianie modelu](http://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Aby przejść do gałęzi, do której chcesz pracować na, uruchom następujące polecenie w polecenia powłoki (Windows lub Linux). 

    git checkout <branch name>

Zmiana *< Nazwa gałęzi\>*  do **wzorca** przełączników z powrotem do **wzorca** gałęzi. Po przełączeniu do gałęzi roboczej, można rozpocząć pracy na dany element roboczy, tworzenie artefaktów kodu lub dokumentacji, wymaganego do ukończenia elementu. 

Możesz również połączyć elementu roboczego się na istniejącą. W **szczegółów** strony elementu roboczego, zamiast klikać **utworzyć nową gałąź**, możesz kliknąć przycisk **+ Dodaj**. Wybierz gałąź, który chcesz połączyć elementu roboczego do. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Można również utworzyć nową gałąź w Git Bash poleceń. Jeśli < Nazwa gałęzi podstawowej\> brakuje, < Nowa nazwa gałęzi\> opiera się na _wzorca_ gałęzi. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Pracy w gałęzi i zatwierdzić zmiany 

Teraz załóżmy, że niektóre zmiany do *danych\_wprowadzanie* gałęzi dla elementu roboczego, takie jak dodanie pliku R w gałęzi w komputerze lokalnym. Można zatwierdzić R pliku dodanego do gałęzi dla tego elementu roboczego, pod warunkiem jesteś w oddziale w powłoce programu Git za pomocą następujących poleceń Git:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Utwórz żądanie ściągnięcia na VSTS 

Gdy wszystko będzie gotowe po kilku zatwierdzeniami i wypchnięć, do scalenia bieżącej gałęzi w swojej gałęzi podstawowej, można kierować **żądania ściągnięcia** na serwerze programu VSTS. 

Przejdź do strony głównej projektu zespołowego, a następnie kliknij przycisk **kod**. Wybierz gałąź do scalenia i nazwę repozytorium Git do scalenia gałęzi do. Następnie kliknij przycisk **żądania ściągnięcia**, kliknij przycisk **nowe żądanie ściągnięcia** tworzenie Przegląd żądania ściągnięcia, przed scalonego pracy w gałęzi do jego podstawowej gałęzi.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Wypełnij niektóre opis tego żądania ściągnięcia, dodawać recenzentów i wysłać go.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Przejrzyj i dokonać scalania 

Po utworzeniu żądania ściągania recenzentów powiadomienie e-mail do przeglądania żądania ściągnięcia. Recenzentów, należy sprawdzić, czy zmiany pracy lub nie, a jeśli to możliwe testowanie zmian z obiektu żądającego. Na podstawie ich oceny, recenzentów można zatwierdzić lub odrzucić żądanie ściągnięcia. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

Po wykonaniu czynności przeglądu gałąź roboczą scalonego jego podstawowej gałęzi, klikając **Complete** przycisku. Możesz usunąć gałąź roboczą, po jego został scalony. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Upewnij się, w lewym górnym rogu, że żądanie jest oznaczony jako **UKOŃCZONO**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Jeśli możesz powrócić do repozytorium, w obszarze **kod**, pojawi się informacja, że użytkownik został przełączony do głównej gałęzi.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Można także użyć następujących poleceń Git gałąź pracy do swojej gałęzi podstawowej i Usuń gałąź pracy po scaleniu:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>Kolejne kroki

[Wykonywanie zadań nauki danych](execute-data-science-tasks.md) pokazano, jak wykonać kilka typowych danych nauki zadań, takich jak interakcyjną eksplorację, analizy danych raportowania i tworzenia modelu przy użyciu narzędzia.

Wskazówki, które pokazują wszystkie kroki procesu **określonych scenariuszy** podawane są również. Wymieniono i połączone z opisami miniatur w [wskazówki przykład](walkthroughs.md) artykułu. Pokazują one sposób łączenia chmury, narzędzia lokalnych i usług w przepływie pracy lub potoku, aby utworzyć aplikację inteligentnego. 

