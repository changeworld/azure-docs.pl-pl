---
title: Tworzenie zapytań względem dzienników z usługi Azure Monitor dla maszyn wirtualnych | Dokumentacja firmy Microsoft
description: Usługa Azure Monitor dla maszyn wirtualnych rozwiązania przekazuje metryki i rejestrowanie danych do usługi Log Analytics i w tym artykule opisano rekordy i zawiera przykładowe zapytania.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 446268f28e7c87196023636889f03be2da92ecfd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967646"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Tworzenie zapytań względem dzienników z usługi Azure Monitor dla maszyn wirtualnych
Usługa Azure Monitor dla maszyn wirtualnych zbiera metryki wydajności i połączenia, komputera i przetwarzanie magazynu danych i informacji o stanie kondycji i przekazuje go do magazynu danych usługi Log Analytics w usłudze Azure Monitor.  Dane te są dostępne dla [wyszukiwania](../log-analytics/log-analytics-log-searches.md) w usłudze Log Analytics. Dane te można zastosować do scenariuszy obejmujących planowania migracji, analizy wydajności, wykrywanie i rozwiązywanie problemów z wydajnością na żądanie.

## <a name="map-records"></a>Rekordy mapy
Jeden rekord jest generowany na godzinę dla każdego komputera unikatowy i procesów, oprócz rekordów, które są generowane, gdy proces lub komputer uruchamia lub jest uruchomiona do usługi Azure Monitor dla funkcji mapy maszyn wirtualnych. Te rekordy mają właściwości podane w poniższych tabelach. Pola i wartości w zdarzeniach ServiceMapComputer_CL są mapowane na pola zasób maszynę w interfejsie API ServiceMap usługi Azure Resource Manager. Pola i wartości w zdarzeniach ServiceMapProcess_CL są mapowane na pola zasobów procesu w interfejsie API ServiceMap usługi Azure Resource Manager. Pole ResourceName_s zgodne pola Nazwa odpowiedniego zasobu usługi Resource Manager. 

Istnieją wewnętrznie generowane właściwości, które służy do identyfikacji procesów unikatowy i komputerów:

- Komputera: Użyj *ResourceId* lub *ResourceName_s* do unikatowej identyfikacji komputera w obszarze roboczym usługi Log Analytics.
- Procesu: Użyj *ResourceId* do unikatowej identyfikacji procesów w obrębie obszaru roboczego usługi Log Analytics. *ResourceName_s* jest unikatowa w obrębie kontekstu komputera, na którym proces działa (MachineResourceName_s) 

Ponieważ wiele rekordów może istnieć dla określonego procesu i komputera w określonym okresie, zapytania może zwrócić więcej niż jeden rekord dla tego samego komputera lub procesu. Aby dołączyć tylko najnowsze rekord, należy dodać "| Funkcja deduplikacji ResourceId"zapytania.

### <a name="connections"></a>Połączenia
Metryki połączeń są zapisywane do nowej tabeli w usłudze Log Analytics — VMConnection. Ta tabela zawiera informacje o połączeniach dla maszyny (przychodzące i wychodzące). Metryki połączeń są również udostępniane za pomocą interfejsów API, które zapewniają środki do uzyskania określona Metryka przedziale czasu.  Połączenia protokołu TCP, wynikające z "*zaakceptować*- ing nasłuchiwania gniazda są dla ruchu przychodzącego, a te utworzone przez *połączyć*- ing do danego adresu IP i portu są ruchu wychodzącego. Kierunek połączenie jest reprezentowane przez właściwość "Direction", który może być ustawiony na **dla ruchu przychodzącego** lub **wychodzących**. 

Rekordów w tych tabelach są generowane na podstawie danych przekazywanych przez agenta zależności. Każdy rekord reprezentuje obserwacji w przedziałach czasu jednej minuty. Właściwość TimeGenerated wskazuje początek przedziału czasu. Każdy rekord zawiera informacje w celu zidentyfikowania odpowiednich jednostek, to znaczy, połączenia lub portu, a także metryki skojarzone z tej jednostki. Obecnie jest zgłaszany tylko aktywność sieciową występujący za pomocą protokołu TCP za pośrednictwem protokołu IPv4.

