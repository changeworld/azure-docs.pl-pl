---
title: Jak wykonywać zapytania dotyczące dzienników z Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) | Microsoft Docs
description: Azure Monitor dla maszyn wirtualnych rozwiązanie zbiera metryki i dane dziennika do i w tym artykule opisano rekordy i zawiera przykładowe zapytania.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/29/2019
ms.openlocfilehash: 69ed49c0e1b90b4086a40bd15f5d276c6cfe137f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162222"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>Jak wykonywać zapytania dotyczące dzienników z Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)
Azure Monitor dla maszyn wirtualnych zbiera metryki wydajności i połączeń, dane spisu komputerów i procesów oraz informacje o stanie kondycji i przekazuje je do obszaru roboczego Log Analytics w Azure Monitor.  Te dane są dostępne dla [zapytań](../../azure-monitor/log-query/log-query-overview.md) w Azure monitor. Te dane można zastosować do scenariuszy, które obejmują Planowanie migracji, analizę pojemności, odnajdywanie i rozwiązywanie problemów z wydajnością na żądanie.

## <a name="map-records"></a>Mapuj rekordy
Jeden rekord jest generowany na godzinę dla każdego unikatowego komputera i procesu, oprócz rekordów, które są generowane, gdy proces lub komputer zostanie uruchomiony lub jest on dołączony do funkcji Azure Monitor dla maszyn wirtualnych map. Te rekordy mają właściwości w poniższych tabelach. Pola i wartości w zdarzeniach ServiceMapComputer_CL są mapowane na pola zasobu maszyny w interfejsie API usługi ServiceMap Azure Resource Manager. Pola i wartości w zdarzeniach ServiceMapProcess_CL są mapowane na pola zasobu procesu w interfejsie API Azure Resource Manager ServiceMap. Pole ResourceName_s pasuje do pola Name w odpowiednim zasobie Menedżer zasobów. 

Istnieją wewnętrznie wygenerowane właściwości, których można użyć do identyfikowania unikatowych procesów i komputerów:

- Komputer: Użyj *ResourceID* lub *ResourceName_s* w celu jednoznacznego zidentyfikowania komputera w obszarze roboczym log Analytics.
- Proces: Użyj *ResourceID* , aby jednoznacznie zidentyfikować proces w obszarze roboczym log Analytics. *ResourceName_s* jest unikatowy w kontekście maszyny, na której jest uruchomiony proces (MachineResourceName_s) 

Ponieważ wiele rekordów może istnieć dla określonego procesu i komputera w określonym przedziale czasu, kwerendy mogą zwrócić więcej niż jeden rekord dla tego samego komputera lub procesu. Aby uwzględnić tylko najnowszy rekord, Dodaj `| summarize arg_max(TimeGenerated, *) by ResourceId` do zapytania.

### <a name="connections-and-ports"></a>Połączenia i porty
Funkcja metryk połączenia wprowadza dwie nowe tabele w Azure Monitor Logs — VMConnection i VMBoundPort. Te tabele zawierają informacje o połączeniach dla komputera (ruchu przychodzącego i wychodzącego), a także portów serwerów, które są w nich otwarte/aktywne. ConnectionMetrics są również udostępniane za pośrednictwem interfejsów API, które udostępniają metodę uzyskiwania określonej metryki w przedziale czasu. Połączenia TCP wynikające z *zaakceptowania* w gnieździe nasłuchiwania są przychodzące, podczas gdy te utworzone przez *połączenie* z danym adresem IP i portem są wychodzące. Kierunek połączenia jest reprezentowany przez Właściwość Direction, która może być ustawiona na wartość **przychodzące** lub **wychodzące**. 

Rekordy w tych tabelach są generowane na podstawie danych zgłaszanych przez Agent zależności. Każdy rekord reprezentuje obserwację w przedziale czasowym 1 minuty. Właściwość TimeGenerated wskazuje początek interwału czasu. Każdy rekord zawiera informacje służące do identyfikacji odpowiedniej jednostki, czyli połączenia lub portu, a także metryk skojarzonych z tą jednostką. Obecnie zgłaszane jest tylko działanie sieciowe, które odbywa się przy użyciu protokołu TCP przez IPv4. 

#### <a name="common-fields-and-conventions"></a>Wspólne pola i konwencje 
Następujące pola i konwencje dotyczą zarówno VMConnection, jak i VMBoundPort: 

