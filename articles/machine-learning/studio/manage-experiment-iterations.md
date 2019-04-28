---
title: Zarządzanie iteracjami eksperymentów
titleSuffix: Azure Machine Learning Studio
description: Jak zarządzanie iteracjami eksperymentów w usłudze Azure Machine Learning Studio. Możesz przejrzeć poprzednich przebiegów eksperymenty, w dowolnym momencie, aby wyzwanie, ponownie oraz ostatecznie upewnij się lub Popraw poprzednie założeń.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 34a72f2e7b6be90654c0f053d5b8978b0283d56c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860260"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio"></a>Zarządzanie iteracjami eksperymentów w usłudze Azure Machine Learning Studio
Tworzenie modelu analizy predykcyjnej jest procesem iteracyjnym - miarę modyfikowania różnych funkcji i parametrów eksperymentu, wyniki zbiegają się do momentu, aż czy masz wyuczonego, skutecznego modelu. Klucz do tego procesu służy do śledzenia różne iteracje eksperymentu, parametry i konfiguracji.



Możesz przejrzeć poprzednich przebiegów eksperymenty, w dowolnym momencie, aby wyzwanie, ponownie oraz ostatecznie upewnij się lub Popraw poprzednie założeń. Po uruchomieniu eksperymentu usługi Machine Learning Studio przechowuje historię wykonywania, łącznie z zestawu danych, moduł i port połączenia i parametry. Ta historia przechwytuje także wyniki, środowisko uruchomieniowe informacje, takie jak uruchamianie i zatrzymywanie razy, komunikaty dziennika i stan wykonania. Można spojrzeć Wstecz w dowolnym z tych przebiegów w dowolnym momencie, aby zapoznać się z chronologii eksperyment i wyników pośrednich. Nawet służy poprzedniego uruchomienia eksperymencie można uruchomić w nowej fazy zapytania i odnajdywania na ścieżce do utworzenia prostej, złożonych lub nawet zespołu modelowania rozwiązania.

> [!NOTE]
> Po wyświetleniu poprzedniego uruchomienia eksperymentu tej wersji eksperymentu jest zablokowana i nie można edytować. Można jednak zapisać kopię, klikając **SAVE AS** i podając nazwę nowej kopii. Usługa Machine Learning Studio zostanie otwarta nowa kopia, której można edytować i uruchomić. Tę kopię eksperymentu jest dostępna w **EKSPERYMENTÓW** listy oraz wszystkich innych eksperymenty.
> 
> 

## <a name="viewing-the-prior-run"></a>Wyświetlanie poprzedniego uruchomienia
Jeśli masz otwartą eksperymentu, uruchamianego co najmniej raz, poprzedniego uruchomienia eksperymentu można wyświetlić, klikając **wcześniejszego uruchomienia** w okienku właściwości.

Załóżmy na przykład, możesz utworzyć eksperyment i wersjami go o 11:23 11:42, a 11:55. Jeśli otworzysz ostatni przebieg eksperymentu (11:55) i kliknij przycisk **wcześniejszego uruchomienia**, wersja uruchomiono o 11:42 jest otwarty.

## <a name="viewing-the-run-history"></a>Wyświetlanie historii uruchamiania
Wszystkich poprzednich przebiegów eksperymentu można wyświetlić, klikając **Widok historii uruchamiania** w eksperymencie open.

Załóżmy na przykład, utworzyć eksperyment za pomocą [regresji liniowej] [ linear-regression] modułu i chcesz obserwować efekt zmiany wartości **tempo uczenia** na użytkownika wyniki eksperymentu. Możesz uruchomić eksperyment wiele razy z różnymi wartościami dla tego parametru, w następujący sposób:

| Wartość stawka nauki | Godzina rozpoczęcia uruchamiania |
| --- | --- |
| 0.1 |9/11/2014 4:18:58 pm |
| 0.2 |9/11/2014 4:24:33 pm |
| 0.4 |9/11/2014 4:28:36 pm |
| 0,5 |9/11/2014 4:33:31 pm |

Jeśli klikniesz **widok HISTORII URUCHAMIANIA**, możesz wyświetlić listę wszystkich tych przebiegów:

![Przykład historii uruchamiania](./media/manage-experiment-iterations/viewrunhistory.jpg)

Kliknij dowolny z tych przebiegów, aby wyświetlić migawkę eksperymentu w momencie uruchomienia. Konfiguracja, wartości parametrów, komentarze i wyniki są zachowywane zapewnienie pełnego rekordu z programem eksperymentu.

> [!TIP]
> Dokumentować swoje iteracje eksperymentu, można zmodyfikować tytuł każdorazowo zostanie uruchomiony, możesz zaktualizować **Podsumowanie** eksperymentu we właściwościach okienku, a można dodać lub zaktualizować komentarze, w przypadku poszczególnych modułów, aby zarejestrować swoje zmiany. Komentarze title, summary i moduł są zapisywane przy każdym uruchomieniu eksperymentu.
> 
> 

Lista eksperymenty w **EKSPERYMENTÓW** kartę w usłudze Machine Learning Studio zawsze zawiera najnowszą wersję eksperymentu. Jeśli otworzysz poprzedniego uruchomienia eksperymentu (przy użyciu **wcześniejszego uruchomienia** lub **widok HISTORII URUCHAMIANIA**), można powrócić do wersji roboczej, klikając **widok HISTORII URUCHAMIANIA** i wybierając polecenie iteracji, która ma **stanu** z **edytowalna**.

## <a name="iterating-on-a-previous-run"></a>Iteracja na poprzedniego uruchomienia
Po kliknięciu **wcześniejszego uruchomienia** lub **widok HISTORII URUCHAMIANIA** i otwórz poprzedniego uruchomienia, Zakończono eksperymentu można wyświetlić w trybie tylko do odczytu.

Jeśli chcesz rozpocząć iterację eksperymentu, począwszy od sposób został skonfigurowany dla poprzedniego uruchomienia, można to zrobić, otwierając przebiegu i klikając **SAVE AS**. Tworzy nowy eksperyment z tytułem nowy, pusty historii, uruchamiania i wszystkie składniki i wartości parametrów poprzedniego uruchomienia. Ten nowy eksperyment znajduje się w **EXPERIMENTS** karcie Strona główna usługi Machine Learning Studio, na które można modyfikować i uruchom go, inicjowania nowego uruchamianie historii dla tej iteracji eksperymentu. 

Na przykład załóżmy, że masz eksperymentu pokazano w poprzedniej sekcji historii uruchamiania. Chcesz obserwować, co się stanie po ustawieniu **tempo uczenia** parametr 0,4 i spróbuj różnych wartości **liczba epok szkolenia** parametru.

1. Kliknij przycisk **widok HISTORII URUCHAMIANIA** , a następnie otwórz iterację eksperymentu, który uruchomił godzinie 4:28:36 (w którym wartość parametru do 0,4).
2. Kliknij przycisk **SAVE AS**.
3. Wprowadź nowy tytuł, a następnie kliknij przycisk **OK** znacznik wyboru. Utworzono nową kopię eksperymentu.
4. Modyfikowanie **liczba epok szkolenia** parametru.
5. Kliknij przycisk **Uruchom**.

Możesz nadal do zmodyfikowania, a następnie uruchom tę wersję eksperymentu, tworzenie nowych historii przebiegów, aby zarejestrować swoją pracę.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
