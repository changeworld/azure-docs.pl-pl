---
title: Wybór funkcji w procesie nauki o danych zespołu
description: Wyjaśnia cel wyboru funkcji i zawiera przykłady ich roli w procesie ulepszania danych uczenia maszynowego.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1127a470a48660ffffa892d24c9f2991ec64c8e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76716686"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Wybór funkcji w zespołowym przetwarzaniu danych dla celów naukowych
W tym artykule opisano cele wyboru funkcji i przedstawiono przykłady jego roli w procesie ulepszania danych uczenia maszynowego. Te przykłady są rysowane z usługi Azure Machine Learning Studio.

Inżynieria i wybór funkcji jest częścią procesu nauki o danych zespołu (TDSP) opisanego w artykule [Co to jest proces nauki o danych zespołu?](overview.md). Inżynieria funkcji i wybór są częścią kroku **Rozwijaj funkcje** TDSP.

* **inżynieria funkcji:** Ten proces próbuje utworzyć dodatkowe odpowiednie funkcje z istniejących nieprzetworzonych funkcji w danych i zwiększyć moc predykcyjną algorytmu uczenia się.
* **wybór funkcji:** Ten proces wybiera kluczowy podzbiór oryginalnych obiektów danych, starając się zmniejszyć wymiarowość problemu szkoleniowego.

Zwykle **inżynieria funkcji** jest stosowana najpierw do generowania dodatkowych operacji, a następnie krok **wyboru operacji** jest wykonywany w celu wyeliminowania nieistotnych, nadmiarowych lub wysoce skorelowanych operacji.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrowanie funkcji z danych — wybór funkcji
Wybór operacji może być używany do klasyfikacji lub zadań regresji. Celem jest wybranie podzbioru obiektów z oryginalnego zestawu danych, które zmniejszają jego wymiary przy użyciu minimalnego zestawu obiektów do reprezentowania maksymalnej ilości wariancji w danych. Ten podzbiór operacji jest używany do trenowania modelu. Wybór funkcji służy dwóm głównym celom.

* Po pierwsze, wybór operacji często zwiększa dokładność klasyfikacji, eliminując nieistotne, nadmiarowe lub wysoce skorelowane operacje.
* Po drugie zmniejsza liczbę funkcji, co sprawia, że proces szkolenia modelu bardziej wydajne. Wydajność jest ważna dla osób uczących się, które są drogie w szkoleniu, takie jak maszyny wektorowe wsparcia.

Chociaż wybór funkcji ma na celu zmniejszenie liczby obiektów w zestawie danych używanych do trenowania modelu, nie jest określany przez termin "redukcja wymiarowości". Metody wyboru operacji wyodrębniają podzbiór oryginalnych operacji w danych bez ich zmiany.  Metody redukcji wymiarowości wykorzystują zaprojektowane funkcje, które mogą przekształcać oryginalne operacje, a tym samym je modyfikować. Przykłady metod redukcji wymiarowości obejmują analizę głównego komponentu, analizę korelacji kanonicznej i rozkład wartości pojedynczej.

Między innymi jedna szeroko stosowana kategoria metod wyboru funkcji w nadzorowanym kontekście nosi nazwę "wybór funkcji opartych na filtrze". Oceniając korelację między każdą funkcją a atrybutem docelowym, metody te stosują miarę statystyczną, aby przypisać wynik do każdej funkcji. Funkcje są następnie klasyfikowane według wyniku, który może służyć do ustawiania progu dla zachowania lub wyeliminowania określonej funkcji. Przykłady miar statystycznych stosowanych w tych metodach obejmują korelację osoby, wzajemne informacje i test Chi squared.

W usłudze Azure Machine Learning Studio dostępne są moduły do wyboru funkcji. Jak pokazano na poniższym rysunku, moduły te obejmują [wybór funkcji opartych na filtrze][filter-based-feature-selection] i [analizę discriminantów liniowych Fisher.][fisher-linear-discriminant-analysis]

![Moduły wyboru funkcji](./media/select-features/feature-Selection.png)

Należy na przykład rozważyć użycie modułu [wybór funkcji opartych na filtrze.][filter-based-feature-selection] Dla wygody, kontynuuj korzystanie z przykładu wyszukiwania tekstu. Załóżmy, że chcesz zbudować model regresji po zestaw 256 funkcji są tworzone za pośrednictwem [funkcji hashing][feature-hashing] modułu i że zmienna odpowiedzi jest "Col1", który zawiera oceny przeglądu książki, począwszy od 1 do 5. Ustawiając "Metoda oceniania funkcji" na "Korelację Pearsona", "Kolumnę docelową" na "Col1" i "Liczbę pożądanych funkcji" na 50. Następnie moduł [filter-based feature selection][filter-based-feature-selection] tworzy zestaw danych zawierający 50 funkcji wraz z atrybutem docelowym "Col1". Na poniższej ilustracji przedstawiono przepływ tego eksperymentu i parametry wejściowe:

![Właściwości modułu wyboru funkcji opartego na filtrze](./media/select-features/feature-Selection1.png)

Na poniższej ilustracji przedstawiono wynikowe zestawy danych:

![Wynikowy zestaw danych dla modułu wyboru operacji opartych na filtrze](./media/select-features/feature-Selection2.png)

Każda funkcja jest punktowana na podstawie korelacji Pearsona między sobą a atrybutem docelowym "Col1". Funkcje z najlepszymi wynikami są przechowywane.

Odpowiednie wyniki wybranych operacji są pokazane na poniższym rysunku:

![Wyniki dla modułu wyboru funkcji opartych na filtrze](./media/select-features/feature-Selection3.png)

Stosując ten moduł [wyboru funkcji opartych na filtrze,][filter-based-feature-selection] 50 z 256 funkcji jest wybieranych, ponieważ mają one najbardziej skorelowane funkcje ze zmienną docelową "Col1", opartą na metodzie oceniania "Korelacja Pearsona".

## <a name="conclusion"></a>Podsumowanie
Inżynieria funkcji i wybór funkcji to dwie często zaprojektowane i wybrane funkcje zwiększają wydajność procesu szkolenia, który próbuje wyodrębnić kluczowe informacje zawarte w danych. Zwiększają one również moc tych modeli, aby dokładnie klasyfikować dane wejściowe i bardziej solidnie przewidywać wyniki zainteresowania. Inżynieria funkcji i wybór mogą również łączyć się, aby nauka była bardziej liczna pod względem obliczeniowym. Czyni to poprzez zwiększenie, a następnie zmniejszenie liczby funkcji potrzebnych do kalibracji lub szkolenia modelu. Matematycznie rzecz biorąc, funkcje wybrane do uczenia modelu są minimalny zestaw niezależnych zmiennych, które wyjaśniają wzorce w danych, a następnie pomyślnie przewidzieć wyniki.

Nie zawsze jest to konieczne do wykonywania inżynierii funkcji lub wyboru funkcji. To, czy jest to potrzebne, czy nie, zależy od zebranych danych, wybranego algorytmu i celu eksperymentu.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