Aby zarządzać, koszt i złożoność, rekordy połączeń nie przedstawiają poszczególnych fizycznych połączeń sieciowych. Wiele połączeń sieci fizycznej są grupowane w logicznej połączenia, które następnie są odzwierciedlane w odpowiedniej tabeli.  Znaczenie, rejestruje w *VMConnection* tabeli reprezentują logiczne grupowanie, a nie poszczególnych fizycznego połączenia, które są przestrzegane. Połączenie z siecią fizyczną współużytkujące taką samą wartość, dla następujących atrybutów dla interwału danego jednej minuty są agregowane w jednym rekordzie logicznych w *VMConnection*. 

| Właściwość | Opis |
|:--|:--|
|Kierunek |Kierunek połączenia, wartość jest *dla ruchu przychodzącego* lub *ruchu wychodzącego* |
|Maszyna |Nazwa FQDN komputera |
|Proces |Tożsamość procesu lub grupy procesów, inicjowanie/akceptować połączenia |
|SourceIp |Adres IP źródła |
|DestinationIp |Adres IP miejsca docelowego |
|DestinationPort |Numer portu docelowego |
|Protokół |Protokół używany dla połączenia.  Wartości *tcp*. |

Aby uwzględnić wpływ grupowania, informacje o liczbie pogrupowanych połączeń fizycznych znajduje się w następujących właściwości rekordu:

| Właściwość | Opis |
|:--|:--|
|LinksEstablished |Liczba połączeń sieci fizycznej, które zostały utworzone w przedziale czasu raportowania |
|LinksTerminated |Liczba połączeń sieci fizycznej, które zostały zakończone raportowania przedziale czasu |
|LinksFailed |Liczba połączeń sieci fizycznej, które nie powiodły się w przedziale czasu raportowania. Te informacje są obecnie dostępne tylko dla połączeń wychodzących. |
|LinksLive |Liczba połączeń sieci fizycznej, które były otwarte na końcu raportowania przedział czasu|

#### <a name="metrics"></a>Metryki

Oprócz metryki liczbę połączeń informacji na temat ilości danych wysłanych i odebranych w danym połączenie logiczne lub portu sieci znajdują się również w następujących właściwości rekordu:

| Właściwość | Opis |
|:--|:--|
|Żądania |Całkowita liczba bajtów wysłanych raportowania przedziale czasu |
|BytesReceived |Całkowita liczba bajtów odebranych w przedziale czasu raportowania |
|Odpowiedzi |Liczba odpowiedzi zaobserwowane w przedziale czasu raportowania. 
|ResponseTimeMax |Największy czas odpowiedzi (w milisekundach) zaobserwowane w przedziale czasu raportowania.  Jeśli brak wartości właściwości jest pusta.|
|ResponseTimeMin |Najmniejszy czas odpowiedzi (w milisekundach) zaobserwowane w przedziale czasu raportowania.  Jeśli brak wartości właściwości jest pusta.|
|ResponseTimeSum |Suma czasów odpowiedzi (w milisekundach) zaobserwowane w przedziale czasu raportowania.  Jeśli brak wartości właściwości jest pusta|

Trzeci typ danych, zgłaszany jest czas odpowiedzi — obiekt wywołujący poświęcić czas oczekiwania na żądanie przesyłane przez połączenie do przetworzenia i przez zdalny punkt końcowy. Czas odpowiedzi, zgłaszane jest oszacowanie czas odpowiedzi true podstawowego protokołu aplikacji. Jest obliczana, przy użyciu algorytmów heurystycznych oparte na obserwacji przepływ danych między źródłowym i docelowym koniec połączenie z siecią fizyczną. Pod względem koncepcyjnym go różni się od czasu ostatniego bajtu żądania pozostawia nadawcy i czas po nadejściu ostatni bajt odpowiedzi powrotu do tego. Te dwa sygnatury czasowe są używane do odróżnić zdarzenia żądań i odpowiedzi dla danego połączenia fizycznych. Różnica między nimi reprezentuje czas odpowiedzi pojedynczego żądania. 

W pierwszej wersji tej funkcji nasze algorytm jest przybliżeniem, która może działać z dowolnym stopniu sukcesu w zależności od rzeczywistej aplikacji protokół dla połączenia określonej sieci. Na przykład bieżącego podejścia działa dobrze w przypadku protokołów odpowiedź na żądanie na podstawie takich jak HTTP (S), ale nie pracować jednokierunkowe lub odpowiednich protokołów opartych na kolejki komunikatów.

