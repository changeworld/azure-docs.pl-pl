---
title: Pulpit nawigacyjny usługi Power BI dla kondycję pojazdów i nawyki kierowców — Azure | Dokumentacja firmy Microsoft
description: Korzystając z możliwości pakietu Cortana Intelligence możesz uzyskać w czasie rzeczywistym i predykcyjny wgląd w kondycję pojazdów i nawyki nawyków.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 03/14/2018
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: b4d36b7d68bad658200024a2fe31a876d411536c
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444653"
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Instrukcje instalacji pulpit nawigacyjny Telemetrii Analytics szablon rozwiązania usługi Power BI pojazdu

Prezentuje rozwiązanie do analizy Telemetrii pojazdów, jak dealerzy i producenci samochodów oraz firmy ubezpieczeniowe są w stanie korzystać z możliwości pakietu Cortana Intelligence. Można uzyskać w czasie rzeczywistym i predykcyjny wgląd w kondycję pojazdów i nawyki nawyki w celu poprawy obsługi klienta, badań i rozwoju i kampanii marketingowych. Te instrukcje krok po kroku przedstawiające sposób konfigurowania raportów usługi Power BI i pulpit nawigacyjny po wdrożeniu rozwiązania w ramach subskrypcji. 

Aby przejrzeć podsumowanie tego rozwiązania, zobacz [podręcznik dotyczący rozwiązań analizy Telemetrii pojazdów](cortana-analytics-playbook-vehicle-telemetry.md).
Aby zapoznać się jeszcze bardziej tego rozwiązania, zobacz [podręcznik dotyczący rozwiązań analizy Telemetrii pojazdów: szczegółami rozwiązania](cortana-analytics-playbook-vehicle-telemetry-deep-dive.md).

