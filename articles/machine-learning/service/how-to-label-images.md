---
title: Jak używać narzędzia do etykietowania danych Azure Machine Learning
title.suffix: Azure Machine Learning
description: W tym artykule przedstawiono sposób korzystania z narzędzi do tagowania danych w Azure Machine Learning projektu etykietowania.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: bc7eaeefcef042417b29eb7c887cc19acf8c5c02
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688232"
---
# <a name="tag-images-in-a-labeling-project"></a>Obrazy tagów w projekcie etykietowania

Gdy administrator projektu tworzy projekt etykietowania w Azure Machine Learning, można użyć narzędzia do etykietowania, aby szybko przygotować dane dla projektu Machine Learning. W tym artykule opisano:

> [!div class="checklist"]
> * Jak uzyskać dostęp do projektów etykietowania
> * Narzędzia do etykietowania
> * Jak używać narzędzi do określonych zadań etykietowania

## <a name="prerequisites"></a>Wymagania wstępne

* Adres URL portalu etykietowania dla uruchomionego projektu etykietowania danych
* [Konto Microsoft](https://account.microsoft.com/account) lub konto Azure Active Directory dla organizacji i projektu

> [!NOTE]
> Administrator projektu może znaleźć adres URL portalu etykietowania na karcie **szczegóły** na stronie **szczegóły projektu** .

## <a name="sign-in-to-the-projects-labeling-portal"></a>Zaloguj się do portalu etykietowania projektu

Przejdź do adresu URL portalu etykietowego dostarczonego przez administratora projektu. Zaloguj się przy użyciu konta e-mail użytego przez administratora do dodania Cię do zespołu. W przypadku większości użytkowników zostanie konto Microsoft. Jeśli projekt etykietowania używa Azure Active Directory, oznacza to, jak się zalogować.

## <a name="understand-the-labeling-task"></a>Zrozumienie zadania etykietowania

Po zalogowaniu zostanie wyświetlona strona przegląd projektu.

Przejdź do **szczegółów, aby wyświetlić szczegółowe instrukcje**. Te instrukcje są specyficzne dla projektu. Wyjaśniają one typ danych, na które się znajdują, jak należy podjąć decyzje i inne istotne informacje. Po przeczytaniu tych informacji Wróć do strony projektu i wybierz pozycję **Rozpocznij etykietowanie**.

## <a name="common-features-of-the-labeling-task"></a>Typowe funkcje zadania etykietowania

We wszystkich zadaniach etykietowania obrazów należy wybrać odpowiedni tag lub Tagi z zestawu, który jest określony przez administratora projektu. Można wybrać pierwsze dziewięć tagów przy użyciu klawiszy liczbowych na klawiaturze.  

W zadaniach klasyfikacji obrazów można wybrać opcję wyświetlania wielu obrazów jednocześnie. Użyj ikon powyżej obszaru obrazu, aby wybrać układ. Aby jednocześnie zaznaczyć wszystkie wyświetlane obrazy, użyj **opcji Zaznacz wszystko**. Aby zaznaczyć poszczególne obrazy, użyj przycisku zaznaczania cyklicznego w prawym górnym rogu obrazu. Musisz wybrać co najmniej jeden obraz, aby zastosować tag. W przypadku wybrania wielu obrazów każdy wybrany tag zostanie zastosowany do wszystkich zaznaczonych obrazów.

W tym miejscu wybieramy układ dwóch przez dwa i zamierzamy zastosować tag "ssak" do obrazów obrazu i programu Orca. Obraz rekina został już oznaczony jako "Cartilaginous ryba", a Iguana nie został jeszcze oznaczony.

![Wiele układów i opcji obrazu](media/how-to-label-images/layouts.png)

> [!Important] 
> Przełączaj układy tylko wtedy, gdy masz nową stronę bez etykietowania danych. Przełączenie układu czyści działanie tagowania strony w toku.

System Azure włącza przycisk **Prześlij** , gdy Otagowano wszystkie obrazy na stronie. Wybierz pozycję **Prześlij** , aby zapisać swoją służbę.

Po przesłaniu tagów dla danych, platforma Azure Odświeża stronę nowym zestawem obrazów z kolejki roboczej.

## <a name="tag-images-for-multi-class-classification"></a>Obrazy tagów dla klasyfikacji wieloklasowej

Jeśli projekt jest typu "Klasyfikacja obrazu wiele klas", przypiszesz jeden tag do całego obrazu. Aby zapoznać się ze wskazówkami w dowolnym momencie, przejdź do strony z **instrukcjami** i wybierz pozycję **Wyświetl szczegółowe instrukcje**.

Jeśli zauważysz, że po przypisaniu znacznika do obrazu wystąpi błąd, możesz go naprawić. Wybierz znak "**X**" na etykiecie, która jest wyświetlana poniżej obrazu, aby wyczyścić tag. Lub zaznacz obraz i wybierz inną klasę. Nowo wybrana wartość zastąpi poprzednio zastosowany tag.

## <a name="tag-images-for-multi-label-classification"></a>Obrazy tagów dla klasyfikacji wieloetykietowej

Jeśli pracujesz nad projektem typu "Klasyfikacja obrazu z wieloma etykietami", zastosujesz jeden *lub więcej* tagów do obrazu. Aby wyświetlić wskazówki właściwe dla projektu, wybierz **instrukcje** i przejdź do szczegółów, aby **wyświetlić szczegółowe instrukcje**.

Wybierz obraz, który chcesz oznaczyć etykietą, a następnie wybierz tag. Tag zostanie zastosowany do wszystkich zaznaczonych obrazów, a następnie zostaną odwybrane. Aby zastosować więcej tagów, należy ponownie wybrać obrazy. Następujące animacje przedstawiają znakowanie wieloetykietowe:

1. **Opcja Zaznacz wszystko** służy do zastosowania tagu "Ocean".
1. Wybrano pojedynczy obraz i otagowano "Closeup".
1. Wybrano trzy obrazy i otagowano "szeroki kąt".

![Animacja pokazuje przepływ wieloetykietowy](media/how-to-label-images/multilabel.gif)

Aby poprawić błąd, kliknij znak "**X**", aby wyczyścić pojedynczy tag, lub wybierz obrazy, a następnie wybierz tag, który czyści tag ze wszystkich zaznaczonych obrazów. Ten scenariusz jest przedstawiony tutaj. Kliknięcie pozycji "Ziemia" spowoduje wyczyszczenie tego tagu z dwóch zaznaczonych obrazów.

![Zrzut ekranu przedstawia wiele selektorów](media/how-to-label-images/multiple-deselection.png)

Na platformie Azure zostanie włączony przycisk **Prześlij** tylko po zastosowaniu co najmniej jednego znacznika do każdego obrazu. Wybierz pozycję **Prześlij** , aby zapisać swoją służbę.

## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Dodawanie tagów do obrazów i określanie pól ograniczenia na potrzeby wykrywania obiektów

Jeśli projekt jest typu "Identyfikacja obiektu (pola ograniczające)", należy określić jedno lub więcej pól ograniczenia w obrazie i zastosować tag do każdego pola. Obrazy mogą zawierać wiele pól, z których każdy ma jeden tag. Użyj **widoku szczegółowe instrukcje** , aby określić, czy w projekcie są używane wiele pól ograniczenia.

1. Wybierz tag dla powiązanego pola, które ma zostać utworzone.
1. Wybierz narzędzie **prostokątnego pola** ![narzędzie prostokątne](media/how-to-label-images/rectangular-box-tool.png) lub wybierz pozycję "R".
3. Kliknij i przeciągnij ukośnie na miejsce docelowe, aby utworzyć niedalekią ramkę. Aby dostosować pole ograniczenia, przeciągnij krawędzie lub rogi.

![Zrzut ekranu przedstawia podstawowe tworzenie pól związanych z ograniczeniami.](media/how-to-label-images/bounding-box-sequence.png)

Aby usunąć pole ograniczenia, kliknij obiekt docelowy w kształcie X, który pojawia się obok pola ograniczenia po utworzeniu.

Nie można zmienić znacznika istniejącego pola ograniczenia. Jeśli wprowadzisz błąd przypisania znacznika, musisz usunąć pole ograniczenia i utworzyć nowe z prawidłowym tagiem.

Domyślnie można edytować istniejące pola ograniczenia. Narzędzie **blokowania/odblokowywania** regionów ![narzędzia do blokowania/odblokowywania regionów](media/how-to-label-images/lock-bounding-boxes-tool.png) lub "L" przełącza takie zachowanie. Jeśli regiony są zablokowane, można zmienić tylko kształt lub lokalizację nowego pola ograniczenia.

Użyj narzędzia **manipulowania regionami** ![](media/how-to-label-images/regions-tool.png) narzędzia do manipulowania regionami, aby dostosować istniejące pole ograniczenia. Przeciągnij krawędzie lub rogi, aby dostosować kształt. Kliknij wewnątrz, aby można było przeciągnąć całe pole ograniczenia. Jeśli nie możesz edytować regionu, prawdopodobnie przełączono narzędzie **blokowania/odblokowywania regionów** .

Użyj narzędzia **pola opartego na szablonach** ![narzędzia pola szablonu](media/how-to-label-images/template-box-tool.png) lub "t", aby utworzyć wiele pól ograniczenia o tym samym rozmiarze. Jeśli obraz nie ma żadnych pól ograniczenia i uaktywniasz pola oparte na szablonach, narzędzie spowoduje utworzenie pól o 50 do 50 pikseli. W przypadku utworzenia pola ograniczenia, a następnie aktywowania pól opartych na szablonach wszystkie nowe pola ograniczenia będą rozmiarem ostatniego utworzonego pola. Po umieszczeniu można zmienić rozmiar pól opartych na szablonie. Zmienianie rozmiaru pola opartego na szablonie zmienia rozmiar tylko określonego pola.

Aby usunąć *wszystkie* pola ograniczające na bieżącym obrazie, wybierz narzędzie **usuń wszystkie regiony** ![narzędzia do usuwania regionów](media/how-to-label-images/delete-regions-tool.png).

Po utworzeniu pól ograniczenia dla obrazu wybierz pozycję **Prześlij** , aby zapisać swoją służbę lub nie będzie można zapisać pracy w toku.

## <a name="finish-up"></a>Zakończ

Gdy przesyłasz stronę oznakowanych danych, platforma Azure nowe dane bez etykiet do użytkownika z kolejki służbowej. Jeśli nie ma więcej dostępnych danych bez etykiet, zostanie wyświetlony komunikat z linkiem do strony głównej portalu.

Gdy skończysz etykietowanie, wybierz swoją nazwę w prawym górnym rogu portalu etykietowania, a następnie wybierz pozycję **Wyloguj się**. Jeśli nie wylogujesz się, ostatecznie platforma Azure przekroczy czas, a następnie przypisze dane do innego Labeler.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [uczenie modeli klasyfikacji obrazów na platformie Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* Przeczytaj informacje o [wykrywaniu obiektów przy użyciu platformy Azure i techniki "szybsze R-CNN"](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)