- Komputer: w pełni kwalifikowana nazwa domeny komputera raportowania 
- Identyfikator agenta: unikatowy identyfikator dla komputera z agentem Log Analytics  
- Maszyna: nazwa zasobu Azure Resource Manager dla maszyny uwidocznionej przez ServiceMap. Ma postać *m-{GUID}* , gdzie *GUID* jest tym samym identyfikatorem GUID co identyfikator agenta  
- Proces: nazwa zasobu Azure Resource Manager dla procesu uwidocznionego przez ServiceMap. Ma postać *p-{szesnastkowy ciąg}* . Proces jest unikatowy w ramach zakresu maszyn i do generowania unikatowego identyfikatora procesu między maszynami, łączenia pól maszyn i procesów. 
- ProcessName: nazwa pliku wykonywalnego procesu raportowania.
- Wszystkie adresy IP są ciągami w formacie kanonicznym IPv4, na przykład *13.107.3.160* 

Aby zarządzać kosztami i złożonością, rekordy połączeń nie reprezentują poszczególnych fizycznych połączeń sieciowych. Wiele połączeń sieci fizycznych jest zgrupowanych w połączenie logiczne, które jest następnie odzwierciedlone w odpowiedniej tabeli.  Oznacza to, że rekordy w tabeli *VMConnection* reprezentują logiczne grupowanie, a nie poszczególne połączenia fizyczne, które są obserwowane. Fizyczne połączenie sieciowe udostępniające tę samą wartość dla następujących atrybutów podczas danego interwału jednominutowego są agregowane w jednym rekordzie logicznym w *VMConnection*. 

| Właściwość | Opis |
|:--|:--|
|Kierunek |Kierunek połączenia, wartość jest *przychodząca* lub *wychodząca* |
|Maszyna |Nazwa FQDN komputera |
|Proces |Tożsamość procesu lub grup procesów, inicjowanie/zaakceptowanie połączenia |
|SourceIp |Adres IP źródła |
|DestinationIp |Adres IP miejsca docelowego |
|DestinationPort |Numer portu miejsca docelowego |
|Protocol (Protokół) |Protokół używany do nawiązywania połączenia.  Wartości to *TCP*. |

W celu uwzględnienia wpływu grupowania informacje o liczbie zgrupowanych połączeń fizycznych są dostępne w następujących właściwościach rekordu:

| Właściwość | Opis |
|:--|:--|
|LinksEstablished |Liczba połączeń sieci fizycznych, które zostały nawiązane w przedziale czasu raportowania |
|LinksTerminated |Liczba połączeń sieci fizycznych, które zostały przerwane w przedziale czasu raportowania |
|LinksFailed |Liczba fizycznych połączeń sieciowych zakończonych niepowodzeniem w przedziale czasu raportowania. Te informacje są obecnie dostępne tylko dla połączeń wychodzących. |
|LinksLive |Liczba otwartych połączeń sieciowych w momencie zakończenia przedziału czasu raportowania|

#### <a name="metrics"></a>Metryki

Oprócz metryk licznik połączeń informacje o ilości danych wysłanych i odebranych w danym połączeniu logicznym lub porcie sieciowym są również zawarte w następujących właściwościach rekordu:

| Właściwość | Opis |
|:--|:--|
|BytesSent |Łączna liczba bajtów wysłanych w przedziale czasu raportowania |
|BytesReceived |Łączna liczba bajtów odebranych w przedziale czasu raportowania |
|Odpowiedzi |Liczba odpowiedzi zaobserwowanych w przedziale czasu raportowania. 
|ResponseTimeMax |Największy czas odpowiedzi (w milisekundach) zaobserwowany w przedziale czasu raportowania. W przypadku braku wartości właściwość jest pusta.|
|ResponseTimeMin |Najmniejszy czas odpowiedzi (w milisekundach) zaobserwowany w przedziale czasu raportowania. W przypadku braku wartości właściwość jest pusta.|
|ResponseTimeSum |Suma wszystkich czasów odpowiedzi (w milisekundach) zaobserwowanych w przedziale czasu raportowania. W przypadku braku wartości właściwość jest pusta.|

