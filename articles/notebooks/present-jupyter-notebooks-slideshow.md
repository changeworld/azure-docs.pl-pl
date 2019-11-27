---
title: Przedstawia notesu programu Jupyter w formie pokazu slajdów na platformie Azure
description: Jak skonfigurować komórek w trybie pokazu slajdów w notesu programu Jupyter, a następnie prezentować pokaz slajdów, przy użyciu rozszerzenia ROŚNIE.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: d180eaf571fa57191e3b0856020b02f05d05e344
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277589"
---
# <a name="run-a-notebook-slideshow"></a>Uruchamianie notesu pokaz slajdów

Notesy platformy Azure jest wstępnie skonfigurowana za pomocą rozszerzenia pokaz slajdów Jupyter/IPython (wzrost), który umożliwia prezentowanie notesu bezpośrednio jako pokaz slajdów. W pokaz slajdów komórek są zwykle wyświetlane pojedynczo przy użyciu rozmiaru czcionki, która jest odpowiednia dla stawać na dużym ekranie, na które może być nadal uruchomiony kod zamiast przełączania na komputerze oddzielnym pokaz.

Na poniższej ilustracji przedstawiono widok notesu standardowego, w którym można zobaczyć języka znaczników Markdown i komórki kodu wszystko ze sobą:

![Notes w standardowy widok](media/slideshow/slideshow-notebook-view.png)

Po rozpoczęciu pokazu slajdów pierwsza komórka zostanie powiększona, aby wypełnić przeglądarkę, gdzie **X** w górnym rogu opuszcza pokaz slajdów **?** w dolnym lewym wyświetla skróty klawiaturowe i strzałki w prawym dolnym rogu przechodzić między slajdami:

![Notes w trybie pokaz slajdów](media/slideshow/slideshow-slide-view.png)

Przygotowywanie notebook pokazu slajdów obejmuje dwa podstawowe zadania:

1. Ponieważ komórek Markdown są renderowane przy użyciu duży rozmiar czcionki, część zawartości może nie być widoczne w pokaz slajdów. Dlatego zwykle ograniczają ilość tekstu w dowolnej komórki; Nagłówek z czterech do sześciu wiersze zwykle sprawdza się najlepiej. Jeśli masz więcej tekstu, Podziel te informacje do wielu komórek.

2. Skonfiguruj zachowanie każdej komórki w pokaz slajdów, za pomocą narzędzi komórki pokaz slajdów. Typy komórki określają zachowanie przycisków nawigacji.

## <a name="the-anatomy-of-a-slideshow"></a>Anatomia pokaz slajdów

Czy wykonać notesu losowych i używać go w ramach pokazu slajdów, zwykle znajdziesz czy wszystkie komórki są mieszane ze sobą, a większość zawartości jest ukryty u dołu okna przeglądarki. Aby ułatwić skuteczne prezentacji, należy następnie przypisać typu Pokaz slajdów do każdej komórki za pomocą narzędzi komórki pokaz slajdów:

1. W menu **Widok** wybierz **pasek narzędzi komórki** > **Pokaz slajdów**:

    ![Włączanie na pasku narzędzi Pokaz slajdów komórki](media/slideshow/slideshow-view-cell-toolbar.png)

1. Lista rozwijana **Typ slajdu** pojawia się w prawym górnym rogu każdej komórki w notesie:

    ![Pasek narzędzi Pokaz slajdów komórki](media/slideshow/slideshow-cell-toolbar.png)

1. Dla każdej komórki wybierz jeden z pięciu typów:

    ![Typy pokaz slajdów komórki](media/slideshow/slideshow-cell-slide-types.png)

    | Typ slajdu | Zachowanie |
    | --- | --- |
    | -(nie ustawiono) | Komórka jest wyświetlana przy użyciu poprzedniej komórki, często nie jest uwzględnione w pokaz slajdów. |
    | Slajd | Komórka jest podstawowym slajdów, przejście, używając strzałek lewego i prawego formantu nawigacji. |
    | Slajd podrzędnych | Komórka jest "podstawowym slajdów, przejście, za pomocą kontrolki nawigacji strzałkę w dół below". Zwraca strzałkę w górę do głównej slajdu. Slajdy podrzędne są używane do dodatkowej materiał, można pominąć w ścieżce głównej prezentacji, ale są łatwo dostępne, jeśli to konieczne. |
    | Fragment | Zawartość komórki pojawia się w kontekście poprzedni slajd lub podrzędną slajdów, korzystając z nawigacji strzałkę w dół (fragment zostanie usunięta, gdy za pomocą strzałki w górę). Fragment z komórkę kodu służy do sprawić, że kod pojawiają się w obrębie slajdu lub można użyć wielu fragmentów, można wprowadzić tekst, który punktory są wyświetlane pojedynczo (Zobacz przykład w następnej sekcji). Ponieważ fragmenty kompilacji bieżącego slajdu, nadmiarowe fragmenty nie będzie widoczny u dołu okna przeglądarki. |
    | Pomiń | Komórki nie jest wyświetlany w pokaz slajdów. |
    | Uwagi | W komórce jako notatki prelegenta, które nie są wyświetlane w pokaz slajdów. |

