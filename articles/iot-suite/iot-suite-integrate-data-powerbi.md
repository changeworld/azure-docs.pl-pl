---
title: Wizualizuj dane monitorowania zdalnego przy użyciu usługi Power BI - Azure | Dokumentacja firmy Microsoft
description: Ten samouczek używa Power BI Desktop i DB rozwiązania Cosmos do integerate danych z rozwiązanie monitorowania zdalnego do wizualizacji dostosowane. Użytkownicy w ten sposób można tworzenie własnych niestandardowych pulpitów nawigacyjnych i udostępniać je użytkownikom nie w ramach rozwiązania.
services: iot-suite
suite: iot-suite
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 45b91241223b1286cc412ab609e60aeadef51b85
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Wizualizuj dane monitorowania zdalnego przy użyciu usługi Power BI

Ten samouczek przeprowadzi użytkownika jak dołączyć dane rozwiązanie monitorowania zdalnego z CosmosDB do usługi Power BI. Z tym połączeniem ustanowić następnie można tworzyć własne niestandardowe pulpity nawigacyjne i dodać je ponownie do pulpitu nawigacyjnego monitorowania zdalnego rozwiązania. Ta workstream umożliwia wyspecjalizowanego wykresy do utworzenia, oprócz tych poza pole. Następnie można w tym samouczku zintegrować z innych strumieni danych lub utworzyć niestandardowe pulpity nawigacyjne zużywanych poza zdalnego rozwiązanie monitorowanie. Tworzenie pulpitów nawigacyjnych w usłudze Power BI oznacza, że można także dokonać każdego ze sobą współdziałać, jak wybierz konkretne elementy panelu. Na przykład można mieć filtr, który pokazuje tylko informacje dotyczące Twojego symulowane pojazdów i każdego z nich pulpitu nawigacyjnego może współpracować pokazać tylko symulowane ciężarówka informacji. Jeśli chcesz użyć narzędzia innego niż usługi Power BI, można rozszerzać te kroki, aby korzystać z wybranych narzędzi wizualizacji i przyłączanie się do rozwiązania Cosmos bazy danych lub w niestandardowej bazie danych, jeśli ustawiono jedną. 

## <a name="prerequisites"></a>Wymagania wstępne

