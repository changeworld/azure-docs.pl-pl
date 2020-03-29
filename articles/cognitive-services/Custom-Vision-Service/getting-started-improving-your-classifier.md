---
title: Ulepszanie klasyfikatora - Usługa Niestandardowej Wizji
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak ilość, jakość i różnorodność danych może poprawić jakość klasyfikatora w usłudze Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: c2858d5f9bca662cbbcd48b2345a7dc2c7ae48b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718546"
---
# <a name="how-to-improve-your-classifier"></a>Jak poprawić swój klasyfikator

W tym przewodniku dowiesz się, jak poprawić jakość klasyfikatora usługi Custom Vision Service. Jakość klasyfikatora zależy od ilości, jakości i różnorodności danych oznaczonych etykietą, które go podasz i jak zrównoważony jest ogólny zestaw danych. Dobry klasyfikator ma zestaw danych zrównoważonego szkolenia, który jest reprezentatywny dla tego, co zostanie przesłane do klasyfikatora. Proces budowania takiego klasyfikatora jest iteracyjny; często trzeba wziąć kilka rund szkolenia, aby osiągnąć oczekiwane wyniki.

Poniżej przedstawiono ogólny wzorzec ułatwiającego tworzenie dokładniejszego klasyfikatora:

1. Trening w pierwszej rundzie
1. Dodaj więcej obrazów i wyważ dane; Przekwalifikować
1. Dodawanie obrazów o różnym tle, oświetleniu, rozmiarze obiektu, kącie kamery i stylu; Przekwalifikować
1. Użyj nowych obrazów do testowania prognozowania
1. Modyfikowanie istniejących danych szkoleniowych zgodnie z wynikami przewidywania

## <a name="prevent-overfitting"></a>Zapobieganie nadmiernemu dosyć

Czasami klasyfikator nauczy się przewidywać na podstawie dowolnych cech, które mają wspólne obrazy. Na przykład, jeśli tworzysz klasyfikator dla jabłek vs. cytrusów, a także używasz obrazów jabłek w rękach i cytrusów na białych talerzach, klasyfikator może nadać nadmierne znaczenie dłoniom kontra talerzom, a nie jabłek kontra cytrusy.

![Obraz nieoczekiwanej klasyfikacji](./media/getting-started-improving-your-classifier/unexpected.png)

Aby rozwiązać ten problem, należy użyć następujących wskazówek dotyczących treningu z bardziej zróżnicowanymi obrazami: podaj obrazy o różnych kątach, tłach, rozmiarze obiektu, grupach i innych odmianach.

## <a name="data-quantity"></a>Ilość danych

Liczba obrazów szkoleniowych jest najważniejszym czynnikiem. Zalecamy użycie co najmniej 50 obrazów na etykietę jako punktu wyjścia. Przy mniejszej liczbie obrazów istnieje większe ryzyko overfittingu i chociaż liczby wydajności mogą sugerować dobrą jakość, model może mieć problemy z rzeczywistymi danymi. 

## <a name="data-balance"></a>Saldo danych

Ważne jest również, aby wziąć pod uwagę względne ilości danych szkoleniowych. Na przykład użycie 500 obrazów dla jednej etykiety i 50 obrazów dla innej etykiety powoduje, że zestaw danych szkoleniowych jest niezrównoważony. Spowoduje to, że model będzie bardziej dokładne w przewidywaniu jednej etykiety niż inny. Jeśli zachowasz co najmniej stosunek 1:2 między etykietą z najmniejszą licznymi obrazami, a etykietą z największą ilością obrazami, możesz uzyskać lepsze wyniki. Jeśli na przykład etykieta z największą liczą 500 obrazów, etykieta z najmniejszą liczą co najmniej obrazów powinna mieć co najmniej 250 obrazów do szkolenia.

## <a name="data-variety"></a>Różnorodność danych

Pamiętaj, aby używać obrazów, które są reprezentatywne dla tego, co zostanie przesłane do klasyfikatora podczas normalnego użytkowania. W przeciwnym razie klasyfikator może nauczyć się przewidywać na podstawie dowolnych cech, które mają wspólne obrazy. Na przykład, jeśli tworzysz klasyfikator dla jabłek vs. cytrusów, a także używasz obrazów jabłek w rękach i cytrusów na białych talerzach, klasyfikator może nadać nadmierne znaczenie dłoniom kontra talerzom, a nie jabłek kontra cytrusy.

![Obraz nieoczekiwanej klasyfikacji](./media/getting-started-improving-your-classifier/unexpected.png)

Aby rozwiązać ten problem, dołącz różne obrazy, aby upewnić się, że klasyfikator może dobrze uogólniać. Poniżej znajduje się kilka sposobów, dzięki czemu zestaw treningowy będzie bardziej zróżnicowany:

* __Tło:__ Podaj obrazy obiektu przed różnymi tłami. Zdjęcia w naturalnych kontekstach są lepsze niż zdjęcia przed neutralnymi tłami, ponieważ dostarczają więcej informacji dla klasyfikatora.

    ![Obraz przykładów tła](./media/getting-started-improving-your-classifier/background.png)

* __Oświetlenie:__ Zapewnij obrazy z różnorodnym oświetleniem (czyli wykonanym z lampą błyskową, wysoką ekspozycją i tak dalej), zwłaszcza jeśli obrazy używane do przewidywania mają inne oświetlenie. Pomocne jest również używanie obrazów o różnym nasyceniu, odcieniu i jasności.

    ![Obraz próbek oświetlenia](./media/getting-started-improving-your-classifier/lighting.png)

* __Rozmiar obiektu:__ Podaj obrazy, w których obiekty różnią się wielkością i liczbą (na przykład zdjęcie kiści bananów i zbliżenie jednego banana). Różne rozmiary pomagają klasyfikatorowi lepiej uogólniać.

    ![Obraz próbek wielkości](./media/getting-started-improving-your-classifier/size.png)

