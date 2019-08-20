---
title: Użyj usługi Azure Cosmos DB Zmień źródło danych do analizy danych w czasie rzeczywistym wizualizować
description: W tym artykule opisano, jak zmiany Kanał informacyjny może służyć przez firmę handlu detalicznego zrozumieć wzorce użytkownika, wykonywania analiz danych w czasie rzeczywistym i wizualizacji.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: 86d4dd706b097891db155214e4edb7e85e054858
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616947"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Użyj usługi Azure Cosmos DB Zmień źródło danych do analizy danych w czasie rzeczywistym wizualizować

Kanał informacyjny zmiany Azure Cosmos DB jest mechanizmem do uzyskiwania ciągłego i przyrostowego źródła rekordów z kontenera usługi Azure Cosmos, ponieważ te rekordy są tworzone lub modyfikowane. Kanału informacyjnego zmian działania pomocy technicznej przez nasłuchiwanie w kontenerze żadnych zmian. Następnie wyświetla posortowaną listę dokumentów, które zostały zmienione w kolejności, w której zostały zmodyfikowane. Aby dowiedzieć się więcej na temat zmian, zobacz [pracy za pomocą zestawienia zmian](change-feed.md) artykułu. 

W tym artykule opisano, jak zmiany Kanał informacyjny może służyć przez firmę handlu elektronicznego, aby zrozumieć wzorce użytkownika, wykonywania analiz danych w czasie rzeczywistym i wizualizacji. Będzie analizować zdarzenia, takie jak wyświetlenie elementu, dodanie elementu do koszyka ich lub zakupu elementu użytkownika. Gdy wystąpi jedno z tych zdarzeń, zostanie utworzony nowy rekord, a zmiany źródła danych dzienników, służące do rejestrowania. Kanału informacyjnego zmian następnie wyzwalaczy szereg kroków skutkuje wizualizacji metryk, które Analizuj wydajność firmy i działania. Przykładowe metryki, które można wizualizować obejmują przychodu, unikatowych odwiedzających witrynę, najpopularniejszych elementów i średnia cena elementów, które są wyświetlane w porównaniu z dodana do koszyka i zakupu. Te przykładowe metryki może pomóc firmie handlu elektronicznego, oceniać swoją popularność lokacji, tworzyć jego reklamy i strategii cenowych i podjęcia decyzji dotyczących spisu, jakie o wartości inwestycji w.

Zainteresowani oglądania wideo o rozwiązaniu przed rozpoczęciem pracy, zobacz poniższy film wideo:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Składniki rozwiązania
Poniższy diagram przedstawia przepływ danych i składniki zaangażowane w rozwiązaniu:

