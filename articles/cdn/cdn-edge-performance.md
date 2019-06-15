---
title: Analizowanie wydajności węzła krawędzi w usłudze Azure CDN | Dokumentacja firmy Microsoft
description: Analizowanie wydajności węzła krawędzi w usłudze Microsoft Azure CDN. Analiza wydajności krańcowej zawiera szczegółowe informacje dotyczące ruchu i użycia przepustowości sieci CDN.
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
ms.openlocfilehash: 7dfa252c29121adca2ecc77c08b2fca81d56e575
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61070099"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analizowanie wydajności węzła brzegowego w usłudze Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie
Analiza wydajności krańcowej zawiera szczegółowe informacje dotyczące ruchu i użycia przepustowości sieci CDN. Te informacje mogą następnie służyć do generowania statystyk trendów, które pozwalają uzyskać wgląd w sposób Twoje zasoby są buforowane i dostarczania ich klientom. Z kolei pozwala na formularzu strategię dotyczące optymalizacji dostarczania zawartości, i aby ustalić, jakie powinny być problemy rozwiązywane lepsze wykorzystywanie usługi CDN. W rezultacie nie tylko będzie można poprawić wydajność dostarczania danych, ale również można zmniejszyć koszty sieci CDN.

> [!NOTE]
> Wszystkie raporty Notacja UTC/GMT podczas określania daty/godziny.
> 
> 

## <a name="reports-and-log-collection"></a>Raporty i zbieranie danych dziennika
Dane o aktywności sieci CDN muszą być gromadzone przez moduł analiza wydajności krańcowej, zanim może generować raporty na nim. Ten proces kolekcji odbywa się po dziennie i obejmuje działania, które miały miejsce w poprzednim dniu. Oznacza to, że raport statystyki reprezentują próbkę codziennych statystyki w czasie zostało przetworzone i nie musi zawierać kompletny zestaw danych dla bieżącego dnia. Podstawową funkcją tych raportów jest do oceny wydajności. Nie powinny one używane dla celów rozliczeniowych lub dokładną statystyki liczbowych.

> [!NOTE]
> Nieprzetworzone dane, z którego są generowane raporty analizy wydajności usługi Edge jest dostępna przez co najmniej 90 dni.
> 
> 

## <a name="dashboard"></a>Pulpit nawigacyjny
Pulpit nawigacyjny analiza wydajności krańcowej śledzi bieżące i historyczne ruchu sieci CDN, za pomocą wykresów i statystyki. Ten pulpit nawigacyjny umożliwia wykrywanie najnowsze i długoterminowych trendów na wydajność ruchu w sieci CDN dla konta usługi.

Ten pulpit nawigacyjny zawiera:

* Interaktywny wykres, który umożliwia wizualizację kluczowe metryki i trendy.
* Oś czasu, który zapewnia poczucie długoterminowych wzorców kluczowe metryki i trendy.
* Kluczowe metryki i informacje statystyczne na temat naszej sieci CDN poprawia ruch w witrynie mierzony ogólnej wydajności, użycia i wydajności.

