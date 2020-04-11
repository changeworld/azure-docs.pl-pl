---
title: Opis obsługi czasu w usłudze Azure Stream Analytics
description: Dowiedz się, jak działa obsługa czasu w usłudze Azure Stream Analytics, na przykład jak wybrać najlepszy czas rozpoczęcia, jak obsługiwać późne i wczesne zdarzenia oraz metryki obsługi czasu.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 55537fb923b26de4e02be35fdb817dee147584d7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115118"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Opis obsługi czasu w usłudze Azure Stream Analytics

W tym artykule omówimy, jak można dokonywać wyborów projektowych, aby rozwiązać praktyczne problemy z obsługą czasu w usłudze Azure Stream Analytics. Decyzje dotyczące projektowania obsługi czasu są ściśle związane z czynnikami zamawiania zdarzeń.

## <a name="background-time-concepts"></a>Pojęcia dotyczące czasu w tle

Aby lepiej oprawiać dyskusję, zdefiniujmy kilka pojęć tła:

- **Czas zdarzenia**: czas, kiedy wystąpiło oryginalne zdarzenie. Na przykład, gdy poruszający się samochód na autostradzie zbliża się do punktu poboru opłat.

- **Czas przetwarzania**: Czas, kiedy zdarzenie dociera do systemu przetwarzania i jest przestrzegane. Na przykład, gdy czujnik punktu poboru opłat widzi samochód, a system komputerowy zajmuje kilka chwil, aby przetworzyć dane.

- **Znak wodny:** znacznik czasu zdarzenia, który wskazuje do tego, do jakiego punktu zdarzenia zostały przystawione do procesora przesyłania strumieniowego. Znaki wodne pozwalają systemowi wskazać wyraźny postęp w pozyskiwania zdarzeń. Ze względu na charakter strumieni przychodzące dane zdarzeń nigdy nie zatrzymuje, więc znaki wodne wskazują postęp do pewnego punktu w strumieniu.

   Koncepcja znaku wodnego jest ważna. Znaki wodne umożliwiają analizie strumieniowej określenie, kiedy system może tworzyć kompletne, poprawne i powtarzalne wyniki, które nie muszą być wycofane. Przetwarzanie można wykonać w sposób gwarantowany, który jest przewidywalny i powtarzalny. Na przykład jeśli przeliczeń musi być wykonane dla niektórych warunków obsługi błędów, znaki wodne są bezpieczne punkty początkowe i końcowe.

