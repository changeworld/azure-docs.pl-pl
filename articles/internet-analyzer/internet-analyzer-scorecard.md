---
title: Tłumaczenie karty wyników | Dokumenty firmy Microsoft
description: Często zadawane pytania dotyczące analizatora internetowego platformy Azure.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a05c704f62d93bdc110d8a5943dd266ca22eb5f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512888"
---
# <a name="interpreting-your-scorecard"></a>Interpretowanie karty wyników

Karta karty wyników zawiera zagregowane i analizowane wyniki testów. Każdy test ma swoje własne karty wyników. Karty wyników zawierają szybkie i miarodajne podsumowania wyników pomiarów, aby zapewnić wyniki oparte na danych dla wymagań sieciowych. Analizator internetowy zajmuje się analizą, co pozwala skupić się na decyzji.

Kartę karty wyników można znaleźć w menu zasobów analizatora internetowego. 


## <a name="filters"></a>Filtry

* ***Badanie:*** Wybierz test, dla którego chcesz wyświetlić wyniki — każdy test ma własną kartę wyników. Dane testowe pojawią się, gdy będzie wystarczająco dużo danych, aby zakończyć analizę - w większości przypadków powinno to nastąpić w ciągu 24 godzin. 
* ***Okres & data zakończenia:*** Codziennie generowane są trzy karty wyników – każda karta wyników odzwierciedla inny okres agregacji – 24 godziny przed (dzień), siedem dni przed (tydzień) i 30 dni przed (miesiąc). Użyj filtru "Data zakończenia", aby wybrać ostatni dzień okresu, który chcesz zobaczyć. 
* ***Państwo:*** Dla każdego kraju, w którego mają się użytkownicy końcowi, jest generowana karta wyników. Filtr globalny zawiera wszystkich użytkowników końcowych.

## <a name="measurement-count"></a>Liczba pomiarów

Liczba pomiarów wpływa na pewność analizy. Im wyższa liczba, tym dokładniejszy wynik. Co najmniej, badania powinny mieć na celu co najmniej 100 pomiarów na punkt końcowy dziennie. Jeśli liczba pomiarów jest zbyt niska, należy skonfigurować klienta JavaScript, aby był wykonywany częściej w aplikacji. Liczba pomiarów dla punktów końcowych A i B powinna być bardzo podobna, chociaż oczekuje się niewielkich różnic i w porządku. W przypadku dużych różnic wyniki nie powinny być zaufane.

## <a name="percentiles"></a>Percentyle

Opóźnienie, mierzone w milisekundach, jest popularną miarą pomiaru prędkości między źródłem a miejscem docelowym w Internecie. Dane opóźnienia nie są zwykle rozłożone (tzn. nie jest zgodna z "Krzywa dzwonka"), ponieważ istnieje "long-tail" dużych wartości opóźnienia, które pochylają wyniki podczas korzystania ze statystyk, takich jak średnia arytmetyczna. Alternatywnie percentyle zapewniają "wolny od dystrybucji" sposób analizowania danych. Na przykład mediana lub 50 percentyl podsumowuje środek rozkładu - połowa wartości jest powyżej niego, a połowa poniżej niego. Wartość 75 percentyla oznacza, że jest większa niż 75% wszystkich wartości w dystrybucji. Analizator internetowy odnosi się do percentyli w skrócie jako P50, P75 i P95.

Percentyle analizatora internetowego są _przykładowymi metrykami_. Jest to w przeciwieństwie do prawdziwej _metryki populacji_. Na przykład dzienna rzeczywista populacja mediana opóźnienia między studentami na Uniwersytecie Południowej Kalifornii i Microsoft jest mediana wartości opóźnienia wszystkich żądań w ciągu tego dnia. W praktyce pomiar wartości wszystkich wniosków jest niepraktyczny, więc zakładamy, że dość duża próbka jest reprezentatywna dla prawdziwej populacji.

Do celów analizy P50 (mediana) jest przydatna jako oczekiwana wartość dla rozkładu opóźnienia. Wyższe percentyle, takie jak P95, są przydatne do identyfikowania, jak duże opóźnienie jest w najgorszych przypadkach. Jeśli jesteś zainteresowany zrozumieniem opóźnienia klienta w ogóle, P50 jest poprawną metryką, na której należy się skupić. Jeśli chodzi o zrozumienie wydajności dla klientów o najgorszych wynikach, p95 powinien być w centrum uwagi. P75 jest równowaga między tymi dwoma.


## <a name="deltas"></a>Delty

Delta jest różnicą w wartościach metryk dla punktów końcowych A i B. Delta są obliczane w celu wykazania korzyści B nad A. Wartości dodatnie wskazują, że B działa lepiej niż A, podczas gdy wartości ujemne wskazują, że wydajność B jest gorsza. Delta może być bezwzględna (np. 10 milisekund) lub względna (5%).

## <a name="confidence-interval"></a>Interwał ufności 

Przedziały ufności (CI) to zakres wartości, które mają prawdopodobieństwo zawierające metrykę populacji, taką jak mediana, P75 lub średnia. Przestrzegamy wspólnej konwencji statystycznej dotyczącej korzystania z 95% CI.

W przypadku analizatora internetowego wąski przedział ufności jest dobry, ponieważ pokazuje, że metryka próbki jest prawdopodobnie bardzo zbliżona do rzeczywistej metryki populacji. Szeroki przedział ufności oznacza mniejszą pewność, że nasza metryka próbki odzwierciedla rzeczywistą metrykę populacji. Najlepszym sposobem na poprawę pw jest zwiększenie liczby pomiarów.

## <a name="time-series"></a>Szeregi czasowe 

Szeregi czasowe pokazuje, jak metryka zmienia się w czasie. W Internecie istnieje wiele czynników czasowych, które wpływają na wydajność, takich jak szczytowe okresy ruchu, różnice populacji w dni powszednie i weekendy i święta.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zapoznaj się [z omówieniem analizatora internetowego.](internet-analyzer-overview.md)
