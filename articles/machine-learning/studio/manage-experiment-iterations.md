---
title: Zarządzanie iteracjami eksperymentów w usłudze Machine Learning Studio | Dokumentacja firmy Microsoft
description: Zarządzanie iteracjami eksperymentów w usłudze Azure Machine Learning Studio
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6a53530f-20d5-40ae-9b49-7b499ccb44b7
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 4dcae0bb3cb89e65079b88f7be68ddf360ce1b8c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834815"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio"></a>Zarządzanie iteracjami eksperymentów w usłudze Azure Machine Learning Studio
Tworzenie modelu analizy predykcyjnej jest procesem iteracyjnym - podczas modyfikowania różnych funkcji i parametry eksperymentu, wyniki stają się zbieżne aż do uzyskania czy masz wyuczonego, skutecznego modelu. Klucz do tego procesu służy do śledzenia różne iteracje eksperymentu parametrów i konfiguracji.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Możesz przejrzeć poprzedniej uruchomień eksperymentów w dowolnym momencie w celu testu, ponownie i ostatecznie albo upewnij się lub uściślić poprzedniej założeń. Po uruchomieniu eksperymentu Machine Learning Studio zawiera historię uruchomienia, w tym zestaw danych, modułu, port połączenia i parametry. Ta historia przechwytuje także wyniki, informacje środowiska uruchomieniowego start i czasy zatrzymania, komunikaty dziennika i stan wykonania. Można przyjrzeć się wstecz żadnego z tych działa w dowolnym momencie można przejrzeć chronologii eksperymentu, a wyniki pośrednie. Nawet służy poprzedniego uruchomienia eksperymencie można uruchomić w nowej fazy zapytania i odnajdowanie ścieżki do tworzenia prostego, złożonych lub nawet rozwiązania modelowania zespół.

> [!NOTE]
> Po wyświetleniu poprzedniego uruchomienia eksperymentu tej wersji eksperymentu jest zablokowany i nie można edytować. Można jednak zapisać kopię, klikając **SAVE AS** i podając nazwę nowej kopii. Usługa Machine Learning Studio otwiera nową kopię, które mogą edytować i uruchamiać. Jest dostępna w tym kopia eksperymentu **EKSPERYMENTY** listy oraz wszystkich innych eksperymentów.
> 
> 

## <a name="viewing-the-prior-run"></a>Wyświetlanie poprzedniego uruchomienia
Jeśli masz otwarty eksperymentu uruchamianego co najmniej raz, uruchom poprzedniego doświadczenia można wyświetlić, klikając **uprzedniego uruchomienia** w okienku właściwości.

Załóżmy na przykład, tworzenie eksperymentu, a jednocześnie wersje 11:23 11:42 i 11:55. Otwórz po ostatnim uruchomieniu eksperymentu (11:55) i kliknięcie **wcześniejszego uruchomienia**, wersja było uruchomione 11:42 jest otwarty.

## <a name="viewing-the-run-history"></a>Wyświetlanie historii wykonywania
Można wyświetlić wszystkich poprzednich uruchomień eksperyment, klikając **Wyświetl historię uruchamiania** w eksperymencie otwarte.

Na przykład załóżmy tworzenie eksperymentów z [regresji liniowej] [ linear-regression] modułu i chcesz obserwować wpływ zmiana wartości **tempo uczenia** na użytkownika wyniki eksperymentu. Możesz uruchomić eksperyment wiele razy z różnych wartości tego parametru, w następujący sposób:

| Wartość współczynnika Learning | Czas rozpoczęcia wykonywania |
| --- | --- |
| 0.1 |2014-9-11 4:18:58 pm |
| 0.2 |2014-9-11:24:33 PM. |
| 0.4 |2014-9-11 4:28:36 pm |
| 0.5 |2014-9-11 4:33:31 pm |

Jeśli klikniesz przycisk **WYŚWIETL HISTORIĘ URUCHAMIANIA**, wyświetlić listę wszystkich tych działa:

![Przykład Historia uruchomień][runhistory]

Kliknij dowolny z tych działa, aby wyświetlić migawki doświadczenia w czasie jej uruchomienia. Konfiguracja, wartości parametrów, komentarze i wyniki są zachowywane daje pełny rekord z programem eksperymentu.

> [!TIP]
> Do dokumentu programu iteracje eksperymentu, możesz zmodyfikować tytuł zawsze zostanie uruchomiony, możesz zaktualizować **Podsumowanie** doświadczenia we właściwościach okienku, a można dodać lub zaktualizować komentarze dotyczące poszczególnych modułów do rejestrowania programu zmiany. Tytuł, podsumowanie i moduł komentarze są zapisywane przy każdym uruchomieniu eksperymentu.
> 
> 

Lista eksperymenty w **EKSPERYMENTY** karty w usłudze Machine Learning Studio zawsze zawiera najnowszą wersję eksperymentu. Po otwarciu poprzedniego uruchomienia eksperyment (przy użyciu **uprzedniego uruchomienia** lub **WYŚWIETL HISTORIĘ URUCHAMIANIA**), można powrócić do wersji roboczej, klikając **WYŚWIETL HISTORIĘ URUCHAMIANIA** i wybierając polecenie iteracji, które ma **stanu** z **edytowalna**.

## <a name="iterating-on-a-previous-run"></a>Iteracja na poprzedniego działania
Po kliknięciu **uprzedniego uruchomienia** lub **WYŚWIETL HISTORIĘ URUCHAMIANIA** i otwórz poprzedniego uruchomienia, Zakończono eksperymentu można wyświetlić w trybie tylko do odczytu.

Jeśli chcesz rozpocząć iterację eksperymentu począwszy od skonfigurowanych dla poprzedniego uruchomienia sposób, aby to zrobić, otwierając Uruchom i klikając pozycję **SAVE AS**. To tworzy nowy eksperyment z nowy tytuł pustą, uruchom historii, i wszystkich składników i wartości parametrów poprzedniego uruchomienia. Ten nowy eksperyment ma na liście **EXPERIMENTS** można zmodyfikować karty w usłudze Machine Learning Studio strony głównej, a i uruchom go, inicjowanie nowy Uruchom historii dla tej iteracji eksperymentu. 

Załóżmy na przykład uruchomienie historii wyświetlone w poprzedniej sekcji eksperymentu. Chcesz obserwować, co się stanie w przypadku ustawienia **tempo uczenia** parametru 0,4 i spróbuj różnych wartości **liczba epok szkolenia** parametru.

1. Kliknij przycisk **WYŚWIETL HISTORIĘ URUCHAMIANIA** , a następnie otwórz iterację eksperymentu, uruchomionego godzinie 4:28:36 (w którym wartość parametru do 0,4).
2. Kliknij przycisk **SAVE AS**.
3. Wprowadź nowy tytuł, a następnie kliknij przycisk **OK** znacznik wyboru. Utworzono nową kopię eksperymentu.
4. Modyfikowanie **liczba epok szkolenia** parametru.
5. Kliknij przycisk **Uruchom**.

Teraz możesz zmodyfikować i uruchomić tej wersji eksperymentu, tworzenie nowych Historia uruchomień, aby zapisać swoją pracę.

<!-- Images -->
[runhistory]:./media/manage-experiment-iterations/viewrunhistory.jpg


<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
