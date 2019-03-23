---
title: Poprawianie klasyfikatora — Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak poprawić jakość klasyfikatora.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 13c0346324ae8e3cf3485985a9014f9999230630
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351443"
---
# <a name="how-to-improve-your-classifier"></a>Jak poprawianie klasyfikatora

W tym przewodniku dowiesz się, jak poprawić jakość usługi Custom Vision Service klasyfikatora. Jakość klasyfikatora zależy od ilości, jakości i różnych danych oznaczonych, podane jest i jak o zrównoważonym obciążeniu jest ogólny zestaw danych. Dobre klasyfikatora ma zestaw danych szkoleniowych o zrównoważonym obciążeniu, który odzwierciedla co zostaną przesłane do klasyfikatora. Proces tworzenia takiego klasyfikatora jest iteracyjne; są często do wykonania kilku rund szkoleń do osiągnięcia oczekiwanych rezultatów.

Poniżej przedstawiono ogólny schemat ułatwiające tworzenie dokładniejszych klasyfikatora:

1. Pierwsze działanie szkolenia
1. Dodaj więcej obrazów i danych; Ponowne szkolenie
1. Dodawanie obrazów z różnymi tła, oświetlenia, rozmiar obiektu, kąt kamery i styl; Ponowne szkolenie
1. Użyj nowych obrazów, aby przetestować prognoz
1. Modyfikowanie istniejących danych szkoleniowych według wyników przewidywań

## <a name="prevent-overfitting"></a>Zapobiegaj overfitting

Czasami klasyfikatora dowiesz się w celu prognozowania na podstawie dowolnego charakterystyki, które są wspólne dla obrazów. Na przykład jeżeli tworzysz klasyfikatora jabłek a cytrusów oraz obrazów jabłka w ręce i citrus była używana na tablicach białe klasyfikatora umożliwia nadmiernego znaczenie w ręce a talerzy zamiast jabłek a cytrusów.

![Obraz przedstawiający nieoczekiwany klasyfikacji](./media/getting-started-improving-your-classifier/unexpected.png)

Aby rozwiązać ten problem, użyj poniższych wskazówek na szkolenie z bardziej zróżnicowane obrazów: Podaj obrazów z różnymi kątami, tła, rozmiar obiektu, grup i innych zmian.

## <a name="data-quantity"></a>Ilość danych

Liczba uczone obrazy jest najważniejszym czynnikiem. Zalecamy używanie co najmniej 50 obrazów na etykietę jako punktu wyjścia. Przy użyciu mniejszej liczby obrazów istnieje większe ryzyko overfitting, a gdy numery wydajności może sugerować dobrej jakości, model może poradzić sobie z brakami rzeczywistych danych. 

## <a name="data-balance"></a>Saldo danych

Należy również wziąć pod uwagę odpowiednich ilości danych szkoleniowych. Na przykład przy użyciu obrazów 500 jednej etykiety i 50 obrazów na inną etykietę sprawia, że w przypadku zestawu imbalanced szkolenia. Spowoduje to model, który ma być bardziej precyzyjne w przewidywaniu jednej etykiety od innych. Jesteś prawdopodobne zobaczyć w poszukiwaniu lepszych wyników, jeśli to Ty masz co najmniej 1:2 stosunek między używanymi etykiety z najmniejszą liczbą obrazami i etykiety z większość obrazów. Na przykład w etykiecie o większość obrazów zawiera 500 obrazów, etykiety z obrazami co najmniej powinien mieć co najmniej 250 obrazów do szkolenia.

## <a name="data-variety"></a>Różnych danych

Pamiętaj używać obrazów, które reprezentują co będzie można przesłać do klasyfikatora podczas normalnego użytkowania. W przeciwnym razie klasyfikatora może dowiedzieć się, jak tworzyć prognozy na podstawie dowolnego charakterystyki, które są wspólne dla obrazów. Na przykład jeżeli tworzysz klasyfikatora jabłek a cytrusów oraz obrazów jabłka w ręce i citrus była używana na tablicach białe klasyfikatora umożliwia nadmiernego znaczenie w ręce a talerzy zamiast jabłek a cytrusów.

![Obraz przedstawiający nieoczekiwany klasyfikacji](./media/getting-started-improving-your-classifier/unexpected.png)

Aby rozwiązać ten problem, obejmują szereg obrazów, aby upewnić się, również uogólnić klasyfikatora. Poniżej ustawiono bardziej zróżnicowane kilka metod, których można dokonać szkolenia:

* __Tło:__ Podaj obrazów przed różnych kolorów tła obiektu. Zdjęcia w kontekstach fizyczne są lepsze niż zdjęcia przed neutralne tła, ponieważ zapewniają dodatkowe informacje dotyczące klasyfikatora.

    ![Obraz tła próbek](./media/getting-started-improving-your-classifier/background.png)

