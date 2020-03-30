---
title: Zarządzanie iteracjami eksperymentów
titleSuffix: ML Studio (classic) - Azure
description: Jak zarządzać iteracjami eksperymentów w usłudze Azure Machine Learning Studio (klasycznym). Możesz przejrzeć poprzednie przebiegi eksperymentów w dowolnym momencie, aby zakwestionować, ponownie sprawdzić i ostatecznie potwierdzić lub udoskonalić wcześniejsze założenia.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 5c8a278f09fdb3b605020e4c2fcf7aa2776906e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204311"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Zarządzanie iteracjami eksperymentów w usłudze Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Tworzenie modelu analizy predykcyjnej jest procesem iteracyjnym — podczas modyfikowania różnych funkcji i parametrów eksperymentu wyniki zbiegają się, dopóki nie upewnisz się, że masz wyszkolony, skuteczny model. Kluczem do tego procesu jest śledzenie różnych iteracji parametrów i konfiguracji eksperymentu.



Możesz przejrzeć poprzednie przebiegi eksperymentów w dowolnym momencie, aby zakwestionować, ponownie sprawdzić i ostatecznie potwierdzić lub udoskonalić wcześniejsze założenia. Po uruchomieniu eksperymentu, Machine Learning Studio (classic) przechowuje historię uruchomienia, w tym zestaw danych, moduł i połączenia portów i parametry. Ta historia przechwytuje również wyniki, informacje o czasie wykonywania, takie jak czas rozpoczęcia i zatrzymania, komunikaty dziennika i stan wykonania. Możesz spojrzeć wstecz na dowolne z tych przebiegów w dowolnym momencie, aby przejrzeć chronologię eksperymentu i wyniki pośrednie. Można nawet użyć poprzedniej serii eksperymentu, aby rozpocząć nową fazę dochodzenia i odnajdywania na swojej drodze do tworzenia prostych, złożonych, a nawet zespołowych rozwiązań modelowania.

> [!NOTE]
> Podczas wyświetlania poprzedniego przebiegu eksperymentu ta wersja eksperymentu jest zablokowana i nie można jej edytować. Można jednak zapisać jego kopię, klikając przycisk **ZAPISZ JAKO** i podając nową nazwę kopii. Machine Learning Studio (klasyczny) otwiera nową kopię, którą można następnie edytować i uruchomić. Ta kopia eksperymentu jest dostępna na liście **EKSPERYMENTY** wraz ze wszystkimi innymi eksperymentami.
> 
> 

## <a name="viewing-the-prior-run"></a>Wyświetlanie poprzedniego uruchomienia
Po otwarciu eksperymentu, który został uruchomiony co najmniej raz, można wyświetlić poprzednie uruchomienie eksperymentu, klikając **prior run** w okienku właściwości.

Załóżmy na przykład, że tworzysz eksperyment i uruchamiasz jego wersje w 11:23, 11:42 i 11:55. Jeśli otworzysz ostatni przebieg eksperymentu (11:55) i klikniesz **opcję Przed uruchomieniem,** zostanie otwarta wersja 11:42.

## <a name="viewing-the-run-history"></a>Wyświetlanie historii biegu
Wszystkie poprzednie przebiegi eksperymentu można wyświetlić, klikając pozycję **Wyświetl historię przebiegu** w otwartym eksperymencie.

Załóżmy na przykład, że tworzysz eksperyment z modułem [regresji liniowej][linear-regression] i chcesz zaobserwować wpływ zmiany wartości **learning rate** na wyniki eksperymentu. Eksperyment można uruchomić wiele razy z różnymi wartościami dla tego parametru, w następujący sposób:

| Wartość szybkości uczenia się | Czas rozpoczęcia biegu |
| --- | --- |
| 0.1 |9/11/2014 16:18:58 |
| 0.2 |9/11/2014 16:24:33 |
| 0,4 |9/11/2014 16:28:36 |
| 0,5 |9/11/2014 16:33:31 |

Po kliknięciu **przycisku WYŚWIETL HISTORIĘ PRZEBIEGu**zostanie wyświetlona lista wszystkich tych przebiegów:

![Przykładowa historia uruchamiania](./media/manage-experiment-iterations/viewrunhistory.jpg)

Kliknij dowolny z tych przebiegów, aby wyświetlić migawkę eksperymentu w momencie jego łowania. Konfiguracja, wartości parametrów, komentarze i wyniki są zachowywane, aby uzyskać pełny rekord tego przebiegu eksperymentu.

> [!TIP]
> Aby udokumentować iteracje eksperymentu, można zmodyfikować tytuł przy każdym uruchomieniu, zaktualizować **podsumowanie** eksperymentu w okienku właściwości i dodać lub zaktualizować komentarze do poszczególnych modułów, aby zarejestrować zmiany. Komentarze do tytułu, podsumowania i modułu są zapisywane przy każdym uruchomieniu eksperymentu.
> 
> 

Lista eksperymentów na karcie **EKSPERYMENTY** w programie Machine Learning Studio (klasycznym) zawsze wyświetla najnowszą wersję eksperymentu. Jeśli otworzysz poprzedni przebieg eksperymentu (przy użyciu **poprzedniego uruchomienia** lub wyświetl **historię przebiegu),** możesz powrócić do wersji roboczej, klikając **pozycję WYŚWIETL HISTORIĘ PRZEBIEGU** i wybierając iterację, która ma **stan** **edytowalny**.

## <a name="iterating-on-a-previous-run"></a>Iteracja w poprzednim biegu
Po kliknięciu **przycisku Przed uruchomieniem** lub **wyświetleniu historii przebiegu** i otwarciu poprzedniego uruchomienia można wyświetlić gotowy eksperyment w trybie tylko do odczytu.

Jeśli chcesz rozpocząć iterację eksperymentu, zaczynając od sposobu skonfigurowania go do poprzedniego uruchomienia, możesz to zrobić, otwierając bieg i klikając przycisk **ZAPISZ JAKO**. Spowoduje to utworzenie nowego eksperymentu z nowym tytułem, pustą historią uruchamiania oraz wszystkimi wartościami składników i parametrów poprzedniego uruchomienia. Ten nowy eksperyment jest wyświetlany na karcie **EKSPERYMENTY** na stronie głównej usługi Machine Learning Studio (klasyczna) i można go modyfikować i uruchamiać, inicjując nową historię uruchamiania dla tej iteracji eksperymentu. 

Załóżmy na przykład, że historia uruchamiania eksperymentu jest wyświetlana w poprzedniej sekcji. Chcesz obserwować, co się dzieje po **ustawieniu learning rate** parametr na 0,4 i spróbuj różnych wartości dla **Liczba epok szkolenia** parametru.

1. Kliknij **pozycję WYŚWIETL HISTORIĘ PRZEBIEGu** i otwórz iterację eksperymentu uruchomionego o godzinie 16:28:36 (w którym ustawiono wartość parametru na 0,4).
2. Kliknij **pozycję ZAPISZ JAKO**.
3. Wprowadź nowy tytuł i kliknij znacznik wyboru **OK.** Zostanie utworzona nowa kopia eksperymentu.
4. Zmodyfikuj parametr **Liczba epok treningowych.**
5. Kliknij **przycisk URUCHOM**.

Teraz możesz kontynuować modyfikowanie i uruchamianie tej wersji eksperymentu, budując nową historię przebiegu w celu zarejestrowania swojej pracy.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
