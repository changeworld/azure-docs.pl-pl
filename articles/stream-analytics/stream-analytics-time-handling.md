---
title: Omówienie obsługi czasu w usłudze Azure Stream Analytics
description: Dowiedz się więcej o czas obsługi w usłudze Azure Stream Analytics
author: jasonwhowell
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 0eb4b77964aa3c07bac2af615a26c3a9199525de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64692375"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Omówienie obsługi czasu w usłudze Azure Stream Analytics

W tym artykule omówiono sposób zapewniania uzasadnienie wyboru tych elementów rozwiązania praktycznego czas obsługi problemów w usłudze Azure Stream Analytics. Czas projektowania obsługi decyzje są ściśle powiązane z czynniki kolejności zdarzeń.

## <a name="background-time-concepts"></a>Pojęcia dotyczące czasu tła

Do lepszego ramki dyskusji czynnością jest zdefiniowanie niektóre pojęcia tła:

- **Czas trwania zdarzenia**: Czas, kiedy wystąpiło zdarzenie, oryginalnym. Na przykład, gdy przenoszenie samochodu na drogach zbliża się stoisku płatny.

- **Czas przetwarzania**: Czas, gdy zdarzenie osiągnie systemu przetwarzania i zostanie wykryty. Na przykład, gdy czujnik stoisku płatny widzi samochodu a systemem komputerowym, potrwa kilka chwil do przetwarzania danych.

- **Znak wodny**: Znacznik czasu zdarzeń, który wskazuje na to, aż punktu, w jaki zostały ingressed do przesyłania strumieniowego procesora zdarzeń. Znaki wodne Niech system wskazać wyczyść zbierać zdarzenia. Zgodnie z naturą strumieni danych przychodzących zdarzenia nigdy nie zatrzymuje, więc znaki wodne informowania o postępie do pewnego momentu w strumieniu.

   Ważne jest pojęciem znaku wodnego. Stream Analytics do określania, kiedy system może utworzyć pełną, poprawny i powtarzalne wyniki, które nie wymagają ma zostać wycofany, Zezwalaj na znaki wodne. Przetwarzanie może odbywać się w sposób gwarantowane, który jest przewidywalny i powtarzalny. Na przykład jeśli policzone ponownie musi odbywać się do obsługi warunek niektórych błędów, znaki wodne są bezpieczne początkowe i końcowe punkty.

