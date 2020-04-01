---
title: Jak kwerendy dzienników z usługi Azure Monitor dla maszyn wirtualnych
description: Rozwiązanie usługi Azure Monitor dla maszyn wirtualnych zbiera metryki i dane dziennika, a w tym artykule opisano rekordy i zawiera przykładowe kwerendy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 61a71539dc034a216689eafd8991df60db96d2a4
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396924"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Jak wysyłać zapytania do dzienników z usługi Azure Monitor dla maszyn wirtualnych

Usługa Azure Monitor dla maszyn wirtualnych zbiera metryki wydajności i połączeń, dane spisu komputerów i procesów oraz informacje o stanie kondycji i przekazuje je do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor.  Te dane są dostępne dla [kwerendy](../../azure-monitor/log-query/log-query-overview.md) w usłudze Azure Monitor. Te dane można zastosować do scenariuszy, które obejmują planowanie migracji, analizę zdolności produkcyjnych, odnajdowanie i rozwiązywanie problemów z wydajnością na żądanie.

## <a name="map-records"></a>Mapowanie rekordów

Jeden rekord jest generowany na godzinę dla każdego unikatowego komputera i procesu, oprócz rekordów, które są generowane, gdy proces lub komputer jest uruchamiany lub jest wbudowany w usługę Azure Monitor dla map maszyn wirtualnych. Te rekordy mają właściwości w poniższych tabelach. Pola i wartości w ServiceMapComputer_CL zdarzenia są mapowane do pól zasobu maszyny w interfejsie API usługi ServiceMap Azure Resource Manager. Pola i wartości w ServiceMapProcess_CL zdarzenia są mapowane do pól zasobu Proces w interfejsie API usługi ServiceMap Azure Resource Manager. Pole ResourceName_s jest zgodne z polem nazwy w odpowiednim zasobie Menedżera zasobów. 

Istnieją wewnętrznie wygenerowane właściwości, których można używać do identyfikowania unikatowych procesów i komputerów:

- Komputer: użyj *identyfikatora resourceid* lub *ResourceName_s,* aby jednoznacznie zidentyfikować komputer w obszarze roboczym usługi Log Analytics.
- Proces: użyj *identyfikatora resourceid,* aby jednoznacznie zidentyfikować proces w obszarze roboczym usługi Log Analytics. *ResourceName_s* jest unikatowa w kontekście maszyny, na której działa proces (MachineResourceName_s) 

Ponieważ dla określonego procesu i komputera w określonym zakresie czasu może istnieć wiele rekordów, kwerendy mogą zwracać więcej niż jeden rekord dla tego samego komputera lub procesu. Aby uwzględnić tylko najnowszy `| summarize arg_max(TimeGenerated, *) by ResourceId` rekord, dodaj do kwerendy.

### <a name="connections-and-ports"></a>Połączenia i porty

Funkcja Metryki połączenia wprowadza dwie nowe tabele w dziennikach usługi Azure Monitor — VMConnection i VMBoundPort. Tabele te zawierają informacje o połączeniach komputera (przychodzące i wychodzące), a także porty serwera, które są na nich otwarte/aktywne. ConnectionMetrics są również udostępniane za pośrednictwem interfejsów API, które zapewniają środki do uzyskania określonej metryki w oknie czasu. Połączenia TCP wynikające z akceptowania na gnieździe *nasłuchiwania* są przychodzące, podczas gdy połączenia *z* danym adresem IP i portem są wychodzące. Kierunek połączenia jest reprezentowany przez właściwość Direction, którą można ustawić na **przychodzącą** lub **wychodzącą**. 

Rekordy w tych tabelach są generowane na podstawie danych zgłoszonych przez agenta zależności. Każdy rekord reprezentuje obserwację w odstępie 1 minuty. TimeGenerated Właściwość wskazuje początek przedziału czasu. Każdy rekord zawiera informacje umożliwiające identyfikację odpowiedniej jednostki, czyli połączenia lub portu, a także metryki skojarzone z tą encją. Obecnie zgłaszana jest tylko aktywność sieciowa występująca przy użyciu protokołu TCP przez protokół IPv4. 

#### <a name="common-fields-and-conventions"></a>Wspólne pola i konwencje 

Następujące pola i konwencje mają zastosowanie zarówno do VMConnection, jak i VMBoundPort: 

