---
title: Power BI, odwiedź pulpit nawigacyjny kondycji vehicle i wspierającym zwyczaje - Azure | Dokumentacja firmy Microsoft
description: Korzystanie z możliwości Cortana Intelligence, aby uzyskać wgląd w czasie rzeczywistym oraz predykcyjnej na vehicle kondycji i wspierającym zwyczaje.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: deguhath
ms.openlocfilehash: 462f5d15e7a39d282bca59f96166cc809447ee14
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837127"
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Instrukcje instalacji pulpit nawigacyjny usługi Power BI Telemetrii analizy rozwiązania szablonu vehicle
Tego łącza menu rozdziałów, w tym podręcznika dotyczącego: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Pokazy rozwiązania analizy Telemetrii Vehicle jak przedstawicielstw samochodu handlowych, producentów samochodów i ubezpieczeń mogą skorzystać z funkcji Cortana Intelligence. Mogą uzyskać szczegółowych informacji w czasie rzeczywistym oraz predykcyjnej na kondycji vehicle zwiększają zwyczaje do poprawy obsługi klienta, badań i rozwoju i kampanii marketingowych. Te szczegółowe instrukcje przedstawiają sposób konfigurowania raportów usługi Power BI i pulpitu nawigacyjnego po wdrożeniu rozwiązania w ramach subskrypcji. 