### <a name="accessing-the-edge-performance-dashboard"></a>Uzyskiwanie dostępu do pulpitu nawigacyjnego wydajności usługi edge
1. Z poziomu bloku profil CDN kliknij **Zarządzaj** przycisku.
   
    ![Blok profilu CDN Zarządzanie przycisku](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
2. Umieść kursor nad **Analytics** kartę, a następnie umieść kursor nad **analiza wydajności krańcowej** okno wysuwane.  Kliknij pozycję **pulpit nawigacyjny**.
   
    Zostanie wyświetlony pulpit nawigacyjny analizy dla węzła krawędzi.

### <a name="chart"></a>Wykres
Pulpit nawigacyjny zawiera wykres, który śledzi metryki w okresie zaznaczona na osi czasu, który pojawia się bezpośrednio pod nim.  Oś czasu, wykresy górę ostatni dwa lata działania usługi CDN jest wyświetlana bezpośrednio pod wykresem.

#### <a name="using-the-chart"></a>Za pomocą wykresu
* Domyślnie będzie dobrobytu stawka wydajności pamięci podręcznej w ciągu ostatnich 30 dni.
* Ten wykres jest generowany na podstawie danych sortowane codziennie.
* Kursor dnia wykres liniowy wskazuje wartość typu date i wartość metryki w tym dniu.
* Kliknij przycisk Zaznacz weekendy, aby przełączyć nakładki światła szarego pionowych słupków, reprezentujących weekendy na wykresie. Tego rodzaju nakładki jest przydatny do identyfikowania wzorców ruchu podczas weekendów.
* Kliknij widok jednego temu roku do przełączenia nakładki aktywności poprzedniego roku w przedziale czasu na wykresie. Ten typ porównania zapewnia wgląd w wykrywania długoterminowych wzorców użycia usługi CDN. W prawym górnym rogu wykresu zawiera legendzie sprawiała, że wskazuje kod koloru dla każdego wiersza wykresu.

#### <a name="updating-the-chart"></a>Aktualizowanie wykresu
* Zakres czasu: Wykonaj jedną z następujących czynności:
  * Wybierz odpowiedni region na osi czasu. Wykres zostanie zaktualizowany z danymi, które odnosi się do wybranego przedziału czasu.
  * Kliknij dwukrotnie wykres, aby wyświetlić wszystkie dostępne dane historyczne maksymalnie dwa lata.
* Metryka: Kliknij ikonę wykres, który pojawia się obok odpowiednią metrykę. Na wykresie, a oś czasu zostanie odświeżona danych dla odpowiedniego metryki.

### <a name="key-metrics-and-statistics"></a>Kluczowe metryki i statystyk
#### <a name="efficiency-metrics"></a>Metryki wydajności
Te metryki ma na celu zobacz, czy można zwiększyć wydajność pamięci podręcznej. Dostępne są następujące główne zalety pochodną wydajność pamięci podręcznej:

* Mniejsze obciążenie na serwerze źródłowym, co może prowadzić do:
  * Lepsza wydajność serwera sieci web.
  * Zmniejszenie kosztów operacyjnych.
* Ulepszona przyspieszania dostarczania danych, ponieważ więcej żądania będą udostępnianie bezpośrednio z usługi CDN.

| Pole | Opis |
| --- | --- |
| Wydajność pamięci podręcznej |Wskazuje wartość procentowa danych przesyłanych, który był obsługiwany z pamięci podręcznej. Tej metryki miar, gdy zbuforowaną wersję żądanej zawartości był obsługiwany bezpośrednio z sieci CDN (serwerów brzegowych) do stron żądających certyfikatów (np. przeglądarka sieci web) |
| Częstotliwość trafień |Wskazuje procent żądań, które zostały obsłużone z pamięci podręcznej. Tej metryki miar, gdy zbuforowaną wersję żądanej zawartości był obsługiwany bezpośrednio z sieci CDN (serwerów brzegowych) do stron żądających certyfikatów (np. przeglądarka sieci web). |
| % bajtów zdalnego — nie konfiguracji pamięci podręcznej |Wskazuje procent ruchu, który był obsługiwany z serwerów źródłowych do sieci CDN (serwerów brzegowych), nie będzie zapisywane w wyniku funkcji pomijania pamięci podręcznej (aparat reguł HTTP). |
| % bajtów zdalnego - wygasła pamięć podręczna |Wskazuje wartość procentowa ruchu, który był obsługiwany z serwerów źródłowych do sieci CDN (serwerów brzegowych) wyniku starych zawartości ponownego sprawdzania poprawności. |

#### <a name="usage-metrics"></a>Metryki użycia
Celem tych metryk jest zapewnienie wglądu w następujące środki jednoczesnym obniżeniu kosztów:

* Minimalizacja kosztów operacyjnych za pośrednictwem sieci CDN.
* Zmniejszenie wydatków na usługi CDN za pośrednictwem wydajność pamięci podręcznej i kompresji.

> [!NOTE]
> Informacje o natężeniu ruchu woluminów reprezentują ruch, który był używany w obliczeniach, wskaźniki i wartości procentowych i mogą być wyświetlane tylko przez część całkowitego ruchu dla dużej liczby klientów.
> 
> 

| Pole | Opis |
| --- | --- |
| Zapisz bajty wysłane |Wskazuje to średnia liczba bajtów przesłanych dla każdego żądania udostępniana przez usługę CDN (serwerów brzegowych) do zleceniodawcy (np. przeglądarka sieci web). |
| Nie szybkość Config bajtów pamięci podręcznej |Wskazuje procent ruch z sieci CDN (serwerów brzegowych) do zleceniodawcy (np. przeglądarka sieci web), które nie będą buforowane z powodu funkcja pomijania pamięci podręcznej. |
| Współczynnik skompresowanych bajtów |Wskazuje wartość procentową ruch wysyłany przez usługę CDN (serwerów brzegowych) do stron żądających certyfikatów (np. przeglądarka sieci web) w formacie skompresowanym. |
| Bajty wysłane |Wskazuje ilość danych, w bajtach, dostarczone przez usługę CDN (serwerów brzegowych) do zleceniodawcy (np. przeglądarka sieci web). |
| W bajtach |Wskazuje ilość danych, w bajtach wysyłanych z stron żądających certyfikatów (np. przeglądarka sieci web) do sieci CDN (serwery krawędzi). |
| Zdalne bajtów |Wskazuje ilość danych, w bajtach wysyłanych z serwerów źródłowych CDN i klienta do usługi CDN (serwerów brzegowych). |

#### <a name="performance-metrics"></a>Metryki wydajności
Te metryki ma na celu śledzenie ogólną wydajność usługi CDN dla ruchu.

| Pole | Opis |
| --- | --- |
| Szybkość transferu |Wskazuje, średnia szybkość jaką zawartość została przeniesiona z sieci CDN do zleceniodawcy. |
| Czas trwania |Wskazuje, Średni czas w milisekundach, jaki zajęło dostarczania elementu zawartości do żądającego (np. przeglądarka sieci web). |
| Liczba żądań skompresowane |Wskazuje procent liczby trafień, dostarczone przez usługę CDN (serwerów brzegowych) do zleceniodawcy (np. przeglądarka sieci web) w formacie skompresowanym. |
| Współczynnik błędów 4xx |Wskazuje procent trafień wygenerowanych przez kod stanu 4xx. |
| Współczynnik błędów 5xx |Wskazuje procent trafień wygenerowanych przez kod stanu 5xx. |
| Trafienia |Wskazuje liczbę żądań dotyczących zawartości usługi CDN. |

#### <a name="secure-traffic-metrics"></a>Bezpieczny ruch metryki
Te metryki ma na celu śledzić wydajność usługi CDN dla ruchu HTTPS.

| Pole | Opis |
| --- | --- |
| Zabezpieczanie wydajność pamięci podręcznej |Wskazuje wartość procentowa danych przesłanych dla żądania HTTPS, które zostały obsłużone z pamięci podręcznej. Tej metryki miar, gdy zbuforowaną wersję żądanej zawartości był obsługiwany bezpośrednio z sieci CDN (serwerów brzegowych) stron żądających certyfikatów (np. przeglądarka sieci web) za pośrednictwem protokołu HTTPS. |
| Zabezpieczanie szybkość transferu |Wskazuje średnia szybkość jaką zawartość została przeniesiona z sieci CDN (serwerów brzegowych) do stron żądających certyfikatów (np. serwery sieci web), za pośrednictwem protokołu HTTPS. |
| Średni czas trwania bezpieczne |Wskazuje, Średni czas w milisekundach, jaki zajęło dostarczania elementu zawartości do żądającego (np. przeglądarka sieci web) za pośrednictwem protokołu HTTPS. |
| Zabezpieczanie trafień |Wskazuje liczbę żądań HTTPS dla zawartości usługi CDN. |
| Zabezpieczanie bajty wysłane |Wskazuje ilość ruchu HTTPS, w bajtach, dostarczone przez usługę CDN (serwerów brzegowych) do zleceniodawcy (np. przeglądarka sieci web). |

## <a name="reports"></a>Raporty
Każdy raport, w tym module zawiera wykres i statystyki użycia przepustowości i ruchu dla różnych typów metryk (np. kody stanu HTTP, kody stanu pamięci podręcznej, żądanie użytkownika adresu URL itp.). Te informacje mogą służyć delve głębiej w sposób zawartość jest obsługiwana dla klientów i dostosowywać zachowanie usługi CDN, aby zwiększyć wydajność dostarczanie danych.

### <a name="accessing-the-edge-performance-reports"></a>Uzyskiwanie dostępu do raportów wydajności usługi edge
1. Z poziomu bloku profil CDN kliknij **Zarządzaj** przycisku.
   
    ![Blok profilu CDN Zarządzanie przycisku](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
2. Umieść kursor nad **Analytics** kartę, a następnie umieść kursor nad **analiza wydajności krańcowej** okno wysuwane.  Kliknij pozycję **HTTP dużego obiektu**.
   
    Zostanie wyświetlony ekran raportów analizy węzła krawędzi.

| Raport | Opis |
| --- | --- |
| Codzienne podsumowanie |Służy do wyświetlania dzienne trendy ruch w wybranym okresie. Każdy słupek na wykresie reprezentuje określonej daty. Rozmiar paska wskazuje łączna liczba trafień, które wystąpiły w tym dniu. |
| Podsumowanie godzinowe |Służy do wyświetlania co godzinę trendy ruchu w określonym przedziale czasu. Każdy słupek na wykresie reprezentuje pojedynczy godzinę w określonym dniu. Rozmiar paska wskazuje łączna liczba trafień, które wystąpiły w trakcie tej godziny. |
| Protokoły |Przedstawia podział ruchu między protokołów HTTP i HTTPS. Wykres pierścieniowy wskazuje procent liczby trafień, które wystąpiły dla każdego typu protokołu. |
| Metody HTTP |Pozwala szybko poznać HTTP, które metody są używane do żądania danych. Zazwyczaj najbardziej typowe metody żądania HTTP są GET, HEAD i POST. Wykres pierścieniowy wskazuje procent liczby trafień, które wystąpiły dla każdego typu Metoda żądania HTTP. |
| Adresy URL |Zawiera wykres, który zawiera najważniejsze 10 żądane adresy URL. Pasek jest wyświetlany dla każdego adresu URL. Wysokość paska wskazuje liczbę trafień, wygenerowanych przez określonego adresu URL za pośrednictwem przedział czasu objętego raportem. Statystyki dla 100 czołowych żądane adresy URL są wyświetlane bezpośrednio poniżej tego wykresu. |
| CNAMEs |Zawiera wykres, który wyświetla top 10 rekordów CNAME, używany do żądania zasobów w czasie zakres raportu. Statystyki dla 100 czołowych zażądał rekordy CNAME są wyświetlane bezpośrednio poniżej tego wykresu. |
| Źródła |Zawiera wykres, który zawiera najważniejsze 10 CDN lub serwerów źródłowych klienta, z których zażądano zasobów w określonym przedziale czasu. Statystyki dla 100 czołowych zażądał CDN lub klient korzystający z serwerów źródłowych są wyświetlane bezpośrednio poniżej tego wykresu. Serwerów źródłowych klienta są identyfikowane przez nazwę określoną w opcji Nazwa katalogu. |
| POP geograficznie |Pokazuje, ile ruchu przesyłane za pośrednictwem określonego punktu Point of presence (POP, POINT). — Trzyliterowy skrót reprezentuje punkt obecności w naszej sieci CDN. |
| Klienci |Zawiera wykres, który zawiera najważniejsze 10 klientów, które zażądał zasobów w określonym przedziale czasu. Na potrzeby tego raportu wszystkie żądania, które pochodzą z tego samego adresu IP są uznawane za z tego samego klienta. Statystyki dotyczące najważniejszych 100 klientów są wyświetlane bezpośrednio poniżej tego wykresu. Ten raport jest przydatne do określania wzorców działania pobierania, najważniejszych klientów. |
| Stany pamięci podręcznej |Zawiera szczegółowy podział zachowania pamięci podręcznej, która może spowodować ujawnienie podejścia do poprawiania ogólnego środowiska użytkownika końcowego. Ponieważ największą wydajność pochodzi z trafień w pamięci podręcznej, można zoptymalizować szybkości dostarczania danych, minimalizując Chybienia pamięci podręcznej i trafień w pamięci podręcznej wygasła. |
| Brak szczegółów |Zawiera wykres, który zawiera najważniejsze 10 adresów URL dla zasobów, dla których aktualności zawartości pamięci podręcznej nie została sprawdzona w określonym przedziale czasu. Statystyki dotyczące najważniejszych 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| CONFIG_NOCACHE Details |Zawiera wykres, który wyświetla adresy URL 10 najważniejszych zasobów, które nie były buforowane z powodu konfiguracji sieci CDN przez klienta. Tego rodzaju zasoby były obsługiwane bezpośrednio z serwera pochodzenia. Statystyki dotyczące najważniejszych 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły UNCACHEABLE |Zawiera wykres, który zawiera najważniejsze 10 adresów URL dla zasobów, których nie można buforować ze względu na dane nagłówka żądania. Statystyki dotyczące najważniejszych 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły TCP_HIT |Zawiera wykres, który zawiera najważniejsze 10 adresów URL dla zasobów, które są przekazywane bezpośrednio z pamięci podręcznej. Statystyki dotyczące najważniejszych 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| TCP_MISS Details |Zawiera wykres, który zawiera najważniejsze 10 adresów URL dla zasobów, które mają stan pamięci podręcznej TCP_MISS. Statystyki dotyczące najważniejszych 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| Szczegóły TCP_EXPIRED_HIT |Zawiera wykres, który zawiera najważniejsze 10 adresów URL starych zasoby, które zostały obsłużone bezpośrednio z punktu obecności. Statystyki dotyczące najważniejszych 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| TCP_EXPIRED_MISS Details |Zawiera wykres, który zawiera najważniejsze 10 adresów URL starych zasoby, dla których nowa wersja ma mają być pobrane z serwera pochodzenia. Statystyki dotyczące najważniejszych 100 adresów URL dla tych typów zasobów są wyświetlane bezpośrednio poniżej tego wykresu. |
| TCP_CLIENT_REFRESH_MISS Details |Zawiera wykres słupkowy, który wyświetla adresy URL 10 najważniejszych dla zasoby zostały pobrane z serwera pochodzenia ze względu na żądanie nie pamięci podręcznej klienta. Statystyki dotyczące najważniejsze 100 adresy URL dla tych typów żądań, które są wyświetlane bezpośrednio poniżej tego wykresu. |
| Typy żądań klienta |Wskazuje typ żądania, które zostały wprowadzone przez klientów HTTP (np. przeglądarki). Ten raport zawiera wykres pierścieniowy, zapewniający strukturę co sposób obsługi żądań. Informacje o ruchu i przepustowości dla każdego typu żądania jest wyświetlana poniżej wykresu. |
| Agent użytkownika |Zawiera wykres słupkowy, wyświetlanie użytkownika 10 agenci na żądanie za pomocą naszej usługi CDN. Zazwyczaj agent użytkownika jest przeglądarki sieci web, media player lub przeglądarce telefonu komórkowego. Statystyki dla najważniejszych czynników 100 użytkowników są wyświetlane bezpośrednio poniżej tego wykresu. |
| Przywołujących |Zawiera wykres słupkowy, wyświetlanie najczęstsze 10 do zawartości, dostępne za pośrednictwem naszej usługi CDN. Zazwyczaj odwołania jest adres URL strony sieci web lub zasobu, który stanowi łącze do zawartości. Szczegółowych informacji znajduje się poniżej wykres odwołań pierwszych 100. |
| Typy kompresji |Zawiera wykres pierścieniowy, który dzieli żądanych zasobów według tego, czy zostały skompresowane przez naszych serwerów granicznych. Odsetek trwałych skompresowany został podzielony przez typ kompresji używany. Szczegółowych informacji znajduje się poniżej wykresu dla każdego typu kompresji i stanu. |
| Typy plików |Zawiera wykres słupkowy, wyświetlającego najważniejszych typów plików 10, wymagające za pośrednictwem naszej usługi CDN dla swojego konta. Na potrzeby tego raportu, typu pliku jest definiowany przez rozszerzenie nazwy pliku elementu zawartości i typu nośnika w Internet (np. .html \[text/html\], .htm \[text/html\], .aspx \[text/html\]itp.). Szczegółowych informacji znajduje się poniżej wykresu dla najważniejszych typów 100 plików. |
| Unikatowych plików |Zawiera wykres przedstawiający całkowitej liczby unikatowych zasobów, zażądanych w określonym dniu, w określonym przedziale czasu. |
| Token uwierzytelniania podsumowania |Zawiera wykres kołowy, który zawiera krótkie omówienie dotyczące tego, czy żądanych zasobów były zabezpieczone przy użyciu uwierzytelniania opartego na tokenach. Chronione zasoby są wyświetlane na wykresie, zgodnie z wynikami próba uwierzytelniania. |
| Token uwierzytelniania Odmów szczegóły |Zawiera wykres słupkowy, który służy do wyświetlania najważniejsze 10 żądań, które zostały odrzucone z powodu uwierzytelniania opartego na tokenach. |
| Kody odpowiedzi HTTP |Zawiera podział kodów stanu HTTP (np. 200 OK, 403 Dostęp zabroniony, 404 Nie znaleziono, itp.), zostały dostarczone klientom HTTP przez naszych serwerów granicznych. Wykres kołowy pozwala szybko ocenić, jak Twoje zasoby zostały obsłużone. Szczegółowe dane statystyczne towarzyszy każdy kod odpowiedzi pod wykresem. |
| Błędy 404 |Zawiera wykres słupkowy, który służy do wyświetlania najważniejsze 10 żądań, które spowodowały 404 Nie znaleziono kodu odpowiedzi. |
| Błędy 403 |Zawiera wykres słupkowy, który służy do wyświetlania najważniejsze 10 żądań, które spowodowały 403 Zabroniony kod odpowiedzi. Kod odpowiedzi 403 Zabroniony występuje, gdy żądanie zostanie odrzucone przez serwer pochodzenia klient lub serwer graniczny na naszych punktów obecności. |
| Błędy 4xx |Zawiera wykres słupkowy, który służy do wyświetlania najważniejsze 10 żądań, które wpłynęły na kod odpowiedzi z zakresu od 400. Ten raport to 403 404 kody odpowiedzi zabronione i nie można odnaleźć. Zazwyczaj kod odpowiedzi 4xx występuje, gdy żądanie zostanie odrzucone w wyniku błędu klienta. |
| 504 błędy |Zawiera wykres słupkowy, który służy do wyświetlania najważniejsze 10 żądań, które spowodowały 504 kod odpowiedzi limit czasu bramy. 504 kod odpowiedzi limit czasu bramy występuje, gdy zostanie przekroczony limit czasu podczas serwera proxy HTTP do komunikowania się z innym serwerem. W przypadku naszej usługi CDN 504 kod odpowiedzi limit czasu bramy zazwyczaj występuje, gdy serwer graniczny nie może nawiązać komunikacji z serwerem pochodzenia klienta. |
| 502 błędy |Zawiera wykres słupkowy, który służy do wyświetlania najważniejsze 10 żądań, które spowodowały 502 kod odpowiedzi Zła brama. 502 kod odpowiedzi Zła brama występuje, gdy wystąpił błąd protokołu HTTP między serwerem i serwer proxy HTTP. W przypadku naszej usługi CDN 502 kod odpowiedzi Zła brama zazwyczaj występuje, gdy serwer pochodzenia klienta zwraca nieprawidłową odpowiedź do serwera brzegowego. Odpowiedź jest nieprawidłowy, jeśli nie można przeanalizować lub jest on niepełny. |
| Błędy 5xx |Zawiera wykres słupkowy, który służy do wyświetlania najważniejsze 10 żądań, które wpłynęły na kod odpowiedzi 500 zakresu.  Ten raport to 502 — Zła brama i 504 kody odpowiedzi limit czasu bramy. |

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure CDN](cdn-overview.md)
* [Statystyki w czasie rzeczywistym w usłudze Microsoft Azure CDN](cdn-real-time-stats.md)
* [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-rules-engine.md)
* [Zaawansowane raporty HTTP](cdn-advanced-http-reports.md)

