---
title: Nowości w sposób przewidzieć vehicle kondycji i zwiększa zwyczaje - Azure | Dokumentacja firmy Microsoft
description: Korzystanie z możliwości Cortana Intelligence, aby uzyskać wgląd w czasie rzeczywistym oraz predykcyjnej na vehicle kondycji i wspierającym zwyczaje.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: bradsev
ms.openlocfilehash: 370ab807ef85240238c51d1693796c26981edb15
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Vehicle rozwiązania analizy Telemetrii podręcznikowym: bezpośrednie Poznaj rozwiązania
To menu łączy do sekcji tego podręcznika dotyczącego: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Ten dokument rozwija na każdym z etapów przedstawiono architekturę rozwiązania. Instrukcje i wskaźniki do dostosowania są uwzględniane. 

## <a name="data-sources"></a>Źródła danych
Rozwiązanie używa dwóch różnych źródeł danych:

* Sygnały symulowane vehicle i zestaw danych diagnostycznych
* Vehicle katalogu

Symulator telematyki vehicle wchodzi w skład tego rozwiązania, jak pokazano na poniższym zrzucie ekranu. Emituje go sygnałów, które odpowiadać stan mechanizm i kierowania wzorca w danym punkcie w czasie i informacji diagnostycznych. Aby pobrać Vehicle telematyki symulatora rozwiązania Visual Studio dla dostosowania, w zależności od wymagań, przejdź do [Vehicle telematyki symulatora](http://go.microsoft.com/fwlink/?LinkId=717075) strony sieci Web. Katalog vehicle zawiera zestaw danych odwołania mapowanego vehicle numery identyfikacyjne (VINs) modeli.

![Symulator telematyki vehicle](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


Ten zestaw danych formacie JSON zawiera następujące schematu.

| Kolumna | Opis | Wartości |
| --- | --- | --- |
| VIN |Losowo generowany VIN |Uzyskane z głównym listę 10 000 VINs losowo wygenerowany |
| Poza temperatury |Gdzie sterujące mechanizm temperatury na zewnątrz |Losowo generowany numer od 0 do 100 |
| Aparat temperatury |Aparat temperatury mechanizm |Losowo generowany numer od 0 do 500 |
| Szybkość |Szybkość aparatu, jaką mechanizm sterujące |Losowo generowany numer od 0 do 100 |
| Paliwa |Poziom paliwa mechanizm |Losowo generowany numer od 0 do 100 (procent poziomu paliwa wskazuje) |
| EngineOil |Poziom wydobycie ropy naftowej aparat mechanizm |Losowo generowany numer od 0 do 100 (procent poziomu wydobycie ropy naftowej aparat oznacza) |
| Opona wykorzystania |Wykorzystanie opona pojazdów. |Losowo generowany numer od 0 do 50 (wskazuje procent poziomu wykorzystania opona) |
| Licznika |Odczytywanie licznika mechanizm |Losowo generowany numer od 0 do 200 000 |
| Accelerator_pedal_position |Pozycja szkła mechanizm klawiszy skrótów |Losowo generowany numer od 0 do 100 (procent poziomu akceleratora wskazuje) |
| Parking_brake_status |Wskazuje, czy mechanizm jest stoi lub nie |Wartość PRAWDA lub FAŁSZ |
| Headlamp_status |Wskazuje, czy jest nim na, lub nie |Wartość PRAWDA lub FAŁSZ |
| Brake_pedal_status |Wskazuje, czy pedału hamulca zostanie naciśnięty, czy nie |Wartość PRAWDA lub FAŁSZ |
| Transmission_gear_position |Pozycja koło zębate transmisji mechanizm |Stan: najpierw drugie, trzecie czwarty, piąte, szóstego, siódme, kolejnego, ósmego |
| Ignition_status |Wskazuje, czy mechanizm jest uruchomiona lub zatrzymana |Wartość PRAWDA lub FAŁSZ |
| Windshield_wiper_status |Wskazuje, czy Wycieraczka szyby jest włączona lub nie |Wartość PRAWDA lub FAŁSZ |
| ABS |Wskazuje, czy ABS prowadzi lub nie |Wartość PRAWDA lub FAŁSZ |
| Sygnatura czasowa |Sygnatura czasowa po utworzeniu punktu danych. |Date |
| Miasto |Lokalizacja mechanizm |Cztery miejscowości, w tym rozwiązaniu: Bellevue, Redmond, Sammamish, Seattle |

Zestaw danych odwołania modelu vehicle mapuje VINs modeli. 

| VIN | Model |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Limuzyna |
| 8J0U8XCPRGW4Z3NQE |Połączenie hybrydowe |
| WORG68Z2PLTNZDBI7 |Rodziny zamknięte |
| JTHMYHQTEPP4WBMRN |Limuzyna |
| W9FTHG27LZN1YWO0Y |Połączenie hybrydowe |
| MHTP9N792PHK08WJM |Rodziny zamknięte |
| EI4QXI2AXVQQING4I |Limuzyna |
| 5KKR2VB4WHQH97PF8 |Połączenie hybrydowe |
| W9NSZ423XZHAONYXB |Rodziny zamknięte |
| 26WJSGHX4MA5ROHNL |Możliwe do przekonwertowania |
| GHLUB6ONKMOSI7E77 |Kombi |
| 9C2RHVRVLMEJDBXLP |Samochód kompaktowy |
| BRNHVMZOUJ6EOCP32 |Mała SUV |
| VCYVW0WUZNBTM594J |Samochodu sportowych |
| HNVCE6YFZSA5M82NY |Średnia SUV |
| 4R30FOR7NUOBL05GJ |Kombi |
| WYNIIY42VKV6OQS1J |Duże SUV |
| 8Y5QKG27QET1RBK7I |Duże SUV |
| DF6OX2WSRA6511BVG |Coupe |
| Z2EOZWZBXAEW3E60T |Limuzyna |
| M4TV6IEALD5QDS3IR |Połączenie hybrydowe |
| VHRA1Y2TGTA84F00H |Rodziny zamknięte |
| R0JAUHT1L1R3BIKI0 |Limuzyna |
| 9230C202Z60XX84AU |Połączenie hybrydowe |
| T8DNDN5UDCWL7M72H |Rodziny zamknięte |
| 4WPYRUZII5YV7YA42 |Limuzyna |
| D1ZVY26UV2BFGHZNO |Połączenie hybrydowe |
| XUF99EW9OIQOMV7Q7 |Rodziny zamknięte |
| 8OMCL3LGI7XNCC21U |Możliwe do przekonwertowania |
| ……. | |

## <a name="ingestion"></a>Wprowadzanie
Kombinacje Azure Event Hubs, analiza strumienia Azure i fabryki danych Azure są używane do pozyskiwania sygnały vehicle, zdarzeń diagnostycznych i w czasie rzeczywistym i partii analytics. Wszystkie te składniki są tworzone i skonfigurowany jako część wdrożenia rozwiązania. 

### <a name="real-time-analysis"></a>Analiza w czasie rzeczywistym
Zdarzenia generowane przez symulator telematyki vehicle są publikowane do Centrum zdarzeń za pomocą Centrum zdarzeń zestawu SDK.  

![Pulpit nawigacyjny Centrum zdarzeń](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

Zadanie Stream Analytics wysyła strumień te zdarzenia z Centrum zdarzeń i przetwarza dane w czasie rzeczywistym do analizy kondycji pojazdów.

![Dane przetwarzania zadania usługi analiza strumienia](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


Zadanie Stream Analytics:

* Wysyła strumień danych z Centrum zdarzeń.
* Wykonuje sprzężenie z mapowania vehicle VIN na odpowiedni model danych źródłowych. 
* Są utrwalane w magazynie obiektów Blob platformy Azure dla analizach wsadowych sformatowanego. 

Następujące zapytanie Stream Analytics jest użytą do utrwalenia danych do magazynu obiektów Blob: 

![Zapytanie dotyczące zadań analizy strumienia dla wprowadzanie danych](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Analiza partii
Dodatkowe ilości sygnały symulowane vehicle i zestaw danych diagnostycznych zostanie również wygenerowany dla bardziej zaawansowane funkcje analizy partii. To dodatkowe woluminu jest wymagany do zapewnienia wolumin danych reprezentatywnych dla przetwarzania wsadowego. W tym celu PrepareSampleDataPipeline jest używany w przepływie pracy fabryki danych do generowania wartość jednego roku sygnały symulowane vehicle i zestaw danych diagnostycznych. Aby pobrać fabryki danych niestandardowych działań .NET rozwiązania Visual Studio dla dostosowania, w zależności od wymagań, przejdź do [działania niestandardowego fabryki danych](http://go.microsoft.com/fwlink/?LinkId=717077) strony sieci Web. 

Ten przepływ pracy zawiera przykładowe dane, gotowy do przetwarzania wsadowego.

![Przykładowe dane przygotowane do przepływu pracy przetwarzania wsadowego](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


Potok składa się z działań niestandardowych .NET fabryki danych.

![Działanie PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

Po potoku wykonuje pomyślnie i RawCarEventsTable zestawu danych jest oznaczona jako "Gotowe", wartość jednego roku sygnały symulowane vehicle i danych diagnostycznych są tworzone. Zostanie wyświetlony następujący folder i plik utworzony na koncie magazynu w kontenerze connectedcar:

![Dane wyjściowe PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>Partycji w zestawie danych
W kroku przygotowania danych sygnały raw vehicle częściową strukturą i zestaw danych diagnostycznych są dzielone na format rok i miesiąc. Zamienia kwerend większą wydajność i skalowalność magazynu długoterminowego przez włączenie błędów w tryb failover tej partycji. Na przykład jako pierwsze konto blob się zapełni, jego błędów za pośrednictwem do następnego konta. 

>[!NOTE] 
>Ten krok w rozwiązaniu dotyczą tylko przetwarzania wsadowego.

Wejście i wyjście zarządzanie danymi:

* **Dane wyjściowe** (etykietą PartitionedCarEventsTable) jest zachowywana przez dłuższy czas jako podstawowych / "rawest" formularza danych w usłudze data lake klienta. 
* **Dane wejściowe** do tego potoku jest zwykle odrzucona, ponieważ danych wyjściowych ma pełnej rozdzielczości w danych wejściowych. Jest on przechowywany (podzielona na partycje) lepiej dla dalszego wykorzystania.

Przepływ pracy zdarzenia samochodu partycji.

![Przepływ pracy zdarzenia samochodu partycji](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


Dane pierwotne jest podzielona na partycje za pomocą działania usługi Azure HDInsight Hive w PartitionCarEventsPipeline, jak pokazano na poniższym zrzucie ekranu. Przykładowe dane generowane przez rok w kroku przygotowania danych jest podzielona na partycje przez rok i miesiąc. Partycje są używane do generowania sygnałów vehicle i danych diagnostycznych dla każdego miesiąca roku (łącznie partycje 12). 

![Działanie PartitionCarEventsPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**Skryptu PartitionConnectedCarEvents Hive**

Partitioncarevents.hql skryptu Hive jest używany do partycjonowania. Znajduje się w folderze \demo\src\connectedcar\scripts pobranego pliku zip. 
    
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

Po potoku została wykonana pomyślnie, zostaną wyświetlone następujące partycje, które są generowane na koncie magazynu w kontenerze connectedcar:

![Dane wyjściowe podzielonym na partycje](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

Teraz zoptymalizowaniu danych, łatwiejsze w zarządzaniu i gotowa do dalszego przetwarzania, aby uzyskać wgląd w partii sformatowanego. 

## <a name="data-analysis"></a>Analiza danych
W tej sekcji zobacz temat jak połączyć usługę Stream Analytics, usługi Azure Machine Learning fabryki danych i HDInsight dla zaawansowanej, zaawansowane analizy na vehicle kondycji i wspierającym zwyczaje.

### <a name="machine-learning"></a>Uczenie maszynowe
Celem jest przewidywanie pojazdów, które wymagają konserwacji lub wycofania oparte na niektórych statystyki kondycji, oparte na następujących założeniach:

* Jeśli spełniony jest jeden z trzech następujących warunków, pojazdów wymagają obsługi konserwacji:
  
  * Wykorzystanie opona jest niska.
  * Poziom wydobycie ropy naftowej aparat jest niski.
  * Temperatury aparat jest wysoki.

* Jeśli spełniony jest jeden z następujących warunków, pojazdów może mieć problemu bezpieczeństwa i wymagają odwołania:
  
  * Temperatury aparat jest wysoka, ale brakuje temperatury na zewnątrz.
  * Temperatury aparat jest niska, ale poza temperatury jest wysoka.

Na podstawie poprzedniego wymagań, dwa oddzielne modele wykrywania anomalii. Jeden model jest vehicle obsługi wykrywania i jeden model jest wykrywania odwołania pojazdów. W obu modelach wbudowany składnik główny algorytm analizy (PCA) służy do wykrywania anomalii. 

#### <a name="maintenance-detection-model"></a>**Model wykrywania konserwacji**

Jeśli jeden trzy wskaźników — opona wykorzystania, aparat wydobycie ropy naftowej lub temperatury aparat — spełnia stanu odpowiednich, model wykrywania konserwacji raporty anomalii. Te trzy zmienne konieczne w związku z tym należy wziąć pod uwagę podczas konstruowania modelu. W eksperymencie w uczeniu maszynowym **Select Columns in Dataset** moduł jest używany do wyodrębnienia tych trzech zmiennych. Następnie moduł wykrywania anomalii oparte na PCA jest używany do tworzenia modelu wykrywania anomalii. 

PCA jest technikę ustalonych w uczeniu maszynowym, który można zastosować funkcji wyboru cech, klasyfikacji i wykrywania anomalii. PCA Konwertuje zestaw przypadków zawierające prawdopodobnie skorelowane zmienne do zestawu wartości o nazwie główne składniki. Koncepcja klucza na podstawie PCA modelowania jest danych projektu na wymiarów dolna miejsca, aby łatwiej zidentyfikować funkcje i anomalie.

Dla każdego nowego danych wejściowych do modelu wykrywania wykrywanie anomalii najpierw oblicza swojej projekcji na eigenvectors. Następnie oblicza błąd znormalizowane odbudowy. Ten błąd znormalizowane jest wynik anomalii: im większa błąd, więcej nietypowe wystąpienia. 

Problem wykrywania konserwacji każdy rekord jest uznawany za punkt w przestrzeni trójwymiarowej zdefiniowany przez opona wykorzystania, aparat wydobycie ropy naftowej i temperatury aparat współrzędnych. Aby przechwytywać anomalie te, PCA służy do projektu na obszar dwuwymiarowa oryginalnych danych w przestrzeni trójwymiarowej. W związku z tym liczba parametrów składników do użycia w PCA wynosi dwa. Ten parametr odgrywa ważną rolę w stosowaniu wykrywania anomalii oparte na PCA. Po użyciu PCA danych projektu, łatwiej są identyfikowane tych nieprawidłowości.

#### <a name="recall-anomaly-detection-model"></a>**Odwołaj model wykrywania anomalii**

W modelu wykrywania anomalii odwołania **Select Columns in Dataset** i moduły wykrywania anomalii oparte na PCA są używane w podobny sposób. W szczególności trzy zmienne — aparat temperatury, poza temperatury i szybkość — są wyodrębniane za pomocą **Select Columns in Dataset** modułu. Zmienna szybkość również jest uwzględnione, ponieważ temperatury aparat zwykle są powiązane z szybkości. Następnie modułu wykrywania anomalii oparte na PCA służy do projektu na dwuwymiarowa obszar danych w przestrzeni trójwymiarowej. Kryteria odwołania są spełnione. Mechanizm wymaga odwołania, gdy aparat temperatury i poza temperatury wysokiej negatywnie skorelowane. Po wykonaniu PCA algorytm wykrywania anomalii oparte na PCA służy do przechwytywania nieprawidłowości. 

Podczas uczenia modelu albo, normalne danych jest używany jako dane wejściowe do uczenia modelu wykrywania anomalii oparte na PCA. (Dane normalne nie wymagają konserwacji lub wycofania). W eksperymencie oceniania wykrywania anomalii uczonego modelu jest używana do wykrywania, czy mechanizm wymaga konserwacji lub wycofania. 

### <a name="real-time-analysis"></a>Analiza w czasie rzeczywistym
Następujące zapytanie SQL usługi analiza strumienia jest używany do pobierania średnią wszystkich parametrów ważny. Te parametry obejmują prędkość, poziom paliwa temperatury aparatu, odczytu licznika, opona wykorzystania, aparatu wydobycie ropy naftowej i inne. Średnie są używane do wykrywania anomalii, wystawiać alertów i określają warunki ogólnej kondycji pojazdów obsługiwane w określonym regionie. Następnie skorelowanych średnich z demograficznych. 

![Stream Analytics zapytania do przetworzenia w czasie rzeczywistym](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Wszystkie wartości średnie są obliczane przez okno wirowania trzy sekundy. Okno wirowania jest stosowana, ponieważ przedziały czasu nienakładający, jak i ciągłe są wymagane. 

Aby dowiedzieć się więcej o możliwościach okien w Stream Analytics, zobacz [okien (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

#### <a name="real-time-prediction"></a>**Prognozowanie w czasie rzeczywistym**

Aplikacja jest zawarte w ramach rozwiązania, który umożliwia operacjonalizację modelu uczenia maszynowego w czasie rzeczywistym. Aplikacja RealTimeDashboardApp jest utworzony i skonfigurowany jako część wdrożenia rozwiązania. Aplikacja:

* Wykrywa, do których analiza strumienia publikuje zdarzenia we wzorcu stale wystąpienia Centrum zdarzeń.

    ![Stream Analytics zapytania do publikowania danych](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Odbiera zdarzenia. Dla każdego zdarzenia, które otrzymuje tej aplikacji: 
   
   * Dane są przetwarzane przy użyciu punktu końcowego (RR) oceniania żądań i odpowiedzi uczenia maszynowego. Punkt końcowy rekordy zasobów są automatycznie publikowane jako część wdrożenia.
   * Dane wyjściowe rekordy zasobów jest publikowany w zestawie danych usługi Power BI za pomocą wypychania interfejsów API.

Ten wzorzec jest również dotyczy scenariuszy, w których chcesz zintegrować aplikacji biznesowych z z przepływem analiz w czasie rzeczywistym. Te scenariusze obejmują alertów, powiadomień i wiadomości.

Aby pobrać rozwiązanie RealtimeDashboardApp Visual Studio dostosowań, zobacz [pobierania RealtimeDashboardApp](http://go.microsoft.com/fwlink/?LinkId=717078) strony sieci Web. 

#### <a name="execute-the-real-time-dashboard-application"></a>**Uruchom aplikację w czasie rzeczywistym pulpitu nawigacyjnego**
1. Wyodrębnij RealtimeDashboardApp i zapisać ją lokalnie.

    ![RealTimeDashboardApp folder](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Uruchom aplikację RealtimeDashboardApp.exe.

3. Wprowadź prawidłowe poświadczenia usługi Power BI, a następnie wybierz **Zaloguj**.  

    ![Okna logowania w aplikacji w czasie rzeczywistym pulpitu nawigacyjnego](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Wybierz **zaakceptować**.

    ![Pulpit nawigacyjny w czasie rzeczywistym końcowego logowania okna aplikacji](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Jeśli chcesz opróżnić w zestawie danych usługi Power BI, należy wykonać RealtimeDashboardApp z parametrem "flushdata". 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Analiza partii
Celem jest pokazanie, jak silniki Contoso korzysta z możliwości obliczeń platformy Azure o danych big data. Te dane ujawnia szczegółowe, bogate informacje na schematy, zachowanie użycia i kondycji pojazdów. Tych informacji umożliwia:

* Poprawy obsługi klienta i zapewnić ich tańsze zapewniając wgląd na zwyczaje i obniżające zużycie paliwa pobudzenie zachowania.
* Zapoznaj się z wyprzedzeniem klientów i ich schematy reguluje decyzje biznesowe oraz zapewnić najlepsze w klasie produktów i usług.

W tym rozwiązaniu są przeznaczone dla następujących metryk:

* **Agresywna zwiększają zachowanie**: identyfikuje trend modeli, lokalizacje pobudzenie warunki i czas roku, aby uzyskać wgląd w bliskiej schematy. Silniki contoso można użyć tych insights kampanii marketingowych wprowadzenie nowych funkcji spersonalizowane i ubezpieczenia opartej na użyciu.
* **Zachowanie pobudzenie obniżające zużycie paliwa**: identyfikuje trend modeli, lokalizacje pobudzenie warunki i czas roku, aby uzyskać wgląd w obniżające zużycie paliwa schematy. Silniki contoso można użyć tych insights dla kampanii marketingowych wprowadzenie nowych funkcji i aktywne raportowania sterowniki zwyczaje pobudzenie ekonomiczne i przyjazny dla środowiska.
* **Odwołaj modele**: identyfikuje modeli, które wymagają odwołań przez operacyjnych eksperymentu uczenia maszynowego wykrywania anomalii.

Oto do szczegółów każdego z tych metryk.

#### <a name="aggressive-driving-behavior-patterns"></a>**Agresywne schematy zachowanie**

Sygnały vehicle podzielonym na partycje i danych diagnostycznych są przetwarzane w AggresiveDrivingPatternPipeline, jak pokazano w poniższym przepływie pracy. Gałąź jest używany do określenia modele, lokalizacji vehicle, warunki jazdy i innych parametrów, które wykazują agresywne schematy.

![Agresywne pobudzenie przepływu pracy wzorca](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Agresywne kierowania zapytań Hive wzorca***

Aggresivedriving.hql skryptu Hive jest używany do analizowania agresywne schematy warunku. Znajduje się w folderze \demo\src\connectedcar\scripts pobranego pliku zip. 

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


Skrypt używa kombinacji pozycji koło zębate transmisji vehicle, stan szkła hamulca i szybkość do wykrywania reckless/agresywne działanie pobudzenie hamowania wzorce dużą prędkością. 

Po potoku została wykonana pomyślnie, zostaną wyświetlone następujące partycje, które są generowane na koncie magazynu w kontenerze connectedcar:

![Dane wyjściowe AggressiveDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Schematy obniżające zużycie paliwa zachowanie**

Sygnały vehicle podzielonym na partycje i danych diagnostycznych są przetwarzane w FuelEfficientDrivingPatternPipeline, jak pokazano w poniższym przepływie pracy. Gałąź jest używany do określenia modele, lokalizacji, vehicle, warunki jazdy i inne właściwości, które wykazują obniżające zużycie paliwa schematy.

![Schematy obniżające zużycie paliwa](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Obniżające zużycie paliwa kierowania zapytań Hive wzorca***

Fuelefficientdriving.hql skryptu Hive jest używany do analizowania obniżające zużycie paliwa schematy warunku. Znajduje się w folderze \demo\src\connectedcar\scripts pobranego pliku zip. 

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

Po potoku została wykonana pomyślnie, zostaną wyświetlone następujące partycje, które są generowane na koncie magazynu w kontenerze connectedcar:

![Dane wyjściowe FuelEfficientDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Odwołania do modelu prognozy**

Eksperymentu uczenia maszynowego jest udostępniane i opublikowane jako usługi sieci web jako część wdrożenia rozwiązania. Wsadowego oceniania punktu końcowego jest używana w tym przepływie pracy. Ma ona zarejestrowana jako danych połączonej usługi fabryki i operationalized za pomocą działania oceniania partii fabryki danych.

![Punkt końcowy Machine learning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

Zarejestrowanej połączonej usługi jest używany w DetectAnomalyPipeline punkty danych przy użyciu modelu wykrywania anomalii. 

![Machine learning działanie wsadowego oceniania z fabryki danych](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

Kilka kroków są wykonywane w tym potoku w celu przygotowania danych, dzięki czemu mogą być operationalized z wsadowego oceniania usługi sieci web. 

![DetectAnomalyPipeline do przewidywania odwołania](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Zapytanie Hive wykrywania anomalii***

Po zakończeniu oceny działania HDInsight przetwarza i agregują dane, które modelu skategoryzowane jako anomalii. Model używa wynik prawdopodobieństwa 0.60 lub nowszej.

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


Po potoku została wykonana pomyślnie, zostaną wyświetlone następujące partycje, które są generowane na koncie magazynu w kontenerze connectedcar:

![Dane wyjściowe DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Publikowanie

### <a name="real-time-analysis"></a>Analiza w czasie rzeczywistym
Jeden z zapytania w zadaniu Stream Analytics publikuje zdarzenia do wystąpienia danych wyjściowych Centrum zdarzeń. 

![Zadania usługi analiza strumienia opublikowane dane wyjściowe wystąpienia Centrum zdarzeń](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

Następujące zapytanie Stream Analytics jest używane do publikowania danych wyjściowych wystąpienie Centrum zdarzeń:

![Stream Analytics zapytania do publikowania danych wyjściowych wystąpienie Centrum zdarzeń](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Ten strumień zdarzeń jest używany przez RealTimeDashboardApp, który jest zawarty w rozwiązaniu. Ta aplikacja używa Usługa sieci web żądań i odpowiedzi w czasie rzeczywistym oceniania uczenia maszynowego. Powoduje ona publikowanie wynikowe dane z zestawem danych usługi Power BI zużycia. 

### <a name="batch-analysis"></a>Analiza partii
Wyniki partii i przetwarzanie w czasie rzeczywistym są publikowane w tabelach bazy danych SQL Azure zużycia. SQL server, bazy danych i tabele są tworzone automatycznie w ramach skrypt instalacyjny. 

Wyniki przetwarzania wsadowego są kopiowane do przepływu pracy składnicy danych.

![Wyniki skopiowane do składnicy danych w przepływie pracy przetwarzania wsadowego](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

Zadanie Stream Analytics została opublikowana do składnicy danych.

![Zadania usługi analiza strumienia opublikowane do składnicy danych](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

Ustawienie składnicy danych jest w zadaniu Stream Analytics.

![Ustawienie składnicy danych w zadaniu Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Używanie
Usługi Power BI pozwala to rozwiązanie sformatowanego pulpitu nawigacyjnego i analizy predykcyjnej wizualizacji danych w czasie rzeczywistym. 

Końcowego pulpitu nawigacyjnego wygląda następująco:

![Pulpit nawigacyjny programu Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Podsumowanie
Ten dokument zawiera szczegółowe przechodzenia z rozwiązania analizy Telemetrii pojazdów. Wzorzec architektury lambda jest używany dla w czasie rzeczywistym i partii analizy w usłudze prognoz i akcji. Ten wzorzec dotyczy szeroką gamę przypadków użycia, które wymagają aktywnej ścieżki (w czasie rzeczywistym) i analiza zimnych ścieżki (partii). 

### <a name="references"></a>Dokumentacja

* [Rozwiązanie programu Visual Studio symulatora telematyki vehicle](http://go.microsoft.com/fwlink/?LinkId=717075) 
* [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
* [Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [Azure SDK centra zdarzeń pozyskiwania strumieni](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Możliwości przenoszenia danych fabryki danych platformy Azure](../../data-factory/v1/data-factory-data-movement-activities.md)
* [Działanie .NET fabryki danych Azure](../../data-factory/v1/data-factory-use-custom-activities.md)
* [Działanie .NET fabryki danych Azure rozwiązania Visual Studio umożliwia przygotowanie przykładowe dane](http://go.microsoft.com/fwlink/?LinkId=717077) 
