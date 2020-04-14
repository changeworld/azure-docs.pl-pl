---
title: Optymalizacja pobierania dużych plików za pomocą usługi Azure CDN
description: W tym artykule wyjaśniono, jak można zoptymalizować pobieranie dużych plików.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: 28b3c4faf62bcd9f9495810927ece03e2dadc1fc
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260534"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optymalizacja pobierania dużych plików za pomocą usługi Azure CDN

Rozmiary plików zawartości dostarczanej przez Internet nadal rosną dzięki ulepszonej funkcjonalności, ulepszonej grafice i zawartości multimedialnej. Wzrost ten jest napędzany przez wiele czynników: penetrację łączy szerokopasmowych, większe niedrogie urządzenia pamięci masowej, powszechny wzrost wideo w wysokiej rozdzielczości i urządzeń podłączonych do Internetu (IoT). Szybki i wydajny mechanizm dostarczania dużych plików ma kluczowe znaczenie dla zapewnienia płynnego i przyjemnego doświadczenia konsumenta.

Dostarczanie dużych plików ma kilka wyzwań. Po pierwsze, średni czas pobierania dużego pliku może być znaczący, ponieważ aplikacje mogą nie pobierać wszystkich danych sekwencyjnie. W niektórych przypadkach aplikacje mogą pobrać ostatnią część pliku przed pierwszą częścią. Jeśli zażąda się tylko niewielkiej ilości pliku lub użytkownik wstrzyma pobieranie, pobieranie może zakończyć się niepowodzeniem. Pobieranie również może być opóźnione, dopóki sieć dostarczania zawartości (CDN) nie pobierze całego pliku z serwera pochodzenia. 

Po drugie opóźnienie między komputerem użytkownika a plikiem określa szybkość, z jaką mogą wyświetlać zawartość. Ponadto problemy z przeciążeniem sieci i przepustowością mają również wpływ na przepustowość. Większe odległości między serwerami a użytkownikami stwarzają dodatkowe możliwości utraty pakietów, co obniża jakość. Obniżenie jakości spowodowane ograniczoną przepływnością i zwiększoną utratą pakietów może wydłużyć czas oczekiwania na zakończenie pobierania pliku. 

Po trzecie, wiele dużych plików nie są dostarczane w całości. Użytkownicy mogą anulować pobieranie w połowie lub obejrzeć tylko kilka pierwszych minut długiego filmu MP4. W związku z tym firmy dostarczające oprogramowanie i media chcą dostarczyć tylko część pliku, który jest wymagany. Wydajna dystrybucja żądanych części zmniejsza ruch wychodzący z serwera pochodzenia. Wydajna dystrybucja zmniejsza również ciśnienie pamięci i we/wy na serwerze pochodzenia. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optymalizacja pod kątem dostarczania dużych plików za pomocą usługi Azure CDN firmy Microsoft

**Usługa Azure CDN Standard z** punktów końcowych firmy Microsoft dostarcza duże pliki bez ograniczenia rozmiaru pliku. Dodatkowe funkcje są domyślnie włączone, aby przyspieszyć dostarczanie dużych plików.

### <a name="object-chunking"></a>Fragmentowanie obiektów 

**Usługa Azure CDN Standard firmy Microsoft** używa techniki zwanej fragmentami obiektów. Gdy wymagany jest duży plik, sieć CDN pobiera mniejsze fragmenty pliku ze źródła. Po odebraniu przez serwer POP usługi CDN żądania pliku pełnego lub zakresu bajtów serwer brzegowy sieci CDN żąda pliku od początku w fragmentach 8 MB. 

Po fragment dociera do krawędzi sieci CDN, jest buforowane i natychmiast serwowane do użytkownika. CdN następnie wstępnie pobiera następny fragment równolegle. Ten dostęp z wyprzedzeniem zapewnia, że zawartość pozostaje jeden fragment przed użytkownikiem, co zmniejsza opóźnienia. Ten proces jest kontynuowany do momentu pobrania całego pliku (jeśli jest to wymagane), wszystkie zakresy bajtów są dostępne (jeśli jest to wymagane) lub klient zakończy połączenie. 

