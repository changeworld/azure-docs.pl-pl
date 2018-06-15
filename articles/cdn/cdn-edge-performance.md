---
title: Analizowanie wydajności węzłów brzegowych w usłudze Azure CDN | Dokumentacja firmy Microsoft
description: Analizowanie wydajności węzłów brzegowych w usłudze Microsoft Azure CDN. Analiza wydajności krawędzi zawiera szczegółowe informacje o ruchu i użycia przepustowości sieci CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: ad285b4e2226c85859acb22ba214cc44c77c08e2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "23843693"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analizowanie wydajności węzła brzegowego w usłudze Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Przegląd
Analiza wydajności krawędzi zawiera szczegółowe informacje o ruchu i użycia przepustowości sieci CDN. Te informacje można wygenerować statystyki trendzie, co pozwala uzyskać wgląd w sposób zasobów są buforowane i dostarczyć do klientów. Z kolei pozwala na formularzu strategii dotyczące optymalizacji dostarczania zawartości i do określenia, jakie problemy powinny być rozwiązywane lepiej wykorzystać CDN. W związku z tym nie tylko będzie można poprawić wydajność dostarczania danych, ale również można zmniejszyć koszty sieci CDN.

> [!NOTE]
> Wszystkie raporty Notacja UTC/GMT podczas określania daty/godziny.
> 
> 

## <a name="reports-and-log-collection"></a>Raporty i zbierania dzienników
Przed może generować raporty na nim należy zbierać dane działanie CDN przez moduł analizy wydajności krawędzi. Ten proces zbierania występuje, gdy dziennie i obejmuje działania, które miały miejsce w poprzednim dniu. To oznacza, że dane statystyczne raportu reprezentują próbkę dzień statystyki w czasie przetwarzania i nie musi zawierać pełny zestaw danych dla bieżącego dnia. Podstawową funkcją tych raportów jest do oceny wydajności. Ich nie stosuje się do celów rozliczeń lub dokładną statystyki liczbowych.

> [!NOTE]
> Dane pierwotne, z którego są generowane raporty analityczne wydajności krawędzi jest dostępna dla co najmniej 90 dni.
> 
> 

## <a name="dashboard"></a>Pulpit nawigacyjny
Pulpitu nawigacyjnego Analytics wydajności krawędzi śledzi bieżące i historyczne ruch CDN przez wykres i statystyki. Ten pulpit nawigacyjny do wykrywania ostatnie i długoterminowych trendów na wydajność ruchu w sieci CDN dla Twojego konta.

Ten pulpit nawigacyjny zawiera:

* Wykres interakcyjny, który umożliwia wizualizację kluczowych metryk i trendów.
* Oś czasu, która zapewnia w pewnym sensie wzorce długoterminowej kluczowych metryk i trendów.
* Kluczowe metryki i informacje statystyczne na temat naszych sieci CDN poprawia ruch w witrynie mierzony ogólnej wydajności, użycia i wydajności.

