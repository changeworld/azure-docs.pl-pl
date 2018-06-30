---
title: Zbieranie i analizowanie liczniki wydajności w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Liczniki wydajności są zbierane przez analizy dzienników do analizowania wydajności agentów systemu Windows i Linux.  W tym artykule opisano sposób konfigurowania kolekcji liczników wydajności dla obu systemu Windows i Linux agentów, ich szczegóły są przechowywane w obszarze roboczym i jak analizować je w portalu Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2017
ms.author: magoedte
ms.component: na
ms.openlocfilehash: b23c170e557d019abf2b9aab8edcb74728bc872d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128779"
---
# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>System Windows i Linux źródła danych wydajności w analizy dzienników
Liczniki wydajności w systemie Windows i Linux zapewniają wgląd w wydajności składników sprzętu, systemów operacyjnych i aplikacji.  Analizy dzienników można zebrać liczników wydajności odstępach częste do analizy w pobliżu czasu rzeczywistego (NRT) oprócz agregowanie danych wydajności dłuższy okres analizy i raportowania.

![Liczniki wydajności](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurowanie liczników wydajności
Konfigurowanie liczników wydajności z [danych menu Ustawienia usługi Analiza dzienników](log-analytics-data-sources.md#configuring-data-sources).

Podczas pierwszej konfiguracji systemu Windows lub Linux wydajności liczniki dla nowego obszaru roboczego analizy dzienników, podano opcję, aby szybko utworzyć kilka typowych liczników.  Na liście obok każdego z nich znajduje się pole wyboru.  Upewnij się, że wszystkie liczniki, które chcesz utworzyć początkowo są zaznaczone, a następnie kliknij przycisk **Dodaj wybrane liczniki wydajności**.

Do liczników wydajności systemu Windows można wybrać określonego wystąpienia dla każdego licznika wydajności. Liczników wydajności systemu Linux wystąpienie poszczególnych liczników, którą można wybrać ma zastosowanie do wszystkich liczników podrzędnych licznika nadrzędnej. W poniższej tabeli przedstawiono typowe wystąpienia dostępne do liczników wydajności zarówno systemu Linux i Windows.

| Nazwa wystąpienia | Opis |
| --- | --- |
| \_Całkowita liczba |Całkowita liczba wszystkich wystąpień |
| \* |Wszystkie wystąpienia |
| (/&#124;/var) |Dopasowuje wystąpienia o nazwie: / lub /var |

### <a name="windows-performance-counters"></a>Liczniki wydajności systemu Windows

![Konfigurowanie liczników wydajności systemu Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Wykonaj tę procedurę, aby dodać nowy licznik wydajności systemu Windows do zbierania.

1. Wpisz nazwę komputera w polu tekstowym w formacie *\counter obiektu (wystąpienia)*.  Po rozpoczęciu wprowadzania, są prezentowane z listą zgodnych wspólnej liczników.  Licznik albo można wybrać z listy lub wpisz własny.  Wszystkie wystąpienia określonego licznika można także wrócić, określając *object\counter*.  

    Podczas zbierania liczników wydajności programu SQL Server z nazwanego wystąpienia, wszystkie o nazwie Nazwa wystąpienia liczników rozpoczyna się od *MSSQL$* i następuje nazwa wystąpienia.  Na przykład, zbieraj dane licznika Stosunek trafień w pamięci podręcznej dziennika dla wszystkich baz danych z obiektu wydajności bazy danych dla serwera SQL o nazwie wystąpienia INST2, określ `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

2. Kliknij przycisk **+** lub naciśnij klawisz **Enter** można dodać licznika do listy.
3. Po dodaniu licznik używa domyślnie 10 sekund na jego **interwału próbkowania**.  Można zmienić to wyższej wartości do 1800 sekund (30 minut), aby zmniejszyć wymagania dotyczące magazynu danych wydajności zebranych.
4. Po zakończeniu dodawania liczników kliknij **zapisać** przycisk w górnej części ekranu, aby zapisać konfigurację.

### <a name="linux-performance-counters"></a>Liczniki wydajności systemu Linux

![Konfigurowanie liczników wydajności systemu Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Wykonaj tę procedurę, aby dodać nowy licznik wydajności systemu Linux do zbierania.

1. Domyślnie wszystkie zmiany konfiguracji są automatycznie przypisany do wszystkich agentów.  Dla agentów systemów Linux plik konfiguracji jest wysyłany do Fluentd modułów zbierających dane.  Jeśli chcesz zmodyfikować ten plik ręcznie na każdym agenta systemu Linux, usuń zaznaczenie pola *Zastosuj poniższą konfigurację na moich maszynach z systemem Linux* i postępuj zgodnie ze wskazówkami poniżej.
2. Wpisz nazwę komputera w polu tekstowym w formacie *\counter obiektu (wystąpienia)*.  Po rozpoczęciu wprowadzania, są prezentowane z listą zgodnych wspólnej liczników.  Licznik albo można wybrać z listy lub wpisz własny.  
3. Kliknij przycisk **+** lub naciśnij klawisz **Enter** można dodać do listy inne liczniki dla obiekt licznika.
4. Wszystkie liczniki dla obiekt używać tego samego **interwału próbkowania**.  Wartość domyślna to 10 sekund.  Możesz zmienić to wyższej wartości do 1800 sekund (30 minut), aby zmniejszyć wymagania dotyczące magazynu danych wydajności zebranych.
5. Po zakończeniu dodawania liczników kliknij **zapisać** przycisk w górnej części ekranu, aby zapisać konfigurację.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Konfigurowanie liczników wydajności systemu Linux w pliku konfiguracji
Zamiast konfigurować liczniki wydajności systemu Linux przy użyciu portalu Azure, istnieje możliwość edytowania plików konfiguracji agenta systemu Linux.  Metryki wydajności do zbierania są kontrolowane przez konfigurację w **/etc/opt/microsoft/omsagent/\<identyfikator obszaru roboczego\>/conf/omsagent.conf**.

Każdy obiekt lub kategorii, metryki wydajności do zbierania powinien być zdefiniowany w pliku konfiguracyjnym jako pojedynczy `<source>` elementu. Składnia jest zgodny ze wzorcem poniżej.

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
| obiekt\_nazwy | Nazwa obiektu do kolekcji. |
| wystąpienie\_wyrażeń regularnych |  A *wyrażenia regularnego* Definiowanie które wystąpienia zbierania. Wartość: `.*` określa wszystkich wystąpień. Aby zbierać metryki procesora tylko \_całkowita liczba wystąpień, można określić `_Total`. Aby zbierać metryki procesu tylko wystąpienia crond lub sshd, można określić: `(crond\|sshd)`. |
| Licznik\_nazwa\_wyrażeń regularnych | A *wyrażenia regularnego* definiujący, które liczniki (dla obiekt) do zbierania. Aby zbierać wszystkie liczniki dla obiekt, podaj: `.*`. Aby zbierać tylko wymiany miejsca liczniki dla obiekt pamięci, na przykład można określić: `.+Swap.+` |
| interval | Częstotliwość pobierane liczniki obiektu. |


Poniższa tabela zawiera listę obiektów i liczników, które można określić w pliku konfiguracji.  Dostępne są dodatkowe liczniki dla określonych aplikacji zgodnie z opisem w [zebrać liczników wydajności dla aplikacji systemu Linux w analizy dzienników](log-analytics-data-sources-linux-applications.md).

| Nazwa obiektu | Nazwa licznika |
|:--|:--|
| Dysk logiczny | % Wolnych węzłów i |
| Dysk logiczny | % Wolnego miejsca |
| Dysk logiczny | % Użytych węzłów i |
| Dysk logiczny | Używany obszar % |
| Dysk logiczny | Bajty odczytu z dysku/s |
| Dysk logiczny | Odczyty dysku/s |
| Dysk logiczny | Transfery dyskowe/s |
| Dysk logiczny | Bajty zapisu na dysku/s |
| Dysk logiczny | Zapisy dysku/s |
| Dysk logiczny | Wolne megabajty |
| Dysk logiczny | Bajty dysku logicznego/s |
| Memory (Pamięć) | % Dostępnej pamięci |
| Memory (Pamięć) | % Dostępnego obszaru wymiany |
| Memory (Pamięć) | Procent wykorzystania pamięci |
| Memory (Pamięć) | Używany obszar wymiany (MB) % |
| Memory (Pamięć) | Dostępna pamięć (MB) |
| Memory (Pamięć) | Dostępny obszar wymiany |
| Memory (Pamięć) | Odczyty stron/s |
| Memory (Pamięć) | Zapisy stron/s |
| Memory (Pamięć) | Strony/s |
| Memory (Pamięć) | Używany obszar wymiany (MB) miejsca |
| Memory (Pamięć) | Używana pamięć (MB) |
| Sieć | Całkowita liczba przesłanych bajtów |
| Sieć | Całkowita liczba odebranych bajtów |
| Sieć | Całkowita liczba bajtów |
| Sieć | Łączna liczba pakietów wysłanych |
| Sieć | Całkowita liczba odebranych pakietów |
| Sieć | Błędy odbioru całkowita |
| Sieć | Błędy wysyłania całkowita |
| Sieć | Całkowita liczba kolizji |
| Dysk fizyczny | Średni Czas dysku w s/Odczyt |
| Dysk fizyczny | Średni Dysku w s/Transfer |
| Dysk fizyczny | Średni Dysku w s/Zapis |
| Dysk fizyczny | Bajty dysku fizycznego/s |
| Proces | Czas uprzywilejowany PCT |
| Proces | Czas użytkownika protokołu PCT |
| Proces | Używane KB pamięci |
| Proces | Wirtualnej pamięci współużytkowanej |
| Procesor | Czas DPC (%) |
| Procesor | Czas bezczynności (%) |
| Procesor | Czas przerwań (%) |
| Procesor | Czas oczekiwania operacji We/Wy (%) |
| Procesor | Czas nieuprzywilejowany (%) |
| Procesor | Czas uprzywilejowany % |
| Procesor | Czas procesora (%) |
| Procesor | Czas użytkownika (%) |
| System | Wolnej pamięci fizycznej |
| System | Wolne miejsce w plikach stronicowania |
| System | Wolnej pamięci wirtualnej |
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
Analizy dzienników zbiera wszystkie liczniki wydajności określony w ich określonego interwału dla wszystkich agentów, na których zainstalowane licznika.  Danych nie jest agregowany i danych pierwotnych jest dostępna we wszystkich widokach wyszukiwania dziennika w czasie trwania określony przez subskrypcję.

## <a name="performance-record-properties"></a>Właściwości rekordu wydajności
Rejestruje wydajności mieć typu **wydajności** i mieć właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Komputer, który zostały zebrane zdarzenia. |
| CounterName |Nazwa licznika wydajności |
| Ścieżka_licznika |Pełna ścieżka licznika w formie \\ \\ \<komputera >\\obiekt(wystąpienie)\\licznika. |
| Równowartości |Wartość liczbowa licznika. |
| InstanceName |Nazwa wystąpienia zdarzenia.  Pusta, jeśli żadne wystąpienie. |
| Nazwa obiektu |Nazwa obiektu wydajności |
| SourceSystem |Typ agenta dane zostały zebrane. <br><br>Połącz OpsManager — agent systemu Windows, bądź bezpośrednio lub SCOM <br> Linux — wszystkich agentów systemu Linux  <br> AzureStorage — Diagnostyka Azure |
| TimeGenerated |Data i godzina, które zostało pobrane dane. |

## <a name="sizing-estimates"></a>Szacuje zmiany rozmiaru
 Oszacowanie dla kolekcji określonego licznika na 10 sekund jest około 1 MB na dzień dla każdego wystąpienia.  Można oszacować wymagania dotyczące magazynu określonego licznika przy użyciu następującej formuły.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Dziennik wyszukiwania z rekordami wydajności
Poniższa tabela zawiera przykłady różnych dziennika wyszukiwania, które pobierają rekordy wydajności.

| Zapytanie | Opis |
|:--- |:--- |
| Wydajności |Wszystkie dane dotyczące wydajności |
| Wydajności &#124; gdzie komputera == "Mój komputer" |Wszystkie dane dotyczące wydajności z określonego komputera |
| Wydajności &#124; w przypadku, gdy CounterName == "Bieżąca długość kolejki dysku" |Wszystkie dane wydajności dla określonego licznika |
| Wydajności &#124; gdzie ObjectName == "Procesor" i CounterName == "% czasu procesora" i InstanceName == "_łącznie" &#124; Podsumuj AVGCPU = avg(Average) przez komputer |Średnie wykorzystanie procesora CPU na wszystkich komputerach |
| Wydajności &#124; w przypadku, gdy CounterName == "% czasu procesora" &#124; Podsumuj AggregatedValue = max(Max) przez komputer |Maksymalne wykorzystanie procesora CPU na wszystkich komputerach |
| Wydajności &#124; gdzie ObjectName == "Dysk logiczny" i CounterName == "Bieżąca długość kolejki dysku" i komputer == "MyComputerName" &#124; Podsumuj AggregatedValue = avg(Average) przez InstanceName |Średnia długość kolejki dysku bieżącego we wszystkich wystąpieniach danego komputera |
| Wydajności &#124; w przypadku, gdy CounterName == "Na sekundę DiskTransfers" &#124; Podsumuj AggregatedValue = percentyl (średnia, 95) przez komputer |95 percentylu z transfery dyskowe/s na wszystkich komputerach |
| Wydajności &#124; w przypadku, gdy CounterName == "% czasu procesora" i InstanceName == "_łącznie" &#124; Podsumuj AggregatedValue = avg(CounterValue) przez bin (TimeGenerated, 1 godz.), komputer |Średniej godzinowej użycia procesora CPU na wszystkich komputerach |
| Wydajności &#124; gdzie komputera == "Mój komputer" i CounterName startswith_cs "%" i InstanceName == "_łącznie" &#124; Podsumuj AggregatedValue = bin (TimeGenerated, 1 godz.), CounterName percentyl (równowartości 70) | Co godzinę percentyl 70 co licznika procent % dla określonego komputera |
| Wydajności &#124; w przypadku, gdy CounterName == "% czasu procesora" i InstanceName == "_łącznie" i komputer == "Mój komputer" &#124; Podsumuj ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] percentyl (równowartości, 75), = ["max(CounterValue)"] = max(CounterValue) przez bin (TimeGenerated, 1 godz.), komputer |Co godzinę średnia, minimalne, maksymalne i percentyl 75 użycia procesora CPU dla określonego komputera |
| Wydajności &#124; gdzie ObjectName == "MSSQL$ INST2: bazy danych" i InstanceName == "główna" | Wszystkie dane dotyczące wydajności z obiektu wydajności bazy danych dla bazy danych master z nazwanego wystąpienia programu SQL Server INST2.  




## <a name="next-steps"></a>Kolejne kroki
* [Zbierania liczników wydajności z aplikacje w systemie Linux](log-analytics-data-sources-linux-applications.md) tym MySQL i Apache HTTP Server.
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) analizować dane zebrane ze źródeł danych i rozwiązania.  
* Zebrane dane, aby wyeksportować [usługi Power BI](log-analytics-powerbi.md) dodatkowe wizualizacje i analizy.