## <a name="prerequisites"></a>Wymagania wstępne
* Wdrażanie [analizy Telemetrii Vehicle](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90) rozwiązania. 
* [Zainstaluj Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331).
* Uzyskaj [subskrypcji platformy Azure](https://azure.microsoft.com/pricing/free-trial/). Jeśli nie masz subskrypcji platformy Azure, Zacznij korzystać z bezpłatnej subskrypcji Azure.
* Otwieranie konta usługi Power BI.

## <a name="cortana-intelligence-suite-components"></a>Składników pakietu Cortana Intelligence
W ramach szablon rozwiązania analizy Telemetrii Vehicle następujące usługi Cortana Intelligence są wdrażane w ramach Twojej subskrypcji:

* **Usługa Azure Event Hubs** wysyła strumień miliony zdarzeń telemetrii vehicle na platformie Azure.
* **Usługa Azure Stream Analytics** zapewnia wgląd w czasie rzeczywistym w kondycję vehicle i będzie się powtarzał tych danych do długoterminowego przechowywania dla bardziej zaawansowane funkcje analizy partii.
* **Usługa Azure Machine Learning** wykrycia anomalii w czasie rzeczywistym i używa przetwarzania wsadowego, aby zapewnić predykcyjnej szczegółowych informacji.
* **Usługa Azure HDInsight** przekształcenia danych na dużą skalę.
* **Fabryka danych Azure** obsługuje aranżacji, planowania, zarządzanie zasobami i monitorowania w potoku przetwarzania wsadowego.

**Power BI** daje to rozwiązanie sformatowanego pulpitu nawigacyjnego dla danych i wizualizacji analizy predykcyjnej. 

Rozwiązanie używa dwóch różnych źródeł danych:

* Sygnały symulowane vehicle i zestawy danych diagnostycznych
* Vehicle katalogu

Symulator telematyki vehicle wchodzi w skład tego rozwiązania. Emituje go sygnałów, które odpowiadają stan vehicle i schematy w danym punkcie w czasie i informacje diagnostyczne. 

Katalog vehicle jest zestawem danych odwołania mapowanego VINs modeli.

## <a name="power-bi-dashboard-preparation"></a>Power BI pulpitu nawigacyjnego przygotowania
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Konfigurowanie w czasie rzeczywistym pulpit nawigacyjny usługi Power BI

#### <a name="start-the-real-time-dashboard-application"></a>Uruchom aplikację w czasie rzeczywistym pulpitu nawigacyjnego
Po zakończeniu wdrożenia, postępuj zgodnie z instrukcjami operacji ręcznych.

1. Pobierz aplikację pulpitu nawigacyjnego w czasie rzeczywistym RealtimeDashboardApp.zip i Rozpakuj go.

2.  W folderze rozpakowane Otwórz plik konfiguracji aplikacji RealtimeDashboardApp.exe.config. Zastąp appSettings dla usługi Event Hubs, magazynu obiektów Blob platformy Azure i połączenia usługi Azure Machine Learning z wartościami w instrukcjach działania ręcznego. Zapisz zmiany.

3. Uruchom aplikację RealtimeDashboardApp.exe. W oknie logowania wprowadź prawidłowe poświadczenia usługi Power BI. 

   ![Power BI w oknie rejestrowania](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
4. Wybierz **zaakceptować**. Aby uruchomić uruchomieniu aplikacji.

   ![Power BI pulpitu nawigacyjnego uprawnień](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

5. Zaloguj się do witryny sieci Web usługi Power BI i utworzyć pulpit nawigacyjny w czasie rzeczywistym.

Teraz możesz przystąpić do konfigurowania pulpit nawigacyjny usługi Power BI.  

### <a name="configure-power-bi-reports"></a>Konfigurowanie raportów usługi Power BI
W czasie rzeczywistym raporty i pulpit nawigacyjny zająć około 30 do 45 minut, aby zakończyć. 

1. Przejdź do [usługi Power BI](http://powerbi.com) strony sieci Web i zaloguj się.

    ![Power BI na stronie rejestracji](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

2. Nowy zestaw danych jest generowany w usłudze Power BI. Wybierz **ConnectedCarsRealtime** zestawu danych.

    ![Zestaw danych ConnectedCarsRealtime](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

3. Aby zapisać raport puste, naciśnij klawisze Ctrl + S.

    ![Pusty raport](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

4. Wprowadź nazwę raportu **Vehicle Telemetrii Analytics online w czasie rzeczywistym - raporty**.

    ![Nazwa raportu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Raporty w czasie rzeczywistym
Trzy raporty w czasie rzeczywistym znajdują się w tym rozwiązaniu:

* Pojazdów w operacji
* Pojazdów wymagających konserwacji
* Vehicle statystyki kondycji

Można skonfigurować wszystkie trzy raporty, albo można wyłączyć po każdym etapie. Następnie można przejść do następnej sekcji na temat sposobu konfigurowania raportów partii. Firma Microsoft zaleca utworzenie wszystkich trzech raportów w celu wizualizacji pełny wgląd w czasie rzeczywistym ścieżki rozwiązania.  

### <a name="vehicles-in-operation-report"></a>Pojazdów w raporcie operacji
1. Kliknij dwukrotnie **strona 1**i zmień jego nazwę **pojazdów w operacji**.

    ![Pojazdów w operacji](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

2. Na **pola** wybierz opcję **vin**. Na **wizualizacje** wybierz opcję **karty** wizualizacji.  

    **Karty** wizualizacji jest tworzony, jak pokazano na poniższej ilustracji:

    ![Wybierz vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

3. Wybierz pusty obszar można dodać nowej wizualizacji.  

4. Na **pola** wybierz opcję **miasta** i **vin**. Na **wizualizacje** wybierz opcję **mapy** wizualizacji. Przeciągnij **vin** do **wartości** obszaru. Przeciągnij **miasta** do **legendy** obszaru. 

    ![Karta wizualizacji](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

5. Na **wizualizacje** wybierz opcję **Format** sekcji. Wybierz **tytuł**i zmień **tekst** do **pojazdów w operacji przez Miasto**.

    ![Pojazdów w operacji przez miasta](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    Końcowe wizualizacji wygląda jak w następującym przykładzie:

    ![Końcowe wizualizacji](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

6. Wybierz pusty obszar można dodać nowej wizualizacji.  

7. Na **pola** wybierz opcję **miasta** i **vin**. Na **wizualizacje** wybierz opcję **wykres kolumnowy grupowany** wizualizacji. Przeciągnij **miasta** do **osi** obszaru. Przeciągnij **vin** do **wartość** obszaru.

8. Sortowanie wykresu przez **liczba vin**.

    ![Liczba vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

9. Zmień wykres **tytuł** do **pojazdów w operacji przez Miasto**. 

10. Wybierz **Format** , a następnie wybierz **kolory danych**. Zmień **Pokaż wszystkie** do **na**.

    ![Kolory danych](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

11. Umożliwia zmianę koloru miasta poszczególnych wybierając kolor symbolu.

    ![Zmiana koloru](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

12. Wybierz pusty obszar można dodać nowej wizualizacji.  

13. Na **wizualizacje** wybierz opcję **wykres kolumnowy grupowany** wizualizacji. Na **pola** karcie, przeciągnij **miasta** do **osi** obszaru. Przeciągnij **modelu** do **legendy** obszaru. Przeciągnij **vin** do **wartość** obszaru.

    ![Wykres kolumnowy grupowany](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    Wykres wygląda na poniższej ilustracji:

    ![Renderowanie](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

14. Rozmieszczanie wszystkich wizualizacjach, dzięki czemu strony wygląda jak w następującym przykładzie:

    ![Pulpit nawigacyjny z wizualizacjami](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Pomyślnie skonfigurowano raport "Pojazdów w operacji". Można utworzyć dalej raport w czasie rzeczywistym, lub można zatrzymać w tym miejscu i konfigurowanie pulpitu nawigacyjnego. 

### <a name="vehicles-requiring-maintenance-report"></a>Raport o konieczności obsługi pojazdów

1. Wybierz ![Dodaj](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) Aby dodać nowy raport. Zmień jego nazwę **pojazdów wymagających obsługi**.

    ![Pojazdów wymagających konserwacji](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

2. Na **pola** wybierz opcję **vin**. Na **wizualizacje** wybierz opcję **karty** wizualizacji.

    ![Karta VIN wizualizacji](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    Zestaw danych zawiera pole o nazwie **MaintenanceLabel**. To pole może mieć wartość "0" lub "1". Wartość jest ustawiana przez modelu uczenia maszynowego, który jest udostępniony w ramach rozwiązania. Jest zintegrowany ze ścieżką w czasie rzeczywistym. Wartość "1" oznacza, że vehicle wymaga konserwacji. 

3. Aby dodać **filtru poziomu strony** były wyświetlane dane dotyczące pojazdów, które wymagają obsługi: 

   a. Przeciągnij **MaintenanceLabel** do **filtrów stron poziomu**.
  
      ![Filtry poziomu strony](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. W dolnej części **strony poziom filtry MaintenanceLabel**, wybierz pozycję **podstawowe filtrowanie**.

      ![Podstawowe filtrowanie](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Ustaw wartość filtru **1**.

      ![Wartość filtru](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

4. Wybierz pusty obszar można dodać nowej wizualizacji.  

5. Na **wizualizacje** wybierz opcję **wykres kolumnowy grupowany** wizualizacji. 

    ![Karta VIN](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Wykres kolumnowy grupowany](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

6. Na **pola** karcie, przeciągnij **modelu** do **osi** obszaru. Przeciągnij **vin** do **wartość** obszaru. Następnie Sortuj wizualizacji przez **liczba vin**. Zmień wykres **tytuł** do **pojazdów wymagających obsługi przez model**. 

7. Na **pola** ![pola obrazu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) sekcji **wizualizacje** karcie, przeciągnij **vin** do **nasycenie kolorów**.

    ![Nasycenie kolorów](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

8. Na **Format** Zmień **kolory danych** do wizualizacji: 

    a. Zmień **Minimum** kolor **F2C812**.

    b. Zmień **maksymalna** kolor **FF6300**.

    ![Nowe kolory danych](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    Nowe kolory wizualizacji wyglądać następująco:

    ![Nowe kolory wizualizacji](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

9. Wybierz pusty obszar można dodać nowej wizualizacji.  

10. Na **wizualizacje** wybierz opcję **wykres kolumnowy grupowany**. Przeciągnij **vin** do **wartość** obszaru. Przeciągnij **miasta** do **osi** obszaru. Sortowanie wykresu przez **liczba vin**. Zmień wykres **tytuł** do **pojazdów wymagających obsługi przez Miasto**.

    ![Pojazdów wymagających obsługi przez miasta](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

11. Wybierz pusty obszar można dodać nowej wizualizacji.  

12. Na **wizualizacje** wybierz opcję **karty wielowierszowych** wizualizacji. Przeciągnij **modelu** i **vin** do **pola** obszaru.

    ![Karta wielowierszowych](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

13. Rozmieszczanie wszystkich wizualizacjach, dzięki czemu raportu końcowego wygląda jak w następującym przykładzie: 

    ![Raport końcowy przeobrażone](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Pomyślnie skonfigurowano raportu w czasie rzeczywistym "Pojazdów wymagających obsługi". Można utworzyć dalej raport w czasie rzeczywistym, lub można zatrzymać w tym miejscu i konfigurowanie pulpitu nawigacyjnego. 

### <a name="vehicle-health-statistics-report"></a>Raport statystyki kondycji vehicle

1. Wybierz ![Dodaj](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) Aby dodać nowy raport. Zmień jego nazwę **statystyki kondycji pojazdów**. 

2. Na **wizualizacje** wybierz opcję **miernika** wizualizacji. Przeciągnij **szybkości** do **wartość**, **wartość minimalna**, i **maksymalna wartość** obszarów.

   ![Statystyki kondycji pojazdów](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

3. W **wartość** obszaru, zmień domyślny agregacji **szybkości** do **średni**.

4. W **wartość minimalna** obszaru, zmień domyślny agregacji **szybkości** do **Minimum**.

5. W **maksymalna wartość** obszaru, zmień domyślny agregacji **szybkości** do **maksymalna**.

   ![Szybkość wartości](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

6. Zmień nazwę **tytuł miernika** do **średnia szybkość**.

   ![Wskaźnik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

7. Wybierz pusty obszar można dodać nowej wizualizacji.  

    Podobnie, Dodaj **miernika** dla **średni wydobycie ropy naftowej aparat**, **średni paliwa**, i **średnia temperatura aparat**.  

8. Zmień agregacji domyślne pól w każdym miernika, tak jak w poprzednich krokach **średnia szybkość** miernika.

    ![Dodatkowe mierników](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

9. Wybierz pusty obszar można dodać nowej wizualizacji.

10. Na **wizualizacje** wybierz opcję **liniowy i kolumnowy grupowany** wizualizacji. Przeciągnij **miasta** do **udostępnionych osi**. Przeciągnij **tirepressure**, **engineoil**, i **szybkości** do **wartości w kolumnie** obszaru. Zmień ich typ agregacji do **średni**. 

11. Przeciągnij **engineTemperature** do **wartości wiersza** obszaru. Zmień typ agregacji do **średni**. 

    ![Kolumny i wartości wiersza](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

12. Zmień wykres **tytuł** do **średnia szybkość, wykorzystania opona aparat wydobycie ropy naftowej i temperatury aparat**.  

    ![Tytuł liniowy i kolumnowy grupowany](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

13. Wybierz pusty obszar można dodać nowej wizualizacji.

14. Na **wizualizacje** wybierz opcję **właściwości** wizualizacji. Przeciągnij **modelu** do **grupy** obszaru. Przeciągnij **MaintenanceProbability** do **wartości** obszaru.

15. Zmień wykres **tytuł** do **modele pojazdów wymagających obsługi**.

    ![Nazwa właściwości](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

16. Wybierz pusty obszar można dodać nowej wizualizacji.

17. Na **wizualizacje** wybierz opcję **100% skumulowany wykres słupkowy** wizualizacji. Przeciągnij **miasta** do **osi** obszaru. Przeciągnij **MaintenanceProbability** i **RecallProbability** do **wartość** obszaru.

    ![Obszary osi i wartości](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

18. Na **Format** zaznacz **kolory danych**. Ustaw **MaintenanceProbability** kolor wartości **F2C80F**.

19. Zmień wykres **tytuł** do **prawdopodobieństwo programu obsługi & odwołania przez Miasto**.

    ![100% skumulowany wykres słupkowy tytułu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

20. Wybierz pusty obszar można dodać nowej wizualizacji.

21. Na **wizualizacje** wybierz opcję **wykres warstwowy** wizualizacji. Przeciągnij **modelu** do **osi** obszaru. Przeciągnij **engineOil**, **tirepressure**, **szybkości**, i **MaintenanceProbability** do **wartości** obszar. Zmień ich typ agregacji do **średni**. 

    ![Typ agregacji](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

22. Zmień wykres **tytuł** do **średni aparat wydobycie ropy naftowej, wykorzystania opona szybkość i prawdopodobieństwo obsługi przez model**.

    ![Obszar wykresu tytułu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

23. Wybierz pusty obszar można dodać nowej wizualizacji.

24. Na **wizualizacje** wybierz opcję **wykres punktowy** wizualizacji. Przeciągnij **modelu** do **szczegóły** i **legendy** obszarów. Przeciągnij **paliwa** do **osi x** obszaru. Zmień agregacji do **średni**. Przeciągnij **engineTemperature** do **osi y** obszaru. Zmień agregacji do **średni**. Przeciągnij **vin** do **rozmiar** obszaru.

    ![Szczegółowe informacje, obszarów legendy, osi i rozmiaru](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

25. Zmień wykres **tytuł** do **średni paliwa, średnia engineTemperature i liczba vin przez Model**.

    ![Wykres punktowy tytułu wykresu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    Raport końcowy wygląda następująco:

    ![Raport końcowy](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Wizualizacje numeru PIN z raportów do pulpitu nawigacyjnego w czasie rzeczywistym
1. Tworzenie pustego pulpitu nawigacyjnego, wybierając znak plus obok pozycji **pulpity nawigacyjne**. Wprowadź nazwę **pulpitu nawigacyjnego Analytics Telemetrii Vehicle**.

    ![Pulpit nawigacyjny oraz symbol](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

2. Przypnij wizualizacje z poprzednich raportów do pulpitu nawigacyjnego. 

    ![Pulpit nawigacyjny symbol numeru pin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    Gdy wszystkie trzy raporty są przypięty do pulpitu nawigacyjnego, powinien wyglądać jak w następującym przykładzie. Jeśli nie utworzono wszystkie raporty, pulpit nawigacyjny może wyglądać inaczej. 

    ![Pulpit nawigacyjny oferujący raporty](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Pomyślnie utworzono w czasie rzeczywistym pulpitu nawigacyjnego. Kontynuując uruchomienia CarEventGenerator.exe i RealtimeDashboardApp.exe na żywo aktualizacje są wyświetlane na pulpicie nawigacyjnym. Wykonanie poniższych kroków zająć około 10 – 15 minut.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Konfigurowanie usługi Power BI przetwarzania wsadowego z pulpitu nawigacyjnego
> [!NOTE]
> Trwa około dwie godziny (od pomyślnego ukończenia wdrożenia) dla potoku, aby zakończyć wykonywania i przetworzyć roku, przez które wygenerowanych danych przetwarzania wsadowego end-to-end. Poczekaj na zakończenie przetwarzania przed rozpoczęciem pracy z następujących kroków:
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Pobierz plik projektanta usługi Power BI

1. Wstępnie skonfigurowane pliku projektanta usługi Power BI wchodzi w skład instrukcji ręczne operacji wdrożenia. Wyszukaj "2. Konfigurowanie pulpitu nawigacyjnego przetwarzania wsadowego usługi Power BI."

2. Pobierz szablon usługi Power BI dla pulpitu nawigacyjnego przetwarzania wsadowego nosi nazwę **ConnectedCarsPbiReport.pbix**.

3. Zapisz lokalnie.

### <a name="configure-power-bi-reports"></a>Konfigurowanie raportów usługi Power BI

1. Otwórz plik projektanta **ConnectedCarsPbiReport.pbix** przy użyciu programu Power BI Desktop. Jeśli nie masz jeszcze go, należy zainstalować Power BI Desktop z [instalacji Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331) witryny sieci Web.

2. Wybierz **Edytuj zapytania**.

    ![Edytowanie zapytań](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

3. Kliknij dwukrotnie **źródła**.

    ![Element źródłowy](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

4. Zaktualizuj parametry połączenia serwera z serwerem Azure SQL, które uzyskano udostępnione jako część wdrożenia. Szukaj w instrukcji ręczne operacji w bazie danych Azure SQL:

    * Server: somethingsrv.database.windows.net
    * Baza danych: connectedcar
    * Nazwa użytkownika: nazwa użytkownika
    * Hasło: Hasło programu SQL Server można zarządzać w portalu Azure.

5. Pozostaw **bazy danych** jako **connectedcar**.

    ![Database (Baza danych)](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

6. Kliknij przycisk **OK**.

7. **Poświadczenia systemu Windows** karta jest domyślnie zaznaczona. Zmień, aby **bazy danych poświadczeń** wybierając **bazy danych** kartę po prawej stronie.

8. Wprowadź **Username** i **hasło** określone podczas jego ustawienia wdrażania bazy danych Azure SQL.

    ![Poświadczenia bazy danych](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

9. Wybierz przycisk **Połącz**.

10. Powtórz poprzednie kroki dla każdego z trzech pozostałych kwerend w okienku po prawej stronie. Następnie zaktualizuj informacje dotyczące połączenia dla źródła danych.

11. Wybierz **Zamknij i załadować**. Power BI Desktop pliku danych zestawy są połączone w tabelach bazy danych SQL.

12. Wybierz **zamknąć** można zamknąć pliku Power BI Desktop.

    ![Zamykanie](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

13. Wybierz **zapisać** Aby zapisać zmiany. 

Wszystkie raporty, które odpowiadają ścieżki w rozwiązaniu przetwarzania wsadowego został skonfigurowany. 

## <a name="upload-to-powerbicom"></a>Przekaż do witryny powerbi.com
1. Przejdź do [portalu sieci web usługi Power BI](http://powerbi.com)i zaloguj się.

2. Wybierz pozycję **Pobieranie danych**.

3. Przekaż plik Power BI Desktop. Wybierz **Pobierz dane** > **pliki** > **pliku lokalnego**.

4. Przejdź do **ConnectedCarsPbiReport.pbix**.

5. Po przekazaniu pliku, przejdź wstecz do obszaru roboczego usługi Power BI. Zestaw danych raportu i pustego pulpitu nawigacyjnego są tworzone automatycznie.  

6. Wykresy numeru PIN, aby nowy pulpit nawigacyjny o nazwie **pulpitu nawigacyjnego Analytics Telemetrii Vehicle** w usłudze Power BI. Wybierz opcję Pusta pulpit nawigacyjny, który został utworzony wcześniej, a następnie przejdź do **raporty** sekcji. Wybierz nowo przesłanym raport.  

    ![Nowy pulpit nawigacyjny usługi Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    Raport ma sześć stron:

    Strona 1: Gęstość Vehicle  
    Strona 2: Kondycja vehicle w czasie rzeczywistym  
    Strona 3: Agresywnie zmiennych pojazdów   
    4 strony: Pojazdów odwołane  
    5 strony: Efektywne zmiennych pojazdów paliwa  
    6 strony: Logo firmy Contoso silniki  

    ![Power BI raport o sześciu stron](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

7. Z **strona 3**, przypiąć następującą zawartość:  

    a. **Liczba vin**  

   ![Vin liczby 3 stron](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Agresywnie zmiennych pojazdów przez model — wykresu wykresu kaskadowego** 

   ![Wykres 3 strony 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

8. Z **5 strony**, przypiąć następującą zawartość: 

    a. **Liczba vin**

   ![Wykres 5 strony 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Obniżające zużycie paliwa pojazdów przez model: kolumnowy grupowany**

   ![Wykres strony 5, 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

9. Z **4 strony**, przypiąć następującą zawartość:  

    a. **Liczba vin** 

   ![Strona 4 wykresu 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Odwołane pojazdów miejscowość, model: właściwości**

   ![Strona 4 wykresu 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

10. Z **6 strony**, przypiąć następującą zawartość:  

    * **Logo firmy Contoso silniki**

    ![Logo firmy Contoso silniki](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Organizowanie pulpitu nawigacyjnego  

1. Przejdź do pulpitu nawigacyjnego.

2. Umieść kursor nad każdym wykresu. Zmień nazwę każdego wykres oparty na podane w poniższym przykładzie gotowy pulpit nawigacyjny nazewnictwa:

   ![Pulpit nawigacyjny organizacji](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
3. Przenieś wykresy około, aby wyglądały jak w poniższym przykładzie pulpitu nawigacyjnego:

    ![Przeobrażone pulpitu nawigacyjnego](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

4. Po utworzeniu wszystkich raportów, które są wymienione w niniejszym dokumencie końcowego gotowy pulpit nawigacyjny wygląda następująco: 

   ![Końcowy pulpitu nawigacyjnego](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Pomyślnie utworzono raporty i pulpit nawigacyjny w celu uzyskania w czasie rzeczywistym, predykcyjnych i zwyczaje rozeznanie partii vehicle kondycji i wspierającym.  
