---
title: Oznaczanie obrazów w projekcie etykietowania
title.suffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z narzędzi do znakowania danych w projekcie etykietowania usługi Azure Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 371b99c794feb4a64eb3e9af389020e25d14eedb
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879432"
---
# <a name="tag-images-in-a-labeling-project"></a>Oznaczanie obrazów w projekcie etykietowania

Po administrator projektu [tworzy projekt etykietowania](https://docs.microsoft.com/azure/machine-learning/how-to-create-labeling-projects#create-a-labeling-project) w usłudze Azure Machine Learning, można użyć narzędzia etykietowania (podgląd publiczny) szybko przygotować dane dla projektu usługi Machine Learning. W tym artykule opisano:

> [!div class="checklist"]
> * Jak uzyskać dostęp do projektów etykietowania
> * Narzędzia do etykietowania
> * Jak korzystać z narzędzi do określonych zadań etykietowania

## <a name="prerequisites"></a>Wymagania wstępne

* Adres URL portalu etykietowania dla uruchomionego projektu etykietowania danych
* [Konto Microsoft](https://account.microsoft.com/account) lub konto usługi Azure Active Directory dla organizacji i projektu

> [!NOTE]
> Administrator projektu może znaleźć adres URL portalu etykietowania na karcie **Szczegóły** na stronie **Szczegóły projektu.**

## <a name="sign-in-to-the-projects-labeling-portal"></a>Zaloguj się do portalu etykietowania projektu

Przejdź do adresu URL portalu etykietowania dostarczonego przez administratora projektu. Zaloguj się przy użyciu konta e-mail używanego przez administratora do dodawania Cię do zespołu. Dla większości użytkowników będzie to Twoje konto Microsoft. Jeśli projekt etykietowania korzysta z usługi Azure Active Directory, w ten sposób zalogujesz się.

## <a name="understand-the-labeling-task"></a>Opis zadania etykietowania

Po zalogowaniu się zostanie wyświetlona strona przeglądu projektu.

Przejdź do **zobacz szczegółowe instrukcje**. Te instrukcje są specyficzne dla projektu. Wyjaśniają one rodzaj danych, z którymi masz do czynienia, jak należy podejmować decyzje i inne istotne informacje. Po przeczytaniu tych informacji wróć do strony projektu i wybierz **pozycję Rozpocznij etykietowanie**.

## <a name="common-features-of-the-labeling-task"></a>Typowe cechy zadania etykietowania

We wszystkich zadaniach etykietowania obrazów można wybrać odpowiedni znacznik lub znaczniki z zestawu określonego przez administratora projektu. Pierwsze dziewięć tagów można wybrać za pomocą klawiszy numerycznych na klawiaturze.  

W zadaniach klasyfikacji obrazów można wybrać wyświetlanie wielu obrazów jednocześnie. Użyj ikon nad obszarem obrazu, aby wybrać układ. 

Aby zaznaczyć wszystkie wyświetlane obrazy jednocześnie, użyj opcji **Zaznacz wszystkie**. Aby wybrać poszczególne obrazy, użyj okrągłego przycisku zaznaczenia w prawym górnym rogu obrazu. Aby zastosować znacznik, należy wybrać co najmniej jeden obraz. Jeśli wybierzesz wiele obrazów, każdy wybrany znacznik zostanie zastosowany do wszystkich zaznaczonych obrazów.

Tutaj wybraliśmy układ dwa na dwa i mamy zamiar zastosować tag "Ssak" do obrazów niedźwiedzia i orki. Obraz rekina został już oznaczony jako "Chrzęstna ryba", a legwan nie został jeszcze oznaczony.

![Wiele układów i zaznaczeń obrazu](./media/how-to-label-images/layouts.png)

> [!Important] 
> Układy przełączają się tylko wtedy, gdy masz nową stronę danych bez etykiety. Przełączanie układów czyści prace tagowania strony w toku.

Platforma Azure włącza przycisk **Prześlij** po oznaczeniu wszystkich obrazów na stronie. Wybierz **pozycję Prześlij,** aby zapisać swoją pracę.

Po przesłaniu tagów dla danych w zasięgu ręki, Platforma Azure odświeża stronę z nowym zestawem obrazów z kolejki pracy.

### <a name="assisted-machine-learning"></a>Wspomagane uczenie maszynowe 

Algorytmy uczenia maszynowego mogą być wyzwalane podczas zadania klasyfikacji wieloklasowej lub wieloznakowej. Jeśli te algorytmy są włączone w projekcie, mogą pojawić się następujące informacje:

* Po oznaczeniu niektórych obrazów, w górnej części ekranu obok nazwy projektu mogą być widoczne **zadania klastrowane.**  Oznacza to, że obrazy są zgrupowane w celu przedstawienia podobnych obrazów na tej samej stronie.  Jeśli tak, przełącz się do jednego z wielu widoków obrazu, aby skorzystać z grupowania.  

* W późniejszym momencie obok nazwy projektu może zostać wyświetlony widok **Zadania z prelabeled.**  Obrazy pojawią się z sugerowaną etykietą, która pochodzi z modelu klasyfikacji uczenia maszynowego. Żaden model uczenia maszynowego nie ma 100% dokładności. Chociaż używamy tylko obrazów, dla których model jest pewny, te obrazy mogą być nadal niepoprawnie wstępnie oznakowane.  Gdy zobaczysz te etykiety, popraw wszystkie nieprawidłowe etykiety przed przesłaniem strony.  

Szczególnie na początku projektu etykietowania model uczenia maszynowego może być wystarczająco dokładny, aby wstępnie oznaczyć mały podzbiór obrazów. Po oznaczeniu tych obrazów, projekt etykietowania powróci do ręcznego etykietowania, aby zebrać więcej danych do następnej rundy szkolenia modelu. Z biegiem czasu model stanie się bardziej pewny siebie co do większej liczby obrazów, co spowoduje więcej zadań z nadlaniem wstępnym w dalszej części projektu.

## <a name="tag-images-for-multi-class-classification"></a>Tagowanie obrazów dla klasyfikacji wieloklasowej

Jeśli projekt ma typ "Klasyfikacja obrazów wieloklasowa", do całego obrazu zostanie przypisany pojedynczy znacznik. Aby w dowolnym momencie zapoznać się ze **wskazówkami,** przejdź do strony Instrukcje i wybierz pozycję **Wyświetl szczegółowe instrukcje**.

Jeśli zdasz sobie sprawę, że po przypisaniu znacznika do obrazu popełniłeś błąd, możesz go naprawić. Wybierz **"X"** na etykiecie wyświetlanej pod obrazem, aby wyczyścić znacznik. Możesz też wybrać obraz i wybrać inną klasę. Nowo wybrana wartość zastąpi poprzednio zastosowany znacznik.

## <a name="tag-images-for-multi-label-classification"></a>Tagowanie obrazów dla klasyfikacji wieloznakowej

Jeśli pracujesz nad projektem typu "Klasyfikacja obrazów multi-label", zastosujesz jeden *lub więcej* tagów do obrazu. Aby wyświetlić wskazówki dotyczące projektu, wybierz **pozycję Instrukcje** i przejdź do **strony Wyświetlanie szczegółowych instrukcji**.

Zaznacz obraz, który chcesz oznaczyć, a następnie zaznacz znacznik. Znacznik zostanie zastosowany do wszystkich zaznaczonych obrazów, a następnie obrazy zostaną odznaczone. Aby zastosować więcej znaczników, należy ponownie wybrać obrazy. Następująca animacja pokazuje znakowanie z wieloma etykietami:

1. **Wybierz wszystko** służy do stosowania tagu "Ocean".
1. Pojedynczy obraz jest zaznaczony i oznaczony jako "Zbliżenie".
1. Trzy obrazy są zaznaczone i oznaczone jako "Szeroki kąt".

![Animacja pokazuje przepływ wielolabelowy](./media/how-to-label-images/multilabel.gif)

Aby poprawić błąd, kliknij "**X**", aby wyczyścić pojedynczy tag lub zaznaczyć obrazy, a następnie wybierz znacznik, który usuwa znacznik ze wszystkich wybranych obrazów. Ten scenariusz jest pokazany tutaj. Kliknięcie na "Land" spowoduje wyczyszczenie tego znacznika z dwóch wybranych obrazów.

![Zrzut ekranu pokazuje wiele deselections](./media/how-to-label-images/multiple-deselection.png)

Platforma Azure włączy przycisk **Prześlij** tylko po zastosowaniu co najmniej jednego tagu do każdego obrazu. Wybierz **pozycję Prześlij,** aby zapisać swoją pracę.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Oznaczanie obrazów i określanie obwiedni do wykrywania obiektów

Jeśli projekt ma typ "Identyfikacja obiektów (obwiednie)", określesz jedno lub więcej pól ograniczających na obrazie i zastosujesz znacznik do każdego pola. Obrazy mogą mieć wiele obwiedni, z których każde ma jeden znacznik. Użyj **Wyświetl szczegółowe instrukcje,** aby ustalić, czy w projekcie jest używanych wiele obwiedni.

1. Wybierz znacznik dla obwiedni, które chcesz utworzyć.
1. Wybierz narzędzie **Prostokątne** pole ![Prostokątne pole](./media/how-to-label-images/rectangular-box-tool.png) lub wybierz "R."
3. Kliknij i przeciągnij po przekątnej po stronie celu, aby utworzyć nierówne obwiedni. Aby dostosować obwiednię, przeciągnij krawędzie lub narożniki.

![Zrzut ekranu przedstawia tworzenie podstawowych obwiedni.](./media/how-to-label-images/bounding-box-sequence.png)

Aby usunąć obwiednię, kliknij obiekt docelowy w kształcie litery X, który pojawia się obok obwiedni po utworzeniu.

Nie można zmienić znacznika istniejącego obwiedni. Jeśli popełnisz błąd przypisania tagu, musisz usunąć obwiednię i utworzyć nową z poprawnym tagiem.

Domyślnie można edytować istniejące obwiednie. Narzędzie **Zablokuj/odblokuj regiony** ![Narzędzie](./media/how-to-label-images/lock-bounding-boxes-tool.png) Zablokuj/odblokowuj regiony lub "L" przełącza to zachowanie. Jeśli regiony są zablokowane, można zmienić tylko kształt lub położenie nowego obwiedni.

Użyj narzędzia ![ **manipulowania regionami** ](./media/how-to-label-images/regions-tool.png) Narzędzia do manipulowania regionami lub "M", aby dostosować istniejące obwiednię. Przeciągnij krawędzie lub narożniki, aby dostosować kształt. Kliknij we wnętrzu, aby móc przeciągnąć całe obwiednie. Jeśli nie możesz edytować regionu, prawdopodobnie przełącz narzędzie **Zablokuj/odblokuj regiony.**

Użyj narzędzia **Pole oparte na szablonach** Narzędzie ![Szablon-box tool](./media/how-to-label-images/template-box-tool.png) lub "T", aby utworzyć wiele obwiedni o tym samym rozmiarze. Jeśli obraz nie ma pól ograniczających i aktywujesz pola oparte na szablonie, narzędzie będzie tworzyć pola o wymiarach 50 na 50 pikseli. Jeśli utworzysz obwiednię, a następnie aktywujesz pola oparte na szablonie, wszystkie nowe obwiedni będą rozmiarem ostatniego utworzonego pola. Po umieszczeniu pola oparte na szablonach można zwymiarować. Zmiana rozmiaru pola opartego na szablonie tylko zmienić rozmiar tego konkretnego pola.

Aby usunąć *wszystkie* obwiednie na bieżącym obrazie, ![zaznacz narzędzie](./media/how-to-label-images/delete-regions-tool.png) **Usuń wszystkie regiony** Usuń regiony .

Po utworzeniu obwiedni dla obrazu wybierz pozycję **Prześlij,** aby zapisać pracę, lub praca w toku nie zostanie zapisana.

## <a name="finish-up"></a>Zakończenie

Po przesłaniu strony oznakowanych danych platforma Azure przypisuje nowe dane bez etykiety z kolejki roboczej. Jeśli nie ma już dostępnych danych bez etykiety, zostanie wyświetlony komunikat z informacją o tym wraz z linkiem do strony głównej portalu.

Po zakończeniu etykietowania wybierz swoje imię i nazwisko w prawym górnym rogu portalu etykietowania, a następnie wybierz pozycję **wyloguj**. Jeśli nie wylogujesz się, ostatecznie platforma Azure "przesunie czas" i przypisze twoje dane do innego labelera.

## <a name="next-steps"></a>Następne kroki

* Dowiedz [się, jak szkolić modele klasyfikacji obrazów na platformie Azure](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)