- Musi mieć uruchomiony rozwiązanie monitorowania zdalnego
- Musi mieć dostęp do [Azure Portal](https://portal.azure.com) i subskrypcji, na którym są uruchomione Centrum IoT i rozwiązania
- Musi mieć [Power BI desktop](https://powerbi.microsoft.com) zainstalowana, będzie wykonywać żadnej wersji


## <a name="information-needed-from-azure-portal"></a>Informacje z portalu Azure

1. Przejdź do [Azure Portal](https://portal.azure.com) i zaloguj ponownie w razie potrzeby

2. W lewym panelu kliknij grup zasobów

    ![Po stronie panelu nawigacji](media/iot-suite-remote-monitoring-powerbi/side_panel.png)

3. Przejdź do grupy zasobów, których rozwiązania Iot jest uruchomiona na i kliknij, aby przejść do strony Przegląd tej grupy zasobów. 

4. Na stronie Przegląd kliknij element, który ma typ "Konto Azure rozwiązania Cosmos bazy danych", nastąpi następnie przekierowanie do strony Przegląd strumienia rozwiązania Cosmos bazy danych dla tego rozwiązania IoT.

    ![Grupa zasobów](media/iot-suite-remote-monitoring-powerbi/resource_groups.png)

5. W okienku po lewej stronie kliknij sekcję "Klucze", a następnie zanotuj następujące wartości do użycia w usługi Power BI:

    - Identyfikator URI
    - Klucz podstawowy

    ![keys](media/iot-suite-remote-monitoring-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Konfigurowanie strumienia w usłudze Power BI
  
1. Otwarcie aplikacji klasycznej usługi Power BI, a następnie kliknij polecenie "Pobierz dane" w lewym górnym rogu. 

    ![Pobieranie danych](media/iot-suite-remote-monitoring-powerbi/get_data.png)

2. Kiedy pojawi się monit o wprowadzenie danych, wybierz wyszukiwanie "Azure DB rozwiązania Cosmos", a następnie wybierz tego łącznika. Ten łącznik zasadniczo pobiera dane bezpośrednio z bazy danych rozwiązania cosmos rozwiązania IoT Azure
  
    ![Cosmos DB](media/iot-suite-remote-monitoring-powerbi/cosmos_db.png)
  
3. Wprowadź informacje, które zostały zarejestrowane powyżej:

    * Identyfikator URI
    * Klucz podstawowy

4. Wybierz wszystkie tabele do zaimportowania do usługi Power BI. Ta akcja spowoduje zaczną działać, wyłącz ładowanie danych. Już działa rozwiązania, tym dłużej może potrwać do ładowania danych (do kilku godzin). 

    ![Importowanie tabel](media/iot-suite-remote-monitoring-powerbi/import_tables.png)

5. Po zakończeniu dane ładowania, kliknij "Edytuj zapytania" w górnym wierszu usługi Power BI i rozwiń wszystkie tabele, klikając strzałkę na pasku żółty dla każdej tabeli. Zasadniczo spowoduje to rozwinięcie Aby wyświetlić wszystkie kolumny. Można zauważyć, jak dane dotyczące czynności takich jak wilgotności, godzina prędkości itp. nie są poprawnego typu.

    ![Nowa kolumna](media/iot-suite-remote-monitoring-powerbi/new_column.png)
  
    Na przykład danych do usługi Power BI została zmieniona na czas UNIX, gdy pochodzi za pośrednictwem łącznika. Aby dopasować do konwersji, teraz możesz można Utwórz nową kolumnę i użyj równanie, aby pobrać go na format daty i godziny: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Zaktualizowano tabeli](media/iot-suite-remote-monitoring-powerbi/updated_table.png)
  
    Document.Device.msg.RECEIVED jest tylko jedna z kolumn w formacie systemu UNIX i można zastąpić osobom, które wymagają konwersji. 
  
    Inne punkty danych przekonwertowano na typu ciąg można zmienić na symulacyjnych lub Int, gdzie odpowiednie korzystającej z tego samego kroki zgodnie z powyższym.

## <a name="creating-a-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Po nawiązaniu połączenia strumienia możesz przystąpić do tworzenia pulpitów nawigacyjnych spersonalizowane! Pulpit nawigacyjny poniżej przedstawiono przykładowy przełączenia dane telemetryczne są immmited przez naszych symulowanego urządzenia, a pokazywanie różnych obracając wokół niego, takich jak: 

* Lokalizacja urządzenia na mapie (po prawej)
* Urządzenia z ich stanu i ważności. (lewego górnego)
* Urządzenia z zasadami w miejscu i będą wszystkie alarmy będzie dla nich (lewy dolny)

![Wizualizacja usługi Power BI](media/iot-suite-remote-monitoring-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publikowanie pulpitu nawigacyjnego i odświeżanie danych

Po pomyślnym utworzeniu pulpitów nawigacyjnych, zalecamy, aby użytkownik [publikowanie pulpitów nawigacyjnych usługi Power BI](https://docs.microsoft.com/en-us/power-bi/desktop-upload-desktop-files) ją udostępnić.

Należy również do [odświeżania danych](https://docs.microsoft.com/en-us/power-bi/refresh-data) na pulpicie nawigacyjnym opublikowane, aby upewnić się, że masz najnowszą zestawu danych.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono temat do wizualizacji danych monitorowania zdalnych przy użyciu usługi Power BI

Aby uzyskać więcej informacji dotyczących dostosowywania zdalnego rozwiązanie monitorowania zobacz:

* [Dostosowywanie rozwiązanie monitorowania zdalnego interfejsu użytkownika](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)
* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

