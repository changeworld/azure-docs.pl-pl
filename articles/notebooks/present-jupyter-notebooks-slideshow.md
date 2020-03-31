---
title: Przedstawianie notesu Jupyter jako pokazu slajdów w wersji Zapoznawczej notesów platformy Azure
description: Dowiedz się, jak skonfigurować komórki do trybu pokazu slajdów w notesie Jupyter, a następnie zaprezentować pokaz slajdów za pomocą rozszerzenia RISE.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 05dd3d9c5580e208ecf6f9e6d762476b0b493a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647122"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Uruchamianie pokazu slajdów notesu w usłudze Azure Notebooks Preview

Notesy platformy Azure jest wstępnie skonfigurowany z Jupyter/IPython Slideshow Extension (RISE), który umożliwia prezentowanie notesu bezpośrednio jako pokaz slajdów. W pokazie slajdów komórki są zazwyczaj wyświetlane po kolei przy użyciu rozmiaru czcionki, który jest odpowiedni do prezentowania na dużych ekranach i nadal można uruchomić kod, zamiast przełączać się na oddzielny komputer demonstracyjny.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Na poniższej ilustracji przedstawiono standardowy widok notesu, w którym można wyświetlić markdown i komórki kodu razem:

![Notes w widoku standardowym](media/slideshow/slideshow-notebook-view.png)

Po uruchomieniu pokazu slajdów, pierwsza komórka jest powiększony, aby wypełnić przeglądarkę, gdzie **X** w lewym górnym rogu wychodzi z pokazu slajdów, **?** w lewym dolnym dolnym połówka wyświetla skróty klawiaturowe, a strzałki w prawym dolnym dolnym prawa nawigują między slajdami:

![Notes w trybie pokazu slajdów](media/slideshow/slideshow-slide-view.png)

Przygotowanie notesu do pokazu slajdów obejmuje dwa podstawowe działania:

1. Ponieważ komórki Markdown są renderowane przy dużych czcionkach, niektóre treści mogą nie być widoczne w pokazie slajdów. W ten sposób zazwyczaj ogranicza się ilość tekstu w danej komórce; nagłówek z czterema do sześciu wierszy zwykle działa najlepiej. Jeśli masz więcej tekstu, podziel te informacje na wiele komórek.

2. Skonfiguruj zachowanie każdej komórki w pokazie slajdów za pomocą paska narzędzi komórki pokazu slajdów. Typy komórek określają zachowanie przycisków nawigacyjnych.

## <a name="the-anatomy-of-a-slideshow"></a>Anatomia pokazu slajdów

Jeśli weźmiesz losowy notes i użyjesz go do pokazu slajdów, zazwyczaj okaże się, że wszystkie komórki są pomieszane ze sobą, a większość zawartości jest ukryta u dołu okna przeglądarki. Aby stworzyć skuteczną prezentację, należy przypisać typ pokazu slajdów do każdej komórki za pomocą paska narzędzi komórki pokazu slajdów:

1. W menu **Widok** wybierz polecenie Pokaz > **slajdów** **paska narzędzi komórki:**

    ![Włączanie paska narzędzi pokazu slajdów komórki](media/slideshow/slideshow-view-cell-toolbar.png)

1. W prawym górnym rogu każdej komórki notesu pojawi się listy rozwijanej **Typ slajdu:**

    ![Pasek narzędzi pokazu slajdów komórki](media/slideshow/slideshow-cell-toolbar.png)

1. Dla każdej komórki wybierz jeden z pięciu typów:

    ![Typy pokazu slajdów komórek](media/slideshow/slideshow-cell-slide-types.png)

    | Typ slajdu | Zachowanie |
    | --- | --- |
    | - (nie ustawiono) | Komórka jest wyświetlana z poprzednią komórką, co często nie jest pożądanym efektem w pokazie slajdów. |
    | Slajdów | Komórka jest slajdem podstawowym, poruszanym za pomocą strzałek w lewo i w prawo formantu nawigacji. |
    | Podsułka | Komórka jest "poniżej" slajdu podstawowego, przejdź do za pomocą strzałki w dół formantu nawigacji. Strzałka w górę powraca do slajdu podstawowego. Podszkieły są używane dla materiału pomocniczego, który można pominąć w głównej ścieżce prezentacji, ale jest łatwo dostępny w razie potrzeby. |
    | Fragment | Zawartość komórki pojawia się w kontekście poprzedniego slajdu lub podszkieli podczas używania strzałki nawigacji w dół (fragment jest usuwany podczas używania strzałki w górę). Można użyć fragmentu z komórką kodu, aby ten kod był wyświetlany w slajdzie, lub można użyć wielu fragmentów, aby punktory tekstowe były wyświetlane jeden po drugim (zobacz przykład w następnej sekcji). Ponieważ fragmenty są budowane na bieżącym slajdzie, nadmiar fragmentów nie będzie widoczny u dołu okna przeglądarki. |
    | Skip | Komórka nie jest wyświetlana w pokazie slajdów. |
    | Uwagi | Komórka zawiera jako notatki prelegenta, które nie są wyświetlane w pokazie slajdów. |