Zgłaszany trzeci typ danych to czas odpowiedzi — jak długo obiekt wywołujący oczekuje na żądanie wysłane przez połączenie, które ma zostać przetworzone przez zdalny punkt końcowy. Raportowany czas odpowiedzi jest oszacowaniem rzeczywistego czasu odpowiedzi podstawowego protokołu aplikacji. Jest on obliczany przy użyciu algorytmów heurystycznych na podstawie obserwacji przepływu danych między źródłowym i docelowym końcem połączenia sieciowego. Ze pojęciową różnicą jest różnica między czasem, gdy ostatni bajt żądania opuszcza nadawcę, oraz czas, kiedy ostatni bajt odpowiedzi trafia do niego. Te dwa sygnatury czasowe służą do odróżnić zdarzeń żądań i odpowiedzi dla danego połączenia fizycznego. Różnica między nimi reprezentuje czas odpowiedzi pojedynczego żądania. 

W pierwszej wersji tej funkcji nasz algorytm to przybliżenie, które może współdziałać z różnymi poziomami sukcesu w zależności od rzeczywistego protokołu aplikacji używanego dla danego połączenia sieciowego. Na przykład bieżące podejście działa dobrze w przypadku protokołów opartych na odpowiedzi na żądania, takich jak HTTP (S), ale nie działa z protokołami jednokierunkowymi lub opartymi na kolejkach komunikatów.

Oto kilka istotnych kwestii, które należy wziąć pod uwagę:

1. Jeśli proces akceptuje połączenia o tym samym adresie IP, ale przez wiele interfejsów sieciowych, zostanie zgłoszony oddzielny rekord dla każdego interfejsu. 
2. Rekordy z symbolem wieloznacznym adresu IP nie będą miały aktywności. Są one dołączone do reprezentowania faktu, że port na komputerze jest otwarty dla ruchu przychodzącego.
3. Aby zmniejszyć poziom szczegółowości i ilości danych, rekordy z symbolem wieloznacznym adresu IP zostaną pominięte, gdy istnieje pasujący rekord (dla tego samego procesu, portu i protokołu) o określonym adresie IP. W przypadku pominięcia wieloznacznego rekordu adresu IP Właściwość rekordu IsWildcardBind z określonym adresem IP zostanie ustawiona na wartość "true", aby wskazać, że port jest narażony na każdy interfejs maszyny raportowania.
4. Porty, które są powiązane tylko z określonym interfejsem, mają IsWildcardBind ustawiony na *wartość false*.

#### <a name="naming-and-classification"></a>Nazewnictwo i Klasyfikacja
Dla wygody adres IP zdalnego końca połączenia jest zawarty we właściwości RemoteIp. W przypadku połączeń przychodzących RemoteIp jest taka sama jak SourceIp, natomiast w przypadku połączeń wychodzących jest taka sama jak DestinationIp. Właściwość RemoteDnsCanonicalNames reprezentuje nazwy kanoniczne DNS zgłoszone przez komputer dla RemoteIp. Właściwości RemoteDnsQuestions i RemoteClassification są zastrzeżone do użytku w przyszłości. 

#### <a name="geolocation"></a>Geolokalizacja
*VMConnection* również zawiera informacje o geolokalizacji dla zdalnego końca każdego rekordu połączenia w następujących właściwościach rekordu: 

| Właściwość | Opis |
|:--|:--|
|RemoteCountry |Nazwa kraju/regionu, w którym znajduje się RemoteIp.  Na przykład *Stany Zjednoczone* |
|RemoteLatitude |Geolokalizacja geograficzna. Na przykład *47,68* |
|RemoteLongitude |Długość geograficzna geolokalizacji. Na przykład *-122,12* |

#### <a name="malicious-ip"></a>Złośliwy adres IP
Każda właściwość RemoteIp w tabeli *VMConnection* jest sprawdzana względem zestawu adresów IP ze znaną złośliwym działaniem. Jeśli RemoteIp zostanie zidentyfikowany jako złośliwe, zostaną wypełnione następujące właściwości (są puste, jeśli adres IP nie jest uważany za złośliwy) w następujących właściwościach rekordu:

