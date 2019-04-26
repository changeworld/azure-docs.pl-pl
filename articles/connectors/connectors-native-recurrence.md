---
title: Planowanie i uruchamianie automatycznych zadań i przepływów pracy z usługą Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań zaplanowanych, jak i cykliczne z łącznikiem cyklu w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
tags: connectors
ms.topic: article
ms.date: 01/08/2019
ms.openlocfilehash: eb22539d1f433e396935f82e4cb3786d5699d21a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60447517"
---
# <a name="create-and-run-recurring-tasks-and-workflows-with-azure-logic-apps"></a>Tworzenie i uruchamianie zadań cyklicznych i przepływów pracy z usługą Azure Logic Apps

Aby zaplanować działania, obciążenia lub procesy, które regularnego uruchamiania, tworzenia przepływu pracy aplikacji logiki, która rozpoczyna się od **harmonogram — cyklicznie** [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts). Można ustawić datę i godzinę uruchamiania przepływu pracy oraz do wykonywania zadań, takich jak te przykłady i inne harmonogramu cyklu:

* Pobieranie danych wewnętrznych: [Uruchamiania procedur składowanych SQL](../connectors/connectors-create-api-sqlazure.md) każdego dnia.
* Pobierz dane zewnętrzne: Ściągnij raporty pogodowe z agencji NOAA co 15 minut.
* Dane raportu: Wyślij wiadomość e-mail jest podsumowanie wszystkich zamówień przekracza określoną ilością w ostatnim tygodniu.
* Przetwarzanie danych: Kompresuj współczesnych przekazanych obrazów w każdy dzień tygodnia, poza godzinami szczytu.
* Wyczyść dane: Usuń wszystkie tweety starsze niż trzy miesiące.
* Archiwizowanie danych: Wypchnij faktur usługa Kopia zapasowa co miesiąc.

Ten wyzwalacz obsługuje wielu wzorców, na przykład:

* Natychmiastowe uruchomienie i Powtórz co *n* liczbę sekund, minuty, godziny, dni, tygodnie lub miesiące.
* Uruchomienia w określonym czasie, a następnie uruchomisz i Powtórz co *n* liczbę sekund, minuty, godziny, dni, tygodnie lub miesiące.
* Uruchom i powtarzania na jeden lub więcej razy każdego dnia, na przykład 8:00 do 17:00:00.
* Uruchom i Powtórz co tydzień, ale tylko dla określonych dni, takich jak soboty i niedziele.
* Uruchom i Powtórz co tydzień, ale tylko dla określonych dni i godziny, np. od poniedziałku do piątku w 8:00 do 17:00:00.

Po każdym aktywowaniu wyzwalacza cyklu, Logic Apps tworzy i uruchamia nowe wystąpienie przepływu pracy aplikacji logiki. 

