---
title: Cortana Intelligence rozwiązania szablonu Podręcznikowym żądanie prognozowania energii | Dokumentacja firmy Microsoft
description: Szablon rozwiązania z analizy Cortana firmy Microsoft, które pomaga prognozy zapotrzebowania na energię narzędzie firmy.
services: cortana-analytics
documentationcenter: ''
author: ilanr9
manager: ilanr9
editor: yijichen
ms.assetid: 8855dbb9-8543-45b9-b4c6-aa743a04d547
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2016
ms.author: ilanr9;yijichen;garye
ms.openlocfilehash: 275e387878900154660d044b26ff5ac03a17a65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23846430"
---
# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Cortana Intelligence rozwiązania szablonu Podręcznikowym żądanie prognozowania energii
## <a name="executive-summary"></a>Podsumowanie dla kierownictwa
W ciągu ostatnich kilku lat Internetu rzeczy (IoT), alternatywnych źródeł energii i danych big data zostały scalone do tworzenia przeważająca możliwości w domenie narzędzia i energii. W tym samym czasie narzędzia i sektora energetycznego całego jak już wspomniano zużycie spłaszczanie z konsumentami wymagających lepszego sposoby kontrolowania ich użycia energii. W związku z tym narzędzie i firm inteligentne siatki znajdują się w potrzeba innowacji i odnawiania samodzielnie. Ponadto wiele siatki zasilania i narzędzie stają się nieaktualne i bardzo kosztowna Obsługa i zarządzanie nimi. W ciągu ostatniego roku zespół pracuje na liczbie promujących zaangażowanie w obrębie domeny energii. Podczas rejestrowania tych transakcji napotkano wielu przypadkach, w których narzędzia lub niezależnym dostawcom oprogramowania (niezależni dostawcy oprogramowania) zostały wyszukiwania do prognozowania dla zapotrzebowania na energię w przyszłości. Te prognozy odgrywa ważną rolę w bieżących i przyszłych firmy i stały się podstawę dla różnych zastosowań. Należą do zasilania krótko- i długoterminowe obciążenia prognozy, handlu, równoważenia obciążenia, optymalizacji siatki itp. Dane big data i zaawansowane analizy AA metody takie jak Learning maszyny (ML) są kluczowych czynników istnienie do produkcji dokładne i niezawodne prognozy.  

W tym podręcznikowym testujemy razem działalności biznesowej i analitycznego wskazówki potrzebne do pomyślnego tworzenia i wdrażania zapotrzebowania na energię prognozy rozwiązania. Te wytyczne proponowanych może pomóc narzędzia i inżynierów danych w ustalaniu rozwiązań pełni operationalized, oparte na chmurze, funkcja prognozowania żądanie analityków danych. Dla przedsiębiorstw, które właśnie uruchamiają swoje dane big data i przebieg zaawansowane metody analizy takie rozwiązanie może reprezentować początkowej inicjatora w ich długoterminowej strategii inteligentne siatki.

> [!TIP]
> Aby pobrać diagramu, który zawiera omówienie architektury tego szablonu, zobacz [architektura szablon rozwiązania Cortana Intelligence żądanie prognozowania energii](cortana-analytics-architecture-demand-forecasting-energy.md).  
> 
> 

## <a name="overview"></a>Omówienie
W tym dokumencie opisano business, danych i aspektów technicznych przy użyciu Cortana Intelligence i w określonego Azure Machine Learning (AML) implementacji i wdrażania rozwiązań prognozowania energii. Dokument składa się z trzech głównych części:  

1. Poznawanie firmy  
2. Opis danych  
3. Implementacja techniczna

**Opis firm** części omówiono aspekt firm, należy zrozumieć i należy wziąć pod uwagę przed podjęciem decyzji o inwestycji. Wyjaśniono, jak kwalifikują się do upewnij się, że analizy predykcyjnej i uczenia maszynowego są rzeczywiście skuteczne i dotyczy problem biznesowy. Dalsze dokumentu opisano podstawy uczenia maszynowego i sposobie ich użycia w celu rozwiązania problemów prognozowania energii. Go opisano wymagania wstępne i kryteria kwalifikacyjne w przypadku użycia. Niektóre przykładowe Użyj przypadków i w przypadku scenariuszy są również dostępne.

Dane są głównego składnika dla dowolnej maszyny uczenia rozwiązania. **Opis danych** części niniejszego dokumentu opisano niektóre istotne aspekty danych. Rodzaj danych niezbędnych do prognozowania energii, wymagania dotyczące jakości danych i jakie źródła danych zwykle istnieje on przedstawiono. Również zasady, jak dane pierwotne służy do przygotowywania funkcje danych, które faktycznie dysków części modelowania.

Trzeci części dokumentu obejmuje **implementacji technicznej** aspekt rozwiązania. Funkcja inżynieryjne i modelowania mają procesu nauki danych i w związku z tym jest omówiono szczegółowo. Obejmuje ona pojęcia usług sieci web, które to ważny do chmury wdrażania rozwiązań analizy predykcyjnej. Możemy również konspektu typowe architektury rozwiązania operationalized end-to-end.

Dokument zawiera ponadto materiałów referencyjnych, którego można użyć w celu uzyskania dodatkowych wiedzę na temat domeny i technologii.

