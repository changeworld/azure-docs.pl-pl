---
title: Jak używać narzędzia do etykietowania danych Azure Machine Learning
title.suffix: Azure Machine Learning
description: W tym artykule przedstawiono sposób korzystania z narzędzi do tagowania danych w Azure Machine Learning projektu etykietowania.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: af12361ed11d0a16e5a5d0cfe5989bb3918ce154
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586397"
---
# <a name="how-to-tag-images-in-a-labeling-project"></a>Jak oznakować obrazy w projekcie etykietowania

Gdy administrator projektu utworzy projekt etykietowania w programie Azure Machine Learning Studio, można użyć narzędzia do etykietowania, aby szybko przygotować dane dla projektu uczenia maszynowego. 

> [!div class="checklist"]
> * Jak uzyskać dostęp do projektów etykietowania
> * Narzędzia do etykietowania
> * Jak używać narzędzi do określonych zadań etykietowania

## <a name="prerequisites"></a>Wymagania wstępne

* Adres URL portalu etykietowania dla uruchomionego projektu etykietowania danych
* [Konto Microsoft](https://account.microsoft.com/account) lub
* Konto Azure Active Directory organizacji i projektu

> [!Note]
> Administrator projektu może znaleźć adres URL portalu etykietowania na karcie **szczegóły** na stronie **szczegółów projektu** . 

## <a name="logging-in-to-the-projects-labeling-portal"></a>Logowanie do portalu etykietowania projektu

Przejdź do adresu URL portalu etykietowego dostarczonego przez administratora projektu. 

Zaloguj się przy użyciu konta e-mail administratora używanego do dodawania Cię do zespołu. W przypadku większości użytkowników logowanie zostanie wykonane przy użyciu konto Microsoft. Jeśli projekt etykietowania używa Azure Active Directory, oznacza to, jak się zalogować. 

## <a name="understanding-the-labeling-task"></a>Zrozumienie zadania etykietowania

Po zalogowaniu nastąpi przełączenie do strony przegląd projektu. 

Pierwszym krokiem do wykonania jest **wyświetlenie szczegółowych instrukcji**. Te instrukcje są specyficzne dla Twojego projektu i objaśniają typ danych, na które się znajdują, sposób podejmowania decyzji i inne istotne informacje. Gdy skończysz, Wróć do strony projektu i wybierz pozycję **Rozpocznij etykietowanie**.

## <a name="common-features-of-the-labeling-task"></a>Typowe funkcje zadania etykietowania

Wszystkie zadania etykietowania obrazów obejmują Wybieranie odpowiednich tagów lub tagów z zestawu określonego przez administratora projektu. Można wybrać jeden z pierwszych dziewięciu tagów przy użyciu klawiszy liczbowych na klawiaturze.  

Zadania klasyfikacji obrazów umożliwiają wybranie wyświetlania wielu obrazów jednocześnie. Różne układy można wybierać przy użyciu ikon powyżej obszaru obrazu. Możesz wybrać wszystkie wyświetlane obrazy jednocześnie, naciskając przycisk **Zaznacz wszystko** . Wybierz pojedyncze zdjęcia przy użyciu przycisku zaznaczania cyklicznego w prawym górnym rogu obszaru obrazu. Musisz wybrać co najmniej jeden obraz, aby zastosować tag. Jeśli wybrano wiele obrazów, wybranie tagu spowoduje zastosowanie tego tagu do każdego z wybranych zdjęć.

W tym miejscu wybieramy układ 2x2 i zamierzamy zastosować tag "ssak" do obrazów obrazu i programu Orca. Obraz ze rekinem został już oznaczony jako "Cartilaginous ryba", a Iguana nie został jeszcze oznaczony.

![Wiele układów i opcji obrazu](media/how-to-label-images/layouts.png)

> [!Important] 
> Przełączaj układy tylko wtedy, gdy była to nowa strona nieoznaczonych danych. Przełączenie układu czyści działanie tagowania strony w toku. 

System Azure włącza przycisk **Prześlij** , gdy Otagowano wszystkie obrazy na stronie. Naciśnij pozycję **Prześlij** , aby zapisać swoją służbę. 

Po przesłaniu tagów dla danych, platforma Azure Odświeża stronę nowym zestawem obrazów z kolejki roboczej.  

## <a name="tagging-images-for-multi-class-classification"></a>Tagowanie obrazów dla klasyfikacji wieloklasowej

Jeśli projekt jest typu "Klasyfikacja obrazu wiele klas", przypiszesz jeden tag do całego obrazu. W dowolnym momencie wybierz stronę z **instrukcjami** i przejdź do **szczegółów** , aby wyświetlić wskazówki dotyczące konkretnego projektu. 

Jak wspomniano wcześniej, możesz wybrać spośród kilku układów prezentacji obrazów. Jeśli po wybraniu obrazu i przypisaniu go tag zobaczysz pomyłkę, możesz rozwiązać ten problem. Jeśli w etykiecie widocznej poniżej obrazu klikniesz element docelowy `X`, wyczyść tag. W przypadku wybrania obrazu i wybrania innej klasy tag przejdzie do nowo wybranej wartości.

## <a name="tagging-images-for-multi-label-classification"></a>Tagowanie obrazów dla klasyfikacji wieloetykietowej

Jeśli pracujesz nad projektem typu "Klasyfikacja obrazu z wieloma etykietami", zastosujesz jeden _lub więcej_ tagów do obrazu. W dowolnym momencie wybierz stronę z **instrukcjami** i przejdź do **szczegółów** , aby wyświetlić wskazówki dotyczące konkretnego projektu. 

Wybierz obraz, który chcesz oznaczyć etykietą, a następnie kliknij tag. Wybór znacznika powoduje zastosowanie go do wszystkich zaznaczonych obrazów i odznacza ich zaznaczenie. Aby zastosować więcej tagów, należy ponownie wybrać obrazy. Ta animacja pokazuje stronę znakowania wieloetykietowego:

* **Opcja Zaznacz wszystko** służy do stosowania tagu "Ocean"
* Wybrano pojedynczy obraz i otagowano "Closeup"
* Wybrano trzy obrazy i otagowano "kąt szeroki"

![Animacja przedstawiająca przepływ wieloetykietowy](media/how-to-label-images/multilabel.gif)

Aby poprawić błąd, możesz kliknąć `X`, aby wyczyścić poszczególne Tagi, lub wybrać obrazy, a następnie wybrać tag, który będzie czyścił tag ze wszystkich zaznaczonych obrazów. Ten scenariusz jest przedstawiony tutaj, gdy kliknięcie pozycji "Ziemia" spowoduje wyczyszczenie tego tagu z dwóch wybranych obrazów.

![Zrzut ekranu przedstawiający wielokrotne Wybieranie](media/how-to-label-images/multiple-deselection.png)

Na platformie Azure zostanie włączony przycisk **Prześlij** tylko po zastosowaniu co najmniej jednego znacznika do każdego obrazu. Naciśnij pozycję **Prześlij** , aby zapisać swoją służbę.

## <a name="tagging-images-and-bounding-boxes-for-object-detection"></a>Tagowanie obrazów i pól ograniczenia na potrzeby wykrywania obiektów

Jeśli projekt jest typu "Identyfikacja obiektu (pola ograniczające)", należy określić co najmniej jedno pole ograniczenia w obrazie i zastosować tag do tego pola. Każdy obraz może zawierać wiele pól ograniczenia, z których każdy ma jeden tag. Użyj **widoku szczegółowe instrukcje** , aby określić, czy Dodawanie wielu pól powiązanych jest odpowiednie dla Twojego projektu.

1. Wybierz tag dla pola ograniczenia, które chcesz utworzyć.
1. Wybierz narzędzie **prostokątnego pola** ![narzędzie prostokątne](media/how-to-label-images/rectangular-box-tool.png) lub naciśnij klawisz "R" 
1. Kliknij i przeciągnij ukośnie na miejsce docelowe, aby utworzyć nierównomierne pole ograniczenia
    * Dostosuj pole ograniczenia, klikając i przeciągając krawędzie lub rogi pola

![Zrzut ekranu przedstawiający podstawowe tworzenie pól związanych z ograniczeniami](media/how-to-label-images/bounding-box-sequence.png)

Jeśli chcesz usunąć pole ograniczenia, kliknij obiekt docelowy w kształcie X, który pojawia się obok pola ograniczenia po utworzeniu.

Nie można ponownie przypisać znacznika istniejącego pola ograniczenia. W przypadku pomyłki z przypisaniem tagów należy usunąć pole ograniczenia i utworzyć nowe z prawidłowym tagiem.

Domyślnie istniejące pola ograniczenia mogą być edytowane. Narzędzie **blokowania/odblokowywania** regionów ![narzędzia do blokowania/odblokowywania regionów](media/how-to-label-images/lock-bounding-boxes-tool.png) lub "L" przełącza takie zachowanie. Jeśli regiony są zablokowane, można zmienić tylko kształt lub lokalizację nowego pola ograniczenia.

Użyj narzędzia **manipulowania regionami** ![](media/how-to-label-images/regions-tool.png) narzędzia do manipulowania regionami, aby dostosować istniejące pole ograniczenia. Możesz kliknąć i przeciągnąć na brzegach lub rogach, aby dostosować kształt. Po kliknięciu wewnątrz wnętrza można przeciągnąć całe pole ograniczenia. Jeśli nie możesz edytować regionu, prawdopodobnie przełączono narzędzie **blokowania/odblokowywania regionów** . 

Użyj narzędzia **pola opartego na szablonach** ![narzędzia pola szablonu](media/how-to-label-images/template-box-tool.png) lub "t", aby utworzyć wiele pól ograniczenia o tym samym rozmiarze. Jeśli obraz nie ma żadnych pól ograniczenia i uaktywniasz pola oparte na szablonach, narzędzie wygeneruje pola pikseli 50x50. Jeśli utworzono pole ograniczenia, a następnie uaktywniasz pola oparte na szablonie, nowe pola ograniczające będą rozmiarem ostatnio wykonanego. Po umieszczeniu można zmienić rozmiar pól opartych na szablonie. Zmienianie rozmiaru pola opartego na szablonie zmienia rozmiar tylko określonego pola. 

Jeśli chcesz usunąć _wszystkie_ pola ograniczające w bieżącym obrazie, możesz wybrać narzędzie **usuń wszystkie regiony** ![narzędzia Usuń regiony](media/how-to-label-images/delete-regions-tool.png). 

Po utworzeniu pól ograniczenia dla obrazu naciśnij pozycję **Prześlij** , aby zapisać swoją służbę. Nie będzie można zapisać pracy w toku, chyba że naciśniesz przycisk **Prześlij** . 

## <a name="finishing-up"></a>Zakończenie 

Gdy przesyłasz stronę oznakowanych danych, platforma Azure przypisze nowe dane bez etykiet z kolejki służbowej. Jeśli nie ma więcej danych bez etykiet, zobaczysz komunikat w tym efekcie z linkiem z powrotem do strony głównej portalu. 

Jeśli wiesz, że nie chcesz więcej etykietować, wybierz swoją nazwę w prawym górnym rogu portalu etykietowania i wybierz pozycję **wylogowaniu**. Jeśli nie wylogujesz się, ostatecznie platforma Azure przekroczy czas, a następnie przypisze dane do innego Labeler. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [uczenie modeli klasyfikacji obrazów na platformie Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* Przeczytaj informacje o [wykrywaniu obiektów przy użyciu platformy Azure i szybszej technice języka R-CNN](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)