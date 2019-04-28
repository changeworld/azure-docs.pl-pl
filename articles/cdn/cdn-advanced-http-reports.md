---
title: Analizowanie statystyk użycia z usługą Azure CDN, zaawansowane raporty HTTP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć zaawansowane raporty HTTP w usłudze Microsoft Azure CDN. Te raporty zawierają szczegółowe informacje o aktywności sieci CDN.
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
ms.openlocfilehash: c8cb4713e38ca0da610c687325f3810f57da2b26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216155"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analizowanie statystyk użycia z usługą Azure CDN, zaawansowane raporty HTTP
## <a name="overview"></a>Omówienie
W tym dokumencie wyjaśniono, zaawansowanych raportów HTTP w usłudze Microsoft Azure CDN. Te raporty zawierają szczegółowe informacje o aktywności sieci CDN.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Dostęp do zaawansowanych raportów HTTP
1. Z poziomu bloku profil CDN kliknij **Zarządzaj** przycisku.
   
    ![Blok profilu CDN Zarządzanie przycisku](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    Zostanie otwarty w portalu zarządzania usługi CDN.
2. Umieść kursor nad **Analytics** kartę, a następnie umieść kursor nad **zaawansowane raporty HTTP** okno wysuwane.  Kliknij pozycję **HTTP platformy dużych**.
   
    ![Portal zarządzania usługi CDN — menu Zaawansowane raporty](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Opcje raportu są wyświetlane.

## <a name="geography-reports-map-based"></a>Raporty lokalizacji geograficznej (opartą na mapie)
Istnieje pięć raportów, które wykorzystują mapę, aby wskazać, regionów, z których żąda zawartości. Te raporty są mapę świata, mapę Stanów Zjednoczonych, Kanadzie mapy, mapa Europa i Azja i Pacyfik mapy.

Każdy raport na podstawie mapy szereguje geograficzne jednostki (czyli krajów, regionów i prowincje) na podstawie procent trafień, które pochodzą z tego regionu. Ponadto mapy znajduje się na ułatwianiu wizualizacji lokalizacji, z których żąda zawartości. Jest w stanie to zrobić za kodowanie kolorami każdego regionu, zależnie od ilości żądanie doświadczenie w danym regionie. Jaśniejsze Zacieniowane regiony wskazują niższe zapotrzebowanie na zawartości, a ciemniejsze regionów wskazują wyższe poziomy żądanie dotyczące Twojej zawartości.

Szczegółowe informacje ruchu sieciowego i przepustowości dla każdego regionu, znajduje się bezpośrednio poniżej mapy. Dzięki temu do wyświetlania całkowita liczba trafień, procent trafień, łączna ilość danych przesyłanych (w gigabajtach) i wartość procentowa danych przesyłanych w poszczególnych regionach. Wyświetl opis każdego z tych metryk. Na koniec po najechaniu kursorem na region (czyli kraju, stanu lub prowincji), nazwę i procent trafień, które wystąpiły w regionie, pojawi się jako etykietka narzędzia.

Krótki opis znajduje się poniżej dla każdego typu raportu opartą na mapie lokalizacji geograficznej.

| Nazwa raportu | Opis |
| --- | --- |
| Mapa świata |Ten raport służy do wyświetlania na całym świecie żądanie dotyczące Twojej zawartości w sieci CDN. Każdego kraju jest oznaczone kolorami na mapę świata, aby wskazać procent trafień, które pochodzą z tego regionu. |
| Mapa USA |Ten raport służy do wyświetlania żądanie dotyczące Twojej zawartości w sieci CDN w Stanach Zjednoczonych. Każdy stan jest oznaczone kolorami na tej mapie, aby wskazać procent trafień, które pochodzą z tego regionu. |
| Kanada mapy |Ten raport służy do wyświetlania żądanie dotyczące Twojej zawartości w sieci CDN w Kanadzie. Każdy region jest oznaczone kolorami na tej mapie, aby wskazać procent trafień, które pochodzą z tego regionu. |
| Mapa Europa |Ten raport służy do wyświetlania żądanie dotyczące Twojej zawartości w sieci CDN w Europie. Każdego kraju jest oznaczone kolorami na tej mapie, aby wskazać procent trafień, które pochodzą z tego regionu. |
| Azja i Pacyfik, część mapy |Ten raport służy do wyświetlania żądanie dotyczące Twojej zawartości w sieci CDN w Azji. Każdego kraju jest oznaczone kolorami na tej mapie, aby wskazać procent trafień, które pochodzą z tego regionu. |

## <a name="geography-reports-bar-charts"></a>Lokalizacja geograficzna raportów (wykresy słupkowe)
Istnieją dwa dodatkowe raporty zapewniające informacje statystyczne według lokalizacji geograficznej, miasta i pierwszych krajów. Te raporty priorytetyzować miast i kraje, odpowiednio, zgodnie z liczbą trafień, które pochodzą z tych regionów. Podczas generowania ten typ raportu, wykres słupkowy poinformuje 10 pierwszych miast lub krajach, które zażądał zawartości za pośrednictwem określonej platformy. Ten wykres słupkowy pozwala szybko ocenić, regionów, które generują najwyższej liczby żądań dotyczących zawartości.

Po lewej stronie programu graph (oś y) wskazuje, ile razy wystąpił w wybranym regionie. Bezpośrednio pod wykresem (oś x) można znaleźć etykietę dla każdego najważniejsze 10 regionach.

### <a name="using-the-bar-charts"></a>Przy użyciu wykresów słupkowych
* Po umieszczeniu wskaźnika myszy nad paskiem, nazwę i łączna liczba trafień, które wystąpiły w tym regionie będą wyświetlane jako etykietka narzędzia.
* Etykietka narzędzia raportu miasta identyfikuje Miasto jego nazwy, województwa oraz skrót kraju.
* Jeśli nie można określić miasto lub region (czyli stan/prowincję/województwo), z której pochodzi żądanie, następnie go będzie wskazuje, że są nieznane. Jeśli kraj jest nieznany, a następnie dwa znaki zapytania (tj.)?), zostanie wyświetlony.
* Raport może zawierać metryki dla "Europa" lub "Azja/Region." Te elementy nie są przeznaczone do zapewnienia informacji statystycznych na wszystkie adresy IP w tych regionach. Przeciwnie dotyczą wyłącznie żądań pochodzących z adresów IP, które są rozproszone na Europa lub Azja zamiast określonym mieście lub kraju.

Poniżej można wyświetlić danych, który został użyty do wygenerowania wykres słupkowy. Można znaleźć całkowita liczba trafień, procent trafień, ilość danych przesyłanych (w gigabajtach), a wartość procentowa danych zostaną przeniesione do góry 250 regionów. Wyświetl opis każdego z tych metryk.

Dla obu typów raportów poniżej znajduje się krótki opis.

| Nazwa raportu | Opis |
| --- | --- |
| Miasta |Ten raport szereguje miast zgodnie z liczbą trafień, które pochodzą z tego regionu. |
| Kraje |Ten raport szereguje krajów zgodnie z liczbą trafień, które pochodzą z tego regionu. |

## <a name="daily-summary"></a>Codzienne podsumowanie
Raport z podsumowaniem codzienne służy do wyświetlania łączną liczbą trafień i transfery danych za pośrednictwem danej platformy codziennie. Te informacje mogą służyć do szybko rozpoznać wzorców działania usługi CDN. Na przykład ten raport może pomóc wykryć dni doświadczonym nowszego lub mniejsza niż oczekiwanego natężenia ruchu.

Podczas generowania ten typ raportu, wykres słupkowy zapewni visual wskazaniem ilość doświadczonym żądanie specyficzne dla platformy codziennie przedziale czasu objętego raportem. Będzie dokonywać, wyświetlając pasek dla każdego dnia w raporcie. Na przykład wybranie czasu o nazwie "Ostatni tydzień" generuje wykres słupkowy z siedmiu słupkami. Każdy słupek będzie wskazywać łączna liczba trafień doświadczenie w tym dniu.

Po lewej stronie programu graph (oś y) wskazuje, ile razy wystąpił w określonym dniu. Bezpośrednio pod wykresem (oś x), możesz znaleźć etykietę, która wskazuje dzień (Format: YYYY-MM-DD) za każdy dzień zawarte w raporcie.

> [!TIP]
> Po umieszczeniu wskaźnika myszy nad paskiem, łączna liczba trafień, które wystąpiły w tym dniu, będą wyświetlane jako etykietka narzędzia.
> 
> 

Poniżej można wyświetlić danych, który został użyty do wygenerowania wykres słupkowy. Można znaleźć łączna liczba trafień i ilość danych przesyłanych (w gigabajtach) za każdy dzień objętego raportem.

## <a name="by-hour"></a>Za godzinę
Raport za godzinę służy do wyświetlania łączną liczbą trafień i transfery danych za pośrednictwem danej platformy w systemie godzinowym. Te informacje mogą służyć do szybko rozpoznać wzorców działania usługi CDN. Na przykład ten raport może pomóc wykryć okresach czasu w ciągu dnia, które wystąpić wyższej lub niższej niż oczekiwanego natężenia ruchu.

Podczas generowania ten typ raportu, wykres słupkowy zapewni visual wskazaniem ilość żądanie specyficzne dla platformy wystąpił w systemie godzinowym w przedziale czasu objętego raportem. Będzie dokonywać, wyświetlając pasek za każdą godzinę objętego raportem. Na przykład wybranie 24-godzinnym przedziale czasu wygeneruje wykres słupkowy z paskami 24. Każdy słupek będzie wskazywać łączna liczba trafień doświadczenie w trakcie tej godziny.

Po lewej stronie programu graph (oś y) wskazuje, ile razy wystąpił na określoną godzinę. Bezpośrednio pod wykresem (oś x), możesz znaleźć etykietę, która wskazuje, Data/Godzina (Format: YYYY-MM-DD gg: mm) dla każdej godziny uwzględnione w raporcie. Czas jest zgłaszany w formacie 24-godzinnego i jest określony, przy użyciu strefy czasowej UTC/GMT.

> [!TIP]
> Po umieszczeniu wskaźnika myszy nad paskiem, łączna liczba trafień, które wystąpiły w trakcie tej godziny będą wyświetlane jako etykietka narzędzia.
> 
> 

Poniżej można wyświetlić danych, który został użyty do wygenerowania wykres słupkowy. Można znaleźć łączna liczba trafień i ilość danych przesyłanych (w gigabajtach) dla każdej godziny objętego raportem.

## <a name="by-file"></a>Za pomocą pliku
Raport w pliku umożliwia wyświetlenie ilości żądanie, jak i ruchu naliczane za pośrednictwem danej platformy najczęściej żądanych zasobów. Podczas generowania ten typ raportu, wykres słupkowy zostanie wygenerowany na górnym 10 najczęściej żądanych zasobów w określonym przedziale czasu.

> [!NOTE]
> Na potrzeby tego raportu adresy URL CNAME edge są konwertowane na ich równoważne adresy URL usługi CDN. Dzięki temu dokładne tally dla całkowitej liczby trafień skojarzone z elementem zawartości, niezależnie od tego, czy usługi CDN lub Microsoft edge CNAME adres URL używany do żądania.
> 
> 

Po lewej stronie programu graph (oś y) wskazuje liczbę żądań dla każdego zasobu w określonym przedziale czasu. Bezpośrednio pod wykresem (oś x) zawiera etykietę, która wskazuje nazwę pliku dla każdego z 10 najważniejszych żądanych zasobów.

Poniżej można wyświetlić danych, który został użyty do wygenerowania wykres słupkowy. Znajdziesz następujące informacje dotyczące wszystkich najważniejszych 250 żądanych zasobów: ścieżka względna, całkowita liczba trafień, procent trafień, ilość danych przesyłanych (w gigabajtach), a wartość procentowa danych zostaną przeniesione.

## <a name="by-file-detail"></a>Według szczegółów pliku
Raport według szczegółów pliku umożliwia wyświetlenie ilości żądanie, jak i ruchu naliczane za pośrednictwem danej platformy dla określonego zasobu. Na samej górze tego raportu jest opcja pliku szczegółów dla. Ta opcja zawiera listę najczęściej żądanych zasobów na wybranej platformie. Aby wygenerować raport, szczegóły plików, będzie konieczne wybranie żądanego zasobu z poziomu opcji pliku szczegółów dla. Po upływie którego wykres słupkowy wskazuje ilość codzienne zapotrzebowania, które on generowany w określonym przedziale czasu.

Po lewej stronie programu graph (oś y) wskazuje, całkowita liczba żądań, że zasób usługi wystąpił w określonym dniu. Bezpośrednio pod wykresem (oś x), możesz znaleźć etykietę, która wskazuje dzień (Format: YYYY-MM-DD) dla CDN, które zostało zgłoszone żądanie dla elementu zawartości.

Poniżej można wyświetlić danych, który został użyty do wygenerowania wykres słupkowy. Można znaleźć łączna liczba trafień i ilość danych przesyłanych (w gigabajtach) za każdy dzień objętego raportem.

## <a name="by-file-type"></a>Według typu pliku
Raport według typu pliku umożliwia wyświetlenie ilości żądanie, jak i ruchu poniesione przez typ pliku. Podczas generowania ten typ raportu, wykres pierścieniowy wskazuje procent trafień generowane przez najważniejszych typów 10 plików.

> [!TIP]
> Po umieszczeniu wskaźnika myszy nad wycinek wykresu pierścieniowego Internet typ nośnika, typu pliku, będą wyświetlane jako etykietka narzędzia.
> 
> 

Poniżej można wyświetlić danych, który został użyty do wygenerowania wykres pierścieniowy. Można znaleźć typu nośnika rozszerzenia/Internet nazwy pliku, łączna liczba trafień, procent trafień, ilość danych przetransferowanych (w gigabajtach) i wartość procentowa danych przesłanych dla każdego typu pliku górnej 250.

## <a name="by-directory"></a>W katalogu
Raport według Directory służy do wyświetlania ilość żądanie, jak i ruchu naliczane za pośrednictwem danej platformy, dla zawartości z określonego katalogu. Podczas generowania ten typ raportu, wykres słupkowy poinformuje całkowita liczba trafień generowane przez zawartość w górnym 10 katalogów.

### <a name="using-the-bar-chart"></a>Za pomocą wykres słupkowy
* Umieść kursor nad paskiem, aby wyświetlić ścieżkę względną do odpowiedniego katalogu.
* Zawartości przechowywanej w podfolderze katalogu nie są liczone przy obliczaniu żądanie przez katalog. To obliczenie opiera się wyłącznie na liczbę żądań wygenerowanych zawartości przechowywanej w katalogu.
* Na potrzeby tego raportu adresy URL CNAME edge są konwertowane na ich równoważne adresy URL usługi CDN. Dzięki temu dokładne zgadzają się dla wszystkich statystyk skojarzone z elementem zawartości, niezależnie od tego, czy usługi CDN lub Microsoft edge CNAME adres URL używany do żądania.

Po lewej stronie programu graph (oś y) wskazuje, całkowita liczba żądań dotyczących zawartości przechowywanej w katalogach pierwszych 10. Każdy słupek na wykresie reprezentuje katalog. Użyj schemat kodowania kolorami porównywać pasek do katalogu, wymienione w sekcji katalogi pełną 250 pierwszych.

Poniżej można wyświetlić danych, który został użyty do wygenerowania wykres słupkowy. Znajdziesz następujące informacje dotyczące wszystkich katalogów najwyższego 250: ścieżka względna, całkowita liczba trafień, procent trafień, ilość danych przesyłanych (w gigabajtach), a wartość procentowa danych zostaną przeniesione.

## <a name="by-browser"></a>Według przeglądarki
Raport w przeglądarce służy do wyświetlania, przeglądarek, które zostały użyte do żądania zawartości. Podczas generowania ten typ raportu, wykres kołowy wskazuje procent żądań obsługiwanych przez pierwsze 10 przeglądarki.

### <a name="using-the-pie-chart"></a>Za pomocą wykresu kołowego
* Umieść kursor nad wycinek wykresu kołowego, aby wyświetlić nazwę i wersję przeglądarki.
* Na potrzeby tego raportu każda kombinacja unikatowe przeglądarki/wersji jest uznawany za innej przeglądarki.
* Wycinek o nazwie "Other" oznacza odsetek żądań obsługiwanych przez wszystkie przeglądarki i wersji.

Poniżej można wyświetlić danych, który został użyty do wygenerowania wykresu kołowego. Znajdziesz numer typu i wersji przeglądarki, łączna liczba trafień i procent trafień dla wszystkich przeglądarek górnej 250.

## <a name="by-referrer"></a>Według odwołania
Raport według odwołania służy do wyświetlania najczęstsze do zawartości na wybranej platformie. Odwołania wskazuje nazwę hosta, na którym zostało wygenerowane żądanie. Podczas generowania ten typ raportu, wykres słupkowy wskazuje ilość generowanych przez 10 najczęstsze żądanie (czyli trafień).

Po lewej stronie programu graph (oś y) wskazuje, całkowita liczba żądań, że zasób usługi wystąpił dla każdego odwołania. Każdy słupek na wykresie reprezentuje odwołania. Schemat kodowania kolorami umożliwia pasek do odwołania, wymienione w sekcji odwołania 250 pierwszych są zgodne.

Poniżej można wyświetlić danych, który został użyty do wygenerowania wykres słupkowy. Można znaleźć adres URL, łączna liczba trafień i procent trafień wygenerowany na podstawie wszystkich najczęstsze 250.

## <a name="by-download"></a>Do pobrania
Przez Pobierz raport umożliwia analizowanie wzorców pobierania najczęściej żądanej zawartości. Początku raport zawiera wykres słupkowy, że porównuje podjęto próbę pobrania z pobrania zakończone najważniejsze 10 żądanych zasobów. Każdy słupek jest oznaczone kolorami zgodnie z tego, czy jest próba pobrania (niebieski) lub Pobieranie ukończone (zielony).

> [!NOTE]
> Na potrzeby tego raportu adresy URL CNAME edge są konwertowane na ich równoważne adresy URL usługi CDN. Dzięki temu dokładne zgadzają się dla wszystkich statystyk skojarzone z elementem zawartości, niezależnie od tego, czy usługi CDN lub Microsoft edge CNAME adres URL używany do żądania.
> 
> 

Po lewej stronie programu graph (oś y) wskazuje nazwę pliku dla każdego z 10 najważniejszych żądanych zasobów. Bezpośrednio pod wykresem (oś x) można znaleźć etykiety, które wskazuje całkowitą liczbę pobrań podjęto zakończone.

Bezpośrednio poniżej wykres słupkowy następujące informacje zostaną wyświetlone najważniejsze 250 żądanych zasobów: ścieżka względna (łącznie z nazwą pliku), ile razy został on pobrany do zakończenia, ile razy żądano jej i wartość procentowa żądania, które wpłynęły na zakończenie pobierania.

> [!TIP]
> Nasze usługi CDN nie otrzymuje informacji przez klienta protokołu HTTP (np. przeglądarka) po element zawartości został całkowicie pobrany. Co w efekcie mamy do obliczania, czy element zawartości całkowitym pobraniu zgodnie z kodów stanu i zakres bajtów żądania. Pierwszą rzeczą, jaką szukamy podczas wprowadzania tego obliczenia jest, czy żądanie skutkuje kod stanu 200 OK. Jeśli tak, następnie przyjrzymy się żądania zakresu bajtów, aby upewnić się, że dotyczą całej zawartości. Na koniec możemy porównać ilość danych przesyłanych do rozmiaru żądanych zasobów. Jeśli przesyłane dane jest równy lub większy niż rozmiar pliku, a żądania zakresu bajtów są odpowiednie dla tego zasobu, naciśnij klawisz będzie traktowane jak zakończenie pobierania.
> 
> Ze względu na charakter interpretacyjny tego raportu należy mieć na uwadze następujące kwestie, które nie może zmienić spójności dokładności tego raportu.
> 
> * Wzorce ruchu nie mogli do dokładnie przewidzieć podczas agentów użytkownika zachowywać się inaczej. Może to zwrócić wyniki Pobieranie ukończone, które są większe niż 100%.
> * Zasoby, które wykorzystują pobierania progresywnego HTTP nie może być dokładnie reprezentowany przez ten raport. Jest to spowodowane użytkownikom wyszukiwanie w inne miejsca w filmie wideo.
> 
> 

## <a name="by-404-errors"></a>Przez błędy 404
Raport przez błędy 404 umożliwia określenie typu zawartości, która generuje największą liczbę kodów stanu 404 Nie znaleziono. Początku raport zawiera wykres słupkowy 10 najważniejszych zasobów, dla których został zwrócony 404 Nie odnaleziono kodu stanu. Ten wykres słupkowy porównuje całkowita liczba żądań z żądaniami, które spowodowało 404 Nie znaleziono kod stanu dla tych zasobów. Każdy słupek jest oznaczone kolorami. Żółty pasek jest używany do wskazania, że żądanie spowodowało 404 Nie odnaleziono kodu stanu. Czerwony pasek jest używany do wskazania całkowita liczba żądań dla zasobu.

> [!NOTE]
> Na potrzeby tego raportu Pamiętaj o następujących kwestiach:
> 
> * Trafienie reprezentuje każde żądanie dla elementu zawartości, niezależnie od tego, kod stanu.
> * Adresy URL CNAME Edge są konwertowane na ich równoważne adresy URL usługi CDN. Dzięki temu dokładne zgadzają się dla wszystkich statystyk skojarzone z elementem zawartości, niezależnie od tego, czy usługi CDN lub Microsoft edge CNAME adres URL używany do żądania.
> 
> 

Po lewej stronie programu graph (oś y) wskazuje nazwę pliku dla wszystkich najważniejszych żądanych zasobów 10, które wpłynęły na 404 Nie odnaleziono kodu stanu. Bezpośrednio pod wykresem (oś x) można znaleźć etykiety, które wskazują, całkowita liczba żądań i liczbę żądań, które spowodowały 404 Nie odnaleziono kodu stanu.

Bezpośrednio poniżej wykres słupkowy następujące informacje zostaną wyświetlone najważniejsze 250 żądanych zasobów: ścieżka względna (takie jak nazwa pliku), liczba żądań, które spowodowały 404 Nie odnaleziono kodu stanu, całkowita liczba prób zażądano elementu zawartości, i Procent żądań, które spowodowały 404 Nie odnaleziono kodu stanu.

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure CDN](cdn-overview.md)
* [Statystyki w czasie rzeczywistym w usłudze Microsoft Azure CDN](cdn-real-time-stats.md)
* [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-rules-engine.md)
* [Analyze Edge Performance](cdn-edge-performance.md)

