---
title: Zarządzanie iteracjami eksperymentów
titleSuffix: ML Studio (classic) - Azure
description: Jak zarządzać iteracjami eksperymentów w Azure Machine Learning Studio (klasyczny). Możesz przejrzeć poprzednich przebiegów eksperymenty, w dowolnym momencie, aby wyzwanie, ponownie oraz ostatecznie upewnij się lub Popraw poprzednie założeń.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: e2e13942b4ada2c6e90a056a0239bc620951638b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168492"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Zarządzanie iteracjami eksperymentów w Azure Machine Learning Studio (klasyczny)
Tworzenie modelu analizy predykcyjnej jest procesem iteracyjnym - miarę modyfikowania różnych funkcji i parametrów eksperymentu, wyniki zbiegają się do momentu, aż czy masz wyuczonego, skutecznego modelu. Klucz do tego procesu służy do śledzenia różne iteracje eksperymentu, parametry i konfiguracji.



Możesz przejrzeć poprzednich przebiegów eksperymenty, w dowolnym momencie, aby wyzwanie, ponownie oraz ostatecznie upewnij się lub Popraw poprzednie założeń. Po uruchomieniu eksperymentu Machine Learning Studio (klasyczny) zachowuje historię przebiegu, w tym połączenia zestawów danych, modułów i portów oraz parametry. Ta historia przechwytuje także wyniki, środowisko uruchomieniowe informacje, takie jak uruchamianie i zatrzymywanie razy, komunikaty dziennika i stan wykonania. Można spojrzeć Wstecz w dowolnym z tych przebiegów w dowolnym momencie, aby zapoznać się z chronologii eksperyment i wyników pośrednich. Nawet służy poprzedniego uruchomienia eksperymencie można uruchomić w nowej fazy zapytania i odnajdywania na ścieżce do utworzenia prostej, złożonych lub nawet zespołu modelowania rozwiązania.

> [!NOTE]
> Po wyświetleniu poprzedniego uruchomienia eksperymentu tej wersji eksperymentu jest zablokowana i nie można edytować. Można jednak zapisać jego kopię, klikając pozycję **Zapisz jako** i podając nową nazwę kopii. Machine Learning Studio (klasyczny) otwiera nową kopię, którą można następnie edytować i uruchamiać. Ta kopia eksperymentu jest dostępna na liście **eksperymentów** wraz ze wszystkimi innymi eksperymentami.
> 
> 

## <a name="viewing-the-prior-run"></a>Wyświetlanie poprzedniego uruchomienia
Jeśli masz otwarty eksperyment, który jest uruchamiany co najmniej raz, możesz wyświetlić poprzedni przebieg eksperymentu, klikając polecenie **poprzednie uruchomienie** w okienku właściwości.

Załóżmy na przykład, możesz utworzyć eksperyment i wersjami go o 11:23 11:42, a 11:55. W przypadku otwarcia ostatniego uruchomienia eksperymentu (11:55) i kliknięcia polecenia **poprzedni przebiegu**zostanie otwarta wersja uruchomiona o 11:42.

## <a name="viewing-the-run-history"></a>Wyświetlanie historii uruchamiania
Aby wyświetlić wszystkie poprzednie uruchomienia eksperymentu, kliknij pozycję **Wyświetl historię uruchamiania** w otwartym eksperymentie.

Załóżmy na przykład, że tworzysz eksperyment z modułem [regresja liniowa][linear-regression] i chcesz obserwować wpływ zmiany wartości **kursu szkoleniowego** na wyniki eksperymentu. Możesz uruchomić eksperyment wiele razy z różnymi wartościami dla tego parametru, w następujący sposób:

| Wartość stawka nauki | Godzina rozpoczęcia uruchamiania |
| --- | --- |
| 0.1 |9/11/2014 4:18:58 pm |
| 0.2 |9/11/2014 4:24:33 pm |
| 0,4 |9/11/2014 4:28:36 pm |
| 0,5 |9/11/2014 4:33:31 pm |

Po kliknięciu przycisku **Wyświetl historię uruchamiania**zostanie wyświetlona lista wszystkich przebiegów:

![Przykład historii uruchamiania](./media/manage-experiment-iterations/viewrunhistory.jpg)

Kliknij dowolny z tych przebiegów, aby wyświetlić migawkę eksperymentu w momencie uruchomienia. Konfiguracja, wartości parametrów, komentarze i wyniki są zachowywane zapewnienie pełnego rekordu z programem eksperymentu.

> [!TIP]
> Aby udokumentować iteracje eksperymentu, można zmodyfikować tytuł przy każdym jego uruchomieniu, można zaktualizować **Podsumowanie** eksperymentu w okienku właściwości, a następnie dodać lub zaktualizować Komentarze do poszczególnych modułów w celu zapisania zmian. Komentarze title, summary i moduł są zapisywane przy każdym uruchomieniu eksperymentu.
> 
> 

Lista eksperymentów na karcie **eksperymenty** w Machine Learning Studio (klasyczny) zawsze wyświetla najnowszą wersję eksperymentu. Jeśli otworzysz poprzednie uruchomienie eksperymentu (przy użyciu **wcześniejszej** **historii uruchamiania lub wyświetlania**), możesz powrócić do wersji roboczej, klikając polecenie **Wyświetl historię uruchamiania** i wybierając iterację mającą **stan** **edytowalny**.

## <a name="iterating-on-a-previous-run"></a>Iteracja na poprzedniego uruchomienia
Po kliknięciu przycisku **poprzedni przebieg** lub **Wyświetl historię uruchamiania** i Otwórz poprzedni przebieg, można wyświetlić gotowy eksperyment w trybie tylko do odczytu.

Jeśli chcesz rozpocząć iterację eksperymentu, rozpoczynając od sposobu skonfigurowania go dla poprzedniego przebiegu, możesz to zrobić, otwierając przebieg i klikając polecenie **Zapisz jako**. Tworzy nowy eksperyment z tytułem nowy, pusty historii, uruchamiania i wszystkie składniki i wartości parametrów poprzedniego uruchomienia. Ten nowy eksperyment znajduje się na liście na karcie **eksperymenty** na stronie głównej Machine Learning Studio (klasycznej) i można go modyfikować i uruchamiać, inicjując nową historię uruchamiania dla tej iteracji eksperymentu. 

Na przykład załóżmy, że masz eksperymentu pokazano w poprzedniej sekcji historii uruchamiania. Chcesz obserwować, co się dzieje, gdy ustawisz parametr **rate uczenia** na 0,4, i spróbuj użyć różnych wartości dla **liczby parametrów szkolenia** .

1. Kliknij pozycję **Wyświetl historię uruchamiania** i Otwórz iterację eksperymentu, który uruchomiono o godzinie 4:28:36 pm (w którym wartość parametru jest ustawiana na 0,4).
2. Kliknij przycisk **Zapisz jako**.
3. Wprowadź nowy tytuł, a następnie kliknij przycisk **OK** . Utworzono nową kopię eksperymentu.
4. Zmodyfikuj **liczbę parametrów szkolenia** .
5. Kliknij przycisk **Uruchom**.

Możesz nadal do zmodyfikowania, a następnie uruchom tę wersję eksperymentu, tworzenie nowych historii przebiegów, aby zarejestrować swoją pracę.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