Jako dodatkowe zasoby na ten temat, zobacz tyler Akidau blogu [Streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) i [Streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>Wybór najlepszego czasu rozpoczęcia

Usługa Stream Analytics daje użytkownikom dwie możliwości pobrania czasu zdarzenia:

1. **Godzina przyjazdu**  

   Czas przybycia jest przypisany do źródła wejściowego, gdy zdarzenie osiągnie źródło. Dostęp do godziny przyjścia można uzyskać za pomocą **EventEnqueuedUtcTime** właściwość dla danych wejściowych centrum zdarzeń, **IoTHub.EnqueuedTime** właściwość dla Usługi IoT Hub i przy użyciu **właściwości BlobProperties.LastModified** dla danych wejściowych obiektu blob.

   Przy użyciu czasu przybycia jest zachowanie domyślne i najlepiej używane do scenariuszy archiwizacji danych, gdzie nie ma logiki czasowej konieczne.

2. **Czas aplikacji** (nazywany również czasem zdarzenia)

   Czas aplikacji jest przypisywany podczas generowania zdarzenia i jest częścią ładunku zdarzenia. Aby przetworzyć zdarzenia według czasu aplikacji, należy użyć **klauzuli sygnatury czasowej według** w kwerendzie select. Jeśli **klauzula sygnatura czasowa według** jest nieobecna, zdarzenia są przetwarzane przez czas przybycia.

   Ważne jest, aby używać sygnatury czasowej w ładunku, gdy logika czasowa jest zaangażowana. W ten sposób można uwzględnić opóźnienia w systemie źródłowym lub w sieci.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Postępy w zakresie czasu w usłudze Azure Stream Analytics

Podczas korzystania z czasu aplikacji, postęp czasu jest oparty na zdarzenia przychodzące. System przetwarzania strumienia jest trudny do poznania, czy nie ma żadnych zdarzeń lub jeśli zdarzenia są opóźnione. Z tego powodu usługa Azure Stream Analytics generuje heurystyczne znaki wodne w następujący sposób dla każdej partycji wejściowej:

1. Za każdym razem, gdy występuje jakiekolwiek zdarzenie przychodzące, znak wodny jest największym czasem zdarzenia, jaki widzieliśmy do tej pory, minus rozmiar okna tolerancji poza kolejnością.

2. Za każdym razem, gdy nie ma zdarzenia przychodzącego, znak wodny jest bieżący szacowany czas przybycia (czas, który upłynął na za kulisami maszyny Wirtualnej przetwarzania zdarzeń z ostatniego czasu zdarzenia wejściowego jest widoczny plus czas przybycia tego zdarzenia wejściowego) minus okno tolerancji późnego przybycia.

   Czas przybycia można oszacować tylko, ponieważ rzeczywisty czas przybycia jest generowany na brokera zdarzeń wejściowych, takich jak centra zdarzeń, a nie maszyny Wirtualnej usługi Azure Stream Analytics przetwarzania zdarzeń.

Projekt służy dwóm dodatkowym celom, oprócz generowania znaków wodnych:

1. System generuje wyniki w odpowiednim czasie z lub bez przychodzących zdarzeń.

   Masz kontrolę nad tym, jak terminowe chcą zobaczyć wyniki wyjściowe. W witrynie Azure portal na stronie **zamawiania zdarzeń** zadania usługi Stream Analytics można skonfigurować ustawienie **zdarzenia poza kolejnością.** Podczas konfigurowania tego ustawienia należy wziąć pod uwagę kompromis terminowości z tolerancją zdarzeń poza kolejnością w strumieniu zdarzeń.

   Okno tolerancji późnego przybycia jest ważne, aby nadal generować znaki wodne, nawet w przypadku braku zdarzeń przychodzących. Czasami może istnieć okres, w którym nie przychodzące zdarzenia przychodzące, takie jak gdy strumień wejściowy zdarzenia jest rozrzedzony. Ten problem jest zaostrzony przez użycie wielu partycji w brokera zdarzeń wejściowych.

   Systemy przetwarzania danych przesyłania strumieniowego bez okna tolerancji późnego przybycia może cierpieć z powodu opóźnionych wyjść, gdy dane wejściowe są rzadkie i używane są wiele partycji.

2. Zachowanie systemu musi być powtarzalne. Powtarzalność jest ważną właściwością systemu przetwarzania danych strumieniowych.

   Znak wodny jest uzyskiwane z czasu przybycia i czasu aplikacji. Oba są utrzymywane w brokera zdarzeń, a tym samym powtarzalne. W przypadku, gdy czas przybycia musi być oszacowany w przypadku braku zdarzeń, usługa Azure Stream Analytics rejestrowa szacowany czas przybycia do powtarzalności podczas odtwarzania w celu odzyskania awarii.

Należy zauważyć, że gdy zdecydujesz się użyć **czasu przybycia** jako czasu zdarzenia, nie ma potrzeby konfigurowania tolerancji poza kolejnością i tolerancji późnego przybycia. Ponieważ **czas przybycia** jest gwarantowane monotonicznie zwiększenie w brokera zdarzeń wejściowych, Usługa Azure Stream Analytics po prostu nie uwzględnia konfiguracji.

## <a name="late-arriving-events"></a>Zdarzenia o późnym przyjeździe

Definicja okna tolerancji późnego przybycia dla każdego zdarzenia przychodzącego usługa Azure Stream Analytics porównuje **czas zdarzenia** z **czasem przybycia;** Jeśli czas zdarzenia znajduje się poza oknem tolerancji, można skonfigurować system, aby upuścić zdarzenie lub dostosować czas zdarzenia, aby mieścił się w granicach tolerancji.

Należy wziąć pod uwagę, że po wygenerowaniu znaków wodnych usługa może potencjalnie odbierać zdarzenia o czasie zdarzenia niższym niż znak wodny. Można skonfigurować usługę, aby **albo upuścić** te zdarzenia lub **dostosować** czas zdarzenia do wartości znaku wodnego.

W ramach korekty **sygnatura czasowa zdarzenia System.Time** jest ustawiona na nową wartość, ale samo pole **czasu zdarzenia** nie ulega zmianie. To dostosowanie jest jedyną sytuacją, w której **sygnatura czasowa zdarzenia System.Time** może różnić się od wartości w polu czasu zdarzenia i może spowodować wygenerowanie nieoczekiwanych wyników.

## <a name="handling-time-variation-with-substreams"></a>Zmiana czasu obsługi z podwrotami

Mechanizm generowania heurystycznego znaku wodnego opisany tutaj działa dobrze w większości przypadków, gdy czas jest głównie synchronizowany między różnymi nadawcami zdarzeń. Jednak w prawdziwym życiu, zwłaszcza w wielu scenariuszach IoT, system ma niewielką kontrolę przez zegar na nadawców zdarzeń. Nadawcy zdarzeń mogą być różnego rodzaju urządzeniami w terenie, być może w różnych wersjach sprzętu i oprogramowania.

Zamiast używać znaku wodnego globalnego do wszystkich zdarzeń w partycji wejściowej, Stream Analytics ma inny mechanizm o nazwie pod strumienie, aby pomóc. Można korzystać z podbiegów w zadaniu, pisząc kwerendę zadania, która używa klauzuli [**TIMESTAMP BY**](/stream-analytics-query/timestamp-by-azure-stream-analytics) i słowa kluczowego **OVER**. Aby wyznaczyć pod strumień, podaj nazwę kolumny klucza `deviceid`po słowie kluczowym **OVER,** na przykład , aby system stosuje zasady czasu przez tę kolumnę. Każdy poddys otrzymuje swój własny niezależny znak wodny. Mechanizm ten jest przydatny, aby umożliwić terminowe generowanie danych wyjściowych, gdy mamy do czynienia z dużymi przekrzywieniami zegara lub opóźnieniami sieci wśród nadawców zdarzeń.

Podstrumy są unikatowym rozwiązaniem dostarczanym przez usługę Azure Stream Analytics i nie są oferowane przez inne systemy przetwarzania danych strumieniowych. Usługa Stream Analytics stosuje okno tolerancji późnego nadejścia do zdarzeń przychodzących, gdy używane są podstrunnie. Ustawienie domyślne (5 sekund) jest prawdopodobnie zbyt małe dla urządzeń z rozbieżnymi znacznikami czasu. Zaleca się, aby rozpocząć z 5 minut i dokonać korekt zgodnie z ich schemat pochylenia zegara urządzenia.

## <a name="early-arriving-events"></a>Wczesne przybycie wydarzeń

Być może zauważyłeś inną koncepcję o nazwie okno wczesnego przyjazdu, które wygląda jak przeciwieństwo okna tolerancji późnego przybycia. Okno to jest ustalone na 5 minut i służy innemu celowi niż późny przyjazd.

Ponieważ usługa Azure Stream Analytics gwarantuje, że zawsze generuje pełne wyniki, można określić tylko **czas rozpoczęcia zadania** jako pierwszy czas wyjścia zadania, a nie czas wejściowy. Czas rozpoczęcia zadania jest wymagany, aby pełne okno zostało przetworzone, a nie tylko od środka okna.

Usługa Stream Analytics następnie wyprowadza czas rozpoczęcia ze specyfikacji kwerendy. Jednak ponieważ broker zdarzeń wejściowych jest indeksowany tylko według czasu przybycia, system musi przetłumaczyć czas rozpoczęcia zdarzenia na czas przybycia. System może rozpocząć przetwarzanie zdarzeń od tego momentu w brokera zdarzeń wejściowych. Z limitem wczesnego przylatywania, tłumaczenie jest proste. Jest to czas rozpoczęcia zdarzenia minus 5-minutowe okno wczesnego przybycia. To obliczenie oznacza również, że system porzuca wszystkie zdarzenia, które są postrzegane jako mające czas zdarzenia 5 minut ealier niż czas przybycia.

Ta koncepcja jest używana w celu zapewnienia, że przetwarzanie jest powtarzalne bez względu na to, od czego zaczniesz wyprowadzać dane wyjściowe. Bez takiego mechanizmu nie byłoby możliwe zagwarantowanie powtarzalności, jak twierdzi wiele innych systemów przesyłania strumieniowego.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Skutki uboczne zdarzeń zamawiania tolerancji czasu

Zadania usługi Stream Analytics mają kilka opcji **zamawiania zdarzeń.** Dwa można skonfigurować w witrynie Azure portal: ustawienie zdarzenia poza kolejnością (tolerancja poza kolejnością) i **zdarzenia, które przychodzą na koniec** opóźnienia (tolerancja **późnego** przybycia). Tolerancja **wczesnego przybycia** jest stała i nie można jej regulować. Te zasady czasu są używane przez usługi Stream Analytics w celu zapewnienia silnych gwarancji. Jednak te ustawienia mają czasami nieoczekiwane implikacje:

1. Przypadkowe wysyłanie zdarzeń, które są zbyt wcześnie.

   Wczesne zdarzenia nie powinny być wyprowadzane normalnie. Jest możliwe, że wczesne zdarzenia są wysyłane do danych wyjściowych, jeśli zegar nadawcy działa zbyt szybko. Wszystkie zdarzenia wczesnego przybycia są odrzucane, więc nie zobaczysz żadnego z nich z danych wyjściowych.

2. Wysyłanie starych zdarzeń do centrum zdarzeń do przetworzenia przez usługę Azure Stream Analytics.

   Podczas gdy stare wydarzenia mogą wydawać się nieszkodliwe na początku, ze względu na zastosowanie tolerancji późnego przybycia, stare wydarzenia mogą zostać usunięte. Jeśli zdarzenia są zbyt stare, **System.Timestamp** wartość jest zmieniana podczas pozyskiwania zdarzeń. Z powodu tego zachowania obecnie usługa Azure Stream Analytics jest bardziej odpowiednia dla scenariuszy przetwarzania zdarzeń w czasie zbliżonym do rzeczywistego, zamiast scenariuszy przetwarzania zdarzeń historycznych. Można ustawić **zdarzenia, które przychodzą późno** czas do największej możliwej wartości (20 dni), aby obejść to zachowanie w niektórych przypadkach.

3. Wyjścia wydają się być opóźnione.

   Pierwszy znak wodny jest generowany w obliczonym czasie: **maksymalny czas zdarzenia, który** system zaobserwował do tej pory, minus rozmiar okna tolerancji poza kolejnością. Domyślnie tolerancja poza kolejnością jest skonfigurowana na zero (00 minut i 00 sekund). Po ustawieniu go na wyższą, niezerową wartość czasu, pierwsze dane wyjściowe zadania przesyłania strumieniowego jest opóźniony o tę wartość czasu (lub większą) ze względu na pierwszy czas znaku wodnego, który jest obliczany.

4. Dane wejściowe są rzadkie.

   Gdy nie ma żadnych danych wejściowych w danej partycji, czas znaku wodnego jest obliczany jako **czas przybycia** minus okno tolerancji późnego przybycia. W rezultacie jeśli zdarzenia wejściowe są rzadkie i rozrzedzone, dane wyjściowe mogą być opóźnione o ten czas. Domyślna **wartość Zdarzeń, które przychodzą z opóźnieniem,** to 5 sekund. Należy spodziewać się pewne opóźnienie podczas wysyłania zdarzeń wejściowych po jednym na raz, na przykład. Opóźnienia mogą się pogorszyć, gdy ustawisz **zdarzenia, które przychodzą późno** okno do dużej wartości.

5. **System.Timestamp** wartość różni się od czasu w polu **czasu zdarzenia.**

   Jak opisano wcześniej, system dostosowuje czas zdarzenia przez okna tolerancji poza kolejnością lub tolerancji późnego przybycia. Wartość **sygnatury czasowej** zdarzenia jest korygowana, ale nie pole **czasu zdarzenia.**

## <a name="metrics-to-observe"></a>Metryki do obserwacji

Za pomocą danych [zadania usługi Stream Analytics](stream-analytics-monitoring.md)można zaobserwować wiele efektów tolerancji czasu zamawiania zdarzeń. Odpowiednie są następujące dane:

|Metryka  | Opis  |
|---------|---------|
| **Zdarzenia poza kolejnością** | Wskazuje liczbę zdarzeń odebranych poza kolejnością, które zostały usunięte lub podane skorygowany sygnatura czasowa. Na tę metrykę ma bezpośredni wpływ konfiguracja ustawienia **zdarzenia poza kolejnością** na stronie **Kolejność zdarzeń** w zadaniu w witrynie Azure portal. |
| **Zdarzenia późnego wprowadzania** | Wskazuje liczbę zdarzeń przybywających z opóźnieniem ze źródła. Ta metryka obejmuje zdarzenia, które zostały usunięte lub zostały skorygowane ich sygnatura czasowa. Na tę metrykę ma bezpośredni wpływ konfiguracja **zdarzeń, które przychodzą późno** ustawienie na stronie **kolejność zdarzeń** w zadaniu w witrynie Azure portal. |
| **Zdarzenia wczesnego wprowadzania** | Wskazuje liczbę zdarzeń przybywających wcześnie ze źródła, które zostały usunięte lub ich sygnatura czasowa została dostosowana, jeśli są one poza 5 minut wcześniej. |
| **Opóźnienie znaku wodnego** | Wskazuje opóźnienie zadania przetwarzania danych przesyłania strumieniowego. Więcej informacji można znaleźć w poniższej sekcji.|

## <a name="watermark-delay-details"></a>Szczegóły opóźnienia znaku wodnego

**Metryka opóźnienia znaku wodnego** jest obliczany jako czas zegara ścienne węzła przetwarzania minus największy znak wodny, który widział do tej pory. Aby uzyskać więcej informacji, zobacz [wpis w blogu z opóźnieniem znaku wodnego](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Może istnieć kilka powodów, dla których ta wartość metryki jest większa niż 0 w normalnej pracy:

1. Nieodłączne opóźnienie przetwarzania potoku przesyłania strumieniowego. Zwykle opóźnienie to jest nominalne.

2. Okno tolerancji poza kolejnością wprowadziło opóźnienie, ponieważ znak wodny jest zmniejszany o rozmiar okna tolerancji.

3. Okno późnego przybycia wprowadziło opóźnienie, ponieważ znak wodny jest zmniejszany o rozmiar okna tolerancji.

4. Pochylenie zegara węzła przetwarzania generującego metrykę.

Istnieje wiele innych ograniczeń zasobów, które mogą powodować spowolnienie potoku przesyłania strumieniowego. Metryka opóźnienia znaku wodnego może wzrosnąć z powodu:

1. Za mało zasobów przetwarzania w usłudze Stream Analytics do obsługi ilości zdarzeń wejściowych. Aby zwiększyć skalowanie zasobów, zobacz [Opis i dostosowanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md).

2. Za mało przepływności w brokerów zdarzeń wejściowych, więc są one ograniczane. Aby uzyskać możliwe rozwiązania, zobacz [Automatyczne skalowanie jednostek przepływności usługi Azure Event Hubs.](../event-hubs/event-hubs-auto-inflate.md)

3. Pochłaniacze danych wyjściowych nie są aprowiowane z wystarczającą pojemnością, więc są ograniczane. Możliwe rozwiązania różnią się znacznie w zależności od smaku używanej usługi wyjściowej.

## <a name="output-event-frequency"></a>Częstotliwość zdarzeń wyjściowych

Usługa Azure Stream Analytics używa postępu znaku wodnego jako jedynego wyzwalacza do tworzenia zdarzeń wyjściowych. Ponieważ znak wodny jest pochodną danych wejściowych, jest powtarzalny podczas odzyskiwania błędów, a także w inicjowanym przez użytkownika ponownym przetwarzaniu.

W przypadku korzystania z [agregatów okiennych](stream-analytics-window-functions.md)usługa generuje tylko dane wyjściowe na końcu okien. W niektórych przypadkach użytkownicy mogą chcieć zobaczyć częściowe agregaty generowane z okien. Agregaty częściowe nie są obecnie obsługiwane w usłudze Azure Stream Analytics.

W innych rozwiązaniach przesyłania strumieniowego zdarzenia wyjściowe mogą być materializowane w różnych punktach wyzwalania, w zależności od okoliczności zewnętrznych. Jest możliwe w niektórych rozwiązaniach, że zdarzenia wyjściowe dla danego przedziału czasu mogą być generowane wiele razy. W miarę dopracowywki wartości wejściowych wyniki zagregowane stają się dokładniejsze. Początkowo można było spekulować o wydarzeniach i z czasem być zmieniane. Na przykład, gdy określone urządzenie jest w trybie offline z sieci, szacowana wartość może być używana przez system. Później to samo urządzenie jest w trybie online do sieci. Następnie rzeczywiste dane zdarzenia mogą być uwzględnione w strumieniu wejściowym. Wyniki wyjściowe z przetwarzania tego przedziału czasowego daje dokładniejsze dane wyjściowe.

## <a name="illustrated-example-of-watermarks"></a>Ilustrowany przykład znaków wodnych

Poniższe ilustracje ilustrują postęp znaków wodnych w różnych okolicznościach.

W tej tabeli przedstawiono przykładowe dane, które są przedstawione na wykresie poniżej. Należy zauważyć, że czas zdarzenia i czas przybycia różnią się, czasami pasujące, a czasami nie.

| Czas wydarzenia | Godzina przyjazdu | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | urządzenie3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | urządzenie3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | urządzenie3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | urządzenie3

Na tej ilustracji stosuje się następujące tolerancje:

- Okna przed wylotem to 5 minut
- Późne przyjście do okna to 5 minut
- Okno ponownego zamówienia to 2 minuty

1. Ilustracja przedstawiająca znak wodny przechodzący przez te zdarzenia:

   ![Ilustracja ze znakiem wodnym usługi Azure Stream Analytics](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Godne uwagi procesy przedstawione na poprzedniej grafice:

   1. Pierwsze zdarzenie (device1) i drugie zdarzenie (device2) mają wyrównane czasy i są przetwarzane bez regulacji. Znak wodny postępuje w każdym zdarzeniu.

   2. Podczas przetwarzania trzeciego zdarzenia (device1) czas przybycia (12:11) poprzedza czas zdarzenia (12:17). Wydarzenie dotarło 6 minut wcześniej, więc wydarzenie zostaje przerwane z powodu 5-minutowej tolerancji wczesnego przybycia.

      Znak wodny nie rozwija się w tym przypadku wczesnego zdarzenia.

   3. Czwarte zdarzenie (device3) i piąte zdarzenie (device1) mają wyrównane czasy i są przetwarzane bez regulacji. Znak wodny postępuje w każdym zdarzeniu.

   4. Podczas przetwarzania szóstego zdarzenia (device3) czas przybycia (12:17) i czas zdarzenia (12:12) jest poniżej poziomu znaku wodnego. Czas zdarzenia jest dostosowany do poziomu znaku wody (12:17).

   5. Po przetworzyniu dwunastego zdarzenia (device3) czas przybycia (12:27) jest 6 minut przed czasem zdarzenia (12:21). Obowiązują zasady dotyczące późnego przyjazdu. Czas zdarzenia jest korygowany (12:22), który znajduje się powyżej znaku wodnego (12:21), więc nie jest stosowana dalsza korekta.

2. Druga ilustracja postępu znaku wodnego bez polityki wczesnego przybycia:

   ![Usługa Azure Stream Analytics brak wczesnej ilustracji znaku wodnego zasad](media/stream-analytics-time-handling/watermark-graph-2.png)

   W tym przykładzie nie są stosowane żadne zasady wczesnego przybycia. Odstające zdarzenia, które przybywają wcześnie podnieść znak wodny znacznie. Zwróć uwagę, że trzecie zdarzenie (deviceId1 w czasie 12:11) nie zostanie usunięte w tym scenariuszu, a znak wodny jest wywoływany do 12:15. Czwarty czas zdarzenia jest dostosowany do przodu 7 minut (12:08 do 12:15) w wyniku.

3. Na końcowej ilustracji używane są podprądy (OVER the DeviceId). Wiele znaków wodnych są śledzone, jeden na strumień. Jest mniej zdarzeń z ich czasy dostosowane w wyniku.

   ![Ilustracja ze znakiem wodnym podstrumażu usługi Azure Stream Analytics](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Następne kroki

- [Zagadnienia dotyczące zamówień zdarzeń usługi Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md)
- [Dane zadań usługi Stream Analytics](stream-analytics-monitoring.md)
