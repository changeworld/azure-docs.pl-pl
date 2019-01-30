---
title: Poprawianie klasyfikatora — Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak poprawić jakość klasyfikatora.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: pafarley
ms.openlocfilehash: 72ba363201b27a8ca31c73af1d0cceb436de468d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209405"
---
# <a name="how-to-improve-your-classifier"></a>Jak poprawianie klasyfikatora

Dowiedz się, jak poprawić jakość usługi Custom Vision Service klasyfikatora. Jakość klasyfikatora jest zależna od kwoty, jakości i różnych danych etykietami, które zapewnia do niej zestaw danych jak o zrównoważonym obciążeniu jest. Dobre klasyfikatora zwykle ma zestaw danych szkoleniowych o zrównoważonym obciążeniu, który odzwierciedla co zostaną przesłane do klasyfikatora. Proces tworzenia takiego klasyfikatora jest często iteracyjne. Są często do wykonania kilku rund szkoleń do osiągnięcia oczekiwanych rezultatów.

Poniżej przedstawiono typowe kroki podjęte w celu poprawy klasyfikatora. Te kroki nie są reguły trudny i szybko, ale Algorytm heurystyczny, który pomoże Ci tworzyć lepsze klasyfikatora.

1. Pierwsze działanie szkolenia
1. Dodaj więcej obrazów i zrównoważenia danych
1. Ponowne próbkowanie
1. Dodawanie obrazów z różnymi tła, oświetlenia, rozmiar obiektu, kąt kamery i stylu
1. Ponowne szkolenie i źródła danych obrazu w celu prognozowania
1. Sprawdź wyniki przewidywań
1. Modyfikowanie istniejących danych treningowych

## <a name="data-quantity-and-data-balance"></a>Saldo danych i ilości danych

Najważniejsze jest przekazywanie wystarczająca ilość obrazów do szkolenia klasyfikatora. Co najmniej 50 obrazów na etykietę do zestawu szkoleniowego są zalecane jako punktu wyjścia. Przy użyciu mniejszej liczby obrazów jest silne ryzyko, że masz overfitting. Gdy numery wydajności może sugerować dobrej jakości, mogą wystąpić problemy rzeczywistych danych. Szkolenie klasyfikatora przy użyciu więcej obrazów ogólnie zwiększyć dokładność wyników przewidywań.

Kolejna kwestia jest związana, upewnij się, że Twoje dane jest równoważone. Na przykład 500 obrazy dla jednej etykiety i 50 obrazów inną etykietę dadzą imbalanced szkolenia zestawu danych powoduje modelu, który ma być bardziej precyzyjne w przewidywaniu jednej etykiety od innych. Jesteś prawdopodobne zobaczyć w poszukiwaniu lepszych wyników, jeśli to Ty masz co najmniej 1:2 stosunek między używanymi etykiety z najmniejszą liczbą obrazami i etykiety z większość obrazów. Na przykład jeśli etykiety z największą liczbą obrazów ma 500 obrazów, etykiety z co najmniej obrazami musi mieć co najmniej 250 obrazów do szkolenia.

## <a name="train-more-diverse-images"></a>Szkolenie bardziej zróżnicowane obrazów

Podaj obrazy, które reprezentują co będzie można przesłać do klasyfikatora podczas normalnego użytkowania. Na przykład jeśli uczony klasyfikatora "apple", klasyfikatora może okazać możliwie dokładny tylko szkolenie zdjęcia jabłek talerzy, ale upewnij prognozy na zdjęciach jabłek w drzewach. Łącznie z wielu różnych obrazów będzie upewnij się, że klasyfikatora nie jest obciążona i również uogólnić. Poniżej ustawiono bardziej zróżnicowane kilka metod, których można dokonać szkolenia:

__Tło:__ Podaj obrazów obiektu przed różnych kolorów tła (czyli owoce na tablicy, a owoców w zbiorze artykułów spożywczych). Zdjęcia w kontekście są lepsze niż zdjęcia przed neutralne tła, ponieważ zapewniają dodatkowe informacje dotyczące klasyfikatora.

![Obraz tła próbek](./media/getting-started-improving-your-classifier/background.png)

__Oświetlenie:__ Udostępnianie obrazów zróżnicowane oświetlenia (oznacza to, przekierowanie z usługą flash, wysoka, itp.), zwłaszcza w przypadku, gdy obrazy używane w celu prognozowania mają różne oświetlenia. Warto również uwzględnić obrazów przy użyciu różnorodnych nasycenie, hue i jasności.

![Obraz przedstawiający przykłady oświetlenia](./media/getting-started-improving-your-classifier/lighting.png)

__Object Size:__ Podaj obrazów, w których obiekty są zależeć od rozmiaru przechwytywania różnych części obiektu. Na przykład zdjęcie kiście banany i zbliżenie banany jednego. Różne zmiany rozmiaru pomaga klasyfikatora generalize lepiej.

![Obraz przedstawiający rozmiar próbki](./media/getting-started-improving-your-classifier/size.png)

__Kąt aparatu:__ Podaj obrazów z opcją kątów kamery różne. Jeśli wszystkie zdjęcia są pobierane z zestawem kamer stałych (np. kamer nadzoru), upewnij się, że przypisać inną etykietę do każdej kamery, nawet wtedy, gdy przechwyceniem te same obiekty, aby uniknąć overfitting — modelowanie niepowiązanych obiektów (takich jak lampposts) jako klucza funkcji.

