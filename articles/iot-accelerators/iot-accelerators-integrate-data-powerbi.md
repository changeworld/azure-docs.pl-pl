---
title: Wizualizuj dane zdalnego monitorowania przy użyciu usługi Power BI — Azure | Dokumenty firmy Microsoft
description: W tym samouczku użyto programu Power BI Desktop i usługi Cosmos DB do integrowania danych z rozwiązania do zdalnego monitorowania w dostosowaną wizualizację. W ten sposób użytkownicy mogą tworzyć własne niestandardowe pulpity nawigacyjne i udostępniać je użytkownikom, którzy nie są w rozwiązaniu.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 8b0f395936c8628ff7b8dab601168538f97ccdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184238"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Wizualizuj dane monitorowania zdalnego za pomocą usługi Power BI

W tym samouczku opisano sposób podłączania danych rozwiązania do zdalnego monitorowania z usługi CosmosDB do usługi Power BI. Po ustanowieniu tego połączenia można następnie utworzyć własne niestandardowe pulpity nawigacyjne i dodać je z powrotem do pulpitu nawigacyjnego rozwiązania zdalnego monitorowania. Ten strumień roboczy pozwala na tworzenie bardziej wyspecjalizowanych wykresów, oprócz tych po wyjęciu z pudełka. Następnie można użyć tego samouczka do integracji z innymi strumieniami danych lub tworzenia niestandardowych pulpitów nawigacyjnych, które mają być używane poza rozwiązaniem do zdalnego monitorowania. Tworzenie pulpitów nawigacyjnych w usłudze Power BI oznacza, że każdy panel może również współdziałać ze sobą podczas wybierania określonych elementów. Na przykład może być filtr, który pokazuje tylko informacje o symulowanych ciężarówkach, a każdy fragment pulpitu nawigacyjnego będzie współdziałał, aby wyświetlać tylko symulowane informacje o ciężarówce. Jeśli chcesz użyć narzędzia innego niż Usługa Power BI, możesz również rozszerzyć te kroki, aby użyć wybranego narzędzia wizualizacji i podłączyć je do bazy danych usługi Cosmos lub niestandardowej bazy danych, jeśli ją skonfigurowano. 

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć obecnie uruchomione rozwiązanie do zdalnego monitorowania
- Musisz mieć dostęp do [witryny Azure portal](https://portal.azure.com) i subskrypcji, w której są uruchomione Centrum IoT i rozwiązanie
- Musisz mieć zainstalowany [pulpit usługi Power BI,](https://powerbi.microsoft.com) każda wersja


## <a name="information-needed-from-azure-portal"></a>Informacje potrzebne z witryny Azure portal

1. Przejdź do [witryny Azure portal](https://portal.azure.com) i zaloguj się w razie potrzeby

2. Na panelu po lewej stronie kliknij pozycję Grupy zasobów

    ![Nav panelu bocznego](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Przejdź do grupy zasobów, na której działa rozwiązanie Iot, i kliknij, aby przejść do strony Przegląd tej grupy zasobów. 

4. Na tej stronie przeglądu kliknij element, który ma typ "Konto usługi Azure Cosmos DB", zostaniesz przesunął się do strony przeglądu strumienia usługi Cosmos DB dla tego rozwiązania IoT.

    ![Grupa zasobów](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. Na panelu po lewej stronie kliknij sekcję "Klawisze" i zanotuj następujące wartości, które mają być używane w usłudze Power BI:

   - Identyfikator URI
   - Klucz podstawowy

     ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Konfigurowanie strumienia w usłudze Power BI
  
1. Otwórz aplikację klasyczną Power BI i kliknij "Pobierz dane" w lewym górnym rogu. 

    ![Pobieranie danych](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Gdy zostaniesz poproszony o wprowadzenie danych, wybierz opcję Wyszukaj "Usługa Azure Cosmos DB" i wybierz ten łącznik. Ten łącznik zasadniczo pobiera dane prosto z bazy danych cosmos rozwiązania Azure IoT Solution
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Wprowadź informacje, które zostały zapisane powyżej:

    * Identyfikator URI
    * Klucz podstawowy

4. Wybierz wszystkie tabele, które mają zostać zaimportowane do usługi Power BI. Ta akcja rozpocznie ładowanie danych. Im dłużej rozwiązanie działa, tym dłużej może potrwać ładowanie danych (do kilku godzin). 

    ![Importowanie tabel](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Po zakończeniu ładowania danych kliknij przycisk "Edytuj kwerendy" w górnym wierszu usługi Power BI i rozwiń wszystkie tabele, klikając strzałki na żółtym pasku dla każdej tabeli. Spowoduje to zasadniczo rozwinąć, aby wyświetlić wszystkie kolumny. Zauważysz, że dane dotyczące takich rzeczy jak wilgotność, czas prędkości itp.

    ![Nowa kolumna](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Na przykład dane przychodzące do usługi Power BI zostały zmienione w czas unix, gdy wszedł przez złącze. Aby dostosować się do tej konwersji, w przyszłości możesz utworzyć nową kolumnę i użyć tego równania, aby wprowadzić ją w format daty: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Zaktualizowana tabela](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.device.msg.received to tylko jedna z kolumn z formatowaniem systemu UNIX i może być zastąpiona innymi, które wymagają konwersji. 
  
    Inne punkty danych zostały przekonwertowane na typ String można zmienić na Doubles lub Int, w stosownych przypadkach, wykonując te same kroki, co powyżej.

## <a name="creating-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Po podłączeniu strumienia możesz stworzyć spersonalizowane pulpity nawigacyjne! Pulpit nawigacyjny poniżej jest przykładem biorąc dane telemetryczne emitowane przez nasze symulowane urządzenia i pokazano różne przestawianie wokół niego, takie jak: 

* Lokalizacja urządzenia na mapie (po prawej)
* Urządzenia z ich stanem i ważnością. (w lewym górnym rogu)
* Urządzenia z wprowadzonymi regułami i jeśli są jakieś alerty dla nich (na dole po lewej)

![Wizualizacja programu PowerBi](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publikowanie pulpitu nawigacyjnego i odświeżanie danych

Po pomyślnym utworzeniu pulpitów nawigacyjnych zalecamy [opublikowanie pulpitów nawigacyjnych usługi Power BI](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) w celu udostępnienia innym osobom.

Należy również [odświeżyć dane](https://docs.microsoft.com/power-bi/refresh-data) na opublikowanym pulpicie nawigacyjnym, aby upewnić się, że masz najnowszy zestaw danych.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak wizualizować dane zdalnego monitorowania przy użyciu usługi Power BI

Aby uzyskać więcej informacji na temat dostosowywania rozwiązania do zdalnego monitorowania, zobacz:

* [Dostosowywanie interfejsu użytkownika rozwiązania do zdalnego monitorowania](iot-accelerators-remote-monitoring-customize.md)
* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

