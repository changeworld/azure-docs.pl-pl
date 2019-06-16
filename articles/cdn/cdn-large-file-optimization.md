---
title: Optymalizacja pobierania dużych plików za pomocą usługi Azure CDN
description: W tym artykule wyjaśniono, jak duże pliki, które mogą być optymalizowane pliki do pobrania.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: 9793348b47763e6de10992b9a8a4606fc532cc4d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60636755"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optymalizacja pobierania dużych plików za pomocą usługi Azure CDN

Rozmiary plików zawartości dostarczane za pośrednictwem Internetu bez przerwy Rozwijaj rozszerzoną funkcjonalność, ulepszone grafiki i zawartości multimedialnej. Ten wzrost jest wymuszany przez wiele czynników: penetracji komórkowej łączności szerokopasmowej, większych urządzeniach niedrogiego magazynu, powszechne wzrost o wysokiej rozdzielczości wideo i połączonych z Internetem urządzeń (IoT). Mechanizm szybkie i wydajne dostarczanie dużych plików krytyczne jest zapewnienie środowiska płynne i przyjemne konsumenta.

Dostarczanie dużych plików ma kilka kwestii. Po pierwsze Średni czas pobierania dużych plików mogą być znaczące, ponieważ aplikacje nie mogą pobierać wszystkie dane sekwencyjnie. W niektórych przypadkach aplikacje mogą pobierać ostatnią część pliku przed pierwszą część. Wymagane są tylko niewielką ilość pliku lub zatrzymaniu dostępny do pobrania, pliki do pobrania może zakończyć się niepowodzeniem. Pobieranie również może zostać opóźnione do po sieć dostarczania zawartości (CDN) pobiera cały plik z serwera pochodzenia. 

Po drugie opóźnienie między komputerem użytkownika i plik Określa szybkość jaką mogą wyświetlać zawartość. Ponadto problemów przeciążenia i pojemność sieci również wpływać na przepływności. Większa odległość między serwerami i użytkownikom tworzyć dodatkowe możliwości utraty pakietów, wystąpią, co zmniejsza jakości. Obniżenie jakości spowodowane ograniczona przepływność i utraty pakietów zwiększone wydłużyć czas oczekiwania do pobierania plików zakończyć. 

Po trzecie liczba dużych plików nie zostały jeszcze dostarczone w całości. Użytkownicy mogą anulować pobieranie w połowie lub Obejrzyj tylko pierwszych kilku minut długich wideo MP4. W związku z tym oprogramowania i dostarczania firmom multimedialnym chcesz dostarczyć tylko część pliku, które są wymagane. Wydajne rozmieszczenie żądanej części zmniejsza ruch wychodzący z serwera pochodzenia. Wydajnej dystrybucji zmniejsza pamięci i wykorzystanie we/wy na serwerze źródłowym. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optymalizacja pod kątem dostarczanie dużych plików o wysokiej dostępności treści Azure firmy Microsoft

**Usługa Azure CDN Standard from Microsoft** punktów końcowych dostarczanie dużych plików bez limitu rozmiaru pliku. Dodatkowe funkcje są włączone domyślnie, aby przyspieszyć dostarczanie dużych plików.

### <a name="object-chunking"></a>Obiekt segmentu 

**Usługa Azure CDN Standard from Microsoft** wykorzystuje technikę o nazwie obiekt segmentu. Jeśli wymagane są duże pliki, sieci CDN pobiera mniejsze kawałki pliku ze źródła. Po serwer protokołu POP CDN odbiera żądanie pliku full lub zakres bajtów, serwer krawędzi sieci CDN żąda pliku z pochodzenia we fragmentach po 8 MB. 

Po odebraniu fragmentów na krawędzi sieci CDN ma pamięci podręcznej i obsłużonych natychmiast użytkownika. Usługa CDN wstępnie wyłapuje następnie dalej fragmentów w sposób równoległy. To pobieranie z wyprzedzeniem gwarantuje, że zawartość pozostaje w jednym fragmencie w przód od użytkownika, co zmniejsza opóźnienie. Ten proces jest kontynuowany aż do całego pobrany plik (jeśli jest to wymagane), wszystkich zakresów bajtów są dostępne (jeśli jest to wymagane), lub klient zakończy połączenie. 