### <a name="accessing-the-edge-performance-dashboard"></a>Uzyskiwanie dostępu do pulpitu nawigacyjnego wydajności krawędzi
1. Blok profilu CDN, kliknij **Zarządzaj** przycisku.
   
    ![Przycisk Zarządzaj blok profilu CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
2. Umieść kursor nad **Analytics** , a następnie umieść kursor nad **analizy wydajności krawędzi** wysuwane okno.  Polecenie **pulpitu nawigacyjnego**.
   
    Pulpitu nawigacyjnego analytics węzła krawędzi jest wyświetlany.

### <a name="chart"></a>Wykres
Pulpit nawigacyjny zawiera śledzący metrykę w okresie zaznaczona na osi czasu, który pojawia się bezpośrednio pod wykresu.  Oś czasu wykresy Konfigurowanie ostatniej dwuletni działania CDN jest wyświetlana bezpośrednio pod wykresem.

#### <a name="using-the-chart"></a>Za pomocą wykresu
* Domyślnie zostanie wykresie stawka wydajności pamięci podręcznej w ciągu ostatnich 30 dni.
* Ten wykres jest generowany na podstawie danych sortowane codziennie.
* Kursora myszy nad dzień w wykres liniowy poinformuje o datę i wartość metryki w tym dniu.
* Kliknij przycisk Wyróżnij weekendy, aby przełączyć nakładki światła szarego pionowych słupków, które reprezentują weekendów na wykresie. Ten typ nakładki jest przydatny do identyfikowania wzorców ruchu w weekendy.
* Kliknij widok jeden temu roku Aby przełączyć nakładką aktywność w poprzednim roku w okresie czasu na wykresie. Ten typ porównania zapewnia wgląd w długoterminowej wzorców użycia sieci CDN. W prawym górnym rogu wykresu zawiera legendy, wskazującą kod koloru, dla każdego wiersza wykresu.

#### <a name="updating-the-chart"></a>Aktualizowanie wykresu
* Przedział czasu: Wykonaj jedną z następujących czynności:
  * Wybierz odpowiedni region na osi czasu. Wykres zostanie zaktualizowany z danymi, które odpowiada wybrany okres czasu.
  * Kliknij dwukrotnie na wykresie, aby wyświetlić wszystkie dostępne dane historyczne maksymalnie dwa lata.
* Metryka: Kliknij ikonę wykres wyświetlany obok żądanego metryki. Oś czasu i wykres zostanie odświeżona danych dla metryki odpowiednie.

### <a name="key-metrics-and-statistics"></a>Kluczowe metryki i dane statystyczne
#### <a name="efficiency-metrics"></a>Metryki wydajności
Te metryki ma na celu zobacz, czy można zwiększyć wydajność pamięci podręcznej. Podstawowe zalety pochodną wydajność pamięci podręcznej są:

* Zmniejszenie obciążenia na serwerze źródłowym, co może prowadzić do:
  * Lepszą wydajność serwera sieci web.
  * Mniejsze koszty operacyjne.
* Ulepszona przyspieszenia dostarczania danych, ponieważ więcej żądań zostanie obsłużona bezpośrednio z sieci CDN.

| Pole | Opis |
| --- | --- |
| Wydajność pamięci podręcznej |Wskazuje procent transferu danych, który zostało obsłużone z pamięci podręcznej. Ta metryka środków po wersja buforowana żądanej zawartości zostało obsłużone bezpośrednio z sieci CDN (serwery krawędzi) do jednostek żądających (np. przeglądarki sieci web) |
| Częstotliwość trafień |Wskazuje procent żądań, które zostały obsłużone z pamięci podręcznej. Ta metryka środków po wersja buforowana żądanej zawartości zostało obsłużone bezpośrednio z sieci CDN (serwery krawędzi) do jednostek żądających (np. przeglądarki sieci web). |
| % liczby bajtów zdalnego — nie konfiguracji pamięci podręcznej |Wskazuje procent ruchu, który został udostępniane przez serwery pochodzenia CDN (serwery krawędzi) nie będzie zapisywane w wyniku funkcja pomijania pamięci podręcznej (aparat reguł HTTP). |
| % Zdalnego bajtów - wygasł pamięci podręcznej |Wskazuje procent ruchu, który zostało obsłużone z serwerów źródła do sieci CDN (serwery krawędzi) wyniku starych ponowna Walidacja zawartości. |

#### <a name="usage-metrics"></a>Metryki użycia
Celem tych metryk jest zapewnienie wglądu w następujących środków Wycinanie koszt:

* Minimalizację kosztów operacyjnych za pośrednictwem sieci CDN.
* Ograniczenie wydatków CDN za pośrednictwem wydajność pamięci podręcznej i kompresji.

> [!NOTE]
> Numery hurtowych ruchu reprezentują ruch, który był używany w obliczeniach stosunek i wartości procentowe i mogą być wyświetlane tylko część łącznie dla ruchu dla dużej liczby klientów.
> 
> 

| Pole | Opis |
| --- | --- |
| Zapisz bajtów wyjściowych |Wskazuje średnią liczbę bajtów przesłanych dla każdego żądania pochodzący z sieci CDN (serwery krawędzi) do obiektu żądającego (np. przeglądarki sieci web). |
| Nie szybkość Config bajtów pamięci podręcznej |Wskazuje procent ruch pochodzący z sieci CDN (serwery krawędzi) do obiektu żądającego (np. przeglądarki sieci web) nie będą buforowane z powodu funkcja pomijania pamięci podręcznej. |
| Szybkość skompresowanych bajtów |Wskazuje procent ruch wysyłany z sieci CDN (serwery krawędzi) do jednostek żądających (np. przeglądarki sieci web) w formacie skompresowanym. |
| Bajty wysłane |Określa ilość danych w bajtach, które zostały dostarczone z sieci CDN (serwery krawędzi) do obiektu żądającego (np. przeglądarki sieci web). |
| Wyrażona w bajtach |Wskazuje ilość danych w bajtach, wysyłane z jednostek żądających (np. przeglądarki sieci web) do sieci CDN (serwery krawędzi). |
| Zdalne bajtów |Określa ilość danych w bajtach, wysyłane z serwerów pochodzenia CDN i klienta do sieci CDN (serwery krawędzi). |

#### <a name="performance-metrics"></a>Metryki wydajności
Te metryki ma na celu śledzenie ogólną wydajność sieci CDN dla ruchu.

| Pole | Opis |
| --- | --- |
| Szybkość transferu |Wskazuje średnią jaką zawartości został przeniesiony z sieci CDN do zleceniodawcy. |
| Czas trwania |Wskazuje, Średni czas w milisekundach, zajęło dostarczania elementu zawartości do zleceniodawcy (np. przeglądarki sieci web). |
| Współczynnik żądań skompresowane |Wskazuje procent trafień, które zostały dostarczone z sieci CDN (serwery krawędzi) do obiektu żądającego (np. przeglądarki sieci web) w formacie skompresowanym. |
| Współczynnik błędów 4xx |Wskazuje procent trafień wygenerowanych 4xx kod stanu. |
| Współczynnik błędów 5xx |Wskazuje procent trafień wygenerowanych 5xx kod stanu. |
| Trafienia |Określa liczbę żądań dotyczących zawartości usługi CDN. |

#### <a name="secure-traffic-metrics"></a>Bezpieczny ruch metryk
Te metryki ma na celu śledzić wydajność sieci CDN dla ruchu HTTPS.

| Pole | Opis |
| --- | --- |
| Zabezpieczenia, wydajność pamięci podręcznej |Wskazuje procent danych przesyłanych do żądań HTTPS, które zostały obsłużone z pamięci podręcznej. Ta metryka środków po wersja buforowana żądanej zawartości zostało obsłużone bezpośrednio z sieci CDN (serwery krawędzi) jednostek żądających (np. przeglądarki sieci web) za pośrednictwem protokołu HTTPS. |
| Zabezpieczanie szybkość transferu |Wskazuje średnią jaką zawartości został przeniesiony z sieci CDN (serwery krawędzi) do jednostek żądających (np. serwery sieci web) za pośrednictwem protokołu HTTPS. |
| Średni czas trwania bezpieczne |Wskazuje, Średni czas w milisekundach, zajęło dostarczania elementu zawartości do zleceniodawcy (np. przeglądarki sieci web) za pośrednictwem protokołu HTTPS. |
| Bezpieczne trafień |Określa liczbę żądań HTTPS dla zawartość usługi CDN. |
| Bezpieczne bajtów wyjściowych |Wskazuje ilość ruchu HTTPS, w bajtach, które zostały dostarczone z sieci CDN (serwery krawędzi) do obiektu żądającego (np. przeglądarki sieci web). |

## <a name="reports"></a>Raporty
Każdy raport, w tym module zawiera wykres i statystyki dotyczące wykorzystania przepustowości i ruchu dla różnych typów metryki (np. kody stanu HTTP, kodów stanu pamięci podręcznej, żądanie adresu URL itp). Te informacje mogą służyć do delve głębiej, w jaki sposób zawartość jest obsługiwana dla klientów i dostosowywać zachowanie CDN, aby poprawić wydajność dostarczania danych.

### <a name="accessing-the-edge-performance-reports"></a>Uzyskiwanie dostępu do raportów wydajności krawędzi
1. Blok profilu CDN, kliknij **Zarządzaj** przycisku.
   
    ![Przycisk Zarządzaj blok profilu CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
2. Umieść kursor nad **Analytics** , a następnie umieść kursor nad **analizy wydajności krawędzi** wysuwane okno.  Polecenie **HTTP dużego obiektu**.
   
    Zostanie wyświetlony ekran raportów analizy węzła krawędzi.

| Raport | Opis |
| --- | --- |
| Dzienne podsumowanie |Umożliwia wyświetlenie dzienne trendy ruchu w określonym przedziale czasu. Każdy słupek na wykresie reprezentuje określonej daty. Rozmiar paska wskazuje łączna liczba trafień, które wystąpiły w tym dniu. |
| Podsumowanie co godzinę |Umożliwia wyświetlanie co godzinę ruchu trendów w określonym przedziale czasu. Każdy słupek na tym wykresie reprezentuje pojedynczy godzinę w określonym dniu. Rozmiar paska wskazuje łączna liczba trafień, które wystąpiły podczas danej godziny. |
| Protokoły |Przedstawia podział ruchu między protokołami HTTP i HTTPS. Wykres pierścieniowy przedstawiający wskazuje procent trafień, które wystąpiły dla każdego typu protokołu. |
| Metody HTTP |Umożliwia uzyskiwanie nowościami, które HTTP metody są używane do żądania danych. Zazwyczaj najbardziej typowe metody żądania HTTP są GET, HEAD i POST. Wykres pierścieniowy przedstawiający wskazuje procent trafień, które wystąpiły dla każdego typu Metoda żądania HTTP. |
| Adresy URL |Zawiera wykresu, który wyświetla top 10 żądanego adresów URL. Dla każdego adresu URL jest wyświetlany pasek. Wysokość paska wskazuje, ile razy wygenerowanych określonego adresu URL za pośrednictwem przedział czasu objętego raportem. Statystyka dla pierwszych 100 zażądał adresy URL są wyświetlane bezpośrednio poniżej tego wykresu. |
| CNAMEs |Zawiera wykresu, który wyświetla pierwszych 10 rekordów CNAME używane do żądania zasobów w czasie span raportu. Statystyka dla pierwszych 100 zażądał CNAME są wyświetlane bezpośrednio poniżej tego wykresu. |
| Źródła |Zawiera wykres przedstawia top 10 CDN lub klienta pochodzenia serwerów, z których wystąpiło zasobów w określonym okresie. Statystyka dla pierwszych 100 zażądał serwery pochodzenia CDN lub klienta są wyświetlane bezpośrednio poniżej tego wykresu. Serwery pochodzenia klienta są identyfikowane przez nazwę zdefiniowane w przypadku opcji Nazwa katalogu. |
| POP Geo |Pokazuje, ile ruchu rozsyłane do określonego punktu elementu obecności (POP). Skrót trzyliterowy reprezentuje POP w naszej sieci CDN. |
| Klienci |Zawiera wykres przedstawia top 10 klientów, którzy zażądali zasobów w określonym okresie. Na potrzeby tego raportu z tego samego klienta są traktowane jako wszystkie żądania, które pochodzą z tego samego adresu IP. Statystyki dotyczące top 100 klientów są wyświetlane bezpośrednio poniżej tego wykresu. Ten raport jest przydatne w przypadku określania wzorce działania pobierania top klientów. |
| Stany pamięci podręcznej |Zapewnia szczegółowe podział zachowanie pamięci podręcznej może ujawnić podejścia do poprawy ogólnej środowisko użytkownika końcowego. Ponieważ największą wydajność pochodzi z trafień w pamięci podręcznej, aby zoptymalizować szybkości dostarczania danych, minimalizując Chybienia pamięci podręcznej i trafień w pamięci podręcznej wygasła. |
| Brak szczegółowych informacji |Zawiera wykres przedstawia top 10 adresów URL dla zasobów, dla których aktualności zawartości pamięci podręcznej nie została sprawdzona w określonym okresie. Statystyki dla górnej 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły CONFIG_NOCACHE |Zawiera wykresu, który wyświetla 10 pierwszych adresów URL dla zasobów, które nie były buforowane z powodu konfiguracji sieci CDN w warstwie klienta. Tego rodzaju zasoby zostały obsłużone bezpośrednio z serwera pochodzenia. Statystyki dla górnej 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły UNCACHEABLE |Zawiera wykres przedstawia top 10 adresów URL dla zasobów, których nie można buforować z powodu danych nagłówka żądania. Statystyki dla górnej 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły TCP_HIT |Zawiera wykres przedstawia top 10 adresów URL dla zasobów, które są obsługiwane bezpośrednio z pamięci podręcznej. Statystyki dla górnej 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły TCP_MISS |Zawiera wykres przedstawia top 10 adresów URL dla zasobów, które będą miały stan TCP_MISS pamięci podręcznej. Statystyki dla górnej 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły TCP_EXPIRED_HIT |Zawiera wykres przedstawia top 10 adresów URL starych zasoby, które były przekazywane bezpośrednio z punktu obecności. Statystyki dla górnej 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły TCP_EXPIRED_MISS |Zawiera wykres przedstawia top 10 adresów URL starych zasobów, dla których nowej wersji ma zostać pobrane z serwera pochodzenia. Statystyki dla górnej 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły TCP_CLIENT_REFRESH_MISS |Zawiera wykres słupkowy wyświetlający 10 pierwszych adresów URL dla zasobów zostały pobrane z serwera pochodzenia z powodu żądania nie pamięci podręcznej klienta. Statystyki dla górnej 100 adresów URL dla tych typów żądań, które są wyświetlane bezpośrednio pod ten wykres. |
| Typy żądań klienta |Wskazuje typ żądań, które zostały dokonane przez klientów HTTP (np. przeglądarki). Ten raport zawiera wykres pierścieniowy przedstawiający zapewnia znaczeniu określające sposób obsługi żądań. Informacje o przepustowości i ruchu dla każdego typu żądania jest wyświetlony poniżej wykresu. |
| Agent użytkownika |Zawiera wykres słupkowy wyświetlanie top agentom użytkownika 10 na żądanie zawartości za pośrednictwem naszego CDN. Agent użytkownika jest zwykle przeglądarki sieci web, odtwarzacza lub przeglądarki telefonu komórkowego. Statystyki dla górnej agenci 100 użytkowników są wyświetlane bezpośrednio pod ten wykres. |
| Odwołań |Zawiera wykres słupkowy wyświetlanie najczęstsze 10 do zawartości dostępnej za pośrednictwem naszego CDN. Zazwyczaj odwołania jest adres URL strony sieci web lub zasobu, który stanowi łącze do zawartości. Szczegółowe informacje podano poniżej wykres dla pierwszych 100 odwołań. |
| Typy kompresji |Zawiera wykres pierścieniowy przedstawiający dzieli żądanych zasobów według tego, czy zostały skompresowane przez serwery krawędzi. Procent zasobów skompresowany dzieli się przez typ kompresji używany. Szczegółowe informacje poniżej wykresu dla każdego typu kompresji i stanu. |
| Typy plików |Zawiera wykres słupkowy Wyświetla top typy plików 10 wymagające za pośrednictwem naszego CDN dla Twojego konta. Na potrzeby tego raportu, typu pliku jest definiowany przez rozszerzenie nazwy pliku elementu zawartości i typ nośnika Internet (np. .html \[tekst i html\], .htm \[tekst i html\], .aspx \[tekstu/html\]itp.). Szczegółowe informacje poniżej wykresu top 100 plików. |
| Unikatowy plików |Zawiera wykres geograficzne całkowitej liczby unikatowych zasobów, zażądanych danego dnia w określonym okresie. |
| Token uwierzytelniania podsumowania |Zawiera wykresu kołowego, która zapewnia szybki przegląd na czy żądanych zasobów były chronione przez uwierzytelnianie oparte na Token. Chronione zasoby są wyświetlane na wykresie zgodnie z wynikami próba uwierzytelniania. |
| Szczegóły Odmów tokenu uwierzytelniania |Zawiera wykres słupkowy, który umożliwia wyświetlenie Najczęstsze żądania 10, które zostały odrzucone z powodu uwierzytelniania opartego na tokenie. |
| Kody odpowiedzi HTTP |Zawiera podział kody stanu HTTP (np. 200 OK 403 Zabroniony, nie można odnaleźć 404, itp.) które zostały dostarczone do klientów HTTP przez serwery krawędzi. Wykres kołowy pozwala szybko ocenić, jak zostały obsłużone zasobów. Szczegółowe dane statystyczne jest dostępna dla każdego kodu odpowiedzi pod wykresem. |
| Błędów 404 |Zawiera wykres słupkowy, który służy do wyświetlania Najczęstsze żądania 10, które wywołały kod odpowiedzi nie znaleziono 404. |
| Błędy 403 |Zawiera wykres słupkowy, który służy do wyświetlania Najczęstsze żądania 10, które wywołały kod odpowiedzi 403 Zabroniony. Kod odpowiedzi 403 Zabroniony występuje, gdy żądanie zostanie odrzucone przez serwer pochodzenia klient lub serwer graniczny na naszych POP. |
| Błędy 4xx |Zawiera wykres słupkowy, który służy do wyświetlania Najczęstsze żądania 10, które wywołały kod odpowiedzi w zakresie od 400. Ten raport to 403 404 kody odpowiedzi zabroniony i nie można odnaleźć. Zazwyczaj kod odpowiedzi 4xx występuje, gdy żądanie zostanie odrzucone wyniku błąd klienta. |
| Błędy 504 |Zawiera wykres słupkowy, który służy do wyświetlania Najczęstsze żądania 10, które wywołały kod odpowiedzi 504 upływu limitu czasu bramy. Kod odpowiedzi 504 upływu limitu czasu bramy występuje, gdy zostanie przekroczony limit czasu podczas serwer proxy HTTP do komunikowania się z innym serwerem. W przypadku naszego CDN 504 kod odpowiedzi upływu limitu czasu bramy zwykle występuje, gdy serwer graniczny nie może nawiązać komunikacji z serwerem pochodzenia klienta. |
| Błędy 502 |Zawiera wykres słupkowy, który służy do wyświetlania Najczęstsze żądania 10, które wywołały kod odpowiedzi 502 Zła brama. 502 kod odpowiedzi Zła brama występuje, gdy wystąpił błąd protokołu HTTP między serwerem i serwer proxy HTTP. W przypadku naszego CDN 502 kod odpowiedzi Zła brama zwykle występuje, gdy serwer pochodzenia klienta zwraca nieprawidłową odpowiedź na serwer graniczny. Odpowiedź jest nieprawidłowa, jeśli nie można przeanalizować lub jest niekompletny. |
| Błędy 5xx |Zawiera wykres słupkowy, który służy do wyświetlania Najczęstsze żądania 10, które wywołały kod odpowiedzi w zakresie 500.  Ten raport to 502 Niewłaściwa brama i 504 kody odpowiedzi upływu limitu czasu bramy. |

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure CDN](cdn-overview.md)
* [Statystyki w czasie rzeczywistym w usłudze Microsoft Azure CDN](cdn-real-time-stats.md)
* [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-rules-engine.md)
* [Raporty HTTP zaawansowane](cdn-advanced-http-reports.md)