Aby uzyskać więcej informacji na temat żądania zakresu bajtów, zobacz [RFC 7233](https://tools.ietf.org/html/rfc7233).

CdN buforuje wszystkie fragmenty, ponieważ są one odbierane. Cały plik nie musi być buforowany w pamięci podręcznej sieci CDN. Kolejne żądania dotyczące zakresów plików lub bajtów są obsługiwane z pamięci podręcznej sieci CDN. Jeśli nie wszystkie fragmenty są buforowane w sieci CDN, pobieranie z fragmentów z prefetch jest używany do żądania fragmentów od źródła. Ta optymalizacja opiera się na zdolności serwera pochodzenia do obsługi żądań zakresu bajtów; Jeśli serwer pochodzenia nie obsługuje żądań zakresu bajtów, ta optymalizacja nie jest skuteczna. 

### <a name="conditions-for-large-file-optimization"></a>Warunki optymalizacji dużych plików
Funkcje optymalizacji dużych plików dla **usługi Azure CDN Standard firmy Microsoft** są domyślnie włączone, gdy używasz ogólnego typu optymalizacji dostarczania sieci Web. Nie ma żadnych ograniczeń dotyczących maksymalnego rozmiaru pliku.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optymalizacja pod kątem dostarczania dużych plików za pomocą usługi Azure CDN firmy Verizon

**Usługa Azure CDN Standard firmy Verizon** i **Azure CDN Premium firmy Verizon** dostarcza duże pliki bez limitu rozmiaru pliku. Dodatkowe funkcje są domyślnie włączone, aby przyspieszyć dostarczanie dużych plików.

### <a name="complete-cache-fill"></a>Pełne wypełnienie pamięci podręcznej

Domyślna funkcja wypełniania pełnej pamięci podręcznej umożliwia sieci CDN wyciągnięcie pliku do pamięci podręcznej, gdy początkowe żądanie zostanie porzucone lub utracone. 

Pełne wypełnienie pamięci podręcznej jest najbardziej przydatne w przypadku dużych zasobów. Zazwyczaj użytkownicy nie pobierają ich od początku do końca. Używają progresywnego pobierania. Domyślne zachowanie wymusza serwer krawędzi do zainicjowania pobierania w tle zasobu z serwera pochodzenia. Następnie zasób znajduje się w lokalnej pamięci podręcznej serwera brzegowego. Po pełnym obiektu znajduje się w pamięci podręcznej, serwer brzegowy spełnia żądania zakresu bajtów do sieci CDN dla obiektu buforowanego.

Domyślne zachowanie można wyłączyć za pomocą aparatu reguł w **usłudze Azure CDN Premium firmy Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Pamięć podręczna równorzędnych wypełnianie hot-filing

Domyślna funkcja wypełniania pamięci podręcznej elementów równorzędnych używa zaawansowanego zastrzeżonego algorytmu. Używa dodatkowych serwerów buforowania krawędzi na podstawie przepustowości i agregacji żądań metryki do obsługi żądań klientów dla dużych, bardzo popularnych obiektów. Ta funkcja zapobiega sytuacji, w której duża liczba dodatkowych żądań są wysyłane do serwera pochodzenia użytkownika. 

### <a name="conditions-for-large-file-optimization"></a>Warunki optymalizacji dużych plików

Funkcje optymalizacji dużych plików dla **platformy Azure CDN Standard firmy Verizon** i azure **CDN Premium firmy Verizon** są domyślnie włączone, gdy używasz ogólnego typu optymalizacji dostarczania sieci Web. Nie ma żadnych ograniczeń dotyczących maksymalnego rozmiaru pliku. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optymalizacja pod kątem dostarczania dużych plików za pomocą usługi Azure CDN Standard firmy Akamai

Usługa Azure CDN Standard z punktów końcowych profilu **Akamai** oferuje funkcję, która zapewnia wydajne duże pliki użytkownikom na całym świecie na dużą skalę. Funkcja zmniejsza opóźnienia, ponieważ zmniejsza obciążenie serwerów pochodzenia.

Funkcja optymalizacji dużych plików włącza optymalizacje i konfiguracje sieci, aby dostarczać duże pliki szybciej i bardziej responsywnie. Ogólne dostarczanie sieci Web za pomocą usługi Azure CDN Standard z punktów końcowych **Akamai** buforuje pliki tylko poniżej 1,8 GB i może tunelować (nie buforować) plików o rozmiarze do 150 GB. Optymalizacja dużych plików buforuje pliki o rozmiarze do 150 GB.

Optymalizacja dużych plików jest skuteczna, gdy spełnione są określone warunki. Warunki obejmują sposób działania serwera pochodzenia oraz rozmiary i typy żądanych plików. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Konfigurowanie punktu końcowego Akamai CDN w celu optymalizacji dostarczania dużych plików

Standard usługi Azure CDN można skonfigurować z punktu końcowego **Akamai,** aby zoptymalizować dostarczanie dla dużych plików za pośrednictwem witryny Azure portal. Można również użyć interfejsów API REST lub dowolnego z sdk klienta, aby to zrobić. Następujące kroki pokazują proces za pośrednictwem witryny Azure portal dla **standardu usługi Azure CDN z profilu Akamai:**

1. Aby dodać nowy punkt końcowy, na stronie **profilu sieci CDN** Akamai wybierz pozycję **Punkt końcowy**.

    ![Nowy punkt końcowy](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. Z listy **rozwijanej Zoptymalizowano pod kątem** wyboru **Duże pobieranie plików**.

    ![Wybrana optymalizacja dużych plików](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Po utworzeniu punktu końcowego sieci CDN stosuje się optymalizacje dużych plików dla wszystkich plików, które odpowiadają określonym kryteriom. W poniższej sekcji opisano ten proces.

### <a name="object-chunking"></a>Fragmentowanie obiektów 

Optymalizacja dużych plików za pomocą **standardu Azure CDN standard firmy Akamai** używa techniki zwanej fragmentami obiektów. Gdy wymagany jest duży plik, sieć CDN pobiera mniejsze fragmenty pliku ze źródła. Po odebraniu przez serwer POP usługi CDN żądania pełnego lub zakresu bajtów sprawdza, czy typ pliku jest obsługiwany dla tej optymalizacji. Sprawdza również, czy typ pliku spełnia wymagania dotyczące rozmiaru pliku. Jeśli rozmiar pliku jest większy niż 10 MB, serwer krawędzi sieci CDN żąda pliku od początku w fragmentach 2 MB. 

Po fragment dociera do krawędzi sieci CDN, jest buforowane i natychmiast serwowane do użytkownika. CdN następnie wstępnie pobiera następny fragment równolegle. Ten dostęp z wyprzedzeniem zapewnia, że zawartość pozostaje jeden fragment przed użytkownikiem, co zmniejsza opóźnienia. Ten proces jest kontynuowany do momentu pobrania całego pliku (jeśli jest to wymagane), wszystkie zakresy bajtów są dostępne (jeśli jest to wymagane) lub klient zakończy połączenie. 

Aby uzyskać więcej informacji na temat żądania zakresu bajtów, zobacz [RFC 7233](https://tools.ietf.org/html/rfc7233).

CdN buforuje wszystkie fragmenty, ponieważ są one odbierane. Cały plik nie musi być buforowany w pamięci podręcznej sieci CDN. Kolejne żądania dotyczące zakresów plików lub bajtów są obsługiwane z pamięci podręcznej sieci CDN. Jeśli nie wszystkie fragmenty są buforowane w sieci CDN, pobieranie z fragmentów z prefetch jest używany do żądania fragmentów od źródła. Ta optymalizacja opiera się na zdolności serwera pochodzenia do obsługi żądań zakresu bajtów; Jeśli serwer pochodzenia nie obsługuje żądań zakresu bajtów, ta optymalizacja nie jest skuteczna.

### <a name="caching"></a>Buforowanie
Optymalizacja dużych plików używa różnych domyślnych czasów buforowania wygaśnięcia z ogólnego dostarczania sieci Web. Rozróżnia buforowanie dodatnie i buforowanie ujemne na podstawie kodów odpowiedzi HTTP. Jeśli serwer pochodzenia określa czas wygaśnięcia za pośrednictwem kontroli pamięci podręcznej lub wygasa nagłówka w odpowiedzi, cdn honoruje tę wartość. Jeśli pochodzenie nie określa, a plik jest zgodny z warunkami typu i rozmiaru dla tego typu optymalizacji, sieć CDN używa wartości domyślnych do optymalizacji dużych plików. W przeciwnym razie sieć CDN używa wartości domyślnych dla ogólnego dostarczania w sieci Web.


|    | Ogólna sieć web | Optymalizacja dużych plików 
--- | --- | --- 
Buforowanie: Pozytywne <br> HTTP 200, 203, 300, <br> 301, 302 i 410 | 7 dni |1 dzień  
Buforowanie: Negatywne <br> HTTP 204, 305, 404, <br> i 405 | Brak | 1 sekunda 

### <a name="deal-with-origin-failure"></a>Radzenie sobie z awarią pochodzenia

Długość limitu czasu odczytu pochodzenia zwiększa się z dwóch sekund dla ogólnego dostarczania w sieci Web do dwóch minut dla typu optymalizacji dużych plików. Ten wzrost odpowiada za większe rozmiary plików, aby uniknąć przedwczesnego limitu czasu połączenia.

Po przesiądeniu limitu czasu połączenia, usługa CDN ponawia kilka razy ponawia proces, zanim wyśle do klienta błąd "504 - limit czasu bramy". 

### <a name="conditions-for-large-file-optimization"></a>Warunki optymalizacji dużych plików

W poniższej tabeli wymieniono zestaw kryteriów, które należy spełnić w celu optymalizacji dużych plików:

Warunek | Wartości 
--- | --- 
Obsługiwane typy plików | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, smoła, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Minimalny rozmiar pliku | 10 MB 
Maksymalna wielkość pliku | 150 GB 
Charakterystyka serwera Origin | Musi obsługiwać żądania zakresu bajtów 

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Należy wziąć pod uwagę następujące dodatkowe aspekty dla tego typu optymalizacji:

- Proces tworzenia fragmentów generuje dodatkowe żądania do serwera pochodzenia. Jednak ogólna ilość danych dostarczonych z pochodzenia jest znacznie mniejsza. Fragmentowanie skutkuje lepszą charakterystyką buforowania w sieci CDN.

- Ciśnienie we/wy pamięci i we/wy są zmniejszane w momencie początkowym, ponieważ dostarczane są mniejsze fragmenty pliku.

- W przypadku fragmentów buforowanych w sieci CDN nie ma żadnych dodatkowych żądań do źródła, dopóki zawartość nie wygaśnie lub nie zostaną eksmitowane z pamięci podręcznej.

- Użytkownicy mogą składać żądania zakresu do sieci CDN, które są traktowane jak każdy normalny plik. Optymalizacja ma zastosowanie tylko wtedy, gdy jest to prawidłowy typ pliku, a zakres bajtów wynosi od 10 MB do 150 GB. Jeśli żądany średni rozmiar pliku jest mniejszy niż 10 MB, należy użyć ogólnego dostarczania w sieci Web.