![Obraz przedstawiający przykłady kąt](./media/getting-started-improving-your-classifier/angle.png)

__Styl:__ Podaj obrazy różnych stylów w tej samej klasy (czyli różne rodzaje cytrusów). Jednak w przypadku obrazów obiektów znacząco różne style (czyli Mickey myszy a rat rzeczywistych), zalecane jest oznaczenie ich jako osobne klasy, aby lepiej reprezentują ich różne funkcje.

![Obraz przedstawiający próbki stylu](./media/getting-started-improving-your-classifier/style.png)

## <a name="use-images-submitted-for-prediction"></a>Korzystanie z obrazów przesłane do prognozowania

Custom Vision Service są przechowywane obrazy przesłane do endpoint prognoz. Aby poprawić klasyfikatora, należy używać tych obrazów, użyj następujące czynności:

1. Aby wyświetlić obrazy przesyłany do usługi klasyfikatora, otwórz [strony sieci web Custom Vision](https://customvision.ai), przejdź do swojego projektu i wybierz __prognozy__ kartę. Widok domyślny pokazuje obrazów z bieżącą iterację. Możesz użyć __iteracji__ rozwijane pole, aby wyświetlić obrazy przesłane podczas poprzednich iteracji.

    ![Obraz karty prognozy](./media/getting-started-improving-your-classifier/predictions.png)

2. Umieść kursor nad obrazu, aby wyświetlić tagi, które zostały przewidywane według klasyfikatora. Obrazy są oceniane tak, aby obrazy, które może przynieść większość zysków klasyfikatora znajdowały się u góry. Aby wybrać inny sortowanie, użyj __sortowania__ sekcji. Aby dodać obraz do istniejących danych szkoleniowych, wybierz obraz, wybierz poprawny tag i kliknij __Zapisz i Zamknij__. Obraz zostanie usunięty z __prognozy__ i dodane do uczone obrazy. Można je wyświetlić, wybierając __Uczone obrazy__ kartę.

    ![Obraz strony znakowania](./media/getting-started-improving-your-classifier/tag.png)

3. Użyj __Train__ przycisk doskonalenie klasyfikatora.

## <a name="visually-inspect-predictions"></a>Wizualnie badać prognozy

Aby przeprowadzić inspekcję prognozy obrazu, wybierz __Uczone obrazy__ , a następnie wybierz pozycję __historii iteracji__. Obrazy z czerwoną otoczkę zostały nieprawidłowo przewidzieć.

![Obraz przedstawiający historii iteracji](./media/getting-started-improving-your-classifier/iteration.png)

Czasami kontroli można zidentyfikować wzorce, które następnie można usunąć, dodając dodatkowe szkolenie danych lub modyfikując istniejące dane szkoleniowe. Na przykład klasyfikatora dla firmy apple, a wapna może niepoprawnie etykietą wszystkich jabłek zielony wapna. Dzięki temu można rozwiązać ten problem, dodając i dostarcza dane szkoleniowe, zawierający obrazy oznakowane jabłek zielony.

## <a name="unexpected-classification"></a>Nieoczekiwany klasyfikacji

Czasami klasyfikatora niepoprawnie uzyskuje informacje o właściwości, które są wspólne dla obrazów. Na przykład jeśli tworzysz klasyfikatora jabłek a cytrusów i są podane obrazy jabłka w ręce i citrus w białe talerzy klasyfikatora może szkolenie dla ręce a białe talerzy zamiast jabłek a cytrusów.

![Obraz przedstawiający nieoczekiwany klasyfikacji](./media/getting-started-improving-your-classifier/unexpected.png)

Aby rozwiązać ten problem, użyj powyższe wskazówki dotyczące szkolenia z bardziej zróżnicowane obrazów: Podaj obrazów z różnymi kątami, tła, rozmiar obiektu, grup i innych wariantów.

## <a name="negative-image-handling"></a>Obsługa ujemna obrazu

Custom Vision Service obsługuje niektóre obsługi automatycznego obrazów ujemna. W przypadku której tworzysz gronowego a banany klasyfikatora, a następnie przesłać obraz butów w celu prognozowania klasyfikatora powinny ocena tego obrazu jako 0% zarówno gronowego i banany.

Z drugiej strony w przypadku obrazów ujemna odmianą obrazy używane w szkolenia, prawdopodobnie modelu będzie klasyfikowania obrazów ujemna jako klasę etykietami z powodu podobieństwa doskonałe. Na przykład jeśli masz pomarańczowy, a grejpfrutów klasyfikatora, a kanał w obrazie clementine, jego może wynik clementine pomarańczowa. Może to nastąpić, ponieważ wiele funkcji clementine (kolor, kształt, Tekstura, naturalnych habitat itp.) w przypadku Pomarańcze podobne.  W przypadku obrazów ujemna tego rodzaju, zalecane jest tworzenie jednej lub więcej rozdzielić znaczniki ("Other") i etykiety ujemna obrazy z tym znacznikiem podczas szkolenia zezwolić na model, który ma lepsze rozróżnienie tych klas.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak można przetestować obrazy programowo, przesyłając je do interfejsu API prognoz.

> [!div class="nextstepaction"]
[Użyj interfejsu API prognoz.](use-prediction-api.md)
