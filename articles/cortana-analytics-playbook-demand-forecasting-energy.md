---
title: Podręcznik dotyczący szablonu rozwiązań analizy Cortany na prognozowaniu popytu energii
description: Szablon rozwiązania za pomocą pakietu Microsoft Cortana Intelligence, ułatwiające prognozowanie popytu na energię prądu.
services: machine-learning
author: ilanr9
manager: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/24/2016
ms.author: garye
ms.openlocfilehash: 43a75c31c0f094bdcb7008a39140226815bda163
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49390299"
---
# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Podręcznik dotyczący szablonu rozwiązań analizy Cortany na prognozowaniu popytu energii
## <a name="executive-summary"></a>Streszczenie
W ciągu kilku ostatnich lat platforma Internetu rzeczy (IoT), alternatywnych źródeł energii i danych big data ma scalane, tworząc duże możliwości w domenie, narzędzie i energii. W tym samym czasie narzędzie i sektora energetycznego całego przejrzane zużycie spłaszczanie przy użyciu klientów wymagających lepsze sposoby kontrolowania ich wykorzystania energii. W związku z tym narzędzia i firm, inteligentne siatki znajdują się w doskonałe potrzebę wprowadzania innowacji i odnowić samodzielnie. Ponadto wiele siatki zużycia i narzędzie stają się nieaktualne i bardzo kosztowna, obsługa i zarządzanie nimi. W ciągu ostatniego roku zespół pracował nad liczby engagements w domenie energii. Podczas tych engagements wystąpił wiele przypadków, w których narzędzia lub niezależnym dostawcom oprogramowania (niezależnym dostawcom oprogramowania) została wyszukiwania do prognozowania zapotrzebowania na energię w przyszłości. Te prognozy odgrywa ważną rolę w działalności bieżące i przyszłe i stały się podstawą do różnych przypadków użycia. Należą do nich prognozy obciążenia krótkoterminowe i długoterminowe zasilania, handlu, równoważenia obciążenia, optymalizacji siatki itp. Dane big data i zaawansowanych analiz (UR) metody takie jak Machine Learning (uczenia Maszynowego) są enablers klucza do produkcji dokładnych i wiarygodnych prognozy.  

W ten podręcznik umieściliśmy razem działalności biznesowej i analitycznych wskazówki potrzebne do pomyślnego tworzenia aplikacji, a następnie wdrażania zapotrzebowania na energię Prognoza rozwiązania. Te wytyczne proponowane może pomóc programy narzędziowe, naukowcom i inżynierom danych w ustaleniu w pełni zoperacjonalizowanej, oparte na chmurze, prognozowaniu popytu rozwiązań. Dla przedsiębiorstw, które po prostu uruchamiają swoje dane big data i zaawansowanych analiz podróż takie rozwiązanie może reprezentować początkowej inicjatora w ich długoterminowej strategii inteligentne siatki.

> [!TIP]
> Aby pobrać diagramu, który zawiera omówienie architektury tego szablonu, zobacz [architekturę szablonu rozwiązania dotyczącego Cortana Intelligence prognozowaniu popytu energii](cortana-analytics-architecture-demand-forecasting-energy.md).  
> 
> 

## <a name="overview"></a>Przegląd
W tym dokumencie opisano firmy, dane i technicznych aspektów przy użyciu pakietu Cortana Intelligence i w określonej Azure maszyny Learning (AML) do implementacji i wdrażania rozwiązań energii, prognozowanie. Dokument składa się z trzech głównych części:  

1. Poznawanie firmy  
2. Opis danych  
3. Implementacja techniczna

**Poznawanie firmy** części opisano aspekt biznesowej należy zrozumieć i wziąć pod uwagę przed podjęciem decyzji o inwestycji. Wyjaśniono, jak zakwalifikować się problem biznesowy do upewnij się, że Analiza predykcyjna i uczenie maszynowe w rzeczywistości skuteczne i ma zastosowanie. Dodatkowo dokumentu objaśnia podstawy uczenia maszynowego i sposobie ich użycia energii, prognozowanie problemów. Zawiera wymagania wstępne oraz kryteria kwalifikacyjne w przypadku użycia. Pewnych przykładowych przypadków użycia i przypadek biznesowy scenariusze są również udostępniane.

Dane są głównym składnika dla dowolnego rozwiązanie uczenia maszynowego. **Interpretacji danych** części niniejszego dokumentu opisano niektórych ważnych aspektów danych. Przedstawia rodzaj danych, które są potrzebne do energii, prognozowanie wymagania dotyczące jakości danych oraz z jakimi źródłami danych zwykle istnieje. Również wyjaśniają, jak dane pierwotne służy do przygotowywania funkcji danych, które faktycznie dysku część modelowania.

Trzecia część dokumentu opisano **implementacji technicznej** aspektów rozwiązania. Inżynieria funkcji modelowania są podstawą procesie nauki o danych i są w związku z tym są szczegółowo omówione w niektórych. Poruszono w nim koncepcji usług sieci web, które to ważny dla wdrożenia rozwiązania do analizy predykcyjnej w chmurze. Możemy również konturu typową architekturę rozwiązania zoperacjonalizowanej end-to-end.

Ponadto dokument zawiera materiały referencyjne dla używanego w celu uzyskania dodatkowych znajomości domeny i technologii.

