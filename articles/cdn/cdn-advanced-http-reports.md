---
title: Analizowanie statystyk użycia za pomocą zaawansowanych raportów HTTP usługi Azure CDN | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć zaawansowane raporty HTTP w usłudze Microsoft Azure CDN. Raporty te zawierają szczegółowe informacje na temat aktywności sieci CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 0b0eec2425f8a1663eb7a09c83a6bad037d1d79c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594108"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analizowanie statystyk użycia za pomocą zaawansowanych raportów HTTP usługi Azure CDN
## <a name="overview"></a>Omówienie
W tym dokumencie opisano zaawansowane raportowanie HTTP w sieci CDN platformy Microsoft Azure. Raporty te zawierają szczegółowe informacje na temat aktywności sieci CDN.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Uzyskiwanie dostępu do zaawansowanych raportów HTTP
1. W bloku profilu sieci CDN kliknij przycisk **Zarządzaj.**
   
    ![Przycisk zarządzania łatami profilu CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania siecią CDN.
2. Umieść wskaźnik myszy na karcie **Analytics,** a następnie umieść wskaźnik myszy na **wysu wysu wysu wysu wysuwu Zaawansowane raporty HTTP.**  Kliknij na **http duża platforma**.
   
    ![Portal zarządzania siecią CDN — menu Raporty zaawansowane](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Zostaną wyświetlone opcje raportu.

## <a name="geography-reports-map-based"></a>Raporty geografii (oparte na mapach)
Istnieje pięć raportów, które korzystają z mapy, aby wskazać regiony, z których żądana jest zawartość. Raporty te to Mapa Świata, Mapa Stanów Zjednoczonych, Mapa Kanady, Mapa Europy i Mapa Azji i Pacyfiku.

Każdy raport oparty na mapie szereguje jednostki geograficzne (tj. kraje/regiony, mapa jest dostarczana, aby ułatwić wizualizację lokalizacji, z których żądana jest zawartość. Jest w stanie to zrobić poprzez kodowanie kolorami każdego regionu w zależności od ilości zapotrzebowania doświadczanego w tym regionie. Jaśniejsze zacienione regiony wskazują na mniejsze zapotrzebowanie na zawartość, a ciemniejsze regiony wskazują na wyższy poziom zapotrzebowania na zawartość.

Szczegółowe informacje o ruchu i przepustowości dla każdego regionu są dostarczane bezpośrednio pod mapą. Dzięki temu można wyświetlić całkowitą liczbę trafień, procent trafień, całkowitą ilość przesyłanych danych (w gigabajtach) i procent danych przesłanych dla każdego regionu. Wyświetl opis dla każdej z tych metryk. Na koniec po umieszczeniu wskaźnika myszy na regionie (tj. kraju/regionu, stanie lub prowincji) nazwa i procent trafień, które wystąpiły w regionie, będą wyświetlane jako etykietka narzędzia.

Poniżej znajduje się krótki opis dla każdego typu raportu geograficznego opartego na mapie.

| Nazwa raportu | Opis |
| --- | --- |
| Mapa świata |Ten raport umożliwia wyświetlanie światowego popytu na zawartość sieci CDN. Każdy kraj/region jest oznaczony kolorami na mapie świata, aby wskazać procent trafień pochodzących z tego regionu. |
| Stany Zjednoczone Mapa |Ten raport umożliwia wyświetlanie zapotrzebowania na zawartość sieci CDN w Stanach Zjednoczonych. Każdy stan jest oznaczony kolorami na tej mapie, aby wskazać procent trafień, które pochodzą z tego regionu. |
| Mapa Kanady |Ten raport umożliwia wyświetlanie zapotrzebowania na zawartość sieci CDN w Kanadzie. Każda prowincja jest kolorami na tej mapie, aby wskazać procent trafień pochodzących z tego regionu. |
| Mapa Europy |Ten raport umożliwia wyświetlanie zapotrzebowania na zawartość sieci CDN w Europie. Każdy kraj/region jest oznaczony kolorami na tej mapie, aby wskazać procent trafień pochodzących z tego regionu. |
| Mapa Regionu Azji i Pacyfiku |Ten raport umożliwia wyświetlanie zapotrzebowania na zawartość sieci CDN w Azji. Każdy kraj/region jest oznaczony kolorami na tej mapie, aby wskazać procent trafień pochodzących z tego regionu. |

## <a name="geography-reports-bar-charts"></a>Raporty geografii (wykresy słupkowe)
Istnieją dwa dodatkowe raporty, które dostarczają informacji statystycznych zgodnie z geografią, które są top miasta i najlepsze kraje. Raporty te klasyfikują miasta i kraje/regiony, odpowiednio, w zależności od liczby trafień pochodzących z tych krajów/regionów. Po wygenerowaniu tego typu raportu wykres słupkowy wskaże 10 największych miast lub krajów/regionów, które zażądały zawartości za granicą na określonej platformie. Ten wykres słupkowy umożliwia szybką ocenę regionów, które generują największą liczbę żądań dotyczących zawartości.

Po lewej stronie wykresu (oś y) wskazuje, ile trafień miało miejsce w określonym regionie. Bezpośrednio pod wykresem (oś x) znajdziesz etykietę dla każdego z 10 najlepszych regionów.

### <a name="using-the-bar-charts"></a>Korzystanie z wykresów słupkowych
* Jeśli najedziesz kursorem na pasek, nazwa i całkowita liczba trafień, które miały miejsce w regionie, będą wyświetlane jako etykietka narzędzia.
* Etykietka narzędzia raportu Najważniejsze miasta identyfikuje miasto według jego nazwy, stanu/prowincji i skrótu kraju/regionu.
* Jeśli nie można ustalić miasta lub regionu (tj. stanu/prowincji), z którego nie można było ustalić żądania, oznacza to, że są one nieznane. Jeśli kraj/region jest nieznany, zostaną wyświetlone dwa znaki zapytania (tj. ??).
* Raport może zawierać dane dotyczące "Europa" lub "Region Azji i Pacyfiku". Elementy te nie są przeznaczone do dostarczania informacji statystycznych na temat wszystkich adresów IP w tych regionach. Mają one raczej zastosowanie tylko do żądań pochodzących z adresów IP, które są rozłożone w Europie lub Azji/Pacyfiku, a nie do określonego miasta lub kraju/regionu.

Dane użyte do wygenerowania wykresu słupkowego można wyświetlić pod nim. Znajdziesz tam całkowitą liczbę trafień, procent trafień, ilość przesłanych danych (w gigabajtach) oraz procent danych przesłanych dla 250 najlepszych regionów. Wyświetl opis dla każdej z tych metryk.

Krótki opis jest dostępny dla obu typów raportów poniżej.

| Nazwa raportu | Opis |
| --- | --- |
| Najpopularniejsze miasta |Ten raport plasuje miasta według liczby trafień, które pochodzą z tego regionu. |
| Najlepsze kraje |Ten raport klasyfikuje kraje/regiony według liczby trafień pochodzących z tego kraju/regionu. |

## <a name="daily-summary"></a>Podsumowanie dzienne
Raport Podsumowanie dzienne umożliwia codzienne wyświetlanie całkowitej liczby trafień i danych przesyłanych za daną platformę. Informacje te mogą służyć do szybkiego rozpoznawania wzorców aktywności sieci CDN. Na przykład ten raport może pomóc wykryć, które dni wystąpiły wyższe lub niższe niż oczekiwano ruchu.

Po wygenerowaniu tego typu raportu wykres słupkowy będzie przedstawiał wizualne wskazanie, jaka jest ilość zapotrzebowania specyficznego dla platformy, jakiego doświadcza się codziennie w okresie objętym raportem. Zrobi to, wyświetlając pasek dla każdego dnia w raporcie. Na przykład wybranie okresu o nazwie "Ostatni tydzień" spowoduje wygenerowanie wykresu słupkowego z siedmioma słupkami. Każdy pasek wskaże całkowitą liczbę trafień, jakich doświadczyli tego dnia.

Po lewej stronie wykresu (oś y) wskazuje, ile trafień miało miejsce w określonym dniu. Bezpośrednio pod wykresem (oś x) znajdziesz etykietę wskazującą datę (Format: YYYY-MM-DD) dla każdego dnia zawartego w raporcie.

> [!TIP]
> Jeśli najedziesz kursorem na pasek, całkowita liczba trafień, które miały miejsce w tym dniu, zostanie wyświetlona jako etykietka narzędzia.
> 
> 

Dane użyte do wygenerowania wykresu słupkowego można wyświetlić pod nim. Znajdziesz tam całkowitą liczbę trafień i ilość przesyłanych danych (w gigabajtach) za każdy dzień objęty raportem.

## <a name="by-hour"></a>Według godziny
Raport Według godziny umożliwia wyświetlanie całkowitej liczby trafień i danych przesyłanych za pomocą określonej platformy co godzinę. Informacje te mogą służyć do szybkiego rozpoznawania wzorców aktywności sieci CDN. Na przykład ten raport może pomóc wykryć okresy w ciągu dnia, które występują wyższy lub niższy niż oczekiwany ruch.

Po wygenerowaniu tego typu raportu wykres słupkowy będzie przedstawiał wizualne wskazanie co do ilości zapotrzebowania specyficznego dla platformy doświadczanego co godzinę w okresie objętym raportem. Zrobi to, wyświetlając pasek dla każdej godziny objętej raportem. Na przykład wybranie 24-godzinnego okresu spowoduje wygenerowanie wykresu słupkowego z dwudziestoma czterema słupkami. Każdy pasek wskazuje całkowitą liczbę trafień doświadczonych w tej godzinie.

Po lewej stronie wykresu (oś y) wskazuje, ile trafień miało miejsce w określonej godzinie. Bezpośrednio pod wykresem (oś x) znajdziesz etykietę wskazującą datę/godzinę (Format: YYYY-MM-DD hh:mm) dla każdej godziny zawartej w raporcie. Czas jest zgłaszany przy użyciu formatu 24-godzinnego i jest określony przy użyciu strefy czasowej UTC/GMT.

> [!TIP]
> Jeśli najedziesz kursorem na pasek, całkowita liczba trafień, które wystąpiły w ciągu tej godziny, zostanie wyświetlona jako etykietka narzędzia.
> 
> 

Dane użyte do wygenerowania wykresu słupkowego można wyświetlić pod nim. Znajdziesz tam całkowitą liczbę trafień i ilość przesyłanych danych (w gigabajtach) za każdą godzinę objętą raportem.

## <a name="by-file"></a>Według pliku
Raport Według pliku umożliwia wyświetlanie ilości popytu i ruchu poniesionego za pomocą określonej platformy dla najbardziej żądanych zasobów. Po wygenerowaniu tego typu raportu wykres słupkowy zostanie wygenerowany na 10 najbardziej żądanych zasobów w określonym okresie.

> [!NOTE]
> Na potrzeby tego raportu adresy URL CNAME krawędzi są konwertowane na ich równoważne adresy URL sieci CDN. Umożliwia to dokładną liczbę trafień skojarzonych z zasobem, niezależnie od adresu URL sieci CDN lub cname krawędzi używanego do żądania.
> 
> 

Po lewej stronie wykresu (oś y) wskazuje liczbę żądań dla każdego zasobu w określonym okresie czasu. Bezpośrednio pod wykresem (oś x) znajduje się etykieta wskazująca nazwę pliku dla każdego z 10 pierwszych żądanych zasobów.

Dane użyte do wygenerowania wykresu słupkowego można wyświetlić pod nim. Znajdziesz tam następujące informacje dla każdego z 250 pierwszych żądanych zasobów: ścieżka względna, całkowita liczba trafień, procent trafień, ilość przesyłanych danych (w gigabajtach) i procent przesyłanych danych.

## <a name="by-file-detail"></a>Według szczegółów pliku
Raport Według szczegółów pliku umożliwia wyświetlanie ilości popytu i ruchu ponoszonego za pomocą określonej platformy dla określonego zasobu. Na samym szczycie tego raportu znajduje się opcja Szczegóły pliku dla. Ta opcja zawiera listę najczęściej żądanych zasobów na wybranej platformie. Aby wygenerować raport Według szczegółów pliku, należy wybrać żądany zasób z opcji Szczegóły pliku dla. Po tym wykres słupkowy wskaże ilość dziennego zapotrzebowania, które wygenerował w określonym okresie czasu.

Po lewej stronie wykresu (oś y) wskazuje całkowitą liczbę żądań, które zasób wystąpił w danym dniu. Bezpośrednio pod wykresem (oś x) znajduje się etykieta wskazująca datę (Format: YYYY-MM-DD), dla której zgłoszono zapotrzebowanie na dysk CDN dla zasobu.

Dane użyte do wygenerowania wykresu słupkowego można wyświetlić pod nim. Znajdziesz tam całkowitą liczbę trafień i ilość przesyłanych danych (w gigabajtach) za każdy dzień objęty raportem.

## <a name="by-file-type"></a>Według typu pliku
Raport Według typu pliku umożliwia wyświetlanie ilości zapotrzebowania i ruchu ponoszonego przez typ pliku. Po wygenerowaniu tego typu raportu wykres pierścieniowy wskaże procent trafień generowanych przez 10 najlepszych typów plików.

> [!TIP]
> Po umieszczeniu wskaźnika myszy na wycinku na wykresie pierścieniowym typ nośnika internetowego tego typu pliku zostanie wyświetlony jako etykietka narzędzia.
> 
> 

Dane użyte do wygenerowania wykresu pierścieniowego można wyświetlić pod nim. Znajdziesz tam rozszerzenie nazwy pliku / typ multimediów internetowych, całkowitą liczbę trafień, procent trafień, ilość przesyłanych danych (w gigabajtach) i procent danych przesyłanych dla każdego z 250 najlepszych typów plików.

## <a name="by-directory"></a>Według katalogu
Raport Według katalogu umożliwia wyświetlanie ilości zapotrzebowania i ruchu ponoszonego za pomocą określonej platformy dla zawartości z określonego katalogu. Po wygenerowaniu tego typu raportu wykres słupkowy wskaże całkowitą liczbę trafień generowanych przez zawartość w 10 najlepszych katalogach.

### <a name="using-the-bar-chart"></a>Korzystanie z wykresu słupkowego
* Umieść wskaźnik myszy na pasku, aby wyświetlić ścieżkę względną do odpowiedniego katalogu.
* Zawartość przechowywana w podfolderze katalogu nie jest liczona podczas obliczania zapotrzebowania według katalogu. Obliczenie to opiera się wyłącznie na liczbie żądań wygenerowanych dla zawartości przechowywanej w katalogu rzeczywistym.
* Na potrzeby tego raportu adresy URL CNAME krawędzi są konwertowane na ich równoważne adresy URL sieci CDN. Umożliwia to dokładną liczbę statystyk skojarzonych z zasobem, niezależnie od adresu URL sieci CDN lub cname krawędzi używanego do żądania.

Po lewej stronie wykresu (oś y) wskazuje całkowitą liczbę żądań zawartości przechowywanej w 10 najlepszych katalogach. Każdy pasek na wykresie reprezentuje katalog. Schemat kodowania kolorami służy do dopasowywania paska do katalogu wymienionego w sekcji Top 250 Pełnych katalogów.

Dane użyte do wygenerowania wykresu słupkowego można wyświetlić pod nim. Znajdziesz tam następujące informacje dla każdego z najlepszych katalogów 250: ścieżka względna, całkowita liczba trafień, procent trafień, ilość przesyłanych danych (w gigabajtach) i procent przesyłanych danych.

## <a name="by-browser"></a>Przez przeglądarkę
Raport Według przeglądarki umożliwia wyświetlanie, które przeglądarki były używane do żądania zawartości. Po wygenerowaniu tego typu raportu wykres kołowy wskaże procent żądań obsługiwanych przez 10 najlepszych przeglądarek.

### <a name="using-the-pie-chart"></a>Korzystanie z wykresu kołowego
* Umieść wskaźnik myszy na wycinku na wykresie kołowym, aby wyświetlić nazwę i wersję przeglądarki.
* Na potrzeby niniejszego raportu każda unikalna kombinacja przeglądarki/wersji jest uważana za inną przeglądarkę.
* Plasterek o nazwie "Inne" wskazuje procent żądań obsługiwanych przez wszystkie inne przeglądarki i wersje.

Dane użyte do wygenerowania wykresu kołowego można wyświetlić pod nim. Znajdziesz tam typ przeglądarki / numer wersji, całkowitą liczbę trafień i procent trafień dla każdej z najlepszych przeglądarek 250.

## <a name="by-referrer"></a>Przez Referrer
Raport Przez polecenie umożliwia wyświetlanie najważniejszych odsyłaczy do zawartości na wybranej platformie. Odsyłacz wskazuje nazwa hosta, z którego wygenerowano żądanie. Po wygenerowaniu tego typu raportu wykres słupkowy wskaże ilość zapotrzebowania (tj. trafień) wygenerowaną przez 10 najlepszych osób odsyłających.

Po lewej stronie wykresu (oś y) wskazuje całkowitą liczbę żądań, które wystąpił zasób dla każdego strony odsyłacza. Każdy pasek na wykresie reprezentuje polecenie. Schemat kodowania kolorami służy do dopasowywania paska do strony odsyłacza wymienionej w sekcji Odsyłacz 250.

Dane użyte do wygenerowania wykresu słupkowego można wyświetlić pod nim. Znajdziesz tam adres URL, łączną liczbę trafień i procent trafień wygenerowanych z każdego z 250 najlepszych osób odsyłanych.

## <a name="by-download"></a>Według pobrania
Raport By Download umożliwia analizowanie wzorców pobierania dla najbardziej żądanej zawartości. Górna część raportu zawiera wykres słupkowy, który porównuje próby pobrania z ukończonymi pobraniami dla 10 pierwszych żądanych zasobów. Każdy pasek jest oznaczony kolorami w zależności od tego, czy jest to próba pobrania (niebieski) czy ukończone pobieranie (zielony).

> [!NOTE]
> Na potrzeby tego raportu adresy URL CNAME krawędzi są konwertowane na ich równoważne adresy URL sieci CDN. Umożliwia to dokładną liczbę statystyk skojarzonych z zasobem, niezależnie od adresu URL sieci CDN lub cname krawędzi używanego do żądania.
> 
> 

Po lewej stronie wykresu (oś y) wskazuje nazwę pliku dla każdego z 10 pierwszych żądanych zasobów. Bezpośrednio pod wykresem (oś x) znajdziesz etykiety wskazujące całkowitą liczbę prób/zakończonych pobrań.

Bezpośrednio pod wykresem słupkowym zostaną wyświetlone następujące informacje dotyczące 250 najwyżej żądanych zasobów: ścieżka względna (w tym nazwa pliku), liczba pobrań do zakończenia, liczba żądanych aktywów oraz procent żądania, które doprowadziły do całkowitego pobrania.

> [!TIP]
> Nasza sieć CDN nie jest informowana przez klienta HTTP (tj. przeglądarki), gdy zasób został całkowicie pobrany. W rezultacie musimy obliczyć, czy zasób został całkowicie pobrany zgodnie z kodami stanu i żądaniami zakresu bajtów. Pierwszą rzeczą, której szukamy podczas wykonywania tego obliczenia, jest to, czy żądanie powoduje kod stanu 200 OK. Jeśli tak, to przyjrzymy się żądania zakresu bajtów, aby upewnić się, że obejmują one cały zasób. Na koniec porównujemy ilość przesłanych danych z wielkością żądanego zasobu. Jeśli przesyłane dane są równe lub większe niż rozmiar pliku, a żądania zakresu bajtów są odpowiednie dla tego zasobu, trafienie zostanie policzone jako pełne pobranie.
> 
> Ze względu na interpretacyjny charakter niniejszego sprawozdania należy pamiętać o następujących kwestiach, które mogą zmienić spójność i dokładność niniejszego sprawozdania.
> 
> * Wzorców ruchu nie można dokładnie przewidzieć, gdy agenci użytkownika zachowują się inaczej. Może to spowodować, że ukończone wyniki pobierania są większe niż 100%.
> * Zasoby korzystające z pobierania progresywnego HTTP mogą nie być dokładnie reprezentowane przez ten raport. Wynika to z użytkowników poszukujących różnych pozycji w filmie.
> 
> 

## <a name="by-404-errors"></a>Przez 404 błędy
Raport Przez 404 błędy umożliwia identyfikację typu zawartości, która generuje najwięcej 404 nie znaleziono kodów stanu. Górna część raportu zawiera wykres słupkowy dla 10 najlepszych zasobów, dla których zwrócono kod stanu 404 Nie znaleziono. Ten wykres słupkowy porównuje całkowitą liczbę żądań z żądaniami, które spowodowały 404 Nie znaleziono kodu stanu dla tych zasobów. Każdy pasek jest oznaczony kolorami. Żółty pasek jest używany do wskazania, że żądanie spowodowało 404 Nie znaleziono kodu stanu. Czerwony pasek służy do wskazywania całkowitej liczby żądań dla zasobu.

> [!NOTE]
> Na potrzeby niniejszego sprawozdania należy zwrócić uwagę na następujące kwestie:
> 
> * Trafienie reprezentuje każde żądanie dla zasobu, niezależnie od kodu stanu.
> * Adresy URL CNAME krawędzi są konwertowane na ich równoważne adresy URL sieci CDN. Umożliwia to dokładną liczbę statystyk skojarzonych z zasobem, niezależnie od adresu URL sieci CDN lub cname krawędzi używanego do żądania.
> 
> 

Po lewej stronie wykresu (oś y) wskazuje nazwę pliku dla każdego z 10 pierwszych żądanych zasobów, które spowodowało kod stanu 404 Nie znaleziono. Bezpośrednio pod wykresem (oś x) znajdują się etykiety wskazujące całkowitą liczbę żądań i liczbę żądań, które spowodowały kod stanu 404 Nie znaleziono.

Bezpośrednio pod wykresem słupkowym zostaną wyświetlone następujące informacje dotyczące 250 najwyżej żądanych zasobów: ścieżka względna (w tym nazwa pliku), liczba żądań, które spowodowały wyświetlenie kodu stanu 404 Nie znaleziono, całkowita liczba żądanych zasobów oraz procent żądań, które spowodowały 404 Nie znaleziono kodu stanu.

## <a name="see-also"></a>Zobacz też
* [Omówienie usługi Azure CDN](cdn-overview.md)
* [Statystyki w czasie rzeczywistym w sieci CDN platformy Microsoft Azure](cdn-real-time-stats.md)
* [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-rules-engine.md)
* [Analizowanie wydajności krawędzi](cdn-edge-performance.md)