Należy pamiętać, nie zamierzamy pokrywał się w tym dokumencie procesu głębiej nauki danych jest aspektów matematycznych i technicznych. Te informacje można znaleźć w [dokumentacji usługi Azure ML](http://azure.microsoft.com/services/machine-learning/) i [blogi](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Odbiorcy docelowi
Docelowymi odbiorcami tego dokumentu jest biznesowych i technicznych pracowników, którzy chcą poznanie i wiedzę na temat usługi Machine Learning na podstawie rozwiązania oraz jak te są używane przede wszystkim w domenie prognozowania energii.

Analityków danych można również korzystać z odczytywania tego dokumentu, aby lepiej zrozumieć proces wysokiego poziomu, który dyski wdrożenia energii, funkcja prognozowania rozwiązania. W tym kontekście może również służyć Aby ustalić linię bazową dobrej i punkt początkowy, aby uzyskać więcej szczegółowych i zaawansowane materiału.

### <a name="industry-trends"></a>Trendów w branży
W ciągu ostatnich kilku lat można utworzyć w obszarze Narzędzia i energii przeważająca możliwości mieć scalonego IoT, alternatywnych źródeł energii i danych big data. W tym samym czasie narzędzia i sektorów energii całego przejrzane zużycie spłaszczanie z konsumentami wymagających lepszego sposoby kontrolowania ich użycia energii.

Wiele narzędzia i firm energii inteligentne zostały pioneering [inteligentne siatki](https://en.wikipedia.org/wiki/Smart_grid) przez wdrożenie wielu Użyj przypadków, które należy użyć danych wygenerowanych przez siatkę. Wiele przypadków użycia obracać wokół własnej charakterystyki produkcji energii elektrycznej: nie zebranych ani Odłóż przechowywane jako magazynu. Tak co jest generowany musi być używany. Narzędzia, które mają być bardziej wydajną należy po prostu prognozy zużycie energii ponieważ który da im większe możliwości **saldo i podaży**, wibracjom energii, zapobiegając w ten **zmniejszyć cieplarniane emisji**i kontrolować koszty.

W przypadku kosztów, istnieje inny istotnym elementem, który jest cena. W potrzeba wprowadzenia nowych możliwości zasilania między narzędzia handlu **przewidywania przyszłego zapotrzebowania, przyszłych cen energii elektrycznej**. Może to ułatwić firm ustalić ich wielkości produkcji.

Użycie słowa "inteligentne", firma Microsoft faktycznie odwoływać się do siatki, które można dowiedzieć się, a następnie tworzenia prognoz. Można przewiduje się zmian okresach zużycia oraz **przewidywany jest tymczasowe przeciążenie sytuacji i automatycznie Dostosuj go**. Zdalnie regulacji zużycie (za pomocą tych liczników inteligentne), można obsługiwać przeciążeń zlokalizowane sytuacji. **Przewidywanie najpierw, a następnie działające**, siatki sprawia, że sama inteligentny wraz z upływem czasu.

W pozostałej części tego dokumentu, możemy koncentruje się na określonej rodziny przypadków użycia, które obejmują prognozowania w przyszłości krótko- i długoterminowej zapotrzebowania na energię. Firma Microsoft pracy w tych obszarach przez kilka miesięcy i uzyskały niektórych wiedzy i umiejętności, która pozwala nam w celu uzyskania wyników kategorii branżowych. Inne przypadki użycia zostanie omówiona również w dokumencie w najbliższej przyszłości.

## <a name="business-understanding"></a>Poznawanie firmy
### <a name="business-goals"></a>Cele biznesowe
**Pokaz energii** celem jest, aby zademonstrować typowe analizy predykcyjnej i rozwiązania, które można wdrożyć w krótkim przedziale czasu uczenia maszynowego. W szczególności naszych bieżącego skoncentrować się na włączenie energii żądanie prognozy rozwiązania, aby jego wartości biznesowej można szybko zrealizowane i wykorzystać na. Informacje przedstawione w tym podręcznika dotyczącego mogą pomóc odbiorcy służącymi do następujących celów:

* Rozwiązania na podstawie krótki czas, aby wartość uczenia maszynowego
* Możliwość rozszerzenia pilotażu korzystać w przypadku innych przypadków użycia lub szerszy zakres, w zależności od ich potrzeb firmy
* Szybko uzyskać informacje o produkcie pakietu Cortana Intelligence Suite

Z tych celów pamiętać tego podręcznika dotyczącego ma na celu dostarczania biznesowych i wiedzy technicznej, który pomoże osiągnięcie tych celów.

### <a name="power-load-and-demand-forecasting"></a>Obciążenia zasilania i prognozowanie żądanie
W ramach sektora energetycznego może istnieć wiele sposobów popytu prognozowania mogą pomóc w rozwiązywaniu problemów o krytycznym znaczeniu dla firmy. W rzeczywistości Prognozowanie żądanie jest uznawana za podstawę wiele przypadków użycia rdzeni w branży. Ogólnie rzecz biorąc, możemy należy wziąć pod uwagę dwa typy prognoz popytu na energię: krótko- i długoterminowej. Każdy z nich może służą do różnych celów i wykorzystania inny sposób. Główna różnica między nimi jest typu horizon prognozowania, co oznacza przedział czasu, w przyszłości, dla którego możemy prognozy.

#### <a name="short-term-load-forecasting"></a>Krótka obciążenia termin prognozowania
W kontekście zapotrzebowania na energię krótki okres załadować prognozowania (STLF) jest zdefiniowany jako zagregowane obciążenia, które jest prognozowanych w niedalekiej przyszłości na różne części siatki (lub siatkę jako całość). W tym kontekście krótkoterminowa jest zdefiniowane jako zakres czasu w zakresie 1 godziny do 24 godzin. W niektórych przypadkach zakres 48 godzin jest także możliwa. W związku z tym STLF jest często w przypadku użycia operacyjne siatki. Oto kilka przykładów STLF zmiennych przypadki użycia:

* I podaży równoważenia
* Obsługa handlowym zasilania
* Tworzenie rynku (ustawienia zasilania cena)
* Optymalizacja operacyjne siatki
* [Odpowiedzi na żądanie](https://en.wikipedia.org/wiki/Demand_response)
* Szczytowy prognozowania żądanie
* Żądanie strony zarządzania
* Równoważenie obciążenia i zapobiegania przeciążenia
* Długoterminowych prognozowania obciążenia
* Wykrywanie błędów i anomalii
* Ograniczenie/bilansowanie godzinami szczytu 

STLF model najczęściej są oparte na near przeszłości (ostatniego dnia lub tygodnia) dane dotyczące zużycia i użyj prognozowanych temperatury jako ważne predykcyjne. Uzyskiwanie dokładne temperatury prognozy przez godzinę i się do 24 godzin staje się mniej żądanie teraz dni. Te modele są mniej wrażliwe na okresach wzorców i trendów zużycia długoterminowej.

Rozwiązania SLTF prawdopodobnie także wygenerować dużą liczbę wywołań prognozowania (żądania obsługi) od czasu ich trwa wywołania co godzinę, a w niektórych przypadkach nawet z częstotliwością wyższy. Jest również bardzo częściej można zobaczyć zagnieżdżenia, gdzie każdego poszczególnych podstacji lub transformatora jest reprezentowany jako model autonomicznych i dlatego odebranej liczby żądań prognozowania są lepsze.

#### <a name="long-term-load-forecasting"></a>Długoterminowych prognozowania obciążenia
Celem o długie termin obciążenia prognozowania (LTLF) jest prognozy żądanie zasilania z zakresu czasu, począwszy od 1 tygodnia wiele miesięcy (i w niektórych przypadkach przez liczbę lat). Ten zakres typu horizon dotyczy przede wszystkim dotyczące planowania i przypadki użycia inwestycji.

W przypadku scenariuszy długoterminowej jest istotne dane wysokiej jakości, które obejmuje zakres wielu lat (minimalna 3 lata). Te modele wyodrębniania sezonowości wzorców danych historycznych zazwyczaj i korzystać z zewnętrznej predicators takie jako pogodą i klimatyzacja wzorce.

Jest ważne wyjaśnić, że jest dłuższy zakres prognozowania, mniej dokładne może być prognozy. W związku z tym należy utworzyć niektóre interwały zaufania wraz z rzeczywistego prognozy, która umożliwiałaby ludzi składników odmiany możliwych do procesu planowania.

Ponieważ scenariusz użycia LTLF przede wszystkim jest planowana, oczekujemy może znacznie niższe prognozowania woluminów (w porównaniu do STLF). Firma Microsoft zwykle zobaczyć te przewidywania osadzone w narzędzi wizualizacji, takich jak Excel lub Power Bi i można wywołać ręcznie przez użytkownika.

### <a name="short-term-vs-long-term-prediction"></a>Krótki okres programu vs. Długoterminowe prognozowania
W poniższej tabeli porównano STLF i LTLF w odniesieniu do najważniejszych atrybuty:

| Atrybut | Prognozy obciążenia krótkoterminowa | Długoterminowych prognozy obciążenia |
| --- | --- | --- |
| Prognozy typu Horizon |Od 1 godziny do 48 godzin |Od 1 do 6 miesięcy lub więcej |
| Poziom szczegółowości danych |Co godzinę |Co godzinę lub codziennie |
| Typowe zastosowania |<ul><li>Żądanie/dostaw równoważenia</li><li>Wybierz godzinę prognozowania</li><li>Odpowiedzi na żądanie</li></ul> |<ul><li>Długoterminowych planowania</li><li>Planowanie zasobów siatki</li><li>Planowanie zasobów</li></ul> |
| Zmienne typowych predykcyjne |<ul><li>Dnia lub tygodnia</li><li>Godzina dnia</li><li>Co godzinę temperatury</li></ul> |<ul><li>Miesiąc roku.</li><li>Dzień miesiąca</li><li>Temperatury długoterminowej i klimatyzacja</li></ul> |
| Zakres danych historycznych |Dwóch do trzech lat. danych |5 – 10 lat. danych |
| Typowy dokładności |MAPE * 5% lub niższa |MAPE * 25% lub niższa |
| Prognozy częstotliwości |Tworzone co godzinę lub co 24 godziny |Utworzone po miesiąc, co kwartał lub roczne |

\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) — oznacza średni procent błędów

Jak wynika z tej tabeli, jest bardzo ważne odróżnić krótko- i długoterminowe prognozowania scenariuszy, jak te reprezentują potrzeb innej firmy i może mieć inne wdrożenie i wzorców użycia.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Przypadek użycia przykład 1: eSmart systemów — optymalizacji przeciążenia
Istotne znaczenie [inteligentne siatki](https://en.wikipedia.org/wiki/Smart_grid) jest dynamicznie i stale optymalizacji i odpowiednio do zmieniających się wzorców użycia. Krótkoterminowej zmiany, które są głównie spowodowane zmianami temperatury może mieć wpływ na zużycie energii (*np.*, więcej mocy jest używana dla warunku lotniczego lub grzejników). W tym samym czasie zużycia energii jest również wpływ długoterminowych trendów. Mogą one obejmować sezonowości efekty, national święta długoterminowej wzrost zużycia i nawet ekonomiczne czynników, takich jak indeksu konsumenta, ceny wydobycie ropy naftowej i PKB.

W tym przypadku [eSmart](http://www.esmartsystems.com/) chce wdrożyć rozwiązania oparte na chmurze, które umożliwia prognozowanie większego ukierunkowania sytuacji przeciążenia, w dowolnym danym podstacji siatki. W szczególności eSmart chciała zidentyfikować podstacji, które mogą przeciążać w ciągu godziny, więc natychmiastowych akcji można podjąć w celu uniknięcia lub rozwiązać takiej sytuacji.

Dokładne i szybkie prognozowania wymaga wykonania trzech modeli predykcyjnych:

* Czas trwania modelu, który umożliwia prognozowanie zużycia energii w każdym podstacji podczas następnego kilka tygodnie lub miesiące
* Krótkoterminowe modelu, który umożliwia prognozowanie sytuacja na danym podstacji w ciągu następnej godziny
* Model temperatury, który umożliwia prognozowanie przyszłych temperatury w wielu scenariuszach

Celem długoterminowej modelu jest rank podstacjami przez ich większego ukierunkowania do przeciążenia (biorąc pod uwagę ich moc transmisji) podczas następnego tygodnia lub miesiąca. Dzięki temu tworzenie krótką listę podstacji służących jako dane wejściowe krótkoterminowej prognozowania. Temperatury jest ważne Prognoza modelu długoterminowej, istnieje potrzeba stale przygotowania prognoz temperatury wielu scenariusza i źródła je jako dane wejściowe do długoterminowego modelu. Prognoz krótkoterminowych następnie wywoływana w celu prognozowania, które podstacji prawdopodobnie przeciążenia w ciągu następnej godziny.

Modele krótko- i długoterminowej są wdrażane pojedynczo na każdym podstacji. W związku z tym praktyczne wykonanie tych modeli wymaga szeroką gamę aranżacji. Aby uzyskać większą dokładność prognozowania w krótkim czasie, bardziej szczegółowego modelu jest dedykowany dla każdej godziny, dnia. Te modele są wykonywane co godzinę i Zakończ działanie w kilka minut, należy przewidzieć czas wystarczający do odpowiedzi i podjęcie odpowiednich działań zapobiegawczych, jeśli to konieczne. Ta kolekcja modeli są stale aktualizowane przez okresowe ponownego trenowania przy użyciu najnowszych danych.

Więcej informacji na temat tego przypadku użycia można znaleźć [tutaj](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Użyj kryteria kwalifikacyjne Case — wymagania wstępne
Główne siła Cortana Intelligence jest zaawansowanych możliwości wdrażanie i skalowanie rozwiązań na learning maszyny. Jest on przeznaczony do obsługi tysięcy prognoz, które są wykonywane jednocześnie. Może automatycznie skalować, aby spełnić zmiany wzorca użycia. Rozwiązania w związku z tym koncentruje się na niezawodność i wydajność obliczeniową. Na przykład firmę jest zainteresowana produkujących zapotrzebowania na energię dokładne prognozy przez godzinę i dla każdej godziny, dnia. Z drugiej strony, Dbamy mniej o udzielenie odpowiedzi na pytanie, dlaczego żądanie jest przydatna, aby był on jest (samego modelu zajmie się tym).

W związku z tym jest zdawać sobie sprawę, że nie wszystkie przypadki użycia i problemów biznesowych mogą skutecznie rozwiązane, za pomocą uczenia maszynowego.

Cortana Intelligence i uczenia maszynowego mogą być bardzo wydajny w rozwiązaniu problemu biznesowego danego, gdy są spełnione poniższe kryteria:

* Jest problem biznesowy w ręcznie **predykcyjnej** charakter. W przykładzie przypadków użycia predykcyjnej jest firmy narzędzie, które chcesz przewidzieć zasilania obciążenie podstacji danego ciągu następnej godziny. Z drugiej strony, będzie analizowanie i klasyfikacji sterowników historycznych żądanie **opisową** charakter i w związku z tym mniejszej stosowane.
* Brak wyraźnego **ścieżkę akcji** podejmowanych po udostępnieniu prognozowania. Na przykład przewidywanie przeciążenia na podstacji w ciągu następnej godziny może wyzwolić aktywnego akcji zmniejszenie obciążenia, który jest skojarzony z tym podstacji i w związku z tym potencjalnie uniemożliwia przeciążenia.
* Reprezentuje przypadek użycia **typowy typ problemu** tak, aby podczas rozwiązać go można przygotować rozwiązywania inne podobne przypadki użycia.
* Klienta można ustawić **cele ilościowe i jakościowe** aby zademonstrować implementacji pomyślne rozwiązanie. Na przykład, dobrym ilościowy cel prognozy żądanie energii będzie wartości progowej wymaganej dokładności (*np.*, maksymalnie 5% błędu jest dozwolone) lub gdy przewidywania podstacji przeciążenia następnie precision (liczba alarmów true) i odwołania (stopień alarmów true) powinna być większa niż wartość progowa w danym. Tych celów powinien pochodzić od klienta cele biznesowe.
* Brak wyraźnego **scenariusza integracji** z firmy biznesowego przepływu pracy. Na przykład można zintegrować prognozy obciążenia podstacji siatki control Center, aby umożliwić działań związanych z zapobieganiem przeciążenia.
* Klient jest gotowy do użycia **danych za pomocą odpowiedniej jakości** do obsługi przypadek użycia (więcej informacji, zobacz następną sekcję, zobacz **jakości danych**, z tego podręcznika dotyczącego).
* Obejmuje architektura odbiorcy danych na chmurze lub **uczenie maszynowe oparte na chmurze**, w tym uczenie Maszynowe Azure i innych składników pakietu Cortana Intelligence Suite.
* Klient jest gotowa do ustanowienia **przepływ danych pełnego** tego urządzenia dostarczanie danych w chmurze w sposób ciągły i chce **operacjonalizacji** rozwiązania.
* Klient jest gotowy do **dedykować zasoby** kto będzie można aktywnie zaangażowane podczas początkowej wdrożenia pilotażowego, aby wiedzy i własność rozwiązania, które mogą zostać przeniesione do klienta po pomyślnym zakończeniu.
* Zasób klienta powinien być **danych wykwalifikowanych specjalistów**, najlepiej naukowca danych.

Kwalifikacji przypadek użycia na podstawie kryteriów powyżej może znacznie poprawić odsetka pomyślnych przypadek użycia i ustanowienia dobrej beachhead dla implementacji przypadków użycia w przyszłości.

### <a name="cloud-based-solutions"></a>Rozwiązań w chmurze
Pakietu Cortana Intelligence Suite na platformie Azure to zintegrowane środowisko, która znajduje się w chmurze. Wdrażanie rozwiązania zaawansowana analityka w środowisku chmury zawierające znaczących korzyści dla firm i w tym samym czasie może oznaczać duże zmiany dla firm, że nadal wykorzystanie lokalnych rozwiązań IT. W ramach sektora energetycznego istnieje wyczyść trend Stopniowa migracja operacji w chmurze. Tego trendu idzie w parze wraz z programowanie inteligentne siatki jak wspomniano powyżej, w **trendów w branży**. Zgodnie z tego podręcznika dotyczącego koncentruje się na rozwiązanie oparte na chmurze w domenie energii, należy wyjaśnić korzyści i innych kwestii związanych z wdrożeniem rozwiązania w chmurze.

Być może Największą zaletą rozwiązanie oparte na chmurze jest koszt. Jako rozwiązanie sprawia, że użycie składników wdrożonych w chmurze, nie istnieje kosztów góry lub kst (koszt z towarów sprzedanych) składnik kosztów skojarzonych z nim. Oznacza to, że nie istnieje potrzeba inwestycji w sprzęt, oprogramowanie i konserwacja IT i dlatego istnieje znacznego obniżenia ryzyko.

Inną zaletą ważne jest struktury kosztów z rozwiązań w chmurze. Serwerów opartych na chmurze obliczeniowych i magazynu można wdrożyć i skalować na just zgodnie z potrzebami. Reprezentuje poprawę wydajności kosztów rozwiązania oparte na chmurze.

Ponadto nie istnieje potrzeba inwestowania w konserwacji IT lub infrastruktury przyszłego rozwoju wszystkich jest częścią oferta oparta na chmurze. W tym zakresie pakietu Cortana Intelligence Suite obejmuje najlepiej w usługach klasy i przechowuje zmieniających się jego mapy drogowej. Nowe funkcje, składników i możliwości są stale wprowadzane i rozwijać.

Dla firmy, która jest uruchamiana jego przejścia do chmury zdecydowanie zalecamy podjęcie podejście stopniowe zaimplementowanie mapy drogowej migracji chmury. Mamy nadzieję, narzędzia i firmy w domenie energii, przypadków użycia, które zostały omówione w tym podręcznika dotyczącego reprezentują doskonała okazja dla wdrażania pilotażowego rozwiązania analizy predykcyjnej w chmurze.

#### <a name="business-case-justification-considerations"></a>Względy biznesowe uzasadnienie wielkość
W wielu przypadkach mogą być zainteresowani biznesowego wyjaśnienia przypadek użycia danego, w którym rozwiązania w chmurze i uczenia maszynowego są ważne składniki klienta. Inaczej niż w przypadku rozwiązania lokalnego, w przypadku rozwiązania oparte na chmurze jest minimalny, składnik góry kosztów i większość elementów kosztów związanych z rzeczywistego użycia. W przypadku wdrażania energii, funkcja prognozowania rozwiązania na pakietu Cortana Intelligence Suite, wielu usług można zintegrować z jednej wspólnej struktury kosztów. Na przykład baz danych (*np.*, SQL Azure) może służyć do przechowywania danych pierwotnych i następnie do rzeczywistego prognozy uczenie Maszynowe Azure służy do hostowania usług prognozowania. W tym przykładzie struktury kosztów mogą obejmować magazynu i składników transakcyjnych.

Z drugiej strony jeden powinien dysponować dobrą znajomością wartości biznesowej systemu operacyjnego zapotrzebowania na energię prognozowania (warunek krótko- i). W rzeczywistości jest zdawać sobie sprawę wartościach biznesowych każdej operacji prognozy. Na przykład dokładnie prognozowania zasilania obciążenia przez następne 24 godziny może uniemożliwić nadprodukcji lub mogą pomagać w zapobieganiu przeciążenia siatki i to można określić pod względem oszczędności finansowe codziennie.

Podstawowa formuła obliczania finansowych zaletą żądanie prognozy rozwiązaniem byłoby: ![Podstawowa formuła obliczania finansowych zaletą żądanie prognozy rozwiązania](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Ponieważ pakietu Cortana Intelligence Suite udostępnia z modelu cenowego, nie jest ponoszenia składnik koszt stały, aby w tej formule nie jest konieczne. Formuły można obliczyć na podstawie codziennie, miesięcznego lub rocznego.

Można znaleźć bieżącego pakietu Cortana Intelligence Suite i Azure ML planów cenowych [tutaj](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Proces tworzenia rozwiązania
Użyj cyklu programowanie zapotrzebowania na energię prognozowania rozwiązania, które zwykle obejmuje 4 fazy, w które wykonujemy oparte na chmurze technologii i usług w ramach pakietu Cortana Intelligence Suite.

Jest to zilustrowane na poniższym diagramie:

![Cykl inteligentne siatki](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

Następujący ustęp opisano proces ten krok 4:

1. **Zbieranie danych** — wszystkie zaawansowane analizy na podstawie rozwiązanie opiera się na danych (zobacz **opis danych**). W szczególności jeśli chodzi o analizy predykcyjnej i prognozowania, firma Microsoft polegają na stałe, dynamiczne przepływu danych. W przypadku prognozowania żądanie energii tych danych można ustalić źródło bezpośrednio z liczników inteligentnych lub już agregowane w lokalnej bazie danych. Możemy również zależne od innych zewnętrznych źródeł danych, takich jak pogodą i temperatury. Ten przepływ bieżących danych należy zorkiestrowana, zaplanowane i przechowywać. [Fabryka danych Azure](http://azure.microsoft.com/services/data-factory/) (ADF) jest naszym głównym najważniejszą metodą roboczą do wykonywania tego zadania.
2. **Modelowanie** — dla prognoz energii dokładne i niezawodne jedną opracowanie (pociąg) i zachować dużą model, że używa danych historycznych oraz wyodrębnia znaczenie i predykcyjnej wzorce w danych. Obszar Machine Learning (ML) ma zostały szybko rośnie z bardziej zaawansowane algorytmy rutynowo opracowywanych. Azure ML Studio udostępnia środowisko użytkowników, które pozwala korzystać z najbardziej zaawansowanych algorytmów uczenia Maszynowego w ramach przepływu pracy nad. Przepływ pracy przedstawia intuicyjne diagram przepływu i obejmuje przygotowanie danych, funkcję wyodrębniania, modelowania i oceny modelu. Użytkownika można ściągnąć setki różne modele, które znajdują się w tym środowisku. Na koniec tej fazy naukowca danych będą mieć model pracy, które jest obliczane i gotowa do wdrożenia.
   
   Poniższy diagram jest Ilustracja typowego przepływu pracy:
   
   ![Modelowanie przepływu pracy](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)
3. **Wdrożenie** — w przypadku modelu pracy w strony, następnym krokiem jest wdrożenie. W tym miejscu modelu jest konwertowany na usługi sieci web, która uwidacznia interfejs API RESTful, który można wywołać jednocześnie za pośrednictwem Internetu od różnych klientów zużycia. Uczenie Maszynowe Azure zapewnia prosty sposób wdrażania modelu bezpośrednio z usługi Azure ML Studio za pomocą jednego kliknięcia przycisku. Pod maską odbywa się cały proces wdrażania. To rozwiązanie może automatycznie skalować do spełnienia wymagane zużycia.
4. **Zużycie** — na tym etapie firma Microsoft faktycznie wprowadzać użycie modelu prognozowania można używać w celu tworzenia prognoz. Z poziomu aplikacji użytkownika mogą być określane na zużycie (*np.*, pulpitu nawigacyjnego) lub bezpośrednio z systemu operacyjnego, takich jak żądanie/Podaj systemu lub rozwiązanie optymalizacji siatki. Wiele zastosowań, mogą być określane z pojedynczego modelu.

## <a name="data-understanding"></a>Opis danych
Po obejmujące względy biznesowe (zobacz **opis firm**) z prognozowania rozwiązania zapotrzebowania na energię, możemy teraz przystąpić do omówiono w nim danych. Wszystkie rozwiązania analizy predykcyjnej opiera się na danych. Energii prognozowania żądanie, możemy polegają na zużycie historycznych danych z różnych poziomów szczegółowości. Czy dane historyczne służy jako materiał raw. Będzie on przechodzą dokładnych analiz, w którym naukowca danych identyfikuje zmienne predykcyjne (nazywane również funkcje), które mogą być przełączane do modelu, który ostatecznie wygeneruje wymagane prognozy.

W dalszej części tej sekcji możemy opisano różne procedury i zagadnienia dla zrozumienia danych oraz sposób przełączyć go do użytecznej postaci.

### <a name="the-model-development-cycle"></a>Cykl modelu programowania
Tworzenie dobrej prognozowania wymaga, aby pewne przygotowania dokładne modeli i planowania. Niszczy procesie modelowania w wielu kroków i koncentrujących się na jednym kroku naraz można znacznych udoskonaleń wynik całego procesu.

Na poniższym diagramie przedstawiono, jak proces modelowania można podzielić na wiele kroków:

![Model programowania cyklu](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Jak widać, że ten cykl składa się z sześciu kroków:

* Formułowanie problem
* Wprowadzanie danych i eksploracja danych
* Przygotowanie danych i inżynieria funkcji
* Modelowanie
* Ocena modelu
* Opracowywanie zawartości

W dalszej części tej sekcji możemy opisano poszczególne kroki i elementów do uwzględnienia w każdym kroku.

### <a name="problem-formulation"></a>Formułowanie problem
Firma Microsoft należy rozważyć formułowanie problem najważniejszych krokiem, który należy wykonać przed wykonania dowolnego rozwiązania analizy predykcyjnej. W tym miejscu możemy przekształcenie problem biznesowy i Rozłóż go do określonych elementów, których będzie możliwe przy użyciu danych i technik modelowania. Jest dobrą praktyką jest sformułować problem jako zestaw pytań, na które chcielibyśmy odpowiedzi. Poniżej przedstawiono niektóre możliwe pytania, które mogą być stosowane w ramach zakresu prognozowania żądanie energii:

* Co to jest oczekiwane obciążenie poszczególnych podstacji w następnej godziny lub dnia?
* Porę dnia Moje siatki doświadczy szczytowego zapotrzebowania?
* Jakie jest prawdopodobieństwo Moje siatki do obsługi oczekiwanego szczytowego obciążenia?
* Podczas każdej godziny, dnia potrzebną moc powinna generować stacji zasilania?

Określanie odpowiedzi na następujące pytania pozwala skupić się na wprowadzenie odpowiednich danych i wdrażania rozwiązania, w pełni wyrównany wykonywanego problem biznesowy. Ponadto firma Microsoft, ustawić niektóre kluczowe metryki, które umożliwiają ocenę wydajności modelu. Na przykład jak dokładny prognozy należy, i co to jest zakres błędów, które nadal będą akceptowane przez firmę?

### <a name="data-sources"></a>Źródła danych
Nowoczesne siatki inteligentne zbiera dane z różnych części i składników siatki. Te dane reprezentuje różne aspekty operacji i użycie siatki zasilania. W zakresie prognozy zapotrzebowania na energię możemy są ograniczenia dyskusji na źródeł danych, które odzwierciedlać zużycie rzeczywiste żądanie. Jedno źródło ważne zużycia energii są inteligentne liczników. Narzędzia na całym świecie są szybkiego wdrażania inteligentne liczników dla swoich klientów. Inteligentne liczników rejestruj zużycie energii rzeczywiste i stale przekazywania tych danych do firmy narzędzia. Dane są zbierane i wysłane w stałych interwałach, począwszy od co 5 minut do 1 godziny. Bardziej zaawansowane liczników inteligentne mogą być programowane zdalnie do kontroli i zrównoważenia rzeczywiste użycie w gospodarstwo domowe. Dane inteligentnych meter jest stosunkowo niezawodne i zawiera sygnaturę czasową. Dzięki temu ważne składnika żądanie prognozy. Dane licznika można agregować (równy określonemu procentowi górę) na różnych poziomach, w ramach topologii siatki: transformatora, podstacji, region, *itp*. Firma Microsoft następnie wybierz poziom agregacji wymagane do tworzenia modelu prognozowania można używać dla niego. Na przykład narzędzie firmy chcą przewidywania przyszłych obciążenie poszczególnych jego podstacji siatki następnie danych wszystkie liczniki można agregowane dla każdego poszczególnych podstacji i używane jako dane wejściowe dla modelu prognozowania można używać. Nazywamy inteligentne liczników wewnętrznego źródła danych.

Prognozy niezawodnej energii będzie używana również innych zewnętrznych źródeł danych. Jeden istotny czynnik, który ma wpływ na zużycie energii jest pogody lub bardziej precyzyjnie temperatury. Dane historyczne pokazuje silne powiązania między temperatury na zewnątrz i zużycie energii. Dni gorące lato konsumentów należy użyć ich klimatyzacja i podczas włączania zima grzewczych. Wiarygodnego źródła historycznych temperatur w lokalizacji siatki jest w związku z tym kluczem. Ponadto firma Microsoft również polegać na dokładne prognozy temperatury jako predykcyjne zużycia energii.

Inne zewnętrznych źródeł danych może również pomóc w budowania modeli prognozowania żądanie energii. Mogą one obejmować długoterminowe klimatyzacja zmiany, ekonomiczny indeksów (*np.*, PKB) i inne. W tym dokumencie firma Microsoft nie będzie zawierał te inne źródła danych.

### <a name="data-structure"></a>Struktury danych
Po określeniu źródeł danych, chcielibyśmy upewnij się, że dane, które zostały zebrane obejmuje funkcje prawidłowe dane. Aby utworzyć modelu prognozy żądanie niezawodne, czy musimy upewnij się, że dane zebrane zawiera elementy danych, które mogą pomóc przewidywania przyszłego zapotrzebowania. Poniżej przedstawiono niektóre podstawowe wymagania dotyczące struktury danych (schemat) nieprzetworzone dane.

Dane pierwotne składa się z wierszy i kolumn. Każda miara jest reprezentowany jako pojedynczy wiersz danych. Każdy wiersz danych zawiera wiele kolumn (nazywane również funkcje lub pól).

1. **Sygnatura czasowa** — pola sygnatury czasowej reprezentuje rzeczywisty czas, gdy zarejestrowano pomiaru. Powinny być zgodne z jednym z typowych formaty daty/godziny. Części zarówno data i godzina powinny być włączone. W większości przypadków jest niepotrzebna raz do zarejestrowania do drugiego poziomu szczegółowości. Należy określić strefę czasową, w którym rejestrowane są dane.
2. **Pomiarowe identyfikator** — to pole określa licznik lub urządzenia do pomiaru. Jest zmienną podzielone na kategorie i może być kombinacja cyfr i znaków.
3. **Wartość zużycia** — jest to rzeczywiste użycie w danym daty/godziny. Zużycie może być mierzone w kWh (kilowatt-hour) lub innych preferowanym jednostki. Należy pamiętać, że jednostka miary musi pozostać spójne we wszystkich pomiarów w danych. W niektórych przypadkach użycia mogą być dostarczane ponad 3 fazy zasilania. W takim przypadku czy musimy zebrać wszystkich faz niezależne zużycia.
4. **Temperatury** — temperatury zwykle są zbierane z niezależne źródło. Jednak powinny być zgodne z dane dotyczące zużycia. Powinien on zawierać timestamp zgodnie z powyższym opisem, co umożliwi jej mają być synchronizowane z danymi rzeczywistego zużycia. Wartość temperatury można określić w stopniach c lub f, ale powinny pozostać spójna we wszystkich pomiarów.
5. **Lokalizacja —** pole lokalizacji jest zwykle skutkiem miejsce, w których zebrano dane temperatury. Może być reprezentowany, jako liczbę kodu pocztowego lub w formacie (lat/long) szerokości geograficznej/długości.

Poniższych tabelach przedstawiono przykłady dobrej konsumenckich i temperatury format danych:

| **Data** | **Czas** | **Identyfikator miernika** | **Faza 1** | **Faza 2** | **Faza 3** |
| --- | --- | --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |ABC1234 |7.0 |2.1 |5.3 |
| 7/1/2015 |10:00:01 |ABC1234 |7.1 |2.2 |4.3 |
| 7/1/2015 |10:00:02 |ABC1234 |6.0 |2.1 |4.0 |

| **Data** | **Czas** | **Lokalizacja** | **Temperatury** |
| --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |11242 |24.4 |
| 7/1/2015 |10:00:01 |11242 |24.4 |
| 7/1/2015 |10:00:02 |11242 |24.5 |

Jak podano powyżej, w tym przykładzie obejmuje 3 różne wartości zużycia skojarzone z 3 fazy zasilania. Należy również zauważyć, że pól daty i godziny są rozdzielone, jednak można także je łączyć w jednej kolumnie. W takim przypadku kolumna lokalizacji jest wyświetlana w formacie 5-cyfrowy kod i temperatury w formacie c stopnia.

### <a name="data-format"></a>Format danych
Pakietu Cortana Intelligence Suite może obsługiwać najbardziej typowych formatów danych, takich jak CSV, TSV, JSON, *itp*. Należy pamiętać, że format danych jest zgodne przez cały cykl życia projektu.

### <a name="data-ingestion"></a>Wprowadzanie danych
Ponieważ energii prognozy jest stale i często przewidzieć, Upewniamy się, że za pomocą procesu wprowadzanie danych stałych i niezawodne jest przepływu danych pierwotnych. Proces wprowadzanie musi zapewniać nieprzetworzone dane są dostępne w procesie prognozowania w wymaganym czasie. Oznacza to, że częstotliwość wprowadzanie danych powinna być większa niż częstotliwość prognozowania.

Na przykład: Jeśli naszych żądanie prognozowania rozwiązanie powoduje wygenerowanie nowej prognozy o 8:00 codziennie, musimy upewnij się, że obejmuje wszystkie dane, które zostały zebrane w ciągu ostatnich 24 godzin ma został w pełni pozyskanych do tego punktu i ma nawet z ostatniej godziny  dane.

Aby to osiągnąć, pakietu Cortana Intelligence Suite udostępnia różne sposoby obsługi procesu wprowadzanie danych. To spowoduje dalsze omówione w **wdrożenia** sekcji tego dokumentu.

### <a name="data-quality"></a>Jakość danych
Źródło danych pierwotnych, które jest wymagane do wykonywania żądanie niezawodnych i precyzyjne prognozowania musi spełniać kryteria jakości niektóre podstawowe dane. Mimo że zaawansowane metody statystyczne może służyć do kompensacji problem jakości niektórych danych, potrzebujemy jeszcze upewnij się, że są możemy przekraczających próg jakości niektóre podstawowe dane podczas wprowadzania nowych danych. Poniżej przedstawiono kilka zagadnienia dotyczące jakości danych pierwotnych:

* **Brak wartości** — odnosi się do sytuacji, gdy określony rozmiar nie zostały zebrane. Podstawowym wymogiem jest, czy brak szybkość wartości nie mogą zawierać więcej niż 10% dowolnego danego okresu. W przypadku pojedynczej wartości nie ma on należy wskazać przy użyciu wstępnie zdefiniowanych wartości (na przykład: "9999"), a nie "0", który może być nieprawidłowa.
* **Dokładność pomiaru** — wartość rzeczywistą zużycia lub temperatury powinny być dokładnie rejestrowane. Pomiary niedokładne utworzy prognoz niedokładne. Zazwyczaj błąd pomiaru powinna być niższa niż 1% względem wartości true.
* **Czas pomiaru** — wymagane jest, że sygnatura czasowa rzeczywistego dane zbierane będą nie różni się o więcej niż 10 sekund względem true czasu rzeczywistego miary.
* **Synchronizacji** — po wielu źródeł danych są używane (*np.*, konsumenckich i temperatury) musi zapewnić, że nie istnieją żadne synchronizacja czasu między nimi. Oznacza to, że różnica czasu między sygnatura czasowa zebranych z dowolnego dwóch niezależnych źródeł danych nie może przekraczać więcej niż 10 sekund.
* **Czas oczekiwania** — jak wspomniano powyżej, w **wprowadzanie danych**, możemy zależą od procesu przepływu i wprowadzanie danych. Aby kontrolować, które Upewniamy się, że firma Microsoft kontroluje opóźnienia przesyłania danych. To jest określony jako różnica czasu między czasem, która została wykonana pomiar i czasu, w którym zostało załadowane do przechowywania pakietu Cortana Intelligence Suite i jest gotowy do użycia. Krótkoterminowe obciążenia prognozowania opóźnienie całkowite nie mogą zawierać więcej niż 30 minut. Długoterminowe obciążenia prognozowania opóźnienie całkowite nie mogą zawierać więcej niż 1 dzień.

### <a name="data-preparation-and-feature-engineering"></a>Przygotowanie danych i inżynieria funkcji
Gdy nieprzetworzone dane zostały pozyskanych (zobacz **wprowadzanie danych**) i został bezpiecznie przechowywane, jest gotowy do przetwarzania. Fazie przygotowywania danych jest zasadniczo pobierania danych pierwotnych i konwertowanie (Przekształcanie, przekształcanie) go w formie na etapie modelowania. Który może obejmować proste operacje, takie jak przy użyciu kolumny danych pierwotnych, tak jak jego rzeczywista wartość zmierzona, wartości standardowych, bardziej złożonych operacji, takich jak [czasu mniej rozwiniętych](https://en.wikipedia.org/wiki/Lag_operator)i inne. Kolumny danych nowo utworzony są określane jako funkcje związane z danymi i proces generowania te nazywa się engineering funkcji. Na koniec tego procesu czy mamy nowy zestaw danych, które zostały uzyskane z danych pierwotnych i może służyć do modelowania. Ponadto fazie przygotowywania danych musi uwzględniać brakujące wartości (zobacz **jakości danych**) i je skorygować. W niektórych przypadkach firma Microsoft również potrzebny do normalizacji danych, aby upewnić się, że wszystkie wartości są reprezentowane w takiej samej skali.

W tej sekcji niektóre typowe funkcje danych, które znajdują się w energii na listę modele prognozy żądanie.

**Czas pracy funkcji:** te funkcje są obliczane na podstawie danych Data/sygnatura czasowa. Są wyodrębniane i przekonwertowane na podzielone na kategorie funkcji, takich jak:

* Godzina dnia — jest to godzina dnia pobierający wartości z zakresu od 0 do 23
* Dzień tygodnia — to reprezentuje dzień tygodnia i przyjmuje wartości z zakresu od 1 (niedziela) do 7 (sobota)
* Dzień miesiąca — to reprezentuje rzeczywistą datę i może przyjmować wartości z zakresu od 1 do 31
* Miesiąc roku — to reprezentuje miesiąca i przyjmuje wartości z zakresu od 1 (styczeń) do 12 (grudzień)
* Weekendowe — jest to funkcja wartość binarną, która pobiera wartości 0 dni tygodnia lub 1 dla weekendowe
* Święto — jest to funkcja wartości binarnej, który pobiera wartości 0 dla regularnych dzień lub 1 dla dniem wolnym od pracy
* Warunki transformaty — warunki transformaty są wagi, które pochodzą z sygnaturą czasową i są używane do przechwytywania sezonowości (cykle) w danych. Ponieważ firma Microsoft może mieć wiele okresów w zestawie danych Firma Microsoft może wymagać wiele warunków transformaty. Na przykład wartości żądanie może być roczne, co tydzień i codzienne okresów/cykle skutkujących 3 warunki transformaty.

**Funkcje miary niezależne:** niezależne funkcje obejmują wszystkie elementy danych, które firma Microsoft ma być używana jako zmienne predykcyjne w modelu. W tym miejscu możemy wykluczyć funkcji zależnych, która potrzebujemy do prognozowania.

* Funkcja Lag — są to czas przesunięte wartości rzeczywistych żądanie. Na przykład funkcji lag 1 będą przechowywane wartości żądanie w poprzedniej godziny (przy założeniu danych co godzinę) względem bieżącą sygnaturę czasową. Podobnie, możemy dodać opóźnienie 2, lag 3, *itp*. Rzeczywiste kombinacja funkcji lag, które są używane są określane w fazie modelowania przez ocena wyników modelu.
* Długoterminowych trendów — ta funkcja reprezentuje liniowy wzrost żądanie latach.

**Funkcja zależnych:** funkcji zależnych jest kolumny danych, którego chcemy nasz model do przewidywania. Z [nadzorowanego uczenia maszynowego](https://en.wikipedia.org/wiki/Supervised_learning), należy najpierw nauczenia modelu, używając funkcji zależnych (określanych także jako etykiety). Dzięki temu modelu dowiedzieć się wzorce w danych skojarzony z funkcją zależnych. W zapotrzebowania na energię prognozy zwykle chcemy przewidzieć rzeczywistego żądanie i dlatego firma Microsoft może używać go jako funkcji zależnych.

**Obsługa brakujących wartości:** w fazie przygotowywania danych musimy będzie określenie najlepszych strategii obsługi brakujące wartości. Przede wszystkim odbywa się przy użyciu różnych statystyczne [metod przypisywania danych](https://en.wikipedia.org/wiki/Imputation_\(statistics\)). W przypadku prognozowania żądanie energii, możemy zazwyczaj przypisują brakujące wartości przy użyciu średniej ruchomej z poprzednich punktów danych.

**Normalizacji danych:** normalizacji danych jest innego typu transformacji używanego do zapewnienia wszystkie dane liczbowe, takie jak Prognoza podobne skali. Pomaga to zwykle, zwiększyć dokładność modelu i dokładność. Firma Microsoft będzie zazwyczaj to zrobić przez podzielenie rzeczywistej wartości przez zakres danych.
Powoduje to skalowanie oryginalnej wartości w dół do mniejszego zakresu, zazwyczaj z zakresu od -1 do 1.

## <a name="modeling"></a>Modelowanie
Faza modelowania jest, gdzie odbywa się konwersji danych do modelu. W podstawowej tego procesu są zaawansowane algorytmy, które skanowania dane historyczne (dane szkoleniowe), Wyodrębnij wzorców i tworzenia modelu. Ten model może później użyć do prognozowania na nowe dane, które nie było używane do tworzenia modelu.

Gdy mamy model niezawodnej pracy, użyj jej do oceniać nowe dane, które mają strukturę dołączenie wymaganych funkcji (X). Proces oceniania spowoduje użycie trwałego modelu (obiekt z fazy szkolenia) i przewidywanie Zmienna docelowa, która jest oznaczona Ŷ.

### <a name="demand-forecasting-modeling-techniques"></a>Żądanie prognozowania technik modelowania
W przypadku prognozowania wykonujemy żądanie korzystanie z danych historycznych, który jest uporządkowany według czasu. Zazwyczaj zwane danych zawierającego wymiar czasu jako [czasu serii](https://en.wikipedia.org/wiki/Time_series). Celem w czasie serii modelowania jest znalezienie czasu pokrewne trendów sezonowości, auto korelacji (korelacji wraz z upływem czasu) i sformułować ich na modelu.

W ostatnich latach zaawansowane algorytmy zostały zaprojektowane do uwzględnienia prognozowania szeregu czas i zwiększyć dokładność prognozowania. Pokrótce omówiono niektóre z nich w tym miejscu.

> [!NOTE]
> Ta sekcja nie jest przeznaczona do użycia jako machine learning i prognozowanie Przegląd, ale raczej jako krótką ankietę modelowania techniki, które są często używane do prognozowania żądanie. Aby uzyskać więcej informacji i materiały edukacyjne o prognozowania szeregu czasu, zdecydowanie zaleca się książki online [Prognozowanie: zasady i praktyki](https://www.otexts.org/book/fpp).
> 
> 

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**MA (średnia)**](https://www.otexts.org/fpp/6/2)
Średniej ruchomej jest jednym z pierwszego technik analitycznych, używane do prognozowania serie czasu i jest nadal w jednym z najbardziej najczęściej używanych techniki obecnie. Jest również podstawą dla bardziej zaawansowanych technik prognozowania. O średniej ruchomej przez uśrednianie punktów najnowszych K, gdzie K określa kolejność średniej ruchomej możemy są Prognozowanie następnego punktu danych.

Przenoszenie technika średni powoduje wygładzanie prognozy i w związku z tym nie może obsłużyć również dużej zmienności danych.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (Wygładzanie wykładnicze)**](https://www.otexts.org/fpp/7/5)
Wykładniczej Wygładzanie (ETS) to rodzina różnych metod, które korzystania średnią ważoną ostatnie punktów danych w celu prognozowania następnego punktu danych. Będzie przypisać wagi wyższe wartości nowszą i stopniowo zmniejszyć to waga dla starszych mierzone wartości. Istnieje wiele różnych metod z tej rodziny, obejmują niektóre z nich, takich jak obsługa sezonowości w danych [Holt Winters okresach metody](https://www.otexts.org/fpp/7/5).

Niektóre z tych metod również współczynnik sezonowości danych.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**ARIMA (Regresja automatycznie zintegrowany przeniesienie średniej)**](https://www.otexts.org/fpp/8)
Automatycznie regresji zintegrowane przeniesienie średniej (ARIMA) jest innej rodziny metod, który jest powszechnie używany do prognozowania szeregów czasowych. Łączy praktycznie metody automatycznego regresji przy średniej ruchomej. Metody Auto-regression używać modeli regresji wykonując poprzedniej wartości szeregu czasu, aby obliczyć następny punkt daty. Metody ARIMA mają zastosowanie również różnicowych metody, które obejmują obliczenie różnicy między punktami danych i używanie zamiast oryginalnej wartości zmierzona. Na koniec ARIMA również sprawia, że użycie ruchoma średnia techniki, które opisano powyżej. Kombinacja wszystkie te metody na różne sposoby jest, co tworzy rodziny metody ARIMA.

ETS i ARIMA są powszechnie używany w tej chwili prognozowania żądanie energii i wiele innych problemów prognozowania. W wielu przypadkach są one łączone ze sobą w celu dostarczenia bardzo dokładne wyniki.

**Ogólne regresja wielokrotna** regresji modeli może być najważniejszymi metoda modelowania w obrębie domeny uczenia maszynowego i statystyki. W kontekście szeregów czasowych używamy regresji w celu przewidywania przyszłych wartości (*np.*, popytu). W regresji możemy zająć liniowy kombinację zmienne predykcyjne i Dowiedz się wagi te zmienne predykcyjne (określane również jako) podczas procesu szkolenia. Celem jest utworzyć regresji, który będzie prognozy naszych przewidywane wartości. Metody regresji są odpowiednie, gdy zmienna docelowa jest liczbą i w związku z tym również pasuje do prognozowania szeregu czasu. Istnieje szereg metod regresji, w tym modele regresji bardzo proste, takich jak [regresji liniowej](https://en.wikipedia.org/wiki/Linear_regression) i bardziej zaawansowane te, takich jak drzew decyzyjnych [lasach losowa](https://en.wikipedia.org/wiki/Random_forest), [Neural Sieci](https://en.wikipedia.org/wiki/Artificial_neural_network)i Boosted drzewa decyzyjnego.

Konstruowanie zapotrzebowania na energię prognozowania jako problem regresji daje dużą elastyczność w wyborze funkcji naszych danych, które mogą być łączone z danych serii czasu rzeczywistego żądanie i czynniki zewnętrzne, takie jak temperatury. Więcej informacji na temat wybrane funkcje zostały omówione w funkcji Engineering (zobacz **Przygotowanie danych i funkcji Engineering**) części tego podręcznika dotyczącego.

Naszym doświadczeniu z implementacji i wdrożenia pilotażowego prognoz żądanie energii znaleźliśmy często modeli regresji zaawansowane, które są dostępne w uczenie Maszynowe Azure w celu uzyskania najlepszych wyników, a wykonujemy korzystać z nich.

## <a name="model-evaluation"></a>Ocena modelu
Ocena modelu ma kluczową rolę w **modelu programowania cyklu**. W tym kroku opisano do sprawdzania poprawności modelu i jej wydajności przy użyciu danych rzeczywistych. Podczas wykonywania kroku modelowania używamy część danych dostępnych do uczenia modelu. W fazie oceny traktujemy pozostałej części danych do testowania modelu. Praktycznie oznacza, że firma Microsoft są zasilania nowych danych modelu została restrukturyzacji, który zawiera te same funkcje co zestawu danych szkoleniowych. Jednak w procesie weryfikacji używamy modelu prognozowania Zmienna docelowa, a nie zapewniają Zmienna docelowa dostępne. Firma Microsoft często można znaleźć tego procesu jako oceniania modelu. Firma Microsoft może następnie użyć wartości true docelowych i porównaj je z przewidywane te. Celem jest mierzenie i zminimalizować Błąd prognozowania, różnicę między prognozy i wartość true. Kwantyfikacja pomiaru błędów jest klucza, ponieważ chcemy dostosowywać model, a następnie sprawdź, czy rzeczywiście zmniejsza się kod błędu. Dostrajanie modelu może odbywać się przez zmodyfikowanie parametrów modelu procesu uczenia lub przez dodanie lub usunięcie funkcji danych (nazywane [odchylenia parametry](https://channel9.msdn.com/Blogs/Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Praktycznie oznacza to, że firma Microsoft może być konieczne powtarzać inżynierii funkcji modelowania, a modelu fazy oceny wielokrotnie, dopóki nie można zmniejszyć błąd do wymaganego poziomu.

Ważne jest, aby nacisk, że błąd prognozowania nigdy nie będzie równa zero nie jest modelu, który można dokładnie przewidzieć co wynik. Istnieje jednak wielkość błędu, który jest akceptowany przez firmę. W procesie weryfikacji chcielibyśmy upewnij się, że nasze Błąd prognozowania modelu znajduje się na poziomie lub lepszą niż poziom tolerancji biznesowych. Dlatego jest ważne, aby ustawić poziom dopuszczalnego błędu na początku cyklu podczas **formułowanie Problem** fazy.

### <a name="typical-evaluation-techniques"></a>Ocena typowe techniki
Istnieją różne sposoby, w których prognozowania błąd może być mierzony i wyliczone. W tej sekcji dotyczą możemy dyskusji technik oceny właściwe szeregów czasowych i w określonych dla zapotrzebowania na energię prognozy.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE oznacza oznacza błąd absolutny procent. Z MAPE możemy obliczania różnicy między prognozowanych każdego punktu i wartością rzeczywistą tego punktu. Następnie możemy określenie błędu dla każdego punktu obliczanie proporcję różnica względem rzeczywistej wartości. W ostatnim kroku będziemy średni tych wartości. Formuła matematyczna używany dla MAPE jest następujący:

![Formuła MAPE](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*gdzie A<sub>t</sub> jest wartością rzeczywistą F<sub>t</sub> jest wartością prognozowaną, a n horizon prognozy.*

## <a name="deployment"></a>Wdrożenie
Po możemy zająć fazy modelowania i zweryfikować wydajności modelu możemy są gotowe do fazy wdrożenia. W tym kontekście wdrożenia oznacza umożliwiające klientowi korzystanie z modelu za rzeczywiste prognoz na nim uruchomione w dużej skali. Koncepcja wdrożenia jest klucz w uczenie Maszynowe Azure, ponieważ naszym głównym celem jest stale wywołania prognoz, a nie tylko uzyskiwanie wglądu w danych. Faza wdrożenia jest częścią, gdy włączysz modelu, który ma być używane na dużą skalę.

W kontekście energii prognozy naszym celem jest wywołania prognoz ciągły i okresowe przy jednoczesnym zapewnieniu, że świeże dane są dostępne dla modelu i czy prognozowanych dane są wysyłane do klienta korzystanie.

### <a name="web-services-deployment"></a>Wdrażanie usługi sieci Web
Głównego bloku konstrukcyjnego można wdrożyć w uczenie Maszynowe Azure to usługa sieci web. Jest to najbardziej efektywny sposób umożliwić zużycie modelu predykcyjnego w chmurze. Usługi sieci Web hermetyzuje modelu i otacza go z [RESTful](http://www.restapitutorial.com/) interfejsu API (Application Programming Interface). Interfejs API może służyć jako część żadnego kodu klienta, jak pokazano na poniższym diagramie.

![Firma Microsoft wdrażania usługi i zużycia](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Jak widać, usługi sieci web jest wdrażana w chmurze pakietu Cortana Intelligence Suite i może być wywoływany za pośrednictwem jej narażonych punkt końcowy interfejsu API REST. Innego typu klientów w różnych domenach można wywołać jednocześnie usługi za pośrednictwem interfejsu API sieci Web. Usługi sieci web można również skalować do obsługi wielu współbieżnych wywołań.

### <a name="a-typical-solution-architecture"></a>Architektura rozwiązania typowych
W przypadku wdrażania rozwiązania prognozowania zapotrzebowania na energię, Dbamy o wdrażanie kompleksowe rozwiązania wykracza poza usługą sieci web prognozowania, który ułatwia przepływu danych. W momencie wywołania możemy nowej prognozy czy musimy upewnij się, że model jest pobierany z funkcjami aktualne dane. Oznacza to, że nowo zebranych danych pierwotnych jest stale pozyskanych, przetwarzane i przekształceniu wymagany zestaw na został skompilowany modelu funkcji. W tym samym czasie chcemy udostępnić prognozowanych danych dla elementu end korzystanie z klientów. Na poniższym diagramie przedstawiono przykład danych cyklu przepływu (lub potoku danych):

![Przepływ danych kompleksowe prognozy zapotrzebowania na energię](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Poniżej przedstawiono kroki, które odbywają się w ramach cyklu prognozy żądanie energii:

1. Miliony wdrożonej danych liczników stale generują dane dotyczące zużycia energii w czasie rzeczywistym.
2. Te dane są zbierane i przekazane do repozytorium chmury (*np.*, obiektów Blob platformy Azure).
3. Przed przetworzeniem, nieprzetworzone dane są agregowane podstacji lub regionalnym zgodnie z definicją w działalności.
4. Funkcja przetwarzania (zobacz **Przygotowanie danych i przetwarzania funkcja**) następnie ma miejsce i tworzy dane, które są wymagane dla modelu uczenia lub oceniania — dane zestawu funkcji są przechowywane w bazie danych (*np.*, SQL Azure).
5. Ponownie szkolenia usługi jest wywoływane, aby ponownie uczenia modelu prognozowania można używać — tym zaktualizowanej wersji modelu jest trwały, aby mogą być używane przez usługę sieci web oceniania.
6. Zgodnie z harmonogramem, która pasuje do wymaganej częstotliwości prognozy wywoływania oceniania usług sieci web.
7. Prognozowanych dane są przechowywane w bazie danych, w których są dostępne dla klienta użycie zakończenia.
8. Klient zużycie pobiera prognozy, stosuje je do siatki i wykorzystuje ona zgodnie z przypadek użycia wymagane.

Należy zauważyć, że cały cykl jest w pełni zautomatyzowany i uruchamia zgodnie z harmonogramem. Cały aranżacji cyklu danych może odbywać się za pomocą narzędzi takich jak [fabryki danych Azure](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Architektura pełnego wdrożenia
Aby można było praktycznie wdrożyć rozwiązanie prognozy żądanie energii na funkcję Cortana Intelligence, musimy upewnij się, że wymagane składniki są połączenia i poprawnie skonfigurowana.

Na poniższym diagramie przedstawiono typowe architektura Cortana Intelligence na podstawie, które implementuje i organizuje cyklu przepływu danych, który jest opisany powyżej:

![Architektura pełnego wdrożenia](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Aby uzyskać więcej informacji o poszczególnych składników oraz całej architektury zapoznaj się szablon rozwiązania energii.

