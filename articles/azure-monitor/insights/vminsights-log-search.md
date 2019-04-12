---
title: Jak dzienniki zapytań z usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Usługa Azure Monitor dla maszyn wirtualnych rozwiązania zbiera metryki i dane dziennika, aby w tym artykule opisano rekordy i zawiera przykładowe zapytania.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: magoedte
ms.openlocfilehash: 8b6745a2b9afe8d3101585e3f7a13f2fc978c84a
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492092"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>Tworzenie zapytań względem dzienników z usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)
Usługa Azure Monitor dla maszyn wirtualnych umożliwia zbieranie informacji o wydajności i metryki połączeń, komputera i przetwarzanie magazynu danych i informacji o stanie kondycji i przekazuje go do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor.  Dane te są dostępne dla [zapytania](../../azure-monitor/log-query/log-query-overview.md) w usłudze Azure Monitor. Dane te można zastosować do scenariuszy obejmujących planowania migracji, analizy wydajności, wykrywanie i rozwiązywanie problemów z wydajnością na żądanie.

## <a name="map-records"></a>Rekordy mapy
Jeden rekord jest generowany na godzinę dla każdego komputera unikatowy i procesów, oprócz rekordów, które są generowane, gdy proces lub komputer uruchamia lub jest uruchomiona do usługi Azure Monitor dla funkcji mapy maszyn wirtualnych. Te rekordy mają właściwości podane w poniższych tabelach. Pola i wartości w zdarzeniach ServiceMapComputer_CL są mapowane na pola zasób maszynę w interfejsie API ServiceMap usługi Azure Resource Manager. Pola i wartości w zdarzeniach ServiceMapProcess_CL są mapowane na pola zasobów procesu w interfejsie API ServiceMap usługi Azure Resource Manager. Pole ResourceName_s zgodne pola Nazwa odpowiedniego zasobu usługi Resource Manager. 

Istnieją wewnętrznie generowane właściwości, które służy do identyfikacji procesów unikatowy i komputerów:

- Komputer: Użyj *ResourceId* lub *ResourceName_s* do unikatowej identyfikacji komputera w obszarze roboczym usługi Log Analytics.
- Proces: Użyj *ResourceId* do unikatowej identyfikacji procesów w obrębie obszaru roboczego usługi Log Analytics. *ResourceName_s* jest unikatowa w obrębie kontekstu komputera, na którym proces działa (MachineResourceName_s) 

Ponieważ wiele rekordów może istnieć dla określonego procesu i komputera w określonym okresie, zapytania może zwrócić więcej niż jeden rekord dla tego samego komputera lub procesu. Aby dołączyć tylko najnowsze rekord, należy dodać "| Funkcja deduplikacji ResourceId"zapytania.

### <a name="connections-and-ports"></a>Połączenia i porty
Funkcja metryki połączeń wprowadza dwa nowe tabele w dzienniki usługi Azure Monitor — VMConnection i VMBoundPort. Te tabele zawierają informacje dotyczące połączenia dla komputera (przychodzące i wychodzące), a także serwer portów, które są otwarte/aktywny na nich. ConnectionMetrics również są udostępniane za pośrednictwem interfejsów API, które zapewniają środki do uzyskania określona Metryka przedziale czasu. Połączenia protokołu TCP, wynikające z *akceptowanie* na gniazdo nasłuchiwania są dla ruchu przychodzącego, a te utworzone przez *łączenie* danego adresu IP i portu są ruchu wychodzącego. Kierunek połączenie jest reprezentowane przez właściwość "Direction", który może być ustawiony na **dla ruchu przychodzącego** lub **wychodzących**. 

Rekordów w tych tabelach są generowane na podstawie danych przekazywanych przez agenta zależności. Każdy rekord reprezentuje obserwacji w przedziałach czasu 1 minuty. Właściwość TimeGenerated wskazuje początek przedziału czasu. Każdy rekord zawiera informacje w celu zidentyfikowania odpowiednich jednostek, to znaczy, połączenia lub portu, a także metryki skojarzone z tej jednostki. Obecnie jest zgłaszany tylko aktywność sieciową występujący za pomocą protokołu TCP za pośrednictwem protokołu IPv4. 

#### <a name="common-fields-and-conventions"></a>Typowe pola i konwencje 
Poniższe pola i konwencje dotyczą zarówno VMConnection, jak i VMBoundPort: 