Oto niektóre ważne punkty, które należy wziąć pod uwagę:

1. Jeśli proces akceptuje połączenia na ten sam adres IP, ale w wielu interfejsów sieciowych, zostanie zgłoszony oddzielny rekord dla każdego interfejsu. 
2. Rekordy z symbolami wieloznacznymi IP będzie zawierać żadnych działań. Zostaną one dołączone do reprezentowania fakt, że port na maszynie jest otwarty dla ruchu przychodzącego ruchu sieciowego.
3. Aby zmniejszyć poziom szczegółowości i ilość danych, rekordów za pomocą adresu IP z symbolami wieloznacznymi zostaną pominięte po pasującego rekordu (w przypadku tego samego procesu, portów i protokołów) przy użyciu określonego adresu IP. Gdy rekord IP symbol wieloznaczny zostanie pominięty, IsWildcardBind właściwości rekordu z określonego adresu IP ustawi na wartość "True" do wskazania, że port jest uwidaczniany przez każdy interfejs raportowania maszyny.
4. Porty, które są powiązane tylko dla określonego interfejsu mają IsWildcardBind ustawiony na wartość "False".

#### <a name="naming-and-classification"></a>Nazewnictwo i klasyfikacji
Dla wygody adres IP zdalnego punktu końcowego połączenia znajduje się we właściwości RemoteIp. Dla połączeń przychodzących RemoteIp jest taka sama jak SourceIp, natomiast w przypadku połączeń wychodzących, jest taka sama jak DestinationIp. Właściwość RemoteDnsCanonicalNames reprezentuje zgłoszone przez maszynę dla RemoteIp nazwy kanonicznej DNS. Właściwości RemoteDnsQuestions i RemoteClassification są zarezerwowane do użytku w przyszłości. 

#### <a name="geolocation"></a>Geolokalizacja
*VMConnection* zawiera również informacje o geolokalizacji na drugim końcu każdego wybranego rekordu połączenia w następujących właściwości rekordu: 

| Właściwość | Opis |
|:--|:--|
|RemoteCountry |Nazwa kraju RemoteIp hostingu.  Na przykład *Stanów Zjednoczonych* |
|RemoteLatitude |Szerokość geograficzną.  Na przykład *47.68* |
|RemoteLongitude |Długość geograficzna geolokalizacji.  Na przykład *-122.12* |

#### <a name="malicious-ip"></a>Złośliwy adres IP
Dla każdej właściwości RemoteIp w *VMConnection* tabeli jest sprawdzana względem zbiór adresów IP przy użyciu znanych złośliwych działań. Jeśli RemoteIp została zidentyfikowana jako szkodliwe następujące właściwości zostaną wypełnione (są puste, jeśli adres IP nie jest uznawane za złośliwe) w następujących właściwości rekordu:

| Właściwość | Opis |
|:--|:--|
|MaliciousIp |Adres RemoteIp |
|IndicatorThreadType | |
|Opis | |
|TLPLevel | |
|Ufność | |
|Ważność | |
|FirstReportedDateTime | |
|LastReportedDateTime | |
|IsActive | |
|ReportReferenceLink | |
|AdditionalInformation | |

### <a name="servicemapcomputercl-records"></a>Rekordy ServiceMapComputer_CL
Rekordy z typem *ServiceMapComputer_CL* zawierają dane spisu dla serwerów z agenta zależności. Te rekordy mają właściwości podane w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
| Typ | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Unikatowy identyfikator dla maszyny w obszarze roboczym |
| ResourceName_s | Unikatowy identyfikator dla maszyny w obszarze roboczym |
| ComputerName_s | Nazwa FQDN komputera |
| Ipv4Addresses_s | Lista IPv4 serwera adresów |
| Ipv6Addresses_s | Lista IPv6 serwera adresów |
| DnsNames_s | Tablica nazw DNS |
| OperatingSystemFamily_s | Windows lub Linux |
| OperatingSystemFullName_s | Pełna nazwa systemu operacyjnego  |
| Bitness_s | Liczba bitów maszyny (32-bitowy lub 64-bitowy)  |
| PhysicalMemory_d | Pamięci fizycznej w Megabajtach |
| Cpus_d | Liczba procesorów CPU |
| CpuSpeed_d | Szybkość Procesora w MHz|
| VirtualizationState_s | *Nieznany*, *fizycznych*, *wirtualnego*, *funkcji hypervisor* |
| VirtualMachineType_s | *funkcji Hyper-v*, *vmware*i tak dalej |
| VirtualMachineNativeMachineId_g | Identyfikator maszyny Wirtualnej, zgodnie z dokonanym przez jego funkcji hypervisor |
| VirtualMachineName_s | Nazwa maszyny Wirtualnej |
| BootTime_t | W czasie rozruchu |