1. Początkowo warto wybrać opcję **slajdu** dla każdej komórki. Następnie można uruchomić pokaz slajdów i wprowadź odpowiednie zmiany.

### <a name="example-fragment-cells-for-bullet-items"></a>Przykład: fragmentu komórek elementów punktora

Aby punktory dla slajdu pojawiły się po jednym, umieść nagłówek slajdu w komórce o promocji z typem **slajdu** , a następnie umieść każdy punktor w oddzielnej komórce o promocji z typem **fragmentu** :

![Przykład tworzenia wielu komórek języka znaczników Markdown dla elementów punktora](media/slideshow/slideshow-fragments.png)

Ponieważ pokaz slajdów renderuje fragmenty z więcej pionowe odstępy niż gdy punktory znajdują się w tej samej komórki, nie można użyć dowolnej liczby punktów.

## <a name="run-the-slideshow"></a>Uruchom pokaz slajdów

1. Jeśli edytujesz wszystkie komórki z promocji, upewnij się, że są one uruchamiane w celu renderowania kodu HTML. w przeciwnym razie pojawiają się one w *postaci* promocji w pokazie slajdów.

1. Po skonfigurowaniu **typu slajdu** dla każdej komórki wybierz komórkę, w której ma zostać uruchomiony pokaz slajdów, a następnie wybierz przycisk **pokazu naciśnij i wyjdź** na głównym pasku narzędzi:

    ![Przycisk włączany/wyłączany ROSNĄĆ w pokazu slajdów na głównym pasku narzędzi](media/slideshow/slideshow-start.png)

1. Aby poruszać się między slajdy, a także fragmenty, należy użyć strzałek w lewo i w prawo w formancie nawigacji. Tekst w kontrolce pokazuje liczbę reprezentującą *slajd. Sub-slajdu*.

    ![Kontrola nawigacji pokaz slajdów](media/slideshow/slideshow-navigation-control.png)

1. Aby poruszać się między slajdy i slajdy podrzędnych, a także fragmenty, w górę i strzałkę w dół, jeśli włączona:

    ![Pokaz slajdów kontrolki podrzędne slajdów](media/slideshow/slideshow-navigation-control-subslide.png)

1. W komórce kodu użyj przycisku odtwarzania, do uruchomienia kodu; dane wyjściowe pojawia się na slajdzie:

    ![Przycisk Odtwórz, aby uruchomić komórkę kodu](media/slideshow/slideshow-run-code-cell.png)

    ![Wyjście komórki kodu, który pojawia się w pokaz slajdów](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Dane wyjściowe komórki jest uważany za część komórki w pokaz slajdów. Jeśli uruchamiasz komórce w notesie lub pokaz slajdów, dane wyjściowe pojawia się w innych widokach także. Aby wyczyścić dane wyjściowe, użyj **komórki** > **bieżące wyjście** > **Wyczyść** polecenie (dla bieżącej komórki) lub **komórkę** > wszystkie dane **wyjściowe** > **Wyczyść** (dla wszystkich komórek).

1. Gdy skończysz korzystać z pokazu slajdów, użyj **X** , aby powrócić do widoku notesu.

## <a name="next-steps"></a>Następne kroki

- [Instrukcje: Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Instrukcje: Instalowanie pakietów z poziomu notesu](install-packages-jupyter-notebook.md)
- [Instrukcje: korzystanie z plików danych](work-with-project-data-files.md)
- [Instrukcje: dostęp do zasobów danych](access-data-resources-jupyter-notebooks.md)