- Komputer: w pełni kwalifikowana nazwa domeny urządzenia do raportowania 
- AgentId: unikatowy identyfikator komputera z agentem usługi Log Analytics  
- Komputer: nazwa zasobu usługi Azure Resource Manager dla komputera udostępniane przez ServiceMap. Jest to formularz *m-{GUID}*, gdzie *identyfikator GUID* jest tym samym identyfikatorem GUID co agentid  
- Proces: nazwa zasobu usługi Azure Resource Manager dla procesu udostępnianego przez servicemap. Jest to ciąg *p-{hex}*. Proces jest unikatowy w zakresie maszyny i wygenerować unikatowy identyfikator procesu na komputerach, połączyć pola Maszyny i Proces. 
- Nazwa procesu: nazwa wykonywalna procesu raportowania.
- Wszystkie adresy IP są ciągami w formacie kanonicznym IPv4, na przykład *13.107.3.160* 

Aby zarządzać kosztami i złożonością, rekordy połączeń nie reprezentują pojedynczych fizycznych połączeń sieciowych. Wiele fizycznych połączeń sieciowych jest grupowanych w połączenie logiczne, które jest następnie odzwierciedlane w odpowiedniej tabeli.  Co oznacza, że rekordy w tabeli *VMConnection* reprezentują logiczne grupowanie, a nie poszczególne połączenia fizyczne, które są obserwowane. Fizyczne połączenie sieciowe dzielące tę samą wartość dla następujących atrybutów w danym interwału jednominutowym jest agregowane w jeden rekord logiczny w *vmconnection*. 

| Właściwość | Opis |
|:--|:--|
|Kierunek |Kierunek połączenia, wartość jest *przychodzące* lub *wychodzące* |
|Maszyna |Nazwa FQDN komputera |
|Proces |Tożsamość procesu lub grup procesów, inicjowanie/akceptowanie połączenia |
|Pw.pw. |Adres IP źródła |
|DestinationIp (Ip) |Adres IP miejsca docelowego |
|Port docelowy |Numer portu miejsca docelowego |
|Protocol (Protokół) |Protokół używany do połączenia.  Wartości to *tcp*. |

Aby uwzględnić wpływ grupowania, informacje o liczbie zgrupowanych połączeń fizycznych znajdują się w następujących właściwościach rekordu:

| Właściwość | Opis |
|:--|:--|
|LinkiNaestione |Liczba fizycznych połączeń sieciowych, które zostały nawiązane w oknie czasu raportowania |
|LinksTerminated (ŁączyTerminowane) |Liczba fizycznych połączeń sieciowych, które zostały zakończone w oknie czasu raportowania |
|ŁączaNiewiększy |Liczba fizycznych połączeń sieciowych, które nie powiodły się w czasie raportowania. Te informacje są obecnie dostępne tylko dla połączeń wychodzących. |
|LinksLive (Na żywo) |Liczba fizycznych połączeń sieciowych, które były otwarte na końcu okna czasu raportowania|

#### <a name="metrics"></a>Metryki

Oprócz danych liczby połączeń informacje o ilości danych wysyłanych i odbieranych na danym połączeniu logicznym lub porcie sieciowym są również zawarte w następujących właściwościach rekordu:

| Właściwość | Opis |
|:--|:--|
|Bajtów |Całkowita liczba bajtów wysłanych w okresie raportowania |
|Bytesreceived |Całkowita liczba bajtów odebranych w okresie raportowania |
|Odpowiedzi |Liczba odpowiedzi zaobserwowanych w okresie raportowania. 
|ResponseTimeMax (OdpowiedźTimeMax) |Największy czas odpowiedzi (milisekundy) obserwowany w oknie czasu raportowania. Jeśli nie ma wartości, właściwość jest pusta.|
|ResponseTimeMin (Czas reakcji) |Najmniejszy czas odpowiedzi (milisekundy) obserwowany w oknie czasu raportowania. Jeśli nie ma wartości, właściwość jest pusta.|
|Czas reakcji |Suma wszystkich czasów odpowiedzi (milisekund) obserwowanych w oknie czasu raportowania. Jeśli nie ma wartości, właściwość jest pusta.|

