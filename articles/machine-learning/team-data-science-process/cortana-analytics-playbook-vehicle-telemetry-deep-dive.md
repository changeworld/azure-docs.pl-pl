---
title: Szczegółowo omówi sposób przewidywania usterek w kondycję pojazdów i nawyki kierowców — Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: b1c01bcd15c5cfc63eae7c2b1983fc9075d4220c
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444388"
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Podręcznik dotyczący rozwiązań analizy Telemetrii pojazdów: szczegółami rozwiązania

Ten artykuł rozwija do poszczególnych etapów przedstawiono w ramach architektury rozwiązania. Instrukcje i wskaźników do dostosowania są uwzględniane. 

Aby przejrzeć podsumowanie tego rozwiązania, zobacz [podręcznik dotyczący rozwiązań analizy Telemetrii pojazdów](cortana-analytics-playbook-vehicle-telemetry.md).


## <a name="data-sources"></a>Źródła danych
Rozwiązanie korzysta z dwóch różnych źródeł danych:

* Sygnały symulowanego pojazdu i zestaw danych diagnostycznych
* Katalog pojazdów

Symulator telematyce pojazdu wchodzi w skład tego rozwiązania, jak pokazano na poniższym zrzucie ekranu. Emituje sygnałów, które odpowiadają stan pojazdu i opracowuje wzorca w danym punkcie w czasie i informacji diagnostycznych.  Katalog pojazdów zawiera odwołanie do zestawu danych, mapująca pojazdu numery identyfikacyjne (VINs) do modeli. Uwaga: Zestaw danych Vehicle Telematics Simulator rozwiązania Visual Studio nie jest już dostępna. 

