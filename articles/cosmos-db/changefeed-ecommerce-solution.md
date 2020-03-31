---
title: Wizualizacja analizy danych w czasie rzeczywistym za pomocą kanału informacyjnego usługi Azure Cosmos DB
description: W tym artykule opisano, jak firma prowadząca handel detaliczny może używać pliku danych zmian do zrozumienia wzorców użytkowników, przeprowadzania analizy i wizualizacji danych w czasie rzeczywistym
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: c0c1a28dc399d3f176f92e656621fec1bc92dbfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513513"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Wizualizacja analizy danych w czasie rzeczywistym za pomocą kanału informacyjnego usługi Azure Cosmos DB

Źródło danych usługi Azure Cosmos DB to mechanizm umożliwiający uzyskanie ciągłego i przyrostowego źródła danych rekordów z kontenera usługi Azure Cosmos podczas tworzenia lub modyfikowania tych rekordów. Obsługa kanału informacyjnego zmian działa, nasłuchiwając kontenera dla wszelkich zmian. Następnie tworzone są dane wyjściowe w postaci posortowanej listy zmienionych dokumentów w kolejności, w której zostały zmodyfikowane. Aby dowiedzieć się więcej o pliku danych o zmianach, zobacz [artykuł praca z nadajnikiem zmian.](change-feed.md) 

W tym artykule opisano, jak firma zajmująca się zmianami może używać danych źródłowych do zrozumienia wzorców użytkowników, przeprowadzania analizy i wizualizacji danych w czasie rzeczywistym. Będziesz analizować zdarzenia, takie jak wyświetlanie elementu przez użytkownika, dodawanie elementu do koszyka lub kupowanie towaru. Gdy wystąpi jedno z tych zdarzeń, tworzony jest nowy rekord, a plik danych o zmianie rejestruje ten rekord. Następnie wyłowi szereg kroków, co powoduje wizualizację metryk analizujących wydajność i działanie firmy. Przykładowe dane, które można wizualizować, obejmują przychody, unikatowe osoby odwiedzające witrynę, najpopularniejsze produkty i średnią cenę elementów, które są wyświetlane w porównaniu z dodaniem do koszyka w porównaniu z zakupami. Te przykładowe dane mogą pomóc firmie handlu elektronicznego ocenić popularność witryny, opracować strategie reklamowe i cenowe oraz podjąć decyzje dotyczące zapasów, w które warto zainwestować.

Chcesz obejrzeć film o rozwiązaniu przed rozpoczęciem pracy, zobacz następujący film:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Składniki rozwiązania
Poniższy diagram przedstawia przepływ danych i składniki zaangażowane w rozwiązanie:

