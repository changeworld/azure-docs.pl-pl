---
title: Podziel kolumnę według przykładu przekształcenia przy użyciu usługi Azure Machine Learning Workbench
description: Dokument referencyjny dotyczący przekształcenia "Podziel kolumnę według przykładu"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 5fe292b92557b4786d638ac21c7adb1f50566feb
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35650263"
---
# <a name="split-column-by-example-transformation"></a>Podziel kolumnę według przykładu przekształcania
Ta transformacja predictively dzieli zawartość kolumny w granicach znaczących bez wprowadzania danych przez użytkownika. Algorytm podziału wybiera granice po przeanalizowaniu zawartość kolumny. Te granice można zdefiniować przez
* Naprawiono ogranicznika
* Wiele ograniczników dowolnego znajdujących się w szczególności kontekstów, lub
* Dane, wzorce lub niektóre typy jednostek

Użytkownicy mogą kontrolować podziału zachowania w trybie zaawansowanym, gdzie można określić ograniczniki lub przez zawierają przykłady żądaną dzielenia.

Teoretycznie operacje podziału również mogą być wykonywane w aplikacji Workbench, używając szeregu *Utwórz kolumnę pochodną według przykładu* przekształcenia. Jednak w przypadku kilku kolumn elementu pochodnego dla każdego z nich osobno nawet przy użyciu metody, na przykład może być bardzo czasochłonne. Podziel predykcyjne umożliwia proste dzielenie bez użytkownika konieczności zawierają przykłady dla każdej kolumny. 

## <a name="how-to-perform-this-transformation"></a>Jak przeprowadzić takie przekształcenie

1. Wybierz kolumnę, która ma być podziału. 
2. Wybierz **Podziel kolumnę według przykładu** z **przekształca** menu. Lub, kliknij prawym przyciskiem myszy w nagłówku wartości wybranej kolumny i wybierz **Podziel kolumnę według przykładu** z menu kontekstowego. Zostanie otwarty Edytor Przekształcanie i obok wybranej kolumny są dodawane nowe kolumny. W tym momencie Workbench analizuje kolumny wejściowej, określa granice podziału i syntetyzuje program do dzielenia kolumny w postaci wyświetlanej w siatce. Syntetyzowany program jest wykonywane względem wszystkich wierszy w kolumnie. Ograniczników, jeśli istnieją, są wykluczane z wynik końcowy.
3. Możesz kliknąć **trybu zaawansowanego** bardziej precyzyjną kontrolę nad przekształcania podziału. 
4. Przejrzyj dane wyjściowe i kliknij przycisk **OK** aby zaakceptować przekształcenie. 

Transformacja ma na celu utworzyć taką samą liczbę kolumn wynikowe we wszystkich wierszach. Jeśli wszystkie wiersze, nie można podzielić na podstawie granic, tworzy *null* dla wszystkich kolumn domyślnie. To zachowanie można zmienić w programie **trybu zaawansowanego**.

### <a name="transform-editor-advanced-mode"></a>Przekształcanie Edytor: Tryb zaawansowany
**Tryb zaawansowany** zapewnia bardziej rozbudowane środowisko do dzielenia kolumny. 

Wybieranie **zachować ogranicznik kolumny** obejmuje ograniczniki wynik końcowy. Ograniczniki są wyłączone domyślnie.

Określanie **ograniczniki** zastępuje logikę wyboru automatycznego ogranicznika. Wiele ograniczników, jeden w każdym wierszu, można określić jako **ograniczniki**. Te znaki są używane jako ogranicznika do dzielenia kolumny.

Czasami dzielenia wartości na podstawie granic tworzy różną liczbę kolumn niż większość innych. W takich przypadkach **wypełnienia kierunek** służy do określania kolejności, w którym powinno być wypełnione kolumny.

Kliknięcie **Pokaż sugerowanymi przykładami** wyświetla wiersze reprezentatywny dla użytkownika, który powinien zapewnić przykładem podziału. Użytkownik może kliknąć na **się** strzałkę po prawej stronie sugerowanego wiersza do podwyższenia poziomu wierszy jako przykład. 

Użytkownik może **Usuń kolumnę** lub **nowych kolumn do wstawienia** klikając prawym przyciskiem myszy na nagłówku **tabeli przykłady**.

Użytkownik może kopiować i Wklej wartości z jedną komórkę do innego celu zapewnienia przykładem podziału.

Użytkownik może przełączać się między **trybie podstawowym** i **trybu zaawansowanego** , klikając łącza w edytorze przekształcania.

### <a name="transform-editor-send-feedback"></a>Przekształcanie Edytor: wysyłanie opinii

Kliknięcie **Wyślij opinię** link zostanie otwarty **opinii** udostępnił okna dialogowego pole komentarze wstępnie wypełnione przy użyciu wybrane parametry i użytkownika przykłady. Użytkownik powinien Przejrzyj zawartość pola komentarze i dostarczenia bardziej szczegółowych informacji, która pomoże nam zrozumieć ten problem. Jeśli użytkownik nie chce udostępniać dane z firmą Microsoft, użytkownik powinien usunąć wstępnie wypełnionych przykładowe dane przed kliknięciem przycisku **Wyślij opinię** przycisku. 