Aby uzyskać więcej informacji na temat żądania zakresu bajtów, zobacz [RFC 7233](https://tools.ietf.org/html/rfc7233).

Po odebraniu usługa CDN buforuje wszelkie fragmenty. Cały plik nie musi być buforowane w pamięci podręcznej usługi CDN. Kolejne żądania dla pliku lub bajt zakresy są udostępniane z pamięci podręcznej usługi CDN. Jeśli nie wszystkie fragmenty są buforowane w usłudze CDN, pobieranie z wyprzedzeniem jest używany do zażądania fragmentów ze źródła. Tego rodzaju optymalizacji opiera się na serwerze źródłowym możliwość obsługi żądania zakresu bajtów; Jeśli serwer pochodzenia nie obsługuje żądania zakresu bajtów, optymalizacja nie jest skuteczna. 

### <a name="conditions-for-large-file-optimization"></a>Warunki Optymalizacja dużych plików
Optymalizacja dużych plików funkcji dla **Azure CDN Standard from Microsoft** są domyślnie włączone, korzystając z typu optymalizacji dostarczania ogólnych sieci web. Nie ma ograniczeń na maksymalny rozmiar pliku.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optymalizacja pod kątem dostarczanie dużych plików za pomocą usługi Azure CDN from Verizon

**Usługa Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon** punktów końcowych dostarczanie dużych plików bez limitu rozmiaru pliku. Dodatkowe funkcje są włączone domyślnie, aby przyspieszyć dostarczanie dużych plików.

### <a name="complete-cache-fill"></a>Wypełnienie pamięci podręcznej ukończone

Funkcja wypełnienie pamięci podręcznej ukończone domyślne umożliwia pobierania pliku w pamięci podręcznej, gdy wstępne żądanie zostanie porzucone lub utraty w sieci CDN. 

Wypełnienie pamięci podręcznej ukończone jest najbardziej przydatny w przypadku dużych zasobów. Zazwyczaj użytkownicy nie mogą pobierać je od początku do końca. Używają pobierania progresywnego. Domyślne zachowanie wymusza na serwerze granicznym, aby zainicjować pobieranie w tle elementu zawartości z serwera pochodzenia. Później element zawartości znajduje się w lokalnej pamięci podręcznej serwer graniczny. Po zakończeniu pełnego obiektu w pamięci podręcznej, serwer krawędzi spełnia żądania zakresu bajtów do sieci CDN dla obiektu w pamięci podręcznej.

Domyślne zachowanie można wyłączyć za pomocą aparatu reguł w **Azure CDN Premium from Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Równorzędna pamięć podręczna wypełnienia hot zgłoszenia

Domyślne równorzędnej pamięci podręcznej wypełnienia hot zgłoszenia funkcja korzysta z zaawansowanego algorytmu. Używa ona dodatkowe krawędzi pamięci podręcznej serwerów, na podstawie przepustowości i agregacji żąda metryk do spełnienia żądania klienta dla obiektów duże, bardzo popularny. Ta funkcja zapobiega sytuacji, w którym dużą liczbę żądań dodatkowe są wysyłane do serwera pochodzenia użytkownika. 

### <a name="conditions-for-large-file-optimization"></a>Warunki Optymalizacja dużych plików

Optymalizacja dużych plików funkcji dla **Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon** są domyślnie włączone, korzystając z typu optymalizacji dostarczania ogólnych sieci web. Nie ma ograniczeń na maksymalny rozmiar pliku. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optymalizacja pod kątem dostarczanie dużych plików za pomocą usługi Azure CDN Standard from Akamai

**Usługa Azure CDN Standard from Akamai** punktów końcowych profilu oferuje funkcję, która dostarcza dużych plików efektywnie użytkowników na całym świecie na dużą skalę. Ta funkcja zmniejsza opóźnienia, ponieważ redukuje obciążenie serwerów źródłowych.

Funkcja typu optymalizacji plików o dużym rozmiarze włącza optymalizację sieci i konfiguracje dostarczanie dużych plików, szybciej i bardziej elastycznie zmieniać. Ogólne dostarczanie w Internecie przy użyciu **Azure CDN Standard from Akamai** punktów końcowych przechowuje pliki tylko poniżej 1,8 GB i może tunelu (nie pamięci podręcznej) pliki do 150 GB. Optymalizacja dużych plików w pamięci podręcznej plików do 150 GB.

Optymalizacja dużych plików jest efektywne, gdy są spełnione określone warunki. Warunki obejmują, jak działa serwer pochodzenia i rozmiary i typy plików, które są wymagane. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Konfigurowanie punktu końcowego usługi CDN firmy Akamai w celu zoptymalizowania dostarczanie dużych plików

Można skonfigurować usługi **Azure CDN Standard from Akamai** punktu końcowego do optymalizacji dostarczania dla dużych plików za pośrednictwem witryny Azure portal. Aby to zrobić, można użyć interfejsów API REST, ani żadnego z zestawów SDK klienta. Poniższe kroki pokazują proces za pośrednictwem interfejsu witryny Azure portal dla **Azure CDN Standard from Akamai** profilu:

1. Aby dodać nowy punkt końcowy na Akamai **profil CDN** wybierz opcję **punktu końcowego**.

    ![Nowy punkt końcowy](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. W **zoptymalizowane pod kątem** listy rozwijanej wybierz **pobierania dużych plików**.

    ![Optymalizacja dużych plików wybrane](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Po utworzeniu punktu końcowego usługi CDN, ma to zastosowanie Optymalizacja dużych plików, dla wszystkich plików spełniających określone kryteria. W poniższej sekcji opisano tego procesu.

### <a name="object-chunking"></a>Obiekt segmentu 

Optymalizacja dużych plików za pomocą **Azure CDN Standard from Akamai** wykorzystuje technikę o nazwie obiekt segmentu. Jeśli wymagane są duże pliki, sieci CDN pobiera mniejsze kawałki pliku ze źródła. Po serwer protokołu POP CDN odbiera żądanie pliku full lub zakres bajtów, sprawdza, czy typ pliku jest obsługiwany dla tego rodzaju optymalizacji. Sprawdza również, czy typ pliku spełnia wymagania dotyczące rozmiaru pliku. Jeśli rozmiar pliku jest większa niż 10 MB, serwer krawędzi sieci CDN żąda pliku z źródła we fragmentach 2 MB. 

Po odebraniu fragmentów na krawędzi sieci CDN ma pamięci podręcznej i obsłużonych natychmiast użytkownika. Usługa CDN wstępnie wyłapuje następnie dalej fragmentów w sposób równoległy. To pobieranie z wyprzedzeniem gwarantuje, że zawartość pozostaje w jednym fragmencie w przód od użytkownika, co zmniejsza opóźnienie. Ten proces jest kontynuowany aż do całego pobrany plik (jeśli jest to wymagane), wszystkich zakresów bajtów są dostępne (jeśli jest to wymagane), lub klient zakończy połączenie. 

Aby uzyskać więcej informacji na temat żądania zakresu bajtów, zobacz [RFC 7233](https://tools.ietf.org/html/rfc7233).

Po odebraniu usługa CDN buforuje wszelkie fragmenty. Cały plik nie musi być buforowane w pamięci podręcznej usługi CDN. Kolejne żądania dla pliku lub bajt zakresy są udostępniane z pamięci podręcznej usługi CDN. Jeśli nie wszystkie fragmenty są buforowane w usłudze CDN, pobieranie z wyprzedzeniem jest używany do zażądania fragmentów ze źródła. Tego rodzaju optymalizacji opiera się na serwerze źródłowym możliwość obsługi żądania zakresu bajtów; Jeśli serwer pochodzenia nie obsługuje żądania zakresu bajtów, optymalizacja nie jest skuteczna.

### <a name="caching"></a>Buforowanie
Optymalizacja dużych plików korzysta inny domyślny czas wygaśnięcia pamięci podręcznej od ogólne dostarczanie w Internecie. Program rozróżnia buforowanie pozytywne i negatywne buforowanie na podstawie kodów odpowiedzi HTTP. Jeśli serwer pochodzenia określa czas wygaśnięcia przy użyciu nagłówka cache-control lub expires nagłówka w odpowiedzi, sieć CDN honoruje tę wartość. Jeśli punkt początkowy nie określił, plik jest zgodne z warunkami typu i rozmiaru dla tego typu optymalizacji sieci CDN używa wartości domyślnych dla Optymalizacja dużych plików. W przeciwnym razie sieci CDN używa wartości domyślnych dla ogólne dostarczanie w Internecie.


|    | Ogólne sieci web | Optymalizacja dużych plików 
--- | --- | --- 
Pamięć podręczna: Dodatnie <br> HTTP 200, 203, 300, <br> 301, 302 i do 410 | 7 dni |1 dzień  
Pamięć podręczna: Ujemne <br> HTTP 204, 305, 404, <br> i 405 | Brak | 1 sekunda 

### <a name="deal-with-origin-failure"></a>Dotyczy błąd źródła

Długość limit czasu odczytu pochodzenia zwiększa się z dwóch sekund, aby uzyskać ogólne dostarczanie w Internecie do dwóch minut dla typu Optymalizacja dużych plików. Ten wzrost kont większych plików uniknąć przedwczesnej limit czasu połączenia.

Jeśli upłynie limit czasu połączenia, sieć CDN ponawia próbę kilkukrotnie przed wysłaniem błąd "504 — Limit czasu bramy" do klienta. 

### <a name="conditions-for-large-file-optimization"></a>Warunki Optymalizacja dużych plików

Poniższa lista zawiera zbiór kryteria, które należy spełnić w celu Optymalizacja dużych plików:

Warunek | Wartości 
--- | --- 
Obsługiwane typy plików | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, tar, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Minimalny rozmiar pliku | 10 MB 
Maksymalna wielkość pliku | 150 GB 
Właściwości serwera pochodzenia | Musi obsługiwać żądania zakresu bajtów 

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Należy wziąć pod uwagę następujące aspekty dodatkowe dla tego typu optymalizacji:

- Segmentu proces generuje dodatkowych żądań do serwera pochodzenia. Łączna ilość danych dostarczonych ze źródła jest jednak znacznie mniejszy. Segmentu powoduje lepsze charakterystyce buforowania usługi CDN.

- Pamięć i wykorzystanie We/Wy są ograniczone w źródle, ponieważ mniejsze części pliku są dostarczane.

- Dla fragmentów zbuforowana w sieci CDN Brak dodatkowych żądań do źródła, do momentu wygaśnięcia zawartości lub zostanie usunięty z pamięci podręcznej.

- Użytkownicy mogą wprowadzać zakresu żądań do usługi CDN, które są traktowane tak jak każdy plik normalny. Optymalizacja ma zastosowanie tylko wtedy, gdy jest prawidłowy typ pliku i zakres bajtów jest od 10 MB do 150 GB. Jeśli średni rozmiar plików wymagane jest mniejsza niż 10 MB, należy użyć ogólne dostarczanie w Internecie.