- Komputer: W pełni kwalifikowaną nazwę domeny z raportowaniem daje używanie maszyny 
- AgentID: Unikatowy identyfikator dla komputera z agentem usługi Log Analytics  
- Komputer: Nazwa zasobu usługi Azure Resource Manager dla maszyny, udostępnianych przez ServiceMap. Ma on postać *m-{GUID}*, gdzie *GUID* jest tego samego identyfikatora GUID jako identyfikator agenta  
- Proces: Nazwa zasobu usługi Azure Resource Manager dla procesu udostępnianych przez ServiceMap. Ma on postać *p-{ciąg szesnastkowy}*. Proces jest unikatowa w zakresie maszyny i można wygenerować unikatowy identyfikator procesu na komputerach, należy połączyć maszyny i proces pól. 
- ProcessName: Nazwa pliku wykonywalnego procesu raportowania.
- Wszystkie adresy IP są ciągami w formacie kanonicznym IPv4, na przykład *13.107.3.160* 

Aby zarządzać, koszt i złożoność, rekordy połączeń nie przedstawiają poszczególnych fizycznych połączeń sieciowych. Wiele połączeń sieci fizycznej są grupowane w logicznej połączenia, które następnie są odzwierciedlane w odpowiedniej tabeli.  Znaczenie, rejestruje w *VMConnection* tabeli reprezentują logiczne grupowanie, a nie poszczególnych fizycznego połączenia, które są przestrzegane. Połączenie z siecią fizyczną współużytkujące taką samą wartość, w przypadku następujących atrybutów dla danego interwału jednej minuty są agregowane w jednym rekordzie logicznych w *VMConnection*. 

| Właściwość | Opis |
|:--|:--|
|Kierunek |Kierunek połączenia, wartość jest *dla ruchu przychodzącego* lub *ruchu wychodzącego* |
|Maszyna |Nazwa FQDN komputera |
|Przetwórz |Tożsamość procesu lub grupy procesów, inicjowanie/akceptować połączenia |
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
|BytesSent |Całkowita liczba bajtów wysłanych raportowania przedziale czasu |
|BytesReceived |Całkowita liczba bajtów odebranych w przedziale czasu raportowania |
|Odpowiedzi |Liczba odpowiedzi zaobserwowane w przedziale czasu raportowania. 
|ResponseTimeMax |Największy czas odpowiedzi (w milisekundach) zaobserwowane w przedziale czasu raportowania. Jeśli brak wartości właściwości jest pusta.|
|ResponseTimeMin |Najmniejszy czas odpowiedzi (w milisekundach) zaobserwowane w przedziale czasu raportowania. Jeśli brak wartości właściwości jest pusta.|
|ResponseTimeSum |Suma czasów odpowiedzi (w milisekundach) zaobserwowane w przedziale czasu raportowania. Jeśli brak wartości właściwości jest pusta.|

Trzeci typ danych, zgłaszany jest czas odpowiedzi — obiekt wywołujący poświęcić czas oczekiwania na żądanie przesyłane przez połączenie do przetworzenia i przez zdalny punkt końcowy. Czas odpowiedzi, zgłaszane jest oszacowanie czas odpowiedzi true podstawowego protokołu aplikacji. Jest obliczana, przy użyciu algorytmów heurystycznych oparte na obserwacji przepływ danych między źródłowym i docelowym koniec połączenie z siecią fizyczną. Pod względem koncepcyjnym go różni się od czasu ostatniego bajtu żądania pozostawia nadawcy i czas po nadejściu ostatni bajt odpowiedzi powrotu do tego. Te dwa sygnatury czasowe są używane do odróżnić zdarzenia żądań i odpowiedzi dla danego połączenia fizycznych. Różnica między nimi reprezentuje czas odpowiedzi pojedynczego żądania. 

W pierwszej wersji tej funkcji nasze algorytm jest przybliżeniem, która może działać z dowolnym stopniu sukcesu w zależności od rzeczywistej aplikacji protokół dla połączenia określonej sieci. Na przykład bieżącego podejścia działa dobrze w przypadku protokołów odpowiedź na żądanie na podstawie takich jak HTTP (S), ale nie pracować jednokierunkowe lub odpowiednich protokołów opartych na kolejki komunikatów.

Oto niektóre ważne punkty, które należy wziąć pod uwagę:

1. Jeśli proces akceptuje połączenia na ten sam adres IP, ale w wielu interfejsów sieciowych, zostanie zgłoszony oddzielny rekord dla każdego interfejsu. 
2. Rekordy z symbolami wieloznacznymi IP będzie zawierać żadnych działań. Zostaną one dołączone do reprezentowania fakt, że port na maszynie jest otwarty dla ruchu przychodzącego ruchu sieciowego.
3. Aby zmniejszyć poziom szczegółowości i ilość danych, rekordów za pomocą adresu IP z symbolami wieloznacznymi zostaną pominięte po pasującego rekordu (w przypadku tego samego procesu, portów i protokołów) przy użyciu określonego adresu IP. Gdy rekord IP symbol wieloznaczny zostanie pominięty, IsWildcardBind właściwości rekordu z określonego adresu IP ustawi na wartość "True" do wskazania, że port jest uwidaczniany przez każdy interfejs raportowania maszyny.
4. Porty, które są powiązane tylko dla określonego interfejsu mają IsWildcardBind równa *False*.

