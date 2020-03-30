---
title: Przewidywanie odpowiedzi za pomocą modeli regresji
titleSuffix: ML Studio (classic) - Azure
description: Jak utworzyć prosty model regresji, aby przewidzieć cenę w data science dla początkujących wideo 4. Zawiera regresję liniową z danymi docelowymi.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 6ad9c7912eee6c3f5ec55b9cd7ab340bc79c9db7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837768"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Prognozowanie odpowiedzi za pomocą prostego modelu
## <a name="video-4-data-science-for-beginners-series"></a>Wideo 4: Data Science dla początkujących serii
Dowiedz się, jak utworzyć prosty model regresji, aby przewidzieć cenę diamentu w data science dla początkujących wideo 4. Narysujemy model regresji z danymi docelowymi.

Aby w pełni wykorzystać wyniki serii, obejrzyj je wszystkie. [Przejdź do listy filmów](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Inne filmy z tej serii
*Data Science dla początkujących* to krótkie wprowadzenie do nauki o danych w pięciu krótkich filmach.

* Film 1: [Odpowiedzi na dane z 5 pytań](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 s)*
* Film 2: [Czy Twoje dane są gotowe do nauki o danych?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek.)*
* Wideo 3: [Zadaj pytanie, na które możesz odpowiedzieć danymi](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 s)*
* Film 4: Przewidywanie odpowiedzi za pomocą prostego modelu
* Wideo 5: [Kopiowanie pracy innych osób w celu nauki o danych](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Transkrypcja: Przewiduj odpowiedź za pomocą prostego modelu
Witamy w czwartym filmie z serii "Data Science for Beginners". W tym jednym zbudujemy prosty model i dokonać przewidywania.

*Model* to uproszczona opowieść o naszych danych. Pokażę ci, o co mi chodzi.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Zbierz odpowiednie, dokładne, podłączone, wystarczające dane
Powiedzmy, że chcę kupić diament. Mam pierścionek, który należał do mojej babci z ustawieniem na 1,35 karatowego diamentu i chcę zorientować się, ile to będzie kosztować. Biorę notatnik i długopis do sklepu jubilerskiego, i zapisać cenę wszystkich diamentów w przypadku i ile ważą w karatach. Począwszy od pierwszego diamentu - to 1,01 karatów i 7366 dolarów.

Teraz przechodzę i robię to dla wszystkich innych diamentów w sklepie.

![Kolumny danych diamentowych](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Zauważ, że nasza lista ma dwie kolumny. Każda kolumna ma inny atrybut - waga w karatach i cenie - a każdy wiersz jest pojedynczym punktem danych reprezentującym pojedynczy romb.

Stworzyliśmy tutaj mały zestaw danych - tabelę. Należy zauważyć, że spełnia nasze kryteria jakości:

* Dane **są istotne** - waga jest zdecydowanie związana z ceną
* To **jest dokładne** - dokładnie sprawdziliśmy ceny, które zapisujemy
* Jest **połączony** - w żadnej z tych kolumn nie ma pustych spacji
* I, jak zobaczymy, to **wystarczająco dużo** danych, aby odpowiedzieć na nasze pytanie

## <a name="ask-a-sharp-question"></a>Zadaj ostre pytanie
Teraz postawimy nasze pytanie w ostry sposób: "Ile będzie kosztować zakup 1,35-karatowego diamentu?"

Nasza lista nie zawiera diamentu 1,35-karatowego, więc będziemy musieli użyć reszty naszych danych, aby uzyskać odpowiedź na pytanie.

## <a name="plot-the-existing-data"></a>Wykreślanie istniejących danych
Pierwszą rzeczą, którą zrobimy, jest narysowanie poziomej linii liczbowej, zwanej osią, aby wykreślić wagi. Zakres odważników wynosi od 0 do 2, więc narysujemy linię, która obejmuje ten zakres i umieścimy kleszcze dla każdej połowy karatu.

Następnie narysujemy oś pionową, aby zarejestrować cenę i połączyć ją z poziomą osią wagi. Będzie to w jednostkach dolarów. Teraz mamy zestaw osi współrzędnych.

![Osie wagowe i cenowe](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Teraz weźmiemy te dane i zamienimy go w *wykres punktowy.* Jest to świetny sposób wizualizacji zestawów danych liczbowych.

Dla pierwszego punktu danych, mamy gałki ocznej pionowej linii na 1,01 karatów. Następnie gałki ocznej poziomej linii na 7.366 dolarów. Tam, gdzie się spotykają, rysujemy kropkę. Jest to nasz pierwszy diament.

Teraz przechodzimy przez każdy diament na tej liście i robimy to samo. Kiedy przechodzimy, to jest to, co otrzymujemy: kilka kropek, po jednej dla każdego diamentu.

![Wykres punktowy](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Rysowanie modelu przez punkty danych
Teraz, jeśli spojrzysz na kropki i zez, kolekcja wygląda jak gruba, rozmyta linia. Możemy wziąć nasz znacznik i narysować przez niego linię prostą.

Rysując linię, stworzyliśmy *model*. Pomyśl o tym, jak biorąc prawdziwy świat i co uproszczone kreskówki wersji. Teraz kreskówka jest w błędzie - linia nie przechodzi przez wszystkie punkty danych. Ale jest to przydatne uproszczenie.

![Linia regresji liniowej](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Fakt, że wszystkie kropki nie przechodzą dokładnie przez linię jest OK. Analitycy danych wyjaśniają to, mówiąc, że istnieje model - to jest linia - a następnie każda kropka ma jakiś *hałas* lub *wariancję* z nim związaną. Istnieje podstawowa idealna relacja, a potem jest ziarnisty, prawdziwy świat, który dodaje hałasu i niepewności.

Ponieważ staramy się odpowiedzieć na pytanie *Ile?* to się nazywa *regresja*. A ponieważ używamy linii prostej, jest to *regresja liniowa.*

## <a name="use-the-model-to-find-the-answer"></a>Użyj modelu, aby znaleźć odpowiedź
Teraz mamy model i zadajemy to nasze pytanie: Ile będzie kosztował diament 1,35 karatowy?

Aby odpowiedzieć na nasze pytanie, gałki ocznej 1,35 karatów i narysować pionową linię. Tam, gdzie przecina linię modelu, gałki ocznej poziomej linii do osi dolara. Trafia dokładnie 10 000. Boom! To odpowiedź: 1,35-karatowy diament kosztuje około 10 000 dolarów.

![Znajdź odpowiedź na model](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Tworzenie interwału ufności
To naturalne, aby zastanawiać się, jak precyzyjne jest to przewidywanie. Warto wiedzieć, czy diament 1,35 karatowy będzie bardzo zbliżony do 10 000 USD, czy też dużo wyższy czy niższy. Aby to zrozumieć, narysujmy kopertę wokół linii regresji, która zawiera większość kropek. Ta koperta nazywa się naszym *przedziałem zaufania*: Jesteśmy pewni, że ceny mieszczą się w tej kopercie, ponieważ w przeszłości większość z nich miała. Możemy narysować jeszcze dwie poziome linie, z których linia 1,35 karatowa przecina górną i dolną część koperty.

![Interwał ufności](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Teraz możemy powiedzieć coś o naszym przedziale ufności: Możemy śmiało powiedzieć, że cena diamentu 1,35 karatowego wynosi około 10 000 USD - ale może to być nawet 8000 USD i może wynosić nawet 12 000 USD.

## <a name="were-done-with-no-math-or-computers"></a>Skończyliśmy bez matematyki i komputerów
Zrobiliśmy to, za co płacą analitycy danych, i zrobiliśmy to tylko rysując:

* Zadaliśmy pytanie, na które moglibyśmy odpowiedzieć za pomocą danych
* Zbudowaliśmy *model* przy użyciu *regresji liniowej*
* Zrobiliśmy *prognozę,* wraz z *przedziałem ufności*

Nie korzystaliśmy z matematyki ani komputerów.

Gdybyśmy mieli więcej informacji, np.

* cięcie diamentu
* zmiany kolorów (jak blisko diament jest biały)
* liczba wtrąceń w diament

... wtedy mielibyśmy więcej kolumn. W takim przypadku matematyka staje się pomocna. Jeśli masz więcej niż dwie kolumny, trudno jest narysować kropki na papierze. Matematyka pozwala bardzo ładnie dopasować tę linię lub płaszczyznę do danych.

Ponadto, jeśli zamiast garści diamentów, mieliśmy dwa tysiące lub dwa miliony, to można to zrobić znacznie szybciej z komputerem.

Dzisiaj rozmawialiśmy o tym, jak wykonać regresję liniową i dokonaliśmy prognozowania przy użyciu danych.

Pamiętaj, aby sprawdzić inne klipy wideo w "Data Science dla początkujących" z Microsoft Azure Machine Learning Studio (classic).

## <a name="next-steps"></a>Następne kroki
* [Wypróbuj pierwszy eksperyment nauki o danych z Machine Learning Studio (klasyczny)](create-experiment.md)
* [Wprowadzenie do uczenia maszynowego na platformie Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