1. Początkowo warto wybrać opcję **Slajd** dla każdej komórki. Następnie można uruchomić pokaz slajdów i wprowadzić odpowiednie zmiany.

### <a name="example-fragment-cells-for-bullet-items"></a>Przykład: komórki fragmentu dla elementów punktorów

Aby punktory na slajdzie były wyświetlane jeden po drugim, umieść nagłówek slajdu w komórce Markdown z typem **slajdu,** a następnie umieść każdy punktor w osobnej komórce Markdown z **typem Fragment:**

![Przykład utworzenia wielu komórek znaczników dla elementów punktorów](media/slideshow/slideshow-fragments.png)

Ponieważ pokaz slajdów renderuje fragmenty z bardziej pionowymi odstępami niż wtedy, gdy wszystkie punktory znajdują się w tej samej komórce, użycie większej liczby elementów punktora może być nie.

## <a name="run-the-slideshow"></a>Uruchamianie pokazu slajdów

1. Jeśli masz edytowane komórki Markdown, upewnij się, że uruchomisz je w celu renderowania kodu HTML, w przeciwnym razie są wyświetlane *jako* Markdown w pokazie slajdów.

1. Po skonfigurowaniu tekstu **slajdu** dla każdej komórki zaznacz komórkę, za pomocą której chcesz rozpocząć pokaz slajdów, a następnie wybierz przycisk **Enter/Exit RISE Slideshow** na głównym pasku narzędzi:

    ![Przycisk Enter/Exit RISE Pokaz slajdów na głównym pasku narzędzi](media/slideshow/slideshow-start.png)

1. Aby poruszać się między slajdami, a także fragmentami, użyj strzałek w lewo i w prawo w formancie nawigacji. Tekst w formancie pokazuje liczbę reprezentującą *slide.sub-slide*.

    ![Kontrolka nawigacji w pokazie slajdów](media/slideshow/slideshow-navigation-control.png)

1. Aby poruszać się między slajdami i podsukaniami, a także fragmentami, użyj strzałek w górę i w dół, jeśli jest włączona:

    ![Kontrolki nawigacji w pokazie slajdów dla podsulatów](media/slideshow/slideshow-navigation-control-subslide.png)

1. W komórce kodu użyj przycisku odtwarzania, aby uruchomić kod; na slajdzie pojawi się wyjście:

    ![Przycisk Odtwórz, aby uruchomić komórkę kodu](media/slideshow/slideshow-run-code-cell.png)

    ![Dane wyjściowe komórki kodu są wyświetlane w pokazie slajdów](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Dane wyjściowe komórki są uważane za część komórki w pokazie slajdów. Jeśli uruchomisz komórkę w widoku notesu lub pokazu slajdów, dane wyjściowe pojawią się również w innym widoku. Aby wyczyścić dane**wyjściowe,** użyj polecenia > **Wyczyść** bieżące wyjścia **komórki** > (dla bieżącej komórki) lub > **Wyczyść** > **Clear** wszystkie wyjścia **komórki**(dla wszystkich komórek).

1. Po zakończeniu pokazu slajdów użyj **x,** aby powrócić do widoku notesu.

## <a name="next-steps"></a>Następne kroki

- [Jak: Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Jak: Instalowanie pakietów z poziomu notesu](install-packages-jupyter-notebook.md)
- [Jak: Praca z plikami danych](work-with-project-data-files.md)
- [Jak: Dostęp do zasobów danych](access-data-resources-jupyter-notebooks.md)