Trzeci typ danych zgłaszanych jest czas odpowiedzi — jak długo obiekt wywołujący spędza oczekiwanie na żądanie wysłane za pomocą połączenia do przetworzenia i odpowiedzi na przez zdalny punkt końcowy. Zgłoszony czas odpowiedzi jest oszacowaniem rzeczywistego czasu odpowiedzi leżącego u podstaw protokołu aplikacji. Jest obliczany przy użyciu heurystyki na podstawie obserwacji przepływu danych między źródłem a docelowym końcem fizycznego połączenia sieciowego. Koncepcyjnie jest to różnica między czasem ostatniego bajtu żądania pozostawia nadawcy, a godziną, kiedy ostatni bajt odpowiedzi powraca do niego. Te dwa sygnatury czasowe są używane do wytyczyć zdarzenia żądania i odpowiedzi na danym połączeniu fizycznym. Różnica między nimi reprezentuje czas odpowiedzi pojedynczego żądania. 

W tej pierwszej wersji tej funkcji nasz algorytm jest przybliżeniem, które może działać z różnym powodzeniem w zależności od rzeczywistego protokołu aplikacji używanego dla danego połączenia sieciowego. Na przykład bieżące podejście działa dobrze dla protokołów opartych na odpowiedzi na żądanie, takich jak HTTP(S), ale nie działa z protokołami opartymi na kolejkach jednokierunkowych lub wiadomości.

Oto kilka ważnych kwestii, które należy wziąć pod uwagę:

1. Jeśli proces akceptuje połączenia na tym samym adresie IP, ale za pośrednictwem wielu interfejsów sieciowych, zostanie zgłoszony osobny rekord dla każdego interfejsu. 
2. Rekordy z wieloznacznym adresem IP nie będą zawierać żadnych działań. Są one włączone do reprezentowania faktu, że port na komputerze jest otwarty dla ruchu przychodzącego.
3. Aby zmniejszyć szczegółowość i ilość danych, rekordy z wieloznacznym adresem IP zostaną pominięte, gdy istnieje pasujący rekord (dla tego samego procesu, portu i protokołu) z określonym adresem IP. Po pominięciu rekordu IP symboli wieloznacznych właściwość rekordu IsWildcardBind z określonym adresem IP zostanie ustawiona na "True", aby wskazać, że port jest narażony na każdy interfejs komputera raportowania.
4. Porty, które są powiązane tylko na określonym interfejsie mają IsWildcardBind ustawiona na *False*.

#### <a name="naming-and-classification"></a>Nazewnictwo i klasyfikacja

Dla wygody adres IP zdalnego końca połączenia znajduje się we właściwości RemoteIp. W przypadku połączeń przychodzących remoteip jest taki sam jak SourceIp, podczas gdy dla połączeń wychodzących jest taki sam jak DestinationIp. Właściwość RemoteDnsCanonicalNames reprezentuje nazwy kanoniczne DNS zgłaszane przez komputer dla usługi RemoteIp. Właściwości RemoteDnsQuestions i RemoteClassification są zarezerwowane do wykorzystania w przyszłości. 

#### <a name="geolocation"></a>Geolokalizacja

*VMConnection* zawiera również informacje geolokalizacji dla zdalnego końca każdego rekordu połączenia w następujących właściwościach rekordu: 

| Właściwość | Opis |
|:--|:--|
|Zdalnekraje |Nazwa kraju/regionu obsługującego program RemoteIp.  Na przykład Stany *Zjednoczone* |
|Zdalna zdolność do działania |Szerokość geograficzna. Na przykład *47,68* |
|Zdalneodwszerwienie |Długość geograficzna. Na przykład *-122.12* |

#### <a name="malicious-ip"></a>Złośliwy adres IP

Każda właściwość RemoteIp w tabeli *VMConnection* jest sprawdzana względem zestawu adresów IP ze znanymi złośliwymi działaniami. Jeśli RemoteIp zostanie zidentyfikowany jako złośliwy, następujące właściwości zostaną wypełnione (są puste, gdy adres IP nie jest uważany za złośliwy) w następujących właściwościach rekordu:

