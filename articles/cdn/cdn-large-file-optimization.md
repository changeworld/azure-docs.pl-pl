---
title: Optymalizacja pobierania plików o dużym z usługą Azure CDN
description: W tym artykule opisano sposób dużych plików mogą być optymalizowane pliki do pobrania.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: v-deasim
ms.openlocfilehash: 2bdb6bdea7b6180e34458883d026161403e4cb58
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optymalizacja pobierania plików o dużym z usługą Azure CDN

Rozmiary plików zawartości dostarczane za pośrednictwem Internetu będzie się rozrastać z powodu rozszerzoną funkcjonalność, ulepszonemu i zawartość multimedialną. Wzrostu jest wymuszany przez wiele czynników: penetracji komórkowej łączności szerokopasmowej, większe urządzeń niedrogich magazynów, powszechnie zwiększenia wysokiej rozdzielczości wideo i podłączonej do Internetu urządzeń (IoT). Mechanizm dostarczania szybkich i wydajnych dużych plików jest krytyczne zapewnienie obsługi płynne i jest przyjemne konsumenta.

Dostarczanie dużych plików ma kilka problemów. Najpierw Średni czas pobierania dużych plików może być istotne, ponieważ aplikacji nie może pobrać wszystkie dane sekwencyjnie. W niektórych przypadkach aplikacje mogą pobierać ostatnia część pliku przed pierwszej części. Wymagany jest mała ilość pliku lub zatrzymaniu pobieranie, pobierania może zakończyć się niepowodzeniem. Pobieranie również mogą być opóźnione aż do po sieci dostarczania zawartości (CDN) pobiera całego pliku z serwera pochodzenia. 

Po drugie opóźnienia między komputerem użytkownika i plik Określa szybkość, jaką można wyświetlić zawartości. Ponadto sieci przeciążona i pojemności problemów miały wpływ na przepustowość. Większej odległości między serwerami i użytkownicy utworzyć dodatkowe możliwości utraty pakietów, występują, co zmniejsza jakości. Obniżenie jakości spowodowane ograniczonej przepustowości i utraty pakietów zwiększona może wydłużyć czas oczekiwania pobierania plików zakończyć. 

Trzecie wielu dużych plików nie są dostarczane w całości. Użytkownicy mogą anulować pobieranie w połowie lub Obejrzyj tylko pierwszy kilka minut długi wideo MP4. W związku z tym oprogramowania oraz nośnika firm dostarczania chce dostarczyć tylko część pliku, który jest wymagane. Efektywne dystrybucji żądanego porcji zmniejsza ruch wychodzący z serwera pochodzenia. Efektywne dystrybucji zmniejsza wykorzystanie we/wy na serwerze źródłowym i ilość pamięci. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optymalizuj dla dostarczanie dużych plików z usługą Azure CDN z firmy Microsoft

**Azure CDN Standard from Microsoft** punkty końcowe dostarczanie dużych plików bez ograniczenie rozmiaru pliku. Dodatkowe funkcje są włączone domyślnie, aby przyspieszyć dostarczanie dużych plików.

### <a name="object-chunking"></a>Obiekt podziału 

**Azure CDN Standard from Microsoft** stosowana jest metoda o nazwie obiektu podziału. Po zażądaniu dużego pliku CDN pobiera mniejsze części pliku ze źródła. Po serwer protokołu POP CDN odbiera żądanie pliku full lub zakresu bajtów, CDN serwer krawędzi żąda pliku ze źródła w fragmentów 8 MB. 

Po odebraniu fragmentów na brzegu sieci CDN ma w pamięci podręcznej i obsługiwane bezpośrednio do użytkownika. Usługa CDN następnie wstępnie wyłapuje dalej fragmentu równolegle. To pobieranie z wyprzedzeniem gwarantuje, że zawartość pozostaje jednym fragmencie wcześniejsze dla użytkownika, co zmniejsza opóźnienia. Ten proces jest kontynuowany do momentu cały plik jest pobierany (jeśli jest to wymagane), wszystkich zakresów bajtów dostępnych (jeśli jest to wymagane), lub klient zakończy połączenie. 