| Właściwość | Opis |
|:--|:--|
|MaliciousIp |Adres RemoteIp |
|IndicatorThreadType |Wykryty wskaźnik zagrożeń to jedna z następujących wartości: *botnet*, *C2*, *CryptoMining*, *sieci darknet*, *DDoS*, *MaliciousUrl*, *złośliwe oprogramowanie*, *phishing*, *proxy*, *opierająca*,  *Listy do obejrzenia*.   |
|Opis |Opis zaobserwowanego zagrożenia. |
|TLPLevel |Poziom protokołu Traffic Light Protocol (TLP) to jedna ze zdefiniowanych wartości: *biały*, *zielony*, *bursztynowy*, *czerwony*. |
|zachowanie |Wartości to *0 – 100*. |
|Ważność |Wartości to *0 – 5*, gdzie *5* jest największa i *0* nie jest poważny. Wartość domyślna to *3*.  |
|FirstReportedDateTime |Pierwszy raz dostawca zgłosił wskaźnik. |
|LastReportedDateTime |Ostatni czas widziany przez przepływanie wskaźnika. |
|IsActive |Wskazuje, że wskaźniki są dezaktywowane z wartością *true* lub *false* . |
|ReportReferenceLink |Linki do raportów związanych z tym, że jest to zauważalne. |
|additionalInformation |Zawiera dodatkowe informacje o zaobserwowanym zagrożeniu, jeśli ma to zastosowanie. |

### <a name="ports"></a>Porty 
Porty na komputerze, który aktywnie akceptują ruch przychodzący lub potencjalnie mogą akceptować ruch, ale są bezczynne w przedziale czasu raportowania, są zapisywane w tabeli VMBoundPort.  

Każdy rekord w VMBoundPort jest identyfikowany przez następujące pola: 

| Właściwość | Opis |
|:--|:--|
|Proces | Tożsamość procesu (lub grup procesów), z którym jest skojarzony port.|
|Przegląd | Adres IP portu (może to być symbol wieloznaczny adresu IP, *0.0.0.0*) |
|Port |Numer portu |
|Protocol (Protokół) | Protokół.  Przykład: *TCP* lub *UDP* (tylko *protokół TCP* jest obecnie obsługiwany).|
 
Tożsamość, którą port pochodzi od powyższych pięciu pól i jest przechowywana we właściwości identyfikator portu. Ta właściwość może służyć do szybkiego znajdowania rekordów dla określonego portu w czasie. 

#### <a name="metrics"></a>Metryki 
Rekordy portów obejmują metryki reprezentujące powiązane z nimi połączenia. Obecnie zgłaszane są następujące metryki (szczegóły dotyczące poszczególnych metryk są opisane w poprzedniej sekcji): 

- BytesSent i BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Oto kilka istotnych kwestii, które należy wziąć pod uwagę:

- Jeśli proces akceptuje połączenia o tym samym adresie IP, ale przez wiele interfejsów sieciowych, zostanie zgłoszony oddzielny rekord dla każdego interfejsu.  
- Rekordy z symbolem wieloznacznym adresu IP nie będą miały aktywności. Są one dołączone do reprezentowania faktu, że port na komputerze jest otwarty dla ruchu przychodzącego. 
- Aby zmniejszyć poziom szczegółowości i ilości danych, rekordy z symbolem wieloznacznym adresu IP zostaną pominięte, gdy istnieje pasujący rekord (dla tego samego procesu, portu i protokołu) o określonym adresie IP. W przypadku pominięcia wieloznacznego rekordu adresu IP Właściwość *IsWildcardBind* rekordu z określonym adresem IP zostanie ustawiona na *wartość true*.  Oznacza to, że port jest narażony na każdy interfejs maszyny raportowania. 
- Porty, które są powiązane tylko z określonym interfejsem, mają IsWildcardBind ustawiony na *wartość false*. 

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL rekordy
Rekordy z typem *ServiceMapComputer_CL* mają dane spisu dla serwerów z agentem zależności. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
| Typ | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Unikatowy identyfikator maszyny w obszarze roboczym |
| ResourceName_s | Unikatowy identyfikator maszyny w obszarze roboczym |
| ComputerName_s | Nazwa FQDN komputera |
| Ipv4Addresses_s | Lista adresów IPv4 serwera |
| Ipv6Addresses_s | Lista adresów IPv6 serwera |
| DnsNames_s | Tablica nazw DNS |
| OperatingSystemFamily_s | System Windows lub Linux |
| OperatingSystemFullName_s | Pełna nazwa systemu operacyjnego  |
| Bitness_s | Liczba bitów maszyny (32-bitowa lub 64-bitowa)  |
| PhysicalMemory_d | Pamięć fizyczna w MB |
| Cpus_d | Liczba procesorów CPU |
| CpuSpeed_d | Szybkość procesora CPU (w MHz)|
| VirtualizationState_s | *nieznane*, *fizyczne*, *wirtualne*, *Funkcja hypervisor* |
| VirtualMachineType_s | *HyperV*, *VMware*i tak dalej |
| VirtualMachineNativeMachineId_g | Identyfikator maszyny wirtualnej przypisany przez funkcję hypervisor |
| VirtualMachineName_s | Nazwa maszyny wirtualnej |
| BootTime_t | Czas rozruchu |