Jako dodatkowe zasoby na ten temat, zobacz wpisy w blogu Tyler Akidau [przesyłania strumieniowego 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) i [przesyłania strumieniowego 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>Wybieranie najlepszej początkowy czas

Stream Analytics zapewnia użytkownikom jedną z dwóch opcji dla pobrania czas trwania zdarzenia:

1. **Godzina nadejścia**  

   Godzina nadejścia są przypisywane w źródło danych wejściowych, gdy zdarzenie osiągnie źródła. Godzina nadejścia mieli dostęp za pomocą **EventEnqueuedUtcTime** właściwości dla danych wejściowych usługi Event Hubs, **IoTHub.EnqueuedTime** właściwości do usługi IoT Hub oraz **BlobProperties.LastModified**  właściwości dla obiektu blob danych wejściowych.

   Przy użyciu czasu odbioru jest to domyślne zachowanie i najlepiej nadaje się do danych, archiwizowanie scenariuszy, w przypadku, gdy jest konieczne nie logiki czasowej.

2. **Czas aplikacji** (zwaną również czas trwania zdarzenia)

   Czas aplikacji jest przypisany, zdarzenie jest generowane, gdy jest częścią ładunku zdarzenia. Aby przetwarzać zdarzenia według czasu aplikacji, należy użyć **sygnatury czasowej przez** klauzuli w zapytaniu select. Jeśli **sygnatury czasowej przez** klauzula jest nieobecne, zdarzenia są przetwarzane przez czas nadejścia.

   Koniecznie Użyj sygnatury czasowej w ładunku w przypadku logiki czasowej. W ten sposób opóźnienia w systemie źródłowym lub w sieci mogą być rozliczane.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Jak czas trwania w usłudze Azure Stream Analytics

Korzystając z czas aplikacji, postępu czasu opiera się na zdarzenia przychodzące. Jest trudne do systemu, aby dowiedzieć się, jeśli nie ma wydarzeń lub są opóźnione zdarzenia przetwarzania strumienia. Z tego powodu Azure Stream Analytics generuje heurystyczne znaków wodnych w następujący sposób dla każdej partycji danych wejściowych:

1. Zawsze, gdy ma żadnych zdarzeń przychodzących, znaku wodnego jest największy czas zdarzenia, które zauważono pory minus rozmiar okna tolerancji poza kolejnością.

2. Zawsze, gdy nie ma żadnego zdarzenia przychodzące, znaku wodnego jest bieżący czas szacowany przybycia (czas, który upłynął w tle maszyny Wirtualnej, przetwarzanie zdarzeń od czasu ostatniego zdarzenia wejściowe są widoczne, a także, wprowadź czas nadejścia zdarzenia) minus okno tolerancji spóźnionego przybycia.

   Godzina nadejścia tylko można oszacować, ponieważ Godzina nadejścia rzeczywistych jest generowany na brokera zdarzeń wejściowych, takich jak Event Hubs, a nie usługi Azure Stream Analytics maszyny Wirtualnej przetwarzanie zdarzeń.

Projekt służy dwóch dodatkowych celów, oprócz generowania znaków wodnych:

1. System generuje wyniki, w odpowiednim czasie z lub bez zdarzeń przychodzących.

   Masz kontrolę nad jak regularnie mają być wyświetlone dane wyjściowe. W witrynie Azure portal na **określanie kolejności zdarzeń** strony zadania usługi Stream Analytics, możesz skonfigurować **zdarzeń poza kolejnością** ustawienie. Podczas konfigurowania tego ustawienia, należy wziąć pod uwagę kompromis osi czasu z tolerancji zdarzeń poza kolejnością w strumieniu zdarzeń.

   Okno tolerancji spóźnionego przybycia ważne jest, aby zachować generowania znaki wodne, nawet przy braku zdarzeń przychodzących. Czasami może występować okres skąd pochodzą nie zdarzeń przychodzących, np. gdy jest rozrzedzony strumień wejściowy zdarzeń. Ten problem jest zwiększone przy użyciu wielu partycji w brokerze dane wejściowe zdarzenia.

   Przesyłanie strumieniowe systemów przetwarzania danych bez okno tolerancji spóźnionego przybycia może to spowodować obniżenie z opóźnieniem dane wyjściowe, gdy dane wejściowe są rozrzedzone i wiele partycji są używane.

2. Zachowanie systemu musi być powtarzalne. Powtarzalności jest właściwością ważnych systemów przetwarzania danych przesyłania strumieniowego.

   Znak wodny jest tworzony na podstawie czasu odbioru i czasu aplikacji. Oba są utrwalonych w brokera zdarzeń, a zatem powtarzalne. W przypadku Godzina nadejścia musi oszacować, w przypadku braku zdarzeń, dzienniki usługi Azure Stream Analytics przybycia szacowany czas powtarzalności podczas powtarzania na potrzeby odzyskiwania po awarii.

Należy zauważyć, że jeśli zdecydujesz się używać **Godzina nadejścia** jako czas zdarzeń nie ma potrzeby konfigurowania poza kolejnością na uszkodzenia i Tolerancja spóźnionego przybycia. Ponieważ **Godzina nadejścia** gwarantuje monotonicznie wzrastająca w brokerze dane wejściowe zdarzenia, usługi Azure Stream Analytics po prostu nie uwzględnia konfiguracje.

## <a name="late-arriving-events"></a>Opóźnione zdarzenia nadchodzących

Zgodnie z definicją okno tolerancji spóźnionego przybycia, dla każdego zdarzenia przychodzące, Azure Stream Analytics porównuje **czas trwania zdarzenia** z **Godzina nadejścia**; Jeśli czas zdarzenia znajduje się poza okno tolerancji możesz można skonfigurować system dostosować czas zdarzenia w granicach tolerancji lub porzucić zdarzenia.

Należy wziąć pod uwagę, że po wygenerowaniu znaki wodne, usługa może potencjalnie otrzymywać zdarzeń z czas zdarzenia jest mniejszy niż znak wodny. Można skonfigurować usługę na albo **porzucić** tych zdarzeń lub **dostosować** wartość limitu czasu zdarzeń.

Jako część korekty, zdarzenia w **System.Timestamp** ustawiono nową wartość, ale **czas trwania zdarzenia** samo pole nie jest zmieniany. To dopasowanie jest jedyną sytuacją, w przypadku, gdy zdarzenie **System.Timestamp** może różnić się od wartości w polu Czas zdarzeń i może spowodować nieoczekiwane wyniki do wygenerowania.

## <a name="handling-time-variation-with-substreams"></a>Obsługa zmiany czasu z substreams

Mechanizm generowania heurystyczne znaku wodnego opisane tutaj działa dobrze w większości przypadków, gdzie czas jest zsynchronizowany przede wszystkim między różnymi nadawców zdarzeń. Jednak w realnym, szczególnie w przypadku wielu scenariuszy IoT, system ma niewielkie kontrolę nad zegar na nadawców zdarzeń. Nadawców zdarzeń może być szeroką gamę urządzeń, w tym polu, być może w różnych wersjach sprzętu i oprogramowania.

Zamiast przy użyciu znaku wodnego globalne do wszystkich zdarzeń w partycji danych wejściowych, Stream Analytics ma inny mechanizm nazywany substreams ułatwiające. Możesz użyć substreams w zadaniu przez napisanie zapytania zadania, który używa [ **TIMESTAMP BY** ](/stream-analytics-query/timestamp-by-azure-stream-analytics) klauzuli i słowo kluczowe **za pośrednictwem**. Aby wyznaczyć strumieni podrzędnych, podaj nazwę kolumny klucza po **za pośrednictwem** — słowo kluczowe, takie jak `deviceid`, dzięki czemu system stosuje zasady czasu według tej kolumny. Każdy strumieni podrzędnych pobiera własne niezależnie od limitu. Ten mechanizm jest przydatne do Zezwalaj na generowanie danych wyjściowych w odpowiednim czasie, podczas rozwiązywania problemów związanych z dużą wynikających z przesunięcia czasowego zegara lub sieci opóźnienia między nadawców zdarzeń.

Substreams są unikatowe rozwiązanie udostępniane przez usługę Azure Stream Analytics i nie są oferowane przez innych przesyłania strumieniowego systemów przetwarzania danych. Stream Analytics dotyczy zdarzeń przychodzących, gdy są używane substreams okno tolerancji spóźnionego przybycia. Ustawieniem domyślnym (5 sekund) jest prawdopodobnie zbyt mały dla urządzeń z sygnaturami czasowymi nierównej. Zaleca się rozpoczynać od 5 minut i wprowadzać zmiany, zgodnie z ich wzorzec niesymetryczność zegara urządzenia.

## <a name="early-arriving-events"></a>Wczesne nadchodzących zdarzeń

Być może zauważono, innym pojęciem wczesne okno przybycia, który wygląda jak przeciwieństwo okno tolerancji spóźnionego przybycia. To okno jest ustalony na 5 minut i służy do innych celów z późnego przybycia, jeden.

Ponieważ usługi Azure Stream Analytics gwarantuje zawsze generuje pełnych wyników, można określić tylko **czas rozpoczęcia zadania** jako dane wyjściowe podczas pierwszego zadania, czas wejściowy. Godzina rozpoczęcia zadania jest wymagane do okno zakończenia jest przetwarzany, nie tylko ze środka okna.

Stream Analytics następnie pochodną godzinę rozpoczęcia specyfikacji zapytania. Jednak ponieważ brokera zdarzeń wejściowych jest tylko indeksowane przez czas nadejścia, system musi zapewniać tłumaczenie godziny rozpoczęcia zdarzenia do czasu odbioru. System można uruchomić przetwarzanie zdarzeń z tego punktu w brokerze dane wejściowe zdarzenia. Wczesne nadchodzących ograniczeń okna Tłumaczenie jest bardzo proste. Jest on uruchamiany minus początku okna nadchodzących 5-minutowy czas trwania zdarzenia. To obliczenie oznaczają też, że system porzuca wszystkie zdarzenia, które są widoczne, o czasie zdarzenia 5 minut późniejszy niż czas nadejścia.

Takie podejście umożliwia upewnij się, że przetwarzanie jest powtarzalne, niezależnie od tego, w której został uruchomiony w danych wyjściowych z. Bez taki mechanizm nie byłoby możliwe gwarantuje powtarzalności, jak wiele systemów przesyłania strumieniowego oświadczeń robią.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Efekty uboczne kolejności czasu tolerancji zdarzeń

Zadania usługi Stream Analytics ma kilka **określanie kolejności zdarzeń** opcje. Dwa można skonfigurować w witrynie Azure portal: **zdarzeń poza kolejnością** ustawienie (poza kolejnością tolerancji), a **zdarzenia odbierane z opóźnieniem** ustawienie (tolerancja spóźnionego przybycia). **Wczesne przybycia** tolerancja jest stała i nie można go zmienić. Te zasady czasu są używane przez usługę Stream Analytics w celu zapewnienia silnej gwarancji. Jednak te ustawienia mają pewne czasami nieoczekiwane skutki:

1. Przypadkowo wysyła zdarzenia, które są zbyt wczesny.

   Wczesne zdarzenia nie powinny zostać zwrócone normalnie. Istnieje możliwość, że wcześniejsze zdarzenia są wysyłane do dane wyjściowe, jeśli zegar nadawcy działa zbyt szybko, chociaż. Wszystkie wcześniejsze nadchodzących zdarzenia są porzucane, dzięki czemu nie będzie mógł przeglądać ich z danych wyjściowych.

2. Wysyłanie starych zdarzeń usługi Event hubs do przetworzenia przez usługę Azure Stream Analytics.

   Chociaż stare zdarzenia może wydawać się nieszkodliwe na najpierw z powodu stosowania tolerancja spóźnionego przybycia, mogą zostać odrzucone starych zdarzeń. W przypadku zdarzenia zbyt stara **System.Timestamp** wartość zostanie zmieniona podczas przyjmowanie zdarzeń. Ze względu na to zachowanie obecnie usługi Azure Stream Analytics jest bardziej odpowiednia dla scenariuszy, zamiast scenariusze przetwarzania zdarzeń historycznych przetwarzania zdarzeń, prawie w czasie rzeczywistym. Możesz ustawić **zdarzenia odbierane z opóźnieniem** czas na największa możliwa wartość (20 dni) Aby obejść ten problem, w niektórych przypadkach.

3. Dane wyjściowe wydają się być opóźnione.

   Pierwszy znak wodny jest generowany w momencie obliczeniowe: **zdarzeń maksymalny czas** system ma zaobserwowane do tej pory minus rozmiar okna tolerancji poza kolejnością. Domyślnie na uszkodzenia poza kolejnością jest skonfigurowany do zero (00 minut i 00 sekund). Po ustawieniu na wartość godziny nowszej, który jest różna od zera, pierwsze dane wyjściowe zadania przesyłania strumieniowego jest opóźnione przez tę wartość czasu (lub nowszego) z powodu limitu, po raz pierwszy jest obliczana.

4. Dane wejściowe są rozrzedzone.

   Jeśli w danej partycji jest Brak danych wejściowych, limitu czasu jest obliczany jako **Godzina nadejścia** minus okno tolerancji spóźnionego przybycia. W wyniku zdarzenia wejściowe są rzadkie i rozrzedzone, może być opóźniony, że ilość czasu danych wyjściowych. Wartość domyślna **zdarzenia odbierane z opóźnieniem** wartość to 5 sekund. Należy się spodziewać się pewne opóźnienie podczas wysyłania zdarzeń wejściowych jednego naraz, na przykład. Można uzyskać niższa, opóźnienia, po ustawieniu **zdarzenia odbierane z opóźnieniem** okna duża wartość.

5. **System.Timestamp** wartość różni się od czasu w **czas trwania zdarzenia** pola.

   Zgodnie z wcześniejszym opisem system dostosowuje czas trwania zdarzenia poza kolejnością na uszkodzenia lub późnego przybycia na uszkodzenia w systemie windows. **System.Timestamp** wartość zdarzenia jest korygowana, ale nie **czas trwania zdarzenia** pola.

## <a name="metrics-to-observe"></a>Metryki do obserwowania

Możesz obserwować liczbę kolejności czasu efekty na uszkodzenia za pomocą zdarzeń [metryki zadania usługi Stream Analytics](stream-analytics-monitoring.md). Następujące metryki są istotne:

|Metryka  | Opis  |
|---------|---------|
| **Zdarzenia poza kolejnością** | Wskazuje liczbę zdarzeń otrzymanych poza kolejnością, które zostały porzucone lub podane skorygowany sygnatury czasowej. Ta metryka jest bezpośrednio wpływ na konfigurację **zdarzeń poza kolejnością** ustawienie **określanie kolejności zdarzeń** strony zadania w witrynie Azure portal. |
| **Opóźnione zdarzenia wejściowe** | Wskazuje liczbę zdarzeń przybywających późne ze źródła. Ta metryka zawiera zdarzenia, które zostały porzucone lub mieli ich sygnatury czasowej została dostosowana. Ta metryka jest bezpośrednio wpływ na konfigurację **zdarzenia odbierane z opóźnieniem** w **określanie kolejności zdarzeń** strony zadania w witrynie Azure portal. |
| **Wczesne zdarzenia wejściowe** | Wskazuje liczbę zdarzeń przybywających wcześnie, ze źródła, albo zostały usunięte lub sygnatur czasowych został dostosowany, jeśli są one dłużej niż 5 minut przed terminem. |
| **Opóźnienie znaku wodnego** | Wskazuje opóźnienie zadania przetwarzania danych przesyłania strumieniowego. Zobacz więcej informacji w poniższej sekcji.|

## <a name="watermark-delay-details"></a>Szczegóły opóźnienie znaku wodnego

**Limitu opóźnienie** metryka jest obliczana jako czas zegarowy węzła przetwarzania minus największy znak wodny widział do tej pory. Aby uzyskać więcej informacji, zobacz [limitu wpis w blogu opóźnienie](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Może istnieć kilka przyczyn, dla których ta wartość metryki jest większa niż 0, w ramach normalnych operacji:

1. Opóźnienie nieprzerwaną pracę przetwarzania potoku transmisji strumieniowych. Zwykle to opóźnienie jest symboliczną cenę.

2. Okno tolerancji poza kolejnością wprowadzono opóźnienia, ponieważ znak wodny zostanie zmniejszona rozmiar okna na uszkodzenia.

3. Okno późnego przybycia wprowadzone opóźnienia, ponieważ znak wodny zostanie zmniejszona rozmiar okna na uszkodzenia.

4. Pochylanie zegara węzła przetwarzania generowania metrykę.

Istnieje szereg innych ograniczeń zasobów, które mogą powodować potoku transmisji strumieniowych zwolnić. Metryka opóźnienie znaku wodnego może wzrosnąć ze względu na:

1. Za mało zasobów przetwarzania w usłudze Stream Analytics obsłużyć zdarzenia wejściowe. Aby skalować zasoby, zobacz [opis i dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md).

2. Nie ma wystarczającej ilości przepustowości w ramach brokerów zdarzeń wejściowych, dzięki czemu są one ograniczone. Możliwe rozwiązania, zobacz [automatycznie skalować w górę jednostek przepływności usługi Azure Event Hubs](../event-hubs/event-hubs-auto-inflate.md).

3. Ujść danych wyjściowych nie są aprowizowane z wystarczającą pojemnością, dzięki czemu są one ograniczone. Możliwe rozwiązania powszechnie zależeć na wersję usługi danych wyjściowych.

## <a name="output-event-frequency"></a>Częstotliwość zdarzeń danych wyjściowych

Usługa Azure Stream Analytics używa znaku wodnego postępu jako wyzwalacz tylko w celu wygenerowania zdarzenia wyjściowe. Ponieważ znak wodny jest tworzony na podstawie danych wejściowych, jest powtarzalne podczas odzyskiwania po awarii, a także na ponowne przetwarzanie zainicjowanej przez użytkownika.

Korzystając z [okresowymi](stream-analytics-window-functions.md), tylko generowanych przez nią danych wyjściowych na końcu okna. W niektórych przypadkach użytkownicy mogą chcieć się częściowej agregacji wygenerowany na podstawie systemu windows. Agregacje częściowe nie są obecnie obsługiwane w usłudze Azure Stream Analytics.

W innych rozwiązań do transmisji strumieniowej zdarzenia wyjściowe można zmaterializowanego w różnych punktach wyzwalacza, w zależności od okoliczności zewnętrznych. Jest możliwe w rozwiązaniach, które można było wygenerować wiele razy zdarzeń wyjściowych na danym przedziale czasowym. Wartości wejściowe są dostosowany, agregacji wyników stają się bardziej precyzyjne operacje. Zdarzenia można przeanalizowania w pierwszy i poprawione wraz z upływem czasu. Na przykład w przypadku niektórych urządzeń jest niedostępny od sieci, wartość szacunkową może służyć przez system. Później w tym samym urządzeniu przejściu do trybu online w sieci. Następnie dane rzeczywiste zdarzenia mogły zawarte w strumieniu wejściowym. Przedział czasu przetwarzania wyników danych wyjściowych generuje dokładniejsze dane wyjściowe.

## <a name="illustrated-example-of-watermarks"></a>Przedstawiono przykład znaki wodne

Następujące obrazy pokazują, jak znaki wodne postępu w różnych warunkach.

W poniższej tabeli przedstawiono przykładowe dane, który jest na wykresie poniżej. Należy zauważyć, że czas zdarzenia i czas nadejścia różnią się w czasami dopasowania i czasami nie.

| czas trwania zdarzenia | Godzina nadejścia | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | device3

Na tej ilustracji są używane następujące dopuszczalnych:

- Wczesne przybycia systemu windows jest 5 minut
- Koniec okna nadchodzących wynosi 5 minut
- Okna zmiany kolejności to 2 minuty

1. Ilustracja znaku wodnego postępu przy użyciu tych zdarzeń:

   ![Usługa Azure Stream Analytics znaku wodnego ilustracji](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Godne uwagi procesów przedstawionych na poprzednim rysunku:

   1. Pierwsze zdarzenie urządzenia (1), a drugie zdarzenie (device2) mają wyrównane razy, a następnie są przetwarzane bez korekt. Na każde zdarzenie w miarę znaku wodnego.

   2. Po przetworzeniu trzecie zdarzenie (urządzenia 1), czas nadejścia (12:11) poprzedza czas zdarzenia (12:17). Zdarzenie dotarła wcześnie, 6 minut, aby zdarzenie zostało porzucone ze względu na wczesnych tolerancja przybycia 5-minutowych.

      Znak wodny nie postępu w tym przypadku wcześniejsze zdarzenia.

   3. Czwarty zdarzeń (device3), a piąta zdarzenia (urządzenia 1) zostały dostosowane razy, a następnie są przetwarzane bez dopasowania. Na każde zdarzenie w miarę znaku wodnego.

   4. Po przetworzeniu zdarzenia szóstego (device3), czas nadejścia (12:17) i czas zdarzenia (12:12) jest poniżej poziomu znaku wodnego. Czas zdarzenia jest dopasowywana do poziomu znacznik limitu górnego (12:17).

   5. Po przetworzeniu zdarzenia dziewiątego (device3), czas nadejścia (12:27) jest 6 minut wcześniej czas zdarzenia (12:21). Stosowana jest zasada późnego przybycia. Czas zdarzenia jest skorygowany (12:22), czyli powyżej limitu (12:21) więc żadne dalsze dostosowanie jest stosowany.

2. Ilustracja drugi znaku wodnego postępuje bez wczesne zasad przybycia:

   ![Usługa Azure Stream Analytics nie wczesne ilustracji znaku wodnego zasad](media/stream-analytics-time-handling/watermark-graph-2.png)

   W tym przykładzie nie wczesne zasady przybycia są stosowane. Zdarzenia odstające, odbierane z wczesnym podnieść znak wodny znacznie. Zwróć uwagę, trzecie zdarzenie (deviceId1 na godziny 12:11) nie zostaje przerwane w tym scenariuszu i znak wodny zostanie zaokrąglona do 12:15. Czwarty czas zdarzenia jest dostosowane do przodu 7 minut (12:08-12:15) w wyniku.

3. Na ilustracji końcowego substreams są używane (za pośrednictwem DeviceId). Wiele znaki wodne są śledzone, jeden na strumień. Istnieją mniej zdarzeń za pomocą ich czasy wyniku.

   ![Usługa Azure Stream Analytics substreams ilustracji znaku wodnego](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Kolejne kroki

- [Zagadnienia dotyczące platformy Azure Stream Analytics zdarzeń zamówienia](stream-analytics-out-of-order-and-late-events.md)
- [Metryki zadania usługi Stream Analytics](stream-analytics-monitoring.md)
