---
title: Harmonogram zadań i regularnie uruchamiania przepływów pracy - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie i planowanie regularnie uruchomionych zadań, działania przepływów pracy, procesów i obciążeń z usługą logic apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 3bd396355681cdde486cfbea7004c9c1aece09da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296791"
---
# <a name="create-and-schedule-regularly-running-tasks-with-azure-logic-apps"></a>Tworzenie i planowanie regularnie uruchomionych zadań za pomocą usługi Azure Logic Apps

Aby zaplanować zadania, akcje, obciążeń lub procesy, które regularnego uruchamiania, można utworzyć przepływu pracy aplikacji logiki, który rozpoczyna się od **harmonogram - cyklu** [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts). Z tego wyzwalacza można ustawić datę i godzinę uruchomienia cyklu i harmonogram cyklu do wykonywania zadań, takich jak te przykłady i inne:

* Pobierz dane wewnętrzne: [uruchomić procedurę Przechowywaną](../connectors/connectors-create-api-sqlazure.md) każdego dnia.
* Pobierz dane zewnętrzne: ściąganie pogody z NOAA co 15 minut.
* Dane raportu: E-mail podsumowania dla wszystkich zamówień przekracza określoną kwotę w zeszłym tygodniu.
* Przetwarzaj dane: kompresowanie dzisiaj przesłał obrazów każdy dzień tygodnia poza godzinami szczytu.
* Czyszczenie danych: Usuń wszystkie tweetów starsze niż trzy miesiące.
* Archiwizowanie danych: Push faktury do usługi Kopia zapasowa co miesiąc.

Wyzwalacz obsługuje wielu wzorców, na przykład:

* Natychmiastowe uruchomienie i Powtórz co *n* liczba sekundy, minuty, godziny, dni, tygodnie lub miesiące.
* Uruchom w określonym czasie, a następnie uruchom i Powtórz co *n* liczba sekund, minuty, godziny, dni, tygodnie lub miesiące.
* Uruchom i powtórz na jeden lub więcej razy dziennie, na przykład na 8:00 AM a 17:00.
* Uruchom i Powtórz co tydzień, ale tylko dla określonych dni, takich jak sobota i niedziela.
* Uruchom i Powtórz co tydzień, ale tylko dla określonych dni i godziny, na przykład od poniedziałku do piątku w 8:00 AM a 17:00.