| Właściwość | Opis |
|:--|:--|
|MaliciousIp (właśc. |Adres RemoteIp |
|Typ wytypu wskaźnika |Wykryto wskaźnik zagrożenia jest jedną z następujących wartości, *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
|Opis |Opis obserwowanego zagrożenia. |
|TLPPoziom |Poziom protokołu sygnalizacji świetlnej (TLP) jest jedną z zdefiniowanych wartości: *Biały,* *Zielony,* *Bursztynowy,* *Czerwony.* |
|Ufność |Wartości to *od 0 do 100*. |
|Ważność |Wartości to *0 – 5*, gdzie *5* jest najcięższy, a *0* wcale nie jest dotkliwe. Wartość domyślna to *3*.  |
|Pierwszy raporteddatetime |Po raz pierwszy dostawca zgłosił wskaźnik. |
|Ostatnia data datetime |Ostatni raz wskaźnik był widziany przez Interflow. |
|Isactive |Wskazuje, że wskaźniki są dezaktywowane wartością *Prawda* lub *Fałda.* |
|Link do raportu |Linki do raportów związanych z danym obserwowalny. |
|DodatkoweInformanie |Zawiera dodatkowe informacje, jeśli dotyczy, o obserwowanym zagrożeniu. |

### <a name="ports"></a>Porty 

Porty na komputerze, które aktywnie akceptują ruch przychodzący lub mogą potencjalnie akceptować ruch, ale są bezczynne w czasie raportowania, są zapisywane w tabeli VMBoundPort.  

Każdy rekord w VMBoundPort jest identyfikowany przez następujące pola: 

| Właściwość | Opis |
|:--|:--|
|Proces | Tożsamość procesu (lub grup procesów), z którymi port jest skojarzony.|
|Ip | Adres IP portu (może to być symbol wieloznaczny IP, *0.0.0.0)* |
|Port |Numer portu |
|Protocol (Protokół) | Protokół.  Przykład *tcp* lub *udp* (obecnie obsługiwany jest tylko *tcp).*|
 
Tożsamość portu pochodzi z powyższych pięciu pól i jest przechowywana we właściwości PortId. Ta właściwość może służyć do szybkiego znajdowania rekordów dla określonego portu w czasie. 

#### <a name="metrics"></a>Metryki 

Rekordy portów obejmują metryki reprezentujące skojarzone z nimi połączenia. Obecnie zgłaszane są następujące metryki (szczegóły dla każdej metryki są opisane w poprzedniej sekcji): 

- BytesSent i BytesReceived 
- LinkiNawiązane, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Oto kilka ważnych kwestii, które należy wziąć pod uwagę:

- Jeśli proces akceptuje połączenia na tym samym adresie IP, ale za pośrednictwem wielu interfejsów sieciowych, zostanie zgłoszony osobny rekord dla każdego interfejsu.  
- Rekordy z wieloznacznym adresem IP nie będą zawierać żadnych działań. Są one włączone do reprezentowania faktu, że port na komputerze jest otwarty dla ruchu przychodzącego. 
- Aby zmniejszyć szczegółowość i ilość danych, rekordy z wieloznacznym adresem IP zostaną pominięte, gdy istnieje pasujący rekord (dla tego samego procesu, portu i protokołu) z określonym adresem IP. Po pominięciu rekordu IP z symbolami wieloznacznych właściwość *IsWildcardBind* dla rekordu o określonym adresie IP zostanie ustawiona na *True*.  Oznacza to, że port jest narażony na każdy interfejs urządzenia raportowania. 
- Porty, które są powiązane tylko na określonym interfejsie mają IsWildcardBind ustawiona na *False*. 

### <a name="vmcomputer-records"></a>Rekordy VMComputer

Rekordy z *typem VMComputer* mają dane spisu serwerów z agentem zależności. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
|TenantId | Unikatowy identyfikator obszaru roboczego |
|SourceSystem | *Insights* | 
|TimeGenerated | Sygnatura czasowa rekordu (UTC) |
|Computer (Komputer) | Nazwa FQDN komputera | 
|AgentId | Unikatowy identyfikator agenta usługi Log Analytics |
|Maszyna | Nazwa zasobu usługi Azure Resource Manager dla komputera udostępniane przez ServiceMap. Jest to formularz *m-{GUID}*, gdzie *identyfikator GUID* jest tym samym identyfikatorem GUID co AgentId. | 
|DisplayName | Nazwa wyświetlana | 
|Pełna nazwa funkcjiwydajna | Pełna nazwa wyświetlana | 
|HostName | Nazwa komputera bez nazwy domeny |
|Czas rozruchu | Czas rozruchu maszyny (UTC) |
|TimeZone | Znormalizowana strefa czasowa |
|VirtualizationState | *wirtualny,* *hipernadzorcy,* *fizyczny* |
|Ipv4Addresses (Ipv4) | Tablica adresów IPv4 | 
|Maski Ipv4Subnet | Tablica masek podsieci IPv4 (w tej samej kolejności co Ipv4Addresses). |
|Ipv4DefaultGateways | Tablica bram IPv4 | 
|Ipv6Addresses (Ipv6) | Tablica adresów IPv6 | 
|Sukienki MacAddresses | Tablica adresów MAC | 
|Nazwy dns | Tablica nazw DNS skojarzonych z urządzeniem. |
|DependencyAgentVersion | Wersja agenta zależności uruchomionego na komputerze. | 
|OperacyjnaRoda Operacyjna | *Linux*, *Windows* |
|OperatingSystemFullName | Pełna nazwa systemu operacyjnego | 
|PhysicalMemoryMB (Pamięć Fizyczna) | Pamięć fizyczna w megabajtach | 
|Procesorów | Liczba procesorów | 
|Szybkość procesora | Szybkość procesora w MHz | 
|VirtualMachineType (Typ wirtualny) | *hyperv*, *vmware*, *xen* |
|VirtualMachineNativeId (Id virtualmachineNativeId) | Identyfikator maszyny Wirtualnej przypisany przez jego hipernadzorcy | 
|VirtualMachineNativeName (VirtualMachineNativeName) | Nazwa maszyny Wirtualnej |
|VirtualMachineHypervisorId | Unikatowy identyfikator funkcji hypervisor hostującej maszynę wirtualną |
|Typ funkcji Hypervisor | *hyperv* |
|HypervisorId | Unikatowy identyfikator hipernadzorcy | 
|HostingProvider | *Azure* |
|_ResourceId | Unikatowy identyfikator zasobu platformy Azure |
|AzureSubscriptionId | Unikatowy globalnie identyfikator identyfikujący subskrypcję | 
|Grupa zasobów Azure | Nazwa grupy zasobów platformy Azure, do. |
|Nazwa usługi AzureResourceName | Nazwa zasobu platformy Azure |
|Lokalizacja usługi Azure | Lokalizacja zasobu platformy Azure |
|Usługa AzureUpdateDomain | Nazwa domeny aktualizacji platformy Azure |
|AzureFaultDomain (Domena usługi AzureFault) | Nazwa domeny błędów platformy Azure |
|Obiekt AzureVmId | Unikatowy identyfikator maszyny wirtualnej platformy Azure |
|Rozmiar platformy Azure | Rozmiar maszyny Wirtualnej platformy Azure |
|AzureImagePublisher | Nazwa wydawcy maszyny Wirtualnej platformy Azure |
|Usługa AzureImageOffering | Nazwa typu oferty maszyny Wirtualnej platformy Azure | 
|AzureImageSku | Jednostka SKU obrazu maszyny wirtualnej platformy Azure | 
|AzureImageVersion | Wersja obrazu maszyny wirtualnej platformy Azure | 
|Usługa AzureCloudServiceName | Nazwa usługi w chmurze platformy Azure |
|Usługa AzureCloudServiceDeployment | Identyfikator wdrożenia usługi w chmurze |
|Nazwa usługi AzureCloudRoleName | Nazwa roli usługi w chmurze |
|Typ roli usługi AzureCloudRoleType | Typ roli usługi w chmurze: *pracownik* lub *sieć Web* |
|Obiekt AzureCloudServiceInstanceId | Identyfikator wystąpienia roli usługi w chmurze |
|Nazwa zestawu AzureVmScaleSet | Nazwa zestawu skalowania maszyny wirtualnej |
|AzureVmScaleSetDeployment | Identyfikator wdrożenia zestawu skalowania maszyny wirtualnej |
|AzureVmScaleSetResourceId | Unikatowy identyfikator zasobu zestawu skalowania maszyny wirtualnej.|
|Obiekt AzureVmScaleSetInstanceId | Unikatowy identyfikator zestawu skalowania maszyny wirtualnej |
|Obiekt AzureServiceFabricClusterId | Unikatowy identyfikator klastra sieci szkieletowej usług Azure | 
|Usługa AzureFabricClusterName | Nazwa klastra sieci szkieletowej usług Azure |

### <a name="vmprocess-records"></a>Rekordy VMProcess

Rekordy z *typem VMProcess* mają dane spisu dla procesów połączonych z TCP na serwerach z agentem zależności. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
|TenantId | Unikatowy identyfikator obszaru roboczego |
|SourceSystem | *Insights* | 
|TimeGenerated | Sygnatura czasowa rekordu (UTC) |
|Computer (Komputer) | Nazwa FQDN komputera | 
|AgentId | Unikatowy identyfikator agenta usługi Log Analytics |
|Maszyna | Nazwa zasobu usługi Azure Resource Manager dla komputera udostępniane przez ServiceMap. Jest to formularz *m-{GUID}*, gdzie *identyfikator GUID* jest tym samym identyfikatorem GUID co AgentId. | 
|Proces | Unikatowy identyfikator procesu mapy usług. Jest w postaci *p-{GUID}*. 
|Plik wykonywalnyName | Nazwa pliku wykonywalnego procesu | 
|DisplayName | Nazwa wyświetlana procesu |
|Rola | Rola procesu: *webserver*, *appServer*, *databaseServer*, *ldapServer*, *smbServer* |
|Grupa | Nazwa grupy procesów. Procesy w tej samej grupie są logicznie powiązane, na przykład, częścią tego samego produktu lub komponentu systemu. |
|StartTime | Czas rozpoczęcia puli procesów |
|Pierwszy Identyfikator | Pierwszy PID w puli procesów |
|Opis | Opis procesu |
|CompanyName | Nazwa firmy |
|Nazwa wewnętrzna | Nazwa wewnętrzna |
|ProductName | Nazwa produktu |
|ProductVersion | Wersja produktu |
|Wersja pliku | Wersja pliku |
|Ścieżka wykonywalna |Ścieżka pliku wykonywalnego |
|Commandline | Wiersz polecenia |
|WorkingDirectory | Katalog roboczy |
|Usługi | Tablica usług, w ramach których proces jest wykonywany |
|UserName | Konto, na którym wykonywany jest proces |
|Domena użytkowników | Domena, w której proces jest wykonywany |
|_ResourceId | Unikatowy identyfikator procesu w obszarze roboczym |


## <a name="sample-map-queries"></a>Przykładowe kwerendy mapy

### <a name="list-all-known-machines"></a>Lista wszystkich znanych maszyn

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Kiedy maszyna wirtualna została ostatnio ponownie uruchomiona

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Podsumowanie maszyn wirtualnych platformy Azure według obrazu, lokalizacji i jednostki SKU

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Wyświetlanie listy pojemności pamięci fizycznej wszystkich zarządzanych komputerów

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Lista nazw komputerów, DNS, IP i OS

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Znajdź wszystkie procesy z "sql" w wierszu polecenia

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Znajdowanie komputera (najnowszego rekordu) według nazwy zasobu

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Znajdowanie komputera (najnowszego rekordu) według adresu IP

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Wyświetlanie listy wszystkich znanych procesów na określonym komputerze

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Lista wszystkich komputerów z programem SQL Server

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Wyświetl listę wszystkich unikatowych wersji produktu curl w moim centrum danych

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Tworzenie grupy komputerów wszystkich komputerów z systemem CentOS

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>Bajty wysyłane i odbierane trendy

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Maszyny wirtualne platformy Azure przesyłają najwięcej bajtów

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Łączenie trendów stanu

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Trend niepowodzeń połączeń

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Porty powiązane

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Liczba otwartych portów na różnych komputerach

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Oceniaj procesy w obszarze roboczym według liczby otwartych portów

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Agreguj zachowanie dla każdego portu

Ta kwerenda może być następnie używana do oceniania portów według aktywności, np.
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Podsumowywanie połączeń wychodzących z grupy komputerów

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
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

## <a name="performance-records"></a>Rekordy wydajności
Rekordy z typem *InsightsMetrics* mają dane dotyczące wydajności z systemu operacyjnego gościa maszyny wirtualnej. Te rekordy mają właściwości w poniższej tabeli:


| Właściwość | Opis |
|:--|:--|
|TenantId | Unikatowy identyfikator obszaru roboczego |
|SourceSystem | *Insights* | 
|TimeGenerated | Czas zebranej wartości (UTC) |
|Computer (Komputer) | Nazwa FQDN komputera | 
|Origin | *vm.azm.ms* |
|Przestrzeń nazw | Kategoria licznika wydajności | 
|Nazwa | Nazwa licznika wydajności |
|Val | Pobrana wartość | 
|Tagi | Powiązane szczegóły dotyczące rekordu. Zobacz poniższą tabelę, aby dostrzec znaczniki używane z różnymi typami rekordów.  |
|AgentId | Unikatowy identyfikator agenta każdego komputera |
|Typ | *Wskaźniki insights* |
|_ResourceId_ | Identyfikator zasobu maszyny wirtualnej |

Liczniki wydajności aktualnie zebrane w tabeli *InsightsMetrics* są wymienione w poniższej tabeli:

| Przestrzeń nazw | Nazwa | Opis | Jednostka | Tagi |
|:---|:---|:---|:---|:---|
| Computer (Komputer)    | Puls             | Puls komputera                        | | |
| Memory (Pamięć)      | DostępneMB           | Dostępne bajty pamięci                    | Bajty          | memorySizeMB - Całkowity rozmiar pamięci|
| Sieć     | WriteBytesPerSecond   | Bajty zapisu sieciowego na sekundę            | PrzeztówPerSekunda | NetworkDeviceId - Identyfikator urządzenia<br>bajty - całkowita liczba wysłanych bajtów |
| Sieć     | ReadBytesPerSecond (Odczyt Naprzemognie)    | Bajty odczytu sieciowego na sekundę             | PrzeztówPerSekunda | networkDeviceId - Identyfikator urządzenia<br>bajty - całkowita liczba odebranych bajtów |
| Procesor   | WykorzystaniePrzestrzysty | Procent wykorzystania procesora          | Wartość procentowa        | totalCpus — łączna wartość procesorów |
| Logiczny dysk | WritesPerSecond       | Zapisy na dysku logicznym na sekundę            | Połów połówek | mountId - Identyfikator instalacji urządzenia |
| Logiczny dysk | 19.00.        | Milisekunda opóźnienia zapisu dysku logicznego    | Milisekund   | mountId - Identyfikator instalacji urządzenia |
| Logiczny dysk | WriteBytesPerSecond   | Bajty zapisu dysku logicznego na sekundę       | PrzeztówPerSekunda | mountId - Identyfikator instalacji urządzenia |
| Logiczny dysk | TransferPerSecond    | Transfery dysku logicznego na sekundę         | Połów połówek | mountId - Identyfikator instalacji urządzenia |
| Logiczny dysk | TransferLatencyMs     | Milisekunda opóźnienia transferu dysku logicznego | Milisekund   | mountId - Identyfikator instalacji urządzenia |
| Logiczny dysk | OdczytySekundy        | Odczyty dysku logicznego na sekundę             | Połów połówek | mountId - Identyfikator instalacji urządzenia |
| Logiczny dysk | ReadLatencyMs (Odczytywanie erekcji)         | Milisekunda opóźnienia odczytu dysku logicznego     | Milisekund   | mountId - Identyfikator instalacji urządzenia |
| Logiczny dysk | ReadBytesPerSecond (Odczyt Naprzemognie)    | Bajty odczytu dysku logicznego na sekundę        | PrzeztówPerSekunda | mountId - Identyfikator instalacji urządzenia |
| Logiczny dysk | FreeSpacePrzestrzeń   | Procent wolnego miejsca na dysku logicznym        | Wartość procentowa        | mountId - Identyfikator instalacji urządzenia |
| Logiczny dysk | FreeSpaceMB (Obszar wolny)           | Bajty wolnego miejsca na dysku logicznym             | Bajty          | mountId - Identyfikator instalacji urządzenia<br>diskSizeMB — całkowity rozmiar dysku |
| Logiczny dysk | PrzeztówPerSekunda        | Bajty dysku logicznego na sekundę             | PrzeztówPerSekunda | mountId - Identyfikator instalacji urządzenia |


## <a name="next-steps"></a>Następne kroki

* Jeśli jesteś nowy do pisania zapytań dziennika w usłudze Azure Monitor, sprawdź, [jak używać usługi Log Analytics](../../azure-monitor/log-query/get-started-portal.md) w witrynie Azure portal do pisania zapytań dziennika.

* Dowiedz się więcej o [pisaniu zapytań](../../azure-monitor/log-query/search-queries.md)wyszukiwania .