* __Kąt kamery:__ Zapewnij zdjęcia wykonane pod różnymi kątami kamery. Alternatywnie, jeśli wszystkie zdjęcia muszą być wykonane za pomocą aparatów stacjonarnych (takich jak kamery monitoringu), należy przypisać&mdash;inną etykietę do każdego regularnie występującego obiektu, aby uniknąć nadmiernego interpretowania niepowiązanych obiektów (takich jak latarnie) jako kluczowej funkcji.

    ![Obraz próbek kątowych](./media/getting-started-improving-your-classifier/angle.png)

* __Styl:__ Podaj obrazy różnych stylów tej samej klasy (na przykład różnych odmian tego samego owocu). Jeśli jednak masz obiekty o drastycznie różnych stylach (takie jak Myszka Miki kontra mysz w prawdziwym życiu), zalecamy oznaczenie ich jako osobnych klas, aby lepiej reprezentować ich odrębne funkcje.

    ![Obraz próbek stylu](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Obrazy negatywne

W pewnym momencie projektu może być konieczne dodanie _próbek negatywnych,_ aby zwiększyć dokładność klasyfikatora. Próbki ujemne to te, które nie pasują do żadnego z pozostałych tagów. Podczas przesyłania tych obrazów zastosuj do nich specjalną etykietę **Negatywną.**

> [!NOTE]
> Usługa Custom Vision obsługuje niektóre automatyczne obsługa obrazów negatywnych. Na przykład, jeśli budujesz klasyfikator winogron vs banana i prześlij obraz buta do prognozowania, klasyfikator powinien uzyskać ten obraz jako bliski 0% zarówno dla winogron, jak i bananów.
> 
> Z drugiej strony, w przypadkach, gdy obrazy negatywne są tylko odmianą obrazów używanych w szkoleniu, jest prawdopodobne, że model klasyfikuje negatywne obrazy jako klasę etykietą ze względu na duże podobieństwa. Na przykład, jeśli masz klasyfikator pomarańczowy vs grejpfruta i karmisz na obrazie klementyny, może to wynik klementyny jako pomarańczy, ponieważ wiele cech klementyny przypomina te z pomarańczy. Jeśli obrazy negatywne są tego rodzaju, zaleca się utworzenie jednego lub więcej dodatkowych tagów (takich jak **Inne)** i oznaczenie negatywnych obrazów tym tagiem podczas szkolenia, aby umożliwić modelowi lepsze rozróżnienie między tymi klasami.

## <a name="use-prediction-images-for-further-training"></a>Użyj obrazów przewidywania do dalszego szkolenia

Podczas korzystania lub testowania klasyfikatora obrazu przez przesłanie obrazów do punktu końcowego przewidywania usługi Usługi Usługi Custom Vision przechowuje te obrazy. Następnie można ich użyć do poprawy modelu.

1. Aby wyświetlić obrazy przesłane do klasyfikatora, otwórz [stronę internetową Usługi Custom Vision](https://customvision.ai), przejdź do projektu i wybierz kartę __Prognozy.__ Widok domyślny pokazuje obrazy z bieżącej iteracji. Menu rozwijanego __Iteracja__ służy do wyświetlania obrazów przesłanych podczas poprzednich iteracji.

    ![zrzut ekranu karty prognoz z obrazami w widoku](./media/getting-started-improving-your-classifier/predictions.png)

2. Umieść wskaźnik myszy na obrazie, aby wyświetlić znaczniki, które były przewidywane przez klasyfikatora. Obrazy są sortowane tak, aby te, które mogą przynieść najwięcej ulepszeń do klasyfikatora, są wymienione u góry. Aby użyć innej metody sortowania, należy zaznaczyć tę metodę w sekcji __Sortowanie.__ 

    Aby dodać obraz do istniejących danych treningowych, zaznacz obraz, ustaw poprawne znaczniki, a następnie kliknij przycisk __Zapisz i zamknij__. Obraz zostanie usunięty z __prognoz__ i dodany do zestawu obrazów szkoleniowych. Można go wyświetlić, wybierając kartę __Obrazy szkoleniowe.__

    ![Obraz strony tagowania](./media/getting-started-improving-your-classifier/tag.png)

3. Następnie użyj __Train__ przycisku, aby przeszkolić klasyfikatora.

## <a name="visually-inspect-predictions"></a>Wizualnie sprawdzaj prognozy

Aby sprawdzić prognozy obrazów, przejdź do karty __Obrazy szkoleniowe,__ wybierz poprzednią iterację treningu w menu rozwijanym **Iteracja** i zaznacz jeden lub więcej tagów w sekcji **Znaczniki.** Widok powinien teraz wyświetlać czerwone pole wokół każdego z obrazów, dla których model nie udało się poprawnie przewidzieć danego tagu.

![Obraz historii iteracji](./media/getting-started-improving-your-classifier/iteration.png)

Czasami oględziny można zidentyfikować wzorce, które można następnie poprawić, dodając więcej danych szkoleniowych lub modyfikując istniejące dane szkoleniowe. Na przykład klasyfikator jabłek i wapna może niepoprawnie oznaczyć wszystkie zielone jabłka jako wapno. Następnie można rozwiązać ten problem, dodając i udostępniając dane szkoleniowe zawierające oznakowane obrazy zielonych jabłek.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku zapoznałeś się z kilkoma technikami, aby dostosować niestandardowy model klasyfikacji obrazów. Następnie dowiedz się, jak programowo testować obrazy, przesyłając je do interfejsu API przewidywania.

> [!div class="nextstepaction"]
> [Używanie interfejsu API prognozowania](use-prediction-api.md)