Należy pamiętać, że firma Microsoft nie będą obejmować w tym dokumencie procesu głębiej do nauki o danych jest jego matematyczne i technicznych aspektów. Te informacje można znaleźć w [dokumentacji usługi Azure ML](http://azure.microsoft.com/services/machine-learning/) i [blogi](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Docelowi odbiorcy
Docelowymi odbiorcami tego dokumentu jest biznesowych i personelu technicznego, którzy chcieliby uzyskać wiedzę i wiedzę na temat usługi Machine Learning na podstawie rozwiązania oraz jak te są używane przede wszystkim w domenie energii, prognozowanie.

Analitycy danych mogą również skorzystać z odczytu tego dokumentu, aby lepiej zrozumieć wysokiego poziomu procesu, która napędza wdrożenia energii, prognozowanie rozwiązania. W tym kontekście może również służyć do ustanowienia dobry punkt odniesienia i punkt początkowy, aby uzyskać więcej informacji szczegółowych i zaawansowane materiału.

### <a name="industry-trends"></a>Trendów w branży
W ciągu kilku ostatnich lat IoT, alternatywnych źródeł energii i danych big data ma scalane, tworząc duże możliwości w obszarze Narzędzia i energii. W tym samym czasie narzędzia i sektorów całej energii Zauważyliśmy już zużycie spłaszczanie przy użyciu klientów wymagających lepsze sposoby kontrolowania ich wykorzystania energii.

Wiele narzędzi i firmom energetycznym inteligentne zostały Pionierski [inteligentnej sieci](https://en.wikipedia.org/wiki/Smart_grid) , wdrażając numer użytkowania przypadki, które użytkowania dane generowane przez siatki. Wiele zastosowań koncentrują się wokół własnej charakterystyki produkcji energii elektrycznej: nie można zbierane ani specjalnie przechowywane jako magazyn. Tak co jest generowany muszą być używane. Narzędzia, które mają być bardziej wydajną muszą prognozować zużycie energii, po prostu ponieważ, która umożliwi im większy **równoważyć popyt i podaż**, nadmierne użycie nadmierny energii, zapobiegając w ten **zmniejszyć cieplarniane emisji**i kontrolowanie kosztów.

W przypadku kosztów, jest innym ważnym aspektem, który jest cena. Korzystać z nowych możliwości w handlu zasilania między narzędzia w potrzeba **prognozować przyszły popyt i przyszłych ceny energii elektrycznej**. Może to ułatwić firmom określić ich wielkość produkcji.

Użycie słowa "inteligentne", faktycznie nazywamy siatki, który można Dowiedz się, a następnie tworzyć prognozy. Jej przewidywać okresowymi zmianami w zużyciu także **przewiduje tymczasowe stanów przeciążenia i automatycznie Dostosuj go**. Zdalnie regulacji zużycie (za pomocą tych inteligentnych mierników), można obsługiwać zlokalizowane stanów przeciążenia. **Prognozowanie najpierw, a następnie działający**, siatki sprawia, że sam inteligentniejsze wraz z upływem czasu.

W pozostałej części tego dokumentu skupimy się na określonej rodziny przypadki użycia, które obejmują Prognozowanie przyszłości krótko- i długoterminowego zapotrzebowania na energię. Firma Microsoft praca była wykonywana w tych obszarach w ciągu kilku miesięcy i weszły do niektórych wiedzy i umiejętności, które umożliwiają nam w celu uzyskania wyników klasy korporacyjnej w branży. Inne przypadki użycia będzie uwzględnione również w dokumencie w niedalekiej przyszłości.

## <a name="business-understanding"></a>Poznawanie firmy
### <a name="business-goals"></a>Cele biznesowe
**Pokaz energii** celem jest, aby przedstawić typowe analizy predykcyjnej i rozwiązanie, które można wdrożyć w bardzo krótkim przedziale czasu uczenia maszynowego. W szczególności naszej bieżącej koncentruje się na temat włączania rozwiązania prognozowania popytu energii, tak, aby jego wartość biznesową można szybko rzędu milionów dolarów i używane po. Informacje przedstawione w tym podręcznik mogą pomóc klientowi wykonywanie następujących celów:

* Rozwiązanie oparte na krótki czas, aby wartość usługi machine learning
* Możliwość rozszerzenia pilotażu mieć wielkość innych przypadków użycia lub szerszy zakres, w zależności od ich potrzeb firmy
* Szybko uzyskać informacje o produkcie pakietu Cortana Intelligence

Z tych celów, pamiętając element playbook ma na celu dostarczać działalności biznesowej i wiedzy technicznej, która będzie pomocna w osiąganiu tych celów.

### <a name="power-load-and-demand-forecasting"></a>Ładowanie zasilania i prognozowanie popytu
W sektorze energetycznym może istnieć wiele sposobów, w której żądanie Prognozowanie mogą pomóc w rozwiązywaniu problemów biznesowych. W rzeczywistości prognozowaniu popytu jest uznawana za podstawę dla wielu przypadków użycia rdzeni w branży. Ogólnie rzecz biorąc, firma Microsoft należy wziąć pod uwagę dwa typy prognoz popytu na energię: krótko- i długoterminowego. Każdej z nich może służyć do innych celów i korzystanie z innego podejścia. Główną różnicą między tymi dwoma jest prognozowania horizon, co oznacza przedział czasu, w przyszłości, dla którego możemy prognozy.

#### <a name="short-term-load-forecasting"></a>Krótki okres obciążenia prognozowania
W kontekście zapotrzebowania na energię krótki okres ładowania Prognozowanie (STLF) jest zdefiniowany jako zagregowane obciążenie, które jest prognozowanych w niedalekiej przyszłości na różne części siatki (lub siatkę jako całość). W tym kontekście krótkoterminowa jest definiowany jako horyzont czasowy liczbą z zakresu od 1 godziny do 24 godzin. W niektórych przypadkach horizon 48 godzin jest również możliwe. W związku z tym STLF jest bardzo częsty w przypadku użycia operacyjnej siatki. Poniżej przedstawiono kilka przykładów STLF na podstawie przypadków użycia:

* Popyt i podaż równoważenia
* Obsługa handlowym zasilania
* Tworzenie rynku (cena ustawienia zasilania)
* Optymalizacja operacyjnej siatki
* [Odpowiedzi na żądanie](https://en.wikipedia.org/wiki/Demand_response)
* Szczytowy Prognozowanie popytu
* Zarządzanie po stronie żądanie
* Równoważenie obciążenia i przeciążenia zapobiegania
* Długoterminowych Prognozowanie obciążenia
* Usterki i wykrywanie anomalii
* Ograniczenie/wyrównywanie szczytu 

STLF model opierają się przede wszystkim na niedalekiej przeszłości (ostatniego dnia lub tygodnia) dane dotyczące zużycia i użyj prognozowanych temperatury jako ważne predykcyjne. Uzyskiwanie temperatury dokładne Prognozowanie na następną godzinę i się do 24 godzin staje się mniejszy żądanie teraz dni. Te modele są mniej podatne na sezonowych wzorców lub trendów długoterminowych zużycia.

Rozwiązania SLTF prawdopodobnie także wygenerować dużą liczbę wywołań prognoz (żądania obsługi) od czasu ich jest wywoływana w systemie godzinowym, a w niektórych przypadkach nawet w przypadku wyższa częstotliwość. Jest także bardzo popularne, aby zobaczyć zagnieżdżenia, gdzie każdego poszczególnych Podstacja lub transformer jest reprezentowany jako modelu autonomicznego, dlatego liczby żądań prognozowania jeszcze większą.

#### <a name="long-term-load-forecasting"></a>Długoterminowych Prognozowanie obciążenia
Celem programu długi okres obciążenia Prognozowanie (LTLF) jest do prognozowania popytu zasilania z horyzont czasowy, począwszy od 1 tygodnia, wiele miesięcy (i w niektórych przypadkach przez liczbę lat). Ten zakres horizon dotyczy przede wszystkim dotyczące planowania i przypadki użycia inwestycji.

Długoterminowe scenariuszy ważne jest zapewnienie wysokiej jakości danych, który obejmuje zakres wielu lat (minimalna 3 lata). Te modele będzie zazwyczaj prowadzenie sezonowości wzorców danych historycznych i korzystają z zewnętrznych predicators takie jak pogody i klimatu wzorców.

Jest ważne wyjaśnić, że im dłuższy jest prognozowania horizon, mniej dokładne może być prognozy. Dlatego ważne jest wygenerować niektóre przedziału ufności wraz z rzeczywistych prognozy, które pozwoliłoby ludzi uwzględnić możliwe zmiany w ich procesu planowania.

Ponieważ scenariusz użycia LTLF przede wszystkim jest planowana, oczekujemy może znacznie niższe woluminy prognozowania (w porównaniu do STLF). Firma Microsoft zwykle widział te przewidywania osadzone w narzędzi wizualizacji, takich jak Excel lub Power Bi i można wywołać jej ręcznie przez użytkownika.

### <a name="short-term-vs-long-term-prediction"></a>Krótki okres programu vs. Długoterminowa prognoz
W poniższej tabeli porównano STLF i LTLF w odniesieniu do najważniejszych atrybuty:

| Atrybut | Prognozowanie obciążenia krótkim okresie czasu | Długoterminowych prognozy obciążenia |
| --- | --- | --- |
| Prognozowanie Horizon |Od 1 godziny do 48 godzin |Od 1 do 6 miesięcy lub więcej |
| Poziom szczegółowości danych |Co godzinę |Co godzinę lub codziennie |
| Typowe przypadki użycia |<ul><li>Żądanie/dostaw równoważenia</li><li>Wybierz godzinę prognozowania</li><li>Odpowiedzi na żądanie</li></ul> |<ul><li>Długoterminowych planowania</li><li>Zasoby siatki planowania</li><li>Planowanie zasobów</li></ul> |
| Typowe czynniki prognostyczne |<ul><li>Dnia lub tygodnia</li><li>godziny dnia</li><li>Temperatura co godzinę</li></ul> |<ul><li>Miesiąc roku</li><li>Dzień miesiąca</li><li>Długoterminowa temperatury i klimatu</li></ul> |
| Zakres danych historycznych |Dwa lub trzy lata, przez które danych |Od pięciu do 10 lat, przez które danych |
| Typowe dokładności |MAPE * 5% lub niższy |MAPE * 25% lub niższy |
| Częstotliwość prognozy |Generowane co godzinę lub co 24 godziny |Generowane po miesięczne, co kwartał lub roczne |

\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) — oznacza średni procent błędów

Jak wynika z tej tabeli, bardzo ważne jest rozróżnienie między krótko- i długoterminowe Prognozowanie scenariuszy, jak te reprezentują potrzebom różnych firm i może mieć innego wdrożenia i wzorców użycia.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Przykład użycia przypadek 1: eSmart systemów — Optymalizacja przeciążenia
Ważną rolę z [inteligentne siatki](https://en.wikipedia.org/wiki/Smart_grid) jest dynamiczne i nieustannie optymalizacji i dostosować w przypadku zmiany wzorców użycia. Krótkookresowych zmian, które głównie są spowodowane przez wahania temperatury mogą mieć wpływ na zużycie energii (*np.*, większe możliwości jest używany dla warunku air lub ogrzewanie). W tym samym czasie zużycia energii jest także wpływ trendów długoterminowych. Mogą one obejmować efekty sezonowości, świąt narodowych, długoterminowego wzrostu użycia i jeszcze economic czynników, takich jak index konsumenta, cena ropa naftowa i PKB.

W tym przypadku użycia [eSmart](http://www.esmartsystems.com/) postanowiła wdrożyć rozwiązanie oparte na chmurze, który umożliwia prognozowanie tendencje sytuacji przeciążenia, w dowolnym danym Podstacja siatki. W szczególności eSmart chciała zidentyfikować podstacje, które mogą mieć przeciążenia w ciągu godziny, więc natychmiastowych akcji może uznać, aby uniknąć lub rozwiązać ten problem.

Dokładne i szybkiego wykonywania prognozowania wymaga wykonania trzech modeli predykcyjnych:

* Długi okres model umożliwia prognozowanie zużycie energii w każdym Podstacja podczas następnego kilka tygodni lub miesięcy
* Model krótkim okresie czasu, który umożliwia prognozowanie sytuacja na danym Podstacja w ciągu następnej godziny
* Model temperatury, który umożliwia prognozowanie przyszłych temperatury za pośrednictwem wielu scenariuszy

Celem długoterminowe modelu jest rank podstacje, ich tendencje do przeciążenia (biorąc pod uwagę ich transmisji moc) podczas następnego tygodnia lub miesiąca. Umożliwia to tworzenie krótką listę podstacje, które będzie służyć jako dane wejściowe dla prognoz krótkoterminowych. Podobnie jak temperatury predykcyjne ważne dla długoterminowego modelu, istnieje konieczność stale przygotowania prognoz temperatury wielu scenariuszy i przekazania ich jako dane wejściowe do długoterminowego modelu. Prognozy krótkoterminowa następnie wywoływana w celu przewidywania, które Podstacja prawdopodobnie przeciążenia w ciągu następnej godziny.

Modele krótko- i długoterminowego są wdrażane indywidualnie dla każdego Podstacja. W związku z tym praktyczne wykonanie tych modeli wymaga rozbudowane aranżacji. Aby uzyskać większą dokładność prognozowania w krótkim czasie, bardziej szczegółowy model jest przeznaczony dla każdej godziny dnia. Te modele są wykonywane co godzinę i zakończy się wykonywanie w ciągu kilku minut wystarczająco dużo czasu na odpowiedź i podejmować prewencyjne działania, w razie potrzeby. Ten zbiór modeli są stale aktualizowane przez okresowego ponownego trenowania przy użyciu najnowszych danych.

Więcej informacji na temat ten przypadek użycia można znaleźć [tutaj](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Użyj kryteria kwalifikacyjne wielkości liter — wymagania wstępne
Głównym pakietu Cortana Intelligence jest zaawansowane możliwości do wdrażania i skalowania rozwiązania przetwarzających uczenia maszynowego. Jest przeznaczony do obsługi tysięcy prognoz, które są wykonywane jednocześnie. Może automatycznie skalować, aby spełnić zmieniające wzorca użycia. Rozwiązanie w związku z tym, koncentruje się na dokładność i wydajność obliczeniową. Na przykład firmę jest zainteresowana produkujących zapotrzebowania na energię dokładnego przewidywania na następną godzinę i dla każdej godziny dnia. Z drugiej strony, jesteśmy zainteresowani mniej udzielenie odpowiedzi na pytanie, dlaczego żądanie przewiduje się, ponieważ jest (w samym modelu zajmie się tym).

W związku z tym jest zdawać sobie sprawę, że nie wszystkie przypadki użycia i problemów biznesowych mogą skutecznie rozwiązany, za pomocą usługi machine learning.

Pakiet Cortana Intelligence i usługi machine learning, może być bardzo skuteczne przy rozwiązywaniu problemu danej firmy, gdy są spełnione poniższe kryteria:

* Jest problem biznesowy w kasie **predykcyjne** charakter. To przykład przypadków użycia predykcyjnej jest prądu, który chcesz przewidzieć power obciążenie Podstacja danego ciągu następnej godziny. Z drugiej strony, analizowanie i klasyfikacja sterowniki historycznych żądanie byłyby **opisowy** w charakterze i w związku z tym mniej stosowane.
* Ma wyraźnego **ścieżkę akcji** należy podjąć po udostępnieniu prognozowania. Na przykład przewidywanie przeciążona Podstacja ciągu następnej godziny może wyzwalać aktywnego akcji zmniejszenie obciążenia, który jest skojarzony z tym Podstacja i dlatego potencjalnie uniemożliwia przeciążenia.
* Reprezentuje przypadek użycia **typowy typ problemu** tak, gdy rozwiązanie go pave sposób rozwiązywania innych podobnych przypadków użycia.
* Klienta można ustawić **cele ilościowe i jakościowe** aby zademonstrować implementacji pomyślne rozwiązanie. Na przykład dobrą ilościowy cel dla prognozy popytu energii będzie od wartości progowej wymaganej dokładności (*np.*, maksymalnie 5% błędu jest dozwolone) lub gdy Prognozowanie Podstacja przeciążenia następnie precyzji (stawka prawdziwie dodatnie) oraz odwołania (stopień prawdziwie dodatnie) powinno być nad wartość progową. Te cele powinny pochodzić od celów biznesowych przez klienta.
* Ma wyraźnego **scenariusza integracji** z firmy biznesowego przepływu pracy. Na przykład prognozy obciążenia Podstacja można zintegrować centrum sterowania siatki, aby umożliwić działania zapobiegania przeciążenia.
* Klient ma gotowych do użycia **danych za pomocą odpowiedniej jakości** obsługuje przypadek użycia (Zobacz więcej w następnej sekcji **dobrej jakości danych**, z tego podręcznika dotyczącego).
* Obejmuje architektury klienta skoncentrowane na danych chmury lub **uczenie maszynowe oparte na chmurze**, w tym uczenie Maszynowe Azure i innych składników pakietu Cortana Intelligence.
* Klient jest gotowy do ustanowienia **przepływ danych typu end to end** tego urządzenia dostarczanie danych w chmurze w sposób ciągły i jest gotowa do **operacjonalizować** rozwiązania.
* Klient jest gotowy do **dedykować zasoby** kto będzie mieć aktywnie uczestniczę w pracach podczas początkowego wdrożenia pilotażowego tak, aby wiedzy i praw własności rozwiązania, które mogą być przesyłane do klientów po pomyślnym zakończeniu.
* Zasób klient powinien zostać **professional wykwalifikowanych danych**, najlepiej wydobywania danych.

Kwalifikacja przypadek użycia, na podstawie kryteriów powyżej może znacznie poprawić odsetka pomyślnych przypadek użycia i ustanowić dobre zdobycie przyczółka do wykonania przypadki użycia w przyszłości.

### <a name="cloud-based-solutions"></a>Rozwiązania w chmurze
Pakiet Cortana Intelligence Suite na platformie Azure to zintegrowane środowisko, które znajdują się w chmurze. Wdrożenie zaawansowane rozwiązanie analityczne w środowisku chmury przechowuje znaczące korzyści dla firm i w tym samym czasie może oznaczać, że duża zmiana dla firmy, nadal Użyj lokalnej rozwiązań IT. W sektorze energetycznym istnieje wyraźna tendencja Stopniowa migracja operacji w chmurze. Ten trend idzie w parze oraz Tworzenie inteligentnych siatki zgodnie z opisem powyżej, w **trendów w branży**. Ponieważ element playbook koncentruje się na rozwiązanie oparte na chmurze w domenie energii, należy w niej przedstawić korzyści oraz inne zagadnienia dotyczące wdrażania rozwiązania w chmurze.

Być może Największą zaletą rozwiązaniem opartym na chmurze jest koszt. Jako rozwiązanie wykorzystuje składniki wdrażane w chmurze, nie ma żadnych kosztów ponoszonych z góry ani KWS (koszt z towarów sprzedanych) składnik kosztów skojarzonych z nim. Oznacza to, że nie ma potrzeby o wartości inwestycji w sprzęt, oprogramowanie i konserwacja IT i dlatego istnieje znaczne zmniejszenie ryzyko biznesowe.

Inną ważną zaletą jest strukturą zgodnie z rzeczywistym użyciem koszt rozwiązań w chmurze. Serwery oparte na chmurze do przetwarzania lub magazynowania można wdrożyć i skalowane na podstawie po prostu jako wymagane. Stanowi to poprawę wydajności koszt rozwiązania oparte na chmurze.

Ponadto nie ma potrzeby inwestowania w konserwacji IT lub rozwoju przyszłych infrastruktury, jak to wszystko jest częścią oferty opartej na chmurze. W tym zakresie pakiet Cortana Intelligence Suite obejmuje najlepszych w klasie usługi i utrzymuje ewoluują jej mapy drogowej. Nowe funkcje, składniki i funkcje są stale wprowadzane i rozwijać.

Dla firmy, która jest uruchamiana jego przejście do chmury zdecydowanie zalecamy podejścia stopniowego implementując mapy drogowej migracji w chmurze. Uważamy, narzędzia i firmy w domenie energii, przypadki użycia, które zostały omówione w tym element playbook reprezentują doskonała okazja do wdrażania pilotażowego rozwiązania do analizy predykcyjnej w chmurze.

#### <a name="business-case-justification-considerations"></a>Zagadnienia biznesowego uzasadnienie wielkości liter
W wielu przypadkach klient może być zainteresowany uzasadnienie biznesowe w przypadku danego zastosowania, w którym rozwiązanie w chmurze i uczenie maszynowe to ważne składniki. Inaczej niż w przypadku rozwiązania lokalnego, w przypadku rozwiązania oparte na chmurze jest minimalny, składnik kosztów ponoszonych z góry i większość elementów kosztów związanych z rzeczywistego użycia. Jeśli chodzi o wdrożenie energii, prognozowanie rozwiązanie na pakiet Cortana Intelligence, wiele usług można zintegrować z jednej wspólnej struktury kosztów. Na przykład bazy danych (*np.*, SQL Azure) mogą być używane do przechowywania danych pierwotnych i następnie dla rzeczywistych prognoz usługi uczenie Maszynowe Azure służy do hostowania usług prognozowania. W tym przykładzie strukturze kosztów może obejmować magazynu i składników transakcyjnych.

Z drugiej strony jeden powinien dysponować dobrą znajomością wartości biznesowej systemu operacyjnego zapotrzebowania na energię Prognozowanie (warunek krótkim lub długim). W rzeczywistości należy do osiągnięcia wartości biznesowej każdego działania prognozy. Na przykład dokładnie Prognozowanie obciążenia zasilania przez następne 24 godziny można zapobiec nadprodukcja lub może pomóc w uniknięciu przeciążenia na siatce i to można określić pod względem oszczędności finansowe, wykonywane codziennie.

Podstawowej formuły do obliczania korzyści finansowe popytu prognozy rozwiązaniem byłoby: ![podstawowej formuły do obliczania korzyści finansowe popytu Prognoza rozwiązania](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Ponieważ pakiet Cortana Intelligence Suite oferuje zgodnie z rzeczywistym użyciem modelu cen, nie ma potrzeby dla są kosztu stałego składnika na następującą formułę. Ta formuła oblicza się na podstawie miesięcznej lub rocznej codziennie.

Bieżący pakiet Cortana Intelligence Suite i planów cenowych usługi uczenie Maszynowe Azure można znaleźć [tutaj](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Proces projektowania rozwiązania
Użyj cyklu rozwoju zapotrzebowania na energię prognozowania, rozwiązanie zazwyczaj polega na 4 fazy, w które udostępnimy technologie chmurowe i usług w pakiecie Cortana Intelligence.

Jest to zilustrowane na poniższym diagramie:

![Cykl inteligentne siatki](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

Następujący akapit w tym artykule opisano proces ten krok 4:

1. **Zbieranie danych** — wszystkie zaawansowane rozwiązanie do analizy na podstawie opiera się na danych (zobacz **interpretacji danych**). W szczególności jeśli chodzi o analizy predykcyjnej i przygotowywać trafniejsze prognozy, Polegamy na stałe, dynamicznych przepływu danych. W przypadku energii prognozowaniu popytu, te dane mogą być pobierane bezpośrednio z inteligentnych mierników lub agregowania się już w lokalnej bazie danych. Możemy również zależy od innych zewnętrznych źródeł danych, takich jak pogoda i temperatury. Ten przepływ bieżących danych należy zorganizowanych, planowane i przechowywane. [Usługa Azure Data Factory](http://azure.microsoft.com/services/data-factory/) (ADF) jest naszym głównym najważniejszą metodą roboczą dla wykonania tego zadania.
2. **Modelowanie** — dla prognoz energii dokładnych i wiarygodnych jeden rozwijania (train) i utrzymywania doskonały model, sprawia, że korzystanie z danych historycznych i wyodrębnia zrozumiałe i predykcyjne wzorce w danych. Obszar Machine Learning (ML) ma zostały rośnie szybko za pomocą bardziej zaawansowanych algorytmów rutynowo opracowywane. Usługa Azure ML Studio udostępnia doskonałe środowisko użytkownika, który ułatwia korzystanie z najbardziej zaawansowanych algorytmów uczenia Maszynowego w ramach przepływu pracy nad. Ten przepływ pracy przedstawia intuicyjne diagram przepływu i obejmuje przygotowywania danych, funkcja wyodrębniania, modelowania i ocenę modelu. Użytkownika można ściągać setki różnych modeli, które znajdują się w tym środowisku. Przed zakończeniem tej fazy analitykiem danych mają model roboczy, który jest w pełni ocenione i gotowe do wdrożenia.
   
   Poniższy diagram jest ilustrację Typowy przepływ pracy:
   
   ![Modelowanie przepływu pracy](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)
3. **Wdrożenie** — przy użyciu modelu pracy w kasie, następnym krokiem jest wdrożenie. W tym miejscu modelu jest konwertowana do usługi sieci web, który uwidacznia interfejs API RESTful, który może być wywoływany współbieżnie przez Internet z różnych klientów przez ludzi. Uczenie Maszynowe systemu Azure zapewnia prostą metodę wdrażania modelu bezpośrednio z usługi Azure ML Studio, za pomocą jednego kliknięcia przycisku. Cały proces wdrażania odbywa się pod maską. To rozwiązanie może automatycznie skalować w celu spełnienia wymagane użycie.
4. **Użycie** — na tym etapie firma naprawdę coś korzystanie z modelu prognozowania można używać w celu tworzenia prognoz. Użycie mogą być napędzane z poziomu aplikacji użytkownika (*np.*, pulpitu nawigacyjnego) lub bezpośrednio z systemu operacyjnego, takie jak żądanie/dostaw równoważenia systemu lub rozwiązanie do optymalizacji siatki. Wiele przypadków użycia może opierać się na jednym modelu.

## <a name="data-understanding"></a>Opis danych
Po obejmujących uwagi firmy (zobacz **poznawanie firmy**) rozwiązanie do prognozowania zapotrzebowania na energię, możemy przystąpić do omówienia części danych. Żadne rozwiązanie do analizy predykcyjnej opiera się na danych. Do prognozowania popytu energii Polegamy na dane historyczne użycia za pomocą różnych poziomów szczegółowości. Te dane historyczne jest używana jako surowców. Będzie uczestniczenia w dokładnej analizy, w którym analityk danych będzie identyfikować czynniki prognostyczne (nazywane również funkcji), które można umieścić w modelu, który ostatecznie wygeneruje wymagane prognozy.

W pozostałej części tej sekcji firma Microsoft opisano różne procedury i zagadnienia, które zrozumieć dane i jak przenieść je do użytecznej postaci.

### <a name="the-model-development-cycle"></a>Model cyklu tworzenia oprogramowania
Tworzenie dobrego Prognozowanie modeli wymaga pewnych przygotowań zachować ostrożność i planowania. Potężne procesie modelowania do wielu kroków i koncentrujących się na jeden krok w danym momencie może znacznie zwiększyć wynik całego procesu.

Na poniższym diagramie przedstawiono, jak proces modelowania można podzielić na wiele kroków:

![Model cyklu tworzenia oprogramowania](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Jak widać cykl składa się z sześć kroków:

* Formułowanie problem
* Pozyskiwanie danych i eksploracja danych
* Przygotowywanie danych i inżynieria funkcji
* Modelowanie
* Ocena modelu
* Opracowywanie zawartości

W pozostałej części tej sekcji zostanie opisano poszczególne kroki i elementów do uwzględnienia w każdym kroku.

### <a name="problem-formulation"></a>Formułowanie problem
Firma Microsoft uważa, formułowanie problem jako najbardziej krytycznym kroku, które należy wykonać przed ich wprowadzeniem żadne rozwiązanie do analizy predykcyjnej. W tym miejscu możemy Przekształcanie problem biznesowy i rozłożyć go do określonych elementów, które można rozwiązać przy użyciu danych i technik modelowania. Jest dobrą praktyką sformułować problem jako zestaw pytań prosimy o poświęcenie do odpowiedzi. Poniżej zamieszczono kilka pytań możliwości, które mogą być stosowane w zakresie prognozowaniu popytu energii:

* Co to jest oczekiwane obciążenie poszczególnych Podstacja w następnej godziny lub dni?
* Porę dnia Moje siatki doświadczy szczytowego zapotrzebowania?
* Jakie jest prawdopodobieństwo Moje siatki do obsługi oczekiwanego szczytowego obciążenia?
* Jak dużo mocy elektrowni będzie generował ciągu każdej godziny dnia?

Formułowanie pytania pozwala skupić się na wprowadzenie odpowiednich danych i wdrażania rozwiązania, w pełni wyrównany pod ręką problem biznesowy. Ponadto firma Microsoft następnie można ustawić niektóre kluczowe metryki, które umożliwiają nam do oceny wydajności modelu. Na przykład jak dokładny prognozy należy, i co to jest zakres wystąpienia błędu, które nadal będą akceptowane przez firmę?

### <a name="data-sources"></a>Źródła danych
Nowoczesne inteligentnej sieci zbiera dane z różnych części i składniki siatki. Te dane reprezentuje różne aspekty operacji i użycie siatki zasilania. W zakresie prognozowania zapotrzebowania na energię firma Microsoft są ograniczanie dyskusji na temat źródeł danych, które odzwierciedlają wykorzystanie rzeczywistego zapotrzebowania. Jedno źródło ważne zużycia energii są inteligentnych mierników. Narzędzia na całym świecie szybko wdrażasz inteligentnych mierników dla swoich klientów. Inteligentnych mierników rejestrowania faktyczne zużycie energii i stale przekazywania tych danych do firmy narzędzia. Dane są zbierane i wysłane w stałych interwałach, począwszy od co 5 minut do 1 godziny. Bardziej zaawansowane inteligentnych mierników może być zaprogramowany zdalnie do kontroli i zrównoważenia rzeczywiste użycie w ramach gospodarstwo domowe. Dane z inteligentnych mierników jest stosunkowo niezawodne i zawiera sygnaturę czasową. Dzięki temu ważne składników do prognozowania popytu. Dane licznika może być agregowany (sumowany się) na różnych poziomach w obrębie topologii siatki: transformer, Podstacja, region, *itp*. Firma Microsoft jest następnie wybrać poziom agregacji wymagane do zbudowania modelu prognozowania dla niego. Na przykład prądu chcą prognozowanie przyszłych obciążenia na każdym z jego podstacje siatki następnie wszystkie mierniki danych umożliwia agregowane dla każdego poszczególnych Podstacja i używany jako dane wejściowe dla modelu prognozowania można używać. Nazywamy inteligentnych mierników wewnętrzne źródło danych.

Prognozy popytu niezawodne energii będzie polegają również na innych zewnętrznych źródeł danych. Jednym ważnym czynnikiem, który ma wpływ na zużycie energii jest pogody lub bardziej precyzyjne temperatury. Dane historyczne pokazuje ściśle powiązane między temperatury zewnętrznej i zużycie energii. Dni gorące lato konsumentów należy używać ich klimatyzacja oraz w trakcie Włącz Igrzyska systemów ogrzewania. Wiarygodnego źródła, historyczne temperatur w lokalizacji siatki jest w związku z tym kluczem. Ponadto firma Microsoft polegają również na dokładne prognozę temperatury jako predykcyjne zużycia energii.

Innych zewnętrznych źródeł danych może również pomóc w tworzeniu modeli prognozy popytu energii. Mogą one obejmować zmiany klimatu długoterminowych, ekonomiczne indeksów (*np.*, PKB) i innym osobom. W tym dokumencie firma Microsoft nie będzie zawierać tych innych źródeł danych.

### <a name="data-structure"></a>Struktura danych
Po określeniu źródeł danych wymagane, chcemy upewnić się, że dane pierwotne, które zostały zebrane obejmuje funkcje poprawnych danych. Aby utworzyć modelu prognozy popytu niezawodne, firma Microsoft należałoby upewnij się, że zbierane dane obejmują elementy danych, które mogą pomóc przewidywania przyszłego popytu. Poniżej przedstawiono niektóre podstawowe wymagania dotyczące struktury danych (schemat) danych pierwotnych.

Dane pierwotne składa się z wierszy i kolumn. Każda miara jest przedstawiana jako pojedynczy wiersz danych. Każdy wiersz danych zawiera wiele kolumn (nazywane również funkcje lub pól).

1. **Sygnatura czasowa** — pole znacznika czasu reprezentuje rzeczywisty czas, gdy miara została zarejestrowana. Powinny być zgodne z jednym z typowych formatów daty/godziny. Daty i godziny elementy mają zostać uwzględnione. W większości przypadków nie ma potrzeby czas, który ma zostać zarejestrowany do drugiego poziomu szczegółowości. Należy określić strefę czasową, w którym dane są zapisywane.
2. **Identyfikator licznika** — to pole identyfikuje urządzenie miary lub licznika. Jest zmienną podzielonych na kategorie i może być kombinacją kombinacja cyfr i znaków.
3. **Wartości zużycie** — jest to rzeczywiste użycie w danej daty/godziny. Wykorzystanie można mierzone w kWh (kilowatt-hour) lub inne preferowane jednostki. Należy zauważyć, że jednostka miary musi pozostać spójne we wszystkich miar w danych. W niektórych przypadkach użycia mogą być dostarczane ponad 3 etapy zasilania. W takiej sytuacji firma Microsoft musi zebrać wszystkie fazy niezależnie od użycia.
4. **Temperatura** — temperatura zwykle są zbierane z niezależnie od źródła. Jednakże powinien być zgodny z danych użycia. Powinien on zawierać sygnaturę czasową zgodnie z powyższym opisem, który umożliwi mają być synchronizowane z danymi rzeczywiste zużycie. Wartości temperatury mogą zostać określone w stopniach Celsjusza lub Fahrenheita, ale powinno pozostać spójne we wszystkich miar.
5. **Lokalizacja —** pole lokalizacji są zwykle skojarzone z miejscem, w którym zostały zebrane dane dotyczące temperatury. Może być reprezentowany w postaci liczby kod pocztowy lub w formacie (lat/długa) szerokości/długości geograficznej.

Poniższych tabelach przedstawiono przykłady dobrej zużycia i temperatury format danych:

| **Data** | **czas** | **Identyfikator miernika** | **Faza 1** | **Faza 2** | **Faza 3** |
| --- | --- | --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |ABC1234 |7.0 |2.1 |5.3 |
| 7/1/2015 |10:00:01 |ABC1234 |7.1 |2.2 |4.3 |
| 7/1/2015 |10:00:02 |ABC1234 |6.0 |2.1 |4.0 |

| **Data** | **czas** | **Lokalizacja** | **temperatura** |
| --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |11242 |24.4 |
| 7/1/2015 |10:00:01 |11242 |24.4 |
| 7/1/2015 |10:00:02 |11242 |24.5 |

Jak widać na powyżej, w tym przykładzie zawiera 3 różne wartości zużycie skojarzone z 3 etapów zasilania. Należy również zauważyć, że pól daty i godziny są oddzielone, jednak one mogą być również połączone w jedną kolumnę. W tym przypadku kolumny lokalizacji jest reprezentowany w formacie 5-cyfrowy kod i temperatury w formacie stopni Celsjusza.

### <a name="data-format"></a>Format danych
Pakiet Cortana Intelligence Suite może obsługiwać najbardziej typowych formatów danych w pliku CSV, TSV, JSON, *itp*. Należy pamiętać, że format danych pozostaje zgodny całego cyklu życia projektu.

### <a name="data-ingestion"></a>Wprowadzanie danych
Ponieważ prognozy popytu energii jest stale i często przewidzieć, Upewniamy się, że nieprzetworzone dane przepływają przez proces pozyskiwania stałych i wiarygodnych danych. Proces pozyskiwania musi gwarancji, że danych pierwotnych jest dostępna w procesie prognozowania w wymaganym czasie. Oznacza to, że częstotliwość wprowadzania danych powinna być większa niż częstotliwość prognozowania.

Na przykład: Jeśli żądanie, nasze rozwiązanie do prognozowania powoduje wygenerowanie nowej prognozy o 8:00 codziennie, a następnie należy upewnić się, że zawierają wszystkie dane, które zostały zebrane w ciągu ostatnich 24 godzin ma został w pełni pozyskane do tego punktu i ma nawet z ostatniej godziny  dane.

Aby to osiągnąć, pakiet Cortana Intelligence Suite oferuje różne sposoby obsługi procesu pozyskiwania danych. To spowoduje dalsze omówione w **wdrożenia** części tego dokumentu.

### <a name="data-quality"></a>Jakość danych
Źródła danych pierwotnych, które jest wymagane do wykonania, prognozowaniu popytu niezawodnych i dokładnych muszą spełniać kryteria jakości niektóre podstawowe dane. Chociaż można używać zaawansowane metody statystyczne w celu kompensacji jakiś problem jakości danych, nadal należy upewnić się, że firma Microsoft są wykraczania poza granice niektóre próg jakości danych podstawowych podczas wprowadzania nowych danych. Poniżej przedstawiono kilka istotnych kwestii dotyczących jakości danych pierwotnych:

* **Brak wartości** — odnosi się do sytuacji, gdy określony rozmiar nie zostały zebrane. Podstawowym wymogiem jest, czy brak współczynnik wartość nie powinna być dłuższa niż 10% dowolnego danego okresu. W przypadku pojedynczej wartości nie ma ona należy wskazać przy użyciu wstępnie zdefiniowanych wartości (na przykład: "9999") a nie "0", który może być nieprawidłowa.
* **Dokładności pomiarów** — rzeczywiste wartości zużycie lub temperatury, powinny być dokładnie rejestrowane. Niedokładne miar dadzą prognoz niedokładne. Zazwyczaj błąd miary, powinno być niższe niż 1% w stosunku do wartości true.
* **Czas pomiaru** — jest to wymagane, rzeczywista sygnatura czasowa danych zebranych będzie nie różni się o więcej niż 10 sekund względem true czasu rzeczywistego miary.
* **Synchronizacja** — w przypadku wielu źródeł danych są używane (*np.*, zużycia i temperatury) Upewniamy się, że nie istnieją żadne synchronizacji czasu między nimi. Oznacza to, że różnica czasu między zebranych sygnaturę czasową od dowolnego dwóch niezależnych źródeł danych nie może przekraczać więcej niż 10 sekund.
* **Opóźnienie** — jak wspomniano powyżej, w **pozyskiwanie danych**, firma Microsoft są zależne od procesu przepływ i pozyskiwania danych. Można kontrolować, Upewniamy się, że możemy kontrolować opóźnienia przesyłania danych. To jest określony jako różnica czasu między czasem, która została wykonana pomiar i czas, w którym został załadowany do magazynu pakietu Cortana Intelligence i jest gotowa do użycia. Krótkoterminowa obciążenia Prognozowanie całkowity czas oczekiwania nie powinna być dłuższa niż 30 minut. Obciążenia w długim okresie Prognozowanie całkowity czas oczekiwania nie powinna być dłuższa niż 1 dzień.

### <a name="data-preparation-and-feature-engineering"></a>Przygotowywanie danych i inżynieria funkcji
Gdy dane pierwotne zostały pozyskane (zobacz **pozyskiwanie danych**) i został bezpiecznie przechowywane, jest gotowy do przetworzenia. Zasadniczo biorąc nieprzetworzonych danych i konwersji (Przekształcanie, przekształcanie) etapu przygotowywania danych do formularza w fazie modelowania. Który może zawierać proste operacje, takie jak przy użyciu kolumny danych pierwotnych, tak jak jego rzeczywista zmierzoną wartość, standardowe wartości, bardziej skomplikowane operacje, takie jak [czasu opóźnione](https://en.wikipedia.org/wiki/Lag_operator)i innym osobom. Kolumny danych nowo utworzone są określane jako funkcje związane z danymi, a proces generowania tych nazywa się technicznego opracowywania funkcji. Przed zakończeniem tego procesu firma Microsoft musi nowy zestaw danych, które zostały uzyskane na podstawie nieprzetworzonych danych i może służyć do modelowania. Ponadto trzeba zadbać o brakujących wartości etapu przygotowywania danych (zobacz **dobrej jakości danych**) i je skorygować. W niektórych przypadkach firma Microsoft również potrzebować do normalizacji danych, aby upewnić się, że wszystkie wartości są przedstawione w tej samej skali.

W tej sekcji listę niektórych funkcji wspólnych danych, które są objęte energia modele prognozy popytu.

**Czas oparte na funkcji:** te funkcje są obliczane na podstawie danych Data/sygnatura czasowa. Te zostaną wyodrębnione i przekształcone w kategorii funkcji, takich jak:

* Godzina dnia — jest to godzinę dnia, która przyjmuje wartości od 0 do 23
* Dzień tygodnia — to reprezentuje dzień tygodnia i przyjmuje wartości od 1 (niedziela) do 7 (sobota)
* Dzień miesiąca — to reprezentuje aktualne i może przyjmować wartości z zakresu od 1 do 31
* Miesiąc roku — to przedstawia miesiąc i przyjmuje wartości od 1 (styczeń) do 12 (grudzień)
* Weekend — jest to funkcja wartości binarnej, który przyjmuje wartość 0 dni tygodnia lub 1 for weekend
* Święta — jest to funkcja wartość binarną, która przyjmuje wartości 0 dla regularnych dzień lub 1 dla święta
* Warunki Fouriera — postanowienia Fouriera są wagi, które są uzyskiwane z sygnaturą czasową i są używane do przechwytywania sezonowości (cykle) w danych. Ponieważ firma Microsoft może mieć wiele sezonów w naszych danych Firma Microsoft może być konieczne Fouriera wiele wersji warunków. Na przykład wartości żądanie może być roczne, co tydzień i codzienne sezonów/cykle co będzie skutkowało 3 Fouriera warunków.

**Funkcje niezależne pomiarów:** niezależne funkcje obejmują wszystkie elementy danych, które firma Microsoft ma być używana jako zmienne predykcyjne w naszym modelu. W tym miejscu możemy wykluczyć zależnej funkcji, którego potrzebujemy do prognozowania.

* Funkcja Lag — są to czas przesunięte wartości rzeczywistego zapotrzebowania. Na przykład funkcji lag 1 będzie przechowywać wartość żądanie w poprzedniej godziny (przy założeniu danych co godzinę) względem bieżącą sygnaturę czasową. Podobnie, możemy dodać opóźnienie 2, funkcja lag 3, *itp*. Rzeczywiste kombinacja funkcji lag, które są używane są określane w fazie modelowania przez ocenę wyników modelu.
* Długoterminowych trendów — ta funkcja reprezentuje liniowy wzrost popytu między latami.

**Funkcja zależna:** zależnej funkcji jest kolumny danych, które prosimy o poświęcenie nasz model do przewidywania. Za pomocą [uczenia maszynowego w trybie nadzorowanym](https://en.wikipedia.org/wiki/Supervised_learning), musimy najpierw nauczenia modelu używając funkcji zależnych (określanych również jako etykiety). Dzięki temu modelu dowiedzieć się więcej wzorców w danych skojarzonego z funkcją zależnych. W prognozowania zapotrzebowania na energię zazwyczaj chcemy przewidzieć rzeczywistego zapotrzebowania, a w związku z tym firma Microsoft będzie używać go jako zależnej funkcji.

**Obsługa brakujących wartości:** w fazie przygotowania danych możemy należałoby określić najlepszą strategię obsługi brakujące wartości. Przede wszystkim odbywa się za pomocą różnych statystyczne [metody przypisywania danych](https://en.wikipedia.org/wiki/Imputation_\(statistics\)). W przypadku energii prognozowaniu popytu, firma Microsoft zwykle przypisują brakujące wartości przy użyciu średniej ruchomej z poprzednich punktów danych.

**Normalizacji danych:** normalizacji danych jest innego typu transformacji, który służy do umożliwienia wszystkie dane liczbowe, takich jak prognozowanie popytu podobne skalowania. Zazwyczaj poprawia to z dokładności modelu i dokładność. Firma Microsoft będzie zazwyczaj to robić przez podzielenie wartości rzeczywistej przez zakres danych.
Skaluj będzie oryginalną wartość w dół do mniejszego zakresu, zazwyczaj zakresu od -1 do 1.

## <a name="modeling"></a>Modelowanie
Faza modelowania jest o tym, gdzie odbywa się konwersja danych do modelu. W obszarach podstawowych tego procesu są zaawansowane algorytmy, które skanowanie dane historyczne (dane szkoleniowe), wyodrębnianie wzorców oraz budowanie modelu. Później można tego modelu do prognozowania na nowych danych, który nie został użyty do tworzenia modelu.

Gdy będziemy już mieć model niezawodnej pracy, firma Microsoft może być następnie użyty do oceniać nowe dane, które są skonstruowane do uwzględnienia wymagane funkcje (X). Proces oceny spowoduje, że użytkowania model utrwalonych (obiekt z fazy szkolenia) i przewidywanie zawiera zmienną docelową, która jest oznaczona Ŷ.

### <a name="demand-forecasting-modeling-techniques"></a>Technik modelowania prognozowania popytu
W przypadku prognozowania, udostępnimy popytu korzystanie z danych historycznych, które są uporządkowane według czasu. Firma Microsoft zazwyczaj odnosi się do danych, który zawiera wymiar czasu jako [czas serii](https://en.wikipedia.org/wiki/Time_series). W czasie serii modelowania ma na celu znaleźć czas powiązane trendów, sezonowości, auto korelacji (korelacja wraz z upływem czasu) i sformułować je w modelu.

W ostatnich latach zostały opracowane zaawansowanych algorytmów umożliwiających Prognozowanie serii czasu i poprawić dokładność prognozowania. Krótko omówimy niektóre z nich w tym miejscu.

> [!NOTE]
> W tej sekcji nie jest przeznaczona do użycia jako uczenia maszynowego i prognozowanie Przegląd, ale raczej jako krótką ankietę modelowania technik, które są często używane do prognozowania popytu. Aby uzyskać więcej informacji i materiały edukacyjne o Prognozowanie serii czasu, zdecydowanie zaleca się książki online [Prognozowanie: zasady i praktyki](https://www.otexts.org/book/fpp).
> 
> 

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**MA (średniej ruchomej)**](https://www.otexts.org/fpp/6/2)
Średnia ruchoma to jedna z pierwszym technik analitycznych, używane do prognozowania serii czasu i jest nadal w jednym z najbardziej najczęściej używanych technik dziś. Jest również podstawą dla bardziej zaawansowane techniki prognozowania. Za pomocą średnia krocząca przy średniej za pośrednictwem K najnowsze punkty, gdzie K oznacza kolejność średniej ruchomej firma Microsoft są Prognozowanie następnego punktu danych.

Przenoszenie technika średni efektem wygładzanie prognozy i w związku z tym nie może obsługiwać również dużej zmienności danych.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (Wygładzanie wykładnicze)**](https://www.otexts.org/fpp/7/5)
Wykładniczy Wygładzanie (ETS) to rodzina różnych metod, które używają średnią ważoną ostatnie punkty danych w celu przewidywania następnego punktu danych. Chodzi o to przypisać wagi wyższej wartości nowszą i stopniowo zmniejszać to wagę dla starszych mierzonych wartości. Istnieje kilka różnych metod z tej rodziny, niektóre z nich obejmują obsługę sezonowości w danych, takich jak [metoda sezonowych Holt-Winters](https://www.otexts.org/fpp/7/5).

Niektóre z tych metod również wziąć pod uwagę sezonowości danych.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**ARIMA (Regresja automatycznie zintegrowany średniej ruchomej)**](https://www.otexts.org/fpp/8)
Automatyczne regresji zintegrowane przeniesienie średniej (ARIMA) jest innej rodziny metody, która jest powszechnie używany do prognozowania szeregu czasowego. Łączy praktycznie metody automatycznego regresji o średniej ruchomej. Metody regresji automatycznie obliczyć następny punkt Data używać modele regresji, wykonując poprzedniej wartości serii czasu. Metody ARIMA mają zastosowanie również różnicowych metody, które obejmują obliczenie różnicy między punktami danych i używa ich zamiast oryginalnej zmierzoną wartość. Na koniec ARIMA również sprawia, że użycie przenoszenie średni technik, które zostały omówione powyżej. Kombinacja wszystkie te metody na różne sposoby jest o tym, co tworzy rodziny metody ARIMA.

ETS i ARIMA są obecnie używanym prognozowaniu popytu energii i wiele innych problemów prognozowania. W wielu przypadkach są one łączone ze sobą w celu dostarczenia bardzo dokładne wyniki.

**Ogólne regresja wielokrotna** modele regresji, może być najważniejszych metoda modelowania w domenie, statystyk i uczenia maszynowego. W kontekście Szeregi czasowe będziemy korzystanie z regresji do przewidywania przyszłych wartości (*np.*, żądanie). W regresji możemy zająć liniowej kombinacji czynniki prognostyczne i Dowiedz się wagi (nazywane również współczynniki) te czynniki prognostyczne podczas szkolenia. Celem jest, aby wygenerować regresji, który będzie prognozy naszej przewidywane wartości. Metody regresji są odpowiednie w przypadku, gdy zmienna docelowa jest wartością liczbową i w związku z tym również pasuje do prognozowania serii czasu. Istnieje szereg metod regresji, w tym modele regresji bardzo prosty przykład [regresji liniowej](https://en.wikipedia.org/wiki/Linear_regression) i innych zaawansowanych te, takie jak drzewa decyzyjne [lasów Random](https://en.wikipedia.org/wiki/Random_forest), [Neural Sieci](https://en.wikipedia.org/wiki/Artificial_neural_network)i wzmocnione drzewa decyzyjne.

Konstruowanie zapotrzebowania na energię Prognozowanie jako problem regresji zapewnia dużą elastyczność przy wyborze nasze funkcje danych, które mogą być łączone z rzeczywistego zapotrzebowania szeregami czasowymi i czynniki zewnętrzne, takie jak temperatury. Więcej informacji na temat wybrane funkcje zostały omówione w inżynierii funkcji (zobacz **funkcji inżynieryjnych i przygotowania danych**) sekcji element playbook.

Z naszych doświadczeń wynika z implementacji i wdrożenia pilotażowego prognozy popytu energii, znaleźliśmy, że modele regresji zaawansowanych, które są dostępne w usłudze Azure ML powodowałyby najlepsze wyniki, a firma Microsoft ich użyć.

## <a name="model-evaluation"></a>Ocena modelu
Ocena modelu pełni ważną rolę w ramach **cyklu projektowania modelu**. Na tym etapie spojrzymy na sprawdzanie poprawności modelu i jej wydajności za pomocą rzeczywistych danych. Podczas wykonywania kroku modelowania część dostępnych danych służy do uczenia modelu. W fazie obliczania podejmujemy pozostałe dane do testowania modelu. Praktycznie oznacza, że firma Microsoft wprowadzając nowe dane modelu została restrukturyzacji, która zawiera te same funkcje jako zestaw danych szkoleniowych. Jednak w procesie weryfikacji używamy modelu do prognozowania zawiera zmienną docelową, a nie zapewniają Zmienna docelowa dostępne. Często nazywamy tego procesu jako oceniania modelu. Firma Microsoft następnie używa wartości true docelowych i porównaj je z tych warunków prognozowanych. Celem jest mierzenie i zminimalizować Błąd prognozowania, różnicę między prognoz i wartość true. Kwantyfikacja pomiaru błędów jest klucz, ponieważ chcemy dostosowania modelu, a następnie sprawdź, czy rzeczywiście zmniejsza się błąd. Dostrajanie modelu może odbywać się przez modyfikowanie parametrów modelu, które sterują procesem uczenia lub dodając lub usuwając funkcje związane z danymi (nazywane [czyszczenia parametrów](https://channel9.msdn.com/Blogs/Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Praktycznie oznacza to, że firma Microsoft może być konieczne powtarzać inżynierii cech, modelowanie i modelu fazy oceny wielokrotnie, dopóki nie jesteśmy w stanie zredukować ten błąd do wymaganego poziomu.

Ważne jest, aby wyróżnienia, że błąd prognozowania nigdy nie będą zero jako nigdy nie jest model, który doskonale potrafi prognozować co wynik. Istnieje jednak powstanie błędu, który jest dopuszczalny przez firmę. W procesie weryfikacji, chcielibyśmy upewnij się, że nasz model prognozowania błąd na poziomie lub lepszą niż poziom tolerancji biznesowych. Dlatego jest ważne, aby ustawić poziom dopuszczalna błędu na początku cyklu podczas **formułowanie Problem** fazy.

### <a name="typical-evaluation-techniques"></a>Technik oceny wykonania typowych
Istnieją różne sposoby, w których prognozowania błąd można ustalić i obliczana. W tej sekcji skupimy dyskusji na temat technik oceny wykonania właściwe szeregów czasowych i w określonej do prognozowania zapotrzebowania na energię.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE oznacza oznacza błąd absolutny wartości procentowej. Za pomocą MAPE możemy obliczenia różnicy między prognozowanych każdy punkt a wartością rzeczywistą tego punktu. Następnie możemy pomagająca błąd dla każdego punktu, obliczając część różnica względem wartości rzeczywistej. Na ostatnim etapie możemy średni te wartości. Formuły matematyczne umożliwiający MAPE jest następująca:

![Formuła MAPE](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*gdzie A<sub>t</sub> jest wartością rzeczywistą F<sub>t</sub> jest wartością prognozowaną, a n horizon prognozy.*

## <a name="deployment"></a>Wdrożenie
Gdy będziemy mieć zająć fazy modelowania i sprawdzone wydajnością modeli, które jesteśmy gotowi przejść do fazy wdrożenia. W tym kontekście rozmieszczania oznacza, że umożliwiające klientowi Używanie modelu przez uruchomienie rzeczywiste prognozy na nim w dużej skali. Koncepcji wdrożenia jest klucz w usłudze Azure ML, ponieważ naszym głównym celem jest stale wywołania prognoz, a nie po prostu uzyskiwanie szczegółowe informacje z danych. Faza wdrożenia jest częścią, gdzie umożliwiliśmy modelu, który ma być używane na dużą skalę.

W kontekście prognozy popytu energii naszym celem jest wywołania prognoz ciągłe i okresowe przy jednoczesnym zapewnieniu, że aktualnych danych jest dostępny dla modelu i że prognozowanych dane są wysyłane do klienta odbierająca komunikaty.

### <a name="web-services-deployment"></a>Wdrażanie usługi sieci Web
Główny blok konstrukcyjny możliwych do wdrożenia w usłudze Azure ML to usługa sieci web. Jest to najbardziej skutecznym sposobem, aby umożliwić użycie modelu predykcyjnego w chmurze. Usługa sieci Web hermetyzuje modelu i otacza go za pomocą [zgodne ze specyfikacją REST](http://www.restapitutorial.com/) interfejsu API (Application Programming Interface). Interfejs API może służyć jako część żadnego kodu klienta, jak pokazano na poniższym diagramie.

![Firma Microsoft, wdrożenia i użycia usługi](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Jak widać, usługę sieci web jest wdrażana w chmurze pakiet Cortana Intelligence Suite i może być wywoływany za pośrednictwem jej narażonych punkt końcowy interfejsu API REST. Inny rodzaj klientów w różnych domenach jednocześnie wywołać usługę za pomocą interfejsu API sieci Web. Usługa sieci web również można skalować do obsługi tysięcy równoczesnych wywołań.

### <a name="a-typical-solution-architecture"></a>Architektury typowego rozwiązania
Wdrażając rozwiązanie do prognozowania zapotrzebowania na energię, jesteśmy zainteresowani wdrażanie kompleksowe rozwiązanie, które wykraczają poza usługi internetowej prognozowania i usprawnia cały przepływ danych. W momencie możemy wywołać nowej prognozy firma Microsoft musiałaby upewnij się, że model jest podawany przy użyciu funkcji aktualne dane. Oznacza to, czy nowo zebrane dane pierwotne stale pozyskiwane, przetwarzane i przekształcane do wymaganej funkcji nastavit został zbudowany modelu. W tym samym czasie chcemy udostępnić prognozowanych danych dla elementu end korzystanie z klientów. Na poniższym diagramie przedstawiono przykład danych przepływu cykl (lub potoku danych):

![Zapotrzebowania na energię prognozy przepływ danych typu End to End](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Poniżej przedstawiono kroki, które odbywają się jako część cykl prognozowania popytu energii:

1. Miliony wdrożonych danych liczników stale generują dane zużycia energii w czasie rzeczywistym.
2. Te dane są zbierane i przekazane do repozytorium w chmurze (*np.*, obiektów Blob platformy Azure).
3. Mogła zostać przetworzona, nieprzetworzone dane są agregowane Podstacja lub poziomie regionalnym, zgodnie z definicją przez firmę.
4. Funkcja przetwarzania (zobacz **przygotowania danych i funkcji przetwarzania**) następnie ma miejsce i generuje dane, które są wymagane dla modelu, szkolenia i oceniania — dane zestawu funkcji są przechowywane w bazie danych (*np.*, SQL Azure).
5. Ponowne szkolenie usługa jest wywoływana do ponownego szkolenia modelu prognozowania można używać — tym zaktualizowanej wersji modelu jest trwałe, dlatego może służyć przez usługi internetowej przyznawania ocen.
6. Usługi internetowej przyznawania ocen jest wywoływana, zgodnie z harmonogramem, która pasuje do wymaganą częstotliwość prognozy.
7. Przewidywane dane są przechowywane w bazie danych, które są dostępne dla klienta użycie zakończenia.
8. Klienta zużycie pobiera prognoz, stosuje je do siatki i wykorzystuje je zgodnie z przypadek użycia wymagane.

Należy pamiętać, że cały cykl jest w pełni zautomatyzowane i działa zgodnie z harmonogramem. Organizowanie cały cykl danych może odbywać się przy użyciu narzędzia, takiego jak [usługi Azure Data Factory](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Architektura wdrożenia typu end to End
W celu wdrożenia praktycznie energii żądanie prognozy rozwiązanie na pakiet Cortana Intelligence, musimy upewnić się, że wymagane składniki są opracowywane i poprawnie skonfigurowane.

Na poniższym diagramie przedstawiono typową architekturę pakietu Cortana Intelligence, na podstawie, które implementuje i organizuje cykl przepływu danych, który został opisany powyżej:

![Architektura wdrożenia typu end to End](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Aby uzyskać więcej informacji dotyczących poszczególnych składników i architektury całego zapoznaj się z szablonu rozwiązania energetycznego.

