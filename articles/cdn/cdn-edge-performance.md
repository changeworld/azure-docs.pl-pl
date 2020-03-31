---
title: Analizowanie wydajności węzła brzegowego w usłudze Azure CDN | Dokumenty firmy Microsoft
description: Analizowanie wydajności węzła brzegowego w sieci CDN platformy Microsoft Azure. Edge Performance Analytics zapewnia szczegółowy ruch informacyjny i wykorzystanie przepustowości dla sieci CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b8a65d4ae6aaac78e642c851a66b745a940fa0ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593897"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analizowanie wydajności węzła brzegowego w usłudze Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie
Analiza wydajności krawędzi zapewnia szczegółowy ruch informacyjny i wykorzystanie przepustowości dla sieci CDN. Informacje te mogą być następnie używane do generowania trendów statystycznych, które pozwalają uzyskać wgląd w sposób buforowania zasobów i dostarczane do klientów. To z kolei pozwala na stworzenie strategii optymalizacji dostarczania treści i określenie, jakie problemy należy rozwiązać, aby lepiej wykorzystać sieć CDN. W rezultacie nie tylko będziesz w stanie poprawić wydajność dostarczania danych, ale także będziesz w stanie obniżyć koszty cdn.

> [!NOTE]
> Wszystkie raporty używają notacji UTC/GMT podczas określania daty/godziny.
> 
> 

## <a name="reports-and-log-collection"></a>Raporty i zbieranie dzienników
Dane aktywności usługi CDN muszą być zbierane przez moduł Analizy wydajności krawędzi, zanim będzie mógł generować raporty na jej temat. Ten proces zbierania danych występuje raz dziennie i obejmuje działanie, które miało miejsce w ciągu poprzedniego dnia. Oznacza to, że statystyki raportu reprezentują próbkę statystyk dnia w czasie, gdy był przetwarzany i niekoniecznie zawierają pełny zestaw danych dla bieżącego dnia. Podstawową funkcją tych sprawozdań jest ocena wydajności. Nie powinny one być używane do celów rozliczeniowych ani do dokładnych statystyk liczbowych.

> [!NOTE]
> Nieprzetworzone dane, z których są generowane raporty analityczne wydajności krawędzi, są dostępne przez co najmniej 90 dni.
> 
> 

## <a name="dashboard"></a>Pulpit nawigacyjny
Pulpit nawigacyjny Edge Performance Analytics śledzi bieżący i historyczny ruch cdn za pomocą wykresu i statystyk. Ten pulpit nawigacyjny służy do wykrywania najnowszych i długoterminowych trendów dotyczących wydajności ruchu sieci CDN na koncie.

Ten pulpit nawigacyjny składa się z:

* Interaktywny wykres, który umożliwia wizualizację kluczowych metryk i trendów.
* Oś czasu, która zapewnia poczucie długoterminowych wzorców dla kluczowych metryk i trendów.
* Kluczowe dane i informacje statystyczne na temat tego, jak nasza sieć CDN poprawia ruch w witrynie, mierzoną ogólną wydajnością, użyciem i wydajnością.