### <a name="editing-an-existing-transformation"></a>Edytowanie istniejącego transformacji

Użytkownik może edytować istniejące **Podziel kolumnę według przykładu** przekształcania, wybierając **Edytuj** opcji kroku przekształcania. Kliknięcie **Edytuj** zostanie otwarty Edytor przekształcania w **trybu zaawansowanego**, i nie są wyświetlane wszystkie przykłady, które zostały podane podczas tworzenia przekształcenia.

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>Przykłady dzielenie na stały, pojedynczego znaku ogranicznika

Jest to typowe w przypadku pól z danymi być rozdzielone według jednej ogranicznika stałych, takich jak przecinek w formacie CSV. Przekształcanie podziału próbuje automatycznie wywnioskować te ograniczników. Na przykład w poniższym scenariuszu go automatycznie wnioskuje "." jako ogranicznik.

### <a name="splitting-ip-addresses"></a>Adresy IP dzielenia

Wartości w pierwszej kolumnie predictively są dzielone na czterech kolumn.

|Adres IP|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>Przykłady dzielenie na wiele ograniczników w ramach określonego kontekstów

Dane użytkownika może obejmować wiele różnych ograniczników, oddzielając różnych pól. Ponadto niektóre wystąpienia ciągu ograniczająca może być ogranicznik, ale nie dla wszystkich. Na przykład w przypadku następujących zestaw ograniczników, wymagane jest "-",","i":" Aby utworzyć żądanego wyniku. Jednak nie wszystkie wystąpienia ":" powinien być punkt podziału, ponieważ nie chcemy podzielić czasu, ale pozostawić ją w jednej kolumnie. Przekształcanie podziału wnioskuje ograniczniki w sytuacjach, w których występują w danych wejściowych, a nie wszystkie możliwe wystąpienie ogranicznika. Transformacja jest również pamiętać o standardowe typy danych, takich jak daty i godziny.   

### <a name="splitting-store-opening-timings"></a>Dzielenie magazynu otwierania chronometrażu

Wartości w następującym *chronometrażu* kolumny predictively uzyskać podzielić na dziewięć kolumny w tabeli pod nim.

|Chronometraż|
|:-----|
|Poniedziałek — piątek: 7:00:00 - 18:00:00, sobota: 9:00:00 - 5:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 9:00:00 - 18:00:00, sobota: 4:00:00 - 4:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 8:30:00 - 7:00 pm, sobota: 3:00:00 - 2:30 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 8:00:00 - 18:00:00, sobota: 2:00:00 - 3:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 4:00:00 - 7:00 pm, sobota: 9:00:00 - 4:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 8:30:00 - 4:30 pm, sobota: 9:00:00 - 5:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 5:30:00 - 6:30 pm, sobota: 5:00:00 - 4:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 8:30:00 - 8:30 pm, sobota: 6:00:00 - 5:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 8:00:00: 00 – 21, sobota: 9:00:00 - 8:00 pm, niedziela: zamknięte|
|Poniedziałek — piątek: 10:00:00 - 9:30 pm, sobota: 9:30:00 - 3:00 pm, niedziela: zamknięte|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|Poniedziałek|Piątek|7:00 am|6:00 pm|Sobota|9:00|5:00 pm|Niedziela|Zamknięte|
|Poniedziałek|Piątek|9:00|6:00 pm|Sobota|4:00 am|4:00 pm|Niedziela|Zamknięte|
|Poniedziałek|Piątek|8:30:00|7:00 pm|Sobota|3:00 am|2:30 pm|Niedziela|Zamknięte|
|Poniedziałek|Piątek|8:00 am|6:00 pm|Sobota|2:00:00|3:00 pm|Niedziela|Zamknięte|
|Poniedziałek|Piątek|4:00 am|7:00 pm|Sobota|9:00|4:00 pm|Niedziela|Zamknięte|
|Poniedziałek|Piątek|8:30:00|4:30 pm.|Sobota|9:00|5:00 pm|Niedziela|Zamknięte|
|Poniedziałek|Piątek|5:30:00|18:30:00|Sobota|5:00:00|4:00 pm|Niedziela|Zamknięte|
|Poniedziałek|Piątek|8:30:00|8:30 pm|Sobota|6:00 am|5:00 pm|Niedziela|Zamknięte|
|Poniedziałek|Piątek|8:00 am|9:00 pm|Sobota|9:00|8:00 pm|Niedziela|Zamknięte|
|Poniedziałek|Piątek|10:00 am|9:30 pm|Sobota|9:30:00|3:00 pm|Niedziela|Zamknięte|

### <a name="splitting-iis-log"></a>Podziału dziennik IIS