Aby wyzwolić aplikacji logiki i uruchomić tylko jeden raz w przyszłości, zobacz [wykonywania zadania tylko jeden raz](#run-once) w dalszej części tego tematu.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). W przeciwnym razie możesz [skorzystać z subskrypcji z płatnością zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/pricing/purchase-options/).

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="add-a-recurrence-trigger-to-your-logic-app"></a>Dodaj wyzwalacz cykliczny aplikacji logiki

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Tworzenie pustej aplikacji logiki lub Dowiedz się, [sposób tworzenia pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Gdy zostanie wyświetlony Projektant aplikacji logiki, w polu wyszukiwania, wybierz **wszystkich**. W polu wyszukiwania wprowadź ciąg "cyklicznie" jako filtr. Z listy wyzwalaczy wybierz następujący wyzwalacz: **Cykl - harmonogramu** 

   ![Wybieranie wyzwalacza "Harmonogram — cykl"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

   Ten wyzwalacz jest teraz pierwszy krok w aplikacji logiki.

3. Ustaw odstęp czasu i częstotliwość cyklu. W tym przykładzie ustaw właściwości tak, aby uruchomić przepływ pracy, co tydzień. 

   ![Ustaw interwał i częstotliwość](./media/connectors-native-recurrence/recurrence-trigger-details.png)

4. Aby uzyskać więcej opcji planowania, wybierz **Pokaż opcje zaawansowane**. 

   ![Więcej opcji](./media/connectors-native-recurrence/recurrence-trigger-more-options.png)

5. Teraz można ustawić następujące opcje: 

   * Ustaw datę i godzinę rozpoczęcia dla aktywowanie wyzwalacza. 
   Jeśli określisz, datę i godzinę, można także zastosować strefy czasowej. 

   * Jeśli wybierzesz opcję "Day" lub "Week" częstotliwości, można wybrać określonych godzinach w cyklu. 

   * Jeśli wybierzesz opcję "Week", możesz wybrać określone dni tygodnia zbyt.
   
   ![Zaawansowane opcje planowania](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   Na przykład załóżmy, że dziś jest poniedziałek, 4 września 2017 r. 
   Następujący wyzwalacz cykliczny nie zostanie wyzwolony *wszelkie wcześniej* niż data rozpoczęcia i godzina, czyli każdy poniedziałek, 18 września 2017 r. o 8:00 czasu PST. 
   Jednak harmonogram cyklu jest ustawiony na 10:30:00, 12:30 PM, a 2:30 PM tylko poniedziałek. Aby po raz pierwszy, aktywuje się i tworzy wystąpienie przepływu pracy aplikacji logiki wynosi 10:30:00. 
   Aby dowiedzieć się więcej na temat sposobu uruchamiania godziny pracy, zobacz te [start przykłady czasu](#start-time).
   Przyszłych uruchomień odbywa się na 12:30:00 i 14:30:00 w dniu. 
   Każdy cykl tworzy własne wystąpienie przepływu pracy. Po tym całego harmonogramu powtarza się przez ponownie poniedziałku. 
   [*Jakie są inne wystąpienia przykładu?*](#example-recurrences)

   ![Zaawansowany przykład planowania](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Wyzwalacz zawiera cykl określony w wersji zapoznawczej tylko w przypadku wybrania częstotliwości "Day" lub "Week".
   
6. Teraz w rozpoczęciu pracy pozostałej z akcjami lub przepływu sterowania instrukcji. Aby uzyskać więcej akcji, które można dodać, zobacz [łączników](../connectors/apis-list.md). 

## <a name="trigger-details"></a>Szczegóły wyzwalacza

Możesz skonfigurować te właściwości dla wyzwalacza.

| Name (Nazwa) | Wymagane | Nazwa właściwości | Type | Opis | 
|----- | -------- | ------------- | ---- | ----------- | 
| **Częstotliwość** | Yes | frequency | String | Jednostka czasu cyklu: **Drugi**, **minutę**, **godzinę**, **dzień**, **tydzień**, lub **miesiąca** | 
| **Interwał** | Yes | interval | Liczba całkowita | Dodatnia liczba całkowita, która opisuje, jak często przepływu pracy jest uruchamiana na podstawie częstotliwości. <p>Domyślny interwał wynosi 1. Poniżej przedstawiono minimalne i maksymalne odstępach czasu: <p>-Miesiąc: 1 – 16 miesięcy </br>-Dzień: 1 – 500 dni </br>-Godzinny: 1-12 000 godzin </br>-Minutowy: 1 72,000 min </br>-Sekundowych: 1 9,999,999 sekundy<p>Na przykład jeśli interwał wynosi 6 i częstotliwość wynosi "Month", cykl jest co 6 miesięcy. | 
| **Strefa czasowa** | Nie | timeZone | String | Ma zastosowanie tylko po określeniu godziny rozpoczęcia, ponieważ ten wyzwalacz nie zaakceptuje [przesunięcie czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Wybierz strefę czasową, który chcesz zastosować. | 
| **Godzina rozpoczęcia** | Nie | startTime | String | Podaj godzinę rozpoczęcia w następującym formacie: <p>RRRR-MM-Ddtgg w przypadku wybrania strefy czasowej <p>— lub — <p>RRRR-MM-Ddtgg, jeśli nie zaznaczysz strefy czasowej <p>Na przykład jeśli chcesz 18 września 2017 r. o 14:00, następnie określ "2017-09-18T14:00:00" i wybierz strefę czasową, np. czasu pacyficznego. Alternatywnie można wskazać "2017-09-18T14:00:00Z" bez strefy czasowej. <p>**Uwaga:** Ten czas rozpoczęcia musi stosować [specyfikacji czasu daty ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [format daty i godziny UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcie czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie zaznaczysz strefy czasowej, należy dodać litera "Z" na końcu bez żadnych spacji. Ta "Z" odnosi się do równowartości [morskich czasu](https://en.wikipedia.org/wiki/Nautical_time). <p>W przypadku prostych harmonogramów czas rozpoczęcia przypada po pierwszym wystąpieniu, natomiast w przypadku harmonogramów złożonych wyzwalacz nie zostanie wyzwolony wszelkie wcześniej niż czas rozpoczęcia. [*Jakie są sposoby, czy można używać daty rozpoczęcia i godzinę?*](#start-time) | 
| **W tych dniach** | Nie | weekDays | Ciąg lub tablicę ciągów | Jeśli wybierzesz opcję "Week", można wybrać jeden lub więcej dni, gdy zachodzi potrzeba uruchomienia przepływu pracy: **Poniedziałek**, **wtorek**, **środę**, **czwartek**, **piątek**, **sobota**, i **Niedziela** | 
| **W tych godzinach** | Nie | hours | Liczba całkowita lub tablicy liczb całkowitych | Jeśli wybierzesz opcję "Day" lub "Week", można wybrać jeden lub więcej liczb całkowitych z zakresu od 0 do 23 godzin dnia, kiedy chcesz uruchomić przepływ pracy. <p>Na przykład jeśli określisz "10", "12" i "14", możesz uzyskać 10 AM, 12 PM i 14: 00 jako znaki godzinę. | 
| **W tych minutach** | Nie | minutes | Liczba całkowita lub tablicy liczb całkowitych | Jeśli wybierzesz opcję "Day" lub "Week", można wybrać jeden lub więcej liczb całkowitych z zakresu od 0 do 59 jako minuty, godziny, kiedy chcesz uruchomić przepływ pracy. <p>Na przykład "30" można określić jako znacznik minutę i godziny, dnia, korzystając z poprzedniego przykładu, możesz uzyskać 10:30:00, 12:30:00 i 14:30:00. | 
||||| 

## <a name="json-example"></a>Przykład kodu JSON

Oto przykład [definicji wyzwalacza cyklu](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger):

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="faq"></a>Często zadawane pytania

<a name="run-once"></a>

**Pyt.:** Co zrobić, jeśli chcę, aby uruchomić aplikację logiki w przyszłości tylko jeden raz? </br>
**Odp.:** Aby wyzwolić aplikacji logiki i uruchomić jeden raz bez cyklicznego, można użyć **harmonogramu: Uruchom raz zadania** szablonu. Po utworzeniu nowej aplikacji logiki, ale przed otwarciem projektanta aplikacji logiki w obszarze **szablony** sekcji z **kategorii** listy wybierz pozycję **harmonogram**, a następnie wybierz pozycję szablon:

![Wybierz pozycję "harmonogramu: Uruchamianie zadania raz"szablonu](./media/connectors-native-recurrence/choose-run-once-template.png)

Lub, jeśli używasz szablonu pustej aplikacji logiki, uruchomić swoją aplikację logiki, za pomocą **zostanie odebrane żądanie po HTTP - żądania** wyzwalacza. Czas rozpoczęcia tego wyzwalacza można przekazać jako parametr. W następnym kroku należy dodać **opóźnienie do — Planowanie** akcji i podaj czas podczas uruchamiania następnej akcji.

<a name="example-recurrences"></a>

**Pyt.:** Jakie są inne harmonogramy cyklu przykład? </br>
**Odp.:** Oto więcej przykładów:

| Cykl | Interval | Częstotliwość | Godzina rozpoczęcia | W tych dniach | W tych godzinach | W tych minutach | Uwaga |
| ---------- | -------- | --------- | ---------- | ------------- | -------------- | ---------------- | ---- |
| Uruchamiany co 15 minut (nie Data i godzina rozpoczęcia) | 15 | Minuta | {Brak} | {unavailable} | {Brak} | {Brak} | Ten harmonogram jest uruchamiany natychmiast, a następnie oblicza przyszłe powtórzenia na podstawie ostatniego czasu wykonywania. | 
| Uruchamiany co 15 minut (przy użyciu Data i godzina rozpoczęcia) | 15 | Minuta | *startDate*T*startTime*Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram nie zaczyna się *wszelkie wcześniej* niż określona data rozpoczęcia i godzina, a następnie oblicza przyszłe powtórzenia na podstawie ostatniego czasu wykonywania. | 
| Uruchamiany co godzinę, o pełnej godzinie (przy użyciu Data i godzina rozpoczęcia) | 1 | Godzina | *Oprócz parametru startDate*Thh:00:00Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram nie zaczyna się *wszelkie wcześniej* niż określona data i godzina rozpoczęcia. Przyszłe powtórzenia uruchamiany co godzinę w pozycji "00" minut. <p>Jeśli częstotliwość wynosi "Week" lub "Month", ten harmonogram jest uruchamiany odpowiednio tylko raz w tygodniu lub raz na miesiąc. | 
| Uruchamiany co godzinę, codziennie (nie Data i godzina rozpoczęcia) | 1 | Godzina | {Brak} | {unavailable} | {Brak} | {Brak} | Ten harmonogram jest uruchamiany natychmiast i oblicza przyszłe powtórzenia na podstawie ostatniego czasu wykonywania. <p>Jeśli częstotliwość wynosi "Week" lub "Month", ten harmonogram jest uruchamiany odpowiednio tylko raz w tygodniu lub raz na miesiąc. | 
| Uruchamiany co godzinę, codziennie (z Data i godzina rozpoczęcia) | 1 | Godzina | *startDate*T*startTime*Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram nie zaczyna się *wszelkie wcześniej* niż określona data rozpoczęcia i godzina, a następnie oblicza przyszłe powtórzenia na podstawie ostatniego czasu wykonywania. <p>Jeśli częstotliwość wynosi "Week" lub "Month", ten harmonogram jest uruchamiany odpowiednio tylko raz w tygodniu lub raz na miesiąc. | 
| Uruchom co 15 minut po pełnej godzinie, co godzinę (przy użyciu Data i godzina rozpoczęcia) | 1 | Godzina | *startDate*T00:15:00Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram nie zaczyna się *wszelkie wcześniej* niż określona data rozpoczęcia i czas uruchamiania o 00:15, 1:15:00, 2:15:00 i tak dalej. | 
| Uruchamiany co 15 minut po pełnej godzinie, co godzinę (nie daty i godziny rozpoczęcia) | 1 | Dzień | {Brak} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Ten harmonogram jest uruchamiany o 00:15, 1:15:00, 2:15:00, i tak dalej. Ponadto ten harmonogram jest odpowiednikiem częstotliwości "Hour" i czas rozpoczęcia "15" minut. | 
| Uruchamiany co 15 minut od znacznika 15-minutowy (nie Data i godzina rozpoczęcia) | 1 | Dzień | {Brak} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ten harmonogram nie zaczyna się do czasu następnego określony znak 15 minut. | 
| Uruchamiany o 8:00:00 każdego dnia (nie Data i godzina rozpoczęcia) | 1 | Dzień | {Brak} | {unavailable} | 8 | {Brak} | Ten harmonogram jest uruchamiany o 8:00 każdego dnia na podstawie określonego harmonogramu. | 
| Uruchamiany o 8:00:00 każdego dnia (o datę i godzinę rozpoczęcia) | 1 | Dzień | *Oprócz parametru startDate*T08:00:00Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram uruchamia 8:00:00 każdego dnia, w oparciu o określonej godzinie. | 
| Uruchamiany o 8:30:00 każdego dnia (nie Data i godzina rozpoczęcia) | 1 | Dzień | {Brak} | {unavailable} | 8 | 30 | Ten harmonogram jest uruchamiany o 8:30:00 każdego dnia na podstawie określonego harmonogramu. | 
| Uruchamiany o 8:30:00 każdego dnia (o datę i godzinę rozpoczęcia) | 1 | Dzień | *Oprócz parametru startDate*T08:30:00Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram jest uruchamiany w określona data rozpoczęcia o 8:30:00. | 
| Uruchamiany o 8:30:00 i 16:30:00 każdego dnia | 1 | Dzień | {Brak} | {unavailable} | 8, 16 | 30 | | 
| Uruchamiany o 8:30:00, 8:45:00, 16:30:00 i 16:45:00 każdego dnia | 1 | Dzień | {Brak} | {unavailable} | 8, 16 | 30, 45 | | 
| Uruchom w każdą sobotę o godzinie 17 (nie Data i godzina rozpoczęcia) | 1 | Tydzień | {Brak} | "Sobota" | 17 | 00 | Ten harmonogram jest uruchamiany w każdą sobotę o godzinie 17:00. | 
| Uruchom w każdą sobotę o godzinie 17 (przy użyciu Data i godzina rozpoczęcia) | 1 | Tydzień | *Oprócz parametru startDate*T17:00:00Z | "Sobota" | {Brak} | {Brak} | Ten harmonogram nie zaczyna się *wszelkie wcześniej* niż określona data i godzina rozpoczęcia, w tym przypadku 9 września 2017 r. o godzinie 17:00. Przyszłe powtórzenia uruchamiania w każdą sobotę o godzinie 17:00. | 
| Uruchomienia każdy wtorek, czwartek o godzinie 17: 00 | 1 | Tydzień | {Brak} | "Wtorek", "Czwartek" | 17 | {Brak} | Ten harmonogram jest uruchamiany każdy wtorek i czwartek o godzinie 17:00. | 
| Uruchamiany co godzinę poza godzinami pracy | 1 | Tydzień | {Brak} | Wybierz wszystkie dni, z wyjątkiem soboty i niedziele. | Wybierz godzin dnia, którym chcesz. | Zaznacz wszystkie minut godzinę, która ma. | Na przykład jeśli godziny pracy są 8:00 AM do 5:00 PM, następnie wybierz pozycję "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" jako godzin dnia. <p>Godziny pracy w przypadku 8:30:00 do 5:30 PM, należy wybrać ostatnich godzin dnia oraz "30" jako minuty godziny. | 
| Uruchom jeden raz każdego dnia w weekendy | 1 | Tydzień | {Brak} | "Sobota", "Niedziela" | Wybierz godzin dnia, którym chcesz. | Wybierz wszystkie minuty godziny zgodnie z potrzebami. | Ten harmonogram jest uruchamiany co sobota i niedziela z określonym harmonogramem. | 
| Uruchamiany co 15 minut, co dwa tygodnie w poniedziałek tylko | 2 | Tydzień | {Brak} | "Poniedziałek" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ten harmonogram jest uruchamiany co drugi poniedziałek pozycji co 15 minut. | 
| Uruchamiany co godzinę przez jeden dzień w miesiącu | 1 | Miesiąc | {patrz Uwaga} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {patrz Uwaga} | Jeśli nie określisz Data i godzina rozpoczęcia tego harmonogramu używa daty i godziny. Aby kontrolować minuty dla harmonogramu cyklu, określ minuty godziny, czas rozpoczęcia lub czas utworzenia. Na przykład jeśli czas rozpoczęcia lub godzina utworzenia 8:25:00, ten harmonogram jest wykonywany o 8:25:00, 9:25:00, 10:25:00, i tak dalej. | 
||||||||| 

<a name="start-time"></a>

**Pyt.:** Jakie są sposoby, czy można używać daty rozpoczęcia i godzinę? </br>
**Odp.:** Poniżej przedstawiono niektóre wzorce, które pokazują sposób kontrolowania cykli z użyciem Data i godzina rozpoczęcia i jak aparat usługi Logic Apps wykonuje te cykli:

| Godzina rozpoczęcia | Cykl bez harmonogramu | Cykl z harmonogramem | 
| ---------- | --------------------------- | ------------------------ | 
| {Brak} | Uruchamia natychmiast pierwszy obciążenia. <p>Uruchamia przyszłych obciążeń na podstawie ostatniego czasu wykonywania. | Uruchamia natychmiast pierwszy obciążenia. <p>Uruchamia przyszłych obciążeń na podstawie określonego harmonogramu. | 
| Godzina rozpoczęcia w przeszłości | Oblicza czas wykonywania na podstawie określonym czasie rozpoczęcia i czas odrzucenia ostatnie uruchomienie. Uruchamia pierwszy obciążenie w przyszłości dalej, czas wykonywania. <p>Uruchamia przyszłych obciążeń na podstawie obliczeń z ostatniego czasu wykonywania. <p>Aby uzyskać więcej informacji zobacz przykład pod tą tabelą. | Uruchamia obciążenia pierwszego *nie wcześniej* niż czas rozpoczęcia opierają na harmonogramie obliczonym na podstawie czasu rozpoczęcia. <p>Uruchamia przyszłych obciążeń na podstawie określonego harmonogramu. <p>**Uwaga:** Jeśli Określ cykl z harmonogramem, ale nie określono, godziny lub minuty dla harmonogramu, następnie przyszłych czas wykonywania są obliczane przy użyciu godzin lub minut, odpowiednio, od momentu pierwszego uruchomienia. | 
| Czas rozpoczęcia obecnie i w przyszłości | Uruchamia pierwszy obciążenia o określonej godzinie. <p>Uruchamia przyszłych obciążeń na podstawie obliczeń z ostatniego czasu wykonywania. | Uruchamia obciążenia pierwszego *nie wcześniej* niż czas rozpoczęcia opierają na harmonogramie obliczonym na podstawie czasu rozpoczęcia. <p>Uruchamia przyszłych obciążeń na podstawie określonego harmonogramu. <p>**Uwaga:** Jeśli Określ cykl z harmonogramem, ale nie określono, godziny lub minuty dla harmonogramu, następnie przyszłych czas wykonywania są obliczane przy użyciu godzin lub minut, odpowiednio, od momentu pierwszego uruchomienia. | 
||||

**Przykład dotyczący ostatnich czas rozpoczęcia z cyklem, ale bez harmonogramu** 

| Godzina rozpoczęcia | Bieżący czas | Cykl | Harmonogram |
| ---------- | ------------ | ---------- | -------- | 
| 2017-09-**07**T14:00:00Z | 2017-09-**08**T13:00:00Z | Co 2 dni | {Brak} | 
||||| 

W tym scenariuszu aplikacji logiki aparatu oblicza uruchomić razy na podstawie czasu uruchomienia odrzuca upłynął czas wykonywania i używa dalej przyszłość godzina rozpoczęcia dla pierwszego uruchomienia. Po tym pierwszym uruchomieniu przyszłych uruchomień są oparte na harmonogramie obliczonym na podstawie czasu rozpoczęcia. Oto jak wygląda cykl:

| Godzina rozpoczęcia | Uruchom po raz pierwszy | Przyszłość czasy uruchomienia | 
| ---------- | ------------ | ---------- | 
| 2017-09 -**07** o 14:00 | 2017-09 -**09** o 14:00 | 2017-09 -**11** o 14:00 </br>2017-09 -**13** o 14:00 </br>2017-09 -**15** o 14:00 </br>i tak dalej...
||||

Więc w tym scenariuszu, niezależnie od tego, jak daleko w przeszłości, określ rozpoczęcie czasu, na przykład 2017-09 -**05** o 14:00 lub 2017-09 -**01** o 14:00, pierwszej czas wykonywania jest taka sama.

## <a name="next-steps"></a>Kolejne kroki

* [Wyzwalacze i akcje przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)
* [Łączniki](../connectors/apis-list.md)