![Wizualizacja projektu](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Generowanie danych:** Symulator danych służy do generowania danych detalicznych, które reprezentują zdarzenia, takie jak użytkownik wyświetlający element, dodający element do koszyka i kupując element. Można wygenerować duży zestaw przykładowych danych przy użyciu generatora danych. Wygenerowane przykładowe dane zawierają dokumenty w następującym formacie:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Baza danych Cosmos:** Wygenerowane dane są przechowywane w kontenerze usługi Azure Cosmos.  

3. **Zmień kanał informacyjny:** Źródło danych zmiany będzie nasłuchiwać zmian w kontenerze usługi Azure Cosmos. Za każdym razem, gdy nowy dokument jest dodawany do kolekcji (czyli gdy wystąpi zdarzenie, takiemu użytkownikowi wyświetlające element, dodające element do koszyka lub kupujące element), źródło danych o zmianach wyzwoli [funkcję platformy Azure.](../azure-functions/functions-overview.md)  

4. **Funkcja platformy Azure:** Funkcja platformy Azure przetwarza nowe dane i wysyła je do [usługi Azure Event Hub](../event-hubs/event-hubs-about.md).  

5. **Centrum zdarzeń:** Usługa Azure Event Hub przechowuje te zdarzenia i wysyła je do [usługi Azure Stream Analytics w](../stream-analytics/stream-analytics-introduction.md) celu przeprowadzenia dalszej analizy.  

6. **Usługa Azure Stream Analytics:** Usługa Azure Stream Analytics definiuje zapytania do przetwarzania zdarzeń i wykonywania analizy danych w czasie rzeczywistym. Te dane są następnie wysyłane do usługi [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **Usługa Power BI:** Usługa Power BI służy do wizualizacji danych wysyłanych przez usługę Azure Stream Analytics. Możesz utworzyć pulpit nawigacyjny, aby zobaczyć, jak zmieniają się metryki w czasie rzeczywistym.  

## <a name="prerequisites"></a>Wymagania wstępne

* Microsoft .NET Framework 4.7.1 lub nowsza

* Microsoft .NET Core 2.1 (lub wyższy)

* Program Visual Studio z programem Universal Windows Platform development, programami .NET dla tworzenia pulpitu oraz ASP.NET i obciążeń programistów sieci Web

* Subskrypcja platformy Microsoft Azure

* Konto usługi Microsoft Power BI

* Pobierz [laboratorium kanału informacyjnego usługi Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) z usługi GitHub. 

## <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure 

Tworzenie zasobów platformy Azure — konto usługi Azure Cosmos DB, konto magazynu, centrum zdarzeń, usługa Stream Analytics wymagana przez rozwiązanie. Te zasoby zostaną wdrożone za pomocą szablonu usługi Azure Resource Manager. Aby wdrożyć te zasoby, wykonaj następujące czynności: 

1. Ustaw zasady wykonywania programu Windows PowerShell na **Nieograniczone**. Aby to zrobić, otwórz **program Windows PowerShell jako administrator** i uruchom następujące polecenia:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Z repozytorium GitHub pobranego w poprzednim kroku przejdź do folderu **Usługi Azure Resource Manager** i otwórz plik o nazwie **parameters.json.**  

3. Podaj wartości parametrów cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, wskazanych w pliku **parameters.json.** Musisz użyć nazw, które nadasz do każdego z zasobów później.  

4. Z **programu Windows PowerShell**przejdź do folderu **Usługi Azure Resource Manager** i uruchom następujące polecenie:

   ```powershell
   .\deploy.ps1
   ```
5. Po wyświetleniu monitu wprowadź **identyfikator subskrypcji**platformy Azure , **changefeedlab** dla nazwy grupy zasobów i **uruchom1** dla nazwy wdrożenia. Gdy zasoby zaczną wdrażać, może upłynąć do 10 minut, aby zakończyć.

## <a name="create-a-database-and-the-collection"></a>Tworzenie bazy danych i kolekcji

Teraz utworzysz kolekcję do przechowywania wydarzeń w witrynie e-commerce. Gdy użytkownik wyświetli element, doda element do koszyka lub kupi przedmiot, kolekcja otrzyma rekord zawierający akcję ("wyświetlone", "dodane" lub "zakupione"), nazwę danego przedmiotu, cenę danego przedmiotu i numer identyfikatora koszyka użytkownika Zaangażowane.

1. Przejdź do [witryny Azure portal](https://portal.azure.com/) i znajdź **konto usługi Azure Cosmos DB** utworzone przez wdrożenie szablonu.  

2. W okienku **Eksplorator danych** wybierz pozycję **Nowa kolekcja** i wypełnij formularz następującymi szczegółami:  

   * W polu **Identyfikator bazy danych** wybierz pozycję **Utwórz nowy**, a następnie wprowadź przycisk **zmiany.** Pozostaw pole **przepływność bazy danych aprowizuj** niezaznaczone.  
   * W polu **Identyfikator kolekcji** wprowadź **plik dopływu zmian**.  
   * W polu **Klucz partycji** wprowadź **/Item**. Wielkość liter jest rozróżniana, więc upewnij się, że wprowadzono go poprawnie.  
   * W polu **Przepływność** wprowadź **10000**.  
   * Wybierz przycisk **OK**.  

3. Następnie utwórz inną kolekcję o nazwie dzierżawy do przetwarzania pliku danych o **zmianach.** Współrzędne kolekcji dzierżaw przetwarzania pliku danych o zmianach przez wielu pracowników. Oddzielna kolekcja jest używana do przechowywania dzierżaw z jedną dzierżawą na partycję.  

4. Wróć do okienka **Eksploratora danych** i wybierz pozycję **Nowa kolekcja** i wypełnij formularz następującymi szczegółami:

   * W polu **Identyfikator bazy danych** wybierz pozycję Użyj **istniejącego**, a następnie wprowadź **przycisk zmiany.**  
   * W polu **Identyfikator kolekcji** wprowadź **dzierżawy**.  
   * W obszarze **Pojemność magazynu**wybierz opcję **Stała**.  
   * Pozostaw pole **Przepływność** ustawione na wartość domyślną.  
   * Wybierz przycisk **OK**.

## <a name="get-the-connection-string-and-keys"></a>Pobierz ciąg połączenia i klucze

### <a name="get-the-azure-cosmos-db-connection-string"></a>Pobierz ciąg połączenia usługi Azure Cosmos DB

1. Przejdź do [witryny Azure portal](https://portal.azure.com/) i znajdź **konto usługi Azure Cosmos DB** utworzone przez wdrożenie szablonu.  

2. Przejdź do **okienka Klawisze,** skopiuj CIĄG POŁĄCZENIA PODSTAWOWEGO i skopiuj go do notatnika lub innego dokumentu, do którego będziesz mieć dostęp w całym laboratorium. Należy oznaczyć go **ciągiem połączenia usługi Cosmos DB**. Musisz skopiować ciąg do kodu później, więc zanotuj i zapamiętaj, gdzie go przechowujesz.

### <a name="get-the-storage-account-key-and-connection-string"></a>Pobierz klucz konta magazynu i parametry połączenia

Konta usługi Azure Storage umożliwiają użytkownikom przechowywanie danych. W tym laboratorium użyjesz konta magazynu do przechowywania danych, które są używane przez funkcję platformy Azure. Funkcja platformy Azure jest wyzwalana, gdy zostanie dokonana jakakolwiek modyfikacja kolekcji.

1. Powrót do grupy zasobów i otwarcie utworzonego wcześniej konta magazynu  

2. Wybierz **klawisze dostępu** z menu po lewej stronie.  

3. Skopiuj wartości pod **kluczem 1** do notatnika lub innego dokumentu, do którego będziesz mieć dostęp w całym laboratorium. **Klucz** należy oznaczyć jako **klucz magazynu,** a **ciąg połączenia** jako ciąg **połączenia magazynu**. Musisz skopiować te ciągi do kodu później, więc zanotuj i zapamiętaj, gdzie je przechowujesz.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Pobierz ciąg połączenia obszaru nazw centrum zdarzeń

Usługa Azure Event Hub odbiera dane zdarzenia, przechowuje, przetwarza i przekazuje dane. W tym laboratorium usługa Azure Event Hub otrzyma dokument za każdym razem, gdy wystąpi nowe zdarzenie (tj. element jest oglądany przez użytkownika, dodany do koszyka użytkownika lub zakupiony przez użytkownika), a następnie przekaże ten dokument do usługi Azure Stream Analytics.

1. Wróć do grupy zasobów i otwórz utworzony i nazwany **obszar nazw Centrum zdarzeń** w przedłabiu.  

2. Z menu po lewej stronie wybierz polecenie **Zasady dostępu współdzielonego.**  

3. Wybierz pozycję **RootManageSharedAccessKey**. Skopiuj **klucz podstawowy ciągu połączenia** do notatnika lub innego dokumentu, do którego będziesz mieć dostęp w całym laboratorium. Należy oznaczyć go ciągiem połączenia **Centrum nazw centrum zdarzeń.** Musisz skopiować ciąg do kodu później, więc zanotuj i zapamiętaj, gdzie go przechowujesz.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Konfigurowanie funkcji platformy Azure w celu odczytu kanału informacyjnego zmian

Po utworzeniu nowego dokumentu lub bieżący dokument jest modyfikowany w kontenerze usługi Cosmos, źródło danych zmiany automatycznie dodaje ten zmodyfikowany dokument do jego historii zmian kolekcji. Teraz skompilujesz i uruchomisz funkcję platformy Azure, która przetwarza źródło danych o zmianach. Gdy dokument jest tworzony lub modyfikowany w utworzonej kolekcji, funkcja platformy Azure zostanie wyzwolona przez źródło danych zmian. Następnie funkcja platformy Azure wyśle zmodyfikowany dokument do Centrum zdarzeń.

1. Wróć do repozytorium sklonowanego na urządzeniu.  

2. Kliknij prawym przyciskiem myszy plik o nazwie **ChangeFeedLabSolution.sln** i wybierz polecenie **Otwórz za pomocą programu Visual Studio**.  

3. Przejdź do **local.settings.json** w programie Visual Studio. Następnie użyj wartości nagranych wcześniej, aby wypełnić puste miejsca.  

4. Przejdź do **ChangeFeedProcessor.cs**. W parametrach funkcji **Uruchom** wykonaj następujące czynności:  

   * Zastąp tutaj tekst **NAZWA KOLEKCJI** nazwą swojej kolekcji. Jeśli po wcześniejszych instrukcji, nazwa kolekcji jest changefeedlabcollection.  
   * Zastąp tutaj tekst **NAZWA KOLEKCJI DZIERŻAWY** nazwą kolekcji dzierżaw. Jeśli po wcześniejszych instrukcjach, nazwa kolekcji dzierżawy jest **dzierżawy**.  
   * U góry programu Visual Studio upewnij się, że pole Projekt startowy po lewej stronie zielonej strzałki jest wyświetlane **ChangeFeedFunction**.  
   * Wybierz **pozycję Start** u góry strony, aby uruchomić program  
   * Możesz potwierdzić, że funkcja jest uruchomiona, gdy aplikacja konsoli mówi "Uruchomiono hosta zadań".

## <a name="insert-data-into-azure-cosmos-db"></a>Wstawianie danych do usługi Azure Cosmos DB 

Aby zobaczyć, jak plik danych o zmianach przetwarza nowe akcje w witrynie handlu elektronicznego, należy symulować dane, które reprezentują użytkowników wyświetlających elementy z katalogu produktów, dodając te elementy do swoich koszyków i kupując towary w koszykach. Dane te są dowolne i w celu replikowania danych w witrynie e-commerce.

1. Przejdź z powrotem do repozytorium w Eksploratorze plików i kliknij prawym przyciskiem myszy **pozycję ChangeFeedFunction.sln,** aby ponownie otworzyć je w nowym oknie programu Visual Studio.  

2. Przejdź do pliku **App.config.** W `<appSettings>` bloku dodaj punkt końcowy i **unikatowy klucz podstawowy,** który jest pobieranym wcześniej kontem usługi Azure Cosmos DB.  

3. Dodaj w **kolekcji** i nazwy **bazy danych.** (Nazwy te powinny być **changefeedlabcollection** i **changefeedlabdatabase,** chyba że zdecydujesz się nazwać swoje inaczej.)

   ![Aktualizowanie ciągów połączeń](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Zapisz zmiany we wszystkich edytowanych plikach.  

5. U góry programu Visual Studio upewnij się, że pole **Projekt startowy** po lewej stronie zielonej strzałki jest o treści **DataGenerator**. Następnie wybierz **przycisk Start** u góry strony, aby uruchomić program.  
 
6. Poczekaj na uruchomienie programu. Gwiazdy oznaczają, że dane nadchodzą! Utrzymuj program w pracy - ważne jest, aby zbierać dużo danych.  

7. Jeśli przejdziesz do [witryny Azure portal](https://portal.azure.com/) , a następnie do konta usługi Cosmos DB w grupie zasobów, a następnie do **Eksploratora danych,** zobaczysz losowo zaimportowane dane zaimportowane w **pliku changefeedlabcollection** .
 
   ![Dane wygenerowane w portalu](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Konfigurowanie zadania analizy strumienia

Usługa Azure Stream Analytics to w pełni zarządzana usługa w chmurze do przetwarzania danych strumieniowych w czasie rzeczywistym. W tym laboratorium będziesz używać analizy strumienia do przetwarzania nowych zdarzeń z Centrum zdarzeń (tj. gdy element jest oglądany, dodawany do koszyka lub zakupiony), włączanie tych zdarzeń do analizy danych w czasie rzeczywistym i wysyłanie ich do usługi Power BI w celu wizualizacji.

1. Z [witryny Azure portal](https://portal.azure.com/)przejdź do grupy zasobów, a następnie do **streamjob1** (zadanie analizy strumienia utworzone w przedładzie).  

2. Wybierz **dane wejściowe,** jak pokazano poniżej.  

   ![Tworzenie danych wejściowych](./media/changefeed-ecommerce-solution/create-input.png)

3. Wybierz **+ Dodaj dane wejściowe strumienia**. Następnie wybierz **centrum zdarzeń** z menu rozwijanego.  

4. Wypełnij nowy formularz wejściowy następującymi szczegółami:

   * W polu **Alias wejściowy** wprowadź **dane wejściowe**.  
   * Wybierz opcję **Wybierz Centrum zdarzeń z subskrypcji**.  
   * Ustaw pole **Subskrypcja** na subskrypcję.  
   * W polu **Obszar nazw Centrum zdarzeń** wprowadź nazwę obszaru nazw centrum zdarzeń utworzonego podczas przednałaka.  
   * W polu **Nazwa Centrum zdarzeń** wybierz opcję Użyj **istniejącego** i wybierz z menu rozwijanego **Centrum zdarzeń1.**  
   * Pozostaw pole nazwy **zasad Centrum zdarzeń** ustawione na wartość domyślną.  
   * Pozostaw **format serializacji zdarzeń** jako **JSON**.  
   * Pozostaw **pole kodowania** ustawione na **UTF-8**.  
   * Pozostaw pole **typu kompresji zdarzenia** ustawione na **Brak**.  
   * Wybierz ikonę **Zapisz**.

5. Przejdź z powrotem do strony zadania analizy strumienia i wybierz pozycję **Dane wyjściowe**.  

6. Wybierz pozycję **+ Dodaj**. Następnie wybierz pozycję **Power BI** z menu rozwijanego.  

7. Aby utworzyć nowe dane wyjściowe usługi Power BI w celu wizualizacji średniej ceny, wykonaj następujące czynności:

   * W polu **Alias wyjście** wprowadź **średniąCenę Naprzejazd**.  
   * Pozostaw pole **Grupuj obszar roboczy** ustawiony na **Autoryzuj połączenie, aby załadować obszary robocze**.  
   * W polu **Nazwa zestawu danych** wprowadź **średniącena .**  
   * W polu **Nazwa tabeli** wprowadź **średniącenastawę**.  
   * Wybierz przycisk **Autoryzuj,** a następnie postępuj zgodnie z instrukcjami, aby autoryzować połączenie z usługą Power BI.  
   * Wybierz ikonę **Zapisz**.  

8. Następnie wróć do **streamjob1** i wybierz pozycję **Edytuj kwerendę**.

   ![Edytuj zapytanie](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Wklej następującą kwerendę do okna kwerendy. Zapytanie **ŚREDNIA CENA** oblicza średnią cenę wszystkich towarów, które są wyświetlane przez użytkowników, średnią cenę wszystkich towarów, które są dodawane do koszyków użytkowników, a średnia cena wszystkich towarów, które są kupowane przez użytkowników. Ten wskaźnik może pomóc firmom handlu elektronicznego w podjęciu decyzji, po jakich cenach sprzedawać przedmioty i w jakie zapasy inwestować. Na przykład jeśli średnia cena oglądanych towarów jest znacznie wyższa niż średnia cena zakupionych towarów, firma może zdecydować się na dodanie tańszych towarów do swoich zapasów.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Następnie wybierz **pozycję Zapisz** w lewym górnym rogu.  

11. Teraz wróć do **streamjob1** i wybierz przycisk **Start** u góry strony. Azure Stream Analytics może potrwać kilka minut, aby uruchomić, ale po pewnym czasie zobaczysz, że zmieni się z "Uruchamianie" na "Uruchamianie".

## <a name="connect-to-power-bi"></a>Łączenie z usługą Power BI

Power BI to pakiet narzędzi do analityki biznesowej, który pozwala analizować dane i dzielić się szczegółowymi informacjami. Jest to świetny przykład na to, jak można strategicznie wizualizować analizowane dane.

1. Zaloguj się do usługi Power BI i przejdź do **obszaru roboczego,** otwierając menu po lewej stronie.  

2. Wybierz **pozycję + Utwórz** w prawym górnym rogu, a następnie wybierz pozycję **Pulpit nawigacyjny,** aby utworzyć pulpit nawigacyjny.  

3. Wybierz **+ Dodaj kafelek** w prawym górnym rogu.  

4. Wybierz **pozycję Niestandardowe dane przesyłania strumieniowego**, a następnie wybierz przycisk **Dalej.**  
 
5. Wybierz **średniącenastawę** z **zestawów danych,** a następnie wybierz **pozycję Dalej**.  

6. W polu **Typ wizualizacji** wybierz z menu rozwijanego pozycję **Wykres słupkowy klastrowany.** W **obszarze Oś**dodaj akcję. Pomiń **legendę** bez dodawania czegokolwiek. Następnie w następnej sekcji o nazwie **Wartość**dodaj **avg**. Wybierz **pozycję Dalej**, a następnie tytuł wykresu i wybierz pozycję **Zastosuj**. Powinieneś zobaczyć nowy wykres na pulpicie nawigacyjnym!  

7. Teraz, jeśli chcesz wizualizować więcej metryk, możesz wrócić do **streamjob1** i utworzyć trzy kolejne dane wyjściowe z następującymi polami.

   a. **Alias wyjściowy:** incomingRevenueOutput, Nazwa zestawu danych: incomingRevenue, Nazwa tabeli: incomingRevenue  
   b. **Alias wyjściowy:** top5Output, Nazwa zestawu danych: top5, Nazwa tabeli: top5  
   d. **Alias wyjściowy:** uniqueVisitorCountOutput, Nazwa zestawu danych: uniqueVisitorCount, Nazwa tabeli: uniqueVisitorCount

   Następnie wybierz **pozycję Edytuj kwerendę** i wklej następujące zapytania **nad** tym, który już napisałeś.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   Zapytanie TOP 5 oblicza 5 pierwszych elementów według liczby zakupionych przedmiotów. Ten wskaźnik może pomóc firmom handlu elektronicznego ocenić, które produkty są najbardziej popularne i może mieć wpływ na decyzje firmy dotyczące reklam, cen i zapasów.

   Zapytanie PRZYCHODY oblicza przychody, sumując ceny wszystkich towarów zakupionych co minutę. Ta metryka może pomóc firmom handlu elektronicznego ocenić jego wyniki finansowe, a także zrozumieć, o jakich porach dnia przyczyniają się do większości przychodów. Może to mieć wpływ na ogólną strategię firmy, w szczególności marketing.

   Kwerenda UNIQUE VISITORS oblicza, ilu unikalnych użytkowników znajduje się na stronie co 5 sekund, wykrywając unikatowe identyfikatory koszyka. Ten wskaźnik może pomóc firmom handlu elektronicznego ocenić ich aktywność w witrynie i opracować strategię zdobywania większej liczby klientów.

8. Teraz można również dodawać kafelki dla tych zestawów danych.

   * W przypadku 5 najlepszych warto zrobić wykres kolumnowy grupowany z elementami jako osią i liczbą jako wartością.  
   * W przypadku przychodów sensowne byłoby zrobienie wykresu liniowego z czasem jako osią i sumą cen jako wartości. Przedział czasu wyświetlania powinien być jak największy, aby dostarczyć jak najwięcej informacji.  
   * Dla unikalnych użytkowników, byłoby sensu zrobić wizualizację karty z liczbą unikalnych użytkowników jako wartość.

   Oto jak przykładowy pulpit nawigacyjny wygląda z tymi wykresami:

   ![wizualizacje](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>Opcjonalnie: Wizualizuj za pomocą witryny e-commerce

Będziesz teraz obserwować, jak możesz korzystać z nowego narzędzia do analizy danych, aby połączyć się z prawdziwą witryną e-commerce. Aby utworzyć witrynę handlu elektronicznego, użyj bazy danych usługi Azure Cosmos do przechowywania listy kategorii produktów (damskie, męskie, unisex), katalogu produktów i listy najpopularniejszych elementów.

1. Przejdź z powrotem do [portalu Azure,](https://portal.azure.com/)a następnie do **konta usługi Cosmos DB,** a następnie do **Eksploratora danych.**  

   Dodaj dwie kolekcje w obszarze**produktów** i **kategorii** **changefeedlabdatabase** - ze stałą pojemnością magazynową.

   Dodaj inną kolekcję w obszarze **changefeedlabdatabase** o nazwie **topItems** i **/Item** jako klucz partycji.

2. Wybierz **kolekcję topItems,** a następnie w obszarze **Skala i Ustawienia** ustaw **czas na żywo** na **30 sekund,** aby aktualizacje topItems co 30 sekund.

   ![Czas wygaśnięcia](./media/changefeed-ecommerce-solution/time-to-live.png)

3. Aby wypełnić kolekcję **topItems** najczęściej kupowanymi przedmiotami, przejdź z powrotem do **streamjob1** i dodaj nowe **dane wyjściowe.** Wybierz **Cosmos DB**.

4. Wypełnij wymagane pola, jak na zdjęciu poniżej.

   ![Wyjście cosmosu](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. Jeśli dodano opcjonalne zapytanie TOP 5 w poprzedniej części laboratorium, przejdź do części 5a. Jeśli nie, przejdź do części 5b.

   5a. W **streamjob1**wybierz **pozycję Edytuj kwerendę** i wklej następującą kwerendę w edytorze zapytań usługi Azure Stream Analytics poniżej kwerendy TOP 5, ale powyżej pozostałych zapytań.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. W **streamjob1**wybierz **pozycję Edytuj kwerendę** i wklej następującą kwerendę w edytorze zapytań usługi Azure Stream Analytics ponad wszystkie inne zapytania.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Otwórz **witrynę EcommerceWebApp.sln** i przejdź do pliku **Web.config** w **Eksploratorze rozwiązań**.  

7. W `<appSettings>` bloku dodaj identyfikator **URI** i **klucz podstawowy** zapisany wcześniej, w którym znajduje się **identyfikator URI** w tym miejscu i klucz podstawowy w **tym miejscu.** Następnie dodaj **nazwę bazy danych** i **nazwę kolekcji,** jak wskazano. (Te nazwy powinny być **changefeedlabdatabase** i **changefeedlabcollection,** chyba że wybrano nazwę swojego inaczej.)

   Wpisz **nazwę kolekcji produktów,** **nazwę kolekcji kategorii**i nazwę **kolekcji najpopularniejszych elementów,** jak wskazano. (Te nazwy powinny być **produkty, kategorie i topItems** chyba że zdecydujesz się nazwać swoje inaczej.)  

8. Przejdź do **folderu Realizacji transakcji** i otwórz go w witrynie **E-commerceWebApp.sln.** Następnie otwórz plik **Web.config** w tym folderze.  

9. W `<appSettings>` bloku dodaj identyfikator **URI** i **klucz podstawowy** zapisany wcześniej, jeśli jest to wskazane. Następnie dodaj **nazwę bazy danych** i **nazwę kolekcji,** jak wskazano. (Te nazwy powinny być **changefeedlabdatabase** i **changefeedlabcollection,** chyba że wybrano nazwę swojego inaczej.)  

10. Naciśnij **przycisk Start** u góry strony, aby uruchomić program.  

11. Teraz możesz bawić się na stronie e-commerce. Podczas wyświetlania elementu, dodawania elementu do koszyka, zmiany ilości towaru w koszyku lub zakupu towaru zdarzenia te będą przekazywane za pośrednictwem kanału informacyjnego usługi Cosmos DB do centrum zdarzeń, asa, a następnie usługi Power BI. Zalecamy dalsze uruchamianie DataGenerator w celu wygenerowania znaczących danych o ruchu internetowym i zapewnienia realistycznego zestawu "Gorących produktów" na stronie e-commerce.

## <a name="delete-the-resources"></a>Usuwanie zasobów

Aby usunąć zasoby utworzone w tym laboratorium, przejdź do grupy zasobów w [witrynie Azure Portal](https://portal.azure.com/), a następnie wybierz **polecenie Usuń grupę zasobów** z menu u góry strony i postępuj zgodnie z podanymi instrukcjami.

## <a name="next-steps"></a>Następne kroki 
  
* Aby dowiedzieć się więcej o pliku danych o [zmianach, zobacz praca z obsługą kanału informacyjnego zmian w usłudze Azure Cosmos DB](change-feed.md) 