![Vehicle telematics simulator](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


Ten zestaw danych sformatowanego JSON zawiera zgodny z następującym schematem.

| Kolumna | Opis | Wartości |
| --- | --- | --- |
| VIN |Losowo generowany VIN |Uzyskany z główną listę VINs generowany losowo 10 000 |
| Temperatury zewnętrznej |Temperatury zewnętrznej, gdzie sterujące pojazdu |Generowany losowo numer z zakresu od 0 do 100 |
| Temperatura silnika |Temperatura silnika pojazdu |Generowany losowo numer z zakresu od 0 do 500 |
| Szybkość |Obroty silnika, jaką sterujące pojazdu |Generowany losowo numer z zakresu od 0 do 100 |
| We wprowadzaniu |Poziom paliwa pojazdu |Generowany losowo numer z zakresu od 0 do 100 (wskazuje procentowy poziom paliwa) |
| EngineOil |Przemysł aparatu pojazdu |Generowany losowo numer z zakresu od 0 do 100 (wskazuje procentowy poziom ropa naftowa aparat) |
| Wykorzystanie opona |Wykorzystanie opona pojazdu |Generowany losowo numer z zakresu od 0 do 50 (wskazuje procentowy poziom wykorzystania opona) |
| Licznika |Odczyt licznika pojazdu |Generowanym losowo numerem z zakresu od 0 do 200 000 |
| Accelerator_pedal_position |Pozycja szkła akceleratora pojazdu |Generowany losowo numer z zakresu od 0 do 100 (wskazuje procentowy poziom akceleratora) |
| Parking_brake_status |Wskazuje, czy pojazdu są zaparkowane, czy nie |Wartość PRAWDA lub FAŁSZ |
| Headlamp_status |Wskazuje, czy jest reflektor na, czy nie |Wartość PRAWDA lub FAŁSZ |
| Brake_pedal_status |Wskazuje, czy pedału spowalnia jest wciśnięty lub nie |Wartość PRAWDA lub FAŁSZ |
| Transmission_gear_position |Pozycja koła zębatego transmisji pojazdu |Stany: po pierwsze, drugi, trzeci, czwarty, piąte, szósty, siódma, ósmą |
| Ignition_status |Wskazuje, czy pojazdu jest uruchomiona lub zatrzymana |Wartość PRAWDA lub FAŁSZ |
| Windshield_wiper_status |Wskazuje, czy Wycieraczka szyby jest ona włączona |Wartość PRAWDA lub FAŁSZ |
| ABS |Wskazuje, czy ABS jest włączone, czy nie |Wartość PRAWDA lub FAŁSZ |
| Znacznik czasu |Sygnatura czasowa, po utworzeniu punktu danych |Date |
| Miasto |Lokalizacja pojazdu |Cztery miast, w tym rozwiązaniu: Bellevue, Redmond, Sammamish, Seattle |

Zestawu danych referencyjnych w modelu pojazdu mapuje VINs modeli. 

| VIN | Modelowanie |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Limuzyna |
| 8J0U8XCPRGW4Z3NQE |Połączenie hybrydowe |
| WORG68Z2PLTNZDBI7 |Rodzina zamknięte |
| JTHMYHQTEPP4WBMRN |Limuzyna |
| W9FTHG27LZN1YWO0Y |Połączenie hybrydowe |
| MHTP9N792PHK08WJM |Rodzina zamknięte |
| EI4QXI2AXVQQING4I |Limuzyna |
| 5KKR2VB4WHQH97PF8 |Połączenie hybrydowe |
| W9NSZ423XZHAONYXB |Rodzina zamknięte |
| 26WJSGHX4MA5ROHNL |Przekonwertować |
| GHLUB6ONKMOSI7E77 |Kombi |
| 9C2RHVRVLMEJDBXLP |Samochód kompaktowy |
| BRNHVMZOUJ6EOCP32 |Małe SUV |
| VCYVW0WUZNBTM594J |Samochód dyscyplin sportowych |
| HNVCE6YFZSA5M82NY |Średnie SUV |
| 4R30FOR7NUOBL05GJ |Kombi |
| WYNIIY42VKV6OQS1J |Duże SUV |
| 8Y5QKG27QET1RBK7I |Duże SUV |
| DF6OX2WSRA6511BVG |Coupe |
| Z2EOZWZBXAEW3E60T |Limuzyna |
| M4TV6IEALD5QDS3IR |Połączenie hybrydowe |
| VHRA1Y2TGTA84F00H |Rodzina zamknięte |
| R0JAUHT1L1R3BIKI0 |Limuzyna |
| 9230C202Z60XX84AU |Połączenie hybrydowe |
| T8DNDN5UDCWL7M72H |Rodzina zamknięte |
| 4WPYRUZII5YV7YA42 |Limuzyna |
| D1ZVY26UV2BFGHZNO |Połączenie hybrydowe |
| XUF99EW9OIQOMV7Q7 |Rodzina zamknięte |
| 8OMCL3LGI7XNCC21U |Przekonwertować |
| ……. | |

## <a name="ingestion"></a>Pozyskiwanie
Połączenia usługi Azure Event Hubs, Azure Stream Analytics i Azure Data Factory służą do pozyskiwania sygnały pojazdu, zdarzenia diagnostyczne w czasie rzeczywistym i analiz wsadowych. Wszystkie te składniki są tworzone i konfigurowane jako część wdrożenia rozwiązania. 

### <a name="real-time-analysis"></a>Analiza w czasie rzeczywistym
Zdarzenia generowane przez symulator vehicle telematics są publikowane do Centrum zdarzeń za pomocą zestawu SDK Centrum zdarzeń.  

![Pulpit nawigacyjny Centrum zdarzeń](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

Zadanie usługi Stream Analytics, które pozyska te zdarzenia z Centrum zdarzeń i przetwarza dane w czasie rzeczywistym, aby przeanalizować kondycję pojazdów.

![Stream Analytics zadania przetwarzania danych](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


Zadanie usługi Stream Analytics:

* Pozwalają pozyskać dane z Centrum zdarzeń.
* Wykonuje sprzężenie z danymi referencyjnymi do mapowania pojazdu VIN odpowiedni model. 
* Są utrwalane w magazynie obiektów Blob platformy Azure do analizy wsadowej sformatowanego. 

Następujące zapytanie usługi Stream Analytics umożliwia utrwalanie danych do magazynu obiektów Blob: 

![Zapytanie dotyczące zadań Stream Analytics dla pozyskiwanie danych](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Analiza usługi Batch
Dodatkowe ilości sygnały symulowanego pojazdu i zestaw danych diagnostycznych zostanie również wygenerowany do pełniejszej analizy wsadowej. Ten dodatkowy wolumin jest wymagane, aby zapewnić dobrą reprezentatywne dane wolumin na potrzeby przetwarzania wsadowego. W tym celu PrepareSampleDataPipeline służy w przepływie pracy usługi Data Factory do generowania sygnałów symulowanego pojazdu i zestaw danych diagnostycznych pomagają w zrealizowaniu jeden rok. Aby pobrać fabrykę danych niestandardowe działanie platformy .NET rozwiązania Visual Studio dla dostosowania, w zależności od wymagań, przejdź do [niestandardowe działanie usługi Data Factory](https://go.microsoft.com/fwlink/?LinkId=717077) strony sieci Web. 

Ten przepływ pracy zawiera przykładowe dane przygotowane na potrzeby przetwarzania wsadowego.

![Przykładowe dane przygotowane na potrzeby przepływu pracy przetwarzania wsadowego](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


Potok składa się z niestandardowe działanie platformy .NET usługi fabryka danych.

![Działanie PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

Po potok wykonuje z powodzeniem, a zestaw danych RawCarEventsTable jest oznaczona jako "Gotowe", są produkowane jednego roku, przez które sygnały symulowanego pojazdu i danych diagnostycznych. Zostanie wyświetlony następujący folder i plik utworzony w ramach konta magazynu w ramach kontenera connectedcar:

![Dane wyjściowe PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>Partycja zestawu danych
W kroku przygotowania danych signals pierwotnych pojazdu lub częściową strukturą i zestaw danych diagnostycznych są podzielone na partycje do formatu roku i miesiąca. Ta partycjonowanie promuje bardziej efektywnego wykonywania zapytań i skalowalności magazynu długoterminowego przez włączenie trybu failover błędów. Na przykład jako pierwszego konta usługi blob się zapełni, jego błędów za pośrednictwem kontu dalej. 

>[!NOTE] 
>Ten krok w rozwiązaniu dotyczą tylko przetwarzania wsadowego.

Dane wejściowe i wyjściowe zarządzanie danymi:

* **Dane wyjściowe** (oznaczonych PartitionedCarEventsTable) są przechowywane przez długi czas jako podstawowe / "rawest" formularza danych w usłudze data lake przez klienta. 
* **Dane wejściowe** do tego potoku jest zazwyczaj odrzucona, ponieważ dane wyjściowe zawiera pełną wierność w danych wejściowych. Jest on przechowywany (partycjonowane) lepiej do późniejszego użytku.

Przepływ pracy zdarzenia samochodu partycji.

![Przepływ pracy zdarzenia samochodu partycji](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


Dane pierwotne jest podzielona na partycje za pomocą działania Hive usługi Azure HDInsight w PartitionCarEventsPipeline, jak pokazano na poniższym zrzucie ekranu. Przykładowe dane generowane przez rok w kroku przygotowania danych jest podzielona na partycje według roku i miesiąca. Partycje są używane do generowania sygnałów pojazdu i danych diagnostycznych w każdym miesiącu roku (łącznie z 12 partycjami). 

![Działanie PartitionCarEventsPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**Skrypt PartitionConnectedCarEvents Hive**

Partitioncarevents.hql skryptu Hive jest używana do partycjonowania. Znajduje się w folderze \demo\src\connectedcar\scripts pliku zip pobranego. 
    
    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string,
                YearNo                             int,
                MonthNo                         int) 
    ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

    INSERT OVERWRITE TABLE Stage_RawCarEvents
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        Year(gendate),
        Month(gendate)

    FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

    INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        YearNo,
        MonthNo
    FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

Po potok wykonuje z powodzeniem, zostaną wyświetlone następujące partycje, które są generowane na koncie magazynu w ramach kontenera connectedcar:

![Dane wyjściowe podzielonym na partycje](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

Teraz zoptymalizowaniu danych, łatwiejsze w obsłudze i gotowa do dalszego przetwarzania, aby uzyskać wgląd w rozbudowane usługi batch. 

## <a name="data-analysis"></a>Analiza danych
W tej sekcji zobaczysz, jak połączyć usługi Stream Analytics, Azure Machine Learning, Data Factory i HDInsight zapewniają rozbudowaną zaawansowanych analiz w kondycję pojazdów i nawyki nawyków.

### <a name="machine-learning"></a>Uczenie maszynowe
Celem jest przewidzieć pojazdów, które wymagają obsługi lub odwołania, w oparciu o niektórych statystyki kondycji, w oparciu o następujące założenia:

* Jeśli jeden z trzech następujących warunków jest spełniony, pojazdów wymagają obsługi konserwacji:
  
  * Wykorzystanie opona jest niskie.
  * Przemysł aparatu jest niska.
  * Temperatura aparat jest wysoki.

* Jeśli jeden z następujących warunków jest spełniony, pojazdy może mieć kwestii bezpieczeństwa i wymagają odwołania:
  
  * Temperatura aparat jest wysoka, ale brakuje temperatury zewnętrznej.
  * Temperatura aparat jest niska, ale temperatury zewnętrznej jest wysoka.

Dwa oddzielne modele oparte na poprzednie wymagania, i wykrywać anomalie. Jeden model jest pojazdu obsługi wykrywania i jeden model jest pojazdu wycofaniu wykrywania. W obu modelach do wykrywania anomalii jest używany algorytm analizy (UPW) wbudowanych składników podmiotu zabezpieczeń. 

#### <a name="maintenance-detection-model"></a>**Model wykrywania konserwacji**

Jeśli trzy wskaźników — wykorzystanie opona, przemysł aparat lub temperatury aparatu — spełnia jego odpowiedniego warunku, model wykrywania konserwacji raporty anomalii. W wyniku tych trzech zmiennych muszą należy wziąć pod uwagę podczas tworzenia modelu. Eksperyment w usłudze machine learning **Select Columns in Dataset** moduł jest używany do wyodrębniania tych trzech zmiennych. Następnie modułu wykrywania anomalii oparty na analizie PCA służy do tworzenia modelu wykrywania anomalii. 

UPW jest technikę ustanowionych w usłudze machine learning, który można zastosować do funkcji wyboru cech, klasyfikacji i wykrywanie anomalii. UPW Konwertuje zestaw przypadków, które zawierają zmienne prawdopodobnie skorelowany do zestawu wartości o nazwie głównych składników. Kluczowe oparty na analizie PCA modelowania będzie danych projektu na dolnym-wymiarowej, aby łatwiej zidentyfikować funkcje i anomalii.

Dla każdego nowe dane wejściowe do modelu wykrywania wykrywanie anomalii najpierw oblicza jego projekcji na eigenvectors. Oblicza błąd odbudowy znormalizowana. Ten błąd znormalizowane jest wyniku anomalii: im większa błąd, bardziej nietypowe wystąpienia. 

W konserwacji problem wykrywania każdy rekord jest uważany za ustalony punkt w przestrzeni trójwymiarowej przez wykorzystanie opona, aparat ropa naftowa i temperatury aparatu współrzędne. Aby przechwycić te anomalie, analizie PCA służy do projektu oryginalnych danych w przestrzeni trójwymiarowej na przestrzeni dwuwymiarowej. W związku z tym parametr liczbę składników do użycia w analizie PCA wynosi dwa. Ten parametr odgrywa ważną rolę w stosowaniu wykrywanie anomalii oparte na analizie PCA. Po analizie PCA przy użyciu danych projektu, te anomalie są identyfikowane łatwiejsze.

#### <a name="recall-anomaly-detection-model"></a>**Odwołaj model wykrywania anomalii**

W model wykrywania anomalii wycofaniu **Select Columns in Dataset** i modułów wykrywania anomalii oparty na analizie PCA są używane w podobny sposób. W szczególności trzy zmienne — aparat temperatury, poza temperatury szybkość — są wyodrębniane i za pomocą **Select Columns in Dataset** modułu. Zmienna szybkość również jest uwzględnione, ponieważ temperatury aparatu zazwyczaj skorelowany szybkości. Następnie modułu wykrywania anomalii oparty na analizie PCA służy do projektu danych z trójwymiarowej przestrzeni na przestrzeni dwuwymiarowej. Spełnione są kryteria odwołania. Pojazdu wymaga odwołania, gdy temperatura silnika i temperatury zewnętrznej bardzo negatywnie skorelowane. Po wykonaniu UPW algorytm wykrywania anomalii oparty na analizie PCA służące do przechwytywania anomalii. 

Szkolenie w obu modeli zwykłych danych jest używany jako dane wejściowe do nauczenia modelu wykrywania anomalii oparty na analizie PCA. (Zwykłych danych nie wymaga konserwacji lub odwołania). Eksperyment oceniania model wykrywania anomalii uczonego służy do wykrywania, czy pojazdu wymaga konserwacji lub wycofania. 

### <a name="real-time-analysis"></a>Analiza w czasie rzeczywistym
Następujące zapytanie SQL usługi Stream Analytics umożliwia uzyskiwanie średnią wszystkich parametrów ważny. Te parametry obejmują prędkość, poziom paliwa, temperatury aparatu, odczyt licznika, wykorzystanie opona, aparatu ropa naftowa i inne. Średnie są używane do wykrywania anomalii, wydawania alerty i określają warunki ogólną kondycję pojazdów obsługiwane w określonym regionie. Średnie następnie skorelowanych z danych demograficznych. 

![Stream Analytics zapytania do przetwarzania w czasie rzeczywistym](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Wszystkie wartości średnie są obliczane okna wirowania trzy sekundy. Okno wirowania jest stosowana, ponieważ wymaganych odstępach czasu nienakładające się ciągłych. 

Aby dowiedzieć się więcej na temat funkcji obsługi okien w usłudze Stream Analytics, zobacz [obsługi okien (usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

#### <a name="real-time-prediction"></a>**Przewidywanie w czasie rzeczywistym**

Aplikacja wchodzi w skład rozwiązania do obsługi operacji modelu usługi machine learning w czasie rzeczywistym. Aplikacja RealTimeDashboardApp zostało utworzone i skonfigurowane jako część wdrożenia rozwiązania. Aplikacja:

* Nasłuchuje wystąpieniem Centrum zdarzeń, gdzie usługi Stream Analytics publikuje zdarzenia we wzorcu stale.

    ![Stream Analytics query do publikowania danych](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Odbiera zdarzenia. Dla każdego zdarzenia, który odbiera tę aplikację: 
   
   * Dane są przetwarzane przy użyciu usługi machine learning odpowiedź na żądanie oceniania (RR) w punkcie końcowym. Punkt końcowy rekordy zasobów jest automatycznie publikowany w ramach wdrożenia.
   * Dane wyjściowe rekordy zasobów opublikowaniu zestawu danych usługi Power BI za pomocą instalacji wypychanej interfejsów API.

Ten wzorzec jest również odpowiednich do scenariuszy, w których chcesz zintegrować aplikację line-of-business z przepływem analizy w czasie rzeczywistym. Te scenariusze obejmują alerty, powiadomienia i komunikaty.

Uwaga: dane dla rozwiązania RealtimeDashboardApp Visual Studio jest już dostępna.

#### <a name="execute-the-real-time-dashboard-application"></a>**Uruchom aplikację pulpitu nawigacyjnego w czasie rzeczywistym**
1. Wyodrębnij RealtimeDashboardApp, a następnie zapisać je lokalnie.

    ![RealTimeDashboardApp folder](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Uruchom aplikację RealtimeDashboardApp.exe.

3. Wprowadź prawidłowe poświadczenia usługi Power BI, a następnie wybierz pozycję **Zaloguj**.  

    ![Okno logowania aplikacji pulpitu nawigacyjnego w czasie rzeczywistym](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Wybierz pozycję **Zaakceptuj**.

    ![Pulpit nawigacyjny w czasie rzeczywistym końcowego logowania okna aplikacji](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Jeśli chcesz opróżnić zestawu danych usługi Power BI, należy wykonać RealtimeDashboardApp z parametrem "flushdata —". 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Analiza usługi Batch
Celem jest, aby pokazać, jak Contoso Motors korzysta z możliwości obliczeniowych platformy Azure i korzystaj z danych big data. Te dane, co spowoduje wyświetlenie szczegółowych schematy, zachowania i kondycję pojazdów. Te informacje umożliwia:

* Poprawy jakości obsługi klienta i przypisz ją tańsze, zapewniając szczegółowe informacje na uzyskiwaniu nawyki i obniżające zużycie paliwa opracowuje zachowania.
* Zapoznaj się z wyprzedzeniem klientów oraz ich opracowuje wzorce, które określają sposób decyzje biznesowe i zapewniają najlepszą w swojej klasie produktów i usług.

W tym rozwiązaniu są stosowane następujące metryki:

* **Agresywne zachęcanie zachowanie**: identyfikuje trend modeli, lokalizacji, opracowuje warunki i czas tego roku, aby uzyskać wgląd w agresywne schematy. Silniki contoso umożliwia wglądów kampanii marketingowych wprowadzają nowe funkcje spersonalizowanych i ubezpieczenia na podstawie użycia.
* **Zachowanie opracowuje obniżające zużycie paliwa**: identyfikuje trend modeli, lokalizacji, opracowuje warunki i czas tego roku, aby uzyskać wgląd w obniżające zużycie paliwa schematy. Contoso Motors umożliwia wprowadzenie nowych funkcji i proaktywne zgłaszanie sterowniki ekonomiczne i przyjaznych dla środowiska nawyków opracowuje wglądowi w szczegółowe dane dla kampanii marketingowych.
* **Odwołaj modeli**: identyfikuje modeli, które wymagają odwołania przez operacjonalizacji eksperymentu uczenia maszynowego wykrywania anomalii.

Przyjrzyjmy się w szczegóły każdego z tych metryk.

#### <a name="aggressive-driving-behavior-patterns"></a>**Agresywne opracowuje wzorce zachowania**

Sygnały pojazdu podzielonym na partycje i dane diagnostyczne są przetwarzane w AggresiveDrivingPatternPipeline, jak pokazano w poniższym przepływie pracy. Gałąź jest używany do określenia modele, lokalizacji, pojazd, warunki pracy i innych parametrów, które wykazują agresywne schematy.

![Agresywne opracowuje przepływu pracy wzorzec](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Agresywne opracowuje zapytanie Hive wzorzec***

Aggresivedriving.hql skryptu Hive jest używany do analizowania agresywne schematy warunku. Znajduje się w folderze \demo\src\connectedcar\scripts pliku zip pobranego. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                   vin                         string, 
                model                        string,
                timestamp                    string,
                city                        string,
                speed                          string,
                transmission_gear_position    string,
                brake_pedal_status            string,
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



    INSERT OVERWRITE TABLE CarEventsAggresive
    select
    vin,
    model,
    timestamp,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'


Skrypt używa kombinacji pozycji koła zębatego transmisji pojazdu, stan szkła spowalnia i szybkość do wykrywania reckless/agresywne opracowuje zachowanie oparte na hamowania wzorców dużą prędkością. 

Po potok wykonuje z powodzeniem, zostaną wyświetlone następujące partycje, które są generowane na koncie magazynu w ramach kontenera connectedcar:

![Dane wyjściowe AggressiveDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Schematy obniżające zużycie paliwa zachowanie**

Sygnały pojazdu podzielonym na partycje i dane diagnostyczne są przetwarzane w FuelEfficientDrivingPatternPipeline, jak pokazano w poniższym przepływie pracy. Gałąź jest używany do określenia modele, lokalizacji, pojazd, warunki pracy i innych właściwości, które wykazują obniżające zużycie paliwa schematy.

![Schematy obniżające zużycie paliwa](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Zapytanie Hive wzorzec opracowuje obniżające zużycie paliwa***

Fuelefficientdriving.hql skryptu Hive jest używany do analizowania obniżające zużycie paliwa schematy warunku. Znajduje się w folderze \demo\src\connectedcar\scripts pliku zip pobranego. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                   vin                         string, 
                model                        string,
                   city                        string,
                speed                          string,
                transmission_gear_position    string,                
                brake_pedal_status            string,            
                accelerator_pedal_position    string,                             
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



    INSERT OVERWRITE TABLE FuelEfficientDriving
    select
    vin,
    model,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    accelerator_pedal_position,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


Skrypt używa kombinacji pozycji koło zębate transmisji vehicle, stan szkła hamulca, szybkości, dźwignia przyspieszenia wykryć obniżające zużycie paliwa działanie pobudzenie przyspieszenie hamowania i szybkości wzorce. 

Po potok wykonuje z powodzeniem, zostaną wyświetlone następujące partycje, które są generowane na koncie magazynu w ramach kontenera connectedcar:

![Dane wyjściowe FuelEfficientDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Odwołaj określane są przewidywania modelu**

Eksperymentu uczenia maszynowego są aprowizowane i publikowane jako usługi sieci web jako część wdrożenia rozwiązania. Wsadowe ocenianie punkt końcowy jest używany w tym przepływie pracy. Ma on zarejestrowany jako usługa połączona fabryka danych i przygotowany do działania przy użyciu data factory działanie wsadowego oceniania przez.

![Punkt końcowy elementu Machine learning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

Zarejestrowanej połączonej usługi jest używana w DetectAnomalyPipeline, który będzie oceniać dane przy użyciu modelu wykrywania anomalii. 

![Machine learning działanie wsadowego oceniania przez w usłudze data factory](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

Kilka kroków są wykonywane w tym potoku w celu przygotowania danych, dzięki czemu może być udało z wsadowego oceniania usługi sieci web. 

![DetectAnomalyPipeline do przewidywania odwołania](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Zapytanie Hive wykrywania anomalii***

Po zakończeniu oceny, działanie HDInsight przetwarza i agreguje dane, które modelu skategoryzowane jako anomalii. Model wykorzystuje wynik prawdopodobieństwa 0.60 lub nowszej.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                            string,
                model                        string,
                gendate                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                fuel                        string,
                engineoil                    string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position    string,
                parking_brake_status        string,
                headlamp_status                string,
                brake_pedal_status            string,
                transmission_gear_position    string,
                ignition_status                string,
                windshield_wiper_status        string,
                abs                          string,
                maintenanceLabel            string,
                maintenanceProbability        string,
                RecallLabel                    string,
                RecallProbability            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                            string,
                model                        string,
                city                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                Year                        string,
                Month                        string                

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

    INSERT OVERWRITE TABLE RecallModel
    select
    vin,
    model,
    city,
    outsidetemperature,
    enginetemperature,
    speed,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from CarEventsAnomaly
    where RecallLabel = '1' AND RecallProbability >= '0.60'


Po potok wykonuje z powodzeniem, zostaną wyświetlone następujące partycje, które są generowane na koncie magazynu w ramach kontenera connectedcar:

![Dane wyjściowe DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Publikowanie

### <a name="real-time-analysis"></a>Analiza w czasie rzeczywistym
Jednego z zapytań w ramach zadania usługi Stream Analytics publikuje zdarzenia wystąpieniem Centrum zdarzeń dane wyjściowe. 

![Zadanie usługi Stream Analytics publikowane wystąpieniem Centrum zdarzeń dane wyjściowe](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

Następujące zapytanie usługi Stream Analytics jest używany do publikowania wystąpieniem Centrum zdarzeń dane wyjściowe:

![Stream Analytics zapytanie, aby opublikować wystąpieniem Centrum zdarzeń dane wyjściowe](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Ten strumień zdarzeń jest używane przez RealTimeDashboardApp, który znajduje się w rozwiązaniu. Ta aplikacja używa odpowiedź na żądanie usługi sieci web do oceniania w czasie rzeczywistym uczenia maszynowego. Publikuje dane wynikowe do usługi Power BI zestawu danych do użycia. 

### <a name="batch-analysis"></a>Analiza usługi Batch
Wyniki usługi batch oraz przetwarzaniem w czasie rzeczywistym są publikowane w tabelach bazy danych SQL Azure do użycia. Program SQL server, bazy danych i tabele są tworzone automatycznie jako część skryptu instalacji. 

Wyniki przetwarzania wsadowego są kopiowane do przepływu pracy składnicy danych.

![Wyniki skopiowane do składnicy danych w przepływie pracy przetwarzania wsadowego](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

Zadanie usługi Stream Analytics została opublikowana do składnicy danych.

![Zadanie usługi Stream Analytics publikowane w składnicy danych](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

Ustawienie składnicy danych znajduje się w zadania usługi Stream Analytics.

![Ustawienie składnicy danych w ramach zadania usługi Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Używanie
Usługa Power BI wyposaża to rozwiązanie rozbudowany pulpit nawigacyjny dla danych w czasie rzeczywistym i wizualizacje analizy predykcyjnej. 

Końcowe pulpit nawigacyjny wygląda następująco:

![Pulpit nawigacyjny usługi Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Podsumowanie
Ten dokument zawiera szczegółowe szczegółów rozwiązania analizy Telemetrii pojazdów. Wzorzec architektury lambda jest używana do czasu rzeczywistego i wsadowych analizy przy użyciu prognozy i akcje. Ten wzorzec ma zastosowanie do szerokiej gamy zastosowań, które wymagają ścieżki aktywnej (w czasie rzeczywistym) i analiza ścieżki nieaktywnej (batch). 

### <a name="references"></a>Dokumentacja

* [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
* [Azure Data Factory](https://docs.microsoft.com/rest/api/datafactory/)
* [Zestaw Azure Event Hubs SDK dla pozyskiwania danych ze strumieni](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Możliwości przenoszenia danych w usłudze Azure Data Factory](../../data-factory/copy-activity-overview.md)
* [Usługa Azure działanie platformy .NET usługi fabryka danych](../../data-factory/transform-data-using-dotnet-custom-activity.md)
* [Usługa Azure działanie platformy .NET usługi fabryka danych rozwiązania Visual Studio używane do przygotowania danych przykładowych](https://go.microsoft.com/fwlink/?LinkId=717077) 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak skonfigurować raporty usługi Power BI i pulpity nawigacyjne dla tego rozwiązania, zobacz [instrukcje dotyczące konfiguracji pulpitu nawigacyjnego można przeprowadzić przy użyciu danych Telemetrycznych analizy rozwiązania szablonu usługi Power BI](cortana-analytics-playbook-vehicle-telemetry-powerbi.md).
