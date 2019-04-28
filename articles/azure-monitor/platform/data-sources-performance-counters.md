---
title: Zbieraj i Analizuj liczników wydajności w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Liczniki wydajności są zbierane przez usługi Azure Monitor do analizowania wydajności agentów systemów Windows i Linux.  W tym artykule opisano sposób konfigurowania zbiór liczników wydajności zarówno Windows, i agenci dla systemu Linux, ich szczegóły są przechowywane w obszarze roboczym i analizowanie ich w witrynie Azure portal.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 93f47529e3be44ff1db4e089bdcdca3eb1b4dea3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363409"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Windows i Linux źródła danych dotyczących wydajności w usłudze Azure Monitor
Liczniki wydajności, Windows i Linux zapewniają wgląd w wydajność składniki sprzętowe, systemów operacyjnych i aplikacji.  Usługa Azure Monitor można zebrać liczników wydajności w krótkich odstępach czasu do analizy prawie czasie rzeczywistym (nRT, NEAR) oprócz agregowania danych wydajności dla dłuższy okres analizy i raportowania.

![Liczniki wydajności](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurowanie liczników wydajności
Skonfiguruj liczniki wydajności z [menu danych w zaawansowanych ustawieniach](agent-data-sources.md#configuring-data-sources).

Podczas pierwszej konfiguracji liczników Windows lub Linux wydajności dla nowego obszaru roboczego, możesz skorzystać z opcji, aby szybko utworzyć kilka typowych liczników.  Na liście obok każdego z nich znajduje się pole wyboru.  Upewnij się, że wszystkie liczniki, które chcesz utworzyć są zaznaczone, a następnie kliknij przycisk **Dodaj wybrane liczniki wydajności**.

Liczniki wydajności Windows możesz wybrać konkretne wystąpienie dla każdego licznika wydajności. Liczniki wydajności systemu Linux wystąpienia poszczególnych liczników, które możesz wybrać mają zastosowanie do wszystkie liczniki podrzędnych licznika nadrzędnej. W poniższej tabeli przedstawiono typowe wystąpienia dostępne liczniki wydajności systemu Linux i Windows.

| Nazwa wystąpienia | Opis |
| --- | --- |
| \_Łączna liczba |Łączna liczba wszystkich wystąpień |
| \* |Wszystkie wystąpienia |
| (/&#124;/var) |Dopasowuje wystąpienia o nazwie: / lub /var |

### <a name="windows-performance-counters"></a>Liczniki wydajności systemu Windows

![Skonfiguruj liczniki wydajności Windows](media/data-sources-performance-counters/configure-windows.png)

Wykonaj poniższą procedurę, aby dodać nowy licznik wydajności Windows do zbierania.

1. Wpisz nazwę licznika w polu tekstowym w formacie *\counter obiektu (wystąpienia)*.  Gdy zaczniesz pisać, zostanie wyświetlona pasującego listę typowych liczników.  Można albo wybierz licznik z listy lub wpisz własny.  Możesz też zwrócić wszystkie wystąpienia określonego licznika, określając *object\counter*.  

    Podczas zbierania liczników wydajności programu SQL Server z nazwanego wystąpienia, wszystkie nazwane wystąpienia liczników rozpoczyna się od *MSSQL$* i następuje nazwa wystąpienia.  Na przykład, aby zbieranie danych licznika Współczynnik trafień w pamięci podręcznej dziennika dla wszystkich baz danych z obiektu wydajności bazy danych SQL o nazwie wystąpienia INST2, należy określić `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

2. Kliknij przycisk **+** lub naciśnij **Enter** można dodać licznika do listy.
3. Podczas dodawania licznika, który używa domyślnej wartości 10 sekund na jego **interwał próbkowania**.  Można zmienić tego na wartość większą do 1800 sekund (30 minut) Jeśli chcesz zmniejszyć wymagania dotyczące magazynu danych wydajności zebranych.
4. Po zakończeniu dodawania liczników, kliknij przycisk **Zapisz** znajdujący się u góry ekranu, aby zapisać konfigurację.

### <a name="linux-performance-counters"></a>Liczniki wydajności systemu Linux

![Skonfiguruj liczniki wydajności systemu Linux](media/data-sources-performance-counters/configure-linux.png)

Wykonaj poniższą procedurę, aby dodać nowy licznik wydajności systemu Linux w celu zbierania.

1. Domyślnie wszystkie zmiany konfiguracji są automatycznie przekazywane do wszystkich agentów.  Agenci dla systemu Linux, aby uzyskać plik konfiguracji są wysyłane do Fluentd modułów zbierających dane.  Jeśli chcesz zmodyfikować ten plik ręcznie na każdym agencie systemu Linux, usuń zaznaczenie pola *Zastosuj poniższą konfigurację do moich maszyn z systemem Linux* i wykonaj poniższe wskazówki.
2. Wpisz nazwę licznika w polu tekstowym w formacie *\counter obiektu (wystąpienia)*.  Gdy zaczniesz pisać, zostanie wyświetlona pasującego listę typowych liczników.  Można albo wybierz licznik z listy lub wpisz własny.  
3. Kliknij przycisk **+** lub naciśnij **Enter** można dodać licznika listę innych liczników dla tego obiektu.
4. Wszystkie liczniki dla obiektu używać tego samego **interwał próbkowania**.  Wartość domyślna to 10 sekund.  Możesz zmienić na wyższa wartość do 1800 sekund (30 minut) Jeśli chcesz zmniejszyć wymagania dotyczące magazynu danych wydajności zebranych.
5. Po zakończeniu dodawania liczników, kliknij przycisk **Zapisz** znajdujący się u góry ekranu, aby zapisać konfigurację.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Skonfiguruj liczniki wydajności systemu Linux w pliku konfiguracji
Zamiast konfigurować liczniki wydajności systemu Linux przy użyciu portalu Azure, masz możliwość edytowania plików konfiguracji agenta systemu Linux.  Metryki wydajności do zbierania są kontrolowane przez tę konfigurację w **/etc/opt/microsoft/omsagent/\<identyfikator obszaru roboczego\>/conf/omsagent.conf**.

Każdy obiekt lub kategorii metryki wydajności do zbierania powinna być zdefiniowana w pliku konfiguracyjnym jako pojedynczy `<source>` elementu. Składnia jest zgodny ze wzorcem poniżej.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


W poniższej tabeli opisano parametry w tym elemencie.

| Parametry | Opis |
|:--|:--|
| obiekt\_nazwy | Nazwa obiektu dla kolekcji. |
| instance\_regex |  A *wyrażenia regularnego* Definiowanie które wystąpienia do zbierania. Wartość: `.*` określa wszystkie wystąpienia. Aby zbierać metryki procesora tylko \_łączna liczba wystąpień, można określić `_Total`. Aby zbierać metryki tylko crond lub sshd wystąpień procesu, można określić: `(crond\|sshd)`. |
| Licznik\_nazwa\_wyrażeń regularnych | A *wyrażenia regularnego* definiowania, które liczników (dla obiektu) do zbierania. Aby zebrać wszystkie liczniki dla obiektu, należy określić: `.*`. Aby zebrać tylko zamiany miejsca liczniki dla obiektów pamięci, na przykład można określić: `.+Swap.+` |
| interval | Częstotliwość, na którym są zbierane liczniki obiektu. |


Poniższa tabela zawiera listę obiektów i liczników, które można określić w pliku konfiguracji.  Dostępne są dodatkowe liczniki dla określonych aplikacji zgodnie z opisem w [zbierania liczników wydajności dla aplikacji systemu Linux w usłudze Azure Monitor](data-sources-linux-applications.md).

| Nazwa obiektu | Nazwa licznika |
|:--|:--|
| Dysk logiczny | % Wolnych węzłów i |
| Dysk logiczny | % Wolnego miejsca |
| Dysk logiczny | % Użytych węzłów i |
| Dysk logiczny | Procent wykorzystania miejsca |
| Dysk logiczny | Bajty odczytu z dysku/s |
| Dysk logiczny | Odczyty dysku/s |
| Dysk logiczny | Transfery dyskowe/s |
| Dysk logiczny | Bajty zapisu na dysku/s |
| Dysk logiczny | Zapisy dysku/s |
| Dysk logiczny | Wolne megabajty |
| Dysk logiczny | Bajty dysku logicznego/s |
| Memory (Pamięć) | % Dostępnej pamięci. |
| Memory (Pamięć) | % Dostępnego obszaru wymiany |
| Memory (Pamięć) | Procent wykorzystania pamięci |
| Memory (Pamięć) | Używany obszar wymiany % |
| Memory (Pamięć) | Dostępna pamięć (MB) |
| Memory (Pamięć) | Dostępny obszar wymiany |
| Memory (Pamięć) | Odczyty stron/s |
| Memory (Pamięć) | Zapisy stron/s |
| Memory (Pamięć) | Strony/s |
| Memory (Pamięć) | Miejsce używany obszar wymiany (MB) |
| Memory (Pamięć) | Używana pamięć (MB) |
| Sieć | Całkowita liczba przesłanych bajtów |
| Sieć | Całkowita liczba odebranych bajtów |
| Sieć | Całkowita liczba bajtów |
| Sieć | Wszystkie pakiety przesyłane |
| Sieć | Łączna liczba odebranych pakietów |
| Sieć | Rx łączna liczba błędów |
| Sieć | Tx łączna liczba błędów |
| Sieć | Łączna liczba konfliktów |
| Dysk fizyczny | Średni Czas dysku w s/Odczyt |
| Dysk fizyczny | Średni Dysku w s/Transfer |
| Dysk fizyczny | Średni Dysku w s/Zapis |
| Dysk fizyczny | Bajty dysku fizycznego/s |
| Przetwórz | Czas uprzywilejowany PCT |
| Przetwórz | Czas użytkownika protokołu PCT |
| Przetwórz | Używana pamięć (KB pamięci) |
| Przetwórz | Wirtualnej pamięci wspólnej |
| Procesor | Czas DPC (%) |
| Procesor | Czas bezczynności (%) |
| Procesor | Czas przerwań (%) |
| Procesor | Czas oczekiwania operacji We/Wy % |
| Procesor | Czas nieuprzywilejowany (%) |
| Procesor | Czas uprzywilejowany % |
| Procesor | Czas procesora (%) |
| Procesor | Czas użytkownika (%) |
| System | Wolna pamięć fizyczna |
| System | Wolne miejsce w plikach stronicowania |
| System | Wolna pamięć wirtualna |
| System | Procesy |
| System | Rozmiar zapisanych w plikach stronicowania |
| System | Czas pracy |
| System | Użytkownicy |


Poniżej przedstawiono konfigurację domyślną dla metryki wydajności.

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
Usługa Azure Monitor umożliwia zbieranie informacji o wszystkich liczników wydajności określonej w ich określonego interwału dla wszystkich agentów, które mają zainstalowanych liczników.  Dane nie są agregowane, a dane pierwotne są dostępne we wszystkich widokach zapytanie dziennika, przez czas określony przez subskrypcję.

## <a name="performance-record-properties"></a>Właściwości rekordu wydajności
Rekordy wydajności mają typ **wydajności** i mają właściwości podane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Komputer, który zostały zebrane zdarzenia. |
| CounterName |Nazwa licznika wydajności |
| CounterPath |Pełna ścieżka licznika w formie \\ \\ \<komputera >\\obiekt(wystąpienie)\\licznika. |
| CounterValue |Wartość liczbowa licznika. |
| InstanceName |Nazwa wystąpienia zdarzenia.  Pusty, jeśli żadne wystąpienie. |
| Nazwa obiektu |Nazwa obiektu wydajności |
| SourceSystem |Typ agenta, które zostały zebrane dane. <br><br>Połącz OpsManager — Windows agent, bezpośrednio lub SCOM <br> Linux — Wszyscy agenci systemu Linux  <br> AzureStorage — Diagnostyka Azure |
| TimeGenerated |Data i godzina, które zostało pobrane dane. |

## <a name="sizing-estimates"></a>Szacuje zmiany rozmiaru
 Przybliżone oszacowanie dla kolekcji określonego licznika w odstępach 10-sekundowych jest około 1 MB na dzień dla każdego wystąpienia.  Można oszacować wymagania dotyczące magazynu określonego licznika przy użyciu następującej formuły.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>Dziennik zapytań przy użyciu rekordów wydajności
Poniższa tabela zawiera przykłady różnych zapytań dziennika, które pobierają rekordy wydajności.

| Zapytanie | Opis |
|:--- |:--- |
| Perf |Wszystkie dane dotyczące wydajności |
| Perf &#124; where Computer == "MyComputer" |Wszystkie dane dotyczące wydajności z określonego komputera |
| Perf &#124; w przypadku, gdy CounterName == "Bieżąca długość kolejki dysku" |Wszystkie dane dotyczące wydajności dla określonego licznika |
| Perf &#124; gdzie ObjectName == "Procesor" i CounterName == "% czasu procesora" i InstanceName == "_łącznie" &#124; Podsumuj AVGCPU = avg(Average) według komputera |Średnie wykorzystanie procesora CPU na wszystkich komputerach |
| Perf &#124; where CounterName == "% Processor Time" &#124; summarize AggregatedValue = max(Max) by Computer |Maksymalne wykorzystanie procesora CPU na wszystkich komputerach |
| Perf &#124; gdzie ObjectName == "DyskLogiczny" i CounterName == "Bieżąca długość kolejki dysku" i komputer == "MyComputerName" &#124; summarize AggregatedValue = avg(Average) przez InstanceName |Średnia długość kolejki na bieżącym dysku we wszystkich wystąpieniach danego komputera |
| Perf &#124; w przypadku, gdy CounterName == "Na sekundę DiskTransfers" &#124; summarize AggregatedValue = percentyl (95 średni,) według komputera |95. percentyl z transfery dyskowe/s na wszystkich komputerach |
| Perf &#124; w przypadku, gdy CounterName == "% czasu procesora" i InstanceName == "_łącznie" &#124; summarize AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 1 godz.), komputer |Średnia co godzinę użycia procesora CPU na wszystkich komputerach |
| Perf &#124; gdzie komputer == "Mój komputer" i CounterName startswith_cs "%" i InstanceName == "_łącznie" &#124; summarize AggregatedValue = bin (TimeGenerated, 1 godz.), CounterName percentyl (CounterValue 70) | Co godzinę 70 percentyl licznika procent co % dla określonego komputera |
| Perf &#124; w przypadku, gdy CounterName == "% czasu procesora" i InstanceName == "_łącznie" i komputer == "Mój komputer" &#124; podsumowanie ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = (CounterValue, 75.), percentyl ["max(CounterValue)"] = max(CounterValue) przez bin (TimeGenerated, 1 godz.), komputer |Co godzinę średnią, minimalną, maksymalną i percentyl 75 użycie procesora CPU dla określonego komputera |
| Perf &#124; gdzie ObjectName == "MSSQL$ INST2: bazy danych" i InstanceName == "master" | Wszystkie dane dotyczące wydajności z obiektu wydajności bazy danych dla bazy danych master z nazwanego wystąpienia programu SQL Server INST2.  




## <a name="next-steps"></a>Kolejne kroki
* [Liczniki wydajności są zbierane z poziomu aplikacji systemu Linux](data-sources-linux-applications.md) tym MySQL i Apache HTTP Server.
* Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md) analizować dane zbierane z innych źródeł danych i rozwiązań.  
* Zebrane dane, aby wyeksportować [usługi Power BI](powerbi.md) dodatkowych wizualizacji i analizy.