#### <a name="naming-and-classification"></a>Nazewnictwo i klasyfikacji
Dla wygody adres IP zdalnego punktu końcowego połączenia znajduje się we właściwości RemoteIp. Dla połączeń przychodzących RemoteIp jest taka sama jak SourceIp, natomiast w przypadku połączeń wychodzących, jest taka sama jak DestinationIp. Właściwość RemoteDnsCanonicalNames reprezentuje zgłoszone przez maszynę dla RemoteIp nazwy kanonicznej DNS. Właściwości RemoteDnsQuestions i RemoteClassification są zarezerwowane do użytku w przyszłości. 

#### <a name="geolocation"></a>Geolokalizacja
*VMConnection* zawiera również informacje o geolokalizacji na drugim końcu każdego wybranego rekordu połączenia w następujących właściwości rekordu: 

| Właściwość | Opis |
|:--|:--|
|RemoteCountry |Nazwa kraju RemoteIp hostingu.  Na przykład *Stanów Zjednoczonych* |
|RemoteLatitude |Szerokość geograficzną. Na przykład *47.68* |
|RemoteLongitude |Długość geograficzna geolokalizacji. Na przykład *-122.12* |

#### <a name="malicious-ip"></a>Złośliwy adres IP
Dla każdej właściwości RemoteIp w *VMConnection* tabeli jest sprawdzana względem zbiór adresów IP przy użyciu znanych złośliwych działań. Jeśli RemoteIp została zidentyfikowana jako szkodliwe następujące właściwości zostaną wypełnione (są puste, jeśli adres IP nie jest uznawane za złośliwe) w następujących właściwości rekordu:

| Właściwość | Opis |
|:--|:--|
|MaliciousIp |Adres RemoteIp |
|IndicatorThreadType |Wskaźnik zagrożenia wykryte jest jednym z następujących wartości *Botnet*, *C2*, *CryptoMining*, *zakres sieci Darknet*, *przed atakami DDos* , *MaliciousUrl*, *złośliwego oprogramowania*, *wyłudzania informacji*, *Proxy*, *potencjalnie niechciane aplikacje*, *Listy do obejrzenia*.   |
|Opis |Opis obserwowanych zagrożeń. |
|TLPLevel |Poziom Rack (RECENT sygnalizacji ulicznej Protocol) jest jednym ze zdefiniowanymi wartościami *biały*, *zielony*, *żółtą*, *Red*. |
|Ufność |Wartości są *0 – 100*. |
|Ważność |Wartości są *0 – 5*, gdzie *5* jest najpoważniejsze i *0* nie jest poważny wcale. Wartość domyślna to *3*.  |
|FirstReportedDateTime |Po raz pierwszy dostawca zgłosił wskaźnika. |
|LastReportedDateTime |Czas ostatniego wskaźnika widzianego przez Interflow. |
|IsActive |Wskazuje, wskaźniki zostaną dezaktywowane z *True* lub *False* wartość. |
|ReportReferenceLink |Zawiera łącza do raportów związanych z danym możliwość obserwowania. |
|AdditionalInformation |Udostępnia dodatkowe informacje, jeśli ma to zastosowanie, temat obserwowanych zagrożenia. |

### <a name="ports"></a>Porty 
Porty na maszynie, które aktywnie akceptuje ruch przychodzący lub potencjalnie może akceptować ruch, ale są w stanie bezczynności raportowania przedziale czasu, są zapisywane w tabeli VMBoundPort.  