## <a name="prerequisites"></a>Wymagania wstępne
* Wdrażanie [analiza Telemetrii pojazdów](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90) rozwiązania. 
* [Instalowanie programu Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331).
* Uzyskaj [subskrypcji platformy Azure](https://azure.microsoft.com/pricing/free-trial/). Jeśli nie masz subskrypcji platformy Azure, Zacznij korzystać z bezpłatnej subskrypcji platformy Azure.
* Otwórz konto usługi Power BI.

## <a name="cortana-intelligence-suite-components"></a>Składników pakietu Cortana Intelligence
Jako część szablonu rozwiązania analizy Telemetrii pojazdów następujące usługi pakietu Cortana Intelligence są wdrażane w ramach subskrypcji:

* **Usługa Azure Event Hubs** pozyskuje miliony zdarzeń telemetrii pojazdów na platformie Azure.
* **Usługa Azure Stream Analytics** udostępnia w czasie rzeczywistym szczegółowe informacje o kondycji pojazdu i utrwalenia tych danych w długoterminowym magazynie do pełniejszej analizy wsadowej.
* **Usługa Azure Machine Learning** wykrywa anomalie w czasie rzeczywistym i używa przetwarzania wsadowego, aby zapewnić predykcyjnego wglądu w dane.
* **Usługa Azure HDInsight** przekształca dane w dużej skali.
* **Usługa Azure Data Factory** obsługuje organizowanie, planowanie, zarządzanie zasobami i monitorowanie potoku przetwarzania wsadowego.

**Usługa Power BI** wyposaża to rozwiązanie w rozbudowany pulpit nawigacyjny dla danych i analizy predykcyjnej, wizualizacji. 

Rozwiązanie korzysta z dwóch różnych źródeł danych:

* Sygnały symulowanego pojazdu i zestawów danych diagnostycznych
* Katalog pojazdów

Symulator telematyce pojazdu wchodzi w skład tego rozwiązania. Emituje sygnałów, które odpowiadają stan pojazdu i schematy w danym punkcie w czasie i informacji diagnostycznych. 

Katalog pojazdów jest zestawu danych referencyjnych, która mapuje VINs modeli.

## <a name="power-bi-dashboard-preparation"></a>Usługa Power BI pulpit nawigacyjny przygotowania
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Ustawianie pulpitu nawigacyjnego w czasie rzeczywistym usługi Power BI

#### <a name="start-the-real-time-dashboard-application"></a>Uruchom aplikację pulpitu nawigacyjnego w czasie rzeczywistym
Po zakończeniu wdrażania, postępuj zgodnie z instrukcjami operacji ręcznych.

1. Pobierz aplikację pulpitu nawigacyjnego w czasie rzeczywistym RealtimeDashboardApp.zip i Rozpakuj go.

1.  W folderze rozpakowany Otwórz RealtimeDashboardApp.exe.config pliku konfiguracyjnego aplikacji. Zastąp appSettings dla usługi Event Hubs, Azure Blob storage i połączeń usługi Azure Machine Learning z wartościami w instrukcjach operacji ręcznych. Zapisz zmiany.

1. Uruchom aplikację RealtimeDashboardApp.exe. W oknie logowania wprowadź prawidłowe poświadczenia usługi Power BI. 

   ![Usługa Power BI w oknie rejestrowania](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
1. Wybierz pozycję **Zaakceptuj**. Aplikacja zaczyna być uruchamiana.

   ![Uprawnienia pulpitu nawigacyjnego usługi Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

1. Zaloguj się do witryny sieci Web usługi Power BI i utworzyć pulpit nawigacyjny w czasie rzeczywistym.

Teraz możesz przystąpić do konfigurowania pulpitu nawigacyjnego usługi Power BI.  

### <a name="configure-power-bi-reports"></a>Konfigurowanie raportów usługi Power BI
W czasie rzeczywistym raporty i pulpity nawigacyjne zająć około 30 do 45 minut. 

1. Przejdź do [usługi Power BI](http://powerbi.com) strony sieci Web i zaloguj się.

    ![Zaloguj się Strona usługi Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

1. Nowy zestaw danych jest generowany w usłudze Power BI. Wybierz **ConnectedCarsRealtime** zestawu danych.

    ![Zestaw danych ConnectedCarsRealtime](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

1. Aby zapisać pusty raport, naciśnij klawisze Ctrl + S.

    ![Pusty raport](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

1. Wprowadź nazwę raportu **w w czasie rzeczywistym analizy Telemetrii pojazdów — raporty**.

    ![Nazwa raportu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Raporty w czasie rzeczywistym
Trzy raporty w czasie rzeczywistym znajdują się w tym rozwiązaniu:

* Pojazdów w operacji
* Konserwacji wymagające pojazdów
* Statystyki kondycji pojazdu

Można skonfigurować wszystkie trzy raporty, lub możesz zatrzymać po każdym etapie. Następnie można przejść do następnej sekcji na temat sposobu konfigurowania raportów usługi batch. Firma Microsoft zaleca utworzenie wszystkie trzy raporty w celu wizualizacji pełny wgląd w dane w czasie rzeczywistym ścieżka rozwiązania.  

### <a name="vehicles-in-operation-report"></a>Pojazdów w raporcie operacji
1. Kliknij dwukrotnie **strona 1**i zmień jego nazwę **pojazdów w operacji**.

    ![Pojazdów w operacji](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

1. Na **pola** zaznacz **vin**. Na **wizualizacje** zaznacz **karty** wizualizacji.  

    **Karty** zostanie utworzona wizualizacja, jak pokazano na poniższej ilustracji:

    ![Wybierz vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

1. Wybierz pusty obszar, aby dodać nową wizualizację.  

1. Na **pola** zaznacz **Miasto** i **vin**. Na **wizualizacje** zaznacz **mapy** wizualizacji. Przeciągnij **vin** do **wartości** obszaru. Przeciągnij **Miasto** do **legendy** obszaru. 

    ![Wizualizacja w postaci karty](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

1. Na **wizualizacje** zaznacz **Format** sekcji. Wybierz **tytuł**i zmień **tekstu** do **pojazdów w operacji według miasta**.

    ![Pojazdów w operacji według miast](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    Końcowe wizualizacji wygląda następująco:

    ![Wizualizacja końcowej](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

1. Wybierz pusty obszar, aby dodać nową wizualizację.  

1. Na **pola** zaznacz **Miasto** i **vin**. Na **wizualizacje** zaznacz **wykres kolumnowy grupowany** wizualizacji. Przeciągnij **Miasto** do **osi** obszaru. Przeciągnij **vin** do **wartość** obszaru.

1. Posortuj wykres według **liczba vin**.

    ![Liczba vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

1. Zmień wykres **tytuł** do **pojazdów w operacji według miasta**. 

1. Wybierz **Format** sekcji, a następnie wybierz **kolory danych**. Zmiana **Pokaż wszystko** do **na**.

    ![Kolory danych](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

1. Umożliwia zmianę koloru poszczególnych miasta, wybierając symbol kolorów.

    ![Zmiana koloru](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

1. Wybierz pusty obszar, aby dodać nową wizualizację.  

1. Na **wizualizacje** zaznacz **wykres kolumnowy grupowany** wizualizacji. Na **pola** kartę, przeciągnij **Miasto** do **osi** obszaru. Przeciągnij **modelu** do **legendy** obszaru. Przeciągnij **vin** do **wartość** obszaru.

    ![Wykres kolumnowy grupowany](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    Wykres wygląda podobnie do następującego:

    ![Renderowanie](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

1. Rozmieszczanie wszystkie wizualizacje tak, aby strona wygląda następująco:

    ![Pulpit nawigacyjny z wizualizacjami](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Pomyślnie skonfigurowano raportu "Pojazdów w operacji". Można utworzyć kolejny raport w czasie rzeczywistym lub można zatrzymać w tym miejscu i konfigurowanie pulpitu nawigacyjnego. 

### <a name="vehicles-requiring-maintenance-report"></a>Raport konserwacji wymagające pojazdów

1. Wybierz ![Dodaj](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) pozwala dodać nowy raport. Zmień jej nazwę **konserwacji wymagające pojazdów**.

    ![Konserwacji wymagające pojazdów](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

1. Na **pola** zaznacz **vin**. Na **wizualizacje** zaznacz **karty** wizualizacji.

    ![Wizualizacja w postaci karty VIN](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    Zestaw danych zawiera pole o nazwie **MaintenanceLabel**. To pole może mieć wartość "0" lub "1". Wartość jest ustawiana przez model uczenia maszynowego, który jest aprowizowany w ramach rozwiązania. Jest zintegrowany ze ścieżką w czasie rzeczywistym. Wartość "1" wskazuje, że pojazdu wymaga konserwacji. 

1. Aby dodać **filtr poziomu strony** możesz wyświetlać dane dla pojazdów, które wymagają obsługi: 

   a. Przeciągnij **MaintenanceLabel** pole **filtry na poziomie strony**.
  
      ![Filtry na poziomie strony](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. W dolnej części **MaintenanceLabel filtry na poziomie strony**, wybierz opcję **filtrowanie podstawowe**.

      ![Filtrowanie podstawowe](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Ustaw wartość filtru **1**.

      ![Wartość filtru](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

1. Wybierz pusty obszar, aby dodać nową wizualizację.  

1. Na **wizualizacje** zaznacz **wykres kolumnowy grupowany** wizualizacji. 

    ![Karta VIN](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Wykres kolumnowy grupowany](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

1. Na **pola** kartę, przeciągnij **modelu** do **osi** obszaru. Przeciągnij **vin** do **wartość** obszaru. Następnie posortować wizualizacje według **liczba vin**. Zmień wykres **tytuł** do **pojazdy wymagające obsługi przez model**. 

1. Na **pola** ![pola obrazu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) części **wizualizacje** kartę, przeciągnij **vin** do **nasycenie koloru**.

    ![Nasycenie koloru](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

1. Na **Format** sekcji, zmień **kolory danych** w wizualizacji: 

    a. Zmiana **Minimum** kolor **F2C812**.

    b. Zmiana **maksymalna** kolor **FF6300**.

    ![Nowe kolory danych](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    Nowy wygląd kolory wizualizacji tak jak w poniższym przykładzie:

    ![Nowe kolory wizualizacji](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

1. Wybierz pusty obszar, aby dodać nową wizualizację.  

1. Na **wizualizacje** zaznacz **wykres kolumnowy grupowany**. Przeciągnij **vin** do **wartość** obszaru. Przeciągnij **Miasto** do **osi** obszaru. Posortuj wykres według **liczba vin**. Zmień wykres **tytuł** do **pojazdy wymagające obsługi według miasta**.

    ![Pojazdy wymagające obsługi według miast](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

1. Wybierz pusty obszar, aby dodać nową wizualizację.  

1. Na **wizualizacje** zaznacz **kart wielowierszowych** wizualizacji. Przeciągnij **modelu** i **vin** do **pola** obszaru.

    ![Karta z wieloma wierszami](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

1. Rozmieszczanie wszystkie wizualizacje tak, aby raport końcowy wygląda następująco: 

    ![Raport końcowy przeobrażone](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Pomyślnie skonfigurowano raportu w czasie rzeczywistym "Maintenance wymagające pojazdów". Można utworzyć kolejny raport w czasie rzeczywistym lub można zatrzymać w tym miejscu i konfigurowanie pulpitu nawigacyjnego. 

### <a name="vehicle-health-statistics-report"></a>Raport statystyki kondycji pojazdu

1. Wybierz ![Dodaj](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) pozwala dodać nowy raport. Zmień jej nazwę **statystyki kondycji pojazdów**. 

1. Na **wizualizacje** zaznacz **miernika** wizualizacji. Przeciągnij **szybkość** do **wartość**, **wartość minimalna**, i **maksymalną wartość** obszarów.

   ![Statystyki kondycji pojazdów](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

1. W **wartość** obszaru, Zmień domyślną agregację **szybkość** do **średni**.

1. W **wartość minimalna** obszaru, Zmień domyślną agregację **szybkość** do **Minimum**.

1. W **maksymalną wartość** obszaru, Zmień domyślną agregację **szybkość** do **maksymalna**.

   ![Wartości prędkości](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

1. Zmień nazwę **tytuł miernika** do **średnia szybkość**.

   ![Wskaźnik](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

1. Wybierz pusty obszar, aby dodać nową wizualizację.  

    Podobnie, Dodaj **miernika** dla **średni przemysł aparatu**, **średni paliwa**, i **średnia temperatura aparatu**.  

1. Zmień domyślna agregacja pola w poszczególnych mierników, tak jak w poprzednich krokach w **średnia szybkość** miernika.

    ![Dodatkowe mierników](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

1. Wybierz pusty obszar, aby dodać nową wizualizację.

1. Na **wizualizacje** zaznacz **liniowy i wykres kolumnowy grupowany** wizualizacji. Przeciągnij **Miasto** do **oś udostępniona**. Przeciągnij **tirepressure**, **engineoil**, i **szybkość** do **wartości w kolumnie** obszaru. Zmień ich typ agregacji na **średni**. 

1. Przeciągnij **engineTemperature** do **wartości wiersza** obszaru. Zmień typ agregacji na **średni**. 

    ![Kolumny i wartości wiersza](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

1. Zmień wykres **tytuł** do **średnia szybkość, wykorzystanie opona, aparat ropa naftowa i temperatury aparatu**.  

    ![Tytuł liniowy i wykres kolumnowy grupowany](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

1. Wybierz pusty obszar, aby dodać nową wizualizację.

1. Na **wizualizacje** zaznacz **drzewa** wizualizacji. Przeciągnij **modelu** do **grupy** obszaru. Przeciągnij **MaintenanceProbability** do **wartości** obszaru.

1. Zmień wykres **tytuł** do **modele pojazdów wymagających obsługi**.

    ![Tytuł mapy drzewa](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

1. Wybierz pusty obszar, aby dodać nową wizualizację.

1. Na **wizualizacje** zaznacz **100% skumulowany wykres słupkowy** wizualizacji. Przeciągnij **Miasto** do **osi** obszaru. Przeciągnij **MaintenanceProbability** i **RecallProbability** do **wartość** obszaru.

    ![Obszary osi i wartości](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

1. Na **Format** zaznacz **kolory danych**. Ustaw **MaintenanceProbability** kolorów do wartości **F2C80F**.

1. Zmień wykres **tytuł** do **prawdopodobieństwo pojazdu konserwacji & odwołania według miasta**.

    ![100% skumulowany wykres słupkowy tytułu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

1. Wybierz pusty obszar, aby dodać nową wizualizację.

1. Na **wizualizacje** zaznacz **wykres warstwowy** wizualizacji. Przeciągnij **modelu** do **osi** obszaru. Przeciągnij **engineOil**, **tirepressure**, **szybkość**, i **MaintenanceProbability** do **wartości** obszar. Zmień ich typ agregacji na **średni**. 

    ![Typ agregacji](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

1. Zmień wykres **tytuł** do **średni przemysł aparatu, wykorzystanie opona, szybkość i prawdopodobieństwa konserwacji przez model**.

    ![Obszaru tytułu wykresu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

1. Wybierz pusty obszar, aby dodać nową wizualizację.

1. Na **wizualizacje** zaznacz **wykres punktowy** wizualizacji. Przeciągnij **modelu** do **szczegóły** i **legendy** obszarów. Przeciągnij **paliwa** do **osi x** obszaru. Zmiana opcji agregacji na **średni**. Przeciągnij **engineTemperature** do **osi y** obszaru. Zmiana opcji agregacji na **średni**. Przeciągnij **vin** do **rozmiar** obszaru.

    ![Szczegółowe informacje, obszary legendy, osi i rozmiaru](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

1. Zmień wykres **tytuł** do **średni paliwa, średnia engineTemperature i liczba vin przez Model**.

    ![Tytuł wykresu punktowego](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    Raport końcowy wygląda następująco:

    ![Raport końcowy](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Przypinanie wizualizacji z raportów na pulpicie nawigacyjnym w czasie rzeczywistym
1. Tworzenie pustego pulpitu nawigacyjnego, wybierając symbol znaku plus obok pozycji **pulpity nawigacyjne**. Wprowadź nazwę **pulpit nawigacyjny analizy Telemetrii pojazdów**.

    ![Pulpit nawigacyjny oraz symbol](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

1. Przypnij wizualizacje z raportów poprzedniej do pulpitu nawigacyjnego. 

    ![Symbolu Przypnij pulpitu nawigacyjnego](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    Gdy wszystkie trzy raporty są przypięte do pulpitu nawigacyjnego, powinno to wyglądać podobnie jak w poniższym przykładzie. Jeśli nie utworzono wszystkie raporty, pulpit nawigacyjny może wyglądać inaczej. 

    ![Pulpit nawigacyjny oferujący raporty](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Pomyślnie utworzono pulpit nawigacyjny w czasie rzeczywistym. W miarę postępu wykonywania CarEventGenerator.exe i RealtimeDashboardApp.exe aktualizacje na żywo jest widoczne na pulpicie nawigacyjnym. Poniższe kroki zająć około 10 – 15 minut.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Ustawianie pulpitu nawigacyjnego przetwarzania wsadowego usługi Power BI
> [!NOTE]
> Trwa około dwóch godzin (od pomyślnego zakończenia wdrożenia) end-to-end wsadowo potoku zakończy się wykonywanie i przetworzyć roku wygenerowane dane. Poczekaj na zakończenie przetwarzania przed kontynuowaniem wykonując następujące kroki:
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Pobierz plik projektanta usługi Power BI

1. Wstępnie skonfigurowane pliku projektanta usługi Power BI wchodzi w skład instrukcji ręczne operacji wdrożenia. Zwróć uwagę na "2. Konfigurowanie usługi Power BI przetwarzania wsadowego z pulpitu nawigacyjnego."

1. Pobierz szablon usługi Power BI dla przetwarzania wsadowego pulpitu nawigacyjnego, w tym miejscu o nazwie **ConnectedCarsPbiReport.pbix**.

1. Zapisz lokalnie.

### <a name="configure-power-bi-reports"></a>Konfigurowanie raportów usługi Power BI

1. Otwórz plik projektanta **ConnectedCarsPbiReport.pbix** przy użyciu programu Power BI Desktop. Jeśli nie masz jeszcze je, zainstaluj program Power BI Desktop z [instalacji programu Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) witryny sieci Web.

1. Wybierz **Edytuj zapytania**.

    ![Edytuj zapytania](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

1. Kliknij dwukrotnie **źródła**.

    ![Element źródłowy](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

1. Zaktualizuj parametry połączenia serwera przy użyciu usługi Azure SQL server, który został aprowizowany w ramach wdrożenia. Szukaj w instrukcje ręcznego operacji w usłudze Azure SQL database:

    * Server: somethingsrv.database.windows.net
    * Baza danych: connectedcar
    * Nazwa użytkownika: nazwa użytkownika
    * Hasło: Hasło programu SQL Server można zarządzać w witrynie Azure portal.

1. Pozostaw **bazy danych** jako **connectedcar**.

    ![Database (Baza danych)](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

1. Kliknij przycisk **OK**.

1. **Poświadczeń Windows** domyślnie wybrana jest karta. Zmień ją na **bazy danych poświadczenia** , wybierając **bazy danych** kartę po prawej stronie.

1. Wprowadź **Username** i **hasło** z usługi Azure SQL database, który został określony podczas instalacji jego wdrożenia.

    ![Poświadczenia bazy danych](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

1. Wybierz przycisk **Połącz**.

1. Powtórz poprzednie kroki dla każdego z trzech pozostałych kwerend w okienku po prawej stronie. Następnie zaktualizuj szczegóły połączenia źródła danych.

1. Wybierz **Zamknij i załaduj**. Power BI Desktop plik danych zestawy są połączone w tabelach bazy danych SQL.

1. Wybierz **Zamknij** można zamknąć pliku programu Power BI Desktop.

    ![Zamykanie](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

1. Wybierz **Zapisz** Aby zapisać zmiany. 

Wszystkie raporty, które odnoszą się do ścieżki w rozwiązaniu wsadowo został skonfigurowany. 

## <a name="upload-to-powerbicom"></a>Przekaż do witryny powerbi.com
1. Przejdź do [portalu sieci web usługi Power BI](http://powerbi.com)i zaloguj się.

1. Wybierz pozycję **Pobieranie danych**.

1. Przekaż plik programu Power BI Desktop. Wybierz **Pobierz dane** > **pliki** > **lokalnego pliku**.

1. Przejdź do **ConnectedCarsPbiReport.pbix**.

1. Po przekazaniu pliku wróć do obszaru roboczego usługi Power BI. Zestaw danych, raport i pustego pulpitu nawigacyjnego są tworzone dla Ciebie.  

1. Przypnij wykresy do nowego pulpitu nawigacyjnego o nazwie **pulpit nawigacyjny analizy Telemetrii pojazdów** w usłudze Power BI. Wybierz pustego pulpitu nawigacyjnego, który został wcześniej utworzony, a następnie przejdź do **raporty** sekcji. Wybierz nowo przesłanym raport.  

    ![Nowy pulpit nawigacyjny usługi Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    Raport zawiera sześć stron:

    Strona 1: Gęstość pojazdu  
    Strona 2: Kondycję pojazdów w czasie rzeczywistym  
    Strona 3: Agresywnie opartych na pojazdów   
    Strona 4: Przypomnieć pojazdów  
    Strona 5: We wprowadzaniu, efektywnie opartych na pojazdów  
    6 strony: Logo firmy Contoso Motors  

    ![Raport usługi Power BI za pomocą sześciu stron](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

1. Z **strona 3**, Przypnij następującej zawartości:  

    a. **Liczba vin**  

   ![Strona 3 liczba vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Agresywne wynika pojazdów model — wykres kaskadowy** 

   ![Wykres strona 3 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

1. Z **strona 5**, Przypnij następującej zawartości: 

    a. **Liczba vin**

   ![Strona 5 wykresu 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Obniżające zużycie paliwa pojazdów przez model: wykres kolumnowy grupowany**

   ![Wykres strona 5, 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

1. Z **strona 4**, Przypnij następującej zawartości:  

    a. **Liczba vin** 

   ![Strona 4 wykresu 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Odwołane pojazdów według Miasto, model: Mapa drzewa**

   ![Strona 4 wykresu 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

1. Z **strona 6**, Przypnij następującej zawartości:  

    * **Logo firmy Contoso Motors**

    ![Logo firmy Contoso Motors](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Organizowanie pulpitu nawigacyjnego  

1. Przejdź do pulpitu nawigacyjnego.

1. Umieść kursor nad poszczególnych wykresów. Zmień nazwę poszczególnych wykresów w oparciu naming, podana w poniższym przykładzie gotowy pulpit nawigacyjny:

   ![Pulpitu nawigacyjnego organizacji](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
1. Przenieś wykresy około, aby wyglądał jak na poniższym przykładzie pulpitu nawigacyjnego:

    ![Przeobrażone pulpitu nawigacyjnego](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

1. Po utworzeniu wszystkie raporty, które są wymienione w niniejszym dokumencie, końcowe gotowy pulpit nawigacyjny wygląda następująco: 

   ![Końcowe pulpitu nawigacyjnego](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Udało Ci się utworzyć raporty i pulpity nawigacyjne w celu uzyskania w czasie rzeczywistym, predykcyjne, a batch wgląd w kondycję pojazdów i nawyki kierowców.  
