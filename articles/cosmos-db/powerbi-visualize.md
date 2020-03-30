---
title: Samouczek usługi Power BI dla łącznika usługi Azure Cosmos DB
description: Ten samouczek usługi Power BI służy do importowania json, tworzenia wnikliwych raportów i wizualizacji danych przy użyciu usługi Azure Cosmos DB i łącznika usługi Power BI.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 1dbdd428a54ebf38c7b880bb9530935c0f748226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69616808"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Wizualizowanie danych usługi Azure Cosmos DB przy użyciu łącznika usługi Power BI

[Usługa Power BI](https://powerbi.microsoft.com/) to usługa online, w której można tworzyć i udostępniać pulpity nawigacyjne i raporty. Power BI Desktop to narzędzie do tworzenia raportów, które umożliwia pobieranie danych z różnych źródeł danych. Usługa Azure Cosmos DB jest jednym ze źródeł danych, którego możesz użyć w aplikacji Power BI Desktop. Możesz połączyć aplikację Power BI Desktop z kontem usługi Azure Cosmos DB przy użyciu łącznika usługi Azure Cosmos DB dla usługi Power BI.  Po zaimportowaniu danych usługi Azure Cosmos DB do usługi Power BI możesz przekształcić je, utworzyć raporty i opublikować te raporty w usłudze Power BI.   

W tym artykule opisano kroki wymagane do połączenia konta usługi Azure Cosmos DB z aplikacją Power BI Desktop. Po nawiązaniu połączenia możesz przejść do dowolnej kolekcji, wyodrębnić dane, przekształcić dane JSON na format tabelaryczny i opublikować raport w usłudze Power BI.

> [!NOTE]
> Łącznik usługi Power BI dla usługi Azure Cosmos DB łączy się z programem Power BI Desktop. Raporty utworzone w programie Power BI Desktop można publikować w PowerBI.com. Bezpośrednie wyodrębnianie danych usługi Azure Cosmos DB nie może być wykonywane z PowerBI.com. 

> [!NOTE]
> Łączenie się z usługą Azure Cosmos DB za pomocą łącznika usługi Power BI jest obecnie obsługiwane tylko dla kont interfejsu API SQL usługi Azure Cosmos DB i interfejsu API Gremlin.

## <a name="prerequisites"></a>Wymagania wstępne
Przed postępem w tym samouczku dotyczącym usługi Power BI upewnij się, że masz dostęp do następujących zasobów:

* [Pobierz najnowszą wersję programu Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Pobierz [przykładowe dane wulkanu](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) z gitHub.

* [Utwórz konto bazy danych usługi Azure Cosmos](https://azure.microsoft.com/documentation/articles/create-account/) i zaimportuj dane wulkanu za pomocą [narzędzia do migracji danych usługi Azure Cosmos DB](import-data.md). Podczas importowania danych należy wziąć pod uwagę następujące ustawienia źródła i miejsc docelowych w narzędziu migracji danych:

   * **Parametry źródła** 

       * **Import z:** Pliki JSON

   * **Parametry docelowe** 

      * **Parametry połączenia:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Klucz partycji:** /Country 

      * **Przepustowość zbierania:** 1000 

Aby udostępnić raporty w PowerBI.com, musisz mieć konto w PowerBI.com.  Aby dowiedzieć się więcej o usłudze Power BI i usłudze Power BI Pro, zobacz [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Zaczynajmy
W tym samouczku wyobraźmy sobie, że jesteś geologiem badającym wulkany na całym świecie. Dane wulkanu są przechowywane na koncie usługi Azure Cosmos DB, a format dokumentu JSON jest następujący:

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

Zostaną pobrane dane wulkanu z konta usługi Azure Cosmos DB i wizualizować dane w interaktywnym raporcie usługi Power BI.

1. Uruchamianie programu Power BI Desktop.

2. Możesz **uzyskać dane**, zobacz **Ostatnie źródła**lub Otwórz **inne raporty** bezpośrednio z ekranu powitalnego. Wybierz "X" w prawym górnym rogu, aby zamknąć ekran. Zostanie wyświetlony widok **raportu** programu Power BI Desktop.
   
   ![Widok raportu pulpitu usługi Power BI — złącze Usługi Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Wybierz wstążkę **Strona główna,** a następnie kliknij **pozycję Pobierz dane**.  Powinno się pojawić okno **Pobierz dane.**

4. Kliknij pozycję **Azure**, wybierz **pozycję Azure Cosmos DB (Beta),** a następnie kliknij pozycję **Połącz**. 

    ![Pobierz dane z programu Power BI Desktop — złącze Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. Na stronie **Łącznik podglądu** kliknij pozycję **Kontynuuj**. Zostanie wyświetlene okno **usługi Azure Cosmos DB.**

6. Określ adres URL punktu końcowego konta usługi Azure Cosmos DB, z którego chcesz pobrać dane, jak pokazano poniżej, a następnie kliknij przycisk **OK**. Aby użyć własnego konta, można pobrać adres URL z pola identyfikator URI w klucze bloku **witryny** Azure portal. Opcjonalnie można podać nazwę bazy danych, nazwę kolekcji lub użyć nawigatora, aby wybrać bazę danych i kolekcję, aby zidentyfikować, skąd pochodzą dane.
   
7. Jeśli łączysz się z tym punktem końcowym po raz pierwszy, zostanie wyświetlony monit o klucz konta. Dla własnego konta pobierz klucz z **pola Klucz podstawowy** w bloku Klucze tylko do **odczytu** w witrynie Azure portal. Wprowadź odpowiedni klawisz, a następnie kliknij przycisk **Połącz**.
   
   Zaleca się używanie klucza tylko do odczytu podczas tworzenia raportów. Zapobiega to niepotrzebnemu narażeniu klucza głównego na potencjalne zagrożenia bezpieczeństwa. Klucz tylko do odczytu jest dostępny w bloku **Klucze** witryny Azure Portal. 
    
8. Po pomyślnym nawiązaniu połączenia konta zostanie wyświetlone okienko **Nawigator.** **Nawigator** pokazuje listę baz danych w ramach konta.

9. Kliknij i rozwiń w bazie danych, z której pochodzą dane raportu, wybierz **volcanodb** (nazwa bazy danych może być inna).   

10. Teraz wybierz kolekcję, która zawiera dane do pobrania, wybierz **volcano1** (nazwa kolekcji może być inna).
    
    W okienku Podgląd jest wyświetlana lista elementów **rekordu.**  Dokument jest reprezentowany jako typ **rekordu** w usłudze Power BI. Podobnie zagnieżdżony blok JSON wewnątrz dokumentu jest również **Rekord**.
    
    ![Samouczek usługi Power BI dla łącznika usługi Power BI usługi Azure Cosmos DB — okno Nawigator](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Kliknij **przycisk Edytuj,** aby uruchomić Edytor zapytań w nowym oknie, aby przekształcić dane.

## <a name="flattening-and-transforming-json-documents"></a>Spłaszczanie i przekształcanie dokumentów JSON
1. Przełącz się do okna Edytora zapytań usługi Power BI, w którym znajduje się **kolumna Dokument** w środkowym okienku.
   ![Edytor zapytań programu Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Kliknij rozwijacz po prawej stronie nagłówka kolumny **Dokument.**  Pojawi się menu kontekstowe z listą pól.  Wybierz pola potrzebne do raportu, na przykład nazwa wulkanu, kraj, region, lokalizacja, elewacja, typ, stan i ostatni wybuch wiedzy. Wyczyść pole **wyboru Użyj oryginalnej nazwy kolumny jako prefiksu,** a następnie kliknij przycisk **OK**.
   
    ![Samouczek usługi Power BI dla łącznika usługi Azure Cosmos DB Power BI — rozwiń dokumenty](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. W okienku środkowym jest wyświetlany podgląd wyniku z zaznaczonymi polami.
   
    ![Samouczek usługi Power BI dla łącznika usługi Power BI usługi Azure Cosmos — spłaszczyć wyniki](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. W naszym przykładzie Location właściwość jest geojson bloku w dokumencie.  Jak widać, lokalizacja jest reprezentowana jako typ **rekordu** w programie Power BI Desktop.  
5. Kliknij rozwiadź rozwiań po prawej stronie nagłówka kolumny Document.Location.  Zostanie wyświetlone menu kontekstowe z polami typu i współrzędnych.  Zaznaczmy pole współrzędne, upewnijmy się, że **użyj oryginalnej nazwy kolumny jako prefiksu,** a następnie kliknij przycisk **OK**.
   
    ![Samouczek usługi Power BI dla łącznika usługi Power BI usługi Azure Cosmos DB — rekord lokalizacji](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. W środkowym okienku jest teraz wyświetlana kolumna współrzędnych typu **Lista.**  Jak pokazano na początku samouczka, dane GeoJSON w tym samouczku jest typu punktu z szerokości i długości geograficznej wartości zarejestrowanych w tablicy współrzędnych.
   
    Element współrzędnych[0] reprezentuje długość geograficzną, podczas gdy współrzędne[1] reprezentują szerokość geograficzną.
    ![Samouczek usługi Power BI dla łącznika usługi Power BI usługi Azure Cosmos — lista współrzędnych](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Aby spłaszczyć tablicę współrzędnych, należy utworzyć **kolumnę niestandardową** o nazwie LatLong.  Zaznacz wstążkę **Dodaj kolumnę** i kliknij **kolumnę niestandardową**.  Zostanie **wyświetlene okno Kolumna niestandardowa.**
8. Podaj nazwę nowej kolumny, np.
9. Następnie określ niestandardową formułę dla nowej kolumny.  W naszym przykładzie połączymy wartości szerokości i długości geograficznej oddzielone przecinkiem, `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`jak pokazano poniżej, przy użyciu następującej formuły: . Kliknij przycisk **OK**.
   
    Aby uzyskać więcej informacji na temat wyrażeń analizy danych (JĘZYK DAX), w tym funkcji języka DAX, odwiedź stronę [język DAX Basics w programie Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics).
   
    ![Samouczek usługi Power BI dla łącznika usługi Power BI usługi Azure Cosmos DB — dodawanie kolumny niestandardowej](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Teraz w środkowym okienku są wyświetlane nowe kolumny LatLong wypełnione wartościami.
    
    ![Samouczek usługi Power BI dla łącznika usługi Power BI usługi Azure Cosmos DB — kolumna Niestandardowe latlong](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Jeśli w nowej kolumnie zostanie wyświetlony błąd, upewnij się, że zastosowane kroki w obszarze Ustawienia kwerendy są zgodne z następującym rysunkiem:
    
    ![Zastosowane kroki powinny być źródło, nawigacja, rozszerzony dokument, rozszerzony dokument.lokalizacja, dodano niestandardowe](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Jeśli kroki są różne, usuń dodatkowe kroki i spróbuj ponownie dodać kolumnę niestandardową. 

11. Kliknij **przycisk Zamknij i Zastosuj,** aby zapisać model danych.
    
    ![Samouczek usługi Power BI dla łącznika usługi Power BI usługi Azure Cosmos DB — zamknij & zastosuj](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Tworzenie raportów
Widok raportu pulpitu usługi Power BI to miejsce, w którym można rozpocząć tworzenie raportów w celu wizualizacji danych.  Raporty można tworzyć, przeciągając i upuszczając pola do kanwy **raportu.**

![Widok raportu pulpitu usługi Power BI — złącze Usługi Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

W widoku Raport powinieneś znaleźć:

1. W okienku **Pola** jest miejsce, w którym można wyświetlić listę modeli danych z polami, których można używać w raportach.
2. Okienko **Wizualizacje.** Raport może zawierać pojedyncze lub wiele wizualizacji.  Wybierz typy wizualizacji dopasowane do twoich potrzeb z okienka **Wizualizacje.**
3. **Kanwa raportu,** w tym miejscu tworzysz wizualizacje raportu.
4. Strona **Raport.** W programie Power BI Desktop można dodać wiele stron raportu.

Poniżej przedstawiono podstawowe kroki tworzenia prostego interaktywnego raportu widoku mapy.

1. W naszym przykładzie utworzymy widok mapy pokazujący lokalizację każdego wulkanu.  W okienku **Wizualizacje** kliknij typ wizualizacji Mapa, jak pokazano na powyższym zrzucie ekranu.  Typ wizualizacji Mapy powinien być namalowany na kanwie **raportu.**  W okienku **Wizualizacja** powinien również być wyświetlany zestaw właściwości związanych z typem wizualizacji Mapy.
2. Teraz przeciągnij i upuść pole LatLong z okienka **Pola** do właściwości **Lokalizacja** w okienku **Wizualizacje.**
3. Następnie przeciągnij i upuść pole Nazwa wulkanu do właściwości **Legenda.**  
4. Następnie przeciągnij i upuść pole Rzędna do właściwości **Rozmiar.**  
5. Teraz powinieneś zobaczyć wizualizację mapy pokazującą zestaw bąbelków wskazujących położenie każdego wulkanu o wielkości bańki korelującej z wysokością wulkanu.
6. Teraz utworzono raport podstawowy.  Można dodatkowo dostosować raport, dodając więcej wizualizacji.  W naszym przypadku dodaliśmy fragmentator typu wulkanu, aby raport był interaktywny.  
   
7. W menu Plik kliknij polecenie **Zapisz** i zapisz plik jako program PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Publikowanie i udostępnianie raportu
Aby udostępnić raport, musisz mieć konto w PowerBI.com.

1. Na pulpicie usługi Power BI kliknij wstążkę **Strona główna.**
2. Kliknij przycisk **Opublikuj**.  Zostanie wyświetlony monit o wprowadzenie nazwy użytkownika i hasła do konta PowerBI.com.
3. Po uwierzytelnieniu poświadczenia raport jest publikowany w wybranym miejscu docelowym.
4. Kliknij **pozycję Otwórz "PowerBITutorial.pbix" w usłudze Power BI,** aby wyświetlić i udostępnić raport w PowerBI.com.
   
    ![Publikowanie w usłudze Power BI Success! Otwieranie samouczka w usłudze Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Utworzenie pulpitu nawigacyjnego w usłudze PowerBI.com
Teraz, gdy masz raport, udostępnij go na PowerBI.com

Podczas publikowania raportu z programu Power BI Desktop do PowerBI.com generuje **raport** i **zestaw danych** w dzierżawie PowerBI.com. Na przykład po opublikowaniu raportu o nazwie **PowerBITutorial** do PowerBI.com, zobaczysz powerbitutorial w sekcji **Raporty** i **zestawy danych** na PowerBI.com.

   ![Zrzut ekranu przedstawiający nowy raport i zestaw danych w PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Aby utworzyć pulpit nawigacyjny umożliwiający udostępnienie, kliknij przycisk **Przypnij żywą stronę** w raporcie PowerBI.com.

   ![Zrzut ekranu przedstawiający nowy raport i zestaw danych w PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Następnie postępuj zgodnie z instrukcjami w [obszarze Przypnij kafelek z raportu,](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) aby utworzyć nowy pulpit nawigacyjny. 

Przed utworzeniem pulpitu nawigacyjnego można również wykonać modyfikacje ad hoc w celu raportowania. Zaleca się jednak, aby wykonać modyfikacje i ponownie opublikować raport w celu PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o usłudze Power BI, zobacz [Wprowadzenie do usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Aby dowiedzieć się więcej o usłudze Azure Cosmos DB, zobacz [stronę docelową dokumentacji usługi Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