![Projekt wizualizacji](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Generowanie danych:** Symulator danych służy do generowania danych detalicznych, które reprezentują zdarzenia, takie jak Użytkownik przeglądający element, Dodawanie elementu do swojego koszyka i kupowanie elementu. Duży zestaw przykładowych danych można wygenerować za pomocą generatora danych. Wygenerowanych przykładowych danych zawiera dokumenty w następującym formacie:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** Wygenerowane dane są przechowywane w kontenerze usługi Azure Cosmos.  

3. **Źródło zmian:** Źródło zmian będzie nasłuchiwać zmian w kontenerze usługi Azure Cosmos. Każdorazowo nowy dokument zostanie dodany do kolekcji (to znaczy po wystąpieniu zdarzenia, od takich użytkowników, wyświetlanie elementu Dodawanie elementu do koszyka ich lub zakupu element), zmiana źródła danych będą wyzwalać [funkcji platformy Azure](../azure-functions/functions-overview.md).  

4. **Funkcja platformy Azure:** Funkcja platformy Azure przetwarza nowe dane i wysyła je do [centrum zdarzeń platformy Azure](../event-hubs/event-hubs-about.md).  

5. **Centrum zdarzeń:** Usługa Azure Event Hub przechowuje te zdarzenia i wysyła je do [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) w celu przeprowadzenia dalszej analizy.  

6. **Azure Stream Analytics:** Azure Stream Analytics definiuje zapytania do przetwarzania zdarzeń i wykonywania analizy danych w czasie rzeczywistym. Te dane są następnie wysyłane do [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Power BI służy do wizualizacji danych wysyłanych przez Azure Stream Analytics. Możesz utworzyć pulpit nawigacyjny, aby zobaczyć, jak zmienić metryki w czasie rzeczywistym.  

## <a name="prerequisites"></a>Wymagania wstępne

* Microsoft .NET Framework 4.7.1 lub nowszej

* Microsoft .NET Core 2.1 (lub nowszej)

* Program Visual Studio z programowania na platformę uniwersalną Windows, programowanie aplikacji klasycznych dla platformy .NET i obciążeń deweloperskich platformy ASP.NET i sieci web

* Subskrypcja platformy Microsoft Azure

* Konta Microsoft Power BI

* Pobierz [zmian usługi Azure Cosmos DB kanału informacyjnego laboratorium](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) z usługi GitHub. 

## <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure 

Tworzenie zasobów platformy Azure — usłudze Azure Cosmos DB, konto magazynu, Centrum zdarzeń usługi Stream Analytics, wymaganego przez rozwiązanie. Wdroży te zasoby za pomocą szablonu usługi Azure Resource Manager. Do wdrażania tych zasobów, wykonaj następujące kroki: 

1. Ustaw zasady wykonywania programu Windows PowerShell **Unrestricted**. Aby to zrobić, otwórz **program Windows PowerShell jako Administrator** i uruchom następujące polecenia:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Z repozytorium GitHub został pobrany w poprzednim kroku, przejdź do **usługi Azure Resource Manager** folder i Otwórz plik o nazwie **parameters.json** pliku.  

3. Podaj wartości dla cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, parametrów, zgodnie z instrukcjami w **parameters.json** pliku. Należy użyć nazwy, które zapewniają do każdego z zasobów później.  

4. Z **programu Windows PowerShell**, przejdź do **usługi Azure Resource Manager** folderu i uruchom następujące polecenie:

   ```powershell
   .\deploy.ps1
   ```
5. Po wyświetleniu monitu wprowadź Azure **identyfikator subskrypcji**, **changefeedlab** jako nazwę grupy zasobów, a **wpisz run1** dla nazwy wdrożenia. Po zasoby rozpocząć wdrażanie, może upłynąć do 10 minut na jej zakończenie.

## <a name="create-a-database-and-the-collection"></a>Utwórz bazę danych i kolekcji

Teraz utworzysz kolekcję zawierającą zdarzeń witryny handlu elektronicznego. Gdy użytkownik przegląda elementu, dodaje element do koszyka ich lub zakupów elementu, kolekcji zostanie wyświetlony rekord, który zawiera akcję ("wyświetlane", "dodaje" lub "zakupione"), nazwa elementu zaangażowane cena elementu występującego i numer identyfikacyjny użytkownika koszyka i nvolved.

1. Przejdź do [witryny Azure Portal](https://portal.azure.com/) i Znajdź **konta usługi Azure Cosmos DB** tworzone przez wdrożenie szablonu.  

2. Z **Eksplorator danych** okienku wybierz **Nowa kolekcja** i wypełnij formularz z następującymi szczegółami:  

   * Aby uzyskać **bazy danych o identyfikatorze** pól, zaznacz **Utwórz nową**, wprowadź **changefeedlabdatabase**. Pozostaw **Aprowizowanie przepływności bazy danych** pole niezaznaczone.  
   * Aby uzyskać **kolekcji** identyfikator wprowadź **changefeedlabcollection**.  
   * Aby uzyskać **klucza partycji** wprowadź **/Item**. Jest rozróżniana wielkość liter, więc upewnij się, że jej prawidłowo wpisana.  
   * Aby uzyskać **przepływności** wprowadź **10000**.  
   * Wybierz przycisk **OK**.  

3. Następnie utworzyć inną kolekcję o nazwie **dzierżawy** dla przetwarzania kanału informacyjnego zmian. Współrzędne kolekcji dzierżaw przetwarzania zestawienia zmian na wielu procesów roboczych. Oddzielne kolekcji służy do przechowywania dzierżaw przy użyciu jednej dzierżawy dla każdej partycji.  

4. Wróć do **Eksplorator danych** okienka, a następnie wybierz **Nowa kolekcja** i wypełnij formularz z następującymi szczegółami:

   * Aby uzyskać **bazy danych o identyfikatorze** pól, zaznacz **Użyj istniejącej**, wprowadź **changefeedlabdatabase**.  
   * Aby uzyskać **identyfikator kolekcji** wprowadź **dzierżawy**.  
   * Aby uzyskać **pojemność magazynu**, wybierz opcję **stałe**.  
   * Pozostaw **przepływności** pole jest ustawione na wartość domyślną.  
   * Wybierz przycisk **OK**.

## <a name="get-the-connection-string-and-keys"></a>Pobierz parametry połączenia i klucze

### <a name="get-the-azure-cosmos-db-connection-string"></a>Pobieranie parametrów połączenia usługi Azure Cosmos DB

1. Przejdź do [witryny Azure Portal](https://portal.azure.com/) i Znajdź **konta usługi Azure Cosmos DB** tworzone przez wdrożenie szablonu.  

2. Przejdź do **klucze** okienko, skopiuj podstawowe parametry połączenia i skopiować go do Notatnika lub innego dokumentu, które mają dostęp do w całym środowisku laboratoryjnym. Należy je oznaczyć **parametry połączenia usługi Cosmos DB**. Konieczne będzie później skopiuj ciąg do kodu, więc Zwróć uwagę i Zapamiętaj, gdzie przechowujesz go.

### <a name="get-the-storage-account-key-and-connection-string"></a>Pobierz parametry połączenia i klucz konta magazynu

Konta usługi Azure Storage umożliwiają przechowywanie danych. W tym środowisku laboratoryjnym użyjesz konta magazynu do przechowywania danych, który jest używany przez funkcję platformy Azure. Funkcja platformy Azure jest wyzwalana po każdej zmianie kolekcji.

1. Wróć do grupy zasobów i otworzyć konto magazynu, która została utworzona wcześniej  

2. Wybierz **klucze dostępu** z menu po lewej stronie.  

3. Skopiuj wartości w obszarze **klucz 1** do Notatnika lub innego dokumentu, które mają dostęp do w całym środowisku laboratoryjnym. Należy oznaczyć **klucz** jako **klucza magazynu** i **parametry połączenia** jako **parametry połączenia magazynu**. Konieczne będzie później skopiuj te ciągi do kodu, więc Zwróć uwagę i Zapamiętaj, gdzie przechowujesz je.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Pobierz parametry połączenia przestrzeni nazw Centrum zdarzeń

Usługi Azure Event Hub odbiera dane zdarzenia, magazyny, procesy i przekazuje dane. W tym środowisku laboratoryjnym, Azure Event Hub otrzyma dokumentu za każdym razem, gdy wystąpi zdarzenie nowe (czyli element jest wyświetlany przez użytkownika, dodane do koszyka użytkownika lub zakupione przez użytkownika) i następnie przekaże tego dokumentu do usługi Azure Stream Analytics.

1. Wróć do grupy zasobów, a następnie otwórz **Namespace Centrum zdarzeń** uprzednio utworzony i o nazwie w prelab.  

2. Wybierz **zasady dostępu współdzielonego** z menu po lewej stronie.  

3. Wybierz **RootManageSharedAccessKey**. Kopiuj **parametry połączenia — klucz podstawowy** do Notatnika lub innego dokumentu, które mają dostęp do w całym środowisku laboratoryjnym. Należy je oznaczyć **Namespace Centrum zdarzeń** parametry połączenia. Konieczne będzie później skopiuj ciąg do kodu, więc Zwróć uwagę i Zapamiętaj, gdzie przechowujesz go.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Konfigurowanie funkcji platformy Azure na odczytywanie zestawienia zmian

Po utworzeniu nowego dokumentu lub zmodyfikowaniu bieżącego dokumentu w kontenerze Cosmos, Źródło zmian automatycznie dodaje zmodyfikowany dokument do historii zmian kolekcji. Teraz utworzysz i uruchom funkcję platformy Azure, która przetwarza zestawienia zmian. Gdy dokument zostanie utworzony lub zmodyfikowany w utworzonej kolekcji, funkcja platformy Azure zostanie wyzwolone przez zestawienia zmian. Następnie funkcja platformy Azure będzie wysyłać zmodyfikowanego dokumentu do Centrum zdarzeń.

1. Wróć do repozytorium, z którego zostało sklonowane na urządzeniu.  

2. Kliknij prawym przyciskiem myszy plik o nazwie **ChangeFeedLabSolution.sln** i wybierz **Otwórz za pomocą programu Visual Studio**.  

3. Przejdź do **local.settings.json** w programie Visual Studio. Następnie należy użyć wartości, które zapisaną wcześniej, aby wypełnić puste miejsca.  

4. Przejdź do **ChangeFeedProcessor.cs**. W parametrach **Uruchom** funkcji, wykonaj następujące czynności:  

   * Zastąp tekst **tutaj nazwę KOLEKCJI YOUR** nazwą kolekcji. Jeśli wykonano instrukcje wcześniej nazwę kolekcji jest changefeedlabcollection.  
   * Zastąp tekst **Twojej dzierżawy KOLEKCJI nazwa tutaj** nazwą kolekcji dzierżaw. Jeśli wykonano instrukcje wcześniej jest nazwa kolekcji dzierżaw **dzierżawy**.  
   * W górnej części programu Visual Studio, upewnij się, że pole projektu startowego na zieloną strzałkę po lewej stronie jest wyświetlany komunikat **ChangeFeedFunction**.  
   * Wybierz **Start** w górnej części strony Aby uruchomić program  
   * Aby potwierdzić, że funkcja działa, gdy aplikacja konsoli jest wyświetlany komunikat "uruchomione zadanie hosta".

## <a name="insert-data-into-azure-cosmos-db"></a>Wstawianie danych do usługi Azure Cosmos DB 

Aby wyświetlić kanał informacyjny zmian przetwarzaniu nowe akcje w witrynie handlu elektronicznego, trzeba symulacji dane, które przedstawiają użytkownikom wyświetlanie elementów z katalogu produktów, dodanie tych elementów do ich koszyki i elementy w swoich koszyki zakupów. Dane te są dowolnego i na potrzeby replikacji danych w handlu elektronicznego witryna będzie wyglądać.

1. Przejdź z powrotem do repozytorium w Eksploratorze plików, a następnie kliknij prawym przyciskiem myszy **ChangeFeedFunction.sln** ponownie otworzyć nowe okno programu Visual Studio.  

2. Przejdź do pliku **App. config** . W bloku Dodaj punkt końcowy i unikatowy **klucz podstawowy** , który został wcześniej pobrany z konta Azure Cosmos DB. `<appSettings>`  

3. Dodaj w **kolekcji** i **bazy danych** nazwy. (Te nazwy powinny być **changefeedlabcollection** i **changefeedlabdatabase** dopóki nie zdecydujesz się nazwę należy do Ciebie inaczej.)

   ![Zaktualizuj parametry połączenia](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Zapisz zmiany we wszystkich plikach, które są edytowane.  

5. Upewnij się, że w górnej części programu Visual Studio **projekt startowy** pole na zieloną strzałkę po lewej stronie jest wyświetlany komunikat **DataGenerator**. Następnie wybierz pozycję **Start** w górnej części strony Aby uruchomić program.  
 
6. Poczekaj, aż program do uruchomienia. Gwiazdki oznaczają, że pochodzą dane! Zachowaj uruchomiony program — jest ważne, zbierane dużą ilość danych.  

7. Jeśli przejdziesz do [witryny Azure Portal](https://portal.azure.com/) , następnie Cosmos DB konta w danej grupie zasobów, następnie do **Eksplorator danych**, zobaczysz dane losowego, zaimportowane w Twojej  **changefeedlabcollection** .
 
   ![Dane wygenerowane w portalu](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Konfigurowanie zadania usługi stream analytics

Usługa Azure Stream Analytics jest w pełni zarządzana usługa w chmurze do przetwarzania w czasie rzeczywistym danych przesyłanych strumieniowo. W tym środowisku laboratoryjnym użyjesz usługi stream analytics można przetwarzać nowych zdarzeń z Centrum zdarzeń (tj. gdy element jest wyświetlane, dodane do koszyka lub kupić), włączenie tych zdarzeń do analizy danych w czasie rzeczywistym i wysyłać je do usługi Power BI dla wizualizacji.

1. Z [witryny Azure Portal](https://portal.azure.com/), następnie przejdź do grupy zasobów, do **streamjob1** (zadanie usługi stream analytics utworzone w prelab).  

2. Wybierz **dane wejściowe** jak pokazano poniżej.  

   ![Tworzenie wejściowych](./media/changefeed-ecommerce-solution/create-input.png)

3. Wybierz **+ Dodaj wejście strumienia**. Następnie wybierz pozycję **Centrum zdarzeń** z menu rozwijanego.  

4. Wprowadź nowy formularz danych wejściowych z następującymi szczegółami:

   * W **wejściowych** alias pole, wprowadź **wejściowych**.  
   * Wybierz opcję dla **wybierz Centrum zdarzeń z subskrypcji**.  
   * Ustaw **subskrypcji** pola do Twojej subskrypcji.  
   * W **przestrzeń nazw Centrum zdarzeń** wprowadź nazwę swojej Namespace Centrum zdarzeń, został utworzony podczas prelab.  
   * W **nazwy Centrum zdarzeń** wybierz opcję **Użyj istniejącej** i wybierz polecenie **hub1 zdarzeń** z menu rozwijanego.  
   * Pozostaw **zasad Centrum zdarzeń** pole nazwy jest ustawiona na wartość domyślną.  
   * Pozostaw **format serializacji zdarzeń** jako **JSON**.  
   * Pozostaw **pola Encoding** równa **UTF-8**.  
   * Pozostaw **typ kompresji zdarzenia** pola **Brak**.  
   * Wybierz ikonę **Zapisz**.

5. Przejdź z powrotem do strony zadania usługi stream analytics, a następnie wybierz pozycję **dane wyjściowe**.  

6. Wybierz pozycję **+ Dodaj**. Następnie wybierz pozycję **usługi Power BI** z menu rozwijanego.  

7. Aby utworzyć nowe dane wyjściowe usługi Power BI można wizualizować średnią cenę, wykonaj następujące czynności:

   * W **alias wyjściowy** wprowadź **averagePriceOutput**.  
   * Pozostaw **obszaru roboczego grupy** pola **Autoryzuj połączenie, aby załadować obszarów roboczych**.  
   * W **Nazwa zestawu danych** wprowadź **averagePrice**.  
   * W **nazwy tabeli** wprowadź **averagePrice**.  
   * Wybierz **Autoryzuj** przycisk, a następnie postępuj zgodnie z instrukcjami, aby autoryzować połączenie do usługi Power BI.  
   * Wybierz ikonę **Zapisz**.  

8. Następnie wróć do **streamjob1** i wybierz **Edytuj zapytanie**.

   ![Edytuj zapytanie](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Wklej poniższe zapytanie w oknie zapytania. **Średnia cena** zapytanie oblicza średnią cenę wszystkie elementy, które są wyświetlane przez użytkowników, średnia cena wszystkie elementy, które są dodawane do użytkowników, koszyki i średnia cena wszystkie elementy, które zostały nabyte przez użytkowników. Ta metryka może pomóc firmom handlu elektronicznego, zdecyduj, jakie ceny na sprzedaż elementów w i co spisu o wartości inwestycji w. Na przykład jeśli średnia cena elementy wyświetlane jest znacznie wyższa niż średnia cena zakupu elementów, firma może wybrać do dodawania elementów mniej kosztowne do jego magazynu.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Następnie wybierz pozycję **Zapisz** w lewym górnym rogu.  

11. Teraz wróć do **streamjob1** i wybierz **Start** znajdujący się u góry strony. Usługa Azure Stream Analytics może potrwać kilka minut, aby uruchomić, ale ostatecznie zobaczysz go zmienić z "Uruchamianie" na "Uruchomiona".

## <a name="connect-to-power-bi"></a>Połączenia z usługą Power BI

Usługa Power BI to pakiet narzędzi do analizy biznesowej do analizowania danych i udostępniania szczegółowych informacji. To doskonałe przykładem sposobu strategicznie wizualizować analizowanych danych.

1. Zaloguj się do usługi Power BI i przejdź do **Mój obszar roboczy** , otwierając menu po lewej stronie strony.  

2. Wybierz **+ Utwórz** w prawym górnym rogu, a następnie wybierz **pulpit nawigacyjny** tworzenia pulpitu nawigacyjnego.  

3. Wybierz **+ Dodaj Kafelek** w prawym górnym rogu.  

4. Wybierz **niestandardowe dane przesyłane strumieniowo**, a następnie wybierz **dalej** przycisku.  
 
5. Wybierz **averagePrice** z **YOUR zestawów danych**, a następnie wybierz **dalej**.  

6. W **typ wizualizacji** wybierz opcję **wykres słupkowy grupowany** z menu rozwijanego. W obszarze **osi**, Dodaj akcję. Pomiń **legendy** bez dodawania niczego. Następnie, w następnej sekcji o nazwie **wartość**, Dodaj **avg**. Wybierz **dalej**, a następnie tytułu wykresu i wybierz **Zastosuj**. Powinien zostać wyświetlony nowy wykres na pulpicie nawigacyjnym!  

7. Teraz, jeśli chcesz zwizualizować więcej metryk, możesz przejść wstecz do **streamjob1** i Utwórz trzy więcej danych wyjściowych z następującymi polami.

   a. **Alias wyjściowy:** incomingRevenueOutput, nazwa zestawu danych: incomingRevenue, nazwa tabeli: incomingRevenue  
   b. **Alias wyjściowy:** top5Output, nazwa zestawu danych: top5, nazwa tabeli: top5  
   c. **Alias wyjściowy:** uniqueVisitorCountOutput, nazwa zestawu danych: uniqueVisitorCount, nazwa tabeli: uniqueVisitorCount

   Następnie wybierz pozycję **Edytuj zapytanie** i wklej następujące zapytania **powyżej** ten, który już powstała z jednego.

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
   
   Zapytanie 5 PIERWSZYCH oblicza 5 pierwszych elementów, uporządkowanych według liczby, które zostały zakupione. Ta metryka może pomóc firmom handlu elektronicznego, ocenić elementy, które są najbardziej popularne i może mieć wpływ na reklamy w firmie, cen i tworzenie spisu decyzji.

   Zapytanie przychód oblicza przychodów przez zsumowanie ceny wszystkich elementów, które zakupione co minutę. Ta metryka może pomóc firmom handlu elektronicznego, ocenić jej wyniki finansowe i zrozumieć, jakie porach dnia Współtworzenie największe przychody. Może to mieć wpływ na ogólnej strategii firmy, w szczególności marketingowych.

   Zapytanie unikatowych osób ODWIEDZAJĄCYCH oblicza liczbę unikatowych osób odwiedzających znajdują się w lokacji co 5 sekund, przez wykrycie koszyka unikatowe identyfikatory Ta metryka może pomóc firmom handlu elektronicznego, ocenić ich działania lokacji i ułożeniu dla niego strategii sposób w celu uzyskania większej liczby klientów.

8. Teraz możesz dodać Kafelki także te zestawy danych.

   * Dla 5 pierwszych sensowne będzie celu wykres kolumnowy grupowany z elementów jako oś oraz liczbę jako wartość.  
   * Dla przychodu sensowne będzie celu wykres liniowy z godziną jako osi i sumę cen jako wartość. Okno czasowe do wyświetlenia powinna być największa możliwa do zapewnienia możliwie najwięcej informacji.  
   * Dla unikatowych osób odwiedzających sensowne będzie celu Wizualizacja w postaci karty przy użyciu liczby unikatowych osób odwiedzających jako wartość.

   Jest to, jak wygląda przykładowy pulpit nawigacyjny za pomocą tych wykresów:

   ![wizualizacje](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>Opcjonalnie: Wizualizowanie w witrynie handlu elektronicznego

Teraz odbywa się w sposób korzystania z nowego narzędzia analizy danych nawiązać połączenia z lokacją rzeczywistych handlu elektronicznego. Aby zbudować witrynę handlu elektronicznego, należy użyć bazy danych usługi Azure Cosmos do przechowywania listy kategorii produktów (kobiety, mężczyźni, Unisex), katalogu produktów i listy najpopularniejszych elementów.

1. Przejdź z powrotem do [witryny Azure Portal](https://portal.azure.com/), następnie do swojej **konta usługi Cosmos DB**, następnie do **Eksplorator danych**.  

   Dodaj dwie kolekcje w obszarze **changefeedlabdatabase** - **produktów** i **kategorie** z pojemnością magazynu stałych.

   Dodaj inną kolekcję w węźle **changefeedlabdatabase** o nazwie **topItems** i **/Item** jako klucza partycji.

2. Wybierz **topItems** kolekcji, a następnie w obszarze **skalowanie i ustawienia** ustaw **czas wygaśnięcia** jako **30 sekund** tak topItems tej aktualizacji co 30 sekund.

   ![Czas wygaśnięcia](./media/changefeed-ecommerce-solution/time-to-live.png)

3. Aby można było wypełnić **topItems** kolekcji z najczęściej zakupione elementy, przejdź z powrotem do **streamjob1** i Dodaj nowy **dane wyjściowe**. Wybierz **usługi Cosmos DB**.

4. Wypełnij wymagane pola, jak pokazano poniżej.

   ![Dane wyjściowe cosmos](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. Opcjonalne zapytanie 5 PIERWSZYCH został dodany w poprzedniej części laboratorium, należy przejść do części 5a. W przeciwnym razie przejdź do części 5b.

   5a. W **streamjob1**, wybierz opcję **Edytuj zapytanie** i wklej poniższe zapytanie w edytorze zapytań usługi Azure Stream Analytics poniżej zapytania 5 PIERWSZYCH, ale powyżej pozostałej części zapytania.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. W **streamjob1**, wybierz opcję **Edytuj zapytanie** i wklej poniższe zapytanie w edytorze zapytań usługi Azure Stream Analytics przede wszystkim inne zapytania.

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

6. Otwórz **EcommerceWebApp.sln** i przejdź do **Web.config** w pliku **Eksploratora rozwiązań**.  

7. W ramach `<appSettings>` zablokować, Dodaj **URI** i **klucza podstawowego** zapisany wcześniej gdzie jest wyświetlany komunikat **URI w tym miejscu** i **tutaj klucz podstawowy**. Następnie dodaj w swojej **Nazwa bazy danych** i **Nazwa kolekcji** wskazane. (Te nazwy powinny być **changefeedlabdatabase** i **changefeedlabcollection** , chyba że zdecydujesz się na nazwę należy do Ciebie inaczej.)

   Wypełnij swoje **Nazwa kolekcji produktów**, **Nazwa kolekcji kategorie**, i **Nazwa kolekcji pierwszych elementów** wskazane. (Te nazwy powinny być **produktów, kategorie i topItems** , chyba że zdecydujesz się na nazwę należy do Ciebie inaczej.)  

8. Przejdź do, a następnie otwórz **folder wyewidencjonowania** w ramach **EcommerceWebApp.sln.** Następnie otwórz **Web.config** plik w tym folderze.  

9. W ramach `<appSettings>` zablokować, Dodaj **URI** i **klucz podstawowy** zapisaną wcześniej w przypadku, gdy wskazane. Następnie dodaj w swojej **Nazwa bazy danych** i **Nazwa kolekcji** wskazane. (Te nazwy powinny być **changefeedlabdatabase** i **changefeedlabcollection** , chyba że zdecydujesz się na nazwę należy do Ciebie inaczej.)  

10. Naciśnij klawisz **Start** w górnej części strony Aby uruchomić program.  

11. Można teraz odtworzyć, w witrynie handlu elektronicznego. Podczas wyświetlania elementu, dodać element do koszyka, zmień liczbę elementów w koszyku lub zakupu element te zdarzenia zostaną przekazane za pośrednictwem usługi Cosmos DB zmiany źródła danych do Centrum zdarzeń, ASA i następnie usługa Power BI. Zaleca się kontynuowania działania DataGenerator do generowania danych ruchu znaczące sieci web i zapewniają realistyczne zestaw produktów"gorąca" w witrynie handlu elektronicznego.

## <a name="delete-the-resources"></a>Usuń zasoby

Aby usunąć zasoby utworzone w ramach tego laboratorium, przejdź do grupy zasobów na [witryny Azure Portal](https://portal.azure.com/), a następnie wybierz **Usuń grupę zasobów** menu w górnej części strony i postępuj zgodnie z instrukcjami podana.

## <a name="next-steps"></a>Kolejne kroki 
  
* Aby dowiedzieć się więcej na temat zmian, zobacz [pracy ze zmianą Obsługa kanału informacyjnego w usłudze Azure Cosmos DB](change-feed.md) 
* [Rozwiązanie powiadomień kanału informacyjnego zmian](change-feed-hl7-fhir-logic-apps.md) dla organizacji opieki zdrowotnej przy użyciu usługi Azure Cosmos DB.
