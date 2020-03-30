---
title: Zbieranie i analizowanie liczników wydajności w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Liczniki wydajności są zbierane przez usługę Azure Monitor w celu analizowania wydajności agentów systemu Windows i Linux.  W tym artykule opisano sposób konfigurowania kolekcji liczników wydajności dla agentów systemu Windows i Systemu Linux, szczegóły są przechowywane w obszarze roboczym i jak je analizować w witrynie Azure portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: d1a972a1d89066b961f2dcc28fba830e3a04ebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274764"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Źródła danych wydajności systemu Windows i Linux w usłudze Azure Monitor
Liczniki wydajności w systemach Windows i Linux zapewniają wgląd w wydajność składników sprzętowych, systemów operacyjnych i aplikacji.  Usługa Azure Monitor może zbierać liczniki wydajności w częstych odstępach czasu analizy w pobliżu czasu rzeczywistego (NRT) oprócz agregowania danych o wydajności w celu długoterminowej analizy i raportowania.

![Liczniki wydajności](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurowanie liczników wydajności
Konfigurowanie liczników wydajności z [menu Dane w obszarze Ustawienia zaawansowane](agent-data-sources.md#configuring-data-sources).

Podczas pierwszej konfiguracji liczników wydajności systemu Windows lub Linux dla nowego obszaru roboczego, masz możliwość szybkiego utworzenia kilku wspólnych liczników.  Na liście obok każdego z nich znajduje się pole wyboru.  Upewnij się, że wszystkie liczniki, które chcesz początkowo utworzyć, są sprawdzane, a następnie kliknij przycisk **Dodaj wybrane liczniki wydajności**.

W przypadku liczników wydajności systemu Windows można wybrać określone wystąpienie dla każdego licznika wydajności. W przypadku liczników wydajności systemu Linux wystąpienie każdego wybranego licznika ma zastosowanie do wszystkich liczników podrzędnych licznika nadrzędnego. W poniższej tabeli przedstawiono typowe wystąpienia dostępne dla liczników wydajności systemu Linux i Windows.

| Nazwa wystąpienia | Opis |
| --- | --- |
| \_Łącznie |Suma wszystkich wystąpień |
| \* |Wszystkie wystąpienia |
| (/&#124;/var) |Dopasowuje wystąpienia o nazwie: / lub /var |

### <a name="windows-performance-counters"></a>Liczniki wydajności systemu Windows

![Konfigurowanie liczników wydajności systemu Windows](media/data-sources-performance-counters/configure-windows.png)

Wykonaj tę procedurę, aby dodać nowy licznik wydajności systemu Windows do zbierania.

1. Wpisz nazwę licznika w polu tekstowym w formacie *obiektu(instancja)\licznika*.  Po rozpoczęciu pisania zostanie wyświetlona lista odpowiadających ci typowych liczników.  Możesz wybrać licznik z listy lub wpisać jeden z własnych.  Można również zwrócić wszystkie wystąpienia dla określonego licznika, określając *object\counter*.  

    Podczas zbierania liczników wydajności programu SQL Server z nazwanych wystąpień wszystkie liczniki nazwanych wystąpień zaczynają się od *MSSQL$* i następują po nazwie wystąpienia.  Na przykład, aby zebrać licznik współczynnik trafień w pamięci dziennika dla wszystkich baz `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`danych z obiektu wydajności Baza danych dla nazwanego wystąpienia SQL INST2, określ .

2. Kliknij **+** lub naciśnij **klawisz Enter,** aby dodać licznik do listy.
3. Po dodaniu licznika używa domyślnego 10 sekund dla jego **interwału próbkowania**.  Można to zmienić na wyższą wartość do 1800 sekund (30 minut), jeśli chcesz zmniejszyć wymagania dotyczące magazynu zebranych danych wydajności.
4. Po zakończeniu dodawania liczników kliknij przycisk **Zapisz** u góry ekranu, aby zapisać konfigurację.

### <a name="linux-performance-counters"></a>Liczniki wydajności systemu Linux

![Konfigurowanie liczników wydajności systemu Linux](media/data-sources-performance-counters/configure-linux.png)

Wykonaj tę procedurę, aby dodać nowy licznik wydajności systemu Linux do zbierania.

1. Domyślnie wszystkie zmiany konfiguracji są automatycznie wypychane do wszystkich agentów.  W przypadku agentów systemu Linux plik konfiguracyjny jest wysyłany do modułu zbierającego dane Fluentd.  Jeśli chcesz ręcznie zmodyfikować ten plik na każdym agencie Systemu Linux, odznacz pole *Zastosuj poniższą konfigurację do moich komputerów z systemem Linux* i postępuj zgodnie z poniższymi wskazówkami.
2. Wpisz nazwę licznika w polu tekstowym w formacie *obiektu(instancja)\licznika*.  Po rozpoczęciu pisania zostanie wyświetlona lista odpowiadających ci typowych liczników.  Możesz wybrać licznik z listy lub wpisać jeden z własnych.  
3. Kliknij **+** lub naciśnij **klawisz Enter,** aby dodać licznik do listy innych liczników obiektu.
4. Wszystkie liczniki dla obiektu używają tego samego **interwału próbkowania**.  Wartość domyślna to 10 sekund.  Można zmienić tę wartość do 1800 sekund (30 minut), jeśli chcesz zmniejszyć wymagania dotyczące magazynu zebranych danych wydajności.
5. Po zakończeniu dodawania liczników kliknij przycisk **Zapisz** u góry ekranu, aby zapisać konfigurację.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Konfigurowanie liczników wydajności systemu Linux w pliku konfiguracyjnym
Zamiast konfigurowania liczników wydajności systemu Linux przy użyciu witryny Azure portal, masz możliwość edytowania plików konfiguracyjnych w agencie systemu Linux.  Dane dotyczące wydajności do zebrania są kontrolowane przez konfigurację w **/etc/opt/microsoft/omsagent/\<workspace id\>/conf/omsagent.conf.**

Każdy obiekt lub kategoria metryki wydajności do zbierania powinny być zdefiniowane w pliku konfiguracji jako pojedynczy `<source>` element. Składnia jest zgodna ze wzorcem poniżej.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Parametry w tym elemencie są opisane w poniższej tabeli.

| Parametry | Opis |
|:--|:--|
| nazwa\_obiektu | Nazwa obiektu kolekcji. |
| instancji\_regex |  Wyrażenie *regularne* definiujące, które wystąpienia mają być zbierane. Wartość: `.*` określa wszystkie wystąpienia. Aby zebrać metryki \_procesora tylko dla `_Total`wystąpienia Suma, można określić . Aby zebrać metryki procesu tylko dla wystąpień crond lub `(crond\|sshd)`sshd, można określić: . |
| nazwa\_\_licznika regex | Wyrażenie *regularne* definiujące liczniki (dla obiektu) do zebrania. Aby zebrać wszystkie liczniki dla `.*`obiektu, należy określić: . Aby zebrać tylko liczniki miejsca wymiany dla obiektu pamięci, można na przykład określić:`.+Swap.+` |
| interval | Częstotliwość, z jaką są zbierane liczniki obiektu. |


W poniższej tabeli wymieniono obiekty i liczniki, które można określić w pliku konfiguracyjnym.  Istnieją dodatkowe liczniki dostępne dla niektórych aplikacji, zgodnie z opisem w [liczniki wydajności Zbieranie dla aplikacji systemu Linux w usłudze Azure Monitor](data-sources-linux-applications.md).

| Nazwa obiektu | Nazwa licznika |
|:--|:--|
| Dysk logiczny | % wolnych i-węzłów |
| Dysk logiczny | % wolnego miejsca |
| Dysk logiczny | % zużytych i-węzłów |
| Dysk logiczny | % używanej przestrzeni |
| Dysk logiczny | Bajty odczytu dysku/s |
| Dysk logiczny | Odczyty dysku/s |
| Dysk logiczny | Transfery dysków/s |
| Dysk logiczny | Bajty zapisu dysku na sekundę |
| Dysk logiczny | Zapisy na dysku/s |
| Dysk logiczny | Darmowe megabajty |
| Dysk logiczny | Bajty dysku logicznego na sekundę |
| Memory (Pamięć) | % dostępnej pamięci |
| Memory (Pamięć) | % dostępnej przestrzeni wymiany |
| Memory (Pamięć) | % używanej pamięci |
| Memory (Pamięć) | % używanej przestrzeni wymiany |
| Memory (Pamięć) | Dostępna pamięć MBytes |
| Memory (Pamięć) | Dostępne wymiany bajtów |
| Memory (Pamięć) | Odczyty strony/s |
| Memory (Pamięć) | Zapis strony/s |
| Memory (Pamięć) | Strony/s |
| Memory (Pamięć) | Używana przestrzeń wymiany bajtów MBytes |
| Memory (Pamięć) | Używane mbyty pamięci |
| Network (Sieć) | Łączna liczba przesłanych bajtów |
| Network (Sieć) | Całkowita liczba odebranych bajtów |
| Network (Sieć) | Całkowita liczba bajtów |
| Network (Sieć) | Łączna liczba przesłanych pakietów |
| Network (Sieć) | Łączna liczba odebranych pakietów |
| Network (Sieć) | Całkowita liczba błędów Rx |
| Network (Sieć) | Całkowita liczba błędów TX |
| Network (Sieć) | Łączna liczba kolizji |
| Dysk fizyczny | Średnia dysk s/odczyt |
| Dysk fizyczny | Średnia s/transfer dysku |
| Dysk fizyczny | Średnia sek./zapis |
| Dysk fizyczny | Bajty dysku fizycznego/s |
| Proces | Pct Uprzywilejowany czas |
| Proces | Pct Czas użytkownika |
| Proces | Używane pamięci kBytes |
| Proces | Wirtualna pamięć współdzielona |
| Procesor | % czasu DPC |
| Procesor | Czas bezczynności (%) |
| Procesor | % czasu przerwania |
| Procesor | % czasu oczekiwania we/wy |
| Procesor | % Miły czas |
| Procesor | % czasu uprzywilejowanego |
| Procesor | Czas procesora (%) |
| Procesor | % czasu użytkownika |
| System | Wolna pamięć fizyczna |
| System | Wolne miejsce w plikach stronicowania |
| System | Darmowa pamięć wirtualna |
| System | Procesy |
| System | Rozmiar przechowywany w plikach stronicowania |
| System | Uptime |
| System | Użytkownicy |


Poniżej przedstawiono domyślną konfigurację metryk wydajności.

    <source>
      type oms_omi
      object_name "Physical Disk"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Logical Disk"
      instance_regex ".*
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Processor"
      instance_regex ".*
      counter_name_regex ".*"
      interval 30s
    </source>

    <source>
      type oms_omi
      object_name "Memory"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>

## <a name="data-collection"></a>Zbieranie danych
Usługa Azure Monitor zbiera wszystkie liczniki wydajności określone w określonym przedziale czasu próbkowania na wszystkich agentów, które mają zainstalowany ten licznik.  Dane nie są agregowane, a nieprzetworzone dane są dostępne we wszystkich widokach zapytań dziennika przez czas określony przez subskrypcję.

## <a name="performance-record-properties"></a>Właściwości rekordu wydajności
Rekordy wydajności mają typ **Perf** i mają właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Komputer, z których odebrano zdarzenie. |
| CounterName |Nazwa licznika wydajności |
| Counterpath |Pełna ścieżka licznika w \\ \\ \<formularzu Komputer \\>licznika obiektu(wystąpienia).\\ |
| Wartość licznika |Wartość liczbowa licznika. |
| InstanceName |Nazwa wystąpienia zdarzenia.  Puste, jeśli nie ma wystąpienia. |
| ObjectName |Nazwa obiektu wydajności |
| SourceSystem |Typ agenta, z jakiego dane zostały zebrane. <br><br>OpsManager – Agent systemu Windows, albo bezpośrednie połączenie lub SCOM <br> Linux – Wszyscy agenci Linuksa  <br> Usługa AzureStorage — diagnostyka platformy Azure |
| TimeGenerated |Data i godzina próbkowania danych. |

## <a name="sizing-estimates"></a>Szacowanie rozmiaru
 Przybliżony szacunek dla zbierania określonego licznika w odstępach 10-sekundowych wynosi około 1 MB dziennie na wystąpienie.  Wymagania dotyczące magazynowania określonego licznika można oszacować za pomocą następującej formuły.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>Rejestrowanie zapytań z rekordami wydajności
Poniższa tabela zawiera różne przykłady zapytań dziennika, które pobierają rekordy wydajności.

| Zapytanie | Opis |
|:--- |:--- |
| Wyd. |Wszystkie dane dotyczące wydajności |
| Perf &#124; gdzie komputer == "MyComputer" |Wszystkie dane dotyczące wydajności z określonego komputera |
| Perf &#124; gdzie CounterName == "Bieżąca długość kolejki dysku" |Wszystkie dane dotyczące wydajności dla określonego licznika |
| Perf &#124; gdzie ObjectName == "Procesor" i CounterName == "% czasu procesora" i InstanceName == "_Total" &#124; podsumować AVGCPU = avg(CounterValue) przez komputer |Średnie wykorzystanie procesora na wszystkich komputerach |
| Perf &#124; gdzie CounterName == "% czasu procesora" &#124; podsumować AggregatedValue = max(CounterValue) przez komputer |Maksymalne wykorzystanie procesora NA wszystkich komputerach |
| Perf &#124; gdzie ObjectName == "LogicalDisk" i CounterName == "Bieżąca długość kolejki dysku" i Komputer == "MyComputerName" &#124; podsumuj AggregatedValue = avg(CounterValue) według InstanceName |Średnia bieżąca długość kolejki dysku we wszystkich wystąpieniach danego komputera |
| Wartość &#124;, w której nazwa przeciwstawna == "Transfery dysków/s" &#124; podsumowują aggregatedValue = percentyl(CounterValue, 95) według komputera |95 percentyl transferów dysków/s na wszystkich komputerach |
| Perf &#124; gdzie Nazwa przeciwstawna == "% czasu procesora" i Nazwa wystąpienia == "_Total" &#124; podsumuj AggregatedValue = avg(CounterValue) przez bin(TimeGenerated, 1h), Komputer |Średnia godzinowa użycia procesora na wszystkich komputerach |
| Perf &#124; gdzie komputer == "MyComputer" i CounterName startswith_cs "%" i InstanceName == "_Total" &#124; podsumować AggregatedValue = percentile(CounterValue, 70) przez bin(TimeGenerated, 1h), CounterName | Godzinowy licznik 70 percentyla każdego % procenta dla danego komputera |
| Perf &#124; gdzie CounterName == "% czasu procesora" i InstanceName == "_Total" i komputer == "MyComputer" &#124; podsumuj ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = percentile(CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) przez bin(TimeGenerated, 1h), Komputer |Średnie godziny pracy, minimalne, maksymalne i 75-percentylowe użycie procesora dla określonego komputera |
| Perf &#124; gdzie ObjectName == "MSSQL$INST2:Databases" i InstanceName == "master" | Wszystkie dane dotyczące wydajności z obiektu wydajności Baza danych dla głównej bazy danych z wystąpienia INST2 o nazwie SQL Server.  




## <a name="next-steps"></a>Następne kroki
* [Zbieraj liczniki wydajności z aplikacji Linux,](data-sources-linux-applications.md) w tym MySQL i Apache HTTP Server.
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md) do analizowania danych zebranych ze źródeł danych i rozwiązań.  
* Eksportowanie zebranych danych do [usługi Power BI](powerbi.md) w celu uzyskania dodatkowych wizualizacji i analiz.