Aby uzyskać więcej informacji dotyczących żądania zakresu bajtów, zobacz [RFC 7233](https://tools.ietf.org/html/rfc7233).

Po odebraniu sieć CDN ma buforować wszystkie fragmenty. Cały plik nie musi być buforowane w pamięci podręcznej usługi CDN. Kolejne żądania dotyczące plików lub bajtów zakresów są obsługiwane z pamięci podręcznej usługi CDN. Jeśli nie wszystkie fragmenty są buforowane w sieci CDN, pobieranie z wyprzedzeniem służy do żądania fragmentów ze źródła. Tego rodzaju optymalizacji opiera się na serwerze źródłowym możliwość obsługi żądania zakresu bajtów; Jeśli na serwerze źródłowym nie obsługuje żądania zakresu bajtów, optymalizacja nie jest skuteczne. 

### <a name="conditions-for-large-file-optimization"></a>Warunki dla optymalizacji dużych plików
Funkcje optymalizacji dla dużych plików **Azure CDN Standard from Microsoft** są włączone domyślnie, używając typu optymalizacji dostarczania ogólne sieci web. Na maksymalny rozmiar pliku nie ma żadnych ograniczeń.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optymalizuj pod kątem dostarczania dużych plików z usługą Azure CDN from Verizon

**Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon** punkty końcowe dostarczanie dużych plików bez ograniczenie rozmiaru pliku. Dodatkowe funkcje są włączone domyślnie, aby przyspieszyć dostarczanie dużych plików.

### <a name="complete-cache-fill"></a>Wypełnienie pamięci podręcznej ukończone

Funkcja wypełnienia pełną pamięci podręcznej domyślne umożliwia sieci CDN może kopiować plik w pamięci podręcznej podczas żądania początkowego jest porzucone lub utracony. 

Najbardziej przydatny w przypadku dużych zasobów jest wypełnienie pamięci podręcznej ukończone. Zwykle użytkownicy nie mogą pobierać je od początku do końca. Używają pobierania progresywnego. Domyślne zachowanie wymusza serwer graniczny, aby zainicjować pobieranie w tle zasobu z serwera pochodzenia. Później zasób znajduje się w lokalnej pamięci podręcznej serwera granicznego. Po pełnej obiektu w pamięci podręcznej, serwer graniczny spełnia żądania zakresu bajtów do sieci CDN dla obiektu w pamięci podręcznej.

Domyślne zachowanie można wyłączyć za pomocą aparatu reguł w **Azure CDN Premium from Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Równorzędna pamięć podręczna wypełnienia hot zgłoszenia

Funkcja dynamicznego zgłoszenia domyślnych równorzędnej pamięci podręcznej wypełnienia używa zaawansowane algorytmu. Używa dodatkowe krawędzi buforowanie serwery oparte na przepustowość i agregacji żąda metryk do spełnienia żądania klienta dla dużych, bardzo popularny obiektów. Ta funkcja zapobiega sytuacji, w którym dużej liczby dodatkowych żądań są wysyłane do serwera pochodzenia użytkownika. 

### <a name="conditions-for-large-file-optimization"></a>Warunki dla optymalizacji dużych plików

Funkcje optymalizacji dla dużych plików **Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon** są włączone domyślnie, używając typu optymalizacji dostarczania ogólne sieci web. Na maksymalny rozmiar pliku nie ma żadnych ograniczeń. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optymalizuj pod kątem dostarczania dużych plików przy użyciu usługi Azure CDN Standard from Akamai

**Azure CDN Standard from Akamai** punkty końcowe profilu oferują funkcja, która dostarcza dużych plików wydajnie użytkownikom na całym świecie na dużą skalę. Funkcja zmniejsza opóźnienia, ponieważ zmniejsza obciążenie serwerów pochodzenia.

Funkcja typu optymalizacji plików o dużym włącza optymalizacje sieci i konfiguracji, aby dostarczyć dużych plików szybciej i bardziej responsively. Dostarczanie ogólne sieci web z **Azure CDN Standard from Akamai** punkty końcowe przechowuje pliki tylko poniżej 1,8 GB i można tunelu (nie pamięci podręcznej) pliki do 150 GB. Optymalizacja dużych plików w pamięci podręcznej plików do 150 GB.

Optymalizacja dużych plików ma zastosowanie, gdy są spełnione określone warunki. Takie jak sposób działania serwera pochodzenia oraz rozmiarów i typów plików, które są wymagane. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Skonfiguruj punkt końcowy Akamai CDN, aby zoptymalizować dostarczanie dużych plików

Można skonfigurować sieci **Azure CDN Standard from Akamai** punktu końcowego w celu zoptymalizowania dostarczania dla dużych plików za pośrednictwem portalu Azure. Aby to zrobić, można użyć interfejsów API REST lub dowolny z zestawów SDK klienta. W poniższej procedurze pokazano proces za pośrednictwem portalu Azure **Azure CDN Standard from Akamai** profilu:

1. Aby dodać nowy punkt końcowy na Akamai **profilu CDN** wybierz pozycję **punktu końcowego**.

    ![Nowy punkt końcowy](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. W **zoptymalizowane pod kątem** listy rozwijanej wybierz **pobierania plików o dużym**.

    ![Wybrane optymalizacji dużych plików](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Po utworzeniu punktu końcowego CDN ma to zastosowanie optymalizacje dużych plików dla wszystkich plików spełniających określone kryteria. W poniższej sekcji opisano tego procesu.

### <a name="object-chunking"></a>Obiekt podziału 

Optymalizacja plików o dużym przy **Azure CDN Standard from Akamai** stosowana jest metoda o nazwie obiektu podziału. Po zażądaniu dużego pliku CDN pobiera mniejsze części pliku ze źródła. Gdy serwer POP w sieci CDN odbierze żądanie pliku full lub zakresu bajtów, sprawdza, czy typ pliku jest obsługiwany dla tego rodzaju optymalizacji. Sprawdza również, czy typ pliku spełnia wymagania dotyczące rozmiaru pliku. Jeśli rozmiar pliku jest większy niż 10 MB, CDN serwer krawędzi żąda pliku ze źródła w fragmentów 2 MB. 

Po odebraniu fragmentów na brzegu sieci CDN ma w pamięci podręcznej i obsługiwane bezpośrednio do użytkownika. Usługa CDN następnie wstępnie wyłapuje dalej fragmentu równolegle. To pobieranie z wyprzedzeniem gwarantuje, że zawartość pozostaje jednym fragmencie wcześniejsze dla użytkownika, co zmniejsza opóźnienia. Ten proces jest kontynuowany do momentu cały plik jest pobierany (jeśli jest to wymagane), wszystkich zakresów bajtów dostępnych (jeśli jest to wymagane), lub klient zakończy połączenie. 

Aby uzyskać więcej informacji dotyczących żądania zakresu bajtów, zobacz [RFC 7233](https://tools.ietf.org/html/rfc7233).

Po odebraniu sieć CDN ma buforować wszystkie fragmenty. Cały plik nie musi być buforowane w pamięci podręcznej usługi CDN. Kolejne żądania dotyczące plików lub bajtów zakresów są obsługiwane z pamięci podręcznej usługi CDN. Jeśli nie wszystkie fragmenty są buforowane w sieci CDN, pobieranie z wyprzedzeniem służy do żądania fragmentów ze źródła. Tego rodzaju optymalizacji opiera się na serwerze źródłowym możliwość obsługi żądania zakresu bajtów; Jeśli na serwerze źródłowym nie obsługuje żądania zakresu bajtów, optymalizacja nie jest skuteczne.

### <a name="caching"></a>Buforowanie
Optymalizacja dużych plików używa czas buforowania wygaśnięcia różne domyślne od dostarczania ogólne sieci web. Program rozróżnia buforowanie dodatnią i ujemną buforowanie na podstawie kodów odpowiedzi HTTP. Jeśli serwer pochodzenia określa czas wygaśnięcia za pośrednictwem nagłówka cache-control lub wygasa nagłówka w odpowiedzi, sieć CDN będzie honorować tej wartości. Gdy nie określono źródła i plik zgodny typ i rozmiar warunki dla tego typu optymalizacji, CDN używa domyślnych wartości dla optymalizacji dużych plików. W przeciwnym razie CDN używa ustawień domyślnych w celu dostarczania ogólne sieci web.


|    | Ogólne sieci web | Optymalizacja dużych plików 
--- | --- | --- 
Buforowanie: dodatnią <br> HTTP 200, 203, 300, <br> 301, 302 i 410 | 7 dni |1 dzień  
Buforowanie: ujemna <br> HTTP 204, 305, 404, <br> i 405 | Brak | 1 sekunda 

### <a name="deal-with-origin-failure"></a>Postępowania w przypadku niepowodzenia źródła

Długość limitu czasu odczytu pochodzenia zwiększa się z dwóch sekund w celu dostarczania ogólne sieci web do dwóch minut dla typu optymalizacji dużych plików. Liczbę kont większych plików uniknąć przedwczesnej limit czasu połączenia.

Jeśli upłynie limit czasu połączenia, sieć CDN ponowi próbę wiele razy przed wysłaniem ich do klienta błąd "504 — Limit czasu bramy". 

### <a name="conditions-for-large-file-optimization"></a>Warunki dla optymalizacji dużych plików

W poniższej tabeli wymieniono zestaw kryteriów należy spełnić w celu optymalizacji dużych plików:

Warunek | Wartości 
--- | --- 
Obsługiwane typy plików | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, tar, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Minimalny rozmiar pliku | 10 MB 
Maksymalny rozmiar pliku | 150 GB 
Właściwości serwera pochodzenia | Musi obsługiwać żądania zakresu bajtów 

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Należy wziąć pod uwagę następujące aspekty dodatkowych dla tego typu optymalizacji:

- Proces segmentu generuje dodatkowych żądań do serwera pochodzenia. Jednak ogólną ilość danych dostarczonych ze źródła jest znacznie mniejszy. Powoduje segmentu lepsze buforowania właściwości w sieci CDN.

- Pamięci i wykorzystanie we/wy zostały zredukowane w źródle, ponieważ są dostarczane mniejsze części pliku.

- Dla fragmentów w sieci CDN w pamięci podręcznej nie ma żadnych dodatkowych żądań do źródła, dopóki zawartość wygaśnie lub nie zostanie usunięty z pamięci podręcznej.

- Użytkownicy mogą wprowadzać żądania zakresu do sieci CDN, które są traktowane jak normalne pliki. Optymalizacja ma zastosowanie tylko wtedy, gdy jest prawidłowy typ pliku i zakres bajtów jest od 10 MB do 150 GB. Jeśli żądany rozmiar pliku średnia jest mniejsza niż 10 MB, należy użyć dostarczania ogólne sieci web.

