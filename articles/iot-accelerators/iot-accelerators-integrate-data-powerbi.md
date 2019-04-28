---
title: Wizualizuj dane monitorowania zdalnego, za pomocą usługi Power BI — Azure | Dokumentacja firmy Microsoft
description: W tym samouczku korzysta z usługi Power BI Desktop i Cosmos DB do integerate danych za pomocą rozwiązania do zdalnego monitorowania w niestandardowych wizualizacji. Dzięki temu użytkownicy mogą tworzyć własne niestandardowe pulpity nawigacyjne i udostępniać je użytkownikom, nie w rozwiązaniu.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 3398c6d318e0e3c51d3f6cfe8af651a6e3f55c9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61448133"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Wizualizuj dane monitorowania zdalnego, za pomocą usługi Power BI

Ten samouczek przeprowadzi Cię jak monit o podłączenie dane rozwiązanie monitorowania zdalnego z bazy danych cosmos DB do usługi Power BI. To połączenie nawiązane następnie można tworzyć własne niestandardowe pulpity nawigacyjne i dodawać je ponownie do pulpitu nawigacyjnego rozwiązania monitorowania zdalnego. Umożliwia to workstream bardziej wyspecjalizowane wykresy utworzone, oprócz tych gotowych. W tym samouczku można następnie użyć do integracji z innych strumieni danych i tworzyć niestandardowe pulpity nawigacyjne do użycia poza rozwiązania do zdalnego monitorowania. Tworzenie pulpitów nawigacyjnych w usłudze Power BI oznacza, że może być każdy panel współdziałają ze sobą, jak wybrać konkretne elementy. Na przykład masz filtr, który wyświetlane są tylko informacje dotyczące Twojego symulowane ciężarówek i każdej części pulpitu nawigacyjnego może wchodzić w interakcje aby pokazać, że tylko symulowane truck informacji. Jeśli chcesz użyć narzędzia innych niż Power BI, możesz rozszerzyć następujące kroki, aby użyć swoje narzędzia wizualizacji i dołączyć do bazy danych Cosmos lub niestandardowej bazy danych, jeśli skonfigurowano jedną. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konieczne jest posiadanie aktualnie uruchomione rozwiązanie monitorowania zdalnego
- Musi mieć dostęp do [witryny Azure Portal](https://portal.azure.com) i Twojej subskrypcji, na którym są uruchomione usługi IoT Hub i rozwiązania
- Konieczne jest posiadanie [Power BI desktop](https://powerbi.microsoft.com) zainstalowany, wykona dowolna wersja


## <a name="information-needed-from-azure-portal"></a>Potrzebnych informacji z witryny Azure Portal

1. Przejdź do [witryny Azure Portal](https://portal.azure.com) i zaloguj się w razie potrzeby

2. Na lewym panelu kliknij opcję grupy zasobów

    ![Po stronie panelu nawigacji](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Przejdź do grupy zasobów, z których rozwiązanie Iot jest zasilany z i kliknij, aby przejść do strony Przegląd w tej grupie zasobów. 

4. Na stronie Przegląd kliknij element, który ma typ "Konta usługi Azure Cosmos DB", możesz następnie nastąpi przekierowanie do strony Przegląd strumienia usługi Cosmos DB dla tego rozwiązania IoT.

    ![Grupa zasobów](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. W panelu po lewej stronie kliknij sekcję "Klucze" i zwróć uwagę na następujące wartości do użycia w usłudze Power BI:

   - URI
   - Klucz podstawowy

     ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Konfigurowanie Stream w usłudze Power BI
  
1. Otwórz aplikację Power BI, a następnie kliknij przycisk "Pobierz dane" w lewym górnym rogu. 

    ![Pobieranie danych](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Po wyświetleniu monitu o podanie danych, należy wybrać opcję wyszukiwania dla "Usługi Azure Cosmos DB" ale wybierz tego łącznika. Ten łącznik zasadniczo pobiera dane bezpośrednio z bazy danych cosmos swojego rozwiązania IoT platformy Azure
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Wprowadź informacje, które zostały zarejestrowane powyżej:

    * URI
    * Klucz podstawowy

4. Zaznacz wszystkie tabele do zaimportowania do usługi Power BI. Ta akcja spowoduje uruchomienie ładowania danych. Już rozwiązania zostały uruchomione, tym dłużej może potrwać danych do załadowania (do kilku godzin). 

    ![Importuj tabele](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Po zakończeniu dane ładowania, kliknij przycisk "Edytuj zapytania" w górnym wierszu usługi Power BI i rozwiń wszystkie tabele, klikając strzałkę na żółty pasek dla każdej tabeli. Spowoduje to zasadniczo rozszerzyć, aby wyświetlić wszystkie kolumny. Można zauważyć, jak dane dotyczące elementy, takie jak wilgotność, skracają czas, itp. nie mają poprawnego typu.

    ![Nowa kolumna](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Na przykład danych przechodzących do usługi Power BI został zmieniony na time systemu UNIX, gdy będą one za pośrednictwem łącznika. Aby dostosować tę konwersję, idąc dalej, należy można Utwórz nową kolumnę i użyj równanie, aby pobrać go na format daty i godziny: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Zaktualizowano tabelę](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.Device.msg.RECEIVED jest tylko jedna z kolumn za pomocą formatowania systemu UNIX i innym osobom, które muszą konwersji można zastąpić. 
  
    Inne punkty danych zostały przekonwertowane na typ, który można zmienić ciąg do wartości podwójnej precyzji lub Int, gdzie odpowiednie korzystając z tych samych kroków zgodnie z powyższym.

## <a name="creating-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Po nawiązaniu połączenia strumienia można przystąpić do tworzenia pulpitów nawigacyjnych spersonalizowane! Pulpit nawigacyjny poniżej znajduje się przykład podjęcia telemetrii jest emitowane przez naszych symulowanych urządzeń i przedstawiający różne tabele przestawne, wokół niego, takie jak: 

* Lokalizacja urządzenia na mapie (po prawej)
* Urządzenia z ich stanu i ważności. (lewo u góry)
* Urządzenia z zasadami w miejscu, a jeśli ma żadnych alertów przechodzi wyłączanie dla nich (lewy dolny róg)

![Wizualizacje usługi Power BI](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publikowanie pulpitu nawigacyjnego i odświeżanie danych

Po pomyślnym utworzeniu pulpitów nawigacyjnych, zaleca się możesz [publikować pulpity nawigacyjne usługi Power BI](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) udostępnianie innym osobom.

Ponadto warto [odświeżania danych](https://docs.microsoft.com/power-bi/refresh-data) na opublikowany pulpit nawigacyjny, aby upewnić się, że masz najnowszy zestaw danych.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano sposób wizualizować dane za pomocą monitorowania zdalnego przy użyciu usługi Power BI

Aby uzyskać więcej informacji na temat dostosowywania rozwiązania do zdalnego monitorowania zobacz:

* [Dostosowywanie rozwiązania do monitorowania zdalnego interfejsu użytkownika](iot-accelerators-remote-monitoring-customize.md)
* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