### <a name="accessing-the-edge-performance-dashboard"></a>Uzyskiwanie dostępu do pulpitu nawigacyjnego wydajności krawędzi
1. W bloku profilu sieci CDN kliknij przycisk **Zarządzaj.**
   
    ![Przycisk zarządzania łatami profilu CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania siecią CDN.
2. Umieść wskaźnik myszy na karcie **Analytics,** a następnie umieść wskaźnik myszy na **wysu wysu wysu wysu wysuwu Edge Performance Analytics.**  Kliknij **pulpit nawigacyjny**.
   
    Zostanie wyświetlony pulpit nawigacyjny analizy węzła krawędzi.

### <a name="chart"></a>Wykres
Pulpit nawigacyjny zawiera wykres, który śledzi metrykę w okresie wybranym na osi czasu, która pojawia się bezpośrednio pod nim.  Oś czasu, która wykresy do ostatnich dwóch lat aktywności sieci CDN jest wyświetlany bezpośrednio pod wykresem.

#### <a name="using-the-chart"></a>Korzystanie z wykresu
* Domyślnie wskaźnik wydajności pamięci podręcznej w ciągu ostatnich 30 dni zostanie na wykresie.
* Ten wykres jest generowany na podstawie danych zestawianych codziennie.
* Najechanie kursorem na dzień na wykresie liniowym wskazuje datę i wartość danych w tym dniu.
* Kliknij przycisk Wyróżnij weekendy, aby przełączyć na wykres nakładkę jasnoszarych pionowych słupków przedstawiających weekendy. Ten typ nakładki jest przydatny do identyfikowania wzorców ruchu w weekendy.
* Kliknij pozycję Wyświetl rok temu, aby przełączyć nakładkę aktywności z poprzedniego roku w tym samym okresie na wykres. Ten typ porównania zapewnia wgląd w długoterminowe wzorce użycia sieci CDN. W prawym górnym rogu wykresu znajduje się legenda wskazująca kod koloru dla każdego wykresu liniowego.

#### <a name="updating-the-chart"></a>Aktualizowanie wykresu
* Zakres czasu: Wykonaj jedną z następujących czynności:
  * Wybierz żądany region na osi czasu. Wykres zostanie zaktualizowany o dane odpowiadające wybranemu okresowi.
  * Kliknij dwukrotnie wykres, aby wyświetlić wszystkie dostępne dane historyczne maksymalnie do dwóch lat.
* Metryka: kliknij ikonę wykresu wyświetlaną obok żądanej metryki. Wykres i oś czasu zostaną odświeżone z danymi dla odpowiedniej metryki.

### <a name="key-metrics-and-statistics"></a>Kluczowe dane i statystyki
#### <a name="efficiency-metrics"></a>Wskaźniki wydajności
Celem tych metryk jest sprawdzenie, czy można poprawić wydajność pamięci podręcznej. Główne korzyści wynikające z wydajności pamięci podręcznej to:

* Mniejsze obciążenie serwera pochodzenia, co może prowadzić do:
  * Lepsza wydajność serwera www.
  * Niższe koszty operacyjne.
* Ulepszone przyspieszenie dostarczania danych, ponieważ więcej żądań będą obsługiwane bezpośrednio z sieci CDN.

| Pole | Opis |
| --- | --- |
| Wydajność pamięci podręcznej |Wskazuje procent przesyłanych danych, które zostały wyświetlone z pamięci podręcznej. Ta metryka mierzy, kiedy buforowana wersja żądanej zawartości była obsługiwana bezpośrednio z sieci CDN (serwerów brzegowych) do żądań (np. przeglądarka internetowa) |
| Współczynnik trafień |Wskazuje procent żądań, które zostały doręczone z pamięci podręcznej. Ta metryka mierzy, gdy buforowana wersja żądanej zawartości była obsługiwana bezpośrednio z sieci CDN (serwerów brzegowych) do żądań (np. przeglądarki sieci Web). |
| % bajtów zdalnych — brak konfiguracji pamięci podręcznej |Wskazuje procent ruchu, który był obsługiwany z serwerów pochodzenia do sieci CDN (serwerów brzegowych), która nie będzie buforowana w wyniku funkcji Bypass Cache (Aparat reguł HTTP). |
| % bajtów zdalnych — wygasła pamięć podręczna |Wskazuje procent ruchu, który był obsługiwany z serwerów pochodzenia do sieci CDN (serwerów brzegowych) w wyniku ponownego oceny nieaktualną zawartości. |

#### <a name="usage-metrics"></a>Metryki użycia
Celem tych wskaźników jest zapewnienie wglądu w następujące środki cięcia kosztów:

* Minimalizacja kosztów operacyjnych za pośrednictwem sieci CDN.
* Zmniejszenie wydatków sieci CDN dzięki wydajności pamięci podręcznej i kompresji.

> [!NOTE]
> Numery woluminów ruchu reprezentują ruch, który był używany w obliczeniach współczynników i wartości procentowych i mogą pokazywać tylko część całkowitego ruchu dla klientów o dużej liczbie.
> 
> 

| Pole | Opis |
| --- | --- |
| Ave Bajty na zewnątrz |Wskazuje średnią liczbę bajtów przesłanych dla każdego żądania obsługiwanego przez sieć CDN (serwery brzegowe) do osoby żądającej (np. przeglądarka internetowa). |
| Brak szybkości bajtów konfiguracji pamięci podręcznej |Wskazuje procent ruchu obsługiwanego z sieci CDN (serwerów brzegowych) do żądacza (np. przeglądarki sieci web), który nie zostanie buforowany z powodu funkcji Bypass Cache. |
| Szybkość skompresowanego bajtu |Wskazuje procent ruchu wysyłanego z sieci CDN (serwerów brzegowych) do osób żądającej (np. przeglądarki internetowej) w skompresowanym formacie. |
| Bajty na zewnątrz |Wskazuje ilość danych w bajtach, które zostały dostarczone z sieci CDN (serwerów brzegowych) do osoby żądającej (np. przeglądarki sieci Web). |
| Bajty w |Wskazuje ilość danych w bajtach wysyłanych przez osoby żądającej (np. przeglądarkę internetową) do sieci CDN (serwerów brzegowych). |
| Pilot bajtów |Wskazuje ilość danych w bajtach wysyłanych z sieci CDN i serwerów pochodzenia klienta do sieci CDN (serwerów brzegowych). |

#### <a name="performance-metrics"></a>Metryki wydajności
Celem tych metryk jest śledzenie ogólnej wydajności sieci CDN dla ruchu.

| Pole | Opis |
| --- | --- |
| Szybkość transferu |Wskazuje średnią szybkość, z jaką zawartość została przeniesiona z sieci CDN do osoby żądawczej. |
| Czas trwania |Wskazuje średni czas, w milisekundach, zajęło dostarczenie zasobu do osoby żądawczej (np. przeglądarki sieci web). |
| Szybkość skompresowanego żądania |Wskazuje procent trafień, które zostały dostarczone z sieci CDN (serwerów brzegowych) do żądacza (np. przeglądarki internetowej) w skompresowanym formacie. |
| 4xx Poziom błędu |Wskazuje procent trafień, które wygenerowały kod stanu 4xx. |
| 5xx Poziom błędu |Wskazuje procent trafień, które wygenerowały kod stanu 5xx. |
| Trafienia |Wskazuje liczbę żądań dotyczących zawartości sieci CDN. |

#### <a name="secure-traffic-metrics"></a>Metryki bezpiecznego ruchu
Celem tych metryk jest śledzenie wydajności sieci CDN dla ruchu HTTPS.

| Pole | Opis |
| --- | --- |
| Bezpieczna wydajność pamięci podręcznej |Wskazuje procent danych przesyłanych dla żądań HTTPS, które zostały wyświetlone z pamięci podręcznej. Ta metryka mierzy, gdy buforowana wersja żądanej zawartości była obsługiwana bezpośrednio z sieci CDN (serwerów brzegowych) do żądań (np. przeglądarki sieci Web) za pośrednictwem protokołu HTTPS. |
| Bezpieczna szybkość transferu |Wskazuje średnią szybkość przesyłania zawartości z sieci CDN (serwerów brzegowych) do osób żądającej (np. serwerów sieci web) za pośrednictwem protokołu HTTPS. |
| Średni czas trwania bezpiecznego |Wskazuje średni czas, w milisekundach, zajęło dostarczenie zasobu do osoby żądawczej (np. przeglądarki sieci web) za pośrednictwem protokołu HTTPS. |
| Bezpieczne trafienia |Wskazuje liczbę żądań HTTPS dla zawartości usługi CDN. |
| Bezpieczne bajty |Wskazuje ilość ruchu HTTPS w bajtach, które zostały dostarczone z sieci CDN (serwerów brzegowych) do żądacza (np. przeglądarki sieci web). |

## <a name="reports"></a>Raporty
Każdy raport w tym module zawiera wykres i statystyki dotyczące wykorzystania przepustowości i ruchu dla różnych typów metryk (np. kody stanu HTTP, kody stanu pamięci podręcznej, adres URL żądania itp.). Te informacje mogą służyć do zagłębiania się w sposób, w jaki zawartość jest obsługiwana klientom i dostroić zachowanie sieci CDN w celu zwiększenia wydajności dostarczania danych.

### <a name="accessing-the-edge-performance-reports"></a>Uzyskiwanie dostępu do raportów wydajności krawędzi
1. W bloku profilu sieci CDN kliknij przycisk **Zarządzaj.**
   
    ![Przycisk zarządzania łatami profilu CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania siecią CDN.
2. Umieść wskaźnik myszy na karcie **Analytics,** a następnie umieść wskaźnik myszy na **wysu wysu wysu wysu wysuwu Edge Performance Analytics.**  Kliknij **duży obiekt HTTP**.
   
    Zostanie wyświetlony ekran raportów analizy węzła krawędzi.

| Raport | Opis |
| --- | --- |
| Podsumowanie dzienne |Umożliwia wyświetlanie dziennych trendów ruchu w określonym przedziale czasu. Każdy pasek na tym wykresie reprezentuje określoną datę. Rozmiar paska wskazuje całkowitą liczbę trafień, które miały miejsce w tym dniu. |
| Podsumowanie godzinowe |Umożliwia wyświetlanie trendów ruchu godzinowego w określonym przedziale czasu. Każdy pasek na tym wykresie reprezentuje jedną godzinę w określonym dniu. Rozmiar paska wskazuje całkowitą liczbę trafień, które wystąpiły w tej godzinie. |
| Protokoły |Wyświetla podział ruchu między protokołami HTTP i HTTPS. Wykres pierścieniowy wskazuje procent trafień, które wystąpiły dla każdego typu protokołu. |
| Metody HTTP |Pozwala szybko zorientować się, które metody HTTP są używane do żądania danych. Zazwyczaj najbardziej typowe metody żądania HTTP są GET, HEAD i POST. Wykres pierścieniowy wskazuje procent trafień, które wystąpiły dla każdego typu metody żądania HTTP. |
| adresy URL |Zawiera wykres, który wyświetla 10 pierwszych żądanych adresów URL. Dla każdego adresu URL zostanie wyświetlony pasek. Wysokość paska wskazuje, ile trafień, które wygenerował dany adres URL w okresie objętym raportem. Statystyki 100 żądanych adresów URL są wyświetlane bezpośrednio pod tym wykresem. |
| CNA |Zawiera wykres, który wyświetla 10 najlepszych CNAMEs używane do żądania zasobów w przedziale czasowym raportu. Statystyki dla 100 najlepszych żądanych CNAME są wyświetlane bezpośrednio pod tym wykresem. |
| Pochodzenia |Zawiera wykres, który wyświetla 10 najlepszych serwerów CDN lub serwerów pochodzenia klienta, z których żądano zasobów przez określony czas. Statystyki dla 100 pierwszych żądanych serwerów CDN lub serwerów pochodzenia klienta są wyświetlane bezpośrednio pod tym wykresem. Serwery pochodzenia klienta są identyfikowane przez nazwę zdefiniowaną w opcji Nazwa katalogu. |
| GeoOkipów |Pokazuje, jaka część ruchu jest kierowana przez określony punkt obecności (POP). Trzyliterowy skrót reprezentuje pop w naszej sieci CDN. |
| Klienci |Zawiera wykres, który wyświetla 10 najlepszych klientów, którzy zażądali zasobów w określonym czasie. Na potrzeby tego raportu wszystkie żądania, które pochodzą z tego samego adresu IP są uważane za pochodzące od tego samego klienta. Statystyki dla 100 najlepszych klientów są wyświetlane bezpośrednio pod tym wykresem. Ten raport jest przydatny do określania wzorców aktywności pobierania dla najlepszych klientów. |
| Stany pamięci podręcznej |Zawiera szczegółowy podział zachowania pamięci podręcznej, które mogą ujawniać podejścia do poprawy ogólnego środowiska użytkownika końcowego. Ponieważ najwyższa wydajność pochodzi z trafień pamięci podręcznej, można zoptymalizować szybkość dostarczania danych, minimalizując chybienia pamięci podręcznej i wygasłe trafienia pamięci podręcznej. |
| BRAK Szczegóły |Zawiera wykres, który wyświetla 10 pierwszych adresów URL dla zasobów, dla których świeżość zawartości pamięci podręcznej nie została sprawdzona przez określony czas. Statystyki 100 najlepszych adresów URL dla tego typu zasobów są wyświetlane bezpośrednio pod tym wykresem. |
| CONFIG_NOCACHE szczegóły |Zawiera wykres, który wyświetla 10 pierwszych adresów URL dla zasobów, które nie zostały buforowane z powodu konfiguracji sieci CDN klienta. Te typy zasobów były obsługiwane bezpośrednio z serwera pochodzenia. Statystyki 100 najlepszych adresów URL dla tego typu zasobów są wyświetlane bezpośrednio pod tym wykresem. |
| UNCACHEABLE Szczegóły |Zawiera wykres, który wyświetla 10 pierwszych adresów URL dla zasobów, których nie można buforować z powodu danych nagłówka żądania. Statystyki 100 najlepszych adresów URL dla tego typu zasobów są wyświetlane bezpośrednio pod tym wykresem. |
| TCP_HIT szczegóły |Zawiera wykres, który wyświetla 10 pierwszych adresów URL dla zasobów, które są obsługiwane natychmiast z pamięci podręcznej. Statystyki 100 najlepszych adresów URL dla tego typu zasobów są wyświetlane bezpośrednio pod tym wykresem. |
| TCP_MISS szczegóły |Zawiera wykres, który wyświetla 10 pierwszych adresów URL dla zasobów, które mają stan pamięci podręcznej TCP_MISS. Statystyki 100 najlepszych adresów URL dla tego typu zasobów są wyświetlane bezpośrednio pod tym wykresem. |
| TCP_EXPIRED_HIT szczegóły |Zawiera wykres, który wyświetla 10 pierwszych adresów URL dla starych zasobów, które zostały wyświetlone bezpośrednio z punktu obecności. Statystyki 100 najlepszych adresów URL dla tego typu zasobów są wyświetlane bezpośrednio pod tym wykresem. |
| TCP_EXPIRED_MISS szczegóły |Zawiera wykres, który wyświetla 10 pierwszych adresów URL dla starych zasobów, dla których nowa wersja musiała zostać pobrana z serwera pochodzenia. Statystyki 100 najlepszych adresów URL dla tego typu zasobów są wyświetlane bezpośrednio pod tym wykresem. |
| TCP_CLIENT_REFRESH_MISS szczegóły |Zawiera wykres słupkowy, który wyświetla 10 pierwszych adresów URL dla zasobów zostały pobrane z serwera pochodzenia z powodu żądania braku pamięci podręcznej od klienta. Statystyki 100 najlepszych adresów URL dla tego typu żądań są wyświetlane bezpośrednio poniżej tego wykresu. |
| Typy żądań klienta |Wskazuje typ żądań, które zostały wykonane przez klientów HTTP (np. przeglądarek). Ten raport zawiera wykres pierścieniowy, który zapewnia poczucie, jak żądania są obsługiwane. Informacje o przepustowości i ruchu dla każdego typu żądania są wyświetlane poniżej wykresu. |
| Agent użytkownika |Zawiera wykres słupkowy przedstawiający 10 najlepszych agentów użytkowników, którzy za pośrednictwem naszej sieci CDN żądają twoich treści. Zazwyczaj agentem użytkownika jest przeglądarka internetowa, odtwarzacz multimedialny lub przeglądarka telefonu komórkowego. Statystyki dla 100 najlepszych agentów użytkowników są wyświetlane bezpośrednio poniżej tego wykresu. |
| Kierujących |Zawiera wykres słupkowy przedstawiający 10 najważniejszych osób odsyłających do treści dostępnych za pośrednictwem naszej sieci CDN. Zazwyczaj strona odsyłacza jest adresem URL strony internetowej lub zasobu, który łączy się z zawartością. Szczegółowe informacje znajdują się poniżej wykresu dla 100 najlepszych osób odsyłaczy. |
| Typy kompresji |Zawiera wykres pierścieniowy, który dzieli żądane zasoby według tego, czy zostały skompresowane przez nasze serwery brzegowe. Procent skompresowanych zasobów jest podzielony według typu używanej kompresji. Szczegółowe informacje znajdują się poniżej wykresu dla każdego typu kompresji i stanu. |
| Typy plików |Zawiera wykres słupkowy, który wyświetla 10 najlepszych typów plików, które zostały wymagane za pośrednictwem naszej sieci CDN dla Twojego konta. Na potrzeby tego raportu typ pliku jest definiowany przez rozszerzenie nazwy pliku zasobu i typ nośnika internetowego (np. \[tekst\].html/html \[ \[\],\].htm text/html , .aspx text/html itp.). Szczegółowe informacje znajdują się poniżej wykresu dla 100 najlepszych typów plików. |
| Unikatowe pliki |Zawiera wykres, który kreśli całkowitą liczbę unikatowych zasobów, które zostały żądane w określonym dniu w określonym czasie. |
| Podsumowanie umajne tokenu |Zawiera wykres kołowy, który zawiera szybki przegląd tego, czy żądane zasoby były chronione przez uwierzytelnianie oparte na tokenie. Zasoby chronione są wyświetlane na wykresie zgodnie z wynikami ich próby uwierzytelnienia. |
| Szczegóły odmowy tokenu Auth |Zawiera wykres słupkowy, który umożliwia wyświetlanie 10 pierwszych żądań, które zostały odrzucone z powodu uwierzytelniania opartego na tokenie. |
| Kody odpowiedzi HTTP |Zawiera podział kodów stanu HTTP (np. 200 OK, 403 Zabronione, 404 Nie znaleziono, itp.), które zostały dostarczone do klientów HTTP przez nasze serwery brzegowe. Wykres kołowy pozwala szybko ocenić, w jaki sposób zasoby zostały wyświetlone. Szczegółowe dane statystyczne są dostarczane dla każdego kodu odpowiedzi poniżej wykresu. |
| 404 Błędy |Zawiera wykres słupkowy, który umożliwia wyświetlanie 10 pierwszych żądań, które spowodowały kod odpowiedzi 404 Nie znaleziono. |
| 403 Błędy |Zawiera wykres słupkowy, który umożliwia wyświetlanie 10 pierwszych żądań, które spowodowały kod odpowiedzi 403 Zabronione. Kod zakazanej odpowiedzi 403 występuje, gdy żądanie zostanie odrzucone przez serwer pochodzenia klienta lub serwer brzegowy w naszym pop. |
| 4xx Błędy |Zawiera wykres słupkowy, który umożliwia wyświetlanie 10 pierwszych żądań, które spowodowały kod odpowiedzi w zakresie 400. Wyłączone z tego raportu są 403 Nie znaleziono i 404 Zakazane kody odpowiedzi. Zazwyczaj kod odpowiedzi 4xx występuje, gdy żądanie zostanie odrzucone w wyniku błędu klienta. |
| 504 Błędy |Zawiera wykres słupkowy, który umożliwia wyświetlanie 10 pierwszych żądań, które spowodowały kod odpowiedzi limitu czasu bramy 504. Kod odpowiedzi limitu czasu bramy 504 występuje, gdy wystąpi limit czasu, gdy serwer proxy HTTP próbuje komunikować się z innym serwerem. W przypadku naszej sieci CDN kod odpowiedzi limitu czasu bramy 504 zazwyczaj występuje, gdy serwer brzegowy nie może nawiązać komunikacji z serwerem pochodzenia klienta. |
| 502 Błędy |Zawiera wykres słupkowy, który umożliwia wyświetlanie 10 pierwszych żądań, które spowodowały kod odpowiedzi 502 Bad Gateway. Kod odpowiedzi bad gateway 502 występuje, gdy wystąpi błąd protokołu HTTP między serwerem a serwerem proxy HTTP. W przypadku naszej sieci CDN kod odpowiedzi 502 Bad Gateway zazwyczaj występuje, gdy serwer pochodzenia klienta zwraca nieprawidłową odpowiedź na serwer brzegowy. Odpowiedź jest nieprawidłowa, jeśli nie może być przeanalizowana lub jeśli jest niekompletna. |
| 5xx Błędy |Zawiera wykres słupkowy, który umożliwia wyświetlanie 10 pierwszych żądań, które spowodowały kod odpowiedzi w zakresie 500.  Z tego raportu wyłączone są kody odpowiedzi 502 Bad Gateway i 504 Gateway Timeout. |

## <a name="see-also"></a>Zobacz też
* [Omówienie usługi Azure CDN](cdn-overview.md)
* [Statystyki w czasie rzeczywistym w sieci CDN platformy Microsoft Azure](cdn-real-time-stats.md)
* [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-rules-engine.md)
* [Zaawansowane raporty HTTP](cdn-advanced-http-reports.md)