### <a name="servicemapprocesscl-type-records"></a>Rejestruje typ ServiceMapProcess_CL
Rekordy z typem *ServiceMapProcess_CL* mają dane spisu dla procesy połączone z protokołu TCP na serwerach z agenta zależności. Te rekordy mają właściwości podane w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
| Typ | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Unikatowy identyfikator procesu wewnątrz obszaru roboczego |
| ResourceName_s | Unikatowy identyfikator procesu na maszynie, na którym jest uruchomiony|
| MachineResourceName_s | Nazwa zasobu maszyny |
| ExecutableName_s | Nazwa pliku wykonywalnego procesu |
| StartTime_t | Czas rozpoczęcia procesu puli |
| FirstPid_d | Pierwszy identyfikator PID w puli procesów |
| Description_s | Opis procesu |
| CompanyName_s | Nazwa firmy |
| InternalName_s | Nazwa wewnętrzna |
| ProductName_s | Nazwa produktu |
| ProductVersion_s | Wersja produktu |
| FileVersion_s | Wersja pliku |
| CommandLine_s | W wierszu polecenia |
| ExecutablePath _s | Ścieżka do pliku wykonywalnego |
| WorkingDirectory_s | Katalog roboczy |
| UserName | Konta, w którym proces jest wykonywany |
| UserDomain | Domeny, w którym proces jest wykonywany |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

### <a name="list-all-known-machines"></a>Listę wszystkich znanych maszyn
ServiceMapComputer_CL | Podsumowanie arg_max(TimeGenerated, *) przez ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Wyświetl listę wszystkich zarządzanych komputerów pojemność pamięci fizycznej.
ServiceMapComputer_CL | Podsumowanie arg_max(TimeGenerated, *) przez ResourceId | Projekt PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Nazwa komputera listy, DNS, adresu IP i systemu operacyjnego.
ServiceMapComputer_CL | Podsumowanie arg_max(TimeGenerated, *) przez ResourceId | Projekt ComputerName_s OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Znajdź wszystkie procesy za pomocą "sql" w wierszu polecenia
ServiceMapProcess_CL | gdzie contains_cs CommandLine_s "sql" | Podsumowanie arg_max(TimeGenerated, *) przez ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Znajdź maszyny (najnowsze rekordu) według nazwy zasobu
Szukaj w "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" (ServiceMapComputer_CL) | Podsumowanie arg_max(TimeGenerated, *) przez ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Znajdź maszyny (najnowsze rekordu) przy użyciu adresu IP
Szukaj w "10.229.243.232" (ServiceMapComputer_CL) | Podsumowanie arg_max(TimeGenerated, *) przez ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Listę wszystkich znanych procesów na określonym komputerze
ServiceMapProcess_CL | gdzie MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | Podsumowanie arg_max(TimeGenerated, *) przez ResourceId

### <a name="list-all-computers-running-sql"></a>Lista wszystkich komputerów z programem SQL
ServiceMapComputer_CL | gdzie ResourceName_s w ((wyszukiwanie w (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Wyświetla listę wszystkich wersji produktu unikatowy programu curl w mojego centrum danych
ServiceMapProcess_CL | gdzie ExecutableName_s == "zawinięcie" | odrębne ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Utwórz grupę komputerów wszystkich komputerów z systemem CentOS
ServiceMapComputer_CL | gdzie contains_cs OperatingSystemFullName_s "CentOS" | odrębne ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Podsumowanie połączenia wychodzące z grupy maszyn
```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="next-steps"></a>Kolejne kroki
* Jeśli dopiero zaczynasz Pisanie zapytań w usłudze Log Analytics, zapoznaj się z [sposób użycia na stronie usługi Log Analytics](../log-analytics/query-language/get-started-analytics-portal.md) w witrynie Azure portal, aby pisać zapytania usługi Log Analytics.
* Dowiedz się więcej o [Pisanie zapytań wyszukiwania](../log-analytics/query-language/search-queries.md).