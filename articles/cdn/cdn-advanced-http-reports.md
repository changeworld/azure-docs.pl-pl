---
title: Analizowanie statystyk użycia z usługą Azure CDN zaawansowane raporty HTTP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć zaawansowane raporty HTTP w usłudze Microsoft Azure CDN. Te raporty zawierają szczegółowe informacje w działaniu usługi CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 2dfbc046674b2da692f30c945aee3ea25ae524eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843616"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analizowanie statystyk użycia z usługą Azure CDN zaawansowane raporty HTTP
## <a name="overview"></a>Omówienie
W tym dokumencie opisano zaawansowane HTTP raportowania w programie Microsoft Azure CDN. Te raporty zawierają szczegółowe informacje w działaniu usługi CDN.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Uzyskiwanie dostępu do zaawansowane raporty HTTP
1. Blok profilu CDN, kliknij **Zarządzaj** przycisku.
   
    ![Przycisk Zarządzaj blok profilu CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
2. Umieść kursor nad **Analytics** , a następnie umieść kursor nad **zaawansowane raporty HTTP** wysuwane okno.  Polecenie **HTTP platformy dużych**.
   
    ![Portal zarządzania w sieci CDN — menu Zaawansowane raporty](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Opcje raportu są wyświetlane.

## <a name="geography-reports-map-based"></a>Lokalizacja geograficzna raporty (w oparciu o mapy)
Służą pięć raporty, które korzystają z mapy, aby wskazać regionów, z których żąda zawartości. Te raporty są World mapy, mapy Stanów Zjednoczonych, Kanady mapy, mapy Europy i Azja i Pacyfik mapy.

Każdy raport na podstawie mapy rangi geograficzne jednostek (tj. krajach, Stany i prowincje) zgodnie z procent trafień, które pochodzą z tego regionu. Ponadto mapy podano ułatwiają wizualizowanie lokalizacje, z których zawartość jest wymagany. Jest w stanie to zrobić przez kolorowania poszczególnych regionach, zależnie od ilości żądanie w tym regionie. Jaśniejsze przyciemnione regionów wskazują niższe żądanie dla zawartości, gdy ciemniejszego regionów wskazać wyższego poziomu żądanie dla zawartości.

Szczegółowe informacje ruchu i przepustowości dla każdego regionu, znajduje się bezpośrednio pod mapy. Dzięki temu można wyświetlić całkowitej liczby trafień, procent trafień, łączną ilość danych przesyłanych (w gigabajtach) i wartość procentowa danych przesyłanych w poszczególnych regionach. Wyświetl opis każdego z tych metryk. Na koniec, po umieszczeniu na region (tj., kraj, Województwo), nazwę i procent trafień, które wystąpiły w regionie zostaną wyświetlone jako etykietka narzędzia.

Krótki opis podano poniżej dla każdego typu raportu na podstawie mapy geograficzne.

| Nazwa raportu | Opis |
| --- | --- |
| Mapa świata |Ten raport umożliwia wyświetlenie na całym świecie żądanie dla zawartości usługi CDN. Każdego kraju jest oznaczone kolorami na mapie world, aby wskazać procent trafień, które pochodzą z tego regionu. |
| Mapy Stanów Zjednoczonych |Ten raport umożliwia wyświetlenie żądanie dla zawartości w sieci CDN w Stanach Zjednoczonych. Każdy stan jest oznaczone kolorami na mapie tego wskazująca procent trafień, które pochodzą z tego regionu. |
| Kanada mapy |Ten raport umożliwia wyświetlenie żądanie dla zawartości CDN Kanady. Każdy region jest oznaczone kolorami na mapie tego wskazująca procent trafień, które pochodzą z tego regionu. |
| Europa mapy |Ten raport umożliwia wyświetlenie żądanie dla zawartości w sieci CDN w Europie. Każdego kraju jest oznaczone kolorami na mapie tego wskazująca procent trafień, które pochodzą z tego regionu. |
| Azja pacyficzny mapy |Ten raport umożliwia wyświetlenie żądanie dla zawartości w sieci CDN w Azji. Każdego kraju jest oznaczone kolorami na mapie tego wskazująca procent trafień, które pochodzą z tego regionu. |

## <a name="geography-reports-bar-charts"></a>Lokalizacja geograficzna raportów (wykresy słupkowe)
Istnieją dwa dodatkowe raporty zapewniające informacje statystyczne według lokalizacji geograficznej, miasta i krajów Top. Te raporty rank miejscowości i krajów, odpowiednio, zgodnie z liczbą trafień pochodzących z tych obszarów. Podczas generowania raportu z tego typu, wykres słupkowy poinformuje o najwyższym miast 10 lub krajach, którzy zażądali zawartości na danej platformie. Ten wykres słupkowy pozwala szybko ocenić regionów generujących największa liczba żądań dla zawartości.

Po lewej stronie wykresu (oś y) wskazuje, ile razy wystąpiły w wybranym regionie. Bezpośrednio pod wykresem (oś x) można znaleźć etykiety dla każdego z najwyższym regionów 10.

### <a name="using-the-bar-charts"></a>Przy użyciu wykresy słupkowe
* Jeśli wskaźnik nad paskiem, nazwę i łączna liczba trafień, które wystąpiły w regionie będzie wyświetlany jako etykietka narzędzia.
* Etykietka narzędzia dla raportu miasta identyfikuje Miasto według jego nazwy, Województwo i skrót kraju.
* Jeśli nie można ustalić miasta lub regionu (np. pola Województwo), z której pochodzi żądanie, następnie go będzie wskazuje, że są nieznane. Jeśli kraju jest nieznany, a następnie dwa znaki zapytania (tj.??), zostanie wyświetlony.
* Raport może zawierać metryki "Europie" lub "Region Azji i Pacyfiku." Te elementy nie są przeznaczone do udostępniają informacje statystyczne dotyczące wszystkich adresów IP w regionach. Zamiast dotyczą wyłącznie żądań, które pochodzą z adresów IP, które są rozproszone na Europie i Azji i Pacyfiku, zamiast do określonych miasta lub kraju.

Można wyświetlić dane, które zostało użyte do wygenerowania na wykresie słupkowym poniżej. Można znaleźć całkowitej liczby trafień, procent trafień, ilość danych przesyłanych (w gigabajtach), a części danych przetransferowanych w regionach pierwszych 250. Wyświetl opis każdego z tych metryk.

Krótki opis jest dostępna dla obu typów raportów poniżej.

| Nazwa raportu | Opis |
| --- | --- |
| Miasta |Ten raport szereguje miast zgodnie z liczbą trafień, które pochodzą z tego regionu. |
| Górny krajach |Ten raport szereguje krajów zgodnie z liczbą trafień, które pochodzą z tego regionu. |

## <a name="daily-summary"></a>Dzienne podsumowanie
Raport dzienne podsumowanie umożliwia wyświetlenie całkowita liczba trafień i danych przesyłanych w konkretnej platformy codziennie. Te informacje mogą służyć do szybkiego wykrycia wzorce działania w sieci CDN w warstwie. Na przykład ten raport ułatwia wykrywanie dni doświadczonym wyższe lub niższe od oczekiwanego natężenia ruchu.

Podczas generowania raportu z tego typu, wykres słupkowy zapewni wizualnego wskazania ilość doświadczonym żądanie specyficzne dla platformy codziennie w okresie objętego raportem. Zostanie ona to wyświetlając pasek dla poszczególnych dni w raporcie. Na przykład, wybierając w okresie o nazwie "Ostatniego tygodnia" wygeneruje wykres słupkowy siedmiu pasków. Każdy pasek będzie wskazywać łączna liczba trafień doświadczenie w tym dniu.

Po lewej stronie wykresu (oś y) wskazuje, ile razy wystąpił na określony dzień. Bezpośrednio pod wykresem (oś x), można znaleźć etykiety, który wskazuje datę (Format: RRRR-MM-DD) dla każdego dnia uwzględnione w raporcie.

> [!TIP]
> Jeśli wskaźnik nad paskiem, łączna liczba trafień, które wystąpiły w tym dniu będą wyświetlane jako etykietka narzędzia.
> 
> 

Można wyświetlić dane, które zostało użyte do wygenerowania na wykresie słupkowym poniżej. Można znaleźć łączna liczba trafień i ilość danych przesyłanych (w gigabajtach) dla każdego dnia objętego raportem.

## <a name="by-hour"></a>Według godzin
Godzina przez raport umożliwia wyświetlenie całkowita liczba trafień i danych przesyłanych w konkretnej platformy co godzinę. Te informacje mogą służyć do szybkiego wykrycia wzorce działania w sieci CDN w warstwie. Na przykład ten raport ułatwia wykrywanie okresy w dniu wystąpić wyższe lub niższe od oczekiwanego natężenia ruchu.

Podczas generowania raportu z tego typu, wykres słupkowy zapewni wizualnego wskazania ilość żądanie specyficzne dla platformy wystąpił na godzinę w przedziale czasu objętego raportem. Zostanie ona to wyświetlając pasek dla każdej godziny objętego raportem. Na przykład wybranie 24-godzinnym przedziale czasu wygeneruje wykres słupkowy z paskami 24. Każdy pasek będzie wskazywać całkowita liczba trafień w czasie tej godziny.

Po lewej stronie wykresu (oś y) wskazuje, ile razy wystąpił na określoną godzinę. Bezpośrednio pod wykresem (oś x), można znaleźć etykietę wskazującą Data/Godzina (Format: RRRR-MM-DD gg: mm) dla każdej godziny uwzględnione w raporcie. Czas jest zgłaszany w formacie 24-godzinnym i zostanie określona, przy użyciu strefy czasowej UTC/GMT.

> [!TIP]
> Jeśli wskaźnik nad paskiem, łączna liczba trafień, które wystąpiły podczas danej godziny będą wyświetlane jako etykietka narzędzia.
> 
> 

Można wyświetlić dane, które zostało użyte do wygenerowania na wykresie słupkowym poniżej. Można znaleźć łączna liczba trafień i ilość danych przesyłanych (w gigabajtach) dla każdej godziny objętego raportem.

## <a name="by-file"></a>W pliku
Raport po pliku umożliwia wyświetlenie ilość żądanie i ruchu poniesionych w konkretnej platformy najbardziej żądanych zasobów. Podczas generowania raportu z tego typu, wykres słupkowy zostaną wygenerowane na najwyższym 10 najczęściej żądanych zasobów w określonym przedziale czasu.

> [!NOTE]
> Na potrzeby tego raportu adresy URL CNAME krawędzi są konwertowane na ich równoważne adresy URL usługi CDN. Dzięki temu dokładne zgadzają całkowitej liczby trafień skojarzone z zasobów, niezależnie od sieci CDN i krawędzi CNAME adres URL używany do żądania.
> 
> 

Lewa strona graph (oś y) wskazuje liczbę żądań dla każdego zasobu w określonym przedziale czasu. Bezpośrednio pod wykresem (oś x), można znaleźć etykiety, który wskazuje nazwę pliku dla każdego z najwyższym 10 żądanych zasobów.

Można wyświetlić dane, które zostało użyte do wygenerowania na wykresie słupkowym poniżej. Można znaleźć następujące informacje dla każdego z najwyższym 250 żądanych zasobów: ścieżka względna, łączna liczba trafień, procent trafień, ilość danych przesyłanych (w gigabajtach), a części danych zostaną przeniesione.

## <a name="by-file-detail"></a>Według szczegółów pliku
Raport przez szczegółów pliku umożliwia wyświetlanie ilości żądanie i ruchu poniesionych w konkretnej platformy dla określonego zasobu. Na górze tego raportu jest szczegółów pliku dla opcji. Ta opcja zawiera listę zasobów najbardziej pożądanych na wybranej platformie. Aby wygenerować raport przez szczegółów pliku, konieczne będzie wybierz żądany zawartości z pliku szczegółów dla opcji. Po upływie którego wykres słupkowy wskazuje ilość dzienny popyt generowany w określonym przedziale czasu.

Lewa strona graph (oś y) wskazuje całkowita liczba żądań, że zasób wystąpił w określonym dniu. Bezpośrednio pod wykresem (oś x), można znaleźć etykiety, który wskazuje datę (Format: RRRR-MM-DD) dla których CDN zostało zgłoszone żądanie dla elementu zawartości.

Można wyświetlić dane, które zostało użyte do wygenerowania na wykresie słupkowym poniżej. Można znaleźć łączna liczba trafień i ilość danych przesyłanych (w gigabajtach) dla każdego dnia objętego raportem.

## <a name="by-file-type"></a>Według typu pliku
Raport według typu plików umożliwia wyświetlanie ilości żądanie i ruchu poniesionych przez typ pliku. Podczas generowania raportu z tego typu, wykres pierścieniowy przedstawiający wskazuje procent trafień generowane przez górny typów plików 10.

> [!TIP]
> Jeśli w ustawieniu kursora wycinek wykresu pierścień Internet typ nośnika czy typ pliku będzie wyświetlany jako etykietka narzędzia.
> 
> 

Można wyświetlić dane, które zostało użyte do wygenerowania wykres pierścieniowy przedstawiający poniżej. Można znaleźć typu nośnika rozszerzenia/Internet nazwy pliku, łączna liczba trafień, procent trafień, ilość danych przetransferowanych (w gigabajtach), a części danych transferu dla każdego typu pliku pierwszych 250.

## <a name="by-directory"></a>Za pomocą katalogu
Raport w katalogu umożliwia wyświetlanie ilości żądanie i ruchu poniesionych w danej platformy na zawartość z określonego katalogu. Podczas generowania raportu z tego typu, wykres słupkowy wskaże całkowita liczba trafień generowane przez zawartość w górnym katalogów 10.

### <a name="using-the-bar-chart"></a>Przy użyciu wykres słupkowy
* Umieść kursor nad paskiem, aby wyświetlić ścieżkę względną do odpowiedniego katalogu.
* Zawartości przechowywanej w podfolderze katalogu nie będą uwzględniane podczas obliczania żądanie za pomocą katalogu. Obliczona w ten sposób zależy od wyłącznie liczba żądań wygenerowany dla zawartości przechowywanej w katalogu rzeczywistych.
* Na potrzeby tego raportu adresy URL CNAME krawędzi są konwertowane na ich równoważne adresy URL usługi CDN. Dzięki temu dokładne zgadzają się dla wszystkich statystyk skojarzone z zasobów, niezależnie od sieci CDN i krawędzi CNAME adres URL używany do żądania.

Całkowita liczba żądań dotyczących zawartości przechowywanych w katalogach 10 pierwszych wskazuje, po lewej stronie wykresu (oś y). Każdy słupek na wykresie reprezentuje katalog. Schemat kolorów umożliwia dopasowanie pasek do katalogu wymienione w sekcji górnej 250 pełne katalogów.

Można wyświetlić dane, które zostało użyte do wygenerowania na wykresie słupkowym poniżej. Można znaleźć następujące informacje dla każdego z góry 250 katalogi: ścieżki względnej, łączna liczba trafień, procent trafień, ilość danych przesyłanych (w gigabajtach), a części danych zostaną przeniesione.

## <a name="by-browser"></a>W przeglądarce
Raport w przeglądarce służy do wyświetlania przeglądarki, które były używane do żądania zawartości. Podczas generowania raportu z tego typu, wykres kołowy wskazuje procent żądań obsłużonych przez górny 10 przeglądarki.

### <a name="using-the-pie-chart"></a>Przy użyciu wykresu kołowego
* Umieść kursor nad wycinek wykresu kołowego, aby wyświetlić nazwy i wersji przeglądarki.
* Na potrzeby tego raportu każda kombinacja unikatowe przeglądarki/wersji jest uznawany za innej przeglądarki.
* Wycinek o nazwie "Inne" wskazuje procent żądań obsłużonych przez wszystkie przeglądarki i wersje.

Poniżej można wyświetlić dane, które zostało użyte do generowania wykresu kołowego. Można znaleźć numer typu i wersji przeglądarki, łączna liczba trafień i procent trafień dla każdego z góry 250 przeglądarki.

## <a name="by-referrer"></a>Według odwołania
Raport według odwołania umożliwia wyświetlenie najczęstsze do zawartości na wybranej platformie. Odwołania wskazuje nazwę hosta, na którym żądanie zostało wygenerowane. Podczas generowania raportu z tego typu, wykres słupkowy wskazuje ilość generowanych przez 10 najczęstsze żądanie (tj. liczba trafień).

Lewa strona graph (oś y) wskazuje całkowita liczba żądań, że zasób wystąpił dla każdego odwołania. Każdy słupek na wykresie reprezentuje odwołania. Schemat kolorów umożliwia dopasowanie pasek do odwołania, wymienione w sekcji górnej 250 odwołania.

Można wyświetlić dane, które zostało użyte do wygenerowania na wykresie słupkowym poniżej. Można znaleźć adres URL, łączna liczba trafień i procent trafień wygenerowane z każdej najczęstsze 250.

## <a name="by-download"></a>Do pobrania
Pobierany raport umożliwia analizę wzorce pobierania najbardziej żądanej zawartości. Na początku raport zawiera wykres słupkowy, że porównanie podjęto pliki do pobrania z pobrania zakończone top 10 żądanych zasobów. Każdy pasek jest oznaczone kolorami według tego, czy jest próba pobrania (niebieski) lub Pobieranie ukończone (zielony).

> [!NOTE]
> Na potrzeby tego raportu adresy URL CNAME krawędzi są konwertowane na ich równoważne adresy URL usługi CDN. Dzięki temu dokładne zgadzają się dla wszystkich statystyk skojarzone z zasobów, niezależnie od sieci CDN i krawędzi CNAME adres URL używany do żądania.
> 
> 

Lewa strona graph (oś y) wskazuje nazwę pliku dla każdego z najwyższym 10 żądanych zasobów. Bezpośrednio pod wykresem (oś x) można znaleźć etykiety, które wskazuje całkowitą liczbę pobrań podjęto ukończone.

Bezpośrednio poniżej na wykresie słupkowym następujące informacje zostaną wyświetlone top 250 żądanych zasobów: ścieżka względna (łącznie z nazwą pliku), ile razy został pobrany do ukończenia liczbę jej żądanie zostało przesłane i procent żądania, które spowodowało ukończenia pobierania.

> [!TIP]
> Nasze CDN nie otrzymuje informacji przez klienta protokołu HTTP (tj. przeglądarki) gdy zasób został całkowicie pobrany. W związku z tym mamy obliczanie, czy zasób całkowitym pobraniu zgodnie z kodów stanu i zakres bajtów żądania. Najpierw szukamy podczas wprowadzania tego obliczenia jest Określa, czy żądanie wyniki w kodzie 200 OK stanu. Jeśli tak, możemy Przyjrzyj się żądania zakresu bajtów, aby upewnić się, że będą one obejmować cały zasobów. Na koniec mamy porównać ilość danych przesyłanych do rozmiaru żądanych zasobów. Jeśli przesyłane dane jest równa lub większa niż rozmiar pliku i żądania zakresu bajtów są odpowiednie dla tego zasobu, naciśnij klawisz będą przeliczane pobrać pełną.
> 
> Ze względu na specyfikę interpretive tego raportu należy mieć na uwadze następujące kwestie, które nie może zmienić spójności dokładność tego raportu.
> 
> * Wzorce ruchu nie można dokładnie przewidzieć, gdy agenci użytkownika zachowywać się inaczej. Może to powodować wyniki pobierania zakończonych, które są większe niż 100%.
> * Zasoby, które korzystają z pobierania progresywnego HTTP nie może być dokładnie reprezentowany przez ten raport. Jest to spowodowane użytkownikom znalezienia w inne miejsca wideo.
> 
> 

## <a name="by-404-errors"></a>Z powodu błędów 404
Raport według błędów 404 umożliwia zidentyfikowanie typu zawartości, która generuje największej liczby kodów stanu 404 Nie znaleziono. Na początku raport zawiera wykres słupkowy trwałych 10 pierwszych, dla których został zwrócony kod stanu nie znaleziono 404. Ten wykres słupkowy porównuje całkowita liczba żądań z żądaniami, które spowodowało 404 Nie odnaleziono kodu stanu tych zasobów. Każdy pasek jest oznaczone kolorami. Żółty pasek jest służy do wskazania, że żądanie spowodowało 404 Nie odnaleziono kodu stanu. Czerwony pasek służy do wskazywania całkowita liczba żądań dla elementu zawartości.

> [!NOTE]
> Na potrzeby tego raportu należy uwzględnić następujące informacje:
> 
> * Trafienie reprezentuje wszystkie żądania dla zasobów, niezależnie od tego, kod stanu.
> * Adresy URL CNAME krawędzi są konwertowane na ich równoważne adresy URL usługi CDN. Dzięki temu dokładne zgadzają się dla wszystkich statystyk skojarzone z zasobów, niezależnie od sieci CDN i krawędzi CNAME adres URL używany do żądania.
> 
> 

Lewa strona graph (oś y) wskazuje nazwę pliku dla każdego z górnego żądanych zasobów 10, które wywołały kod stanu nie znaleziono 404. Bezpośrednio pod wykresem (oś x) można znaleźć etykiet wskazujących całkowita liczba żądań i liczba żądań, które wywołały kod stanu nie znaleziono 404.

Bezpośrednio poniżej na wykresie słupkowym następujące informacje zostaną wyświetlone top 250 żądanych zasobów: ścieżka względna (takie jak nazwa pliku), liczba żądań, które spowodowało 404 Nie odnaleziono kodu stanu, całkowita liczba przypadków, w których zażądano elementu zawartości, i Procent żądań, które spowodowało 404 Nie odnaleziono kodu stanu.

## <a name="see-also"></a>Zobacz też
* [Omówienie usługi Azure CDN](cdn-overview.md)
* [Statystyki w czasie rzeczywistym w usłudze Microsoft Azure CDN](cdn-real-time-stats.md)
* [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-rules-engine.md)
* [Analizowanie wydajności](cdn-edge-performance.md)