* __Oświetlenie:__ Udostępnianie obrazów zróżnicowane oświetlenia (który pochodzi z usługą flash, wysoka) i tak dalej, zwłaszcza w przypadku, gdy obrazy używane w celu prognozowania mają różne oświetlenia. Warto także używać obrazów z różnymi nasycenie, hue i jasności.

    ![Obraz przedstawiający przykłady oświetlenia](./media/getting-started-improving-your-classifier/lighting.png)

* __Object Size:__ Podaj obrazów, w których obiekty różnią się pod względem rozmiaru i liczby (na przykład zdjęcie kiście banany i zbliżenie banany pojedynczy). Różne zmiany rozmiaru pomaga klasyfikatora generalize lepiej.

    ![Obraz przedstawiający rozmiar próbki](./media/getting-started-improving-your-classifier/size.png)

* __Kąt aparatu:__ Podaj obrazów z opcją kątów kamery różne. Alternatywnie, jeśli wszystkie zdjęcia musi być stosowana z kamer stałych (np. kamer nadzoru), upewnij się, można przypisać inną etykietę do każdego obiektu występujące regularnie, aby uniknąć overfitting&mdash;interpretowanie niepowiązanych obiektów (takich jak lampposts) jako kluczową funkcją.

    ![Obraz przedstawiający przykłady kąt](./media/getting-started-improving-your-classifier/angle.png)

* __Styl:__ Podaj obrazy różnych stylów tej samej klasie (na przykład różnych odmian tej samej). Jednak w przypadku obiektów znacząco różne style (na przykład Mickey myszy, a myszy rzeczywistych) zalecamy ich etykiety jak osobnych klas w celu lepszego ich różne funkcje.

    ![Obraz przedstawiający próbki stylu](./media/getting-started-improving-your-classifier/style.png)

## <a name="use-prediction-images-for-further-training"></a>Korzystanie z obrazów prognoz do dalszego szkoleniowych

Jeśli używasz lub test klasyfikatora obraz po przesłaniu obrazów do endpoint prognoz usługi Custom Vision przechowuje te obrazy. Następnie można użyć w celu ulepszenia modelu.

1. Aby wyświetlić obrazy przesyłany do usługi klasyfikatora, otwórz [strony sieci web Custom Vision](https://customvision.ai), przejdź do swojego projektu i wybierz __prognozy__ kartę. Widok domyślny pokazuje obrazów z bieżącą iterację. Możesz użyć __iteracji__ rozwijane menu, aby wyświetlić obrazy przesłane podczas poprzednich iteracji.

    ![Zrzut ekranu przedstawiający prognozy są tym karcie przy użyciu obrazów w widoku](./media/getting-started-improving-your-classifier/predictions.png)

2. Umieść kursor nad obrazu, aby wyświetlić tagi, które zostały przewidywane według klasyfikatora. Obrazy są sortowane, tak, aby te, które może przynieść najbardziej ulepszenia klasyfikatora są wyświetlane u góry. Aby użyć innej metody sortowania, wybierz odpowiednią pozycję w __sortowania__ sekcji. 

    Aby dodać obraz do istniejących danych szkoleniowych, wybierz obraz, ustaw prawidłowe tagi i kliknij przycisk __Zapisz i Zamknij__. Obraz zostanie usunięty z __prognozy__ i dodać do zbioru uczone obrazy. Można je wyświetlić, wybierając __Uczone obrazy__ kartę.

    ![Obraz strony znakowania](./media/getting-started-improving-your-classifier/tag.png)

3. Następnie użyj __Train__ przycisk doskonalenie klasyfikatora.

## <a name="visually-inspect-predictions"></a>Wizualnie badać prognozy

Aby przeprowadzić inspekcję prognozy obrazu, przejdź do __Uczone obrazy__ , a następnie wybierz swoje poprzedniej iteracji szkolenia w **iteracji** menu rozwijane i zaznacz jeden lub więcej tagów w obszarze **tagi** sekcji. Widok powinien być teraz ustawiony czerwoną otoczkę wokół pozycji wszystkich obrazów, dla których modelu nie można poprawnie przewidzieć danym znacznikiem.

![Obraz przedstawiający historii iteracji](./media/getting-started-improving-your-classifier/iteration.png)

Czasami kontroli można zidentyfikować wzorce, które następnie można usunąć, dodając więcej danych szkoleniowych lub modyfikując istniejące dane szkoleniowe. Na przykład klasyfikatora jabłek a wapna może niepoprawnie etykietą wszystkich jabłek zielony wapna. Następnie można rozwiązać ten problem, przez dodanie i dostarcza dane szkoleniowe, zawierający obrazy oznakowane jabłek zielony.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku przedstawiono kilka technik, które umożliwiają bardziej precyzyjne obraz niestandardowy model klasyfikacji. Dowiedz się, jak przetestować obrazy programowo, przesyłając je do interfejsu API prognoz.

> [!div class="nextstepaction"]
> [Użyj interfejsu API prognoz.](use-prediction-api.md)