Oto inny przykład wiele dowolnego ograniczników. W tym przykładzie zawiera również kontekstowych ogranicznika "/", które nie muszą zostać podzielone wewnątrz adresy URL lub ścieżki plików. Jest za uciążliwe wykonać taki podział przy użyciu wielu *Utwórz kolumnę pochodną według przykładu* transformacje i podanie przykładów dla każdego pola. Przy użyciu transformacji podziału można wykonać predykcyjne dzielenie bez udzielania żadnych przykładów.

|logtext|
|:-----|
|192.128.138.20 — [16 października/2016 16:22:33-0200] "/images/picture.gif GET protokołu HTTP/1.1" 234 343 www.yahoo.com "http://www.example.com/" "Mozilla/4.0 (zgodny; MSIE 4)""-"|
|10.128.72.213 — [Oct/17/2016 12:43:12 +0300] "GET /news/stuff.html HTTP/1.1" 200 6233 www.aol.com "http://www.sample.com/" "Mozilla/5.0 (MSIE)" "-"|
|192.165.71.165 - - [12/Nov/2016 14:22:44 -0500] "GET /sample.ico HTTP/1.1" 342 7342 www.facebook.com "-" "Mozilla/5.0 (Windows; U; Windows NT 5.1; rv:1.7.3)" "-"|
|10.166.64.165 — [23 listopada/2016 01:52:45 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.167.1.193 — [2017-16-sty 22:34:56 0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.147.76.193 — [2017-28-sty 26:36:16 +0800] "GET /search.php HTTP/1.1" 400 1777 www.bing.com "-" "Mozilla/4.0 (zgodny; MSIE 6.0; Windows NT 5.1)""-"|
|192.166.64.165 — [23 marca/2017 01:55:25 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|11.167.1.193 — [2017-16-kwi 11:34:36 0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|

Pobiera podzielony na:

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|16 października/2016|16:22:33|-0200|GET|images/picture.gif|HTTP|1.1|234|343|www.yahoo.com|http://www.example.com/|Mozilla|4.0|zgodny; MSIE 4|
|10.128.72.213|KTZ/17/2016|12:43:12|+0300|GET|news/stuff.html|HTTP|1.1|200|6233|www.aol.com|http://www.sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|2016-12-lis|14:22:44|-0500|GET|sample.ico|HTTP|1.1|342|7342|www.facebook.com|-|Mozilla|5.0|Windows; U; Windows NT 5.1; rv:1.7.3|
|10.166.64.165|23 listopada/2016|01:52:45|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|192.167.1.193|16 stycznia/2017|22:34:56|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|
|192.147.76.193|2017-28-sty|26:36:16|+0800|GET|Search.php|HTTP|1.1|400|1777|www.bing.com|-|Mozilla|4.0|zgodny; MSIE 6.0; Windows NT 5.1|
|192.166.64.165|2017-23-marca|01:55:25|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|11.167.1.193|2017-16-kwi|11:34:36|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>Przykłady dzielenia bez ograniczników
W niektórych przypadkach nie ma żadnych rzeczywistych ograniczniki i pola danych może wystąpić w sposób ciągły obok siebie. W tym przypadku transformacji podziału automatycznie wykrywa wzorce w danych prawdopodobnie wywnioskowania punktów podziału. Na przykład w poniższym scenariuszu chcemy oddzielić kwotę od typ waluty i podziału automatycznie wnioskuje granic między danych liczbowych i nieliczbową jako punkt podziału.

### <a name="splitting-amount-with-currency-symbol"></a>Podział ilości symbol waluty

|Ilość|Amount_1|Amount_2|
|:-----|:-----|:-----|
|\$14|$|14|
|£9|£|9|
|\$34|$|34|
|€ 18|€ |18|
|\$42|$|42|
|\$7|$|7|
|£42|£|42|
|\$16|$|16|
|€ 16|€ |16|
|\$15|$|15|
|\$16|$|16|
|€ 64|€ |64|

W poniższym przykładzie chcemy oddzielić wartości wag od jednostki miary. Ponownie wnioskowania podziału automatycznie wykrywa granicę znaczących i preferuje go inne możliwe ograniczniki takich jak "." znaków. 

### <a name="splitting-weights-with-units"></a>Dzielenie wagi z jednostkami

|Waga|Weight_1|Weight_2|
|:-----|:-----|:-----|
|2.27KG|2.27|KG|
|1L|1|L|
|2.5KG|2.5|KG|
|2KG|2|KG|
|1.7KGA|1.7|KGA|
|3KG|3|KG|
|2KG|2|KG|
|125G|125|G|
|500G|500|G|
|1.5KGA|1.5|KGA|

## <a name="technical-notes"></a>Uwagi techniczne

Funkcja transformacji podziału opiera się na **predykcyjne syntezy Program** techniki. W tej metodzie programów przekształcania danych są rozpoznawane automatycznie w oparciu o dane wejściowe. Te programy są przekształcony w języku specyficznym dla domeny. Język DSL opiera się na ograniczniki i pola, które występują w szczególności kontekstów wyrażenia regularnego. Więcej informacji na temat tej technologii można znaleźć w [ostatnie publikacji na ten temat](https://www.microsoft.com/en-us/research/publication/automated-data-extraction-using-predictive-program-synthesis/). 