### <a name="servicemapprocess_cl-type-records"></a>ServiceMapProcess_CL typy rekordów
Rekordy z typem *ServiceMapProcess_CL* mają dane spisu dla procesów połączonych z protokołem TCP na serwerach z agentem zależności. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
| Typ | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Unikatowy identyfikator procesu w obszarze roboczym |
| ResourceName_s | Unikatowy identyfikator procesu znajdującego się na komputerze, na którym jest uruchomiony|
| MachineResourceName_s | Nazwa zasobu maszyny |
| ExecutableName_s | Nazwa pliku wykonywalnego procesu |
| StartTime_t | Godzina rozpoczęcia puli procesów |
| FirstPid_d | Pierwszy Identyfikator PID w puli procesów |
| Description_s | Opis procesu |
| CompanyName_s | Nazwa firmy |
| InternalName_s | Nazwa wewnętrzna |
| ProductName_s | Nazwa produktu |
| ProductVersion_s | Wersja produktu |
| FileVersion_s | Wersja pliku |
| CommandLine_s | Wiersz polecenia |
| ExecutablePath_s | Ścieżka do pliku wykonywalnego. |
| WorkingDirectory_s | Katalog roboczy |
| Uż | Konto, na którym proces jest wykonywany |
| UserDomain | Domena, w której wykonywany jest proces |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

### <a name="list-all-known-machines"></a>Wyświetl listę wszystkich znanych maszyn
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Kiedy maszyna wirtualna została ostatnio uruchomiona ponownie
```kusto
let Today = now(); ServiceMapComputer_CL | extend DaysSinceBoot = Today - BootTime_t | summarize by Computer, DaysSinceBoot, BootTime_t | sort by BootTime_t asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Podsumowanie maszyn wirtualnych platformy Azure według obrazu, lokalizacji i jednostki SKU
```kusto
ServiceMapComputer_CL | where AzureLocation_s != "" | summarize by ComputerName_s, AzureImageOffering_s, AzureLocation_s, AzureImageSku_s
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Wyświetl listę pojemności pamięci fizycznej wszystkich zarządzanych komputerów.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s
```

### <a name="list-computer-name-dns-ip-and-os"></a>Wyświetl listę nazw komputerów, DNS, IP i systemu operacyjnego.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Znajdź wszystkie procesy z "SQL" w wierszu polecenia
```kusto
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Znajdź maszynę (najnowszy rekord) według nazwy zasobu
```kusto
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Znajdź komputer (najnowszy rekord) według adresu IP
```kusto
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Wyświetl listę wszystkich znanych procesów na określonym komputerze
```kusto
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Wyświetl listę wszystkich komputerów z systemem SQL Server
```kusto
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Wyświetl listę wszystkich unikatowych wersji programu zwinięcie w moim centrum danych
```kusto
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Utwórz grupę komputerów z systemem CentOS
```kusto
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s
```

### <a name="bytes-sent-and-received-trends"></a>Liczba bajtów wysłanych i otrzymanych trendów
```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Które maszyny wirtualne platformy Azure przesyłają najwięcej bajtów
```kusto
VMConnection | join kind=fullouter(ServiceMapComputer_CL) on $left.Computer == $right.ComputerName_s | summarize count(BytesSent) by Computer, AzureVMSize_s | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Trendy stanu łącza
```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Trend błędów połączeń
```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Powiązane porty
```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Liczba otwartych portów między maszynami
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Ocena procesów w obszarze roboczym według liczby otwartych portów
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Zagregowane zachowanie dla każdego portu
Tego zapytania można następnie użyć do oceny portów według aktywności, np. portów z większością ruchu przychodzącego/wychodzącego, porty z większością połączeń
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Podsumuj połączenia wychodzące z grupy maszyn
```kusto
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

## <a name="next-steps"></a>Następne kroki
* Jeśli jesteś nowym sposobem pisania zapytań dzienników w Azure Monitor, zapoznaj się z [tematem jak używać log Analytics](../../azure-monitor/log-query/get-started-portal.md) w Azure Portal do zapisywania zapytań dzienników.

* Dowiedz się więcej na temat [pisania zapytań wyszukiwania](../../azure-monitor/log-query/search-queries.md).