>[!NOTE]
>Usługa Azure Monitor dla maszyn wirtualnych nie obsługuje gromadzenia i rejestrowania danych portu w obszarze roboczym usługi Log Analytics w następujących regionach:  
>- Wschodnie stany USA  
>- Europa Zachodnia
>
> Zbieranie tych danych jest włączone w drugim [obsługiwane regiony](vminsights-onboard.md#log-analytics) monitora platformy Azure dla maszyn wirtualnych. 

Każdy rekord w VMBoundPort jest identyfikowane za pomocą następujących pól: 

| Właściwość | Opis |
|:--|:--|
|Przetwórz | Tożsamość procesu (lub grupy procesów), z którymi jest skojarzony z portu.|
|Adres IP | Port adresu IP (mogą być symbolami wieloznacznymi adres IP, *0.0.0.0*) |
|Port |Numer portu |
|Protokół | Protokół.  Przykład *tcp* lub *udp* (tylko *tcp* jest obecnie obsługiwane).|
 
Tożsamość portu jest tworzony na podstawie powyższych pięć pól i jest przechowywany we właściwości identyfikator portu. Ta właściwość umożliwia szybkie znajdowanie rekordy na określonym porcie w czasie. 

#### <a name="metrics"></a>Metryki 
Port rekordy obejmują reprezentujące połączenia skojarzone z nimi metryk. Obecnie następujące metryki są zgłaszane (szczegółowe informacje dotyczące każdego metryki są opisane w poprzedniej sekcji): 

- Żądania i BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Oto niektóre ważne punkty, które należy wziąć pod uwagę:

- Jeśli proces akceptuje połączenia na ten sam adres IP, ale w wielu interfejsów sieciowych, zostanie zgłoszony oddzielny rekord dla każdego interfejsu.  
- Rekordy z symbolami wieloznacznymi IP będzie zawierać żadnych działań. Zostaną one dołączone do reprezentowania fakt, że port na maszynie jest otwarty dla ruchu przychodzącego ruchu sieciowego. 
- Aby zmniejszyć poziom szczegółowości i ilość danych, rekordów za pomocą adresu IP z symbolami wieloznacznymi zostaną pominięte po pasującego rekordu (w przypadku tego samego procesu, portów i protokołów) przy użyciu określonego adresu IP. Gdy rekord IP symbol wieloznaczny zostanie pominięty, *IsWildcardBind* właściwość dla rekordu z określonego adresu IP zostanie ustawiona *True*.  Oznacza to, że port jest uwidaczniany przez każdy interfejs raportowania maszyny. 
- Porty, które są powiązane tylko dla określonego interfejsu mają IsWildcardBind równa *False*. 

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
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="when-was-the-vm-last-rebooted"></a>Kiedy został maszyny Wirtualnej ostatniego ponownego rozruchu
```kusto
let Today = now(); ServiceMapComputer_CL | extend DaysSinceBoot = Today - BootTime_t | summarize by Computer, DaysSinceBoot, BootTime_t | sort by BootTime_t asc`
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Podsumowanie dotyczące maszyn wirtualnych platformy Azure według obrazu, lokalizacji i jednostki SKU
```kusto
ServiceMapComputer_CL | where AzureLocation_s != "" | summarize by ComputerName_s, AzureImageOffering_s, AzureLocation_s, AzureImageSku_s`
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Wyświetl listę wszystkich zarządzanych komputerów pojemność pamięci fizycznej.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`
```

### <a name="list-computer-name-dns-ip-and-os"></a>Nazwa komputera listy, DNS, adresu IP i systemu operacyjnego.
```kusto
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Znajdź wszystkie procesy za pomocą "sql" w wierszu polecenia
```kusto
ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Znajdź maszyny (najnowsze rekordu) według nazwy zasobu
```kusto
search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Znajdź maszyny (najnowsze rekordu) przy użyciu adresu IP
```kusto
search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Listę wszystkich znanych procesów na określonym komputerze
```kusto
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`
```

### <a name="list-all-computers-running-sql-server"></a>Lista wszystkich komputerów z uruchomionym programem SQL Server
```kusto
ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Wyświetla listę wszystkich wersji produktu unikatowy programu curl w mojego centrum danych
```kusto
ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Utwórz grupę komputerów wszystkich komputerów z systemem CentOS
```kusto
ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`
```

### <a name="bytes-sent-and-received-trends"></a>Liczba bajtów wysłanych i odebranych trendów
```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart`
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Które maszyny wirtualne platformy Azure są przesyłania najbardziej bajtów
```kusto
VMConnection | join kind=fullouter(ServiceMapComputer_CL) on $left.Computer == $right.ComputerName_s | summarize count(BytesSent) by Computer, AzureVMSize_s | sort by count_BytesSent desc`
```

### <a name="link-status-trends"></a>Trendy stan łącza
```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart`
```

### <a name="connection-failures-trend"></a>Trend błędów połączenia
```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart`
```

### <a name="bound-ports"></a>Powiązane z portami
```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Liczba otwartych portów na komputerach
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Ocena procesów w obszarze roboczym przez liczbę portów, które mają otwarte
```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Zachowanie agregacji dla poszczególnych portów
To zapytanie następnie służy do oceniania portów przez działanie, np. porty większość ruchu przychodzącego/wychodzącego, portów przy użyciu większość połączeń
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Podsumowanie połączenia wychodzące z grupy maszyn
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

## <a name="next-steps"></a>Kolejne kroki
* Jeśli dopiero zaczynasz Pisanie zapytań dzienników w usłudze Azure Monitor, zapoznaj się z [jak używać usługi Log Analytics](../../azure-monitor/log-query/get-started-portal.md) w witrynie Azure portal do pisania zapytań log.
* Dowiedz się więcej o [Pisanie zapytań wyszukiwania](../../azure-monitor/log-query/search-queries.md).