Podczas cyklu wyzwalacza każdorazowo, Logic Apps tworzy i uruchamia nowe wystąpienie przepływu pracy aplikacji logiki.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [rozpocząć pracę z bezpłatnym kontem platformy Azure](https://azure.microsoft.com/free/). W przeciwnym razie możesz [skorzystać z subskrypcji z płatnością zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/pricing/purchase-options/).

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="add-a-recurrence-trigger-to-your-logic-app"></a>Dodawanie do aplikacji logiki wyzwalacza cyklu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Tworzenie aplikacji logiki puste lub Dowiedz się [sposób tworzenia aplikacji logiki puste](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Gdy zostanie wyświetlona projektanta aplikacji logiki w polu wyszukiwania wprowadź "cyklu" jako filtr. Wybierz **harmonogram - cyklu** wyzwalacza. 

   ![Harmonogram - wyzwalacza cyklu](./media/connectors-native-recurrence/add-recurrence-trigger.png)

   Tego wyzwalacza jest teraz pierwszym etapem aplikacji logiki.

3. Ustaw odstęp czasu i częstotliwość cyklu. W tym przykładzie ustaw te właściwości, aby uruchomić przepływ pracy, co tydzień. 

   ![Ustaw interwał i częstotliwości](./media/connectors-native-recurrence/recurrence-trigger-details.png)

4. Aby uzyskać więcej opcji harmonogramu, wybierz **Pokaż zaawansowane opcje**. 

   ![Więcej opcji](./media/connectors-native-recurrence/recurrence-trigger-more-options.png)

5. Teraz można ustawić następujące opcje: 

   * Ustaw datę i godzinę dla wyzwalania wyzwalacza. 
   Należy określić datę i godzinę, możesz również stosować strefę czasową. 

   * W przypadku wybrania "Day" lub "Tydzień" częstotliwości, można wybrać określonych godzin czasu cyklu. 

   * W przypadku wybrania "Tydzień", można wybrać określone dni tygodnia zbyt.
   
   ![Zaawansowane opcje planowania](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   Na przykład załóżmy, że dzisiaj jest poniedziałek, 4 września 2017. 
   Następujące wyzwalacza cyklu nie wyzwalać *żadnych wcześniej* niż data rozpoczęcia i godzina, czyli na 8:00 AM PST poniedziałek, 18 września 2017 r. 
   Jednak w przypadku harmonogramu powtarzającego jest ustawiony dla godziny 10:30, 12:30:00 i 2:30 będzie tylko poniedziałku. Dlatego po raz pierwszy czy wyzwalacz generowane i tworzy wystąpienie przepływu pracy aplikacji logiki wynosi 10:30 AM. 
   Aby dowiedzieć się więcej na temat sposobu uruchamiania czas pracy, zobacz te [start przykłady czasu](#start-time).
   Przyszłych przebiegów odbywa się na 12:30:00 i 14:30:00 w dniu. 
   Każdy cykl tworzy własne wystąpienie przepływu pracy. Po wykonaniu tej całego harmonogramu powtarza całego ponownie poniedziałku. 
   [*Co to są inne wystąpienia przykład?*](#example-recurrences)

   ![Przykład planowania zaawansowane](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Wyzwalacz pokazuje podglądu dla określonego cykl tylko po wybraniu częstotliwość "Day" lub "Tydzień".
   
6. Teraz skompilować pozostałych przepływu pracy z akcjami lub instrukcji sterowania przepływem. Więcej akcji, które można dodać, zobacz [łączniki](../connectors/apis-list.md). 

## <a name="trigger-details"></a>Szczegóły wyzwalacza

Można skonfigurować te właściwości dla wyzwalacza cyklu.

| Name (Nazwa) | Wymagane | Nazwa właściwości | Typ | Opis | 
|----- | -------- | ------------- | ---- | ----------- | 
| **Częstotliwość** | Yes | frequency | Ciąg | Jednostka czasu w cyklu: **drugi**, **minutę**, **godzina**, **dzień**, **tygodnia**, lub  **Miesiąc** | 
| **Interwał** | Yes | interval | Liczba całkowita | Dodatnia liczba całkowita, w tym artykule opisano, jak często uruchamia przepływ pracy na podstawie częstotliwości. <p>Domyślny interwał wynosi 1. Poniżej przedstawiono minimalne i maksymalne odstępach czasu: <p>-Miesięczny: 1-16 miesięcy </br>-Dniowego: 1-500 dni </br>-Godzinnym: 1-12 000 godzin </br>-Minutowy: 1-72,000 minut </br>-Drugi: 1-9,999,999 sekund<p>Na przykład jeśli interwał to 6 i częstotliwości jest "Miesiąc", cykl jest co 6 miesięcy. | 
| **Strefa czasowa** | Nie | timeZone | Ciąg | Dotyczy tylko po określeniu godziny rozpoczęcia, ponieważ wyzwalacz nie akceptuje [przesunięcie UTC](https://en.wikipedia.org/wiki/UTC_offset). Wybierz strefę czasową, który chcesz zastosować. | 
| **Godzina rozpoczęcia** | Nie | startTime | Ciąg | Podaj godzinę rozpoczęcia w następującym formacie: <p>RRRR-MM-Ddtgg w przypadku wybrania strefy czasowej <p>— lub — <p>RRRR-MM-Ddtgg, jeśli nie zaznaczysz strefy czasowej <p>Tak na przykład, jeśli chcesz 18 września 2017 godzinie 2:00, określ "2017-09-18T14:00:00" i wybierz strefę czasową, takie jak czas pacyficzny. Alternatywnie można wskazać "2017-09-18T14:00:00Z" bez strefę czasową. <p>**Uwaga:** ta godzina rozpoczęcia musi występować po [ISO 8601 daty czasu specyfikacji](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [format daty i godziny UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcie UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie zaznaczysz strefy czasowej, należy dodać litery "Z" na końcu bez spacji. To "Z" odwołuje się do jego odpowiednik [milową czas](https://en.wikipedia.org/wiki/Nautical_time). <p>Proste harmonogramów, godzina rozpoczęcia jest pierwsze wystąpienie, natomiast w przypadku złożonych harmonogramy wyzwalacz nie wyzwalać żadnych wcześniej niż czas rozpoczęcia. [*Jakie są sposoby, że można używać datę i godzinę?*](#start-time) | 
| **W tych dniach** | Nie | weekDays | Ciąg lub tablicę ciągów | Jeśli wybierzesz "Tydzień", można wybrać co najmniej jeden dzień, jeśli chcesz uruchomić przepływ pracy: **poniedziałek**, **wtorek**, **środa**, **czwartek** , **Piątek**, **sobota**, i **niedziela** | 
| **W tych godzinach** | Nie | hours | Liczba całkowita lub tablicy liczba całkowita | W przypadku wybrania "Day" lub "Tydzień", można wybrać jeden lub więcej liczb całkowitych od 0 do 23 godzin dnia, gdy chcesz uruchomić przepływ pracy. <p>Na przykład jeśli określisz "10", "12" i "14" możesz uzyskać 10 AM, 12 PM i 14: 00 jako znaki godzinę. | 
| **W tych minutach** | Nie | minutes | Liczba całkowita lub tablicy liczba całkowita | W przypadku wybrania "Day" lub "Tydzień", można wybrać co najmniej jeden liczby całkowite z zakresu od 0 do 59 jako minuty, godziny, jeśli chcesz uruchomić przepływ pracy. <p>Na przykład "30" można określić jako minuta znaku i w poprzednim przykładzie, godziny, dnia, możesz uzyskać godziny 10:30, 12:30:00 i 14:30:00. | 
||||| 

## <a name="json-example"></a>Przykład JSON

Oto przykład [definicję wyzwalacza cyklu](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger):

``` json
{
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
}
```

## <a name="faq"></a>Często zadawane pytania

<a name="example-recurrences"></a>

**Pytanie:** co to są inne przykład cyklu harmonogramy? </br>
**Odpowiedź:** poniżej przedstawiono więcej przykładów:

| Cykl | Interwał | Częstotliwość | Godzina rozpoczęcia | W tych dniach | W tych godzinach | W tych minutach | Uwaga |
| ---------- | -------- | --------- | ---------- | ------------- | -------------- | ---------------- | ---- |
| Uruchom co 15 minut (nie datę i godzinę rozpoczęcia) | 15 | Minuta | {Brak} | {unavailable} | {Brak} | {Brak} | Ten harmonogram rozpoczyna się od razu, a następnie oblicza przyszłe powtórzenia na podstawie ostatniego czasu wykonywania. | 
| Uruchom co 15 minut (o datę i godzinę rozpoczęcia) | 15 | Minuta | *startDate*T*startTime*Z | {unavailable} | {Brak} | {Brak} | Nie możesz uruchomić ten harmonogram *żadnych wcześniej* niż określona data rozpoczęcia i godzina, a następnie oblicza przyszłe powtórzenia na podstawie ostatniego czasu wykonywania. | 
| Uruchom co godzinę w ciągu godziny (o datę i godzinę rozpoczęcia) | 1 | Godzina | *datą rozpoczęcia*Thh:00:00Z | {unavailable} | {Brak} | {Brak} | Nie możesz uruchomić ten harmonogram *żadnych wcześniej* niż określona data i godzina rozpoczęcia. Przyszłe powtórzenia uruchamiane co godzinę o "00" znak minuty. <p>W przypadku "Week" lub "Month" częstotliwość tego harmonogramu odpowiednio działa tylko jeden dzień w tygodniu lub jeden dzień w miesiącu. | 
| Uruchom co godzinę, codziennie (nie datę i godzinę rozpoczęcia) | 1 | Godzina | {Brak} | {unavailable} | {Brak} | {Brak} | Ten harmonogram rozpoczyna się natychmiast i oblicza przyszłe powtórzenia na podstawie ostatniego czasu wykonywania. <p>W przypadku "Week" lub "Month" częstotliwość tego harmonogramu odpowiednio działa tylko jeden dzień w tygodniu lub jeden dzień w miesiącu. | 
| Uruchom co godzinę, codziennie (o datę i godzinę rozpoczęcia) | 1 | Godzina | *startDate*T*startTime*Z | {unavailable} | {Brak} | {Brak} | Nie możesz uruchomić ten harmonogram *żadnych wcześniej* niż określona data rozpoczęcia i godzina, a następnie oblicza przyszłe powtórzenia na podstawie ostatniego czasu wykonywania. <p>W przypadku "Week" lub "Month" częstotliwość tego harmonogramu odpowiednio działa tylko jeden dzień w tygodniu lub jeden dzień w miesiącu. | 
| Uruchom co 15 minut po pełnej godzinie, co godzinę (o datę i godzinę rozpoczęcia) | 1 | Godzina | *datą rozpoczęcia*T00:15:00Z | {unavailable} | {Brak} | {Brak} | Nie możesz uruchomić ten harmonogram *żadnych wcześniej* niż określona data i godzina w 00:15 AM, 1:15:00, 2:15 AM, na którym uruchomiono rozpoczęcia i tak dalej. | 
| Uruchom co 15 minut po pełnej godzinie, co godzinę (nie datę i godzinę rozpoczęcia) | 1 | Dzień | {Brak} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Ten harmonogram uruchamiania na 00:15 AM, 1:15:00, 2:15 AM, i tak dalej. Ponadto ten harmonogram jest odpowiednikiem częstotliwość "Godzina" i godzinę rozpoczęcia o "15" minut. | 
| Uruchom co 15 minut od znacznika 15 minut (nie datę i godzinę rozpoczęcia) | 1 | Dzień | {Brak} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ten harmonogram nie zaczyna się do momentu następnej znak 15 minut. | 
| Uruchom o 8:00 każdego dnia (nie datę i godzinę rozpoczęcia) | 1 | Dzień | {Brak} | {unavailable} | 8 | {Brak} | Ten harmonogram uruchamiania o 8:00 każdego dnia, na podstawie określonego harmonogramu. | 
| Uruchom o 8:00 każdego dnia (o datę i godzinę rozpoczęcia) | 1 | Dzień | *datą rozpoczęcia*T08:00:00Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram uruchamia 8:00 AM codziennie, oparte na określonym czasie rozpoczęcia. | 
| Uruchom na 8:30 AM każdego dnia (nie datę i godzinę rozpoczęcia) | 1 | Dzień | {Brak} | {unavailable} | 8 | 30 | Ten harmonogram uruchamiania na 8:30 AM codziennie na podstawie określonego harmonogramu. | 
| Uruchom na 8:30 AM każdego dnia (o datę i godzinę rozpoczęcia) | 1 | Dzień | *datą rozpoczęcia*T08:30:00Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram rozpoczyna się od określona data rozpoczęcia w 8:30 AM. | 
| Uruchom przy 8:30 AM i 4:30 będzie codziennie | 1 | Dzień | {Brak} | {unavailable} | 8, 16 | 30 | | 
| Uruchom na 8:30 AM, 8:45 AM, 4:30 będzie i 4:45 PM codziennie | 1 | Dzień | {Brak} | {unavailable} | 8, 16 | 30, 45 | | 
| Uruchamiane co sobotę po 17: 00 (nie datę i godzinę rozpoczęcia) | 1 | Tydzień | {Brak} | "Sobota" | 17 | 00 | Ten harmonogram jest uruchamiane co sobotę w 5:00 PM. | 
| Uruchom każdą sobotę godzinie 5 (o datę i godzinę rozpoczęcia) | 1 | Tydzień | *datą rozpoczęcia*T17:00:00Z | "Sobota" | {Brak} | {Brak} | Nie możesz uruchomić ten harmonogram *żadnych wcześniej* niż określona data i godzina rozpoczęcia, w tym przypadku 9 września 2017, 5:00 PM. Przyszłe powtórzenia uruchamiane co sobotę w 5:00 PM. | 
| Uruchom co Tuesday, Thursday godzinie 5 | 1 | Tydzień | {Brak} | "Wtorek", "Czwartek" | 17 | {Brak} | Ten harmonogram jest uruchamiane co wtorek i czwartek w 5:00 PM. | 
| Uruchom co godzinę poza godzinami pracy | 1 | Tydzień | {Brak} | Wybierz wszystkie dni, z wyjątkiem sobota i niedziela. | Wybierz godziny, dnia, który ma. | Zaznacz wszystkie minut godzinę, która ma. | Na przykład jeśli godziny pracy są 8:00 AM do 5:00 PM, następnie wybierz pozycję "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" godzin dnia. <p>Jeśli godziny pracy są 8:30 AM do 5:30 będzie, wybierz poprzednich godzin dnia plus "30" jako minuty, godziny. | 
| Uruchom raz każdego dnia w weekendy | 1 | Tydzień | {Brak} | "Sobota", "Niedziela" | Wybierz godziny, dnia, który ma. | Wybierz wszystkie minuty, godziny, zależnie od potrzeb. | Ten harmonogram uruchamiania każdego sobota i niedziela z określonym harmonogramem. | 
| Uruchom co 15 minut, co dwa tygodnie od poniedziałku tylko | 2 | Tydzień | {Brak} | "Poniedziałek" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ten harmonogram jest uruchamiane co drugi poniedziałek w znak co 15 minut. | 
| Uruchom co godzinę przez jeden dzień w miesiącu | 1 | Miesiąc | {patrz Uwaga} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {patrz Uwaga} | Jeśli nie określisz, datę i godzinę, Data i godzina utworzenia korzysta z tego harmonogramu. Aby kontrolować minuty dla harmonogramu cyklu, określ minuty, godziny rozpoczęcia, godziny, lub Użyj czasu utworzenia. Na przykład, jeśli czas rozpoczęcia lub czas utworzenia 8:25 AM, ten harmonogram jest uruchamiane w 8:25 AM, 9:25 AM, 10:25 AM, i tak dalej. | 
||||||||| 

<a name="start-time"></a>

**Pytanie:** co to są sposoby, że można używać datę i godzinę? </br>
**Odpowiedź:** poniżej przedstawiono niektóre wzorców, które zawierają metody kontrolowania cyklu o datę i godzinę i jak aparat Logic Apps wykonuje te cykle:

| Godzina rozpoczęcia | Cykl bez harmonogramu | Cykl z harmonogramem | 
| ---------- | --------------------------- | ------------------------ | 
| {Brak} | Uruchamia natychmiast pierwszy obciążenia. <p>Uruchamia przyszłych obciążeń na podstawie ostatniego czasu wykonywania. | Uruchamia natychmiast pierwszy obciążenia. <p>Uruchamia przyszłych obciążeń na podstawie określonego harmonogramu. | 
| Godzina rozpoczęcia w przeszłości | Oblicza czas wykonywania na podstawie określonym czasie rozpoczęcia i uruchom odrzucenia ostatnich godzin. Uruchamia pierwszy obciążenia w przyszłości dalej czasu wykonywania. <p>Uruchamia przyszłych obciążeń na podstawie obliczeń od momentu ostatniego uruchomienia. <p>Aby uzyskać więcej wyjaśnienie Zobacz przykład poniżej tej tabeli. | Uruchamia pierwszy obciążenia *nie wcześniej* niż czas rozpoczęcia, na podstawie harmonogramu obliczana na podstawie czasu rozpoczęcia. <p>Uruchamia przyszłych obciążeń na podstawie określonego harmonogramu. <p>**Uwaga:** Określ harmonogram cyklu, ale nie podaje godziny i minuty dla harmonogramu, a następnie przyszłych czasy są obliczane przy użyciu godzinach i minutach, odpowiednio, od momentu pierwszego uruchomienia. | 
| Czas rozpoczęcia obecnie i w przyszłości | Uruchamia pierwszy obciążenia w określonym czasie rozpoczęcia. <p>Uruchamia przyszłych obciążeń na podstawie obliczeń od momentu ostatniego uruchomienia. | Uruchamia pierwszy obciążenia *nie wcześniej* niż czas rozpoczęcia, na podstawie harmonogramu obliczana na podstawie czasu rozpoczęcia. <p>Uruchamia przyszłych obciążeń na podstawie określonego harmonogramu. <p>**Uwaga:** Określ harmonogram cyklu, ale nie podaje godziny i minuty dla harmonogramu, a następnie przyszłych czasy są obliczane przy użyciu godzinach i minutach, odpowiednio, od momentu pierwszego uruchomienia. | 
||||

**Przykład dla ostatnich czas rozpoczęcia cyklu, ale bez harmonogramu** 

| Godzina rozpoczęcia | Bieżący czas | Cykl | Harmonogram |
| ---------- | ------------ | ---------- | -------- | 
| 2017-09-**07**T14:00:00Z | 2017-09-**08**T13:00:00Z | Co 2 dni | {Brak} | 
||||| 

W tym scenariuszu aplikacje logiki aparatu oblicza uruchomić razy na podstawie czasu rozpoczęcia odrzuca minął czas wykonywania i używa dalej przyszłości godziny rozpoczęcia dla przy pierwszym uruchomieniu. Po tym pierwszym uruchomieniu przyszłych przebiegów są oparte na harmonogram obliczana na podstawie czasu rozpoczęcia. Oto, jak wygląda tego cyklu:

| Godzina rozpoczęcia | Uruchom po raz pierwszy | Przyszłe uruchomić razy | 
| ---------- | ------------ | ---------- | 
| 2017-09 -**07** godzinie 2:00 | 2017-09 -**09** godzinie 2:00 | 2017-09 -**11** godzinie 2:00 </br>2017-09 -**13** godzinie 2:00 </br>2017-09 -**15** godzinie 2:00 </br>i tak dalej...
||||| 

Tak dla tego scenariusza, niezależnie od tego, jak daleko w przeszłości Określ interwał czasu, na przykład 2017-09 -**05** godzinie 2:00 lub 2017-09 -**01** godzinie 2:00, pierwszego czasu wykonywania jest taka sama.

## <a name="next-steps"></a>Kolejne kroki

* [Wyzwalacze i akcje przepływu pracy](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)
* [Łączniki](../connectors/apis-list.md)
