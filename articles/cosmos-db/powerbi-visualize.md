---
title: Usługa Power BI samouczek dla łącznika usługi Azure Cosmos DB
description: Użyj tego samouczka usługi Power BI, importować JSON, tworzyć raporty bezpośrednio i wizualizuj dane przy użyciu łącznika usługi Azure Cosmos DB i usługa Power BI.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 76531de279dfe6e9b73b3895f0ef63c4c88b63cd
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979007"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Wizualizuj dane z usługi Azure Cosmos DB przy użyciu łącznika usługi Power BI

[Usługa Power BI](https://powerbi.microsoft.com/) to usługa online, na którym można tworzyć i udostępniać pulpity nawigacyjne i raporty. Power BI Desktop jest narzędziu, która umożliwia pobieranie danych z różnych źródeł danych raportu. Usługa Azure Cosmos DB jest jednym z źródła danych, które można użyć w programie Power BI Desktop. Power BI Desktop możesz połączyć konto usługi Azure Cosmos DB za pomocą łącznika usługi Azure Cosmos DB dla usługi Power BI.  Po zaimportowaniu danych usługa Azure Cosmos DB do usługi Power BI, można przekształcić je, tworzenie raportów i publikowania raportów usługi Power BI.   

W tym artykule opisano kroki wymagane do połączenia z kontem usługi Azure Cosmos DB Power BI Desktop. Po nawiązaniu połączenia możesz przejść do kolekcji, wyodrębniania danych, przekształcanie danych JSON w formacie tabelarycznym i opublikowania raportu usługi Power BI.

> [!NOTE]
> Łącznik usługi Power BI dla usługi Azure Cosmos DB łączy w programie Power BI Desktop. Raporty utworzone w programie Power BI Desktop mogą być publikowane w witrynie PowerBI.com. Nie można przeprowadzić bezpośredniego wyodrębniania danych z usługi Azure Cosmos DB z witryny PowerBI.com. 

> [!NOTE]
> Łączenie z usługą Azure Cosmos DB za pomocą łącznika usługi Power BI jest obecnie obsługiwane dla interfejsu API SQL usługi Azure Cosmos DB i tylko konta interfejsu API języka Gremlin.

## <a name="prerequisites"></a>Wymagania wstępne
Przed wykonaniem instrukcji zawartych w tym samouczku usługa Power BI, upewnij się, że masz dostęp do następujących zasobów:

* [Pobierz najnowszą wersję programu Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Pobierz [przykładowe dane swe dzieła](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) z usługi GitHub.

* [Tworzenie konta bazy danych usługi Azure Cosmos DB](https://azure.microsoft.com/documentation/articles/create-account/) i zaimportuj dane swe dzieła przy użyciu [narzędzia migracji danych usługi Azure Cosmos DB](import-data.md). Podczas importowania danych, należy wziąć pod uwagę następujące ustawienia dla źródła i miejsca docelowe w narzędziu do migracji danych:

   * **Parametry źródła** 

       * **Importuj z:** Pliki JSON

   * **Parametry docelowego** 

      * **Parametry połączenia:** `AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Klucz partycji:**  /kraj 

      * **Przepływność kolekcji:** 1000 

Udostępnianie raportów w witrynie PowerBI.com, musisz mieć konto w witrynie PowerBI.com.  Aby dowiedzieć się więcej na temat usługi Power BI i programie Power BI Pro, zobacz [ https://powerbi.microsoft.com/pricing ](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Zaczynajmy
W tym samouczku Wyobraźmy sobie czy geologist, bada wulkany na całym świecie. Dane swe dzieła są przechowywane na koncie usługi Azure Cosmos DB i format dokumentu JSON jest następujący:

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

Spowoduje pobranie danych swe dzieła z konta usługi Azure Cosmos DB i wizualizowanie danych w interaktywnych raportów usługi Power BI.

1. Uruchom program Power BI Desktop.

2. Możesz **Pobierz dane**, zobacz **ostatnio używane źródła**, lub **Otwórz inne raporty** bezpośrednio na ekranie powitalnym. Wybierz pozycję "X" w prawym górnym rogu, aby zamknąć ekran. **Raportu** wyświetlany jest widok programu Power BI Desktop.
   
   ![Usługa Power BI Desktop widoku raportu — łącznik usługi Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Wybierz **Home** wstążki, a następnie kliknij pozycję **Pobierz dane**.  **Pobierz dane** powinno pojawić się okno.

4. Kliknij pozycję **Azure**, wybierz opcję **usługi Azure Cosmos DB (Beta)**, a następnie kliknij przycisk **Connect**. 

    ![Usługa Power BI Desktop, Pobierz dane - łącznik usługi Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. Na **łącznik w wersji zapoznawczej** kliknij **Kontynuuj**. **Usługi Azure Cosmos DB** zostanie wyświetlone okno.

6. Określ URL punktu końcowego konta usługi Azure Cosmos DB będzie chcesz pobrać dane, jak pokazano poniżej, a następnie kliknij przycisk **OK**. Aby użyć własnego konta, możesz pobrać adres URL w polu identyfikatora URI w **klucze** bloku witryny Azure portal. Możesz opcjonalnie podaj nazwę bazy danych, a nazwa kolekcji lub umożliwia Nawigator wybierz bazę danych i kolekcji do identyfikowania, skąd pochodzą dane.
   
7. Jeśli łączysz się ten punkt końcowy po raz pierwszy, zostanie wyświetlony monit o klucz konta. Dla konta użytkownika, należy pobrać klucza z **klucza podstawowego** pole w **klucze tylko do odczytu** bloku witryny Azure portal. Wprowadź odpowiedni klucz, a następnie kliknij przycisk **Connect**.
   
   Zalecamy użycie klucza tylko do odczytu, podczas tworzenia raportów. Zapobiega to niepotrzebnym ujawnieniem klucz główny na potencjalne zagrożenia bezpieczeństwa. Klucz tylko do odczytu jest dostępny z **klucze** bloku witryny Azure portal. 
    
8. Gdy konto zostanie pomyślnie połączony, **Nawigator** zostanie wyświetlone okienko. **Nawigator** pokazuje listę baz danych w ramach konta.

9. Kliknij i rozwiń węzeł bazy danych, gdzie dane dla raportu, który jest dostarczany, wybierz **volcanodb** (nazwa bazy danych może się różnić).   

10. Teraz wybierz kolekcję, która zawiera dane, aby pobrać, zaznacz **volcano1** (nazwę kolekcji może się różnić).
    
    Okienko podglądu pokazuje listę **rekordu** elementów.  Dokument jest reprezentowany jako **rekordu** typu w usłudze Power BI. Podobnie, zagnieżdżony blok JSON wewnątrz dokument jest również **rekordu**.
    
    ![Usługa Power BI samouczek dla łącznika usługi Azure Cosmos DB w usłudze Power BI — oknie Nawigator](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Kliknij przycisk **Edytuj** można uruchomić edytora zapytań w nowym oknie do przekształcania danych.

## <a name="flattening-and-transforming-json-documents"></a>Spłaszczanie i przekształcanie dokumentów JSON
1. Przejdź do okna edytora zapytań programu Power BI, gdzie **dokumentu** kolumny w środkowym okienku.
   ![Edytor zapytań programu Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Ekspander po prawej stronie kliknij pozycję **dokumentu** nagłówek kolumny.  Pojawi się menu kontekstowe z listy pól.  Wybierz pola, czego potrzebujesz do raportu, na przykład swe dzieła kraj, Region, lokalizacji, podniesienia uprawnień, stan i typ ostatniego wulkanu wiedzieć. Usuń zaznaczenie pola wyboru **Użyj oryginalnej nazwy kolumny jako prefiksu** , a następnie kliknij przycisk **OK**.
   
    ![Usługa Power BI samouczek dla łącznika usługi Azure Cosmos DB w usłudze Power BI — rozwiń dokumentów](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. W środkowym okienku wyświetlane Podgląd wyników za pomocą pól wybranych.
   
    ![Usługa Power BI samouczek dla łącznika usługi Azure Cosmos DB w usłudze Power BI — spłaszczanie wyników](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. W tym przykładzie właściwość lokalizacji jest blokiem GeoJSON w dokumencie.  Jak widać, lokalizacja jest reprezentowany jako **rekordu** typu w programie Power BI Desktop.  
5. Polecenie ekspander po prawej stronie Document.Location nagłówek kolumny.  Menu kontekstowe z polami typu i współrzędne są wyświetlane.  Teraz wybierz pola współrzędnych, upewnij się, **Użyj oryginalnej nazwy kolumny jako prefiksu** nie jest zaznaczone, a następnie kliknij przycisk **OK**.
   
    ![Usługa Power BI samouczek dla łącznika usługi Azure Cosmos DB w usłudze Power BI — przy użyciu rekordu lokalizacji](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. W okienku Centrum zawiera obecnie kolumnę współrzędne **listy** typu.  Jak pokazano na początku tego samouczka, dane GeoJSON, w tym samouczku jest typu punktu wartości długości i szerokości geograficznej rejestrowane w tablicy współrzędne.
   
    Element współrzędne [0] reprezentuje długości geograficznej podczas współrzędne [1] reprezentuje szerokość.
    ![Usługa Power BI samouczek dotyczący łącznika usługi Azure Cosmos DB w usłudze Power BI — lista współrzędnych](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Spłaszczanie tablicę współrzędnych, należy utworzyć **kolumna niestandardowa** o nazwie LatLong.  Wybierz **Dodaj kolumnę** wstążki, a następnie kliknij polecenie **kolumna niestandardowa**.  **Kolumna niestandardowa** zostanie wyświetlone okno.
8. Podaj nazwę dla nowej kolumny, np. LatLong.
9. Następnie określ Formuła niestandardowa dla nowej kolumny.  W tym przykładzie firma Microsoft będzie łączyć wartości długości i szerokości geograficznej, oddzielone przecinkiem, jak pokazano poniżej, korzystając z następującego wzoru: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Kliknij przycisk **OK**.
   
    Aby uzyskać więcej informacji na Data Analysis Expressions (DAX) w tym funkcje języka DAX, odwiedź [podstawy języka DAX w programie Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics).
   
    ![Usługa Power BI samouczek dla łącznika usługi Azure Cosmos DB w usłudze Power BI — Dodawanie kolumny niestandardowej](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Teraz w okienku Centrum wyświetlone nowe kolumny LatLong wypełnione wartościami.
    
    ![Usługa Power BI samouczek dotyczący łącznika usługi Azure Cosmos DB w usłudze Power BI — kolumna niestandardowa LatLong](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Jeśli otrzymasz komunikat o błędzie w nowej kolumnie, upewnij się, że zastosowane kroki w obszarze Ustawienia zapytania są takie same poniższej ilustracji:
    
    ![Zastosowane kroki powinien być źródło nawigacji, rozwinięta dokumentu, rozwinięte Document.Location, dodano kolumnę niestandardową](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Jeśli kroki są różne, Usuń dodatkowe czynności, a następnie spróbuj ponownie dodać kolumnę niestandardową. 

11. Kliknij przycisk **Zamknij i Zastosuj** można zapisać modelu danych.
    
    ![Usługa Power BI samouczek dla łącznika usługi Azure Cosmos DB w usłudze Power BI — Zamknij i Zastosuj](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Tworzenie raportów
Powerview raportu programu Power BI Desktop jest, gdzie możesz rozpocząć tworzenie raportów w celu wizualizacji danych.  Możesz tworzyć raporty, przeciągając i upuszczając pola do **raportu** kanwy.

![Usługa Power BI Desktop widoku raportu — łącznik usługi Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

W widoku raportu należy odnaleźć:

1. **Pola** okienko, jest to, gdzie można wyświetlić listę modeli danych z polami, można użyć na potrzeby raportów.
2. **Wizualizacje** okienka. Raport może zawierać jedną lub wiele wizualizacji.  Wybierz typy wizualizacji pasuje do Twoich potrzeb, od **wizualizacje** okienka.
3. **Raportu** kanwy, jest to, które służy do tworzenia wizualizacji do raportu.
4. **Raportu** strony. W programie Power BI Desktop, można dodać wielu stronach raportu.

Poniżej przedstawiono podstawowe kroki tworzenia prostego interaktywny widok raportu w formie mapy.

1. W tym przykładzie utworzymy widoku mapy, wskazuje lokalizację każdego swe dzieła.  W **wizualizacje** okienku kliknąć typ wizualizacji mapy, jak podkreślono na powyższym zrzucie ekranu.  Powinien zostać wyświetlony typ wizualizacji mapy rysowane na **raportu** kanwy.  **Wizualizacji** okienku powinien również wyświetlać zestaw właściwości związanych z typ wizualizacji mapy.
2. Teraz przeciągnij i upuść pola LatLong z **pola** okienku **lokalizacji** właściwości w **wizualizacje** okienka.
3. Następnie przeciągnij i upuść pola Nazwa swe dzieła **legendy** właściwości.  
4. Następnie przeciągnij i upuść pole podniesienia uprawnień do **rozmiar** właściwości.  
5. Powinien zostać wyświetlony mapy wizualizację liczbową zbiór bąbelki wskazującą lokalizację każdego swe dzieła Rozmiar bąbelka korelacji na podniesienie swe dzieła.
6. Utworzono teraz podstawowego raportu.  Możesz dostosować raport, poprzez dodanie większej liczby wizualizacji.  W naszym przypadku dodaliśmy fragmentator typu swe dzieła, aby uczynić interaktywnymi raportu.  
   
7. W menu Plik kliknij polecenie **Zapisz** i Zapisz plik jako PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Publikowanie i udostępnianie Twojego raportu
Aby udostępnić raport, musisz mieć konto w witrynie PowerBI.com.

1. W programie Power BI Desktop kliknij **Home** wstążki.
2. Kliknij przycisk **publikowania**.  Monit o podanie nazwy użytkownika i hasła dla konta usługi PowerBI.com.
3. Po uwierzytelnieniu poświadczeń raport został opublikowany do wybranej lokalizacji docelowej.
4. Kliknij przycisk **Otwórz "PowerBITutorial.pbix" w usłudze Power BI** aby zobaczyć i udostępnianie Twojego raportu w witrynie PowerBI.com.
   
    ![Publikowanie do usługi Power BI sukces! Samouczek Otwórz w usłudze Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Tworzenie pulpitu nawigacyjnego w witrynie PowerBI.com
Teraz, gdy masz raport, pozwala udostępnić go w witrynie PowerBI.com

Podczas publikowania raportu z programu Power BI Desktop w witrynie PowerBI.com, generuje **raportu** i **Dataset** w dzierżawie usługi PowerBI.com. Na przykład po opublikowany raport o nazwie **PowerBITutorial** w witrynie PowerBI.com, zobaczysz PowerBITutorial zarówno **raporty** i **zestawów danych** sekcje na Witryny PowerBI.com.

   ![Zrzut ekranu przedstawiający nowy raport i zestaw danych w witrynie PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Aby utworzyć, które można udostępnić pulpit nawigacyjny, kliknij **Przypnij stronę dynamiczną** przycisk w raporcie witryny PowerBI.com.

   ![Zrzut ekranu przedstawiający nowy raport i zestaw danych w witrynie PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Następnie postępuj zgodnie z instrukcjami wyświetlanymi w [przypinanie kafelka z raportu](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) do utworzenia nowego pulpitu nawigacyjnego. 

Przed utworzeniem pulpitu nawigacyjnego, można także utworzyć ad-hoc modyfikacje do raportu. Zaleca się jednak, aby użyć programu Power BI Desktop do wykonywania modyfikacji i ponownie opublikować raport w witrynie PowerBI.com.

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
## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej o usłudze Power BI, zobacz [Rozpoczynanie pracy z usługą Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Aby dowiedzieć się więcej na temat usługi Azure Cosmos DB, zobacz [strony docelowej dokumentacji usługi Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

